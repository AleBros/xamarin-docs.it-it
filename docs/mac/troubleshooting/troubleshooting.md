---
title: Suggerimenti sulla risoluzione dei problemi di xamarin. Mac
description: Questo documento descrive gli approcci per la risoluzione dei problemi riscontrati durante lo sviluppo di applicazioni xamarin. Mac. Illustra anche modi per ottenere supporto.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: f498aab5bfaffc08a22f62a318f8f9f73ab0afca
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61237497"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Suggerimenti sulla risoluzione dei problemi di xamarin. Mac

## <a name="overview"></a>Panoramica

In alcuni casi è creato un blocco durante l'uso di un progetto, l'impossibilità di ottenere un'API da usare nel modo desiderato o in sta tentando di risolvere un bug. Sono disponibili alcune risorse utili per il nostro obiettivo in Xamarin è appositamente per la scrittura di applicazioni per dispositivi mobili e desktop in modo corretto.

Con una di queste risorse, esistono alcuni passaggi di preparazione da eseguire per aiutarlo a risolvere rapidamente il problema:

- Determinare la causa radice del problema come migliore possibile per segnalare gli errori:
 
     - "Blocchi applicazione" è difficile da diagnosticare. "L'applicazione si blocca quando si restituisce una matrice vuota di questa chiamata" è molto più semplice utilizzare la correzione.

     - "Non è possibile ottenere NSTable funzionamento" è meno utile rispetto a "Nessuno dei metodi nella mio NSTableDelegate sembrano essere chiamato in questo caso".

- Se possibile fornire un programma di esempio di piccole dimensioni che mostra il problema. Esplorare il funzionamento tramite le pagine del codice sorgente per il problema accetta gli ordini di grandezza più tempo e impegno.

- Sapere cosa le modifiche apportate all'applicazione che causano un problema venga visualizzato possano restringere l'elenco di origine del problema. Notare se è stato aggiornato di recente le versioni di xamarin. Mac, trimming sezioni dell'applicazione per trovare la parte che ha causato il problema, o il test precedente si basa per trovare le modifiche introdotte il problema può essere molto utile.


### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>Operazioni da eseguire quando l'app si blocca senza output

Nella maggior parte dei casi, il debugger di Visual Studio per Mac rileverà le eccezioni e arresti anomali dell'applicazione e consentono di verificare la causa principale. Tuttavia, esistono alcuni casi in cui l'applicazione verrà bounce nel dock e quindi uscire con poco o nessun output. Questi possono includere:

- Problemi di firma del codice.
- Alcuni arresti anomali di runtime di mono.
- Alcune eccezioni Objective-c e arresti anomali del sistema.
- Alcuni arresti anomali molto presto la durata del processo.
- Alcuni stack overflow.
- La versione di macOS riportata nelle **Info. plist** è più recente rispetto alla versione attualmente installata di macOS oppure non è valido.

Debug di questi programmi può essere frustrante, come trovare le informazioni necessarie possono essere difficile. Ecco alcuni approcci che possono essere utili:

- Verificare che la versione di macOS elencata nella **Info. plist** corrisponde a quello della versione di macOS attualmente installati nel computer.
- Verificare di Visual Studio per l'Output dell'applicazione Mac (**View** -> **riquadri** -> **Output applicazione**) per analisi dello stack o di output in rosso da Cocoa che indicano l'output.
- Eseguire l'applicazione dalla riga di comando ed esaminare l'output (nelle **Terminal** app) usando: 

     `MyApp.app/Contents/MacOS/MyApp` (dove `MyApp` è il nome dell'applicazione)
- È possibile aumentare l'output aggiungendo "MONO_LOG_LEVEL" al comando nella riga di comando, ad esempio: 

     `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- È possibile allegare un debugger nativo (`lldb`) per il processo per vedere se che fornisce altre informazioni (richiede una licenza a pagamento). Ad esempio, eseguire le operazioni seguenti:

    1. Immettere `lldb MyApp.app/Contents/MacOS/MyApp` nel terminale.
    2. Immettere `run` nel terminale.
    3. Immettere `c` nel terminale.
    4. Viene chiuso quando è terminato il debug.
- Come ultima risorsa, prima di chiamare `NSApplication.Init` nel `Main` metodo (o in altre posizioni in base alle esigenze), è possibile scrivere testo in un file in una posizione nota per tenere traccia in quale passaggio del lancio si verificano problemi.

## <a name="known-issues"></a>Problemi noti

Le sezioni seguenti illustrano i problemi noti e le relative soluzioni.

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>Impossibile connettersi al debugger in modalità sandbox App

Il debugger si connette all'App xamarin. Mac tramite TCP, il che significa che per impostazione predefinita quando si abilita il sandboxing, non è in grado di connettersi all'app, in modo che se si prova a eseguire l'app senza le dovute autorizzazioni abilitate, viene visualizzato un errore *"Unable to connect to il debugger"*. 

[![Modifica degli Entitlement](troubleshooting-images/debug01.png "modifica degli Entitlement")](troubleshooting-images/debug01-large.png#lightbox)

Il **Consenti rete le connessioni in uscita (Client)** autorizzazione è necessaria affinché il debugger, l'abilitazione di questa una consentirà debug normalmente. Poiché è possibile eseguire il debug senza di essa, abbiamo aggiornato il `CompileEntitlements` di destinazione per `msbuild` per aggiungere automaticamente tale autorizzazione per gli Entitlement per qualsiasi app che è in modalità sandbox per il debug solo le compilazioni. Le build di versione devono usare i diritti specificati nel file dei diritti, senza modificato.

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System. NotSupportedException: non sono disponibili dati per la codifica 437
 
Quando si include librerie di terze parti 3rd nell'app xamarin. Mac, è possibile ottenere un errore nel modulo "System. NotSupportedException: Non sono disponibili dati per la codifica 437" quando si tenta di compilare ed eseguire l'app. Ad esempio, le librerie, ad esempio `Ionic.Zip.ZipFile`, possono generare questa eccezione durante l'operazione.

Questo problema può essere risolto, aprire le opzioni per il progetto xamarin. Mac, passare a **compilazione Mac** > **internazionalizzazione** e controllando il **occidentale** internazionalizzazione:

[![Modifica delle opzioni di compilazione](troubleshooting-images/issue01.png "Modifica delle opzioni di compilazione")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>Errori di compilazione (mm5103)

Questo errore è causato in genere quando una nuova versione di Xcode è release e avere installato la nuova versione, ma non ancora eseguirlo. Prima di provare a eseguire la compilazione con una nuova versione di Xcode, è necessario prima eseguire almeno una volta che la versione.

La prima volta che si esegue una nuova versione di Xcode, lo installa numerosi strumenti da riga di comando richiesti da xamarin. Mac. Inoltre, è consigliabile eseguire una compilazione pulita dopo l'aggiornamento di Xcode o con la versione di xamarin. Mac.

Se non è possibile risolvere questo problema, please [segnalare un bug](#filing-a-bug).

### <a name="missing-entitlementsplist"></a>Entitlements. plist mancante

La versione più recente di Visual Studio per Mac ha rimosso la sezione dei diritti dal **Info. plist** editor ed è stato inserito in separato **entitlements. plist** editor (per un migliore supporto multipiattaforma con xamarin. IOS).

Con il nuovo Visual Studio per Mac è installato, quando si crea un nuovo progetto di app xamarin. Mac, un **entitlements. plist** file verrà automaticamente aggiunto all'albero del progetto:

![Selezionare gli Entitlement](troubleshooting-images/entitlements01.png "selezionare gli Entitlement")

Se si fa doppio clic il **entitlements. plist** verrà visualizzati file, l'Editor dei diritti:

[![Modifica degli Entitlement](troubleshooting-images/entitlements02.png "modifica degli Entitlement")](troubleshooting-images/entitlements02-large.png#lightbox)

Per i progetti xamarin. Mac esistenti, è necessario creare manualmente il **entitlements. plist** file facendo clic sul progetto nel **riquadro della soluzione** e scegliendo **Add**  >  **Nuovo File...** . Successivamente, selezionare **xamarin. Mac** > **elenco proprietà vuoto**:

![Aggiunta di un nuovo elenco di proprietà](troubleshooting-images/entitlements03.png "aggiungendo un nuovo elenco di proprietà")

Immettere `Entitlements` per il nome, quindi scegliere il **New** pulsante. Se il progetto incluso in precedenza un file dei diritti, verrà richiesto di aggiungerlo al progetto invece di creare un nuovo file:

[![Verifica per determinare se la sovrascrittura di un file](troubleshooting-images/entitlements04.png "verifica per determinare se la sovrascrittura di un file")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="community-support-on-the-forums"></a>Supporto della community nei forum

La community degli sviluppatori che utilizzano prodotti Xamarin è meraviglioso, visitare molti nostri [forum di xamarin. Mac](http://forums.xamarin.com/categories/mac) per condividere esperienze e le proprie competenze. Inoltre, i tecnici di Xamarin visitare periodicamente il forum per.

<a name="filing-a-bug"/>

## <a name="filing-a-bug"></a>Invio di un bug

I tuoi commenti sono importanti per noi. Se individuare i problemi con xamarin. Mac:

- Cercare nel [repository di problemi](https://github.com/xamarin/xamarin-macios/issues) 
- Prima di passare ai problemi di GitHub, si è tenuta traccia dei problemi di Xamarin in [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi), dove è possibile cercare i problemi corrispondenti.
- Se non si riesce a trovare un problema corrispondente, inserirne uno nuovo nel [repository di problemi GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

I problemi di GitHub sono tutti pubblici. Non è possibile nascondere commenti o allegati. 

Includere tutte le informazioni disponibili seguenti:                                                                                                                                          

- Un esempio semplice che riproduce il problema. Questo è **molto importante**, ove possibile. 
- L'analisi dello stack completa dell'arresto anomalo.
- Il codice C# relativo all'arresto anomalo. 
