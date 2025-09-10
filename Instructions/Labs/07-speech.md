---
lab:
  title: Erkennen und Synthetisieren von Sprache
  description: 'Implementieren Sie eine Sprechuhr, die Sprache in Text und Text in Sprache konvertiert.'
---

# Erkennen und Synthetisieren von Sprache

**Azure KI Speech** ist ein Dienst, der sprachbezogene Funktionen bietet, darunter:

- Eine *Spracherkennungs*-API, mit der Sie die Spracherkennung implementieren können (konvertieren hörbarer gesprochener Wörter in Text).
- Eine *Sprachsynthese*-API mit der Sie eine Sprachsynthese implementieren können (konvertieren von Text in hörbare Sprache).

In dieser Übung werden Sie diese beiden APIs verwenden, um eine Anwendung für eine sprechende Uhr zu implementieren.

Obwohl diese Übung auf Python basiert, können Sie Sprachanwendungen mit mehreren sprachspezifischen SDKs entwickeln, einschließlich:

- [Azure KI Speech-SDK für Python](https://pypi.org/project/azure-cognitiveservices-speech/)
- [Azure KI Speech-SDK für .NET](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech)
- [Azure KI Speech-SDK für JavaScript](https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk)

Diese Übung dauert ca. **30** Minuten.

> **HINWEIS** : Diese Übung ist so konzipiert, dass sie in der Azure-Cloudshell abgeschlossen wird, bei der direkter Zugriff auf die Soundhardware Ihres Computers nicht unterstützt wird. Das Lab wird daher Audiodateien für Spracheingabe- und -ausgabedatenströme verwenden. Der Code zum Erzielen der gleichen Ergebnisse mithilfe eines Mikrofons und Lautsprechers wird für Ihre Referenz bereitgestellt.

## Erstellen einer Azure KI Speech-Ressource

Beginnen wir mit der Erstellung einer Azure KI Speech-Ressource.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit dem Microsoft-Konto an, das mit Ihrem Azure-Abonnement verknüpft ist.
1. Suchen Sie im oberen Suchfeld nach dem **Sprachdienst**. Wählen Sie ihn aus der Liste aus und dann **Erstellen**.
1. Stellen Sie die Ressource mithilfe der folgenden Einstellungen bereit:
    - **Abonnement**: *Ihr Azure-Abonnement*.
    - **Ressourcengruppe**: *Wählen oder erstellen Sie eine Ressourcengruppe*.
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein*.
    - **Tarif**: Wählen Sie **F0** (*Free*) oder **S** (*Standard*) aus, falls F nicht verfügbar ist.
1. Wählen Sie **Überprüfen und erstellen** aus und dann **Erstellen**, um die Ressource bereitzustellen.
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, und wechseln Sie dann zur bereitgestellten Ressource.
1. Sie können die Seite **Schlüssel und Endpunkte** im Abschnitt **Ressourcenverwaltungt** anzeigen. Sie benötigen die Informationen auf dieser Seite später in der Übung.

## Vorbereiten und Konfigurieren der sprechenden Uhr-App

1. Schließen Sie die Seite **Schlüssel und Endpunkte** nicht. Verwenden Sie im Azure-Portal die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung aus. Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Bereich am unteren Rand des Azure-Portals.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    **<font color="red">Stellen Sie sicher, dass Sie zur klassischen Version der Cloud Shell gewechselt haben, bevor Sie fortfahren.</font>**

1. Geben Sie im PowerShell-Bereich die folgenden Befehle ein, um das GitHub-Repository für diese Übung zu klonen:

    ```
   rm -r mslearn-ai-language -f
   git clone https://github.com/microsoftlearning/mslearn-ai-language
    ```

    > **Tipp**: Wenn Sie Befehle in die Cloud Shell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers in Anspruch nehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Nachdem das Repository geklont wurde, navigieren Sie zu dem Ordner, der die Anwendungscodedateien der sprechenden Uhr enthält:  

    ```
   cd mslearn-ai-language/Labfiles/07-speech/Python/speaking-clock
    ```

1. Führen Sie im Befehlszeilenbereich den folgenden Befehl aus, um die Codedateien im Ordner **speaking-clock** anzuzeigen:

    ```
   ls -a -l
    ```

    Die Dateien enthalten eine Konfigurationsdatei (**.env**) und eine Codedatei (**speaking-clock.py**). Die Audiodateien, die Ihre Anwendung verwendet, befinden sich im Unterordner **Audio**.

1. Erstellen Sie eine virtuelle Python-Umgebung und installieren Sie das Azure KI Speech-SDK-Paket sowie andere erforderliche Pakete, indem Sie den folgenden Befehl ausführen:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-cognitiveservices-speech==1.42.0
    ```

1. Geben Sie den folgenden Befehl ein, um die Konfigurationsdatei zu bearbeiten:

    ```
   code .env
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Aktualisieren Sie die Konfigurationswerte so, dass sie die **Region** und einen **Schlüssel** aus der Azure KI Speech-Ressource enthalten, die Sie erstellt haben (verfügbar auf der Seite **Schlüssel und Endpunkt** für Ihre Azure KI Übersetzer-Ressource im Azure-Portal).
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q**, um den Code-Editor zu schließen, während die Befehlszeile der Cloud Shell geöffnet bleibt.

## Hinzufügen von Code zur Verwendung des Azure KI Speech SDK

> **Tipp**: Achten Sie beim Hinzufügen von Code auf den richtigen Einzug.

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Codedatei zu bearbeiten:

    ```
   code speaking-clock.py
    ```

1. Suchen Sie oben in der Codedatei unter den vorhandenen Namespace-Referenzen den Kommentar **Import namespaces**. Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie für die Verwendung des Azure KI Speech SDK benötigen:

    ```python
   # Import namespaces
   from azure.core.credentials import AzureKeyCredential
   import azure.cognitiveservices.speech as speech_sdk
    ```

1. Beachten Sie, dass der Code in der **Main**-Funktion unter dem Kommentar **Get config settings** von Ihnen in der Konfigurationsdatei definierte Werte für Schlüssel und Region lädt.

1. Fügen Sie unter dem Kommentar **Configure speech service** den folgenden Code hinzu, um den KI Services-Schlüssel und die Region zu verwenden und Ihre Verbindung zum Azure KI Services Speech-Endpunkt zu konfigurieren:

    ```python
   # Configure speech service
   speech_config = speech_sdk.SpeechConfig(speech_key, speech_region)
   print('Ready to use speech service in:', speech_config.region)
    ```

1. Speichern Sie Ihre Änderungen (*STRG+S*), lassen Sie den Code-Editor jedoch geöffnet.

## Ausführen der App

Bisher kann die App nichts anderes, als eine Verbindung zu Ihrem Azure KI Speech-Dienst herzustellen. Es ist jedoch nützlich, sie auszuführen und zu überprüfen, ob sie funktioniert, bevor Sie Sprachfunktionen hinzufügen.

1. Geben Sie in der Befehlszeile den folgenden Befehl ein, um die Sprachuhr-App auszuführen:

    ```
   python speaking-clock.py
    ```

    Der Code sollte die Region der Speech-Dienstressource anzeigen, die die Anwendung verwenden wird. Bei einer erfolgreichen Ausführung wird angegeben, dass die App mit Ihrer Azure KI Speech-Ressource verbunden ist.

## Hinzufügen von Code zur Spracherkennung

Da Sie nun eine **SpeechConfig** für den Sprachdienst in der Azure KI Services-Ressource Ihres Projekts haben, können Sie die **Sprache-in-Text**-API verwenden, um Sprache zu erkennen und in Text umzuwandeln.

In diesem Verfahren wird die Spracheingabe aus einer Audiodatei erfasst, die Sie hier wiedergeben können:

<video controls src="https://github.com/MicrosoftLearning/mslearn-ai-language/raw/refs/heads/main/Instructions/media/Time.mp4" title="Wie spät ist es?" width="150"></video>

1. Beachten Sie, dass der Code in der Codedatei die **TranscribeCommand**-Funktion verwendet, um gesprochene Eingaben zu akzeptieren. Suchen Sie dann in der Funktion **TranscribeCommand** den Kommentar **Configure speech recognition** und fügen Sie den entsprechenden Code unten ein, um einen **SpeechRecognizer**-Client zu erstellen, der zur Erkennung und Transkription von Sprache aus einer Audiodatei verwendet werden kann:

    ```python
   # Configure speech recognition
   current_dir = os.getcwd()
   audioFile = current_dir + '/time.wav'
   audio_config = speech_sdk.AudioConfig(filename=audioFile)
   speech_recognizer = speech_sdk.SpeechRecognizer(speech_config, audio_config)
    ```

1. Fügen Sie in der Funktion **TranscribeCommand** unter dem Kommentar **Process speech input** (Spracheingabe verarbeiten) den folgenden Code ein, um auf gesprochene Eingaben zu lauschen. Achten Sie darauf, dass Sie den Code am Ende der Funktion, die den Befehl zurückgibt, nicht ersetzen:

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

1. Speichern Sie Ihre Änderungen (*STRG+S*) und führen Sie das Programm dann in der Befehlszeile unter dem Code-Editor erneut aus:
1. Überprüfen Sie die Ausgabe, die die Rede in der Audiodatei erfolgreich „hören“ und eine entsprechende Antwort zurückgeben sollte (beachten Sie, dass Ihre Azure-Cloud-Shell möglicherweise auf einem Server ausgeführt wird, der sich in einer anderen Zeitzone als Ihrer befindet!)

    > **Tipp**: Wenn der SpeechRecognizer einen Fehler erkennt, wird das Ergebnis „Abgebrochen“ angezeigt. Der Code in der Anwendung zeigt dann die Fehlermeldung an. Die wahrscheinlichste Ursache ist ein falscher Regionswert in der Konfigurationsdatei.

## Synthetisieren von Sprache

Ihre Anwendung für die sprechende Uhr akzeptiert gesprochene Eingaben, aber sie spricht nicht wirklich! Lassen Sie uns das beheben, indem wir Code zur Sprachsynthese hinzufügen.

Aufgrund der Hardwareeinschränkungen der Cloudshell leiten wir die synthetisierte Sprachausgabe erneut an eine Datei weiter.

1. Beachten Sie, dass der Code in der Codedatei die **TellTime**-Funktion verwendet, um Benutzenden über die aktuelle Zeit zu informieren.
1. Fügen Sie in der Funktion **TellTime** unter dem Kommentar **Configure speech synthesis** (Sprachsynthese konfigurieren) den folgenden Code ein, um einen **SpeechSynthesizer**-Client zu erstellen, der zum Generieren von Sprachausgaben verwendet werden kann:

    ```python
   # Configure speech synthesis
   output_file = "output.wav"
   speech_config.speech_synthesis_voice_name = "en-GB-RyanNeural"
   audio_config = speech_sdk.audio.AudioConfig(filename=output_file)
   speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config, audio_config,)
    ```

1. Fügen Sie in der Funktion **TellTime** unter dem Kommentar **Synthesize spoken output** (Gesprochene Ausgabe synthetisieren) den folgenden Code ein, um die gesprochene Ausgabe zu generieren. Achten Sie darauf, dass Sie den Code am Ende der Funktion, die die Antwort ausgibt, nicht ersetzen:

    ```python
   # Synthesize spoken output
   speak = speech_synthesizer.speak_text_async(response_text).get()
   if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
   else:
        print("Spoken output saved in " + output_file)
    ```

1. Speichern Sie Ihre Änderungen (*STRG+S*) und führen Sie das Programm erneut aus. Es sollte angegeben werden, dass die Sprachausgabe in einer Datei gespeichert wurde.

1. Wenn Sie über einen Media Player verfügen, der WAV-Audiodateien wiedergeben kann, laden Sie die generierte Datei herunter, indem Sie den folgenden Befehl eingeben:

    ```
   download ./output.wav
    ```

    Der Downloadbefehl erstellt unten rechts im Browser einen Popuplink, den Sie auswählen können, um die Datei herunterzuladen und zu öffnen.

    Die Datei sollte in etwa so klingen:

    <video controls src="https://github.com/MicrosoftLearning/mslearn-ai-language/raw/refs/heads/main/Instructions/media/Output.mp4" title="Es ist 2:15 Uhr." width="150"></video>

## Verwenden von Sprachsynthese-Markupsprache

Mit der Speech Synthesis Markup Language (SSML) können Sie die Art und Weise, wie Ihre Sprache synthetisiert wird, mithilfe eines XML-basierten Formats anpassen.

1. Ersetzen Sie in der Funktion **TellTime** den gesamten aktuellen Code unter dem Kommentar **Synthetize spoken output** (Gesprochene Ausgabe synthetisieren) durch den folgenden Code (belassen Sie den Code unter dem Kommentar **Print the response** (Antwort ausgeben)):

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
       print("Spoken output saved in " + output_file)
    ```

1. Speichern Sie Ihre Änderungen und führen Sie das Programm erneut aus. Es sollte erneut angegeben werden, dass die Sprachausgabe in einer Datei gespeichert wurde.
1. Laden Sie die generierte Datei herunter und geben Sie sie wieder. Sie sollte ähnlich wie Folgendes klingen:
    
    <video controls src="https://github.com/MicrosoftLearning/mslearn-ai-language/raw/refs/heads/main/Instructions/media/Output2.mp4" title="Es ist 5:30 Uhr. Zeit, dieses Lab zu beenden." width="150"></video>

## (OPTIONAL) Was geschieht, wenn Sie über ein Mikrofon und einen Lautsprecher verfügen?

In dieser Übung haben Sie Audiodateien für die Spracheingabe und -ausgabe verwendet. Sehen wir uns an, wie der Code geändert werden kann, um Audiohardware zu verwenden.

### Spracherkennung unter Verwendung des Mikrofons

Wenn Sie über ein Mikrofon verfügen, können Sie den folgenden Code verwenden, um gesprochene Eingaben für die Spracherkennung zu erfassen:

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

> **Hinweis**: Das Standardmikrofon des Systems ist die Standardaudioeingabe, sodass Sie auch einfach die AudioConfig ganz weglassen können!

### Verwenden der Sprachsynthese mit einem Lautsprecher

Wenn Sie über einen Lautsprecher verfügen, können Sie den folgenden Code verwenden, um Sprache zu synthetisieren.

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

> **Hinweis**: Der Standardlautsprecher des Systems ist die Standardaudioausgabe, sodass Sie auch einfach die AudioConfig ganz weglassen können!

## Bereinigen

Wenn Sie mit der Erkundung von Azure KI Speech fertig sind, sollten Sie die in dieser Übung erstellten Ressourcen löschen, um unnötige Azure-Kosten zu vermeiden.

1. Schließen Sie den Azure Cloud Shell-Bereich
1. Navigieren Sie im Azure-Portal zu der Azure KI Speech-Ressource, die Sie in diesem Lab erstellt haben.
1. Wählen Sie auf der Ressourcenseite **Löschen** aus, und folgen Sie den Anweisungen zum Löschen der Ressource.

## Weitere Informationen

Weitere Informationen zur Verwendung der APIs für **Spracherkennung** und **Sprachsynthese** finden Sie in der [Dokumentation zur Spracherkennung](https://learn.microsoft.com/azure/ai-services/speech-service/index-speech-to-text) und der [Dokumentation zur Sprachsynthese](https://learn.microsoft.com/azure/ai-services/speech-service/index-text-to-speech).
