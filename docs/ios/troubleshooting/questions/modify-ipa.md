---
title: È possibile aggiungere o rimuovere file da un file IPA dopo averli compilati in Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: f63cc544b251ca284a8d087e09f9cbc4dfb3f769
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030961"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>È possibile aggiungere o rimuovere file da un file IPA dopo averli compilati in Visual Studio?

Sì, è possibile, ma in genere è necessario firmare nuovamente il bundle di `.app` dopo aver apportato la modifica.

Si noti che la modifica del file di `.ipa` non è necessaria nel normale utilizzo. Questo articolo viene fornito esclusivamente a scopo informativo.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Esempio: rimozione di un file da un archivio `.ipa`

Per questo esempio si presuppone che il nome del progetto Novell. iOS sia `iPhoneApp1` e che il `generated session id` sia `cc530d20d6b19da63f6f1c6f67a0a254`

1. Compilare il file di `.ipa` come di consueto da Visual Studio.

2. Passa all'host di compilazione Mac.

3. Trovare la compilazione nella cartella `~/Library/Caches/Xamarin/mtbs/builds`. È possibile incollare questo percorso in **Finder > vai > Vai alla cartella** per esplorare la cartella in Finder. Cercare la cartella corrispondente al nome del progetto. All'interno di tale cartella, cercare la cartella corrispondente al `generated session id` della compilazione. Probabilmente si tratta della sottocartella con l'ora di modifica più recente.

4. Aprire una nuova finestra di `Terminal.app`.

5. Digitare `cd` nella finestra terminal. app, quindi trascinare & rilasciare la cartella `generated session id` nella finestra `Terminal.app`:

    ![](modify-ipa-images/session-id-folder.png "Locating the generated session id folder in Finder")

6. Digitare il tasto INVIO per cambiare directory nella cartella `generated session id`.

7. Decomprimere il file di `.ipa` in una cartella di `old/` temporanea usando il comando seguente. Modificare i nomi di `Ad-Hoc` e `iPhoneApp1` in base alle esigenze per il progetto specifico.

    > Ditto-XK bin/iPhone/Ad-Hoc/iPhoneApp1-1.0. ipa vecchio/

8. Lasciare aperta la finestra `Terminal.app`.

9. Eliminare i file desiderati dal `.ipa`. È possibile spostarli nel cestino usando il Finder o eliminarli dalla riga di comando usando `Terminal.app`. Per visualizzare il contenuto del file di `Payload/iPhone` in Finder, controllare il file e selezionare **Mostra contenuto pacchetto**.

10. Utilizzando lo stesso approccio generale del passaggio 3, trovare il file di log in `~/Library/Logs/Xamarin/MonoTouchVS/` con il nome del progetto e il `generated session id` nel nome:![](modify-ipa-images/build-log.png "Individuare il log di compilazione del progetto in Finder")

11. Aprire il log di compilazione dal passaggio 10, ad esempio facendo doppio clic su di esso.

12. Trovare la riga che include `tool /usr/bin/codesign execution started with arguments: -v --force --sign`.

13. Digitare `/usr/bin/codesign` nella finestra terminal. app del passaggio 8.

14. Copiare tutti gli argomenti che iniziano con `-v` dalla riga nel passaggio 12 e incollarli nella finestra terminal. app.

15. Modificare l'ultimo argomento in modo che sia il bundle `.app` che si trova all'interno della cartella `old/Payload/`, quindi eseguire il comando.

    ```bash
    /usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
    ```

16. Passare alla directory `old/` nel terminale:

    ```bash
    cd old
    ```

17. Utilizzare il comando `zip` per comprimere il contenuto della directory in un nuovo file di `.ipa`. È possibile modificare l'argomento `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` per restituire l'output del file di `.ipa` in qualsiasi posizione:

    ```bash
    zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
    ```

## <a name="common-error-messages"></a>Messaggi di errore comuni

Se viene visualizzato `Invalid Signature. A sealed resource is missing or invalid.`, significa che in genere qualcosa è stato modificato all'interno del bundle di `.app` e che il bundle di `.app` non è stato firmato di nuovo in seguito. Si noti inoltre che se si desidera creare un `.ipa` con un profilo di distribuzione, è _necessario_ compilare il `.ipa` originale con un profilo di distribuzione. In caso contrario, la `Entitlements.xcent` non sarà corretta.

Per fornire un esempio concreto di come può verificarsi questo errore, se si esegue il comando di `codesign --verify` seguente nella finestra del terminale dopo il passaggio 9, verrà visualizzato l'errore, insieme alla relativa ragione precisa:

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

E il processo di verifica dell'app Store segnalerà un messaggio di errore simile al seguente:

> ERRORE ITMS-90035: "firma non valida. Una risorsa sealed manca o non è valida. Il file binario nel percorso [iPhoneApp1. app/iPhoneApp1] contiene una firma non valida. Assicurarsi di aver firmato l'applicazione con un certificato di distribuzione, non un certificato ad hoc o un certificato di sviluppo. Verificare che le impostazioni di firma del codice in Xcode siano corrette a livello di destinazione (che eseguono l'override di tutti i valori a livello di progetto). Assicurarsi inoltre che il bundle che si sta caricando sia stato compilato usando una destinazione di rilascio in Xcode, non un simulatore di destinazione. Se si è certi che le impostazioni di firma del codice siano corrette, scegliere "Pulisci tutto" in Xcode, eliminare la directory "Build" nel Finder e ricompilare la destinazione di rilascio. Per ulteriori informazioni, consultare [https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)"
