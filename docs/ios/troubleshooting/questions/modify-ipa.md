---
title: È possibile aggiungere file o rimuovere i file da un file IPA dopo averlo compilato in Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b8b61ba38491b2085233dd1b30a82bc57d2baaed
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30777931"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>È possibile aggiungere file o rimuovere i file da un file IPA dopo averlo compilato in Visual Studio?

Sì, è possibile, ma in genere è necessario firmare nuovamente il `.app` bundle dopo aver apportato la modifica.

Si noti che la modifica di `.ipa` file non è necessario nell'utilizzo normale. In questo articolo viene fornito esclusivamente per scopi informativi.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Esempio: rimozione di un file da un `.ipa` archivio

Questo esempio presuppone che sia il nome del progetto xamarin `iPhoneApp1` e `generated session id` è `cc530d20d6b19da63f6f1c6f67a0a254`

1.  Compilare il `.ipa` file normalmente da Visual Studio.

2.  Passare all'host di compilazione Mac.

3.  Trovare la compilazione nel `~/Library/Caches/Xamarin/mtbs/builds` cartella. È possibile incollare il percorso in **Finder > passare > passare alla cartella** per individuare la cartella di ricerca. Cercare la cartella che corrisponde al nome del progetto. All'interno di tale cartella, cercare la cartella corrispondente il `generated session id` della compilazione. Si tratterà probabilmente la sottocartella contenente l'ora di modifica più recente.

4.  Aprire una nuova `Terminal.app` finestra.

5.  Tipo `cd ` nella finestra Terminal.app e quindi trascina selezione di `generated session id` nella cartella di `Terminal.app` finestra:

    ![](modify-ipa-images/session-id-folder.png "Individuare la cartella di id di sessione generato in Finder")

6.  Digitare il tasto INVIO per spostarsi nel `generated session id` cartella.

7.  Decomprimere il `.ipa` file in una variabile temporanea `old/` cartella utilizzando il comando seguente. Regolare il `Ad-Hoc` e `iPhoneApp1` nomi in base alle esigenze per un particolare progetto.

    > punteggiatura bin/iPhone/Ad-Hoc/iPhoneApp1-1.0.ipa - xk precedente /

8.  Mantenere il `Terminal.app` finestra aperta.

9.  Eliminare i file desiderati dal `.ipa`. È possibile spostarli nel Cestino tramite ricerca o eliminarle nella riga di comando mediante `Terminal.app`. Per visualizzare il contenuto del `Payload/iPhone` file in Finder, tenendo premuto CTRL e selezionare **visualizzare il contenuto del pacchetto**.

10.  Utilizzando lo stesso approccio generale come indicato al passaggio 3, trovare il file di log in `~/Library/Logs/Xamarin/MonoTouchVS/` con il nome di progetto e `generated session id` nel nome: ![ ] (modify-ipa-images/build-log.png "individuare il log di compilazione del progetto in Finder")

11.  Aprire il log di compilazione dal passaggio 10, ad esempio facendovi doppio clic.

12.  Trovare la riga che include `tool /usr/bin/codesign execution started with arguments: -v --force --sign`.

13.  Tipo `/usr/bin/codesign ` nella finestra Terminal.app passaggio 8.

14.  Copiare tutti gli argomenti a partire da `-v` dalla riga di nel passaggio 12 e incollarli nella finestra Terminal.app.

15.  Modificare l'ultimo argomento sia il `.app` bundle si trova all'interno di `old/Payload/` cartella e quindi eseguire il comando.

```bash
/usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
```

16.  Modificare nel `old/` directory Terminal:

```bash
cd old
```

17.  Comprimere il contenuto della directory in un nuovo `.ipa` file utilizzando il `zip` comando. È possibile modificare il `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` argomento di output di `.ipa` file ogni volta che si desidera:

```bash
zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
```

## <a name="common-error-messages"></a>Messaggi di errore comuni

Se viene visualizzato `Invalid Signature. A sealed resource is missing or invalid.`, in genere, ciò significa che un elemento è stato modificato all'interno di `.app` bundle e che il `.app` bundle non è stato correttamente nuovamente firmato in un secondo momento. Si noti inoltre che se si desidera creare un `.ipa` con un profilo di distribuzione, si _deve_ compilare originale `.ipa` con un profilo di distribuzione. In caso contrario il `Entitlements.xcent` non saranno corretti.

Per fornire un esempio concreto di cui può verificarsi questo errore, se si esegue le operazioni seguenti `codesign --verify` comando nella finestra del terminale dopo il passaggio 9, verrà visualizzato l'errore con la causa precisa dell'errore:

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

E il processo di verifica App Store segnalerà un messaggio di errore simile:

> Errore ITMS-90035: "firma non valida. Una risorsa sealed è mancante o non valido. Il file binario nel percorso [iPhoneApp1.app/iPhoneApp1] contiene una firma non valida. Assicurarsi di che aver effettuato l'accesso dell'applicazione con un certificato di distribuzione, non un certificato ad hoc o un certificato di sviluppo. Verificare che le impostazioni di firma codice in Xcode siano corrette a livello di destinazione (che eseguono l'override di tutti i valori a livello di progetto). Inoltre, verificare che il pacchetto di cui che si sta caricando è stato creato utilizzando una destinazione di rilascio in Xcode, non è una destinazione di simulatore. Se si è certi che le impostazioni di firma codice siano corrette, scegliere "Pulita tutto" in Xcode, eliminare la directory "build" in Finder e ricompilare la destinazione di rilascio. Per altre informazioni, consultare [ https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html ](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)"
