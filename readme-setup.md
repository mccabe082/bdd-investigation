# **[Cucumber Tutorial](https://cucumber.io/docs/guides/10-minute-tutorial/) Setup**

Using Linux Mint OS - this is the only available system to me right now.

## **Java SE**
This tutorial requires Java Standard Edition. For this tutorial, I'm using Java SE 8u241.

### **Installing Java Development Kit**
download [JDK](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html) using the appropriate link. Unfortunately you need to create an Oracle account and provide personal information.  Have followed the instalation instruction from [here](https://linux4one.com/how-to-install-java-on-linux-mint-19/).

Create a new directory for java installation.
```bash
sudo mkdir /usr/local/oracle-java-8
```

Now extract downloaded Java file inside the recently created directory.
```bash
sudo tar -zxf jdk-8u241-linux-x64.tar.gz -C /usr/local/oracle-java-8
```
Now run the following command to create alternatives. This it will create symbolic links for default commands.
```bash
sudo update-alternatives --install "/usr/bin/java" "java" "/usr/local/oracle-java-8/jdk1.8.0_241/bin/java" 1500
```
```bash
sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/local/oracle-java-8/jdk1.8.0_241/bin/javac" 1500
```
```bash
sudo update-alternatives --install "/usr/bin/javaws" "javaws" "/usr/local/oracle-java-8/jdk1.8.0_241/bin/javaws" 1500
```

Check Current Java Version running following command.
```bash
java -version
```

Now to change the default Java version run the following command.
```bash
sudo update-alternatives --config java
```

### **Java Runtime Environment**

I think this comes bundled with JDK

## **Gradle**

Download the latest version of Gradle from [here](https://gradle.org/releases/).  Extract to the */opt* directory using the folowing commands...

```bash
$ mkdir /opt/gradle
$ unzip -d /opt/gradle gradle-6.3-bin.zip
$ ls /opt/gradle/gradle-6.3
```

Configure your PATH environment variable to include the bin directory of the unzipped distribution, add the following in */home/david/.bashrc*...
```bash
# DMC- Setting PATH so it includes gradle
export GRADLE_HOME=/opt/gradle/gradle-6.3/bin
export PATH=$PATH:$GRADLE_HOME
```

verify installation with the following command
```bash
gradle -v
```

## **IntelliJ Community Edition**

Download the community edition from [here](https://www.jetbrains.com/idea/download/#section=linux)

Extract the tarball to a directory that supports file execution.
```bash
sudo tar -xzf ideaIC-2019.3.4.tar.gz -C /opt
```
Open a console and cd into "{installation home}/bin" and type:
```bash
./idea.sh
```
to start the application. As a side effect, this will initialize various configuration files in the ~/.IdeaIC2019.3 directory.

# **Example Cucumber Project**

## **Initialise a Cucumber Project**

Here we initialise a java application using gradle:

```bash
david@david-Latitude-E5440:~/Leonardo-remote-work/cucumber-tutorial $ gradle init
Starting a Gradle Daemon, 2 incompatible and 1 stopped Daemons could not be reused, use --status for details

Select type of project to generate:
  1: basic
  2: application
  3: library
  4: Gradle plugin
Enter selection (default: basic) [1..4] 2

Select implementation language:
  1: C++
  2: Groovy
  3: Java
  4: Kotlin
  5: Swift
Enter selection (default: Java) [1..5] 3

Select build script DSL:
  1: Groovy
  2: Kotlin
Enter selection (default: Groovy) [1..2] 1

Select test framework:
  1: JUnit 4
  2: TestNG
  3: Spock
  4: JUnit Jupiter
Enter selection (default: JUnit 4) [1..4] 1

Project name (default: cucumber-tutorial): 

Source package (default: cucumber.tutorial): 


> Task :init
Get more help with your project: https://docs.gradle.org/6.3/userguide/tutorial_java_projects.html

BUILD SUCCESSFUL in 50s
2 actionable tasks: 2 executed
```

Update the build.gradle script

For Gradle 5.0 or more recent, you can add the following dependency block to build.gradle.
```groovy
dependencies {
    testCompile 'io.cucumber:cucumber-java:5.5.0'
}
```

For Gradle 5.0 or more recent, the following configuration must be added to build.gradle.
```groovy
configurations {
    cucumberRuntime {
        extendsFrom testImplementation
    }
}
```

Add the following Gradle cucumber task in build.gradle
```groovy
task cucumber() {
    dependsOn assemble, compileTestJava
    doLast {
        javaexec {
            main = "io.cucumber.core.cli.Main"
            classpath = configurations.cucumberRuntime + sourceSets.main.output + sourceSets.test.output
            args = ['--plugin', 'pretty', '--glue', 'gradle.cucumber', 'src/test/resources']
        }
    }
}
```
