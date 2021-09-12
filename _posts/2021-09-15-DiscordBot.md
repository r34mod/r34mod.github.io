---
layout: single
title: Discord Bot
date: 2020-09-15
classes: wide
header:
  teaser: /assets/images/slae32.png
categories:
  - JavaScript
  - Node
tags:
  - JavaScript
  - Node
  - npm
---

En este Post empezaremos a aprender como crear un bot para Discord. El proyecto fue del 2020, asi que puede que haya metodos que no sean exactamente igual, pero la base es la misma.

Para comenzar, necesitamos la creacion de variables constantes para nuestro correcto funcionamiento.

```javascript

// JavaScript source code
const Discord = require('discord.js');
const {Client, Attachment, MessageEmbed} = require('discord.js');

const clientBot = new Client();
const cheerio = require('cheerio');
const request = require('request');

const token = 'introducir vuesttro token del bot';
clientBot.login(process.env.token);

const ytdl = require("ytdl-core");

const variable = '<';
const varGame = '-';
const queue = new Map();

```
