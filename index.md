---
title: Azure KI Language-Übungen
permalink: index.html
layout: home
---

# Azure KI Language-Übungen

In den folgenden Übungen können Sie praktische Erfahrungen sammeln und typische Aufgaben kennenlernen, die Fachkräfte in der Entwicklung beim Erstellen von Lösungen für natürliche Sprache in Azure ausführen. 

> **Hinweis**: Um die Übung zu abzuschließen, benötigen Sie ein Azure-Abonnement. Wenn Sie noch kein Konto haben, können Sie sich für ein [Azure-Konto](https://azure.microsoft.com/free) anmelden. Für neue Benutzende gibt es dort eine kostenlose Testoption, die Guthaben für die ersten 30 Tage beinhaltet.

## Übungen

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %} {% for activity in labs  %}
<hr>
### [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }})

{{activity.lab.description}}

{% endfor %}

<hr>

> **Hinweis**: Diese Übungen können zwar eigenständig absolviert werden, sie sind jedoch als Ergänzung zu den Modulen auf [Microsoft Learn](https://learn.microsoft.com/training/paths/develop-language-solutions-azure-ai/) gedacht, in denen Sie tiefer in einige der zugrunde liegenden Konzepte eintauchen können, auf denen diese Übungen basieren. 
