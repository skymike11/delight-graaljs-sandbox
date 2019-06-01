# Graal Sandbox

A secure sandbox for executing JavaScript in Java apps using the [Graal JS](https://github.com/graalvm/graaljs) engine.

This project is based on a [PR](https://github.com/javadelight/delight-nashorn-sandbox/pull/87/) in the [Nashorn Sandbox](https://github.com/javadelight/delight-nashorn-sandbox) project by [Marco Ellwanger](https://github.com/mellster2012). The Graal JS sandbox has been extracted from the Nashorn Sandbox repository to provide projects with a dedicated module to use if Graal JS sandbox capabilities are required.

Part of the [Java Delight Suite](https://github.com/javadelight/delight-main#java-delight-suite).

[![Build Status](https://travis-ci.org/javadelight/delight-graaljs-sandbox.svg?branch=master)](https://travis-ci.org/javadelight/delight-graaljs-sandbox)

Open Security Issues: [Nashorn # 73](https://github.com/javadelight/delight-nashorn-sandbox/issues/73)

## Usage

The sandbox by default **blocks access to all** Java classes.

Classes, which should be used in JavaScript, must be explicitly allowed.

```java
GraalSandbox sandbox = GraalSandboxes.create();
     
sandbox.allow(File.class);
     
sandbox.eval("var File = Java.type('java.io.File'); File;")
```

Or you can inject your Java object as a JS global variable

```java
GraalSandboxes sandbox = GraalSandboxes.create();

sandbox.inject("fromJava", new Object());

sandbox.eval("fromJava.getClass();");
```

The sandbox also allows limiting the CPU time and memory usage of scripts. This allows terminating scripts which contain infinite loops and other problematic code.

```java
GraalSandbox sandbox = GraalSandboxes.create();
     
sandbox.setMaxCPUTime(100);
sandbox.setMaxMemory(500*1024);
sandbox.allowNoBraces(false);
sandbox.setMaxPreparedStatements(30); // because preparing scripts for execution is expensive
sandbox.setExecutor(Executors.newSingleThreadExecutor());
     
sandbox.eval("var o={}, i=0; while (true) {o[i++]='abc';};");
```

This code will raise a ScriptCPUAbuseException.

The sandbox beautifies the JavaScript code for this and injects additional statements into the submitted code. It is thus possible that the original line numbers from
the submitted JS code are not preserved. To debug the code, which is generated by the sandbox, activate its debug mode as follows using log4j.properties file:

```
log4j.logger.delight.graaljssandbox.internal.GraalSandboxImpl=DEBUG
```

This will output the generated JS on the console as follows:

```
--- Running JS ---
var \__it = Java.type('delight.graaljssandbox.internal.InterruptTest');var \__if=function(){\__it.test();};
while(true) {__if();
  i = i+1;
}
--- JS END ---
```

## Maven

Just add the following dependency to your projects.

```
<dependency>
    <groupId>org.javadelight</groupId>
    <artifactId>delight-graaljs-sandbox</artifactId>
    <version>[insert latest version]</version>
</dependency>
```

This artifact is available on [Maven Central](https://search.maven.org/#search%7Cga%7C1%7Cdelight-graaljs-sandbox) and 
[BinTray](https://bintray.com/javadelight/javadelight/delight-graaljs-sandbox).

[![Maven Central](https://img.shields.io/maven-central/v/org.javadelight/delight-graaljs-sandbox.svg)](https://search.maven.org/#search%7Cga%7C1%7Cdelight-graaljs-sandbox)

## Contributors

[Marco Ellwanger](https://github.com/mellster2012): Initial support for GraalJS engine by implementing sandbox implementation backed by GraalJS.

## Version History

- 0.1.0: Initial support for Graal JS ([Nashorn PR #87](https://github.com/javadelight/delight-nashorn-sandbox/pull/87/) by [mellster2012](https://github.com/mellster2012)) 

