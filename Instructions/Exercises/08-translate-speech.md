---
lab:
  title: Übersetzen von gesprochener Sprache
  module: Module 8 - Translate speech with Azure AI Speech
---

# Übersetzen von gesprochener Sprache

Azure KI Speech enthält eine Sprachübersetzungs-API, mit der Sie gesprochene Sprache übersetzen können. Angenommen, Sie möchten eine Übersetzungsanwendung entwickeln, die Benutzer verwenden können, wenn sie an Orte reisen, wo sie nicht die lokale Sprache sprechen. Sie könnten dann Ausdrücke wie „Where is the station?“ (Wo ist der Bahnhof?) oder „I need to find a pharmacy“ (Ich suche eine Apotheke) in ihrer Muttersprache sagen und diese in die lokale Sprache übersetzen lassen.

> **HINWEIS**: Für diese Übung ist es erforderlich, dass Sie einen Computer mit Lautsprechern/Kopfhörern verwenden. Für eine optimale Benutzererfahrung ist auch ein Mikrofon erforderlich. Einige gehostete virtuelle Umgebungen können möglicherweise Audiodaten von Ihrem lokalen Mikrofon erfassen, aber wenn dies nicht funktioniert (oder Sie überhaupt kein Mikrofon haben), können Sie eine bereitgestellte Audiodatei für die Spracheingabe verwenden. Befolgen Sie die Anweisungen sorgfältig, da Sie verschiedene Optionen auswählen müssen, je nachdem, ob Sie ein Mikrofon oder die Audiodatei verwenden.

## Bereitstellen einer *Azure KI Speech*-Ressource

Wenn Sie noch nicht über eine solche Ressource in Ihrem Abonnement verfügen, müssen Sie eine **Azure KI Speech**-Ressource bereitstellen.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Suchen Sie im Suchfeld oben nach **Azure KI Services**, und drücken Sie die **EINGABETASTE**, wählen Sie dann in den Ergebnissen unter **Sprachdienst** die Option **Erstellen** aus.
1. Erstellen Sie eine Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine*.
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Wählen Sie **F0** (*Free*) oder **S** (*Standard*) aus, falls F nicht verfügbar ist.
    - **Hinweis zu verantwortungsvoller KI**: Zustimmen.
1. Wählen Sie **Überprüfen und erstellen** und dann **Erstellen**, um die Ressource bereitzustellen.
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, und wechseln Sie dann zur bereitgestellten Ressource.
1. Sehen Sie sich die Seite **Schlüssel und Endpunkt** an. Sie benötigen die Informationen auf dieser Seite später in der Übung.

## Vorbereiten der Entwicklung einer App in Visual Studio Code

Sie entwickeln Ihre Sprach-App mit Visual Studio Code. Die Codedateien für Ihre App wurden in einem GitHub-Repository bereitgestellt.

> **Tipp**: Wenn Sie das Repository **mslearn-ai-language** bereits geklont haben, öffnen Sie es in Visual Studio Code. Führen Sie andernfalls die folgenden Schritte aus, um es in Ihre Entwicklungsumgebung zu klonen.

