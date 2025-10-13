# Anforderungen

## Ausführen in Cloud Shell

* Azure-Abonnement mit OpenAI-Zugriff
* Wenn es in der Azure Cloud Shell ausgeführt wird, wählen Sie die Bash-Shell aus. Die Azure CLI und die Azure Developer CLI sind in der Cloud Shell enthalten.

## Lokales Ausführen

* Sie können die Web-App nach dem Ausführen des Bereitstellungsskripts lokal ausführen:
    * [Azure Developer CLI (azd)](https://learn.microsoft.com/en-us/azure/developer/azure-developer-cli/install-azd)
    * [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)
    * Azure-Abonnement mit OpenAI-Zugriff


## Umgebungsvariablen

Die  `.env` Datei wird vom Skript*azdeploy.sh* erstellt. Der KI-Modellendpunkt, der API-Schlüssel und der Modellname werden während der Bereitstellung der Ressourcen hinzugefügt.

## Azure-Ressourcenbereitstellung

Die bereitgestellten `azdeploy.sh` Ressourcen werden in Azure erstellt:

* Ändern Sie die beiden Variablen oben im Skript so, dass sie Ihren Anforderungen entsprechen, ändern Sie nichts anderes.
* Mit dem Skript wird Folgendes durchgeführt:
    * Stellen Sie das *gpt-4o*-Modell mit AZD bereit.
    * Erstellt den Azure-Containerregistrierungsdienst
    * Verwendet ACR-Aufgaben zum Erstellen und Bereitstellen des Dockerfile-Images auf ACR
    * Erstellt den App Service Plan
    * Erstellt die App Service Web App
    * Konfiguriert die Web-App für das Containerimage in ACR
    * Konfiguriert die Web App-Umgebungsvariablen
    * Das Skript wird den App Service-Endpunkt bereitstellen.

Das Skript bietet zwei Bereitstellungsoptionen: 1. Vollständige Bereitstellung; und 2. Erneutes Bereitstellen nur des Images. Option 2 ist nur für die Bereitstellung nach der Bereitstellung vorgesehen, wenn Sie mit Änderungen in der Anwendung experimentieren möchten. 

> Hinweis: Sie können das Skript in PowerShell oder Bash mit dem `bash azdeploy.sh` Befehl ausführen. Mit diesem Befehl können Sie auch das Skript in Bash ausführen, ohne es zu einer ausführbaren Datei machen zu müssen.

## Lokale Entwicklung

### Bereitstellen des KI-Modells für Azure

Sie können das Projekt lokal ausführen und nur das KI-Modell mit dem Folgen dieser Schritte:

1. **Initialisieren sie die Umgebung** (wählen Sie einen beschreibenden Namen aus):

   ```bash
   azd env new gpt-realtime-lab --confirm
   # or: azd env new your-name-gpt-experiment --confirm
   ```
   
   **Wichtig:** Dieser Name wird Teil Ihrer Azure-Ressourcennamen!  
   Die `--confirm` Kennzeichnung legt dies als Standardumgebung ohne Aufforderung fest.

1. **Legen Sie Ihre Ressourcengruppe**fest:

   ```bash
   azd env set AZURE_RESOURCE_GROUP "rg-your-name-gpt"
   ```

1. **Anmelden und Bereitstellen von KI-Ressourcen**:

   ```bash
   az login
   azd provision
   ```

    > **Wichtig:** NICHT ausführen `azd deploy` – die App ist in den AZD-Vorlagen nicht konfiguriert.

Wenn Sie das Modell nur mithilfe der `azd provision` Methode bereitgestellt haben, MÜSSEN Sie eine `.env` Datei im Stammverzeichnis des Verzeichnisses mit den folgenden Einträgen erstellen:

```
AZURE_VOICE_LIVE_ENDPOINT=""
AZURE_VOICE_LIVE_API_KEY=""
VOICE_LIVE_MODEL=""
VOICE_LIVE_VOICE="en-US-JennyNeural"
VOICE_LIVE_INSTRUCTIONS="You are a helpful AI assistant with a focus on world history. Respond naturally and conversationally. Keep your responses concise but engaging."
VOICE_LIVE_VERBOSE="" #Suppresses excessive logging to the terminal if running locally
```

Hinweise:

1. Der Endpunkt ist der Endpunkt für das Modell und sollte nur `https://<proj-name>.cognitiveservices.azure.com` enthalten.
1. Der API-Schlüssel ist der Schlüssel für das Modell.
1. Das Modell ist der Modellname, der während der Bereitstellung verwendet wird.
1. Sie können diese Werte aus dem KI Foundry-Portal abrufen.

### Lokales Ausführen des Projekts

Das Projekt wurde mithilfe von **UV**erstellt und verwaltet, aber es ist nicht erforderlich, das Projekt auszuführen. 

Wenn Sie**uv**installiert haben:

* Führen Sie `uv venv` aus, um die Umgebung zu erstellen
* Führen Sie `uv sync` aus, um Pakete hinzuzufügen
* Alias, der für Web-App erstellt wurde: `uv run web` um das `flask_app.py` Skript zu starten.
* requirements.txt Datei erstellt mit `uv pip compile pyproject.toml -o requirements.txt`

Wenn Sie **uv**nicht installiert haben:

* Umgebung erstellen: `python -m venv .venv`
* Aktivieren der Umgebung: `.\.venv\Scripts\Activate.ps1`
* Installieren von Abhängigkeiten: `pip install -r requirements.txt`
* Anwendung ausführen (aus Projektstamm): `python .\src\real_time_voice\flask_app.py`
