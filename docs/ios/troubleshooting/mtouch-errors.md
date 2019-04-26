---
title: Errori di xamarin. IOS
description: Questo documento descrive vari errori generati da mtouch, lo strumento utilizzato per aggregare le applicazioni xamarin. IOS. Gli errori sono elencati in base al codice e ha una descrizione completa.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: e6e3a989db922dc2941cca4c888c862ffe159241
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61422052"
---
# <a name="xamarinios-errors"></a>Errori di xamarin. IOS

## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx: i messaggi di errore di mtouch

Ad esempio, parametri, ambiente, mancano gli strumenti.

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
    -->

<a name="MT0000" />

### <a name="mt0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MT0000: Errore imprevisto. compilare un report sui bug a https://github.com/xamarin/xamarin-macios/issues/new

Si è verificata una condizione di errore imprevisto. Verificare [inviare un report sui bug](https://github.com/xamarin/xamarin-macios/issues/new) quante più informazioni possibili, tra cui:

* Compilazione completa dei log, con massimo livello di dettaglio (ad esempio `-v -v -v -v` nella **argomenti aggiuntivi di mtouch**);
* Un test case minimo riprodurre l'errore. e
* Tutte le informazioni di versione

Il modo più semplice per ottenere informazioni sulla versione esatta è usare il **Visual Studio per Mac** dal menu **informazioni su Visual Studio per Mac** elemento, **Mostra dettagli** pulsante e copiare/incollare il informazioni di versione (è possibile usare la **informazioni sulla copia** pulsante).

<a name="MT0001" />

### <a name="mt0001--devname-was-provided-without-any-device-specific-action"></a>MT0001: '-devname' è stato specificato senza l'intervento specifico del dispositivo

Si tratta di un avviso che viene generato se - devname viene passato a mtouch quando nessuna azione specifici del dispositivo (-logdev /-installdev /-killdev /-launchdev /-listapps) è stato richiesto.

<a name="MT0002" />

### <a name="mt0002-could-not-parse-the-environment-variable-"></a>MT0002: Non è stato possibile analizzare la variabile di ambiente *.

Questo errore si verifica se si prova a impostare una chiave di ambiente non valido = coppia variabile-valore. Il formato corretto è: `mtouch --setenv=VARIABLE=VALUE`

<a name="MT0003" />

### <a name="mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MT0003: Nome dell'applicazione ' * .exe ' è in conflitto con un nome di assembly (DLL) SDK o il prodotto.

Nome dell'assembly eseguibile e il nome dell'applicazione non può corrispondere al nome di qualsiasi dll nell'app. . Modificare il nome del file eseguibile.

<a name="MT0004" />

### <a name="mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a>MT0004: Nuova logica di refcounting richiede SGen deve essere abilitata troppo.

Se si abilita l'estensione di refcounting è necessario abilitare anche il garbage collector SGen in iOS del progetto le opzioni di compilazione (scheda Avanzate).

A partire da xamarin. IOS 7.2.1 questo requisito è stata eliminata, la nuova logica di refcounting può essere abilitata con Boehm sia SGen Garbage Collector.

<a name="MT0005" />

### <a name="mt0005-the-output-directory--does-not-exist"></a>MT0005: La directory di output * non esiste.

Creare la directory.

Questo errore non viene più generato, mtouch creerà automaticamente la directory se non esiste.

<a name="MT0006" />

### <a name="mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a>MT0006: Non vi è alcuna piattaforma di sviluppo per sistemi operativi *, usare: piattaforma = PLAT per specificare il SDK.

Xamarin. IOS non è possibile trovare la directory SDK nel percorso indicato nel messaggio di errore. Verificare che il percorso sia corretto.

<a name="MT0007" />

### <a name="mt0007-the-root-assembly--does-not-exist"></a>MT0007: L'assembly radice * non esiste.

Xamarin. IOS non è possibile trovare l'assembly nel percorso indicato nel messaggio di errore. Verificare che il percorso sia corretto.

<a name="MT0008" />

### <a name="mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a>MT0008: È necessario fornire un assembly & unica, che si trova assembly radice: *.

Più di un assembly radice passato a mtouch, anche se può essere presente solo una radice.

<a name="MT0009" />

### <a name="mt0009-error-while-loading-assemblies-"></a>MT0009: Errore durante il caricamento degli assembly: *.

Si è verificato un errore durante il caricamento di assembly i riferimenti all'assembly radice. Altre informazioni possono essere fornite nell'output di compilazione.

<a name="MT0010" />

### <a name="mt0010-could-not-parse-the-command-line-arguments-"></a>MT0010: Non è riuscito ad analizzare gli argomenti della riga di comando: *.

Si è verificato un errore durante l'analisi degli argomenti della riga di comando. Verificare che siano tutti corretti.

<a name="MT0011" />

### <a name="mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a>MT0011: * è stato creato un runtime più recente (*) rispetto a quello supportato MonoTouch.

Questo avviso viene segnalato in genere perché il progetto contiene un riferimento a una libreria di classi che non è stato creato usando la libreria di classi base di xamarin. IOS.

Esattamente un'app con .NET 4.0 SDK potrebbe non funzionare in un sistema che supportano solo .NET 2.0, una libreria compilata usando .NET 4.0 potrebbe non funzionare in xamarin. IOS, è possibile usare API non è presente in xamarin. IOS.

La soluzione generale consiste nel compilare la libreria come una libreria di classi xamarin. IOS. Questo può essere eseguita creando un nuovo progetto di libreria di classi xamarin. IOS e aggiungervi tutti i file di origine. Se non hai il codice sorgente per la libreria, si deve contattare il fornitore e richiedere che offrono una versione compatibile con xamarin. IOS della loro libreria.

<a name="MT0012" />

### <a name="mt0012-incomplete-data-is-provided-to-complete-"></a>MT0012: Vengono forniti dati incompleti per completare *.

Questo errore non viene segnalato più nella versione corrente di xamarin. IOS.

<a name="MT0013" />

### <a name="mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a>MT0013: Il supporto della profilatura richiede sgen deve essere abilitata troppo.

SGen (-sgen) devono essere abilitate se profilatura (-profilatura) è abilitato.

<a name="MT0014" />

### <a name="mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a>MT0014: IOS * SDK non supporta compilazione di applicazioni destinate a *.

Questa situazione può verificarsi nelle circostanze seguenti:

*  ARMv6 è abilitato e Xcode 4.5 o versione successiva sia installato.
*  ARMv7s è abilitato e sia installato Xcode 4.4 o versioni precedenti.

Verificare che la versione installata di Xcode supporta le architetture selezionate.

<a name="MT0015" />

### <a name="mt0015-invalid-abi--supported-abis-are-i386-x8664--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a>MT0015: ABI non è valido: *. ABI supportati sono: i386, x86_64, armv7, armv7 + llvm, armv7 + llvm + thumb2, armv7s, armv7s + llvm, armv7s + llvm + thumb2, arm64 e arm64 + llvm.

Un'interfaccia ABI non valida passata a mtouch. Specificare un ABI valido.

<a name="MT0016" />

### <a name="mt0016-the-option--has-been-deprecated"></a>MT0016: L'opzione * è stato deprecato.

L'opzione di mtouch indicato è stato deprecato e verrà ignorato.

<a name="MT0017" />

### <a name="mt0017-you-should-provide-a-root-assembly"></a>MT0017: È necessario fornire un assembly principale.

È necessario specificare un assembly radice (in genere l'eseguibile principale) quando si compila un'app.

<a name="MT0018" />

### <a name="mt0018-unknown-command-line-argument-"></a>MT0018: Argomento della riga di comando sconosciuto: *.

Mtouch non riconosce l'argomento della riga di comando indicato nel messaggio di errore.

<a name="MT0019" />

### <a name="mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a>MT0019: Solo una, [log | installare | kill | avviare] dev o --[avviare | debug] opzione System Image Manager può essere usata.

Esistono diverse opzioni per mtouch non possono essere usati contemporaneamente:

-  --logdev
-  --installdev
-  --killdev
-  --launchdev
-  --launchdebug
-  --launchsim

<a name="MT0020" />

### <a name="mt0020-the-valid-options-for--are-"></a>MT0020: Le opzioni valide per '\*'sono'\*'.

<a name="MT0021" />

### <a name="mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a>MT0021: Non è possibile compilare usando gcc / g + + (-uso-gcc) quando si usa il programma di registrazione statico (questo è il valore predefinito durante la compilazione per il dispositivo). Rimuovere--gcc di utilizzo del contrassegno o usare il programma di registrazione dinamica (-registrar: dinamico).

<a name="MT0022" />

### <a name="mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a>MT0022: Le opzioni ': non supportato: enable-generics-in-registrar' e '-registrar' non sono compatibili.

Entrambe le opzioni di rimozione `--unsupported--enable-generics-in-registrar` e `--registrar`. A partire da xamarin. IOS 7.2.1 le registrar predefinito supporta i generics.

Questo errore non verrà più visualizzato (l'argomento della riga di comando `--unsupported--enable-generics-in-registrar` è stata rimossa dal mtouch).

<a name="MT0023" />

### <a name="mt0023-application-name-exe-conflicts-with-another-user-assembly"></a>MT0023: Nome dell'applicazione ' * .exe ' è in conflitto con un altro assembly utente.

Nome dell'assembly eseguibile e il nome dell'applicazione non può corrispondere al nome di qualsiasi dll nell'app. . Modificare il nome del file eseguibile.

<a name="MT0024" />

### <a name="mt0024-could-not-find-required-file-"></a>MT0024: Impossibile trovare il file ' *'.

<a name="MT0025" />

### <a name="mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a>MT0025: È stata specificata alcuna versione del SDK. Aggiungere `--sdk=X.Y` per specificare quali iOS SDK deve essere usato per compilare l'applicazione.

<a name="MT0026" />

### <a name="mt0026-could-not-parse-the-command-line-argument--"></a>MT0026: Non è riuscito ad analizzare l'argomento della riga di comando ' *': *

<a name="MT0027" />

### <a name="mt0027-the-options--and--are-not-compatible"></a>MT0027: Delle opzioni\*'e'\*' non sono compatibili.

<a name="MT0028" />

### <a name="mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a>MT0028: Impossibile abilitare il grafico a TORTA (-a torta) quando la destinazione iOS 4.1 o versioni precedenti. Disabilitare la TORTA (-a torta: false) o impostare la destinazione di distribuzione almeno iOS 4.2

<a name="MT0029" />

### <a name="mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a>MT0029: REPL (-Abilita repl) è supportato solo nel simulatore (-sim).

REPL è supportata solo se si sta creando per il simulatore. Ciò significa che se si passa `--enable-repl` di mtouch, è necessario passare anche `--sim`.

<a name="MT0030" />

### <a name="mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a>MT0030: Il nome del file eseguibile (\*) e il nome dell'app (\*) sono diversi, questo potrebbe impedire i log di arresto anomalo del sistema Introduzione anomali con simboli in modo corretto.

Se Xcode symbolicates (traduce gli indirizzi di memoria per i nomi delle funzioni e i numeri di riga/file) il processo potrebbe non riuscire se il file eseguibile e app hanno nomi diversi (senza estensione).

Per correggere questo modificare 'Nome dell'applicazione' nelle opzioni di compilazione/iOS dell'applicazione del progetto o modifica 'l'Assembly nome' nelle opzioni di compilazione/Output del progetto.

<a name="MT0031" />

### <a name="mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a>MT0031: Gli argomenti della riga di comando '-enable-background-fetch' e '-launch-per-recupero in background ' richiede '-launchsim' troppo.

<a name="MT0032" />

### <a name="mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a>MT0032: L'opzione '-debugtrack' viene ignorato a meno che non '-debug' è specificato anche.

<a name="MT0033" />

### <a name="mt0033-a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a>MT0033: Deve fare riferimento a un progetto xamarin. IOS monotouch o DLL

<a name="MT0034" />

### <a name="mt0034-cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a>MT0034: Non può includere sia 'monotouch' e 'Dll' nello stesso progetto xamarin. IOS - '\*' viene fatto riferimento in modo esplicito, mentre '\*' fa riferimento ' *'.

<!-- MT0035 unused -->

<a name="MT0036" />

### <a name="mt0036-cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a>MT0036: Non è possibile avviare un * simulatore per un * app. Abilitare il architecture(s) corretto in iOS del progetto le opzioni di compilazione (pagina avanzate).

<a name="MT0037" />

### <a name="mt0037-monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x8664"></a>MT0037: monotouch è compatibile a 64 bit non. Fare riferimento a DLL oppure non eseguono la compilazione per un'architettura a 64 bit (ARM64 e/o x86_64).

<a name="MT0038" />

### <a name="mt0038-the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a>MT0038: Le Registrar precedente (-registrar: oldstatic | olddynamic) non sono supportati quando si fa riferimento a DLL.

<a name="MT0039" />

### <a name="mt0039-applications-targeting-armv6-cannot-reference-xamariniosdll"></a>MT0039: Le applicazioni destinate a ARMv6 non possono fare riferimento a DLL.

<a name="MT0040" />

### <a name="mt0040-could-not-find-the-assembly--referenced-by-"></a>MT0040: Non è riuscito a trovare l'assembly '\*', fa'\*'.

<a name="MT0041" />

### <a name="mt0041-cannot-reference-both-monotouchdll-and-xamariniosdll"></a>MT0041: Non è possibile fare riferimento a 'monotouch' sia 'Dll'.

<a name="MT0042" />

### <a name="mt0042-no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a>MT0042: È stato trovato alcun riferimento al metodo monotouch o DLL. Verrà aggiunto un riferimento a monotouch.

<a name="MT0043" />

### <a name="mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a>MT0043: Il garbage collector Boehm non è supportato quando si fa riferimento 'Dll'. Il garbage collector SGen è stato selezionato invece.

Solo il garbage collector SGen è supportato con progetti unificato. Assicurarsi che non sono presenti flag aggiuntivi di mtouch specificando il garbage collector Boehm.

<a name="MT0044" />

### <a name="mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a>MT0044: - listsim è supportato solo con Xcode 6.0 o versione successiva.

Installare una versione più recente di Xcode.

<a name="MT0045" />

### <a name="mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a>MT0045:: estensione è supportata solo quando si usa iOS SDK 8.0 (o versione successiva).

<!-- MT0046 is not reported anymore -->

<a name="MT0047" />

### <a name="mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a>MT0047: La destinazione di distribuzione minima per le applicazioni unificata è 5.1.1, la destinazione di distribuzione corrente è ' *'. Selezionare una destinazione di distribuzione più recente in iOS del progetto opzioni dell'applicazione.

<!-- MT0048 is not reported anymore -->

<a name="MT0049" />

### <a name="mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a>MT0049: *.framework è supportata solo se la destinazione di distribuzione è 8.0 o versione successiva. * funzionalità potrebbero non funzionare correttamente.

Il framework specificato non è supportato nella versione di iOS che si intende la destinazione di distribuzione. Aggiornare la destinazione di distribuzione per una versione di iOS più recente o rimuovere l'utilizzo del framework specificato dall'app.

<!-- MT0050 is not reported anymore -->

<a name="MT0051" />

### <a name="mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a>MT0051: Xamarin. IOS * richiede Xcode 5.0 o versioni successive. La versione corrente di Xcode (disponibile nel *) è *.

Installare una versione successiva Xcode.

<a name="MT0052" />

### <a name="mt0052-no-command-specified"></a>MT0052: Nessun comando specificato.

È stata specificata alcuna azione per mtouch.

<!-- 0053 is used by mmp -->

<a name="MT0054" />

### <a name="mt0054-unable-to-canonicalize-the-path--"></a>MT0054: Impossibile rendere canonici il percorso ' *': *

Si tratta di un errore interno. Se viene visualizzato questo errore, inviare un bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT0055" />

### <a name="mt0055-the-xcode-path--does-not-exist"></a>MT0055: Il percorso di Xcode ' *' non esiste.

Il percorso di Xcode passato usando `--sdkroot` non esiste. Specificare un percorso valido.

<a name="MT0056" />

### <a name="mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a>MT0056: Xcode non è stato trovato nel percorso predefinito (/ /Applications/xcode.app). Installare Xcode, o passare un percorso personalizzato usando-- sdkroot <path>.

<a name="MT0057" />

### <a name="mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a>MT0057: Non è possibile determinare il percorso a Xcode.app dalla radice di sdk ' *'. Specificare il percorso completo per il bundle Xcode.app.

Il percorso passato usando `--sdkroot` non corrisponde a un'app valida di Xcode. Specificare un percorso a un'app di Xcode.

<a name="MT0058" />

### <a name="mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a>MT0058: Il Xcode.app»\*' non è valido (il file '\*' non esiste).

Il percorso passato usando `--sdkroot` non corrisponde a un'app valida di Xcode. Specificare un percorso a un'app di Xcode.

<a name="MT0059" />

### <a name="mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a>MT0059: Non è stato possibile trovare il Xcode attualmente selezionato nel sistema: *

<a name="MT0060" />

### <a name="mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a>MT0060: Impossibile trovare il Xcode attualmente selezionato nel sistema. 'xcode-select-print-path' ha restituito ' *', ma tale directory non esiste.

<a name="MT0061" />

### <a name="mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a>MT0061: Nessun Xcode.app specificato (-- sdkroot utilizzando), utilizzando il sistema di Xcode come riportato da 'xcode-select-print-path': *

Questo è un avviso informativo, che spiega che Xcode sarà utilizzata, poiché è stato specificato nessuno.

<a name="MT0062" />

### <a name="mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a>MT0062: Nessun Xcode.app specificato (tramite 'xcode-select-print-path' o -- sdkroot), usando invece il valore predefinito di Xcode: *

Questo è un avviso informativo, che spiega che Xcode sarà utilizzata, poiché è stato specificato nessuno.

<a name="MT0063" />

### <a name="mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a>MT0063: Impossibile trovare il file eseguibile nell'estensione * (Nessuna voce CFBundleExecutable in Info. plist)

Ogni file Info. plist deve avere un file eseguibile (con la voce CFBundleExecutable), tuttavia, una voce deve essere generata automaticamente durante la compilazione.

Ciò indica in genere un bug in xamarin. IOS; inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un test case.

<a name="MT0064" />

### <a name="mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a>MT0064: Xamarin. IOS supporta solo Framework incorporati con i progetti unificato.

Xamarin. IOS supporta solo Framework incorporati quando si usa l'API unificata; aggiornare il progetto per usare l'API unificata.

<a name="MT0065" />

### <a name="mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a>MT0065: Xamarin. IOS supporta solo Framework incorporati quando la destinazione di distribuzione è almeno 8.0 (destinazione di distribuzione corrente: * Framework incorporati: *)

Xamarin. IOS supporta solo Framework incorporati quando la destinazione di distribuzione è almeno 8.0 (perché le versioni precedenti di iOS non supporta il Framework incorporati).

Aggiornare la destinazione di distribuzione nel file Info. plist del progetto 8.0 o versione successiva.

<a name="MT0066" />

### <a name="mt0066-invalid-build-registrar-assembly-"></a>MT0066: Assembly di registrar di compilazione non è valido: *

Ciò indica in genere un bug in xamarin. IOS; inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un test case.

<a name="MT0067" />

### <a name="mt0067-invalid-registrar-"></a>MT0067: Programma di registrazione non è valido: *

Ciò indica in genere un bug in xamarin. IOS; inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un test case.

<a name="MT0068" />

### <a name="mt0068-invalid-value-for-target-framework-"></a>MT0068: Valore non valido per framework di destinazione: *.

È stato passato un framework di destinazione non è valido usando l'argomento---framework di destinazione. Specificare un framework di destinazione valido.

<a name="MT0069" />

<!--### MT0069: currently unused -->

<a name="MT0070" />

### <a name="mt0070-invalid-target-framework--valid-target-frameworks-are-"></a>MT0070: Framework di destinazione non è valido: *. Framework di destinazione validi sono: *.

È stato passato un framework di destinazione non è valido usando l'argomento---framework di destinazione. Specificare un framework di destinazione valido.

<a name="MT0071" />

### <a name="mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT0071: Piattaforma sconosciuta: *. Ciò indica in genere un bug in xamarin. IOS; inviare un report sui bug a http://bugzilla.xamarin.com con un test case.

Ciò indica in genere un bug in xamarin. IOS; inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un test case.

<a name="MT0072" />

### <a name="mt0072-extensions-are-not-supported-for-the-platform-"></a>MT0072: Le estensioni non sono supportate per la piattaforma ' *'.

Ciò indica in genere un bug in xamarin. IOS; inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un test case.

<a name="MT0073" />

### <a name="mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MT0073: Xamarin. IOS * non supporta una destinazione di distribuzione di * (il valore minimo è *). Selezionare una destinazione di distribuzione più recente in Info. plist del progetto.

La destinazione di distribuzione minima è quello specificato nel messaggio di errore; Selezionare una destinazione di distribuzione più recente in Info. plist del progetto.

Se non è possibile aggiornare la destinazione di distribuzione, usare una versione precedente di xamarin. IOS.

<a name="MT0074" />

### <a name="mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a>MT0074: Xamarin. IOS * non supporta una destinazione di distribuzione minima di * (il valore massimo è *). Selezionare una destinazione di distribuzione precedenti nel file Info. plist del progetto o eseguire l'aggiornamento a una versione più recente di xamarin. IOS.

Xamarin. IOS non supporta l'impostazione della destinazione di distribuzione minima di una versione superiore rispetto a per che questa particolare versione di xamarin. IOS è stata compilata.

Selezionare una destinazione di distribuzione minima precedente nel file Info. plist del progetto, o eseguire l'aggiornamento a una versione più recente di xamarin. IOS.

<a name="MT0075" />

### <a name="mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a>MT0075: Architettura non valido ' *' per * progetti. Le architetture valide sono: *

È stata specificata un'architettura non è valida. Verificare che l'architettura sia valida.

<a name="MT0076" />

### <a name="mt0075-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a>MT0075: Architettura non specificato (tramite l'argomento - abi). È necessaria per un'architettura * progetti.

Ciò indica in genere un bug in xamarin. IOS; inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un test case.

<a name="MT0077" />

### <a name="mt0076-watchos-projects-must-be-extensions"></a>MT0076: I progetti WatchOS devono essere le estensioni.

Ciò indica in genere un bug in xamarin. IOS; inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un test case.

<a name="MT0078" />

### <a name="mt0077-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a>MT0077: Le compilazioni incrementali sono abilitate con una destinazione di distribuzione < 8.0 (attualmente *). Questo non è supportato (l'applicazione risultante non verrà avviate in iOS 9), in modo che la destinazione di distribuzione verrà impostata su 8.0.

Questo è un avviso che informa che la destinazione di distribuzione è stata impostata su 8.0 per questa compilazione in modo che funzionamento delle compilazioni incrementali correttamente.

Le compilazioni incrementali sono supportate solo quando la destinazione di distribuzione è almeno 8.0 (perché l'applicazione risultante non verrà avviata in iOS 9 in caso contrario).

<a name="MT0079" />

### <a name="mt0078-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a>MT0078: La versione consigliata di Xcode per xamarin. IOS * è Xcode * o versione successiva. La versione corrente di Xcode (disponibile nel *) è *.

Si tratta di un avviso che informa che la versione corrente di Xcode non è la versione consigliata di Xcode per questa versione di xamarin. IOS.

Aggiornare Xcode per garantire il comportamento ottima.

<a name="MT0080" />

### <a name="mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MT0080: La disabilitazione NewRefCount, --new-refcount:false, è deprecata.

Si tratta di un avviso che informa che la richiesta di disabilitazione il nuovo conteggio dei riferimenti (-- nuova - refcount:false) è stata ignorata.

La nuova funzionalità di conteggio dei riferimenti a questo punto è obbligatoria per tutti i progetti, e pertanto non è possibile disabilitare più.

<a name="MT0081" />

### <a name="mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a>MT0081: L'argomento della riga di comando, segnalazione di arresto anomalo download richiede inoltre - download-arresto anomalo del sistema-report-to.

<a name="MT0082" />

### <a name="mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a>MT0082: REPL (-Abilita repl) è supportato solo quando il collegamento non viene utilizzato (-nolink).

<a name="MT0083" />

### <a name="mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a>MT0083: Bitcode ASM-only non è supportata in watchOS. Usare entrambi bitcode:: marcatore o: bitcode: completo.

<a name="MT0084" />

### <a name="mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a>MT0084: Bitcode non è supportato nel simulatore. Non passare: bitcode quando si compila per simulatore.

<a name="MT0085" />

### <a name="mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a>MT0085: Nessun riferimento a ' *' è stato trovato. Verrà aggiunto automaticamente.

<a name="MT0086" />

### <a name="mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a>MT0086: Un framework di destinazione (: framework di destinazione) deve essere specificata durante la compilazione per TVOS o WatchOS.

Ciò indica in genere un bug in xamarin. IOS; inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un test case.

<a name="MT0087" />

### <a name="mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a>MT0087: Le compilazioni incrementali (-fastdev) non è supportata con il Garbage Collector Boehm. Verranno disabilitate le compilazioni incrementali.

<a name="MT0088" />

### <a name="mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a>MT0088: Il Garbage Collector deve essere in modalità cooperativa per le app watchOS. Rimuovere l'argomento-- coop: false per mtouch.

<a name="MT0089" />

### <a name="mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a>MT0089: L'opzione '\*'non è possibile ottenere il valore'\*' quando la modalità cooperativa è abilitata per il Garbage Collector.

<a name="MT0091" />

### <a name="mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MT0091: Questa versione di xamarin. IOS richiede il * SDK (forniti con Xcode *). L'aggiornamento di Xcode per ottenere i file di intestazione obbligatori o impostare il comportamento del linker gestito al collegamento Framework SDK solo (per tentare di evitare le nuove API).

Xamarin. IOS richiede i file di intestazione, dalla versione di SDK specificato nel messaggio di errore, per compilare l'applicazione. Il metodo consigliato per risolvere questo errore consiste nell'aggiornare Xcode per ottenere il SDK richiesto, questa includerà tutti i file di intestazione obbligatori. Se si dispone di più versioni di Xcode installati o si vuole usare un Xcode in un percorso non predefinito, assicurarsi di impostare il percorso di Xcode corretto nelle preferenze dell'IDE.

Un potenziale soluzione alternativa consiste nell'abilitare il linker gestito. Questa operazione rimuoverà inutilizzati API, ad esempio, nella maggior parte dei casi, la nuova API in cui i file di intestazione sono mancanti (o incompleti). Tuttavia non funzionerà se il progetto usa API che è stato introdotto in una versione di SDK più recente rispetto a quello di Xcode fornisce.

Una soluzione di ultima riconosciuti, è possibile usare una versione precedente di xamarin. IOS, che supporta il SDK al progetto richiede.

<!-- MT0092 used by mlaunch -->

<a name="MT0093" />

### <a name="mt0093-could-not-find-mlaunch"></a>MT0093: Impossibile trovare 'mlaunch'.

<!-- MT0094 is not reported anymore -->

<a name="MT0095" />

### <a name="mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a>MT0095: File AOT non possono essere copiati nella directory di destinazione {dest}: {error}

<a name="MT0096" />

### <a name="mt0096-no-reference-to-xamariniosdll-was-found"></a>MT0096: È stato trovato alcun riferimento alla DLL.

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

<a name="MT0099" />

### <a name="mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0099: Errore interno *. Inviare un report sui bug con un test case (http://bugzilla.xamarin.com).

Questo messaggio di errore viene segnalato quando un controllo di consistenza interno in xamarin. IOS ha esito negativo.

Questo indica un bug in xamarin. IOS; inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un test case.

<a name="MT0100" />

### <a name="mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0100: Destinazione di compilazione di assembly non valido: ' *'. Inviare un report sui bug con un test case (http://bugzilla.xamarin.com).

Questo messaggio di errore viene segnalato quando un controllo di consistenza interno in xamarin. IOS ha esito negativo.

Questo valore è sempre un bug in xamarin. IOS; inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un test case.

<a name="MT0101" />

### <a name="mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a>MT0101: L'assembly ' *' è specificato più volte in: gli argomenti di destinazione di compilazione assembly.

L'assembly indicato nel messaggio di errore è specificato più volte in-- argomenti assembly-build-destinazione. Assicurarsi che ogni assembly citata solo una volta.

<a name="MT0102" />

### <a name="mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a>MT0102: Gli assembly\*'e'\*' hanno lo stesso nome di destinazione ('\*'), ma con una diversa destinazioni ('\*' e ' *').

Gli assembly indicati nel messaggio di errore sono le destinazioni di compilazione in conflitto.

Ad esempio:

    --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary

In questo esempio sta tentando di creare una libreria dinamica e un framework con la stessa casa automobilistica (`MyBinary`).

<a name="MT0103" />

### <a name="mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a>MT0103: L'oggetto statico '\*' contiene più di un assembly ('\*'), ma ogni oggetto statico deve corrispondere a un assembly.

Tutti gli assembly indicati nel messaggio di errore vengono compilati in un singolo oggetto statico. Ciò non è consentito, tutti gli assembly devono essere compilati in un altro oggetto statico.

Ad esempio:

    --assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary

Questo esempio si prova a compilare un oggetto statico (`MyBinary`) costituito da due assembly (`Assembly1.dll` e `Assembly2.dll`), che non è consentito.

<a name="MT0105" />

### <a name="mt0105-no-assembly-build-target-was-specified-for-"></a>MT0105: È stata specificata alcuna destinazione di compilazione di assembly per ' *'.

Quando specifica l'assembly di compilazione di destinazione mediante `--assembly-build-target`, tutti gli assembly nell'app deve avere assegnata una destinazione di compilazione.

Questo errore viene segnalato quando l'assembly indicato nel messaggio di errore non dispone di un assembly destinazione assegnato di compilazione.

Vedere la documentazione relativa `--assembly-build-target` per altre informazioni.

<a name="MT0106" />

### <a name="mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a>MT0106: Nome di destinazione di compilazione dell'assembly '\*' non è valido: il carattere '\*' non è consentita.

Il nome di destinazione di compilazione dell'assembly deve essere un nome file valido.

Ad esempio, questi valori verranno attivano questo errore:

    --assembly-build-target:Assembly1.dll=staticobject=my/path.o

Poiché `my/path.o` non è un nome di file valido a causa il carattere separatore di directory.

<a name="MT0107" />

### <a name="mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a>MT0107: Degli assembly\*' dispone di diverse ottimizzazioni LLVM personalizzate (\*), che non è consentito quando tutte siano compilati in un unico file binario.

<a name="MT0108" />

### <a name="mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a>MT0108: Destinazione di compilazione dell'assembly ' *' non corrisponde a tutti gli assembly.

<a name="MT0109" />

### <a name="mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a>MT0109: L'assembly '{0}' è stato caricato da un percorso diverso rispetto al percorso specificato (fornito percorso: {1}, percorso effettivo: {2}).

Si tratta di un avviso che indica che un assembly cui viene fatto riferimento dall'applicazione è stato caricato da un percorso diverso rispetto a quanto richiesto.

Ciò potrebbe indicare che l'app fa riferimento a più assembly con lo stesso nome, ma da posizioni diverse, che potrebbero portare a risultati imprevisti (verrà utilizzato solo l'assembly prima).

<a name="MT0110" />

### <a name="mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a>MT0110: Le compilazioni incrementali sono state disabilitate perché questa versione di xamarin. IOS non supporta le compilazioni incrementali nei progetti che includono le librerie di associazione di terze parti e che viene compilato in bitcode.

Le compilazioni incrementali sono state disabilitate perché questa versione di xamarin. IOS non supporta le compilazioni incrementali nei progetti che includono le librerie di associazione di terze parti e che viene compilato in bitcode (progetti tvOS e watchOS).

È richiesta alcuna azione da parte dell'utente, questo messaggio è puramente informativo.

Per altre informazioni vedere bug &[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710).

Questo avviso non viene segnalato più.

<a name="MT0111" />

### <a name="mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a>MT0111: Bitcode abilitato dal momento che questa versione di xamarin. IOS non supporta compilazione watchOS i progetti che usano LLVM senza abilitare bitcode.

Bitcode abilitato automaticamente questa versione di xamarin. IOS supporta compilazione watchOS i progetti che usano LLVM senza abilitare bitcode.

È richiesta alcuna azione da parte dell'utente, questo messaggio è puramente informativo.

Per altre informazioni vedere bug &[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634).

<a name="MT0112" />

### <a name="mt0112-native-code-sharing-has-been-disabled-because-"></a>MT0112: Condivisione del codice nativo è stata disabilitata perché *

Esistono diversi motivi, è possibile disabilitare la condivisione del codice:

* Poiché la destinazione di distribuzione dell'app contenitore è precedente a iOS 8.0 (ha *)).

Condivisione del codice nativo richiede iOS 8.0 perché la condivisione del codice nativo viene implementata usando il framework di utente, che è stata introdotta con iOS 8.0.

* Poiché l'app contenitore include I18N assembly (*).

Condivisione del codice nativo è attualmente non supportata se l'app contenitore include gli assembly I18N.

* perché l'app contenitore contiene le definizioni xml personalizzato per il linker gestito (*).

Non è supportata per i progetti che usano le definizioni xml personalizzato per il linker gestito richiede la condivisione del codice nativo.

<a name="MT0113" />

### <a name="mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a>MT0113: Condivisione del codice nativo è stata disabilitata per l'estensione ' *' poiché *.

* Poiché le opzioni bitcode differiscono tra l'app contenitore (\*) e l'estensione (\*).

  Condivisione del codice nativo, è necessario che le opzioni bitcode corrispondano tra i progetti che condividono codice.

* Poiché--assembly-build-target opzioni sono diverse tra l'app contenitore (\*) e l'estensione (\*).

  Condivisione del codice nativo, è necessario che--assembly-build-target opzioni sono identiche tra i progetti che condividono codice.

  Questa condizione può verificarsi se compilazioni incrementali sono non sia abilitato o disabilitato in tutti i progetti.

* Poiché gli assembly I18N sono diversi tra l'app contenitore (\*) e l'estensione (\*).

  Condivisione del codice nativo non è attualmente supportata per le estensioni che includono gli assembly I18N.

* Poiché gli argomenti per il compilatore AOT sono diversi tra l'app contenitore (\*) e l'estensione (\*).

  Condivisione del codice nativo, è necessario che gli argomenti per il compilatore AOT non differiscono tra i progetti che condividono codice.

* Poiché gli altri argomenti per il compilatore AOT sono diversi tra l'app contenitore (\*) e l'estensione (\*).

  Condivisione del codice nativo, è necessario che gli argomenti per il compilatore AOT non differiscono tra i progetti che condividono codice.

  Questa condizione si verifica se 'Eseguire tutte le operazioni a virgola mobile a 32 come valore float a 64 bit' è diverso tra progetti.

* Poiché LLVM non è abilitato o disabilitato in entrambe le app contenitore (\*) e l'estensione (\*).

  Condivisione del codice nativo, è necessario che LLVM è abilitato o disabilitato per tutti i progetti che condividono codice.

* Poiché le impostazioni del linker gestito sono differenti tra l'app contenitore (\*) e l'estensione (\*).

  Condivisione del codice nativo, è necessario che le impostazioni del linker gestiti sono identiche per tutti i progetti che condividono codice.

* Poiché gli assembly ignorati per il linker gestito sono diversi tra l'app contenitore (\*) e l'estensione (\*).

  Condivisione del codice nativo, è necessario che le impostazioni del linker gestiti sono identiche per tutti i progetti che condividono codice.

* Poiché l'estensione dispone di definizioni xml personalizzato per il linker gestito (*).

  Non è supportata per i progetti che usano le definizioni xml personalizzato per il linker gestito richiede la condivisione del codice nativo.

* Poiché l'app contenitore non viene compilato per l'interfaccia ABI * (mentre l'estensione è la compilazione per questa interfaccia ABI).

  Condivisione del codice nativo, è necessario che l'app contenitore viene compilata per tutte le architetture qualsiasi estensione app compilato per.

  Ad esempio: questa condizione si verifica quando si compila un'estensione per ARMv7 + ARM64, ma l'app contenitore compila solo per ARM64.

* Poiché l'app contenitore è la compilazione per l'interfaccia ABI \*, che non sono compatibili con ABI dell'estensione (\*).

  Condivisione del codice nativo, è necessario che tutti i progetti di compilazione per esattamente la stessa API.

  Ad esempio: questa condizione si verifica quando si compila un'estensione per ARMv7 + llvm thumb2, ma l'app contenitore compila solo per ARMv7 + llvm.

* Poiché l'app contenitore fa riferimento all'assembly '\*'from'\*', mentre l'estensione fa riferimento a una versione diversa da ' *'.

  Condivisione del codice nativo, è necessario che tutti i progetti che condividono codice usano le stesse versioni per tutti gli assembly.

<!-- MT0114: used by mmp -->

<a name="MT0115" />

### <a name="mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a>MT0115: È consigliabile fare riferimento a simboli dinamici usando il codice (-dynamic-symbol-mode = codice) quando è abilitato bitcode.

Progetti xamarin. IOS farà spesso riferimento nativi simboli in modo dinamico, il che significa che al linker nativo potrebbe rimuovere tali simboli nativi durante il processo di collegamento nativo, perché il linker nativo non vede che vengono utilizzati questi simboli.

In genere xamarin. IOS chiederà al linker nativo di mantenere tali simboli (con il `-u symbol` flag del linker), ma quando la compilazione per bitcode al linker nativo non accetta il `-u` flag.

Xamarin. IOS ha implementato una soluzione alternativa: si genera codice nativo extra che fa riferimento a questi simboli e, pertanto al linker nativo noteranno che vengono utilizzati questi simboli. Questa operazione viene eseguita automaticamente durante la compilazione per bitcode.

Se `--dynamic-symbol-mode=linker` viene passato a mtouch, questo alternativo soluzione verrà disabilitata e xamarin. IOS verrà effettuato un tentativo di passare `-u` al linker nativo. Ciò comporterà molto probabilmente gli errori del linker nativo.

La soluzione consiste nel rimuovere il `--dynamic-symbol-mode=linker` argomento dagli argomenti aggiuntivi di mtouch nelle opzioni di compilazione del progetto.

<!-- 0116 - 0124: free to use -->

<a name="MT0116" />

### <a name="mt0116-invalid-architecture-arch-32-bit-architectures-are-not-supported-when-deployment-target-is-11-or-later-make-sure-the-project-does-not-build-for-a-32-bit-architecture"></a>MT0116: Architettura non valida: {arch}. le architetture a 32 bit non sono supportate quando la destinazione di distribuzione è 11 o versione successiva. Assicurarsi che il progetto non compilato per un'architettura a 32 bit.

iOS 11 non dispone del supporto per le applicazioni a 32 bit, in modo che non è supportata per la compilazione per un'applicazione a 32 bit quando la destinazione di distribuzione è iOS 11 o versione successiva.

Modificare l'architettura di destinazione nelle opzioni di compilazione del progetto iOS in arm64 o modificare la destinazione di distribuzione nel file Info. plist del progetto a una versione precedente di iOS.

<a name="MT0117" />

### <a name="mt0117-cant-launch-a-32-bit-app-on-a-simulator-that-only-supports-64-bit"></a>MT0117: Non è possibile avviare un'app a 32 bit in un simulatore che supporta solo a 64 bit.

<a name="MT0118" />

### <a name="mt0118-aot-files-could-not-be-found-at-the-expected-directory-msymdir"></a>MT0118: I file di AOT non è stati trovati nella directory previsto '{msymdir}'.

<!-- 0119 - 0123: free to use -->

<a name="MT0123" />

### <a name="mt0123-the-executable-assembly--does-not-reference-"></a>MT0123: L'assembly eseguibile * non fa riferimento a *.

È stato possibile trovare alcun riferimento all'assembly di piattaforma (DLL / Xamarin.TVOS.dll / Xamarin.WatchOS.dll) nell'assembly eseguibile.

Questo accade solitamente in cui non è presente codice nel progetto eseguibile che utilizza qualsiasi elemento da assembly di piattaforma. ad esempio un metodo Main vuoto (e nessun altro codice) indicherà questo errore:

```csharp
class Program {
    void Main (string[] args)
    {
    }
}
```

Usando un'API dall'assembly di piattaforma risolverà l'errore:

```csharp
class Program {
    void Main (string[] args)
    {
        System.Console.WriteLine (typeof (UIKit.UIWindow));
    }
}
```

<a name="MT0124" />

### <a name="mt0124-could-not-set-the-current-language-to-lang-according-to-langlang-exception"></a>MT0124: Impossibile impostare la lingua corrente su "{lang}" (in base alla lingua = {LANG}): {eccezione exception}

Si tratta di un avviso, a indicare che non è stato possibile impostare il linguaggio corrente per la lingua nel messaggio di errore.

La lingua corrente verrà impostata automaticamente la lingua del sistema.

<a name="MT0125" />

### <a name="mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a>MT0125: --Assembly-build-destinazione argomento della riga di comando verrà ignorato nel simulatore.

È richiesta alcuna azione, questo messaggio è puramente informativo.

<a name="MT0126" />

### <a name="mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a>MT0126: Le compilazioni incrementali sono state disabilitate perché le compilazioni incrementali non sono supportate nel simulatore.

È richiesta alcuna azione, questo messaggio è puramente informativo.

<a name="MT0127" />

### <a name="mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a>MT0127: Le compilazioni incrementali sono state disabilitate perché questa versione di xamarin. IOS non supporta le compilazioni incrementali nei progetti che includono le librerie di associazione di più di uno terze parti.

Le compilazioni incrementali sono state disabilitate automaticamente perché questa versione di xamarin. IOS non è sempre compila i progetti con più librerie di terze parti associazione correttamente.

È richiesta alcuna azione, questo messaggio è puramente informativo.

Per altre informazioni vedere bug &[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727).

<a name="MT0128" />

### <a name="mt0128-could-not-touch-the-file--"></a>MT0128: Non è stato possibile usi il file ' *': *

Si è verificato un errore quando si tocca un file (che viene eseguito per garantire l'esecuzione corretta delle compilazioni parziale).

Questo avviso molto probabile che può essere ignorato; in caso di eventuali problemi, inviare un bug (https://bugzilla.xamarin.com] (https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)) e verrà esaminato.

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx: I messaggi di errore correlati del progetto

### <a name="mt10xx-installer--mtouch"></a>MT10xx: Programma di installazione / mtouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

<a name="MT1001" />

### <a name="mt1001-could-not-find-an-application-at-the-specified-directory"></a>MT1001: Non è stato possibile trovare un'applicazione nella directory specificata

<a name="MT1002" />

### <a name="mt1002-could-not-create-symlinks-files-were-copied"></a>MT1002: Non è possibile creare collegamenti simbolici, sono stati copiati i file

<a name="MT1003" />

### <a name="mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a>MT1003: Non è stato possibile terminare l'applicazione ' *'. Potrebbe essere necessario terminare manualmente l'applicazione.

<a name="MT1004" />

### <a name="mt1004-could-not-get-the-list-of-installed-applications"></a>MT1004: Impossibile ottenere l'elenco delle applicazioni installate.

<a name="MT1005" />

### <a name="mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a>MT1005: Non è stato possibile terminare l'applicazione '\*'nel dispositivo'\*': *-potrebbe essere necessario terminare manualmente l'applicazione.

<a name="MT1006" />

### <a name="mt1006-could-not-install-the-application--on-the-device--"></a>MT1006: Non è stato possibile installare l'applicazione '\*'nel dispositivo'\*': *.

<a name="MT1007" />

### <a name="mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a>MT1007: Non è stato possibile avviare l'applicazione '\*'nel dispositivo'\*': *. È comunque possibile avviare l'applicazione manualmente toccandola.

<a name="MT1008" />

### <a name="mt1008-failed-to-launch-the-simulator"></a>MT1008: Impossibile avviare il simulatore

Questo errore viene segnalato se non è stato possibile avviare il simulatore di mtouch.   Questa situazione può verificarsi a volte perché esiste già un processo di simulatore obsoleti o inutilizzati in esecuzione.

Il comando seguente inviato nella riga di comando Unix è utilizzabile per Terminarli simulatore bloccate:

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

<a name="MT1009" />

### <a name="mt1009-could-not-copy-the-assembly--to--"></a>MT1009: Impossibile copiare l'assembly '\*'a'\*': *

Si tratta di un problema noto in alcune versioni di xamarin. IOS.

In questo caso all'utente, provare a eseguire la soluzione alternativa seguente:

```bash
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

Tuttavia, poiché questo problema è stato risolto nella versione più recente di xamarin. IOS, inviare un nuovo bug in [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con le informazioni di versione completo e l'output di log di compilazione.

<a name="MT1010" />

### <a name="mt1010-could-not-load-the-assembly--"></a>MT1010: Non è riuscito a caricare l'assembly ' *': *

<a name="MT1011" />

### <a name="mt1011-could-not-add-missing-resource-file-"></a>MT1011: Impossibile aggiungere il file di risorsa mancante: ' *'

<a name="MT1012" />

### <a name="mt1012-failed-to-list-the-apps-on-the-device--"></a>MT1012: Non è stato possibile elencare le app nel dispositivo ' *': *

<a name="MT1013" />

### <a name="mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT1013: Errore di rilevamento delle dipendenze: nessun file da confrontare. Inviare un report sui bug a http://bugzilla.xamarin.com con un test case.

Ciò indica un bug in xamarin. IOS. Inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con caes un test.

<a name="MT1014" />

### <a name="mt1014-failed-to-re-use-cached-version-of--"></a>MT1014: Non è stato possibile usare nuovamente la versione memorizzata nella cache di ' *': *.

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: Non è riuscito a creare il file eseguibile ' *': *

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: Non è riuscito a creare il file eseguibile ' *': *

<a name="MT1016" />

### <a name="mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a>MT1016: Impossibile creare il file si noti che poiché esiste già una directory con lo stesso nome.

Rimuovere la directory `NOTICE` dal progetto.

<a name="MT1017" />

### <a name="mt1017-failed-to-create-the-notice-file-"></a>MT1017: Impossibile creare il file Avviso: *.

<a name="MT1018" />

### <a name="mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a>MT1018: L'applicazione controlli di firma codice non riusciti e non possono essere installato nel dispositivo ' *'. Verificare i certificati, profili di provisioning e gli ID del bundle. Probabilmente il dispositivo non fa parte del profilo di provisioning selezionato (errore: 0xe8008015).

<a name="MT1019" />

### <a name="mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a>MT1019: L'applicazione ha i diritti non supportati dal profilo di provisioning corrente e non è stato possibile installare nel dispositivo ' *'. Per informazioni più dettagliate controllare i Log del dispositivo iOS (errore: 0xe8008016).

Questa situazione può verificarsi se:

* L'applicazione dispone dei diritti che non supporta il profilo di provisioning corrente.
  Possibili soluzioni:
  - Specificare un diverso profilo di provisioning che supporta gli Entitlement le esigenze dell'applicazione.
  - Rimuovere i diritti non supportati nel profilo di provisioning corrente.
* Il dispositivo che si sta tentando di distribuire non è incluso nel profilo di provisioning in uso.
  Possibili soluzioni:
  - Creare una nuova app da un modello in Xcode, selezionare lo stesso profilo di provisioning e distribuire al dispositivo stesso. In alcuni casi Xcode può aggiornare automaticamente i profili di provisioning con i nuovi dispositivi (in altri casi che xcode chiederà cosa fare).
  -Vai a iOS Dev Center e aggiornare il profilo di provisioning con il nuovo dispositivo, quindi scaricare il profilo di provisioning aggiornato nel computer.

Nella maggior parte dei casi che visualizzare altre informazioni sull'errore di Log del dispositivo iOS, che consentono la diagnosi del problema.

<a name="MT1020" />

### <a name="mt1020-failed-to-launch-the-application--on-the-device--"></a>MT1020: Non è stato possibile avviare l'applicazione '\*'nel dispositivo'\*': *

<a name="MT1021" />

### <a name="mt1021-could-not-copy-the-file--to--2"></a>MT1021: Impossibile copiare il file '\*'a'\*': {2}

Impossibile copiare un file. Il messaggio di errore dell'operazione di copia contiene ulteriori informazioni sull'errore.

<a name="MT1022" />

### <a name="mt1022-could-not-copy-the-directory--to--2"></a>MT1022: Impossibile copiare la directory '\*'a'\*': {2}

Impossibile copiare una directory. Il messaggio di errore dell'operazione di copia contiene ulteriori informazioni sull'errore.

<a name="MT1023" />

### <a name="mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a>MT1023: Non è stato possibile comunicare con il dispositivo per trovare l'applicazione ' *': *

Si è verificato un errore durante il tentativo di cercare un'applicazione nel dispositivo.

Operazioni da eseguire risolvere il problema:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.

<a name="MT1024" />

### <a name="mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a>MT1024: Non è stato possibile verificare la firma dell'applicazione nel dispositivo ' *'. Assicurarsi che il profilo di provisioning viene installato e non scaduto (errore: 0xe8008017).

Il dispositivo ha rifiutato l'installazione dell'applicazione perché non è stato possibile verificare la firma.

Assicurarsi che il profilo di provisioning viene installato e non scaduto.

<a name="MT1025" />

### <a name="mt1025-could-not-list-the-crash-reports-on-the-device-"></a>MT1025: Non è stato possibile elencare i report sugli arresti anomali nel dispositivo *.

Si è verificato un errore durante il tentativo di elencare i report di arresto anomalo del sistema nel dispositivo.

Operazioni da eseguire risolvere il problema:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes (questo comando rimuoverà qualsiasi report sugli arresti anomali dal dispositivo).

<a name="MT1026" />

### <a name="mt1026-could-not-download-the-crash-report--from-the-device-"></a>MT1026: Non è riuscito a scaricare il report di arresto anomalo del sistema * dal dispositivo *.

Si è verificato un errore durante il tentativo di scaricare il report sugli arresti anomali dal dispositivo.

Operazioni da eseguire risolvere il problema:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes (questo comando rimuoverà qualsiasi report sugli arresti anomali dal dispositivo).

<a name="MT1027" />

### <a name="mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a>MT1027: Non è possibile usare Xcode 7 + per avviare le applicazioni nei dispositivi con iOS * (Xcode 7 supporta solo iOS 6 +).

Non è possibile usare Xcode 7 + per avviare le applicazioni nei dispositivi con versione di iOS precedente alla 6.0.

Utilizza una versione precedente di Xcode, o toccare l'app manualmente per avviarla.

<a name="MT1028" />

### <a name="mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a>MT1028: Specifica del dispositivo non è valido: ' *'. Previsto 'ios', 'watchos' o 'tutti'.

Specifiche del dispositivo passato usando-- dispositivo non è valido. I valori validi sono: 'ios', 'watchos' o 'tutti'.

<a name="MT1029" />

### <a name="mt1029-could-not-find-an-application-at-the-specified-directory-"></a>MT1029: Non è stato possibile trovare un'applicazione nella directory specificata: *

Il percorso dell'applicazione passato a launchdev: non esiste. Specificare un bundle dell'app valido.

<a name="MT1030" />

### <a name="mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a>MT1030: L'avvio di applicazioni nel dispositivo usando un identificatore del bundle è deprecata. Passare il percorso completo per il pacchetto da avviare.

È consigliabile per passare il percorso per le app devono essere avviati sul dispositivo anziché solo l'id del bundle.

<a name="MT1031" />

### <a name="mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1031: Non è stato possibile avviare l'app '\*'nel dispositivo'\*' perché il dispositivo è bloccato. . Sbloccare il dispositivo e riprovare.

. Sbloccare il dispositivo e riprovare.

<a name="MT1032" />

### <a name="mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a>MT1032: Questo file eseguibile dell'applicazione potrebbe essere troppo grande (* MB) da eseguire nel dispositivo. Se è stato abilitato bitcode si potrebbe voler disabilitarla per lo sviluppo, è richiesto solo per inviare le applicazioni ad Apple.

<a name="MT1033" />

### <a name="mt1033-could-not-uninstall-the-application--from-the-device--"></a>MT1033: Non è stato possibile disinstallare l'applicazione '\*'dal dispositivo'\*': *

<!-- 1034 used by mmp -->

<a name="MT1035" />

### <a name="mt1035-cannot-include-different-versions-of-the-framework-name"></a>MT1035: Non è possibile includere versioni diverse del framework '{name}'

Non è possibile eseguire il collegamento con versioni diverse dello stesso framework.

Ciò accade solitamente quando un'estensione fa riferimento a una versione diversa di un framework da quella dell'app contenitore (possibilmente tramite un assembly di binding di terze parti).

Questo errore è seguito saranno presenti più [MT1036](#MT1036) Elenca i percorsi per ogni framework di diversi errori.

<a name="MT1036" />

### <a name="mt1036-framework-name-included-from-path-related-to-previous-error"></a>MT1036: Framework '{name}' incluso da: {path} (correlato all'errore precedente)

Questo errore viene segnalato solo in combinazione con [MT1036](#MT1036). Vedi [MT1036](#MT1036) per altre informazioni.

### <a name="mt11xx-debug-service"></a>MT11xx: Eseguire il debug del servizio

<!--
  MT11xx DebugService.cs
  -->

<a name="MT1101" />

### <a name="mt1101-could-not-start-app"></a>MT1101: Non è possibile avviare l'app

<a name="MT1102" />

### <a name="mt1102-could-not-attach-to-the-app-to-kill-it-"></a>MT1102: Impossibile connettersi all'app (per terminarlo): *

<a name="MT1103" />

### <a name="mt1103-could-not-detach"></a>MT1103: Impossibile disconnettersi

<a name="MT1104" />

### <a name="mt1104-failed-to-send-packet-"></a>MT1104: Non è riuscito a inviare il pacchetto: *

<a name="MT1105" />

### <a name="mt1105-unexpected-response-type"></a>MT1105: Tipo di risposta non prevista

<a name="MT1106" />

### <a name="mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a>MT1106: Impossibile ottenere l'elenco delle applicazioni nel dispositivo: Timeout della richiesta.

<a name="MT1107" />

### <a name="mt1107-application-failed-to-launch-"></a>MT1107: Impossibile avviare l'applicazione: *

Verificare se il dispositivo venga bloccato.

Se si distribuisce un'app aziendale oppure usando un profilo di provisioning gratuito, è possibile considerare attendibile lo sviluppatore (questo concetto verrà spiegato <a href="https://stackoverflow.com/a/30726375/183422">qui</a>).

<a name="MT1108" />

### <a name="mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a>MT1108: Impossibile trovare gli strumenti di sviluppo per questo dispositivo XX (AA).

Alcune operazioni da mtouch richiedono la <tt>DeveloperDiskImage.dmg</tt> file sia presente.   Questo file è incluso in Xcode e si trova rispetto al SDK che si usa per la compilazione, in genere il <tt>Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg</tt>.

Questo errore può verificarsi uno perché non è un DeveloperDiskImage.dmg corrispondenti al dispositivo di cui si è connessi.

<a name="MT1109" />

### <a name="mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1109: Applicazione non è stato possibile avviare il dispositivo è bloccato. . Sbloccare il dispositivo e riprovare.

Verificare se il dispositivo venga bloccato.

<a name="MT1110" />

### <a name="mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a>MT1110: Applicazione non è stato possibile avviare a causa di restrizioni di sicurezza di iOS. Verificare che lo sviluppatore è considerato attendibile.

Se si distribuisce un'app aziendale oppure usando un profilo di provisioning gratuito, è possibile considerare attendibile lo sviluppatore (questo concetto verrà spiegato <a href="https://stackoverflow.com/a/30726375/183422">qui</a>).

<a name="MT1111" />

### <a name="mt1111-application-launched-successfully-but-its-not-possible-to-wait-for-the-app-to-exit-as-requested-because-its-not-possible-to-detect-app-termination-when-launching-using-gdbserver"></a>MT1111: Applicazione avviata correttamente, ma non 's possibili di attesa per l'app uscire dalla come richiesto perché non è possibile rilevare la terminazione di app quando si avvia usando gdbserver.

### <a name="mt12xx-simulator"></a>MT12xx: Simulatore

<!--
  MT12xx simcontroller.cs
  -->

<a name="MT1201" />

### <a name="mt1201-could-not-load-the-simulator-"></a>MT1201: Non è riuscito a caricare il simulatore: *

<a name="MT1202" />

### <a name="mt1202-invalid-simulator-configuration-"></a>MT1202: Configurazione di simulatore non è valido: *

<a name="MT1203" />

### <a name="mt1203-invalid-simulator-specification-"></a>MT1203: Specifica il simulatore non è valido: *

<a name="MT1204" />

### <a name="mt1204-invalid-simulator-specification--runtime-not-specified"></a>MT1204: Specifica il simulatore non valido ' *': runtime non è specificato.

<a name="MT1205" />

### <a name="mt1205-invalid-simulator-specification--device-type-not-specified"></a>MT1205: Specifica il simulatore non valido ' *': tipo di dispositivo non è specificato.

<a name="MT1206" />

### <a name="mt1206-could-not-find-the-simulator-runtime-"></a>MT1206: Non è riuscito a trovare il runtime di simulatore ' *'.

<a name="MT1207" />

### <a name="mt1207-could-not-find-the-simulator-device-type-"></a>MT1207: Non è riuscito a trovare il tipo di dispositivo simulatore ' *'.

<a name="MT1208" />

### <a name="mt1208-could-not-find-the-simulator-runtime-"></a>MT1208: Non è riuscito a trovare il runtime di simulatore ' *'.

<a name="MT1209" />

### <a name="mt1209-could-not-find-the-simulator-device-type-"></a>MT1209: Non è riuscito a trovare il tipo di dispositivo simulatore ' *'.

<a name="MT1210" />

### <a name="mt1210-invalid-simulator-specification--unknown-key-"></a>MT1210: Specifica il simulatore non è valido: \*, chiave sconosciuto '\*'

<a name="MT1211" />

### <a name="mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a>MT1211: La versione di simulatore»\*'non supporta il tipo di simulatore'\*'

<a name="MT1212" />

### <a name="mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a>MT1212: Non è riuscito a creare una versione del simulatore in cui digitare = * runtime e = *.

<a name="MT1213" />

### <a name="mt1213-invalid-simulator-specification-for-xcode-4-"></a>MT1213: Specifica il simulatore non è valido per Xcode 4: *

<a name="MT1214" />

### <a name="mt1214-invalid-simulator-specification-for-xcode-5-"></a>MT1214: Specifica il simulatore non è valido per Xcode 5: *

<a name="MT1215" />

### <a name="mt1215-invalid-sdk-specified-"></a>MT1215: SDK non valido specificato: *

<a name="MT1216" />

### <a name="mt1216-could-not-find-the-simulator-udid-"></a>MT1216: Non è stato trovato il simulatore UDID ' *'.

<a name="MT1217" />

### <a name="mt1217-could-not-load-the-app-bundle-at-"></a>MT1217: Non è riuscito a caricare il bundle dell'app in ' *'.

<a name="MT1218" />

### <a name="mt1218-no-bundle-identifier-found-in-the-app-at-"></a>MT1218: Nessun identificatore del bundle disponibile nell'app al ' *'.

<a name="MT1219" />

### <a name="mt1219-could-not-find-the-simulator-for-"></a>MT1219: Non è stato trovato il simulatore per ' *'.

<a name="MT1220" />

### <a name="mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a>MT1220: Impossibile trovare il runtime più recente di simulatore per il dispositivo ' *'.

Questo in genere indica un problema con Xcode.

Operazioni da eseguire risolvere il problema:

* Usare una sola volta il simulatore in Xcode.
* Passare un'esplicita versione del SDK tramite sdk - <version>.
* Reinstallare Xcode.

<a name="MT1221" />

### <a name="mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a>MT1221: Non è stato trovato il simulatore iPhone associato per il simulatore di WatchOS ' *'.

Quando si avvia un'app WatchOS in un simulatore di WatchOS, deve essere presente un coppia simulatore iOS anche.

Guarda i simulatori possono essere abbinati a iOS simulatori tramite interfaccia utente di dispositivi di Xcode (-finestra > dispositivi).

### <a name="mt13xx-linkwith"></a>MT13xx: [LinkWith]

<!--
  MT13xx [LinkWith]
  -->

<a name="MT1301" />

### <a name="mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a>MT1301: La libreria nativa `*` (\*) è stata ignorata perché non corrisponde la architecture(s) compilazione corrente (\*)

<a name="MT1302" />

### <a name="mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a>MT1302: Non è possibile estrarre la libreria nativa ' *' da '+'. Assicurarsi che la libreria nativa è stata correttamente incorporata nell'assembly gestiti (se l'assembly è stato creato tramite un progetto di associazione, la libreria nativa deve essere incluso nel progetto e la proprietà azione compilazione deve essere 'ObjcBindingNativeLibrary').

<a name="MT1303" />

### <a name="mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a>MT1303: Non è stato possibile decomprimere il framework nativo '\*'from'\*'. Esaminare il log di compilazione per altre informazioni del comando nativo 'zip'.

Impossibile decomprimere il framework nativo specificato dalla libreria di binding.

Esaminare il registro bulid per altre informazioni sull'errore dal comando nativa 'zip'.

<a name="MT1304" />

### <a name="mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a>MT1304: Il framework incorporato ' *' in * non è valido: non contiene un file Info. plist.

Framework incorporati specificato non contiene un file Info. plist e non è pertanto un framework valido.

Assicurarsi che il framework è valido.

<a name="MT1305" />

### <a name="mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a>MT1305: La libreria di binding '\*' contiene un framework per utente (\*), ma Framework incorporati utente richiede iOS 8.0 (destinazione di distribuzione corrente è *). Impostare la destinazione di distribuzione nel file Info. plist per almeno 8.0.

La libreria di binding specificati contiene un framework incorporato, ma xamarin. IOS supporta solo Framework incorporati su iOS 8.0 o versione successiva.

Impostare la destinazione di distribuzione nel file Info. plist per almeno 8.0 per risolvere questo errore (o non usare Framework incorporati).

### <a name="mt14xx-crash-reports"></a>MT14xx: Report sugli arresti anomali

<!--
  MT14xx    CrashReports.cs
  -->

<a name="MT1400" />

### <a name="mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a>MT1400: Impossibile aprire il servizio di report di arresto anomalo del sistema: AFCConnectionOpen restituito *

Si è verificato un errore durante il tentativo di accedere ai report sugli arresti anomali dal dispositivo.

Operazioni da eseguire risolvere il problema:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes (questo comando rimuoverà qualsiasi report sugli arresti anomali dal dispositivo).

<a name="MT1401" />

### <a name="mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a>MT1401: Impossibile chiudere il servizio di report di arresto anomalo del sistema: AFCConnectionClose restituito *

Si è verificato un errore durante il tentativo di accedere ai report sugli arresti anomali dal dispositivo.

Operazioni da eseguire risolvere il problema:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes (questo comando rimuoverà qualsiasi report sugli arresti anomali dal dispositivo).

<a name="MT1402" />

### <a name="mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a>MT1402: Non è possibile leggere le informazioni sul file per *: AFCFileInfoOpen restituito *

Si è verificato un errore durante il tentativo di accedere ai report sugli arresti anomali dal dispositivo.

Operazioni da eseguire risolvere il problema:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes (questo comando rimuoverà qualsiasi report sugli arresti anomali dal dispositivo).

<a name="MT1403" />

### <a name="mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a>MT1403: Non è possibile leggere segnalazione di arresto anomalo: AFCDirectoryOpen (*) ha restituito: *

Si è verificato un errore durante il tentativo di accedere ai report sugli arresti anomali dal dispositivo.

Operazioni da eseguire risolvere il problema:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes (questo comando rimuoverà qualsiasi report sugli arresti anomali dal dispositivo).

<a name="MT1404" />

### <a name="mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a>MT1404: Non è possibile leggere segnalazione di arresto anomalo: AFCFileRefOpen (*) ha restituito: *

Si è verificato un errore durante il tentativo di accedere ai report sugli arresti anomali dal dispositivo.

Operazioni da eseguire risolvere il problema:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes (questo comando rimuoverà qualsiasi report sugli arresti anomali dal dispositivo).

<a name="MT1405" />

### <a name="mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a>MT1405: Non è possibile leggere segnalazione di arresto anomalo: AFCFileRefRead (*) ha restituito: *

Si è verificato un errore durante il tentativo di accedere ai report sugli arresti anomali dal dispositivo.

Operazioni da eseguire risolvere il problema:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes (questo comando rimuoverà qualsiasi report sugli arresti anomali dal dispositivo).

<a name="MT1406" />

### <a name="mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a>MT1406: Impossibile elencare i report sugli arresti anomali: AFCDirectoryOpen (*) ha restituito: *

Si è verificato un errore durante il tentativo di accedere ai report sugli arresti anomali dal dispositivo.

Operazioni da eseguire risolvere il problema:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes (questo comando rimuoverà qualsiasi report sugli arresti anomali dal dispositivo).

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx: MachO

<!--
  MT16xx    MachO.cs
  -->

<a name="MT1600" />

### <a name="mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1600: Non una Mach-O libreria dinamica (intestazione sconosciuta '0 x *'): *.

Si è verificato un errore durante l'elaborazione di libreria di collegamento dinamico in questione.

Assicurarsi che la libreria dinamica è una libreria dinamica Mach-O valida.

Il formato di una libreria può essere verificato utilizzando il `file` comando dal terminale:

    file -arch all -l /path/to/library.dylib

<a name="MT1601" />

### <a name="mt1601-not-a-static-library-unknown-header--"></a>MT1601: Non è una libreria statica (intestazione sconosciuta ' *'): *.

Si è verificato un errore durante l'elaborazione della libreria statica in questione.

Assicurarsi che la libreria statica è una libreria statica Mach-O valida.

Il formato di una libreria può essere verificato utilizzando il `file` comando dal terminale:

    file -arch all -l /path/to/library.a

<a name="MT1602" />

### <a name="mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1602: Non una Mach-O libreria dinamica (intestazione sconosciuta '0 x *'): *.

Si è verificato un errore durante l'elaborazione di libreria di collegamento dinamico in questione.

Assicurarsi che la libreria dinamica è una libreria dinamica Mach-O valida.

Il formato di una libreria può essere verificato utilizzando il `file` comando dal terminale:

    file -arch all -l /path/to/library.dylib

<a name="MT1603" />

### <a name="mt1603-unknown-format-for-fat-entry-at-position--in-"></a>MT1603: Formato sconosciuto per la voce fat nella posizione * in *.

Si è verificato un errore durante l'elaborazione dell'archivio fat in questione.

Assicurarsi che l'archivio fat non è valido.

Il formato di un archivio fat può essere verificato utilizzando il `file` comando dal terminale:

    file -arch all -l /path/to/file

<a name="MT1604" />

### <a name="mt1604-file-of-type--is-not-a-macho-file-"></a>MT1604: File di tipo * non è un file MachO (*).

Si è verificato un errore durante l'elaborazione del file MachO in questione.

Assicurarsi che il file è una libreria dinamica Mach-O valida.

Il formato di un file può essere verificato utilizzando il `file` comando dal terminale:

    file -arch all -l /path/to/file

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx: Messaggi di errore del linker

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

<a name="MT2001" />

### <a name="mt2001-could-not-link-assemblies"></a>MT2001: Non è possibile collegare gli assembly

Questo errore indica che il linker gestito si è verificato un errore imprevisto, ad esempio, un'eccezione e non è stato possibile completare o salvare l'assembly in fase di elaborazione. Altre informazioni sull'errore esatto farà parte del log di compilazione, ad esempio

```
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

È importante archiviare una segnalazione di bug per tali problemi. Nella maggior parte dei casi, una soluzione alternativa è possibile specificare fino a quando non viene pubblicata una correzione adeguata. Le informazioni sopra riportate sono critiche (insieme a un test case e/o l'assembly binairy) per risolvere il problema.

<a name="MT2002" />

### <a name="mt2002-can-not-resolve-reference-"></a>MT2002: Non può risolvere il riferimento: *

<a name="MT2003" />

### <a name="mt2003-option--will-be-ignored-since-linking-is-disabled"></a>MT2003: Opzione ' *' verrà ignorato perché il collegamento è disabilitato

<a name="MT2004" />

### <a name="mt2004-extra-linker-definitions-file--could-not-be-located"></a>MT2004: File di definizione del linker extra ' *' non è stata trovata.

<a name="MT2005" />

### <a name="mt2005-definitions-from--could-not-be-parsed"></a>MT2005: Le definizioni di ' *' non può essere analizzata.

<a name="MT2006" />

### <a name="mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a>MT2006: Impossibile caricare file mscorlib. dll: *. Reinstallare xamarin. IOS.

In genere, ciò indica che si è verificato un problema con l'installazione di xamarin. IOS. Provare a reinstallare xamarin. IOS.

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

<a name="MT2010" />

### <a name="mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MT2010: HttpMessageHandler sconosciuto `*`. I valori validi sono HttpClientHandler (impostazione predefinita), CFNetworkHandler o NSUrlSessionHandler

<a name="MT2011" />

### <a name="mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a>MT2011: TlsProvider sconosciuto `*`.  I valori validi sono default o appletls.

Il valore passato `tls-provider=` non è un provider TLS (Transport Layer Security) valido.

Il `default` e `appletls` sono gli unici valori validi ed entrambi rappresentano la stessa opzione, che consiste nel fornire il SSL/TLS supportata tramite l'API di TLS Apple nativa.

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

<a name="MT2015" />

### <a name="mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a>MT2015: HttpMessageHandler valido `*` per watchOS. L'unico valore valido è NSUrlSessionHandler.

Si tratta di un avviso che si verifica perché il file di progetto specifica un HttpMessageHandler non valido.

Le versioni precedenti di questi strumenti di anteprima generate per impostazione predefinita un valore non valido nel file di progetto.

Per risolvere questo problema, aprire il file di progetto in un editor di testo e rimuovere tutti i nodi HttpMessageHandler da XML.

<a name="MT2016" />

### <a name="mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a>MT2016: Non è valido TlsProvider `legacy` opzione. L'unico valore valido `appletls` verrà utilizzato.

Il `legacy` provider, che è stato un SSLv3 completamente gestito / TLSv1 unico provider, non viene fornita con xamarin. IOS più. I progetti che utilizzavano il vecchio provider e a questo punto compilare con la versione più recente `appletls` uno.

Per risolvere questo problema, aprire il file di progetto in un editor di testo e rimuovere tutti i ' MtouchTlsProvider ' nodi da XML.

<a name="MT2017" />

### <a name="mt2017-could-not-process-xml-description"></a>MT2017: Non è riuscito a elaborare descrizione XML.

Ciò significa che si verifica un errore nel [file di configurazione del linker XML personalizzato](https://developer.xamarin.com/guides/cross-platform/advanced/custom_linking/) è stata specificata, consultare il file.

<a name="MT2018" />

### <a name="mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a>MT2018: L'assembly '\*' viene fatto riferimento da due percorsi diversi: '\*' e ' *'.

L'assembly indicato nel messaggio di errore viene caricato da più posizioni. Assicurarsi di usare sempre la stessa versione di un assembly.

<a name="MT2019" />

### <a name="mt2019-can-not-load-the-root-assembly-"></a>MT2019: Impossibile caricare l'assembly radice ' *'

Impossibile caricare l'assembly radice. Verificare che il percorso nel messaggio di errore fa riferimento a un file esistente, e che sia un assembly .NET valido.

<a name="MT202x" />

### <a name="mt202x-binding-optimizer-failed-processing-"></a>MT202x: Associazione di Query Optimizer non è riuscita l'elaborazione `...`.

Un evento imprevisto si è verificato quando si tenta di ottimizzare generato codice di associazione. L'elemento che ha causato il problema è denominato nel messaggio di errore. Per risolvere il problema, l'assembly denominato (o che contiene il tipo o un metodo denominato) dovranno essere forniti un [report sui bug](http://bugzilla.xamarin.com) insieme a un log di compilazione completa con livello di dettaglio abilitata (vale a dire `-v -v -v -v` nel **aggiuntivi di mtouch gli argomenti**).

L'ultima cifra `x` sarà:
* `0` per un nome di assembly.
* `1` per un nome di tipo.
* `3` per un nome di metodo.

<a name="MT2030" />

### <a name="mt2030-remove-user-resources-failed-processing-"></a>MT2030: Rimuovere le risorse utente non è riuscita l'elaborazione `...`.

Un evento imprevisto si è verificato durante il tentativo di rimuovere le risorse utente. L'assembly che ha causato il problema è denominato nel messaggio di errore. Per risolvere il problema l'assembly dovrà essere fornito una [report sui bug](http://bugzilla.xamarin.com) insieme a un log di compilazione completa con livello di dettaglio abilitata (vale a dire `-v -v -v -v` nel **argomenti aggiuntivi di mtouch**).

Le risorse utente sono i file inclusi all'interno di assembly (come risorse) che devono essere estratti, in fase di compilazione, alla creazione del pacchetto dell'applicazione. vale a dire:

* `__monotouch_content_*` e `__monotouch_pages_*` risorse; e
* Le librerie native incorporati all'interno di un assembly di binding.

<a name="MT2040" />

### <a name="mt2040-default-httpmessagehandler-setter-failed-processing-"></a>MT2040: Setter HttpMessageHandler predefinito non è stato possibile elaborare `...`.

Un evento imprevisto si è verificato durante il tentativo di impostare il valore predefinito `HttpMessageHandler` per l'applicazione. Inviare un [report sui bug](http://bugzilla.xamarin.com) insieme a un log di compilazione completa con livello di dettaglio abilitata (vale a dire `-v -v -v -v` nel **argomenti aggiuntivi di mtouch**).

<a name="MT2050" />

### <a name="mt2050-code-remover-failed-processing-"></a>MT2050: Metodo di rimozione di codice non è riuscita l'elaborazione `...`.

Un evento imprevisto si è verificato durante il tentativo di rimuovere il codice dal BCL shipping con l'applicazione. Inviare un [report sui bug](http://bugzilla.xamarin.com) insieme a un log di compilazione completa con livello di dettaglio abilitata (vale a dire `-v -v -v -v` nel **argomenti aggiuntivi di mtouch**).

<a name="MT2060" />

### <a name="mt2060-sealer-failed-processing-"></a>MT2060: Elaborazione non riuscita sealer `...`.

Un evento imprevisto si è verificato durante il tentativo di bloccare i tipi o metodi (finale) o quando devirtualizing alcuni metodi. L'assembly che ha causato il problema è denominato nel messaggio di errore. Per risolvere il problema l'assembly dovrà essere fornito una [report sui bug](http://bugzilla.xamarin.com) insieme a un log di compilazione completa con livello di dettaglio abilitata (vale a dire `-v -v -v -v` nel **argomenti aggiuntivi di mtouch**).

<a name="MT2070" />

### <a name="mt2070-metadata-reducer-failed-processing-"></a>MT2070: Elaborazione non riuscita Reducer metadati `...`.

Un evento imprevisto si è verificato durante il tentativo di ridurre i metadati dall'applicazione. L'assembly che ha causato il problema è denominato nel messaggio di errore. Per risolvere il problema l'assembly dovrà essere fornito una [report sui bug](http://bugzilla.xamarin.com) insieme a un log di compilazione completa con livello di dettaglio abilitata (vale a dire `-v -v -v -v` nel **argomenti aggiuntivi di mtouch**).

<a name="MT2080" />

### <a name="mt2080-marknsobjects-failed-processing-"></a>MT2080: L'elaborazione non è stato possibile MarkNSObjects `...`.

Un evento imprevisto si è verificato durante il tentativo di contrassegnare `NSObject` sottoclassi dall'applicazione. L'assembly che ha causato il problema è denominato nel messaggio di errore. Per risolvere il problema l'assembly dovrà essere fornito una [report sui bug](http://bugzilla.xamarin.com) insieme a un log di compilazione completa con livello di dettaglio abilitata (vale a dire `-v -v -v -v` nel **argomenti aggiuntivi di mtouch**).

<a name="MT2090" />

### <a name="mt2090-inliner-failed-processing-"></a>MT2090: Elaborazione non riuscita inliner `...`.

Un evento imprevisto si è verificato durante il tentativo di codice inline dall'applicazione. L'assembly che ha causato il problema è denominato nel messaggio di errore. Per risolvere questo problema l'assembly dovrà essere fornito una [report sui bug](https://bugzilla.xamarin.com) insieme a un log di compilazione completa con livello di dettaglio abilitata (vale a dire `-v -v -v -v` nel **argomenti aggiuntivi di mtouch**).

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

<a name="MT2100" />

### <a name="mt2100-smart-enum-conversion-preserver-failed-processing-"></a>MT2100: Mantieni di conversione smart Enum non è riuscita l'elaborazione `...`.

Un evento imprevisto si è verificato durante il tentativo di contrassegnare i metodi di conversione per le enumerazioni smart dall'applicazione. L'assembly che ha causato il problema è denominato nel messaggio di errore. Per risolvere questo problema l'assembly dovrà essere fornito una [report sui bug](https://bugzilla.xamarin.com) insieme a un log di compilazione completa con livello di dettaglio abilitata (vale a dire `-v -v -v -v` nel **argomenti aggiuntivi di mtouch**).

<a name="MT2101" />

### <a name="mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a>MT2101: Non è possibile risolvere il riferimento '\*', cui viene fatto riferimento dal metodo'\*' in ' *'.

Un riferimento all'assembly non valido rilevato durante l'elaborazione del metodo indicato nel messaggio di errore.

L'assembly che ha causato il problema è denominato nel messaggio di errore. Per risolvere il problema l'assembly dovrà essere fornito una [report sui bug](https://bugzilla.xamarin.com) insieme a un log di compilazione completa con livello di dettaglio abilitata (vale a dire `-v -v -v -v` nel **argomenti aggiuntivi di mtouch**).

<a name="MT2102" />

### <a name="mt2102-error-processing-the-method--in-the-assembly--"></a>MT2102: Errore durante l'elaborazione del metodo '\*'nell'assembly'\*': *

Un evento imprevisto si è verificato durante il tentativo di contrassegnare il metodo indicato nel messaggio di errore.

L'assembly che ha causato il problema è denominato nel messaggio di errore. Per risolvere il problema l'assembly dovrà essere fornito una [report sui bug](https://bugzilla.xamarin.com) insieme a un log di compilazione completa con livello di dettaglio abilitata (vale a dire `-v -v -v -v` nel **argomenti aggiuntivi di mtouch**).

<a name="MT2103" />

### <a name="mt2103-error-processing-assembly--"></a>MT2103: Errore durante l'elaborazione di assembly '\*': *

Si è verificato un errore imprevisto durante l'elaborazione di un assembly.

L'assembly che ha causato il problema è denominato nel messaggio di errore. Per risolvere questo problema l'assembly dovrà essere fornito una [report sui bug](https://bugzilla.xamarin.com) insieme a un log di compilazione completa con livello di dettaglio abilitata (vale a dire `-v -v -v -v` nel **argomenti aggiuntivi di mtouch**).

<a name="MT2104" />

### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104: Impossibile collegare l'assembly '{0}' perché è in modalità mista.

Assembly in modalità mista non possono essere elaborati dal linker.

Vedere https://msdn.microsoft.com/library/x0w2664k.aspx per altre informazioni sugli assembly in modalità mista.

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx: Messaggi di errore AOT

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

<a name="MT3001" />

### <a name="mt3001-could-not-aot-the-assembly-"></a>MT3001: Potrebbe non AOT di assembly ' *'

Ciò indica in genere un bug nel compilatore AOT. Inviare un bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) con un progetto che può essere utilizzato per riprodurre l'errore.

In alcuni casi è possibile risolvere questo problema disabilitando le compilazioni incrementali in iOS del progetto opzione di compilazione (ma è ancora un bug, pertanto è necessario segnalarlo comunque).

<a name="MT3002" />

### <a name="mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-httpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbackshttpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbacks"></a>MT3002: Restrizione di AOT: Metodo ' *' deve essere statico poiché è decorata con [MonoPInvokeCallback]. Vedere [https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks)

Questo messaggio di errore viene fornito dal compilatore AOT.

<a name="MT3003" />

### <a name="mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a>MT3003: In conflitto, le opzioni di debug e - llvm. Disabilitazione del debug soft.

Debug non è supportato quando è abilitato LLVM. Se è necessario eseguire il debug dell'app, disabilitare innanzitutto LLVM.

<a name="MT3004" />

### <a name="mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a>MT3004: Potrebbe non AOT di assembly ' *' perché non esiste.

<a name="MT3005" />

### <a name="mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a>MT3005: La dipendenza '\*'dell'assembly'\*' non è stato trovato. Esaminare i riferimenti del progetto.

Ciò si verifica in genere quando un assembly fa riferimento a un'altra versione di un assembly di piattaforma (in genere la versione di .NET 4 di mscorlib. dll).

Questo non è supportato e potrà non compilare oppure vengano eseguite correttamente (l'assembly può usare API dalla versione di .NET 4 di mscorlib. dll che non dispone della versione di xamarin. IOS).

<a name="MT3006" />

### <a name="mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a>MT3006: Impossibile calcolare una mappa di dipendenza completa per il progetto. Ciò comporterà tempi di compilazione più lenti perché xamarin. IOS non è possibile rilevare correttamente ciò che deve essere ricompilato (e cosa non deve essere ricompilato). Esaminare gli avvisi precedenti per altri dettagli.

 compilare o eseguire in modo corretto (l'assembly può usare API dalla versione di .NET 4 di mscorlib. dll che non dispone della versione di xamarin. IOS).

<a name="MT3007" />

### <a name="mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a>MT3007: I file di informazioni di debug (*. mdb) non verranno caricati quando llvm è abilitato.

<a name="MT3008" />

### <a name="mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a>MT3008: Supporto per Bitcode richiede l'uso di back-end di LLVM AOT (-llvm)

Supporto per Bitcode richiede l'uso di back-end di LLVM AOT (-llvm).

Supporto per Bitcode disabilitare o abilitare LLVM.

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx: I messaggi di errore di generazione del codice

### <a name="mt40xx-main"></a>MT40xx: Main

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

<a name="MT4001" />

### <a name="mt4001-the-main-template-could-not-be-expanded-to-"></a>MT4001: Il modello principale non può essere ampliato a `*`.

Si è verificato un errore durante la generazione di main.m. Inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4002" />

### <a name="mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4002: Impossibile compilare il codice generato per i metodi P/Invoke. Inviare un report sui bug a http://bugzilla.xamarin.com

Impossibile compilare il codice generato per i metodi P/Invoke. Inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="mt41xx-registrar"></a>MT41xx: Registrar

<!--
  MT41xx registrar.m
  -->

<a name="MT4101" />

### <a name="mt4101-the-registrar-cannot-build-a-signature-for-type-"></a>MT4101: Il programma di registrazione non è possibile generare una firma per il tipo `*`.

Un tipo è stato trovato nell'API esportati che il runtime non riesce a effettuare il marshalling a/da Objective-C.

Se si ritiene che xamarin. IOS deve supportare il tipo in questione, inviare una richiesta di funzionalità avanzata senza [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4102" />

### <a name="mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a>MT4102: Il programma di registrazione trovato un tipo non valido `*` nella firma del metodo `*`. In alternativa, utilizzare `*`.

Questo attualmente avviene solo con un solo tipo: System.DateTime. Usare invece l'equivalente di Objective-C (NSDate).

<a name="MT4103" />

### <a name="mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a>MT4103: Il programma di registrazione trovato un tipo non valido `*` nella firma del metodo `*`: Il tipo implementa INativeObject, ma non dispone di un costruttore che accetta due (IntPtr, bool) argomenti

Questo errore si verifica quando le registrar rilevato un tipo in una firma con le caratteristiche menzionate. Il programma di registrazione potrebbe essere necessario creare nuove istanze del tipo e in questo caso richiede un costruttore con (IntPtr, bool) firma - il primo argomento (IntPtr) specifica l'handle gestito, mentre il secondo se il chiamante passa la proprietà dell'oggetto nativo handle (se questo valore è false 'Mantieni' verrà chiamato sull'oggetto).

<a name="MT4104" />

### <a name="mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a>MT4104: Il programma di registrazione non è possibile effettuare il marshalling il valore restituito per il tipo `*` nella firma del metodo `*`.

Un tipo è stato trovato nell'API esportati che il runtime non riesce a effettuare il marshalling a/da Objective-C.

Se si ritiene che xamarin. IOS deve supportare il tipo in questione, inviare una richiesta di funzionalità avanzata senza [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4105" />

### <a name="mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4105: Il programma di registrazione non è possibile effettuare il marshalling del parametro di tipo `*` nella firma del metodo `*`.

Se si ritiene che xamarin. IOS deve supportare il tipo in questione, inviare una richiesta di funzionalità avanzata senza [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4106" />

### <a name="mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a>MT4106: Il programma di registrazione non è possibile effettuare il marshalling il valore restituito per la struttura `*` nella firma del metodo `*`.

Un tipo è stato trovato nell'API esportati che il runtime non riesce a effettuare il marshalling a/da Objective-C.

Se si ritiene che xamarin. IOS deve supportare il tipo in questione, inviare una richiesta di funzionalità avanzata senza [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4107" />

### <a name="mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4107: Il programma di registrazione non è possibile effettuare il marshalling del parametro di tipo `*` nella firma del metodo `+`.

Un tipo è stato trovato nell'API esportati che il runtime non riesce a effettuare il marshalling a/da Objective-C.

Se si ritiene che xamarin. IOS deve supportare il tipo in questione, inviare una richiesta di funzionalità avanzata senza [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4108" />

### <a name="mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a>MT4108: Il programma di registrazione non è possibile ottenere il tipo ObjectiveC per tipo gestito `*`.

Un tipo è stato trovato nell'API esportati che il runtime non riesce a effettuare il marshalling a/da Objective-C.

Se si ritiene che xamarin. IOS deve supportare il tipo in questione, inviare una richiesta di funzionalità avanzata senza [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4109" />

### <a name="mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4109: Impossibile compilare il codice di registrar generato. Inviare un report sui bug a http://bugzilla.xamarin.com

Impossibile compilare il codice generato per il registrar. Log di compilazione conterrà l'output del compilatore nativo, che spiega il motivo per cui non è la compilazione di codice.

Questo valore è sempre un bug in xamarin. IOS; inviare un report sui bug a [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com) con il progetto o un test case.

<a name="MT4110" />

### <a name="mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a>MT4110: Il programma di registrazione non è possibile effettuare il marshalling il parametro out di tipo `*` nella firma del metodo `*`.

<a name="MT4111" />

### <a name="mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a>MT4111: Il programma di registrazione non è possibile generare una firma per il tipo `*` nel metodo `*`.

<a name="MT4112" />

### <a name="mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvancedtopicsregistrarhttpsdeveloperxamarincomguidesiosadvancedtopicsregistrar-for-more-information"></a>MT4112: Il programma di registrazione trovato un tipo non valido `*`. La registrazione di tipi generici con Objective-C non è supportata e potrebbe causare un comportamento casuale e/o arresti anomali del sistema (per le versioni precedenti la compatibilità con le versioni precedenti di xamarin. IOS è possibile ignorare questo errore passando `--unsupported--enable-generics-in-registrar` come un aggiuntivi di mtouch argomento nella pagina Opzioni di compilazione iOS del progetto. Visualizzare [developer.xamarin.com/guides/ios/advanced_topics/registrar](https://developer.xamarin.com/guides/ios/advanced_topics/registrar) per altre informazioni).

<a name="MT4113" />

### <a name="mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a>MT4113: Il programma di registrazione trovato un metodo generico: '\*.\*'. Esportazione di metodi generici non è supportato e causerà un comportamento casuale e/o arresti anomali del sistema.

<a name="MT4114" />

### <a name="mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4114: Errore imprevisto durante il registrar per il metodo '\*.\*'-inviare un report sui bug a http://bugzilla.xamarin.com

<a name="MT4116" />

### <a name="mt4116-could-not-register-the-assembly--"></a>MT4116: Non è stato possibile registrare l'assembly ' *': *

<a name="MT4117" />

### <a name="mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4117: Il programma di registrazione trovato una mancata corrispondenza di firma del metodo '*.*'-il selettore indica il metodo accetta * parametri, mentre il metodo gestito ha * parametri.

<a name="MT4118" />

### <a name="mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a>MT4118: Non è possibile registrare due tipi gestiti ('\*'e'\*') con lo stesso nome nativo ('* ').

<a name="MT4119" />

### <a name="mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a>MT4119: Impossibile registrare il selettore di '\*'del membro'\*. *' perché il selettore è già registrato in un membro diverso.

<a name="MT4120" />

### <a name="mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4120: Le registrar è stato trovato un tipo di campo sconosciuto '\*'nel campo'\*. *'. Inviare un report sui bug a http://bugzilla.xamarin.com

Questo errore indica un bug in xamarin. IOS. Inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4121" />

### <a name="mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a>MT4121: Non è possibile usare GCC / G + + per compilare il codice generato dal programma di registrazione statico quando si usa il framework di account (i file di intestazione forniti da Apple utilizzato durante la compilazione richiedono Clang). Utilizzare Clang (-del compilatore: clang) o il programma di registrazione dinamica (-registrar: dinamico).

<a name="MT4122" />

### <a name="mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a>MT4122: Non è possibile usare il compiler Clang fornito nel *.* SDK per compilare il codice generato dal programma di registrazione statico quando non ASCII digitare nomi ('* ') sono presenti nell'applicazione. Utilizzare GCC / G + + (-del compilatore: gcc | g + +), il programma di registrazione dinamica (-registrar: dinamico) o un SDK più recente.

<a name="MT4123" />

### <a name="mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a>MT4123: Il tipo del parametro variadic della funzione variadic ' *' deve essere System. IntPtr.

<a name="MT4124" />

### <a name="mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4124: Non è valido * trovato in ' *'. Inviare un report sui bug a http://bugzilla.xamarin.com

Questo errore indica un bug in xamarin. IOS. Inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4125" />

### <a name="mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a>MT4125: Il programma di registrazione trovato un tipo non valido '\*'nella firma del metodo'\*': L'interfaccia deve avere un attributo di protocollo che specifica il tipo di wrapper.

<a name="MT4126" />

### <a name="mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a>MT4126: Non è possibile registrare due protocolli gestiti ('\*'e'\*') con lo stesso nome nativo ('* ').

<a name="MT4127" />

### <a name="mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a>MT4127: Non è possibile registrare più di un metodo di interfaccia per il metodo '\*' (che sta implementando '\*').

<a name="MT4128" />

### <a name="mt4128-the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a>MT4128: Il programma di registrazione trovato un tipo di parametro generico non valido '\*'nel metodo'\*'. Il parametro generico deve avere un vincolo 'NSObject'.

<a name="MT4129" />

### <a name="mt4129-the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a>MT4129: Il programma di registrazione trovato un tipo restituito generico non valido '\*'nel metodo'\*'. Il tipo restituito generico deve avere un vincolo 'NSObject'.

<a name="MT4130" />

### <a name="mt4130-the-registrar-cannot-export-static-methods-in-generic-classes-"></a>MT4130: Il programma di registrazione non è possibile esportare i metodi statici in classi generiche ('* ').

<a name="MT4131" />

### <a name="mt4131-the-registrar-cannot-export-static-properties-in-generic-classes-"></a>MT4131: Il programma di registrazione non è possibile esportare le proprietà statiche in classi generiche ('\*.\*').

<a name="MT4132" />

### <a name="mt4132-the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a>MT4132: Il programma di registrazione trovato un tipo restituito generico non valido '\*'nella proprietà'\*'. Il tipo restituito deve avere un vincolo 'NSObject'.

<a name="MT4133" />

### <a name="mt4133-cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a>MT4133: Impossibile costruire un'istanza del tipo ' *' da Objective-C perché il tipo è generico. [Eccezione]

<a name="MT4134" />

### <a name="mt4134-your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a>MT4134: L'applicazione usi il ' *' framework, che non è incluso nel SDK in uso per compilare l'app iOS (questo framework è stato introdotto in iOS *, mentre si sta creando con iOS * SDK.) Selezionare una versione di SDK più recente di opzioni di compilazione iOS dell'app.

<a name="MT4135" />

### <a name="mt4135-the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a>MT4135: Il membro '\*.\*' ha un attributo di esportazione che non specifica un selettore. È necessario un selettore.

<a name="MT4136" />

### <a name="mt4136-the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a>MT4136: Il programma di registrazione non è possibile effettuare il marshalling il tipo di parametro '\*'del parametro'\*'nel metodo'\*. *'

<!-- MT4137 is unused -->

<a name="MT4138" />

### <a name="mt4138-the-registrar-cannot-marshal-the-property-type--of-the-property-"></a>MT4138: Il programma di registrazione non è possibile effettuare il marshalling il tipo della proprietà '\*'della proprietà'\*. *'.

<a name="MT4139" />

### <a name="mt4139-the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a>MT4139: Il programma di registrazione non è possibile effettuare il marshalling il tipo della proprietà '\*'della proprietà'\*. *'. Proprietà con l'attributo [connessione] deve avere un tipo di proprietà di NSObject (o una sottoclasse di NSObject).

<a name="MT4140" />

### <a name="mt4140-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4140: Il programma di registrazione trovato una mancata corrispondenza di firma del metodo '*.*'-il selettore indica il metodo variadic accetta * parametri, mentre il metodo gestito ha * parametri.

<a name="MT4141" />

### <a name="mt4141-cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a>MT4141: Non è possibile registrare il selettore di '\*'nel membro'\*' perché xamarin. IOS in modo implicito registra questo selettore.

Ciò si verifica quando si crea una sottoclasse di un tipo di framework e prova a implementare un 'Mantieni', 'release' o 'dealloc' metodo:

```csharp
class MyNSObject : NSObject
{
    [Export ("retain")]
    new void Retain () {}

    [Export ("release")]
    new void Release () {}

    [Export ("dealloc")]
    new void Dealloc () {}
}
```

È tuttavia possibile eseguire l'override di questi metodi se la classe non è la sottoclasse prima di framework digitare:

```csharp
class MyNSObject : NSObject
{
}

class MyCustomNSObject : MyNSObject
{
    [Export ("retain")]
    new void Retain () {}

    [Export ("release")]
    new void Release () {}

    [Export ("dealloc")]
    new void Dealloc () {}
}
```

In questo caso eseguirà l'override xamarin. IOS `retain`, `release` e `dealloc` sul `MyNSObject` (classe), e si verifica alcun conflitto.

<a name="MT4142" />

### <a name="mt4142-failed-to-register-the-type-"></a>MT4142: Non è riuscito a registrare il tipo di ' *'.

<a name="MT4143" />

### <a name="mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a>MT4143: La classe ObjectiveC ' *' non è stato possibile registrato, non sembra derivi da qualsiasi classe ObjectiveC noti (inclusi NSObject).

<a name="MT4144" />

### <a name="mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4144: Non è possibile registrare il metodo ' *' poiché non dispone di un trampoline associato. Inviare un report sui bug a http://bugzilla.xamarin.com.

Ciò indica un bug in xamarin. IOS. Inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4145" />

### <a name="mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a>MT4145: Enum non valido ' *': le enumerazioni con l'attributo [nativa] devono avere un tipo di enumerazione sottostante di 'long' o 'ulong'.

<a name="MT4146" />

### <a name="mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a>MT4146: Il parametro Name dell'attributo della classe di Registrar\*' ('\*') contiene un carattere non valido: '\*' (\*).

Il nome di una classe Objectice-C non può contenere spazi vuoti, che significa che il `Register` attributo della classe gestita corrispondente non può avere un `Name` parametro non può contenere sia uno spazio vuoto.

Verificare che il `Register` attributo della classe gestita indicata nel messaggio di errore non contiene gli spazi vuoti.

<a name="MT4147" />

### <a name="mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a>MT4147: Rilevato un protocollo che eredita dal protocollo JSExport quando si usa il programma di registrazione dinamica. Non è possibile esportare in modo dinamico; protocolli in JavaScriptCore è necessario utilizzare il programma di registrazione statico (aggiungere '-registrar: statici agli argomenti aggiuntivi di mtouch nelle opzioni di compilazione per selezionare il programma di registrazione statico di iOS del progetto).

<a name="MT4148" />

### <a name="mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a>MT4148: Il programma di registrazione trovato un protocollo generico: ' *'. Protocolli generici di esportazione non è supportato.

<a name="MT4149" />

### <a name="mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a>MT4149: Non è possibile registrare il metodo '\*.\*' perché il tipo del primo parametro ('\*') non corrisponde al tipo di categoria ('\*').

<a name="MT4150" />

### <a name="mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a>MT4150: Non è possibile registrare il tipo '\*' perché la proprietà Type ('\*') nella relativa categoria di attributo non eredita da NSObject.

<a name="MT4151" />

### <a name="mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a>MT4151: Non è possibile registrare il tipo di ' *' perché non è impostata la proprietà del tipo nel relativo attributo di categoria.

<a name="MT4152" />

### <a name="mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a>MT4152: Non è possibile registrare il tipo di ' *' come una categoria perché implementa INativeObject o alle sottoclassi NSObject.

<a name="MT4153" />

### <a name="mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a>MT4153: Non è possibile registrare il tipo '\*' come una categoria perché è generico.

<a name="MT4154" />

### <a name="mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a>MT4154: Non è possibile registrare il metodo '\*' come metodo categoria perché è generico.

<a name="MT4155" />

### <a name="mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a>MT4155: Non è possibile registrare il metodo '\*'con il selettore'\*' come metodo di categoria su ' *' di Objective-C ha già un'implementazione per questo selettore.

<a name="MT4156" />

### <a name="mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a>MT4156: Non è possibile registrare due categorie ('\*'e'\*') con lo stesso nome nativo ('* ').

<a name="MT4157" />

### <a name="mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a>MT4157: Non è possibile registrare il metodo di categoria '\*' perché almeno un parametro è obbligatorio (e relativo tipo deve corrispondere al tipo di categoria '\*')

<a name="MT4158" />

### <a name="mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a>MT4158: Non è possibile registrare il costruttore * categoria * perché non sono supportati i costruttori in categorie.

<a name="MT4159" />

### <a name="mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a>MT4159: Non è possibile registrare il metodo ' *' come metodo categoria perché i metodi di categoria devono essere statici.

<a name="MT4160" />

### <a name="mt4160-invalid-character---found-in-selector--for-"></a>MT4160: Carattere non valido '\*' (\*) disponibile nel selettore delle aree '\*'for'\*'.

<a name="MT4161" />

### <a name="mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a>MT4161: Il programma di registrazione trovata una struttura non supportata '\*': Tutti i campi in una struttura devono anche essere strutture (campo '\*'con tipo'{2}' non è una struttura).

Il programma di registrazione trovata una struttura con i campi non supportati.

Tutti i campi in una struttura che viene esposto a Objective-C devono essere anche strutture (non classi).

<a name="MT4162" />

### <a name="mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a>MT4162: Il tipo '\*' (usato come * {2}) non è disponibile in * * (è stata introdotta in * *)\* compilare con una versione * SDK (in genere eseguita usando la versione più recente di Xcode.

Il programma di registrazione trovato un tipo che non è incluso nel SDK corrente.

Aggiornare Xcode.

<a name="MT4163" />

### <a name="mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4163: Errore interno nel programma di registrazione (*). Inviare un report sui bug a http://bugzilla.xamarin.com

Questo errore indica un bug in xamarin. IOS. Inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4164" />

### <a name="mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a>MT4164: Non è possibile esportare la proprietà '\*' perché il selettore '\*' è una parola chiave Objective-C. Usare un nome diverso.

Il selettore per la proprietà in questione non è un identificatore valido di Objective-C.

Usare un identificatore valido di Objective-C come selettori.

<a name="MT4165" />

### <a name="mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a>MT4165: Il programma di registrazione non è stato trovato il tipo 'System. void' in uno degli assembly a cui viene fatto riferimento.

Questo errore probabilmente indica un bug in xamarin. IOS. Inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4166" />

### <a name="mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a>MT4166: Non è possibile registrare il metodo '\*' perché la firma contiene un tipo (\*) che non è un tipo di riferimento.

Ciò indica in genere un bug in xamarin. IOS; inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4167" />

### <a name="mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a>MT4167: Non è possibile registrare il metodo '\*' perché la firma contiene un tipo generico (\*) con un tipo di argomento generico che non è una sottoclasse di NSObject (*).

Ciò indica in genere un bug in xamarin. IOS; inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4168" />

### <a name="mt4168-cannot-register-the-type-managedname-because-its-objective-c-name-exportedname-is-an-objective-c-keyword-please-use-a-different-name"></a>MT4168: Non è possibile registrare il tipo ' {gestiti\_name}' perché il relativo nome di Objective-C ' {esportato\_nome}' è una parola chiave Objective-C. Usare un nome diverso.

Il nome di Objective-C per il tipo in questione non è un identificatore valido di Objective-C.

Usare un identificatore valido di Objective-C.

<a name="MT4169" />

### <a name="mt4169-failed-to-generate-a-pinvoke-wrapper-for-method-message"></a>MT4169: Non è riuscito a generare un wrapper di P/Invoke per {metodo}: {message}

Xamarin. IOS non è stato possibile generare una funzione wrapper P/Invoke per menzionata.
Verificare il messaggio di errore segnalato per individuare la causa sottostante.

<a name="MT4170" />

### <a name="mt4170-the-registrar-cant-convert-from-managed-type-to-native-type-for-the-return-value-in-the-method-method"></a>MT4170: Il programma di registrazione non è possibile convertire da '{tipo gestito}' '{tipo nativo}' per il valore restituito del metodo {metodo}.

Vedere la descrizione dell'errore <a href="#MT4172">MT4172</a>.

<a name="MT4171" />

### <a name="mt4171-the-bindas-attribute-on-the-member-member-is-invalid-the-bindas-type-type-is-different-from-the-property-type-type"></a>MT4171: L'attributo BindAs sul membro {membro} non è valido: il tipo di BindAs {type} è diverso dal tipo di proprietà di {type}.

Assicurarsi che il tipo dell'attributo BindAs corrisponde al tipo del membro che è associato.

<a name="MT4172" />

### <a name="mt4172-the-registrar-cant-convert-from-native-type-to-managed-type-for-the-parameter-parameter-name-in-the-method-method"></a>MT4172: Il registrar non può convertire da '{native type}' a '{tipo gestito}' per il parametro '{parameter name}' nel metodo {metodo}.

Il registrar non supporta la conversione tra i tipi indicati.

Si tratta di un bug in xamarin. IOS, se l'API in questione viene fornito da xamarin. IOS; inviare un bug nel [ http://bugzilla.xamarin.com ] [ 1].

Se si verifica questo problema durante lo sviluppo di un progetto di associazione per una libreria nativa, siamo aperti per l'aggiunta del supporto per nuove combinazioni dei tipi. In questo caso, inviare una richiesta di funzionalità avanzata ([http://bugzilla.xamarin.com][2]) con un test case e si sarà valutarlo.

[1]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS
[2]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS&component=General&bug_severity=enhancement

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx: GCC e la toolchain di messaggi di errore

### <a name="mt51xx-compilation"></a>MT51xx: Compilazione

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

<a name="MT5101" />

### <a name="mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a>MT5101: Mancante ' *' compilatore. Installare il componente 'Strumenti da riga di comando' Xcode

<a name="MT5102" />

### <a name="mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5102: Non è stato possibile assemblare il file ' *'. Inviare un report sui bug a http://bugzilla.xamarin.com

<a name="MT5103" />

### <a name="mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5103: Impossibile compilare il file ' *'. Inviare un report sui bug a http://bugzilla.xamarin.com

<a name="MT5104" />

### <a name="mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a>MT5104: Non è stato trovato né il '\*'né la'\*' compilatore. Installare il componente 'Strumenti da riga di comando' Xcode

<!-- 5105 is used by mmp -->

<a name="MT5106" />

### <a name="mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5106: Non è stato compilato i file ' *'. Inviare un report sui bug a http://bugzilla.xamarin.com

Ciò indica in genere un bug in xamarin. IOS; inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="mt52xx-linking"></a>MT52xx: Collegamento

<!--
  MT52xx linking
  -->

<a name="MT5201" />

### <a name="mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a>MT5201: Collegamento nativa non è riuscita. Vedere il log di compilazione e i contrassegni utente forniti per gcc: *

<a name="MT5202" />

### <a name="mt5202-native-linking-failed-please-review-the-build-log"></a>MT5202: Collegamento nativa non è riuscita. Esaminare il log di compilazione.

<a name="MT5203" />

### <a name="mt5203-native-linking-warning-"></a>MT5203: Collegamento avviso nativo: *

<!--- 5204-5208 are not used -->

<a name="MT5209" />

### <a name="mt5209-native-linking-error-"></a>MT5209: Collegamento di errore nativo: *

<a name="MT5210" />

### <a name="mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a>MT5210: Native del collegamento non riuscito, undefined simbolo: *. Verificare che sono stato fatto riferimento a tutti i framework necessari e le librerie native vengono collegate correttamente.

Ciò si verifica quando il linker nativo non è possibile trovare un simbolo di cui viene fatto riferimento in un punto. Esistono diversi motivi, che questo errore può verificarsi:

* Un'associazione di terze parti è necessario un framework, ma l'associazione non viene specificato nella relativa `[LinkWith]` attributo. Soluzioni:
  - Se si è l'autore dell'associazione di terze parti o abbia accesso all'origine dei dati, modificare il binding `[LinkWith]` attributo per includere il framework deve:

            [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]

  - Se non è possibile modificare l'associazione di terze parti, è possibile collegare manualmente con il framework richiesto passando `-gcc_flags '-framework SystemFramework'` a `mtouch` (ciò viene eseguito modificando gli argomenti aggiuntivi di mtouch nella pagina Opzioni di compilazione iOS del progetto. Tenere presente che questa operazione deve essere eseguita per tutte le configurazioni di progetto).
* In alcuni casi un'associazione gestita è composto da numerose librerie native e tutto deve essere incluso nelle associazioni. È possibile avere più di una libreria nativa in ogni progetto di associazione, in modo che la soluzione consiste nell'aggiungere semplicemente tutte le librerie native necessari per il progetto di associazione.</li>
* Un'associazione gestita fa riferimento ai simboli nativi che non esistono nella libreria nativa.
    Ciò accade solitamente quando esiste un'associazione da alcuni minuti e il codice nativo è stato modificato durante tale periodo in modo che una determinata classe nativa ha stato rimossa o rinominata, mentre l'associazione non è stato aggiornato.
* P/Invoke fa riferimento a un simbolo nativo che non esiste. A partire da xamarin. IOS 7.4 un' <a href="#MT5214">MT5214</a> errore verrà segnalato per questo caso (per altre informazioni, vedere MT5214).
* Un'associazione di terze parti / libreria è stata creata usando C++, ma l'associazione specifica non nella relativa `[LinkWith]` attributo. Ciò in genere è abbastanza semplice riconoscere, perché i simboli sono l'alterazione dei simboli di C++ (un esempio comune è `__ZNKSt9exception4whatEv`).
  - Se si è l'autore dell'associazione di terze parti o abbia accesso all'origine dei dati, modificare l'associazione `[LinkWith]` attributo da impostare il `IsCxx` flag:

            [LinkWith ("mylib.a", IsCxx = true)]

  - Se non è possibile modificare l'associazione di terze parti o per i collegamenti manualmente con una libreria di terze parti, è possibile impostare il flag equivalente passando <code>-cxx</code> di mtouch (questa operazione viene eseguita modificando gli argomenti aggiuntivi di mtouch nella pagina Opzioni di compilazione iOS del progetto . Tenere presente che questa operazione deve essere eseguita per tutte le configurazioni di progetto).

<a name="MT5211" />

### <a name="mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a>MT5211: Native del collegamento non riuscito, non definito classe Objective-C: \*. Il simbolo '\*' non è stato trovato in tutte le librerie o Framework collegato con l'applicazione.

Ciò si verifica quando il linker nativo non trova una classe di Objective-C che viene fatto riferimento in un punto. Esistono diversi motivi, questo errore può verificarsi: uguali a quelle per [MT5210](#MT5210) e inoltre:

* Un'associazione di terze parti associato un protocollo Objective-C, ma è stata non annotarlo con la <code>[Protocol]</code> attributo nella propria definizione api. Soluzioni:
  - Aggiungi parametro mancante `[Protocol]` attributo:

              [BaseType (typeof (NSObject))]
              [Protocol] // Add this
              public interface MyProtocol
              {
              }

<a name="MT5212" />

### <a name="mt5212-native-linking-failed-duplicate-symbol-"></a>MT5212: Native del collegamento non riuscito, duplicare simbolo: *.

Ciò si verifica quando il linker nativo rileva i simboli duplicati tra tutte le librerie native. In seguito questo errore potrebbero essere presenti uno o più [MT5213](#MT5213) errori con la posizione di ogni occorrenza del simbolo. Cause di questo errore:

* La stessa libreria nativa è inclusa due volte.
* Per definire gli stessi simboli si verificano due librerie native distinte.
* Una libreria nativa non è stato correttamente creata e contiene più volte lo stesso simbolo.
  È possibile verificarlo utilizzando il set di comandi da un terminale seguente (sostituire i386 con x86_64/armv7/armv7s/arm64 in base alla quale architettura si sta creando per):

        # Native libraries are usually fat libraries, containing binary code for
        # several architectures in the same file. First we extract the binary
        # code for the architecture we're interested in.
        lipo libNative.a -thin i386 -output libNative.i386.a

        # Now query the native library for the duplicated symbol.
        nm libNative.i386.a | fgrep 'SYMBOL'

        # You can also list the object files inside the native library.
        # In most cases this will reveal duplicated object files.
        ar -t libNative.i386.a

  Esistono alcuni modi possibili per risolvere questo problema:

  - Richiedere che il provider della libreria nativa di risolvere il problema e fornire la versione aggiornata.
  - Risolverlo personalmente rimuovendo i file di oggetto aggiuntivo (funziona solo se il problema è in realtà file oggetto duplicato)

            # Find out if the library is a fat library, and which
            # architectures it contains.
            lipo -info libNative.a

            # Extract each architecture (i386/x86_64/armv7/armv7s/arm64) to a separate file
            lipo libNative.a -thin ARCH -output libNative.ARCH.a

            # Extract the object files for the offending architecture
            # This will remove the duplicates by overwriting them
            # (since they have the same filename)
            mkdir -p ARCH
            cd ARCH
            ar -x ../libNative.ARCH.a

            # Reassemble the object files in an .a
            ar -r ../libNative.ARCH.a *.o
            cd ..

            # Reassemble the fat library
            lipo *.a -create -output libNative.a

  - Chiedere al linker di rimuovere il codice inutilizzato. Xamarin. IOS eseguirà questa operazione automaticamente se vengono soddisfatte tutte le condizioni seguenti:
    - Tutti i binding terze `[LinkWith]` gli attributi sono abilitati SmartLink:

            [assembly: LinkWith ("libNative.a", SmartLink = true)]

    - Non `-gcc_flags` viene passato a mtouch (nel campo degli argomenti aggiuntivi di mtouch di opzioni di compilazione iOS del progetto).
    - È anche possibile chiedere al linker direttamente di rimuovere il codice inutilizzato aggiungendo `-gcc_flags -dead_strip` agli argomenti aggiuntivi di mtouch in iOS del progetto opzioni di compilazione.

<a name="MT5213" />

### <a name="mt5213-duplicate-symbol-in--location-related-to-previous-error"></a>MT5213: Simbolo duplicato in: * (percorso relativo all'errore precedente)

Questo errore viene segnalato solo in combinazione con [MT5212](#MT5212). Vedi [MT5212](#MT5212) per altre informazioni.

<a name="MT5214" />

### <a name="mt5214-native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a>MT5214: Native del collegamento non riuscito, undefined simbolo: *. Questo simbolo è stato fatto riferimento al membro gestito *. Verificare che tutti i framework necessari siano stati librerie native e riferimento collegate.

Questo errore viene segnalato quando il codice gestito contiene P/Invoke a un metodo nativo che non esiste. Ad esempio:

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

Esistono diverse soluzioni possibili:

  -  Rimuovere il in questione P/Invoke dal codice sorgente.
  -  Abilitare il linker gestito per tutti gli assembly (ciò viene eseguito in iOS del progetto le opzioni di compilazione, tramite l'impostazione "Comportamento del Linker" a "Tutti gli assembly"). Verranno rimossi in modo efficace tutti i P/Invoke non si usa dall'app (automaticamente, invece che manualmente come al punto precedente). Lo svantaggio è che in questo modo le compilazioni del simulatore leggermente più lento e il debugger può interrompere l'app Se Usa la reflection, sono disponibili altre informazioni sul linker [qui](~/ios/deploy-test/linker.md) )
  -  Creare una libreria nativa secondo che contiene i simboli native mancanti. Si noti che questa è semplicemente una soluzione alternativa (se si tenta di chiamare tali funzioni, l'app si arresterà).

<a name="MT5215" />

### <a name="mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a>MT5215: Fa riferimento a ' *' potrebbero richiedere ulteriore - framework = XXX o - lXXX istruzioni al linker nativo

Si tratta di un avviso, a indicare che è stato rilevato un P/Invoke che fa riferimento alla libreria in questione, ma l'app non è collegato con esso.

<a name="MT5216" />

### <a name="mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5216: Collegamento nativa non è riuscita per *. Inviare un report sui bug a http://bugzilla.xamarin.com

Questo errore viene segnalato quando l'output del compilatore AOT di collegamento.

Questo errore probabilmente indica un bug in xamarin. IOS. Inviare un report sui bug a [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT5217" />

### <a name="mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a>MT5217: Collegamento native probabilmente non è riuscita perché la riga di comando del linker è troppo lunga (* caratteri).

Collegamento nativa non è riuscita ed è possibile che ciò si è verificato il comando del linker è troppo lungo.

Progetti xamarin. IOS farà spesso riferimento nativi simboli in modo dinamico, il che significa che al linker nativo potrebbe rimuovere tali simboli nativi durante il processo di collegamento nativo, perché il linker nativo non vede che vengono utilizzati questi simboli.

In genere xamarin. IOS chiederà al linker nativo di mantenere tali simboli usando la `-u symbol` flag del linker, ma se sono presenti molti di questi simboli, l'intera riga di comando potrebbe superare la lunghezza massima della riga di comando come specificato dal sistema operativo.

Esistono alcuni possibili origini per i simboli di questo tipo dinamici:

* P/Invoke ai metodi nelle librerie collegate in modo statico (dove è il nome della dll `__Internal` nell'attributo DllImport `[DllImport ("__Internal")]`).
* Campo riferimenti alle posizioni di memoria nelle librerie collegate in modo statico dai progetti di binding (`[Field]` attributi).
* Classi di Objective-C cui viene fatto riferimento nelle librerie collegate in modo statico da progetti di binding (quando si usano le compilazioni incrementali o se non si usa il programma di registrazione statico).

Possibili soluzioni:

* Abilitare il linker gestito (se possibile per tutti gli assembly anziché solo assembly SDK). Questa operazione potrebbe rimuovere un numero sufficiente di origini per i simboli dinamici in modo che l'opzione del linker da riga di comando non ha superato il valore massimo.
* Ridurre il numero di P/Invoke, i riferimenti di campo e/o le classi di Objective-C.
* Riscrivere i simboli dinamici con nomi più brevi.
* Passare `-dlsym:false` come argomenti aggiuntivi di mtouch in iOS del progetto opzioni di compilazione. Con questa opzione, xamarin. IOS genererà un riferimento nativo nel codice compilati con AOT e non sia necessario richiederlo al linker di mantenere questo simbolo. Tuttavia, funziona solo per il dispositivo si basa e provocherà errori del linker se esistono P/Invoke per le funzioni che non esistono nella libreria statica.
* Passare `--dynamic-symbol-mode=code` come un argomenti aggiuntivi di mtouch in iOS del progetto opzioni di compilazione. Con questa opzione, xamarin. IOS genererà codice nativo aggiuntivo che fa riferimento a questi simboli invece di chiedere al linker nativo di mantenere questi simboli utilizzando gli argomenti della riga di comando. Lo svantaggio di questo approccio è che aumenterà le dimensioni del file eseguibile in qualche modo.
* Abilitare il programma di registrazione statico passando `--registrar:static` come argomenti aggiuntivi di mtouch in iOS del progetto le opzioni di compilazione (per le compilazioni del simulatore, poiché il programma di registrazione statico è già il valore predefinito per le compilazioni di dispositivo). Il programma di registrazione statico genera codice che fa riferimento alle classi di Objective-C in modo statico, in modo che non è necessario chiedere al linker nativo di mantenere tali classi.
* Disabilitare le compilazioni incrementali (per compilazioni del dispositivo). Quando le compilazioni incrementali sono abilitate, il codice generato dal programma di registrazione statico non verrà considerato dal linker nativo, il che significa che xamarin. IOS deve richiedere ancora al linker di mantenere le classi di Objective-C a cui viene fatto riferimento. Disattivando quindi le compilazioni incrementali impedirà questa esigenza.

<a name="MT5218" />

### <a name="mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MT5218: Non è possibile ignorare il simbolo dinamico {simbolo} (-ignorare-dynamic-symbol = {simbolo}) poiché non è stato rilevato come simbolo di dinamico.

L'argomento della riga di comando `--ignore-dynamic-symbol=symbol` passato, ma questo simbolo non è un simbolo che è stato riconosciuto come un simbolo dinamico che deve essere conservato manualmente.

Esistono due motivi principali per questo oggetto:

* Il nome del simbolo non è corretto.
    * Non anteporre un carattere di sottolineatura per il nome del simbolo.
    * Il simbolo per le classi di Objective-C è `OBJC_CLASS_$_<classname>`.
* Il simbolo è corretto, ma è un simbolo che già viene mantenuto in modo normale (alcune build cause opzioni l'elenco esatto dei simboli dinamici al variare).

### <a name="mt53xx-other-tools"></a>MT53xx: Altri strumenti

<!--
  MT53xx other tools
  -->

<a name="MT5301" />

### <a name="mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a>MT5301: Strumento 'striscia' mancante. Installare il componente 'Strumenti da riga di comando' Xcode

<a name="MT5302" />

### <a name="mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a>MT5302: Strumento 'dsymutil' mancante. Installare il componente 'Strumenti da riga di comando' Xcode

<a name="MT5303" />

### <a name="mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a>MT5303: Impossibile generare i simboli di debug (directory di dSYM). Esaminare il log di compilazione.

Si è verificato un errore durante l'esecuzione di dsymutil nella directory di file con estensione App finale per creare i simboli di debug. Esaminare il log di compilazione per vedere l'output di dsymutil e osservare il modo in cui può essere corretta.

<a name="MT5304" />

### <a name="mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a>MT5304: Non è stato possibile rimuovere il file binario finale. Esaminare il log di compilazione.

Si è verificato un errore quando si esegue lo strumento 'Rimuovi' per rimuovere le informazioni di debug dall'applicazione.

<a name="MT5305" />

### <a name="mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a>MT5305: Strumento 'lipo' mancante. Installare il componente 'Strumenti da riga di comando' Xcode

<a name="MT5306" />

### <a name="mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a>MT5306: Errore durante la creazione di una libreria fat. Esaminare il log di compilazione.

Si è verificato un errore quando si esegue lo strumento 'lipo'. Esaminare il log di compilazione per visualizzare l'errore segnalato da 'lipo'.

<a name="MT5307" />

### <a name="mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a>MT5307: Non è stato possibile firmare il file eseguibile. Esaminare il log di compilazione.

Si è verificato un errore durante l'accesso all'applicazione. Esaminare il log di compilazione per visualizzare l'errore segnalato da 'codesign'.

<!-- 5308 is used by mmp -->
<!-- 5309 is used by mmp -->
<!-- 5310 is used by mmp -->

## <a name="mt6xxx-mtouch-internal-tools-error-messages"></a>MT6xxx: strumenti di mtouch interno i messaggi di errore

### <a name="mt600x-stripper"></a>MT600x: Eliminazione formattazione

<!--
 MT6xxx mtouch internal tools
  MT600x Stripper
  -->

<a name="MT6001" />

### <a name="mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a>MT6001: Versione in esecuzione di Cecil non supporta la rimozione dei assembly

<a name="MT6002" />

### <a name="mt6002-could-not-strip-assembly-"></a>MT6002: Non è stato possibile rimuovere assembly `*`.

Si è verificato un errore durante la rimozione dei codice gestito (rimuovendo il codice IL) dagli assembly nell'applicazione.

<a name="MT6003" />

### <a name="mt6003-unauthorizedaccessexception-message"></a>MT6003: [Message UnauthorizedAccessException]

Si è verificato un errore sicurezza durante la rimozione dei simboli dall'applicazione di debug.

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx: Messaggi di errore di MSBuild

<!--
 MT7xxx msbuild errors
  -->

<a name="MT7001" />

### <a name="mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a>MT7001: Non è stato possibile risolvere l'host di indirizzi IP per le impostazioni del debugger Wi-Fi.

*Attività di MSBuild: DetectDebugNetworkConfigurationTaskBase*

Passaggi di risoluzione dei problemi:

- Provare a eseguire `csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'` (che fornisce un indirizzo IP e non un errore ovviamente).
- Provare a eseguire "ping \`hostname\`" che potrebbe fornire maggiori informazioni, ad esempio: `cannot resolve MyHost.local: Unknown host`

In alcuni casi, è un problema di "rete locale" e possono essere indirizzato tramite l'aggiunta di host sconosciuto `127.0.0.1   MyHost.local` in `/etc/hosts`.

<a name="MT7002" />

### <a name="mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a>MT7002: Questa macchina non ha le schede di rete. Ciò è necessario durante il debug o la profilatura sul dispositivo tramite Wi-Fi.

*Attività di MSBuild: DetectDebugNetworkConfigurationTaskBase*

<a name="MT7003" />

### <a name="mt7003-the-app-extension--does-not-contain-an-infoplist"></a>MT7003: L'estensione dell'App ' *' non contiene un file Info. plist.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7004" />

### <a name="mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a>MT7004: L'estensione dell'App ' *' non specifica un CFBundleIdentifier.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7005" />

### <a name="mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a>MT7005: L'estensione dell'App ' *' non specifica un CFBundleExecutable.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7006" />

### <a name="mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7006: L'estensione dell'App '\*' ha un CFBundleIdentifier non valido (\*), non inizia con CFBundleIdentifier del bundle app principale (*).

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7007" />

### <a name="mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7007: L'estensione dell'App '\*' ha un CFBundleIdentifier (\*) che termina con il suffisso "Key" non valido.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7008" />

### <a name="mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a>MT7008: L'estensione dell'App ' *' non specifica un CFBundleShortVersionString.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7009" />

### <a name="mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7009: L'estensione dell'App ' *' è un file Info. plist non è valido: non contiene un dizionario NSExtension.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7010" />

### <a name="mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a>MT7010: L'estensione dell'App ' *' è un file Info. plist non è valido: il dizionario NSExtension non contenga un valore NSExtensionPointIdentifier.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7011" />

### <a name="mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a>MT7011: L'estensione WatchKit ' *' è un file Info. plist non è valido: il dizionario NSExtension non contiene un dizionario NSExtensionAttributes.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7012" />

### <a name="mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a>MT7012: L'estensione WatchKit ' *' non ha esattamente una app watch.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7013" />

### <a name="mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a>MT7013: L'estensione WatchKit ' *' è un file Info. plist non è valido: UIRequiredDeviceCapabilities deve contenere la funzionalità 'watch-complementare'.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7014" />

### <a name="mt7014-the-watch-app--does-not-contain-an-infoplist"></a>MT7014: L'App Watch ' *' non contiene un file Info. plist.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7015" />

### <a name="mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7015: L'App Watch ' *' non specifica un CFBundleIdentifier.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7016" />

### <a name="mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7016: L'App Watch '\*' ha un CFBundleIdentifier non valido (\*), non inizia con CFBundleIdentifier del bundle app principale (*).

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7017" />

### <a name="mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a>MT7017: L'App Watch '\*' non ha un valore UIDeviceFamily valido. Previsto 'Watch (4)' ma trovato '\* (*)'.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7018" />

### <a name="mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7018: L'App Watch ' *' non specifica un CFBundleExecutable

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7019" />

### <a name="mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a>MT7019: L'App Watch '\*' ha un valore WKCompanionAppBundleIdentifier non valido ('\*'), non corrisponde CFBundleIdentifier del bundle dell'app principale ('* ').

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7020" />

### <a name="mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a>MT7020: L'App Watch ' *' è un file Info. plist non è valido: la chiave WKWatchKitApp debba essere presenti e avere un valore 'true'.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7021" />

### <a name="mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7021: L'App Watch ' *' è un file Info. plist non è valido: la chiave LSRequiresIPhoneOS non deve essere presente.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7022" />

### <a name="mt7022-the-watch-app--does-not-contain-a-watch-extension"></a>MT7022: L'App Watch ' *' non contiene un'estensione di espressioni di controllo.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7023" />

### <a name="mt7023-the-watch-extension--does-not-contain-an-infoplist"></a>MT7023: L'estensione di espressioni di controllo ' *' non contiene un file Info. plist.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7024" />

### <a name="mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a>MT7024: L'estensione di espressioni di controllo ' *' non specifica un CFBundleIdentifier.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7025" />

### <a name="mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a>MT7025: L'estensione di espressioni di controllo ' *' non specifica un CFBundleExecutable.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7026" />

### <a name="mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7026: L'estensione di espressioni di controllo»\*' ha un CFBundleIdentifier non valido (\*), non inizia con CFBundleIdentifier del bundle app principale (*).

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7027" />

### <a name="mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7027: L'estensione di espressioni di controllo»\*' ha un CFBundleIdentifier (\*) che termina con il suffisso "Key" non valido.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7028" />

### <a name="mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7028: L'estensione di espressioni di controllo ' *' è un file Info. plist non è valido: non contiene un dizionario NSExtension.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7029" />

### <a name="mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a>MT7029: L'estensione di espressioni di controllo ' *' è un file Info. plist non è valido: il NSExtensionPointIdentifier deve essere "com.apple.watchkit".

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7030" />

### <a name="mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a>MT7030: L'estensione di espressioni di controllo ' *' è un file Info. plist non è valido: il dizionario NSExtension deve contenere NSExtensionAttributes.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7031" />

### <a name="mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a>MT7031: L'estensione di espressioni di controllo ' *' è un file Info. plist non è valido: il dizionario NSExtensionAttributes deve contenere un WKAppBundleIdentifier.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7032" />

### <a name="mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a>MT7032: L'estensione WatchKit ' *' è un file Info. plist non è valido: UIRequiredDeviceCapabilities non deve contenere la funzionalità 'watch-complementare'.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7033" />

### <a name="mt7033-the-watch-app--does-not-contain-an-infoplist"></a>MT7033: L'App Watch ' *' non contiene un file Info. plist.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7034" />

### <a name="mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7034: L'App Watch ' *' non specifica un CFBundleIdentifier.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7035" />

### <a name="mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a>MT7035: L'App Watch '\*' non ha un valore UIDeviceFamily valido. Previsto '\*', trovato'\* (\*)'.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7036" />

### <a name="mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7036: L'App Watch ' *' non specifica un CFBundleExecutable.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7037" />

### <a name="mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a>MT7037: L'estensione WatchKit '{extensionName}' ha un valore WKAppBundleIdentifier non valido ('\*'), non corrisponde CFBundleIdentifier dell'App per le espressioni di controllo ('\*').

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7038" />

### <a name="mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a>MT7038: L'App Watch ' *' è un file Info. plist non è valido: il WKCompanionAppBundleIdentifier deve esistere e deve corrispondere CFBundleIdentifier del bundle dell'app principale.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7039" />

### <a name="mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7039: L'App Watch ' *' è un file Info. plist non è valido: la chiave LSRequiresIPhoneOS non deve essere presente.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7040" />

### <a name="mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a>MT7040: Il bundle dell'app {AppBundlePath} non contiene un file Info. plist.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7041" />

### <a name="mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a>MT7041: Percorso file Info. plist principale non è specificato un CFBundleIdentifier.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7042" />

### <a name="mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a>MT7042: Percorso file Info. plist principale non è specificato un CFBundleExecutable.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7043" />

### <a name="mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a>MT7043: Percorso file Info. plist principale non è specificato un CFBundleSupportedPlatforms.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7044" />

### <a name="mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a>MT7044: Percorso file Info. plist principale non è specificato un UIDeviceFamily.

*Attività di MSBuild: ValidateAppBundleTaskBase*

<a name="MT7045" />

### <a name="mt7045-unrecognized-format-"></a>MT7045: Formato non è stata riconosciuta: *.

*Attività di MSBuild: PropertyListEditorTaskBase*

Dove * può essere:

- stringa
- array
- dict
- bool
- real
- numero intero
- date
- Data

<a name="MT7046" />

### <a name="mt7046-add-entry--incorrectly-specified"></a>MT7046: Aggiungi: Voce, *, in modo non corretto specificato.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7047" />

### <a name="mt7047-add-entry--contains-invalid-array-index"></a>MT7047: Aggiungi: Voce, *, contiene l'indice di matrice non valido.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7048" />

### <a name="mt7048-add--entry-already-exists"></a>MT7048: Aggiungere: * voce esiste già.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7049" />

### <a name="mt7049-add-cant-add-entry--to-parent"></a>MT7049: Aggiungi: Non è possibile aggiungere una voce, *, come padre.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7050" />

### <a name="mt7050-delete-cant-delete-entry--from-parent"></a>MT7050: Elimina: Non è possibile eliminare una voce, *, dall'elemento padre.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7051" />

### <a name="mt7051-delete-entry--contains-invalid-array-index"></a>MT7051: Elimina: Voce, *, contiene l'indice di matrice non valido.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7052" />

### <a name="mt7052-delete-entry--does-not-exist"></a>MT7052: Elimina: Voce, *, non esiste.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7053" />

### <a name="mt7053-import-entry--incorrectly-specified"></a>MT7053: Import: Voce, *, in modo non corretto specificato.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7054" />

### <a name="mt7054-import-entry--contains-invalid-array-index"></a>MT7054: Import: Voce, *, contiene l'indice di matrice non valido.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7055" />

### <a name="mt7055-import-error-reading-file-"></a>MT7055: Import: Errore durante la lettura File: *.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7056" />

### <a name="mt7056-import-cant-add-entry--to-parent"></a>MT7056: Import: Non è possibile aggiungere una voce, *, come padre.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7057" />

### <a name="mt7057-merge-cant-add-array-entries-to-dict"></a>MT7057: Unione: Non è possibile aggiungere le voci della matrice a dict.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7058" />

### <a name="mt7058-merge-specified-entry-must-be-a-container"></a>MT7058: Unione: Voce specificata deve essere un contenitore.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7059" />

### <a name="mt7059-merge-entry--contains-invalid-array-index"></a>MT7059: Unione: Voce, *, contiene l'indice di matrice non valido.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7060" />

### <a name="mt7060-merge-entry--does-not-exist"></a>MT7060: Unione: Voce, *, non esiste.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7061" />

### <a name="mt7061-merge-error-reading-file-"></a>MT7061: Unione: Errore durante la lettura File: *.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7062" />

### <a name="mt7062-set-entry--incorrectly-specified"></a>MT7062: Impostare: Voce, *, in modo non corretto specificato.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7063" />

### <a name="mt7063-set-entry--contains-invalid-array-index"></a>MT7063: Impostare: Voce, *, contiene l'indice di matrice non valido.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7064" />

### <a name="mt7064-set-entry--does-not-exist"></a>MT7064: Impostare: Voce, *, non esiste.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7065" />

### <a name="mt7065-unknown-propertylist-editor-action-"></a>MT7065: Azione di editor PropertyList sconosciuta: *.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7066" />

### <a name="mt7066-error-loading--"></a>MT7066: Errore durante il caricamento ' *': *.

*Attività di MSBuild: PropertyListEditorTaskBase*

<a name="MT7067" />

### <a name="mt7067-error-saving--"></a>MT7067: Errore durante il salvataggio ' *': *.

*Attività di MSBuild: PropertyListEditorTaskBase*

## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx: Messaggi di errore di runtime

<!--
 MT8xxx runtime
  MT800x misc
  -->

<a name="MT8001" />

### <a name="mt8001-version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a>MT8001: Versione non corrispondente tra il runtime di xamarin. IOS nativo e monotouch. Reinstallare xamarin. IOS.

<a name="MT8002" />

### <a name="mt8002-could-not-find-the-method--in-the-type-"></a>MT8002: Non è stato possibile trovare il metodo '\*'nel tipo'\*'.

<a name="MT8003" />

### <a name="mt8003-failed-to-find-the-closed-generic-method--on-the-type-"></a>MT8003: Impossibile trovare il metodo generico chiuso '\*'nel tipo'\*'.

<a name="MT8004" />

### <a name="mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a>MT8004: Non è possibile creare un'istanza di * per l'oggetto nativo 0 x * (di tipo ' *'), in quanto esiste già un'altra istanza per questo oggetto nativo (di tipo *).

<a name="MT8005" />

### <a name="mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a>MT8005: Tipo di wrapper '\*'manca la relativa classe ObjectiveC nativo'\*'.

<a name="MT8006" />

### <a name="mt8006-failed-to-find-the-selector--on-the-type-"></a>MT8006: Impossibile trovare il selettore di '\*'nel tipo'\*'

<a name="MT8007" />

### <a name="mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a>MT8007: Non è possibile ottenere il descrittore del metodo per il selettore '\*'nel tipo'\*', perché il selettore non corrisponde a un metodo

<a name="MT8008" />

### <a name="mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8008: La versione della DLL caricata è stata compilata per * bit, mentre il processo è * bits. Inviare un bug nel http://bugzilla.xamarin.com.

Ciò indica che problemi nel processo di compilazione. Inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8009" />

### <a name="mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8009: Impossibile individuare il blocco per il metodo di conversione per il metodo delegato *.*' s parametro & *. Inviare un bug nel http://bugzilla.xamarin.com.

Questo indica che un'API non è stata associata correttamente. Se si tratta di un'API esposta da Xamarin, inviare un bug nel nostro bugzilla ([http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)), se si tratta di un'associazione di terze parti, contattare il fornitore.

<a name="MT8010" />

### <a name="mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a>MT8010: Tipo nativo dimensioni mancata corrispondenza tra Xamarin. [iOS | Mac]. dll e l'architettura in esecuzione. Xamarin. [iOS | Mac]. dll sia stata compilata per *-bit, mentre il processo corrente è *-bit.

Ciò indica che problemi nel processo di compilazione. Inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8011" />

### <a name="mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8011: Impossibile individuare il delegato all'attributo di conversione di blocco ([DelegateProxy]) per il valore restituito del metodo *.*. Inviare un bug nel http://bugzilla.xamarin.com.

Xamarin. IOS non è riuscito a individuare un metodo richiesto in fase di esecuzione (per convertire un delegato a un blocco).

Ciò indica in genere un bug in xamarin. IOS; inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8012" />

### <a name="mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8012: DelegateProxyAttribute non valido per il valore restituito del metodo *.*: Tipodelegato è null. Inviare un bug nel http://bugzilla.xamarin.com.

L'attributo DelegateProxy per il metodo in questione non è valido.

Ciò indica in genere un bug in xamarin. IOS; inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8013" />

### <a name="mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8013: DelegateProxyAttribute non valido per il valore restituito del metodo *.*: Tipodelegato ({2}) specifica un tipo senza un campo 'Handler'. Inviare un bug nel http://bugzilla.xamarin.com.

L'attributo DelegateProxy per il metodo in questione non è valido.

Ciò indica in genere un bug in xamarin. IOS; inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8014" />

### <a name="mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8014: DelegateProxyAttribute non valido per il valore restituito del metodo *.*: Il Tipodelegato ({2}) il campo 'Handler' è null. Inviare un bug nel http://bugzilla.xamarin.com.

L'attributo DelegateProxy per il metodo in questione non è valido.

Ciò indica in genere un bug in xamarin. IOS; inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8015" />

### <a name="mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8015: DelegateProxyAttribute non valido per il valore restituito del metodo *.*: Il Tipodelegato ({2}) il campo 'Handler' non è un delegato, si tratta di un *. Inviare un bug nel http://bugzilla.xamarin.com.

L'attributo DelegateProxy per il metodo in questione non è valido.

Ciò indica in genere un bug in xamarin. IOS; inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8016" />

### <a name="mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8016: Impossibile convertire il delegato viene bloccato per il valore restituito del metodo *.*, poiché l'input non è un delegato, si tratta di un *. Inviare un bug nel http://bugzilla.xamarin.com.

L'attributo DelegateProxy per il metodo in questione non è valido.

Ciò indica in genere un bug in xamarin. IOS; inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<!-- 8017 is used by mmp -->

<a name="MT8018" />

### <a name="mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8018: Errore di coerenza interno. Inviare un report sui bug a http://bugzilla.xamarin.com.

Ciò indica un bug in xamarin. IOS. Inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8019" />

### <a name="mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a>MT8019: Non è riuscito a trovare l'assembly * negli assembly caricati.

Ciò indica un bug in xamarin. IOS. Inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8020" />

### <a name="mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a>MT8020: Non è riuscito a trovare il modulo con MetadataToken * nell'assembly *.

Ciò indica un bug in xamarin. IOS. Inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8021" />

### <a name="mt8021-unknown-implicit-token-type-"></a>MT8021: Tipo di token sconosciuto implicito: *.

Ciò indica un bug in xamarin. IOS. Inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8022" />

### <a name="mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8022: Previsto il riferimento al token * sia un *, ma è un' *. Inviare un report sui bug a http://bugzilla.xamarin.com.

Ciò indica un bug in xamarin. IOS. Inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8023" />

### <a name="mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8023: È necessario un oggetto istanza per costruire un metodo generico chiuso per il metodo generico aperto: * (riferimento ai token: *). Inviare un report sui bug a http://bugzilla.xamarin.com.

Ciò indica un bug in xamarin. IOS. Inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8024" />

### <a name="mt8024-could-not-find-a-valid-extension-type-for-the-smart-enum-smarttype-please-file-a-bug-at-httpsbugzillaxamarincom"></a>MT8024: Impossibile trovare un tipo di estensione valido per l'enumerazione smart '{smart_type}'. Inviare un bug nel https://bugzilla.xamarin.com.

Ciò indica un bug in xamarin. IOS. Inviare un bug nel [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).
