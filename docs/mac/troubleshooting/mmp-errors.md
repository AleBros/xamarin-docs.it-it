---
title: Messaggi di errore di Novell. Mac (MMP)
description: Questo documento elenca gli errori generati da MMP, lo strumento usato per creare un pacchetto di assembly compilati in un'applicazione Mac eseguibile.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5B26339F-A202-4E41-9229-D0BC9E77868E
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: 32fecaff7f6896c03f3ac094d478fef3338f844d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025727"
---
# <a name="xamarinmac-error-messages-mmp"></a>Messaggi di errore di Novell. Mac (MMP)

## <a name="mm0xxx-mmp-error-messages"></a>MM0xxx: messaggi di errore MMP

Ad esempio, parametri, ambiente, strumenti mancanti.

<a name="MM0000" />

#### <a name="mm0000-unexpected-error---please-file-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MM0000: errore imprevisto. inviare un report sui bug in https://github.com/xamarin/xamarin-macios/issues/new

Si è verificata una condizione di errore imprevista. Inviare [un report sui bug](https://github.com/xamarin/xamarin-macios/issues/new) con quante più informazioni possibile, tra cui:

* Log di compilazione completi, con livello di dettaglio massimo, ad esempio `-v -v -v -v` negli **argomenti aggiuntivi di MMP**;
* Test case minimo per riprodurre l'errore. e
* Tutte le informazioni sulla versione

Il modo più semplice per ottenere le informazioni esatte sulla versione consiste nell'usare il menu **Xamarin Studio** , informazioni **su Xamarin Studio** elemento, visualizzare il pulsante **Dettagli** e copiare e incollare le informazioni sulla versione. è possibile usare il pulsante **copia informazioni** .

<a name="MM0001" />

#### <a name="mm0001-this-version-of-xamarinmac-requires-mono-0-the-current-mono-version-is-1-please-update-the-monoframework-from-httpmono-projectcomdownloads"></a>MM0001: questa versione di Novell. Mac richiede mono {0} (la versione corrente di mono è {1}). Aggiornare mono. Framework da http://mono-project.com/Downloads

<a name="MM0003" />

#### <a name="mm0003-application-name-0exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MM0003: il nome dell'applicazione '{0}. exe ' è in conflitto con un nome di assembly SDK o prodotto (con estensione dll).

<a name="MM0007" />

#### <a name="mm0007-the-root-assembly-0-does-not-exist"></a>MM0007: l'assembly radice '{0}' non esiste

<a name="MM0008" />

#### <a name="mm0008-you-should-provide-one-root-assembly-only-found-0-assemblies-1"></a>MM0008: è necessario specificare solo un assembly radice, trovato {0} assembly:'{1}'

<a name="MM0009" />

#### <a name="mm0009-error-while-loading-assemblies-"></a>MM0009: errore durante il caricamento degli assembly: *.

Si è verificato un errore durante il caricamento degli assembly dai riferimenti all'assembly radice. Ulteriori informazioni possono essere fornite nell'output di compilazione.

<a name="MM0010" />

#### <a name="mm0010-could-not-parse-the-command-line-arguments-0"></a>MM0010: non è stato possibile analizzare gli argomenti della riga di comando: {0}

<!-- 0013 is unused -->

<a name="MM0016" />

#### <a name="mm0016-the-option-0-has-been-deprecated"></a>MM0016: l'opzione '{0}' è stata deprecata.

<a name="MM0017" />

#### <a name="mm0017-you-should-provide-a-root-assembly"></a>MM0017: è necessario fornire un assembly radice

<a name="MM0018" />

#### <a name="mm0018-unknown-command-line-argument-0"></a>MM0018: argomento della riga di comando sconosciuto:'{0}'

<a name="MM0020" />

#### <a name="mm0020-the-valid-options-for-0-are-1"></a>MM0020: le opzioni valide per '{0}' sono '{1}'.

<a name="MM0023" />

#### <a name="mm0023-application-name-0exe-conflicts-with-another-user-assembly"></a>MM0023: il nome dell'applicazione '{0}. exe ' è in conflitto con un altro assembly utente.

<a name="MM0026" />

#### <a name="mm0026-could-not-parse-the-command-line-argument-0-1"></a>MM0026: non è stato possibile analizzare l'argomento della riga di comando '{0}': {1}

<a name="MM0043" />

#### <a name="mm0043-the-boehm-garbage-collector-is-not-supported-the-sgen-garbage-collector-has-been-selected-instead"></a>MM0043: il Garbage Collector Boehm non è supportato. Il Garbage Collector SGen è stato selezionato.

<a name="MM0050" />

#### <a name="mm0050-you-cannot-provide-a-root-assembly-if---no-root-assembly-is-passed"></a>MM0050: non è possibile fornire un assembly radice se viene passato--No-root-assembly.

<a name="MM0051" />

#### <a name="mm0051-an-output-directory---output-is-required-if---no-root-assembly-is-passed"></a>MM0051: è richiesta una directory di output (--output) se viene passato--No-root-assembly.

<a name="MM0053" />

#### <a name="mm0053-cannot-disable-new-refcount-with-the-unified-api"></a>MM0053: non è possibile disabilitare la nuova refcount con la API unificata.

<a name="MM0056" />

#### <a name="mm0056-cannot-find-xcode-in-any-of-our-default-locations-please-install-xcode-or-pass-a-custom-path-using---sdkrootpath"></a>MM0056: non è possibile trovare Xcode nei percorsi predefiniti. Installare Xcode o passare un percorso personalizzato usando--sdkroot =\<percorso >

<a name="MM0059" />

#### <a name="mm0059-could-not-find-the-currently-selected-xcode-on-the-system-0"></a>MM0059: Impossibile trovare il Xcode attualmente selezionato nel sistema: {0};

<a name="MM0060" />

#### <a name="mm0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned-0-but-that-directory-does-not-exist"></a>MM0060: Impossibile trovare il Xcode attualmente selezionato nel sistema. ' Xcode-Select--Print-Path ' ha restituito '{0}', ma tale directory non esiste.

<a name="MM0068" />

#### <a name="mm0068-invalid-value-for-target-framework-0"></a>MM0068: valore non valido per il Framework di destinazione: {0}.

<a name="MM0071" />

#### <a name="mm0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinmac-please-file-a-bug-report-at-httpsbugzillaxamarincom-with-a-test-case"></a>MM0071: piattaforma sconosciuta: *. Questo indica in genere un bug in Novell. Mac; inviare un report sui bug in https://bugzilla.xamarin.com con una test case.

Questo indica in genere un bug in Novell. Mac; inviare un report sui bug in [https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac) con una test case.

<a name="MM0073" />

#### <a name="mm0073-xamarinmac--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MM0073: Novell. Mac \* non supporta una destinazione di distribuzione di \* (il valore minimo è \*). Selezionare una destinazione di distribuzione più recente nel file INFO. plist del progetto.

La destinazione di distribuzione minima è quella specificata nel messaggio di errore. Selezionare una destinazione di distribuzione più recente nel file INFO. plist del progetto.

Se non è possibile aggiornare la destinazione di distribuzione, usare una versione precedente di Novell. Mac.

<a name="MM0074" />

#### <a name="mm0074-xamarinmac--does-not-support-a-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinmac"></a>MM0074: Novell. Mac \* non supporta una destinazione di distribuzione di \* (il massimo è \*). Selezionare una destinazione di distribuzione precedente nel file INFO. plist del progetto o eseguire l'aggiornamento a una versione più recente di Novell. Mac.

Novell. Mac non supporta l'impostazione della destinazione di distribuzione minima su una versione superiore rispetto alla versione per la quale è stata creata questa versione specifica di Novell. Mac.

Selezionare una destinazione di distribuzione minima precedente nel file INFO. plist del progetto o eseguire l'aggiornamento a una versione più recente di Novell. Mac.

<a name="MM0079" />

#### <a name="mm0079-internal-error---no-executable-was-copied-into-the-app-bundle-please-contact-supportxamarincom"></a>MM0079: errore interno-nessun file eseguibile copiato nel bundle dell'app. Contattare 'support@xamarin.com'

<a name="MM0080" />

#### <a name="mm0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MM0080: la disabilitazione di NewRefCount,--New-refcount: false, è deprecata.

<!-- 0088 used by mtouch -->
<!-- 0089 used by mtouch -->

<a name="MM0091" />

#### <a name="mm0091-this-version-of-xamarinmac-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-use-the-dynamic-registrar-or-set-the-managed-linker-behaviour-to-link-platform-or-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MM0091: questa versione di Novell. Mac richiede il \* SDK (fornito con Xcode \*). Aggiornare Xcode per ottenere i file di intestazione necessari o usare il registrar dinamico oppure impostare il comportamento del linker gestito per collegare solo gli SDK della piattaforma o del Framework di collegamento (per provare a evitare le nuove API).

Novell. Mac richiede che i file di intestazione, dalla versione SDK specificata nel messaggio di errore, creino l'applicazione con il registrar statico. Il metodo consigliato per correggere questo errore consiste nell'aggiornare Xcode per ottenere l'SDK necessario, inclusi tutti i file di intestazione necessari. Se sono installate più versioni di Xcode o si vuole usare un Xcode in un percorso non predefinito, assicurarsi di impostare il percorso di Xcode corretto nelle preferenze dell'IDE.

Una possibile soluzione alternativa consiste nell'abilitare il linker gestito. Questa operazione rimuoverà l'API non usata, incluso, nella maggior parte dei casi, la nuova API in cui i file di intestazione sono mancanti (o incompleti). Questa operazione non funzionerà tuttavia se il progetto usa l'API introdotta in un SDK più recente rispetto a quella fornita da Xcode.

Una seconda potenziale soluzione alternativa consiste nell'usare invece il registrar dinamico. In questo modo si imposterà un costo di avvio per la registrazione dinamica dei tipi, ma si rimuoverà il requisito del file di intestazione. 

Una soluzione Last-Straw è quella di usare una versione precedente di Novell. Mac, che supporta l'SDK richiesto dal progetto.

<a name="MM0097" />

#### <a name="mm0097-machineconfig-file-0-can-not-be-found"></a>MM0097: non è possibile trovare il file Machine. config '{0}'.

<a name="MM0098" />

#### <a name="mm0098-aot-compilation-is-only-available-on-unified"></a>MM0098: la compilazione AOT è disponibile solo in Unified

<a name="MM0099" />

#### <a name="mm0099-internal-error-0-please-file-a-bug-report-with-a-test-case-httpsbugzillaxamarincom"></a>MM0099: errore interno {0}. Inviare un report sui bug con un test case (https://bugzilla.xamarin.com).

<a name="MM0114" />

#### <a name="mm0114-hybrid-aot-compilation-requires-all-assemblies-to-be-aot-compiled"></a>MM0114: la compilazione AOT ibrida richiede che tutti gli assembly siano compilati in AOT.

<a name="MM0129" />

#### <a name="mm0129-debugging-symbol-file-for--does-not-match-the-assembly-and-is-ignored"></a>MM0129: il file di simboli di debug per ' *' non corrisponde all'assembly e viene ignorato.

Non è stato possibile caricare i simboli di debug, ovvero un file con estensione pdb (solo PDB portatile) o un file con estensione mdb, per l'assembly specificato.

Ciò significa in genere che l'assembly è più recente o meno recente dei simboli. Poiché non corrispondono, non possono essere usati e i simboli vengono ignorati.

Questo avviso non influirà sull'applicazione compilata, ma potrebbe non essere possibile eseguirne il debug interamente (in particolare il codice dell'assembly specificato). Potrebbero inoltre mancare alcune informazioni sulle eccezioni, le analisi dello stack e le segnalazioni di arresti anomali.

