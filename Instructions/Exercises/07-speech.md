---
lab:
  title: Erkennen und Synthetisieren von Sprache
  module: Module 4 - Create speech-enabled apps with Azure AI services
---

# Erkennen und Synthetisieren von Sprache

**Azure KI Speech** ist ein Dienst, der sprachbezogene Funktionen bietet, darunter:

- Eine *Spracherkennungs*-API, mit der Sie die Spracherkennung implementieren können (konvertieren hörbarer gesprochener Wörter in Text).
- Eine *Sprachsynthese*-API mit der Sie eine Sprachsynthese implementieren können (konvertieren von Text in hörbare Sprache).

In dieser Übung werden Sie diese beiden APIs verwenden, um eine Anwendung für eine sprechende Uhr zu implementieren.

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
1. Wählen Sie **Überprüfen und erstellen** aus und dann **Erstellen**, um die Ressource bereitzustellen.
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

1. Wechseln Sie in Visual Studio Code im Bereich **Explorer** zum Ordner **Labfiles/07-speech**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **CSharp** oder **Python** und den darin enthaltenen Ordner **speaking-clock**. Jeder Ordner enthält die sprachspezifischen Codedateien für eine App, in die Sie Azure KI Speech-Funktionen integrieren.
1. Klicken Sie mit der rechten Maustaste auf den Ordner **speaking-clock**, der Ihre Codedateien enthält, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Azure KI Speech SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**

    ```
    dotnet add package Microsoft.CognitiveServices.Speech --version 1.30.0
    ```

    **Python**

    ```
    pip install azure-cognitiveservices-speech==1.30.0
    ```

1. Öffnen Sie im Bereich **Explorer** im Ordner **speaking-clock** die Konfigurationsdatei für Ihre bevorzugte Sprache.

    - **C#**: appsettings.json
    - **Python**: .env

1. Aktualisieren Sie die Konfigurationswerte so, dass sie die **Region** und einen **Schlüssel** aus der Azure KI Speech-Ressource enthalten, die Sie erstellt haben (verfügbar auf der Seite **Schlüssel und Endpunkt** für Ihre Azure KI Speech-Ressource im Azure-Portal).

    > **HINWEIS**: Achten Sie darauf, die *Region* für Ihre Ressource hinzuzufügen, <u>nicht</u> den Endpunkt!

1. Speichern Sie die Konfigurationsdatei.

## Hinzufügen von Code zur Verwendung des Azure KI Speech SDK

1. Beachten Sie, dass der Ordner **speaking-clock** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: speaking-clock.py

    Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie für die Verwendung des Azure KI Speech SDK benötigen:

    **C#** : Program.cs

    ```csharp
    // Import namespaces
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    ```

    **Python**: speaking-clock.py

    ```python
    # Import namespaces
    import azure.cognitiveservices.speech as speech_sdk
    ```

1. Beachten Sie, dass in der Funktion **Main** der Code zum Laden des Schlüssels und der Region des Diensts aus der Konfigurationsdatei bereits bereitgestellt wurde. Sie müssen diese Variablen verwenden, um eine **SpeechConfig** für Ihre Azure KI Speech-Ressource zu erstellen. Fügen Sie den folgenden Code unter dem Kommentar **Configure speech service** (Speech-Dienst konfigurieren) hinzu:

    **C#** : Program.cs

    ```csharp
    // Configure speech service
    speechConfig = SpeechConfig.FromSubscription(aiSvcKey, aiSvcRegion);
    Console.WriteLine("Ready to use speech service in " + speechConfig.Region);
    
    // Configure voice
    speechConfig.SpeechSynthesisVoiceName = "en-US-AriaNeural";
    ```

    **Python**: speaking-clock.py

    ```python
    # Configure speech service
    speech_config = speech_sdk.SpeechConfig(ai_key, ai_region)
    print('Ready to use speech service in:', speech_config.region)
    ```

1. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **speaking-clock** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python speaking-clock.py
    ```

1. Wenn Sie C# verwenden, können Sie alle Warnungen zur Verwendung des **await**-Operators in asynchronen Methoden ignorieren. Dies wird später behoben. Der Code sollte die Region der Speech-Dienstressource anzeigen, die die Anwendung verwenden wird.

## Hinzufügen von Code zur Spracherkennung

Nachdem Sie jetzt über eine **SpeechConfig** für den Sprachdienst in Ihrer Azure KI Speech-Ressource verfügen, können Sie die **Sprache-in-Text**-API verwenden, um Sprache zu erkennen und in Text zu transkribieren.

> **WICHTIG**: Dieser Abschnitt enthält Anweisungen für zwei alternative Verfahren. Führen Sie das erste Verfahren aus, wenn Sie über ein funktionierendes Mikrofon verfügen. Führen Sie das zweite Verfahren aus, wenn Sie gesprochene Eingaben mithilfe einer Audiodatei simulieren möchten.

### Wenn Sie über ein funktionierendes Mikrofon verfügen

1. Beachten Sie in der **Main**-Funktion für Ihr Programm, dass der Code die **TranscribeCommand**-Funktion verwendet, um gesprochene Eingaben zu akzeptieren.
1. Fügen Sie in der Funktion **TranscribeCommand** unter dem Kommentar **Configure speech recognition** (Spracherkennung konfigurieren) den entsprechenden Code unten ein, um einen **SpeechRecognizer**-Client zu erstellen, der zur Erkennung und Transkription von Sprache unter Verwendung des Standardmikrofons des Systems verwendet werden kann:

    **C#**

    ```csharp
    // Configure speech recognition
    using AudioConfig audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using SpeechRecognizer speechRecognizer = new SpeechRecognizer(speechConfig, audioConfig);
    Console.WriteLine("Speak now...");
    ```

    **Python**

    ```python
    # Configure speech recognition
    audio_config = speech_sdk.AudioConfig(use_default_microphone=True)
    speech_recognizer = speech_sdk.SpeechRecognizer(speech_config, audio_config)
    print('Speak now...')
    ```

1. Wechseln Sie nun zum Abschnitt **Hinzufügen von Code zum Verarbeiten des transkribierten Befehls** weiter unten.

---

### Alternativ können Sie Audioeingaben aus einer Datei verwenden.

1. Geben Sie im Terminalfenster den folgenden Befehl ein, um eine Bibliothek zu installieren, mit der Sie die Audiodatei wiedergeben können:

    **C#**

    ```
    dotnet add package System.Windows.Extensions --version 4.6.0 
    ```

    **Python**

    ```
    pip install playsound==1.2.2
    ```

1. Fügen Sie in der Codedatei für Ihr Programm unter den vorhandenen Namespaceimporten den folgenden Code hinzu, um die soeben installierte Bibliothek zu importieren:

    **C#** : Program.cs

    ```csharp
    using System.Media;
    ```

    **Python**: speaking-clock.py

    ```python
    from playsound import playsound
    ```

1. Beachten Sie in der **Main**-Funktion, dass der Code die **TranscribeCommand**-Funktion verwendet, um gesprochene Eingaben zu akzeptieren. Fügen Sie dann in der Funktion **TranscribeCommand** unter dem Kommentar **Configure speech recognition** (Spracherkennung konfigurieren) den entsprechenden Code unten ein, um einen **SpeechRecognizer**-Client zu erstellen, der zur Erkennung und Transkription von Sprache aus einer Audiodatei verwendet werden kann:

    **C#** : Program.cs

    ```csharp
    // Configure speech recognition
    string audioFile = "time.wav";
    SoundPlayer wavPlayer = new SoundPlayer(audioFile);
    wavPlayer.Play();
    using AudioConfig audioConfig = AudioConfig.FromWavFileInput(audioFile);
    using SpeechRecognizer speechRecognizer = new SpeechRecognizer(speechConfig, audioConfig);
    ```

    **Python**: speaking-clock.py

    ```python
    # Configure speech recognition
    current_dir = os.getcwd()
    audioFile = current_dir + '\\time.wav'
    playsound(audioFile)
    audio_config = speech_sdk.AudioConfig(filename=audioFile)
    speech_recognizer = speech_sdk.SpeechRecognizer(speech_config, audio_config)
    ```

---

### Hinzufügen von Code zum Verarbeiten des transkribierten Befehls

1. Fügen Sie in der Funktion **TranscribeCommand** unter dem Kommentar **Process speech input** (Spracheingabe verarbeiten) den folgenden Code ein, um auf gesprochene Eingaben zu lauschen. Achten Sie darauf, dass Sie den Code am Ende der Funktion, die den Befehl zurückgibt, nicht ersetzen:

    **C#** : Program.cs

    ```csharp
    // Process speech input
    SpeechRecognitionResult speech = await speechRecognizer.RecognizeOnceAsync();
    if (speech.Reason == ResultReason.RecognizedSpeech)
    {
        command = speech.Text;
        Console.WriteLine(command);
    }
    else
    {
        Console.WriteLine(speech.Reason);
        if (speech.Reason == ResultReason.Canceled)
        {
            var cancellation = CancellationDetails.FromResult(speech);
            Console.WriteLine(cancellation.Reason);
            Console.WriteLine(cancellation.ErrorDetails);
        }
    }
    ```

    **Python**: speaking-clock.py

    ```python
    # Process speech input
    speech = speech_recognizer.recognize_once_async().get()
    if speech.reason == speech_sdk.ResultReason.RecognizedSpeech:
        command = speech.text
        print(command)
    else:
        print(speech.reason)
        if speech.reason == speech_sdk.ResultReason.Canceled:
            cancellation = speech.cancellation_details
            print(cancellation.reason)
            print(cancellation.error_details)
    ```

1. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **speaking-clock** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python speaking-clock.py
    ```

