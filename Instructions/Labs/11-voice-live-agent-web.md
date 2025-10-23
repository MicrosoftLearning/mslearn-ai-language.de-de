---
lab:
  title: Entwickeln eines Azure KI Voice Live-VoIP-Agents
  description: 'Erfahren Sie, wie Sie eine Web-App erstellen, um Echtzeit-Sprachinteraktionen mit einem Azure KI Voice Live-Agent zu ermöglichen.'
---

# Entwickeln eines Azure KI Voice Live-VoIP-Agents

In dieser Übung absolvieren Sie eine Flask-basierte Python-Web-App, die Echtzeit-Sprachinteraktionen mit einem Agent ermöglicht. Sie fügen den Code hinzu, um die Sitzung zu initialisieren und Sitzungsereignisse zu behandeln. Sie verwenden ein Bereitstellungsskript, das: das KI-Modell bereitstellt; erstellt ein Bild der App in der Azure Container Registry (ACR) mithilfe von ACR-Aufgaben; und erstellt dann eine Azure App Service-Instanz, die das Image abruft. Zum Testen der App benötigen Sie ein Audiogerät mit Mikrofon- und Lautsprecherfunktionen.

Während diese Übung auf Python basiert, können Sie ähnliche Anwendungen für andere sprachspezifische SDKs entwickeln. einschließlich:

