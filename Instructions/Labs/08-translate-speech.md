---
lab:
  title: Übersetzen von gesprochener Sprache
  description: Übersetzen Sie Sprache und implementieren Sie sie in Ihrer eigenen App.
---

# Übersetzen von gesprochener Sprache

Azure KI Speech enthält eine Sprachübersetzungs-API, mit der Sie gesprochene Sprache übersetzen können. Angenommen, Sie möchten eine Übersetzungsanwendung entwickeln, die Benutzer verwenden können, wenn sie an Orte reisen, wo sie nicht die lokale Sprache sprechen. Sie könnten dann Ausdrücke wie „Where is the station?“ (Wo ist der Bahnhof?) oder „I need to find a pharmacy“ (Ich suche eine Apotheke) in ihrer Muttersprache sagen und diese in die lokale Sprache übersetzen lassen. In dieser Übung verwenden Sie das Azure KI Speech-SDK für Python, um eine einfache Anwendung basierend auf diesem Beispiel zu erstellen.

Obwohl diese Übung auf Python basiert, können Sie ähnliche Anwendungen zur Sprachübersetzung mit mehreren sprachspezifischen SDKs entwickeln, einschließlich:

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

## Vorbereiten der Entwicklung einer App in Cloud Shell

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

1. Nachdem das Repository geklont wurde, navigieren Sie zu dem Ordner, der die Codedateien enthält:

    ```
   cd mslearn-ai-language/Labfiles/08-speech-translation/Python/translator
    ```

1. Führen Sie im Befehlszeilenbereich den folgenden Befehl aus, um die Codedateien im Ordner **translator** anzuzeigen:

    ```
   ls -a -l
    ```

    Die Dateien enthalten eine Konfigurationsdatei (**.env**) und eine Codedatei (**translator.py**).

