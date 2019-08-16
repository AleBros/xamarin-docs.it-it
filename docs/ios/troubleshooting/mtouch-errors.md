---
title: Errori di Novell. iOS
description: Questo documento descrive i vari errori generati da mTouch, lo strumento usato per aggregare le applicazioni Novell. iOS. Gli errori sono elencati in base al codice ed è stata fornita una descrizione completa.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: 77174070b227bf8cba94078b1ed1f0a6a8fcf1f8
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528707"
---
# <a name="xamarinios-errors"></a>Errori di Novell. iOS

## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx: messaggi di errore mTouch

ad esempio parametri, ambiente, strumenti mancanti.

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
    -->

<a name="MT0000" />

### <a name="mt0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MT0000: Errore imprevisto. compilare un report sui bug in https://github.com/xamarin/xamarin-macios/issues/new

Si è verificata una condizione di errore imprevista. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con il maggior numero di informazioni possibile, tra cui:

* Log di compilazione completi, con livello di dettaglio massimo ( `-v -v -v -v` ad esempio negli **argomenti mTouch aggiuntivi**);
* Test case minimo per riprodurre l'errore. e
* Tutte le informazioni sulla versione

Il modo più semplice per ottenere informazioni esatte sulla versione consiste nell'usare il menu **Visual Studio per Mac** , informazioni **su Visual Studio per Mac** elemento, visualizzare il pulsante **Dettagli** e copiare e incollare le informazioni sulla versione (è possibile usare il pulsante **copia informazioni** ) .

<a name="MT0001" />

### <a name="mt0001--devname-was-provided-without-any-device-specific-action"></a>MT0001:'-DEVNAME ' è stato fornito senza alcuna azione specifica del dispositivo

Si tratta di un avviso che viene generato se-devname viene passato a mTouch quando non è stata richiesta alcuna azione specifica del dispositivo (-logdev/-installdev/-killdev/-launchdev/-ListApps).

<a name="MT0002" />

### <a name="mt0002-could-not-parse-the-environment-variable-"></a>MT0002: Non è stato possibile analizzare la variabile di ambiente *.

Questo errore si verifica se si tenta di impostare una coppia chiave-valore della variabile di ambiente non valida. Il formato corretto è:`mtouch --setenv=VARIABLE=VALUE`

<a name="MT0003" />

### <a name="mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MT0003: Il nome dell'applicazione ' *. exe ' è in conflitto con un nome di assembly SDK o prodotto (con estensione dll).

Il nome dell'assembly eseguibile e il nome dell'applicazione non possono corrispondere al nome di una dll nell'app. Modificare il nome del file eseguibile.

<a name="MT0004" />

### <a name="mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a>MT0004: La nuova logica refcounting richiede l'abilitazione di SGen.

Se si Abilita l'estensione refcounting, è necessario abilitare anche il Garbage Collector SGen nelle opzioni di compilazione iOS del progetto (scheda Avanzate).

A partire da Novell. iOS 7.2.1 questo requisito è stato rimosso, la nuova logica refcounting può essere abilitata con i Garbage Collector Boehm e SGen.

<a name="MT0005" />

### <a name="mt0005-the-output-directory--does-not-exist"></a>MT0005: La directory di output * non esiste.

Creare la directory.

Questo errore non viene più generato, mTouch creerà automaticamente la directory se non esiste.

<a name="MT0006" />

### <a name="mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a>MT0006: Non esiste alcuna piattaforma devel in *, usare--Platform = PLAT per specificare l'SDK.

Novell. iOS non è in grado di trovare la directory SDK nel percorso indicato nel messaggio di errore. Verificare che il percorso sia corretto.

<a name="MT0007" />

### <a name="mt0007-the-root-assembly--does-not-exist"></a>MT0007: L'assembly radice * non esiste.

Novell. iOS non è in grado di trovare l'assembly nel percorso indicato nel messaggio di errore. Verificare che il percorso sia corretto.

<a name="MT0008" />

### <a name="mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a>MT0008: È necessario fornire un solo assembly radice, trovato # Assemblies: *.

Più di un assembly radice è stato passato a mTouch, mentre può essere presente un solo assembly radice.

<a name="MT0009" />

### <a name="mt0009-error-while-loading-assemblies-"></a>MT0009: Errore durante il caricamento degli assembly: *.

Si è verificato un errore durante il caricamento degli assembly dai riferimenti all'assembly radice. Ulteriori informazioni possono essere fornite nell'output di compilazione.

<a name="MT0010" />

### <a name="mt0010-could-not-parse-the-command-line-arguments-"></a>MT0010: Non è stato possibile analizzare gli argomenti della riga di comando: *.

Si è verificato un errore durante l'analisi degli argomenti della riga di comando. Verificare che siano tutti corretti.

<a name="MT0011" />

### <a name="mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a>MT0011: * è stato creato in base a un runtime più recente (*) rispetto al supporto MonoTouch.

Questo avviso viene in genere segnalato perché il progetto contiene un riferimento a una libreria di classi che non è stata compilata con la BCL Novell. iOS.

Allo stesso modo in cui un'app che usa .NET 4,0 SDK potrebbe non funzionare in un sistema che supporta solo .NET 2,0, una libreria compilata con .NET 4,0 potrebbe non funzionare in Novell. iOS, può usare l'API non presente in Novell. iOS.

La soluzione generale consiste nel compilare la libreria come libreria di classi Novell. iOS. Questa operazione può essere eseguita creando un nuovo progetto libreria di classi Novell. iOS e aggiungendovi tutti i file di origine. Se non si dispone del codice sorgente per la libreria, è necessario contattare il fornitore e richiedere che forniscano una versione compatibile con Novell. iOS della libreria.

<a name="MT0012" />

### <a name="mt0012-incomplete-data-is-provided-to-complete-"></a>MT0012: I dati incompleti vengono forniti per completare *.

Questo errore non viene più segnalato nella versione corrente di Novell. iOS.

<a name="MT0013" />

### <a name="mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a>MT0013: Il supporto per la profilatura richiede l'abilitazione di SGen.

È necessario abilitare SGen (--Sgen) se è abilitata la profilatura (--profilatura).

<a name="MT0014" />

### <a name="mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a>MT0014: IOS * SDK non supporta la compilazione di applicazioni destinate a *.

Questo problema può verificarsi nelle circostanze seguenti:

* ARMv6 è abilitato e viene installato Xcode 4,5 o versione successiva.
* ARMv7s è abilitato e viene installato Xcode 4,4 o versioni precedenti.

Verificare che la versione installata di Xcode supporti le architetture selezionate.

<a name="MT0015" />

### <a name="mt0015-invalid-abi--supported-abis-are-i386-x86_64--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a>MT0015: ABI non valido: *. Le interfacce Abi supportate sono: i386, x86_64, ARMv7, ARMv7 + LLVM, ARMv7 + LLVM + Thumb2, armv7s, armv7s + LLVM, armv7s + LLVM + Thumb2, arm64 e arm64 + LLVM.

Un ABI non valido è stato passato a mTouch. Specificare un valore ABI valido.

<a name="MT0016" />

### <a name="mt0016-the-option--has-been-deprecated"></a>MT0016: L'opzione * è stata deprecata.

L'opzione mTouch indicata è stata deprecata e verrà ignorata.

<a name="MT0017" />

### <a name="mt0017-you-should-provide-a-root-assembly"></a>MT0017: È necessario fornire un assembly radice.

Quando si compila un'app, è necessario specificare un assembly radice, in genere il file eseguibile principale.

<a name="MT0018" />

### <a name="mt0018-unknown-command-line-argument-"></a>MT0018: Argomento della riga di comando sconosciuto: *.

MTouch non riconosce l'argomento della riga di comando indicato nel messaggio di errore.

<a name="MT0019" />

### <a name="mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a>MT0019: È possibile usare una sola opzione--[log | install | Kill | Launch] dev o--[Launch | debug] SIM.

Sono disponibili diverse opzioni per mTouch che non possono essere usate contemporaneamente:

- --logdev
- --installdev
- --killdev
- --launchdev
- --launchdebug
- --launchsim

<a name="MT0020" />

### <a name="mt0020-the-valid-options-for--are-"></a>MT0020: Le opzioni valide per '\*'\*sono ''.

<a name="MT0021" />

### <a name="mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a>MT0021: Non è possibile compilare usando gcc/g + + (--use-GCC) quando si usa il registrar statico (impostazione predefinita durante la compilazione per il dispositivo). Rimuovere il flag--USE-GCC oppure usare il registrar dinamico (--Registrar: Dynamic).

<a name="MT0022" />

### <a name="mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a>MT0022: Le opzioni '--non supportate--Enable-generics-in-registrar ' è--registrar ' non sono compatibili.

Rimuovere entrambe le opzioni `--unsupported--enable-generics-in-registrar` e `--registrar`. A partire da Novell. iOS 7.2.1 il registrar predefinito supporta i generics.

