---
lab:
  title: Grundlegendes zur Voice-Live-API
  description: 'Erfahren Sie, wie Sie die Voice-Live-API verwenden und anpassen, die im Playground von Azure AI Foundry verfügbar ist.'
---

# Grundlegendes zur Voice-Live-API

In dieser Übung erstellen Sie einen Agent in Azure AI Foundry und erkunden die Voice-Live-API im Speech-Playground. 

Diese Übung dauert ca. **30** Minuten.

> <span style="color:red">**Hinweis**:</span> Einige der in dieser Übung verwendeten Technologien befinden sich derzeit in der Vorschau oder in der aktiven Entwicklung. Es kann zu unerwartetem Verhalten, Warnungen oder Fehlern kommen.

> <span style="color:red">**Hinweis**:</span> Diese Übung soll in einer Browserumgebung mit direktem Zugriff auf das Mikrofon Ihres Computers durchgeführt werden. Während die Konzepte in Azure Cloud Shell kennengelernt werden können, benötigen die interaktiven Sprachfunktionen Zugriff auf lokale Audiohardware.

## Erstellen eines Azure KI Foundry-Projekts

Beginnen wir mit dem Erstellen eines Azure AI Foundry-Projekts.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartfenster, die bei der ersten Anmeldung geöffnet werden, und verwenden Sie gegebenenfalls das Logo **Azure AI Foundry** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht (schließen Sie das **Hilfe**-Fenster, falls es geöffnet ist):

    ![Screenshot der Azure AI Foundry-Startseite mit ausgewählter Option „Agent erstellen“.](../media/ai-foundry-new-home-page.png)

1. Wählen Sie auf der Startseite **Agent erstellen**.

1. Geben Sie im Assistenten **Agent erstellen** einen gültigen Namen für Ihr Projekt ein. 

1. Wählen Sie **Erweiterte Optionen** und nehmen Sie die folgenden Einstellungen vor:
    - **Azure AI Foundry-Ressource**: *Behalten Sie den Standardnamen bei.*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Region:** Wählen Sie zufällig eine Region aus den folgenden Optionen aus:\*
        - USA (Ost) 2
        - Schweden, Mitte

    > \* Zum Zeitpunkt des Erstellen dieser Anleitung wird die Voice-Live-API nur in den zuvor aufgeführten Regionen unterstützt. Durch die zufällige Auswahl eines Standorts wird sichergestellt, dass eine einzelne Region nicht mit dem Datenverkehr überfordert wird. Dies stellt eine reibungslosere Erfahrung sicher. Bei erreichenden Dienstgrenzwerte besteht die Möglichkeit, ein weiteres Projekt in einer anderen Region zu erstellen.

1. Wählen Sie **Erstellen** und überprüfen Sie Ihre Konfiguration. Warten Sie, bis der Einrichtungsvorgang abgeschlossen ist.

    >**Hinweis**: Wenn Sie eine Berechtigungsfehlermeldung erhalten, klicken Sie auf die Schaltfläche **Korrigieren**, um die erforderlichen Berechtigungen hinzuzufügen und fortzufahren.

1. Nach Erstellen Ihres Projekts werden Sie standardmäßig zum Agents-Playground im Azure AI Foundry-Portal weitergeleitet, der in etwa wie folgt aussieht:

    ![Screenshot eines Azure KI-Projekts im Azure AI Foundry-Portal.](../media/ai-foundry-project-2.png)

## Starten eines Voice-Live-Beispiels

 In diesem Abschnitt der Übung interagieren Sie mit einem der Agents. 

1. Wählen Sie im Navigationsbereich **Playgrounds** aus.

1. Suchen Sie die Gruppe **Speech-Playground** und wählen Sie die Schaltfläche **Speech-Playground testen** aus.

1. Der Speech-Playground bietet viele vordefinierte Optionen. Verwenden Sie die horizontale Scrollleiste, um zum Ende der Liste zu navigieren und die Kachel **Voice Live** auszuwählen. 

    ![Screenshot der Kachel „Voice Live“.](../media/voice-live-tile.png)

1. Wählen Sie im Bereich **Testen mit Beispielen** das Agent-Beispiel **Ungezwungener Chat** aus.

1. Stellen Sie sicher, dass Ihr Mikrofon und Ihre Lautsprecher funktionieren und wählen Sie unten auf der Seite die Schaltfläche **Starten** aus. 

    Beachten Sie bei der Interaktion mit dem Agent, dass Sie diesen unterbrechen können und er macht eine Pause, um Ihnen zuzuhören. Versuchen Sie beim Sprechen verschieden lange Pausen zwischen den Wörtern und Sätzen zu machen. Achten Sie darauf, wie schnell der Agent die Pausen erkennt und die Unterhaltung füllt. Wenn Sie fertig sind, wählen Sie die Schaltfläche **Beenden** aus.

1. Starten Sie die anderen Beispiel-Agents, um zu sehen, wie sie sich verhalten.

    Während Sie die verschiedenen Agents erkunden, beachten Sie die Änderungen im Abschnitt **Antwortanweisung** im Panel **Konfiguration** .

## Konfigurieren des Agents 

In diesem Abschnitt ändern Sie die Stimme des Agents und fügen dem Agent **Ungezwungener Chat** einen Avatar hinzu. Das Panel **Konfiguration** ist in drei Abschnitte unterteilt: **GenAI**, **Sprache** und **Avatar**.

