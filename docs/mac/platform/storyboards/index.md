---
title: Introduzione agli storyboard
description: In questo articolo viene fornita un'introduzione all'utilizzo in un'app Xamarin.Mac storyboard. Viene descritto come creare e gestire l'interfaccia utente dell'app usando gli storyboard e Interface Builder di Xcode.
ms.topic: article
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: ccee60b5d953987e858ef592d005cec9803b8b96
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="introduction-to-storyboards"></a>Introduzione agli storyboard

_In questo articolo viene fornita un'introduzione all'utilizzo in un'app Xamarin.Mac storyboard. Vengono illustrate la creazione e la gestione dell'interfaccia utente dell'applicazione mediante storyboard e generatore del Xcode di interfaccia._

Gli storyboard consentono di sviluppare un'interfaccia utente per l'app Xamarin.Mac che include non solo i controlli e le definizioni di finestra, ma contiene inoltre i collegamenti tra finestre diverse (tramite segues) e gli stati di visualizzazione.

[![](images/intro01.png "Un esempio dell'interfaccia utente in Xcode")](images/intro01.png#lightbox)

In questo articolo verrà forniscono un'introduzione all'utilizzo di storyboard per definire l'interfaccia utente dell'applicazione un Xamarin.Mac.

<a name="What-are-Storyboards" />

## <a name="what-are-storyboards"></a>Quali sono gli storyboard?

Con storyboard, tutti di interfaccia utente dell'applicazione un Xamarin.Mac possono essere definiti in un'unica posizione con tutta la navigazione tra i singoli elementi e delle interfacce utente. Storyboard per Xamarin.Mac, funzionano in modo molto simile agli storyboard per xamarin. IOS. Tuttavia, contengono un set diverso di _definire i tipi_ a causa di idiomi interfaccia diversa.

<a name="Working-with-Scenes" />

### <a name="working-with-scenes"></a>Utilizzo di scene

Come descritto in precedenza, uno Storyboard definisce l'interfaccia utente per un'applicazione specificata suddivisa in una panoramica delle funzionalità del relativo _visualizzazione controller_. In Generatore di interfaccia di Xcode, ognuno di questi controller si trova nella propria _scena_.

[![](images/intro02.png "Un controller di visualizzazione di esempio")](images/intro02.png#lightbox)

Ogni scena rappresenta un oggetto vista e visualizzazione Controller coppia con un set di righe (denominato Segues) che si connettono ogni scena nell'interfaccia utente, mostrando in tal modo le relative relazioni. Alcuni Segues definire come una visualizzazione Controller contiene uno o più visualizzazioni figlio o controller di visualizzazione. Altri Segues, definiscono le transizioni tra Controller di visualizzazione (ad esempio visualizzazione della finestra di dialogo o popover). 

[![](images/intro03.png "Segue un esempio")](images/intro03.png#lightbox)

La cosa più importante da notare è che ogni Segue rappresenta il flusso di un tipo di dati tra l'elemento dell'interfaccia utente dell'applicazione specificato.

<a name="Working-with-View-Controllers" />

### <a name="working-with-view-controllers"></a>Utilizzo di controller di visualizzazione

Visualizzazione controller definiscono le relazioni tra una determinata visualizzazione di informazioni all'interno di un'app Mac e il modello di dati che fornisce le informazioni. Ogni scena di primo livello dello storyboard rappresenta un Controller di visualizzazione nel codice dell'app Xamarin.Mac.

[![](images/intro04.png "Un esempio di visualizzazione controller vengono ignorati")](images/intro04.png#lightbox)

In questo modo, ogni Controller di visualizzazione è un'associazione indipendente e riutilizzabile di rappresentazione visiva dell'informazione (visualizzazione) e la logica per consentire di controllare le informazioni.

All'interno di una determinata scena, è possibile effettuare le operazioni che verrebbero normalmente sono stati gestiti da singoli `.xib` file: 

 - Controlli (ad esempio i pulsanti e caselle di testo) e subviews sul posto.
 - Definire le posizioni dell'elemento e i vincoli di layout automatico.
 - Associazione azioni e prese per esporre elementi dell'interfaccia utente al codice.

<a name="Working-with-Segues" />

### <a name="working-with-segues"></a>Utilizzo di Segues

Come descritto in precedenza, Segues forniscono le relazioni tra tutte le scene di cui definiscono l'interfaccia utente dell'applicazione. Se si ha familiarità con l'utilizzo degli storyboard in iOS, si è certi che Segues per iOS in genere definiscono le transizioni tra le viste schermo intero. Questo comportamento è diverso da, macOS quando Segues definiscono in genere "contenimento" (in una scena è figlio di un elemento padre scena).

In Mac OS, la maggior parte delle applicazioni tendono a raggruppare le visualizzazioni all'interno della stessa finestra usando gli elementi dell'interfaccia utente, ad esempio le visualizzazioni suddivise e schede. A differenza di iOS, è necessario passata e disattivazione dello schermo in cui viste, visualizzare lo spazio a causa del fisico limitato.

Dato tendenze del macOS verso il contenimento, esistono situazioni in cui _presentazione Segues_ vengono utilizzate, ad esempio le finestre modali, visualizzazioni di finestra e questi elementi costituiscono.

Quando si utilizza Segues presentazione, è possibile eseguire l'override di `PrepareForSegue` metodo del Controller di visualizzazione padre per la presentazione per inizializzare e variabili e fornire i dati per il Controller di visualizzazione viene presentato.

<a name="Design-and-Run-Times" />

### <a name="design-and-run-times"></a>Progettazione e tempi di esecuzione

In fase di progettazione (quando layout out l'interfaccia utente in Generatore di interfaccia di Xcode), ogni elemento dell'interfaccia utente dell'applicazione è suddivisa in elementi costitutivi di:

- **Scene** , ovvero sono costituiti da:
    - **Visualizzazione Controller** -che definiscono le relazioni tra le visualizzazioni e i dati che li supportano.
    - **Viste e sottoviste** -gli elementi effettivi che costituiscono l'interfaccia utente.
    - **Indipendenza Segues** -che definiscono le relazioni padre-figlio tra le scene.
- **Presentazione Segues** -che definiscono le modalità di presentazione singoli. 

Mediante la definizione di ogni elemento in questo modo, e consente il caricamento lazy di ogni elemento solo quando è necessario in fase di esecuzione. In Mac OS, l'intero processo è stato progettato per consentire agli sviluppatori di creare interfacce utente complesse e flessibile che richiedono un minimo di un backup di codice per poterli utilizzare, tutti pur essendo efficienti con le risorse di sistema possibili.

<a name="Storyboard-Quick-Start" />

## <a name="storyboard-quick-start"></a>Avvio rapido di storyboard

Nel [avvio rapido di Storyboard](~/mac/platform/storyboards/quickstart.md) Guida, verrà creata una semplice app Xamarin.Mac che introduce i concetti principali di gestione di storyboard per creare un'interfaccia utente. L'app di esempio sarà costituito da una vista consente di dividere contenente un _Area del contenuto_ e _Area controllo_ e presenta una semplice finestra di dialogo Preferenze. Segues verrà usato per unire tutti gli elementi dell'interfaccia utente.

<a name="Working-with-Storyboards" />

## <a name="working-with-storyboards"></a>Utilizzo di storyboard

In questa sezione vengono illustrate le informazioni dettagliate di [utilizzo storyboard](~/mac/platform/storyboards/indepth.md) in un'app Xamarin.Mac. Adotteremo approfondimenti scene e come sono composti da controller di visualizzazione e di visualizzazione. Quindi, si verrà esaminato un modo scene sono associate insieme Segues. Infine, si verrà esaminato un utilizzo di tipi Segue personalizzati. 

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Esempio di Storyboard complessi

Per un esempio di un esempio di utilizzo di storyboard in un'app Xamarin.Mac complesso, vedere il [App di esempio SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter è un editor di codice sorgente semplice che offre supporto per il completamento del codice e informazioni sulla sintassi.

Il codice SourceWriter include tutti i commenti e, dove possibile, sono stati indicati i collegamenti delle tecnologie e dei metodi principali alle informazioni corrispondenti nelle guide di Xamarin.Mac.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato un rapido controllo di gestione di storyboard in un'app Xamarin.Mac. È stato illustrato come creare una nuova app mediante storyboard e come definire un'interfaccia utente. È stato anche illustrato come spostarsi tra le diverse finestre e gli stati di visualizzazione utilizzando segues.


## <a name="related-links"></a>Collegamenti correlati

- [Hello, Mac (sample)](https://developer.xamarin.com/samples/mac/Hello_Mac/) (Hello, Mac - Esempio)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