Questo errore non viene più visualizzato (l'argomento `--unsupported--enable-generics-in-registrar` della riga di comando è stato rimosso da mTouch).

<a name="MT0023" />

### <a name="mt0023-application-name-exe-conflicts-with-another-user-assembly"></a>MT0023: Il nome dell'applicazione ' *. exe ' è in conflitto con un altro assembly utente.

Il nome dell'assembly eseguibile e il nome dell'applicazione non possono corrispondere al nome di una dll nell'app. Modificare il nome del file eseguibile.

<a name="MT0024" />

### <a name="mt0024-could-not-find-required-file-"></a>MT0024: Il file richiesto ' *' non è stato trovato.

<a name="MT0025" />

### <a name="mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a>MT0025: Non è stata specificata alcuna versione SDK. Aggiungere `--sdk=X.Y` per specificare quale SDK di iOS usare per compilare l'applicazione.

<a name="MT0026" />

### <a name="mt0026-could-not-parse-the-command-line-argument--"></a>MT0026: Non è stato possibile analizzare l'argomento della riga di comando ' *': *

<a name="MT0027" />

### <a name="mt0027-the-options--and--are-not-compatible"></a>MT0027: Le opzioni '\*' è\*' non sono compatibili.

<a name="MT0028" />

### <a name="mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a>MT0028: Non è possibile abilitare la torta (a torta) quando la destinazione è iOS 4,1 o versioni precedenti. Disabilitare PIE (-Pie: false) o impostare la destinazione di distribuzione almeno per iOS 4,2

<a name="MT0029" />

### <a name="mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a>MT0029: REPL (--Enable-REPL) è supportato solo nel simulatore (--SIM).

REPL è supportato solo se si sta compilando per il simulatore. Ciò significa che se si passa `--enable-repl` a mTouch, è necessario passare `--sim`anche.

<a name="MT0030" />

### <a name="mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a>MT0030: Il nome dell'eseguibile (\*) e il nome dell'app (\*) sono diversi. questa operazione può impedire che i registri di arresto anomalo arrestino correttamente.

Quando Xcode symbolicates (converte gli indirizzi di memoria in nomi di funzione e di file o numeri di riga), il processo potrebbe non riuscire se l'eseguibile e l'app hanno nomi diversi (senza l'estensione).

Per risolvere questo problema, modificare il nome dell'applicazione nelle opzioni di compilazione/iOS del progetto oppure modificare il nome dell'assembly nelle opzioni di compilazione/output del progetto.

<a name="MT0031" />

### <a name="mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a>MT0031: Gli argomenti della riga di comando '--Enable-background-fetch ' è--Launch-for-background-fetch ' richiedono '--launchsim '.

<a name="MT0032" />

### <a name="mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a>MT0032: L'opzione '--debugtrack ' viene ignorata a meno che non sia specificato anche '--debug '.

<a name="MT0033" />

### <a name="mt0033-a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a>MT0033: Un progetto Novell. iOS deve fare riferimento A MonoTouch. dll o Novell. iOS. dll

<a name="MT0034" />

### <a name="mt0034-cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a>MT0034: Non è possibile includere sia ' MonoTouch. dll ' che ' Novell. iOS. dll ' nello stesso progetto Novell. iOS.\*viene fatto riferimento in modo esplicito a'\*', mentre ' *' fa riferimento a' '.

<!-- MT0035 unused -->

<a name="MT0036" />

### <a name="mt0036-cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a>MT0036: Non è possibile avviare un * simulatore per un'app *. Abilitare le architetture corrette nelle opzioni di compilazione iOS del progetto (pagina Avanzate).

<a name="MT0037" />

### <a name="mt0037-monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x86_64"></a>MT0037: MonoTouch. dll non è compatibile con 64 bit. È possibile fare riferimento a Novell. iOS. dll o non compilare per un'architettura a 64 bit (ARM64 e/o x86_64).

<a name="MT0038" />

### <a name="mt0038-the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a>MT0038: I registrar precedenti (--Registrar: oldstatic | olddynamic) non sono supportati quando si fa riferimento a Novell. iOS. dll.

<a name="MT0039" />

### <a name="mt0039-applications-targeting-armv6-cannot-reference-xamariniosdll"></a>MT0039: Le applicazioni destinate a ARMv6 non possono fare riferimento a Novell. iOS. dll.

<a name="MT0040" />

### <a name="mt0040-could-not-find-the-assembly--referenced-by-"></a>MT0040: Non è stato possibile trovare l'\*assembly '', a cui\*fa riferimento ''.

<a name="MT0041" />

### <a name="mt0041-cannot-reference-both-monotouchdll-and-xamariniosdll"></a>MT0041: Non è possibile fare riferimento sia a' MonoTouch. dll ' che a' Novell. iOS. dll '.

<a name="MT0042" />

### <a name="mt0042-no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a>MT0042: Non è stato trovato alcun riferimento a MonoTouch. dll o Novell. iOS. dll. Verrà aggiunto un riferimento a MonoTouch. dll.

<a name="MT0043" />

### <a name="mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a>MT0043: Il Garbage Collector Boehm non è attualmente supportato quando si fa riferimento a' Novell. iOS. dll '. Il Garbage Collector SGen è stato selezionato.

Solo il Garbage Collector SGen è supportato con i progetti unificati. Verificare che non siano presenti flag mTouch aggiuntivi che specifichino Boehm come Garbage Collector.

<a name="MT0044" />

### <a name="mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a>MT0044:--listsim è supportato solo con Xcode 6,0 o versione successiva.

Installare una versione più recente di Xcode.

<a name="MT0045" />

### <a name="mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a>MT0045:--Extension è supportato solo quando si usa l'SDK iOS 8,0 (o versione successiva).

<!-- MT0046 is not reported anymore -->

<a name="MT0047" />

### <a name="mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a>MT0047: La destinazione di distribuzione minima per le applicazioni unificate è 5.1.1, la destinazione di distribuzione corrente è' *'. Selezionare una destinazione di distribuzione più recente nelle opzioni per le applicazioni iOS del progetto.

<!-- MT0048 is not reported anymore -->

<a name="MT0049" />

### <a name="mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a>MT0049: *. Framework è supportato solo se la destinazione di distribuzione è 8,0 o successiva. * le funzionalità potrebbero non funzionare correttamente.

Il Framework specificato non è supportato nella versione iOS a cui fa riferimento la destinazione di distribuzione. Aggiornare la destinazione di distribuzione a una versione più recente di iOS oppure rimuovere l'utilizzo del Framework specificato dall'app.

<!-- MT0050 is not reported anymore -->

<a name="MT0051" />

### <a name="mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a>MT0051: Novell. iOS * richiede Xcode 5,0 o versione successiva. La versione corrente di Xcode (disponibile in *) è *.

Installare una versione più recente di Xcode.

<a name="MT0052" />

### <a name="mt0052-no-command-specified"></a>MT0052: Nessun comando specificato.

Non è stata specificata alcuna azione per mTouch.

<!-- 0053 is used by mmp -->

<a name="MT0054" />

### <a name="mt0054-unable-to-canonicalize-the-path--"></a>MT0054: Impossibile Canonicalize il percorso "*": *

Si tratta di un errore interno. Se viene visualizzato questo errore, inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT0055" />

### <a name="mt0055-the-xcode-path--does-not-exist"></a>MT0055: Il percorso di Xcode ' *' non esiste.

Il percorso di Xcode passato `--sdkroot` con non esiste. Specificare un percorso valido.

<a name="MT0056" />

### <a name="mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a>MT0056: Xcode non è stato trovato nel percorso predefinito (/Applications/Xcode.app). Installare Xcode o passare un percorso personalizzato usando--sdkroot \<percorso >.

<a name="MT0057" />

### <a name="mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a>MT0057: Non è possibile determinare il percorso di Xcode. app dalla radice SDK ' *'. Specificare il percorso completo del bundle Xcode. app.

Il percorso passato usando `--sdkroot` non specifica un'app Xcode valida. Specificare un percorso per un'app Xcode.

<a name="MT0058" />

### <a name="mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a>MT0058: Xcode. app '\*' non è valido (il file '\*' non esiste).

Il percorso passato usando `--sdkroot` non specifica un'app Xcode valida. Specificare un percorso per un'app Xcode.

<a name="MT0059" />

### <a name="mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a>MT0059: Non è stato possibile trovare il Xcode attualmente selezionato nel sistema: *

<a name="MT0060" />

### <a name="mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a>MT0060: Non è stato possibile trovare il Xcode attualmente selezionato nel sistema. ' Xcode-Select--Print-Path ' ha restituito ' *', ma tale directory non esiste.

<a name="MT0061" />

### <a name="mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a>MT0061: Non è stato specificato Xcode. app (con--sdkroot), usando il sistema Xcode segnalato da' Xcode-Select--Print-Path ': *

Si tratta di un avviso informativo, che spiega quale Xcode verrà usato, perché non è stato specificato alcun valore.

<a name="MT0062" />

### <a name="mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a>MT0062: Non è stato specificato Xcode. app (con--sdkroot o ' Xcode-Select--Print-Path '), usando invece il valore predefinito di Xcode: *

Si tratta di un avviso informativo, che spiega quale Xcode verrà usato, perché non è stato specificato alcun valore.

<a name="MT0063" />

### <a name="mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a>MT0063: Il file eseguibile non è stato trovato nell'estensione * (nessuna voce CFBundleExecutable in info. plist)

Ogni info. plist deve avere un eseguibile (usando la voce CFBundleExecutable), tuttavia una voce deve essere generata automaticamente durante la compilazione.

Questo indica in genere un bug in Novell. iOS; inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un test case.

<a name="MT0064" />

### <a name="mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a>MT0064: Novell. iOS supporta solo Framework incorporati con progetti unificati.

Novell. iOS supporta solo Framework incorporati quando si usa il API unificata; aggiornare il progetto per usare la API unificata.

<a name="MT0065" />

### <a name="mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a>MT0065: Novell. iOS supporta solo Framework incorporati quando la destinazione di distribuzione è almeno 8,0 (destinazione di distribuzione corrente: * Framework incorporati: *)

Novell. iOS supporta solo Framework incorporati quando la destinazione di distribuzione è almeno 8,0 (perché le versioni precedenti di iOS non supportano i Framework incorporati).

Aggiornare la destinazione di distribuzione nel file INFO. plist del progetto a 8,0 o versione successiva.

<a name="MT0066" />

### <a name="mt0066-invalid-build-registrar-assembly-"></a>MT0066: Assembly registrar compilazione non valido: *

Questo indica in genere un bug in Novell. iOS; inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un test case.

<a name="MT0067" />

### <a name="mt0067-invalid-registrar-"></a>MT0067: Registrar non valido: *

Questo indica in genere un bug in Novell. iOS; inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un test case.

<a name="MT0068" />

### <a name="mt0068-invalid-value-for-target-framework-"></a>MT0068: Valore non valido per il Framework di destinazione: *.

Un Framework di destinazione non valido è stato passato usando l'argomento--Target-Framework. Specificare un Framework di destinazione valido.

<a name="MT0069" />

<!--### MT0069: currently unused -->

<a name="MT0070" />

### <a name="mt0070-invalid-target-framework--valid-target-frameworks-are-"></a>MT0070: Framework di destinazione non valido: *. I Framework di destinazione validi sono: *.

Un Framework di destinazione non valido è stato passato usando l'argomento--Target-Framework. Specificare un Framework di destinazione valido.

<a name="MT0071" />

### <a name="mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT0071: Piattaforma sconosciuta: *. Questo indica in genere un bug in Novell. iOS; inviare un report sui bug in http://bugzilla.xamarin.com con un test case.

Questo indica in genere un bug in Novell. iOS; inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un test case.

<a name="MT0072" />

### <a name="mt0072-extensions-are-not-supported-for-the-platform-"></a>MT0072: Le estensioni non sono supportate per la piattaforma ' *'.

Questo indica in genere un bug in Novell. iOS; inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un test case.

<a name="MT0073" />

### <a name="mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MT0073: Novell. iOS * non supporta una destinazione di distribuzione * (il valore minimo è *). Selezionare una destinazione di distribuzione più recente nel file INFO. plist del progetto.

La destinazione di distribuzione minima è quella specificata nel messaggio di errore. Selezionare una destinazione di distribuzione più recente nel file INFO. plist del progetto.

Se non è possibile aggiornare la destinazione di distribuzione, usare una versione precedente di Novell. iOS.

<a name="MT0074" />

### <a name="mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a>MT0074: Novell. iOS * non supporta la destinazione di distribuzione minima * (il valore massimo è *). Selezionare una destinazione di distribuzione precedente nel file INFO. plist del progetto o eseguire l'aggiornamento a una versione più recente di Novell. iOS.

Novell. iOS non supporta l'impostazione della destinazione di distribuzione minima su una versione superiore rispetto alla versione per la quale è stata creata questa versione specifica di Novell. iOS.

Selezionare una destinazione di distribuzione minima precedente nel file INFO. plist del progetto o eseguire l'aggiornamento a una versione più recente di Novell. iOS.

<a name="MT0075" />

### <a name="mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a>MT0075: Architettura ' *' non valida per i progetti *. Le architetture valide sono: *

È stata specificata un'architettura non valida. Verificare che l'architettura sia valida.

<a name="MT0076" />

### <a name="mt0076-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a>MT0076: Non è stata specificata alcuna architettura (usando l'argomento--ABI). È necessaria un'architettura per i progetti *.

Questo indica in genere un bug in Novell. iOS; inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un test case.

<a name="MT0077" />

### <a name="mt0077-watchos-projects-must-be-extensions"></a>MT0077: I progetti watchos devono essere estensioni.

Questo indica in genere un bug in Novell. iOS; inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un test case.

<a name="MT0078" />

### <a name="mt0078-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a>MT0078: Le compilazioni incrementali sono abilitate con una destinazione di distribuzione < 8,0 (attualmente *). Questa operazione non è supportata (l'applicazione risultante non verrà avviata in iOS 9), quindi la destinazione della distribuzione verrà impostata su 8,0.

Si tratta di un avviso che informa che la destinazione di distribuzione è stata impostata su 8,0 per questa compilazione, in modo che le compilazioni incrementali funzionino correttamente.

Le compilazioni incrementali sono supportate solo se la destinazione di distribuzione è almeno 8,0 (perché l'applicazione risultante non verrà avviata in iOS 9 in caso contrario).

<a name="MT0079" />

### <a name="mt0079-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a>MT0079: La versione di Xcode consigliata per Novell. iOS * è Xcode * o versione successiva. La versione corrente di Xcode (disponibile in *) è *.

Si tratta di un avviso che informa che la versione corrente di Xcode non è la versione consigliata di Xcode per questa versione di Novell. iOS.

Aggiornare Xcode per garantire un comportamento ottimale.

<a name="MT0080" />

### <a name="mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MT0080: La disabilitazione di NewRefCount,--New-refcount: false, è deprecata.

Si tratta di un avviso che informa che la richiesta di disabilitazione del nuovo refcount (--New-refcount: false) è stata ignorata.

La nuova funzionalità refcount è ora obbligatoria per tutti i progetti e pertanto non è più possibile disabilitarla.

<a name="MT0081" />

### <a name="mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a>MT0081: L'argomento della riga di comando--download-crash-report richiede anche--download-crash-report-to.

<a name="MT0082" />

### <a name="mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a>MT0082: REPL (--Enable-REPL) è supportato solo quando non viene usato il collegamento (--nolink).

<a name="MT0083" />

### <a name="mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a>MT0083: Bitcode solo asm non supportato in watchos. Usare--bitcode: marker o--bitcode: Full.

<a name="MT0084" />

### <a name="mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a>MT0084: Bitcode non è supportato nel simulatore. Non passare--bitcode durante la compilazione per il simulatore.

<a name="MT0085" />

### <a name="mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a>MT0085: Non è stato trovato alcun riferimento a' *'. Verrà aggiunto automaticamente.

<a name="MT0086" />

### <a name="mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a>MT0086: È necessario specificare un Framework di destinazione (--Target-Framework) quando si compila per TVOS o Watchos.

Ciò indica un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT0087" />

### <a name="mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a>MT0087: Le compilazioni incrementali (--Fastdev) non sono supportate con il GC Boehm. Le compilazioni incrementali verranno disabilitate.

<a name="MT0088" />

### <a name="mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a>MT0088: Il catalogo globale deve essere in modalità cooperativa per le app watchos. Rimuovere l'argomento--Coop: false per mTouch.

<a name="MT0089" />

### <a name="mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a>MT0089: L'opzione '\*' non può assumere il valore\*'' quando è abilitata la modalità cooperativa per il GC.

<a name="MT0091" />

### <a name="mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MT0091: Questa versione di Novell. iOS richiede l'SDK * (fornito con Xcode *). Aggiornare Xcode per ottenere i file di intestazione necessari o impostare il comportamento del linker gestito per collegare solo gli SDK del Framework (per provare a evitare le nuove API).

Per compilare l'applicazione, Novell. iOS richiede i file di intestazione, dalla versione SDK specificata nel messaggio di errore. Il metodo consigliato per correggere questo errore consiste nell'aggiornare Xcode per ottenere l'SDK necessario, inclusi tutti i file di intestazione necessari. Se sono installate più versioni di Xcode o si vuole usare un Xcode in un percorso non predefinito, assicurarsi di impostare il percorso di Xcode corretto nelle preferenze dell'IDE.

Una possibile soluzione alternativa consiste nell'abilitare il linker gestito. Questa operazione rimuoverà l'API non usata, incluso, nella maggior parte dei casi, la nuova API in cui i file di intestazione sono mancanti (o incompleti). Questa operazione non funzionerà tuttavia se il progetto usa l'API introdotta in un SDK più recente rispetto a quella fornita da Xcode.

Una soluzione Last-Straw è quella di usare una versione precedente di Novell. iOS, che supporta l'SDK richiesto dal progetto.

<!-- MT0092 used by mlaunch -->

<a name="MT0093" />

### <a name="mt0093-could-not-find-mlaunch"></a>MT0093: Impossibile trovare ' mlaunch '.

<!-- MT0094 is not reported anymore -->

<a name="MT0095" />

### <a name="mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a>MT0095: Non è stato possibile copiare i file AOT nella directory di destinazione {dest}: {Error}

<a name="MT0096" />

### <a name="mt0096-no-reference-to-xamariniosdll-was-found"></a>MT0096: Nessun riferimento a Novell. iOS. dll trovato.

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

<a name="MT0099" />

### <a name="mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0099: Errore interno *. Inviare un report sui bug con un test case (http://bugzilla.xamarin.com).

Questo messaggio di errore viene segnalato quando una verifica di coerenza interna in Novell. iOS ha esito negativo.

Questo indica in genere un bug in Novell. iOS; inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un test case.

<a name="MT0100" />

### <a name="mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0100: Destinazione compilazione assembly non valida:' *'. Inviare un report sui bug con un test case (http://bugzilla.xamarin.com).

Questo messaggio di errore viene segnalato quando una verifica di coerenza interna in Novell. iOS ha esito negativo.

Questo indica in genere un bug in Novell. iOS; inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un test case.

<a name="MT0101" />

### <a name="mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a>MT0101: L'assembly ' *' è specificato più volte negli argomenti--assembly-build-target.

L'assembly indicato nel messaggio di errore viene specificato più volte negli argomenti--assembly-build-target. Verificare che ogni assembly venga indicato una sola volta.

<a name="MT0102" />

### <a name="mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a>MT0102: Gli assembly '\*' è\*' hanno lo stesso nome di destinazione (\*''), ma destinazioni diverse (\*'' è *').

Gli assembly indicati nel messaggio di errore presentano destinazioni di compilazione in conflitto.

Ad esempio:

```
  --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary
```

Questo esempio sta provando a creare una libreria dinamica e un Framework con la stessa marca (`MyBinary`).

<a name="MT0103" />

### <a name="mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a>MT0103: L'oggetto statico '\*' contiene più di un assembly ('\*'), ma ogni oggetto statico deve corrispondere esattamente a un assembly.

Gli assembly indicati nel messaggio di errore vengono tutti compilati in un singolo oggetto statico. Questa operazione non è consentita, ogni assembly deve essere compilato in un oggetto statico diverso.

Ad esempio:

```
--assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary
```

In questo esempio si tenta di compilare un oggetto`MyBinary`statico () costituito da due`Assembly1.dll` assembly `Assembly2.dll`(e), che non è consentito.

<a name="MT0105" />

### <a name="mt0105-no-assembly-build-target-was-specified-for-"></a>MT0105: Non è stata specificata alcuna destinazione per la compilazione di assembly per ' *'.

Quando si specifica la destinazione di compilazione `--assembly-build-target`dell'assembly usando, a ogni assembly nell'app deve essere assegnata una destinazione di compilazione.

Questo errore viene segnalato quando l'assembly indicato nel messaggio di errore non dispone di una destinazione di compilazione dell'assembly assegnata.

`--assembly-build-target` Per ulteriori informazioni, vedere la documentazione relativa a.

<a name="MT0106" />

### <a name="mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a>MT0106: Il nome della destinazione di compilazione\*dell'assembly '' non è valido\*: il carattere '' non è consentito.

Il nome della destinazione della compilazione dell'assembly deve essere un nome di file valido.

Ad esempio, questi valori attiveranno questo errore:

```
--assembly-build-target:Assembly1.dll=staticobject=my/path.o
```

Poiché `my/path.o` non è un nome di file valido a causa del carattere separatore di directory.

<a name="MT0107" />

### <a name="mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a>MT0107: Per gli assembly\*'' sono disponibili ottimizzazioni LLVM personalizzate\*diverse (), che non sono consentite quando vengono compilate in un unico file binario.

<a name="MT0108" />

### <a name="mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a>MT0108: La destinazione di compilazione dell'assembly ' *' non corrisponde ad alcun assembly.

<a name="MT0109" />

### <a name="mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a>MT0109: L'assembly '{0}' è stato caricato da un percorso diverso rispetto al percorso specificato (percorso specificato {1}:, percorso effettivo {2}:).

Si tratta di un avviso che indica che un assembly a cui fa riferimento l'applicazione è stato caricato da un percorso diverso da quello richiesto.

Questo potrebbe significare che l'app fa riferimento a più assembly con lo stesso nome, ma da posizioni diverse, che potrebbero causare risultati imprevisti (verrà usato solo il primo assembly).

<a name="MT0110" />

### <a name="mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a>MT0110: Le compilazioni incrementali sono state disabilitate perché questa versione di Novell. iOS non supporta le compilazioni incrementali nei progetti che includono librerie di associazione di terze parti e che vengono compilate in bitcode.

Le compilazioni incrementali sono state disabilitate perché questa versione di Novell. iOS non supporta le compilazioni incrementali nei progetti che includono librerie di associazione di terze parti e che vengono compilate in bitcode (progetti tvOS e watchos).

Non è richiesta alcuna azione da parte dell'utente, questo messaggio è puramente informativo.

Per altre informazioni, vedere il bug #[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710).

Questo avviso non viene più segnalato.

<a name="MT0111" />

### <a name="mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a>MT0111 Bitcode è stato abilitato perché questa versione di Novell. iOS non supporta la compilazione di progetti watchos con LLVM senza abilitare bitcode.

Bitcode è stato abilitato automaticamente perché questa versione di Novell. iOS non supporta la compilazione di progetti watchos con LLVM senza abilitare bitcode.

Non è richiesta alcuna azione da parte dell'utente, questo messaggio è puramente informativo.

Per altre informazioni, vedere il bug #[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634).

<a name="MT0112" />

### <a name="mt0112-native-code-sharing-has-been-disabled-because-"></a>MT0112: La condivisione del codice nativo è stata disabilitata perché *

La condivisione del codice può essere disabilitata per più motivi:

* Poiché la destinazione di distribuzione dell'app contenitore è precedente a iOS 8,0 (*).

La condivisione del codice nativo richiede iOS 8,0 perché la condivisione del codice nativo viene implementata usando i Framework utente, introdotta con iOS 8,0.

* Poiché l'app contenitore include gli assembly I18N (*).

La condivisione del codice nativo non è attualmente supportata se l'app contenitore include gli assembly I18N.

* Poiché l'app contenitore contiene definizioni XML personalizzate per il linker gestito (*).

La condivisione del codice nativo richiede non è supportata per i progetti che usano definizioni XML personalizzate per il linker gestito.

<a name="MT0113" />

### <a name="mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a>MT0113: La condivisione del codice nativo è stata disabilitata per l'estensione ' *' perché *.

* Poiché le opzioni di bitcode sono diverse dall'app contenitore\*() e dall'estensione\*().

  Per la condivisione del codice nativo è necessario che le opzioni bitcode corrispondano tra i progetti che condividono il codice.

* Poiché le opzioni--assembly-build-target sono diverse tra l'app contenitore (\*) e l'estensione (\*).

  Per la condivisione del codice nativo è necessario che le opzioni--assembly-build-target siano identiche tra i progetti che condividono il codice.

  Questa condizione può verificarsi se le compilazioni incrementali non sono abilitate o disabilitate in tutti i progetti.

* Poiché gli assembly i18n sono diversi tra l'app contenitore (\*) e l'estensione (\*).

  La condivisione del codice nativo non è attualmente supportata per le estensioni che includono gli assembly I18N.

* Poiché gli argomenti del compilatore AOT sono diversi tra l'app contenitore (\*) e l'estensione (\*).

  Per la condivisione del codice nativo è necessario che gli argomenti del compilatore AOT non differiscano tra i progetti che condividono il codice.

* Poiché gli altri argomenti del compilatore AOT sono diversi tra l'app contenitore (\*) e l'estensione (\*).

  Per la condivisione del codice nativo è necessario che gli argomenti del compilatore AOT non differiscano tra i progetti che condividono il codice.

  Questa condizione si verifica se l'esecuzione di tutte le operazioni float a 32 bit come float a 64 bit è diversa tra i progetti.

* Poiché LLVM non è abilitato o disabilitato sia nell'app contenitore (\*) che nell'estensione (\*).

  Per la condivisione del codice nativo è necessario che LLVM sia abilitato o disabilitato per tutti i progetti che condividono il codice.

* Poiché le impostazioni del linker gestite sono diverse tra l'app contenitore\*() e l'estensione\*().

  Per la condivisione del codice nativo è necessario che le impostazioni del linker gestite siano identiche per tutti i progetti che condividono il codice.

* Poiché gli assembly ignorati per il linker gestito sono diversi tra l'app contenitore (\*) e l'estensione (\*).

  Per la condivisione del codice nativo è necessario che le impostazioni del linker gestite siano identiche per tutti i progetti che condividono il codice.

* Poiché l'estensione dispone di definizioni XML personalizzate per il linker gestito (*).

  La condivisione del codice nativo richiede non è supportata per i progetti che usano definizioni XML personalizzate per il linker gestito.

* Poiché l'app contenitore non viene compilata per l'ABI * (mentre è in corso la compilazione dell'estensione per questa ABI).

  Per la condivisione del codice nativo è necessario che l'app contenitore venga compilata per tutte le architetture per le quali viene compilata l'estensione.

  Ad esempio, questa condizione si verifica quando si compila un'estensione per ARM64 + ARMv7, ma l'app contenitore viene compilata solo per ARM64.

* Poiché l'app contenitore viene compilata per \*l'ABI, che non è compatibile con l'Abi dell'\*estensione ().

  Per la condivisione del codice nativo è necessario che tutti i progetti siano compilati per la stessa API.

  Ad esempio, questa condizione si verifica quando si compila un'estensione per ARMv7 + LLVM + Thumb2, ma l'app contenitore viene compilata solo per ARMv7 + LLVM.

* Poiché l'app contenitore fa riferimento all'assembly '\*' di '\*', mentre l'estensione fa riferimento a una versione diversa da' *'.

  Per la condivisione del codice nativo è necessario che tutti i progetti che condividono il codice usino le stesse versioni per tutti gli assembly.

<!-- MT0114: used by mmp -->

<a name="MT0115" />

### <a name="mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a>MT0115: È consigliabile fare riferimento a simboli dinamici usando il codice (--Dynamic-symbol-Mode = code) quando bitcode è abilitato.

I progetti Novell. iOS spesso fanno riferimento a simboli nativi in modo dinamico, il che significa che il linker nativo potrebbe rimuovere questi simboli nativi durante il processo di collegamento nativo, perché il linker nativo non verifica che questi simboli vengano usati.

In genere, Novell. IOS chiederà al linker nativo di preservare tali simboli `-u symbol` (usando il flag del linker), ma quando si compila per bitcode il linker nativo `-u` non accetta il flag.

Novell. iOS ha implementato una soluzione alternativa: viene generato codice nativo aggiuntivo che fa riferimento a questi simboli, quindi il linker nativo vedrà che questi simboli vengono usati. Questa operazione viene eseguita automaticamente durante la compilazione in bitcode.

Se `--dynamic-symbol-mode=linker` viene passato a mTouch, questa soluzione alternativa verrà disabilitata e Novell. iOS tenterà di passare `-u` al linker nativo. Questa operazione comporterà probabilmente errori nativi del linker.

La soluzione consiste nel rimuovere l' `--dynamic-symbol-mode=linker` argomento dagli argomenti mTouch aggiuntivi nelle opzioni di compilazione del progetto.

<!-- 0116 - 0124: free to use -->

<a name="MT0116" />

### <a name="mt0116-invalid-architecture-arch-32-bit-architectures-are-not-supported-when-deployment-target-is-11-or-later-make-sure-the-project-does-not-build-for-a-32-bit-architecture"></a>MT0116: Architettura non valida: {Arch}. le architetture a 32 bit non sono supportate quando la destinazione di distribuzione è 11 o successiva. Verificare che il progetto non venga compilato per un'architettura a 32 bit.

iOS 11 non contiene il supporto per le applicazioni a 32 bit, pertanto non è supportata la compilazione per un'applicazione a 32 bit quando la destinazione di distribuzione è iOS 11 o versione successiva.

Modificare l'architettura di destinazione nelle opzioni di compilazione iOS del progetto in arm64 o modificare la destinazione di distribuzione nel file INFO. plist del progetto in una versione precedente di iOS.

<a name="MT0117" />

### <a name="mt0117-cant-launch-a-32-bit-app-on-a-simulator-that-only-supports-64-bit"></a>MT0117: Non è possibile avviare un'app a 32 bit in un simulatore che supporta solo 64 bit.

<a name="MT0118" />

### <a name="mt0118-aot-files-could-not-be-found-at-the-expected-directory-msymdir"></a>MT0118: Impossibile trovare i file AOT nella directory prevista ' {msymdir}'.

<!-- 0119 - 0123: free to use -->

<a name="MT0123" />

### <a name="mt0123-the-executable-assembly--does-not-reference-"></a>MT0123: L'assembly eseguibile * non fa riferimento a *.

Non è stato possibile trovare alcun riferimento all'assembly della piattaforma (Novell. iOS. dll/Novell. TVOS. dll/Novell. Watchos. dll) nell'assembly eseguibile.

Questa situazione si verifica in genere quando non è presente codice nel progetto eseguibile che usa qualsiasi elemento dall'assembly della piattaforma; ad esempio, un metodo Main vuoto (e nessun altro codice) visualizzerà questo errore:

```csharp
class Program {
    void Main (string[] args)
    {
    }
}
```

L'uso di un'API dall'assembly della piattaforma consente di risolvere l'errore:

```csharp
class Program {
    void Main (string[] args)
    {
        System.Console.WriteLine (typeof (UIKit.UIWindow));
    }
}
```

<a name="MT0124" />

### <a name="mt0124-could-not-set-the-current-language-to-lang-according-to-langlang-exception"></a>MT0124: Non è stato possibile impostare la lingua corrente su' {lang}' (in base a LANG = {LANG}): {Exception}

Si tratta di un avviso che indica che non è stato possibile impostare la lingua corrente sulla lingua del messaggio di errore.

Per impostazione predefinita, la lingua corrente è la lingua di sistema.

<a name="MT0125" />

### <a name="mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a>MT0125: L'argomento della riga di comando--assembly-build-target viene ignorato nel simulatore.

Non è richiesta alcuna azione. questo messaggio è puramente informativo.

<a name="MT0126" />

### <a name="mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a>MT0126: Le compilazioni incrementali sono state disabilitate perché le compilazioni incrementali non sono supportate nel simulatore.

Non è richiesta alcuna azione. questo messaggio è puramente informativo.

<a name="MT0127" />

### <a name="mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a>MT0127: Le compilazioni incrementali sono state disabilitate perché questa versione di Novell. iOS non supporta le compilazioni incrementali nei progetti che includono più di una libreria di binding di terze parti.

Le compilazioni incrementali sono state disabilitate automaticamente perché questa versione di Novell. iOS non sempre compila i progetti con più librerie di binding di terze parti correttamente.

Non è richiesta alcuna azione. questo messaggio è puramente informativo.

Per altre informazioni, vedere il bug #[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727).

<a name="MT0128" />

### <a name="mt0128-could-not-touch-the-file--"></a>MT0128: Non è stato possibile toccare il file ' *': *

Si è verificato un errore durante il tocco di un file (operazione eseguita per garantire che le compilazioni parziali vengano eseguite correttamente).

Questo avviso può essere probabilmente ignorato. in caso di problemi, si verifica un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) che verrà analizzato.

<a name="MT0135" />

### <a name="mt0135-did-not-link-system-framework-0-referenced-by-assembly-1-because-it-was-introduced-in-2-3-and-were-using-the-2-4-sdk"></a>MT0135: Il Framework di{0}sistema '' (a cui fa riferimento l'assembly '{1}') non è stato collegato {2} perché è stato introdotto in {3}e {2} viene usato l' {4} SDK.

Per compilare l'applicazione, Novell. iOS deve collegarsi a librerie di sistema, alcune delle quali dipendono dalla versione SDK specificata nel messaggio di errore. Poiché si sta usando una versione precedente dell'SDK, le chiamate a tali API possono avere esito negativo in fase di esecuzione.

Il metodo consigliato per correggere questo errore consiste nell'aggiornare Xcode per ottenere l'SDK necessario. Se sono installate più versioni di Xcode o si vuole usare un Xcode in un percorso non predefinito, assicurarsi di impostare il percorso di Xcode corretto nelle preferenze dell'IDE.

In alternativa, consentire al [linker](https://docs.microsoft.com/en-us/xamarin/ios/deploy-test/linker) gestito di rimuovere le API inutilizzate, tra cui, nella maggior parte dei casi, quelle nuove che richiedono la libreria specificata. Tuttavia, questa operazione non funzionerà se il progetto richiede API introdotte in un SDK più recente rispetto a quello fornito da Xcode.

Come ultima soluzione di paglia, usare una versione precedente di Novell. iOS che non richiede che questi nuovi SDK siano presenti durante il processo di compilazione.

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx: Messaggi di errore relativi al progetto

### <a name="mt10xx-installer--mtouch"></a>MT10xx: Programma di installazione/mTouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

<a name="MT1001" />

### <a name="mt1001-could-not-find-an-application-at-the-specified-directory"></a>MT1001: Impossibile trovare un'applicazione nella directory specificata

<a name="MT1002" />

### <a name="mt1002-could-not-create-symlinks-files-were-copied"></a>MT1002: Non è stato possibile creare i collegamenti simbolici, file copiati

<a name="MT1003" />

### <a name="mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a>MT1003: Non è stato possibile terminare l'applicazione ' *'. Potrebbe essere necessario terminare l'applicazione manualmente.

<a name="MT1004" />

### <a name="mt1004-could-not-get-the-list-of-installed-applications"></a>MT1004: Non è stato possibile ottenere l'elenco delle applicazioni installate.

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx: Messaggi di errore relativi al progetto

<a name="MT1005" />

### <a name="mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a>MT1005: Non è stato possibile terminare l'\*applicazione '' sul dispositivo\*'': *-potrebbe essere necessario terminare l'applicazione manualmente.

<a name="MT1006" />

### <a name="mt1006-could-not-install-the-application--on-the-device--"></a>MT1006: Non è stato possibile installare l'\*applicazione '' nel dispositivo\*'': *.

<a name="MT1007" />

### <a name="mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a>MT1007: Non è stato possibile avviare l'\*applicazione '' nel dispositivo\*'': *. È comunque possibile avviare l'applicazione manualmente toccando.

<a name="MT1008" />

### <a name="mt1008-failed-to-launch-the-simulator"></a>MT1008: Non è stato possibile avviare il simulatore

Questo errore viene segnalato se mTouch non è riuscito ad avviare il simulatore.   Questo problema può verificarsi a volte perché è già in esecuzione un processo del simulatore non aggiornato o non attivo.

Il seguente comando emesso nella riga di comando di UNIX può essere usato per terminare i processi del simulatore bloccati:

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

<a name="MT1009" />

### <a name="mt1009-could-not-copy-the-assembly--to--"></a>MT1009: Non è stato possibile copiare l'\*assembly ''\*in '': *

Si tratta di un problema noto in alcune versioni di Novell. iOS.

Se si verifica questa situazione, provare la soluzione alternativa seguente:

```bash
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

Tuttavia, poiché questo problema è stato risolto nella versione più recente di Novell. iOS, inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con le informazioni complete sulla versione e l'output del log di compilazione.

<a name="MT1010" />

### <a name="mt1010-could-not-load-the-assembly--"></a>MT1010: Non è stato possibile caricare l'assembly ' *': *

<a name="MT1011" />

### <a name="mt1011-could-not-add-missing-resource-file-"></a>MT1011: Non è stato possibile aggiungere il file di risorse mancante:' *'

<a name="MT1012" />

### <a name="mt1012-failed-to-list-the-apps-on-the-device--"></a>MT1012: Non è stato possibile elencare le app nel dispositivo ' *': *

<a name="MT1013" />

### <a name="mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT1013: Errore di rilevamento delle dipendenze: nessun file da confrontare. Inviare un report sui bug in http://bugzilla.xamarin.com con un test case.

Ciò indica un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un test case.

<a name="MT1014" />

### <a name="mt1014-failed-to-re-use-cached-version-of--"></a>MT1014: Non è stato possibile riutilizzare la versione memorizzata nella cache di ' *': *.

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: Non è stato possibile creare il file eseguibile ' *': *

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: Non è stato possibile creare il file eseguibile ' *': *

<a name="MT1016" />

### <a name="mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a>MT1016: Impossibile creare il file di avviso perché esiste già una directory con lo stesso nome.

Rimuovere la directory `NOTICE` dal progetto.

<a name="MT1017" />

### <a name="mt1017-failed-to-create-the-notice-file-"></a>MT1017: Impossibile creare il file di avviso: *.

<a name="MT1018" />

### <a name="mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a>MT1018: L'applicazione non ha superato i controlli di firma codice e non è stato possibile installarla nel dispositivo ' *'. Verificare i certificati, i profili di provisioning e gli ID bundle. Probabilmente il dispositivo non fa parte del profilo di provisioning selezionato (errore: 0xe8008015).

<a name="MT1019" />

### <a name="mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a>MT1019: L'applicazione ha diritti non supportati dal profilo di provisioning corrente e non è stato possibile installarli nel dispositivo ' *'. Per informazioni più dettagliate, consultare il log del dispositivo iOS (errore: 0xe8008016).

Questo problema può verificarsi se:

* L'applicazione dispone di diritti non supportati dal profilo di provisioning corrente.
  Possibili soluzioni:
  - Specificare un profilo di provisioning diverso che supporti i diritti necessari per l'applicazione.
  - Rimuovere i diritti non supportati nel profilo di provisioning corrente.
* Il dispositivo in cui si sta provando a eseguire la distribuzione non è incluso nel profilo di provisioning in uso.
  Possibili soluzioni:
  - Creare una nuova app da un modello in Xcode, selezionare lo stesso profilo di provisioning e distribuirlo nello stesso dispositivo. A volte Xcode può aggiornare automaticamente i profili di provisioning con i nuovi dispositivi (in altri casi Xcode chiederà cosa fare).
  -Accedere al centro per sviluppatori iOS e aggiornare il profilo di provisioning con il nuovo dispositivo, quindi scaricare il profilo di provisioning aggiornato nel computer.

Nella maggior parte dei casi, altre informazioni sull'errore verranno stampate nel log del dispositivo iOS, che può aiutare a diagnosticare il problema.

<a name="MT1020" />

### <a name="mt1020-failed-to-launch-the-application--on-the-device--"></a>MT1020: Non è stato possibile avviare l'\*applicazione '' nel dispositivo\*'': *

<a name="MT1021" />

### <a name="mt1021-could-not-copy-the-file--to--2"></a>MT1021: Non è stato possibile copiare il\*file ''\*in '':{2}

Non è stato possibile copiare un file. Il messaggio di errore dell'operazione di copia contiene ulteriori informazioni sull'errore.

<a name="MT1022" />

### <a name="mt1022-could-not-copy-the-directory--to--2"></a>MT1022: Non è stato possibile copiare la\*directory ''\*in '':{2}

Non è stato possibile copiare una directory. Il messaggio di errore dell'operazione di copia contiene ulteriori informazioni sull'errore.

<a name="MT1023" />

### <a name="mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a>MT1023: Non è stato possibile comunicare con il dispositivo per trovare l'applicazione ' *': *

Si è verificato un errore durante il tentativo di ricerca di un'applicazione nel dispositivo.

Per risolvere il problema, eseguire le operazioni seguenti:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.

<a name="MT1024" />

### <a name="mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a>MT1024: Non è stato possibile verificare la firma dell'applicazione nel dispositivo ' *'. Verificare che il profilo di provisioning sia installato e non scaduto (errore: 0xe8008017).

Il dispositivo ha rifiutato l'installazione dell'applicazione perché non è stato possibile verificare la firma.

Verificare che il profilo di provisioning sia installato e non sia scaduto.

<a name="MT1025" />

### <a name="mt1025-could-not-list-the-crash-reports-on-the-device-"></a>MT1025: Impossibile elencare i report di arresto anomalo del dispositivo *.

Si è verificato un errore durante il tentativo di elencare i report di arresto anomalo del dispositivo.

Per risolvere il problema, eseguire le operazioni seguenti:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes. verranno rimossi tutti i report di arresto anomalo del dispositivo.

<a name="MT1026" />

### <a name="mt1026-could-not-download-the-crash-report--from-the-device-"></a>MT1026: Non è stato possibile scaricare il report di arresto anomalo * dal dispositivo *.

Si è verificato un errore durante il tentativo di scaricare le segnalazioni di arresti anomali dal dispositivo.

Per risolvere il problema, eseguire le operazioni seguenti:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes. verranno rimossi tutti i report di arresto anomalo del dispositivo.

<a name="MT1027" />

### <a name="mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a>MT1027: Non è possibile usare Xcode 7 + per avviare applicazioni nei dispositivi con iOS * (Xcode 7 supporta solo iOS 6 +).

Non è possibile usare Xcode 7 + per avviare le applicazioni nei dispositivi con la versione di iOS inferiore a 6,0.

Usare una versione precedente di Xcode oppure toccare l'app manualmente per avviarla.

<a name="MT1028" />

### <a name="mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a>MT1028: La specifica del dispositivo non è valida:' *'. Sono previsti ' iOS ',' watchos ' o ' all'.

La specifica del dispositivo passata usando--Device non è valida. I valori validi sono:' iOS ',' watchos ' o ' all'.

<a name="MT1029" />

### <a name="mt1029-could-not-find-an-application-at-the-specified-directory-"></a>MT1029: Non è stato possibile trovare un'applicazione nella directory specificata: *

Il percorso dell'applicazione passato a--launchdev non esiste. Specificare un bundle dell'app valido.

<a name="MT1030" />

### <a name="mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a>MT1030: L'avvio delle applicazioni sul dispositivo con un identificatore del bundle è deprecato. Passare il percorso completo al bundle da avviare.

È consigliabile passare il percorso dell'app da avviare sul dispositivo anziché solo l'ID del bundle.

<a name="MT1031" />

### <a name="mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1031: Non è stato possibile avviare l'\*app '' sul dispositivo\*'' perché il dispositivo è bloccato. Sbloccare il dispositivo e riprovare.

Sbloccare il dispositivo e riprovare.

<a name="MT1032" />

### <a name="mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a>MT1032: Il file eseguibile dell'applicazione potrebbe essere troppo grande (* MB) per l'esecuzione nel dispositivo. Se bitcode è stato abilitato, potrebbe essere necessario disabilitarlo per lo sviluppo, ma è necessario solo inviare le applicazioni ad Apple.

<a name="MT1033" />

### <a name="mt1033-could-not-uninstall-the-application--from-the-device--"></a>MT1033: Non è stato possibile disinstallare l'\*applicazione '' dal dispositivo\*'': *

<!-- 1034 used by mmp -->

<a name="MT1035" />

### <a name="mt1035-cannot-include-different-versions-of-the-framework-name"></a>MT1035: Non è possibile includere versioni diverse del Framework ' {name}'

Non è possibile collegarsi a versioni diverse dello stesso Framework.

Questa situazione si verifica in genere quando un'estensione fa riferimento a una versione diversa di un Framework rispetto all'app contenitore (possibilmente tramite un assembly di associazione di terze parti).

In seguito a questo errore saranno presenti più errori [MT1036](#MT1036) che elencano i percorsi per ogni Framework diverso.

<a name="MT1036" />

### <a name="mt1036-framework-name-included-from-path-related-to-previous-error"></a>MT1036: Framework ' {name}' incluso da: {Path} (correlato all'errore precedente)

Questo errore viene segnalato solo insieme a [MT1036](#MT1036). Per ulteriori informazioni, vedere [MT1036](#MT1036) .

### <a name="mt11xx-debug-service"></a>MT11xx: Servizio di debug

<!--
  MT11xx DebugService.cs
  -->

<a name="MT1101" />

### <a name="mt1101-could-not-start-app"></a>MT1101: Non è stato possibile avviare l'app

<a name="MT1102" />

### <a name="mt1102-could-not-attach-to-the-app-to-kill-it-"></a>MT1102: Non è stato possibile connettersi all'app (per terminarla): *

<a name="MT1103" />

### <a name="mt1103-could-not-detach"></a>MT1103: Non è stato possibile scollegare

<a name="MT1104" />

### <a name="mt1104-failed-to-send-packet-"></a>MT1104: Non è stato possibile inviare il pacchetto: *

<a name="MT1105" />

### <a name="mt1105-unexpected-response-type"></a>MT1105: Tipo di risposta imprevisto

<a name="MT1106" />

### <a name="mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a>MT1106: Non è stato possibile ottenere l'elenco delle applicazioni nel dispositivo: Timeout della richiesta.

<a name="MT1107" />

### <a name="mt1107-application-failed-to-launch-"></a>MT1107: Non è stato possibile avviare l'applicazione: *

Verificare se il dispositivo è bloccato.

Se si distribuisce un'app aziendale o si usa un profilo di provisioning gratuito, potrebbe essere considerato attendibile lo sviluppatore (questo argomento è illustrato <a href="https://stackoverflow.com/a/30726375/183422">qui</a>).

<a name="MT1108" />

### <a name="mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a>MT1108: Gli strumenti di sviluppo per questo dispositivo XX (YY) non sono stati trovati.

Per alcune operazioni di mTouch è necessario `DeveloperDiskImage.dmg` che il file sia presente.   Questo file fa parte di Xcode ed è in genere posizionato in relazione all'SDK usato per la compilazione in `Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg`.

Questo errore può verificarsi perché non si dispone di un DeveloperDiskImage. dmg corrispondente al dispositivo connesso.

<a name="MT1109" />

### <a name="mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1109: Non è stato possibile avviare l'applicazione perché il dispositivo è bloccato. Sbloccare il dispositivo e riprovare.

Verificare se il dispositivo è bloccato.

<a name="MT1110" />

### <a name="mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a>MT1110: Non è stato possibile avviare l'applicazione a causa di restrizioni di sicurezza iOS. Verificare che lo sviluppatore sia attendibile.

Se si distribuisce un'app aziendale o si usa un profilo di provisioning gratuito, potrebbe essere considerato attendibile lo sviluppatore (questo argomento è illustrato <a href="https://stackoverflow.com/a/30726375/183422">qui</a>).

<a name="MT1111" />

### <a name="mt1111-application-launched-successfully-but-its-not-possible-to-wait-for-the-app-to-exit-as-requested-because-its-not-possible-to-detect-app-termination-when-launching-using-gdbserver"></a>MT1111: L'applicazione è stata avviata, ma non è possibile attendere che l'app venga chiusa come richiesto perché non è possibile rilevare la terminazione dell'app quando viene avviata con gdbserver.

### <a name="mt12xx-simulator"></a>MT12xx: Simulatore

<!--
  MT12xx simcontroller.cs
  -->

<a name="MT1201" />

### <a name="mt1201-could-not-load-the-simulator-"></a>MT1201: Non è stato possibile caricare il simulatore: *

<a name="MT1202" />

### <a name="mt1202-invalid-simulator-configuration-"></a>MT1202: Configurazione del simulatore non valida: *

<a name="MT1203" />

### <a name="mt1203-invalid-simulator-specification-"></a>MT1203: Specifica del simulatore non valida: *

<a name="MT1204" />

### <a name="mt1204-invalid-simulator-specification--runtime-not-specified"></a>MT1204: La specifica del simulatore ' *' non è valida: il runtime non è specificato.

<a name="MT1205" />

### <a name="mt1205-invalid-simulator-specification--device-type-not-specified"></a>MT1205: La specifica del simulatore ' *' non è valida: tipo di dispositivo non specificato.

<a name="MT1206" />

### <a name="mt1206-could-not-find-the-simulator-runtime-"></a>MT1206: Il runtime del simulatore ' *' non è stato trovato.

<a name="MT1207" />

### <a name="mt1207-could-not-find-the-simulator-device-type-"></a>MT1207: Il tipo di dispositivo del simulatore ' *' non è stato trovato.

<a name="MT1208" />

### <a name="mt1208-could-not-find-the-simulator-runtime-"></a>MT1208: Il runtime del simulatore ' *' non è stato trovato.

<a name="MT1209" />

### <a name="mt1209-could-not-find-the-simulator-device-type-"></a>MT1209: Il tipo di dispositivo del simulatore ' *' non è stato trovato.

<a name="MT1210" />

### <a name="mt1210-invalid-simulator-specification--unknown-key-"></a>MT1210: Specifica del simulatore \*non valida: chiave\*sconosciuta ''

<a name="MT1211" />

### <a name="mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a>MT1211: La versione del simulatore '\*' non supporta il tipo di simulatore ''\*

<a name="MT1212" />

### <a name="mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a>MT1212: Non è stato possibile creare una versione del simulatore in cui digitare = * e Runtime = *.

<a name="MT1213" />

### <a name="mt1213-invalid-simulator-specification-for-xcode-4-"></a>MT1213: Specifica del simulatore non valida per Xcode 4: *

<a name="MT1214" />

### <a name="mt1214-invalid-simulator-specification-for-xcode-5-"></a>MT1214: Specifica del simulatore non valida per Xcode 5: *

<a name="MT1215" />

### <a name="mt1215-invalid-sdk-specified-"></a>MT1215: SDK specificato non valido: *

<a name="MT1216" />

### <a name="mt1216-could-not-find-the-simulator-udid-"></a>MT1216: Non è stato possibile trovare il simulatore UDID ' *'.

<a name="MT1217" />

### <a name="mt1217-could-not-load-the-app-bundle-at-"></a>MT1217: Non è stato possibile caricare il bundle dell'app in ' *'.

<a name="MT1218" />

### <a name="mt1218-no-bundle-identifier-found-in-the-app-at-"></a>MT1218: Non sono stati trovati identificatori del bundle nell'app in ' *'.

<a name="MT1219" />

### <a name="mt1219-could-not-find-the-simulator-for-"></a>MT1219: Il simulatore per ' *' non è stato trovato.

<a name="MT1220" />

### <a name="mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a>MT1220: Non è stato possibile trovare il runtime del simulatore più recente per il dispositivo ' *'.

Questo indica in genere un problema con Xcode.

Per risolvere il problema, eseguire le operazioni seguenti:

* Usare il simulatore una volta in Xcode.
* Passare una versione esplicita dell'SDK usando- \<-SDK version >.
* Reinstallare Xcode.

<a name="MT1221" />

### <a name="mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a>MT1221: Non è stato possibile trovare il simulatore iPhone associato per il simulatore Watchos ' *'.

Quando si avvia un'app Watchos in un simulatore Watchos, deve essere presente anche un simulatore iOS associato.

I simulatori di controllo possono essere abbinati a simulatori iOS usando l'interfaccia utente dei dispositivi di Xcode (finestra dei menu-> dispositivi).

### <a name="mt13xx-linkwith"></a>MT13xx: [LinkWith]

<!--
  MT13xx [LinkWith]
  -->

<a name="MT1301" />

### <a name="mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a>MT1301: La libreria `*` nativa\*() è stata ignorata perché non corrisponde alle architetture di compilazione correnti (\*)

<a name="MT1302" />

### <a name="mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a>MT1302: Non è stato possibile estrarre la libreria nativa ' *' da' +'. Verificare che la libreria nativa sia stata incorporata correttamente nell'assembly gestito. se l'assembly è stato compilato utilizzando un progetto di associazione, la libreria nativa deve essere inclusa nel progetto e la relativa azione di compilazione deve essere ' ObjcBindingNativeLibrary '.

<a name="MT1303" />

### <a name="mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a>MT1303: Non è stato possibile decomprimere\*il Framework nativo\*'' da' '. Per ulteriori informazioni sul comando "zip" nativo, vedere il log di compilazione.

Non è stato possibile decomprimere il Framework nativo specificato dalla libreria di associazione.

Per ulteriori informazioni su questo errore dal comando "zip" nativo, consultare il log bulid.

<a name="MT1304" />

### <a name="mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a>MT1304: Il Framework incorporato ' *' in * non è valido: non contiene un file INFO. plist.

Il Framework incorporato specificato non contiene un file INFO. plist e pertanto non è un Framework valido.

Verificare che il Framework sia valido.

<a name="MT1305" />

### <a name="mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a>MT1305: La libreria di binding\*'' contiene un Framework utente\*(), ma i Framework utente incorporati richiedono iOS 8,0 (la destinazione di distribuzione corrente è *). Impostare la destinazione di distribuzione nel file INFO. plist almeno 8,0.

La libreria di associazione specificata contiene un Framework incorporato, ma Novell. iOS supporta solo Framework incorporati in iOS 8,0 o versione successiva.

Impostare la destinazione di distribuzione nel file INFO. plist su almeno 8,0 per risolvere questo errore (oppure non usare Framework incorporati).

### <a name="mt14xx-crash-reports"></a>MT14xx: Segnalazioni di arresti anomali

<!--
  MT14xx    CrashReports.cs
  -->

<a name="MT1400" />

### <a name="mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a>MT1400: Impossibile aprire il servizio report di arresto anomalo: AFCConnectionOpen restituito *

Si è verificato un errore durante il tentativo di accedere alle segnalazioni di arresti anomali dal dispositivo.

Per risolvere il problema, eseguire le operazioni seguenti:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes. verranno rimossi tutti i report di arresto anomalo del dispositivo.

<a name="MT1401" />

### <a name="mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a>MT1401: Impossibile chiudere il servizio report di arresto anomalo del sistema: AFCConnectionClose restituito *

Si è verificato un errore durante il tentativo di accedere alle segnalazioni di arresti anomali dal dispositivo.

Per risolvere il problema, eseguire le operazioni seguenti:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes. verranno rimossi tutti i report di arresto anomalo del dispositivo.

<a name="MT1402" />

### <a name="mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a>MT1402: Impossibile leggere le informazioni sul file per *: AFCFileInfoOpen restituito *

Si è verificato un errore durante il tentativo di accedere alle segnalazioni di arresti anomali dal dispositivo.

Per risolvere il problema, eseguire le operazioni seguenti:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes. verranno rimossi tutti i report di arresto anomalo del dispositivo.

<a name="MT1403" />

### <a name="mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a>MT1403: Impossibile leggere la segnalazione degli arresti anomali: AFCDirectoryOpen (*) restituito: *

Si è verificato un errore durante il tentativo di accedere alle segnalazioni di arresti anomali dal dispositivo.

Per risolvere il problema, eseguire le operazioni seguenti:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes. verranno rimossi tutti i report di arresto anomalo del dispositivo.

<a name="MT1404" />

### <a name="mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a>MT1404: Impossibile leggere la segnalazione degli arresti anomali: AFCFileRefOpen (*) restituito: *

Si è verificato un errore durante il tentativo di accedere alle segnalazioni di arresti anomali dal dispositivo.

Per risolvere il problema, eseguire le operazioni seguenti:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes. verranno rimossi tutti i report di arresto anomalo del dispositivo.

<a name="MT1405" />

### <a name="mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a>MT1405: Impossibile leggere la segnalazione degli arresti anomali: AFCFileRefRead (*) restituito: *

Si è verificato un errore durante il tentativo di accedere alle segnalazioni di arresti anomali dal dispositivo.

Per risolvere il problema, eseguire le operazioni seguenti:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes. verranno rimossi tutti i report di arresto anomalo del dispositivo.

<a name="MT1406" />

### <a name="mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a>MT1406: Impossibile elencare le segnalazioni di arresti anomali: AFCDirectoryOpen (*) restituito: *

Si è verificato un errore durante il tentativo di accedere alle segnalazioni di arresti anomali dal dispositivo.

Per risolvere il problema, eseguire le operazioni seguenti:

* Eliminare l'applicazione dal dispositivo e riprovare.
* Disconnettere il dispositivo e riconnetterlo.
* Riavviare il dispositivo.
* Riavviare il computer Mac.
* Sincronizzare il dispositivo con iTunes. verranno rimossi tutti i report di arresto anomalo del dispositivo.

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx: MachO

<!--
  MT16xx    MachO.cs
  -->

<a name="MT1600" />

### <a name="mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1600: Non è una libreria dinamica di Mach-O (intestazione sconosciuta ' 0x *'): *.

Si è verificato un errore durante l'elaborazione della libreria dinamica in questione.

Verificare che la libreria dinamica sia una libreria dinamica Mach-O valida.

Il formato di una raccolta può essere verificato usando il `file` comando da un terminale:

```
file -arch all -l /path/to/library.dylib
```

<a name="MT1601" />

### <a name="mt1601-not-a-static-library-unknown-header--"></a>MT1601: Non è una libreria statica (intestazione sconosciuta ' *'): *.

Si è verificato un errore durante l'elaborazione della libreria statica in questione.

Verificare che la libreria statica sia una libreria statica Mach-O valida.

Il formato di una raccolta può essere verificato usando il `file` comando da un terminale:

```
file -arch all -l /path/to/library.a
```

<a name="MT1602" />

### <a name="mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1602: Non è una libreria dinamica di Mach-O (intestazione sconosciuta ' 0x *'): *.

Si è verificato un errore durante l'elaborazione della libreria dinamica in questione.

Verificare che la libreria dinamica sia una libreria dinamica Mach-O valida.

Il formato di una raccolta può essere verificato usando il `file` comando da un terminale:

```
file -arch all -l /path/to/library.dylib
```

<a name="MT1603" />

### <a name="mt1603-unknown-format-for-fat-entry-at-position--in-"></a>MT1603: Formato sconosciuto per la voce FAT alla posizione * in *.

Si è verificato un errore durante l'elaborazione dell'archivio FAT in questione.

Verificare che l'archivio Fat sia valido.

Il formato di un archivio FAT può essere verificato usando il `file` comando da un terminale:

```
file -arch all -l /path/to/file
```

<a name="MT1604" />

### <a name="mt1604-file-of-type--is-not-a-macho-file-"></a>MT1604: Il file di tipo * non è un file Macho (*).

Si è verificato un errore durante l'elaborazione del file macho in questione.

Verificare che il file sia una libreria dinamica Mach-O valida.

Il formato di un file può essere verificato usando il `file` comando da un terminale:

```
file -arch all -l /path/to/file
```

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx: Messaggi di errore del linker

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

<a name="MT2001" />

### <a name="mt2001-could-not-link-assemblies"></a>MT2001: Non è stato possibile collegare gli assembly

Questo errore indica che il linker gestito ha rilevato un errore imprevisto, ad esempio un'eccezione, e non è stato possibile completare o salvare l'assembly in fase di elaborazione. Altre informazioni sull'errore esatto faranno parte del log di compilazione, ad esempio

```
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

È importante archiviare un report sui bug per tali problemi. Nella maggior parte dei casi è possibile fornire una soluzione alternativa fino a quando non viene pubblicata una correzione corretta. Le informazioni sopra riportate sono critiche (insieme a una test case e/o all'assembly binairy) per risolvere il problema.

<a name="MT2002" />

### <a name="mt2002-can-not-resolve-reference-"></a>MT2002: Non è possibile risolvere il riferimento: *

<a name="MT2003" />

### <a name="mt2003-option--will-be-ignored-since-linking-is-disabled"></a>MT2003: L'opzione ' *' verrà ignorata perché il collegamento è disabilitato

<a name="MT2004" />

### <a name="mt2004-extra-linker-definitions-file--could-not-be-located"></a>MT2004: Impossibile trovare il file di definizioni del linker aggiuntivo ' *'.

<a name="MT2005" />

### <a name="mt2005-definitions-from--could-not-be-parsed"></a>MT2005: Non è stato possibile analizzare le definizioni di ' *'.

<a name="MT2006" />

### <a name="mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a>MT2006: Non è possibile caricare mscorlib. dll da: *. Reinstallare Novell. iOS.

Questo indica in genere un problema con l'installazione di Novell. iOS. Provare a reinstallare Novell. iOS.

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

<a name="MT2010" />

### <a name="mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MT2010: HttpMessageHandler `*`sconosciuto. I valori validi sono HttpClientHandler (impostazione predefinita), CFNetworkHandler o NSUrlSessionHandler

<a name="MT2011" />

### <a name="mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a>MT2011: TlsProvider `*`sconosciuto.  I valori validi sono default o appletls.

Il valore specificato per `tls-provider=` non è un provider TLS (Transport Layer Security) valido.

`default` E`appletls` sono gli unici valori validi ed entrambi rappresentano la stessa opzione, ovvero per fornire il supporto SSL/TLS tramite l'API TLS nativa di Apple.

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

<a name="MT2015" />

### <a name="mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a>MT2015: HttpMessageHandler `*` non valido per watchos. L'unico valore valido è NSUrlSessionHandler.

Si tratta di un avviso che si verifica perché il file di progetto specifica un HttpMessageHandler non valido.

Le versioni precedenti degli strumenti di anteprima generate per impostazione predefinita un valore non valido nel file di progetto.

Per correggere il problema, aprire il file di progetto in un editor di testo e rimuovere tutti i nodi HttpMessageHandler dal codice XML.

<a name="MT2016" />

### <a name="mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a>MT2016: Opzione TlsProvider `legacy` non valida. Verrà utilizzato l'unico `appletls` valore valido.

Il `legacy` provider, che era un provider solo SSLv3/TLSv1 completamente gestito, non viene più fornito con Novell. iOS. I progetti che usavano questo provider obsoleti e ora compilati `appletls` con quello più recente.

Per correggere il problema, aprire il file di progetto in un editor di testo e rimuovere tutti i nodi "MtouchTlsProvider" dal codice XML.

<a name="MT2017" />

### <a name="mt2017-could-not-process-xml-description"></a>MT2017: Impossibile elaborare la descrizione XML.

Ciò significa che si è verificato un errore nel [file di configurazione del linker XML personalizzato](~/cross-platform/deploy-test/linker.md) fornito. controllare il file.

<a name="MT2018" />

### <a name="mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a>MT2018: Si fa riferimento\*all'assembly '' da due posizioni diverse:'\*' è *'.

L'assembly indicato nel messaggio di errore viene caricato da più posizioni. Assicurarsi di usare sempre la stessa versione di un assembly.

<a name="MT2019" />

### <a name="mt2019-can-not-load-the-root-assembly-"></a>MT2019: Non è possibile caricare l'assembly radice "*"

Non è stato possibile caricare l'assembly radice. Verificare che il percorso nel messaggio di errore faccia riferimento a un file esistente e che sia un assembly .NET valido.

<a name="MT202x" />

### <a name="mt202x-binding-optimizer-failed-processing-"></a>MT202x: L'elaborazione `...`dell'utilità di ottimizzazione del binding non è riuscita.

Si è verificato un errore imprevisto durante il tentativo di ottimizzare il codice di associazione generato. Il nome dell'elemento che causa il problema è indicato nel messaggio di errore. Per risolvere questo problema, è necessario che l'assembly denominato (o che contiene il tipo o il metodo denominato) venga fornito in un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) insieme a un log di compilazione completo con il livello `-v -v -v -v` di dettaglio abilitato (ad esempio negli **argomenti mTouch aggiuntivi**).

L'ultima cifra `x` sarà:
* `0`per un nome di assembly;
* `1`per un nome di tipo;
* `3`per il nome di un metodo;

<a name="MT2030" />

### <a name="mt2030-remove-user-resources-failed-processing-"></a>MT2030: L'elaborazione `...`delle risorse utente non è riuscita.

Si è verificato un errore imprevisto durante il tentativo di rimuovere le risorse utente. Il nome dell'assembly che causa il problema è indicato nel messaggio di errore. Per risolvere questo problema, è necessario fornire l'assembly in un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) insieme a un log di compilazione completo con il livello `-v -v -v -v` di dettaglio abilitato, ad esempio negli **argomenti mTouch aggiuntivi**.

Le risorse utente sono file inclusi all'interno di assembly (come risorse) che devono essere estratti, in fase di compilazione, per creare il bundle dell'applicazione. vale a dire:

* `__monotouch_content_*`e `__monotouch_pages_*` risorse e
* Librerie native incorporate all'interno di un assembly di associazione.

<a name="MT2040" />

### <a name="mt2040-default-httpmessagehandler-setter-failed-processing-"></a>MT2040: L'elaborazione `...`del setter HttpMessageHandler predefinito non è riuscita.

Si è verificato un errore imprevisto durante il `HttpMessageHandler` tentativo di impostare l'impostazione predefinita per l'applicazione. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) insieme a un log di compilazione completo con il livello di dettaglio abilitato, `-v -v -v -v` ad esempio negli **argomenti mTouch aggiuntivi**.

<a name="MT2050" />

### <a name="mt2050-code-remover-failed-processing-"></a>MT2050: Impossibile elaborare `...`il codice di rimozione.

Si è verificato un errore imprevisto durante il tentativo di rimuovere il codice dalla libreria BCL con l'applicazione. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) insieme a un log di compilazione completo con il livello di dettaglio abilitato, `-v -v -v -v` ad esempio negli **argomenti mTouch aggiuntivi**.

<a name="MT2060" />

### <a name="mt2060-sealer-failed-processing-"></a>MT2060: L'elaborazione `...`del Sealer non è riuscita.

Si è verificato un errore imprevisto durante il tentativo di sealing di tipi o metodi (finali) o durante la devirtualizzazione di alcuni metodi. Il nome dell'assembly che causa il problema è indicato nel messaggio di errore. Per risolvere questo problema, è necessario fornire l'assembly in un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) insieme a un log di compilazione completo con il livello `-v -v -v -v` di dettaglio abilitato, ad esempio negli **argomenti mTouch aggiuntivi**.

<a name="MT2070" />

### <a name="mt2070-metadata-reducer-failed-processing-"></a>MT2070: Impossibile elaborare `...`il riduttore dei metadati.

Si è verificato un errore imprevisto durante il tentativo di ridurre i metadati dall'applicazione. Il nome dell'assembly che causa il problema è indicato nel messaggio di errore. Per risolvere questo problema, è necessario fornire l'assembly in un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) insieme a un log di compilazione completo con il livello `-v -v -v -v` di dettaglio abilitato, ad esempio negli **argomenti mTouch aggiuntivi**.

<a name="MT2080" />

### <a name="mt2080-marknsobjects-failed-processing-"></a>MT2080: Elaborazione `...`di MarkNSObjects non riuscita.

Si è verificato un errore imprevisto `NSObject` durante il tentativo di contrassegnare le sottoclassi dall'applicazione. Il nome dell'assembly che causa il problema è indicato nel messaggio di errore. Per risolvere questo problema, è necessario fornire l'assembly in un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) insieme a un log di compilazione completo con il livello `-v -v -v -v` di dettaglio abilitato, ad esempio negli **argomenti mTouch aggiuntivi**.

<a name="MT2090" />

### <a name="mt2090-inliner-failed-processing-"></a>MT2090: Elaborazione `...`inline non riuscita.

Si è verificato un errore imprevisto durante il tentativo di incorporare il codice dall'applicazione. Il nome dell'assembly che causa il problema è indicato nel messaggio di errore. Per risolvere questo problema, è necessario fornire l'assembly in un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) insieme a un log di compilazione completo con il livello di dettaglio abilitato, ad esempio `-v -v -v -v` negli **argomenti mTouch aggiuntivi**.

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

<a name="MT2100" />

### <a name="mt2100-smart-enum-conversion-preserver-failed-processing-"></a>MT2100: L'elaborazione `...`del preserver di conversione Smart enum non è riuscita.

Si è verificato un errore imprevisto durante il tentativo di contrassegnare i metodi di conversione per le enumerazioni intelligenti dall'applicazione. Il nome dell'assembly che causa il problema è indicato nel messaggio di errore. Per risolvere questo problema, è necessario fornire l'assembly in un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) insieme a un log di compilazione completo con il livello di dettaglio abilitato, ad esempio `-v -v -v -v` negli **argomenti mTouch aggiuntivi**.

<a name="MT2101" />

### <a name="mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a>MT2101: Non è possibile risolvere il\*riferimento '', a cui fa riferimento\*il metodo '' in ' *'.

Si è verificato un riferimento a un assembly non valido durante l'elaborazione del metodo indicato nel messaggio di errore.

Il nome dell'assembly che causa il problema è indicato nel messaggio di errore. Per risolvere questo problema, è necessario fornire l'assembly in un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) insieme a un log di compilazione completo con il livello `-v -v -v -v` di dettaglio abilitato, ad esempio negli **argomenti mTouch aggiuntivi**.

