---
lab:
  title: Benutzerdefinierte Textklassifizierung
  module: Module 3 - Getting Started with Natural Language Processing
---

# Benutzerdefinierte Textklassifizierung

Azure KI Language umfasst mehrere NLP-Funktionen, einschließlich der Identifizierung von Schlüsselbegriffen, der Textzusammenfassung und der Stimmungsanalyse. Der Sprachdienst bietet zudem benutzerdefinierte Features wie benutzerdefinierte Fragen und Antworten und die benutzerdefinierte Textklassifizierung.

Damit Sie die benutzerdefinierte Textklassifizierung des Azure KI Language-Diensts testen können, konfigurieren Sie das Modell mithilfe von Language Studio und testen es anschließend mit einer einfachen Befehlszeilenanwendung, die in Cloud Shell ausgeführt wird. Die hier verwendeten Muster und Funktionen können Sie für Ihre echten Anwendungen übernehmen.

## Bereitstellen einer *Azure KI Language*-Ressource

Wenn Sie noch keine solche Ressource in Ihrem Abonnement haben, müssen Sie eine **Azure KI Language**-Ressource bereitstellen. Darüber hinaus müssen Sie die Funktion **Benutzerdefinierte Textklassifizierung und Extraktion** aktivieren.

1. Öffnen Sie in einem Browser das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit Ihrem Microsoft-Konto an.
1. Wählen Sie oben im Portal das Suchfeld aus, suchen Sie nach `Azure AI services`, und erstellen Sie eine **Sprachdienst**-Ressource.
1. Wählen Sie das Feld mit **benutzerdefinierter Textklassifizierung** aus. Wählen Sie dann **Mit dem Erstellen Ihrer Ressource fortfahren** aus.
1. Erstellen Sie eine Ressource mit den folgenden Einstellungen:
    - **Abonnement**: *Ihr Azure-Abonnement*.
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine*.
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*:
    - **Name**: *Geben Sie einen eindeutigen Namen ein*.
    - **Tarif**: Wählen Sie **F0** (*Free*) oder **S** (*Standard*) aus, falls F nicht verfügbar ist.
    - **Speicherkonto**: Neues Speicherkonto
      - **Speicherkontoname**: *Geben Sie einen eindeutigen Namen ein*.
      - **Speicherkontotyp**: Standard-LRS
    - **Hinweis zu verantwortlicher KI**: Ausgewählt

1. Wählen Sie **Überprüfen und erstellen** aus und dann **Erstellen**, um die Ressource bereitzustellen.
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, und wechseln Sie dann zur bereitgestellten Ressource.
1. Sehen Sie sich die Seite **Schlüssel und Endpunkt** an. Sie benötigen die Informationen auf dieser Seite später in der Übung.

## Hochladen von Beispielartikeln

Nachdem Sie den Azure KI Language-Dienst und das Speicherkonto erstellt haben, müssen Sie Beispielartikel hochladen, mit denen das Modell später trainiert werden soll.

1. Laden Sie auf einer neuen Browserregisterkarte Beispielartikel von `https://aka.ms/classification-articles` herunter, und extrahieren Sie die Dateien in einen Ordner Ihrer Wahl.

1. Navigieren Sie im Azure-Portal zu dem Speicherkonto, das Sie erstellt haben, und wählen Sie es aus.

1. Wählen Sie unter **Einstellungen** in Ihrem Speicherkonto **Konfiguration** aus. Aktivieren Sie auf dem Bildschirm „Konfiguration“ die Option **Anonymen Blobzugriff zulassen**, und wählen Sie dann **Speichern** aus.

