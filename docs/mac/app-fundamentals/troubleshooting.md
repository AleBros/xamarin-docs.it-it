---
title: Segnalazione dei bug
description: Questo documento descrive gli approcci per la risoluzione dei bug.
ms.topic: article
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 9b0d757c951f9244beb093a0a9b13ac1d069b507
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="reporting-bugs"></a>Segnalazione dei bug

## <a name="overview"></a>Panoramica

In alcuni casi si tutti spesso bloccati mentre si lavora in un progetto, l'impossibilità di ottenere un'API per funzionare nel modo desiderato o tentativo di risolvere un bug. È stata fornita alcune risorse necessarie per l'obiettivo di Xamarin è per poter essere eseguita correttamente in scrittura di applicazioni per dispositivi mobili e desktop.

Con uno qualsiasi di queste risorse, esistono alcuni passaggi di preparazione da eseguire per risolvere il problema in tempi rapidi:

- Determinare la causa principale del problema come migliore possibile per segnalare gli errori:
 
     - "L'applicazione si blocca" è difficile da diagnosticare. "L'applicazione si blocca quando si restituisce una matrice vuota di questa chiamata" è molto più semplice utilizzare la correzione.

     - "Non è possibile ottenere NSTable funzionamento" è meno utile rispetto a "Nessuno dei metodi in my NSTableDelegate sembra essere chiamato in questo caso".

- Se possibile, fornire un programma di esempio di piccole dimensioni che mostra il problema. Approfondire tramite le pagine del codice sorgente per il problema viene ordini di grandezza più tempo e fatica.

- Conoscere le modifiche apportate all'applicazione che causano un problema di possono limitare rapidamente verso il basso l'origine del problema. Notare se è stata aggiornata recentemente Xamarin.Mac, troncando sezioni dell'applicazione per trovare la parte che causa il problema, le versioni o test precedente compilazioni per trovare la modifica di stato introdotto il problema può essere molto utile.


### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>Operazioni da eseguire quando l'app si blocca e non produce output

Nella maggior parte dei casi, il debugger in Visual Studio per Mac rileverà le eccezioni e arresti anomali dell'applicazione e consentono di individuare la causa radice. Esistono tuttavia alcuni casi in cui l'applicazione verrà bounce su ancoraggio e quindi chiudere con un output nullo o nessuna. Questi possono includere:

- Problemi di firma del codice.
- Determinati runtime mono arresti anomali.
- Alcune eccezioni Objective-c e arresti anomali del sistema.
- Alcuni arresti anomali all'inizio la durata del processo.
- Alcuni stack overflow.
- La versione di macOS elencata nella **Info. plist** è più recente rispetto alla versione attualmente installata macOS o non è valido.

Debug di questi programmi possa essere frustrante, come ricerca possono essere difficile le informazioni necessarie. Ecco alcuni approcci che possono essere utili:

