---
lab:
  title: Analysieren von Text
  module: Module 3 - Develop natural language processing solutions
---

# Analysieren von Text

**Azure Language** unterstützt die Analyse von Text, einschließlich Spracherkennung, Stimmungsanalyse, Schlüsselbegriffserkennung und Entitätserkennung.

Angenommen, ein Reiseunternehmen möchte Hotelbewertungen verarbeiten, die auf der Website des Unternehmens abgegeben wurden. Mit Azure KI Language können sie die Sprache, in der jede Bewertung verfasst ist, die Stimmung (positiv, neutral oder negativ) der Bewertungen, Schlüsselbegriffe, die auf die Hauptthemen der Bewertung hinweisen, und benannte Entitäten wie Orte, Sehenswürdigkeiten oder Personen, die in den Bewertungen erwähnt werden, bestimmen.

## Bereitstellen einer *Azure KI Language*-Ressource

Wenn Sie noch nicht über eine solche Ressource in Ihrem Abonnement verfügen, müssen Sie eine **Azure KI Language Services**-Ressource Ihrem Azure-Abonnement bereitstellen.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Wählen Sie **Ressource erstellen**.
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

## Vorbereitung auf das Entwickeln einer App in Visual Studio Code

Sie entwickeln Ihre Textanalyse-App mit Visual Studio Code. Die Codedateien für Ihre App wurden in einem GitHub-Repository bereitgestellt.

> **Tipp**: Wenn Sie das Repository **mslearn-ai-language** bereits geklont haben, öffnen Sie es in Visual Studio Code. Führen Sie andernfalls die folgenden Schritte aus, um es in Ihre Entwicklungsumgebung zu klonen.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/mslearn-ai-language` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.

    > **Hinweis:** Wenn Visual Studio Code eine Popupnachricht anzeigt, in der Sie aufgefordert werden, dem geöffneten Code zu vertrauen, klicken Sie auf die Option **Ja, ich vertraue den Autoren** im Popupfenster.

4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus.

## Konfigurieren der Anwendung

Es werden Anwendungen für C# und Python bereitgestellt sowie eine Beispieltextdatei, mit der Sie die Zusammenfassung testen können. Beide Apps verfügen über die gleiche Funktionalität. In dieser Übung stellen Sie zunächst einige wichtige Teile der Anwendung fertig, um die Verwendung Ihrer Azure KI Language-Ressource zu aktivieren.

1. Navigieren Sie in Visual Studio Code im Bereich **Explorer** zum Ordner **Labfiles/01-analyze-text**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **CSharp** oder **Python** und den darin enthaltenen **text-analysis**- Ordner. Jeder Ordner enthält die sprachspezifischen Dateien für eine App, in die Sie die Textanalysefunktion Azure KI Language integrieren möchten.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **text-analysis**, der Ihre Codedateien enthält, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Textanalyse-SDK-Paket Azure KI Language, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen: Installieren Sie für die Python-Übung auch das `dotenv`-Paket:

    **C#:**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.3.0
    ```

    **Python**:

    ```
    pip install azure-ai-textanalytics==5.3.0
    pip install python-dotenv
    ```

3. Öffnen Sie im Bereich **Explorer** im Ordner **text-analysis** die Konfigurationsdatei für Ihre bevorzugte Sprache.

    - **C#**: appsettings.json
    - **Python**: .env
    
4. Aktualisieren Sie die Konfigurationswerte, sodass sie den **Endpunkt** und einen **Schlüssel** aus der von Ihnen erstellten Azure Language-Ressource (verfügbar auf der Seite **Schlüssel und Endpunkt** für Ihre Azure KI Language-Ressource im Azure-Portal) enthalten.
5. Speichern Sie die Konfigurationsdatei.

6. Beachten Sie, dass der Ordner **text-analysis** eine Codedatei für die Clientanwendung enthält:

    - **C#**: Program.cs
    - **Python**: text-analysis.py

    Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das Textanalyse-SDK verwenden zu können:

    **C#**: Programs.cs

    ```csharp
    // import namespaces
    using Azure;
    using Azure.AI.TextAnalytics;
    ```

    **Python**: text-analysis.py

    ```python
    # import namespaces
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.textanalytics import TextAnalyticsClient
    ```

7. Beachten Sie, dass in der Funktion **Main** der Code zum Laden des Schlüssels und des Endpunkts des Azure KI Language-Diensts aus der Konfigurationsdatei bereits bereitgestellt wurde. Suchen Sie dann den Kommentar **Create client using endpoint and key** (Client mit Endpunkt und Schlüssel erstellen), und fügen Sie den folgenden Code hinzu, um einen Client für die Textanalyse-API zu erstellen:

    **C#**: Programs.cs

    ```C#
    // Create client using endpoint and key
    AzureKeyCredential credentials = new AzureKeyCredential(aiSvcKey);
    Uri endpoint = new Uri(aiSvcEndpoint);
    TextAnalyticsClient aiClient = new TextAnalyticsClient(endpoint, credentials);
    ```

    **Python**: text-analysis.py

    ```Python
    # Create client using endpoint and key
    credential = AzureKeyCredential(ai_key)
    ai_client = TextAnalyticsClient(endpoint=ai_endpoint, credential=credential)
    ```

8. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **text-analysis** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    - **C#**: `dotnet run`
    - **Python**: `python text-analysis.py`

    > **Tipp**: Sie können das Symbol **Maximize panel size** (Panelgröße maximieren) (**^**) in der Terminalsymbolleiste verwenden, um mehr des Konsolentexts anzuzeigen.

9. Beobachten Sie die Ausgabe, da der Code ohne Fehler ausgeführt werden sollte, wobei der Inhalt jeder Bewertungstextdatei im Ordner **reviews** angezeigt wird. Die Anwendung erstellt erfolgreich einen Client für die Textanalyse-API, nutzt ihn aber nicht. Dies wird im nächsten Verfahren behoben.

## Hinzufügen von Code zum Erkennen der Sprache

Nachdem Sie nun einen Client für die API erstellt haben, können wir diesen verwenden, um die Sprache zu erkennen, in der die einzelnen Bewertungen verfasst sind.

1. Suchen Sie in der **Main**-Funktion für Ihr Programm den Kommentar **Get language** (Sprache erhalten). Fügen Sie dann unter diesem Kommentar den Code hinzu, der zum Erkennen der Sprache im jeweiligen Bewertungsdokument erforderlich ist:

    **C#**: Programs.cs

    ```csharp
    // Get language
    DetectedLanguage detectedLanguage = aiClient.DetectLanguage(text);
    Console.WriteLine($"\nLanguage: {detectedLanguage.Name}");
    ```

    **Python**: text-analysis.py

    ```python
    # Get language
    detectedLanguage = ai_client.detect_language(documents=[text])[0]
    print('\nLanguage: {}'.format(detectedLanguage.primary_language.name))
    ```

     > **Hinweis**: *In diesem Beispiel wird jede Bewertung einzeln analysiert, was zu einem separaten Aufruf des Diensts für jede Datei führt. Ein alternativer Ansatz besteht in der Erstellung einer Sammlung von Dokumenten und deren Übergeben an den Dienst in einem einzigen Aufruf. Bei beiden Ansätzen besteht die Antwort des Diensts aus einer Sammlung von Dokumenten. Dies ist der Grund, warum im obigen Python-Code der Index des ersten (und einzigen) Dokuments in der Antwort ([0]) angegeben ist.*

1. Speichern Sie die Änderungen. Kehren Sie dann zum integrierten Terminal in den Ordner **text-analysis** zurück, und führen sie das Programm erneut aus.
1. Sehen Sie sich die Ausgabe an, und beachten Sie, dass dieses Mal die Sprache für jede Bewertung identifiziert wird.

## Hinzufügen von Code zum Auswerten des Standpunkts

*Standpunktanalyse* ist eine häufig verwendete Methode zur Klassifizierung von Text als *positiv* oder *negativ* (oder möglicherweise als *neutral* oder *gemischt*). Sie wird häufig verwendet, um Beiträge in sozialen Medien, Produktbewertungen und andere Elemente zu analysieren, bei denen der Standpunkt des Texts nützliche Erkenntnisse liefern kann.

1. Suchen Sie in der **Main**-Funktion für Ihr Programm den Kommentar **Get sentiment** (Standpunkt erhalten). Fügen Sie dann unter diesem Kommentar den Code hinzu, der zum Erkennen des Standpunkts im jeweiligen Bewertungsdokument erforderlich ist:

    **C#**: Program.cs

    ```csharp
    // Get sentiment
    DocumentSentiment sentimentAnalysis = aiClient.AnalyzeSentiment(text);
    Console.WriteLine($"\nSentiment: {sentimentAnalysis.Sentiment}");
    ```

    **Python**: text-analysis.py

    ```python
    # Get sentiment
    sentimentAnalysis = ai_client.analyze_sentiment(documents=[text])[0]
    print("\nSentiment: {}".format(sentimentAnalysis.sentiment))
    ```

1. Speichern Sie die Änderungen. Kehren Sie dann zum integrierten Terminal in den Ordner **text-analysis** zurück, und führen sie das Programm erneut aus.
1. Beobachten Sie die Ausgabe, und beachten Sie, dass der Standpunkt der Bewertungen erkannt wird.

## Hinzufügen von Code zum Identifizieren von Schlüsselausdrücken

Es kann hilfreich sein, Schlüsselbegriffe in einer Textsammlung zu identifizieren, um die wichtigsten Themen zu bestimmen, die behandelt werden.