1. Wenn Sie ein Mikrofon verwenden, sprechen Sie deutlich und sagen Sie „What time is it?“ (Wie spät ist es?). Das Programm sollte Ihre gesprochenen Eingaben transkribieren und die Zeit anzeigen (basierend auf der lokalen Zeit des Computers, auf dem der Code ausgeführt wird, was möglicherweise nicht die richtige Zeit an Ihrem Standort ist).

    Der SpeechRecognizer gibt Ihnen etwa fünf Sekunden Zeit zum Sprechen. Wenn er keine gesprochene Eingabe erkannt, erzeugt er das Ergebnis „No match“ (Keine Übereinstimmung).

    Wenn der SpeechRecognizer auf einen Fehler stößt, gibt er das Ergebnis „Cancelled“ (Abgebrochen) aus. Der Code in der Anwendung zeigt dann die Fehlermeldung an. Die wahrscheinlichste Ursache ist ein falscher Schlüssel oder eine falsche Region in der Konfigurationsdatei.

## Synthetisieren von Sprache

Ihre Anwendung für die sprechende Uhr akzeptiert gesprochene Eingaben, aber sie spricht nicht wirklich! Lassen Sie uns das beheben, indem wir Code zur Sprachsynthese hinzufügen.

1. Beachten Sie in der Funktion **Main** für Ihr Programm, dass der Code die Funktion **TellTime** verwendet, um dem Benutzer die aktuelle Zeit mitzuteilen.
1. Fügen Sie in der Funktion **TellTime** unter dem Kommentar **Configure speech synthesis** (Sprachsynthese konfigurieren) den folgenden Code ein, um einen **SpeechSynthesizer**-Client zu erstellen, der zum Generieren von Sprachausgaben verwendet werden kann:

    **C#** : Program.cs

    ```csharp
    // Configure speech synthesis
    speechConfig.SpeechSynthesisVoiceName = "en-GB-RyanNeural";
    using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig);
    ```

    **Python**: speaking-clock.py

    ```python
    # Configure speech synthesis
    speech_config.speech_synthesis_voice_name = "en-GB-RyanNeural"
    speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config)
    ```

    > **HINWEIS** Die Standardaudiokonfiguration verwendet das standardmäßige Audiogerät des Systems für die Ausgabe, sodass Sie keine **AudioConfig** explizit bereitstellen müssen. Wenn Sie die Audioausgabe an eine Datei umleiten müssen, können Sie dazu eine **AudioConfig** mit einem Dateipfad verwenden.

1. Fügen Sie in der Funktion **TellTime** unter dem Kommentar **Synthesize spoken output** (Gesprochene Ausgabe synthetisieren) den folgenden Code ein, um die gesprochene Ausgabe zu generieren. Achten Sie darauf, dass Sie den Code am Ende der Funktion, die die Antwort ausgibt, nicht ersetzen:

    **C#** : Program.cs

    ```csharp
    // Synthesize spoken output
    SpeechSynthesisResult speak = await speechSynthesizer.SpeakTextAsync(responseText);
    if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
    {
        Console.WriteLine(speak.Reason);
    }
    ```

    **Python**: speaking-clock.py

    ```python
    # Synthesize spoken output
    speak = speech_synthesizer.speak_text_async(response_text).get()
    if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

1. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **speaking-clock** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python speaking-clock.py
    ```