1. Starten Sie Visual Studio Code.
1. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/mslearn-ai-language` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
1. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
1. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus.

## Konfigurieren der Anwendung

Anwendungen für C# und Python wurden bereitgestellt. Beide Apps verfügen über die gleiche Funktionalität. Zuerst schließen Sie einige wichtige Teile der Anwendung ab, um die Verwendung Ihrer Azure KI Speech-Ressource zu aktivieren.

1. Wechseln Sie in Visual Studio Code im Bereich **Explorer** zum Ordner **Labfiles/08-speech-translation**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **CSharp** oder **Python** und den darin enthaltenen Ordner **translator**. Jeder Ordner enthält die sprachspezifischen Codedateien für eine App, in die Sie Azure KI Speech-Funktionen integrieren.
1. Klicken Sie mit der rechten Maustaste auf den Ordner **translator**, der Ihre Codedateien enthält, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Azure KI Speech SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**

    ```
    dotnet add package Microsoft.CognitiveServices.Speech --version 1.30.0
    ```

    **Python**

    ```
    pip install azure-cognitiveservices-speech==1.30.0
    ```

1. Öffnen Sie im Bereich **Explorer** im Ordner **translator** die Konfigurationsdatei für Ihre bevorzugte Sprache.

    - **C#**: appsettings.json
    - **Python**: .env

1. Aktualisieren Sie die Konfigurationswerte so, dass sie die **Region** und einen **Schlüssel** aus der Azure KI Speech-Ressource enthalten, die Sie erstellt haben (verfügbar auf der Seite **Schlüssel und Endpunkt** für Ihre Azure KI Speech-Ressource im Azure-Portal).

    > **HINWEIS**: Achten Sie darauf, die *Region* für Ihre Ressource hinzuzufügen, <u>nicht</u> den Endpunkt!

1. Speichern Sie die Konfigurationsdatei.

## Hinzufügen von Code zur Verwendung des Speech SDK

1. Beachten Sie, dass der Ordner **translator** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: translator.py

    Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie für die Verwendung des Azure KI Speech SDK benötigen:

    **C#** : Program.cs

    ```csharp
    // Import namespaces
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Translation;
    ```

    **Python**: translator.py

    ```python
    # Import namespaces
    import azure.cognitiveservices.speech as speech_sdk
    ```

1. Beachten Sie, dass in der Funktion **Main** der Code zum Laden des Schlüssels und der Region des Azure KI Speech-Diensts aus der Konfigurationsdatei bereits bereitgestellt wurde. Sie müssen diese Variablen verwenden, um eine **SpeechTranslationConfig** für Ihre Azure KI Speech-Ressource zu erstellen, die Sie für die Übersetzung der gesprochenen Eingaben verwenden. Fügen Sie den folgenden Code unter dem Kommentar **Configure translation** (Übersetzung konfigurieren) hinzu:

    **C#** : Program.cs

    ```csharp
    // Configure translation
    translationConfig = SpeechTranslationConfig.FromSubscription(aiSvcKey, aiSvcRegion);
    translationConfig.SpeechRecognitionLanguage = "en-US";
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("es");
    translationConfig.AddTargetLanguage("hi");
    Console.WriteLine("Ready to translate from " + translationConfig.SpeechRecognitionLanguage);
    ```

    **Python**: translator.py

    ```python
    # Configure translation
    translation_config = speech_sdk.translation.SpeechTranslationConfig(ai_key, ai_region)
    translation_config.speech_recognition_language = 'en-US'
    translation_config.add_target_language('fr')
    translation_config.add_target_language('es')
    translation_config.add_target_language('hi')
    print('Ready to translate from',translation_config.speech_recognition_language)
    ```

1. Sie verwenden die **SpeechTranslationConfig**, um Sprache in Text zu übersetzen, aber Sie verwenden auch eine **SpeechConfig**, um Übersetzungen in gesprochener Sprache zu synthetisieren. Fügen Sie den folgenden Code unter dem Kommentar **Configure speech** (Sprache konfigurieren) hinzu:

    **C#** : Program.cs

    ```csharp
    // Configure speech
    speechConfig = SpeechConfig.FromSubscription(aiSvcKey, aiSvcRegion);
    ```

    **Python**: translator.py

    ```python
    # Configure speech
    speech_config = speech_sdk.SpeechConfig(ai_key, ai_region)
    ```

1. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **translator** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python translator.py
    ```

1. Wenn Sie C# verwenden, können Sie alle Warnungen zur Verwendung des **await**-Operators in asynchronen Methoden ignorieren. Dies wird später behoben. Der Code sollte eine Meldung anzeigen, dass er für die Übersetzung aus en-US bereit ist, und Sie zur Eingabe einer Zielsprache auffordern. Drücken Sie die EINGABETASTE, um das Programm zu beenden.

## Implementieren von Sprachübersetzung

Nachdem Sie eine **SpeechTranslationConfig** für den Azure KI Speech-Dienst erstellt haben, können Sie die Azure KI Speech-Übersetzungs-API verwenden, um Sprache zu erkennen und zu übersetzen.

> **WICHTIG**: Dieser Abschnitt enthält Anweisungen für zwei alternative Verfahren. Führen Sie das erste Verfahren aus, wenn Sie über ein funktionierendes Mikrofon verfügen. Führen Sie das zweite Verfahren aus, wenn Sie gesprochene Eingaben mithilfe einer Audiodatei simulieren möchten.

### Wenn Sie über ein funktionierendes Mikrofon verfügen

