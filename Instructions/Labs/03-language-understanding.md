---
lab:
  title: Erstellen eines Language Understanding-Modells mit dem Azure KI Language-Dienst
  description: 'Erstellen Sie ein benutzerdefiniertes Language-Understanding-Modell, um Eingaben zu interpretieren, Absichten vorherzusagen und Entitäten zu identifizieren.'
---

# Erstellen eines Language Understanding-Modells mit dem Sprachdienst

Mithilfe des Azure KI Language-Diensts können Sie ein *Conversational-Language-Understanding-Modell* definieren, mit dem Anwendungen *Äußerungen* von Benutzenden in natürlicher Sprache interpretieren (Text- oder Spracheingabe), die *Absicht* von Benutzenden vorhersagen (was sie erreichen möchten) und alle *Entitäten* identifizieren können, auf die die Absicht angewendet werden soll.

Beispielsweise kann erwartet werden, dass ein Conversational Language-Modell für eine Uhranwendung Eingaben wie die folgenden verarbeitet:

*Wie spät ist es in London?*

Diese Art von Eingabe ist ein Beispiel für eine *Äußerung* (etwas, das ein Benutzer sagen oder eingeben kann), deren gewünschte *Absicht* es ist, die Zeit an einem bestimmten Ort (einer *Entität*) zu erfahren, in diesem Fall London.

> **HINWEIS** Die Aufgabe eines Conversational Language-Modells besteht darin, die Absicht von Benutzer*innen vorherzusagen und alle Entitäten zu identifizieren, für die die Absicht gilt. Es ist <u>nicht</u> der Auftrag eines Conversational Language-Modells, die zur Erfüllung der Absicht erforderlichen Aktionen tatsächlich durchzuführen. Beispielsweise kann die Uhranwendung ein Conversational Language-Modell verwenden, um zu erkennen, dass der Benutzer die Uhrzeit in London erfahren möchte. Aber die Clientanwendung selbst muss dann die Logik implementieren, um die richtige Zeit zu bestimmen und dem Benutzer zu präsentieren.

In dieser Übung verwenden Sie den Azure KI Language-Dienst, um ein Conversational-Language-Understanding-Modell zu erstellen und das Python-SDK zum Implementieren einer Client-App zu verwenden, die dieses Model nutzt.

Obwohl diese Übung auf Python basiert, können Sie Conversational-Understanding-Anwendungen mit mehreren sprachspezifischen SDKs entwickeln, einschließlich:

