---
title: Azure KI Language-Übungen
permalink: index.html
layout: home
---

# Azure KI Language-Übungen

Die folgenden Übungen sind eine Ergänzung für die Module von Microsoft Learn für das [Entwickeln von Lösungen in natürlicher Sprache](https://learn.microsoft.com/training/paths/develop-language-solutions-azure-ai/).


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %} {% for activity in labs  %} {% unless activity.url contains 'ai-foundry' %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endunless %} {% endfor %}
