---
lab:
  title: Erstellen einer „Fragen und Antworten“-Lösung
  description: Verwenden Sie Azure KI Language zum Erstellen einer benutzerdefinierten „Fragen und Antworten“-Lösung.
---

# Erstellen einer „Fragen und Antworten“-Lösung

Eines der häufigsten Unterhaltungsszenarien ist die Bereitstellung von Unterstützung über eine Wissensdatenbank mit häufig gestellten Fragen (FAQs). Viele Organisationen veröffentlichen FAQs als Dokumente oder Webseiten. Dies funktioniert gut bei einer kleinen Menge von Frage-Antwort-Paaren, aber die Suche in umfangreichen Dokumenten kann schwierig und zeitaufwändig sein.

**Azure KI Language** enthält eine *Fragen-und-Antworten*-Funktion, die es Ihnen ermöglicht, eine Wissensdatenbank mit Frage-Antwort-Paaren zu erstellen. Diese Datenbank kann mithilfe von Eingaben in natürlicher Sprache abgefragt werden und wird am häufigsten als Ressource verwendet, mit der ein Bot Antworten auf Fragen suchen kann, die von Benutzer*innen gesendet wurden. In dieser Übung verwenden Sie das Azure KI Language-Python-SDK für die Textanalyse, um eine einfache Anwendung für Fragen und Antworten zu implementieren.

Obwohl diese Übung auf Python basiert, können Sie Anwendungen für Fragen und Antworten mit mehreren sprachspezifischen SDKs entwickeln, einschließlich:

