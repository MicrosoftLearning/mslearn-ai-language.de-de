---
lab:
  title: Erstellen einer „Fragen und Antworten“-Lösung
  module: Module 6 - Create question answering solutions with Azure AI Language
---

# Erstellen einer „Fragen und Antworten“-Lösung

Eines der häufigsten Unterhaltungsszenarien ist die Bereitstellung von Unterstützung über eine Wissensdatenbank mit häufig gestellten Fragen (FAQs). Viele Organisationen veröffentlichen FAQs als Dokumente oder Webseiten. Dies funktioniert gut bei einer kleinen Menge von Frage-Antwort-Paaren, aber die Suche in umfangreichen Dokumenten kann schwierig und zeitaufwändig sein.

**Azure KI Language** enthält eine *Fragen-und-Antworten*-Funktion, die es Ihnen ermöglicht, eine Wissensdatenbank mit Frage-Antwort-Paaren zu erstellen. Diese Datenbank kann mithilfe von Eingaben in natürlicher Sprache abgefragt werden und wird am häufigsten als Ressource verwendet, mit der ein Bot Antworten auf Fragen suchen kann, die von Benutzer*innen gesendet wurden.

## Bereitstellen einer *Azure KI Language*-Ressource

Wenn Sie noch nicht über eine solche Ressource in Ihrem Abonnement verfügen, müssen Sie eine **Azure KI Language**-Dienstressource bereitstellen. Um eine Wissensdatenbank für die Beantwortung von Fragen zu erstellen und zu hosten, müssen Sie die Funktion **Fragen und Antworten** aktivieren.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Wählen Sie **Ressource erstellen**.
1. Suchen Sie im Suchfeld nach dem **Sprachdienst**. Wählen Sie dann in den Ergebnissen unter **Sprachdienst** die Option **Erstellen** aus.
1. Wählen Sie den Block **Benutzerdefinierte Fragen und Antworten** aus. Wählen Sie dann **Mit dem Erstellen Ihrer Ressource fortfahren** aus. Sie müssen die folgenden Einstellungen eingeben:

    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine*.
    - **Region**: *Wählen Sie einen beliebigen verfügbaren Speicherort aus.*
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Wählen Sie **F0** (*Free*) oder **S** (*Standard*) aus, falls F nicht verfügbar ist.
    - **Azure-Suchregion**: *Wählen Sie einen Speicherort in derselben globalen Region wie Ihre Language-Ressource aus.*
    - **Azure Search-Tarif**: „Free (F)“ (*Wenn dieser Tarif nicht verfügbar ist, wählen Sie „Basic (B)“* ) aus.
    - **Hinweis zur verantwortlichen Verwendung von KI**: *Zustimmen*

1. Wählen Sie **Erstellen + Überprüfen** und dann **Erstellen** aus.

    > **HINWEIS** „Benutzerdefinierte Fragen und Antworten“ verwendet Azure Search zum Indizieren und Abfragen der Wissensdatenbank mit Fragen und Antworten.

1. Warten Sie, bis die Bereitstellung abgeschlossen ist, und wechseln Sie dann zur bereitgestellten Ressource.
1. Sehen Sie sich die Seite **Schlüssel und Endpunkt** an. Sie benötigen die Informationen auf dieser Seite später in der Übung.

## Erstellen eines „Fragen und Antworten“-Projekt

