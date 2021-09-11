---
layout: single
title: Red Social Android App parte 2
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

Ya vimos la primera parte de esta aplicacion que es el inicio de sesion. Tras esto, adentramos a un menu principal, sencillo de navegar y con la informacion suficiente para acceder a las demas ventanas y fragmentos, en los cuales tendremos el chat y nuestros amigos agregados.


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


Siempre hacemos referencias con FirebaseUser para obtener las keys del usuario. 

El DatabaseReference lo usamos para obtener le Id de nuestro usuario generada en la tabla que hagamos referencia, mas adelante veremos su uso en detalle.


Tras tener los atributos necesarios, empezaremos con el metodo **onCreate** , con el que se maneja toda la clase y para el usuario, la interfaz en la que se localiza en ese momento.

Para empezar, cree un menu que funciona deslizandose de izquierda a derecha y cambiando los **Fragmentos** que seran los "chats", "peticiones", "solicitudes", "llamadas"
Para ello utilizamos un switch, pasandole como dato una posicion especifica.

```java

 @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_principal);

        ViewPager2 view2 = findViewById(R.id.viewPage);
        view2.setAdapter(new AdapterPages(this));

        final TabLayout tabLayout = findViewById(R.id.tabla);
        TabLayoutMediator tabLayoutMediator = new TabLayoutMediator(tabLayout, view2, new TabLayoutMediator.TabConfigurationStrategy() {
            @Override
            public void onConfigureTab(@NonNull TabLayout.Tab tab, int position) {
                switch (position){
                    case 0:
                        tab.setText("Usuarios");
                        tab.setIcon(R.drawable.ic_users);
                        break;
                    case 1:
                        tab.setText("Chats");
                        tab.setIcon(R.drawable.ic_chat);
                        break;
                    case 2:
                        tab.setText("Solicitudes");
                        tab.setIcon(R.drawable.ic_contacto);
                        final BadgeDrawable badgeDrawable = tab.getOrCreateBadge();
                        badgeDrawable.setBackgroundColor(
                                ContextCompat.getColor(getApplicationContext(), R.color.colorAccent)
                        );
                        badgeDrawable.setVisible(true);
                        //badgeDrawable.setNumber(2);
                        ref_soli_cont.addValueEventListener(new ValueEventListener() {
                            @Override
                            public void onDataChange(@NonNull DataSnapshot snapshot) {
                                if(snapshot.exists()){
                                    Integer count = snapshot.getValue(Integer.class);
                                    badgeDrawable.setVisible(true);
                                    if(count.equals("0")){

                                        badgeDrawable.setVisible(false);
                                    }else{
                                        badgeDrawable.setNumber(count);
                                    }
                                }
                            }

                            @Override
                            public void onCancelled(@NonNull DatabaseError error) {

                            }
                        });
                        break;
                    case 3:
                        tab.setText("Llamadas");
                        tab.setIcon(R.drawable.ic_contacting);
                        break;

                }
            }
        });

```
