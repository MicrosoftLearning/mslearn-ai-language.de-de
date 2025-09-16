---
lab:
  title: Extrahieren benutzerdefinierter Entitäten
  description: 'Trainieren Sie ein Modell, um benutzerdefinierte Entitäten aus der Texteingabe mithilfe von Azure KI Language zu extrahieren.'
---

# Extrahieren benutzerdefinierter Entitäten

Neben anderen Funktionen zur Verarbeitung natürlicher Sprache können Sie mit dem Azure AI Language Service benutzerdefinierte Entitäten definieren und Instanzen dieser Entitäten aus Text extrahieren.

Um die benutzerdefinierte Entitätsextraktion zu testen, erstellen Sie zunächst ein Modell und trainieren es über Azure KI Language Studio. Anschließend verwenden Sie Python, um es zu testen.

Obwohl diese Übung auf Python basiert, können Sie ähnliche Anwendungen zur Textklassifizierung mit mehreren sprachspezifischen SDKs entwickeln, einschließlich:

- [Azure KI-Textanalyse-Clientbibliothek für Python](https://pypi.org/project/azure-ai-textanalytics/)
- [Azure KI-Textanalyse-Clientbibliothek für .NET](https://www.nuget.org/packages/Azure.AI.TextAnalytics)
- [Azure KI-Textanalyse-Clientbibliothek für JavaScript](https://www.npmjs.com/package/@azure/ai-text-analytics)

Diese Übung dauert ca. **35** Minuten.

## Bereitstellen einer *Azure KI Language*-Ressource

Wenn Sie noch keine solche Ressource in Ihrem Abonnement haben, müssen Sie eine **Azure KI Language**-Ressource bereitstellen. Darüber hinaus müssen Sie die Funktion **Benutzerdefinierte Textklassifizierung und Extraktion** aktivieren.

1. Öffnen Sie das Azure-Portal in einem Browser unter `https://portal.azure.com` und melden Sie sich mit Ihrem Microsoft-Konto an.
1. Klicken Sie auf die Schaltfläche **Ressource erstellen**, suchen Sie nach *Sprache*, und erstellen Sie eine Ressource vom Typ **Sprachdienst**. Wenn Sie sich auf der Seite für *Weitere Features auswählen* befinden, wählen Sie das benutzerdefinierte Feature mit **der Extraktion der benutzerdefinierten benannten Entitätserkennung** aus. Erstellen Sie die Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen oder erstellen Sie eine Ressourcengruppe*.
    - **Region**: *Wählen Sie aus einer der folgenden Regionen*\*
        - Australien (Osten)
        - Indien, Mitte
        - East US
        - USA (Ost) 2
        - Nordeuropa
        - USA Süd Mitte
        - Schweiz, Norden
        - UK, Süden
        - Europa, Westen
        - USA, Westen 2
        - USA, Westen 3
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Wählen Sie **F0** (*kostenlos*) oder **S** (*Standard*), wenn F nicht verfügbar ist.
    - **Speicherkonto**: Neues Speicherkonto:
      - **Speicherkontoname**: *Geben Sie einen eindeutigen Namen ein*.
      - **Speicherkontotyp**: Standard-LRS
    - **Hinweis zu verantwortlicher KI**: Ausgewählt

1. Wählen Sie **Überprüfen und erstellen** aus und dann **Erstellen**, um die Ressource bereitzustellen.
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, und wechseln Sie dann zur bereitgestellten Ressource.
1. Sehen Sie sich die Seite **Schlüssel und Endpunkt** an. Sie benötigen die Informationen auf dieser Seite später in der Übung.

## Konfigurieren des rollenbasierten Zugriffs für Benutzende

> **Hinweis**: Wenn Sie diesen Schritt auslassen, erhalten Sie eine 403-Fehlermeldung, wenn Sie versuchen, eine Verbindung zu Ihrem benutzerdefinierten Projekt herzustellen. Es ist wichtig, dass Ihr aktueller Benutzer über diese Rolle verfügt, um auf Blobdaten des Speicherkontos zuzugreifen, auch wenn Sie der Besitzer des Speicherkontos sind.

1. Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto.
2. Wählen Sie im linken Navigationsmenü **Access Control (IAM)** aus.
3. Wählen Sie **Hinzufügen**, um Rollenzuordnungen hinzuzufügen, und wählen Sie die Rolle **Mitwirkende für die Speicherung von Blobdaten** für das Speicherkonto.
4. Wählen Sie unter **Zugriff zuweisen zu** die Option **Benutzer, Gruppe oder Dienstprinzipal** aus.
5. Wählen Sie **Mitglieder auswählen** aus.
6. Wählen Sie Ihren Benutzer aus. Sie können im Feld **Auswählen** nach Benutzernamen suchen.

## Hochladen von Beispielanzeigen

Nachdem Sie den Azure KI Language-Dienst und das Speicherkonto erstellt haben, müssen Sie Beispielanzeigen hochladen, mit denen das Modell später trainiert werden soll.

1. Laden Sie in einem neuen Browserfenster als Beispiel klassifizierte Anzeigen von `https://aka.ms/entity-extraction-ads`herunter und extrahieren Sie die Dateien in einen Ordner Ihrer Wahl.

2. Navigieren Sie im Azure-Portal zu dem Speicherkonto, das Sie erstellt haben, und wählen Sie es aus.

3. Wählen Sie in Speicherkonto unter **Einstellungen** die Option **Konfiguration** aus, und aktivieren Sie die Option **Anonymen Blob-Zugriff zulassen**. Wählen Sie anschließend **Speichern** aus.

4. Klicken Sie im linken Menü unter **Datenspeicher** auf **Container**. Klicken Sie im angezeigten Bildschirm auf **+ Container**. Geben Sie dem Container den Namen `classifieds`, und legen Sie die Option **Anonyme Zugriffsebene** auf **Container (Anonymer Lesezugriff für Container und Blobs)** fest.

    > **HINWEIS**: Wenn Sie ein Speicherkonto für eine echte Lösung konfigurieren, achten Sie darauf, dass Sie die richtige Zugriffsstufe zuweisen. Weitere Informationen zu den einzelnen Zugriffsebenen finden Sie in der [Dokumentation zu Azure Storage](https://learn.microsoft.com/azure/storage/blobs/anonymous-read-access-configure).

5. Wählen Sie den erstellten Container aus, und klicken Sie auf die Schaltfläche **Hochladen**, um die heruntergeladenen Beispielanzeigen hochzuladen.

## Erstellen eines benutzerdefinierten Projekts zur Erkennung benannter Entitäten

Jetzt können Sie ein benutzerdefiniertes Benanntes Entitätserkennungsprojekt erstellen. Dieses Projekt bietet einen Arbeitsplatz, an dem Sie Ihr Model erstellen, trainieren und bereitstellen können.

> **Hinweis**: Sie können Ihr Modell auch über die REST-API erstellen, trainieren und bereitstellen.

1. Öffnen Sie in einem neuen Browserfenster das Azure KI Language Studio-Portal unter `https://language.cognitive.azure.com/`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Wenn Sie zur Auswahl einer Sprachressource aufgefordert werden, wählen Sie die folgenden Einstellungen aus:

    - **Azure-Verzeichnis**: Das Azure-Verzeichnis mit Ihrem Abonnement.
    - **Azure-Abonnement**: Ihr Azure-Abonnement.
    - **Ressourcentyp**: Sprache.
    - **Sprachressource**: Die Azure KI Language-Ressource, die Sie zuvor erstellt haben.

    Sollten Sie <u>nicht</u> zur Auswahl einer Sprachressource aufgefordert, kann dies daran liegen, dass Ihr Abonnement mehrere Sprachressourcen enthält. Gehen Sie in diesem Fall folgendermaßen vor:

    1. Wählen Sie auf der Leiste oben auf der Seite die Schaltfläche **Einstellungen (&#9881;)** aus.
    2. Gehen Sie auf der Seite **Einstellungen** zur Registerkarte **Ressourcen**.
    3. Wählen Sie die soeben erstellte Sprachressource aus, und klicken Sie auf **Switch resource** (Ressource wechseln).
    4. Klicken Sie oben auf der Seite auf **Language Studio**, um zur Startseite von Language Studio zurückzukehren.

1. Wählen Sie oben im Portal im Menü **Neues Erstellen** die Option **Benutzerdefinierte benannte Entitätserkennung** aus.

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

> **Tipp**: Wenn Sie eine Fehlermeldung erhalten, dass Sie nicht berechtigt sind, diesen Vorgang durchzuführen, müssen Sie eine Rollenzuweisung hinzufügen. Um dies zu beheben, fügen wir zu Benutzenden, die das Lab ausführen, die Rolle „Mitwirkender an Speicherblobdaten“ auf dem Speicherkonto hinzu. Weitere Details finden Sie [auf der Dokumentationsseite](https://learn.microsoft.com/azure/ai-services/language-service/custom-named-entity-recognition/how-to/create-project?tabs=portal%2Clanguage-studio#enable-identity-management-for-your-resource)

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
1. Beachten Sie im Bereich **Aktivität**, dass dieses Dokument dem Dataset für das Training des Modells hinzugefügt wird.
1. Verwenden Sie die Schaltfläche **Nächstes Dokument**, um zum nächsten Dokument zu wechseln und den entsprechenden Entitäten für die gesamte Gruppe von Dokumenten Text zuzuweisen, und fügen Sie sie dem Trainingsdatensatz hinzu.
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

## Vorbereiten der Entwicklung einer App in Cloud Shell

Um die Funktionen der benutzerdefinierten Entitätsextraktion des Azure KI Language-Diensts zu testen, entwickeln Sie eine einfache Konsolenanwendung in Azure Cloud Shell.

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
    ```

1. After the repo has been cloned, navigate to the folder containing the application code files:  

    ```
    cd mslearn-ai-language/Labfiles/05-custom-entity-recognition/Python/custom-entities
    ```

## Configure your application

1. In the command line pane, run the following command to view the code files in the **custom-entities** folder:

    ```
   ls -a -l
    ```

    The files include a configuration file (**.env**) and a code file (**custom-entities.py**). The text your application will analyze is in the **ads** subfolder.

1. Create a Python virtual environment and install the Azure AI Language Text Analytics SDK package and other required packages by running the following command:

    ```
   python -m venv labenv ./labenv/bin/Activate.ps1 pip install -r requirements.txt azure-ai-textanalytics==5.3.0
    ```

1. Enter the following command to edit the application configuration file:

    ```
   code .env
    ```

    The file is opened in a code editor.

1. Update the configuration values to include the  **endpoint** and a **key** from the Azure Language resource you created (available on the **Keys and Endpoint** page for your Azure AI Language resource in the Azure portal).The file should already contain the project and deployment names for your custom entity extraction model.
1. After you've replaced the placeholders, within the code editor, use the **CTRL+S** command or **Right-click > Save** to save your changes and then use the **CTRL+Q** command or **Right-click > Quit** to close the code editor while keeping the cloud shell command line open.

## Add code to extract entities

1. Enter the following command to edit the application code file:

    ```
    code custom-entities.py
    ```

1. Review the existing code. You will add code to work with the AI Language Text Analytics SDK.

    > **Tip**: As you add code to the code file, be sure to maintain the correct indentation.

1. At the top of the code file, under the existing namespace references, find the comment **Import namespaces** and add the following code to import the namespaces you will need to use the Text Analytics SDK:

    ```python
   # import namespaces
   from azure.core.credentials import AzureKeyCredential
   from azure.ai.textanalytics import TextAnalyticsClient
    ```

1. Beachten Sie, dass in der **Main**-Funktion bereits Code zum Laden des Azure KI Language-Dienstendpunkts und -schlüssels sowie der Projekt- und Bereitstellungsnamen aus der Konfigurationsdatei bereitgestellt wurde. Suchen Sie dann den Kommentar **Create client using endpoint and key** und fügen Sie den folgenden Code hinzu, um einen Client für die Textanalyse zu erstellen:

    ```Python
   # Create client using endpoint and key
   credential = AzureKeyCredential(ai_key)
   ai_client = TextAnalyticsClient(endpoint=ai_endpoint, credential=credential)
    ```

1. Beachten Sie, dass der vorhandene Code alle Dateien im Ordner **ads** liest und eine Liste erstellt, die ihre Inhalte enthält. Suchen Sie den Kommentar **Extract entities** und fügen Sie den folgenden Code hinzu:

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

1. Speichern Sie Ihre Änderungen (STRG+S), und geben Sie dann den folgenden Befehl ein, um das Programm auszuführen (maximieren Sie den Bereich der Cloud Shell und ändern Sie die Größe der Panels, um mehr Text im Befehlszeilenbereich anzuzeigen):

    ```
   python custom-entities.py
    ```

1. Beobachten Sie die Ausgabe. Die Anwendung sollte Details der Entitäten in den einzelnen Textdateien auflisten.

## Bereinigung

Wenn Sie das Projekt nicht mehr benötigen, können Sie es von Ihrer **Projektseite** in Language Studio löschen. Sie können auch den Azure KI Language-Dienst und das zugeordnete Speicherkonto im [Azure-Portal](https://portal.azure.com) entfernen.
