---
lab:
  title: Analysieren von Text
  description: 'Verwenden Sie Azure KI Language zum Analysieren von Text, einschließlich Spracherkennung, Stimmungsanalyse, Schlüsselbegriffserkennung und Entitätserkennung.'
---

# Analysieren von Text

**Azure KI Language** unterstützt die Analyse von Text, einschließlich Spracherkennung, Stimmungsanalyse, Schlüsselbegriffserkennung und Entitätserkennung.

Angenommen, ein Reiseunternehmen möchte Hotelbewertungen verarbeiten, die auf der Website des Unternehmens abgegeben wurden. Mit Azure KI Language können sie die Sprache, in der jede Bewertung verfasst ist, die Stimmung (positiv, neutral oder negativ) der Bewertungen, Schlüsselbegriffe, die auf die Hauptthemen der Bewertung hinweisen, und benannte Entitäten wie Orte, Sehenswürdigkeiten oder Personen, die in den Bewertungen erwähnt werden, bestimmen. In dieser Übung verwenden Sie das Azure KI Language-Python-SDK für die Textanalyse, um eine einfache Anwendung für Hotelbewertungen basierend auf diesem Beispiel zu implementieren.

Obwohl diese Übung auf Python basiert, können Sie ähnliche Anwendungen zur Textanalyse mit mehreren sprachspezifischen SDKs entwickeln, einschließlich:

