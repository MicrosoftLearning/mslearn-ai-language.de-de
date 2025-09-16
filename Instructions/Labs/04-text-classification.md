---
lab:
  title: Benutzerdefinierte Textklassifizierung
  description: Wenden Sie benutzerdefinierte Klassifizierungen auf Texteingaben mithilfe von Azure KI Language an.
---

# Benutzerdefinierte Textklassifizierung

Azure KI Language umfasst mehrere NLP-Funktionen, einschließlich der Identifizierung von Schlüsselbegriffen, der Textzusammenfassung und der Stimmungsanalyse. Der Sprachdienst bietet zudem benutzerdefinierte Features wie benutzerdefinierte Fragen und Antworten und die benutzerdefinierte Textklassifizierung.

Um die benutzerdefinierte Textklassifizierung des Azure KI Language-Diensts zu testen, konfigurieren Sie das Modell mithilfe von Language Studio und verwenden Sie dann eine Python-Anwendung zum Testen.

Obwohl diese Übung auf Python basiert, können Sie ähnliche Anwendungen zur Textklassifizierung mit mehreren sprachspezifischen SDKs entwickeln, einschließlich:

- [Azure KI-Textanalyse-Clientbibliothek für Python](https://pypi.org/project/azure-ai-textanalytics/)
- [Azure KI-Textanalyse-Clientbibliothek für .NET](https://www.nuget.org/packages/Azure.AI.TextAnalytics)
- [Azure KI-Textanalyse-Clientbibliothek für JavaScript](https://www.npmjs.com/package/@azure/ai-text-analytics)

Diese Übung dauert ca. **35** Minuten.

## Bereitstellen einer *Azure KI Language*-Ressource

Wenn Sie noch keine solche Ressource in Ihrem Abonnement haben, müssen Sie eine **Azure KI Language**-Ressource bereitstellen. Darüber hinaus müssen Sie die Funktion **Benutzerdefinierte Textklassifizierung und Extraktion** aktivieren.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Wählen Sie **Ressource erstellen** aus.
1. Suchen Sie im Suchfeld nach dem **Sprachdienst**. Wählen Sie dann in den Ergebnissen unter **Sprachdienst** die Option **Erstellen** aus.
1. Wählen Sie das Feld mit **benutzerdefinierter Textklassifizierung** aus. Wählen Sie dann **Mit dem Erstellen Ihrer Ressource fortfahren** aus.
1. Erstellen Sie eine Ressource mit den folgenden Einstellungen:
    - **Abonnement**: *Ihr Azure-Abonnement*.
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine*.
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
    - **Name**: *Geben Sie einen eindeutigen Namen ein*.
    - **Tarif**: Wählen Sie **F0** (*Free*) oder **S** (*Standard*) aus, falls F nicht verfügbar ist.
    - **Speicherkonto**: Neues Speicherkonto
      - **Speicherkontoname**: *Geben Sie einen eindeutigen Namen ein*.
      - **Speicherkontotyp**: Standard-LRS
    - **Hinweis zu verantwortlicher KI**: Ausgewählt

1. Wählen Sie **Überprüfen und erstellen** aus und dann **Erstellen**, um die Ressource bereitzustellen.
1. Warten Sie, bis die Bereitstellung abgeschlossen ist und wechseln Sie dann zur Ressourcengruppe.
1. Suchen Sie das von Ihnen erstellte Speicherkonto, wählen Sie es aus und überprüfen Sie, ob die _Kontoart_ **StorageV2** ist. Wenn es sich um „v1“ handelt, führen Sie ein Upgrade der Speicherkontoart auf dieser Ressourcenseite durch.

## Konfigurieren des rollenbasierten Zugriffs für Benutzende

> **HINWEIS:** Wenn Sie diesen Schritt auslassen, erhalten Sie eine 403-Fehlermeldung beim Versuch, eine Verbindung zu Ihrem benutzerdefinierten Projekt herzustellen. Es ist wichtig, dass Ihr aktueller Benutzer über diese Rolle verfügt, um auf Blobdaten des Speicherkontos zuzugreifen, auch wenn Sie der Besitzer des Speicherkontos sind.

1. Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto.
2. Wählen Sie im linken Navigationsmenü **Access Control (IAM)** aus.
3. Wählen Sie **Hinzufügen** aus, um Rollenzuordnungen hinzuzufügen, und wählen Sie die Rolle **Besitzer von Speicherblobdaten** für das Speicherkonto aus.
4. Wählen Sie unter **Zugriff zuweisen zu** die Option **Benutzer, Gruppe oder Dienstprinzipal** aus.
5. Wählen Sie **Mitglieder auswählen** aus.
6. Wählen Sie Ihren Benutzer aus. Sie können im Feld **Auswählen** nach Benutzernamen suchen.

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

> **Tipp**: Wenn Sie eine Fehlermeldung erhalten, dass Sie nicht berechtigt sind, diesen Vorgang durchzuführen, müssen Sie eine Rollenzuweisung hinzufügen. Um dies zu beheben, fügen wir zu Benutzenden, die das Lab ausführen, die Rolle „Mitwirkender an Speicherblobdaten“ auf dem Speicherkonto hinzu. Weitere Details finden Sie [auf der Dokumentationsseite](https://learn.microsoft.com/azure/ai-services/language-service/custom-named-entity-recognition/how-to/create-project?tabs=portal%2Clanguage-studio#enable-identity-management-for-your-resource)

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

## Vorbereiten der Entwicklung einer App in Cloud Shell

Um die Funktionen der benutzerdefinierten Textklassifizierung des Azure KI Language-Diensts zu testen, entwickeln Sie eine einfache Konsolenanwendung in Azure Cloud Shell.

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
   cd mslearn-ai-language/Labfiles/04-text-classification/Python/classify-text
    ```

## Konfigurieren der Anwendung

1. Führen Sie im Befehlszeilenbereich den folgenden Befehl aus, um die Codedateien im Ordner **classify-text** anzuzeigen:

    ```
   ls -a -l
    ```

    Die Dateien enthalten eine Konfigurationsdatei (**.env**) und eine Codedatei (**classify-text.py**). Der Text, den Ihre Anwendung analysiert, befindet sich im Unterordner **articles**.

1. Erstellen Sie eine virtuelle Python-Umgebung und installieren Sie das Azure KI Language-Textanalyse-SDK-Paket sowie andere erforderliche Pakete, indem Sie den folgenden Befehl ausführen:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-ai-textanalytics==5.3.0
    ```

1. Geben Sie den folgenden Befehl ein, um die Anwendungskonfigurationsdatei zu bearbeiten:

    ```
   code .env
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Aktualisieren Sie die Konfigurationswerte so, dass sie den **Endpunkt** und einen **Schlüssel** aus der Azure Language-Ressource enthalten, die Sie erstellt haben (verfügbar auf der Seite **Schlüssel und Endpunkt** für Ihre Azure KI-Sprachressource im Azure-Portal). Die Datei sollte bereits die Projekt- und Bereitstellungsnamen für Ihr Textklassifizierungsmodell enthalten.
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie im Code-Editor den Befehl **STRG+S** oder **Rechtsklick > Speichern**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q** oder **Rechtsklick > Beenden**, um den Code-Editor zu schließen, während die Cloud Shell-Befehlszeile geöffnet bleibt.

## Hinzufügen von Code zum Klassifizieren von Dokumenten

1. Geben Sie den folgenden Befehl ein, um die Anwendungscodedatei zu bearbeiten:

    ```
    code classify-text.py
    ```

1. Überprüfen Sie den vorhandenen Code. Sie fügen Code zum Arbeiten mit dem KI Language-Textanalyse-SDK hinzu.

    > **Tipp**: Achten Sie beim Hinzufügen von Code zur Codedatei darauf, den richtigen Einzug beizubehalten.

1. Suchen Sie oben in der Codedatei unter den vorhandenen Namespace-Verweisen den Kommentar **Import namespaces** und fügen Sie den folgenden Code hinzu, um die Namespaces zu importieren, die Sie für die Verwendung des Textanalyse-SDK benötigen:

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

1. Beachten Sie, dass der vorhandene Code alle Dateien im Ordner **articles** liest und eine Liste erstellt, die ihre Inhalte enthält. Suchen Sie dann den Kommentar **Get Classifications**, und fügen Sie den folgenden Code hinzu:

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

1. Speichern Sie Ihre Änderungen (STRG+S), und geben Sie dann den folgenden Befehl ein, um das Programm auszuführen (maximieren Sie den Bereich der Cloud Shell und ändern Sie die Größe der Panels, um mehr Text im Befehlszeilenbereich anzuzeigen):

    ```
   python classify-text.py
    ```

1. Beobachten Sie die Ausgabe. Die Anwendung sollte eine Klassifizierungs- und Konfidenzbewertung für jede Textdatei auflisten.

## Bereinigen

Wenn Sie das Projekt nicht mehr benötigen, können Sie es von Ihrer **Projektseite** in Language Studio löschen. Sie können auch den Azure KI Language-Dienst und das zugeordnete Speicherkonto im [Azure-Portal](https://portal.azure.com) entfernen.
