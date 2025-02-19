---
layout: post
title:  "Comment recevoir des notifications de connexion SSH sur Telegram ?"
author: mdestroy
categories: [ Security ]
tags: [ SSH, Telegram ]
image: assets/images/HSImages/telegram-sshrc.png
---

Surveiller les connexions SSH à votre serveur est essentiel pour garantir sa sécurité et détecter rapidement toute activité suspecte.
Et si vous pouviez être immédiatement averti sur Telegram chaque fois qu'une connexion SSH est établie ?
Grâce à un utilitaire appelé sshrc, il est possible de recevoir des notifications directement sur votre smartphone ou ordinateur de toutes les connexions ssh à votre serveur.

Dans cet article, nous verrons comment configurer ce système pratique.

# Qu'est ce qu'un bot Telegram ?

Un bot Telegram est une application automatisée qui interagit avec les utilisateurs via des messages ou commandes, sans interface traditionnelle.
Créés avec l'API Telegram Bot, ces bots peuvent automatiser des tâches, gérer des groupes, se connecter à des services tiers comme Google Sheets ou GitHub, et offrir des outils variés, du rappel simple aux systèmes complexes de gestion.

# Création du bot Telegram

Pour créer un bot Telegram, ouvrez votre application, puis saisissez tout simplement `Botfather` dans la barre de recherche comme dans l'image ci-dessous:

#![A la recherche de BotFather]({{ site.baseurl }}/assets/images/HSImages/telegram-sshrc1.jpeg)

<a href="/assets/images/HSImages/telegram-sshrc1.jpeg" target="_blank">
    <img src="/assets/images/HSImages/telegram-sshrc1.jpeg" alt="Image 1" style="max-width: 100%; height: auto;">
</a>

Une fois dans le chatbox de BotFather, choisissez l'option **/newbot**:

![Chat avec BotFather]({{ site.baseurl }}/assets/images/HSImages/telegram-sshrc2.jpeg)

Vous aurez à repondre à une serie de questions de façon interactive pour finaliser la création du bot.
Il vous sera demandé de choisir un nom pour votre bot, ensuite un nom d'utilisateur qui doit se terminer par `_bot`.
Notez bien votre token qui sera affiché tout juste après HTTP API (section barrée en rouge dans l'image ci-dessous).

![Création du bot]({{ site.baseurl }}/assets/images/HSImages/telegram-sshrc3.jpeg)

Son format est le suivant:

{% highlight ruby %} 123456789:ABCdEFGhIJkLmNOpQRStUVwXyZ1234567890 {% endhighlight %}

En cliquant sur le lien t.me/[BotUser Name] encadré dans l'image présentée ci-dessus, vous accéderez au chatbox de votre bot que vous venez de créer.

![Chat avec le bot créé]({{ site.baseurl }}/assets/images/HSImages/telegram-sshrc4.jpeg)

Laissez juste un message dans le chat...

![Message avec le bot]({{ site.baseurl }}/assets/images/HSImages/telegram-sshrc5.jpeg)

...Puis, accédez au lien ci-dessous avec votre navigateur Web:

{% highlight ruby %} https://api.telegram.org/bot[Token du Bot]/getUpdates {% endhighlight %}

Notez l'ID du chat. Il nous sera utile pour l'envoi des messages sur l'API du bot qui vient d'être créé.

![Updates]({{ site.baseurl }}/assets/images/HSImages/telegram-sshrc6.png)

Essayons à présent d'envoyer un message à notre application Telegram via l'API:

{% highlight ruby %} https://api.telegram.org/bot[Token du Bot]/sendMessage?chat_id=[ID du chat du Bot]&text=Hello Again{% endhighlight %}

![Message via le navigateur sur Telegram]({{ site.baseurl }}/assets/images/HSImages/telegram-sshrc7.png)

Notez bien ce lien car c'est à travers celui-ci que les notifications seront envoyées sur l'application Telegram.
Nous y reviendrons bien plutard dans la suite de l'article.


Jusqu'à présent, on est sûr que notre bot Telegram est fonctionnel et que les messages envoyés depuis Internet sont bien reçus par le bot.
Passons à présent à la configuration de notre serveur pour la configuration des notifications vers Telegram pour toute connexion SSH.


# Qu'est ce sshrc ?

[sshrc](https://github.com/Russell91/sshrc) est un utilitaire qui permet d'exécuter automatiquement une configuration personnalisée ou un script lors de la connexion à un serveur distant via SSH.
Il est particulièrement utile pour appliquer des paramètres d'environnement spécifiques, des alias ou des commandes adaptés à chaque session distante, sans modifier les fichiers de configuration du serveur distant comme **.bashrc** ou **.profile**.

Lorsque sshrc est configuré dans le fichier **/etc/ssh/sshrc** du serveur distant, il permet l'exécution d'un script ou d'un fichier de configuration à chaque fois qu'un utilisateur établit une connexion SSH avec ce serveur.
**Cette configuration permet de définir des paramètres globaux pour toutes les sessions ou d'exécuter automatiquement des commandes spécifiques pour tous les utilisateurs SSH.**


# Configuration de sshrc sur le serveur


Lorsqu'il est configuré, le script **/etc/ssh/sshrc** est sollicité pour tout utilisateur établissant une connexion SSH.
Il ne nous restera plus qu'à configurer l'envoi des messages via l'API de notre bot dans le script **/etc/ssh/sshrc**.


Avec la variable `$SSH_CONNECTION`, nous avons les adresses IP source et destination ainsi que les ports liés à la connexion SSH en cours.

![La variable $SSH_CONNECTION]({{ site.baseurl }}/assets/images/HSImages/telegram-sshrc8.png)

Nous allons nous en servir pour inclure l'adresse source dans les notifications envoyées sur Telegram.

Ci-dessous le contenu du script sshrc:

{% highlight ruby %} 

#! /bin/bash

BOT_TOKEN= [Token du Bot]
CHAT_ID= [ID du Chat avec le Bot]

IP=$(echo $SSH_CONNECTION | cut -d " " -f 1)
MESSAGE="[SSH] $USER vient de se connecter avec l'adresse  $IP"

wget -o /dev/null -O /dev/null "https://api.telegram.org/bot${BOT_TOKEN}/sendMessage?chat_id=${CHAT_ID}&text=${MESSAGE}"


{% endhighlight %}

![Script sshrc]({{ site.baseurl }}/assets/images/HSImages/telegram-sshrc9.png)


A la prochaine connexion SSH, vous recevrez une notification sur Telegram.

![Nouvelle connexion SSH]({{ site.baseurl }}/assets/images/HSImages/telegram-sshrc10.jpeg)

