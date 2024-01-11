---
lab:
  title: Extrahieren benutzerdefinierter Entitäten
  module: Module 3 - Getting Started with Natural Language Processing
---

# Extrahieren benutzerdefinierter Entitäten

Zusätzlich zu anderen Funktionen für die linguistische Datenverarbeitung können Sie mit dem Azure KI Language-Dienst benutzerdefinierte Entitäten definieren und Instanznen davon aus Text extrahieren.

Um die benutzerdefinierte Entitätsextraktion zu testen, erstellen Sie zunächst ein Modell und trainieren es über Azure KI Language Studio. Anschließend verwenden Sie eine Befehlszeilenanwendung, um es zu testen.

## Bereitstellen einer *Azure KI Language*-Ressource

Wenn Sie noch nicht über eine solche Ressource in Ihrem Abonnement verfügen, müssen Sie eine **Azure KI Language**-Dienstressource bereitstellen. Darüber hinaus müssen Sie benutzerdefinierte Textklassifizierung verwenden, um das Feature **Benutzerdefinierte Textklassifizierung und Extraktion** zu aktivieren.

1. Öffnen Sie in einem Browser das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit Ihrem Microsoft-Konto an.
1. Klicken Sie auf die Schaltfläche **Ressource erstellen**, suchen Sie nach *Language*, und erstellen Sie eine **Azure KI Language-Dienstressource**. Wenn Sie nach *zusätzlichen Features* gefragt werden, wählen Sie die Option **Benutzerdefinierte Textklassifizierung  -extraktion** aus. Erstellen Sie die Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie sie.*
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Wählen Sie **F0** (*Free*) oder **S** (*Standard*) aus, falls F nicht verfügbar ist.
    - **Speicherkonto**: Neues Speicherkonto:
      - **Speicherkontoname**: *Geben Sie einen eindeutigen Namen ein*.
      - **Speicherkontotyp**: Standard-LRS
    - **Hinweis zu verantwortlicher KI**: Ausgewählt

1. Wählen Sie **Überprüfen und erstellen** aus und dann **Erstellen**, um die Ressource bereitzustellen.
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, und wechseln Sie dann zur bereitgestellten Ressource.
1. Zeigen Sie die Seite **Schlüssel und Endpunkt** an. Sie benötigen die Informationen auf dieser Seite später in der Übung.

## Hochladen von Beispielanzeigen

Nachdem Sie den Azure KI Language-Dienst und das Speicherkonto erstellt haben, müssen Sie Beispielanzeigen hochladen, mit denen das Modell später trainiert werden soll.

1. Laden Sie auf einer neuen Browserregisterkarte klassifizierte Beispielanzeigen von `https://aka.ms/entity-extraction-ads` herunter, und extrahieren Sie die Dateien in einen Ordner Ihrer Wahl.

2. Navigieren Sie im Azure-Portal zu dem Speicherkonto, das Sie erstellt haben, und wählen Sie es aus.

3. Wählen Sie in Ihrem Speicherkonto **Konfiguration** unter **Einstellungen** aus, aktivieren Sie die Option **Anonymen Blobzugriff zulassen**, und wählen Sie dann **Speichern** aus.

