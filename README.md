# Using Oracle GraalVM in OCI DevOps Build Pipelines

This sample shows how to use Oracle GraalVM in OCI DevOps build pipelines to build a Java hello world application. You can use this approach to build any high performance Java application with Oracle GraalVM and OCI DevOps.

## What is GraalVM?

GraalVM is a high performance JDK distribution that can accelerate any Java workload running on the HotSpot JVM.

GraalVM Native Image ahead-of-time compilation enables you to build lightweight Java applications that are smaller, faster, and use less memory and CPU. At build time, GraalVM Native Image analyzes a Java application and its dependencies to identify just what classes, methods, and fields are absolutely necessary and generates optimized machine code for just those elements.

Oracle GraalVM is available for use on Oracle Cloud Infrastructure (OCI) at no additional cost.

## What is OCI DevOps?

OCI DevOps services provide a continuous integration and deployment (CI/CD) platform for developers to automate the build, test, and deployment of software applications on Oracle Cloud.

OCI DevOps build and deployment pipelines reduce change-driven errors and decrease the time customers spend on building and deploying software. The service also provides private Git repositories to store your code and supports connections to external code repositories. 


## Updating the Build Specification File

To install and use Oracle GraalVM in the DevOps build pipeline, update your build specification file as follows:

1. Add the following command to install the required Oracle GraalVM components. For example, this command installs Native Image along with the Java Development Kit (JDK) and other necessary dependencies.

    ```shell
    steps:
      - type: Command
        name: "Install the latest Oracle GraalVM for JDK 17 - JDK and Native Image"
        command: |
          yum -y install graalvm-17-native-image
    ```

2. Set the JAVA_HOME environment variable.

    ```shell
    env:
      variables:
        "JAVA_HOME" : "/usr/lib64/graalvm/graalvm-java17"
    ```

3. Set the PATH environment variable.

    ```shell
    env:
      variables:
        # PATH is a reserved variable and cannot be defined as a variable.
        # PATH can be changed in a build step and the change is visible in subsequent steps.
    
    steps:
      - type: Command
        name: "Set the PATH here"
        command: |
          export PATH=$JAVA_HOME/bin:$PATH
    ```

4. Build a native executable for your Java application.

    ```shell
    steps:
      - type: Command
        name: "Example 2: Build a native executable with the installed Oracle GraalVM for JDK 17 - Native Image"
        command: |
          mvn --no-transfer-progress -Pnative -DskipTests package
    ```


5. The native executable file is available under `target/my-app`.

    ```shell
    outputArtifacts:
      - name: app_native_executable
        type: BINARY
        location: target/my-app
    ```

Here's the complete [build specification](graal_spec.yaml) file.


## Build Logs

1. The `yum install` build log statements should be similar to:

    ```shell
    ...
    EXEC: Installed:   
    EXEC:   graalvm-17-native-image.x86_64 0:17.0.7-2.el7                                    
    EXEC:    
    EXEC: Dependency Installed:   
    EXEC:   glibc-static.x86_64 0:2.17-326.0.5.el7_9                                         
    EXEC:   graalvm-17-jdk.x86_64 0:17.0.7-2.el7                                             
    EXEC:   libstdc++-static.x86_64 0:4.8.5-44.0.3.el7                                       
    EXEC:   zlib-static.x86_64 0:1.2.7-21.el7_9                                              
    EXEC:    
    EXEC: Dependency Updated:   
    EXEC:   glibc.x86_64 0:2.17-326.0.5.el7_9                                                
    EXEC:   glibc-common.x86_64 0:2.17-326.0.5.el7_9                                         
    EXEC:   glibc-devel.x86_64 0:2.17-326.0.5.el7_9                                          
    EXEC:   glibc-headers.x86_64 0:2.17-326.0.5.el7_9                                        
    EXEC:    
    EXEC: Complete!   
    ...
    ```

