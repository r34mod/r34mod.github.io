---
layout: single
title: TCP bind shellcode
date: 2018-11-18
classes: wide
header:
  teaser: /assets/images/slae32.png
categories:
  - java
  - android
tags:
  - java
  - android
  - backend
---
A bind shellcode listens on a socket, waiting for a connection to be made to the server then executes arbitrary code, typically spawning shell for the connecting user. This post demonstrates a simple TCP bind shellcode that executes a shell.

The shellcode does the following:
1. Creates a socket
2. Binds the socket to an IP address and port
3. Listens for incoming connections
4. Redirects STDIN, STDOUT and STDERR to the socket once a connection is made
5. Executes a shell

### Java prototype
---------------
To better understand the process of creating a bind shellcode, I created a prototype in C that uses the same functions that'll be used in the assembly version. The full code is shown here. We'll walk through each section of the code after.

```java

    private FirebaseAuth authfire;
    private  FirebaseAuth.AuthStateListener authlistener;

    public static final int SIGN_IN=1;
    
    List<AuthUI.IdpConfig> provider = Arrays.asList(
            new AuthUI.IdpConfig.GoogleBuilder().build(),
            new AuthUI.IdpConfig.FacebookBuilder().build());
```

#### 1. Socket creation