1. Wählen Sie im Menü auf der linken Seite unter **Datenspeicher** die Option **Container** aus. Klicken Sie im angezeigten Bildschirm auf **+ Container**. Geben Sie dem Container den Namen `articles`, und legen Sie die Option **Anonyme Zugriffsebene** auf **Container (Anonymer Lesezugriff für Container und Blobs)** fest.

    > **HINWEIS**: Wenn Sie ein Speicherkonto für eine echte Lösung konfigurieren, achten Sie darauf, dass Sie die richtige Zugriffsstufe zuweisen. Weitere Informationen zu den einzelnen Zugriffsebenen finden Sie in der [Azure Storage-Dokumentation](https://learn.microsoft.com/azure/storage/blobs/anonymous-read-access-configure).

1. Nachdem Sie den Container erstellt haben, wählen Sie ihn aus und wählen Sie dann die Schaltfläche **Hochladen** aus. Wählen Sie **Nach Dateien durchsuchen** aus, um nach den heruntergeladenen Beispielartikeln zu suchen. Wählen Sie dann die Option **Hochladen** aus.

## Erstellen eines Projekts zur benutzerdefinierten Textklassifizierung

Nachdem die Konfiguration nun abgeschlossen ist, erstellen Sie ein Projekt zur benutzerdefinierten Textklassifizierung. Dieses Projekt bietet einen Arbeitsplatz, an dem Sie Ihr Model erstellen, trainieren und bereitstellen können.

> **HINWEIS**: In diesem Lab wird **Language Studio** verwendet. Sie können Ihr Model jedoch auch über die REST-API erstellen, trainieren und bereitstellen.

1. Öffnen Sie auf einer neuen Browserregisterkarte das Azure KI Language Studio-Portal unter `https://language.cognitive.azure.com/`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Wenn Sie zur Auswahl einer Sprachressource aufgefordert werden, wählen Sie die folgenden Einstellungen aus:

    - **Azure-Verzeichnis**: Das Azure-Verzeichnis mit Ihrem Abonnement.
    - **Azure-Abonnement**: Ihr Azure-Abonnement.
    - **Ressourcentyp**: Sprache.
    - **Sprachressource**: Die Azure KI Language-Ressource, die Sie zuvor erstellt haben.

    Sollten Sie <u>nicht</u> zur Auswahl einer Sprachressource aufgefordert, kann dies daran liegen, dass Ihr Abonnement mehrere Sprachressourcen enthält. Gehen Sie in diesem Fall folgendermaßen vor:

    1. Klicken Sie auf der Leiste oben auf die Schaltfläche **Einstellungen (&#9881;)**.
    2. Gehen Sie auf der Seite **Einstellungen** zur Registerkarte **Ressourcen**.
    3. Wählen Sie die soeben erstellte Sprachressource aus, und klicken Sie auf **Switch resource** (Ressource wechseln).
    4. Klicken Sie oben auf der Seite auf **Language Studio**, um zur Homepage von Language Studio zurückzukehren.

1. Klicken Sie oben im Portal im Menü **Neu erstellen** auf **Benutzerdefinierte Textklassifizierung**.
1. Dann erscheint die Seite **Speicher verbinden**. Alle Werte sind bereits ausgefüllt. Wählen Sie also **Weiter** aus.
1. Wählen Sie auf der Seite **Projekttyp auswählen** die Option **Klassifizierung mit einfacher Bezeichnung** aus. Wählen Sie **Weiter**aus.
1. Legen Sie im Bereich **Grundlegende Informationen eingeben** Folgendes fest:
    - **Name**: `ClassifyLab`  
    - **Primäre Textsprache**: Englisch (USA)
    - **Beschreibung:** `Custom text lab`

1. Wählen Sie **Weiter** aus.
1. Wählen Sie auf der Seite **Container auswählen** in der Dropdownliste **Blobspeichercontainer** Ihren *Artikelcontainer* aus.
1. Wählen Sie die Option **Nein, ich muss meine Dateien als Teil dieses Projekts bezeichnen** aus. Wählen Sie **Weiter**aus.
1. Wählen Sie **Projekt erstellen** aus.

## Beschriften Ihrer Daten

Nachdem Sie das Projekt erstellt haben, müssen Sie Ihre Daten taggen (markieren), um das Modell für die Klassifizierung von Text zu trainieren.

1. Wählen Sie auf der linken Seite **Datenbeschriftung** aus, falls es noch nicht ausgewählt ist. Es wird eine Liste der Dateien angezeigt, die Sie in Ihr Speicherkonto hochgeladen haben.
1. Wählen Sie rechts im Bereich **Aktivität** die Option **+ Klasse hinzufügen** aus.  Die Artikel in diesem Lab umfassen vier Klassen, die Sie erstellen müssen: `Classifieds`, `Sports`, `News` und `Entertainment`.

    ![Screenshot: Seite zum Taggen von Daten mit der Schaltfläche „Klasse hinzufügen“.](../media/tag-data-add-class-new.png#lightbox)

1. Nachdem Sie Ihre vier Klassen erstellt haben, wählen Sie **Artikel 1** aus, um zu beginnen. Sie können den Artikel lesen, die Klasse der Datei definieren und das Dataset (Training oder Test) zuweisen.
1. Weisen Sie jedem Artikel im Bereich **Aktivität** auf der rechten Seite die entsprechende Klasse und das entsprechende Dataset (Training oder Test) zu.  Sie können eine Bezeichnung aus der Liste der Bezeichnungen auf der rechten Seite auswählen und jeden Artikel mithilfe der Optionen am unteren Rand des Aktivitätsbereichs auf **Training** oder **Testen** festlegen. Wählen Sie **Nächstes Dokument** aus, um zum nächsten Dokument zu wechseln. Weisen Sie für die Verwendung in diesem Lab die folgenden Klassen und Datasets zu, um das Modell zu trainieren und. es zu testen:

    | Artikel  | Klasse  | Dataset  |
    |---------|---------|---------|
    | Artikel 1 | Sport | Training |
    | Artikel 10 | Neuigkeiten | Training |
    | Artikel 11 | Entertainment | Testen |
    | Artikel 12 | Neuigkeiten | Testen |
    | Artikel 13 | Sport | Testen |
    | Artikel 2 | Sport | Training |
    | Artikel 3 | Kleinanzeigen | Training |
    | Artikel 4 | Kleinanzeigen | Training |
    | Artikel 5 | Entertainment | Training |
    | Artikel 6 | Entertainment | Training |
    | Artikel 7 | Neuigkeiten | Training |
    | Artikel 8 | Neuigkeiten | Training |
    | Artikel 9 | Entertainment | Training |

    > **HINWEIS** In Language Studio sind die Dateien in alphabetischer Reihenfolge sortiert, daher ist die Liste oben nicht der Reihe nach angeordnet. Rufen Sie immer beide Dokumentseiten auf, wenn Sie Ihre Artikel markieren.

1. Wählen Sie **Bezeichnungen speichern** aus, um Ihre Bezeichnungen zu speichern.

## Trainieren Ihres Modells

Nachdem Sie Ihre Daten getaggt haben, müssen Sie Ihr Modell trainieren.

1. Wählen Sie im Menü auf der linken Seite **Trainingsaufträge** aus.
1. Wählen Sie **Trainingsauftrag starten** aus.
1. Trainieren Sie ein neues Modell mit dem Namen `ClassifyArticles`.
1. Wählen Sie die Option **Manuelle Aufteilung von Trainings- und Testdaten verwenden** aus.

    > **TIPP** In Ihren eigenen Klassifizierungsprojekten teilt der Azure KI Language-Dienst den Testsatz automatisch prozentual auf. Dies ist bei umfangreichen Datasets hilfreich. Bei kleineren Datasets ist es wichtig, das Training mit einer geeigneten Klassenverteilung durchzuführen.

1. Wählen Sie **Trainieren** aus.

> **WICHTIG** Das Training des Modells kann mehrere Minuten dauern. Sie erhalten eine Benachrichtigung, wenn der Vorgang abgeschlossen ist.

## Auswerten des Modells

Bei echten Anwendungen zur Textklassifizierung sollten Sie die Leistung Ihres Modells unbedingt auswerten und verbessern.

1. Wählen Sie **Modellleistung** aus, und wählen Sie ihr Modell **ClassifyArticles** aus. Es werden die Bewertung Ihres Modells, Leistungsmetriken und der Zeitpunkt des Trainings angezeigt. Wenn die Bewertung Ihres Modells nicht 100 % beträgt, bedeutet dies, dass eines der zum Testen verwendeten Dokumente nicht der Bezeichnung entspricht, die ihm gegeben wurde. Anhand solcher Fehler können Sie erkennen, wo Nachbesserungen nötig sind.
1. Wählen Sie die Registerkarte **Testsatzdetails** aus. Im Falle von Fehlern können Sie auf dieser Registerkarte die Artikel sehen, die Sie zum Testen angegeben haben und was das Modell für sie vorausgesagt hat und ob dies im Widerspruch zu ihrer Testbezeichnung steht. Die Registerkarte zeigt standardmäßig nur falsche Vorhersagen an. Sie können die Option **Nur Abweichungen anzeigen** umschalten, um alle Artikel zu sehen, die Sie zum Testen angegeben haben, und um zu sehen, wie sie jeweils vorhergesagt wurden.

## Bereitstellen Ihres Modells

Sobald Sie mit den Trainingsergebnissen Ihres Modells zufrieden sind, stellen Sie es bereit. Auf diese Weise können Sie Text über die API klassifizieren.

1. Wählen Sie im linken Bereich **Modell bereitstellen** aus.
1. Wählen Sie **Bereitstellung hinzufügen** aus, geben Sie dann `articles` in das Feld **Neuen Bereitstellungsnamen erstellen** ein, und wählen Sie die **ClassifyArticles** im Feld **Modell** aus.
1. Wählen Sie **Bereitstellen** aus, um Ihr Modell bereitzustellen.
1. Nachdem Ihr Modell bereitgestellt wurde, lassen Sie diese Seite geöffnet. Im nächsten Schritt benötigen Sie Ihren Projekt- und Bereitstellungsnamen.

## Vorbereiten der Entwicklung einer App in Visual Studio Code

Um die benutzerdefinierten Textklassifizierungsfunktionen des Azure KI Language-Diensts zu testen, entwickeln Sie eine einfache Konsolenanwendung in Visual Studio Code.

> **Tipp**: Wenn Sie das Repository **mslearn-ai-language** bereits geklont haben, öffnen Sie es in Visual Studio Code. Führen Sie andernfalls die folgenden Schritte aus, um es in Ihre Entwicklungsumgebung zu klonen.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/mslearn-ai-language` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.

    > **Hinweis:** Wenn Visual Studio Code eine Popupnachricht anzeigt, in der Sie aufgefordert werden, dem geöffneten Code zu vertrauen, klicken Sie auf die Option **Ja, ich vertraue den Autoren** im Popupfenster.

4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus.

## Konfigurieren der Anwendung

Es werden Anwendungen für C# und Python bereitgestellt sowie eine Beispieltextdatei, mit der Sie die Zusammenfassung testen können. Beide Apps verfügen über die gleiche Funktionalität. Zuerst schließen Sie einige wichtige Teile der Anwendung ab, um die Verwendung Ihrer Azure KI Language-Ressource zu aktivieren.

1. Wechseln Sie in Visual Studio Code im Bereich **Explorer** zum Ordner **Labfiles/04-text-classification**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **CSharp** oder **Python** und den darin enthaltenen Ordner **classify-text**. Jeder Ordner enthält die sprachspezifischen Dateien für eine App, in die Sie Azure KI Language-Textklassifizierungsfunktionen integrieren.
1. Klicken Sie mit der rechten Maustaste auf den Ordner **classify-text**, der Ihre Codedateien enthält, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Azure KI Language-Textanalyse-SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#:**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.3.0
    ```

    **Python**:

    ```
    pip install azure-ai-textanalytics==5.3.0
    ```

1. Öffnen Sie im Bereich **Explorer** im Ordner **classify-text** die Konfigurationsdatei für Ihre bevorzugte Sprache.

    - **C#**: appsettings.json
    - **Python**: .env
    
1. Aktualisieren Sie die Konfigurationswerte so, dass sie den **Endpunkt** und einen **Schlüssel** aus der Azure KI Language-Ressource enthalten, die Sie erstellt haben (verfügbar auf der Seite **Schlüssel und Endpunkt** für Ihre Azure KI Language-Ressource im Azure-Portal). Die Datei sollte bereits die Projekt- und Bereitstellungsnamen für Ihr Textklassifizierungsmodell enthalten.
1. Speichern Sie die Konfigurationsdatei.

## Hinzufügen von Code zum Klassifizieren von Dokumenten

Jetzt können Sie den Azure KI Language-Dienst verwenden, um Dokumente zu klassifizieren.

1. Erweitern Sie den Ordner **articles** im Ordner **classify-text**, um die Textartikel anzuzeigen, die von der Anwendung klassifiziert werden.
1. Öffnen Sie im Ordner **classify-text** die Codedatei für die Clientanwendung:

    - **C#** : Program.cs
    - **Python**: classify-text.py

1. Suchen Sie den Kommentar **Import namespaces**. Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das Textanalyse-SDK verwenden zu können:

    **C#**: Programs.cs

    ```csharp
    // import namespaces
    using Azure;
    using Azure.AI.TextAnalytics;
    ```

    **Python**: classify-text.py

    ```python
    # import namespaces
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.textanalytics import TextAnalyticsClient
    ```

1. Beachten Sie, dass in der Funktion **Main** der Code zum Laden des Schlüssels und des Endpunkts des Azure KI Language-Diensts und der Projekt- und Bereitstellungsnamen aus der Konfigurationsdatei bereits bereitgestellt wurde. Suchen Sie dann den Kommentar **Create client using endpoint and key** (Client mit Endpunkt und Schlüssel erstellen), und fügen Sie den folgenden Code hinzu, um einen Client für die Textanalyse-API zu erstellen:

    **C#**: Programs.cs

    ```csharp
    // Create client using endpoint and key
    AzureKeyCredential credentials = new AzureKeyCredential(aiSvcKey);
    Uri endpoint = new Uri(aiSvcEndpoint);
    TextAnalyticsClient aiClient = new TextAnalyticsClient(endpoint, credentials);
    ```

    **Python**: classify-text.py

    ```Python
    # Create client using endpoint and key
    credential = AzureKeyCredential(ai_key)
    ai_client = TextAnalyticsClient(endpoint=ai_endpoint, credential=credential)
    ```

1. Beachten Sie in der Funktion **Main**, dass der vorhandene Code alle Dateien im Ordner **articles** liest und eine Liste erstellt, die deren Inhalt enthält. Suchen Sie dann den Kommentar **Get Classifications**, und fügen Sie den folgenden Code hinzu:

    **C#** : Program.cs

    ```csharp
    // Get Classifications
    ClassifyDocumentOperation operation = await aiClient.SingleLabelClassifyAsync(WaitUntil.Completed, batchedDocuments, projectName, deploymentName);

    int fileNo = 0;
    await foreach (ClassifyDocumentResultCollection documentsInPage in operation.Value)
    {
        
        foreach (ClassifyDocumentResult documentResult in documentsInPage)
        {
            Console.WriteLine(files[fileNo].Name);
            if (documentResult.HasError)
            {
                Console.WriteLine($"  Error!");
                Console.WriteLine($"  Document error code: {documentResult.Error.ErrorCode}");
                Console.WriteLine($"  Message: {documentResult.Error.Message}");
                continue;
            }

            Console.WriteLine($"  Predicted the following class:");
            Console.WriteLine();

            foreach (ClassificationCategory classification in documentResult.ClassificationCategories)
            {
                Console.WriteLine($"  Category: {classification.Category}");
                Console.WriteLine($"  Confidence score: {classification.ConfidenceScore}");
                Console.WriteLine();
            }
            fileNo++;
        }
    }
    ```
    
    **Python**: classify-text.py

    ```Python
    # Get Classifications
    operation = ai_client.begin_single_label_classify(
        batchedDocuments,
        project_name=project_name,
        deployment_name=deployment_name
    )

    document_results = operation.result()

    for doc, classification_result in zip(files, document_results):
        if classification_result.kind == "CustomDocumentClassification":
            classification = classification_result.classifications[0]
            print("{} was classified as '{}' with confidence score {}.".format(
                doc, classification.category, classification.confidence_score)
            )
        elif classification_result.is_error is True:
            print("{} has an error with code '{}' and message '{}'".format(
                doc, classification_result.error.code, classification_result.error.message)
            )
    ```

1. Speichern Sie die Änderungen an der Codedatei.

## Testen Ihrer Anwendung

Ihre Anwendung kann jetzt getestet werden.

1. Geben Sie das integrierte Terminal für den Ordner **classify-text** zurück und dann den folgenden Befehl zur Ausführung des Programms ein:

    - **C#** : `dotnet run`
    - **Python**: `python classify-text.py`

    > **Tipp**: Sie können das Symbol " **Panelgröße** maximieren" (**^**) in der Terminalsymbolleiste verwenden, um mehr über den Konsolentext anzuzeigen.

1. Beobachten Sie die Ausgabe. Die Anwendung sollte eine Klassifizierungs- und Konfidenzbewertung für jede Textdatei auflisten.


## Bereinigung

Wenn Sie das Projekt nicht mehr benötigen, können Sie es von Ihrer **Projektseite** in Language Studio löschen. Sie können auch den Azure KI Language-Dienst und das zugeordnete Speicherkonto im [Azure-Portal](https://portal.azure.com) entfernen.
