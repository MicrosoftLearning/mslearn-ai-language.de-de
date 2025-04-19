---
lab:
  title: Erkennen und Synthetisieren von Sprache (Azure AI Foundry-Version)
  module: Module 4 - Create speech-enabled apps with Azure AI services
---

<!--
Possibly update to use standalone AI Service instead of Foundry?
-->

# Erkennen und Synthetisieren von Sprache

**Azure KI Speech** ist ein Dienst, der sprachbezogene Funktionen bietet, darunter:

- Eine *Spracherkennungs*-API, mit der Sie die Spracherkennung implementieren können (konvertieren hörbarer gesprochener Wörter in Text).
- Eine *Sprachsynthese*-API mit der Sie eine Sprachsynthese implementieren können (konvertieren von Text in hörbare Sprache).

In dieser Übung werden Sie diese beiden APIs verwenden, um eine Anwendung für eine sprechende Uhr zu implementieren.

> **HINWEIS** : Diese Übung ist so konzipiert, dass sie in der Azure-Cloudshell abgeschlossen wird, bei der direkter Zugriff auf die Soundhardware Ihres Computers nicht unterstützt wird. Das Lab wird daher Audiodateien für Spracheingabe- und -ausgabedatenströme verwenden. Der Code zum Erzielen der gleichen Ergebnisse mithilfe eines Mikrofons und Lautsprechers wird für Ihre Referenz bereitgestellt.

## Erstellen eines Azure KI Foundry-Projekts

Beginnen wir mit dem Erstellen eines Azure AI Foundry-Projekts.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartbereiche, die beim ersten Anmelden geöffnet werden, und verwenden Sie bei Bedarf das **Azure AI Foundry-Logo** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht:

    ![Screenshot des Azure KI Foundry-Portals.](./ai-foundry/media/ai-foundry-home.png)

1. Wählen Sie auf der Startseite **+ Projekt erstellen**.
1. Geben Sie im Assistenten **Projekt erstellen** einen gültigen Namen für Ihr Projekt ein und wählen Sie, falls ein vorhandener Hub vorgeschlagen wird, die Option zum Erstellen eines neuen. Überprüfen Sie dann die Azure-Ressourcen, die automatisch erstellt werden, um Ihren Hub und Ihr Projekt zu unterstützen.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Hubname**: *Ein gültiger Name für Ihren Hub*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*
    - **Standort**: Wählen Sie eine beliebige verfügbare Region aus
    - **Azure KI Services oder Azure OpenAI verbinden**: *Erstellen Sie eine neue KI-Dienst-Ressource*
    - **Verbinden von Azure KI-Suche**: *Erstellen Sie eine neue Ressource von Azure KI-Suche mit einem eindeutigen Namen*

1. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
1. Sobald Ihr Projekt erstellt wurde, schließen Sie alle angezeigten Tipps und überprüfen Sie die Projektseite im Azure AI Foundry-Portal, die in etwa wie in der folgenden Abbildung aussehen sollte:

    ![Screenshot eines Azure KI-Projekts im Azure AI Foundry-Portal.](./ai-foundry/media/ai-foundry-project.png)

## Vorbereiten und Konfigurieren der sprechenden Uhr-App

1. Wechseln Sie im Azure AI Foundry-Portal zur **Übersichtsseite** Ihres Projekts.
1. Notieren Sie sich im Bereich **Projektdetails** die **Projekt-Verbindungszeichenfolge** und den **Speicherort** für Ihr Projekt. Sie verwenden die Verbindungszeichenfolge, um in einer Client-Anwendung eine Verbindung zu Ihrem Projekt herzustellen, und Sie benötigen den Speicherort, um eine Verbindung zum Azure KI Services Speech-Endpunkt herzustellen.
1. Öffnen Sie eine neue Browserregisterkarte (wobei das Azure AI Foundry-Portal auf der vorhandenen Registerkarte geöffnet bleibt). Wechseln Sie dann in der neuen Registerkarte zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit Ihren Azure-Anmeldedaten an, wenn Sie dazu aufgefordert werden.
1. Verwenden Sie die Taste **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung aus. Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Bereich am unteren Rand des Azure-Portals.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    > **Tipp**: Wenn Sie Befehle in die Cloudshell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers einnehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Geben Sie im PowerShell-Bereich die folgenden Befehle ein, um das GitHub-Repository für diese Übung zu klonen:

    ```
   rm -r mslearn-ai-language -f
   git clone https://github.com/microsoftlearning/mslearn-ai-language mslearn-ai-language
    ```

    ***Folgen Sie nun den Schritten für die von Ihnen gewählte Programmiersprache.***

