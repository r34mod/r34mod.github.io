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

 1. Metodo onCreate
 2. Metodo estado (del usuario)
 3. Metodos de informacion 
 4. Cerrar sesion


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

### 1 Metodo onCreate

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


Cada **case** representa un Fragmento distinto, y en el caso de solicitudes, incluimos unos metodos para visualizar el numero de solicitudes que tenemos y cuando nos hayamos metido, nos manda un mensaje (Toast) diciendo que ya hemos visualizado todas, dandonos asi un valor de "0".



```java


tabLayoutMediator.attach();
        view2.registerOnPageChangeCallback(new ViewPager2.OnPageChangeCallback() {
            @Override
            public void onPageSelected(int position){
                super.onPageSelected(position);
                BadgeDrawable badgeDrawable = tabLayout.getTabAt(position).getOrCreateBadge();
                badgeDrawable.setVisible(false);

                if(position==2){
                    contadorcero();
                }
            }
        });

        final FirebaseUser usuarios = FirebaseAuth.getInstance().getCurrentUser();

        usuario();


    }
    
    
 ```
 
 Y el metodo de contador de solicitudes, que siempre creamos metodos a parte para poder trabajar mejor con ellos. Sencillamente es un comprobador y nos devuelve la cifra de solicitudes con un Toast
 
 ```java
 
 private void contadorcero(){
        ref_soli_cont.addListenerForSingleValueEvent(new ValueEventListener() {
            @Override
            public void onDataChange(@NonNull DataSnapshot snapshot) {
                if(snapshot.exists()){
                    ref_soli_cont.setValue(0);
                    Toast.makeText(Principal.this, "Es 0", Toast.LENGTH_SHORT).show();
                }
            }

            @Override
            public void onCancelled(@NonNull DatabaseError error) {

            }
        });
    }
 
 ```

### 2 Metodo de estado

Ahora hacemos un metodo que nos informa si nuestro usuario esta en linea o no, ademas si esta desconectado nos da la fecha de su ultima vez

```java

  private void estadoUser(final String estado) {
        ref_estado.addListenerForSingleValueEvent(new ValueEventListener() {
            @Override
            public void onDataChange(@NonNull DataSnapshot snapshot) {
                Estado st = new Estado(estado, "","","");
                ref_estado.setValue(st);

            }

            @Override
            public void onCancelled(@NonNull DatabaseError error) {

            }
        });
    }

    @Override
    protected void onResume() {
        super.onResume();
        estadoUser("conectado");
    }

    @Override
    protected void onPause() {
        super.onPause();
        estadoUser("desconectado");
        cogerFecha();
    }



```

Y ahora el metodo de la fecha y hora actual, obteniendola de su dispositivo movil

```java

 private void cogerFecha() {
        final Calendar c = Calendar.getInstance();
        final SimpleDateFormat format = new SimpleDateFormat("HH:mm");
        final SimpleDateFormat date = new SimpleDateFormat("dd/MM/yyyy");

        ref_estado.addListenerForSingleValueEvent(new ValueEventListener() {
            @Override
            public void onDataChange(@NonNull DataSnapshot snapshot) {
                ref_estado.child("fecha").setValue(date.format(c.getTime()));
                ref_estado.child("hora").setValue(format.format(c.getTime()));
            }

            @Override
            public void onCancelled(@NonNull DatabaseError error) {

            }
        });
    }
    
```

Con este metodo, lo guardamos todo en la base de datos de Firebase y haciendo siempre referencias al usuario concreto de ese dispositivo.
Tambien, utilizamos la libreria "Calendar" para poder trabajar mas comodo y obtener las fechas y horas.


### 3 Los metodos de informacion del usuario

Ahora necesitamos almacenar toda la informacion de nuestro usuario en la base de datos, su email, nombre, foto de perfil...

Creamos el metodo **usuario()** y comprobamos si existe o no, si no existe obtenemos los distintos datos que queremos y los enviamos a la base de datos.
Ademas, agregamos una fecha, pero no tiene mayor importancia.
Luego, tras almacenarse en el Firebase, habra valores como el estado, el nombre o el numero de telefono que se podran modificar por el usuario.

**ACLARACION: el ultimo campo "" esta vacio porque es el numero de telefono, y aqui el usuario lo pondra si el/ella quiere. ** 


```java

 private void usuario() {
        reference_user.addListenerForSingleValueEvent(new ValueEventListener() {
            @Override
            public void onDataChange(@NonNull DataSnapshot snapshot) {
                if(!snapshot.exists()){
                    Users us = new Users(
                        firebaseUser.getUid(),
                            firebaseUser.getDisplayName(),
                            firebaseUser.getEmail(),
                            firebaseUser.getPhotoUrl().toString(),
                            "desconectado",
                            "14/03/2021",
                            "02:41",
                            0,0,""
                    );
                    reference_user.setValue(us);
                }
            }

            @Override
            public void onCancelled(@NonNull DatabaseError error) {

            }
        });
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        MenuInflater inflater = getMenuInflater();
        inflater.inflate(R.menu.menu_principal, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(@NonNull MenuItem item) {
        switch (item.getItemId()){
            case R.id.boton_cerrar:
                AuthUI.getInstance().signOut(this).addOnCompleteListener(
                        new OnCompleteListener<Void>() {
                            @Override
                            public void onComplete(@NonNull Task<Void> task) {
                                finish();
                                Toast.makeText(Principal.this, "Cerrando sesion", Toast.LENGTH_SHORT).show();
                                login();

                            }
                        });

            break;
        }
        return super.onOptionsItemSelected(item);
    }

    /**

     * Método para iniciar/registrarte en la app y nos mandaria a una clase Principaal


     */

    private void login() {
        Intent princi = new Intent(this, MainActivity.class);
        princi.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP | Intent.FLAG_ACTIVITY_CLEAR_TASK | Intent.FLAG_ACTIVITY_NEW_TASK);
        startActivity(princi);
    }

```

Y para finalizar, el boton login() y el sistema de cierre de sesion desde la pantalla principal
