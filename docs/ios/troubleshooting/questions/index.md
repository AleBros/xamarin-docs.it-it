---
title: Domande frequenti su xamarin. IOS
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 8b90b76cad5277fe76fc476a0bcd6f600e91b256
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421828"
---
# <a name="ios-frequently-asked-questions"></a>Domande frequenti su iOS

## <a name="general-questions"></a>Domande generali

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[È possibile usare una macchina virtuale Mac con Xamarin?](mac-vm.md)
Sì, ma solo sull'hardware Mac.

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[Come è possibile eseguire il downgrade di Xcode?](downgrade-xcode.md)
Questa guida vengono forniti collegamenti per accedere a versioni precedenti di Xcode, nonché la versione più recente.

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[Dove è possibile impostare i percorsi di iOS SDK?](ios-sdk.md)
Per la maggior parte degli utenti questi vengono impostati nei percorsi appropriati automaticamente. Questa guida vengono elencati i percorsi SDK predefiniti e su come modificarli se necessario.

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[Come è possibile a riabilitare le opzioni per sviluppatori dopo l'aggiornamento di iOS?](update-developer-options.md)
Un bug di iOS può causare le opzioni per gli sviluppatori a scomparire dopo aver aggiornato le versioni di iOS, questa è stata osservata quando si passa a iOS 8. x. Questa guida descrive come le opzioni possono essere riabilitate.

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[La posizione dell'utente non funziona in iOS 8](ios8-user-location.md)
Questa guida fornisce informazioni su come modificare Info. plist per abilitare la posizione dell'utente in iOS 8.

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[Dove si può trovare il file dSYM per aggiungere simboli di debug ai log di arresto anomalo iOS?](symbolicate-ios-crash.md)
Questa guida descrive i passaggi di base per symbolicating log di arresto anomalo iOS per facilitare la diagnosi degli arresti anomali. Anche collegamenti a ulteriori risorse per più avanzato di tecniche di decodifica e le informazioni sull'interpretazione dei log di arresto anomalo iOS.


### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[Come si impostano le variabili di ambiente di runtime Mono per i progetti iOS in Xamarin Studio?](xs-mono-runtime.md)
Se è necessario impostare variabili di ambiente di alcun runtime di Mono, possono essere impostate nel **opzioni progetto > Esegui > Generale** pagina.

## <a name="publishing-questions"></a>Domande di pubblicazione

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[Errore durante l'invio all'App Store: "Bundle non valido - opzioni non possa essere incorporate in bitcode vengono rilevate nell'invio"](invalid-bundle-bitcode.md)

Inviare le app che _richiedono_ bitcode, ad esempio App watchOS e tvOS, deve essere effettuata con Xcode 9.

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[È possibile modificare il percorso di output del file IPA?](ipa-output-path.md)
A partire da Xamarin ciclo 7, è possibile utilizzare destinazioni MSBuild personalizzate per ottenere questo risultato.

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[Come è possibile copiare i file di output IPA nella cartella di rilascio TFS?](ipa-tfs.md)
Sì, questa guida viene descritto come.

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[È possibile aggiungere file a o rimuovere i file da un file IPA dopo la compilazione in Visual Studio?](modify-ipa.md)
Sì, è possibile, ma in genere è necessario firmare nuovamente il `.app` bundle dopo aver apportato la modifica. Si noti che la modifica di `.ipa` file non è necessario nell'utilizzo normale. Questo articolo viene fornito esclusivamente per scopi informativi.

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[È possibile creare un archivio xcarchive da Visual Studio?](create-xcarchive.md)
A partire da Xamarin 4, è ora possibile creare un `.xcarchive` da Windows, impostare il `ArchiveOnBuild` proprietà `true`.

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[Perché l'invio app non riesce con l'errore: "Disallowed paths ( "iTunesMetadata.plist" ) found at ..." (Percorsi non consentiti ( "iTunesMetadata.plist" ) rilevati in...)?](itunesmetadata-disallowed-paths.md)
Questo errore è il risultato di una modifica nel processo di verifica App Store di Apple. Questo errore specifico viene _non_ correlata a una versione particolare di aver installato Xamarin, pertanto il downgrade saranno _non_ Guida in linea. In questa guida il collegamento per altre informazioni su come risolvere il problema.


## <a name="diagnosing-specific-error-messages"></a>La diagnostica dei messaggi di errore specifico

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[Errore di iOS Designer con RegisterServicePort](error-registerserviceport.md)
Gli errori con `RegisterServicePort` e messaggi di errore simile come in precedenza sono in genere un problema con spyware/malware nel computer. Questa guida illustra in dettaglio per confermare la diagnosi e informazioni sulla rimozione di malware/spyware.

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[Perché la compilazione iOS non riesce con l'errore: nessuna chiave di firma del codice iPhone valida trovata nel keychain?](no-codesigning-keys.md)
Questo messaggio di errore si verifica quando il progetto in questione esegue la ricerca di credenziali di firma codice valide ma non è possibile trovarli. La firma del codice è necessaria per il test e le distribuzioni nei dispositivi iOS fisici; così come App e Ad-hoc archiviare le compilazioni.

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[Perché l'app iOS 9 restituisce l'errore: System.Exception: Failed to marshal the Objective-C object (System.Exception: marshalling oggetto Objective-C non riuscito)?](exception-marshal-obj-c.md)
Modifiche apportate all'API in iOS 9 richiede l'utilizzo di un costruttore di callback quando la chiamata a codice non gestito, come l'API sottostante a questo punto si aspetta.

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[Errore di runtime: Impossibile trovare o caricare l'assembly mscorlib.dll](error-mscorlib-not-found.md)
Questo problema si verifica quando la *nascosto* `.monotouch-32` e `.monotouch-64` cartelle non sono presenti il `.xcarchive` per la firma / creazione pacchetto IPA, ha attivato l'errore di runtime.

## <a name="deprecated"></a>Deprecato

> [!IMPORTANT]
> Gli articoli seguenti si applicano ai problemi che sono stati risolti nelle versioni recenti di Xamarin. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.



### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[Il file IPA è di 0 byte](ipa-zero-bytes.md)
Si sono verificati alcuni problemi noti nelle versioni precedenti di Xamarin che potrebbe causare il file IPA nel Windows sia pari a 0 byte.

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[Errore IBTool: Non è stato possibile completare l'operazione.](error-ibtool.md)
Apple [fissa](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html) ciò `ibtool` bug in Xcode 6.1.1, pertanto, l'aggiornamento a Xcode 6.1.1 o versione successiva è la soluzione più semplice.

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[Errore MT1009: Impossibile copiare l'assembly](error-mt1009.md)
Questo problema riguarda gli utenti che eseguono 7.2.6 di xamarin. IOS. Quindi questo problema è causato le autorizzazioni del file che necessitano di privilegi più elevati quando viene installato xamarin. IOS con un account utente diverso, l'account dello sviluppatore principale.

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[System. Exception AMDeviceNotificationSubscribe ha restituito...](exception-amddevicenotificationsubscribe.md)
Questo messaggio può essere visualizzato in una finestra di dialogo di errore quando si avvia Visual Studio per Mac o nel `mtbserver.log` file. Si noti che questo è un problema insolito. Se Visual Studio si verificano problemi durante la connessione all'host di compilazione Mac, sono presenti altri errori che sono più probabile che venga visualizzato nel `mtbserver.log` file.

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe non trovato rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
Questo errore può apparire nel `Mac Server Log` in Visual Studio.