2. The native executable build log statements should be similar to:

    ```shell
    ...
    EXEC: ========================================================================================================================   
    EXEC: GraalVM Native Image: Generating 'my-app' (static executable)...   
    EXEC: ========================================================================================================================   
    EXEC: [1/8] Initializing...                                                                                    (5.3s @ 0.15GB)   
    EXEC:  Java version: 17.0.7+8-LTS, vendor version: Oracle GraalVM 17.0.7+8.1   
    EXEC:  Graal compiler: optimization level: b, target machine: x86-64-v3, PGO: off   
    EXEC:  C compiler: gcc (redhat, x86_64, 4.8.5)   
    EXEC:  Garbage collector: Serial GC (max heap size: 80% of RAM)   
    EXEC: [2/8] Performing analysis...  [****]                                                                    (11.7s @ 0.23GB)   
    EXEC:    1,816 (59.62%) of  3,046 types reachable   
    EXEC:    1,700 (46.00%) of  3,696 fields reachable   
    EXEC:    7,709 (36.23%) of 21,276 methods reachable   
    EXEC:      615 types,     0 fields, and   277 methods registered for reflection   
    EXEC:       49 types,    32 fields, and    48 methods registered for JNI access   
    EXEC:        4 native libraries: dl, pthread, rt, z   
    EXEC: [3/8] Building universe...                                                                               (1.5s @ 0.47GB)   
    EXEC: [4/8] Parsing methods...      [*]                                                                        (1.2s @ 0.45GB)   
    EXEC: [5/8] Inlining methods...     [***]                                                                      (1.0s @ 0.33GB)   
    EXEC: [6/8] Compiling methods...    [***]                                                                      (6.4s @ 0.36GB)   
    EXEC: [7/8] Layouting methods...    [*]                                                                        (0.6s @ 0.43GB)   
    EXEC: [8/8] Creating image...       [*]                                                                        (1.4s @ 0.50GB)   
    EXEC:    1.69MB (32.80%) for code area:     4,225 compilation units   
    EXEC:    3.32MB (64.33%) for image heap:   48,540 objects and 1 resources   
    EXEC:  151.98kB ( 2.88%) for other data   
    EXEC:    5.16MB in total   
    EXEC: ------------------------------------------------------------------------------------------------------------------------   
    EXEC: Top 10 origins of code area:                                Top 10 object types in image heap:   
    EXEC:  833.67kB java.base                                          411.87kB byte[] for java.lang.String   
    EXEC:  766.58kB svm.jar (Native Image)                             376.74kB byte[] for code metadata   
    EXEC:   48.47kB com.oracle.svm.svm_enterprise                      323.39kB java.lang.String   
    EXEC:   18.29kB org.graalvm.nativeimage.base                       284.77kB java.lang.Class   
    EXEC:   15.48kB jdk.internal.vm.ci                                 251.81kB byte[] for general heap data   
    EXEC:   14.81kB org.graalvm.sdk                                    147.53kB java.util.HashMap$Node   
    EXEC:    3.92kB jdk.internal.vm.compiler                           111.71kB char[]   
    EXEC:    1.17kB jdk.proxy3                                          78.90kB java.lang.Object[]   
    EXEC:    1.15kB jdk.proxy1                                          70.94kB com.oracle.svm.core.hub.DynamicHubCompanion   
    EXEC:   360.00B jdk.proxy2                                          68.46kB byte[] for reflection metadata   
    EXEC:    54.00B for 1 more packages                                435.79kB for 516 more object types   
    EXEC: ------------------------------------------------------------------------------------------------------------------------   
    EXEC: Recommendations:   
    EXEC:  G1GC: Use the G1 GC ('--gc=G1') for improved latency and throughput.   
    EXEC:  PGO:  Use Profile-Guided Optimizations ('--pgo') for improved throughput.   
    EXEC:  HEAP: Set max heap for improved and more predictable memory usage.   
    EXEC:  CPU:  Enable more CPU features with '-march=native' for improved performance.   
    EXEC:  BRPT: Try out the new build reports with '-H:+BuildReport'.   
    EXEC: ------------------------------------------------------------------------------------------------------------------------   
    EXEC:                         1.8s (6.0% of total time) in 32 GCs | Peak RSS: 1.14GB | CPU load: 3.21   
    EXEC: ------------------------------------------------------------------------------------------------------------------------   
    EXEC: Produced artifacts:   
    EXEC:  /workspace/gvmee-yum/target/build.json (build_info)   
    EXEC:  /workspace/gvmee-yum/target/my-app (executable)   
    EXEC: ========================================================================================================================   
    EXEC: Finished generating 'my-app' in 29.7s.   
    ...
    ```