- [Azure VoiceLive-Clientbibliothek für .NET](https://www.nuget.org/packages/Azure.AI.VoiceLive/)

In dieser Übung ausgeführte Aufgaben:

* Herunterladen der Basisdateien für die App
* Hinzufügen von Code zum Abschließen der Web-App
* Überprüfen der gesamten Codebasis
* Aktualisieren und Ausführen des Bereitstellungsskripts
* Anzeigen und Testen der Anwendung

Diese Übung dauert ca. **30** Minuten.

## Starten der Azure Cloud Shell, und Herunterladen der Dateien

In diesem Abschnitt der Übung laden Sie die eine gezippte Datei herunter, die die Basisdateien für die App enthält.

1. Navigieren Sie in Ihrem Browser zum Azure-Portal [https://portal.azure.com](https://portal.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an, wenn Sie dazu aufgefordert werden.

1. Verwenden Sie die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud Shell im Azure-Portal zu erstellen, und wählen Sie eine ***Bash***-Umgebung aus. Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Bereich am unteren Rand des Azure-Portals.

    > **Hinweis**: Wenn Sie zuvor eine Cloud Shell erstellt haben, die eine *PowerShell*-Umgebung verwendet, wechseln Sie diese zu ***Bash***.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

1. Führen Sie den folgenden Befehl in der **Bash-Shell** aus, um die Übungsdateien herunterzuladen und zu entpacken. Der zweite Befehl ändert sich auch in das Verzeichnis für die Übungsdateien.

    ```bash
    wget https://github.com/MicrosoftLearning/mslearn-ai-language/raw/refs/heads/main/downloads/python/voice-live-web.zip
    ```

    ```
    unzip voice-live-web.zip && cd voice-live-web
    ```

## Hinzufügen von Code zum Abschließen der Web-App

Nachdem die Übungsdateien heruntergeladen wurden, besteht der nächste Schritt darin, Code zum Abschließen der Anwendung hinzuzufügen. Die folgenden Schritte werden in Cloud Shell ausgeführt. 

>**Tipp:** Ändern Sie die Größe von Cloud Shell, um weitere Informationen und mehr Code anzuzeigen, indem Sie den oberen Rahmen ziehen. Sie können außerdem die Schaltflächen zum Minimieren und Maximieren verwenden, um zwischen Cloud Shell und der Hauptschnittstelle des Portals zu wechseln.

Führen Sie den folgenden Befehl aus, um in das *src*-Verzeichnis zu wechseln, bevor Sie mit der Übung fortfahren.

```bash
cd src
```

### Hinzufügen von Code zum Implementieren des Sprach-Live-Assistenten

In diesem Abschnitt fügen Sie Code zum Implementieren des Sprach-Live-Assistenten hinzu. Die **\_\_init\_\_**-Methode initialisiert den Sprachassistenten, indem die Azure VoiceLive-Verbindungsparameter (Endpunkt, Anmeldeinformationen, Modell, VoIP und Systemanweisungen) gespeichert und Laufzeitzustandsvariablen eingerichtet werden, um den Verbindungslebenszyklus zu verwalten und Benutzerunterbrechungen während Unterhaltungen zu behandeln. Die **Start**-Methode importiert die erforderlichen Azure VoiceLive SDK-Komponenten, mit denen die WebSocket-Verbindung hergestellt und die Echtzeit-Sprachsitzung konfiguriert wird.

1. Führen Sie den folgenden Befehl aus, um die *flask_app.py* Datei zur Bearbeitung zu öffnen.

    ```bash
    code flask_app.py
    ```

1. Suchen Sie im Code nach der **IMPLEMENTIERUNG DES #BEGIN VOICE LIVE ASSISTANT - CODE MIT KOMMENTARKOMMENTAR** AUSRICHTEN. Kopieren Sie den folgenden Code, und geben Sie ihn direkt unterhalb des Kommentars ein. Achten Sie darauf, den Einzug zu überprüfen.

    ```python
    def __init__(
        self,
        endpoint: str,
        credential,
        model: str,
        voice: str,
        instructions: str,
        state_callback=None,
    ):
        # Store Azure Voice Live connection and configuration parameters
        self.endpoint = endpoint
        self.credential = credential
        self.model = model
        self.voice = voice
        self.instructions = instructions
        
        # Initialize runtime state - connection established in start()
        self.connection = None
        self._response_cancelled = False  # Used to handle user interruptions
        self._stopping = False  # Signals graceful shutdown
        self.state_callback = state_callback or (lambda *_: None)

    async def start(self):
        # Import Voice Live SDK components needed for establishing connection and configuring session
        from azure.ai.voicelive.aio import connect  # type: ignore
        from azure.ai.voicelive.models import (
            RequestSession,
            ServerVad,
            AzureStandardVoice,
            Modality,
            InputAudioFormat,
            OutputAudioFormat,
        )  # type: ignore
    ```

1. Geben Sie **strg+s ein**, um Ihre Änderungen zu speichern, und lassen Sie den Editor für den nächsten Abschnitt geöffnet.

### Hinzufügen von Code zum Implementieren des Sprach-Live-Assistenten

In diesem Abschnitt fügen Sie Code zum Konfigurieren der VoIP-Livesitzung hinzu. Dies gibt die Modalitäten an (nur Audio wird von der API nicht unterstützt), die Systemanweisungen, die das Verhalten des Assistenten, die Azure TTS-Stimme für Antworten, das Audioformat für Eingabe- und Ausgabedatenströme sowie die serverseitige Voice-Aktivitätserkennung (VAD) definieren, die angibt, wie das Modell erkennt, wann Benutzer beginnen und sprechen.

1. Suchen Sie nach der **#BEGIN CONFIGURE VOICE LIVE SESSION - RICHTEN SIE CODE MIT KOMMENTAR**-Kommentar im Code. Kopieren Sie den folgenden Code, und geben Sie ihn direkt unterhalb des Kommentars ein. Achten Sie darauf, den Einzug zu überprüfen.

    ```python
    # Configure VoiceLive session with audio/text modalities and voice activity detection
    session_config = RequestSession(
        modalities=[Modality.TEXT, Modality.AUDIO],
        instructions=self.instructions,
        voice=voice_cfg,
        input_audio_format=InputAudioFormat.PCM16,
        output_audio_format=OutputAudioFormat.PCM16,
        turn_detection=ServerVad(threshold=0.5, prefix_padding_ms=300, silence_duration_ms=500),
    )
    await conn.session.update(session=session_config)
    ```

1. Geben Sie **strg+s ein**, um Ihre Änderungen zu speichern, und lassen Sie den Editor für den nächsten Abschnitt geöffnet.

### Hinzufügen von Code zum Behandeln von Sitzungsereignissen

In diesem Abschnitt fügen Sie Code hinzu, um Ereignishandler für die VoIP-Livesitzung hinzuzufügen. Die Ereignishandler reagieren während des Unterhaltungslebenszyklus auf wichtige VoiceLive-Sitzungsereignisse: **_handle_session_updated**-Signale, wenn die Sitzung für die Benutzereingabe bereit ist, **_handle_speech_started** erkennt, wann der Benutzer spricht und die Unterbrechungslogik implementiert, indem alle laufenden Audiowiedergabe des Assistenten beendet und in Bearbeitungsantworten abgebrochen werden, um den natürlichen Unterhaltungsfluss zu ermöglichen, und **_handle_speech_stopped** behandelt, wenn der Benutzer mit dem Sprechen fertig ist und der Assistent mit der Verarbeitung der Eingabe beginnt.

1. Suchen Sie im Code nach den **#BEGIN HANDLE SESSION EVENTS - ALIGN CODE WITH COMMENT**-Kommentar im Code. Kopieren Sie den folgenden Code, und geben Sie ihn direkt unterhalb des Kommentars ein, achten Sie darauf, den Einzug zu überprüfen.

    ```python
    async def _handle_event(self, event, conn, verbose=False):
        """Handle Voice Live events with clear separation by event type."""
        # Import event types for processing different Voice Live server events
        from azure.ai.voicelive.models import ServerEventType
        
        event_type = event.type
        if verbose:
            _broadcast({"type": "log", "level": "debug", "event_type": str(event_type)})
        
        # Route Voice Live server events to appropriate handlers
        if event_type == ServerEventType.SESSION_UPDATED:
            await self._handle_session_updated()
        elif event_type == ServerEventType.INPUT_AUDIO_BUFFER_SPEECH_STARTED:
            await self._handle_speech_started(conn)
        elif event_type == ServerEventType.INPUT_AUDIO_BUFFER_SPEECH_STOPPED:
            await self._handle_speech_stopped()
        elif event_type == ServerEventType.RESPONSE_AUDIO_DELTA:
            await self._handle_audio_delta(event)
        elif event_type == ServerEventType.RESPONSE_AUDIO_DONE:
            await self._handle_audio_done()
        elif event_type == ServerEventType.RESPONSE_DONE:
            # Reset cancellation flag but don't change state - _handle_audio_done already did
            self._response_cancelled = False
        elif event_type == ServerEventType.ERROR:
            await self._handle_error(event)

    async def _handle_session_updated(self):
        """Session is ready for conversation."""
        self.state_callback("ready", "Session ready. You can start speaking now.")

    async def _handle_speech_started(self, conn):
        """User started speaking - handle interruption if needed."""
        self.state_callback("listening", "Listening… speak now")
        
        try:
            # Stop any ongoing audio playback on the client side
            _broadcast({"type": "control", "action": "stop_playback"})
            
            # If assistant is currently speaking or processing, cancel the response to allow interruption
            current_state = assistant_state.get("state")
            if current_state in {"assistant_speaking", "processing"}:
                self._response_cancelled = True
                await conn.response.cancel()
                _broadcast({"type": "log", "level": "debug", 
                          "msg": f"Interrupted assistant during {current_state}"})
            else:
                _broadcast({"type": "log", "level": "debug", 
                          "msg": f"User speaking during {current_state} - no cancellation needed"})
        except Exception as e:
            _broadcast({"type": "log", "level": "debug", 
                      "msg": f"Exception in speech handler: {e}"})

    async def _handle_speech_stopped(self):
        """User stopped speaking - processing input."""
        self.state_callback("processing", "Processing your input…")

    async def _handle_audio_delta(self, event):
        """Stream assistant audio to clients."""
        if self._response_cancelled:
            return  # Skip cancelled responses
            
        # Update state when assistant starts speaking
        if assistant_state.get("state") != "assistant_speaking":
            self.state_callback("assistant_speaking", "Assistant speaking…")
        
        # Extract and broadcast Voice Live audio delta as base64 to WebSocket clients
        audio_data = getattr(event, "delta", None)
        if audio_data:
            audio_b64 = base64.b64encode(audio_data).decode("utf-8")
            _broadcast({"type": "audio", "audio": audio_b64})

    async def _handle_audio_done(self):
        """Assistant finished speaking."""
        self._response_cancelled = False
        self.state_callback("ready", "Assistant finished. You can speak again.")

    async def _handle_error(self, event):
        """Handle Voice Live errors."""
        error = getattr(event, "error", None)
        message = getattr(error, "message", "Unknown error") if error else "Unknown error"
        self.state_callback("error", f"Error: {message}")

    def request_stop(self):
        self._stopping = True
    ```

1. Geben Sie **strg+s ein**, um Ihre Änderungen zu speichern, und lassen Sie den Editor für den nächsten Abschnitt geöffnet.

### Überprüfen des Codes in der App

Bisher haben Sie der App Code hinzugefügt, um den Agent zu implementieren und Agentereignisse zu behandeln. Nehmen Sie sich ein paar Minuten Zeit, um den vollständigen Code und die Kommentare zu überprüfen, um ein besseres Verständnis darüber zu erhalten, wie die App Dlientstatus und -Vorgänge verarbeitet.

1. Wenn Sie fertig sind, geben Sie **strg+q** ein, um den Editor zu verlassen. 

## Aktualisieren und Ausführen des Bereitstellungsskripts

In diesem Abschnitt nehmen Sie eine kleine Änderung am **azdeploy.sh** Bereitstellungsskript vor und führen dann die Bereitstellung aus. 

### Aktualisieren des Bereitstellungsskripts

Es gibt nur zwei Werte, die Sie oben im **azdeploy.sh** Bereitstellungsskript ändern sollten. 

* Der **rg**-Wert gibt die Ressourcengruppe an, die die Bereitstellung enthalten soll. Sie können den Standardwert akzeptieren oder Ihren eigenen Wert eingeben, wenn Sie für eine bestimmte Ressourcengruppe bereitstellen müssen.

* Der **Standort**-Wert legt die Region für die Bereitstellung fest. Das in der Übung verwendete *gpt-4o*-Modell kann in anderen Regionen bereitgestellt werden, es kann jedoch Beschränkungen in einer bestimmten Region geben. Wenn die Bereitstellung in Ihrer ausgewählten Region fehlschlägt, probieren Sie **"eastus2** " oder **Schwedenzentral** aus. 

    ```
    rg="rg-voicelive" # Replace with your resource group
    location="eastus2" # Or a location near you
    ```

1. Führen Sie die folgenden Befehle in der Cloud Shell aus, um mit der Bearbeitung des Bereitstellungsskripts zu beginnen.

    ```bash
    cd ~/01-voice-live-web
    ```
    
    ```bash
    code azdeploy.sh
    ```

1. Aktualisieren Sie die Werte für **rg** und **Speicherort**, um Ihre Anforderungen zu erfüllen, und geben Sie dann **strg+s** ein, um Ihre Änderungen zu speichern, und **strg+q**, um den Editor zu beenden.

### Ausführen des Bereitstellungsskripts

Das Bereitstellungsskript stellt das KI-Modell bereit und erstellt die erforderlichen Ressourcen in Azure, um eine containerisierte App in App Service auszuführen.

1. Führen Sie den folgenden Befehl in der Cloud Shell aus, um mit der Bereitstellung der Azure-Ressourcen und der Anwendung zu beginnen.

    ```bash
    bash azdeploy.sh
    ```

1. Wählen Sie **Option 1** für die Erstbereitstellung aus.

    Die Bereitstellung sollte in 5 bis 10 Minuten abgeschlossen sein. Während der Bereitstellung werden Sie möglicherweise zur Eingabe der folgenden Informationen/Aktionen aufgefordert:
    
    * Wenn Sie aufgefordert werden, sich bei Azure zu authentifizieren, folgen Sie den Anweisungen, die Ihnen angezeigt werden.
    * Wenn Sie aufgefordert werden, ein Abonnement auszuwählen, verwenden Sie die Pfeiltasten, um Ihr Abonnement hervorzuheben, und drücken Sie **Eingeben**. 
    * Es werden wahrscheinlich einige Warnungen während der Bereitstellung angezeigt, und diese können ignoriert werden.
    * Wenn die Bereitstellung während der KI-Modellbereitstellung fehlschlägt, ändern Sie die Region im Bereitstellungsskript, und versuchen Sie es erneut. 
    * Regionen in Azure können zu Zeiten beschäftigt sein und die Anzeigedauer der Bereitstellungen unterbrechen. Wenn die Bereitstellung nach der Modellbereitstellung fehlschlägt, führen Sie das Bereitstellungsskript erneut aus.

## Anzeigen und Testen der App

Nach Abschluss der Bereitstellung wird eine „Bereitstellung abgeschlossen!" Die Nachricht wird zusammen mit einem Link zur Web-App in der Shell angezeigt. Sie können diesen Link auswählen oder zur App Service-Ressource navigieren und die App von dort aus starten. Es kann einige Minuten dauern, bis die Anwendung geladen wird. 

1. Wählen Sie die Schaltfläche **Sitzung starten** aus, um eine Verbindung mit dem Modell herzustellen.
1. Sie werden aufgefordert, der Anwendung Zugriff auf Audiogeräte zu gewähren.
1. Beginnen Sie mit dem Modell zu sprechen, wenn Sie von der App aufgefordert werden, mit dem Sprechen zu beginnen.

Problembehandlung:

* Wenn die App fehlende Umgebungsvariablen meldet, starten Sie die Anwendung in App Service neu.
* Wenn in dem in der Anwendung angezeigten Protokoll übermäßige *Audioabschnittsmeldungen* angezeigt werden, wählen Sie **Sitzung beenden** aus, und starten Sie die Sitzung erneut. 
* Wenn die App überhaupt nicht funktioniert, überprüfen Sie den gesamten Code und den ordnungsgemäßen Einzug. Wenn Sie Änderungen vornehmen müssen, führen Sie die Bereitstellung erneut aus, und wählen Sie **Option 2** aus, um nur das Image zu aktualisieren.

## Bereinigen von Ressourcen

Führen Sie den folgenden Befehl in der Cloud Shell aus, um alle für diese Übung bereitgestellten Ressourcen zu entfernen. Sie werden aufgefordert, den Ressourcenlöschvorgang zu bestätigen.

```
azd down --purge
```