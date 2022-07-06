# Using GraalVM Enterprise in OCI DevOps Build Pipelines

This sample shows how to use GraalVM Enterprise Edition in OCI DevOps build pipelines to build a Java hello world application. You can use this approach to build any high-performance Java application with GraalVM Enterprise and OCI DevOps.

## What is GraalVM?

GraalVM is a high-performance JDK distribution that can accelerate any Java workload running on the HotSpot JVM.

GraalVM Native Image ahead-of-time compilation enables you to build lightweight Java applications that are smaller, faster, and use less memory and CPU. At build time, GraalVM Native Image analyzes a Java application and its dependencies to identify just what classes, methods, and fields are absolutely necessary and generates optimized machine code for just those elements.

GraalVM Enterprise Edition is available for use on Oracle Cloud Infrastructure (OCI) at no additional cost.

## What is OCI DevOps?

OCI DevOps services provide a continuous integration and deployment (CI/CD) platform for developers to automate the build, test, and deployment of software applications on Oracle Cloud.

OCI DevOps build and deployment pipelines reduce change-driven errors and decrease the time customers spend on building and deploying software. The service also provides private Git repositories to store your code and supports connections to external code repositories. 


## Updating the Build Specification File

To install and use GraalVM Enterprise in the DevOps build pipeline, update your build specification file as follows:

1. Add the following command to install the one or more required GraalVM Enterprise components. For example, this command installs Native Image along with the Java Development Kit (JDK) and other necessary dependencies.

    ```shell
    steps:
      - type: Command
        name: "Install the latest GraalVM Enterprise 22.x for Java 17 - JDK and Native Image"
        command: |
          yum -y install graalvm22-ee-17-native-image
    ```

2. Set the JAVA_HOME environment variable.

    ```shell
    env:
      variables:
        "JAVA_HOME" : "/usr/lib64/graalvm/graalvm22-ee-java17"
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
        name: "Example 2: Build a native executable with the installed GraalVM Enterprise 22.x for Java 17 - Native Image"
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
    EXEC:   graalvm22-ee-17-native-image.x86_64 0:22.1.0.1-1.el7                             
    EXEC:    
    EXEC: Dependency Installed:   
    EXEC:   glibc-static.x86_64 0:2.17-326.0.1.el7_9                                         
    EXEC:   graalvm22-ee-17-jdk.x86_64 0:22.1.0.1-1.el7                                      
    EXEC:   libstdc++-static.x86_64 0:4.8.5-44.0.3.el7                                       
    EXEC:   zlib-static.x86_64 0:1.2.7-20.el7_9                                              
    EXEC:    
    EXEC: Complete!
    ...
    ```

2. The native executable build log statements should be similar to:

    ```shell
    ...
    EXEC: ==================================================================
    EXEC: GraalVM Native Image: Generating 'my-app' (executable)...   
    EXEC: ==================================================================
    EXEC: [1/7] Initializing...                     (5.6s @ 0.11GB)   
    EXEC:  Version info: 'GraalVM 22.1.0.1 Java 17 EE'   
    EXEC:  C compiler: gcc (redhat, x86_64, 4.8.5)   
    EXEC:  Garbage collector: Serial GC   
    EXEC: [2/7] Performing analysis...  [******]    (9.5s @ 0.32GB)   
    EXEC:    1,880 (62.46%) of  3,010 classes reachable   
    EXEC:    1,684 (46.71%) of  3,605 fields reachable   
    EXEC:    7,784 (36.98%) of 21,049 methods reachable   
    EXEC:       21 classes,     0 fields, and   285 methods registered for reflection   
    EXEC:       48 classes,    32 fields, and    47 methods registered for JNI access   
    EXEC: [3/7] Building universe...                (1.1s @ 0.45GB)   
    EXEC: [4/7] Parsing methods...      [*]         (0.8s @ 0.58GB)   
    EXEC: [5/7] Inlining methods...     [****]      (1.2s @ 0.97GB)   
    EXEC: [6/7] Compiling methods...    [*****]     (21.2s @ 0.75GB)   
    EXEC: [7/7] Creating image...                   (0.9s @ 0.92GB)   
    EXEC:    2.62MB (46.31%) for code area:    3,708 compilation units   
    EXEC:    2.45MB (43.34%) for image heap:     945 classes and 38,518 objects   
    EXEC:  600.06KB (10.35%) for other data   
    EXEC:    5.66MB in total   
    EXEC: ------------------------------------------------------------------
    ...
    EXEC: ------------------------------------------------------------------
    EXEC: 0.9s (2.1% of total time) in 18 GCs | Peak RSS: 2.44GB | CPU load: 3.41   
    EXEC: ------------------------------------------------------------------
    EXEC: Produced artifacts:   
    EXEC:  /workspace/gvmee-yum/target/my-app (executable)   
    EXEC:  /workspace/gvmee-yum/target/my-app.build_artifacts.txt   
    EXEC: ==================================================================
    EXEC: Finished generating 'my-app' in 41.7s.   
    ...
    ```
