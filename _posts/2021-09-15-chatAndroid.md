---
layout: single
title: Red Social Android App
date: 2021-09-15
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
Primera parte de una aplicacion de mensajeria, estilo red social, mostrando su desarrollo y explicandolo a su vez.

Empezamos por la seccion de la conexion con la base de datos. En este caso **Firebase**

Los pasos que veremos en este post:
1. Conexion con Firebase
2. Autentificacion 
3. Inicio de sesion
4. Entrando al menu principal

### Java prototype
---------------
Para entender mejor lo desarrollado, se mostrara partes del codigo. Mostrare ademas el nombre de la clase con la que trabajamos.

```java
    
import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;
import androidx.core.content.ContextCompat;

import android.Manifest;
import android.content.Intent;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Toast;

import com.firebase.ui.auth.AuthUI;
import com.google.firebase.auth.FirebaseAuth;
import com.google.firebase.auth.FirebaseUser;

import java.util.Arrays;
import java.util.List;

public class MainActivity extends AppCompatActivity {
    
    private FirebaseAuth authfire;
    private  FirebaseAuth.AuthStateListener authlistener;

    public static final int SIGN_IN=1;
    
    List<AuthUI.IdpConfig> provider = Arrays.asList(
            new AuthUI.IdpConfig.GoogleBuilder().build(),
            new AuthUI.IdpConfig.FacebookBuilder().build());
```

#### 2. Socket creation

En este paso, se mostrara el metodo **onCreate** y a su vez como iniciamos sesion y comprobamos el usuario.

```java


 @Override
    protected void onCreate(Bundle savedInstanceState) {
        setTheme(R.style.startTheme);
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);


        authfire = FirebaseAuth.getInstance();
        authlistener = new FirebaseAuth.AuthStateListener() {
            @Override
            public void onAuthStateChanged(@NonNull FirebaseAuth firebaseAuth) {
                FirebaseUser usuario = firebaseAuth.getCurrentUser();
                if(usuario != null){
                    principal();
                    Toast.makeText(MainActivity.this, "Log in", Toast.LENGTH_SHORT).show();;
                }else{
                    startActivityForResult(AuthUI.getInstance().createSignInIntentBuilder()
                    .setAvailableProviders(provider).setIsSmartLockEnabled(false).build(),SIGN_IN);
                }
            }
        };
    }

```
