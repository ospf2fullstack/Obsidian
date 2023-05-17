# Java
## Installation
```
/$ sudo curl https://download.java.net/java/GA/jdk20/bdc68b4b9cbc4ebcb30745c85038d91d/36/GPL/openjdk-20_linux-x64_bin.tar.gz --output /opt/openjdk-20_linux-x64_bin.tar.gz -yes

/$ sudo tar -xf /opt/openjdk-20_linux-x64_bin.tar.gz -C /opt/

```
## Version Check
```
java -version
```



# JDK (Java Development Kit)
To Develop, Build and Run Java applications. 
These tools are listed within the JDK file directory 
```
ls jdk-version/bin
```

## Develop
java debugger tool - *jdb*
document source code - *javadoc*

## Build
Build and Compile - *javac*
Archive code/libraries - *jar*

## Run
Java Runtime Environment - *jre*
Loader - *java*

#  Building a Basic Java Application
## Compile 
1. Develop Source Code
```java
public class MyClass {
	public static void main(String[] args) {
		System.out.println("Hello World");
	}
}
```
2. Compile (convert human readable code into computer code (intermediary Byte Code) using the javac command)
```java
javac MyClass.java
output: MyClass.class
```
3. Run
```java
java MyClass
```

## Package 
JAR (Java Archive) is used to package multiple files and dependencies into a compressed, shippable file. 
```java
jar cf MyClass.jar 
```

## Documentation
javadoc utility is used to generate documentation. 
```java
javadoc -d doc MyClass.java
```



#code 