1. Erstellen Sie eine virtuelle Python-Umgebung und installieren Sie das Azure KI Speech-SDK-Paket sowie andere erforderliche Pakete, indem Sie den folgenden Befehl ausführen:

    ```
    python -m venv labenv
    ./labenv/bin/Activate.ps1
    pip install -r requirements.txt azure-cognitiveservices-speech==1.42.0
    ```

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Konfigurationsdatei zu bearbeiten:

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
   code translator.py
    ```

1. Suchen Sie oben in der Codedatei unter den vorhandenen Namespace-Referenzen den Kommentar **Import namespaces**. Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie für die Verwendung des Azure KI Speech SDK benötigen:

    ```python
   # Import namespaces
   from azure.core.credentials import AzureKeyCredential
   import azure.cognitiveservices.speech as speech_sdk
    ```

1. Beachten Sie, dass der Code in der **Main**-Funktion unter dem Kommentar **Get config settings** von Ihnen in der Konfigurationsdatei definierte Werte für Schlüssel und Region lädt.

1. Suchen Sie den folgenden Code unter dem Kommentar **Configure translation** und fügen Sie den folgenden Code hinzu, um Ihre Verbindung mit dem Azure KI Services-Sprachendpunkt zu konfigurieren:

    ```python
   # Configure translation
   translation_config = speech_sdk.translation.SpeechTranslationConfig(speech_key, speech_region)
   translation_config.speech_recognition_language = 'en-US'
   translation_config.add_target_language('fr')
   translation_config.add_target_language('es')
   translation_config.add_target_language('hi')
   print('Ready to translate from',translation_config.speech_recognition_language)
    ```

1. Sie verwenden die **SpeechTranslationConfig**, um Sprache in Text zu übersetzen, aber Sie verwenden auch eine **SpeechConfig**, um Übersetzungen in gesprochener Sprache zu synthetisieren. Fügen Sie den folgenden Code unter dem Kommentar **Configure speech** (Sprache konfigurieren) hinzu:

    ```python
   # Configure speech
   speech_config = speech_sdk.SpeechConfig(speech_key, speech_region)
   print('Ready to use speech service in:', speech_config.region)
    ```

1. Speichern Sie Ihre Änderungen (*STRG+S*), lassen Sie den Code-Editor jedoch geöffnet.

## Ausführen der App

Bisher kann die App nichts anderes, als eine Verbindung zu Ihrer Azure KI Speech-Ressource herzustellen. Es ist jedoch nützlich, sie auszuführen und zu überprüfen, ob sie funktioniert, bevor Sie Sprachfunktionen hinzufügen.

1. Geben Sie in der Befehlszeile den folgenden Befehl ein, um die Übersetzer-App auszuführen:

    ```
   python translator.py
    ```

    Der Code sollte die Region der Sprachdienstressource anzeigen, die die Anwendung verwendet, eine Meldung, dass sie bereit ist, von „en-US“ zu übersetzen und Sie zur Eingabe einer Zielsprache aufzufordern. Bei einer erfolgreichen Ausführung wird angegeben, dass die App mit Ihrem Azure KI Speech-Dienst verbunden ist. Drücken Sie die EINGABETASTE, um das Programm zu beenden.

## Implementieren von Sprachübersetzung

Nachdem Sie eine **SpeechTranslationConfig** für den Azure KI Speech-Dienst erstellt haben, können Sie die Azure KI Speech-Übersetzungs-API verwenden, um Sprache zu erkennen und zu übersetzen.

1. Beachten Sie in der Codedatei, dass der Code die **Translate**-Funktion verwendet, um Spracheingaben zu übersetzen. Fügen Sie dann in der **Translate**-Funktion unter dem Kommentar **Translate speech** (Sprache übersetzen) den folgenden Code hinzu, um einen **TranslationRecognizer**-Client zu erstellen, der zum Erkennen und Übersetzen von Sprache aus einer Datei verwendet werden kann.

    ```python
   # Translate speech
   current_dir = os.getcwd()
   audioFile = current_dir + '/station.wav'
   audio_config_in = speech_sdk.AudioConfig(filename=audioFile)
   translator = speech_sdk.translation.TranslationRecognizer(translation_config, audio_config = audio_config_in)
   print("Getting speech from file...")
   result = translator.recognize_once_async().get()
   print('Translating "{}"'.format(result.text))
   translation = result.translations[targetLanguage]
   print(translation)
    ```

1. Speichern Sie die Änderungen (*CTRL+S*) und führen Sie das Programm erneut aus:

    ```
   python translator.py
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie einen gültigen Sprachcode ein (*fr*, *es* oder *hi*). Das Programm sollte Ihre Eingabedatei transkribieren und in die von Ihnen angegebene Sprache übersetzen (Französisch, Spanisch oder Hindi). Wiederholen Sie diesen Vorgang, wobei Sie jede von der Anwendung unterstützte Sprache ausprobieren.

    > **HINWEIS:** Die Übersetzung in Hindi wird aufgrund von Zeichencodierungsproblemen möglicherweise nicht immer ordnungsgemäß im Konsolenfenster angezeigt.

1. Wenn Sie fertig sind, drücken Sie die EINGABETASTE, um das Programm zu beenden.

> **HINWEIS**: Der Code in Ihrer Anwendung übersetzt die Eingabe in einem einzigen Aufruf in alle drei Sprachen. Es wird nur die Übersetzung für die spezifische Sprache angezeigt, aber Sie könnten jede der Übersetzungen abrufen, indem Sie den Zielsprachcode in der **translations**-Sammlung des Ergebnisses angeben.

## Synthetisieren der Übersetzung in Sprache

Bisher übersetzt Ihre Anwendung gesprochene Eingaben in Text, was ausreichend sein kann, wenn Sie während einer Reise eine Person um Hilfe bitten müssen. Es wäre jedoch besser, wenn die Übersetzung in einer passenden Stimme laut gesprochen würde.

> **Hinweis:** Aufgrund der Hardwareeinschränkungen der Cloudshell leiten wir die synthetisierte Sprachausgabe an eine Datei weiter.

1. Suchen Sie in der **Translate**-Funktion den Kommentar **Synthesize Translation** und fügen Sie den folgenden Code hinzu, um einen **SpeechSynthesizer**-Client zu verwenden, um die Übersetzung als Sprache zu synthetisieren und sie als WAV-Datei zu speichern:

    ```python
   # Synthesize translation
   output_file = "output.wav"
   voices = {
            "fr": "fr-FR-HenriNeural",
            "es": "es-ES-ElviraNeural",
            "hi": "hi-IN-MadhurNeural"
   }
   speech_config.speech_synthesis_voice_name = voices.get(targetLanguage)
   audio_config_out = speech_sdk.audio.AudioConfig(filename=output_file)
   speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config, audio_config_out)
   speak = speech_synthesizer.speak_text_async(translation).get()
   if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
   else:
        print("Spoken output saved in " + output_file)
    ```

