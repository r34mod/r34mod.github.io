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

Ya vimos la primera parte de esta aplicacion que es el inicio de sesion. Tras esto, adentramos a un menu principal, sencillo de navegar y con la informacion suficiente para acceder a las demas ventanas.


Esta clase la llamamos **Principal** 

Este sera el orden que trabajaremos:

- 1. Metodo onCreate
- 2. Metodo estado (del usuario)
- 3. Metodos de informacion 
- 4. Cerrar sesion


Empezando con el onCreate, primero os dejare loos import y los atributos creados.


### Imports y atributos de la clase.

```java

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.content.ContextCompat;
import androidx.viewpager2.widget.ViewPager2;

import android.content.Intent;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuInflater;
import android.view.MenuItem;
import android.widget.Toast;

import com.bumptech.glide.Glide;
import com.example.friendchatting.Peds.Estado;
import com.example.friendchatting.Peds.Users;
import com.example.friendchatting.adapters.AdapterPages;
import com.firebase.ui.auth.AuthUI;
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.android.material.badge.BadgeDrawable;
import com.google.android.material.tabs.TabLayout;
import com.google.android.material.tabs.TabLayoutMediator;
import com.google.firebase.auth.FirebaseAuth;
import com.google.firebase.auth.FirebaseUser;
import com.google.firebase.database.DataSnapshot;
import com.google.firebase.database.DatabaseError;
import com.google.firebase.database.DatabaseReference;
import com.google.firebase.database.FirebaseDatabase;
import com.google.firebase.database.ValueEventListener;

import java.text.SimpleDateFormat;
import java.util.Calendar;



    FirebaseUser firebaseUser = FirebaseAuth.getInstance().getCurrentUser();
    FirebaseDatabase database = FirebaseDatabase.getInstance();
    DatabaseReference reference_user = database.getReference("Users").child(firebaseUser.getUid());
    DatabaseReference ref_soli_cont = database.getReference("Contador").child(firebaseUser.getUid());
    DatabaseReference ref_estado = database.getReference("Estado").child(firebaseUser.getUid());


```