>**Hinweis:** Wenn Sie eine der Konfigurationsoptionen ändern oder mit ihnen interagieren, müssen Sie unten im Panel **Konfiguration** die Schaltfläche **Anwenden** auswählen, um den Agent zu aktivieren.

Wählen Sie den Agent **Ungezwungener Chat** aus. Ändern Sie als Nächstes die Stimme des Agents und fügen Sie einen Avatar mit den folgenden Anweisungen hinzu:

1. Wählen Sie **> Sprache** aus, um den Abschnitt zu erweitern und auf die Optionen zuzugreifen.

1. Wählen Sie das Dropdownmenü in der Option **Stimme** und dann eine andere Stimme aus.

1. Wählen Sie **Anwenden** aus, um Ihre Änderungen zu speichern, und dann **Starten**, um den Agent zu starten und die Änderungen zu hören.

    Wiederholen Sie die vorherigen Schritte, um einige verschiedene Stimmen auszuprobieren. Fahren Sie mit dem nächsten Schritt fort, wenn Sie mit der Stimmauswahl fertig sind.

1. Wählen Sie **> Avatar** aus, um den Abschnitt zu erweitern und auf die Optionen zuzugreifen.

1. Wählen Sie die Umschaltfläche aus, um die Funktion zu aktivieren und einen der Avatare auszuwählen. 

1. Wählen Sie **Anwenden** aus, um Ihre Änderungen zu speichern, und dann **Starten**, um den Agent zu starten. 

    Achten Sie auf die Animation und Synchronisierung des Avatars mit dem Audio.

1. Erweitern Sie den Abschnitt **> GenAI** und deaktivieren Sie den Umschalter**Proaktive Interaktion**. Wählen Sie als Nächstes **Anwenden** aus, um Ihre Änderungen zu speichern, und dann **Starten**, um den Agent zu starten.

    Wenn **Proaktive Interaktion** deaktiviert ist, initiiert der Agent die Unterhaltung nicht. Fragen Sie den Agenten: „Kannst du mir sagen, was du tust?“, um eine Unterhaltung zu starten.

>**Tipp:** Sie können **Auf Standard zurücksetzen** auswählen und dann **Anwenden**, um den Agent auf sein Standardverhalten zurückzusetzen.

Wenn Sie fertig sind, fahren Sie mit dem nächsten Abschnitt fort.

## Erstellen eines Voice-Agents

In diesem Abschnitt erstellen Sie ihren eigenen Voice-Agent von Grund auf neu.

1. Wählen Sie im Panelabschnitt **Testen mit eigener Vorlage** **Mit leerer Vorlage beginnen** aus. 

1. Erweitern Sie den Abschnitt **> GenAI** im Panel **Konfiguration**.

1. Wählen Sie das Dropdownmenü **Generatives KI-Modell** und dann das Modell **GPT-4o Mini Realtime** aus.

1. Fügen Sie den folgenden Text im Abschnitt **Antwortanweisung** hinzu.

    ```
    You are a voice agent named "Ava" who acts as a friendly car rental agent. 
    ```

1. Legen Sie den Schieberegler für die **Antworttemperatur** auf einen Wert von **0,8** fest. 

1. Aktivieren Sie die Umschaltfläche **Proaktive Interaktion**.

1. Wählen Sie **Anwenden** aus, um Ihre Änderungen zu speichern, und dann **Starten**, um den Agent zu starten.

    Der Agent wird sich vorstellen und fragen, wie er Ihnen heute helfen kann. Fragen Sie den Agenten: „Hast du für Donnerstag eine Limousine zur Miete zur Verfügung?" Achten Sie darauf, wie lange es dauert, bis der Agent reagiert. Stellen Sie dem Agent weitere Fragen, um zu sehen, wie er reagiert. Wenn Sie fertig sind, fahren Sie mit dem nächsten Schritt fort.

1. Erweitern Sie den Abschnitt **> Sprache** im Panel **Konfiguration**.

1. **Aktivieren** Sie die Umschaltfläche **Ende der Äußerung (EOU)**.

1. **Aktivieren** Sie die Umschaltfläche **Audioerweiterung**.

1. Wählen Sie **Anwenden** aus, um Ihre Änderungen zu speichern, und dann **Starten**, um den Agent zu starten.

    Fragen Sie nachem sich der Agent vorgestellt hat: „Kann ich bei dir ein Flugzeug mieten?“ Beachten Sie, dass der Agent schneller auf Ihre Frage antwortet, als er es zuvor getan hat. Die Einstellung **Ende der Äußerung (EOU)** konfiguriert den Agent so, dass er Pausen und das Ende einer Aussage basierend auf Kontext und Semantik erkennt. Dies ermöglicht es, eine natürlichere Unterhaltung zu führen.

Wenn Sie fertig sind, fahren Sie mit dem nächsten Abschnitt fort.

## Bereinigen von Ressourcen

Nachdem Sie die Übung abgeschlossen haben, löschen Sie das von Ihnen erstellte Projekt, um unnötigen Ressourceneinsatz zu vermeiden.

1. Wählen Sie im Navigationsmenü von AI Foundry **Verwaltungszentrum** aus.
1. Wählen Sie im rechten Informationsbereich **Projekt löschen** aus und bestätigen Sie dann den Löschvorgang.