Segnalare questo problema all'editore del pacchetto di assembly (ad esempio, autore NuGet), in modo che sia possibile risolverlo nelle versioni future.

<a name="MM0130" />

#### <a name="mm0130-no-root-assemblies-found-you-should-provide-at-least-one-root-assembly"></a>MM0130: non sono stati trovati assembly radice. È necessario fornire almeno un assembly radice.

Quando si esegue `--runregistrar`, deve essere fornito almeno un assembly radice.

<a name="MM0131" />

#### <a name="mm0131-product-assembly-0-not-found-in-assembly-list-1"></a>MM0131: l'assembly '{0}' del prodotto non è stato trovato nell'elenco di assembly:'{1}'

Quando si esegue `--runregistrar`, l'elenco di assembly deve includere l'assembly del prodotto, Novell. Mac, XamMac.

<a name="MM0132" />

#### <a name="mm0132-unknown-optimization--valid-values-are-"></a>MM0132: ottimizzazione sconosciuta: \*. I valori validi sono: \*

L'ottimizzazione specificata non è stata riconosciuta.

Il formato accettato è `[+|-]optimization-name`, dove `optimization-name` è uno dei valori elencati nel messaggio di errore.

Per una descrizione completa di ogni ottimizzazione, vedere [ottimizzazioni delle compilazioni](https://developer.xamarin.com/guides/cross-platform/macios/build-optimizations) .

<a name="MM0133" />

#### <a name="mm0133-found-more-than-1-assembly-matching-0-choosing-first-1"></a>MM0133: sono stati trovati più di 1 assembly corrispondenti a'{0}' che scelgono First:'{1}'

<a name="MM0134" />

#### <a name="mm0134-32-bit-applications-should-be-migrated-to-64-bit"></a>MM0134: è necessario eseguire la migrazione delle applicazioni a 32 bit a 64 bit.

Apple ha annunciato che non consentirà l'invii di App Store macOS di app a 32 bit (a partire dal 2018 gennaio). 

Inoltre, le applicazioni a 32 bit non vengono eseguite sulla versione di macOS dopo High Sierra "senza compromessi". 

Per altri dettagli: https://developer.apple.com/news/?id=06282017a

Si consiglia di aggiornare l'applicazione e tutte le dipendenze a 64 bit.

<a name="MM0135" />

#### <a name="mm0135-did-not-link-system-framework-0-referenced-by-assembly-1-because-it-was-introduced-in-2-3-and-were-using-the-2-4-sdk"></a>MM0135: non è stato collegato il Framework di sistema '{0}' (a cui fa riferimento l'assembly '{1}') perché è stato introdotto in {2} {3}e viene usato {2} SDK {4}.

Per compilare l'applicazione, Novell. Mac deve collegarsi a librerie di sistema, alcune delle quali dipendono dalla versione SDK specificata nel messaggio di errore. Poiché si sta usando una versione precedente dell'SDK, le chiamate a tali API possono avere esito negativo in fase di esecuzione.

Il metodo consigliato per correggere questo errore consiste nell'aggiornare Xcode per ottenere l'SDK necessario. Se sono installate più versioni di Xcode o si vuole usare un Xcode in un percorso non predefinito, assicurarsi di impostare il percorso di Xcode corretto nelle preferenze dell'IDE.

In alternativa, consentire al [linker](https://docs.microsoft.com/xamarin/mac/deploy-test/linker) gestito di rimuovere le API inutilizzate, tra cui, nella maggior parte dei casi, quelle nuove che richiedono la libreria specificata. Tuttavia, questa operazione non funzionerà se il progetto richiede API introdotte in un SDK più recente rispetto a quello fornito da Xcode.

Come ultima soluzione di paglia, usare una versione precedente di Novell. Mac che non richiede che questi nuovi SDK siano presenti durante il processo di compilazione.

## <a name="mm1xxx-file-copy--symlinks-project-related"></a>MM1xxx: copia del file/collegamenti simbolici (correlati al progetto)

<a name="MM1034" />

#### <a name="mm1034-could-not-create-symlink-file---target-error-number"></a>MM1034: non è stato possibile creare il collegamento simbolico ' {file}'->' {target}': errore {Number}

### <a name="mm14xx-product-assemblies"></a>MM14xx: assembly del prodotto

<a name="MM1401" />

#### <a name="mm1401-the-required-0-assembly-is-missing-from-the-references"></a>MM1401: manca l'assembly '{0}' obbligatorio nei riferimenti

<a name="MM1402" />

#### <a name="mm1402-the-assembly-0-is-not-compatible-with-this-tool"></a>MM1402: l'assembly '{0}' non è compatibile con questo strumento

<a name="MM1403" />

#### <a name="mm1403-0-1-could-not-be-found-target-framework-0-is-unusable-to-package-the-application"></a>MM1403: Impossibile trovare {0}'{1}'. Il Framework di destinazione '{0}' non è utilizzabile per creare il pacchetto dell'applicazione.

<a name="MM1404" />

#### <a name="mm1404-target-framework-0-is-invalid"></a>MM1404: il Framework di destinazione '{0}' non è valido.

<a name="MM1405" />

#### <a name="mm1405-usefullxammacframework-must-always-target-framework-net-45-not-0-which-is-invalid"></a>MM1405: useFullXamMacFramework deve sempre avere come destinazione il Framework .NET 4,5, non '{0}', che non è valido

<a name="MM1406" />

#### <a name="mm1406-target-framework-0-is-invalid-when-targetting-xamarinmac-45-net-framwork"></a>MM1406: il Framework di destinazione '{0}' non è valido quando destinazione Novell. Mac 4,5 .NET Fram.

<a name="MM1407" />

#### <a name="mm1407-mismatch-between-xamarinmac-reference-0-and-target-framework-selected-1"></a>MM1407: mancata corrispondenza tra il riferimento Novell. Mac '{0}' e il Framework di destinazione selezionato '{1}'.

### <a name="mm15xx-assembly-gathering-not-requiring-linker-errors"></a>MM15xx: errori di raccolta assembly (senza necessità del linker)

<a name="MM1501" />

#### <a name="mm1501-can-not-resolve-reference-0"></a>MM1501: non è possibile risolvere il riferimento: {0}

### <a name="machocs"></a>MachO.cs

<a name="MM1600" />

#### <a name="mm1600-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1600: non è una libreria dinamica Mach-O (intestazione sconosciuta ' 0x{0}'): {1}.

<a name="MM1601" />

#### <a name="mm1601-not-a-static-library-unknown-header-0-1"></a>MM1601: non è una libreria statica (intestazione sconosciuta '{0}'): {1}.

<a name="MM1602" />

#### <a name="mm1602-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1602: non è una libreria dinamica Mach-O (intestazione sconosciuta ' 0x{0}'): {1}.

<a name="MM1603" />

#### <a name="mm1603-unknown-format-for-fat-entry-at-position-0-in-1"></a>MM1603: formato sconosciuto per la voce FAT alla posizione {0} in {1}.

<a name="MM1604" />

#### <a name="mm1604-file-of-type-0-is-not-a-macho-file-1"></a>MM1604: il file di tipo {0} non è un file Macho ({1}).

## <a name="mm2xxx-linker"></a>MM2xxx: linker

### <a name="mm20xx-linker-general-errors"></a>MM20xx: errori del linker (generale)

<a name="MM2001" />

#### <a name="mm2001-could-not-link-assemblies"></a>MM2001: non è stato possibile collegare gli assembly

<a name="MM2002" />

#### <a name="mm2002-can-not-resolve-reference-0"></a>MM2002: non è possibile risolvere il riferimento: {0}

<a name="MM2003" />

#### <a name="mm2003-option-0-will-be-ignored-since-linking-is-disabled"></a>MM2003: l'opzione '{0}' verrà ignorata perché il collegamento è disabilitato

<a name="MM2004" />

#### <a name="mm2004-extra-linker-definitions-file-0-could-not-be-located"></a>MM2004: non è stato possibile trovare il file di definizioni del linker aggiuntivo '{0}'.

<a name="MM2005" />

#### <a name="mm2005-definitions-from-0-could-not-be-parsed"></a>MM2005: non è stato possibile analizzare le definizioni di '{0}'.

<a name="MM2006" />

#### <a name="mm2006-native-library-0-was-referenced-but-could-not-be-found"></a>MM2006: è stato fatto riferimento alla libreria nativa '{0}', che però non è stata trovata.

<a name="MM2007" />

#### <a name="mm2007-xamarinmac-unified-api-against-a-full-net-profile-does-not-support-linking-pass-the--nolink-flag"></a>MM2007: Novell. Mac API unificata rispetto a un profilo .NET completo non supporta il collegamento. Passare il flag-nolink.

<a name="MM2009" />

#### <a name="mm2009-referenced-by-01------this-message-is-related-to-mm2006-"></a>MM2009: a cui fa riferimento {0}.{1} @ no__t_2_\* questo messaggio è correlato a MM2006 \*\*

<a name="MM2010" />

#### <a name="mm2010-unknown-httpmessagehandler-0-valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MM2010: `{0}`HttpMessageHandler sconosciuta. I valori validi sono HttpClientHandler (impostazione predefinita), CFNetworkHandler o NSUrlSessionHandler

<a name="MM2011" />

#### <a name="mm2011-unknown-tlsprovider-0--valid-values-are-default-or-appletls"></a>MM2011: TLSProvider sconosciuto '{0}.  I valori validi sono default o appletls

<a name="MM2012" />

#### <a name="mm2012-only-first-0-of-1-referenced-by-warnings-shown--this-message-related-to-2009-"></a>MM2012: vengono visualizzate solo le prime {0} di {1} "a cui fa riferimento". \*\* questo messaggio correlato a 2009 \*\*

<a name="MM2013" />

#### <a name="mm2013-failed-to-resolve-the-reference-to-0-referenced-in-1-the-app-will-not-include-the-referenced-assembly-and-may-fail-at-runtime"></a>MM2013: non è stato possibile risolvere il riferimento a "{0}", a cui viene fatto riferimento in "{1}". L'app non includerà l'assembly a cui si fa riferimento e potrebbe non riuscire in fase di esecuzione.

<a name="MM2014" />

#### <a name="mm2014-xamarinmac-extensions-do-not-support-linking-request-for-linking-will-be-ignored--this-message-is-obsolete-in-xm-36-"></a>MM2014: le estensioni Novell. Mac non supportano il collegamento. La richiesta di collegamento verrà ignorata. \*\* questo messaggio è obsoleto in XM 3.6 + \*\*

<!-- 2015 used by mtouch -->

<a name="MM2016" />

#### <a name="mm2016-invalid-tlsprovider-0-option-the-only-valid-value-1-will-be-used"></a>MM2016: opzione TlsProvider `{0}` non valida. Verrà utilizzato l'unico valore valido `{1}`.

<a name="MM2017" />

#### <a name="mm2017-could-not-process-xml-description-0"></a>MM2017: Impossibile elaborare la descrizione XML: {0}

<a name="MM202x" />

#### <a name="mm202x-binding-optimizer-failed-processing-"></a>MM202x: l'elaborazione dell'utilità di ottimizzazione del binding non è riuscita. `...`.

<a name="MM2100" />

#### <a name="mm2100-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2100: Novell. Mac API classica non supporta il collegamento alla piattaforma.

<a name="MM2103" />

#### <a name="mm2103-error-processing-assembly--"></a>MM2103: errore durante l'elaborazione dell'assembly '\*': *

Si è verificato un errore imprevisto durante l'elaborazione di un assembly.

Il nome dell'assembly che causa il problema è indicato nel messaggio di errore. Per risolvere questo problema, è necessario fornire l'assembly in un [report sui bug](https://bugzilla.xamarin.com) insieme a un log di compilazione completo con il livello di dettaglio abilitato, ad esempio `-v -v -v -v` negli **argomenti mTouch aggiuntivi**.

<a name="MM2104" />

#### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104: non è possibile collegare l'assembly '{0}' perché è in modalità mista.

Gli assembly in modalità mista non possono essere elaborati dal linker.

Per ulteriori informazioni sugli assembly in modalità mista, vedere https://docs.microsoft.com/cpp/dotnet/mixed-native-and-managed-assemblies.

<a name="MM2106" />

#### <a name="mm2106-could-not-optimize-the-call-to-blockliteralsetupblockunsafe-in--at-offset--because-"></a>MM2106: non è stato possibile ottimizzare la chiamata a BlockLiteral. SetupBlock [unsafe] in \* all'offset \* perché \*.

Il linker segnala questo avviso quando non è in grado di ottimizzare una chiamata a `BlockLiteral.SetupBlock` o `Block.SetupBlockUnsafe`.

Il messaggio punterà al metodo che chiama `BlockLiteral.SetupBlock[Unsafe]`e può anche fornire indicazioni sul motivo per cui non è stato possibile ottimizzare la chiamata.

Inviare un [problema](https://github.com/xamarin/xamarin-macios/issues/new) insieme a un log di compilazione completo, in modo che sia possibile esaminare l'errore e possibilmente abilitare più scenari in futuro.

<a name="MM2107" />

#### <a name="mm2107-its-not-safe-to-remove-the-dynamic-registrar-because-reasons"></a>MM2107: non è sicuro rimuovere il registrar dinamico perché {reasons}

Il linker segnala questo avviso quando lo sviluppatore richiede la rimozione del registrar dinamico (passando `--optimize:remove-dynamic-registrar` a MMP), ma il linker determina che non è sicuro.

Per rimuovere l'avviso, rimuovere l'argomento di ottimizzazione in MMP oppure passare `--nowarn:2107` per ignorarlo.

Per impostazione predefinita, questa opzione viene abilitata automaticamente ogni volta che è possibile e sicura.

<a name="MM2108" />

#### <a name="mm2108-0-was-stripped-of-architectures-except-1-to-comply-with-app-store-restrictions-this-could-break-exisiting-codesigning-signatures-consider-stripping-the-library-with-lipo-or-disabling-with---optimize-trim-architectures"></a>MM2108:'{0}' è stato rimosso dalle architetture ad eccezione di '{1}' per rispettare le restrizioni dell'app Store. Questo potrebbe interrompere le firme di coprogettazione esistente. Provare a rimuovere la libreria con Lipo o disabilitarla con--Optimize =-Trim-Architectures ");

App Store rifiuta ora le applicazioni che contengono librerie e Framework che contengono varianti a 32 bit. La libreria è stata rimossa dalle architetture non usate quando è stata copiata nel bundle finale dell'applicazione.

Questa operazione è generalmente sicura e ridurrà le dimensioni del bundle dell'applicazione come vantaggio aggiuntivo. Tuttavia, qualsiasi Framework in bundle con firma codice avrà la firma invalidata (e verrà nuovamente firmata in un secondo momento se l'applicazione è firmata).

Si consiglia di usare `lipo` per rimuovere le architetture non necessarie in modo permanente dalla libreria di origine. Se l'applicazione non viene pubblicata nell'App Store, questa rimozione può essere disabilitata passando `--optimize=-trim-architectures` come argomenti MMP aggiuntivi.

<a name="MM2109"/>

#### <a name="mm2109-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2109: Novell. Mac API classica non supporta il collegamento alla piattaforma.

## <a name="mm3xxx-aot"></a>MM3xxx: AOT

### <a name="mm30xx-aot-general-errors"></a>MM30xx: errori AOT (generale)

<a name="MM3001" />

#### <a name="mm3001-could-not-aot-the-assembly-0"></a>MM3001: non è stato possibile AOT l'assembly '{0}'

<!-- 3002 used by mtouch -->
<!-- 3003 used by mtouch -->
<!-- 3004 used by mtouch -->
<!-- 3005 used by mtouch -->
<!-- 3006 used by mtouch -->
<!-- 3007 used by mtouch -->
<!-- 3008 used by mtouch -->

<a name="MM3009" />

#### <a name="mm3009-aot-of-0-was-requested-but-was-not-found"></a>MM3009: l'AOT di '{0}' è stato richiesto ma non è stato trovato

<a name="MM3010" />

#### <a name="mm3010-exclusion-of-aot-of-0-was-requested-but-was-not-found"></a>MM3010: è stato richiesto l'esclusione di AOT di '{0}' ma non è stato trovato

## <a name="mm4xxx-code-generation"></a>MM4xxx: generazione del codice

### <a name="mm40xx-driverm"></a>MM40xx: driver. m

<a name="MM4001" />

#### <a name="mm4001-the-main-template-could-not-be-expanded-to-0"></a>MM4001: non è stato possibile espandere il modello principale per `{0}`.

### <a name="mm41xx-registrar"></a>MM41xx: Registrar

<a name="MM4134" />

#### <a name="mm4134-your-application-is-using-the-0-framework-which-isnt-included-in-the-macos-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-osx-2-while-youre-building-with-the-macos-1-sdk-this-configuration-is-not-supported-with-the-static-registrar-pass---registrardynamic-as-an-additional-mmp-argument-in-your-projects-mac-build-option-to-select-alternatively-select-a-newer-sdk-in-your-apps-mac-build-options"></a>MM4134: l'applicazione usa il Framework '{0}', che non è incluso nell'SDK di MacOS usato per compilare l'app (questo Framework è stato introdotto in OSX {2}, mentre si sta creando con MacOS {1} SDK). Questa configurazione non è supportata con il registrar statico (Pass--Registrar: Dynamic come argomento aggiuntivo MMP nell'opzione di compilazione Mac del progetto da selezionare). In alternativa, selezionare un SDK più recente nelle opzioni di compilazione Mac dell'app.

<a name="MM4173" />

#### <a name="mm4173-the-registrar-cant-compute-the-block-signature-for-the-delegate-of-type-delegate-type-in-the-method-method-because-"></a>MM4173: il registrar non è in grado di calcolare la firma del blocco per il delegato di tipo {delegate-type} nel metodo {Method} perché *.

Si tratta di un avviso che indica che il registrar non è riuscito a inserire la firma del blocco del metodo specificato nel codice del registrar generato, perché il registrar non è stato in grado di elaborarlo.

Ciò significa che la firma del blocco deve essere calcolata in fase di esecuzione, che è piuttosto più lenta.

Attualmente esistono due possibili motivi per questo avviso:

1. Il tipo del delegato gestito è un `System.Delegate` o `System.MulticastDelegate`. Questi tipi non rappresentano una firma specifica, il che significa che il registrar non può calcolare la firma nativa corrispondente. In questo caso la correzione prevede l'uso di un tipo delegato specifico per il blocco (in alternativa, è possibile ignorare l'avviso aggiungendo `--nowarn:4173` come argomento MMP aggiuntivo nelle opzioni di compilazione Mac del progetto).
2. Il registrar non è in grado di trovare il metodo `Invoke` del delegato. Questa operazione non deve essere eseguita, quindi è necessario riportare un [problema](https://github.com/xamarin/xamarin-macios/issues/new) con un progetto di test in modo da poterlo correggere.

<a name="MT4174" />

#### <a name="mt4174-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-methods-parameter-parameter"></a>MT4174: Impossibile individuare il blocco da delegare al metodo di conversione per il parametro {method} del Metodo {Parameter}.

Si tratta di un avviso che indica che il registrar statico non è riuscito a trovare il metodo per creare un delegato per un blocco Objective-C. Verrà effettuato un tentativo in fase di esecuzione per trovare il metodo, ma probabilmente avrà esito negativo anche con un'eccezione MT8009.

Una delle possibili cause di questo avviso è la scrittura manuale di binding per un'API che utilizza blocchi. È consigliabile usare un progetto di binding per associare il codice Objective-C, in particolare quando si tratta di blocchi, perché è piuttosto complicato ottenerlo correttamente quando si esegue questa operazione manualmente.

In caso contrario, inviare un [problema](https://github.com/xamarin/xamarin-macios/issues/new) con un test case.

## <a name="mm5xxx-gcc-and-toolchain"></a>MM5xxx: GCC e

### <a name="mm51xx-compilation"></a>MM51xx: compilazione

<a name="MM5101" />

#### <a name="mm5101-missing-0-compiler-please-install-xcode-command-line-tools-component"></a>MM5101: manca il compilatore '{0}'. Installare il componente "strumenti da riga di comando" di Xcode.

<!-- 5102 used by mtouch -->

<a name="MM5103" />

#### <a name="mm5103-failed-to-compile-error-code---0-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MM5103: non è stato possibile compilare. Codice di errore-{0}. Inviare un report sui bug all'http://bugzilla.xamarin.com

<!-- 5104 used by mtouch -->

### <a name="mm52xx-linking"></a>MM52xx: collegamento

<a name="MM5202" />

#### <a name="mm5202-monoframework-mdk-is-missing-please-install-the-mdk-for-your-monoframework-version-from-httpmono-projectcomdownloads"></a>MM5202: mono. Framework MDK mancante. Installare MDK per la versione di mono. Framework da http://mono-project.com/Downloads

<a name="MM5203" />

#### <a name="mm5203-cant-find-libxammaca-likely-because-of-a-corrupted-xamarinmac-installation-please-reinstall-xamarinmac"></a>MM5203: Impossibile trovare libxammac. a, probabilmente a causa di un'installazione Novell. Mac danneggiata. Reinstallare Novell. Mac.

<a name="MM5204" />

#### <a name="mm5204-invalid-architecture-x86_64-is-only-supported-on-non-classic-profiles"></a>MM5204: architettura non valida. x86_64 è supportato solo nei profili non classici.

<a name="MM5205" />

#### <a name="mm5205-invalid-architecture-0-valid-architectures-are-i386-and-x86_64-when---profilemobile"></a>MM5205: l'architettura '{0}' non è valida. Le architetture valide sono i386 e x86_64 (When--profile = mobile).

<a name="MM5218" />

#### <a name="mm5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MM5218: non è possibile ignorare il simbolo dinamico {Symbol} (--Ignore-Dynamic-symbol = {symbol}) perché non è stato rilevato come simbolo dinamico.

Vedere l' [avviso mTouch equivalente](~/ios/troubleshooting/mtouch-errors.md#MT5218).

<!-- 5206 used by mtouch -->
<!-- 5207 used by mtouch -->
<!-- 5208 used by mtouch -->
<!-- 5209 used by mtouch -->
<!-- 5210 used by mtouch -->
<!-- 5211 used by mtouch -->
<!-- 5212 used by mtouch -->
<!-- 5213 used by mtouch -->
<!-- 5214 used by mtouch -->
<!-- 5215 used by mtouch -->
<!-- 5216 used by mtouch -->
<!-- 5217 used by mtouch -->

### <a name="mm53xx-other-tools"></a>MM53xx: altri strumenti

<a name="MM5301" />

#### <a name="mm5301-pkg-config-could-not-be-found-please-install-the-monoframework-from-httpmono-projectcomdownloads"></a>MM5301: pkg-config non è stato trovato. Installare Mono. Framework da http://mono-project.com/Downloads

<!-- 5302 used by mtouch -->
<!-- 5303 used by mtouch -->
<!-- 5304 used by mtouch -->

<a name="MM5305" />

#### <a name="mm5305-missing-otool-tool-please-install-xcode-command-line-tools-component"></a>MM5305: manca lo strumento ' otool '. Installare il componente "strumenti da riga di comando" di Xcode

<a name="MM5306" />

#### <a name="mm5306-missing-dependencies-please-install-xcode-command-line-tools-component"></a>MM5306: dipendenze mancanti. Installare il componente "strumenti da riga di comando" di Xcode

<a name="MM5308" />

#### <a name="mm5308-xcode-license-agreement-may-not-have-been-accepted--please-launch-xcode"></a>MM5308: il contratto di licenza di Xcode potrebbe non essere stato accettato.  Avviare Xcode.

<a name="MM5309" />

#### <a name="mm5309-native-linking-failed-with-error-code-1-check-build-log-for-details"></a>MM5309: il collegamento nativo non è riuscito con codice di errore 1. Per informazioni dettagliate, controllare il log di compilazione.

<a name="MM5310" />

#### <a name="mm5310-install_name_tool-failed-with-an-error-code-0-check-build-log-for-details"></a>MM5310: install_name_tool non riuscita con codice di errore '{0}'. Per informazioni dettagliate, controllare il log di compilazione.

<a name="MM5311" />

#### <a name="mm5311-lipo-failed-with-an-error-code-0-check-build-log-for-details"></a>MM5311: Lipo non riuscito con codice di errore '{0}'. Per informazioni dettagliate, controllare il log di compilazione.

<!-- MM6xxx: mmp internal tools -->
<!-- MM7xxx: reserved -->

## <a name="mm8xxx-runtime"></a>MM8xxx: Runtime

### <a name="mm800x-misc"></a>MM800x: varie

<!-- 8000 used by mtouch -->
<!-- 8001 used by mtouch -->
<!-- 8002 used by mtouch -->
<!-- 8003 used by mtouch -->
<!-- 8004 used by mtouch -->
<!-- 8005 used by mtouch -->
<!-- 8006 used by mtouch -->
<!-- 8007 used by mtouch -->
<!-- 8008 used by mtouch -->
<!-- 8009 used by mtouch -->
<!-- 8010 used by mtouch -->
<!-- 8011 used by mtouch -->
<!-- 8012 used by mtouch -->
<!-- 8013 used by mtouch -->
<!-- 8014 used by mtouch -->
<!-- 8015 used by mtouch -->
<!-- 8016 used by mtouch -->

<a name="MM8017" />

#### <a name="mm8017-the-boehm-garbage-collector-is-not-supported-please-use-sgen-instead"></a>MM8017: il Garbage Collector Boehm non è supportato. Usare SGen in alternativa.

<a name="MM8025" />

#### <a name="mm8025-failed-to-compute-the-token-reference-for-the-type-typeassemblyqualifiedname-because-reasons"></a>MM8025: non è stato possibile calcolare il riferimento al token per il tipo ' {Type. AssemblyQualifiedName}' perché {reasons}

Ciò indica un bug in Novell. Mac. Inviare un bug in [https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac).

Una possibile soluzione alternativa consiste nel disabilitare l'ottimizzazione `register-protocols` passando `--optimize:-register-protocols` come argomento MMP aggiuntivo nelle opzioni di compilazione Mac del progetto.

<a name="MM8026" />

#### <a name="mm8026--is-not-supported-when-the-dynamic-registrar-has-been-linked-away"></a>MM8026: * non è supportato quando il registrar dinamico è stato collegato.

Questo indica in genere un bug in Novell. Mac, perché il registrar dinamico non deve essere collegato se necessario. Inviare un bug in [https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

È possibile forzare il linker a lasciare il registrar dinamico aggiungendo `--optimize=-remove-dynamic-registrar` agli argomenti MMP aggiuntivi nelle opzioni di compilazione Mac del progetto.