<a name="MT2102" />

### <a name="mt2102-error-processing-the-method--in-the-assembly--"></a>MT2102: Errore durante l'elaborazione del\*metodo '' nell'assembly\*'': *

Si è verificato un evento imprevisto durante il tentativo di contrassegnare il metodo indicato nel messaggio di errore.

Il nome dell'assembly che causa il problema è indicato nel messaggio di errore. Per risolvere questo problema, è necessario fornire l'assembly in un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) insieme a un log di compilazione completo con il livello `-v -v -v -v` di dettaglio abilitato, ad esempio negli **argomenti mTouch aggiuntivi**.

<a name="MT2103" />

### <a name="mt2103-error-processing-assembly--"></a>MT2103: Errore durante l'elaborazione\*dell'assembly '': *

Si è verificato un errore imprevisto durante l'elaborazione di un assembly.

Il nome dell'assembly che causa il problema è indicato nel messaggio di errore. Per risolvere questo problema, è necessario fornire l'assembly in un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) insieme a un log di compilazione completo con il livello `-v -v -v -v` di dettaglio abilitato, ad esempio negli **argomenti mTouch aggiuntivi**.

<a name="MT2104" />

### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104: Non è possibile collegare l'{0}assembly '' perché è in modalità mista.

Gli assembly in modalità mista non possono essere elaborati dal linker.