1. Suchen Sie in der **Main**-Funktion für Ihr Programm den Kommentar **Get key phrases** (Schlüsselbegriffe erhalten). Fügen Sie dann unter diesem Kommentar den Code hinzu, der zum Erkennen der Schlüsselbegriffe im jeweiligen Bewertungsdokument erforderlich ist:

    **C#**: Program.cs

    ```csharp
    // Get key phrases
    KeyPhraseCollection phrases = aiClient.ExtractKeyPhrases(text);
    if (phrases.Count > 0)
    {
        Console.WriteLine("\nKey Phrases:");
        foreach(string phrase in phrases)
        {
            Console.WriteLine($"\t{phrase}");
        }
    }
    ```

    **Python**: text-analysis.py

    ```python
    # Get key phrases
    phrases = ai_client.extract_key_phrases(documents=[text])[0].key_phrases
    if len(phrases) > 0:
        print("\nKey Phrases:")
        for phrase in phrases:
            print('\t{}'.format(phrase))
    ```

1. Speichern Sie die Änderungen. Kehren Sie dann zum integrierten Terminal in den Ordner **text-analysis** zurück, und führen sie das Programm erneut aus.
1. Sehen Sie sich die Ausgabe an, und beachten Sie, dass jedes Dokument Schlüsselbegriffe enthält, die Einblicke in die Thematik der jeweiligen Bewertung geben.

## Hinzufügen von Code zum Extrahieren von Entitäten

Häufig werden in Dokumenten oder anderen Textsammlungen Personen, Orte, Zeiträume oder andere Entitäten erwähnt. Die Textanalyse-API kann mehrere Kategorien (und Unterkategorien) einer Entität in Ihrem Text erkennen.

1. Suchen Sie in der **Main**-Funktion für Ihr Programm den Kommentar **Get entities** (Entitäten erhalten). Fügen Sie dann unter diesem Kommentar den Code hinzu, der zum Identifizieren von Entitäten erforderlich ist, die in der jeweiligen Bewertung erwähnt werden:

    **C#**: Program.cs

    ```csharp
    // Get entities
    CategorizedEntityCollection entities = aiClient.RecognizeEntities(text);
    if (entities.Count > 0)
    {
        Console.WriteLine("\nEntities:");
        foreach(CategorizedEntity entity in entities)
        {
            Console.WriteLine($"\t{entity.Text} ({entity.Category})");
        }
    }
    ```

    **Python**: text-analysis.py

    ```python
    # Get entities
    entities = ai_client.recognize_entities(documents=[text])[0].entities
    if len(entities) > 0:
        print("\nEntities")
        for entity in entities:
            print('\t{} ({})'.format(entity.text, entity.category))
    ```

1. Speichern Sie die Änderungen. Kehren Sie dann zum integrierten Terminal in den Ordner **text-analysis** zurück, und führen sie das Programm erneut aus.
1. Beobachten Sie die Ausgabe, und beachten Sie die Entitäten, die im Text erkannt wurden.

## Hinzufügen von Code zum Extrahieren von verknüpften Entitäten

Zusätzlich zu kategorisierten Entitäten kann die Textanalyse-API Entitäten erkennen, für die bekannte Links zu Datenquellen wie Wikipedia verfügbar sind.

1. Suchen Sie in der **Main**-Funktion für Ihr Programm den Kommentar **Get linked entities** (Verknüpfte Entitäten erhalten). Fügen Sie dann unter diesem Kommentar den Code hinzu, der zum Identifizieren von verknüpften Entitäten erforderlich ist, die in der jeweiligen Bewertung erwähnt werden:

    **C#**: Program.cs

    ```csharp
    // Get linked entities
    LinkedEntityCollection linkedEntities = aiClient.RecognizeLinkedEntities(text);
    if (linkedEntities.Count > 0)
    {
        Console.WriteLine("\nLinks:");
        foreach(LinkedEntity linkedEntity in linkedEntities)
        {
            Console.WriteLine($"\t{linkedEntity.Name} ({linkedEntity.Url})");
        }
    }
    ```

    **Python**: text-analysis.py

    ```python
    # Get linked entities
    entities = ai_client.recognize_linked_entities(documents=[text])[0].entities
    if len(entities) > 0:
        print("\nLinks")
        for linked_entity in entities:
            print('\t{} ({})'.format(linked_entity.name, linked_entity.url))
    ```

1. Speichern Sie die Änderungen. Kehren Sie dann zum integrierten Terminal in den Ordner **text-analysis** zurück, und führen sie das Programm erneut aus.
1. Sehen Sie sich die Ausgabe an, und beachten Sie die verknüpften Entitäten, die identifiziert werden.

## Bereinigen von Ressourcen

Wenn Sie die Erkundung des Dienstes Azure KI Language abgeschlossen haben, können Sie die in dieser Übung erstellten Ressourcen löschen. Gehen Sie dazu wie folgt vor:

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.

2. Navigieren Sie zur Azure KI Language-Ressource, die Sie in dieser Übung erstellt haben.

3. Wählen Sie auf der Seite „Ressource“ die Option **Delete** (Löschen) aus, und folgen Sie den Anweisungen zum Löschen der Ressource.

## Weitere Informationen

Weitere Informationen zur Verwendung von **Azure KI Language** finden Sie in der [Dokumentation](https://learn.microsoft.com/azure/ai-services/language-service/).