1. Nachdem das Repository geklont wurde, navigieren Sie zu dem Ordner, der die Anwendungscodedateien der sprechenden Uhr enthält:  

    **Python**

    ```
   cd mslearn-ai-language/labfiles/07b-speech/python/speaking-clock
    ```

    **C#**

    ```
   cd mslearn-ai-language/labfiles/07b-speech/c-sharp/speaking-clock
    ```

1. Geben Sie im Befehlszeilenbereich von Cloud Shell den folgenden Befehl ein, um die Bibliotheken zu installieren, die Sie verwenden möchten:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-identity azure-ai-projects azure-cognitiveservices-speech==1.42.0
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Microsoft.CognitiveServices.Speech --version 1.42.0
    ```

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Konfigurationsdatei zu bearbeiten:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Ersetzen Sie in der Codedatei die Platzhalter **your_project_endpoint** und **your_location** durch die Verbindungszeichenfolge und den Speicherort für Ihr Projekt (kopiert von der Seite **Overview** des Projekts im Azure AI Foundry-Portal).
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q**, um den Code-Editor zu schließen, während die Befehlszeile der Cloud Shell geöffnet bleibt.

## Hinzufügen von Code zur Verwendung des Azure KI Speech SDK

> **Tipp**: Achten Sie beim Hinzufügen von Code auf den richtigen Einzug.

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Codedatei zu bearbeiten:

    **Python**

    ```
   code speaking-clock.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Suchen Sie oben in der Codedatei unter den vorhandenen Namespace-Referenzen den Kommentar **Import namespaces**. Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie für die Verwendung des Azure KI Speech-SDK mit der Azure KI Services-Ressource in Ihrem Azure AI Foundry-Projekt benötigen:

    **Python**

    ```python
   # Import namespaces
   from dotenv import load_dotenv
   from azure.ai.projects.models import ConnectionType
   from azure.identity import DefaultAzureCredential
   from azure.core.credentials import AzureKeyCredential
   from azure.ai.projects import AIProjectClient
   import azure.cognitiveservices.speech as speech_sdk
    ```

    **C#**

    ```csharp
   // Import namespaces
   using Azure.Identity;
   using Azure.AI.Projects;
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Audio;
    ```

1. In der **Hauptfunktion** wird unter dem Kommentar **Get config settings** darauf hingewiesen, dass der Code die Projektverbindungszeichenfolge und den Speicherort lädt, die Sie in der Konfigurationsdatei definiert haben.

1. Fügen Sie den folgenden Code unter dem Kommentar **Get AI Speech endpoint and key from the project** hinzu:

    **Python**

    ```python
   # Get AI Services key from the project
   project_client = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())

   ai_svc_connection = project_client.connections.get_default(
      connection_type=ConnectionType.AZURE_AI_SERVICES,
      include_credentials=True, 
    )

   ai_svc_key = ai_svc_connection.key

    ```

    **C#**

    ```csharp
   // Get AI Services key from the project
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());

   ConnectionResponse aiSvcConnection = projectClient.GetConnectionsClient().GetDefaultConnection(ConnectionType.AzureAIServices, true);

   var apiKeyAuthProperties = aiSvcConnection.Properties as ConnectionPropertiesApiKeyAuth;

   var aiSvcKey = apiKeyAuthProperties.Credentials.Key;
    ```

    Dieser Code stellt eine Verbindung mit Ihrem Azure AI Foundry-Projekt her, ruft die mit KI Services verbundene Standardressource ab und ruft den für die Verwendung benötigten Authentifizierungsschlüssel ab.

