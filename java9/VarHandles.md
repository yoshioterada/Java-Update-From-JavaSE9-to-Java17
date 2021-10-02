# JEP 193: Variable Handles について

Java 9 で Variable Handles と呼ばれる新しい API が追加されました。これは Java 7 の [MethodHandle](https://docs.oracle.com/javase/jp/16/docs/api/java.base/java/lang/invoke/MethodHandle.html) を拡張し、クラス [java.lang.invoke.VarHandle](https://docs.oracle.com/javase/jp/9/docs/api/java/lang/invoke/VarHandle.html) でフィールドの変数や配列に対して強い型付けを持って参照ができます。ただし変数の参照だけでなく、Atomic 操作やリフレクションなどの操作に利用可能です。  
例えば、ある一つの変数に対してアトミック操作を行うことによって、並列プログラミング時に変数に対するアクセスを保護を行うことができます。
これまでは、[アトミック関連クラス](https://docs.oracle.com/javase/jp/9/docs/api/java/util/concurrent/atomic/package-summary.html)でこれらの操作を行ってきましたが、これからは、Variable Handles を使用することができます。  
また、リフレクションの代わりに使用することもできます。リフレクション API と比較した場合、優れたパフォーマンスを発揮し、型安全性を持たせることができます。

つまり、Variable Handles　はパフォーマンスが求められる場面における、リフレクションの代替として、もしくはアトミック操作を利用する場面において利用を検討することができます。

```text
アトミック操作(不可分操作)とは
途中に割り込みが入らない操作の事で、例えば、銀行口座の入金や出金の処理では、金額の不一致を防ぐために排他制御が必要です。例えば、排他制御を行いアトミック性を確保します。

NOTE: 
Java で、変数に volatile キーワードをつけても Atomic にはならず排他制御には利用できません。
```

```text
Variable Handles は、C/C++11 Atomic　と互換性を持つように設計されています。今回の VarHandle を実装せずに、従来のAtomic API に対して修正を加えた場合、C/C++ 11 で追加されたメモリモデルを利用するために、追加のアクセス整合性ポリシーが必要となり、実装が複雑化する可能性がありました。それを避けるために Variable Handles を導入しています

C/C++11: 2011年に見直されたC言語の仕様の規格:  
C/C++11のメモリの取得と解放の詳細については、
https://cpprefjp.github.io/reference/atomic.html
https://cpprefjp.github.io/reference/atomic/memory_order.html
```

## Variable Handle の目的

Variable Handle は下記の４点を考慮して提供される API です。

* ***安全性:*** JVM を壊さないように メモリを扱う
* ***整合性:*** フィールドへのアクセスは、getfield および putfield バイト・コードと同じアクセス規則に従う (final は操作不可)
* ***パフォーマンス:*** sun.misc.Unsafe と同等のパフォーマンスを提供
* ***ユーザビリティ:*** sun.misc.Unsafe よりも優れた API を提供

並列処理プログラミングの需要が増える一方で、今まで、クラスの変数（フィールド）に対する操作を、アトミック操作や順序付けされた操作として、うまく実装できない課題がありました。

 これまで、これらを実現する為に、synchronized を使用するか、 [java.util.concurrent.atomic](https://docs.oracle.com/javase/jp/8/docs/api/java/util/concurrent/atomic/package-summary.html) を使用してきました。しかし、パフォーマンスが悪かったり、処理に対するオーバヘッドが大きいため、よりパフォーマンスを求めるために、[sun.misc.Unsafe](http://hg.openjdk.java.net/jdk8/jdk8/jdk/file/687fd7c7986d/src/share/classes/sun/misc/Unsafe.java) という、非推奨で非サポートの JVM の組み込み関数が使用されてきました(Java 1.5 以降では、java.util.concurrent.atomic 配下のクラスで内部的に Unsafe を利用)。 特に、Unsafe の組み込み関数は高速であるため、非推奨であるにもかかわらず、フレームワークやライブラリ等で幅広く利用され、結果として移植性や安全性が損なわれていました。

 ### 現在の実装の問題点

[JEP 193: Variable Handles](https://openjdk.java.net/jeps/193) のオーナーの ***Paul Sandoz*** によると現在の問題点を、[下記のように説明](http://cr.openjdk.java.net/~psandoz/varhandles/jvmls14-varHandles.pdf)しています。

```text
* Atomic* classes have overhead
    • Not used in j.u.concurrent classes
* sun.misc.Unsafe is... 
    unsafe, not “portable”, and going away
    • CAS is too important to relegate
* No unified/safe model for accessing on and off-heap
```

訳すと下記の通りです。

* Atomic 関連のクラスは、オーバヘッドが高い
    - パフォーマンスが悪いため Atomic 関連クラスを java.util.concurrent クラスの中で使用していない
* sun.misc.Unsafe は
    - 安全でなく、ポータビリティもない、そして今後取り除かれる。
    - CAS(Copy and Set)は移管するのにとても重要
* Heap に対してアクセスするための統合的で安全なモデルがない


## sun.misc.Unsafe を利用した実装例（非推奨）

sun.misc.Unsafe を使用した変数を Atomic で書き換える例を下記に記載します。  
MyCounter のコンストラクターで counter のメモリ・アドレス の offset 値を取得しています。incrementAndGet() メソッドで、この offset 値を使用して変数（フィールド）の値を更新します。
書き換える対象の変数は、値を読み書きするすべてのスレッドから見えるように、volatile として宣言する必要があります。([JLS 8.3.1.4. volatile Fields](https://docs.oracle.com/javase/specs/jls/se17/html/jls-8.html#jls-8.3.1.4)) 

incrementAndGet()メソッドでは while ループを使用し、成功するまで操作を繰り返し行います。 実際には compareAndSwapLong() を呼び出し、変数が記載されているメモリのオフセットを指定し、現在の値と比較したのち、以前の値を１増加しています。そして更新したのち、以前の値が変更されたかどうかを確認しています。ここではブロッキング処理は実装していないため、高速にクラス内のインスタンス変数を書き換えることができています。  
しかし、型安全性がないため Java 言語の観点では推奨された実装方法ではありません。

```java
import java.util.stream.IntStream;
import java.lang.reflect.Field;
import sun.misc.Unsafe;

class MyCounter {
    private Unsafe unsafe;
    private volatile long counter = 0;
    private long valueOffset;

    public MyCounter() throws IllegalAccessException, NoSuchFieldException {
        unsafe = getUnsafe();
        valueOffset = unsafe.objectFieldOffset(MyCounter.class.getDeclaredField("counter"));
    }

    public static void main(String... argv){
        try{
            MyCounter main = new MyCounter();

            IntStream.range(0,100)
                .forEach(num -> {
                    //実際にはここが並列処理として実行される
                    System.out.println(main.incrementAndGet());
                });
        }catch (IllegalAccessException | NoSuchFieldException e){
            e.printStackTrace();
        }
    }

    private Unsafe getUnsafe() throws IllegalAccessException, NoSuchFieldException {
        Field f = Unsafe.class.getDeclaredField("theUnsafe");
        f.setAccessible(true);
        return (Unsafe) f.get(null);
    }

    public long incrementAndGet() {
        long current = counter;
        long next = current + 1;

        while (!unsafe.compareAndSwapLong(this, valueOffset, current, next)) {
            counter = next;
        }
        return counter;
    }
}
```

### Java 11 移行の sun.misc.Unsafe の扱い

[sun.misc.Unsafe は Java11 から非サポート](http://hg.openjdk.java.net/jdk/jdk11/file/6889f13694c6/src/jdk.unsupported/share/classes/sun/misc/Unsafe.java)になります。

java.util.concurrent.atomic パッケージ配下の Atomic* 関連クラスも、将来的に VarHandles で書き換えられるようです。
実際に、Java 17 の [java.util.concurrent.atomic.AtomicInteger](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/atomic/AtomicInteger.html) のソースコードを確認した所、下記のようにコメントが記載されていました。これによると、現時点では、まだ起動時の依存関係で未解決の問題があるため Unsafe を利用しているようですが、将来的には書き換えられると思われます。

```java
import jdk.internal.misc.Unsafe;

    /*
     * This class intended to be implemented using VarHandles, but there
     * are unresolved cyclic startup dependencies.
     */
    private static final Unsafe U = Unsafe.getUnsafe();
    private static final long VALUE
        = U.objectFieldOffset(AtomicInteger.class, "value");
```

Java 11 で危険を承知で利用したい場合は、module-info.java に require を記載する等の方法で回避できますが
基本的には非推奨なため、ここではその詳細については割愛します。

## java.lang.invoke.VarHandle の利用方法

VarHandle を利用するためには、下記のクラスを利用します。  
[クラス java.lang.invoke.VarHandle](https://docs.oracle.com/javase/jp/9/docs/api/java/lang/invoke/VarHandle.html)  

### サンプルコード

複数のスレッドから変更されるインスタンス変数（フィールド）を持つクラスを定義します。


```java
import java.nio.ByteBuffer;

public class Data {
    public int counter = 1;
    private int privateField = 10;
    public String name = "Yoshio Terada";
    public byte[] data = new byte[]{1, 0, 0, 0, 1, 0, 0, 0};
    public char[] charArray = new char[]{'A','B','C','D','E','F'};
    public ByteBuffer dataBuffer = ByteBuffer.wrap(this.data);
}
```

VarHandle を利用して読み書きをするコード例を下記に示します。ここでは最初に ATOMIC 操作ではない API を利用しています。

```java
    public void nonAtomicGetAndSetEvaluation(Data data){
        try {
            VarHandle varHandle = MethodHandles.lookup().findVarHandle(Data.class,"counter", int.class);
            Data data = new Data();

            //Read Access
            System.out.println(varHandle.get(data)); //アクセスモード： GET
            System.out.println(varHandle.getVolatile(data)); //アクセスモード： GET_VOLATILE
            System.out.println(varHandle.getOpaque(data)); //アクセスモード： GET_OPAQUE
            System.out.println(varHandle.getAcquire(data)); //アクセスモード：　GET_ACQUIRE

            //Write Access
            int newValue = 2;
            varHandle.set(data, newValue);　//アクセスモード： SET
            System.out.println(varHandle.get(data)); //アクセスモード： GET
            varHandle.setVolatile(data, newValue + 1); //アクセスモード： SET_VOLATILE
            System.out.println(varHandle.get(data));
            varHandle.setOpaque(data, newValue + 2); //アクセスモード：　SET_OPAQUE
            System.out.println(varHandle.get(data));
            varHandle.setRelease(data, newValue + 3); //アクセスモード： SET_RELEASE
            System.out.println(varHandle.get(data));

        } catch (NoSuchFieldException | IllegalAccessException e) {
            e.printStackTrace();
        }
    }

    public void arrayCheck(Data data) {
        VarHandle byteArrayViewVarHandle = MethodHandles.arrayElementVarHandle(char[].class);
        // char 配列 {'A','B','C','D','E','F'} より char を一つづつ取得
        for (int i = 0; i < data.charArray.length; i++) {
            var out = byteArrayViewVarHandle.getAcquire(data.charArray, i);
            System.out.println(out);
            // char が 'C' , 'D' ならば 'Z' に置き換え
            if (out.equals('C')|| out.equals('D')) {
                byteArrayViewVarHandle.setRelease(data.charArray, i, 'Z');
            }
        }
        // char 配列は {'A','B','Z','Z','E','F'} に置き換わる
    }
```

### ソースコードの説明

[VarHandle](https://docs.oracle.com/javase/jp/11/docs/api/java.base/java/lang/invoke/VarHandle.html) オブジェクトは、[MethodHandles.Lookup](https://docs.oracle.com/javase/jp/11/docs/api/java.base/java/lang/invoke/MethodHandles.Lookup.html) クラスのファクトリ・メソッドを使用して作成できます。

Lookup のファクトリから VarHandle を生成するために３種類のメソッドが用意されています。インスタンス変数、クラス変数用、そしてリフレクションの代わりに利用可能な VarHandle を生成することができます。

* [findStaticVarHandle​(Class<?> decl, String name, Class<?> type)](https://docs.oracle.com/javase/jp/11/docs/api/java.base/java/lang/invoke/MethodHandles.Lookup.html#findStaticVarHandle(java.lang.Class,java.lang.String,java.lang.Class))

```java
VarHandle staticVarHandle =　MethodHandles.lookup()
  .findStaticVarHandle(Hello.class, "staticConter", int.class);
```

* [findVarHandle​(Class<?> recv, String name, Class<?> type)](https://docs.oracle.com/javase/jp/11/docs/api/java.base/java/lang/invoke/MethodHandles.Lookup.html#findVarHandle(java.lang.Class,java.lang.String,java.lang.Class))

```java
VarHandle varHandle =　MethodHandles.lookup()
  .findVarHandle(Hello.class, "count", int.class);
```

* [unreflectVarHandle​(Field f)](https://docs.oracle.com/javase/jp/11/docs/api/java.base/java/lang/invoke/MethodHandles.Lookup.html#unreflectVarHandle(java.lang.reflect.Field))

```java
VarHandle unreflectVarHandle =　MethodHandles
  .lookup()
  .unreflectVarHandle(Hello.class.getDeclaredField("count"));
```


### アクセスモード

VarHandle は異なるアクセスモードで変数への読み取り、書き込みのアクセスをサポートしています。

[列挙型 VarHandle.AccessMode](https://docs.oracle.com/javase/jp/11/docs/api/java.base/java/lang/invoke/VarHandle.AccessMode.html) に31 個のアクセスモードが定義されており、AccessMode ごとに、VarHandle 内に対応メソッドがあります。たとえば、アクセス モード GET_AND_ADD の場合、VarHandle.getAndAdd() メソッドを使用します。
上記のサンプルコードでは、ATOMIC ではないアクセス・モードを利用しています。

#### ATOMIC ではない読み書き用のアクセス・タイプと対応するメソッド

|  アクセスモード  |  対応するメソッド |　ATOMIC か否か  |
| ---- | ---- | ---- |
|GET |get()| NON ATOMIC|
|GET_ACQUIRE |getAcquire()| NON ATOMIC|
|GET_OPAQUE |getOpaque()| NON ATOMIC|
|GET_VOLATILE|getVolatile()| NON ATOMIC|
|SET|set()| NON ATOMIC|
|SET_OPAQUE |setOpaque()| NON ATOMIC|
|SET_RELEASE|setRelease()| NON ATOMIC|
|SET_VOLATILE|setVolatile()| NON ATOMIC|

#### ATOMIC で読み書きをするアクセスタイプと対応するメソッドを下記に示します。

* アトミックで比較・設定する更新アクセスモード

|  アクセスモード  |  対応するメソッド |　ATOMIC か否か  |
| ---- | ---- | ---- |
|COMPARE_AND_EXCHANGE|compareAndExchange()|ATOMIC|
|COMPARE_AND_EXCHANGE_ACQUIRE|compareAndExchangeAcquire()|ATOMIC|
|COMPARE_AND_EXCHANGE_RELEASE|compareAndExchangeRelease()|ATOMIC|
|COMPARE_AND_SET|compareAndSet()|ATOMIC|
|GET_AND_SET|getAndSet()|ATOMIC|
|GET_AND_SET_ACQUIRE|getAndSetAcquire()|ATOMIC|
|GET_AND_SET_RELEASE|getAndSetRelease()|ATOMIC|
|WEAK_COMPARE_AND_SET|weakCompareAndSet()|できる限り(Possibly) ATOMIC|
|WEAK_COMPARE_AND_SET_ACQUIRE|weakCompareAndSetAcquire()|できる限り(Possibly) ATOMIC|
|WEAK_COMPARE_AND_SET_PLAIN|weakCompareAndSetPlain()|できる限り(Possibly) ATOMIC|
|WEAK_COMPARE_AND_SET_RELEASE|weakCompareAndSetRelease()|できる限り(Possibly) ATOMIC|

* 数値用のアトミック更新アクセスモード

|  アクセスモード  |  対応するメソッド |　ATOMIC か否か  |
| ---- | ---- | ---- |
|GET_AND_ADD| getAndAdd()|ATOMIC|
|GET_AND_ADD_ACQUIRE|getAndAddAcquire()|ATOMIC|
|GET_AND_ADD_RELEASE|getAndAddRelease()|ATOMIC|

* ビット単位でのアトミック更新アクセスモード

|  アクセスモード  |  対応するメソッド |　ATOMIC か否か  |
| ---- | ---- | ---- |
|GET_AND_BITWISE_AND|getAndBitwiseAnd()|ATOMIC|
|GET_AND_BITWISE_AND_ACQUIRE|getAndBitwiseAndAcquire()|ATOMIC|
|GET_AND_BITWISE_AND_RELEASE|getAndBitwiseAndRelease()|ATOMIC|
|GET_AND_BITWISE_OR|getAndBitwiseOr()|ATOMIC|
|GET_AND_BITWISE_OR_ACQUIRE|getAndBitwiseOrAcquire()|ATOMIC|
|GET_AND_BITWISE_OR_RELEASE|getAndBitwiseOrRelease()|ATOMIC|
|GET_AND_BITWISE_XOR|getAndBitwiseXor()|ATOMIC|
|GET_AND_BITWISE_XOR_ACQUIRE|getAndBitwiseXorAcquire()|ATOMIC|
|GET_AND_BITWISE_XOR_RELEASE|getAndBitwiseXorRelease|ATOMIC|

### Atomic での更新サンプル

上記のアクセスモードの内 ATOMIC 操作が可能なメソッドを利用して実装を行います。数値用、ビット単位、比較をして更新するメソッドが用意されているため、必要に応じたメソッドを呼び出します。

```java
    public void atomicUpdate(Data data){
        try {
            VarHandle varHandle = MethodHandles.lookup().findVarHandle(Data.class, "name", String.class);
            String expectedValue = "Yoshio Terada";
            String newValue = "I Love Duke!!";

            // compareAndSet() は比較・更新に成功したか否かを真偽値で返す　（ここでは true が表示、値は I Love Duke に変更)
            System.out.println(varHandle.compareAndSet(data, expectedValue, newValue)); 
            // compareAndExchangeAcquire　は読み込み時にメモリをバリアし比較・更新したい場合に利用、返り値は変更前の値（ここでは I Love Duke が表示、値は Yoshio Teradaに変更)
            System.out.println(varHandle.compareAndExchangeAcquire(data, newValue, expectedValue));
            // compareAndExchangeRelease は書き込み時にメモリをバリアし比較・更新したい場合に利用、返り値は変更前の値（ここでは Yoshio Terada が表示、値は I Love Dukeに変更)
            System.out.println(varHandle.compareAndExchangeRelease(data, expectedValue, newValue));
            // getAndSetAcquire は読み込み時にメモリをバリアし更新したい場合に利用、返り値は変更前の値（ここでは I Love Duke が表示、値は Yoshio Teradaに変更)
            System.out.println(varHandle.getAndSetAcquire(data, expectedValue));       
            //最後に Yoshio Terada が表示される
            System.out.println(data.name);
        } catch (NoSuchFieldException | IllegalAccessException e) {
            e.printStackTrace();
        }
    }

    public void numericAtomicUpdate (Data data) {
        try {
            VarHandle varHandle = MethodHandles.lookup().findVarHandle(Data.class, "counter", int.class);
            final int adder = 1;

            //読み込み時にメモリをバリアしたい場合 Acquire を利用、返り値は変更前の値（ここでは1が表示)
            System.out.println(varHandle.getAndAddAcquire(data,adder));
            //書き込み時にメモリをバリアしたい場合 Release を利用　返り値は変更前の値 (ここでは2が表示)
            System.out.println(varHandle.getAndAddRelease(data,adder));
            System.out.println(data.counter); // (ここで3が表示)
        } catch (NoSuchFieldException | IllegalAccessException e) {
            e.printStackTrace();
        }
    }

```

### リフレクションの代わりに Variable Handles を利用する

最後に、リフレクション API を利用する代わりに、VarHandle を利用する方法を紹介します。  
ここでは、private フィールドにアクセスし、その値を確認したり、更新するサンプルを下記に紹介します。MethodHandles のルックアップで　unreflectVarHandle() を呼び出して VarHandle のインスタンスを生成します。
そして、get(), set() などのメソッドを呼び出して値を更新しています。

```java
public class Data {
    private int privateField = 10;
}

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;
import java.lang.reflect.Field;

public class DataUpdater {
    public static void main(String... argv) {
        DataUpdater main = new DataUpdater();
        Data data = new Data();
        main.insteadOfReflectionTest(data);
    }

    public void insteadOfReflectionTest() {

    public void insteadOfReflectionTest(Data data) {
        try {
            Class<? extends Data> class1 = data.getClass();
            Field privateField = class1.getDeclaredField("privateField");

            // private フィールドにアクセスするための VarHandle を取得
            VarHandle unreflectVarHandle = MethodHandles.privateLookupIn(class1, MethodHandles.lookup())
                    .unreflectVarHandle(privateField);

            // private フィールドの現在値を取得 10 を出力
            System.out.println(unreflectVarHandle.get(data));
            // private フィールドを新しい値で更新
            unreflectVarHandle.set(data, 20);
            // private フィールドの現在値を取得 20 を出力
            System.out.println(unreflectVarHandle.get(data));

        } catch (NoSuchFieldException | SecurityException | IllegalAccessException e) {
            e.printStackTrace();
        }
    }
}
```

### メモリ・フェンス

今回、詳しく取り上げていませんが、VarHandle は C++11 の [atomic_thread_fence](https://cpprefjp.github.io/reference/atomic/atomic_thread_fence.html) に対応したメモリの順番を制御するためのメモリ・フェンスの機能も持っています。  
フェンス操作は、メモリ順序をきめ細かく制御するための最小限の機能を提要しています。VarHandle では、異なるフェンスを作成するために 5 つの static メソッドを提供しています。

***フェンスの強弱***  
loadLoadFence fence(弱) < acquire fence（中） < full fence(強)  
storeStoreFence fence(弱) < release fence（中） < full fence(強)

|  Method  |  フェンス前の操作 |フェンス後の操作  | C++ 11 atomic_thread_fence との対応 | 意味 |
| ---- | ---- | ---- | ---- | ---- |
| fullFence()|loads and stores|loads and stores|memory_order_seq_cst|フェンス前のロードとストアが、フェンス後、ロードとストアで並べ替えられないようにする|
| acquireFence()|loads|loads and stores|memory_order_acquire|フェンス前のロードがフェンス後にロードおよびストアで並べ替えられないようにする|
| releaseFence()|loads and stores|stores|memory_order_release|フェンス前のロードとストアがフェンス後にストアで並び替えられないようにする|
| loadLoadFence()|loads|loads||フェンス前のロードがフェンス後のロードと並び替えられないようにする|
| storeStoreFence()|stores|stores||フェンス前のストアがフェンス後のストアと並べ替えられないようにする|


## まとめ

上記のサンプル・コードで示したように、VarHandle はアトミック操作やリフレクションの代わりに、利用できることがわかりました。[アトミック関連クラス](https://docs.oracle.com/javase/jp/9/docs/api/java/util/concurrent/atomic/package-summary.html)は、利用する際にオーバヘッドが高く、sun.misc.Unsafe はハイ・パフォーマンスではあるものの、非推奨のクラスでした。こうした課題を解決するために提供された VarHandle はパフォーマンスが求められる場面において利用が可能です。  
利用する上では、いくつかの注意点があります。そこで実際に使用する際には API ドキュメントを注意深く読んで、理解してお使いください。
注意点を理解した上でご利用いただく事で、推奨された方法で並列プログラミング時のパフォーマンスを向上させることも可能ですので、この記事を手始めにお試しください。

備考  
VarHandle は既に Fork/Join 関連クラスで実際に sun.misc.Unsafe から書き換えられています。下記の ForkJoinPool の実装中では Fence の実際の使用例も記載されていますので、参照してください。  
(参照：[ForkJoinPool](https://github.com/yoshioterada/jdk/blob/master/src/java.base/share/classes/java/util/concurrent/ForkJoinPool.java), [ForkJoinTask](https://github.com/yoshioterada/jdk/blob/master/src/java.base/share/classes/java/util/concurrent/ForkJoinTask.java))

また現在、Incubator のプロジェクトとして位置づけされている [JEP 412: Foreign Function & Memory API (Incubator)](https://openjdk.java.net/jeps/412) は実装で Variable Handle を利用して実装しています。  
[Foreign Function & Memory API のソースコードはこちら](https://github.com/yoshioterada/jdk/tree/master/src/jdk.incubator.foreign/share/classes/jdk/incubator/foreign)

## 参考

* [java.lang.invoke.VarHandle](https://docs.oracle.com/javase/jp/9/docs/api/java/lang/invoke/VarHandle.html)
* [JEP 193: Variable Handles](https://openjdk.java.net/jeps/193)
* [Using JDK 9 Memory Order Modes by Doug Lea.](http://gee.cs.oswego.edu/dl/html/j9mm.html)
* [Acquire and Release Semantics](https://preshing.com/20120913/acquire-and-release-semantics/)

---
***TODO: 上記のサンプルをマルチ・スレッドのコードに書き直し、もう少し検証をしたい。***
