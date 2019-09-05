---
title: È possibile aggiungere o rimuovere file da un file IPA dopo averli compilati in Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: d1546b83304d8c66f7433bd77c5ebecc9dc95aaa
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278475"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>È possibile aggiungere o rimuovere file da un file IPA dopo averli compilati in Visual Studio?

Sì, è possibile, ma in genere è necessario firmare di nuovo il `.app` bundle dopo aver apportato la modifica.

Si noti che la `.ipa` modifica del file non è necessaria nel normale utilizzo. Questo articolo viene fornito esclusivamente a scopo informativo.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Esempio: rimozione di un file da `.ipa` un archivio

Per questo esempio si presuppone che il nome del progetto Novell. iOS sia `iPhoneApp1` `generated session id` e che sia`cc530d20d6b19da63f6f1c6f67a0a254`

1. Compilare il `.ipa` file come di consueto da Visual Studio.

2. Passa all'host di compilazione Mac.

3. Trovare la compilazione nella `~/Library/Caches/Xamarin/mtbs/builds` cartella. È possibile incollare questo percorso in **Finder > vai > Vai alla cartella** per esplorare la cartella in Finder. Cercare la cartella corrispondente al nome del progetto. All'interno di tale cartella, cercare la cartella corrispondente alla `generated session id` della compilazione. Probabilmente si tratta della sottocartella con l'ora di modifica più recente.

4. Aprire una nuova `Terminal.app` finestra.

5. Digitare `cd` nella finestra terminal. app, quindi trascinare & rilasciare la `generated session id` cartella nella `Terminal.app` finestra:

    ![](modify-ipa-images/session-id-folder.png "Individuazione della cartella di ID sessione generata in Finder")

6. Digitare il tasto INVIO per cambiare directory nella `generated session id` cartella.

7. Decomprimere il `.ipa` file in `old/` una cartella temporanea usando il comando seguente. Modificare i `Ad-Hoc` nomi `iPhoneApp1` e in base alle esigenze per il progetto specifico.

    > Ditto-XK bin/iPhone/Ad-Hoc/iPhoneApp1-1.0. ipa vecchio/

8. Lasciare aperta `Terminal.app` la finestra.

9. Eliminare i file desiderati da `.ipa`. È possibile spostarli nel cestino usando Finder o eliminarli dalla riga di comando usando `Terminal.app`. Per visualizzare il contenuto del `Payload/iPhone` file in Finder, fare clic con il pulsante destro del mouse sul file e scegliere **Mostra contenuto pacchetto**.

10. Utilizzando lo stesso approccio generale del passaggio 3, trovare il file di log con `~/Library/Logs/Xamarin/MonoTouchVS/` il nome del progetto e il `generated session id` nel nome: ![](modify-ipa-images/build-log.png "Individuare il log di compilazione del progetto in Finder")

11. Aprire il log di compilazione dal passaggio 10, ad esempio facendo doppio clic su di esso.

12. Trovare la riga che include `tool /usr/bin/codesign execution started with arguments: -v --force --sign`.

13. Digitare `/usr/bin/codesign` nella finestra terminal. app del passaggio 8.

14. Copiare tutti gli argomenti a partire `-v` da dalla riga nel passaggio 12 e incollarli nella finestra terminal. app.

15. Modificare l'ultimo argomento in modo che `.app` sia il bundle che `old/Payload/` si trova all'interno della cartella, quindi eseguire il comando.

    ```bash
    /usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
    ```

16. Passare alla `old/` directory nel terminale:

    ```bash
    cd old
    ```

17. Comprimere il contenuto della directory in un nuovo `.ipa` file usando il `zip` comando. È possibile modificare l' `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` argomento per l'output `.ipa` del file in qualsiasi posizione:

    ```bash
    zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
    ```

## <a name="common-error-messages"></a>Messaggi di errore comuni

Se viene visualizzato `Invalid Signature. A sealed resource is missing or invalid.`, significa che in genere qualcosa è stato modificato all' `.app` interno dell'aggregazione e `.app` che il bundle non è stato firmato di nuovo in seguito. Si noti inoltre che se si desidera creare un `.ipa` con un profilo di distribuzione, è _necessario_ compilare l' `.ipa` originale con un profilo di distribuzione. In caso `Entitlements.xcent` contrario, l'oggetto non sarà corretto.

Per fornire un esempio concreto di come può verificarsi questo errore, se si esegue il `codesign --verify` comando seguente nella finestra del terminale dopo il passaggio 9, verrà visualizzato l'errore, insieme alla relativa ragione precisa dell'errore:

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

E il processo di verifica dell'app Store segnalerà un messaggio di errore simile al seguente:

> ERROR ITMS-90035: "Firma non valida. Una risorsa sealed manca o non è valida. Il file binario nel percorso [iPhoneApp1. app/iPhoneApp1] contiene una firma non valida. Assicurarsi di aver firmato l'applicazione con un certificato di distribuzione, non un certificato ad hoc o un certificato di sviluppo. Verificare che le impostazioni di firma del codice in Xcode siano corrette a livello di destinazione (che eseguono l'override di tutti i valori a livello di progetto). Assicurarsi inoltre che il bundle che si sta caricando sia stato compilato usando una destinazione di rilascio in Xcode, non un simulatore di destinazione. Se si è certi che le impostazioni di firma del codice siano corrette, scegliere "Pulisci tutto" in Xcode, eliminare la directory "Build" nel Finder e ricompilare la destinazione di rilascio. Per ulteriori informazioni, consultare [https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)"