Per https://docs.microsoft.com/cpp/dotnet/mixed-native-and-managed-assemblies ulteriori informazioni sugli assembly in modalità mista, vedere.

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx: Messaggi di errore AOT

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

<a name="MT3001" />

### <a name="mt3001-could-not-aot-the-assembly-"></a>MT3001: Non è stato possibile AOT l'assembly ' *'

Questo indica in genere un bug nel compilatore AOT. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un progetto che può essere usato per riprodurre l'errore.

A volte è possibile aggirare questo problema disabilitando le compilazioni incrementali nell'opzione di compilazione iOS del progetto (ma si tratta comunque di un bug, quindi è necessario segnalarlo comunque).

<a name="MT3002" />

### <a name="mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-developerxamarincomguidesiosadvanced_topicslimitationsreverse_callbacksiosinternalslimitationsmdreverse-callbacks"></a>MT3002: Restrizione AOT: Il metodo ' *' deve essere statico perché è decorato con [MonoPInvokeCallback]. Vedere [developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](~/ios/internals/limitations.md#reverse-callbacks)

Questo messaggio di errore deriva dal compilatore AOT.

<a name="MT3003" />

### <a name="mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a>MT3003: Conflitto tra le opzioni--debug e--LLVM. Il debug Soft è disabilitato.