1. Speichern Sie die Änderungen (*CTRL+S*) und führen Sie das Programm erneut aus:

    ```
   python translator.py
    ```

1. Überprüfen Sie die Ausgabe der Anwendung, die anzeigen sollte, dass die Übersetzung der Sprachausgabe in einer Datei gespeichert wurde. Wenn Sie fertig sind, drücken Sie die **EINGABETASTE**, um das Programm zu beenden.
1. Wenn Sie über einen Media Player verfügen, der WAV-Audiodateien wiedergeben kann, laden Sie die generierte Datei herunter, indem Sie den folgenden Befehl eingeben:

    ```
   download ./output.wav
    ```

    Der Downloadbefehl erstellt unten rechts im Browser einen Popuplink, den Sie auswählen können, um die Datei herunterzuladen und zu öffnen.

> **HINWEIS**
> *In diesem Beispiel haben Sie eine **SpeechTranslationConfig** zum Übersetzen von Sprache in Text und dann eine **SpeechConfig** zum Synthetisieren von Übersetzung zu Sprache verwendet. Sie können die Übersetzung tatsächlich direkt mithilfe der **SpeechTranslationConfig** synthetisieren. Dies funktioniert jedoch nur bei der Übersetzung in eine einzelne Sprache und führt zu einem Audiodatenstrom, der in der Regel als Datei gespeichert wird.*

## Bereinigen von Ressourcen

Wenn Sie mit der Erkundung des Azure KI Speech-Diensts fertig sind, können Sie die in dieser Übung erstellten Ressourcen löschen. Gehen Sie dazu wie folgt vor:

1. Schließen Sie den Azure Cloud Shell-Bereich
1. Navigieren Sie im Azure-Portal zu der Azure KI Speech-Ressource, die Sie in diesem Lab erstellt haben.
1. Wählen Sie auf der Ressourcenseite **Löschen** aus, und folgen Sie den Anweisungen zum Löschen der Ressource.

## Was geschieht, wenn Sie über ein Mikrofon und einen Lautsprecher verfügen?

In dieser Übung haben Sie Audiodateien für die Spracheingabe und -ausgabe verwendet. Sehen wir uns an, wie der Code geändert werden kann, um Audiohardware zu verwenden.

### Verwenden der Sprachübersetzung mit einem Mikrofon

1. Wenn Sie über ein Mikrofon verfügen, können Sie den folgenden Code verwenden, um gesprochene Eingaben für die Sprachübersetzung zu erfassen:

    ```python
   # Translate speech
   audio_config_in = speech_sdk.AudioConfig(use_default_microphone=True)
   translator = speech_sdk.translation.TranslationRecognizer(translation_config, audio_config = audio_config_in)
   print("Speak now...")
   result = translator.recognize_once_async().get()
   print('Translating "{}"'.format(result.text))
   translation = result.translations[targetLanguage]
   print(translation)
    ```

> **Hinweis**: Das Standardmikrofon des Systems ist die Standardaudioeingabe, sodass Sie auch einfach die AudioConfig ganz weglassen können!

### Verwenden der Sprachsynthese mit einem Lautsprecher

1. Wenn Sie über einen Lautsprecher verfügen, können Sie den folgenden Code verwenden, um Sprache zu synthetisieren.
    
    ```python
   # Synthesize translation
   voices = {
            "fr": "fr-FR-HenriNeural",
            "es": "es-ES-ElviraNeural",
            "hi": "hi-IN-MadhurNeural"
   }
   speech_config.speech_synthesis_voice_name = voices.get(targetLanguage)
   audio_config_out = speech_sdk.audio.AudioConfig(use_default_speaker=True)
   speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config, audio_config_out)
   speak = speech_synthesizer.speak_text_async(translation).get()
   if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

> **Hinweis**: Der Standardlautsprecher des Systems ist die Standardaudioausgabe, sodass Sie auch einfach die AudioConfig ganz weglassen können!

## Weitere Informationen

Weitere Informationen zur Verwendung der Azure KI Speech-Übersetzungs-API finden Sie in der [Dokumentation zur Sprachübersetzung](https://learn.microsoft.com/azure/ai-services/speech-service/speech-translation).
