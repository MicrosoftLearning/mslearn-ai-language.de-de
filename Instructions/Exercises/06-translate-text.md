---
lab:
  title: Übersetzen von Text
  module: Module 3 - Getting Started with Natural Language Processing
---

# Übersetzen von Text

**Azure KI Übersetzer** ist ein Dienst, mit dem Sie Texte zwischen Sprachen übersetzen können. In dieser Übung verwenden Sie ihn, um eine einfache App zu erstellen, die Eingaben in jeder unterstützten Sprache in die Zielsprache Ihrer Wahl übersetzt.

## Bereitstellen einer *Azure KI Übersetzer*-Ressource

Wenn Sie noch nicht über eine solche Ressource in Ihrem Abonnement verfügen, müssen Sie eine **Azure KI Übersetzer**-Ressource bereitstellen.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Suchen Sie im Suchfeld oben nach **Azure KI Services**, und drücken Sie die **Eingabetaste**, wählen Sie dann in den Ergebnissen unter **Übersetzer** **Erstellen** aus.
1. Erstellen Sie eine Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine*.
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Wählen Sie **F0** (*Free*) oder **S** (*Standard*) aus, falls F nicht verfügbar ist.
    - **Hinweis zu verantwortungsvoller KI**: Zustimmen.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, und wechseln Sie dann zur bereitgestellten Ressource.
1. Sehen Sie sich die Seite **Schlüssel und Endpunkt** an. Sie benötigen die Informationen auf dieser Seite später in der Übung.

## Vorbereiten der Entwicklung einer App in Visual Studio Code

Sie entwickeln Ihre Textübersetzungs-App mit Visual Studio Code. Die Codedateien für Ihre App wurden in einem GitHub-Repository bereitgestellt.

