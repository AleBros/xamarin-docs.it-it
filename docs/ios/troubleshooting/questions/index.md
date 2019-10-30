---
title: Domande frequenti su Novell. iOS
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: e79fca8c59ae49d27cd335106ca57945be106031
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031081"
---
# <a name="ios-frequently-asked-questions"></a>Domande frequenti su iOS

## <a name="general-questions"></a>Domande generali

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[È possibile usare una macchina virtuale Mac con Xamarin?](mac-vm.md)
Sì, ma solo su hardware Mac.

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[Come è possibile eseguire il downgrade di Xcode?](downgrade-xcode.md)
Questa guida fornisce i collegamenti per accedere alle versioni precedenti di Xcode e alla versione più recente.

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[Dove è possibile impostare i percorsi di iOS SDK?](ios-sdk.md)
Per la maggior parte degli utenti, questi vengono impostati automaticamente nei percorsi appropriati. Questa guida elenca i percorsi SDK predefiniti e come modificarli, se necessario.

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[Come è possibile riabilitare le opzioni per gli sviluppatori dopo l'aggiornamento di iOS?](update-developer-options.md)
Un bug iOS può causare la scomparsa delle opzioni dello sviluppatore dopo l'aggiornamento delle versioni di iOS. questa osservazione è stata osservata quando si passa a iOS 8. x. Questa guida descrive come è possibile riabilitare le opzioni.

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[La posizione dell'utente non funziona in iOS 8](ios8-user-location.md)
Questa guida descrive come modificare info. plist per abilitare la posizione utente in iOS 8.

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[Dove si può trovare il file dSYM per aggiungere simboli di debug ai log di arresto anomalo iOS?](symbolicate-ios-crash.md)
Questa guida descrive i passaggi di base per i log di arresto anomalo di symbolicating iOS per semplificare la diagnosi degli arresti anomali. Sono inoltre disponibili collegamenti a risorse aggiuntive per tecniche di simbolizzazione più avanzate & informazioni sull'interpretazione dei log di arresto anomalo di iOS.

### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[Come si impostano le variabili di ambiente di runtime Mono per i progetti iOS in Xamarin Studio?](xs-mono-runtime.md)
Se è necessario impostare le variabili di ambiente di runtime per mono, è possibile impostarle nelle **Opzioni del progetto > eseguire > pagina generale** .

## <a name="publishing-questions"></a>Pubblicazione di domande

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[Errore durante l'invio all'App Store: "Bundle non valido: le opzioni non consentite per l'incorporamento in bitcode sono rilevate nell'invio"](invalid-bundle-bitcode.md)

L'invio di app che _richiedono_ bitcode, ad esempio watchos e app tvOS, deve essere eseguito con Xcode 9.

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[È possibile modificare il percorso di output del file IPA?](ipa-output-path.md)
A partire da Novell Cycle 7, è possibile usare destinazioni MSBuild personalizzate per ottenere questo risultato.

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[Come è possibile copiare i file di output IPA nella cartella di ricezione TFS?](ipa-tfs.md)
Sì, in questa guida viene descritto come.

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[È possibile aggiungere o rimuovere file da un file IPA dopo averli compilati in Visual Studio?](modify-ipa.md)
Sì, è possibile, ma in genere è necessario firmare nuovamente il bundle di `.app` dopo aver apportato la modifica. Si noti che la modifica del file di `.ipa` non è necessaria nel normale utilizzo. Questo articolo viene fornito esclusivamente a scopo informativo.

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[È possibile creare un archivio con estensione xcarchive da Visual Studio?](create-xcarchive.md)
A partire da Novell 4, è ora possibile creare un `.xcarchive` da Windows impostando la proprietà `ArchiveOnBuild` su `true`.

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[Perché l'invio dell'app non riesce con l'errore: "Percorsi non consentiti ( "iTunesMetadata.plist" ) in ..." ?](itunesmetadata-disallowed-paths.md)
Questo errore è il risultato di una modifica nel processo di verifica dell'app Store di Apple. Questo errore specifico _non_ è correlato alla versione specifica di Novell installata, quindi il downgrade _non_ sarà utile. Questa guida include collegamenti a ulteriori informazioni su come risolvere il problema.

## <a name="diagnosing-specific-error-messages"></a>Diagnosi di messaggi di errore specifici

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[Errore di iOS Designer con RegisterServicePort](error-registerserviceport.md)
Gli errori con `RegisterServicePort` e messaggi di errore simili, come sopra, rappresentano in genere un problema con spyware/malware nel computer. Questa guida descrive in dettaglio la diagnosi e le informazioni sulla rimozione dello spyware o del malware.

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[Perché la compilazione iOS non riesce con l'errore: nessuna chiave di firma del codice iPhone valida trovata nel keychain?](no-codesigning-keys.md)
Questo messaggio di errore viene visualizzato quando il progetto in questione cerca le credenziali di firma codice valide ma non le trova. La firma del codice è necessaria per i test e le distribuzioni nei dispositivi iOS fisici. così come le compilazioni di & App Store ad hoc.

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[Perché l'app iOS 9 genera l'errore: System.Exception: impossibile effettuare il marshalling dell'oggetto Objective-C?](exception-marshal-obj-c.md)
Le modifiche alle API in iOS 9 richiedono che venga usato un costruttore di callback quando si chiama il codice non gestito, perché l'API sottostante ora lo prevede.

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[Errore di runtime: impossibile trovare o caricare l'assembly mscorlib.dll](error-mscorlib-not-found.md)
Questo problema si verifica quando le cartelle `.monotouch-32` e `.monotouch-64` *nascoste* non sono presenti nel `.xcarchive` per la creazione della firma/IPA, che attiva l'errore di Runtime.

### <a name="compile-error-can-not-encode-offset-x-in-resulting-scattered-relocationerror-encode-offset-scattered-relocationmd"></a>[Errore di compilazione: non è possibile codificare l'offset X in una rilocazione distribuita risultante](error-encode-offset-scattered-relocation.md)
Questo problema si verifica quando si compila per le architetture a 32 bit, ad esempio ARMv7, quando il file binario finale è troppo grande per la piattaforma nativa.

## <a name="deprecated"></a>Deprecato

> [!IMPORTANT]
> Gli articoli seguenti si applicano ai problemi risolti nelle versioni recenti di Novell. Tuttavia, se il problema si verifica nella versione più recente del software, inserire un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con le informazioni complete sul controllo delle versioni e l'output del log di compilazione completo.

### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[Il file IPA è di 0 byte](ipa-zero-bytes.md)
Si sono verificati alcuni problemi noti nelle versioni precedenti di Novell che potevano causare 0 byte per il file IPA in Windows.

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[Errore IBTool: Impossibile completare l'operazione.](error-ibtool.md)
Apple ha [corretto](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html) questo `ibtool` bug in Xcode 6.1.1, quindi l'aggiornamento a Xcode 6.1.1 o versione successiva è la soluzione più semplice.

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[Errore MT1009: Impossibile copiare l'assembly](error-mt1009.md)
Ciò influiscono sugli utenti che eseguono Novell. iOS 7.2.6. Questo problema è dovuto alle autorizzazioni per i file che richiedono privilegi più elevati quando Novell. iOS viene installato con un account utente diverso, quindi l'account principale dello sviluppatore.

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[System. Exception AMDeviceNotificationSubscribe ha restituito...](exception-amddevicenotificationsubscribe.md)
Questo messaggio può essere visualizzato in una finestra di dialogo di errore quando si avvia per la prima volta Visual Studio per Mac o nel file di `mtbserver.log`. Si noti che si tratta di un problema non comune. Se si verificano problemi durante la connessione di Visual Studio all'host di compilazione Mac, sono presenti altri errori che potrebbero essere visualizzati nel file di `mtbserver.log`.

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe non trovato rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
Questo errore può essere visualizzato nel `Mac Server Log` in Visual Studio.