1. Beachten Sie in der **Main**-Funktion für Ihr Programm, dass der Code die **Translate**-Funktion verwendet, um gesprochene Eingaben zu übersetzen.
1. Fügen Sie in der **Translate**-Funktion unter dem Kommentar **Translate speech** (Sprache übersetzen) den folgenden Code hinzu, um einen **TranslationRecognizer**-Client zu erstellen, der zum Erkennen und Übersetzen von Sprache mithilfe des Standardsystemmikrofons für Eingaben verwendet werden kann.

    **C#** : Program.cs

    ```csharp
    // Translate speech
    using AudioConfig audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using TranslationRecognizer translator = new TranslationRecognizer(translationConfig, audioConfig);
    Console.WriteLine("Speak now...");
    TranslationRecognitionResult result = await translator.RecognizeOnceAsync();
    Console.WriteLine($"Translating '{result.Text}'");
    translation = result.Translations[targetLanguage];
    Console.OutputEncoding = Encoding.UTF8;
    Console.WriteLine(translation);
    ```

    **Python**: translator.py

    ```python
    # Translate speech
    audio_config = speech_sdk.AudioConfig(use_default_microphone=True)
    translator = speech_sdk.translation.TranslationRecognizer(translation_config, audio_config = audio_config)
    print("Speak now...")
    result = translator.recognize_once_async().get()
    print('Translating "{}"'.format(result.text))
    translation = result.translations[targetLanguage]
    print(translation)
    ```

    > **HINWEIS**: Der Code in Ihrer Anwendung übersetzt die Eingabe in einem einzigen Aufruf in alle drei Sprachen. Es wird nur die Übersetzung für die spezifische Sprache angezeigt, aber Sie könnten jede der Übersetzungen abrufen, indem Sie den Zielsprachcode in der **translations**-Sammlung des Ergebnisses angeben.

1. Fahren Sie nun mit dem Abschnitt **Programm ausführen** weiter unten fort.

---

### Alternativ können Sie Audioeingaben aus einer Datei verwenden.

1. Geben Sie im Terminalfenster den folgenden Befehl ein, um eine Bibliothek zu installieren, mit der Sie die Audiodatei wiedergeben können:

    **C#** : Program.cs

    ```csharp
    dotnet add package System.Windows.Extensions --version 4.6.0 
    ```

    **Python**: translator.py

    ```python
    pip install playsound==1.3.0
    ```

1. Fügen Sie in der Codedatei für Ihr Programm unter den vorhandenen Namespaceimporten den folgenden Code hinzu, um die soeben installierte Bibliothek zu importieren:

    **C#** : Program.cs

    ```csharp
    using System.Media;
    ```

    **Python**: translator.py

    ```python
    from playsound import playsound
    ```

1. Beachten Sie in der **Main**-Funktion für Ihr Programm, dass der Code die **Translate**-Funktion verwendet, um gesprochene Eingaben zu übersetzen. Fügen Sie dann in der **Translate**-Funktion unter dem Kommentar **Translate speech** (Sprache übersetzen) den folgenden Code hinzu, um einen **TranslationRecognizer**-Client zu erstellen, der zum Erkennen und Übersetzen von Sprache aus einer Datei verwendet werden kann.

    **C#** : Program.cs

    ```csharp
    // Translate speech
    string audioFile = "station.wav";
    SoundPlayer wavPlayer = new SoundPlayer(audioFile);
    wavPlayer.Play();
    using AudioConfig audioConfig = AudioConfig.FromWavFileInput(audioFile);
    using TranslationRecognizer translator = new TranslationRecognizer(translationConfig, audioConfig);
    Console.WriteLine("Getting speech from file...");
    TranslationRecognitionResult result = await translator.RecognizeOnceAsync();
    Console.WriteLine($"Translating '{result.Text}'");
    translation = result.Translations[targetLanguage];
    Console.OutputEncoding = Encoding.UTF8;
    Console.WriteLine(translation);
    ```

    **Python**: translator.py

    ```python
    # Translate speech
    audioFile = 'station.wav'
    playsound(audioFile)
    audio_config = speech_sdk.AudioConfig(filename=audioFile)
    translator = speech_sdk.translation.TranslationRecognizer(translation_config, audio_config = audio_config)
    print("Getting speech from file...")
    result = translator.recognize_once_async().get()
    print('Translating "{}"'.format(result.text))
    translation = result.translations[targetLanguage]
    print(translation)
    ```

---

### Ausführen des Programms

1. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **translator** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python translator.py
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie einen gültigen Sprachcode ein (*fr*, *es* oder *hi*), und sprechen Sie dann bei Verwendung eines Mikrofons mit klarer Stimme, und sagen Sie „Where is the station?“ (Wo ist der Bahnhof?) oder einen anderen Satz, den Sie bei Reisen im Ausland verwenden könnten. Das Programm sollte Ihre gesprochene Eingabe transkribieren und in die von Ihnen angegebene Sprache übersetzen (Französisch, Spanisch oder Hindi). Wiederholen Sie diesen Vorgang, wobei Sie jede von der Anwendung unterstützte Sprache ausprobieren. Wenn Sie fertig sind, drücken Sie die EINGABETASTE, um das Programm zu beenden.

    Der TranslationRecognizer gibt Ihnen etwa 5 Sekunden Zeit, um zu sprechen. Wenn er keine gesprochene Eingabe erkannt, erzeugt er das Ergebnis „No match“ (Keine Übereinstimmung). Die Übersetzung in Hindi wird aufgrund von Zeichencodierungsproblemen möglicherweise nicht immer ordnungsgemäß im Konsolenfenster angezeigt.

> **HINWEIS**: Der Code in Ihrer Anwendung übersetzt die Eingabe in einem einzigen Aufruf in alle drei Sprachen. Es wird nur die Übersetzung für die spezifische Sprache angezeigt, aber Sie könnten jede der Übersetzungen abrufen, indem Sie den Zielsprachcode in der **translations**-Sammlung des Ergebnisses angeben.

## Synthetisieren der Übersetzung in Sprache

Bisher übersetzt Ihre Anwendung gesprochene Eingaben in Text, was ausreichend sein kann, wenn Sie während einer Reise eine Person um Hilfe bitten müssen. Es wäre jedoch besser, wenn die Übersetzung in einer passenden Stimme laut gesprochen würde.

1. Fügen Sie in der **Translate**-Funktion unter dem Kommentar **Synthesize Translation** (Übersetzung synthetisieren) den folgenden Code hinzu, um einen **SpeechSynthesizer**-Client zu verwenden, um die Übersetzung als Sprache über den Standardlautsprecher zu synthetisieren:

    **C#** : Program.cs

    ```csharp
    // Synthesize translation
    var voices = new Dictionary<string, string>
                    {
                        ["fr"] = "fr-FR-HenriNeural",
                        ["es"] = "es-ES-ElviraNeural",
                        ["hi"] = "hi-IN-MadhurNeural"
                    };
    speechConfig.SpeechSynthesisVoiceName = voices[targetLanguage];
    using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig);
    SpeechSynthesisResult speak = await speechSynthesizer.SpeakTextAsync(translation);
    if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
    {
        Console.WriteLine(speak.Reason);
    }
    ```

    **Python**: translator.py

    ```python
    # Synthesize translation
    voices = {
            "fr": "fr-FR-HenriNeural",
            "es": "es-ES-ElviraNeural",
            "hi": "hi-IN-MadhurNeural"
    }
    speech_config.speech_synthesis_voice_name = voices.get(targetLanguage)
    speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config)
    speak = speech_synthesizer.speak_text_async(translation).get()
    if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

1. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **translator** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python translator.py
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie einen gültigen Sprachcode ein (*fr*, *es* oder *hi*), und sprechen Sie dann mit klarer Stimme in das Mikrofon, und sagen Sie einen Satz, den Sie bei einer Auslandsreise verwenden würden. Das Programm sollte Ihre gesprochene Eingabe transkribieren und mit einer gesprochenen Übersetzung antworten. Wiederholen Sie diesen Vorgang, wobei Sie jede von der Anwendung unterstützte Sprache ausprobieren. Wenn Sie fertig sind, drücken Sie die **EINGABETASTE**, um das Programm zu beenden.

> **HINWEIS**
> *In diesem Beispiel haben Sie eine **SpeechTranslationConfig** verwendet, um Sprache in Text zu übersetzen. Dann haben Sie mit einer **SpeechConfig** die Übersetzung als Sprache synthetisiert. Tatsächlich können Sie die **SpeechTranslationConfig** verwenden, um die Übersetzung direkt zu synthetisieren. Dies funktioniert jedoch nur bei der Übersetzung in eine einzelne Sprache und resultiert in einem Audiostream, der normalerweise als Datei gespeichert wird, anstatt direkt an einen Lautsprecher gesendet zu werden.*

## Weitere Informationen

Weitere Informationen zur Verwendung der Azure KI Speech-Übersetzungs-API finden Sie in der [Dokumentation zur Sprachübersetzung](https://learn.microsoft.com/azure/ai-services/speech-service/speech-translation).