1. Fügen Sie unter dem Kommentar **Configure speech service** den folgenden Code hinzu, um den KI Services-Schlüssel und die Region Ihres Projekts zu verwenden und Ihre Verbindung zum Azure KI Services Speech-Endpunkt zu konfigurieren.

   **Python**

    ```python
   # Configure speech service
   speech_config = speech_sdk.SpeechConfig(ai_svc_key, location)
   print('Ready to use speech service in:', speech_config.region)
    ```

    **C#**

    ```csharp
   // Configure speech service
   speechConfig = SpeechConfig.FromSubscription(aiSvcKey, location);
   Console.WriteLine("Ready to use speech service in " + speechConfig.Region);
    ```

1. Speichern Sie Ihre Änderungen (*STRG+S*), lassen Sie den Code-Editor jedoch geöffnet.

## Ausführen der App

Bisher kann die App nichts anderes, als eine Verbindung zu Ihrem Azure AI Foundry-Projekt herzustellen, um die für die Nutzung des Sprachdienstes erforderlichen Details abzurufen. Es ist jedoch nützlich, sie auszuführen und zu überprüfen, ob sie funktioniert, bevor Sie Sprachfunktionen hinzufügen.

1. Geben Sie in der Befehlszeile unterhalb des Code-Editors den folgenden Azure CLI-Befehl ein, um das Azure-Konto zu ermitteln, das für die Sitzung angemeldet ist:

    ```
   az account show
    ```

    Die resultierende JSON-Ausgabe sollte Details Ihres Azure-Kontos und das Abonnement enthalten, in dem Sie arbeiten (das sollte dasselbe Abonnement sein, in dem Sie Ihr Azure AI Foundry-Projekt erstellt haben.)

    Ihre App verwendet die Azure-Anmeldeinformationen für den Kontext, in dem sie ausgeführt wird, um die Verbindung mit Ihrem Projekt zu authentifizieren. In einer Produktionsumgebung kann die App so konfiguriert werden, dass sie mit einer verwalteten Identität ausgeführt wird. In dieser Entwicklungsumgebung werden Ihre authentifizierten Anmeldeinformationen für die Cloudshell-Sitzung verwendet.

    > **Hinweis**: Sie können sich mit dem Azure CLI-Befehl `az login` bei Azure in Ihrer Entwicklungsumgebung anmelden. In diesem Fall hat sich die Cloud-Shell bereits mit den Azure-Anmeldedaten angemeldet, mit denen Sie sich beim Portal angemeldet haben. Eine explizite Anmeldung ist also nicht erforderlich. Weitere Informationen zur Verwendung der Azure CLI zur Authentifizierung bei Azure finden Sie unter [Authentifizieren bei Azure mithilfe der Azure CLI](https://learn.microsoft.com/cli/azure/authenticate-azure-cli).

1. Geben Sie in der Befehlszeile den folgenden sprachspezifischen Befehl ein, um die Sprachuhr-App auszuführen:

    **Python**

    ```
   python speaking-clock.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Wenn Sie C# verwenden, können Sie alle Warnungen zur Verwendung des **await**-Operators in asynchronen Methoden ignorieren. Dies wird später behoben. Der Code sollte die Region der Speech-Dienstressource anzeigen, die die Anwendung verwenden wird. Eine erfolgreiche Ausführung gibt an, dass die App mit Ihrem Azure AI Foundry-Projekt verbunden und den Schlüssel abgerufen hat, den sie für die Verwendung des Azure KI Speech-Diensts benötigt.

## Hinzufügen von Code zur Spracherkennung

Da Sie nun eine **SpeechConfig** für den Sprachdienst in der Azure KI Services-Ressource Ihres Projekts haben, können Sie die **Sprache-in-Text**-API verwenden, um Sprache zu erkennen und in Text umzuwandeln.

In diesem Verfahren wird die Spracheingabe aus einer Audiodatei erfasst, die Sie hier wiedergeben können:

<video controls src="ai-foundry/media/Time.mp4" title="Wie spät ist es?" width="150"></video>

1. Beachten Sie in der **Main**-Funktion, dass der Code die **TranscribeCommand**-Funktion verwendet, um gesprochene Eingaben zu akzeptieren. Fügen Sie dann in der Funktion **TranscribeCommand** unter dem Kommentar **Configure speech recognition** (Spracherkennung konfigurieren) den entsprechenden Code unten ein, um einen **SpeechRecognizer**-Client zu erstellen, der zur Erkennung und Transkription von Sprache aus einer Audiodatei verwendet werden kann:

    **Python**

    ```python
   # Configure speech recognition
   current_dir = os.getcwd()
   audioFile = current_dir + '/time.wav'
   audio_config = speech_sdk.AudioConfig(filename=audioFile)
   speech_recognizer = speech_sdk.SpeechRecognizer(speech_config, audio_config)
    ```

    **C#**

    ```csharp
   // Configure speech recognition
   string audioFile = "time.wav";
   using AudioConfig audioConfig = AudioConfig.FromWavFileInput(audioFile);
   using SpeechRecognizer speechRecognizer = new SpeechRecognizer(speechConfig, audioConfig);
    ```

1. Fügen Sie in der Funktion **TranscribeCommand** unter dem Kommentar **Process speech input** (Spracheingabe verarbeiten) den folgenden Code ein, um auf gesprochene Eingaben zu lauschen. Achten Sie darauf, dass Sie den Code am Ende der Funktion, die den Befehl zurückgibt, nicht ersetzen:

    **Python**

    ```python
   # Process speech input
   print("Listening...")
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

    **C#**

    ```csharp
   // Process speech input
   Console.WriteLine("Listening...");
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

1. Speichern Sie Ihre Änderungen (*STRG+S*), und geben Sie dann in der Befehlszeile unter dem Code-Editor den folgenden Befehl ein, um das Programm auszuführen:

    **Python**

    ```
   python speaking-clock.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Überprüfen Sie die Ausgabe der Anwendung, die die Rede in der Audiodatei erfolgreich „hören“ und eine entsprechende Antwort zurückgeben sollte (beachten Sie, dass Ihre Azure-Cloud-Shell möglicherweise auf einem Server ausgeführt wird, der sich in einer anderen Zeitzone als Ihrer befindet!)

    > **Tipp**: Wenn der SpeechRecognizer einen Fehler erkennt, wird das Ergebnis „Abgebrochen“ angezeigt. Der Code in der Anwendung zeigt dann die Fehlermeldung an. Die wahrscheinlichste Ursache ist ein falscher Regionswert in der Konfigurationsdatei.

## Synthetisieren von Sprache

Ihre Anwendung für die sprechende Uhr akzeptiert gesprochene Eingaben, aber sie spricht nicht wirklich! Lassen Sie uns das beheben, indem wir Code zur Sprachsynthese hinzufügen.

Aufgrund der Hardwareeinschränkungen der Cloudshell leiten wir die synthetisierte Sprachausgabe erneut an eine Datei weiter.

1. Beachten Sie in der Funktion **Main** für Ihr Programm, dass der Code die Funktion **TellTime** verwendet, um dem Benutzer die aktuelle Zeit mitzuteilen.
1. Fügen Sie in der Funktion **TellTime** unter dem Kommentar **Configure speech synthesis** (Sprachsynthese konfigurieren) den folgenden Code ein, um einen **SpeechSynthesizer**-Client zu erstellen, der zum Generieren von Sprachausgaben verwendet werden kann:

    **Python**

    ```python
   # Configure speech synthesis
   output_file = "output.wav"
   speech_config.speech_synthesis_voice_name = "en-GB-RyanNeural"
   audio_config = speech_sdk.audio.AudioConfig(filename=output_file)
   speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config, audio_config,)
    ```

    **C#**

    ```csharp
   // Configure speech synthesis
   var outputFile = "output.wav";
   speechConfig.SpeechSynthesisVoiceName = "en-GB-RyanNeural";
   using var audioConfig = AudioConfig.FromWavFileOutput(outputFile);
   using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    ```

1. Fügen Sie in der Funktion **TellTime** unter dem Kommentar **Synthesize spoken output** (Gesprochene Ausgabe synthetisieren) den folgenden Code ein, um die gesprochene Ausgabe zu generieren. Achten Sie darauf, dass Sie den Code am Ende der Funktion, die die Antwort ausgibt, nicht ersetzen:

    **Python**

    ```python
   # Synthesize spoken output
   speak = speech_synthesizer.speak_text_async(response_text).get()
   if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
       print(speak.reason)
   else:
       print("Spoken output saved in " + outputFile)
    ```

    **C#**

    ```csharp
   // Synthesize spoken output
   SpeechSynthesisResult speak = await speechSynthesizer.SpeakTextAsync(responseText);
   if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
   {
       Console.WriteLine(speak.Reason);
   }
   else
   {
       Console.WriteLine("Spoken output saved in " + outputFile);
   }
    ```

1. Speichern Sie Ihre Änderungen (*STRG+S*), und geben Sie dann in der Befehlszeile unter dem Code-Editor den folgenden Befehl ein, um das Programm auszuführen:

   **Python**

    ```
   python speaking-clock.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Überprüfen Sie die Ausgabe der Anwendung, die anzeigen sollte, dass die Sprachausgabe in einer Datei gespeichert wurde.
1. Wenn Sie über einen Media Player verfügen, der .wav-Audiodateien abspielen kann, klicken Sie in der Symbolleiste des Cloud-Shell-Bereichs auf die Schaltfläche **Dateien hochladen/herunterladen**, um die Audiodatei aus Ihrem App-Ordner herunterzuladen und anschließend abzuspielen:

    **Python**

    /home/*user*`/mslearn-ai-language/Labfiles/07b-speech/Python/speaking-clock/output.wav`

    **C#**

    /home/*user*`/mslearn-ai-language/Labfiles/07b-speech/C-Sharp/speaking-clock/output.wav`

    Die Datei sollte in etwa so klingen:

    <video controls src="./ai-foundry/media/Output.mp4" title="Es ist 2:15 Uhr." width="150"></video>

## Verwenden von Sprachsynthese-Markupsprache

Mit der Speech Synthesis Markup Language (SSML) können Sie die Art und Weise, wie Ihre Sprache synthetisiert wird, mithilfe eines XML-basierten Formats anpassen.

1. Ersetzen Sie in der Funktion **TellTime** den gesamten aktuellen Code unter dem Kommentar **Synthetize spoken output** (Gesprochene Ausgabe synthetisieren) durch den folgenden Code (belassen Sie den Code unter dem Kommentar **Print the response** (Antwort ausgeben)):

    **Python**

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
   else:
       print("Spoken output saved in " + outputFile)
    ```

   **C#**

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
   else
   {
        Console.WriteLine("Spoken output saved in " + outputFile);
   }
    ```

1. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **speaking-clock** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **Python**

    ```
   python speaking-clock.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Überprüfen Sie die Ausgabe der Anwendung, die anzeigen sollte, dass die Sprachausgabe in einer Datei gespeichert wurde.
1. Wenn Sie über einen Media Player verfügen, der .wav-Audiodateien abspielen kann, klicken Sie in der Symbolleiste des Cloud-Shell-Bereichs auf die Schaltfläche **Dateien hochladen/herunterladen**, um die Audiodatei aus Ihrem App-Ordner herunterzuladen und anschließend abzuspielen:

    **Python**

    /home/*user*`/mslearn-ai-language/Labfiles/07b-speech/Python/speaking-clock/output.wav`

    **C#**

    /home/*user*`/mslearn-ai-language/Labfiles/07b-speech/C-Sharp/speaking-clock/output.wav`

    Die Datei sollte in etwa so klingen:
    
    <video controls src="./ai-foundry/media/Output2.mp4" title="Es ist 5:30 Uhr. Zeit, dieses Lab zu beenden." width="150"></video>

## Bereinigen

Wenn Sie mit der Erkundung von Azure KI Speech fertig sind, sollten Sie die in dieser Übung erstellten Ressourcen löschen, um unnötige Azure-Kosten zu vermeiden.

1. Kehren Sie zu der Browserregisterkarte zurück, die das Azure-Portal enthält (oder öffnen Sie das [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` erneut in einer neuen Browserregisterkarte) und sehen Sie sich den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.

## Was geschieht, wenn Sie über ein Mikrofon und einen Lautsprecher verfügen?

In dieser Übung haben Sie Audiodateien für die Spracheingabe und -ausgabe verwendet. Sehen wir uns an, wie der Code geändert werden kann, um Audiohardware zu verwenden.

### Spracherkennung unter Verwendung des Mikrofons

Wenn Sie über ein Mikrofon verfügen, können Sie den folgenden Code verwenden, um gesprochene Eingaben für die Spracherkennung zu erfassen:

**Python**

```python
# Configure speech recognition
audio_config = speech_sdk.AudioConfig(use_default_microphone=True)
speech_recognizer = speech_sdk.SpeechRecognizer(speech_config, audio_config)
print('Speak now...')

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

**C#**

```csharp
// Configure speech recognition
using AudioConfig audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using SpeechRecognizer speechRecognizer = new SpeechRecognizer(speechConfig, audioConfig);
Console.WriteLine("Speak now...");

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

> **Hinweis**: Das Standardmikrofon des Systems ist die Standardaudioeingabe, sodass Sie auch einfach die AudioConfig ganz weglassen können!

### Verwenden der Sprachsynthese mit einem Lautsprecher

Wenn Sie über einen Lautsprecher verfügen, können Sie den folgenden Code verwenden, um Sprache zu synthetisieren.

**Python**

```python
response_text = 'The time is {}:{:02d}'.format(now.hour,now.minute)

# Configure speech synthesis
speech_config.speech_synthesis_voice_name = "en-GB-RyanNeural"
audio_config = speech_sdk.audio.AudioOutputConfig(use_default_speaker=True)
speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config, audio_config)

# Synthesize spoken output
speak = speech_synthesizer.speak_text_async(response_text).get()
if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
    print(speak.reason)
```

**C#**

```csharp
var now = DateTime.Now;
string responseText = "The time is " + now.Hour.ToString() + ":" + now.Minute.ToString("D2");

// Configure speech synthesis
speechConfig.SpeechSynthesisVoiceName = "en-GB-RyanNeural";
using var audioConfig = AudioConfig.FromDefaultSpeakerOutput();
using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Synthesize spoken output
SpeechSynthesisResult speak = await speechSynthesizer.SpeakTextAsync(responseText);
if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
{
    Console.WriteLine(speak.Reason);
}
```

> **Hinweis**: Der Standardlautsprecher des Systems ist die Standardaudioausgabe, sodass Sie auch einfach die AudioConfig ganz weglassen können!

## Weitere Informationen

Weitere Informationen zur Verwendung der APIs für **Spracherkennung** und **Sprachsynthese** finden Sie in der [Dokumentation zur Spracherkennung](https://learn.microsoft.com/azure/ai-services/speech-service/index-speech-to-text) und der [Dokumentation zur Sprachsynthese](https://learn.microsoft.com/azure/ai-services/speech-service/index-text-to-speech).