- [Azure KI-Unterhaltungs-Clientbibliothek für Python](https://pypi.org/project/azure-ai-language-conversations/)
- [Azure KI-Unterhaltungs-Clientbibliothek für .NET](https://www.nuget.org/packages/Azure.AI.Language.Conversations)
- [Azure KI-Unterhaltungs-Clientbibliothek für JavaScript](https://www.npmjs.com/package/@azure/ai-language-conversations)

Diese Übung dauert ca. **35** Minuten.

## Bereitstellen einer *Azure KI Language*-Ressource

Wenn Sie noch nicht über eine solche Ressource in Ihrem Abonnement verfügen, müssen Sie eine **Azure KI Language Services**-Ressource Ihrem Azure-Abonnement bereitstellen.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Wählen Sie **Ressource erstellen** aus.
1. Suchen Sie im Suchfeld nach dem **Sprachdienst**. Wählen Sie dann in den Ergebnissen unter **Sprachdienst** die Option **Erstellen** aus.
1. Stellen Sie die Ressource mithilfe der folgenden Einstellungen bereit:
    - **Abonnement**: *Ihr Azure-Abonnement*.
    - **Ressourcengruppe**: *Wählen oder erstellen Sie eine Ressourcengruppe*.
    - **Region**: *Wählen Sie aus einer der folgenden Regionen*\*
        - Australien (Osten)
        - Indien, Mitte
        - China, Osten 2
        - East US
        - USA (Ost) 2
        - Nordeuropa
        - USA Süd Mitte
        - Schweiz, Norden
        - UK, Süden
        - Europa, Westen
        - USA, Westen 2
        - USA, Westen 3
    - **Name**: *Geben Sie einen eindeutigen Namen ein*.
    - **Tarif**: Wählen Sie **F0** (*kostenlos*) oder **S** (*Standard*), wenn F nicht verfügbar ist.
    - **Hinweis zu verantwortungsvoller KI**: Zustimmen.
1. Wählen Sie **Überprüfen und erstellen** aus und dann **Erstellen**, um die Ressource bereitzustellen.
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, und wechseln Sie dann zur bereitgestellten Ressource.
1. Sehen Sie sich die Seite **Schlüssel und Endpunkt** an. Sie benötigen die Informationen auf dieser Seite später in der Übung.

## Erstellen eines Conversational Language Understanding-Projekts

Nachdem Sie nun eine Dokumenterstellungsressource erstellt haben, können Sie sie zum Erstellen eines Conversational Language Understanding-Projekts verwenden.

1. Öffnen Sie auf einer neuen Browserregisterkarte das Azure KI Language Studio-Portal unter `https://language.cognitive.azure.com/`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.

1. Wenn Sie zur Auswahl einer Sprachressource aufgefordert werden, wählen Sie die folgenden Einstellungen aus:

    - **Azure-Verzeichnis**: Das Azure-Verzeichnis mit Ihrem Abonnement.
    - **Azure-Abonnement**: Ihr Azure-Abonnement.
    - **Ressourcentyp**: Sprache.
    - **Sprachressource**: Die Azure KI Language-Ressource, die Sie zuvor erstellt haben.

    Sollten Sie <u>nicht</u> zur Auswahl einer Sprachressource aufgefordert, kann dies daran liegen, dass Ihr Abonnement mehrere Sprachressourcen enthält. Gehen Sie in diesem Fall folgendermaßen vor:

    1. Wählen Sie auf der Leiste oben auf der Seite die Schaltfläche **Einstellungen (&#9881;)** aus.
    2. Gehen Sie auf der Seite **Einstellungen** zur Registerkarte **Ressourcen**.
    3. Wählen Sie die soeben erstellte Sprachressource aus, und klicken Sie auf **Switch resource** (Ressource wechseln).
    4. Klicken Sie oben auf der Seite auf **Language Studio**, um zur Homepage von Language Studio zurückzukehren.

1. Klicken Sie oben im Portal im Menü **Neu erstellen** auf die Option **Conversational Language Understanding**.

1. Geben Sie im Dialogfeld **Projekt erstellen** auf der Seite **Grundlegende Informationen eingeben** die folgenden Details ein, und wählen Sie dann **Weiter** aus:
    - **Name**: `Clock`
    - **Utterances primary language** (Primäre Sprache für Äußerungen): Englisch
    - **Enable multiple languages in project?** (Mehrere Sprachen im Projekt aktivieren?): *Nicht ausgewählt*
    - **Beschreibung:** `Natural language clock`

1. Wählen Sie auf der Seite **Überprüfen und fertigstellen** die Option **Erstellen** aus.

### Erstellen von Absichten

Als Erstes definieren Sie im neuen Projekt einige Absichten. Das Modell sagt letztendlich vorher, welche dieser Absichten Benutzer*innen anfordern, wenn eine Äußerung in natürlicher Sprache übermittelt wird.

> **Tipp**: Wenn Ihnen bei der Arbeit an Ihrem Projekt einige Tipps angezeigt werden, lesen Sie diese, und wählen Sie **Verstanden** aus, um sie zu schließen, oder wählen Sie **Alle überspringen** aus.

1. Wählen Sie auf der Seite **Schemadefinition** auf der Registerkarte **Absichten** die Option **＋Hinzufügen** aus, um die neue Absicht `GetTime` hinzuzufügen.
1. Stellen Sie sicher, dass die Absicht **GetTime** aufgeführt ist (zusammen mit der Standardabsicht **Keine**). Fügen Sie dann die folgenden zusätzlichen Absichten hinzu:
    - `GetDay`
    - `GetDate`

### Beschriften der Absichten mit Beispieläußerungen

Um dem Modell zu helfen vorherzusagen, welche Absicht Benutzer*innen anfordern, müssen Sie jede Absicht mit einigen Beispielaussagen beschriften.

1. Wählen Sie im Bereich auf der linken Seite die Seite **Datenbeschriftung** aus.

> **Tipp**: Sie können den Bereich mit dem Symbol **>>** erweitern, um die Seitennamen anzuzeigen, und ihn mit dem Symbol **<<** wieder ausblenden.

1. Wählen Sie die neue Absicht **GetTime** aus, und geben Sie die Äußerung `what is the time?` ein. Dadurch wird die Äußerung als Beispieleingabe für die Absicht hinzugefügt.
1. Fügen Sie die folgenden zusätzlichen Äußerungen für die Absicht **GetTime** hinzu:
    - `what's the time?`
    - `what time is it?`
    - `tell me the time`

    > **HINWEIS**: Um eine neue Äußerung hinzuzufügen, schreiben Sie die Äußerung in das Textfeld neben der Absicht und drücken Sie dann die EINGABETASTE. 

1. Wählen Sie die Absicht **GetDay** aus, und fügen Sie die folgenden Äußerungen als Beispieleingaben für diese Absicht hinzu:
    - `what day is it?`
    - `what's the day?`
    - `what is the day today?`
    - `what day of the week is it?`

1. Wählen Sie die Absicht **GetDate** aus, und fügen Sie die folgenden Äußerungen hinzu:
    - `what date is it?`
    - `what's the date?`
    - `what is the date today?`
    - `what's today's date?`

1. Nachdem Sie für jede Ihrer Absichten Äußerungen hinzugefügt haben, wählen Sie **Änderungen speichern** aus.

### Trainieren und Testen des Modells

Nachdem Sie nun einige Absichten hinzugefügt haben, trainieren Sie das Sprachmodell und testen, ob es die Absichten aus Benutzereingaben richtig vorhersagen kann.

1. Wählen Sie im Fensterbereich links **Trainingsaufträge** aus. Wählen Sie dann **+ Trainingsauftrag starten** aus.

1. Wählen Sie im Dialogfeld **Einen Trainingsauftrag starten**, die Option zum Trainieren eines neuen Modells aus, und nennen Sie es `Clock`. Wählen Sie den Modus **Standardtraining** und die Standardoptionen für das **Teilen von Daten** aus.

1. Um mit dem Trainieren Ihres Modells zu beginnen, wählen Sie **Trainieren** aus.

1. Wenn das Training abgeschlossen ist (was mehrere Minuten dauern kann), ändert sich der **Status** für den Auftrag in **Training erfolgreich**.

1. Wählen Sie die Seite **Modellleistung** und dann das Modell **Clock** aus. Sehen Sie sich die Metriken für die Gesamtauswertung und die Auswertung pro Absicht (*Genauigkeit*, *Abruf* und *F1-Score*) sowie die *Konfusionsmatrix* an, die durch die beim Training durchgeführte Auswertung generiert wurde. (Beachten Sie, dass aufgrund der geringen Anzahl von Beispieläußerungen möglicherweise nicht alle Absichten in die Ergebnisse einbezogen werden können).

    > **HINWEIS:** Weitere Informationen zu den Auswertungsmetriken finden Sie in der [Dokumentation](https://learn.microsoft.com/azure/ai-services/language-service/conversational-language-understanding/concepts/evaluation-metrics).

1. Wählen Sie auf der Seite **Bereitstellung eines Modells** die Option **Bereitstellung hinzufügen** aus.

1. Wählen Sie im Dialogfeld **Bereitstellung hinzufügen** die Option **Neuen Bereitstellungsnamen erstellen** aus, und geben Sie dann `production` ein.

1. Wählen Sie im Feld **Modell** das Modell **Clock** aus, und wählen Sie dann **Bereitstellen** aus. Die Bereitstellung kann einige Zeit in Anspruch nehmen.

1. Nachdem das Modell bereitgestellt wurde, wählen Sie auf der Seite **Bereitstellungen testen** im Feld **Bereitstellungsname** die Bereitstellung **Production** aus.

1. Geben Sie den folgenden Text in das leere Textfeld ein, und wählen Sie dann **Test ausführen** aus:

    `what's the time now?`

    Überprüfen Sie das zurückgegebene Ergebnis, und achten Sie darauf, dass es die vorhergesagte Absicht (bei der es sich um **GetTime** handeln sollte) und eine Zuverlässigkeitsbewertung enthält, die die Wahrscheinlichkeit angibt, die das Modell für die vorhergesagte Absicht berechnet hat. Auf der JSON-Registerkarte sehen Sie die Zuverlässigkeit der jeweiligen potenziellen Absicht (wobei die vorhergesagte Absicht die höchste Zuverlässigkeitsbewertung aufweist).

1. Löschen Sie das Textfeld, und führen Sie dann einen weiteren Test mit folgendem Text aus:

    `tell me the time`

    Überprüfen Sie erneut die vorhergesagte Absicht und die Zuverlässigkeitsbewertung.

1. Verwenden Sie den folgenden Text:

    `what's the day today?`

    Hoffentlich sagt das Modell die **GetDay**-Absicht vorher.

## Hinzufügen von Entitäten

Bisher haben Sie ein paar Beispieläußerungen definiert, die sich Absichten zuordnen lassen. Die meisten realen Anwendungen enthalten komplexere Äußerungen, aus denen bestimmte Datenentitäten extrahiert werden müssen, um mehr Kontext für die Absicht zu erhalten.

### Hinzufügen einer durch maschinelles Lernen erworbenen Entität

Die häufigste Art von Entität ist eine *durch maschinelles Lernen erworbene* Entität, bei der das Modell lernt, Entitätswerte anhand von Beispielen zu identifizieren.

1. Kehren Sie in Language Studio zur Seite **Schemadefinition** zurück, und klicken Sie dann auf der Registerkarte **Entitäten** auf **&#65291; Hinzufügen**, um eine neue Entität hinzuzufügen.

1. Geben Sie im Dialogfeld **Entität hinzufügen** den Entitätsnamen `Location` ein, und stellen Sie sicher, dass die Registerkarte **Gelernt** ausgewählt ist. Wählen Sie dann **Entität hinzufügen** aus.

1. Kehren Sie nach der Erstellung der Entität **Location** zur Seite **Datenbeschriftung** zurück.
1. Wählen Sie die Absicht **GetTime** aus, und geben Sie die folgende neue Beispieläußerung ein:

    `what time is it in London?`

1. Wenn die Äußerung hinzugefügt wurde, wählen Sie das Wort **London** aus. Wählen Sie dann in der angezeigten Dropdownliste den Eintrag **Standort** aus, um anzugeben, dass „London“ ein Beispiel für einen Standort ist.

1. Fügen Sie ein weiteres Beispiel für die Absicht **GetTime** hinzu:

    `Tell me the time in Paris?`

1. Nachdem die Äußerung hinzugefügt wurde, wählen Sie das Wort **Paris** aus und ordnen es der Entität **Standort** zu.

1. Fügen Sie ein weiteres Beispiel für die Absicht **GetTime** hinzu:

    `what's the time in New York?`

1. Nachdem die Äußerung hinzugefügt wurde, wählen Sie die Wörter **New York** aus und ordnen sie der Entität **Standort** zu.

1. Wählen Sie **Änderungen speichern** aus, um die neuen Äußerungen zu speichern.

### Hinzufügen einer *list*-Entität (Liste)

In einigen Fällen können gültige Werte für eine Entität auf eine Liste mit bestimmten Begriffen und Synonymen beschränkt werden. Dies kann der App helfen, Instanzen der Entität in Äußerungen zu identifizieren.

1. Kehren Sie in Language Studio zur Seite **Schemadefinition** zurück, und klicken Sie dann auf der Registerkarte **Entitäten** auf **&#65291; Hinzufügen**, um eine neue Entität hinzuzufügen.

1. Geben Sie im Dialogfeld **Entität hinzufügen** den Entitätsnamen `Weekday` ein, und wählen Sie die Entitätsregisterkarte **Liste** aus. Wählen Sie anschließend **Entität hinzufügen** aus.

1. Stellen Sie auf der Seite für die Entität **Weekday** im Abschnitt **Gelernt** sicher, dass **Nicht erforderlich** ausgewählt ist. Wählen Sie dann im Abschnitt **Liste** die Option **+ Neue Liste hinzufügen** aus. Geben Sie dann den folgenden Wert und das Synonym ein, und wählen Sie **Speichern** aus:

    | Listenschlüssel | Synonyme|
    |-------------------|---------|
    | `Sunday` | `Sun` |

    > **HINWEIS** Um die Felder der neuen Liste einzugeben, geben Sie den Wert `Sunday` in das Textfeld ein, klicken Sie dann auf das Feld, in dem „Wert eingeben und Enter drücken ...“ angezeigt wird, geben Sie die Synonyme ein und drücken Sie die EINGABETASTE.

1. Wiederholen Sie den vorherigen Schritt, um die folgenden Listenkomponenten hinzuzufügen:

    | Wert | Synonyme|
    |-------------------|---------|
    | `Monday` | `Mon` |
    | `Tuesday` | `Tue, Tues` |
    | `Wednesday` | `Wed, Weds` |
    | `Thursday` | `Thur, Thurs` |
    | `Friday` | `Fri` |
    | `Saturday` | `Sat` |

1. Kehren Sie nach dem Hinzufügen und Speichern der Listenwerte zur Seite **Datenbeschriftung** zurück.
1. Wählen Sie die Absicht **GetDate** aus, und geben Sie die folgende neue Beispieläußerung ein:

    `what date was it on Saturday?`

1. Wenn die Äußerung hinzugefügt wurde, wählen Sie das Wort ***Saturday*** (Samstag) und in der angezeigten Dropdownliste **Wochentag** aus.

1. Fügen Sie eine weitere Beispieläußerung für die Absicht **GetDate** hinzu:

    `what date will it be on Friday?`

1. Wenn die Äußerung hinzugefügt wurde, ordnen Sie **Friday** (Freitag) der Entität **Weekday** (Wochentag) hinzu.

1. Fügen Sie eine weitere Beispieläußerung für die Absicht **GetDate** hinzu:

    `what will the date be on Thurs?`

1. Wenn die Äußerung hinzugefügt wurde, ordnen Sie **Thurs** (Donners.) der Entität **Weekday** (Wochentag) hinzu.

1. Wählen Sie **Änderungen speichern** aus, um die neuen Äußerungen zu speichern.

### Hinzufügen einer *vordefinierten* Entität

Der Azure KI Language-Dienst stellt eine Reihe *vordefinierter* Entitäten bereit, die häufig in Konversationsanwendungen verwendet werden.

1. Kehren Sie in Language Studio zur Seite **Schemadefinition** zurück, und klicken Sie dann auf der Registerkarte **Entitäten** auf **&#65291; Hinzufügen**, um eine neue Entität hinzuzufügen.

1. Geben Sie im Dialogfeld **Entität hinzufügen** den Entitätsnamen `Date` ein, und wählen Sie die Entitätsregisterkarte **Vordefiniert** aus. Wählen Sie anschließend **Entität hinzufügen** aus.

1. Stellen Sie auf der Seite für die Entität **Date** im Abschnitt **Gelernt** sicher, dass **Nicht erforderlich** ausgewählt ist. Wählen Sie dann im Abschnitt **Vordefiniert** die Option **+ Neue vordefinierte hinzufügen** aus.

1. Wählen Sie in der Liste **Vordefinierte Entität auswählen** die Option **DateTime** aus, und wählen Sie dann **Speichern** aus.
1. Kehren Sie nach dem Hinzufügen der vordefinierten Entität zur **Datenbeschriftungsseite** zurück
1. Wählen Sie die Absicht **GetDay** aus, und geben Sie die folgende neue Beispieläußerung ein:

    `what day was 01/01/1901?`

1. Wenn die Äußerung hinzugefügt wurde, wählen Sie ***01/01/1901*** und in der angezeigten Dropdownliste **Datum** aus.

1. Fügen Sie ein weiteres Beispiel für die Absicht **GetDay** hinzu:

    `what day will it be on Dec 31st 2099?`

1. Wenn die Äußerung hinzugefügt wurde, ordnen Sie **Dec 31st 2099** der Entität **Date** (Datum) zu.

1. Wählen Sie **Änderungen speichern** aus, um die neuen Äußerungen zu speichern.

### Erneutes Trainieren des Modells

Nachdem Sie das Schema geändert haben, müssen Sie das Modell erneut trainieren und testen.

1. Wählen Sie auf der Seite **Trainingsaufträge** die Option **Trainingsauftrag starten** aus.

1. Wählen Sie im Dialogfeld **Trainingsauftrag starten** die Option **Vorhandenes Modell überschreiben** aus, und geben Sie das Modell **Clock** an. Wählen Sie **Trainieren** aus, um das Modell zu trainieren. Wenn Sie dazu aufgefordert werden, bestätigen Sie, dass Sie das vorhandene Modell überschreiben möchten.

1. Wenn das Training abgeschlossen ist, wird der **Status** des Auftrags zu **Training erfolgreich** aktualisiert.

1. Wählen Sie die Seite **Modellleistung** und dann das Modell **Clock** aus. Sehen Sie sich die Auswertungsmetriken (*Genauigkeit*, *Abruf* und *F1-Score*) und die *Konfusionsmatrix* an, die durch die beim Training durchgeführte Auswertung generiert wurden (beachten Sie, dass aufgrund der geringen Anzahl an Beispieläußerungen möglicherweise nicht alle Absichten in den Ergebnissen enthalten sind).

1. Wählen Sie auf der Seite **Bereitstellen eines Modells** die Option **Bereitstellung hinzufügen** aus.

1. Wählen Sie im Dialogfeld **Bereitstellung hinzufügen** die Option **Vorhandenen Bereitstellungsnamen außer Kraft setzen** aus, und wählen Sie dann **Production** (Produktion) aus.

1. Wählen Sie das Modell **Clock** im Feld **Modell** und dann  **Bereitstellen** aus, um es bereitzustellen. Dieser Vorgang kann einige Zeit dauern.

1. Wenn das Modell bereitgestellt ist, wählen Sie auf der Seite **Bereitstellungen testen** im Feld **Bereitstellungsname** die Bereitstellung **Production** aus, und testen Sie diese mit dem folgenden Text:

    `what's the time in Edinburgh?`

1. Überprüfen Sie das zurückgegebene Ergebnis, das die Absicht **GetTime** und die Entität **Location** (Ort) mit dem Textwert „Edinburgh“ vorhersagen sollte.

1. Testen Sie die folgenden Äußerungen:

    `what time is it in Tokyo?`

    `what date is it on Friday?`

    `what's the date on Weds?`

    `what day was 01/01/2020?`

    `what day will Mar 7th 2030 be?`

## Verwenden des Modells aus einer Client-App

In einem echten Projekt würden Sie Absichten und Entitäten iterativ optimieren, erneut trainieren und erneut testen, bis Sie mit der Vorhersageleistung zufrieden sind. Wenn Sie das Modell getestet haben und mit der Vorhersageleistung zufrieden sind, können Sie es in einer Client-App verwenden, indem Sie die entsprechende REST-Schnittstelle oder ein Runtime-SDK aufrufen.

### Vorbereiten der Entwicklung einer App in Cloud Shell

Sie entwickeln Ihre Language-Understanding-App mithilfe von Cloud Shell im Azure-Portal. Die Codedateien für Ihre App wurden in einem GitHub-Repository bereitgestellt.

1. Verwenden Sie im Azure-Portal die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung aus. Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Bereich am unteren Rand des Azure-Portals.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    **<font color="red">Stellen Sie sicher, dass Sie zur klassischen Version der Cloud Shell gewechselt haben, bevor Sie fortfahren.</font>**

1. Geben Sie im PowerShell-Bereich die folgenden Befehle ein, um das GitHub-Repository für diese Übung zu klonen:

    ```
   rm -r mslearn-ai-language -f
   git clone https://github.com/microsoftlearning/mslearn-ai-language
    ```

    > **Tipp**: Wenn Sie Befehle in die Cloudshell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers einnehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Navigieren Sie nach dem Klonen des Repositorys zu dem Ordner, der die Codedateien der Anwendung enthält:  

    ```
   cd mslearn-ai-language/Labfiles/03-language/Python/clock-client
    ```

### Konfigurieren der Anwendung

1. Führen Sie im Befehlszeilenbereich den folgenden Befehl aus, um die Codedateien im Ordner **clock-client** anzuzeigen:

    ```
   ls -a -l
    ```

    Die Dateien enthalten eine Konfigurationsdatei (**.env**) und eine Codedatei (**clock-client.py**).

1. Erstellen Sie eine virtuelle Python-Umgebung und installieren Sie das Azure KI Language-Unterhaltungs-SDK-Paket sowie andere erforderliche Pakete, indem Sie den folgenden Befehl ausführen:

    ```
   python -m venv labenv
    ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-ai-language-conversations==1.1.0
    ```
1. Geben Sie den folgenden Befehl ein, um die Konfigurationsdatei zu bearbeiten:

    ```
   code .env
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Aktualisieren Sie die Konfigurationswerte so, dass sie den **Endpunkt** und einen **Schlüssel** aus der Azure KI Language-Ressource enthalten, die Sie erstellt haben (verfügbar auf der Seite **Schlüssel und Endpunkt** für Ihre Azure KI Language-Ressource im Azure-Portal).
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie im Code-Editor den Befehl **STRG+S** oder **Rechtsklick > Speichern**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q** oder **Rechtsklick > Beenden**, um den Code-Editor zu schließen, während die Cloud Shell-Befehlszeile geöffnet bleibt.

### Hinzufügen von Code zur Anwendung

1. Geben Sie den folgenden Befehl ein, um die Anwendungscodedatei zu bearbeiten:

    ```
   code clock-client.py
    ```

1. Überprüfen Sie den vorhandenen Code. Sie fügen Code zum Arbeiten mit dem KI Language-Unterhaltungs-SDK hinzu.

    > **Tipp**: Achten Sie beim Hinzufügen von Code zur Codedatei darauf, den richtigen Einzug beizubehalten.

1. Suchen Sie oben in der Codedatei unter den vorhandenen Namespace-Verweisen den Kommentar **Import namespaces** und fügen Sie den folgenden Code hinzu, um die Namespaces zu importieren, die Sie für die Verwendung des KI Language-Unterhaltungs-SDK benötigen:

    ```python
   # Import namespaces
   from azure.core.credentials import AzureKeyCredential
   from azure.ai.language.conversations import ConversationAnalysisClient
    ```

1. Beachten Sie, dass in der **Main**-Funktion bereits Code zum Laden des Vorhersageendpunkts und des Schlüssels aus der Konfigurationsdatei bereitgestellt wurde. Suchen Sie dann den Kommentar **Create a client for the Language service model** und fügen Sie den folgenden Code hinzu, um einen Unterhaltungsanalyseclient für Ihre KI Language-App zu erstellen:

    ```python
   # Create a client for the Language service model
   client = ConversationAnalysisClient(
        ls_prediction_endpoint, AzureKeyCredential(ls_prediction_key))
    ```

1. Beachten Sie, dass der Code in der **Main**-Funktion zu Benutzereingaben auffordert, bis der Benutzer „quit“ eingibt. Suchen Sie in dieser Schleife nach dem Kommentar **Call the Language service model to get intent and entities** (Sprachdienstmodell aufrufen, um Absicht und Entitäten zu erhalten), und fügen Sie den folgenden Code hinzu:

    ```python
   # Call the Language service model to get intent and entities
   cls_project = 'Clock'
   deployment_slot = 'production'

   with client:
        query = userText
        result = client.analyze_conversation(
            task={
                "kind": "Conversation",
                "analysisInput": {
                    "conversationItem": {
                        "participantId": "1",
                        "id": "1",
                        "modality": "text",
                        "language": "en",
                        "text": query
                    },
                    "isLoggingEnabled": False
                },
                "parameters": {
                    "projectName": cls_project,
                    "deploymentName": deployment_slot,
                    "verbose": True
                }
            }
        )

   top_intent = result["result"]["prediction"]["topIntent"]
   entities = result["result"]["prediction"]["entities"]

   print("view top intent:")
   print("\ttop intent: {}".format(result["result"]["prediction"]["topIntent"]))
   print("\tcategory: {}".format(result["result"]["prediction"]["intents"][0]["category"]))
   print("\tconfidence score: {}\n".format(result["result"]["prediction"]["intents"][0]["confidenceScore"]))

   print("view entities:")
   for entity in entities:
        print("\tcategory: {}".format(entity["category"]))
        print("\ttext: {}".format(entity["text"]))
        print("\tconfidence score: {}".format(entity["confidenceScore"]))

   print("query: {}".format(result["result"]["query"]))
    ```

    Der Aufruf des Conversational-Understanding-Modells gibt eine Vorhersage (bzw. ein Ergebnis) zurück, die die oberste (wahrscheinlichste) Absicht sowie alle Entitäten enthält, die in der Eingabeäußerung erkannt wurden. Ihre Clientanwendung muss diese Vorhersage jetzt verwenden, um die entsprechende Aktion zu bestimmen und durchzuführen.

1. Suchen Sie nach dem Kommentar **Apply the appropriate action** (Die entsprechende Aktion anwenden), und fügen Sie den folgenden Code hinzu, der nach Absichten sucht, die von der Anwendung unterstützt werden (**GetTime**, **GetDate** und **GetDay**), und bestimmt, ob relevante Entitäten erkannt wurden, bevor eine vorhandene Funktion zum Erzeugen einer entsprechenden Antwort aufgerufen wird.

    ```python
   # Apply the appropriate action
   if top_intent == 'GetTime':
        location = 'local'
        # Check for entities
        if len(entities) > 0:
            # Check for a location entity
            for entity in entities:
                if 'Location' == entity["category"]:
                    # ML entities are strings, get the first one
                    location = entity["text"]
        # Get the time for the specified location
        print(GetTime(location))

   elif top_intent == 'GetDay':
        date_string = date.today().strftime("%m/%d/%Y")
        # Check for entities
        if len(entities) > 0:
            # Check for a Date entity
            for entity in entities:
                if 'Date' == entity["category"]:
                    # Regex entities are strings, get the first one
                    date_string = entity["text"]
        # Get the day for the specified date
        print(GetDay(date_string))

   elif top_intent == 'GetDate':
        day = 'today'
        # Check for entities
        if len(entities) > 0:
            # Check for a Weekday entity
            for entity in entities:
                if 'Weekday' == entity["category"]:
                # List entities are lists
                    day = entity["text"]
        # Get the date for the specified day
        print(GetDate(day))

   else:
        # Some other intent (for example, "None") was predicted
        print('Try asking me for the time, the day, or the date.')
    ```

1. Speichern Sie Ihre Änderungen (STRG+S), und geben Sie dann den folgenden Befehl ein, um das Programm auszuführen (maximieren Sie den Bereich der Cloud Shell und ändern Sie die Größe der Panels, um mehr Text im Befehlszeilenbereich anzuzeigen):

    ```
   python clock-client.py
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie Äußerungen ein, um die Anwendung zu testen. Versuchen Sie zum Beispiel:

    *Hello*

    *Wie spät ist es?*

    *Wie spät ist es in London?*

    *Welches Datum haben wir?*

    *Welches Datum ist am Sonntag?*

    *Welcher Tag ist heute?*

    *Welcher Tag ist der 01.01.2025?*

    > **Hinweis**: Die Logik in der Anwendung ist absichtlich einfach und unterliegt einer Reihe von Einschränkungen. Wenn Sie beispielsweise die Zeit abrufen, wird nur eine eingeschränkte Gruppe von Städten unterstützt, und Sommerzeit wird ignoriert. Ziel ist es, ein Beispiel für ein typisches Muster für die Verwendung des Sprachdiensts zu sehen, in dem Ihre Anwendung Folgendes muss:
    >   1. Verbinden mit einem Vorhersageendpunkt.
    >   2. Übermitteln einer Äußerung, um eine Vorhersage zu erhalten.
    >   3. Implementieren von Logik, um angemessen auf die vorhergesagte(n) Absicht und Entitäten zu reagieren.

1. Wenn Sie die Tests abgeschlossen haben, geben Sie *quit* (Beenden) ein.

## Bereinigen von Ressourcen

Wenn Sie die Erkundung des Dienstes Azure KI Language abgeschlossen haben, können Sie die in dieser Übung erstellten Ressourcen löschen. Gehen Sie dazu wie folgt vor:

1. Schließen Sie den Azure Cloud Shell-Bereich
1. Navigieren Sie im Azure-Portal zu der Azure KI Language-Ressource, die Sie in diesem Lab erstellt haben.
1. Wählen Sie auf der Ressourcenseite **Löschen** aus, und folgen Sie den Anweisungen zum Löschen der Ressource.

## Weitere Informationen

Weitere Informationen zu Conversational Language Understanding in Azure KI Language finden Sie in der [Dokumentation zu Azure KI Language](https://learn.microsoft.com/azure/ai-services/language-service/conversational-language-understanding/overview).