1. Wenn Sie dazu aufgefordert werden, sprechen Sie deutlich in das Mikrofon und sagen Sie „What time is it?“ (Wie spät ist es?). Das Programm sollte eine Sprachausgabe erzeugen und Ihnen die Uhrzeit mitteilen.

## Verwenden einer anderen Stimme

Ihre Anwendung für die sprechende Uhr verwendet eine Standardstimme, die Sie ändern können. Der Speech-Dienst unterstützt eine Reihe von *Standardstimmen* sowie eher menschenähnliche *neuronale* Stimmen. Sie können auch *benutzerdefinierte* Stimmen erstellen.

> **Hinweis**: Eine Liste der neuralen und Standardstimmen finden Sie im [Sprachkatalog](https://speech.microsoft.com/portal/voicegallery) im Speech Studio.

1. Ändern Sie in der Funktion **TellTime** unter dem Kommentar **Configure speech synthesis** (Sprachsynthese konfigurieren) den Code wie folgt, um eine alternative Stimme anzugeben, bevor Sie den **SpeechSynthesizer**-Client erstellen:

   **C#** : Program.cs

    ```csharp
    // Configure speech synthesis
    speechConfig.SpeechSynthesisVoiceName = "en-GB-LibbyNeural"; // change this
    using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig);
    ```

    **Python**: speaking-clock.py

    ```python
    # Configure speech synthesis
    speech_config.speech_synthesis_voice_name = 'en-GB-LibbyNeural' # change this
    speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config)
    ```

1. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **speaking-clock** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python speaking-clock.py
    ```

1. Wenn Sie dazu aufgefordert werden, sprechen Sie deutlich in das Mikrofon und sagen Sie „What time is it?“ (Wie spät ist es?). Das Programm sollte mit der angegebenen Stimme sprechen und Ihnen die Uhrzeit mitteilen.

## Verwenden von Sprachsynthese-Markupsprache

Mit der Speech Synthesis Markup Language (SSML) können Sie die Art und Weise, wie Ihre Sprache synthetisiert wird, mithilfe eines XML-basierten Formats anpassen.

1. Ersetzen Sie in der Funktion **TellTime** den gesamten aktuellen Code unter dem Kommentar **Synthetize spoken output** (Gesprochene Ausgabe synthetisieren) durch den folgenden Code (belassen Sie den Code unter dem Kommentar **Print the response** (Antwort ausgeben)):

   **C#** : Program.cs

    ```csharp
    // Synthesize spoken output
    string responseSsml = $@"
        <speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
            <voice name='en-GB-LibbyNeural'>
                {responseText}
                <break strength='weak'/>
                Time to end this lab!
            </voice>
        </speak>";
    SpeechSynthesisResult speak = await speechSynthesizer.SpeakSsmlAsync(responseSsml);
    if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
    {
        Console.WriteLine(speak.Reason);
    }
    ```

    **Python**: speaking-clock.py

    ```python
    # Synthesize spoken output
    responseSsml = " \
        <speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'> \
            <voice name='en-GB-LibbyNeural'> \
                {} \
                <break strength='weak'/> \
                Time to end this lab! \
            </voice> \
        </speak>".format(response_text)
    speak = speech_synthesizer.speak_ssml_async(responseSsml).get()
    if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

1. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **speaking-clock** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python speaking-clock.py
    ```

1. Wenn Sie dazu aufgefordert werden, sprechen Sie deutlich in das Mikrofon und sagen Sie „What time is it?“ (Wie spät ist es?). Das Programm sollte in der Stimme sprechen, die in der SSML angegeben ist (und damit die in der SpeechConfig angegebene Stimme außer Kraft setzen), und Ihnen die Uhrzeit mitteilen und nach einer Pause sagen, dass es Zeit ist, dieses Lab zu beenden – was auch der Fall ist!

## Weitere Informationen

Weitere Informationen zur Verwendung der APIs für **Spracherkennung** und **Sprachsynthese** finden Sie in der [Dokumentation zur Spracherkennung](https://learn.microsoft.com/azure/ai-services/speech-service/index-speech-to-text) und der [Dokumentation zur Sprachsynthese](https://learn.microsoft.com/azure/ai-services/speech-service/index-text-to-speech).