Il debug non è supportato quando LLVM è abilitato. Se è necessario eseguire il debug dell'app, disabilitare prima LLVM.

<a name="MT3004" />

### <a name="mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a>MT3004: Non è stato possibile AOT l'assembly ' *' perché non esiste.

<a name="MT3005" />

### <a name="mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a>MT3005: Impossibile trovare la\*dipendenza '' dell'assembly\*''. Verificare i riferimenti del progetto.

Questo problema si verifica in genere quando un assembly fa riferimento a un'altra versione di un assembly della piattaforma, in genere la versione .NET 4 di mscorlib. dll.

Questa operazione non è supportata e non può essere compilata o eseguita correttamente (l'assembly può usare l'API della versione .NET 4 di mscorlib. dll che la versione di Novell. iOS non ha).

<a name="MT3006" />

### <a name="mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a>MT3006: Non è stato possibile calcolare una mappa delle dipendenze completa per il progetto. Questo comporterà tempi di compilazione più lenti perché Novell. iOS non è in grado di rilevare correttamente gli elementi che devono essere ricompilati e non è necessario ricompilarli. Per altri dettagli, vedere gli avvisi precedenti.

 compilazione o esecuzione corretta (l'assembly può usare l'API della versione .NET 4 di mscorlib. dll che la versione di Novell. iOS non è presente).

<a name="MT3007" />

### <a name="mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a>MT3007: I file di informazioni di debug (*. mdb) non verranno caricati quando LLVM è abilitato.

<a name="MT3008" />

### <a name="mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a>MT3008: Il supporto di bitcode richiede l'uso del back-end AOT LLVM (--LLVM)

Il supporto di bitcode richiede l'uso del back-end AOT LLVM (--LLVM).

Disabilitare il supporto di bitcode o abilitare LLVM.

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx: Messaggi di errore di generazione del codice

### <a name="mt40xx-main"></a>MT40xx: Main

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

<a name="MT4001" />

### <a name="mt4001-the-main-template-could-not-be-expanded-to-"></a>MT4001: Non è stato possibile espandere il modello principale `*`in.

Si è verificato un errore `main.m`durante la generazione di. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4002" />

### <a name="mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4002: Impossibile compilare il codice generato per i metodi P/Invoke. Inviare un report sui bug in http://bugzilla.xamarin.com

Impossibile compilare il codice generato per i metodi P/Invoke. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

### <a name="mt41xx-registrar"></a>MT41xx: Registrar

<!--
  MT41xx registrar.m
  -->

<a name="MT4101" />

### <a name="mt4101-the-registrar-cannot-build-a-signature-for-type-"></a>MT4101: Il registrar non può compilare una firma `*`per il tipo.

È stato trovato un tipo nell'API esportata che il runtime non è in grado di eseguire il marshalling da e verso Objective-C.

Se si ritiene che Novell. iOS supporti il tipo in questione, inviare una richiesta di miglioramento su [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4102" />

### <a name="mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a>MT4102: Il Registrar ha rilevato un `*` tipo non valido nella `*`firma per il metodo. In alternativa, utilizzare `*`.

Attualmente si verifica solo un tipo: System.DateTime. Usare invece l'equivalente Objective-C (NSDate).

<a name="MT4103" />

### <a name="mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a>MT4103: Il Registrar ha rilevato un `*` tipo non valido nella `*`firma per il metodo: Il tipo implementa INativeObject, ma non dispone di un costruttore che accetta due argomenti (IntPtr, bool)

Questo errore si verifica quando il registrar incontra un tipo in una firma con le caratteristiche indicate. Il registrar potrebbe dover creare nuove istanze del tipo e, in questo caso, richiede un costruttore con la firma (IntPtr, bool), il primo argomento (IntPtr) specifica l'handle gestito, mentre il secondo se il chiamante passa la proprietà del nativo handle (se questo valore è false ' conserva ' verrà chiamato sull'oggetto).

<a name="MT4104" />

### <a name="mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a>MT4104: Il registrar non può effettuare il marshalling `*` del valore restituito per `*`il tipo nella firma per il metodo.

È stato trovato un tipo nell'API esportata che il runtime non è in grado di eseguire il marshalling da e verso Objective-C.

