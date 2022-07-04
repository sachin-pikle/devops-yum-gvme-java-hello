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

Here's the complete [build specification](graal_spec.yaml) file.
