# Java 9 から Java 17 までのアップデートのまとめ

## Java SE 9

[Java SE 9 の全機能一覧](https://openjdk.java.net/projects/jdk9/)

### 代表的なアップデート機能

* [JEP 193: Var Handles](https://openjdk.java.net/jeps/193)
* [JEP 222: JShell: The Java Shell (Read-Eval-Print Loop)](https://openjdk.java.net/jeps/222)
* [JEP 248: Make G1 the Default Garbage Collector](https://openjdk.java.net/jeps/248)
* [JEP 259: Stack-Walking API](https://openjdk.java.net/jeps/259)
* [JEP 269: Convenience Factory Methods for Collections](https://openjdk.java.net/jeps/269)

#### モジュール関連

* [JEP 200: The Modular JDK](https://openjdk.java.net/jeps/200)
* [JEP 201: Modular Source Code](https://openjdk.java.net/jeps/201)
* [JEP 220: Modular Run-Time Images](https://openjdk.java.net/jeps/220)
* [JEP 260: Encapsulate Most Internal APIs](https://openjdk.java.net/jeps/260)
* [JEP 261: Module System](https://openjdk.java.net/jeps/261)
* [JEP 275: Modular Java Application Packaging](https://openjdk.java.net/jeps/275)
* [JEP 282: jlink: The Java Linker](https://openjdk.java.net/jeps/282)

### その他

* [Other Enhancements](./java9/others.md)

## Java SE 10 

[Java SE 10 の全機能一覧](https://openjdk.java.net/projects/jdk/10/)

### 代表的なアップデート機能

* [JEP 286: Local-Variable Type Inference](https://openjdk.java.net/jeps/286)
* [JEP 307: Parallel Full GC for G1](https://openjdk.java.net/jeps/307)
* [JEP 310: Application Class-Data Sharing](https://openjdk.java.net/jeps/310)


### その他

* [Other Enhancements](./java10/others.md)


## Java SE 11

[Java SE 11 の全機能一覧](https://openjdk.java.net/projects/jdk/11/)

### 代表的なアップデート機能

* [JEP 321: HTTP Client](https://openjdk.java.net/jeps/321)
* [JEP 328: Flight Recorder](https://openjdk.java.net/jeps/328)
* [JEP 320: Remove the Java EE and CORBA Modules](https://openjdk.java.net/jeps/320)
* [JEP 335: Deprecate the Nashorn JavaScript Engine](https://openjdk.java.net/jeps/335)

### その他

* [Other Enhancements](./java11/others.md)
* [New Method added in String class](./java11/Improve-String-Class.md)
    - [isBlank](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html#isBlank())
    - [lines](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html#lines())
    - [repeat](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html#repeat(int))
    - [strip](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html#strip())
    - [stripLeading](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html#stripLeading())
    - [stripTrailing](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html#stripTrailing())

## Java SE 12

[Java SE 12 の全機能一覧](https://openjdk.java.net/projects/jdk/12/)

### 代表的なアップデート機能

* [JEP 230:	Microbenchmark Suite](https://openjdk.java.net/jeps/230)
* [JEP 334:	JVM Constants API](https://openjdk.java.net/jeps/334)
* [JEP 340:	One AArch64 Port, Not Two](https://openjdk.java.net/jeps/340)
* [JEP 341:	Default CDS Archives](https://openjdk.java.net/jeps/341)
* [JEP 344:	Abortable Mixed Collections for G1](https://openjdk.java.net/jeps/344)
* [JEP 346:	Promptly Return Unused Committed Memory from G1](https://openjdk.java.net/jeps/346)


## Java SE 13
[Java SE 13 の全機能一覧](https://openjdk.java.net/projects/jdk/13/)

### 代表的なアップデート機能

* [JEP 353: Reimplement the Legacy Socket API](https://openjdk.java.net/jeps/353)
* [AArch64: ZGC for Aarch64](https://bugs.openjdk.java.net/browse/JDK-8214527)


### その他

* [JEP 350:	Dynamic CDS Archives](https://openjdk.java.net/jeps/350)
* [JEP 351:	ZGC: Uncommit Unused Memory](https://openjdk.java.net/jeps/351)



## Java SE 14
[Java SE 14 の全機能一覧](https://openjdk.java.net/projects/jdk/14/)

### 代表的なアップデート機能

* [JEP 349: JFR Event Streaming](https://openjdk.java.net/jeps/349)
* [JEP 358: Helpful NullPointerExceptions](https://openjdk.java.net/jeps/358)
* [JEP 361: Switch Expressions](https://openjdk.java.net/jeps/361)
* [JEP 363: Remove the Concurrent Mark Sweep (CMS) Garbage Collector](https://openjdk.java.net/jeps/363)

### その他

* [JPE 345:	NUMA-Aware Memory Allocation for G1](https://openjdk.java.net/jeps/345)
* [JPE 352:	Non-Volatile Mapped Byte Buffers](https://openjdk.java.net/jeps/352)
* [JPE 362:	Deprecate the Solaris and SPARC Ports](https://openjdk.java.net/jeps/362)
* [JPE 364:	ZGC on macOS](https://openjdk.java.net/jeps/364)
* [JPE 365:	ZGC on Windows](https://openjdk.java.net/jeps/365)
* [JPE 366:	Deprecate the ParallelScavenge + SerialOld GC Combination](https://openjdk.java.net/jeps/366)
* [JPE 367:	Remove the Pack200 Tools and API](https://openjdk.java.net/jeps/367)


## Java SE 15
[Java SE 15 の全機能一覧](https://openjdk.java.net/projects/jdk/15/)

### 代表的なアップデート機能

* [JEP 371: Hidden Classes](https://openjdk.java.net/jeps/371)
* [JEP 372: Remove the Nashorn JavaScript Engine](https://openjdk.java.net/jeps/372)
* [JEP 373: Reimplement the Legacy DatagramSocket API](https://openjdk.java.net/jeps/373)
* [JEP 374: Deprecate and Disable Biased Locking](https://openjdk.java.net/jeps/374)
* [JEP 378: Text Blocks](https://openjdk.java.net/jeps/378)
* [JEP 377: ZGC: A Scalable Low-Latency Garbage Collector (Production)](https://openjdk.java.net/jeps/377)
* [JEP 379: Shenandoah: A Low-Pause-Time Garbage Collector (Production)](https://openjdk.java.net/jeps/379)

### その他

* [JEP 339:	Edwards-Curve Digital Signature Algorithm (EdDSA)](https://openjdk.java.net/jeps/339)
* [JEP 377:	ZGC: A Scalable Low-Latency Garbage Collector](https://openjdk.java.net/jeps/377)
* [JEP 381:	Remove the Solaris and SPARC Ports](https://openjdk.java.net/jeps/381)
* [JEP 385:	Deprecate RMI Activation for Removal](https://openjdk.java.net/jeps/385)


## Java SE 16
[Java SE 16 の全機能一覧](https://openjdk.java.net/projects/jdk/16/)

### 代表的なアップデート機能

* [JEP 347: Enable C++14 Language Features](https://openjdk.java.net/jeps/347)
* [JEP 357:	Migrate from Mercurial to Git](https://openjdk.java.net/jeps/357)
* [JEP 369: Migrate to GitHub](https://openjdk.java.net/jeps/369)
* [JEP 376: ZGC: Concurrent Thread-Stack Processing (ZGC Improvement)](https://openjdk.java.net/jeps/376)
* [JEP 380: Unix-Domain Socket Channels](https://openjdk.java.net/jeps/380)
* [JEP 387: Elastic Metaspace](https://openjdk.java.net/jeps/387)
* [JEP 392: Packaging Tool](https://openjdk.java.net/jeps/392)
* [JEP 395: Records](https://openjdk.java.net/jeps/395)
* [JEP 396: Strongly Encapsulate JDK Internals by Default](https://openjdk.java.net/jeps/396)

### その他

* [JEP 386:	Alpine Linux Port](https://openjdk.java.net/jeps/386)
* [JEP 388:	Windows/AArch64 Port](https://openjdk.java.net/jeps/388)
* [JEP 390:	Warnings for Value-Based Classes](https://openjdk.java.net/jeps/390)
* [JEP 394:	Pattern Matching for instanceof](https://openjdk.java.net/jeps/394)


## Java SE 17
[Java SE 16 の全機能一覧](https://openjdk.java.net/projects/jdk/17/)

### 代表的なアップデート機能

* [JEP 356: Enhanced Pseudo-Random Number Generators](https://openjdk.java.net/jeps/356)
* [JEP 398: Deprecate the Applet API for Removal](https://openjdk.java.net/jeps/398)
* [JEP 409: Sealed Classes](https://openjdk.java.net/jeps/409)
* [JEP 410: Remove the Experimental AOT and JIT Compiler](https://openjdk.java.net/jeps/410)
h
* [JEP 411: Deprecate the Security Manager for Removal](https://openjdk.java.net/jeps/411)

### その他

* [JEP 306:	Restore Always-Strict Floating-Point Semantics](https://openjdk.java.net/jeps/306)
* [JEP 382:	New macOS Rendering Pipeline](https://openjdk.java.net/jeps/382)
* [JEP 391:	macOS/AArch64 Port](https://openjdk.java.net/jeps/391)
* [JEP 403:	Strongly Encapsulate JDK Internals](https://openjdk.java.net/jeps/403)
* [JEP 407:	Remove RMI Activation](https://openjdk.java.net/jeps/407)
* [JEP 415:	Context-Specific Deserialization Filters](https://openjdk.java.net/jeps/415)

## Java SE 17 Migration Guide
[Java SE 17 Migration Guide](https://docs.oracle.com/en/java/javase/17/migrate/getting-started.html)

* JDKの大幅な変更 (Significant Changes in the JDK)
    - [JDK 17 リリースの大幅な変更](https://docs.oracle.com/en/java/javase/17/migrate/significant-changes-jdk-release.html#GUID-339B2415-8BA8-438C-93AF-F9C746F7CE45)
    - [JDK 16 リリースの大幅な変更](https://docs.oracle.com/en/java/javase/17/migrate/significant-changes-jdk-release.html#GUID-327C39ED-C3FD-4637-906A-36C6697E85D5)
    - [JDK 15 リリースの大幅な変更](https://docs.oracle.com/en/java/javase/17/migrate/significant-changes-jdk-release.html#GUID-F0F718F5-13E5-45DC-BD8E-D1C455CA9977)
    - [JDK 14 リリースの大幅な変更](https://docs.oracle.com/en/java/javase/17/migrate/significant-changes-jdk-release.html#GUID-6B103F24-CA12-4645-A37C-7AB9E7EF1878)
    - [JDK 13 リリースの大幅な変更](https://docs.oracle.com/en/java/javase/17/migrate/significant-changes-jdk-release.html#GUID-E2EE0BC5-F0AA-4F83-A344-B9D5E424E040)
    - [JDK 12 リリースの大幅な変更](https://docs.oracle.com/en/java/javase/17/migrate/significant-changes-jdk-release.html#GUID-7D4768D0-738C-4554-BAC5-A8DBBEFA3857)
    - [JDK 11 リリースの大幅な変更](https://docs.oracle.com/en/java/javase/17/migrate/significant-changes-jdk-release.html#GUID-3BDB37C7-DF0C-44C7-AF3C-409F846DFF99)

* セキュリティ更新プログラム (Security Updates)
    - [JDK 17 のセキュリティ更新プログラム](https://docs.oracle.com/en/java/javase/17/migrate/security-updates.html#GUID-0A1C677D-F9A3-4B61-8D09-EFB793859789)
    - [JDK 16 のセキュリティ更新プログラム](https://docs.oracle.com/en/java/javase/17/migrate/security-updates.html#GUID-89D42765-3057-4054-9386-4748BC45585E)
    - [JDK 15 のセキュリティ更新プログラム](https://docs.oracle.com/en/java/javase/17/migrate/security-updates.html#GUID-9C6409FA-3EE2-4717-A1B4-B9A01690C081)
    - [JDK 14 のセキュリティ更新プログラム](https://docs.oracle.com/en/java/javase/17/migrate/security-updates.html#GUID-0AF2966D-5A34-4AC5-8FE1-D9E6E8788D55)
    - [JDK 13 のセキュリティ更新プログラム](https://docs.oracle.com/en/java/javase/17/migrate/security-updates.html#GUID-19C53993-A9C3-48EC-BD8E-3F3934CF9D6D)
    - [JDK 11 および JDK 12 のセキュリティ更新プログラム](https://docs.oracle.com/en/java/javase/17/migrate/security-updates.html#GUID-8C36237D-76BB-4ADD-B026-4EB3EAA6BE99)
    - [JDK 9 および JDK 10 のセキュリティ更新プログラム](https://docs.oracle.com/en/java/javase/17/migrate/security-updates.html#GUID-D6EE05FB-6791-43B3-A610-3F4416DEE508)

* 削除された API (Removed APIs)
    - [JDK 17 で削除された API](https://docs.oracle.com/en/java/javase/17/migrate/removed-apis.html#GUID-9EF06707-9441-433E-BA50-9DD99F1C07F4)
    - [JDK 16 で削除された API](https://docs.oracle.com/en/java/javase/17/migrate/removed-apis.html#GUID-902FAA85-CFCF-4FD4-A25D-5060C7DBE650)
    - [JDK 15 で削除された API](https://docs.oracle.com/en/java/javase/17/migrate/removed-apis.html#GUID-233853B8-0782-429E-BEF7-7532EE610E63)
    - [JDK 14 で削除された API](https://docs.oracle.com/en/java/javase/17/migrate/removed-apis.html#GUID-4BB7838C-7F93-4C3B-86AD-28FFE46F736B)
    - [JDK 13 で削除された API](https://docs.oracle.com/en/java/javase/17/migrate/removed-apis.html#GUID-D78F54F4-24CC-41BE-AD7A-A11B28FDDD33)
    - [JDK 12 で削除された API](https://docs.oracle.com/en/java/javase/17/migrate/removed-apis.html#GUID-A7ABC76A-5037-4052-8718-562923FC7028)
    - [JDK 11 で削除された API](https://docs.oracle.com/en/java/javase/17/migrate/removed-apis.html#GUID-4B613B7E-B150-4D0A-835C-2393C60BE1F8)
    - [JDK 10 で削除された API](https://docs.oracle.com/en/java/javase/17/migrate/removed-apis.html#GUID-246BC701-2A75-4F3F-B0E2-CB0F20E523B7)
    - [JDK 9  で削除された API](https://docs.oracle.com/en/java/javase/17/migrate/removed-apis.html#GUID-2A8D44E2-DB96-4C2D-9BE4-CFF9FC524397)

* 削除されたツールとコンポーネント (Removed Tools and Components)
    - [JDK 17 で削除されたツールとコンポーネント](https://docs.oracle.com/en/java/javase/17/migrate/removed-tools-and-components.html#GUID-D6C71B90-5F66-4426-899B-899B110CC64D)
    - [JDK 16 で削除されたツールとコンポーネント](https://docs.oracle.com/en/java/javase/17/migrate/removed-tools-and-components.html#GUID-F182E075-858A-4468-9434-8FC1704E7BB7)
    - [JDK 15 で削除されたツールとコンポーネント](https://docs.oracle.com/en/java/javase/17/migrate/removed-tools-and-components.html#GUID-BBCF36FE-C892-4769-95CB-AB3FFC3A3B13)
    - [JDK 14 で削除されたツールとコンポーネント](https://docs.oracle.com/en/java/javase/17/migrate/removed-tools-and-components.html#GUID-DC21025C-3236-4ACA-A23E-35B07BB7C21A)
    - [JDK 13 で削除されたツールとコンポーネント](https://docs.oracle.com/en/java/javase/17/migrate/removed-tools-and-components.html#GUID-7699DC53-53F9-4FB9-A548-05C58EC4F47F)
    - [JDK 12 で削除されたツールとコンポーネント](https://docs.oracle.com/en/java/javase/17/migrate/removed-tools-and-components.html#GUID-11F78105-D735-430D-92DD-6C37958FCBC3)
    - [JDK 11 で削除されたツールとコンポーネント](https://docs.oracle.com/en/java/javase/17/migrate/removed-tools-and-components.html#GUID-460800EF-A523-4B10-B694-E3536AC419C1)
    - [JDK 9 および JDK 10 で削除されたツールとコンポーネント](https://docs.oracle.com/en/java/javase/17/migrate/removed-tools-and-components.html#GUID-12237744-E23B-42F8-8892-26BA1BDD63F2)

 * Migrating From JDK 8 to Later JDK Releases

    - [Strong Encapsulation in the JDK](https://docs.oracle.com/en/java/javase/17/migrate/migrating-jdk-8-later-jdk-releases.html#GUID-7BB28E4D-99B3-4078-BDC4-FC24180CE82B)
    - [New Version-String Scheme](https://docs.oracle.com/en/java/javase/17/migrate/migrating-jdk-8-later-jdk-releases.html#GUID-3A71ECEF-5FC5-46FE-9BA9-88CBFCE828CB)        
    - [Changes to the Installed JDK/JRE Image](https://docs.oracle.com/en/java/javase/17/migrate/migrating-jdk-8-later-jdk-releases.html#GUID-D867DCCC-CEB5-4AFA-9D11-9C62B7A3FAB1)
    - [Deployment](https://docs.oracle.com/en/java/javase/17/migrate/migrating-jdk-8-later-jdk-releases.html#GUID-954372A5-5954-4075-A1AF-6A9168371246)
    - [Changes to Garbage Collection](https://docs.oracle.com/en/java/javase/17/migrate/migrating-jdk-8-later-jdk-releases.html#GUID-1F270BDA-50B0-49C8-807E-0B727CCC5169)
    - [Running Java Applets](https://docs.oracle.com/en/java/javase/17/migrate/migrating-jdk-8-later-jdk-releases.html#GUID-430961F0-C3DF-4F31-BC02-1FE5414B1062)
    - [Behavior Change in Regular Expression Matching](https://docs.oracle.com/en/java/javase/17/migrate/migrating-jdk-8-later-jdk-releases.html#GUID-7DACC239-E71D-4B89-B582-201EA7CEBC38)
    - [Security Manager Deprecated for Removal](https://openjdk.java.net/jeps/411)