Wenn Sie eine Wissensdatenbank für Fragen und Antworten in Ihrer Azure KI Language-Ressource erstellen möchten, können Sie das Language Studio-Portal zum Erstellen eines „Fragen und Antworten“-Projekts verwenden. In diesem Fall erstellen Sie eine Wissensdatenbank mit Fragen und Antworten zu [Microsoft Learn](https://docs.microsoft.com/learn).

1. Navigieren Sie auf einer neuen Browserregisterkarte zum Language Studio-Portal unter [https://language.cognitive.azure.com/](https://language.cognitive.azure.com/), und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Wenn Sie zur Auswahl einer Language-Ressource aufgefordert werden, wählen Sie die folgenden Einstellungen aus:
    - **Azure-Verzeichnis**: Das Azure-Verzeichnis mit Ihrem Abonnement.
    - **Azure-Abonnement**: Ihr Azure-Abonnement.
    - **Ressourcentyp**: Language
    - **Ressourcenname**: Die Azure KI Language-Ressource, die Sie zuvor erstellt haben.

    Sollten Sie <u>nicht</u> zur Auswahl einer Sprachressource aufgefordert, kann dies daran liegen, dass Ihr Abonnement mehrere Sprachressourcen enthält. Gehen Sie in diesem Fall folgendermaßen vor:

    1. Klicken Sie auf der Leiste oben auf die Schaltfläche **Einstellungen (&#9881;)**.
    2. Gehen Sie auf der Seite **Einstellungen** zur Registerkarte **Ressourcen**.
    3. Wählen Sie die soeben erstellte Sprachressource aus, und klicken Sie auf **Switch resource** (Ressource wechseln).
    4. Klicken Sie oben auf der Seite auf **Language Studio**, um zur Startseite von Language Studio zurückzukehren.

1. Klicken Sie oben im Portal im Menü **Neu erstellen** auf **Benutzerdefinierte Fragebeantwortung**.
1. Wählen Sie im Assistenten ***Projekt erstellen** auf der Seite **Spracheinstellung wählen** die Option **Sprache für alle Projekte festlegen** aus und wählen Sie **Englisch** als Sprache aus. Wählen Sie **Weiter** aus.
1. Geben Sie auf der Seite **Grundlegende Informationen eingeben** die folgenden Informationen ein:
    - **Name** `LearnFAQ`
    - **Beschreibung:** `FAQ for Microsoft Learn`
    - **Standardantwort, wenn keine Antwort zurückgegeben wird**: `Sorry, I don't understand the question`
1. Wählen Sie **Weiter** aus.
1. Wählen Sie auf der Seite **Überprüfen und fertigstellen** die Option **Projekt erstellen** aus.

## Hinzufügen von Quellen zur Wissensdatenbank

Sie können eine Wissensdatenbank von Grund auf neu erstellen, aber es ist üblich, zuerst Fragen und Antworten von einer vorhandenen FAQ-Seite oder aus einem vorhandenen Dokument zu importieren. In diesem Fall importieren Sie Daten von einer vorhandenen FAQ-Webseite für Microsoft Learn, und Sie importieren auch einige vordefinierte Fragen und Antworten für „Smalltalk“, um gängige Konversationsszenarios zu unterstützen.

1. Wählen Sie auf der Seite **Manage sources** (Quellen verwalten) für Ihr „Fragen und Antworten“-Projekt in der Liste **&#9547; Add source** (Quelle hinzufügen) die Option **URLs** aus. Wählen Sie dann im Dialogfeld **URLs hinzufügen** die Option **+ URL hinzufügen** aus, und geben Sie den folgenden Namen und die URL ein, bevor Sie **Alle hinzufügen** auswählen, um sie der Wissensdatenbank hinzuzufügen:
    - **Name**: `Learn FAQ Page`
    - **URL**: `https://docs.microsoft.com/en-us/learn/support/faq`
1. Wählen Sie auf der Seite **Manage sources** (Quellen verwalten) für Ihr „Fragen und Antworten“-Projekt in der Liste **&#9547; Add source** (Quelle hinzufügen) die Option **Chitchat** (Smalltalk) aus. Wählen Sie im Dialogfeld **Smalltalk hinzufügen** die Option **Freundlich** aus, und wählen Sie anschließend **Smalltalk hinzufügen** aus.

## Bearbeiten der Wissensdatenbank

Ihre Wissensdatenbank wurde mit Frage-Antwort-Paaren aus den häufig gestellten Fragen zu Microsoft Learn aufgefüllt, die durch eine Reihe von *Smalltalk*-Frage-Antwort-Paaren ergänzt wurden. Sie können die Wissensdatenbank erweitern, indem Sie zusätzliche Frage-Antwort-Paare hinzufügen.

1. Wählen Sie in Ihrem **LearnFAQ**-Projekt in Language Studio die Seite **Wissensdatenbank bearbeiten** aus, um die vorhandenen Frage-Antwort-Paare zu sehen (wenn einige Tipps angezeigt werden, lesen Sie diese, und wählen Sie **Verstanden** aus, um sie zu schließen, oder wählen Sie **Alle überspringen** aus).
1. Wählen Sie in der Wissensdatenbank auf der Registerkarte **Frage-Antwort-Paare** die Option **＋** aus, und erstellen Sie ein neues Frage-Antwort-Paar mit den folgenden Einstellungen:
    - **Quelle**: `https://docs.microsoft.com/en-us/learn/support/faq`
    - **Frage**: `What are Microsoft credentials?`
    - **Antwort**: `Microsoft credentials enable you to validate and prove your skills with Microsoft technologies.`
1. Wählen Sie **Fertig** aus.
1. Erweitern Sie auf der Seite der Frage **Was sind Microsoft-Zertifizierungen?** den Eintrag **Alternative Fragen**. Fügen Sie dann die alternative Frage `How can I demonstrate my Microsoft technology skills?` hinzu.

    In einigen Fällen ist es sinnvoll, Benutzer*innen eine Nachverfolgung der Antwort zu ermöglichen, indem eine *mehrteilige* Unterhaltung erstellt wird. Mit deren Hilfe können Benutzer*innen die Frage iterativ verfeinern, um die benötigte Antwort zu erhalten.

1. Erweitern Sie unter der Antwort, die Sie für die Zertifizierungsfrage eingegeben haben, den Eintrag **Folgeäußerungen**, und fügen Sie die folgende Folgeäußerung hinzu:
    - **Text, der dem Benutzer in der Eingabeaufforderung angezeigt wird**: `Learn more about credentials`.
    - Wählen Sie die Registerkarte **Verknüpfung zu neuem Paar erstellen** aus, und geben Sie den folgenden Text ein: `You can learn more about credentials on the [Microsoft credentials page](https://docs.microsoft.com/learn/credentials/).`
    - Wählen Sie **Nur im kontextbezogenen Flow anzeigen** aus. Mit dieser Option wird sichergestellt, dass die Antwort nur jeweils im Kontext einer Folgefrage aus der ursprünglichen Zertifizierungsfrage zurückgegeben wird.
1. Wählen Sie **Äußerung hinzufügen** aus.

## Trainieren und Testen der Wissensdatenbank

Nachdem Sie nun über eine Wissensdatenbank verfügen, können Sie sie in Language Studio testen.

1. Speichern Sie die Änderungen an Ihrer Wissensdatenbank, indem Sie auf der linken Seite auf der Registerkarte **Frage-Antwort-Paare** die Schaltfläche **Speichern** auswählen.
1. Nachdem die Änderungen gespeichert wurden, wählen Sie die Schaltfläche **Test** aus, um den Testbereich zu öffnen.
1. Deaktivieren Sie im Testbereich oben die Option **Kurzantwort einschließen** (falls sie nicht bereits deaktiviert wurde). Geben Sie dann unten die Nachricht `Hello` ein. Daraufhin sollte eine geeignete Antwort zurückgegeben werden.
1. Geben Sie im Testbereich unten die Nachricht `What is Microsoft Learn?` ein. Es sollte eine entsprechende Antwort aus den häufig gestellten Fragen zurückgegeben werden.
1. Geben Sie die Nachricht `Thanks!` ein. Eine entsprechende Smalltalkantwort sollte zurückgegeben werden.
1. Geben Sie die Nachricht `Tell me about Microsoft credentials` ein. Die von Ihnen erstellte Antwort sollte zusammen mit einem Link zu einer Folgeäußerung zurückgegeben werden.
1. Klicken Sie auf den Link zur Folgeäußerung **Learn more about credentials**. Die Folgeantwort mit einem Link zur Zertifizierungsseite sollte zurückgegeben werden.
1. Wenn Sie mit dem Testen der Wissensdatenbank fertig sind, schließen Sie den Testbereich.

## Bereitstellen der Wissensdatenbank

Die Wissensdatenbank stellt einen Back-End-Dienst bereit, den Clientanwendungen zum Beantworten von Fragen nutzen können. Jetzt können Sie Ihre Wissensdatenbank veröffentlichen und über einen Client auf deren REST-Schnittstelle zugreifen.

1. Navigieren Sie im Projekt **LearnFAQ** in Language Studio zur Seite **Wissensdatenbank bereitstellen**.
1. Wählen Sie oben auf der Seite **Bereitstellen** aus. Wählen Sie dann **Bereitstellen** aus, um zu bestätigen, dass Sie die Wissensdatenbank bereitstellen möchten.
1. Wenn die Bereitstellung abgeschlossen ist, wählen Sie **Vorhersage-URL abrufen** aus, um den REST-Endpunkt für Ihre Wissensdatenbank anzuzeigen. Beachten Sie, dass die Beispielanforderung Parameter für Folgendes enthält:
    - **projectName**: Der Name Ihres Projekts (sollte *LearnFAQ* sein)
    - **deploymentName**: Der Name Ihrer Bereitstellung (sollte *production* sein)
1. Schließen Sie das Dialogfeld „Vorhersage-URL“.

## Vorbereiten der Entwicklung einer App in Visual Studio Code

Sie entwickeln Ihre „Fragen und Antworten“-App mit Visual Studio Code. Die Codedateien für Ihre App wurden in einem GitHub-Repository bereitgestellt.

> **Tipp**: Wenn Sie das Repository **mslearn-ai-language** bereits geklont haben, öffnen Sie es in Visual Studio Code. Führen Sie andernfalls die folgenden Schritte aus, um es in Ihre Entwicklungsumgebung zu klonen.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/mslearn-ai-language` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.

    > **Hinweis:** Wenn Visual Studio Code eine Popupnachricht anzeigt, in der Sie aufgefordert werden, dem geöffneten Code zu vertrauen, klicken Sie auf die Option **Ja, ich vertraue den Autoren** im Popupfenster.

4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus.

## Konfigurieren der Anwendung

Es werden Anwendungen für C# und Python bereitgestellt sowie eine Beispieltextdatei, mit der Sie die Zusammenfassung testen können. Beide Apps verfügen über die gleiche Funktionalität. Zuerst schließen Sie einige wichtige Teile der Anwendung ab, um die Verwendung Ihrer Azure KI Language-Ressource zu aktivieren.

1. Wechseln Sie in Visual Studio Code im Bereich **Explorer** zum Ordner **Labfiles/02-qna**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner**CSharp** oder **Python** und den darin enthaltenen Ordner **qna-app**. Jeder Ordner enthält die sprachspezifischen Dateien für eine App, in die Sie Azure KI Language-Funktionen für Fragen und Antworten integrieren möchten.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **qna-app**, der Ihre Codedateien enthält, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das „Fragen und Antworten“-SDK-Paket für Azure KI Language, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#:**

    ```
    dotnet add package Azure.AI.Language.QuestionAnswering
    ```

    **Python**:

    ```
    pip install azure-ai-language-questionanswering
    ```

3. Öffnen Sie im Bereich **Explorer** im Ordner **qna-app** die Konfigurationsdatei für Ihre bevorzugte Sprache.

    - **C#**: appsettings.json
    - **Python**: .env
    
4. Aktualisieren Sie die Konfigurationswerte so, dass sie den **Endpunkt** und einen **Schlüssel** aus der Azure KI Language-Ressource enthalten, die Sie erstellt haben (verfügbar auf der Seite **Schlüssel und Endpunkt** für Ihre Azure KI Language-Ressource im Azure-Portal). Der Projektname und der Bereitstellungsname für Ihre bereitgestellten Wissensdatenbank sollten sich ebenfalls in dieser Datei befinden.
5. Speichern Sie die Konfigurationsdatei.

## Hinzufügen von Code zur Anwendung

Jetzt können Sie den Code hinzufügen, der zum Importieren der erforderlichen SDK-Bibliotheken erforderlich ist, eine authentifizierte Verbindung mit Ihrem bereitgestellten Projekt herstellen und Fragen senden.

1. Beachten Sie, dass der Ordner **qna-app** eine Codedatei für die Clientanwendung enthält:

    - **C#**: Program.cs
    - **Python**: qna-app.py

    Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das Textanalyse-SDK verwenden zu können:

    **C#**: Programs.cs

    ```csharp
    // import namespaces
    using Azure;
    using Azure.AI.Language.QuestionAnswering;
    ```

    **Python**: qna-app.py

    ```python
    # import namespaces
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.language.questionanswering import QuestionAnsweringClient
    ```

1. Beachten Sie, dass in der Funktion **Main** der Code zum Laden des Schlüssels und des Endpunkts des Azure KI Language-Diensts aus der Konfigurationsdatei bereits bereitgestellt wurde. Suchen Sie dann den Kommentar **Create client using endpoint and key** (Client mit Endpunkt und Schlüssel erstellen), und fügen Sie den folgenden Code hinzu, um einen Client für die Textanalyse-API zu erstellen:

    **C#**: Programs.cs

    ```C#
    // Create client using endpoint and key
    AzureKeyCredential credentials = new AzureKeyCredential(aiSvcKey);
    Uri endpoint = new Uri(aiSvcEndpoint);
    QuestionAnsweringClient aiClient = new QuestionAnsweringClient(endpoint, credentials);
    ```

    **Python**: qna-app.py

    ```Python
    # Create client using endpoint and key
    credential = AzureKeyCredential(ai_key)
    ai_client = QuestionAnsweringClient(endpoint=ai_endpoint, credential=credential)
    ```

1. Suchen Sie in der Funktion **Main** den Kommentar **Submit a question and display the answer**, und fügen Sie den folgenden Code hinzu, um wiederholt Fragen aus der Befehlszeile zu lesen, sie an den Dienst zu übermitteln und Details der Antworten anzuzeigen:

    **C#**: Programs.cs

    ```C#
    // Submit a question and display the answer
    string user_question = "";
    while (true)
        {
            Console.Write("Question: ");
            user_question = Console.ReadLine();
            if (user_question.ToLower() == "quit")
                break;
            QuestionAnsweringProject project = new QuestionAnsweringProject(projectName, deploymentName);
            Response<AnswersResult> response = aiClient.GetAnswers(user_question, project);
            foreach (KnowledgeBaseAnswer answer in response.Value.Answers)
            {
                Console.WriteLine(answer.Answer);
                Console.WriteLine($"Confidence: {answer.Confidence:P2}");
                Console.WriteLine($"Source: {answer.Source}");
                Console.WriteLine();
            }
        }
    ```

    **Python**: qna-app.py

    ```Python
    # Submit a question and display the answer
    user_question = ''
    while True:
        user_question = input('\nQuestion:\n')
        if user_question.lower() == "quit":                
            break
        response = ai_client.get_answers(question=user_question,
                                        project_name=ai_project_name,
                                        deployment_name=ai_deployment_name)
        for candidate in response.answers:
            print(candidate.answer)
            print("Confidence: {}".format(candidate.confidence))
            print("Source: {}".format(candidate.source))
    ```

1. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **qna-app** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    - **C#** : `dotnet run`
    - **Python**: `python qna-app.py`

    > **Tipp**: Sie können das Symbol **Fenstergröße maximieren** (**^**) in der Terminalsymbolleiste verwenden, um mehr vom Konsolentext anzuzeigen.

1. Wenn Sie dazu aufgefordert werden, geben Sie eine Frage ein, die an Ihr „Fragen und Antworten“-Projekt übermittelt werden soll. Beispiel: `What is a learning path?`.
1. Überprüfen Sie die Antwort, die zurückgegeben wird.
1. Stellen Sie weitere Fragen. Wenn Sie fertig sind, geben Sie die Zeichenfolge `quit` ein.

## Bereinigen von Ressourcen

Wenn Sie die Erkundung des Dienstes Azure KI Language abgeschlossen haben, können Sie die in dieser Übung erstellten Ressourcen löschen. Gehen Sie dazu wie folgt vor:

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Navigieren Sie zur Azure KI Language-Ressource, die Sie in dieser Übung erstellt haben.
3. Wählen Sie auf der Seite „Ressource“ die Option **Delete** (Löschen) aus, und folgen Sie den Anweisungen zum Löschen der Ressource.

## Weitere Informationen

Weitere Informationen zu Fragen und Antworten in Azure KI Language finden Sie in der [Dokumentation zu Azure KI Language](https://learn.microsoft.com/azure/ai-services/language-service/question-answering/overview).
