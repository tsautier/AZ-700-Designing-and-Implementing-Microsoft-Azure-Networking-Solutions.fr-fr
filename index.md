---
title: Instructions hébergées en ligne
permalink: index.html
layout: home
---

# Répertoire de contenu

Les fichiers d’exercice requis peuvent être [TÉLÉCHARGÉS ICI](https://github.com/MicrosoftLearning/AZ-700-Designing-and-Implementing-Microsoft-Azure-Networking-Solutions/archive/master.zip).

Les liens hypertexte vers chaque exercice sont répertoriés ci-dessous.

## Exercice

{% assign Exercise = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %}
| Module | Exercice |
| --- | --- | 
{% for activity in Exercise  %}| {{ activity.Exercise.module }} | [{{ activity.Exercise.title }}{% if activity.Exercise.type %} - {{ activity.Exercise.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

## Démonstrations (en préparation)

{% assign demos = site.pages | where_exp:"page", "page.url contains ’/Instructions/Demos’" %}
| Module | Démonstration |
| --- | --- |
{% for activity in demos  %}| {{ activity.demo.module }} | [{{ activity.demo.title }}{% if activity.demo.type %} - {{ activity.demo.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

