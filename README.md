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
        name: "Install the latest Oracle GraalVM for JDK 21 (Native Image and JDK)"
        command: |
          yum -y install graalvm-21-native-image
    ```

2. Set the JAVA_HOME environment variable.

    ```shell
    env:
      variables:
        "JAVA_HOME" : "/usr/lib64/graalvm/graalvm-java21"
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
        name: "Example 2: Build a native executable with the installed Oracle GraalVM Native Image"
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
    EXEC:   graalvm-21-native-image.x86_64 0:21.0.7-1.el7                                    
    EXEC:    
    EXEC: Dependency Installed:   
    EXEC:   glibc-static.x86_64 0:2.17-326.0.9.el7_9.3                                       
    EXEC:   graalvm-21-jdk.x86_64 0:21.0.7-1.el7                                             
    EXEC:   libstdc++-static.x86_64 0:4.8.5-44.0.3.el7                                       
    EXEC:   zlib-static.x86_64 0:1.2.7-21.el7_9                                              
    EXEC:    
    EXEC: Dependency Updated:   
    EXEC:   glibc.x86_64 0:2.17-326.0.9.el7_9.3                                              
    EXEC:   glibc-common.x86_64 0:2.17-326.0.9.el7_9.3                                       
    EXEC:   glibc-devel.x86_64 0:2.17-326.0.9.el7_9.3                                        
    EXEC:   glibc-headers.x86_64 0:2.17-326.0.9.el7_9.3                                      
    EXEC:    
    EXEC: Complete! 
    ...
    ```

2. The native executable build log statements should be similar to:

    ```shell
    ...
    EXEC: ========================================================================================================================   
    EXEC: GraalVM Native Image: Generating 'my-app' (executable)...   
    EXEC: ========================================================================================================================   
    EXEC: For detailed information and explanations on the build output, visit:   
    EXEC: https://github.com/oracle/graal/blob/master/docs/reference-manual/native-image/BuildOutput.md   
    EXEC: ------------------------------------------------------------------------------------------------------------------------   
    EXEC: [1/8] Initializing...                                                                                    (3.1s @ 0.08GB)   
    EXEC:  Java version: 21.0.7+8-LTS, vendor version: Oracle GraalVM 21.0.7+8.1   
    EXEC:  Graal compiler: optimization level: b, target machine: x86-64-v3, PGO: off   
    EXEC:  C compiler: gcc (redhat, x86_64, 4.8.5)   
    EXEC:  Garbage collector: Serial GC (max heap size: 80% of RAM)   
    EXEC:  1 user-specific feature(s):   
    EXEC:  - com.oracle.svm.thirdparty.gson.GsonFeature   
    EXEC: ------------------------------------------------------------------------------------------------------------------------   
    EXEC:  2 experimental option(s) unlocked:   
    EXEC:  - '-H:BuildOutputJSONFile' (origin(s): command line)   
    EXEC:  - '-H:+StaticExecutableWithDynamicLibC' (origin(s): command line)   
    EXEC: ------------------------------------------------------------------------------------------------------------------------   
    EXEC: Build resources:   
    EXEC:  - 5.11GB of memory (75.6% of 6.76GB system memory, determined at start)   
    EXEC:  - 4 thread(s) (100.0% of 4 available processor(s), determined at start)   
    EXEC: [2/8] Performing analysis...  [*****]                                                                    (8.4s @ 0.18GB)   
    EXEC:     2,020 reachable types   (60.5% of    3,337 total)   
    EXEC:     1,898 reachable fields  (45.0% of    4,222 total)   
    EXEC:     8,773 reachable methods (36.7% of   23,875 total)   
    EXEC:       723 types,    37 fields, and   332 methods registered for reflection   
    EXEC:        49 types,    33 fields, and    48 methods registered for JNI access   
    EXEC:         4 native libraries: dl, pthread, rt, z   
    EXEC: [3/8] Building universe...                                                                               (1.1s @ 0.24GB)   
    EXEC: [4/8] Parsing methods...      [*]                                                                        (0.9s @ 0.25GB)   
    EXEC: [5/8] Inlining methods...     [***]                                                                      (0.9s @ 0.27GB)   
    EXEC: [6/8] Compiling methods...    [**]                                                                       (4.3s @ 0.23GB)   
    EXEC: [7/8] Laying out methods...   [*]                                                                        (0.5s @ 0.29GB)   
    EXEC: [8/8] Creating image...       [*]                                                                        (1.1s @ 0.15GB)   
    EXEC:    1.86MB (31.99%) for code area:     4,670 compilation units   
    EXEC:    3.27MB (56.02%) for image heap:   52,925 objects and 43 resources   
    EXEC:  715.69kB (11.99%) for other data   
    EXEC:    5.83MB in total   
    EXEC: ------------------------------------------------------------------------------------------------------------------------   
    EXEC: Top 10 origins of code area:                                Top 10 object types in image heap:   
    EXEC:  918.10kB java.base                                          690.03kB byte[] for java.lang.String   
    EXEC:  787.34kB svm.jar (Native Image)                             571.49kB byte[] for code metadata   
    EXEC:   83.42kB com.oracle.svm.svm_enterprise                      382.84kB heap alignment   
    EXEC:   19.45kB org.graalvm.nativeimage.base                       361.92kB java.lang.String   
    EXEC:   16.98kB jdk.internal.vm.ci                                 317.67kB java.lang.Class   
    EXEC:   15.33kB jdk.proxy2                                         168.34kB java.util.HashMap$Node       
    EXEC:   14.47kB org.graalvm.collections                            114.01kB char[]   
    EXEC:   13.89kB jdk.proxy1                                          81.83kB java.lang.Object[]   
    EXEC:    4.85kB jdk.internal.vm.compiler                            80.70kB byte[] for reflection metadata   
    EXEC:    3.62kB jdk.proxy3                                          78.91kB com.oracle.svm.core.hub.DynamicHubCompanion   
    EXEC:   471.00B for 1 more packages                                496.27kB for 532 more object types   
    EXEC:                               Use '-H:+BuildReport' to create a report with more details.   
    EXEC: ------------------------------------------------------------------------------------------------------------------------   
    EXEC: Security report:   
    EXEC:  - Binary does not include Java deserialization.   
    EXEC:  - Use '--enable-sbom' to embed a Software Bill of Materials (SBOM) in the binary.   
    EXEC: ------------------------------------------------------------------------------------------------------------------------   
    EXEC: Recommendations:   
    EXEC:  G1GC: Use the G1 GC ('--gc=G1') for improved latency and throughput.   
    EXEC:  PGO:  Use Profile-Guided Optimizations ('--pgo') for improved throughput.   
    EXEC:  INIT: Adopt '--strict-image-heap' to prepare for the next GraalVM release.   
    EXEC:  HEAP: Set max heap for improved and more predictable memory usage.   
    EXEC:  CPU:  Enable more CPU features with '-march=native' for improved performance.   
    EXEC: ------------------------------------------------------------------------------------------------------------------------   
    EXEC:                         1.8s (8.3% of total time) in 101 GCs | Peak RSS: 0.71GB | CPU load: 3.27   
    EXEC: ------------------------------------------------------------------------------------------------------------------------   
    EXEC: Produced artifacts:   
    EXEC:  /workspace/gvmee-yum/build.json (build_info)   
    EXEC:  /workspace/gvmee-yum/target/my-app (executable)   
    EXEC: ========================================================================================================================   
    EXEC: Finished generating 'my-app' in 20.6s.   
    ...
    ```
