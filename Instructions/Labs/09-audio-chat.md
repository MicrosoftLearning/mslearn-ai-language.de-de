---
lab:
  title: Entwicklung einer audiofähigen Chat-App
  description: 'Erfahren Sie, wie Sie mit Azure AI Foundry eine generative KI-App erstellen können, die Audioeingaben unterstützt.'
---

# Entwicklung einer audiofähigen Chat-App

In dieser Übung verwenden Sie das generative KI-Modell *Phi-4-multimodal-instruct*, um Antworten auf Aufforderungen zu erzeugen, die Audiodateien enthalten. Sie entwickeln eine App, die KI-Unterstützung für ein Herstellerunternehmen bietet, indem Sie Azure AI Foundry und das Python-OpenAI-SDK verwenden, um Sprachnachrichten der Kundschaft zusammenzufassen.

Obwohl diese Übung auf Python basiert, können Sie ähnliche Anwendungen mit mehreren sprachspezifischen SDKs entwickeln, einschließlich:

- [Azure KI-Projekte für Python](https://pypi.org/project/azure-ai-projects)
- [OpenAI-Bibliothek für Python](https://pypi.org/project/openai/)
- [Azure KI-Projekte für Microsoft .NET](https://www.nuget.org/packages/Azure.AI.Projects)
- [Azure OpenAI-Clientbibliothek für Microsoft .NET](https://www.nuget.org/packages/Azure.AI.OpenAI)
- [Azure KI-Projekte für JavaScript](https://www.npmjs.com/package/@azure/ai-projects)
- [Azure OpenAI-Bibliothek für TypeScript](https://www.npmjs.com/package/@azure/openai)

Diese Übung dauert ca. **30** Minuten.

## Erstellen eines Azure KI Foundry-Projekts

Beginnen wir mit dem Erstellen eines Azure KI Foundry-Projekts.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartbereiche, die beim ersten Anmelden geöffnet werden, und verwenden Sie bei Bedarf das **Azure AI Foundry-Logo** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht:

    ![Screenshot des Azure KI Foundry-Portals.](../media/ai-foundry-home.png)

1. Suchen Sie auf der Startseite im Abschnitt **Modelle und Funktionen erkunden** nach dem Modell `Phi-4-multimodal-instruct`, das wir in unserem Projekt verwenden werden.
1. Wählen Sie in den Suchergebnissen das Modell **Phi-4-multimodal-instruct** aus, um dessen Details anzuzeigen, und wählen Sie dann oben auf der Seite für das Modell die Option **Dieses Modell verwenden** aus.
1. Wenn Sie zur Erstellung eines Projekts aufgefordert werden, geben Sie einen gültigen Namen für Ihr Projekt ein und entfalten Sie die Option **Erweiterte Optionen**.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Azure KI Foundry-Ressource**: *Ein gültiger Name für Ihre Azure KI Foundry-Ressource*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Region**: Wählen Sie die **empfohlene AI Foundry-Instanz aus***\*

    > \* Einige Azure KI-Ressourcen unterliegen regionalen Modellkontingenten. Sollte im weiteren Verlauf der Übung eine Kontingentgrenze überschritten werden, müssen Sie möglicherweise eine weitere Ressource in einer anderen Region anlegen. Sie können die neueste regionale Verfügbarkeit für bestimmte Modelle in der [Azure AI Foundry-Dokumentation](https://learn.microsoft.com/azure/ai-foundry/how-to/deploy-models-serverless-availability#region-availability) überprüfen

1. Klicken Sie auf **Erstellen**, und warten Sie, bis das Projekt erstellt wird.

    Es kann einige Minuten dauern, bis der Vorgang abgeschlossen ist.

1. Wählen Sie **Zustimmen und fortfahren**, um den Modellbedingungen zuzustimmen, und wählen Sie dann **Bereitstellen** aus, um die Phi-Modellimplementierung abzuschließen.

1. Wenn Ihr Projekt erstellt wird, werden die Modelldetails automatisch geöffnet. Notieren Sie sich den Namen der Modellimplementierung, was **Phi-4-multimodal-instruct** sein sollte

1. Wählen Sie im Navigationsbereich auf der linken Seite **Übersicht**, um die Hauptseite Ihres Projekts anzuzeigen, die wie folgt aussieht:

    > **Hinweis**: Wenn die Fehlermeldung *Unzureichende Berechtigungen** angezeigt wird, klicken Sie auf die Schaltfläche „**Beheben**“, um das Problem zu beheben.

    ![Screenshot einer Projektübersichtsseite von Azure AI Foundry.](../media/ai-foundry-project.png)

## Das Erstellen einer Clientanwendung

Nachdem Sie ein Modell bereitgestellt haben, können Sie die Azure AI Foundry- und die Azure KI-Modellinferenz-SDKs verwenden, um eine Anwendung zu entwickeln, die mit dem Modell chattet.

> **Tipp**: Sie können Ihre Lösung mit Python oder Microsoft C# entwickeln. Folgen Sie den Anweisungen im entsprechenden Abschnitt für Ihre ausgewählte Sprache.

### Vorbereiten der Anwendungskonfiguration

1. Wechseln Sie im Azure AI Foundry-Portal zur **Übersichtsseite** Ihres Projekts.
1. Notieren Sie sich im Bereich **Projektdetails** den **Azure AI Foundry-Projektendpunkt**. Sie verwenden diesen Endpunkt, um in einer Clientanwendung eine Verbindung zu Ihrem Projekt herzustellen.
1. Öffnen Sie eine neue Browserregisterkarte (wobei das Azure AI Foundry-Portal auf der vorhandenen Registerkarte geöffnet bleibt). Wechseln Sie dann in der neuen Registerkarte zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit Ihren Azure-Anmeldedaten an, wenn Sie dazu aufgefordert werden.

    Schließen Sie alle Willkommensbenachrichtigungen, um die Startseite des Azure-Portals anzuzeigen.

1. Verwenden Sie die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud-Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung ohne Speicher in Ihrem Abonnement.

    Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Fenster am unteren Rand des Azure-Portals. Sie können die Größe dieses Bereichs ändern oder maximieren, um die Arbeit zu vereinfachen.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    **<font color="red">Stellen Sie sicher, dass Sie zur klassischen Version der Cloud Shell gewechselt haben, bevor Sie fortfahren.</font>**

1. Geben Sie im Cloud Shell-Bereich die folgenden Befehle ein, um das GitHub-Repository mit den Codedateien für diese Übung zu klonen (geben Sie den Befehl ein oder kopieren Sie ihn in die Zwischenablage und klicken Sie dann mit der rechten Maustaste in die Befehlszeile, um ihn als reinen Text einzufügen):

    ```
   rm -r mslearn-ai-audio -f
   git clone https://github.com/MicrosoftLearning/mslearn-ai-language
    ```

    > **Tipp**: Wenn Sie Befehle in die Cloudshell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers einnehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Navigieren Sie nach dem Klonen des Repositorys zu dem Ordner, der die Codedateien der Anwendung enthält:  

    ```
   cd mslearn-ai-language/Labfiles/09-audio-chat/Python
    ````

1. Geben Sie im Befehlszeilenbereich von Cloud Shell den folgenden Befehl ein, um die Bibliotheken zu installieren, die Sie verwenden möchten:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-identity azure-ai-projects openai
    ```

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Konfigurationsdatei zu bearbeiten:

    ```
   code .env
    ```

    Die Datei sollte in einem Code-Editor geöffnet werden.

1. Ersetzen Sie in der Codedatei den Platzhalter **your_project_endpoint** durch den Endpunkt für Ihr Projekt (von der Seite **Übersicht** des Azure AI Foundry-Portals kopiert) und den Platzhalter **your_model_deployment** durch den Namen, den Sie Ihrer Phi-4-multimodal-instruct-Modellimplementierung zugewiesen haben.

1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie im Code-Editor den Befehl **CTRL+S** oder **Rechtsklick > Speichern**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **CTRL+Q** oder **Rechtsklick > Beenden**, um den Code-Editor zu schließen, während die Befehlszeile der Cloud-Shell geöffnet bleibt.

### Schreiben von Code, um sich mit Ihrem Projekt zu verbinden und einen Chat-Client für Ihr Modell zu erhalten

> **Tipp**: Achten Sie beim Hinzufügen von Code auf den richtigen Einzug.

1. Geben Sie zum Bearbeiten der Codedatei den folgenden Befehl ein:

    ```
   code audio-chat.py
    ```

1. Beachten Sie in der Codedatei die vorhandenen Anweisungen, die am Anfang der Datei hinzugefügt wurden, um die erforderlichen SDK-Namespaces zu importieren. Suchen Sie dann den Kommentar **Verweise hinzufügen** und fügen Sie den folgenden Code hinzu, um auf die Namespaces in den zuvor installierten Bibliotheken zu verweisen:

    ```python
   # Add references
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
    ```

1. Beachten Sie, dass der Code in der **Hauptfunktion** unter dem Kommentar **Get configuration settings** die Zeichenfolge für die Projektverbindung und die Werte für den Namen der Modellbereitstellung lädt, die Sie in der Konfigurationsdatei definiert haben.

1. Suchen Sie den Kommentar **Initialize the project client** und fügen Sie den folgenden Code hinzu, um eine Verbindung mit Ihrem Azure AI Foundry-Projekt herzustellen:

    > **Tipp**: Achten Sie darauf, die richtige Einzugsebene für den Code beizubehalten.

    ```python
   # Initialize the project client
   project_client = AIProjectClient(            
       credential=DefaultAzureCredential(
           exclude_environment_credential=True,
           exclude_managed_identity_credential=True
       ),
       endpoint=project_endpoint,
   )
    ```

1. Fügen Sie unter dem Kommentar **Einen Chat-Client abrufen** den folgenden Code ein, um ein Client-Objekt zum Chatten mit Ihrem Modell zu erstellen:

    ```python
   # Get a chat client
   openai_client = project_client.get_openai_client(api_version="2024-10-21")
    ```

### Schreiben von Code zur Übermittlung eines audiobasierten Prompts

Vor dem Übermitteln des Prompts müssen wir die Audiodatei für die Anforderung codieren. Anschließend können wir die Audiodaten mit einem Prompt für das LLM an die Benutzernachricht anfügen. Beachten Sie, dass der Code einen Loop (Schleife) enthält, damit Benutzende einen Prompt eingeben können, bis sie „quit“ eingeben. 

1. Geben Sie unter dem Kommentar **Encode the audio file** den folgenden Code ein, um die folgende Audiodatei vorzubereiten:

    <video controls src="https://github.com/MicrosoftLearning/mslearn-ai-language/raw/refs/heads/main/Instructions/media/avocados.mp4" title="Eine Anforderung für Avocados" width="150"></video>

    ```python
   # Encode the audio file
   file_path = "https://github.com/MicrosoftLearning/mslearn-ai-language/raw/refs/heads/main/Labfiles/09-audio-chat/data/avocados.mp3"
   response = requests.get(file_path)
   response.raise_for_status()
   audio_data = base64.b64encode(response.content).decode('utf-8')
    ```

1. Fügen Sie unter dem Kommentar **Get a response to audio input** den folgenden Code hinzu, um einen Prompt zu übermitteln:

    ```python
   # Get a response to audio input
   response = openai_client.chat.completions.create(
       model=model_deployment,
       messages=[
           {"role": "system", "content": system_message},
           { "role": "user",
               "content": [
               { 
                   "type": "text",
                   "text": prompt
               },
               {
                   "type": "input_audio",
                   "input_audio": {
                       "data": audio_data,
                       "format": "mp3"
                   }
               }
           ] }
       ]
   )
   print(response.choices[0].message.content)
    ```

1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern. Sie können den Code-Editor (**STRG+Q**) auch schließen, wenn Sie möchten.

### Bei Azure anmelden und die App ausführen

1. Geben Sie im Befehlszeilenbereich der Cloud-Shell den folgenden Befehl ein, um sich bei Azure anzumelden.

    ```
   az login
    ```

    **<font color="red">Sie müssen sich bei Azure anmelden - auch wenn die Cloud-Shell-Sitzung bereits authentifiziert ist.</font>**

    > **Hinweis**: In den meisten Szenarien ist nur die Verwendung von *az login* ausreichend. Wenn Sie jedoch Abonnements in mehreren Mandqanten haben, müssen Sie möglicherweise den Mandanten mit dem Parameter *--tenant* angeben. Weitere Informationen finden Sie unter [Interaktive Anmeldung bei Azure mit der Azure CLI](https://learn.microsoft.com/cli/azure/authenticate-azure-cli-interactively).
    
1. Wenn Sie dazu aufgefordert werden, folgen Sie den Anweisungen, um die Anmeldeseite in einer neuen Registerkarte zu öffnen, und geben Sie den angegebenen Authentifizierungscode und Ihre Azure-Anmeldeinformationen ein. Schließen Sie dann den Anmeldevorgang in der Befehlszeile ab, und wählen Sie das Abonnement aus, das Ihren Azure AI Foundry Hub enthält, wenn Sie dazu aufgefordert werden.

1. Geben Sie im Befehlszeilenbereich von Cloud Shell den folgenden Befehl ein, um die App auszuführen:

    ```
   python audio-chat.py
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie den folgenden Prompt ein: 

    ```
   Can you summarize this customer's voice message?
    ```

1. Überprüfen Sie die Antwort.

### Verwenden einer anderen Audiodatei

1. Suchen Sie im Code-Editor für Ihren App-Code den Code, den Sie zuvor unter dem Kommentar **Encode the audio file** hinzugefügt haben. Ändern Sie dann die Dateipfad-URL wie folgt, um eine andere Audiodatei für die Anforderung zu verwenden (lassen Sie den vorhandenen Code nach dem Dateipfad stehen):

    ```python
   # Encode the audio file
   file_path = "https://github.com/MicrosoftLearning/mslearn-ai-language/raw/refs/heads/main/Labfiles/09-audio-chat/data/fresas.mp3"
    ```

    Die neue Datei klingt wie folgt:

    <video controls src="https://github.com/MicrosoftLearning/mslearn-ai-language/raw/refs/heads/main/Instructions/media/fresas.mp4" title="Eine Anforderung für Erdbeeren" width="150"></video>

 1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern. Sie können den Code-Editor (**STRG+Q**) auch schließen, wenn Sie möchten.

1. Geben Sie im Befehlszeilenbereich von Cloud Shell unterhalb des Code-Editors den folgenden Befehl ein, um die App auszuführen:

    ```
   python audio-chat.py
    ```

1. Geben Sie den folgenden Prompt ein, wenn Sie dazu aufgefordert werden: 
    
    ```
   Can you summarize this customer's voice message? Is it time-sensitive?
    ```

1. Überprüfen Sie die Antwort. Geben Sie dann `quit` zum Beenden des Programms ein.

    > **Hinweis**: In dieser einfachen App wurde keine Logik implementiert, um die aufgezeichneten Unterhaltungen beizubehalten. Daher behandelt das Modell jeden Prompt als neue Anforderung ohne Kontext des vorherigen Prompts.

1. Sie können die App weiterhin ausführen, verschiedene Prompttypen auswählen und unterschiedliche Prompts ausprobieren. Wenn Sie fertig sind, geben Sie `quit` ein, um das Programm zu beenden.

    Wenn Sie Zeit haben, können Sie den Code ändern, um eine andere Eingabeaufforderung und Ihre eigenen Audiodateien zu verwenden, die über das Internet zugänglich sind.

    > **Hinweis**: In dieser einfachen App wurde keine Logik implementiert, um die aufgezeichneten Unterhaltungen beizubehalten. Daher behandelt das Modell jeden Prompt als neue Anforderung ohne Kontext des vorherigen Prompts.

## Zusammenfassung

In dieser Übung haben Sie Azure AI Foundry und das Azure AI Inference SDK verwendet, um eine Client-Anwendung zu erstellen, die ein multimodales Modell verwendet, um Antworten auf Audio zu generieren.

## Bereinigen

Wenn Sie die Erkundung von Azure AI Foundry abgeschlossen haben, sollten Sie die Ressourcen, die Sie in dieser Übung erstellt haben, löschen, um unnötige Azure-Kosten zu vermeiden.

1. Kehren Sie zu der Browserregisterkarte zurück, die das Azure-Portal enthält (oder öffnen Sie das [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` erneut in einer neuen Browserregisterkarte) und sehen Sie sich den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.
