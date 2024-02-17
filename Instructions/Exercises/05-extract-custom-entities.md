---
lab:
  title: Extrahieren benutzerdefinierter Entitäten
  module: Module 3 - Getting Started with Natural Language Processing
---

# Extrahieren benutzerdefinierter Entitäten

Neben anderen Funktionen zur Verarbeitung natürlicher Sprache können Sie mit dem Azure AI Language Service benutzerdefinierte Entitäten definieren und Instanzen dieser Entitäten aus Text extrahieren.

Um die benutzerdefinierte Entitätsextraktion zu testen, erstellen Sie zunächst ein Modell und trainieren es über Azure KI Language Studio. Anschließend verwenden Sie eine Befehlszeilenanwendung, um es zu testen.

## Bereitstellen einer *Azure KI Language*-Ressource

Wenn Sie noch keine solche Ressource in Ihrem Abonnement haben, müssen Sie eine **Azure KI Language**-Ressource bereitstellen. Darüber hinaus müssen Sie die Funktion **Benutzerdefinierte Textklassifizierung und Extraktion** aktivieren.

1. Öffnen Sie das Azure-Portal in einem Browser unter `https://portal.azure.com` und melden Sie sich mit Ihrem Microsoft-Konto an.
1. Klicken Sie auf die Schaltfläche **Ressource erstellen**, suchen Sie nach *Language*, und erstellen Sie eine **Azure KI Language-Dienstressource**. Wenn Sie nach *zusätzlichen Funktionen* gefragt werden, wählen Sie die Option **Benutzerdefinierte Textklassifizierung und -extraktion** aus. Erstellen Sie die Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen oder erstellen Sie eine Ressourcengruppe*.
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Wählen Sie **F0** (*kostenlos*) oder **S** (*Standard*), wenn F nicht verfügbar ist.
    - **Speicherkonto**: Neues Speicherkonto:
      - **Speicherkontoname**: *Geben Sie einen eindeutigen Namen ein*.
      - **Speicherkontotyp**: Standard-LRS
    - **Hinweis zu verantwortlicher KI**: Ausgewählt

1. Wählen Sie **Überprüfen und erstellen** aus und dann **Erstellen**, um die Ressource bereitzustellen.
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, und wechseln Sie dann zur bereitgestellten Ressource.
1. Sehen Sie sich die Seite **Schlüssel und Endpunkt** an. Sie benötigen die Informationen auf dieser Seite später in der Übung.

## Hochladen von Beispielanzeigen

Nachdem Sie den Azure KI Language-Dienst und das Speicherkonto erstellt haben, müssen Sie Beispielanzeigen hochladen, mit denen das Modell später trainiert werden soll.

1. Laden Sie in einem neuen Browserfenster als Beispiel klassifizierte Anzeigen von `https://aka.ms/entity-extraction-ads`herunter und extrahieren Sie die Dateien in einen Ordner Ihrer Wahl.

2. Navigieren Sie im Azure-Portal zu dem Speicherkonto, das Sie erstellt haben, und wählen Sie es aus.

3. Wählen Sie in Speicherkonto unter **Einstellungen** die Option **Konfiguration** aus, und aktivieren Sie die Option **Anonymen Blob-Zugriff zulassen**. Wählen Sie anschließend **Speichern** aus.

