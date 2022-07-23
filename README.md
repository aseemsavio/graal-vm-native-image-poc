# graal-vm-native-image-poc

This project explains how `native-image` can be built from a single class Java Application.

To start, we `cd` into the src directory.
```
cd src
```

To compile the class
```
javac Main.java
```
This would result in a `Main.class` file in the same directory.

To run the class with the GraalVM JVM or with any JVM
```
java Main
```
Here's the response:
```
Hello World
```

Now, timing the execution on my M1 Macbook Pro.
```
time java Main
```
Here's the response:
```
Hello World
java Main  0.06s user 0.02s system 113% cpu 0.074 total
```

In the user mode (typically applications are run in user mode), the java application takes `~600ms` to start and run 
the simple hello world program.
This time is mostly to bring up the JVM. JVM applications are best suited for long-running applications like web servers.

Even though the JVM applications are fast in long-running use cases, they tend to have a higher startup time due to the
issue mentioned above. This can be rectified by creating a `native-image` of the application.

Graal VM's `native-image` utility compiles all the JDK classes and your classes into a self-contained binary that can be 
run without the JVM.

To create a native image,
```
native-image Main
```
This generates the following logs and two files - `main` executable and a `.txt` file.
```
aseemsavio@MacBook-Pro src % native-image Main
========================================================================================================================
GraalVM Native Image: Generating 'main' (executable)...
========================================================================================================================
[1/7] Initializing...                                                                                    (7.5s @ 0.17GB)
 Version info: 'GraalVM 22.1.0 Java 17 CE'
 C compiler: cc (apple, arm64, 13.1.6)
 Garbage collector: Serial GC
[2/7] Performing analysis...  [******]                                                                   (5.0s @ 0.96GB)
   2,849 (74.93%) of  3,802 classes reachable
   3,374 (51.27%) of  6,581 fields reachable
  12,862 (44.64%) of 28,811 methods reachable
      35 classes,     0 fields, and   353 methods registered for reflection
      57 classes,    59 fields, and    51 methods registered for JNI access
[3/7] Building universe...                                                                               (0.5s @ 1.16GB)
[4/7] Parsing methods...      [*]                                                                        (0.5s @ 0.56GB)
[5/7] Inlining methods...     [****]                                                                     (0.8s @ 1.42GB)
[6/7] Compiling methods...    [**]                                                                       (4.1s @ 1.41GB)
[7/7] Creating image...                                                                                  (1.3s @ 0.35GB)
   4.32MB (35.52%) for code area:    7,549 compilation units
   6.97MB (57.30%) for image heap:   1,704 classes and 93,017 objects
 893.30KB ( 7.17%) for other data
  12.16MB in total
------------------------------------------------------------------------------------------------------------------------
Top 10 packages in code area:                               Top 10 object types in image heap:
 663.16KB java.util                                          977.68KB byte[] for general heap data
 339.13KB java.lang                                          934.59KB byte[] for code metadata
 258.06KB java.text                                          876.72KB java.lang.String
 236.67KB java.util.regex                                    645.13KB java.lang.Class
 197.47KB com.oracle.svm.jni                                 531.76KB byte[] for java.lang.String
 190.89KB java.util.concurrent                               389.06KB java.util.HashMap$Node
 156.13KB com.oracle.svm.core.reflect                        222.58KB com.oracle.svm.core.hub.DynamicHubCompanion
 146.05KB java.math                                          168.16KB java.util.HashMap$Node[]
  98.20KB java.util.logging                                  163.67KB java.lang.String[]
  95.28KB com.oracle.svm.core.genscavenge                    156.47KB java.util.concurrent.ConcurrentHashMap$Node
      ... 115 additional packages                                 ... 774 additional object types
                                           (use GraalVM Dashboard to see all)
------------------------------------------------------------------------------------------------------------------------
                        0.8s (4.0% of total time) in 18 GCs | Peak RSS: 3.26GB | CPU load: 4.14
------------------------------------------------------------------------------------------------------------------------
Produced artifacts:
 /Users/aseemsavio/Projects/Java/graal-vm-native-image-poc/graal-vm-native-image-poc/src/main (executable)
 /Users/aseemsavio/Projects/Java/graal-vm-native-image-poc/graal-vm-native-image-poc/src/main.build_artifacts.txt
========================================================================================================================
Finished generating 'main' in 20.5s.

```

The generated executable can be run by just calling it.

```
./main
```

The response is
```
Hello World
```
Timing it, we can see that it took less than `1 ms`!!
```
aseemsavio@MacBook-Pro src % time ./main
Hello World
./main  0.00s user 0.00s system 80% cpu 0.010 total
aseemsavio@MacBook-Pro src % 
```