Se si ritiene che Novell. iOS supporti il tipo in questione, inviare una richiesta di miglioramento su [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4105" />

### <a name="mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4105: Il registrar non può effettuare il marshalling del parametro di `*`tipo `*` nella firma per il metodo.

Se si ritiene che Novell. iOS supporti il tipo in questione, inviare una richiesta di miglioramento su [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4106" />

### <a name="mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a>MT4106: Il registrar non può effettuare il marshalling `*` del valore restituito per `*`la struttura nella firma per il metodo.

È stato trovato un tipo nell'API esportata che il runtime non è in grado di eseguire il marshalling da e verso Objective-C.

Se si ritiene che Novell. iOS supporti il tipo in questione, inviare una richiesta di miglioramento su [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4107" />

### <a name="mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4107: Il registrar non può effettuare il marshalling del parametro di `+`tipo `*` nella firma per il metodo.

È stato trovato un tipo nell'API esportata che il runtime non è in grado di eseguire il marshalling da e verso Objective-C.

Se si ritiene che Novell. iOS supporti il tipo in questione, inviare una richiesta di miglioramento su [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4108" />

### <a name="mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a>MT4108: Il registrar non è in grado di ottenere il `*`tipo ObjectiveC per il tipo gestito.

È stato trovato un tipo nell'API esportata che il runtime non è in grado di eseguire il marshalling da e verso Objective-C.

Se si ritiene che Novell. iOS supporti il tipo in questione, inviare una richiesta di miglioramento su [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4109" />

### <a name="mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4109: Impossibile compilare il codice del registrar generato. Inviare un report sui bug in http://bugzilla.xamarin.com

Impossibile compilare il codice generato per il registrar. Il log di compilazione conterrà l'output del compilatore nativo, spiegando il motivo per cui il codice non viene compilato.

Si tratta sempre di un bug in Novell. iOS; inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con il progetto o un test case.

<a name="MT4110" />

### <a name="mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a>MT4110: Il registrar non è in grado di effettuare `*` il marshalling del `*`parametro out di tipo in Signature per Method.

<a name="MT4111" />

### <a name="mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a>MT4111: Il registrar non può compilare una firma `*` per il `*`tipo nel metodo.

<a name="MT4112" />

### <a name="mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvanced_topicsregistrariosinternalsregistrarmd-for-more-information"></a>MT4112: Il Registrar ha rilevato un `*`tipo non valido. La registrazione di tipi generici con Objective-C non è supportata e può causare un comportamento casuale e/o arresti anomali (per compatibilità con le versioni precedenti di Novell. iOS è possibile ignorare questo errore passando `--unsupported--enable-generics-in-registrar` come mTouch aggiuntivi argomento nella pagina delle opzioni di compilazione iOS del progetto. Per ulteriori informazioni, vedere [Developer.Xamarin.com/guides/iOS/advanced_topics/registrar](~/ios/internals/registrar.md) .

<a name="MT4113" />

### <a name="mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a>MT4113: Il Registrar ha trovato un metodo generico\*:\*' .'. L'esportazione di metodi generici non è supportata e comporta un comportamento casuale e/o arresti anomali.

<a name="MT4114" />

### <a name="mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4114: Si è verificato un errore imprevisto nel registrar\*per il metodo '\*.'. inviare un report sui bug in http://bugzilla.xamarin.com

<a name="MT4116" />

### <a name="mt4116-could-not-register-the-assembly--"></a>MT4116: Non è stato possibile registrare l'assembly ' *': *

<a name="MT4117" />

### <a name="mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4117: Il Registrar ha rilevato una mancata corrispondenza di firma nel metodo ' *.'.* il selettore indica che il metodo accetta * parametri, mentre il metodo gestito presenta * parametri.

<a name="MT4118" />

### <a name="mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a>MT4118: Non è possibile registrare due tipi gestiti\*(''\*è ') con lo stesso nome nativo (' *').

<a name="MT4119" />

### <a name="mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a>MT4119: Non è stato possibile registrare il\*selettore ''\*del membro '. *' perché il selettore è già registrato in un membro diverso.

<a name="MT4120" />

### <a name="mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4120: Il Registrar ha trovato un tipo di\*campo sconosciuto ''\*nel campo '. *'. Inviare un report sui bug in http://bugzilla.xamarin.com

Questo errore indica un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4121" />

### <a name="mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a>MT4121: Non è possibile usare GCC/G + + per compilare il codice generato dal registrar statico quando si usa il Framework degli account (i file di intestazione forniti da Apple usati durante la compilazione richiedono Clang). Usare Clang (--Compiler: Clang) o il registrar dinamico (--Registrar: Dynamic).

<a name="MT4122" />

### <a name="mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a>MT4122: Non è possibile usare il compilatore Clang fornito in *.* SDK per compilare il codice generato dal registrar statico quando nell'applicazione sono presenti nomi di tipo non ASCII (' *'). Usare GCC/G + + (--Compiler: GCC | G + +), il registrar dinamico (--Registrar: Dynamic) o un SDK più recente.

<a name="MT4123" />

### <a name="mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a>MT4123: Il tipo del parametro Variadic nella funzione Variadic ' *' deve essere System. IntPtr.

<a name="MT4124" />

### <a name="mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4124: * Trovato non valido in ' *'. Inviare un report sui bug in http://bugzilla.xamarin.com

Questo errore indica un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4125" />

### <a name="mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a>MT4125: Il Registrar ha trovato un tipo\*non valido '' nella firma\*per il metodo '': L'interfaccia deve avere un attributo del protocollo che specifica il tipo di wrapper.

<a name="MT4126" />

### <a name="mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a>MT4126: Non è possibile registrare due protocolli gestiti\*(''\*è ') con lo stesso nome nativo (' *').

<a name="MT4127" />

### <a name="mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a>MT4127: Non è possibile registrare più di un metodo di interfaccia per\*il metodo '' (che\*sta implementando '').

<a name="MT4128" />

### <a name="mt4128-the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a>MT4128: Il Registrar ha trovato un tipo di parametro\*generico '' non valido\*nel metodo ''. Il parametro generico deve avere un vincolo ' NSObject '.

<a name="MT4129" />

### <a name="mt4129-the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a>MT4129: Il Registrar ha trovato un tipo restituito generico\*non valido '' nel\*metodo ''. Il tipo restituito generico deve avere un vincolo ' NSObject '.

<a name="MT4130" />

### <a name="mt4130-the-registrar-cannot-export-static-methods-in-generic-classes-"></a>MT4130: Il registrar non è in grado di esportare metodi statici in classi generiche (' *').

<a name="MT4131" />

### <a name="mt4131-the-registrar-cannot-export-static-properties-in-generic-classes-"></a>MT4131: Il registrar non è in grado di esportare proprietà statiche\*in\*classi generiche (' .').

<a name="MT4132" />

### <a name="mt4132-the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a>MT4132: Il Registrar ha trovato un tipo restituito generico\*non valido '' nella\*proprietà''. Il tipo restituito deve avere un vincolo ' NSObject '.

<a name="MT4133" />

### <a name="mt4133-cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a>MT4133: Non è possibile costruire un'istanza del tipo ' *' da Objective-C perché il tipo è generico. [Eccezione runtime]

<a name="MT4134" />

### <a name="mt4134-your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a>MT4134: L'applicazione usa il Framework "*", che non è incluso nell'SDK di iOS usato per compilare l'app (questo Framework è stato introdotto in iOS *, mentre si sta creando con iOS * SDK). Selezionare un SDK più recente nelle opzioni di compilazione iOS dell'app.

<a name="MT4135" />

### <a name="mt4135-the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a>MT4135: Il membro '\*.\*' ha un attributo Export che non specifica un selettore. Un selettore è obbligatorio.

<a name="MT4136" />

### <a name="mt4136-the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a>MT4136: Il registrar non è in grado di\*effettuare il marshalling del\*tipo di parametro ''\*del parametro '' nel metodo '. *'

<!-- MT4137 is unused -->

<a name="MT4138" />

### <a name="mt4138-the-registrar-cannot-marshal-the-property-type--of-the-property-"></a>MT4138: Il registrar non è in grado di\*effettuare il marshalling del\*tipo di proprietà'' della proprietà'. *'.

<a name="MT4139" />

### <a name="mt4139-the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a>MT4139: Il registrar non è in grado di\*effettuare il marshalling del\*tipo di proprietà'' della proprietà'. *'. Le proprietà con l'attributo [Connect] devono avere un tipo di proprietà NSObject (o una sottoclasse di NSObject).

<a name="MT4140" />

### <a name="mt4140-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4140: Il Registrar ha rilevato una mancata corrispondenza di firma nel metodo ' *.'.* il selettore indica che il metodo Variadic accetta * parametri, mentre il metodo gestito presenta * parametri.

<a name="MT4141" />

### <a name="mt4141-cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a>MT4141: Non è possibile registrare il\*selettore ''\*sul membro '' perché Novell. iOS registra in modo implicito questo selettore.

Questo errore si verifica quando si crea una sottoclasse di un tipo di Framework e si tenta di implementare un metodo ' conserva ',' release ' o ' Dealloc ':

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

È tuttavia possibile eseguire l'override di questi metodi se la classe non è la prima sottoclasse del tipo di Framework:

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

In questo caso Novell. iOS eseguirà `retain`l' `release` override `dealloc` di e `MyNSObject` sulla classe e non si verifica alcun conflitto.

<a name="MT4142" />

### <a name="mt4142-failed-to-register-the-type-"></a>MT4142: Non è stato possibile registrare il tipo ' *'.

<a name="MT4143" />

### <a name="mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a>MT4143: Non è stato possibile registrare la classe ObjectiveC "*" perché non sembra derivare da alcuna classe ObjectiveC nota (incluso NSObject).

<a name="MT4144" />

### <a name="mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4144: Non è possibile registrare il metodo ' *' perché non dispone di un trampolino associato. Inviare un report sui bug all' http://bugzilla.xamarin.com indirizzo.

Ciò indica un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4145" />

### <a name="mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a>MT4145: Enum ' *' non valido: le enumerazioni con l'attributo [Native] devono avere un tipo enum sottostante ' Long ' o ' ulong '.

<a name="MT4146" />

### <a name="mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a>MT4146: Il parametro Name dell'attributo\*registrar nella classe '' ('\*') contiene un carattere non valido:'\*' (\*).

Il nome di una classe objectice-C non può contenere spazi vuoti, il che `Register` significa che l'attributo nella classe gestita corrispondente non `Name` può contenere uno spazio vuoto.

Verificare che l' `Register` attributo nella classe gestita indicata nel messaggio di errore non contenga spazi vuoti.

<a name="MT4147" />

### <a name="mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a>MT4147: Rilevato un protocollo che eredita dal protocollo JSExport durante l'utilizzo del registrar dinamico. Non è possibile esportare i protocolli in JavaScriptCore dinamicamente; il registrar statico deve essere usato (aggiungere '--Registrar: static agli argomenti aggiuntivi di mTouch nelle opzioni di compilazione iOS del progetto per selezionare il registrar statico).

<a name="MT4148" />

### <a name="mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a>MT4148: Il Registrar ha trovato un protocollo generico:' *'. L'esportazione di protocolli generici non è supportata.

<a name="MT4149" />

### <a name="mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a>MT4149: Non è possibile registrare il\*metodo\*' .' perché il tipo del primo parametro (\*'') non corrisponde al tipo di categoria (\*'').

<a name="MT4150" />

### <a name="mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a>MT4150: Non è possibile registrare il\*tipo '' perché la proprietà del\*tipo ('') nell'attributo Category non eredita da NSObject.

<a name="MT4151" />

### <a name="mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a>MT4151: Non è possibile registrare il tipo ' *' perché la proprietà Type nel relativo attributo Category non è impostata.

<a name="MT4152" />

### <a name="mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a>MT4152: Non è possibile registrare il tipo ' *' come categoria perché implementa INativeObject o sottoclassi NSObject.

<a name="MT4153" />

### <a name="mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a>MT4153: Non è possibile registrare il\*tipo '' come categoria perché è generico.

<a name="MT4154" />

### <a name="mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a>MT4154: Non è possibile registrare il\*metodo '' come metodo di categoria perché è generico.

<a name="MT4155" />

### <a name="mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a>MT4155: Non è possibile registrare il\*metodo '' con il\*selettore '' come metodo di categoria su' *' perché in Objective-C è già presente un'implementazione per questo selettore.

<a name="MT4156" />

### <a name="mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a>MT4156: Non è possibile registrare due categorie\*(''\*è ') con lo stesso nome nativo (' *').

<a name="MT4157" />

### <a name="mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a>MT4157: Non è possibile registrare il metodo\*di categoria '' perché è necessario almeno un parametro (e il tipo deve corrispondere al tipo\*di categoria '')

<a name="MT4158" />

### <a name="mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a>MT4158: Non è possibile registrare il costruttore * nella categoria * perché i costruttori nelle categorie non sono supportati.

<a name="MT4159" />

### <a name="mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a>MT4159: Non è possibile registrare il metodo ' *' come metodo Category perché i metodi Category devono essere statici.

<a name="MT4160" />

### <a name="mt4160-invalid-character---found-in-selector--for-"></a>MT4160: Il carattere '\*' (\*) trovato nel selettore '\*'\*per '' non è valido.

<a name="MT4161" />

### <a name="mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a>MT4161: Il Registrar ha rilevato una struttura non supportata\*'': Anche tutti i campi in una struttura devono essere strutture (il\*campo '' con{2}tipo '' non è una struttura).

Il Registrar ha trovato una struttura con campi non supportati.

Tutti i campi in una struttura esposta a Objective-C devono essere anche strutture (non classi).

<a name="MT4162" />

### <a name="mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a>MT4162: Il tipo '\*' (usato come * {2}) non è disponibile in * * (è stato introdotto in * *)\* compilare con un SDK * più recente (in genere è possibile usare la versione più recente di Xcode.

Il Registrar ha rilevato un tipo non incluso nell'SDK corrente.

Aggiornare Xcode.

<a name="MT4163" />

### <a name="mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4163: Errore interno del registrar (*). Inviare un report sui bug in http://bugzilla.xamarin.com

Questo errore indica un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4164" />

### <a name="mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a>MT4164: Impossibile esportare la proprietà'\*' perché il selettore '\*' è una parola chiave Objective-C. Usare un nome diverso.

Il selettore per la proprietà in questione non è un identificatore Objective-C valido.

Usare un identificatore Objective-C valido come selettori.

<a name="MT4165" />

### <a name="mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a>MT4165: Il registrar non è riuscito a trovare il tipo ' System. void ' in nessuno degli assembly a cui si fa riferimento.

Questo errore indica con maggiore probabilità un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4166" />

### <a name="mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a>MT4166: Non è possibile registrare il\*metodo '' perché la firma contiene un\*tipo () che non è un tipo di riferimento.

Questo indica in genere un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4167" />

### <a name="mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a>MT4167: Non è possibile registrare il\*metodo '' perché la firma contiene un tipo\*generico () con un tipo di argomento generico che non è una sottoclasse NSObject (*).

Questo indica in genere un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4168" />

### <a name="mt4168-cannot-register-the-type-managed_name-because-its-objective-c-name-exported_name-is-an-objective-c-keyword-please-use-a-different-name"></a>MT4168: Non è possibile registrare il tipo '\_{Managed Name}' perché il nome Objective-c\_' {exported Name}' è una parola chiave Objective-c. Usare un nome diverso.

Il nome Objective-C per il tipo in questione non è un identificatore Objective-C valido.

Usare un identificatore Objective-C valido.

<a name="MT4169" />

### <a name="mt4169-failed-to-generate-a-pinvoke-wrapper-for-method-message"></a>MT4169: Non è stato possibile generare un wrapper P/Invoke per {Method}: {Message}

Novell. iOS non è riuscito a generare una funzione wrapper P/Invoke per l'oggetto indicato.
Verificare il messaggio di errore segnalato per la cause sottostante.

<a name="MT4170" />

### <a name="mt4170-the-registrar-cant-convert-from-managed-type-to-native-type-for-the-return-value-in-the-method-method"></a>MT4170: Il registrar non può convertire da' {Managed Type}' a' {Native Type}' per il valore restituito nel metodo {method}.

Vedere la descrizione dell'errore <a href="#MT4172">MT4172</a>.

<a name="MT4171" />

### <a name="mt4171-the-bindas-attribute-on-the-member-member-is-invalid-the-bindas-type-type-is-different-from-the-property-type-type"></a>MT4171: L'attributo binds sul membro {member} non è valido: il tipo di binding {type} è diverso dal tipo di proprietà {Type}.

Verificare che il tipo nell'attributo binds corrisponda al tipo del membro a cui è collegato.

<a name="MT4172" />

### <a name="mt4172-the-registrar-cant-convert-from-native-type-to-managed-type-for-the-parameter-parameter-name-in-the-method-method"></a>MT4172: Il registrar non può convertire ' {Native Type}' in ' {Managed Type}' per il parametro ' {parameter name}' nel metodo {method}.

Il registrar non supporta la conversione tra i tipi indicati.

Si tratta di un bug in Novell. iOS se l'API in questione viene fornita da Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Se si esegue questa operazione durante lo sviluppo di un progetto di binding per una libreria nativa, è possibile aggiungere il supporto per nuove combinazioni di tipi. In tal caso, inviare una richiesta di miglioramento su [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con una test case e valutarla.

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx: Messaggi di errore GCC e.

### <a name="mt51xx-compilation"></a>MT51xx: Compilazione

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

<a name="MT5101" />

### <a name="mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a>MT5101: Manca il compilatore ' *'. Installare il componente "strumenti da riga di comando" di Xcode

<a name="MT5102" />

### <a name="mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5102: Impossibile assemblare il file ' *'. Inviare un report sui bug in http://bugzilla.xamarin.com

<a name="MT5103" />

### <a name="mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5103: Impossibile compilare il file ' *'. Inviare un report sui bug in http://bugzilla.xamarin.com

<a name="MT5104" />

### <a name="mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a>MT5104: Impossibile trovare il compilatore '\*' o\*''. Installare il componente "strumenti da riga di comando" di Xcode

<!-- 5105 is used by mmp -->

<a name="MT5106" />

### <a name="mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5106: Non è stato possibile compilare i file ' *'. Inviare un report sui bug in http://bugzilla.xamarin.com

Questo indica in genere un bug in Novell. iOS; inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

### <a name="mt52xx-linking"></a>MT52xx: Collegamento

<!--
  MT52xx linking
  -->

<a name="MT5201" />

### <a name="mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a>MT5201: Il collegamento nativo non è riuscito. Verificare il log di compilazione e i flag utente forniti a GCC: *

<a name="MT5202" />

### <a name="mt5202-native-linking-failed-please-review-the-build-log"></a>MT5202: Il collegamento nativo non è riuscito. Verificare il log di compilazione.

<a name="MT5203" />

### <a name="mt5203-native-linking-warning-"></a>MT5203: Avviso di collegamento nativo: *

<!--- 5204-5208 are not used -->

<a name="MT5209" />

### <a name="mt5209-native-linking-error-"></a>MT5209: Errore di collegamento nativo: *

<a name="MT5210" />

### <a name="mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a>MT5210: Il collegamento nativo non è riuscito. simbolo non definito: *. Verificare che sia stato fatto riferimento a tutti i Framework necessari e che le librerie native siano collegate correttamente.

Ciò si verifica quando il linker nativo non riesce a trovare un simbolo a cui si fa riferimento in un punto qualsiasi. Questo problema può verificarsi per diversi motivi:

* Un'associazione di terze parti richiede un Framework, ma il binding non lo specifica nell' `[LinkWith]` attributo. Soluzioni
  - Se si è l'autore dell'associazione di terze parti o si ha accesso all'origine, modificare l' `[LinkWith]` attributo dell'associazione in modo da includere il Framework necessario:

    ```csharp
    [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]
    ```

  - Se non è possibile modificare l'associazione di terze parti, è possibile collegarsi manualmente con il Framework `-gcc_flags '-framework SystemFramework'` richiesto `mtouch` passando a. questa operazione viene eseguita modificando gli argomenti aggiuntivi di mTouch nella pagina delle opzioni di compilazione iOS del progetto. Tenere presente che questa operazione deve essere eseguita per ogni configurazione di progetto.
* In alcuni casi, un'associazione gestita è costituita da più librerie native e deve essere inclusa nelle associazioni. In ogni progetto di associazione è possibile avere più di una libreria nativa, quindi la soluzione consiste nell'aggiungere tutte le librerie native necessarie al progetto di binding.</li>
* Un'associazione gestita fa riferimento a simboli nativi che non esistono nella libreria nativa.
    Questo problema si verifica in genere quando esiste un'associazione per un certo periodo di tempo e il codice nativo è stato modificato durante tale periodo di tempo, in modo che una determinata classe nativa sia stata rimossa o rinominata, mentre l'associazione non è stata aggiornata.
* Un P/Invoke fa riferimento a un simbolo nativo che non esiste. A partire da Novell. iOS 7,4 verrà segnalato un errore <a href="#MT5214">MT5214</a> per questo caso. per ulteriori informazioni, vedere MT5214.
* Un'associazione o una libreria di terze parti è C++stata compilata utilizzando, ma non viene specificata `[LinkWith]` nell'attributo dal binding. Si tratta in genere di un'operazione abbastanza semplice da riconoscere, perché i simboli C++ sono simboli alterati (un `__ZNKSt9exception4whatEv`esempio comune è).
  - Se si è l'autore dell'associazione di terze parti o si ha accesso all'origine, modificare l' `[LinkWith]` attributo dell'associazione per impostare il `IsCxx` flag:

    ```csharp
    [LinkWith ("mylib.a", IsCxx = true)]
    ```

  - Se non è possibile modificare l'associazione di terze parti o se si esegue il collegamento manualmente con una libreria di terze parti, è possibile impostare il flag `-cxx` equivalente passando a mTouch. questa operazione viene eseguita modificando gli argomenti mTouch aggiuntivi nella pagina delle opzioni di compilazione iOS del progetto . Tenere presente che questa operazione deve essere eseguita per ogni configurazione di progetto.

<a name="MT5211" />

### <a name="mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a>MT5211: Il collegamento nativo non è riuscito e la classe Objective-C \*non è stata definita:. Il simbolo '\*' non è stato trovato in alcuna libreria o Framework collegato all'applicazione.

Ciò si verifica quando il linker nativo non riesce a trovare una classe Objective-C a cui si fa riferimento in un punto qualsiasi. Questo problema può verificarsi per diversi motivi: come per [MT5210](#MT5210) e in aggiunta:

* Un'associazione di terze parti ha associato un protocollo Objective-C, ma non l'ha annotato con l'attributo nella definizione dell' `[Protocol]` API. Soluzioni
  - Aggiungere l'attributo `[Protocol]` mancante:

    ```csharp
    [BaseType (typeof (NSObject))]
    [Protocol] // Add this
    public interface MyProtocol
    {
    }
    ```

<a name="MT5212" />

### <a name="mt5212-native-linking-failed-duplicate-symbol-"></a>MT5212: Il collegamento nativo non è riuscito. simbolo duplicato: *.

Ciò si verifica quando il linker nativo rileva simboli duplicati tra tutte le librerie native. In seguito a questo errore potrebbero essere presenti uno o più errori [MT5213](#MT5213) con la posizione per ogni occorrenza del simbolo. Possibili cause dell'errore:

* La stessa libreria nativa è inclusa due volte.
* Due librerie native distinte si verificano per definire gli stessi simboli.
* Una libreria nativa non è compilata correttamente e contiene lo stesso simbolo più di una volta.
  Per confermare questo problema, è possibile usare il set di comandi seguente da un terminale (sostituire i386 con x86_64/ARMv7/armv7s/arm64 in base all'architettura per cui si sta creando):

  ```
  # Native libraries are usually fat libraries, containing binary code for
  # several architectures in the same file. First we extract the binary
  # code for the architecture we're interested in.
  lipo libNative.a -thin i386 -output libNative.i386.a

  # Now query the native library for the duplicated symbol.
  nm libNative.i386.a | fgrep 'SYMBOL'

  # You can also list the object files inside the native library.
  # In most cases this will reveal duplicated object files.
  ar -t libNative.i386.a
  ```

  Per risolvere il problema, è possibile eseguire le operazioni seguenti:

  - Richiedere che il provider della libreria nativa la Correggi e fornisca la versione aggiornata.
  - Risolvere il problema rimuovendo i file oggetto aggiuntivi (funziona solo se il problema è in realtà file oggetto duplicati)

  ```
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
  ```

  - Richiedere al linker di rimuovere il codice inutilizzato. Novell. iOS eseguirà questa operazione automaticamente se vengono soddisfatte tutte le condizioni seguenti:
    - Tutti gli attributi dei `[LinkWith]` binding di terze parti hanno abilitato SmartLink:

      ```csharp
      [assembly: LinkWith ("libNative.a", SmartLink = true)]
      ```

    - No `-gcc_flags` viene passato a mTouch (nel campo argomenti mTouch aggiuntivi delle opzioni di compilazione iOS del progetto).
    - È anche possibile richiedere direttamente al linker di rimuovere il codice non usato aggiungendo `-gcc_flags -dead_strip` gli argomenti mTouch aggiuntivi nelle opzioni di compilazione iOS del progetto.

<a name="MT5213" />

### <a name="mt5213-duplicate-symbol-in--location-related-to-previous-error"></a>MT5213: Simbolo duplicato in: * (percorso correlato all'errore precedente)

Questo errore viene segnalato solo insieme a [MT5212](#MT5212). Per ulteriori informazioni, vedere [MT5212](#MT5212) .

<a name="MT5214" />

### <a name="mt5214-native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a>MT5214: Il collegamento nativo non è riuscito. simbolo non definito: *. A questo simbolo è stato fatto riferimento il membro gestito *. Verificare che sia stato fatto riferimento a tutti i Framework necessari e che siano state collegate librerie native.

Questo errore viene segnalato quando il codice gestito contiene un P/Invoke a un metodo nativo che non esiste. Ad esempio:

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

Esistono alcune possibili soluzioni:

- Rimuovere i P/Invoke in questione dal codice sorgente.
- Abilitare il linker gestito per tutti gli assembly (questa operazione viene eseguita nelle opzioni di compilazione iOS del progetto, impostando "comportamento del linker" su "tutti gli assembly"). In questo modo verranno rimossi tutti i P/Invoke non usati dall'app (automaticamente, anziché manualmente come il punto precedente). Lo svantaggio è che questa operazione renderà il simulatore più lento e potrebbe interrompere l'app se usa la reflection. altre informazioni sul linker sono disponibili [qui](~/ios/deploy-test/linker.md) )
- Creare una seconda libreria nativa che contiene i simboli nativi mancanti. Si noti che questa è semplicemente una soluzione alternativa (se si tenta di chiamare queste funzioni, l'app si arresterà in modo anomalo).

<a name="MT5215" />

### <a name="mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a>MT5215: I riferimenti a' *' potrebbero richiedere istruzioni aggiuntive-Framework = XXX o-lXXX al linker nativo

Si tratta di un avviso che indica che è stato rilevato un P/Invoke per fare riferimento alla libreria in questione, ma l'app non è collegata.

<a name="MT5216" />

### <a name="mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5216: Il collegamento nativo non è riuscito per *. Inviare un report sui bug in http://bugzilla.xamarin.com

Questo errore viene segnalato durante il collegamento dell'output dal compilatore AOT.

Questo errore indica con maggiore probabilità un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT5217" />

### <a name="mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a>MT5217: Il collegamento nativo potrebbe non essere riuscito perché la riga di comando del linker era troppo lungo (* caratteri).

Il collegamento nativo non è riuscito ed è possibile che si sia verificato perché il comando del linker era troppo lungo.

I progetti Novell. iOS spesso fanno riferimento a simboli nativi in modo dinamico, il che significa che il linker nativo potrebbe rimuovere questi simboli nativi durante il processo di collegamento nativo, perché il linker nativo non verifica che questi simboli vengano usati.

In genere, Novell. IOS chiederà al linker nativo di memorizzare tali simboli `-u symbol` usando il flag del linker, ma se sono presenti molti simboli di questo tipo, l'intera riga di comando potrebbe superare la lunghezza massima della riga di comando, come specificato dal sistema operativo.

Esistono alcune possibili origini per tali simboli dinamici:

* P/richiama i metodi nelle librerie collegate in modo statico, in cui il nome della `__Internal` dll è nell'attributo `[DllImport ("__Internal")]`dllimport.
* Riferimenti dei campi a posizioni di memoria nelle librerie collegate in modo statico da`[Field]` progetti di associazione (attributi).
* Le classi Objective-C a cui viene fatto riferimento nelle librerie collegate in modo statico dai progetti di associazione (quando si usano compilazioni incrementali o quando non si usa il registrar statico).

Possibili soluzioni:

* Abilitare il linker gestito (se possibile per tutti gli assembly anziché solo gli assembly SDK). Questo potrebbe rimuovere un numero sufficiente di origini per i simboli dinamici, in modo che la riga di comando del linker non superi il valore massimo.
* Ridurre il numero di P/Invoke, i riferimenti ai campi e/o le classi Objective-C.
* Riscrivere i simboli dinamici per avere nomi più brevi.
* Passare `-dlsym:false` come argomento mTouch aggiuntivo nelle opzioni di compilazione iOS del progetto. Con questa opzione, Novell. iOS genererà un riferimento nativo nel codice compilato con AOT e non sarà necessario richiedere al linker di memorizzare questo simbolo. Tuttavia, questo funziona solo per le compilazioni di dispositivi e causerà errori del linker se sono presenti P/Invoke per le funzioni che non esistono nella libreria statica.
* Passare `--dynamic-symbol-mode=code` come argomenti mTouch aggiuntivi nelle opzioni di compilazione iOS del progetto. Con questa opzione, Novell. iOS genererà codice nativo aggiuntivo che fa riferimento a questi simboli anziché chiedere al linker nativo di conservarli usando gli argomenti della riga di comando. Lo svantaggio di questo approccio è che aumenterà le dimensioni del file eseguibile.
* Abilitare il registrar statico passando `--registrar:static` come argomento mTouch aggiuntivo nelle opzioni di compilazione iOS del progetto (per le compilazioni del simulatore, perché il registrar statico è già quello predefinito per le compilazioni di dispositivi). Il registrar statico genererà codice che fa riferimento a classi Objective-C in modo statico, quindi non è necessario richiedere al linker nativo di mantengono tali classi.
* Disabilitare le compilazioni incrementali (per le compilazioni di dispositivi). Quando le compilazioni incrementali sono abilitate, il codice generato dal registrar statico non verrà considerato dal linker nativo, il che significa che Novell. iOS deve comunque richiedere al linker di mantenere le classi Objective-C a cui si fa riferimento. In questo modo, la disabilitazione delle compilazioni incrementali impedisce tale necessità.

<a name="MT5218" />

### <a name="mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MT5218: Non è possibile ignorare il simbolo dinamico {Symbol} (--Ignore-Dynamic-symbol = {symbol}) perché non è stato rilevato come simbolo dinamico.

L'argomento `--ignore-dynamic-symbol=symbol` della riga di comando è stato passato, ma questo simbolo non è un simbolo riconosciuto come simbolo dinamico che deve essere mantenuto manualmente.

Esistono due motivi principali:

* Il nome del simbolo non è corretto.
    * Non anteporre un carattere di sottolineatura al nome del simbolo.
    * Il simbolo per le classi Objective-C `OBJC_CLASS_$_<classname>`è.
* Il simbolo è corretto, ma è un simbolo che è già mantenuto per mezzo normale (alcune opzioni di compilazione causano la variazione esatta dell'elenco di simboli dinamici).

### <a name="mt53xx-other-tools"></a>MT53xx: Altri strumenti

<!--
  MT53xx other tools
  -->

<a name="MT5301" />

### <a name="mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a>MT5301: Manca lo strumento "strip". Installare il componente "strumenti da riga di comando" di Xcode

<a name="MT5302" />

### <a name="mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a>MT5302: Manca lo strumento ' dsymutil '. Installare il componente "strumenti da riga di comando" di Xcode

<a name="MT5303" />

### <a name="mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a>MT5303: Non è stato possibile generare i simboli di debug (directory dSYM). Verificare il log di compilazione.

Si è verificato un errore durante l'esecuzione di dsymutil nella directory Final. app per creare i simboli di debug. Esaminare il log di compilazione per visualizzare l'output di dsymutil e vedere come è possibile risolverlo.

<a name="MT5304" />

### <a name="mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a>MT5304: Non è stato possibile rimuovere il file binario finale. Verificare il log di compilazione.

Si è verificato un errore durante l'esecuzione dello strumento "strip" per rimuovere le informazioni di debug dall'applicazione.

<a name="MT5305" />

### <a name="mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a>MT5305: Manca lo strumento "Lipo". Installare il componente "strumenti da riga di comando" di Xcode

<a name="MT5306" />

### <a name="mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a>MT5306: Non è stato possibile creare una libreria FAT. Verificare il log di compilazione.

Si è verificato un errore durante l'esecuzione dello strumento "Lipo". Esaminare il log di compilazione per visualizzare l'errore segnalato da "Lipo".

<a name="MT5307" />

### <a name="mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a>MT5307: Non è stato possibile firmare il file eseguibile. Verificare il log di compilazione.

Si è verificato un errore durante la firma dell'applicazione. Esaminare il log di compilazione per visualizzare l'errore segnalato da' codesign '.

<!-- 5308 is used by mmp -->
<!-- 5309 is used by mmp -->
<!-- 5310 is used by mmp -->

## <a name="mt6xxx-mtouch-internal-tools-error-messages"></a>MT6xxx: messaggi di errore degli strumenti interni mTouch

### <a name="mt600x-stripper"></a>MT600x: Spogliarellista

<!--
 MT6xxx mtouch internal tools
  MT600x Stripper
  -->

<a name="MT6001" />

### <a name="mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a>MT6001: La versione in esecuzione di Cecil non supporta la rimozione degli assembly

<a name="MT6002" />

### <a name="mt6002-could-not-strip-assembly-"></a>MT6002: Impossibile rimuovere l'assembly `*`.

Si è verificato un errore durante la rimozione del codice gestito (rimozione del codice IL) dagli assembly nell'applicazione.

<a name="MT6003" />

### <a name="mt6003-unauthorizedaccessexception-message"></a>MT6003: [messaggio UnauthorizedAccessException]

Errore di sicurezza si è verificato durante la rimozione dei simboli di debug dall'applicazione.

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx: Messaggi di errore di MSBuild

<!--
 MT7xxx msbuild errors
  -->

<a name="MT7001" />

### <a name="mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a>MT7001: Non è stato possibile risolvere gli IP host per le impostazioni del debugger Wi-Fi.

*Attività MSBuild: DetectDebugNetworkConfigurationTaskBase*

Passaggi per la risoluzione dei problemi:

- provare a eseguire `csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'` , che dovrebbe fornire un indirizzo IP e non un errore, ovviamente.
- provare a eseguire "ping \`hostname\`", che potrebbe fornire ulteriori informazioni, ad esempio:`cannot resolve MyHost.local: Unknown host`

In alcuni casi, si tratta di un problema di "rete locale" e può essere risolto aggiungendo l'host `127.0.0.1   MyHost.local` sconosciuto in. `/etc/hosts`

<a name="MT7002" />

### <a name="mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a>MT7002: Il computer non dispone di schede di rete. Questa operazione è necessaria per il debug o la profilatura sul dispositivo tramite Wi-Fi.

*Attività MSBuild: DetectDebugNetworkConfigurationTaskBase*

<a name="MT7003" />

### <a name="mt7003-the-app-extension--does-not-contain-an-infoplist"></a>MT7003: L'estensione ' *' dell'app non contiene un file INFO. plist.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7004" />

### <a name="mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a>MT7004: L'estensione ' *' dell'app non specifica un CFBundleIdentifier.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7005" />

### <a name="mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a>MT7005: L'estensione ' *' dell'app non specifica un CFBundleExecutable.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7006" />

### <a name="mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7006: L'estensione '\*' dell'app ha un CFBundleIdentifier non\*valido () e non inizia con il CFBundleIdentifier del bundle dell'app principale (*).

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7007" />

### <a name="mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7007: L'estensione '\*' dell'app ha un CFBundleIdentifier\*() che termina con il suffisso ". Key" non valido.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7008" />

### <a name="mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a>MT7008: L'estensione ' *' dell'app non specifica un CFBundleShortVersionString.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7009" />

### <a name="mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7009: L'estensione ' *' dell'app contiene un file INFO. plist non valido: non contiene un dizionario NSExtension.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7010" />

### <a name="mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a>MT7010: L'estensione ' *' dell'app contiene un file INFO. plist non valido: il dizionario NSExtension non contiene un valore NSExtensionPointIdentifier.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7011" />

### <a name="mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a>MT7011: L'estensione WatchKit ' *' contiene un file INFO. plist non valido: il dizionario NSExtension non contiene un dizionario NSExtensionAttributes.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7012" />

### <a name="mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a>MT7012: L'estensione WatchKit ' *' non contiene esattamente un'app Watch.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7013" />

### <a name="mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a>MT7013: L'estensione WatchKit ' *' contiene un file INFO. plist non valido: UIRequiredDeviceCapabilities deve contenere la funzionalità "Watch-Companion".

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7014" />

### <a name="mt7014-the-watch-app--does-not-contain-an-infoplist"></a>MT7014: L'app Watch ' *' non contiene un file INFO. plist.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7015" />

### <a name="mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7015: L'app Watch "*" non specifica un CFBundleIdentifier.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7016" />

### <a name="mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7016: L'app Watch '\*' ha un CFBundleIdentifier non valido\*() e non inizia con il CFBundleIdentifier del bundle dell'app principale (*).

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7017" />

### <a name="mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a>MT7017: L'app Watch '\*' non ha un valore UIDeviceFamily valido. È previsto ' Watch (4)', ma\* è stato trovato ' (*)'.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7018" />

### <a name="mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7018: L'app Watch "*" non specifica un CFBundleExecutable

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7019" />

### <a name="mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a>MT7019: L'app Watch '\*' contiene un valore WKCompanionAppBundleIdentifier non valido (\*''), non corrisponde al CFBundleIdentifier del bundle dell'app principale (' *').

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7020" />

### <a name="mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a>MT7020: Il file INFO. plist dell'app Watch ' *' non è valido: la chiave WKWatchKitApp deve essere presente e il valore è' true '.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7021" />

### <a name="mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7021: Il file INFO. plist dell'app Watch ' *' non è valido: la chiave LSRequiresIPhoneOS non deve essere presente.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7022" />

### <a name="mt7022-the-watch-app--does-not-contain-a-watch-extension"></a>MT7022: L'app Watch ' *' non contiene un'estensione Watch.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7023" />

### <a name="mt7023-the-watch-extension--does-not-contain-an-infoplist"></a>MT7023: L'estensione ' *' dell'espressione di controllo non contiene un file INFO. plist.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7024" />

### <a name="mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a>MT7024: L'estensione Watch "*" non specifica un CFBundleIdentifier.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7025" />

### <a name="mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a>MT7025: L'estensione Watch "*" non specifica un CFBundleExecutable.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7026" />

### <a name="mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7026: L'estensione '\*' dell'espressione di controllo contiene un\*valore CFBundleIdentifier () non valido. non inizia con il CFBundleIdentifier del bundle dell'app principale (*).

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7027" />

### <a name="mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7027: L'estensione '\*' dell'espressione di controllo ha\*un CFBundleIdentifier () che termina con il suffisso ". Key" non valido.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7028" />

### <a name="mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7028: L'estensione ' *' dell'espressione di controllo contiene un file INFO. plist non valido: non contiene un dizionario NSExtension.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7029" />

### <a name="mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a>MT7029: L'estensione ' *' dell'espressione di controllo contiene un file INFO. plist non valido: NSExtensionPointIdentifier deve essere "com. Apple. watchkit".

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7030" />

### <a name="mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a>MT7030: L'estensione ' *' dell'espressione di controllo contiene un file INFO. plist non valido: il dizionario NSExtension deve contenere NSExtensionAttributes.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7031" />

### <a name="mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a>MT7031: L'estensione ' *' dell'espressione di controllo contiene un file INFO. plist non valido: il dizionario NSExtensionAttributes deve contenere un WKAppBundleIdentifier.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7032" />

### <a name="mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a>MT7032: L'estensione WatchKit ' *' contiene un file INFO. plist non valido: UIRequiredDeviceCapabilities non deve contenere la funzionalità "Watch-Companion".

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7033" />

### <a name="mt7033-the-watch-app--does-not-contain-an-infoplist"></a>MT7033: L'app Watch ' *' non contiene un file INFO. plist.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7034" />

### <a name="mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7034: L'app Watch "*" non specifica un CFBundleIdentifier.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7035" />

### <a name="mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a>MT7035: L'app Watch '\*' non ha un valore UIDeviceFamily valido. È previsto\*'', ma\* è\*stato trovato ' ()'.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7036" />

### <a name="mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7036: L'app Watch "*" non specifica un CFBundleExecutable.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7037" />

### <a name="mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a>MT7037: L'estensione WatchKit ' {ExtensionName}' ha un valore WKAppBundleIdentifier non valido (\*''), non corrisponde al CFBundleIdentifier ('\*') dell'app Watch.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7038" />

### <a name="mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a>MT7038: L'app Watch ' *' contiene un file INFO. plist non valido: il WKCompanionAppBundleIdentifier deve esistere e deve corrispondere al CFBundleIdentifier del bundle dell'app principale.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7039" />

### <a name="mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7039: Il file INFO. plist dell'app Watch ' *' non è valido: la chiave LSRequiresIPhoneOS non deve essere presente.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7040" />

### <a name="mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a>MT7040: Il bundle dell'app {AppBundlePath} non contiene un file INFO. plist.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7041" />

### <a name="mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a>MT7041: Il percorso principale info. plist non specifica un CFBundleIdentifier.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7042" />

### <a name="mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a>MT7042: Il percorso principale info. plist non specifica un CFBundleExecutable.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7043" />

### <a name="mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a>MT7043: Il percorso principale info. plist non specifica un CFBundleSupportedPlatforms.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7044" />

### <a name="mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a>MT7044: Il percorso principale info. plist non specifica un UIDeviceFamily.

*Attività MSBuild: ValidateAppBundleTaskBase*

<a name="MT7045" />

### <a name="mt7045-unrecognized-format-"></a>MT7045: Formato non riconosciuto: *.

*Attività MSBuild: PropertyListEditorTaskBase*

Dove * può essere:

- string
- array
- dict
- bool
- real
- integer
- date
- data

<a name="MT7046" />

### <a name="mt7046-add-entry--incorrectly-specified"></a>MT7046: Aggiungi: Voce, *, specificata in modo errato.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7047" />

### <a name="mt7047-add-entry--contains-invalid-array-index"></a>MT7047: Aggiungi: Entry, *, contiene un indice di matrice non valido.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7048" />

### <a name="mt7048-add--entry-already-exists"></a>MT7048: Aggiunta: * la voce esiste già.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7049" />

### <a name="mt7049-add-cant-add-entry--to-parent"></a>MT7049: Aggiungi: Impossibile aggiungere voce, *, al padre.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7050" />

### <a name="mt7050-delete-cant-delete-entry--from-parent"></a>MT7050: Elimina: Non è possibile eliminare la voce, *, dall'elemento padre.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7051" />

### <a name="mt7051-delete-entry--contains-invalid-array-index"></a>MT7051: Elimina: Entry, *, contiene un indice di matrice non valido.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7052" />

### <a name="mt7052-delete-entry--does-not-exist"></a>MT7052: Elimina: La voce "*" non esiste.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7053" />

### <a name="mt7053-import-entry--incorrectly-specified"></a>MT7053: Importare Voce, *, specificata in modo errato.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7054" />

### <a name="mt7054-import-entry--contains-invalid-array-index"></a>MT7054: Importare Entry, *, contiene un indice di matrice non valido.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7055" />

### <a name="mt7055-import-error-reading-file-"></a>MT7055: Importare Errore durante la lettura del file: *.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7056" />

### <a name="mt7056-import-cant-add-entry--to-parent"></a>MT7056: Importare Impossibile aggiungere voce, *, al padre.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7057" />

### <a name="mt7057-merge-cant-add-array-entries-to-dict"></a>MT7057: Merge Non è possibile aggiungere voci di matrice a dict.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7058" />

### <a name="mt7058-merge-specified-entry-must-be-a-container"></a>MT7058: Merge La voce specificata deve essere un contenitore.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7059" />

### <a name="mt7059-merge-entry--contains-invalid-array-index"></a>MT7059: Merge Entry, *, contiene un indice di matrice non valido.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7060" />

### <a name="mt7060-merge-entry--does-not-exist"></a>MT7060: Merge La voce "*" non esiste.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7061" />

### <a name="mt7061-merge-error-reading-file-"></a>MT7061: Merge Errore durante la lettura del file: *.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7062" />

### <a name="mt7062-set-entry--incorrectly-specified"></a>MT7062: Impostare: Voce, *, specificata in modo errato.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7063" />

### <a name="mt7063-set-entry--contains-invalid-array-index"></a>MT7063: Impostare: Entry, *, contiene un indice di matrice non valido.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7064" />

### <a name="mt7064-set-entry--does-not-exist"></a>MT7064: Impostare: La voce "*" non esiste.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7065" />

### <a name="mt7065-unknown-propertylist-editor-action-"></a>MT7065: Azione dell'editor proprietà sconosciuta: *.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7066" />

### <a name="mt7066-error-loading--"></a>MT7066: Errore durante il caricamento di ' *': *.

*Attività MSBuild: PropertyListEditorTaskBase*

<a name="MT7067" />

### <a name="mt7067-error-saving--"></a>MT7067: Errore durante il salvataggio di ' *': *.

*Attività MSBuild: PropertyListEditorTaskBase*

## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx: Messaggi di errore di runtime

<!--
 MT8xxx runtime
  MT800x misc
  -->

<a name="MT8001" />

### <a name="mt8001-version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a>MT8001: Mancata corrispondenza tra le versioni del runtime Novell. iOS nativo e di MonoTouch. dll. Reinstallare Novell. iOS.

<a name="MT8002" />

### <a name="mt8002-could-not-find-the-method--in-the-type-"></a>MT8002: Non è stato possibile trovare il\*metodo '' nel tipo\*''.

<a name="MT8003" />

### <a name="mt8003-failed-to-find-the-closed-generic-method--on-the-type-"></a>MT8003: Non è stato possibile trovare il metodo generico\*chiuso '' nel tipo\*''.

<a name="MT8004" />

### <a name="mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a>MT8004: Non è possibile creare un'istanza di * per l'oggetto nativo 0x * (di tipo ' *') perché esiste già un'altra istanza per questo oggetto nativo (di tipo *).

<a name="MT8005" />

### <a name="mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a>MT8005: Nel tipo wrapper\*'' manca la classe ObjectiveC nativa '\*'.

<a name="MT8006" />

### <a name="mt8006-failed-to-find-the-selector--on-the-type-"></a>MT8006: Il selettore '\*' non è stato trovato nel\*tipo ''

<a name="MT8007" />

### <a name="mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a>MT8007: Non è possibile ottenere il descrittore del\*metodo per il selettore '' nel tipo '\*' perché il selettore non corrisponde a un metodo

<a name="MT8008" />

### <a name="mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8008: La versione caricata di Novell. iOS. dll è stata compilata per * BITS, mentre il processo è * BITS. Inviare un bug all'indirizzo http://bugzilla.xamarin.com.

Ciò indica che si è verificato un errore nel processo di compilazione. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8009" />

### <a name="mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8009: Impossibile individuare il blocco da delegare al metodo di conversione per il metodo *.* parametro s # *. Inviare un bug all'indirizzo http://bugzilla.xamarin.com.

Indica che un'API non è stata associata correttamente. Se si tratta di un'API esposta da Novell, inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new). Se si tratta di un'associazione di terze parti, rivolgersi al fornitore.

<a name="MT8010" />

### <a name="mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a>MT8010: Dimensioni del tipo nativo non corrispondenti tra Novell. [iOS | Mac]. dll e l'architettura in esecuzione. Novell. [iOS | Mac]. dll è stato compilato per * bit, mentre il processo corrente è * bit.

Ciò indica che si è verificato un errore nel processo di compilazione. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8011" />

### <a name="mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8011: Impossibile individuare il delegato per bloccare l'attributo di conversione ([DelegateProxy]) per il valore restituito per il metodo *.* Inviare un bug all'indirizzo http://bugzilla.xamarin.com.

Novell. iOS non è stato in grado di individuare un metodo necessario in fase di esecuzione (per convertire un delegato in un blocco).

Questo indica in genere un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8012" />

### <a name="mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8012: DelegateProxyAttribute non valido per il valore restituito per il metodo *.* : DelegateType è null. Inviare un bug all'indirizzo http://bugzilla.xamarin.com.

L'attributo DelegateProxy per il metodo in questione non è valido.

Questo indica in genere un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8013" />

### <a name="mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8013: DelegateProxyAttribute non valido per il valore restituito per il metodo *.* : DelegateType ({2}) specifica un tipo senza un campo ' Handler '. Inviare un bug all'indirizzo http://bugzilla.xamarin.com.

L' `[DelegateProxy]` attributo per il metodo in questione non è valido.

Questo indica in genere un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8014" />

### <a name="mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8014: DelegateProxyAttribute non valido per il valore restituito per il metodo *.* : Il campo ' Handler{2}' di delegateType è null. Inviare un bug all'indirizzo http://bugzilla.xamarin.com.

L' `[DelegateProxy]` attributo per il metodo in questione non è valido.

Questo indica in genere un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8015" />

### <a name="mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8015: DelegateProxyAttribute non valido per il valore restituito per il metodo *.* : Il campo ' Handler{2}' di delegateType () non è un delegato, è un *. Inviare un bug all'indirizzo http://bugzilla.xamarin.com.

L'attributo DelegateProxy per il metodo in questione non è valido.

Questo indica in genere un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8016" />

### <a name="mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8016: Non è possibile convertire il delegato in blocco per il valore restituito per il metodo *.* poiché l'input non è un delegato, è un *. Inviare un bug all'indirizzo http://bugzilla.xamarin.com.

L' `[DelegateProxy]` attributo per il metodo in questione non è valido.

Questo indica in genere un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<!-- 8017 is used by mmp -->

<a name="MT8018" />

### <a name="mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8018: Errore di coerenza interna. Inviare un report sui bug all' http://bugzilla.xamarin.com indirizzo.

Ciò indica un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8019" />

### <a name="mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a>MT8019: Impossibile trovare l'assembly * negli assembly caricati.

Ciò indica un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8020" />

### <a name="mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a>MT8020: Il modulo con MetadataToken * non è stato trovato nell'assembly *.

Ciò indica un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8021" />

### <a name="mt8021-unknown-implicit-token-type-"></a>MT8021: Tipo di token implicito sconosciuto: *.

Ciò indica un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8022" />

### <a name="mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8022: È previsto che il riferimento al token * sia un *, ma è un *. Inviare un report sui bug all' http://bugzilla.xamarin.com indirizzo.

Ciò indica un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8023" />

### <a name="mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8023: Un oggetto istanza è necessario per costruire un metodo generico chiuso per il metodo generico aperto: * (riferimento al token: *). Inviare un report sui bug all' http://bugzilla.xamarin.com indirizzo.

Ciò indica un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8024" />

### <a name="mt8024-could-not-find-a-valid-extension-type-for-the-smart-enum-smart_type-please-file-a-bug-at-httpsbugzillaxamarincom"></a>MT8024: Non è stato possibile trovare un tipo di estensione valido per l'enumerazione intelligente ' {smart_type}'. Inviare un bug all'indirizzo https://bugzilla.xamarin.com.

Ciò indica un bug in Novell. iOS. Inviare un nuovo problema in [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).