4. Klicken Sie im linken Menü unter **Datenspeicher** auf **Container**. Klicken Sie im angezeigten Bildschirm auf **+ Container**. Geben Sie dem Container den Namen `classifieds`, und legen Sie die Option **Anonyme Zugriffsebene** auf **Container (Anonymer Lesezugriff für Container und Blobs)** fest.

    > **HINWEIS**: Wenn Sie ein Speicherkonto für eine echte Lösung konfigurieren, achten Sie darauf, dass Sie die richtige Zugriffsstufe zuweisen. Weitere Informationen zu den einzelnen Zugriffsebenen finden Sie in der [Dokumentation zu Azure Storage](https://learn.microsoft.com/azure/storage/blobs/anonymous-read-access-configure).

5. Wählen Sie den erstellten Container aus, und klicken Sie auf die Schaltfläche **Hochladen**, um die heruntergeladenen Beispielanzeigen hochzuladen.

## Erstellen eines benutzerdefinierten Projekts zur Erkennung benannter Entitäten

Erstellen Sie nun ein benutzerdefiniertes Projekt zur Erkennung benannter Entitäten. Dieses Projekt bietet einen Arbeitsplatz, an dem Sie Ihr Model erstellen, trainieren und bereitstellen können.

> **Hinweis**: Sie können Ihr Modell auch über die REST-API erstellen, trainieren und bereitstellen.

1. Öffnen Sie in einem neuen Browserfenster das Azure KI Language Studio-Portal unter `https://language.cognitive.azure.com/`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Wenn Sie zur Auswahl einer Sprachressource aufgefordert werden, wählen Sie die folgenden Einstellungen aus:

    - **Azure-Verzeichnis**: Das Azure-Verzeichnis mit Ihrem Abonnement.
    - **Azure-Abonnement**: Ihr Azure-Abonnement.
    - **Ressourcentyp**: Sprache.
    - **Sprachressource**: Die Azure KI Language-Ressource, die Sie zuvor erstellt haben.

    Sollten Sie <u>nicht</u> zur Auswahl einer Sprachressource aufgefordert, kann dies daran liegen, dass Ihr Abonnement mehrere Sprachressourcen enthält. Gehen Sie in diesem Fall folgendermaßen vor:

    1. Klicken Sie auf der Leiste oben auf die Schaltfläche **Einstellungen (&#9881;)**.
    2. Gehen Sie auf der Seite **Einstellungen** zur Registerkarte **Ressourcen**.
    3. Wählen Sie die soeben erstellte Sprachressource aus, und klicken Sie auf **Switch resource** (Ressource wechseln).
    4. Klicken Sie oben auf der Seite auf **Language Studio**, um zur Startseite von Language Studio zurückzukehren.

1. Wählen Sie oben im Portal im Menü **Neu erstellen** die Option *Benutzerdefinierte benannte Entitätserkennung**.

1. Erstellen Sie ein neues Projekt mit den folgenden Einstellungen:
    - **Speicher verbinden**: *Dieser Wert ist wahrscheinlich bereits ausgefüllt. Ändern Sie ihn gegebenenfalls und geben Sie Ihr Speicherkonto an.*
    - **Grundlegende Informationen:**
    - **Name**: `CustomEntityLab`
        - **Primäre Textsprache**: Englisch (USA)
        - **Enthält Ihr Dataset Dokumente, die nicht in der gleichen Sprache verfasst sind?** *Nein*
        - **Beschreibung:** `Custom entities in classified ads`
    - **Container:**
        - **Blob-Speichercontainer**: klassifiziert
        - **Sind Ihre Dateien mit Klassen getaggt?**: Nein, ich muss meine Dateien als Teil dieses Projekts taggen.

## Beschriften Ihrer Daten

Nachdem Sie das Projekt erstellt haben, müssen Sie Ihre Daten taggen, um das Modell für die Erkennung von Entitäten zu trainieren.

1. Wenn die Seite **Datenbeschriftung** noch nicht geöffnet ist, wählen Sie im Bereich auf der linken Seite **Datenbeschriftung** aus. Es wird eine Liste der Dateien angezeigt, die Sie in Ihr Speicherkonto hochgeladen haben.
1. Wählen Sie rechts im Bereich **Aktivität** die Option **Entität hinzufügen** und fügen Sie eine neue Entität mit Namen `ItemForSale` hinzu.
1.  Wiederholen Sie den vorherigen Schritt, um die folgenden Entitäten zu erstellen:
    - `Price`
    - `Location`
1. Nachdem Sie die drei Entitäten erstellt haben, wählen Sie **Ad 1.txt** aus, und lesen Sie die Anzeige.
1. In *Ad 1.txt*: 
    1. Heben Sie den Text *face cord of firewood* hervor und wählen Sie die Entität **ItemForSale** aus.
    1. Heben Sie den Text *Denver, CO* hervor und wählen Sie die Entität **Ort** aus.
    1. Heben Sie den Text *$90* hervor, und wählen Sie die Entität **Preis** aus.
1. Beachten Sie im Fenster **Aktivität**, dass dieses Dokument dem Datensatz für das Training des Modells hinzugefügt wird.
1. Gehen Sie zum nächsten Dokument (Schaltfläche **Nächstes Dokument**). Weisen Sie weiter Text zu den entsprechenden Entitäten zu. Weisen Sie den Text dem gesamten Satz von Dokumenten zu, indem Sie alles dem Trainingsdatensatz hinzufügen.
1. Nachdem Sie das letzte Dokument (*Ad 9.txt*) beschriftet haben, speichern Sie die Beschriftungen.

## Trainieren Ihres Modells

Nachdem Sie Ihre Daten getaggt haben, müssen Sie Ihr Modell trainieren.

1. Wählen Sie im Fensterbereich links die Option **Trainingsaufträge** aus.
2. Klicken Sie auf **Trainingsauftrag starten**.
3. Trainieren des neuen Modells `ExtractAds`
4. Wählen Sie **Automatisches Aufteilen des Testsatzes aus Trainingsdaten** aus.

    > **TIPP**: Verwenden Sie bei Ihren eigenen Extraktionsprojekten die Testaufteilung, die am besten zu Ihren Daten passt. Bei konsistenteren Daten und größeren Datasets teilt der Azure KI Language-Dienst den Testsatz automatisch prozentual auf. Bei kleineren Datasets ist es wichtig, mit der richtigen Auswahl an möglichen Eingabedokumenten zu trainieren.

5. Klicken Sie auf **Trainieren**.

    > **WICHTIG**: Das Training des Modells kann mehrere Minuten dauern. Sie erhalten eine Benachrichtigung, wenn der Vorgang abgeschlossen ist.

## Auswerten des Modells

Bei echten Anwendungen sollten Sie die Leistung Ihres Modells unbedingt auswerten und verbessern, um zu überprüfen, ob es die erwartete Leistung erbringt. Die Details Ihres trainierten Modells sowie alle fehlgeschlagenen Tests werden links auf zwei Seiten angezeigt.

Wählen Sie im Menü auf der linken Seite **Leistung der Modelle** aus, und wählen Sie Ihr `ExtractAds`-Modell aus. Es werden die Bewertung Ihres Modells, Leistungsmetriken und der Zeitpunkt des Trainings angezeigt. Sie können sehen, ob bei einigen Testdokumenten Fehler aufgetreten sind, und diese Fehler helfen Ihnen zu verstehen, wo Sie sich verbessern müssen.

## Bereitstellen Ihres Modells

Sobald Sie mit den Trainingsergebnissen Ihres Modells zufrieden sind, stellen Sie es bereit. Auf diese Weise können Sie Entitäten über die API erkennen.

1. Wählen Sie im linken Bereich **Modell bereitstellen** aus.
2. Wählen Sie **Bereitstellung hinzufügen** aus, geben Sie dann den Namen `AdEntities` ein, und wählen Sie das Modell **ExtractAds** aus der Dropdownliste für das Modell aus.
3. Klicken Sie auf **Bereitstellen**, um Ihr Modell bereitzustellen.

## Vorbereitung auf das Entwickeln einer App in Visual Studio Code

Um die benutzerdefinierten Entitätsextraktion des Azure KI Language-Dienstes zu testen, entwickeln Sie eine einfache Konsolenanwendung in Visual Studio Code.

> **Tipp**: Wenn Sie das Repository **mslearn-ai-language** bereits geklont haben, öffnen Sie es in Visual Studio Code. Führen Sie andernfalls die folgenden Schritte aus, um es in Ihre Entwicklungsumgebung zu klonen.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/mslearn-ai-language` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus.

## Konfigurieren der Anwendung

Es werden Anwendungen für C# und Python bereitgestellt sowie eine Beispieltextdatei, mit der Sie die Zusammenfassung testen können. Beide Apps verfügen über die gleiche Funktionalität. In dieser Übung stellen Sie zunächst einige wichtige Teile der Anwendung fertig, um die Verwendung Ihrer Azure KI Language-Ressource zu aktivieren.

1. Wechseln Sie im Fensterbereich **Explorer** in Visual Studio Code zum Ordner **Labfiles/05-custom-entity-recognition**, und erweitern Sie je nach der bevorzugten Sprache den Ordner **CSharp** oder **Python** und den darin enthaltenen Ordner **custom-entities**. Jeder Ordner enthält die sprachspezifischen Dateien für eine App, in die Sie die Textklassifizierungsfunktion von Azure KI Language integrieren werden.
1. Klicken Sie mit der rechten Maustaste auf den Ordner **custom-entities**, der Ihre Code-Dateien enthält und öffnen Sie ein integriertes Terminal. Installieren Sie dann das SDK-Paket für die Azure KI Language-Textanalyse. Führen Sie dafür den entsprechenden Befehl für Ihre bevorzugte Sprache aus:

    **C#:**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.3.0
    ```

    **Python**:

    ```
    pip install azure-ai-textanalytics==5.3.0
    ```

1. Öffnen Sie im Bereich **Explorer** im Ordner **custom-entities** die Konfigurationsdatei für Ihre bevorzugte Sprache.

    - **C#**: appsettings.json
    - **Python**: .env
    
1. Aktualisieren Sie die Konfigurationswerte, sodass sie den **Endpunkt** und einen **Schlüssel** aus der von Ihnen erstellten Azure Language-Ressource (verfügbar auf der Seite **Schlüssel und Endpunkt** für Ihre Azure KI Language-Ressource im Azure-Portal) enthalten. Die Datei sollte bereits die Projekt- und Bereitstellungsnamen für Ihr benutzerdefiniertes Modell der Entitätsextraktion enthalten.
1. Speichern Sie die Konfigurationsdatei.

## Hinzufügen von Code zum Extrahieren von Entitäten

Jetzt ist die Vorbereitung abgeschlossen und Sie können mit dem Azure KI Language-Dienst benutzerdefinierte Entitäten aus Text extrahieren.

1. Erweitern Sie den Ordner **Anzeigen** im Ordner **custom-entities**. Sie sehen darin die klassifizierten Anzeigen die Ihre Anwendung analysieren wird.
1. Öffnen Sie im Ordner **custom-entities** die Codedatei für die Clientanwendung:

    - **C#**: Program.cs
    - **Python**: custom-entities.py

1. Suchen Sie den Kommentar **Namespaces importieren**. Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das Textanalyse-SDK verwenden zu können:

    **C#**: Programs.cs

    ```csharp
    // import namespaces
    using Azure;
    using Azure.AI.TextAnalytics;
    ```

    **Python**: custom-entities.py

    ```python
    # import namespaces
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.textanalytics import TextAnalyticsClient
    ```

1. In der **Main**-Funktion ist zu beachten, dass der Code zum Laden des Azure KI Language Service-Endpunkts und -Schlüssels sowie der Projekt- und Bereitstellungsnamen aus der Konfigurationsdatei bereits vorhanden ist. Suchen Sie dann den Kommentar **Create client using endpoint and key** (Client mit Endpunkt und Schlüssel erstellen), und fügen Sie den folgenden Code hinzu, um einen Client für die Textanalyse-API zu erstellen:

    **C#**: Programs.cs

    ```csharp
    // Create client using endpoint and key
    AzureKeyCredential credentials = new(aiSvcKey);
    Uri endpoint = new(aiSvcEndpoint);
    TextAnalyticsClient aiClient = new(endpoint, credentials);
    ```

    **Python**: custom-entities.py

    ```Python
    # Create client using endpoint and key
    credential = AzureKeyCredential(ai_key)
    ai_client = TextAnalyticsClient(endpoint=ai_endpoint, credential=credential)
    ```

1. beachten Sie in der **Main**-Funktion, dass der vorhandene Code alle Dateien im Ordner **Anzeigen** liest und eine Liste erstellt, die ihre Inhalte enthält. Der C#-Codes verwendet eine Liste von **TextDocumentInput**-Objekten, um den Dateinamen (als eine ID) und die Sprache anzugeben. In Python wird eine einfache Liste der Textinhalte verwendet.
1. Suchen Sie die Kommentarextraktionsentitäten****, und fügen Sie den folgenden Code hinzu:

    **C#**: Program.cs

    ```csharp
    // Extract entities
    RecognizeCustomEntitiesOperation operation = await aiClient.RecognizeCustomEntitiesAsync(WaitUntil.Completed, batchedDocuments, projectName, deploymentName);

    await foreach (RecognizeCustomEntitiesResultCollection documentsInPage in operation.Value)
    {
        foreach (RecognizeEntitiesResult documentResult in documentsInPage)
        {
            Console.WriteLine($"Result for \"{documentResult.Id}\":");

            if (documentResult.HasError)
            {
                Console.WriteLine($"  Error!");
                Console.WriteLine($"  Document error code: {documentResult.Error.ErrorCode}");
                Console.WriteLine($"  Message: {documentResult.Error.Message}");
                Console.WriteLine();
                continue;
            }

            Console.WriteLine($"  Recognized {documentResult.Entities.Count} entities:");

            foreach (CategorizedEntity entity in documentResult.Entities)
            {
                Console.WriteLine($"  Entity: {entity.Text}");
                Console.WriteLine($"  Category: {entity.Category}");
                Console.WriteLine($"  Offset: {entity.Offset}");
                Console.WriteLine($"  Length: {entity.Length}");
                Console.WriteLine($"  ConfidenceScore: {entity.ConfidenceScore}");
                Console.WriteLine($"  SubCategory: {entity.SubCategory}");
                Console.WriteLine();
            }

            Console.WriteLine();
        }
    }
    ```

    **Python**: custom-entities.py

    ```Python
    # Extract entities
    operation = ai_client.begin_recognize_custom_entities(
        batchedDocuments,
        project_name=project_name,
        deployment_name=deployment_name
    )

    document_results = operation.result()

    for doc, custom_entities_result in zip(files, document_results):
        print(doc)
        if custom_entities_result.kind == "CustomEntityRecognition":
            for entity in custom_entities_result.entities:
                print(
                    "\tEntity '{}' has category '{}' with confidence score of '{}'".format(
                        entity.text, entity.category, entity.confidence_score
                    )
                )
        elif custom_entities_result.is_error is True:
            print("\tError with code '{}' and message '{}'".format(
                custom_entities_result.error.code, custom_entities_result.error.message
                )
            )
    ```

1. Speichern Sie die Änderungen an der Code-Datei.

## Testen Ihrer Anwendung

Ihre Anwendung kann jetzt getestet werden.

1. Geben Sie das integrierte Terminal für den Ordner **classify-text** zurück und dann den folgenden Befehl zur Ausführung des Programms ein:

    - **C#** : `dotnet run`
    - **Python**: `python custom-entities.py`

    > **Tipp**: Sie können das Symbol " **Panelgröße** maximieren" (**^**) in der Terminalsymbolleiste verwenden, um mehr über den Konsolentext anzuzeigen.

1. Beobachten Sie die Ausgabe. Die Anwendung sollte Details der Entitäten in den einzelnen Textdateien auflisten.

## Bereinigung

Wenn Sie das Projekt nicht mehr benötigen, können Sie es von Ihrer **Projektseite** in Language Studio löschen. Sie können auch den Azure KI Language-Dienst und das zugeordnete Speicherkonto im [Azure-Portal](https://portal.azure.com) entfernen.
