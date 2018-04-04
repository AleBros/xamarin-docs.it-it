---
title: Domande frequenti
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: f8264c48b3b4679d45d5603b5637df0016cd3d17
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="frequently-asked-questions"></a>Domande frequenti

## <a name="general-questions"></a>Domande di carattere generale

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[È possibile usare una macchina virtuale Mac con Xamarin?](mac-vm.md)
Sì, ma solo su hardware Mac.

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[Come è possibile eseguire il downgrade di Xcode?](downgrade-xcode.md)
Questa guida vengono forniti i collegamenti per accedere alle versioni precedenti di Xcode, nonché la versione più recente.

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[Dove è possibile impostare i percorsi di iOS SDK?](ios-sdk.md)
Per la maggior parte degli utenti questi vengono impostati nei percorsi appropriati automaticamente. Questa guida vengono elencati i percorsi di SDK predefiniti e su come modificarle se necessario.

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[Come è possibile riabilitare opzioni per gli sviluppatori dopo l'aggiornamento a iOS?](update-developer-options.md)
Un bug di iOS può causare opzioni per gli sviluppatori a scomparire dopo l'aggiornamento delle versioni di iOS, questa è stata osservata quando si passa a iOS 8. x. Questa guida descrive come le opzioni possono essere riabilitate.

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[La posizione dell'utente non funziona in iOS 8](ios8-user-location.md)
Questa guida viene descritto come modificare Info. plist per abilitare il percorso di utente in iOS 8.

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[Dove si può trovare il file dSYM per aggiungere simboli di debug ai log di arresto anomalo iOS?](symbolicate-ios-crash.md)
Questa guida descrive i passaggi di base per symbolicating i registri di arresto anomalo iOS per facilitare la diagnosi di arresti anomali del sistema. Anche collegamenti a risorse aggiuntive per più avanzate tecniche symbolication & informazioni sull'interpretazione dei log di arresto anomalo iOS.


### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[Come si impostano le variabili di ambiente di runtime Mono per i progetti iOS in Xamarin Studio?](xs-mono-runtime.md)
Se è necessario impostare qualsiasi runtime variabili di ambiente per Mono, possono essere impostate nel **opzioni progetto > eseguire > Generale** pagina.

## <a name="publishing-questions"></a>Domande di pubblicazione

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[Errore durante l'invio all'archivio di App: "Bundle non valido - opzioni non possibile essere incorporati in bitcode vengono rilevati nell'inoltro"](invalid-bundle-bitcode.md)

Invio dell'App che _richiedono_ bitcode, ad esempio le app watchOS e tvOS, deve essere effettuata con Xcode 9.

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[È possibile modificare il percorso di output del file IPA?](ipa-output-path.md)
A partire da Xamarin ciclo 7, è possibile utilizzare le destinazioni di MSBuild personalizzate per ottenere questo risultato.

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[Come copiare i file di output IPA alla cartella di ricezione TFS?](ipa-tfs.md)
Sì, questa guida viene descritto come.

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[È possibile aggiungere file o rimuovere i file da un file IPA dopo averlo compilato in Visual Studio?](modify-ipa.md)
Sì, è possibile, ma in genere è necessario firmare nuovamente il `.app` bundle dopo aver apportato la modifica. Si noti che la modifica di `.ipa` file non è necessario nell'utilizzo normale. In questo articolo viene fornito esclusivamente per scopi informativi.

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[È possibile creare un archivio .xcarchive da Visual Studio?](create-xcarchive.md)
A partire da Xamarin 4, è ora possibile creare un `.xcarchive` da Windows mediante l'impostazione di `ArchiveOnBuild` proprietà `true`.

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[Perché l'invio dell'app non riesce con l'errore: "Percorsi non consentiti ( "iTunesMetadata.plist" ) in ..." ?](itunesmetadata-disallowed-paths.md)
Questo errore è il risultato di una modifica nel processo di verifica App Store di Apple. Questo errore specifico è _non_ correlata a una particolare versione di Xamarin è stato installato, pertanto il downgrade verrà _non_ della Guida. Questa guida include collegamenti ad altre informazioni su come risolvere il problema.


## <a name="diagnosing-specific-error-messages"></a>La diagnostica dei messaggi di errore specifico

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[Errore di iOS Designer con RegisterServicePort](error-registerserviceport.md)
Gli errori con `RegisterServicePort` e messaggi di errore simili come sopra sono in genere un problema con spyware/malware nel computer. Questa guida descrive la conferma la diagnosi e informazioni sulla rimozione di malware/spyware.

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[Perché la compilazione iOS non riesce con l'errore: nessuna chiave di firma del codice iPhone valida trovata nel keychain?](no-codesigning-keys.md)
Questo messaggio di errore si verifica quando il progetto in questione esegue la ricerca di credenziali di firma codice valide ma non è possibile trovarli. La firma del codice è obbligatorio per il test e le distribuzioni nei dispositivi iOS fisici; nonché Ad-hoc & App archivio compilazioni.

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[Perché l'app iOS 9 genera l'errore: System.Exception: impossibile effettuare il marshalling dell'oggetto Objective-C?](exception-marshal-obj-c.md)
Modifiche all'API di iOS 9 richiede l'utilizzo di un costruttore di callback quando la chiamata a codice non gestito, come l'API sottostante ora si aspetta.

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[Errore di runtime: impossibile trovare o caricare l'assembly mscorlib.dll](error-mscorlib-not-found.md)
Questo problema si verifica quando il *nascosto* `.monotouch-32` e `.monotouch-64` cartelle non sono presenti il `.xcarchive` per la firma / creazione IPA, attivato l'errore di runtime.

## <a name="deprecated"></a>Deprecato

> [!IMPORTANT]
> Negli articoli seguenti si applicano a problemi che sono stati risolti nelle versioni recenti di Xamarin. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.



### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[Il file IPA è di 0 byte](ipa-zero-bytes.md)
Si sono verificati alcuni problemi noti nelle versioni precedenti di Xamarin che potrebbe causare il file IPA in Windows a 0 byte.

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[Errore IBTool: Impossibile completare l'operazione.](error-ibtool.md)
Apple [fissa](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html) questo `ibtool` bug in Xcode 6.1.1, pertanto l'aggiornamento a Xcode 6.1.1 o versione successiva è la soluzione più semplice.

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[Errore MT1009: Impossibile copiare l'assembly](error-mt1009.md)
Questo problema riguarda gli utenti che eseguono 7.2.6 di xamarin. IOS. Questo problema sarà a causa di autorizzazioni di file che richiedono privilegi più elevati durante l'installazione di xamarin. IOS con un account utente diversi account principale dello sviluppatore.

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[System. Exception AMDeviceNotificationSubscribe ha restituito...](exception-amddevicenotificationsubscribe.md)
Questo messaggio viene visualizzato in una finestra di dialogo di errore quando si avvia Visual Studio per Mac o nel `mtbserver.log` file. Si noti che questo è un problema comune. Se Visual Studio si verificano problemi di connessione all'host di compilazione Mac, sono presenti altri errori che più possono vengono visualizzati di `mtbserver.log` file.

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe non trovato rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
Questo errore può essere visualizzato nel `Mac Server Log` in Visual Studio.
