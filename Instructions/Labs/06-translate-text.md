---
lab:
  title: Übersetzen von Text
  description: Übersetzen Sie bereitgestellten Text in alle unterstützten Sprachen mit Azure KI Übersetzer.
---

# Übersetzen von Text

**Azure KI Übersetzer** ist ein Dienst, mit dem Sie Texte zwischen Sprachen übersetzen können. In dieser Übung verwenden Sie ihn, um eine einfache App zu erstellen, die Eingaben in jeder unterstützten Sprache in die Zielsprache Ihrer Wahl übersetzt.

Obwohl diese Übung auf Python basiert, können Sie ähnliche Anwendungen zur Textübersetzung mit mehreren sprachspezifischen SDKs entwickeln, einschließlich:

- [Azure KI-Übersetzungs-Clientbibliothek für Python](https://pypi.org/project/azure-ai-translation-text/)
- [Azure KI-Übersetzungs-Clientbibliothek für .NET](https://www.nuget.org/packages/Azure.AI.Translation.Text)
- [Azure KI-Übersetzungs-Clientbibliothek für JavaScript](https://www.npmjs.com/package/@azure-rest/ai-translation-text)

Diese Übung dauert ca. **30** Minuten.

## Bereitstellen einer *Azure KI Übersetzer*-Ressource

Wenn Sie noch nicht über eine solche Ressource in Ihrem Abonnement verfügen, müssen Sie eine **Azure KI Übersetzer**-Ressource bereitstellen.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Suchen Sie im Suchfeld oben nach **Übersetzer** und wählen Sie dann **Übersetzer** in den Ergebnissen aus.
1. Erstellen Sie eine Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine*.
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Wählen Sie **F0** (*Free*) oder **S** (*Standard*) aus, falls F nicht verfügbar ist.
1. Wählen Sie **Überprüfen und erstellen** aus und dann **Erstellen**, um die Ressource bereitzustellen.
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, und wechseln Sie dann zur bereitgestellten Ressource.
1. Sehen Sie sich die Seite **Schlüssel und Endpunkt** an. Sie benötigen die Informationen auf dieser Seite später in der Übung.

## Vorbereiten der Entwicklung einer App in Cloud Shell

Um die Textübersetzungsfunktionen von Azure KI Übersetzer zu testen, entwickeln Sie eine einfache Konsolenanwendung in der Azure Cloud Shell.

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
   cd mslearn-ai-language/Labfiles/06-translator-sdk/Python/translate-text
    ```

## Konfigurieren der Anwendung

1. Führen Sie im Befehlszeilenbereich den folgenden Befehl aus, um die Codedateien im Ordner **translate-text** anzuzeigen:

    ```
   ls -a -l
    ```

    Die Dateien enthalten eine Konfigurationsdatei (**.env**) und eine Codedatei (**translate.py**).

1. Erstellen Sie eine virtuelle Python-Umgebung und installieren Sie das Azure KI-Übersetzungs-SDK-Paket sowie andere erforderliche Pakete, indem Sie den folgenden Befehl ausführen:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-ai-translation-text==1.0.1
    ```

1. Geben Sie den folgenden Befehl ein, um die Anwendungskonfigurationsdatei zu bearbeiten:

    ```
   code .env
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Aktualisieren Sie die Konfigurationswerte so, dass sie die **Region** und einen **Schlüssel** aus der Azure KI Übersetzer-Ressource enthalten, die Sie erstellt haben (verfügbar auf der Seite **Schlüssel und Endpunkt** für Ihre Azure KI Übersetzer-Ressource im Azure-Portal).

    > **HINWEIS**: Achten Sie darauf, die *Region* für Ihre Ressource hinzuzufügen, <u>nicht</u> den Endpunkt!

1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie im Code-Editor den Befehl **STRG+S** oder **Rechtsklick > Speichern**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q** oder **Rechtsklick > Beenden**, um den Code-Editor zu schließen, während die Cloud Shell-Befehlszeile geöffnet bleibt.

## Hinzufügen von Code zum Übersetzen von Text

1. Geben Sie den folgenden Befehl ein, um die Anwendungscodedatei zu bearbeiten:

    ```
   code translate.py
    ```

1. Überprüfen Sie den vorhandenen Code. Sie fügen Code zum Arbeiten mit dem Azure KI-Übersetzungs-SDK hinzu.

    > **Tipp**: Achten Sie beim Hinzufügen von Code zur Codedatei darauf, den richtigen Einzug beizubehalten.

1. Suchen Sie oben in der Codedatei unter den vorhandenen Namespace-Verweisen den Kommentar **Import namespaces** und fügen Sie den folgenden Code hinzu, um die Namespaces zu importieren, die Sie für die Verwendung des Übersetzungs-SDK benötigen:

    ```python
   # import namespaces
   from azure.core.credentials import AzureKeyCredential
   from azure.ai.translation.text import *
   from azure.ai.translation.text.models import InputTextItem
    ```

1. Beachten Sie, dass der in der **Main**-Funktion vorhandene Code die Konfigurationseinstellungen liest.
1. Suchen Sie den Kommentar **Create client using endpoint and key**, und fügen Sie den folgenden Code hinzu:

    ```python
   # Create client using endpoint and key
   credential = AzureKeyCredential(translatorKey)
   client = TextTranslationClient(credential=credential, region=translatorRegion)
    ```

1. Suchen Sie den Kommentar **Choose target language** und fügen Sie den folgenden Code hinzu. Dieser gibt mithilfe des Textübersetzerdiensts eine Liste der unterstützten Sprachen für die Übersetzung zurück und fordert Benutzende auf, einen Sprachcode für die Zielsprache auszuwählen:

    ```python
   # Choose target language
   languagesResponse = client.get_supported_languages(scope="translation")
   print("{} languages supported.".format(len(languagesResponse.translation)))
   print("(See https://learn.microsoft.com/azure/ai-services/translator/language-support#translation)")
   print("Enter a target language code for translation (for example, 'en'):")
   targetLanguage = "xx"
   supportedLanguage = False
   while supportedLanguage == False:
        targetLanguage = input()
        if  targetLanguage in languagesResponse.translation.keys():
            supportedLanguage = True
        else:
            print("{} is not a supported language.".format(targetLanguage))
    ```

1. Suchen Sie den Kommentar **Translate text** und fügen Sie den folgenden Code hinzu. Dieser fordert Benutzende wiederholt dazu auf, Text zu übersetzen, und verwendet den Azure KI Übersetzer-Dienst, um Text in die Zielsprache zu übersetzen (die Quellsprache wird automatisch erkannt). Er zeigt die Ergebnisse an, bis Benutzende die Sitzung *beenden*:

    ```python
   # Translate text
   inputText = ""
   while inputText.lower() != "quit":
        inputText = input("Enter text to translate ('quit' to exit):")
        if inputText != "quit":
            input_text_elements = [InputTextItem(text=inputText)]
            translationResponse = client.translate(body=input_text_elements, to_language=[targetLanguage])
            translation = translationResponse[0] if translationResponse else None
            if translation:
                sourceLanguage = translation.detected_language
                for translated_text in translation.translations:
                    print(f"'{inputText}' was translated from {sourceLanguage.language} to {translated_text.to} as '{translated_text.text}'.")
    ```

1. Speichern Sie Ihre Änderungen (STRG+S), und geben Sie dann den folgenden Befehl ein, um das Programm auszuführen (maximieren Sie den Bereich der Cloud Shell und ändern Sie die Größe der Panels, um mehr Text im Befehlszeilenbereich anzuzeigen):

    ```
   python translate.py
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie eine gültige Zielsprache aus der angezeigten Liste ein.
1. Geben Sie einen Ausdruck ein, der übersetzt werden soll (z. B. `This is a test` oder `C'est un test`), und zeigen Sie die Ergebnisse an, bei denen die Ausgangssprache erkannt und der Text in die Zielsprache übersetzt wurde.
1. Wenn Sie fertig sind, geben Sie die Zeichenfolge `quit` ein. Sie können die Anwendung erneut ausführen und eine andere Zielsprache auswählen.

## Bereinigen von Ressourcen

Wenn Sie mit der Erkundung des Azure KI Übersetzer-Diensts fertig sind, können Sie die in dieser Übung erstellten Ressourcen löschen. Gehen Sie dazu wie folgt vor:

1. Schließen Sie den Azure Cloud Shell-Bereich
1. Navigieren Sie im Azure-Portal zu der Azure KI Übersetzer-Ressource, die Sie in diesem Lab erstellt haben.
1. Wählen Sie auf der Ressourcenseite **Löschen** aus, und folgen Sie den Anweisungen zum Löschen der Ressource.

## Weitere Informationen

Weitere Informationen zur Verwendung von **Azure KI Übersetzer** finden Sie in der [Dokumentation zum Azure KI Übersetzer](https://learn.microsoft.com/azure/ai-services/translator/).