> **Tipp**: Wenn Sie das Repository **mslearn-ai-language** bereits geklont haben, öffnen Sie es in Visual Studio Code. Führen Sie andernfalls die folgenden Schritte aus, um es in Ihre Entwicklungsumgebung zu klonen.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/mslearn-ai-language` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus.

## Konfigurieren der Anwendung

Anwendungen für C# und Python wurden bereitgestellt. Beide Apps verfügen über die gleiche Funktionalität. Zunächst vervollständigen Sie einige wichtige Teile der Anwendung, damit diese Ihre Azure KI Übersetzer-Ressource nutzen kann.

1. Wechseln Sie in Visual Studio Code im Bereich **Explorer** zum Ordner **Labfiles/06b-translator-sdk**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **CSharp** oder **Python** und den darin enthaltenen Ordner **translate-text**. Jeder Ordner enthält die sprachspezifischen Codedateien für eine App, in die Sie die Azure KI Übersetzer-Funktionalität integrieren.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **translate-text**, der Ihre Codedateien enthält, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Azure KI Übersetzer-SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#:**

    ```
    dotnet add package Azure.AI.Translation.Text --version 1.0.0-beta.1
    ```

    **Python**:

    ```
    pip install azure-ai-translation-text==1.0.0b1
    ```

3. Öffnen Sie im Bereich **Explorer** im Ordner **translate-text** die Konfigurationsdatei für Ihre bevorzugte Sprache.

    - **C#**: appsettings.json
    - **Python**: .env
    
4. Aktualisieren Sie die Konfigurationswerte so, dass sie die **Region** und einen **Schlüssel** aus der Azure KI Übersetzer-Ressource enthalten, die Sie erstellt haben (verfügbar auf der Seite **Schlüssel und Endpunkt** für Ihre Azure KI Übersetzer-Ressource im Azure-Portal).

    > **HINWEIS**: Achten Sie darauf, die *Region* für Ihre Ressource hinzuzufügen, <u>nicht</u> den Endpunkt!

5. Speichern Sie die Konfigurationsdatei.

## Hinzufügen von Code zum Übersetzen von Text

Jetzt können Sie Azure KI Übersetzer verwenden, um Text zu übersetzen.

1. Beachten Sie, dass der Ordner **translate-text** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: translate.py

    Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das Textanalyse-SDK verwenden zu können:

    **C#**: Programs.cs

    ```csharp
    // import namespaces
    using Azure;
    using Azure.AI.Translation.Text;
    ```

    **Python**: translate.py

    ```python
    # import namespaces
    from azure.ai.translation.text import *
    from azure.ai.translation.text.models import InputTextItem
    ```

1. Beachten Sie in der Funktion **Main**, dass der vorhandene Code die Konfigurationseinstellungen liest.
1. Suchen Sie den Kommentar **Create client using endpoint and key**, und fügen Sie den folgenden Code hinzu:

    **C#**: Programs.cs

    ```csharp
    // Create client using endpoint and key
    AzureKeyCredential credential = new(translatorKey);
    TextTranslationClient client = new(credential, translatorRegion);
    ```

    **Python**: translate.py

    ```python
    # Create client using endpoint and key
    credential = TranslatorCredential(translatorKey, translatorRegion)
    client = TextTranslationClient(credential)
    ```

1. Suchen Sie den Kommentar **Choose target language**, und fügen Sie den folgenden Code hinzu, der den Textübersetzungsdienst verwendet, um eine Liste der unterstützten Sprachen für die Übersetzung zurückzugeben, und Benutzer*innen auffordert, einen Sprachcode für die Zielsprache auszuwählen.

    **C#**: Programs.cs

    ```csharp
    // Choose target language
    Response<GetLanguagesResult> languagesResponse = await client.GetLanguagesAsync(scope:"translation").ConfigureAwait(false);
    GetLanguagesResult languages = languagesResponse.Value;
    Console.WriteLine($"{languages.Translation.Count} languages available.\n(See https://learn.microsoft.com/azure/ai-services/translator/language-support#translation)");
    Console.WriteLine("Enter a target language code for translation (for example, 'en'):");
    string targetLanguage = "xx";
    bool languageSupported = false;
    while (!languageSupported)
    {
        targetLanguage = Console.ReadLine();
        if (languages.Translation.ContainsKey(targetLanguage))
        {
            languageSupported = true;
        }
        else
        {
            Console.WriteLine($"{targetLanguage} is not a supported language.");
        }

    }
    ```

    **Python**: translate.py

    ```python
    # Choose target language
    languagesResponse = client.get_languages(scope="translation")
    print("{} languages supported.".format(len(languagesResponse.translation)))
    print("(See https://learn.microsoft.com/azure/ai-services/translator/language-support#translation)")
    print("Enter a target language code for translation (for example, 'en'):")
    targetLanguage = "xx"
    supportedLanguage = False
    while supportedLanguage == False:
        targetLanguage = input()
        if  targetLanguage in languagesResponse.translation.keys():
            supportedLanguage = True
        else:
            print("{} is not a supported language.".format(targetLanguage))
    ```

1. Suchen Sie den Kommentar **Translate text**, und fügen Sie den folgenden Code hinzu, der Benutzer*innen wiederholt dazu auffordert, zu übersetzenden Text anzugeben, den Azure KI Übersetzer-Dienst verwendet, um den Text in die Zielsprache zu übersetzen (nachdem die Ausgangssprache automatisch erkannt wurde), und die Ergebnisse anzeigt, bis Benutzer*innen *quit* eingeben.

    **C#**: Programs.cs

    ```csharp
    // Translate text
    string inputText = "";
    while (inputText.ToLower() != "quit")
    {
        Console.WriteLine("Enter text to translate ('quit' to exit)");
        inputText = Console.ReadLine();
        if (inputText.ToLower() != "quit")
        {
            Response<IReadOnlyList<TranslatedTextItem>> translationResponse = await client.TranslateAsync(targetLanguage, inputText).ConfigureAwait(false);
            IReadOnlyList<TranslatedTextItem> translations = translationResponse.Value;
            TranslatedTextItem translation = translations[0];
            string sourceLanguage = translation?.DetectedLanguage?.Language;
            Console.WriteLine($"'{inputText}' translated from {sourceLanguage} to {translation?.Translations[0].To} as '{translation?.Translations?[0]?.Text}'.");
        }
    } 
    ```

    **Python**: translate.py

    ```python
    # Translate text
    inputText = ""
    while inputText.lower() != "quit":
        inputText = input("Enter text to translate ('quit' to exit):")
        if inputText != "quit":
            input_text_elements = [InputTextItem(text=inputText)]
            translationResponse = client.translate(content=input_text_elements, to=[targetLanguage])
            translation = translationResponse[0] if translationResponse else None
            if translation:
                sourceLanguage = translation.detected_language
                for translated_text in translation.translations:
                    print(f"'{inputText}' was translated from {sourceLanguage.language} to {translated_text.to} as '{translated_text.text}'.")
    ```

1. Speichern Sie die Änderungen an der Codedatei.

## Testen Ihrer Anwendung

Ihre Anwendung kann jetzt getestet werden.

1. Geben Sie im integrierten Terminal für den Ordner **Translate text** den folgenden Befehl zur Ausführung des Programms ein:

    - **C#**: `dotnet run`
    - **Python**: `python translate.py`

    > **Tipp**: Sie können das Symbol **Fenstergröße maximieren** (**^**) in der Terminalsymbolleiste verwenden, um mehr vom Konsolentext anzuzeigen.

1. Wenn Sie dazu aufgefordert werden, geben Sie eine gültige Zielsprache aus der angezeigten Liste ein.
1. Geben Sie einen Ausdruck ein, der übersetzt werden soll (z. B. `This is a test` oder `C'est un test`), und zeigen Sie die Ergebnisse an, bei denen die Ausgangssprache erkannt und der Text in die Zielsprache übersetzt wurde.
1. Wenn Sie fertig sind, geben Sie die Zeichenfolge `quit` ein. Sie können die Anwendung erneut ausführen und eine andere Zielsprache auswählen.

## Bereinigung

Wenn Sie Ihr Projekt nicht mehr benötigen, können Sie die Azure KI Übersetzer-Ressource im [Azure-Portal](https://portal.azure.com) löschen.
