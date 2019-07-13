---
title: È possibile aggiungere file a o rimuovere i file da un file IPA dopo la compilazione in Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 047ee06522d4b2c07937e0e1bd9985248a164f01
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865023"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>È possibile aggiungere file a o rimuovere i file da un file IPA dopo la compilazione in Visual Studio?

Sì, è possibile, ma in genere è necessario firmare nuovamente il `.app` bundle dopo aver apportato la modifica.

Si noti che la modifica di `.ipa` file non è necessario nell'utilizzo normale. Questo articolo viene fornito esclusivamente per scopi informativi.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Esempio: rimozione di un file da un `.ipa` archive

Questo esempio presuppone che il nome del progetto xamarin. IOS sia `iPhoneApp1` e il `generated session id` è `cc530d20d6b19da63f6f1c6f67a0a254`

1. Compilare il `.ipa` file normalmente da Visual Studio.

2. Passare all'host di compilazione Mac.

3. Trovare la compilazione nel `~/Library/Caches/Xamarin/mtbs/builds` cartella. È possibile incollare in questo percorso **Finder > Vai > passare alla cartella** per passare alla cartella in Finder. Cercare la cartella che corrisponde al nome del progetto. All'interno di tale cartella, cercare la cartella che corrisponde il `generated session id` della compilazione. Si tratterà molto probabilmente la sottocartella che include l'ora di modifica più recente.

4. Aprire una nuova `Terminal.app` finestra.

5. Tipo `cd` nella finestra del terminal e quindi trascina selezione il `generated session id` nella cartella di `Terminal.app` finestra:

    ![](modify-ipa-images/session-id-folder.png "Individuare la cartella con l'id sessione generato in Finder")

6. Digitare il tasto INVIO per modificare la directory nel `generated session id` cartella.

7. Decomprimere il `.ipa` file in una variabile temporanea `old/` cartella usando il comando seguente. Modificare il `Ad-Hoc` e `iPhoneApp1` nomi in base alle esigenze per un particolare progetto.

    > -xk bin/iPhone/Ad-Hoc/iPhoneApp1-1.0.ipa precedente di punteggiatura /

8. Mantenere il `Terminal.app` finestra aperta.

9. Eliminare i file desiderati dal `.ipa`. È possibile spostarli nel Cestino mediante il Finder oppure eliminarle nella riga di comando tramite `Terminal.app`. Per visualizzare il contenuto del `Payload/iPhone` file in Finder, tenendo premuto CTRL e selezionare **Mostra contenuti pacchetto**.

10. Usando lo stesso approccio generale come indicato nel passaggio 3, individuare il file di log nella `~/Library/Logs/Xamarin/MonoTouchVS/` che contiene sia il nome del progetto e il `generated session id` nel nome: ![](modify-ipa-images/build-log.png "Individuare il log di compilazione progetto in Finder")

11. Aprire il log di compilazione nel passaggio 10, ad esempio facendovi doppio clic.

12. Trovare la riga che include `tool /usr/bin/codesign execution started with arguments: -v --force --sign`.

13. Tipo `/usr/bin/codesign` nella finestra terminal dal passaggio 8.

14. Copiare tutti gli argomenti a partire da `-v` dalla riga di nel passaggio 12 e incollarli nella finestra terminal.

15. Modificare l'ultimo argomento sia la `.app` bundle che si trova all'interno di `old/Payload/` cartella e quindi eseguire il comando.

    ```bash
    /usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
    ```

16. Passare il `old/` directory nel terminale:

    ```bash
    cd old
    ```

17. Comprimere i contenuti della directory in una nuova `.ipa` file utilizzando il `zip` comando. È possibile modificare il `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` argomento per restituire il `.ipa` file ogni volta che si vuole:

    ```bash
    zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
    ```

## <a name="common-error-messages"></a>Messaggi di errore comuni

Se viene visualizzato `Invalid Signature. A sealed resource is missing or invalid.`, che in genere significa che qualcosa è stato modificato all'interno di `.app` bundle e che il `.app` bundle non è stato correttamente nuovamente firmato in un secondo momento. Si noti anche che se si vuole creare un `.ipa` con un profilo di distribuzione, si _necessario_ compilazione originale `.ipa` con un profilo di distribuzione. In caso contrario il `Entitlements.xcent` non saranno corrette.

Per fornire un esempio concreto di cui può verificarsi questo errore, se si esegue la seguente `codesign --verify` comando nella finestra del terminale dopo il passaggio 9, verrà visualizzato l'errore e la causa precisa dell'errore:

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

E il processo di verifica App Store segnalerà un messaggio di errore simile:

> ERROR ITMS-90035: "Firma non valida. Una risorsa sealed è mancante o non valido. Il file binario nel percorso [iPhoneApp1.app/iPhoneApp1] contiene una firma non valida. Assicurarsi di che aver effettuato l'accesso all'applicazione con un certificato di distribuzione, non un certificato ad hoc o un certificato di sviluppo. Verificare che le impostazioni di firma codice in Xcode siano corrette a livello di destinazione (che esegue l'override di tutti i valori a livello di progetto). Inoltre, assicurarsi che il bundle di cui che si sta caricando è stato creato usando una destinazione di rilascio in Xcode, non una destinazione simulatore. Se si è certi che le impostazioni di firma codice siano corrette, scegliere "Pulisci tutto" in Xcode, eliminare la directory "build" nel Finder e ricompilare la destinazione di rilascio. Per altre informazioni, consultare [ https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html ](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)"