- [Azure KI Language Service „Fragen und Antworten“-Clientbibliothek für Python](https://pypi.org/project/azure-ai-language-questionanswering/)
- [Azure KI Language Service „Fragen und Antworten“-Clientbibliothek für .NET](https://www.nuget.org/packages/Azure.AI.Language.QuestionAnswering)

Diese Übung dauert ca. **20** Minuten.

## Bereitstellen einer *Azure KI Language*-Ressource

Wenn Sie noch nicht über eine solche Ressource in Ihrem Abonnement verfügen, müssen Sie eine **Azure KI Language**-Dienstressource bereitstellen. Um eine Wissensdatenbank für die Beantwortung von Fragen zu erstellen und zu hosten, müssen Sie die Funktion **Fragen und Antworten** aktivieren.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Wählen Sie **Ressource erstellen** aus.
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
1. Sie können die Seite **Schlüssel und Endpunkte** im Abschnitt **Ressourcenverwaltungt** anzeigen. Sie benötigen die Informationen auf dieser Seite später in der Übung.

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

## Vorbereiten der Entwicklung einer App in Cloud Shell

Sie entwickeln Ihre App für Fragen und Antworten mit Cloud Shell im Azure-Portal. Die Codedateien für Ihre App wurden in einem GitHub-Repository bereitgestellt.

1. Verwenden Sie im Azure-Portal die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung aus. Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Bereich am unteren Rand des Azure-Portals.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    **<font color="red">Stellen Sie sicher, dass Sie zur klassischen Version der Cloud Shell gewechselt haben, bevor Sie fortfahren.</font>**

1. Geben Sie im PowerShell-Bereich die folgenden Befehle ein, um das GitHub-Repository für diese Übung zu klonen:

    ```
    rm -r mslearn-ai-language -f
    git clone https://github.com/microsoftlearning/mslearn-ai-language
    ```

    > **Tipp**: Wenn Sie Befehle in die Cloud Shell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers in Anspruch nehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Navigieren Sie nach dem Klonen des Repositorys zu dem Ordner, der die Codedateien der Anwendung enthält:  

    ```
    cd mslearn-ai-language/Labfiles/02-qna/Python/qna-app
    ```

## Konfigurieren der Anwendung

1. Führen Sie im Befehlszeilenbereich den folgenden Befehl aus, um die Codedateien im Ordner **qna-app** anzuzeigen:

    ```
   ls -a -l
    ```

    Die Dateien enthalten eine Konfigurationsdatei (**.env**) und eine Codedatei (**qna-app.py**).

1. Erstellen Sie eine virtuelle Python-Umgebung und installieren Sie das Azure KI Language-Frage-Antworten-SDK-Paket sowie andere erforderliche Pakete, indem Sie den folgenden Befehl ausführen:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-ai-language-questionanswering
    ```

1. Geben Sie den folgenden Befehl ein, um die Konfigurationsdatei zu bearbeiten:

    ```
    code .env
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Aktualisieren Sie die Konfigurationswerte so, dass sie den **Endpunkt** und einen **Schlüssel** aus der Azure Language-Ressource enthalten, die Sie erstellt haben (verfügbar auf der Seite **Schlüssel und Endpunkt** für Ihre Azure KI Language-Ressource im Azure-Portal). Der Projektname und der Bereitstellungsname für Ihre bereitgestellten Wissensdatenbank sollten sich ebenfalls in dieser Datei befinden.
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie im Code-Editor den Befehl **STRG+S** oder **Rechtsklick > Speichern**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q** oder **Rechtsklick > Beenden**, um den Code-Editor zu schließen, während die Cloud Shell-Befehlszeile geöffnet bleibt.

## Hinzufügen von Code zum Benutzen Ihrer Wissensdatenbank

1. Geben Sie den folgenden Befehl ein, um die Anwendungscodedatei zu bearbeiten:

    ```
    code qna-app.py
    ```

1. Überprüfen Sie den vorhandenen Code. Sie fügen Code hinzu, um mit Ihrer Wissensdatenbank zu arbeiten.

    > **Tipp**: Achten Sie beim Hinzufügen von Code zur Codedatei darauf, den richtigen Einzug beizubehalten.

1. Suchen Sie in der Codedatei den Kommentar **Import namespaces**. Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie benötigen, um das Frage-Antwort-SDK verwenden zu können:

    ```python
   # import namespaces
   from azure.core.credentials import AzureKeyCredential
   from azure.ai.language.questionanswering import QuestionAnsweringClient
    ```

1. Beachten Sie, dass in der **Main**-Funktion bereits Code zum Laden des Azure KI Language-Dienstendpunkts und -schlüssels aus der Konfigurationsdatei bereitgestellt wurde. Suchen Sie dann den Kommentar **Create client using endpoint and key** und fügen Sie den folgenden Code hinzu, um einen Client für Fragen und Antworten zu erstellen:

    ```Python
   # Create client using endpoint and key
   credential = AzureKeyCredential(ai_key)
   ai_client = QuestionAnsweringClient(endpoint=ai_endpoint, credential=credential)
    ```

1. Suchen Sie in der Codedatei den Kommentar **Submit a question and display the answer** und fügen Sie den folgenden Code hinzu, um wiederholt Fragen aus der Befehlszeile zu lesen, sie an den Dienst zu übermitteln und Details der Antworten anzuzeigen:

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

1. Speichern Sie Ihre Änderungen (STRG+S), und geben Sie dann den folgenden Befehl ein, um das Programm auszuführen (maximieren Sie den Bereich der Cloud Shell und ändern Sie die Größe der Panels, um mehr Text im Befehlszeilenbereich anzuzeigen):

    ```
   python qna-app.py
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie eine Frage ein, die an Ihr „Fragen und Antworten“-Projekt übermittelt werden soll. Beispiel: `What is a learning path?`.
1. Überprüfen Sie die Antwort, die zurückgegeben wird.
1. Stellen Sie weitere Fragen. Wenn Sie fertig sind, geben Sie die Zeichenfolge `quit` ein.

## Bereinigen von Ressourcen

Wenn Sie die Erkundung des Dienstes Azure KI Language abgeschlossen haben, können Sie die in dieser Übung erstellten Ressourcen löschen. Gehen Sie dazu wie folgt vor:

1. Schließen Sie den Azure Cloud Shell-Bereich
1. Navigieren Sie im Azure-Portal zu der Azure KI Language-Ressource, die Sie in diesem Lab erstellt haben.
1. Wählen Sie auf der Ressourcenseite **Löschen** aus, und folgen Sie den Anweisungen zum Löschen der Ressource.

## Weitere Informationen

Weitere Informationen zu Fragen und Antworten in Azure KI Language finden Sie in der [Dokumentation zu Azure KI Language](https://learn.microsoft.com/azure/ai-services/language-service/question-answering/overview).
