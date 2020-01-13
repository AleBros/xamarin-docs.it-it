---
title: Suggerimenti per la risoluzione dei problemi di Xamarin.Mac
description: Questo documento descrive gli approcci per la risoluzione dei problemi riscontrati durante lo sviluppo di applicazioni Xamarin.Mac. Vengono inoltre illustrati i modi per ottenere supporto.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 7bb17abf8cdb943780bb3939aae8e461925b6517
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73001602"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi di Xamarin.Mac

## <a name="overview"></a>Panoramica

A volte si rimane bloccati quando si lavora a un progetto, in quanto non è possibile ottenere un'API per lavorare nel modo desiderato o nel tentativo di aggirare un bug. L'obiettivo di Novell è quello di poter scrivere le applicazioni per dispositivi mobili e desktop e sono state fornite alcune risorse per aiutare.

Con una qualsiasi di queste risorse, è possibile eseguire alcune operazioni di preparazione che consentono di risolvere rapidamente il problema:

- Determinare la causa principale del problema nel modo più appropriato possibile per segnalare arresti anomali:

  - "Arresto anomalo dell'applicazione" è difficile da diagnosticare. "L'applicazione si arresta in modo anomalo quando restituisco una matrice vuota a questa chiamata" è molto più semplice da risolvere.

  - "Non è possibile ottenere il NSTable di lavoro" è meno utile di "nessuno dei metodi in NSTableDelegate sembra essere chiamato in questo caso".

- Se possibile, fornire un piccolo programma di esempio che mostra il problema. L'analisi delle pagine del codice sorgente che cercano il problema richiede più tempo e lavoro.

- Conoscere le modifiche apportate all'applicazione per fare in modo che venga visualizzato un problema può limitare rapidamente l'origine del problema. Se sono state aggiornate di recente versioni di Xamarin.Mac, è possibile ritagliare le sezioni dell'applicazione per trovare la parte che causa il problema oppure testare le compilazioni precedenti per trovare la modifica introdotta. il problema può essere molto utile.

### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>Cosa fare quando l'app si arresta in modo anomalo senza output

Nella maggior parte dei casi, il debugger in Visual Studio per Mac catturerà eccezioni e arresti anomali dell'applicazione e consentirà di individuare la causa principale. Tuttavia, esistono alcuni casi in cui l'applicazione verrà rimbalzata sul Dock e verrà chiusa con un output minimo o nullo. Questi possono includere:

- Problemi di firma del codice.
- Alcuni arresti anomali del runtime di mono.
- Alcune eccezioni Objective-c e arresti anomali.
- Alcuni arresti anomali molto presto durano la durata del processo.
- Overflow dello stack.
- La versione macOS elencata nel file **info. plist** è più recente della versione MacOS attualmente installata oppure non è valida.

Il debug di questi programmi può essere frustrante, in quanto la ricerca delle informazioni necessarie può essere difficile. Ecco alcuni approcci che possono essere utili:

- Verificare che la versione macOS elencata nel file **info. plist** sia la stessa della versione di MacOS attualmente installata nel computer.
- Controllare l'output dell'applicazione Visual Studio per Mac (**visualizzare** -> **rilievi** -> **output dell'applicazione**) per le analisi dello stack o l'output in rosso da Cocoa che può descrivere l'output.
- Eseguire l'applicazione dalla riga di comando ed esaminare l'output (nell'app **Terminal** ) usando:

  `MyApp.app/Contents/MacOS/MyApp` (dove `MyApp` è il nome dell'applicazione)
- È possibile aumentare l'output aggiungendo "MONO_LOG_LEVEL" al comando nella riga di comando, ad esempio:

  `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- È possibile alleghi un debugger nativo (`lldb`) al processo per verificare se sono disponibili altre informazioni, ovvero è necessaria una licenza a pagamento. Ad esempio, eseguire le operazioni seguenti:

  1. Immettere `lldb MyApp.app/Contents/MacOS/MyApp` nel terminale.
  2. Immettere `run` nel terminale.
  3. Immettere `c` nel terminale.
  4. Termina al termine del debug.
- Come ultima risorsa, prima di chiamare `NSApplication.Init` nel metodo di `Main` (o in altre posizioni come richiesto), è possibile scrivere testo in un file in una posizione nota per individuare il passaggio del lancio in cui si verificano problemi.

## <a name="known-issues"></a>Problemi noti

Le sezioni seguenti riguardano i problemi noti e le relative soluzioni.

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>Non è possibile connettersi al debugger nelle app in modalità sandbox

Il debugger si connette alle app Xamarin.Mac tramite TCP. il che significa che, per impostazione predefinita, quando si Abilita il sandboxing, non è in grado di connettersi all'app, pertanto se si tenta di eseguire l'app senza le autorizzazioni corrette abilitate, viene ricevuto un errore *"Impossibile connettersi al debugger"* .

[![Modifica dei diritti](troubleshooting-images/debug01.png "Modifica dei diritti")](troubleshooting-images/debug01-large.png#lightbox)

L'autorizzazione **Consenti connessioni di rete (client) in uscita** è quella necessaria per il debugger. questa operazione consente di eseguire il debug in modo normale. Poiché non è possibile eseguire il debug senza di esso, è stata aggiornata la destinazione `CompileEntitlements` per `msbuild` per aggiungere automaticamente tale autorizzazione ai diritti per qualsiasi app creata in modalità sandbox solo per le compilazioni di debug. Le build di rilascio devono usare i diritti specificati nel file dei diritti, senza modifiche.

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System. NotSupportedException: non sono disponibili dati per la codifica 437

Quando si includono librerie di terze parti nell'app Xamarin.Mac, è possibile che venga ricevuto un errore nel formato "System. NotSupportedException: non sono disponibili dati per la codifica 437" durante il tentativo di compilare ed eseguire l'app. Ad esempio, le librerie, ad esempio `Ionic.Zip.ZipFile`, possono generare questa eccezione durante l'operazione.

Questo problema può essere risolto aprendo le opzioni per il progetto Xamarin.Mac, passando a **Mac Build** > **internazionalizzazione** e controllando l'internazionalizzazione **occidentale** :

[![Modifica delle opzioni di compilazione](troubleshooting-images/issue01.png "Modifica delle opzioni di compilazione")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>Compilazione non riuscita (mm5103)

Questo errore si verifica in genere quando viene rilasciata una nuova versione di Xcode ed è stata installata la nuova versione ma non è stata ancora eseguita. Prima di provare a compilare con una nuova versione di Xcode, è necessario prima eseguire tale versione almeno una volta.

La prima volta che si esegue una nuova versione di Xcode, vengono installati diversi strumenti della riga di comando richiesti da Xamarin.Mac. Inoltre, è consigliabile eseguire una compilazione pulita dopo l'aggiornamento di Xcode o della versione di Xamarin.Mac.

Se non è possibile risolvere il problema, inviare [un bug](#filing-a-bug).

### <a name="missing-entitlementsplist"></a>Mancano i diritti. plist

La versione più recente di Visual Studio per Mac ha rimosso la sezione dei diritti dall'editor **info. plist** e la ha inserita in un editor di **diritti distinti. plist** (per un migliore supporto multipiattaforma con Xamarin.iOS).

Con la nuova Visual Studio per Mac installata, quando si crea un nuovo progetto di app Xamarin.Mac, un file con **estensione plist dei diritti** verrà automaticamente aggiunto all'albero del progetto:

![Selezione dei diritti](troubleshooting-images/entitlements01.png "Selezione dei diritti")

Se si fa doppio clic sul file **titles. plist** , verrà visualizzato l'editor dei diritti:

[![Modifica dei diritti](troubleshooting-images/entitlements02.png "Modifica dei diritti")](troubleshooting-images/entitlements02-large.png#lightbox)

Per i progetti Xamarin.Mac esistenti, è necessario creare manualmente il file con **estensione plist dei diritti** facendo clic con il pulsante destro del mouse sul progetto nella **riquadro della soluzione** e scegliendo **Aggiungi** > **nuovo file.** Selezionare quindi **Xamarin.Mac** > **elenco di proprietà vuoto**:

![Aggiunta di un nuovo elenco di proprietà](troubleshooting-images/entitlements03.png "Aggiunta di un nuovo elenco di proprietà")

Immettere `Entitlements` per il nome e fare clic sul pulsante **nuovo** . Se il progetto includeva in precedenza un file dei diritti, verrà richiesto di aggiungerlo al progetto anziché creare un nuovo file:

[![Verifica della sovrascrittura di un file](troubleshooting-images/entitlements04.png "Verifica della sovrascrittura di un file")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="community-support-on-the-forums"></a>Supporto della community nei forum

La community di sviluppatori che usano i prodotti Novell è straordinaria e molti visitano i nostri [forum Xamarin.Mac](https://forums.xamarin.com/categories/mac) per condividere esperienze e la loro esperienza. Inoltre, i tecnici Novell visitano periodicamente il forum per assistenza.

<a name="filing-a-bug"/>

## <a name="filing-a-bug"></a>Archiviazione di un bug

Il feedback è importante per noi. Se si riscontrano problemi con Xamarin.Mac:

- Cercare nel [repository di problemi](https://github.com/xamarin/xamarin-macios/issues)
- Prima di passare ai problemi di GitHub, si è tenuta traccia dei problemi di Xamarin in [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi), dove è possibile cercare i problemi corrispondenti.
- Se non si riesce a trovare un problema corrispondente, inserirne uno nuovo nel [repository di problemi GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

I problemi di GitHub sono tutti pubblici. Non è possibile nascondere commenti o allegati.

Includere tutte le informazioni disponibili seguenti:

- Un esempio semplice che riproduce il problema. Questo è **molto importante**, ove possibile.
- L'analisi dello stack completa dell'arresto anomalo.
- Il codice C# relativo all'arresto anomalo.