- Verificare che la versione di macOS elencata nel **Info. plist** corrisponde a quella della versione di macOS attualmente installati nel computer.
- Verificare di Visual Studio per l'Output dell'applicazione Mac (**vista** -> **Pad** -> **Output dell'applicazione**) per elencare le tracce o di output in colore rosso da Cocoa che indicano l'output.
- Eseguire l'applicazione dalla riga di comando ed esaminare l'output (nel **Terminal** app) tramite: 

     `MyApp.app/Contents/MacOS/MyApp` (dove `MyApp` è il nome dell'applicazione)
- È possibile aumentare l'output aggiungendo "MONO_LOG_LEVEL" al comando nella riga di comando, ad esempio: 

     `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- È possibile allegare un debugger nativo (`lldb`) per il processo per vedere se che fornisce altre informazioni (è necessaria una licenza a pagamento). Ad esempio, eseguire le operazioni seguenti:

    1. Immettere `lldb MyApp.app/Contents/MacOS/MyApp` nei servizi Terminal.
    2. Immettere `run` nei servizi Terminal.
    3. Immettere `c` nei servizi Terminal.
    4. Uscire da quando è terminato il debug.
- Come ultima risorsa, prima di chiamare `NSApplication.Init` nel `Main` metodo (o in altre posizioni in base alle esigenze), è possibile scrivere testo in un file in un percorso noto per tenere traccia in quale fase di avvio si eseguono dei problemi.

## <a name="known-issues"></a>Problemi noti

Le sezioni seguenti illustrano i problemi noti e le relative soluzioni.

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>Impossibile connettersi al debugger di App create mediante sandbox

Il debugger si connette alle App Xamarin.Mac tramite TCP, il che significa che per impostazione predefinita quando si abilita il sandboxing, non è in grado di connettersi all'app, pertanto se si tenta di eseguire l'app senza le autorizzazioni appropriate abilitate, viene visualizzato un errore *"Impossibile connettersi a il debugger"*. 

[![Modifica i diritti](troubleshooting-images/debug01.png "i diritti di modifica")](troubleshooting-images/debug01-large.png#lightbox)

Il **consentire connessioni in uscita rete (Client)** autorizzazione è necessaria per il debugger, l'abilitazione di questo consente il debug normalmente. Poiché non è possibile eseguire il debug senza di esso, è stata aggiornata la `CompileEntitlements` di destinazione per `msbuild` per aggiungere automaticamente l'autorizzazione per i diritti per qualsiasi applicazione in cui è in modalità sandbox per il debug solo le compilazioni. Build di rilascio devono usare i diritti specificati nel file dei diritti, senza modificato.

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System. NotSupportedException: non sono disponibili dati per la codifica 437
 
Quando nell'app Xamarin.Mac, incluse le librerie di terze parti 3rd, potrebbe essere visualizzato un errore nel modulo "System. NotSupportedException: dati non sono disponibili per la codifica 437" durante il tentativo di compilare ed eseguire l'app. Ad esempio, le librerie, ad esempio `Ionic.Zip.ZipFile`, può generare questa eccezione durante l'operazione.

Questo può essere risolto, aprire le opzioni per il progetto Xamarin.Mac, verrà **Mac compilare** > **internazionalizzazione** e controllando il **occidentale** internazionalizzazione:

[![Modifica delle opzioni di compilazione](troubleshooting-images/issue01.png "Modifica delle opzioni di compilazione")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>Errori di compilazione (mm5103)

Questo errore è dovuto in genere quando una nuova versione di Xcode versione viene installata la nuova versione, ma non eseguirla. Prima di tentare di eseguire la compilazione con una nuova versione di Xcode, è necessario innanzitutto eseguire almeno una volta che la versione.

Alla prima esecuzione di una nuova versione di Xcode, installarlo diversi strumenti da riga di comando richiesti da Xamarin.Mac. Inoltre, dopo l'aggiornamento di Xcode o con la versione Xamarin.Mac sarà necessario eseguire una compilazione pulita.

Se è possibile risolvere questo problema, [segnalare un bug](#filing-a-bug).

### <a name="missing-entitlementsplist"></a>Entitlements.plist mancante

La versione più recente di Visual Studio per Mac ha rimosso la sezione dei diritti di **Info. plist** editor ed è stato inserito in separata **Entitlements.plist** editor (per un migliore supporto multipiattaforma con xamarin).

Con il nuovo Visual Studio per Mac è stato installato, quando si crea un nuovo progetto di app Xamarin.Mac, un **Entitlements.plist** file verrà automaticamente aggiunto alla struttura di progetto:

![Selezione dei diritti](troubleshooting-images/entitlements01.png "selezionando i diritti")

Se si fa doppio clic il **Entitlements.plist** verrà visualizzati i file, l'Editor dei diritti:

[![Modifica i diritti](troubleshooting-images/entitlements02.png "i diritti di modifica")](troubleshooting-images/entitlements02-large.png#lightbox)

Per i progetti Xamarin.Mac esistenti, è necessario creare manualmente il **Entitlements.plist** file facendo clic sul progetto nel **soluzione riempimento** e selezionando **Aggiungi**  >  **Nuovo File...** . Selezionare quindi **Xamarin.Mac** > **elenco proprietà vuoto**:

![Aggiunta di un nuovo elenco di proprietà](troubleshooting-images/entitlements03.png "aggiunta di un nuovo elenco di proprietà")

Immettere `Entitlements` per il nome e fare clic su di **New** pulsante. Se il progetto è incluso in precedenza un file dei diritti, verrà richiesto di aggiungerlo al progetto anziché creare un nuovo file:

[![Verifica per determinare se la sovrascrittura di un file](troubleshooting-images/entitlements04.png "verifica per determinare se la sovrascrittura di un file")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="contacting-support-business-or-enterprise-licenses"></a>Contattare il supporto tecnico (licenze aziendale)

Se si dispone di un'azienda o una licenza enterprise, si è idonei per richiedere assistenza direttamente dai tecnici Xamarin mediante i ticket di supporto. Vedere [xamarin.com/support](http://xamarin.com/support) per informazioni dettagliate.

## <a name="community-support-on-the-forums"></a>Supporto della community nei forum

La community degli sviluppatori che utilizzano i prodotti di Xamarin è straordinaria e molti visitare il nostro [Xamarin.Mac forum](http://forums.xamarin.com/categories/mac) per condividere esperienze e le competenze. Inoltre, i tecnici Xamarin periodicamente visitare il forum per.

<a name="filing-a-bug"/>

## <a name="filing-a-bug"></a>Creazione di un bug

Commenti e suggerimenti sono importanti per Microsoft. Se i problemi di Xamarin.Mac:

- Cercare nel [repository di problemi](https://github.com/xamarin/xamarin-macios/issues) 
- Prima di passare ai problemi di GitHub, si è tenuta traccia dei problemi di Xamarin in [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi), dove è possibile cercare i problemi corrispondenti.
- Se non si riesce a trovare un problema corrispondente, inserirne uno nuovo nel [repository di problemi GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

I problemi di GitHub sono tutti pubblici. Non è possibile nascondere commenti o allegati. 

Includere tutte le informazioni disponibili seguenti:                                                                                                                                          

- Un esempio semplice che riproduce il problema. Questo è **molto importante**, ove possibile. 
- L'analisi dello stack completa dell'arresto anomalo.
- Il codice C# relativo all'arresto anomalo. 