4. Wählen Sie im Menü auf der linken Seite unter **Datenspeicher** die Option **Container** aus. Klicken Sie im angezeigten Bildschirm auf **+ Container**. Geben Sie dem Container den Namen `classifieds`, und legen Sie die Option **Anonyme Zugriffsebene** auf **Container (Anonymer Lesezugriff für Container und Blobs)** fest.

    > **HINWEIS**: Achten Sie beim Konfigurieren eines Speicherkontos für eine echte Lösung darauf, die geeignete Zugriffsebene zuzuweisen. Weitere Informationen zu den einzelnen Zugriffsebenen finden Sie in der [Azure Storage-Dokumentation](https://learn.microsoft.com/azure/storage/blobs/anonymous-read-access-configure).

5. Nachdem der Container erstellt wurde, wählen Sie ihn aus, und klicken Sie auf die Schaltfläche **Hochladen**, und laden Sie die heruntergeladenen Beispielanzeigen hoch.

## Erstellen eines benutzerdefinierten Projekts zur Erkennung benannter Entitäten

Jetzt können Sie ein Projekt für die benutzerdefinierte benannte Entitätserkennung neu erstellen. Dieses Projekt bietet einen Arbeitsplatz, an dem Sie Ihr Model erstellen, trainieren und bereitstellen können.

> **HINWEIS**: Sie können Ihr Modell auch über die REST-API erstellen, trainieren und bereitstellen.

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

1. Wählen Sie oben im Portal im Menü **Neu erstellen** die Option *Benutzerdefinierte benannte Entitätserkennung** aus.

1. Erstellen Sie ein neues Projekt mit den folgenden Einstellungen:
    - **Speicher verbinden**: *Dieser Wert ist wahrscheinlich bereits ausgefüllt. Ändern Sie ihn in Ihr Speicherkonto, sofern dies nicht bereits der Fall ist.*
    - **Grundlegende Informationen:**
    - **Name**: `CustomEntityLab`
        - **Primäre Textsprache**: Englisch (USA)
        - **Enthält Ihr Dataset Dokumente, die nicht in der gleichen Sprache verfasst sind?** : *Nein*
        - **Beschreibung:** `Custom entities in classified ads`
    - **Container:**
        - **Blobspeichercontainer**: Kleinanzeigen
        - **Sind Ihre Dateien mit Klassen getaggt?**: Nein, ich muss meine Dateien als Teil dieses Projekts taggen.

## Beschriften Ihrer Daten

Nachdem Sie das Projekt erstellt haben, müssen Sie Ihre Daten taggen, um das Modell für die Erkennung von Entitäten zu trainieren.

1. Wenn die Seite **Datenbeschriftung** noch nicht geöffnet ist, wählen Sie im Bereich auf der linken Seite die Option **Datenbeschriftung** aus. Es wird eine Liste der Dateien angezeigt, die Sie in Ihr Speicherkonto hochgeladen haben.
1. Wählen Sie rechts im Bereich **Aktivität** die Option **Entität hinzufügen** aus, und fügen Sie eine neue Entität namens `ItemForSale` hinzu.
1.  Wiederholen Sie den vorherigen Schritt, um die folgenden Entitäten zu erstellen:
    - `Price`
    - `Location`
1. Nachdem Sie Ihre drei Entitäten erstellt haben, wählen Sie **Ad 1.txt** aus, damit Sie sie lesen können.
1. In *Ad 1.txt*: 
    1. Markieren Sie den Text *face cord of firewood*, und wählen Sie die Entität **ItemForSale** aus.
    1. Markieren Sie den Text *Denver, CO*, und wählen Sie die Entität **Location** aus.
    1. Markieren Sie den Text *$90*, und wählen Sie die Entität **Price** aus.
1. Beachten Sie im Bereich **Aktivität**, dass dieses Dokument zum Dataset zum Trainieren des Modells hinzugefügt wird.
1. Wechseln Sie mit der Schaltfläche **Nächstes Dokument** zum nächsten Dokument, und setzen Sie das Zuweisen von Text zu den entsprechenden Entitäten für die gesamte Gruppe von Dokumenten fort. Fügen Sie also alle dem Trainingsdataset hinzu.
1. Wenn Sie das letzte Dokument (*Ad 9.txt*) beschriftet haben, speichern Sie die Beschriftungen.

## Trainieren Ihres Modells

Nachdem Sie Ihre Daten getaggt haben, müssen Sie Ihr Modell trainieren.

1. Wählen Sie im Bereich auf der linken Seite **Trainingsaufträge** aus.
2. Wählen Sie **Trainingsauftrag starten** aus.
3. Wählen Sie die Option zum Trainieren eines neuen Modells mit dem Namen `ExtractAds` aus.
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
2. Wählen Sie **Bereitstellung hinzufügen** aus, geben Sie dann den Namen `AdEntities` ein, und wählen Sie das Modell **ExtractAds** aus.
3. Klicken Sie auf **Bereitstellen**, um Ihr Modell bereitzustellen.

## Vorbereiten der Entwicklung einer App in Visual Studio Code

Um die benutzerdefinierten Extraktionsfunktionen für Entitäten des Azure KI Language-Diensts zu testen, entwickeln Sie eine einfache Konsolenanwendung in Visual Studio Code.

> **Tipp**: Wenn Sie das Repository **mslearn-ai-language** bereits geklont haben, öffnen Sie es in Visual Studio Code. Führen Sie andernfalls die folgenden Schritte aus, um es in Ihrer Entwicklungsumgebung zu klonen.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/mslearn-ai-language` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus.

## Konfigurieren der Anwendung

Es werden Anwendungen für C# und Python bereitgestellt sowie eine Beispieltextdatei, mit der Sie die Zusammenfassung testen können. Beide Apps verfügen über die gleiche Funktionalität. Zuerst schließen Sie einige wichtige Teile der Anwendung ab, um die Verwendung Ihrer Azure KI Language-Ressource zu aktivieren.

1. Navigieren Sie im Bereich ** Explorer** in Visual Studio Code zum Ordner **Labfiles/05-custom-entity-recognition**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **CSharp** oder **Python** und den darin enthaltenen Ordner **custom-entities**. Jeder Ordner enthält die sprachspezifischen Dateien für eine App, in die Sie Azure KI Language-Textklassifizierungsfunktionen integrieren.
1. Klicken Sie mit der rechten Maustaste auf den Ordner **custom-entities**, der Ihre Codedateien enthält, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Azure KI Language-Textanalyse-SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#:**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.3.0
    ```

    **Python**:

    ```
    pip install azure-ai-textanalytics==5.3.0
    ```

1. Öffnen Sie im Bereich **Explorer** im Ordner **custom-entities** die Konfigurationsdatei für Ihre bevorzugte Sprache.

    - **C#** : appsettings.json
    - **Python**: .env
    
1. Aktualisieren Sie die Konfigurationswerte so, dass sie den **Endpunkt** und einen **Schlüssel** aus der Azure KI Language-Ressource enthalten, die Sie erstellt haben (verfügbar auf der Seite **Schlüssel und Endpunkt** für Ihre Azure KI Language-Ressource im Azure-Portal). Die Datei sollte bereits die Projekt- und Bereitstellungsnamen für Ihr benutzerdefiniertes Extraktionsmodell für Entitäten enthalten.
1. Speichern Sie die Konfigurationsdatei.

## Hinzufügen von Code zum Extrahieren von Entitäten

Jetzt können Sie den Azure KI Language-Dienst verwenden, um benutzerdefinierte Entitäten aus Text zu extrahieren.

1. Erweitern Sie den Ordner **ads** im Ordner **custom-entities**, um die klassifizierten Anzeigen anzuzeigen, die Ihre Anwendung analysieren soll.
1. Öffnen Sie im Ordner **custom-entities** die Codedatei für die Clientanwendung:

    - **C#** : Program.cs
    - **Python**: custom-entities.py

1. Suchen Sie den Kommentar **Import namespaces**. Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das Textanalyse-SDK verwenden zu können:

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

1. Beachten Sie, dass in der Funktion **Main** der Code zum Laden des Schlüssels und des Endpunkts des Azure KI Language-Diensts und der Projekt- und Bereitstellungsnamen aus der Konfigurationsdatei bereits bereitgestellt wurde. Suchen Sie dann den Kommentar **Create client using endpoint and key** (Client mit Endpunkt und Schlüssel erstellen), und fügen Sie den folgenden Code hinzu, um einen Client für die Textanalyse-API zu erstellen:

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

1. Beachten Sie in der Funktion **Main**, dass der vorhandene Code alle Dateien im Ordner **articles** liest und eine Liste erstellt, die deren Inhalt enthält. Im Fall des C#-Codes wird die Liste der **TextDocumentInput**-Objekte verwendet, um den Dateinamen als ID und die Sprache einzuschließen. In Python wird eine einfache Liste der Textinhalte verwendet.
1. Suchen Sie die Kommentar **Extract entities**, und fügen Sie den folgenden Code hinzu:

    **C#** : Program.cs

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

1. Speichern Sie die Änderungen an der Codedatei.

## Testen Ihrer Anwendung

Ihre Anwendung kann jetzt getestet werden.

1. Geben Sie im integrierten Terminal für den Ordner **classify-text** den folgenden Befehl zur Ausführung des Programms ein:

    - **C#** : `dotnet run`
    - **Python**: `python custom-entities.py`

    > **Tipp**: Sie können das Symbol **Fenstergröße maximieren** (**^**) in der Terminalsymbolleiste verwenden, um mehr vom Konsolentext anzuzeigen.

1. Beobachten Sie die Ausgabe. Die Anwendung sollte Details der Entitäten in jeder Textdatei auflisten.

## Bereinigen

Wenn Sie das Projekt nicht mehr benötigen, können Sie es von Ihrer **Projektseite** in Language Studio löschen. Sie können auch den Azure KI Language-Dienst und das zugeordnete Speicherkonto im [Azure-Portal](https://portal.azure.com) entfernen.