- [Azure KI-Textanalyse-Clientbibliothek für Python](https://pypi.org/project/azure-ai-textanalytics/)
- [Azure KI-Textanalyse-Clientbibliothek für .NET](https://www.nuget.org/packages/Azure.AI.TextAnalytics)
- [Azure KI-Textanalyse-Clientbibliothek für JavaScript](https://www.npmjs.com/package/@azure/ai-text-analytics)

Diese Übung dauert ca. **30** Minuten.

## Bereitstellen einer *Azure KI Language*-Ressource

Wenn Sie noch nicht über eine solche Ressource in Ihrem Abonnement verfügen, müssen Sie eine **Azure KI Language Services**-Ressource Ihrem Azure-Abonnement bereitstellen.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Wählen Sie **Ressource erstellen** aus.
1. Suchen Sie im Suchfeld nach dem **Sprachdienst**. Wählen Sie dann in den Ergebnissen unter **Sprachdienst** die Option **Erstellen** aus.
1. Klicken Sie auf **Continue to create your resource** (Mit Erstellung Ihrer Ressource fortfahren).
1. Stellen Sie die Ressource mithilfe der folgenden Einstellungen bereit:
    - **Abonnement**: *Ihr Azure-Abonnement*.
    - **Ressourcengruppe**: *Wählen oder erstellen Sie eine Ressourcengruppe*.
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein*.
    - **Tarif**: Wählen Sie **F0** (*kostenlos*) oder **S** (*Standard*), wenn F nicht verfügbar ist.
    - **Hinweis zu verantwortungsvoller KI**: Zustimmen.
1. Wählen Sie **Überprüfen und erstellen** aus und dann **Erstellen**, um die Ressource bereitzustellen.
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, und wechseln Sie dann zur bereitgestellten Ressource.
1. Sie können die Seite **Schlüssel und Endpunkte** im Abschnitt **Ressourcenverwaltungt** anzeigen. Sie benötigen die Informationen auf dieser Seite später in der Übung.

## Klonen des Repositorys für diesen Kurs

Sie entwickeln Ihren Code mithilfe von Cloud Shell aus dem Azure-Portal. Die Codedateien für Ihre App wurden in einem GitHub-Repository bereitgestellt.

1. Verwenden Sie im Azure-Portal die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung aus. Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Bereich am unteren Rand des Azure-Portals.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    **<font color="red">Stellen Sie sicher, dass Sie zur klassischen Version der Cloud Shell gewechselt haben, bevor Sie fortfahren.</font>**

1. Geben Sie im PowerShell-Bereich die folgenden Befehle ein, um das GitHub-Repository für diese Übung zu klonen:

    ```
    rm -r mslearn-ai-language -f
    git clone https://github.com/microsoftlearning/mslearn-ai-language
    ```

    > **Tipp**: Wenn Sie Befehle in die Cloud Shell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers in Anspruch nehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Navigieren Sie nach dem Klonen des Repositorys zu dem Ordner, der die Codedateien der Anwendung enthält:  

    ```
    cd mslearn-ai-language/Labfiles/01-analyze-text/Python/text-analysis
    ```

## Konfigurieren der Anwendung

1. Führen Sie im Befehlszeilenbereich den folgenden Befehl aus, um die Codedateien im Ordner **text-analysis** anzuzeigen:

    ```
   ls -a -l
    ```

    Die Dateien enthalten eine Konfigurationsdatei (**.env**) und eine Codedatei (**text-analysis.py**). Der Text, den Ihre Anwendung analysiert, befindet sich im Unterordner **reviews**.

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

1. Aktualisieren Sie die Konfigurationswerte, sodass sie den **Endpunkt** und einen **Schlüssel** aus der von Ihnen erstellten Azure Language-Ressource (verfügbar auf der Seite **Schlüssel und Endpunkt** für Ihre Azure KI Language-Ressource im Azure-Portal) enthalten.
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie im Code-Editor den Befehl **STRG+S** oder **Rechtsklick > Speichern**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q** oder **Rechtsklick > Beenden**, um den Code-Editor zu schließen, während die Cloud Shell-Befehlszeile geöffnet bleibt.

## Hinzufügen von Code zum Herstellen einer Verbindung mit Ihrer Azure KI Language-Ressource

1. Geben Sie den folgenden Befehl ein, um die Anwendungscodedatei zu bearbeiten:

    ```
    code text-analysis.py
    ```

1. Überprüfen Sie den vorhandenen Code. Sie fügen Code zum Arbeiten mit dem KI Language-Textanalyse-SDK hinzu.

    > **Tipp**: Achten Sie beim Hinzufügen von Code zur Codedatei darauf, den richtigen Einzug beizubehalten.

1. Suchen Sie oben in der Codedatei unter den vorhandenen Namespace-Verweisen den Kommentar **Import namespaces** und fügen Sie den folgenden Code hinzu, um die Namespaces zu importieren, die Sie für die Verwendung des Textanalyse-SDK benötigen:

    ```python
   # import namespaces
   from azure.core.credentials import AzureKeyCredential
   from azure.ai.textanalytics import TextAnalyticsClient
    ```

1. Beachten Sie, dass in der **Main**-Funktion bereits Code zum Laden des Azure KI Language-Dienstendpunkts und -schlüssels aus der Konfigurationsdatei bereitgestellt wurde. Suchen Sie dann den Kommentar **Create client using endpoint and key** (Client mit Endpunkt und Schlüssel erstellen), und fügen Sie den folgenden Code hinzu, um einen Client für die Textanalyse-API zu erstellen:

    ```Python
   # Create client using endpoint and key
   credential = AzureKeyCredential(ai_key)
   ai_client = TextAnalyticsClient(endpoint=ai_endpoint, credential=credential)
    ```

1. Speichern Sie Ihre Änderungen (STRG+S), und geben Sie dann den folgenden Befehl ein, um das Programm auszuführen (maximieren Sie den Bereich der Cloud Shell und ändern Sie die Größe der Panels, um mehr Text im Befehlszeilenbereich anzuzeigen):

    ```
   python text-analysis.py
    ```

1. Beobachten Sie die Ausgabe, da der Code ohne Fehler ausgeführt werden sollte, wobei der Inhalt jeder Bewertungstextdatei im Ordner **reviews** angezeigt wird. Die Anwendung erstellt erfolgreich einen Client für die Textanalyse-API, nutzt ihn aber nicht. Dies soll im nächsten Abschnitt behoben werden.

## Hinzufügen von Code zum Erkennen der Sprache

Nachdem Sie nun einen Client für die API erstellt haben, können wir diesen verwenden, um die Sprache zu erkennen, in der die einzelnen Bewertungen verfasst sind.

1. Suchen Sie im Code-Editor den Kommentar **Get language**. Fügen Sie dann den Code hinzu, der zum Erkennen der Sprache im jeweiligen Bewertungsdokument erforderlich ist:

    ```python
   # Get language
   detectedLanguage = ai_client.detect_language(documents=[text])[0]
   print('\nLanguage: {}'.format(detectedLanguage.primary_language.name))
    ```

     > **Hinweis**: *In diesem Beispiel wird jede Bewertung einzeln analysiert, was zu einem separaten Aufruf des Diensts für jede Datei führt. Ein alternativer Ansatz besteht in der Erstellung einer Sammlung von Dokumenten und deren Übergeben an den Dienst in einem einzigen Aufruf. Bei beiden Ansätzen besteht die Antwort des Diensts aus einer Sammlung von Dokumenten. Dies ist der Grund, warum im obigen Python-Code der Index des ersten (und einzigen) Dokuments in der Antwort ([0]) angegeben ist.*

1. Speichern Sie die Änderungen. Führen Sie dann das Programm erneut aus.
1. Sehen Sie sich die Ausgabe an, und beachten Sie, dass dieses Mal die Sprache für jede Bewertung identifiziert wird.

## Hinzufügen von Code zum Auswerten des Standpunkts

*Standpunktanalyse* ist eine häufig verwendete Methode zur Klassifizierung von Text als *positiv* oder *negativ* (oder möglicherweise als *neutral* oder *gemischt*). Sie wird häufig verwendet, um Beiträge in sozialen Medien, Produktbewertungen und andere Elemente zu analysieren, bei denen der Standpunkt des Texts nützliche Erkenntnisse liefern kann.

1. Suchen Sie im Code-Editor den Kommentar **Get sentiment**. Fügen Sie dann den Code hinzu, der zum Erkennen der Stimmung im jeweiligen Bewertungsdokument erforderlich ist:

    ```python
   # Get sentiment
   sentimentAnalysis = ai_client.analyze_sentiment(documents=[text])[0]
   print("\nSentiment: {}".format(sentimentAnalysis.sentiment))
    ```

1. Speichern Sie die Änderungen. Schließen Sie dann den Code-Editor und führen Sie das Programm erneut aus.
1. Beobachten Sie die Ausgabe, und beachten Sie, dass der Standpunkt der Bewertungen erkannt wird.

## Hinzufügen von Code zum Identifizieren von Schlüsselausdrücken

Es kann hilfreich sein, Schlüsselbegriffe in einer Textsammlung zu identifizieren, um die wichtigsten Themen zu bestimmen, die behandelt werden.

1. Suchen Sie im Code-Editor den Kommentar **Get key phrases**. Fügen Sie dann den Code hinzu, der zum Erkennen der Schlüsselbegriffe im jeweiligen Bewertungsdokument erforderlich ist:

    ```python
   # Get key phrases
   phrases = ai_client.extract_key_phrases(documents=[text])[0].key_phrases
   if len(phrases) > 0:
        print("\nKey Phrases:")
        for phrase in phrases:
            print('\t{}'.format(phrase))
    ```

1. Speichern Sie die Änderungen und führen Sie das Programm erneut aus.
1. Sehen Sie sich die Ausgabe an, und beachten Sie, dass jedes Dokument Schlüsselbegriffe enthält, die Einblicke in die Thematik der jeweiligen Bewertung geben.

## Hinzufügen von Code zum Extrahieren von Entitäten

Häufig werden in Dokumenten oder anderen Textsammlungen Personen, Orte, Zeiträume oder andere Entitäten erwähnt. Die Textanalyse-API kann mehrere Kategorien (und Unterkategorien) einer Entität in Ihrem Text erkennen.

1. Suchen Sie im Code-Editor den Kommentar **Get entities**. Fügen Sie dann den Code hinzu, der zum Erkennen der Entitäten im jeweiligen Bewertungsdokument erforderlich ist:

    ```python
   # Get entities
   entities = ai_client.recognize_entities(documents=[text])[0].entities
   if len(entities) > 0:
        print("\nEntities")
        for entity in entities:
            print('\t{} ({})'.format(entity.text, entity.category))
    ```

1. Speichern Sie die Änderungen und führen Sie das Programm erneut aus.
1. Beobachten Sie die Ausgabe, und beachten Sie die Entitäten, die im Text erkannt wurden.

## Hinzufügen von Code zum Extrahieren von verknüpften Entitäten

Zusätzlich zu kategorisierten Entitäten kann die Textanalyse-API Entitäten erkennen, für die bekannte Links zu Datenquellen wie Wikipedia verfügbar sind.

1. Suchen Sie im Code-Editor den Kommentar **Get linked entities**. Fügen Sie dann den Code hinzu, der zum Erkennen der verknüpften Entitäten im jeweiligen Bewertungsdokument erforderlich ist:

    ```python
   # Get linked entities
   entities = ai_client.recognize_linked_entities(documents=[text])[0].entities
   if len(entities) > 0:
        print("\nLinks")
        for linked_entity in entities:
            print('\t{} ({})'.format(linked_entity.name, linked_entity.url))
    ```

1. Speichern Sie die Änderungen und führen Sie das Programm erneut aus.
1. Sehen Sie sich die Ausgabe an, und beachten Sie die verknüpften Entitäten, die identifiziert werden.

## Bereinigen von Ressourcen

Wenn Sie die Erkundung des Dienstes Azure KI Language abgeschlossen haben, können Sie die in dieser Übung erstellten Ressourcen löschen. Gehen Sie dazu wie folgt vor:

1. Schließen Sie den Azure Cloud Shell-Bereich
1. Navigieren Sie im Azure-Portal zu der Azure KI Language-Ressource, die Sie in diesem Lab erstellt haben.
1. Wählen Sie auf der Ressourcenseite **Löschen** aus, und folgen Sie den Anweisungen zum Löschen der Ressource.

## Weitere Informationen

Weitere Informationen zur Verwendung von **Azure KI Language** finden Sie in der [Dokumentation](https://learn.microsoft.com/azure/ai-services/language-service/).
