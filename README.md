# GraalVM Hands-on Lab
_February 2022_

Table of Contents:

* **[Exercise 1: Requirements](#exercise-1-requirements)**
* **[Exercise 2: GraalVM Enterprise](#exercise-2-graalvm-enterprise)**
   * [Exercise 2.1: Setup GraalVM Enterprise Edition](#exercise-21-setup-graalvm-enterprise-edition)
   * [Exercise 2.2: High-performance modern JIT compiler for Java](#exercise-22-high-performance-modern-jit-compiler-for-java)
   * [Exercise 2.3: Ahead-of-Time (AOT) Compiler for Java Bytecode](#exercise-23-ahead-of-time-aot-compiler-for-java-bytecode)
   * [Exercise 2.4: Polyglot - Combine JavaScript, Java, and R](#exercise-24-polyglot---combine-javascript-java-and-r)
* **[Exercise 3: Microservices](#exercise-3-microservices)**
   * [Exercise 3.1: Creating native image inside Docker](#exercise-31-creating-native-image-inside-docker)
   * [Exercise 3.2: Sending a request to the application](#exercise-32-sending-a-request-to-the-application)
* **[Exercise 4: SpringBoot](#exercise-4-springboot)**
   * [Exercise 4.1: Clone the sample SpringBoot Application](#exercise-41-clone-the-sample-springboot-application)
   * [Exercise 4.2: Compile and run the application using GraalVM Native Image](#exercise-42-compile-and-run-the-application-using-graalvm-native-image)
   * [Exercise 4.3: Creating Docker Image](#exercise-43-creating-docker-image)
   * [Exercise 4.4: Deploy it to Kubernetes cluster](#exercise-44-deploy-it-to-kubernetes-cluster)


# Exercise 1: Requirements

In order to get yourself ready for this workshop, you need to prepare your machine/laptop to have the following requirements.

  * The supported OSes for this workshop are MacOS and Linux. Windows is supported by GraalVM but for this workshop we do not use Windows. This hands-on labs exercise have been tested with Oracle Linux 8.2, Ubuntu 20.04, Fedora 32 (with minor tweak due to CGroup v2 issue - see the workaround at the later part of this hands-on labs), MacOS Catalina 10.15.6 and MacOS BigSur 11.5.1.
  * Install the following tools : `git`, `curl`, `unzip`, `Docker`, `Apache Maven` and `your favourite IDE` (optional).
  * Internet connection. You will need to access some online Github repositories during workshop exercises.
  * Uninstall any JDK/OpenJDK that comes with the OS. Example Fedora 32 comes with OpenJDK 8.
    * On Fedora 32 execute ```sudo rpm -qa | grep java``` or ```sudo rpm -qa | grep jdk```, the output is something like (could be different from your machine) ```java-1.8.0-openjdk.x86_64``` and    uninstall using ```sudo yum remove java-1.8.0-openjdk.x86_64```.
  * You also need to install Docker and deploy local Docker Container Registry to try exercise 3 and 4.
  * You might also need to deploy local Kubernetes cluster for exercise 4.


# Exercise 2: GraalVM Enterprise

There are a lot of different parts to GraalVM, so while you may have heard of it, there are almost certainly things that it can do that you don't know about yet. In this workshop we'll go through some of the diverse features of GraalVM and show you what they can do for you.

In this workshop we will be using GraalVM Enterprise Edition 22.0.0.2 for JDK 11 which can be downloaded from [OTN - Oracle Technology Network](https://www.oracle.com/downloads/graalvm-downloads.html)

#### **_Important_** :

Everytime you see red computer icon ![user input](images/userinput.png) it means a command that you literally need to type from within your terminal.

## Exercise 2.1: Setup GraalVM Enterprise Edition

Below are the steps to setup **GraalVM Enterprise Edition 22.0.0.2 for JDK 11** which is the latest GraalVM version at the time I wrote this workshop materials.

* a) In order to get started with GraalVM Enterprise Edition, you will need to download it from [OTN - Oracle Technology Network](https://www.oracle.com/downloads/graalvm-downloads.html).

* b) Select Release Version 22.0.0.2, Java Version 11, and your OS (operating system) type.
If you are using MacOS (like I do), you can choose macOS for the OS. Another supported OS is Windows and Linux.
For this workshop we are only use either macOS or Linux. Windows has lesser features right now, therefore we don't use it now.

* c) Once you selected the OS, you can download 3 GraalVM components from OTN.

  1.  Oracle GraalVM Enterprise Edition Core
  2.  Oracle GraalVM Enterprise Edition Native Image
  3.  GraalVM LLVM Toolchain Plugin
  4.  Oracle GraalVM Enterprise Edition Node.js Runtime Plugin

  Beside the above 3 components we also need _GraalVM R Language Plugin_ component for this workshop.

  You can't download _GraalVM R Language Plugin_ from OTN, but you can install it online using ```gu``` utility.

  We will install it at later part of this workshop material.

  ![Download Picture 3](images/download-3.png)
  Optionally, you can also download Oracle GraalVM Enterprise Edition Python Language Plugin, Oracle GraalVM Enterprise Edition Ruby Language Plugin and Oracle GraalVM Enterprise Edition WebAssembly Language Plugin. But they are not required for this workshop.

   You need to login to OTN to be able to download the binaries. If you have an existing Oracle credential you can use it, but if not you can create one as seen from the following picture.

  ![Download Picture 4](images/download-page-04.png)

* d) Once downloaded successfully, you can extract it out using below commands

  * On MacOS

    ![user input](images/userinput.png)
    >```sh
    >tar -zxf graalvm-ee-java11-darwin-amd64-22.0.0.2.tar.gz
    >```

  * On Linux

     ![user input](images/userinput.png)
     >```sh
     >tar -zxf graalvm-ee-java11-linux-amd64-22.0.0.2.tar.gz
     >```

* e) It will create a new directory named "graalvm-ee-java11-22.0.0.2". Move it to any path that you want for example for MacOS I used to put it under ```/Library/Java/JavaVirtualMachines/```, or on Linux you can put it under ```/opt/```. This is will become your GraalVM installation directory.

  * On MacOS

    ![user input](images/userinput.png)
    >```sh
    >sudo mv graalvm-ee-java11-22.0.0.2 /Library/Java/JavaVirtualMachines/.
    >```

    So, your GraalVM installation directory on MacOS is ```/Library/Java/JavaVirtualMachines/graalvm-ee-java11-22.0.0.2/Contents/Home```

  * On Linux

     ![user input](images/userinput.png)
     >```sh
     >sudo mv graalvm-ee-java11-22.0.0.2 /opt/.
     >```

     And then your GraalVM installation directory on Linux is ```/opt/graalvm-ee-java11-22.0.0.2```

* f) Modify your terminal shell accordingly. Open your terminal and based on your shell type bash/zsh do the following.

  * On MacOS

    ![user input](images/userinput.png)

    for zsh:
    >```sh
    > vi ~/.zshrc
    >```

    for bash:
    >```sh
    > vi ~/.bashrc
    >```

    and add the following to ```~/.zshrc``` or ```~/.bashrc``` files:

    >```sh
    >export GRAALVM_HOME=/Library/Java/JavaVirtualMachines/graalvm-ee-java11-22.0.0.2/Contents/Home
    >export PATH=$PATH:$GRAALVM_HOME/bin
    >```

    Save it, and then source using the following command:

    ![user input](images/userinput.png)

    for zsh:
    >```sh
    > source ~/.zshrc
    >```

    for bash:
    >```sh
    > source ~/.bashrc
    >```

  * On Linux

    The same steps like MacOS above, except the ```GRAALVM_HOME``` directory is slightly different

    ![user input](images/userinput.png)
    >```sh
    >export GRAALVM_HOME=/opt/graalvm-ee-java11-22.0.0.2
    >export PATH=$PATH:$GRAALVM_HOME/bin
    >```

    Save it, and source it (see the above MacOS step).

* g) That's it. You have just installed GraalVM on your MacOS or Linux machine. Next 2 steps are verifying the GraalVM installation and then setup Native Image, LLVM toolchain and R components.

* h) Verifying GraalVM installation.

  You can verify that the GraalVM versions will be used with the following commands:

  ![user input](images/userinput.png)
  >```sh
  >java -version
  >```

  Should output:

  ```
  java version "11.0.14" 2022-01-18 LTS
  Java(TM) SE Runtime Environment GraalVM EE 22.0.0.2 (build 11.0.14+8-LTS-jvmci-22.0-b05)
  Java HotSpot(TM) 64-Bit Server VM GraalVM EE 22.0.0.2 (build 11.0.14+8-LTS-jvmci-22.0-b05, mixed mode, sharing)

  ```

  Verify GraalVM JavaScript version

  ![user input](images/userinput.png)
  >```sh
  >js --version
  >```

  Should output:

  ```
  GraalVM JavaScript (GraalVM EE Native 22.0.0.2)
  ```

* i) The final step of the setup is to install Native Image, LLVM toolchain, and R using GraalVM Utility ```gu```

   * On MacOS / Linux

      Assuming your default download directory is ```~/Downloads/```, you can run the following commands:

    ![user input](images/userinput.png)
    >```sh
    >gu install -L ~/Downloads/native-image-installable-svm-svmee-java11-darwin-amd64-22.0.0.2.jar
    >gu install -L ~/Downloads/llvm-toolchain-installable-java11-darwin-amd64-22.0.0.2.jar
    >gu install -L ~/Downloads/nodejs-installable-svm-svmee-java11-darwin-amd64-22.0.0.2.jar
    >gu install R
    >```

    **Configure FastR**

    After installing FastR component using ```gu install R``` which will download R binary file from github, you are encourage to configure it.
    Below is how you do it on MacOS and Linux:

    On Linux

    ![user input](images/userinput.png)
    >```sh
    >/opt/graalvm-ee-java11-22.0.0.2/jre/languages/R/bin/configure_fastr
    >```

    On MacOS

    ![user input](images/userinput.png)
    >```sh
    >/Library/Java/JavaVirtualMachines/graalvm-ee-java11-22.0.0.2/Contents/Home/languages/R/bin/configure_fastr
    >```

    The output is something like the following:

    On Linux

    ```
    The basic configuration of FastR was successfull.

    Note: if you intend to install R packages you may need additional dependencies.
    The following packages should cover depenedencies of the most commonly used R packages:
    On Debian based systems: apt-get install build-essential gfortran libxml2 libc++-dev
    On Oracle Linux: yum groupinstall 'Development Tools' && yum install gcc-gfortran

    Default personal library directory (/home/mluther/R/x86_64-pc-linux-gnu-library/fastr-22.0.0.2-4.0) does exist. Do you wish to create it? (Yy/Nn) y
    Creating personal library directory: /home/mluther/R/x86_64-pc-linux-gnu-library/fastr-22.0.0.2-4.0
    DONE

    ```

    On MacOS

    ```
    FastR should not have any requirements on MacOS.

    Note: if you intend to install R packages you may need additional dependencies.
    The most common dependency is GFortran, which must be of version 8.3.0 or later.
    See https://gcc.gnu.org/wiki/GFortranBinaries.
    If the 'gfortran' binary is not on the system path, you need to configure the full path to it in /Library/Java/JavaVirtualMachines/graalvm-ee-java11-22.0.0.2/Contents/Home/languages/R/etc/Makeconf (variable FC)

    Default personal library directory (/Users/mluther/R/x86_64-apple-darwin-library/fastr-22.0.0.2-4.0) does not exist.
    Do you wish to create it now? (Yy/Nn) Y
    Creating personal library directory: /Users/mluther/R/x86_64-apple-darwin-library/fastr-22.0.0.2-4.0
    DONE
    ```

    **Native Image prerequisite**

    Native Image requires ```glibc-devel, zlib-devel, and gcc``` libraries as seen [here](https://docs.oracle.com/en/graalvm/enterprise/22/docs/reference-manual/native-image/#prerequisites) to be installed on your MacOS or Linux machine. You can install those libraries using package manager available in your OS.

    **Oracle Linux** using ```yum``` package manager

    ![user input](images/userinput.png)
    >```sh
    >sudo yum install gcc glibc-devel zlib-devel
    >```

    **Ubuntu Linux** using ```apt-get``` package manager

    ![user input](images/userinput.png)
    >```sh
    >sudo apt-get install build-essential libz-dev zlib1g-dev
    >```

    **Other Linux** using ```rpm``` package manager

    ![user input](images/userinput.png)
    >```sh
    >sudo dnf install gcc glibc-devel zlib-devel libstdc++-static
    >```

    **MacOS**

    ![user input](images/userinput.png)
    >```sh
    >xcode-select --install
    >```


Verify the installation of NodeJS is successfull by typing the following command

![user input](images/userinput.png)
>```sh
>$GRAALVM_HOME/bin/node --version:graalvm
>```

It should gives you an output something like the following:

```
GraalVM EE Native Polyglot Engine Version 22.0.0.2
Java Version 11.0.14
Java VM Version GraalVM 22.0.0.2 Java 11 EE
GraalVM Home /Library/Java/JavaVirtualMachines/graalvm-ee-java11-22.0.0.2/Contents/Home
  Installed Languages:
    JavaScript version 22.0.0.2
  Installed Tools:
    Agent Script            version 1.1
    Code Coverage           version 0.1.0
    CPU Sampler             version 0.5.0
    CPU Tracer              version 0.3.0
    Debug Protocol Server   version 0.1
    heap                    version 22.0.0.2
    Heap Allocation Monitor version 0.1.0
    Insight                 version 1.1
    Chrome Inspector        version 0.1
    Language Server         version 0.1
    Memory Tracer           version 0.2
    Sandbox                 version 22.0.0.2
```

Check GraalVM Utility ```gu``` version

![user input](images/userinput.png)
>```sh
>gu --version
>```

Should output:

```
GraalVM Updater 22.0.0.2
```

Finally make sure all component are listed inside ```gu``` by running the following command

![user input](images/userinput.png)
>```sh
>gu list
>```

Should output:

```
ComponentId              Version             Component name                Stability                     Origin
---------------------------------------------------------------------------------------------------------------------------------
graalvm                  22.0.0.2            GraalVM Core                  Supported
R                        22.0.0.2            FastR                         Experimental                  github.com
espresso                 22.0.0.2            Java on Truffle               Supported
js                       22.0.0.2            Graal.js                      Supported
llvm-toolchain           22.0.0.2            LLVM.org toolchain            Supported                     github.com
native-image             22.0.0.2            Native Image                  Early adopter
nodejs                   22.0.0.2            Graal.nodejs                  Supported
ruby                     22.0.0.2            TruffleRuby                   Experimental
wasm                     22.0.0.2            GraalWasm                     Experimental
```    


Congratulation! You have successfully installed GraalVM Enterprise Edition along with its Native Image, LLVM toolchain, R and NodeJS components.

Next, we will be running some application on GraalVM Enterprise.


## Exercise 2.2: High-performance modern JIT compiler for Java

GraalVM in general can be ran in 2 different modes, first as a pure _**JIT compiler**_, and the other as _**AOT (Ahead-of-Time) compiler**_.

We will try out GraalVM AOT at the later part of this workshop, but now let's drill into *JIT compiler and how it can help to boost application performance*.

**:: Graal JIT ::**

In this exercise, we'll be using materials from the following article:
https://medium.com/graalvm/graalvm-ten-things-12d9111f307d

The simplified version of the source code repository can be found in this [Github repo](https://github.com/marthenlt/native-image-workshop).

You can simply clone the source code by using this command:

![user input](images/userinput.png)
>```sh
>git clone https://github.com/marthenlt/native-image-workshop.git
>```

Once you've cloned the above repo you can then change directory to ```native-image-workshop``` and unzip ```large.zip``` file. See the following commands:

![user input](images/userinput.png)
>```sh
>cd native-image-workshop
>unzip large.zip
>```

So if you do ```ls -al``` the output of your working directory is something like this :

```
drwxr-xr-x  13 mluther  staff        416 Sep  2 01:50 .
drwxr-xr-x   9 mluther  staff        288 Aug 28 13:04 ..
drwxr-xr-x  16 mluther  staff        512 Sep  2 01:50 .git
-rw-r--r--   1 mluther  staff         33 Aug 11 12:30 .gitignore
-rw-r--r--   1 mluther  staff        545 Aug 11 12:30 README.md
-rw-r--r--   1 mluther  staff       2341 Aug 25 23:06 Streams.java
-rw-r--r--   1 mluther  staff       1127 Aug 11 12:30 TopTen.java
-rw-r--r--   1 mluther  staff         81 Aug 11 12:30 c2.sh
-rw-r--r--   1 mluther  staff         59 Aug 11 12:30 graal.sh
-rwxr-xr-x   1 mluther  staff  151397500 Sep 20  2019 large.txt
-rw-r--r--   1 mluther  staff   40230188 Aug 11 12:30 large.zip
-rw-r--r--   1 mluther  staff       1024 Aug 11 12:30 small.txt
-rw-r--r--   1 mluther  staff         55 Aug 11 12:30 timer.bat
```

We'll use ```TopTen.java``` example program, which gives you the top-ten words in ```large.txt``` file (file size is round 150 MB).
It uses Stream Java API to traverse, sort and count all the words (total there are 22,377,500 words).

Below is the TopTen.java program looks like:

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.Arrays;
import java.util.function.Function;
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class TopTen {

    public static void main(String[] args) {
        Arrays.stream(args)
                .flatMap(TopTen::fileLines)
                .flatMap(line -> Arrays.stream(line.split("\\b")))
                .map(word -> word.replaceAll("[^a-zA-Z]", ""))
                .filter(word -> word.length() > 0)
                .map(word -> word.toLowerCase())
                .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()))
                .entrySet().stream()
                .sorted((a, b) -> -a.getValue().compareTo(b.getValue()))
                .limit(10)
                .forEach(e -> System.out.format("%s = %d%n", e.getKey(), e.getValue()));
    }

    private static Stream<String> fileLines(String path) {
        try {
            return Files.lines(Paths.get(path));
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

}
```

**:: Graal JIT - _Compile The TopTen.java Program_ ::**

GraalVM includes a `javac` compiler, but it isn't any different from the
standard one for the purposes of this demo, so you could use your system `javac`
instead if you wanted to.

To compile use the following command:

![user input](images/userinput.png)
>```sh
> javac TopTen.java
>```

If we run the `java` command included in GraalVM we'll be automatically using
the GraalVM JIT compiler - no extra configuration is needed. I'll use the `time`
command to get the real, wall-clock elapsed time it takes to run the entire
program from start to finish, rather than setting up a complicated
micro-benchmark.

Use below command to measure how long GraalVM Enterprise can finished running TopTen.java

![user input](images/userinput.png)
>```sh
> time java TopTen large.txt
>```

The output looks like the following, but the speed is really depends on your machine/laptop.

On Linux

```
sed = 502500
ut = 392500
in = 377500
et = 352500
id = 317500
eu = 317500
eget = 302500
vel = 300000
a = 287500
sit = 282500

real	0m21.411s
user	0m30.780s
sys	0m1.224s
```

On MacOS (on my MacOS machine)

```
sed = 502500
ut = 392500
in = 377500
et = 352500
id = 317500
eu = 317500
eget = 302500
vel = 300000
a = 287500
sit = 282500
java TopTen large.txt  11.62s user 0.49s system 114% cpu 10.535 total
```

GraalVM is written in Java, rather than C++ like most other JIT compilers for
Java. We think this allows us to improve it more quickly than existing
compilers, with powerful new optimisations such as partial escape analysis that
aren't available in the standard JIT compilers for HotSpot. This can make your
Java programs run significantly faster.

To run without the GraalVM JIT compiler to compare, we can use the flag
`-XX:-UseJVMCICompiler`. JVMCI is the interface between GraalVM and the JVM. You
could also compare against your standard JVM as well.

![user input](images/userinput.png)
>```sh
> time java -XX:-UseJVMCICompiler TopTen large.txt
>```

On Linux

```
sed = 502500
ut = 392500
in = 377500
et = 352500
id = 317500
eu = 317500
eget = 302500
vel = 300000
a = 287500
sit = 282500

real	0m32.080s
user	0m32.719s
sys	0m0.490s
```

On MacOS (on my MacOS machine)

```
sed = 502500
ut = 392500
in = 377500
et = 352500
id = 317500
eu = 317500
eget = 302500
vel = 300000
a = 287500
sit = 282500
java -XX:-UseJVMCICompiler TopTen large.txt  15.91s user 0.30s system 106% cpu 15.282 total
```

This shows GraalVM running our Java program in around two-thirds of the
wall-clock (real) time it takes to run it with a standard HotSpot compiler. In
an area where we are used to treating single-digit percentage increases in
performance as significant, this is a big-deal.

You'll still get a result better than HotSpot if you use the Community
Edition, but it won't be quite as a good as the Enterprise Edition.

Twitter is one of the companies [using Graal JIT in production
today](https://www.youtube.com/watch?v=OSyvidFXL7M), and they say that for them
it is paying off in terms of real money saved. Twitter are using Graal to run
Scala applications - Graal works at the level of JVM bytecode so it works for
any JVM language.

This is the first way you can use GraalVM - simply as a drop-in better JIT
compiler for your existing Java applications.


## Exercise 2.3: Ahead-of-Time (AOT) Compiler for Java Bytecode

We have learned from previous exercise that GraalVM Enterprise can boost Java program performance without changing any code.

In the next exercise, we will be using GraalVM Native Image to Ahead-of-Time compile Java Bytecode into a native binary executable file.

**:: Graal AOT ::**

The Java platform is particularly strong for long-running processes and peak
performance, but short-running processes can suffer from longer startup time and
relatively high memory usage.

For example, if we run the same application with a much smaller input text file called ```small.txt``` - around 1 KB instead of 150 MB,
then it seems to take an unreasonably long time, and quite a lot of memory at 70 MB, to run for such a small file. We use `-l` to print the
memory used as well as time used.


![user input](images/userinput.png)
>```sh
> /usr/bin/time -v java TopTen small.txt   # -l on Mac instead of -v
>```

```
sed = 6
sit = 6
amet = 6
mauris = 3
volutpat = 3
vitae = 3
dolor = 3
libero = 3
tempor = 2
suscipit = 2
	Command being timed: "java TopTen small.txt"
	User time (seconds): 0.39
	System time (seconds): 0.04
	Percent of CPU this job got: 168%
	Elapsed (wall clock) time (h:mm:ss or m:ss): 0:00.26
	Average shared text size (kbytes): 0
	Average unshared data size (kbytes): 0
	Average stack size (kbytes): 0
	Average total size (kbytes): 0
	Maximum resident set size (kbytes): 76116
...
```

GraalVM gives us a tool that solves this problem. We said that GraalVM is like a
compiler library and it can be used in many different ways. One of those is to
compile *ahead-of-time*, to a native executable image, instead of compiling
*just-in-time* at runtime. This is similar to how a conventional compiler like
`gcc` works.

**:: Graal AOT - _Creating Binary Executable Using Native Image_::**

Now let's creating our first binary executable file using GraalVM Native Image from an existing TopTen bytecode.
Execute below command to create a TopTen's native binary executable:

![user input](images/userinput.png)
>```sh
> native-image --no-fallback TopTen
>```

The output is something like the following:

```
========================================================================================================================
GraalVM Native Image: Generating 'topten'...
========================================================================================================================
[1/7] Initializing...                                                                                    (5.1s @ 0.08GB)
 Version info: 'GraalVM 22.0.0.2 Java 11 EE'
[2/7] Performing analysis...  [*****]                                                                    (6.4s @ 0.88GB)
   2,791 (81.90%) of  3,408 classes reachable
   3,445 (60.91%) of  5,656 fields reachable
  12,583 (71.94%) of 17,491 methods reachable
      32 classes,     0 fields, and   140 methods registered for reflection
      57 classes,    59 fields, and    51 methods registered for JNI access
[3/7] Building universe...                                                                               (0.6s @ 1.06GB)
[4/7] Parsing methods...      [*]                                                                        (0.6s @ 0.52GB)
[5/7] Inlining methods...     [****]                                                                     (0.8s @ 1.21GB)
[6/7] Compiling methods...    [****]                                                                    (12.7s @ 0.80GB)
[7/7] Creating image...                                                                                  (1.7s @ 1.20GB)
   5.93MB (46.49%) for code area:    6,630 compilation units
   6.06MB (47.46%) for image heap:   1,683 classes and 96,102 objects
 790.91KB ( 6.05%) for other data
  12.77MB in total
------------------------------------------------------------------------------------------------------------------------
Top 10 packages in code area:                               Top 10 object types in image heap:
 987.31KB java.util                                            1.85MB byte[] for general heap data
 471.23KB java.lang                                          588.54KB java.lang.String
 400.95KB java.text                                          505.74KB byte[] for java.lang.String
 351.81KB java.util.stream                                   442.23KB java.lang.Class
 294.81KB java.util.concurrent                               269.38KB java.util.HashMap$Node
 257.37KB java.util.regex                                    195.84KB java.util.concurrent.ConcurrentHashMap$Node
 205.81KB com.oracle.svm.jni                                 137.28KB char[]
 185.81KB java.util.logging                                  131.57KB java.util.HashMap$Node[]
 151.95KB java.io                                             83.95KB sun.util.locale.LocaleObjectCache$CacheEntry
 151.44KB java.math                                           76.24KB byte[] for method metadata
      ... 122 additional packages                                 ... 755 additional object types
                                           (use GraalVM Dashboard to see all)
------------------------------------------------------------------------------------------------------------------------
                        0.8s (2.7% of total time) in 20 GCs | Peak RSS: 4.92GB | CPU load: 8.48
------------------------------------------------------------------------------------------------------------------------
Produced artifacts:
 /Users/mluther/OracleDocs/Oracle Content/Marthen/Demo/exercise-delete/native-image-workshop/topten (executable)
 /Users/mluther/OracleDocs/Oracle Content/Marthen/Demo/exercise-delete/native-image-workshop/topten.build_artifacts.txt
========================================================================================================================
Finished generating 'topten' in 29.3s.
```

This command produces a native executable called `topten`. This executable isn't
a launcher for the JVM, it doesn't link to the JVM, and it doesn't bundle the
JVM in any way. `native-image` really does compile your Java code, and any Java
libraries you use, all the way down to simple machine code. For runtime
components like the garbage collector we are running our own new VM called the
SubstrateVM, which like GraalVM is also written in Java.

If you are curious (just like me) to know the breakdown of the code size per method in the final executable binary as well as in the image heap, you can re-execute the above command by adding `-H:+DashboardHeap` and `-H:+DashboardCode` options, as seen from below command:

```
native-image -H:DashboardDump=dashboard -H:+DashboardAll --no-fallback TopTen
```

It will produce BGV dump file called `dashboard.bgv` and print additional output in the terminal :

```
# Printing Dashboard Code-Breakdown BGV dump to: /Users/mluther/OracleDocs/Oracle Content/Marthen/Demo/exercise-delete/native-image-workshop/dashboard.bgv
[7/7] Creating image...                                                                                  (1.7s @ 0.68GB)
# Printing Dashboard Heap-Breakdown BGV dump to: /Users/mluther/OracleDocs/Oracle Content/Marthen/Demo/exercise-delete/native-image-workshop/dashboard.bgv
   5.93MB (46.50%) for code area:    6,630 compilation units
   6.06MB (47.48%) for image heap:   1,683 classes and 96,040 objects
 787.89KB ( 6.03%) for other data
  12.76MB in total
```

Now you can view it using GraalVM Dashboard at [https://www.graalvm.org/dashboard/](https://www.graalvm.org/dashboard/) by uploading the `dashboard.bgv` file.

![user input](images/dashboard-1.png)

![user input](images/dashboard-2.png)

![user input](images/dashboard-3.png)

![user input](images/dashboard-4.png)

![user input](images/dashboard-5.png)

If we look at the libraries which `topten` uses you can see they are only
standard system libraries. We could also move just this one file to a system
which has never had a JVM installed and run it there to verify it doesn't use a
JVM or any other files. It's also pretty small - this executable is less than 8
MB.

![user input](images/userinput.png)
>```sh
> ldd topten    # otool -L topten on Mac
>```

```
	linux-vdso.so.1 =>  (0x00007ffe1555b000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f6bda7c6000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f6bda5aa000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f6bda3a6000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f6bda190000)
	librt.so.1 => /lib64/librt.so.1 (0x00007f6bd9f88000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f6bd9d51000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f6bd9983000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f6bdaac8000)
	libfreebl3.so => /lib64/libfreebl3.so (0x00007f6bd9780000)
```

And here's the output from Mac:

```
topten:
	/usr/lib/libz.1.dylib (compatibility version 1.0.0, current version 1.2.11)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1281.100.1)
	/System/Library/Frameworks/Foundation.framework/Versions/C/Foundation (compatibility version 300.0.0, current version 1675.129.0)
	/System/Library/Frameworks/CoreFoundation.framework/Versions/A/CoreFoundation (compatibility version 150.0.0, current version 1675.129.0)
	/usr/lib/libobjc.A.dylib (compatibility version 1.0.0, current version 228.0.0)
```

Check the file size of the newly generated file

```
$ du -h topten
7.2M  topten

```

If we run it, we can see that it starts super fast, and uses less memory too. It's so fast that you won't notice the time taken to execute it. You don't feel that pause you always get when running a short-running command with the JVM.

![user input](images/userinput.png)
>```sh
> /usr/bin/time -v ./topten small.txt  # -l on Mac instead of -v
>```

```
sed = 6
sit = 6
amet = 6
mauris = 3
volutpat = 3
vitae = 3
dolor = 3
libero = 3
tempor = 2
suscipit = 2
	Command being timed: "./topten small.txt"
	User time (seconds): 0.00
	System time (seconds): 0.00
	Percent of CPU this job got: 50%
	Elapsed (wall clock) time (h:mm:ss or m:ss): 0:00.00
	Average shared text size (kbytes): 0
	Average unshared data size (kbytes): 0
	Average stack size (kbytes): 0
	Average total size (kbytes): 0
	Maximum resident set size (kbytes): 6192
```

As you can see from the above, that GraalVM AOT via Native Image requires only 6,1 MB memory shown by Maximum resident set size (kbytes) indicator.
Whereas GraalVM JIT requires 76.1 MB memory. Here we are looking at 12x smaller memory footprint requires by AOT'ed application.

Application start-up is also worth to mention, see the "Elapsed (wall clock)" from the 2 examples.
GraalVM JIT elapsed (wall clock) time is 26 ms (0:00.26)
GraalVM AOT elapsed (wall clock) time is 0 ms (0:00.00)
It was too fast until time utility count it as 0 ms.

Now let's see how to make AOT application throughput performance (TPS - transaction per second) even more faster.

In the next part of the AOT, we will create a **PGO (Profile Guided Optimisation)** file to make the native binary executable application's throughput faster.

**:: Graal AOT - _PGO (Profile Guided Optimisation)_::**

`PGO` is a way to _teach_ GraalVM AOT compiler to further optimize the throughput of the resulted native binary executable application.

For this exercise we will be using `Streams.java` progam as seen below.

```java
import java.util.Arrays;
import java.util.Random;

public class Streams {

	static final double EMPLOYMENT_RATIO = 0.5;
	static final int MAX_AGE = 100;
	static final int MAX_SALARY = 200_000;

	public static void main(String[] args) {

		int iterations;
		int dataLength;
		try {
			iterations = Integer.valueOf(args[0]);
			dataLength = Integer.valueOf(args[1]);
		} catch (Throwable ex) {
			System.out.println("expected 2 integer arguments: number of iterations, length of data array");
			return;
		}

		/* Create data set with a deterministic random seed. */
		Random random = new Random(42);
		Person[] persons = new Person[dataLength];
		for (int i = 0; i < dataLength; i++) {
			persons[i] = new Person(
					random.nextDouble() >= EMPLOYMENT_RATIO ? Employment.EMPLOYED : Employment.UNEMPLOYED,
					random.nextInt(MAX_SALARY),
					random.nextInt(MAX_AGE));
		}

		long totalTime = 0;
		for (int i = 1; i <= 20; i++) {
			long startTime = System.currentTimeMillis();

			long checksum = benchmark(iterations, persons);

			long iterationTime = System.currentTimeMillis() - startTime;
			totalTime += iterationTime;
			System.out.println("Iteration " + i + " finished in " + iterationTime + " milliseconds with checksum " + Long.toHexString(checksum));
		}
		System.out.println("TOTAL time: " + totalTime);
	}

	static long benchmark(int iterations, Person[] persons) {
		long checksum = 1;
		for (int i = 0; i < iterations; ++i) {
			double result = getValue(persons);

			checksum = checksum * 31 + (long) result;
		}
		return checksum;
	}

	/*
	 * The actual stream expression that we want to benchmark.
	 */
	public static double getValue(Person[] persons) {
		return Arrays.stream(persons)
				.filter(p -> p.getEmployment() == Employment.EMPLOYED)
				.filter(p -> p.getSalary() > 100_000)
				.mapToInt(Person::getAge)
				.filter(age -> age >= 40).average()
				.getAsDouble();
	}
}

enum Employment {
	EMPLOYED, UNEMPLOYED
}

class Person {
	private final Employment employment;
	private final int age;
	private final int salary;

	public Person(Employment employment, int height, int age) {
		this.employment = employment;
		this.salary = height;
		this.age = age;
	}

	public int getSalary() {
		return salary;
	}

	public int getAge() {
		return age;
	}

	public Employment getEmployment() {
		return employment;
	}
}
```

Compile it using below command:

![user input](images/userinput.png)
>```sh
> javac Streams.java
>```

And then create the native binary executable using below command:

![user input](images/userinput.png)
>```sh
> native-image --no-fallback Streams
>```

Run the native binary executable:

![user input](images/userinput.png)
>```sh
> ./streams 100000 200
>```

The above will create an array of 200 Person objects, with 100K iteration to calculate the average age that meet the criteria.

The output is something like the following:

```
Iteration 1 finished in 191 milliseconds with checksum e6e0b70aee921601
Iteration 2 finished in 172 milliseconds with checksum e6e0b70aee921601
Iteration 3 finished in 172 milliseconds with checksum e6e0b70aee921601
Iteration 4 finished in 174 milliseconds with checksum e6e0b70aee921601
Iteration 5 finished in 173 milliseconds with checksum e6e0b70aee921601
Iteration 6 finished in 172 milliseconds with checksum e6e0b70aee921601
Iteration 7 finished in 171 milliseconds with checksum e6e0b70aee921601
Iteration 8 finished in 172 milliseconds with checksum e6e0b70aee921601
Iteration 9 finished in 172 milliseconds with checksum e6e0b70aee921601
Iteration 10 finished in 173 milliseconds with checksum e6e0b70aee921601
Iteration 11 finished in 172 milliseconds with checksum e6e0b70aee921601
Iteration 12 finished in 175 milliseconds with checksum e6e0b70aee921601
Iteration 13 finished in 172 milliseconds with checksum e6e0b70aee921601
Iteration 14 finished in 171 milliseconds with checksum e6e0b70aee921601
Iteration 15 finished in 173 milliseconds with checksum e6e0b70aee921601
Iteration 16 finished in 171 milliseconds with checksum e6e0b70aee921601
Iteration 17 finished in 173 milliseconds with checksum e6e0b70aee921601
Iteration 18 finished in 173 milliseconds with checksum e6e0b70aee921601
Iteration 19 finished in 172 milliseconds with checksum e6e0b70aee921601
Iteration 20 finished in 172 milliseconds with checksum e6e0b70aee921601
TOTAL time: 3466
```

The result is 4686 miliseconds, and that'd be the throughput result before we optimise the `streams` binary executable application using PGO.

Next we will create a PGO file and create a new `streams` binary executable application.

There are 2 ways of creating a PGO file:
* Via `java -Dgraal.PGOInstrument`
* Via `native-image --pgo-instrument`


### Generating PGO file via `java -Dgraal.PGOInstrument`

In this exercise we will create a PGO file named ```streams.iprof``` via `java -Dgraal.PGOInstrument`, we can do that by executing below command:

Older GraalVM version use the following command:

![user input](images/userinput.png)
>```sh
> java -Dgraal.PGOInstrument=streams.iprof Streams 100000 200
>```

Newer GraalVM version such as GraalVM Enterprise Edition 22.0.0.2 that we are using now, you may use the following command:

![user input](images/userinput.png)
>```sh
> java -Dgraal.PGOInstrument=streams.iprof -Djvmci.CompilerIdleDelay=0 Streams 100000 200
>```

`-Djvmci.CompilerIdleDelay=0` param is to disable multiple compiler isolation when running PGOInstrument

A new file profiling file called `streams.iprof` will be created. You can do ```more streams.iprof``` to see what is the inside of it.

The output is something like this:

```
{
  "version": "0.1.0",
  "types": [
    { "id": 0, "typeName": "int" },
    { "id": 1, "typeName": "char" },
    { "id": 2, "typeName": "java.lang.String" },
    { "id": 3, "typeName": "void" },
    { "id": 4, "typeName": "java.lang.Object" },
    { "id": 5, "typeName": "boolean" },
    { "id": 6, "typeName": "java.util.Locale" },
    { "id": 7, "typeName": "java.util.stream.Sink" },
    { "id": 8, "typeName": "java.util.stream.AbstractPipeline" },
    { "id": 9, "typeName": "java.util.stream.ReferencePipeline$2" },
    { "id": 10, "typeName": "java.util.stream.IntPipeline$9" },
    { "id": 11, "typeName": "java.util.stream.ReferencePipeline$4" },
    { "id": 12, "typeName": "jdk.internal.org.objectweb.asm.ByteVector" },
    { "id": 13, "typeName": "[C" },
    { "id": 14, "typeName": "[B" },
    { "id": 15, "typeName": "sun.nio.cs.UTF_8$Encoder" },
    { "id": 16, "typeName": "java.util.stream.Sink$ChainedReference" },
    { "id": 17, "typeName": "java.util.stream.IntPipeline$9$1" },
    { "id": 18, "typeName": "java.util.stream.ReferencePipeline$2$1" },
    { "id": 19, "typeName": "java.util.stream.ReferencePipeline$4$1" },
    { "id": 20, "typeName": "java.util.stream.StreamShape" },
    { "id": 21, "typeName": "java.util.stream.ReferencePipeline$StatelessOp" },
    { "id": 22, "typeName": "java.util.stream.IntPipeline$StatelessOp" },
    { "id": 23, "typeName": "long" },
    { "id": 24, "typeName": "java.util.function.Consumer" },
    { "id": 25, "typeName": "java.util.Spliterators$ArraySpliterator" },
    { "id": 26, "typeName": "Streams$$Lambda$bcba0c9074f907ff1118ccf4b20382b375b44963" },
    { "id": 27, "typeName": "Streams$$Lambda$c53cfc0c6f6864e593fb5fc8f47a4c561a797150" },
    { "id": 28, "typeName": "java.util.Spliterator" },
    { "id": 29, "typeName": "java.util.stream.StreamOpFlag" },
    { "id": 30, "typeName": "[LPerson;" },
    { "id": 31, "typeName": "double" },
    { "id": 32, "typeName": "Streams" },
    { "id": 33, "typeName": "java.util.OptionalDouble" },
    { "id": 34, "typeName": "java.util.stream.IntPipeline" },
    { "id": 35, "typeName": "[Ljava.lang.Object;" },
    { "id": 36, "typeName": "java.util.Spliterators" },
    { "id": 37, "typeName": "java.util.stream.TerminalOp" },
    { "id": 38, "typeName": "java.util.stream.ReduceOps$7" },
    { "id": 39, "typeName": "java.util.stream.PipelineHelper" },
    { "id": 40, "typeName": "java.util.stream.ReduceOps$ReduceOp" },
    { "id": 41, "typeName": "Streams$$Lambda$eae7de59100ee7efdaf17ed2cdd0bde92ce7cd36" },
    { "id": 42, "typeName": "Streams$$Lambda$05225ea80029b82a7c73c194f3554dc78ecdb5db" },
    { "id": 43, "typeName": "java.util.stream.ReduceOps$7ReducingSink" },
    { "id": 44, "typeName": "java.util.stream.IntPipeline$$Lambda$28f2139532a62de6690b06ac907ce20a1b664ed0" },
    { "id": 45, "typeName": "java.lang.CharacterDataLatin1" },
    { "id": 46, "typeName": "java.lang.CharacterData" }
  ],
  "methods": [
    { "id": 0, "methodName": "charAt", "signature": [ 2, 1, 0 ] },
    { "id": 1, "methodName": "<init>", "signature": [ 4, 3 ] },
    { "id": 2, "methodName": "hashCode", "signature": [ 2, 0 ] },
    { "id": 3, "methodName": "indexOf", "signature": [ 2, 0, 0, 0 ] },
    { "id": 4, "methodName": "equals", "signature": [ 2, 5, 4 ] },
    { "id": 5, "methodName": "toUpperCase", "signature": [ 2, 2, 6 ] },
    { "id": 6, "methodName": "wrapSink", "signature": [ 8, 7, 7 ] },
    { "id": 7, "methodName": "putUTF8", "signature": [ 12, 12, 2 ] },
    { "id": 8, "methodName": "encode", "signature": [ 15, 0, 13, 0, 0, 14 ] },

...

```

Next we can then re-create the topten binary executable with our PGO `streams.iprof`, type the following command:

![user input](images/userinput.png)
>```sh
> native-image --pgo=streams.iprof Streams
>```

Then we execute the same benchmarking again..

![user input](images/userinput.png)
>```sh
> ./streams 100000 200
>```

The result is:

```
Iteration 1 finished in 169 milliseconds with checksum e6e0b70aee921601
Iteration 2 finished in 152 milliseconds with checksum e6e0b70aee921601
Iteration 3 finished in 149 milliseconds with checksum e6e0b70aee921601
Iteration 4 finished in 150 milliseconds with checksum e6e0b70aee921601
Iteration 5 finished in 150 milliseconds with checksum e6e0b70aee921601
Iteration 6 finished in 149 milliseconds with checksum e6e0b70aee921601
Iteration 7 finished in 148 milliseconds with checksum e6e0b70aee921601
Iteration 8 finished in 148 milliseconds with checksum e6e0b70aee921601
Iteration 9 finished in 149 milliseconds with checksum e6e0b70aee921601
Iteration 10 finished in 148 milliseconds with checksum e6e0b70aee921601
Iteration 11 finished in 151 milliseconds with checksum e6e0b70aee921601
Iteration 12 finished in 149 milliseconds with checksum e6e0b70aee921601
Iteration 13 finished in 148 milliseconds with checksum e6e0b70aee921601
Iteration 14 finished in 149 milliseconds with checksum e6e0b70aee921601
Iteration 15 finished in 149 milliseconds with checksum e6e0b70aee921601
Iteration 16 finished in 148 milliseconds with checksum e6e0b70aee921601
Iteration 17 finished in 149 milliseconds with checksum e6e0b70aee921601
Iteration 18 finished in 148 milliseconds with checksum e6e0b70aee921601
Iteration 19 finished in 149 milliseconds with checksum e6e0b70aee921601
Iteration 20 finished in 149 milliseconds with checksum e6e0b70aee921601
TOTAL time: 3001
```

The new benchmark (as a result of PGO) shows a better throughput of 3001 milliseconds compare to 3466 miliseconds which is showing more than 13.4% better throughput.


### Generating PGO file via `native-image --pgo-instrument`

Another way of creating a PGO file is using `native-image --pgo-instrument`.

This way you will create a `default.iprof` file from `native-image` tools directly. Execute below command:

![user input](images/userinput.png)
>```sh
>  native-image --pgo-instrument Streams
>```

Note that `default.iprof` PGO file is not immediately created after you ran the above command.

You need to run it the newly created binary `streams` executable file again. Execute below command:

![user input](images/userinput.png)
>```sh
> ./streams 100000 200
>```

Once finished you can see that `default.iprof` file is created. You can then do ```more default.iprof``` to see what is the inside of it.

Final step is to create an optimized TopTen native binary executable using below command:

![user input](images/userinput.png)
>```sh
>  native-image --pgo Streams
>```

And re-run our test again:

![user input](images/userinput.png)
>```sh
> ./streams 100000 200
>```

You will see more or less this output result (could be slightly different from within your machine) :

```
Iteration 1 finished in 27 milliseconds with checksum e6e0b70aee921601
Iteration 2 finished in 26 milliseconds with checksum e6e0b70aee921601
Iteration 3 finished in 27 milliseconds with checksum e6e0b70aee921601
Iteration 4 finished in 27 milliseconds with checksum e6e0b70aee921601
Iteration 5 finished in 27 milliseconds with checksum e6e0b70aee921601
Iteration 6 finished in 29 milliseconds with checksum e6e0b70aee921601
Iteration 7 finished in 27 milliseconds with checksum e6e0b70aee921601
Iteration 8 finished in 26 milliseconds with checksum e6e0b70aee921601
Iteration 9 finished in 27 milliseconds with checksum e6e0b70aee921601
Iteration 10 finished in 28 milliseconds with checksum e6e0b70aee921601
Iteration 11 finished in 27 milliseconds with checksum e6e0b70aee921601
Iteration 12 finished in 26 milliseconds with checksum e6e0b70aee921601
Iteration 13 finished in 26 milliseconds with checksum e6e0b70aee921601
Iteration 14 finished in 27 milliseconds with checksum e6e0b70aee921601
Iteration 15 finished in 27 milliseconds with checksum e6e0b70aee921601
Iteration 16 finished in 27 milliseconds with checksum e6e0b70aee921601
Iteration 17 finished in 27 milliseconds with checksum e6e0b70aee921601
Iteration 18 finished in 27 milliseconds with checksum e6e0b70aee921601
Iteration 19 finished in 27 milliseconds with checksum e6e0b70aee921601
Iteration 20 finished in 27 milliseconds with checksum e6e0b70aee921601
TOTAL time: 539
```

The latest benchmark shows even better throughput of 539 milliseconds compare to initial 3001 miliseconds which is showing around 82% better throughput, or almost 6X faster!

By now you have learned how to optimize an AOT binary executable file using PGO.

The `native-image` tool has some
[restrictions](https://github.com/oracle/graal/blob/master/substratevm/LIMITATIONS.md)
such as all classes having to be available during compilation, and some
limitations around reflection. It has some additional advantages over basic
compilation as well in that static initializers are run during compilation, so
you can reduce the work done each time an application loads.

This is a second way that you can use GraalVM -- as a way to distribute and run
your existing Java programs with a low-footprint and fast-startup. It also frees
you from configuration issues such as finding the right jar files at runtime,
and allows you to have smaller Docker images.


## Exercise 2.4: Polyglot - Combine JavaScript, Java, and R

GraalVM includes implementations of JavaScript, Ruby, R and Python on JVM. These are written using a new language implementation framework called
_**Truffle**_ that makes it possible to implement language interpreters that are
both simple and high performance. When you write a language interpreter using
Truffle, Truffle will automatically use GraalVM on your behalf to give you a JIT
compiler for your language. So GraalVM is not only a JIT compiler and
ahead-of-time native compiler for Java, it can also be a JIT compiler for
JavaScript, Ruby, R and Python through Truffle.

The languages in GraalVM aim to be drop-in replacements for your existing
languages. For example we can install a Node.js module:

![user input](images/userinput.png)
>```sh
> $GRAALVM_HOME/bin/npm install color
>```

```
+ color@4.2.0
added 6 packages from 6 contributors and audited 6 packages in 1.781s
found 0 vulnerabilities
```

We can write a little NodeJS program, name `color.js` using this module to convert an RGB HTML color to HSL (Hue, Saturation and Lightness):

```javascript
var Color = require('color');

process.argv.slice(2).forEach(function (val) {
  console.log(Color(val).hsl().string());
});
```

Then we can run that in the usual way:

![user input](images/userinput.png)
>```sh
> $GRAALVM_HOME/bin/node color.js '#42aaf4'
>```

```
hsl(204.89999999999998, 89%, 60.8%)
```

The languages in GraalVM work together - there's an API which lets you run code
from one language in another. This lets you write polyglot programs - programs
written in more than one language.

You might want to do this because you want to write the majority of your
application in one language, but there's a library in another language's
ecosystem that you'd like to use. For example, JavaScript doesn't have a great
solution for arbitrarily-large integers. I found several modules like
`big-integer` but these are all inefficient as they store components of the
number as JavaScript floating point numbers. Java's `BigInteger` class is more
efficient so let's use that instead to do some arbitrarily-large integer
arithmetic.

JavaScript also doesn't include any built-in support for drawing graphs, where R
does include excellent support for this. Let's use R's `svg` module to draw a
3D scatter plot of a trigonometric function.

In both cases we can use GraalVM's polyglot API, and we can just compose the
results from these other languages into JavaScript.

First, let's install the express npm package:

![user input](images/userinput.png)
>```sh
> $GRAALVM_HOME/bin/npm install express
>```

```
+ express@4.17.2
added 50 packages from 37 contributors and audited 61 packages in 6.646s
```

Next, let's create a simple NodeJS file `polyglot.js` using below code, you can copy and paste it in your IDE, and then run it.

![user input](images/userinput.png)
```js
const express = require('express')
const app = express()

const BigInteger = Java.type('java.math.BigInteger')

app.get('/', function (req, res) {
  var text = 'Hello World from Graal.js!<br> '

  // Using Java standard library classes
  text += BigInteger.valueOf(10).pow(100)
          .add(BigInteger.valueOf(43)).toString() + '<br>'

  // Using R interoperability to create graphs
  text += Polyglot.eval('R',
    `svg();
     require(lattice);
     x <- 1:100
     y <- sin(x/10)
     z <- cos(x^1.3/(runif(1)*5+10))
     print(cloud(x~y*z, main="cloud plot"))
     grDevices:::svg.off()
    `);

  res.send(text)
})

app.listen(3000, function () {
  console.log('Example app listening on port 3000!')
})
```

![user input](images/userinput.png)
>```sh
> $GRAALVM_HOME/bin/node --jvm --polyglot polyglot.js
>```

Open http://localhost:3000/ in your browser to see the result.

![polyglot.js](images/polyglot.png)

That's the third thing we can do with GraalVM which is to run programs written in multiple programming languages. With this Polyglot capability in GraalVM, you can use whichever language you think is best to solve your problem as well as whichever library you need, no matter which language it came from.


# Exercise 3: Microservices

Creating your first Micronaut GraalVM application

![micronaut](images/micronaut.png)

Next we'll learn how to create a Hello World Micronaut Graal application. To get started, we can go and visit [Micronaut GraalVM tutorial](https://guides.micronaut.io/latest/micronaut-creating-first-graal-app-gradle-java.html)

You can learn Micronaut framework such as creating a Service class with `@Introspected` and `@Singleton` annotations, and also creating a Controller class with `@Controller`, creating Native Image executable, as well as running a multi-stage Docker build for Native Image.

But due to we have a limited time in this workshop, you can do your due diligent of studying Micronaut framework in your time.

To save time you can download source code of Micronaut Graal application from https://guides.micronaut.io/latest/micronaut-creating-first-graal-app-gradle-java.zip

Once downloaded. Extract it to any directory you like. Here's the application's structure looks like in your terminal.

```
total 200
drwxr-xr-x  15 mluther  staff    480 Aug 21 14:58 .
drwxr-xr-x  15 mluther  staff    480 Aug 21 14:55 ..
-rw-r--r--@  1 mluther  staff    115 Aug 20 17:01 .gitignore
drwxr-xr-x   6 mluther  staff    192 Aug 21 14:56 .gradle
-rw-r--r--@  1 mluther  staff    467 Aug 20 17:01 README.md
-rw-r--r--@  1 mluther  staff    897 Aug 20 17:01 build.gradle
drwxr-xr-x@  3 mluther  staff     96 Aug 21 14:55 gradle
-rw-r--r--@  1 mluther  staff     23 Aug 20 17:01 gradle.properties
-rwxr-xr-x@  1 mluther  staff   8070 Aug 20 17:01 gradlew
-rwxr-xr-x@  1 mluther  staff   2763 Aug 20 17:01 gradlew.bat
-rw-r--r--@  1 mluther  staff    245 Aug 20 17:01 micronaut-cli.yml
-rw-r--r--@  1 mluther  staff  63896 Aug 21 14:54 micronaut-creating-first-graal-app-gradle-java.zip
-rw-r--r--@  1 mluther  staff     35 Aug 20 17:01 settings.gradle
drwxr-xr-x@  4 mluther  staff    128 Aug 21 14:55 src
```

Next is to build that application using Gradle. Run the following command to build the application.

![user input](images/userinput.png)
>```sh
> ./gradlew assemble
>```

Once finished you can run the application using a standard `java -jar`

![user input](images/userinput.png)
>```sh
> java -jar build/libs/micronautguide-0.1-all.jar
>```

The output is something like this:

```
 __  __ _                                  _
|  \/  (_) ___ _ __ ___  _ __   __ _ _   _| |_
| |\/| | |/ __| '__/ _ \| '_ \ / _` | | | | __|
| |  | | | (__| | | (_) | | | | (_| | |_| | |_
|_|  |_|_|\___|_|  \___/|_| |_|\__,_|\__,_|\__|
  Micronaut (v3.3.0)

19:24:34.299 [main] INFO  io.micronaut.runtime.Micronaut - Startup completed in 684ms. Server Running: http://localhost:8080
```

You can access the RESTful web service by using either your web browser or `curl`.

You can hit ```localhost:8080/conferences``` multiple times to see different output.

```
/Users/mluther/workshop/micronaut > curl localhost:8080/conferences/random
{"name":"Oracle Code One"}%

/Users/mluther/workshop/micronaut > curl localhost:8080/conferences/random
{"name":"Micronaut Summit"}%
```

Building Native Image in Micronaut is quite simple and straightforward. There is a Maven & Gradle Native Image Plugin available.

To convert this application to Native Image executable binary, you can run the following command:

![user input](images/userinput.png)
>```sh
> ./gradlew nativeCompile
>```

The output is something like the following in my MacBook running MacOS BigSur.

```
To honour the JVM settings for this build a single-use Daemon process will be forked. See https://docs.gradle.org/7.3.1/userguide/gradle_daemon.html#sec:disabling_the_daemon.
Daemon will be stopped at the end of the build

> Task :compileJava
Note: Creating bean classes for 2 type elements

> Task :generateResourcesConfigFile
[native-image-plugin] Resources configuration written into /Users/mluther/OracleDocs/Oracle Content/Marthen/Demo/exercise-delete/micronaut-creating-first-graal-app-gradle-java/build/native/generated/generateResourcesConfigFile/resource-config.json

> Task :nativeCompile
Toolchain detection is disabled, will use GraalVM from /Library/Java/JavaVirtualMachines/graalvm-ee-java11-22.0.0.2/Contents/Home/.
[native-image-plugin] Using executable path: /Library/Java/JavaVirtualMachines/graalvm-ee-java11-22.0.0.2/Contents/Home/bin/native-image
========================================================================================================================
GraalVM Native Image: Generating 'micronautguide'...
========================================================================================================================
[1/7] Initializing...                                                                                    (7.6s @ 0.14GB)
 Version info: 'GraalVM 22.0.0.2 Java 11 EE'
 2 user-provided feature(s)
  - io.micronaut.buffer.netty.NettyFeature
  - io.micronaut.http.netty.graal.HttpNettyFeature
[2/7] Performing analysis...  [**************]                                                          (39.9s @ 3.43GB)
  12,687 (90.44%) of 14,028 classes reachable
  17,971 (62.47%) of 28,767 fields reachable
  63,295 (61.31%) of 103,236 methods reachable
     689 classes,   238 fields, and 1,652 methods registered for reflection
      68 classes,    89 fields, and    55 methods registered for JNI access
[3/7] Building universe...                                                                               (2.8s @ 1.81GB)
[4/7] Parsing methods...      [*]                                                                        (1.5s @ 3.49GB)
[5/7] Inlining methods...     [*****]                                                                    (4.0s @ 2.09GB)
[6/7] Compiling methods...    [*******]                                                                 (51.0s @ 5.51GB)
[7/7] Creating image...                                                                                  (6.0s @ 3.47GB)
  37.08MB (58.26%) for code area:   38,743 compilation units
  21.67MB (34.05%) for image heap:   8,990 classes and 343,833 objects
   4.90MB ( 7.70%) for other data
  63.64MB in total
------------------------------------------------------------------------------------------------------------------------
Top 10 packages in code area:                               Top 10 object types in image heap:
   2.11MB sun.security.ssl                                     9.11MB byte[] for general heap data
   1.52MB java.util                                            2.67MB java.lang.Class
   1.50MB io.netty.buffer                                      1.88MB byte[] for java.lang.String
   1.38MB com.oracle.svm.core.reflect                          1.70MB java.lang.String
   1.10MB com.sun.crypto.provider                            440.18KB java.util.LinkedHashMap
 850.86KB java.util.concurrent                               407.70KB byte[] for method metadata
 788.22KB sun.security.x509                                  382.31KB java.util.HashMap$Node
 780.11KB java.lang                                          323.41KB int[]
 761.01KB io.netty.handler.codec.http2                       287.86KB com.oracle.svm.core.util.LazyFinalReference
 681.61KB reactor.core.publisher                             268.31KB java.util.concurrent.ConcurrentHashMap$Node
      ... 481 additional packages                                 ... 2798 additional object types
                                           (use GraalVM Dashboard to see all)
------------------------------------------------------------------------------------------------------------------------
                        9.4s (7.8% of total time) in 58 GCs | Peak RSS: 7.76GB | CPU load: 8.58
------------------------------------------------------------------------------------------------------------------------
Produced artifacts:
 /Users/mluther/OracleDocs/Oracle Content/Marthen/Demo/exercise-delete/micronaut-creating-first-graal-app-gradle-java/build/native/nativeCompile/micronautguide (executable)
 /Users/mluther/OracleDocs/Oracle Content/Marthen/Demo/exercise-delete/micronaut-creating-first-graal-app-gradle-java/build/native/nativeCompile/micronautguide.build_artifacts.txt
========================================================================================================================
Finished generating 'micronautguide' in 1m 58s.
[native-image-plugin] Native Image written to: /Users/mluther/OracleDocs/Oracle Content/Marthen/Demo/exercise-delete/micronaut-creating-first-graal-app-gradle-java/build/native/nativeCompile

BUILD SUCCESSFUL in 2m 12s
6 actionable tasks: 6 executed
```

The newly created native binary executable file called `micronautguide` will be located at `build/native/nativeCompile` folder of current application working directory.

You can run it as per other native command line tools.

![user input](images/userinput.png)
>```sh
> cd build/native/nativeCompile
> ./micronautguide
>```


## Exercise 3.1: Creating native image inside Docker

To build a Docker image of your Native Image, make sure you docker daemon has already running, you can then run below command:

![user input](images/userinput.png)
>```sh
> ./gradlew dockerBuildNative
>```
>>
>>If you are using Fedora 31 and above, you most likely failed executing the above command. The reason being Fedora 31 and above by default is using CGroup v2 which is not compatible with Docker at the time I wrote this hands-on labs.
>>On my Fedora 32 the script failed with message _**"OCI runtime create failed: this version of runc doesn't work on cgroups v2: unknown"**_
Here's the error output:
>>
>>```
>>Sending build context to Docker daemon  65.13MB
>>Step 1/10 : FROM oracle/graalvm-ce:20.1.0-java8 as graalvm
>>---> fa8819f7526a
>>Step 2/10 : RUN gu install native-image
>>---> Running in 97c5d3a66402
>>OCI runtime create failed: this version of runc doesn't work on cgroups v2: unknown
>>```
>>
>>The workaround can be find [here](https://www.linuxuprising.com/2019/11/how-to-install-and-use-docker-on-fedora.html)
>>
>> On your Fedora run the following commands:
>>
>>![user input](images/userinput.png)
>>```sh
>> sudo dnf install grubby
>> sudo grubby --update-kernel=ALL --args="systemd.unified_cgroup_hierarchy=0"
>> sudo reboot now  #reboot your machine
>>```
>>
>> Once your Fedora machine rebooted, try to execute `./gradlew dockerBuildNative` again :
>>
>>![user input](images/userinput.png)
>>```sh
>>./gradlew dockerBuildNative
>>```
>>
>> You should be able to build docker image now.


Here's the output of running the above command from my MacOS machine. Do keep in mind that it might different from your machine.

```
To honour the JVM settings for this build a single-use Daemon process will be forked. See https://docs.gradle.org/7.3.1/userguide/gradle_daemon.html#sec:disabling_the_daemon.
Daemon will be stopped at the end of the build

> Task :dockerfileNative
Dockerfile written to: /Users/mluther/OracleDocs/Oracle Content/Marthen/Demo/exercise-delete/micronaut-creating-first-graal-app-gradle-java/build/docker/native-main/DockerfileNative

> Task :dockerBuildNative
Building image using context '/Users/mluther/OracleDocs/Oracle Content/Marthen/Demo/exercise-delete/micronaut-creating-first-graal-app-gradle-java/build/docker/native-main'.
Using Dockerfile '/Users/mluther/OracleDocs/Oracle Content/Marthen/Demo/exercise-delete/micronaut-creating-first-graal-app-gradle-java/build/docker/native-main/DockerfileNative'
Using images 'micronautguide'.
Step 1/13 : FROM ghcr.io/graalvm/native-image:java11-21.3.0 AS graalvm
 ---> b8c385acd31c
Step 2/13 : WORKDIR /home/app
 ---> Running in 9266b1ab9ab7
Removing intermediate container 9266b1ab9ab7
 ---> 155394709b45
Step 3/13 : COPY layers/libs /home/app/libs
 ---> d54990c277d4
Step 4/13 : COPY layers/classes /home/app/classes
 ---> eae68d215c55
Step 5/13 : COPY layers/resources /home/app/resources
 ---> b5d9ec33748f
Step 6/13 : COPY layers/application.jar /home/app/application.jar
 ---> 86d4e42baabc
Step 7/13 : RUN mkdir /home/app/config-dirs
 ---> Running in 9827de85b193
Removing intermediate container 9827de85b193
 ---> f246cca96120
Step 8/13 : COPY config-dirs/generateResourcesConfigFile /home/app/config-dirs/generateResourcesConfigFile
 ---> bf1dd0dc98c7
Step 9/13 : RUN native-image -cp /home/app/libs/*.jar:/home/app/resources:/home/app/application.jar --no-fallback -H:Name=application -H:ConfigurationFileDirectories=/home/app/config-dirs/generateResourcesConfigFile -H:Class=example.micronaut.Application
 ---> Running in a74b751beaa8
[application:26]    classlist:   3,463.04 ms,  0.94 GB
[application:26]        (cap):     665.56 ms,  0.94 GB
[application:26]        setup:   2,999.96 ms,  0.94 GB
[application:26]     (clinit):   1,223.69 ms,  5.05 GB
[application:26]   (typeflow):   7,811.11 ms,  5.05 GB
[application:26]    (objects):  35,101.06 ms,  5.05 GB
[application:26]   (features):  12,022.77 ms,  5.05 GB
[application:26]     analysis:  60,109.24 ms,  5.05 GB
[application:26]     universe:   4,329.55 ms,  5.05 GB
[application:26]      (parse):   1,995.72 ms,  5.05 GB
[application:26]     (inline):   5,769.93 ms,  5.75 GB
[application:26]    (compile):  26,957.72 ms,  6.00 GB
[application:26]      compile:  38,210.88 ms,  6.00 GB
[application:26]        image:   5,236.84 ms,  6.00 GB
[application:26]        write:     658.84 ms,  6.00 GB
[application:26]      [total]: 115,459.17 ms,  6.00 GB
# Printing build artifacts to: /home/app/application.build_artifacts.txt
Removing intermediate container a74b751beaa8
 ---> e8a5e8eab708
Step 10/13 : FROM frolvlad/alpine-glibc:alpine-3.12
 ---> 39c4d33bd807
Step 11/13 : RUN apk update && apk add libstdc++
 ---> Using cache
 ---> 33c06101e4f9
Step 12/13 : COPY --from=graalvm /home/app/application /app/application
 ---> 5203dddf4fdc
Step 13/13 : ENTRYPOINT ["/app/application"]
 ---> Running in 129b230a35b3
Removing intermediate container 129b230a35b3
 ---> 6c59e16284b2
Successfully built 6c59e16284b2
Successfully tagged micronautguide:latest
Created image with ID '6c59e16284b2'.

BUILD SUCCESSFUL in 3m 15s
9 actionable tasks: 5 executed, 4 up-to-date
```

You can then run `docker images` command to check the newly created docker image.

![user input](images/userinput.png)
>```sh
> docker images
> REPOSITORY                                                               TAG                                                     IMAGE ID       CREATED         SIZE
> micronautguide                                                           latest                                                  6c59e16284b2   2 minutes ago   75.3MB
> ghcr.io/graalvm/native-image                                             java11-21.3.0                                           b8c385acd31c   2 months ago    832MB
> mysql                                                                    latest                                                  5a4e492065c7   5 months ago    514MB
> paketobuildpacks/run                                                     tiny-cnb                                                633bcda21ad9   5 months ago    17.4MB
>```

It will create an image with `micronautguide:latest` tag. To execute it, run the following command:

![user input](images/userinput.png)
>```sh
> sudo docker run -dp 3000:8080 --name testapp micronautguide
>```

You can then send the traffic by hitting local port 3000 to test the container.


## Exercise 3.2: Sending a request to the application

From another terminal, you can run a few cURL requests to test the application:

![user input](images/userinput.png)
>```sh
> time curl localhost:3000/conferences/random
>```

>```sh
> {"name":"Greach"}
> real    0m0.016s
> user    0m0.005s
> sys     0m0.004s
>```

Finally, stop the docker container:

![user input](images/userinput.png)
>```sh
> sudo docker stop testapp
>```


# Exercise 4: SpringBoot

In this exercise 4, we will explore Spring Boot example applications, run it in GraalVM as JIT then compile it to AOT via Native Image.

We are going to use Spring Boot PetClinic JPA that I forked and modified from [Spring Native Project](https://github.com/spring-projects-experimental/spring-native.git)


## Exercise 4.1: Clone my Spring Boot application

Clone the application source code from my [Github repository](https://github.com/marthenlt/graalvm-native-image-spring-petclinic.git).

![user input](images/userinput.png)
>```sh
>git clone https://github.com/marthenlt/graalvm-native-image-spring-petclinic.git
>```


## Exercise 4.2: Compile and run the application using GraalVM Native Image

In order to proceed with compiling and building this application, you need to have Apache Maven version 3.x installed in your machine.
If you type:

![user input](images/userinput.png)
>```sh
>mvn --version
>```

In my machine it shows below output:

```
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
Maven home: /Users/mluther/custom-libs/apache-maven-3.6.3
Java version: 1.8.0_301, vendor: Oracle Corporation, runtime: /Library/Java/JavaVirtualMachines/graalvm-ee-java8-20.3.2/Contents/Home/jre
Default locale: en_SG, platform encoding: UTF-8
OS name: "mac os x", version: "10.16", arch: "x86_64", family: "mac"
```

I have customized PetClinic JPA Spring Boot application to support 3 DBs, they are
- Oracle 19c Enterprise
- MySQL 8.0
- H2 v1.4.200

So you need to install any of these application to run it.

Once the DB server is up and running you can modify the DB connection settings inside `application.properties` file located under `src/main/resources/application.properties`.

Here's the `application.properties` looks like:

```
# select database to choose from, it supports H2, MySQL and Oracle (tested with Oracle 19c Enterprise)
# values either:
#     h2, mysql, oracle
database=mysql


# Spring datasource settings - H2 - Uncomment below line if you are using H2
# =====================================================================================
#spring.datasource.url=jdbc:h2:mem:petclinic
#spring.datasource.driver-class-name=org.h2.Driver
#spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
#spring.datasource.username=sa
#spring.datasource.password=password
# To enable H2 console, uncomment below line, and then you can navigate to http://localhost:8080/h2-console
#spring.h2.console.enabled=true


# Spring datasource settings - MySQL - Uncomment below line if you are using MySQL
# =====================================================================================
spring.datasource.url=jdbc:mysql://${MYSQL_HOST:localhost}:3306/petclinic?serverTimezone=UTC&useUnicode=yes&characterEncoding=UTF-8&useSSL=false&allowPublicKeyRetrieval=true
spring.jpa.database-platform=org.hibernate.dialect.MySQLDialect
spring.datasource.username=petclinic
spring.datasource.password=petclinic


# Spring datasource and JPA settings - Oracle - Uncomment below line if you are using Oracle
# ============================================================================================
#spring.datasource.url=jdbc:oracle:thin:@//${ORACLE_HOST:localhost}:1521/orcl
#spring.datasource.driver-class-name=oracle.jdbc.OracleDriver
#spring.jpa.database-platform=org.hibernate.dialect.Oracle12cDialect
#spring.datasource.username=system
#spring.datasource.password=oracle
#spring.datasource.continue-on-error=true
```

By default, my Spring Boot PetClinic application is using MySQL.

You can then compile and build it using Maven as standard Java application by running the following command.

![user input](images/userinput.png)
>```sh
>mvn clean package -DskipTests
>```

We just skip the tests for this exercise, to save up some times.
Once finished, we can run it using the following command:

![user input](images/userinput.png)
>```sh
>java -jar target/petclinic-jpa-0.0.1-SNAPSHOT.jar
>```

It will then run the fatjar as JIT mode on GraalVM. Below is the output looks like:

```
              |\      _,,,--,,_
             /,`.-'`'   ._  \-;;,_
  _______ __|,4-  ) )_   .;.(__`'-'__     ___ __    _ ___ _______
 |       | '---''(_/._)-'(_\_)   |   |   |   |  |  | |   |       |
 |    _  |    ___|_     _|       |   |   |   |   |_| |   |       | __ _ _
 |   |_| |   |___  |   | |       |   |   |   |       |   |       | \ \ \ \
 |    ___|    ___| |   | |      _|   |___|   |  _    |   |      _|  \ \ \ \
 |   |   |   |___  |   | |     |_|       |   | | |   |   |     |_    ) ) ) )
 |___|   |_______| |___| |_______|_______|___|_|  |__|___|_______|  / / / /
 ==================================================================/_/_/_/

:: Built with Spring Boot :: 2.4.0-M2


2021-08-22 23:27:22.607  INFO 47142 --- [           main] o.s.s.petclinic.PetClinicApplication     : Starting PetClinicApplication v0.0.1-SNAPSHOT using Java 1.8.0_301 on MacBook-Pro.local with PID 47142 (/Users/mluther/Downloads/delete/graalvm-native-image-spring-petclinic/target/petclinic-jpa-0.0.1-SNAPSHOT.jar started by mluther in /Users/mluther/Downloads/delete/graalvm-native-image-spring-petclinic)
2021-08-22 23:27:22.611  INFO 47142 --- [           main] o.s.s.petclinic.PetClinicApplication     : No active profile set, falling back to default profiles: default
2021-08-22 23:27:23.790  INFO 47142 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data JPA repositories in DEFERRED mode.
2021-08-22 23:27:23.911  INFO 47142 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 110 ms. Found 4 JPA repository interfaces.
2021-08-22 23:27:24.581  INFO 47142 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2021-08-22 23:27:24.686  INFO 47142 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2021-08-22 23:27:24.686  INFO 47142 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.38-dev]
2021-08-22 23:27:24.761  INFO 47142 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2021-08-22 23:27:24.761  INFO 47142 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 2088 ms
2021-08-22 23:27:25.209  INFO 47142 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPetclinicNativeImage - Starting...
2021-08-22 23:27:25.506  INFO 47142 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPetclinicNativeImage - Start completed.
2021-08-22 23:27:26.097  INFO 47142 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2021-08-22 23:27:26.316  INFO 47142 --- [         task-1] o.hibernate.jpa.internal.util.LogHelper  : HHH000204: Processing PersistenceUnitInfo [name: default]
2021-08-22 23:27:27.478  INFO 47142 --- [         task-1] org.hibernate.Version                    : HHH000412: Hibernate ORM core version 5.4.20.Final
2021-08-22 23:27:27.482  INFO 47142 --- [         task-1] org.hibernate.cfg.Environment            : HHH000205: Loaded properties from resource hibernate.properties: {hibernate.bytecode.use_reflection_optimizer=false, hibernate.bytecode.provider=none}
2021-08-22 23:27:27.630  INFO 47142 --- [         task-1] o.hibernate.annotations.common.Version   : HCANN000001: Hibernate Commons Annotations {5.1.0.Final}
2021-08-22 23:27:28.511  INFO 47142 --- [         task-1] org.hibernate.dialect.Dialect            : HHH000400: Using dialect: org.hibernate.dialect.MySQLDialect
2021-08-22 23:27:29.315  INFO 47142 --- [         task-1] o.h.tuple.entity.EntityMetamodel         : HHH000157: Lazy property fetching available for: org.springframework.samples.petclinic.owner.Owner
2021-08-22 23:27:29.553  INFO 47142 --- [         task-1] o.h.e.t.j.p.i.JtaPlatformInitiator       : HHH000490: Using JtaPlatform implementation: [org.hibernate.engine.transaction.jta.platform.internal.NoJtaPlatform]
2021-08-22 23:27:29.571  INFO 47142 --- [         task-1] j.LocalContainerEntityManagerFactoryBean : Initialized JPA EntityManagerFactory for persistence unit 'default'
2021-08-22 23:27:29.661  INFO 47142 --- [           main] o.s.b.a.e.web.EndpointLinksResolver      : Exposing 13 endpoint(s) beneath base path '/actuator'
2021-08-22 23:27:29.749  INFO 47142 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2021-08-22 23:27:29.750  INFO 47142 --- [           main] DeferredRepositoryInitializationListener : Triggering deferred initialization of Spring Data repositories…
2021-08-22 23:27:30.570  INFO 47142 --- [           main] DeferredRepositoryInitializationListener : Spring Data repositories initialized!
2021-08-22 23:27:30.585  INFO 47142 --- [           main] o.s.s.petclinic.PetClinicApplication     : Started PetClinicApplication in 8.499 seconds (JVM running for 9.324)
2021-08-22 23:27:40.295  INFO 47142 --- [nio-8080-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
2021-08-22 23:27:40.296  INFO 47142 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
2021-08-22 23:27:40.296  INFO 47142 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 0 ms

```

The application starts in 9.324 seconds.

You can then open your web browser and open `http://localhost:8080`

![petclinic-ui](images/petclinic-ui.png)

Feel free to play around with the application for a while.

Now let us compile it into native executable binary via GraalVM Native Image.

I have prepare a shell script called `compile.sh` to do this. However due to same changes in Native Image Reflection method signature in  `com.oracle.svm.hosted.config.ReflectionRegistryAdapter.registerField` we need to use older version of GraalVM Enterprise Edition version 20.3.3 and re-install Native Image 20.3.2 again.

The issue that am getting with GraalVM 21.x.x and above is :

```
Fatal error:java.lang.NoSuchMethodError: com.oracle.svm.hosted.config.ReflectionRegistryAdapter.registerField(Ljava/lang/Class;Ljava/lang/String;ZZ)V
	at org.springframework.graalvm.support.ReflectionHandler.register(ReflectionHandler.java:291)
	at org.springframework.graalvm.support.SpringFeature.duringSetup(SpringFeature.java:90)
	at com.oracle.svm.hosted.NativeImageGenerator.lambda$setupNativeImage$16(NativeImageGenerator.java:896)
	at com.oracle.svm.hosted.FeatureHandler.forEachFeature(FeatureHandler.java:71)
	at com.oracle.svm.hosted.NativeImageGenerator.setupNativeImage(NativeImageGenerator.java:896)
	at com.oracle.svm.hosted.NativeImageGenerator.doRun(NativeImageGenerator.java:530)
	at com.oracle.svm.hosted.NativeImageGenerator.run(NativeImageGenerator.java:491)
	at com.oracle.svm.hosted.NativeImageGeneratorRunner.buildImage(NativeImageGeneratorRunner.java:380)
	at com.oracle.svm.hosted.NativeImageGeneratorRunner.build(NativeImageGeneratorRunner.java:543)
	at com.oracle.svm.hosted.NativeImageGeneratorRunner.main(NativeImageGeneratorRunner.java:119)
```

Because `com.oracle.svm.hosted.config.ReflectionRegistryAdapter.registerField()` method signature has been changed from :

```
public void registerField(Class<?> type,
                          String fieldName,
                          boolean allowWrite,
                          boolean allowUnsafeAccess)
                   throws NoSuchFieldException
```

In [GraalVM version 20.x](https://javadoc.io/static/org.graalvm.nativeimage/svm/20.1.0/com/oracle/svm/hosted/config/ReflectionRegistryAdapter.html#registerField-java.lang.Class-java.lang.String-boolean-boolean-) , into :

```
public void registerField(Class<?> type,
                          String fieldName,
                          boolean allowWrite)
                   throws NoSuchFieldException
```

In [GraalVM version 21.x](https://javadoc.io/static/org.graalvm.nativeimage/svm/21.2.0/com/oracle/svm/hosted/config/ReflectionRegistryAdapter.html#registerField-java.lang.Class-java.lang.String-boolean-)


Please use GraalVM 20.3.2 to continue the rest of the exercise. It should work.


 * _or another alternative is to use [PetClinic JPA from upstream Spring Native project](https://github.com/spring-projects-experimental/spring-native/tree/main/samples/petclinic-jpa)_

Invoke the `compile.sh` script to start building. Do keep in mind, it might take a few minutes to fully compile the application. You can grab a coffee or have a toilet break after running below command.

![user input](images/userinput.png)
>```sh
>./compile.sh
>```

Below is the output of running `compile.sh` script successfully in my Mac.

```
Packaging petclinic-jpa with Maven
Aug 23, 2021 12:02:40 AM org.hibernate.Version logVersion
INFO: HHH000412: Hibernate ORM core version 5.4.20.Final
Unpacking petclinic-jpa-0.0.1-SNAPSHOT.jar
Compiling petclinic-jpa with GraalVM Version 20.3.2 (Java Version 1.8.0_291-b10)
SUCCESS
```

Noted that in my MacBook it took almost 9 minutes to complete.

You will find the newly created native executable file named `petclinic-jpa` inside `target` folder.

```
total 504392
drwxr-xr-x  13 mluther  staff        416 Aug 23 00:10 .
drwxr-xr-x  22 mluther  staff        704 Aug 23 00:02 ..
drwxr-xr-x   3 mluther  staff         96 Aug 23 00:02 .wro4j
drwxr-xr-x  12 mluther  staff        384 Aug 23 00:02 classes
drwxr-xr-x   3 mluther  staff         96 Aug 23 00:02 generated-sources
drwxr-xr-x   3 mluther  staff         96 Aug 23 00:02 generated-test-sources
drwxr-xr-x   3 mluther  staff         96 Aug 23 00:02 maven-archiver
drwxr-xr-x   3 mluther  staff         96 Aug 23 00:02 maven-status
drwxr-xr-x   6 mluther  staff        192 Aug 23 00:10 native-image
-rwxr-xr-x   1 mluther  staff  206679240 Aug 23 00:10 petclinic-jpa
-rw-r--r--   1 mluther  staff   51170983 Aug 23 00:02 petclinic-jpa-0.0.1-SNAPSHOT.jar
-rw-r--r--   1 mluther  staff     393791 Aug 23 00:02 petclinic-jpa-0.0.1-SNAPSHOT.jar.original
drwxr-xr-x   4 mluther  staff        128 Aug 23 00:02 test-classes
```

Do keep in mind that this `petclinic-jpa` file is platform specific binary, in my context it is MacOS binary.  

The size of `petclinic-jpa` is around 197MB. It has everything it needs to be run without JDK/JRE. You can run it as per normal CLI tool.

![user input](images/userinput.png)
>```sh
>target/petclinic-jpa
>```

The output from my Mac shows:

```

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::             (v2.4.0-M2)

2021-08-23 00:52:49.776  INFO 48786 --- [           main] o.s.s.petclinic.PetClinicApplication     : Starting PetClinicApplication using Java 1.8.0_291 on MacBook-Pro.local with PID 48786 (/Users/mluther/Downloads/delete/graalvm-native-image-spring-petclinic/target/petclinic-jpa started by mluther in /Users/mluther/Downloads/delete/graalvm-native-image-spring-petclinic)
2021-08-23 00:52:49.776  INFO 48786 --- [           main] o.s.s.petclinic.PetClinicApplication     : No active profile set, falling back to default profiles: default
2021-08-23 00:52:49.829  INFO 48786 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data JPA repositories in DEFERRED mode.
2021-08-23 00:52:49.831  INFO 48786 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 1 ms. Found 4 JPA repository interfaces.
2021-08-23 00:52:49.862  INFO 48786 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
Aug 23, 2021 12:52:49 AM org.apache.coyote.AbstractProtocol init
INFO: Initializing ProtocolHandler ["http-nio-8080"]
Aug 23, 2021 12:52:49 AM org.apache.catalina.core.StandardService startInternal
INFO: Starting service [Tomcat]
Aug 23, 2021 12:52:49 AM org.apache.catalina.core.StandardEngine startInternal
INFO: Starting Servlet engine: [Apache Tomcat/9.0.38-dev]
Aug 23, 2021 12:52:49 AM org.apache.catalina.core.ApplicationContext log
INFO: Initializing Spring embedded WebApplicationContext
2021-08-23 00:52:49.865  INFO 48786 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 88 ms
2021-08-23 00:52:49.870  WARN 48786 --- [           main] i.m.c.i.binder.jvm.JvmGcMetrics          : GC notifications will not be available because MemoryPoolMXBeans are not provided by the JVM
2021-08-23 00:52:49.894  INFO 48786 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPetclinicNativeImage - Starting...
2021-08-23 00:52:49.915  INFO 48786 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPetclinicNativeImage - Start completed.
2021-08-23 00:52:49.963  INFO 48786 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2021-08-23 00:52:49.965  INFO 48786 --- [         task-1] o.hibernate.jpa.internal.util.LogHelper  : HHH000204: Processing PersistenceUnitInfo [name: default]
2021-08-23 00:52:49.965  INFO 48786 --- [         task-1] org.hibernate.Version                    : HHH000412: Hibernate ORM core version 5.4.20.Final
2021-08-23 00:52:49.966  INFO 48786 --- [         task-1] org.hibernate.cfg.Environment            : HHH000205: Loaded properties from resource hibernate.properties: {hibernate.bytecode.use_reflection_optimizer=false, hibernate.bytecode.provider=none}
2021-08-23 00:52:49.966  INFO 48786 --- [         task-1] o.hibernate.annotations.common.Version   : HCANN000001: Hibernate Commons Annotations {5.1.0.Final}
2021-08-23 00:52:49.967  INFO 48786 --- [         task-1] org.hibernate.dialect.Dialect            : HHH000400: Using dialect: org.hibernate.dialect.MySQLDialect
2021-08-23 00:52:49.973  INFO 48786 --- [         task-1] o.h.tuple.entity.EntityMetamodel         : HHH000157: Lazy property fetching available for: org.springframework.samples.petclinic.owner.Owner
2021-08-23 00:52:49.978  INFO 48786 --- [         task-1] o.h.e.t.j.p.i.JtaPlatformInitiator       : HHH000490: Using JtaPlatform implementation: [org.hibernate.engine.transaction.jta.platform.internal.NoJtaPlatform]
2021-08-23 00:52:49.978  INFO 48786 --- [         task-1] j.LocalContainerEntityManagerFactoryBean : Initialized JPA EntityManagerFactory for persistence unit 'default'
2021-08-23 00:52:50.026  WARN 48786 --- [           main] ion$DefaultTemplateResolverConfiguration : Cannot find template location: classpath:/templates/ (please add some templates or check your Thymeleaf configuration)
2021-08-23 00:52:50.031  INFO 48786 --- [           main] o.s.b.a.e.web.EndpointLinksResolver      : Exposing 12 endpoint(s) beneath base path '/actuator'
Aug 23, 2021 12:52:50 AM org.apache.coyote.AbstractProtocol start
INFO: Starting ProtocolHandler ["http-nio-8080"]
2021-08-23 00:52:50.035  INFO 48786 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2021-08-23 00:52:50.035  INFO 48786 --- [           main] DeferredRepositoryInitializationListener : Triggering deferred initialization of Spring Data repositories…
2021-08-23 00:52:50.058  INFO 48786 --- [           main] DeferredRepositoryInitializationListener : Spring Data repositories initialized!
2021-08-23 00:52:50.060  INFO 48786 --- [           main] o.s.s.petclinic.PetClinicApplication     : Started PetClinicApplication in 0.333 seconds (JVM running for 0.335)
Aug 23, 2021 12:53:00 AM org.apache.catalina.core.ApplicationContext log
INFO: Initializing Spring DispatcherServlet 'dispatcherServlet'
2021-08-23 00:53:00.214  INFO 48786 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
2021-08-23 00:53:00.216  INFO 48786 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 2 ms
```

Look how fast the application starts now. It only took 0.335 second.

It is almost 28x faster compare to run the same application in JIT mode which took 9.324 seconds.


## Exercise 4.3: Creating Docker image

Now let's create a container image of the application and then push it to a Container Registry afterwards for future use.

In this workshop we will be using Docker for creating a container image. You can use other technology that suite you best. And also we will be using a local Container Registry from Docker Hub to store the image. Again, you can choose any other Container Registry if you like.

_Disclaimer_: This workshop will assume that you have installed all prerequisite softwares defined in [Exercise 1: Requirements](#exercise-1-requirements) above.

I will use Linux as the base OS of the image, so if your current machine operating system that you use to perform this exercise is also Linux, you can proceed with the following "Linux OS" steps, otherwise jump straight to "MacOS" steps.

### Linux OS

If you are using Linux OS to perform this exercise, you can run the following commands to create a container image of the application.

![user input](images/userinput.png)
>```sh
>sudo docker build -f Dockerfile-workshop -t localhost:5000/petclinic-mysql-native-image:0.12 .
>```

### MacOS

If you are using MacOS to perform this exercise, I have provided a script called `build-on-docker.sh` that will do the following :

  * Package the application via Maven
  * And then do a multi-stage Docker `build`. multistage will make sure the executable is compatible with Linux OS.

Here's the script file looks like:

```
#!/usr/bin/env bash

./mvnw package -DskipTests

#docker build -f Dockerfile-multistage -t marthenl/petclinic-mysql-native-image:0.12 .

#workshop..
docker build -f Dockerfile-multistage-workshop -t localhost:5000/petclinic-mysql-native-image:0.12 .
```

The script will look for a Dockerfile named `Dockerfile-multistage-workshop` and then will create a container with a tag `localhost:5000/petclinic-mysql-native-image:0.12`.

Run the script to start building the container image. Do take note that multistage build will take much longer time to create a container image because the AOT compilation process is taking place inside the container. It is recommended to allocate enough CPU and Memory for Docker daemon. As for my MacBook, I allocate 6 CPUs and 16 GBs of RAM and it took around 39 minutes to complete the multistage build.

![user input](images/userinput.png)
>```sh
>sudo ./build-on-docker.sh
>```

Wait until Docker build process is completed.

Once Docker build process is completed, a newly created container image named `localhost:5000/petclinic-mysql-native-image:0.12` will be available. You can check it with the following command :

![user input](images/userinput.png)
>```sh
>sudo docker images
>```

The output is something like this:

```
REPOSITORY                                                               TAG                                                     IMAGE ID       CREATED              SIZE
localhost:5000/petclinic-mysql-native-image                              0.12                                                    ca0aa9d22f3d   About a minute ago   245MB
micronautguide                                                           latest                                                  551f23d2b735   3 days ago           71.1MB
mysql                                                                    latest                                                  5a4e492065c7   8 days ago           514MB
```

As you can see the image size is around 245 MBs.

You can then push it to a local Container Registry to be deployed later on in Kubernetes.

![user input](images/userinput.png)
>```sh
>sudo docker push localhost:5000/petclinic-mysql-native-image:0.12
>```

You can also inspect the image by running `docker inspect <IMAGE_NAME>`

![user input](images/userinput.png)
>```sh
>sudo docker inspect localhost:5000/petclinic-mysql-native-image:0.12
>```

It will give you tons of information e.g. Id, RepoTags, Created on, number of layers, etc.

Just FYI - the above image is consist of 3 layers, which can be seen by running the following command :

![user input](images/userinput.png)
>```sh
>sudo docker inspect localhost:5000/petclinic-mysql-native-image:0.12 | grep -5 Layes
>```

Which will give you something like this :

```
},
"Name": "overlay2"
},
"RootFS": {
"Type": "layers",
"Layers": [
    "sha256:bc276c40b172b1c5467277d36db5308a203a48262d5f278766cf083947d05466",
    "sha256:e8df5395a1465805edb6f4efa8339182a5cc519dd6103d58109787c5a6042654",
    "sha256:bb565fc941b563b62a0ac15a3c1f3d21c90074db987a825e83c17ac0eb0b044f"
]
},
```

If you more sophisticated tools like [Dive](https://github.com/wagoodman/dive), you see better representation of the image as seen below.

![user input](images/userinput.png)
>```sh
>dive localhost:5000/petclinic-mysql-native-image:0.12
>```

![dive](images/dive.png)


As you can see our `petclinic-jpa` application is installed inside `/` folder.

Next we are going to deploy our container image to Kubernetes cluster.


## Exercise 4.4: Deploy it to Kubernetes cluster

For this workshop I am using Kubernetes v1.19.1. Other Kubernetes versions might also be supported, please refer to Kubernetes documentation.

I have prepared everything that we need to deploy our container on Kubernetes. You can browse to 'kubernetes' folder for more detail.

![user input](images/userinput.png)
>```sh
>cd kubernetes
>ls -al
>```

Here's the `kubernetes` folder looks like.

```
total 104
drwxr-xr-x  15 mluther  staff   480 Aug 25 21:28 .
drwxr-xr-x  27 mluther  staff   864 Aug 26 00:22 ..
-rwxr-xr-x   1 mluther  staff   189 Aug 22 23:10 cleanup.sh
-rwxr-xr-x   1 mluther  staff   130 Aug 22 23:10 delete-all-evicted-pods.sh
-rw-r--r--   1 mluther  staff   853 Aug 25 21:28 deploy-app.yaml
-rwxr-xr-x   1 mluther  staff   349 Aug 22 23:10 deploy-petclinic-native-as-knative-service.sh
-rwxr-xr-x   1 mluther  staff   154 Aug 22 23:10 deploy-petclinic-native-as-standard-pod.sh
-rw-r--r--   1 mluther  staff   296 Aug 22 23:10 external-mysql-svc.yaml
-rw-r--r--   1 mluther  staff   974 Aug 22 23:10 istio-minimal-operator.yaml
-rw-r--r--   1 mluther  staff  1063 Aug 22 23:10 knative-serving.yaml
-rw-r--r--   1 mluther  staff   846 Aug 22 23:10 mysql-deployment.yaml
-rw-r--r--   1 mluther  staff   411 Aug 22 23:10 mysql-pv.yaml
-rw-r--r--   1 mluther  staff   847 Aug 24 23:39 petclinic-app-deployment.yaml
-rw-r--r--   1 mluther  staff  4907 Aug 22 23:10 readme.md
drwxr-xr-x   8 mluther  staff   256 Aug 22 23:10 tekton
```

Just focus on 3 different Kubernetes YAML files:

  * `mysql-pv.yaml` : this is for MySQL Persistent Volume storage.
  * `mysql-deployment.yaml` : this is to deploy MySQL and create a service called 'mysql'
  * `deploy-app.yaml` : this YAML file is to deploy our application and then will create a NodePort service to access it.

Do the following steps to start deploying our container image in Kubernetes cluster.

Create a PV for MySQL, and deploy MySQL Pod. Run the following command:

![user input](images/userinput.png)
>```sh
>kubectl apply -f mysql-pv.yaml
>kubectl apply -f mysql-deployment.yaml
>```

Make sure the PV and Pod is appeared by executing the following command:

![user input](images/userinput.png)
>```sh
>kubectl get pv
>```

```
NAME              CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS   REASON   AGE
mysql-pv-volume   20Gi       RWO            Retain           Bound    default/mysql-pv-claim   manual                  2d
```

![user input](images/userinput.png)
>```sh
>kubectl get pod
>```

```
NAME                     READY   STATUS    RESTARTS   AGE
mysql-68579b78bb-t89vf   1/1     Running   0          3s
```

Intialize MySQL database to be used by our application.

Initialize DB and user access:

![user input](images/userinput.png)
>```sh
>kubectl exec -it mysql-68579b78bb-t89vf -- /bin/bash
>```


![user input](images/userinput.png)
>```sh
>>root@mysql-68579b78bb-t89vf:/# mysql -uroot -ppassword
>>

```
Warning: Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 7
Server version: 5.6.51 MySQL Community Server (GPL)

Copyright (c) 2000, 2021, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

Copy and paste the following:

```
CREATE DATABASE IF NOT EXISTS petclinic;

ALTER DATABASE petclinic
  DEFAULT CHARACTER SET utf8
  DEFAULT COLLATE utf8_general_ci;

GRANT ALL PRIVILEGES ON petclinic.* TO 'petclinic@%' IDENTIFIED BY 'petclinic';

grant all on petclinic.* to 'petclinic'@'%';
FLUSH PRIVILEGES;

use petclinic;

CREATE TABLE IF NOT EXISTS vets (
  id INT(4) UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
  first_name VARCHAR(30),
  last_name VARCHAR(30),
  INDEX(last_name)
) engine=InnoDB;

CREATE TABLE IF NOT EXISTS specialties (
  id INT(4) UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(80),
  INDEX(name)
) engine=InnoDB;

CREATE TABLE IF NOT EXISTS vet_specialties (
  vet_id INT(4) UNSIGNED NOT NULL,
  specialty_id INT(4) UNSIGNED NOT NULL,
  FOREIGN KEY (vet_id) REFERENCES vets(id),
  FOREIGN KEY (specialty_id) REFERENCES specialties(id),
  UNIQUE (vet_id,specialty_id)
) engine=InnoDB;

CREATE TABLE IF NOT EXISTS types (
  id INT(4) UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(80),
  INDEX(name)
) engine=InnoDB;

CREATE TABLE IF NOT EXISTS owners (
  id INT(4) UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
  first_name VARCHAR(30),
  last_name VARCHAR(30),
  address VARCHAR(255),
  city VARCHAR(80),
  telephone VARCHAR(20),
  INDEX(last_name)
) engine=InnoDB;

CREATE TABLE IF NOT EXISTS pets (
  id INT(4) UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(30),
  birth_date DATE,
  type_id INT(4) UNSIGNED NOT NULL,
  owner_id INT(4) UNSIGNED NOT NULL,
  INDEX(name),
  FOREIGN KEY (owner_id) REFERENCES owners(id),
  FOREIGN KEY (type_id) REFERENCES types(id)
) engine=InnoDB;

CREATE TABLE IF NOT EXISTS visits (
  id INT(4) UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
  pet_id INT(4) UNSIGNED NOT NULL,
  visit_date DATE,
  description VARCHAR(255),
  FOREIGN KEY (pet_id) REFERENCES pets(id)
) engine=InnoDB;

INSERT IGNORE INTO vets VALUES (1, 'James', 'Carter');
INSERT IGNORE INTO vets VALUES (2, 'Helen', 'Leary');
INSERT IGNORE INTO vets VALUES (3, 'Linda', 'Douglas');
INSERT IGNORE INTO vets VALUES (4, 'Rafael', 'Ortega');
INSERT IGNORE INTO vets VALUES (5, 'Henry', 'Stevens');
INSERT IGNORE INTO vets VALUES (6, 'Sharon', 'Jenkins');

INSERT IGNORE INTO specialties VALUES (1, 'radiology');
INSERT IGNORE INTO specialties VALUES (2, 'surgery');
INSERT IGNORE INTO specialties VALUES (3, 'dentistry');

INSERT IGNORE INTO vet_specialties VALUES (2, 1);
INSERT IGNORE INTO vet_specialties VALUES (3, 2);
INSERT IGNORE INTO vet_specialties VALUES (3, 3);
INSERT IGNORE INTO vet_specialties VALUES (4, 2);
INSERT IGNORE INTO vet_specialties VALUES (5, 1);

INSERT IGNORE INTO types VALUES (1, 'cat');
INSERT IGNORE INTO types VALUES (2, 'dog');
INSERT IGNORE INTO types VALUES (3, 'lizard');
INSERT IGNORE INTO types VALUES (4, 'snake');
INSERT IGNORE INTO types VALUES (5, 'bird');
INSERT IGNORE INTO types VALUES (6, 'hamster');

INSERT IGNORE INTO owners VALUES (1, 'George', 'Franklin', '110 W. Liberty St.', 'Madison', '6085551023');
INSERT IGNORE INTO owners VALUES (2, 'Betty', 'Davis', '638 Cardinal Ave.', 'Sun Prairie', '6085551749');
INSERT IGNORE INTO owners VALUES (3, 'Eduardo', 'Rodriquez', '2693 Commerce St.', 'McFarland', '6085558763');
INSERT IGNORE INTO owners VALUES (4, 'Harold', 'Davis', '563 Friendly St.', 'Windsor', '6085553198');
INSERT IGNORE INTO owners VALUES (5, 'Peter', 'McTavish', '2387 S. Fair Way', 'Madison', '6085552765');
INSERT IGNORE INTO owners VALUES (6, 'Jean', 'Coleman', '105 N. Lake St.', 'Monona', '6085552654');
INSERT IGNORE INTO owners VALUES (7, 'Jeff', 'Black', '1450 Oak Blvd.', 'Monona', '6085555387');
INSERT IGNORE INTO owners VALUES (8, 'Maria', 'Escobito', '345 Maple St.', 'Madison', '6085557683');
INSERT IGNORE INTO owners VALUES (9, 'David', 'Schroeder', '2749 Blackhawk Trail', 'Madison', '6085559435');
INSERT IGNORE INTO owners VALUES (10, 'Carlos', 'Estaban', '2335 Independence La.', 'Waunakee', '6085555487');

INSERT IGNORE INTO pets VALUES (1, 'Leo', '2000-09-07', 1, 1);
INSERT IGNORE INTO pets VALUES (2, 'Basil', '2002-08-06', 6, 2);
INSERT IGNORE INTO pets VALUES (3, 'Rosy', '2001-04-17', 2, 3);
INSERT IGNORE INTO pets VALUES (4, 'Jewel', '2000-03-07', 2, 3);
INSERT IGNORE INTO pets VALUES (5, 'Iggy', '2000-11-30', 3, 4);
INSERT IGNORE INTO pets VALUES (6, 'George', '2000-01-20', 4, 5);
INSERT IGNORE INTO pets VALUES (7, 'Samantha', '1995-09-04', 1, 6);
INSERT IGNORE INTO pets VALUES (8, 'Max', '1995-09-04', 1, 6);
INSERT IGNORE INTO pets VALUES (9, 'Lucky', '1999-08-06', 5, 7);
INSERT IGNORE INTO pets VALUES (10, 'Mulligan', '1997-02-24', 2, 8);
INSERT IGNORE INTO pets VALUES (11, 'Freddy', '2000-03-09', 5, 9);
INSERT IGNORE INTO pets VALUES (12, 'Lucky', '2000-06-24', 2, 10);
INSERT IGNORE INTO pets VALUES (13, 'Sly', '2002-06-08', 1, 10);

INSERT IGNORE INTO visits VALUES (1, 7, '2010-03-04', 'rabies shot');
INSERT IGNORE INTO visits VALUES (2, 8, '2011-03-04', 'rabies shot');
INSERT IGNORE INTO visits VALUES (3, 8, '2009-06-04', 'neutered');
INSERT IGNORE INTO visits VALUES (4, 7, '2008-09-04', 'spayed');

```

Type `exit` twice to quit from MySQL CLI and quit the interactive shell.

Done! You have just created a Database, User, Table and some dummy data to play with.

Now we are ready to deploy our container to Kubernetes. First lets examine the `deploy-app.yaml` file. Here's the file looks like.

```
apiVersion: v1
kind: Service
metadata:
  name: petclinic-graalvm-native-image
spec:
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  selector:
    app: petclinic-graalvm-native-image
  type: NodePort
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: petclinic-graalvm-native-image
  labels:
    app: petclinic-graalvm-native-image
spec:
  replicas: 1
  selector:
    matchLabels:
      app: petclinic-graalvm-native-image
  template:
    metadata:
      labels:
        app: petclinic-graalvm-native-image
    spec:
      containers:
        - name: petclinic-graalvm-native-image
          image: localhost:5000/petclinic-mysql-native-image:0.12
          env:
            - name: COLOR
              value: "blue"
            - name: VERSION
              value: "v0.12"
          ports:
            - containerPort: 8080
```

Look at the part where it defined the container location which is pointing to the container that we have pushed earlier on.

```
containers:
  - name: petclinic-graalvm-native-image
    image: localhost:5000/petclinic-mysql-native-image:0.12     <-- our container
```

Let's deploy our container by executing the following command:

![user input](images/userinput.png)
>```sh
>kubectl apply -f deploy-app.yaml
>```

Let's check it to see whether it can be deployed and run successfully.

![user input](images/userinput.png)
>```sh
>kubectl get pods
>```

The output is something like this:

```
NAME                                            READY   STATUS    RESTARTS   AGE
mysql-68579b78bb-t89vf                          1/1     Running   0          26h
petclinic-graalvm-native-image-c9b445f6-ffg2l   1/1     Running   0          6s
```

It simply says that our application is deployed successfully. Now let's test it, but first let us get the service URL to access the container from outside. Execute this command.

![user input](images/userinput.png)
>```sh
>kubectl get svc
>```

It will give something like the following:

```
NAME                                      TYPE           CLUSTER-IP       EXTERNAL-IP                                            PORT(S)                                      AGE
kubernetes                                ClusterIP      10.96.0.1        <none>                                                 443/TCP                                      93d
mysql                                     ClusterIP      None             <none>                                                 3306/TCP                                     93d
petclinic-graalvm-native-image            NodePort       10.99.210.191    <none>                                                 80:30941/TCP                                 4m12s
petclinic-knative-serving                 ExternalName   <none>           knative-local-gateway.istio-system.svc.cluster.local   80/TCP                                       93d
petclinic-knative-serving-00001           ClusterIP      10.102.171.156   <none>                                                 80/TCP                                       93d
petclinic-knative-serving-00001-private   ClusterIP      10.103.26.183    <none>                                                 80/TCP,9090/TCP,9091/TCP,8022/TCP,8012/TCP   93d
```

Look for the container ID `petclinic-graalvm-native-image`. From there you get to see that the service type for this container is NodePort using port 30941 to forward to the container.

You can open your web browser and go to `http://localhost:30941/`

![petclinic-on-container-1](images/petclinic-on-container-port-30941.png)

![petclinic-on-container-2](images/petclinic-on-container-port-30941-2.png)

Congratulations! You have deployed your Native Image application in a container and then push it to local Container Registry, and then eventually deployed it on Kubernetes cluster.


# Conclusions

You have seen GraalVM in action, Microservices with GraalVM using containerization technology. Should you need more information, feel free to contact me.
