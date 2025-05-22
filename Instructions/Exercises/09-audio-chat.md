---
lab:
  title: Entwicklung einer audiofähigen Chat-App
  description: 'Erfahren Sie, wie Sie mit Azure AI Foundry eine generative KI-App erstellen können, die Audioeingaben unterstützt.'
---

# Entwicklung einer audiofähigen Chat-App

In dieser Übung verwenden Sie das generative KI-Modell *Phi-4-multimodal-instruct*, um Antworten auf Aufforderungen zu erzeugen, die Audiodateien enthalten. Sie entwickeln eine App, die KI-Unterstützung für ein Herstellerunternehmen bietet, indem Sie Azure AI Foundry und den Azure KI-Modellinferenz-Dienst verwenden, um Sprachnachrichten der Kundschaft zusammenzufassen.

Diese Übung dauert ca. **30** Minuten.

## Erstellen eines Azure KI Foundry-Projekts

Beginnen wir mit dem Erstellen eines Azure AI Foundry-Projekts.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartbereiche, die beim ersten Anmelden geöffnet werden, und verwenden Sie bei Bedarf das **Azure AI Foundry-Logo** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht:

    ![Screenshot des Azure KI Foundry-Portals.](../media/ai-foundry-home.png)

1. Wählen Sie auf der Startseite **+ Projekt erstellen**.
1. Geben Sie im Assistenten **Projekt erstellen** einen gültigen Namen für Ihr Projekt ein und wählen Sie, falls ein vorhandener Hub vorgeschlagen wird, die Option, einen neuen zu erstellen. Überprüfen Sie dann die Azure-Ressourcen, die automatisch erstellt werden, um Ihren Hub und Ihr Projekt zu unterstützen.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Hubname**: *Geben Sie einen gültigen Namen für Ihren Hub an*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Region:** Wählen Sie eine der folgenden Regionen aus\*:
        - East US
        - USA (Ost) 2
        - USA Nord Mitte
        - USA Süd Mitte
        - Schweden, Mitte
        - USA (Westen)
        - USA, Westen 3
    - A**zure KI Services oder Azure OpenAI verbinden**: *Wählen Sie Neuen KI-Dienst erstellen aus*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Zum Zeitpunkt des Schreibens steht das Microsoft Modell *Phi-4-multimodal-instruct*, das wir in dieser Übung verwenden werden, in diesen Regionen zur Verfügung. Sie können die neueste regionale Verfügbarkeit für bestimmte Modelle in der [Azure AI Foundry-Dokumentation](https://learn.microsoft.com/azure/ai-foundry/how-to/deploy-models-serverless-availability#region-availability) überprüfen. Wenn später in der Übung eine regionale Kontingentgrenze erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen.

1. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
1. Sobald Ihr Projekt erstellt wurde, schließen Sie alle angezeigten Tipps und überprüfen Sie die Projektseite im Azure AI Foundry-Portal, die in etwa wie in der folgenden Abbildung aussehen sollte:

    ![Screenshot eines Azure KI-Projekts im Azure AI Foundry-Portal.](../media/ai-foundry-project.png)

## Bereitstellen eines multimodalen Modells

Jetzt sind Sie bereit, ein multimodales Modell bereitzustellen, das audio-basierte Eingaben unterstützen kann. Es gibt mehrere Modelle, aus denen Sie wählen können, einschließlich des OpenAI *gpt-4o*-Modells. In dieser Übung verwenden wir ein *Phi-4-Multimodal-Instruct*-Modell.

1. Verwenden Sie in der Symbolleiste oben rechts auf Ihrer Azure AI Foundry-Projektseite das Symbol **Funktionen anzeigen** (**&#9215;**) , um das Feature **Modelle zum Azure KI-Modellinferenz-Service bereitstellen** zu aktivieren. Mit diesem Feature wird sichergestellt, dass Ihre Modellbereitstellung für den Azure KI-Rückschlussdienst verfügbar ist, den Sie im Anwendungscode verwenden.
1. Wählen Sie im linken Fensterbereich für Ihr Projekt im Abschnitt **Meine Assets** die Seite **Modelle + Endpunkte**.
1. Wählen Sie auf der Seite **Modelle + Endpunkte** auf der Registerkarte **Modellbereitstellungen** im Menü **+ Modell bereitstellen** die Option **Basismodell bereitstellen**.
1. Suchen Sie in der Liste das Modell **Phi-4-multimodal-instruct**, wählen Sie es aus und bestätigen Sie es.
1. Stimmen Sie der Lizenzvereinbarung zu, wenn Sie dazu aufgefordert werden, und stellen Sie das Modell mit den folgenden Einstellungen bereit, indem Sie **Anpassen** in den Bereitstellungsdetails wählen:
    - **Bereitstellungsname:***Ein gültiger Name für die Modellimplementierung*
    - **Bereitstellungstyp**: Globaler Standard
    - **Bereitstellungsdetails**: *Verwenden der Standardeinstellungen.*
1. Warten Sie, bis der Bereitstellungsstatus **Abgeschlossen** ist.

## Das Erstellen einer Clientanwendung

Nachdem Sie das Modell bereitgestellt haben, können Sie die Bereitstellung in einer Clientanwendung verwenden.

> **Tipp**: Sie können Ihre Lösung mit Python oder Microsoft C# entwickeln. Folgen Sie den Anweisungen im entsprechenden Abschnitt für Ihre ausgewählte Sprache.

### Vorbereiten der Anwendungskonfiguration

1. Wechseln Sie im Azure AI Foundry-Portal zur **Übersichtsseite** Ihres Projekts.
1. Beachten Sie im Bereich **Projektdetails** die **Projektverbindungszeichenfolge**. Sie verwenden diese Verbindungszeichenfolge, um eine Verbindung mit Ihrem Projekt in einer Clientanwendung herzustellen.
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
    git clone https://github.com/MicrosoftLearning/mslearn-ai-language mslearn-ai-audio
    ```

    > **Tipp**: Wenn Sie Befehle in die Cloudshell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers einnehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Navigieren Sie nach dem Klonen des Repositorys zu dem Ordner, der die Codedateien der Anwendung enthält:  

    **Python**

    ```
    cd mslearn-ai-audio/Labfiles/09-audio-chat/Python
    ```

    **C#**

    ```
    cd mslearn-ai-audio/Labfiles/09-audio-chat/C-sharp
    ```

1. Geben Sie im Befehlszeilenbereich von Cloud Shell den folgenden Befehl ein, um die Bibliotheken zu installieren, die Sie verwenden möchten:

    **Python**

    ```
    python -m venv labenv
    ./labenv/bin/Activate.ps1
    pip install -r requirements.txt azure-identity azure-ai-projects azure-ai-inference
    ```

    **C#**

    ```
    dotnet add package Azure.Identity
    dotnet add package Azure.AI.Inference --version 1.0.0-beta.3
    dotnet add package Azure.AI.Projects --version 1.0.0-beta.3
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

    Die Datei sollte in einem Code-Editor geöffnet werden.

1. Ersetzen Sie in der Datei den Platzhalter **your_project_connection_string** durch die Verbindungszeichenfolge für Ihr Projekt (kopiert von der Seite **Übersicht** des Projekts im Azure KI Foundry-Portal) und den Platzhalter **your_model_deployment** durch den Namen, den Sie Ihrer Bereitstellung des GPT-4-Modells zugewiesen haben.

1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie im Code-Editor den Befehl **CTRL+S** oder **Rechtsklick > Speichern**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **CTRL+Q** oder **Rechtsklick > Beenden**, um den Code-Editor zu schließen, während die Befehlszeile der Cloud-Shell geöffnet bleibt.

### Schreiben von Code, um sich mit Ihrem Projekt zu verbinden und einen Chat-Client für Ihr Modell zu erhalten

> **Tipp**: Achten Sie beim Hinzufügen von Code auf den richtigen Einzug.

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Codedatei zu bearbeiten:

    **Python**

    ```
    code audio-chat.py
    ```

    **C#**

    ```
    code Program.cs
    ```

1. Beachten Sie in der Codedatei die vorhandenen Anweisungen, die am Anfang der Datei hinzugefügt wurden, um die erforderlichen SDK-Namespaces zu importieren. Suchen Sie dann den Kommentar **Verweise hinzufügen** und fügen Sie den folgenden Code hinzu, um auf die Namespaces in den zuvor installierten Bibliotheken zu verweisen:

    **Python**

    ```python
    # Add references
    from dotenv import load_dotenv
    from azure.identity import DefaultAzureCredential
    from azure.ai.projects import AIProjectClient
    from azure.ai.inference.models import (
        SystemMessage,
        UserMessage,
        TextContentItem,
    )
    ```

    **C#**

    ```csharp
    // Add references
    using Azure.Identity;
    using Azure.AI.Projects;
    using Azure.AI.Inference;
    ```

1. Beachten Sie, dass der Code in der **Hauptfunktion** unter dem Kommentar **Get configuration settings** die Zeichenfolge für die Projektverbindung und die Werte für den Namen der Modellbereitstellung lädt, die Sie in der Konfigurationsdatei definiert haben.

1. Finden Sie den Kommentar **Initialisieren des Projektclients** und fügen Sie den folgenden Code hinzu, um eine Verbindung mit Ihrem Azure AI Foundry-Projekt herzustellen. Verwenden Sie dazu die Azure-Anmeldeinformationen, mit denen Sie derzeit angemeldet sind:

    **Python**

    ```python
    # Initialize the project client
    project_client = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```csharp
    // Initialize the project client
    var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

1. Fügen Sie unter dem Kommentar **Einen Chat-Client abrufen** den folgenden Code ein, um ein Client-Objekt zum Chatten mit Ihrem Modell zu erstellen:

    **Python**

    ```python
    # Get a chat client
    chat_client = project_client.inference.get_chat_completions_client(model=model_deployment)
    ```

    **C#**

    ```csharp
    // Get a chat client
    ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```
    

### Schreiben Sie Code, um eine URL-basierte audio-basierte Eingabeaufforderung zu übermitteln

1. Fügen Sie im Code-Editor für die Datei **audio-chat.py** im Abschnitt loop unter dem Kommentar **Eine Antwort auf eine Audioeingabe erhalten** den folgenden Code ein, um eine Eingabeaufforderung mit folgendem Ton zu übermitteln:

    <video controls src="../media/avocados.mp4" title="Eine Anforderung für Avocados" width="150"></video>

    **Python**

    ```python
    # Get a response to audio input
    file_path = "https://github.com/MicrosoftLearning/mslearn-ai-language/raw/refs/heads/main/Labfiles/09-audio-chat/data/avocados.mp3"
    response = chat_client.complete(
        messages=[
            SystemMessage(system_message),
            UserMessage(
                [
                    TextContentItem(text=prompt),
                    {
                        "type": "audio_url",
                        "audio_url": {"url": file_path}
                    }
                ]
            )
        ]
    )
    print(response.choices[0].message.content)
    ```

    **C#**

    ```csharp
    // Get a response to audio input
    string audioUrl = "https://github.com/MicrosoftLearning/mslearn-ai-language/raw/refs/heads/main/Labfiles/09-audio-chat/data/avocados.mp3";
    var requestOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(system_message),
            new ChatRequestUserMessage(
                new ChatMessageTextContentItem(prompt),
                new ChatMessageAudioContentItem(new Uri(audioUrl))),
        },
        Model = model_deployment
    };
    var response = chat.Complete(requestOptions);
    Console.WriteLine(response.Value.Content);
    ```

1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern. Sie können den Code-Editor (**STRG+Q**) auch schließen, wenn Sie möchten.

1. Geben Sie im Befehlszeilenbereich von Cloud Shell unterhalb des Code-Editors den folgenden Befehl ein, um die App auszuführen:

    **Python**

    ```
    python audio-chat.py
    ```

    **C#**

    ```
    dotnet run
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie den folgenden Prompt ein: 

    ```
    Can you summarize this customer's voice message?
    ```

1. Überprüfen Sie die Antwort.

### Verwenden einer anderen Audiodatei

1. Suchen Sie im Code-Editor für Ihren App-Code den Code, den Sie zuvor unter dem Kommentar **Eine Antwort auf eine Audioeingabe erhalten** hinzugefügt haben. Ändern Sie dann den Code wie folgt, um eine andere Audiodatei auszuwählen:

    <video controls src="../media/fresas.mp4" title="Eine Anforderung für Erdbeeren" width="150"></video>

    **Python**

    ```python
    # Get a response to audio input
    file_path = "https://github.com/MicrosoftLearning/mslearn-ai-language/raw/refs/heads/main/Labfiles/09-audio-chat/data/fresas.mp3"
    response = chat_client.complete(
        messages=[
            SystemMessage(system_message),
            UserMessage(
                [
                    TextContentItem(text=prompt),
                    {
                        "type": "audio_url",
                        "audio_url": {"url": file_path}
                    }
                ]
            )
        ]
    )
    print(response.choices[0].message.content)
    ```

    **C#**

    ```csharp
    // Get a response to audio input
    string audioUrl = "https://github.com/MicrosoftLearning/mslearn-ai-language/raw/refs/heads/main/Labfiles/09-audio-chat/data/fresas.mp3";
    var requestOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(system_message),
            new ChatRequestUserMessage(
                new ChatMessageTextContentItem(prompt),
                new ChatMessageAudioContentItem(new Uri(audioUrl))),
        },
        Model = model_deployment
    };
    var response = chat.Complete(requestOptions);
    Console.WriteLine(response.Value.Content);
    ```

1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern. Sie können den Code-Editor (**STRG+Q**) auch schließen, wenn Sie möchten.

1. Geben Sie im Befehlszeilenbereich von Cloud Shell unterhalb des Code-Editors den folgenden Befehl ein, um die App auszuführen:

    **Python**

    ```
    python audio-chat.py
    ```

    **C#**

    ```
    dotnet run
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
