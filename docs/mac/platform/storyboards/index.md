---
title: Introduzione agli storyboard in xamarin. Mac
description: Questo articolo fornisce un'introduzione all'uso con gli storyboard in un'app xamarin. Mac. Viene descritto come creare e gestire l'interfaccia utente dell'app usando gli storyboard e Interface Builder di Xcode.
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 166a50021c22aa09be3eecdb8b745a70e75c3d51
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031484"
---
# <a name="introduction-to-storyboards-in-xamarinmac"></a>Introduzione agli storyboard in xamarin. Mac

_Questo articolo fornisce un'introduzione all'uso con gli storyboard in un'app xamarin. Mac. Viene illustrata la creazione e la gestione dell'interfaccia utente dell'app usando gli storyboard e Interface Builder di Xcode._

Gli storyboard consentono inoltre di sviluppare un'interfaccia utente per l'app xamarin. Mac che include non solo i controlli e le definizioni di finestra, ma contiene anche i collegamenti tra finestre diverse (tramite gli elementi segue) e visualizzare gli stati.

[![](images/intro01.png "Un esempio dell'interfaccia utente in Xcode")](images/intro01.png#lightbox)

Questo articolo fornirà un'introduzione all'uso degli storyboard per definire l'interfaccia utente di un'app xamarin. Mac.

<a name="What-are-Storyboards" />

## <a name="what-are-storyboards"></a>Quali sono gli storyboard?

Usando gli storyboard, tutti dell'interfaccia utente di un'app xamarin. Mac possono essere definiti in un'unica posizione con tutta la navigazione tra le interfacce utente e singoli elementi. Storyboard per xamarin. Mac, funziona in modo molto simile agli storyboard per xamarin. IOS. Tuttavia, contengono un set diverso di _tipi di elemento Segue_ a causa di idiomi di interfaccia diverse.

<a name="Working-with-Scenes" />

### <a name="working-with-scenes"></a>Lavora in background

Come indicato in precedenza, uno Storyboard definisce tutti l'interfaccia utente per una determinata app, suddivise in una panoramica delle funzionalità del relativo _controller di visualizzazione_. In Interface Builder di Xcode, ognuno di questi controller si trova un proprio _scena_.

[![](images/intro02.png "Un controller di visualizzazione di esempio")](images/intro02.png#lightbox)

Ogni scena rappresenta un vista e una coppia di Controller di visualizzazione specificato con un set di righe (denominato segue) che si connettono ogni scena nell'interfaccia utente, pertanto che mostra le relative relazioni. Alcuni segue definire un Controller di visualizzazione contiene uno o più visualizzazioni figlio o i controller di visualizzazione. Altri segue, definire le transizioni tra Controller di visualizzazione (ad esempio visualizzando una finestra di dialogo o nel popover). 

[![](images/intro03.png "Elemento segue un esempio")](images/intro03.png#lightbox)

L'aspetto più importante da notare è che ogni elemento Segue rappresenta il flusso di qualche tipo di dati tra l'elemento specificato dell'interfaccia utente dell'app.

<a name="Working-with-View-Controllers" />

### <a name="working-with-view-controllers"></a>Utilizzo di controller di visualizzazione

I controller di visualizzazione definiscono le relazioni tra una determinata visualizzazione di informazioni all'interno di un'app Mac e il modello di dati che fornisce tali informazioni. Ogni scena di primo livello nello Storyboard rappresenta un Controller di visualizzazione nel codice dell'app xamarin. Mac.

[![](images/intro04.png "Un esempio di controller di visualizzazione vengono ignorati")](images/intro04.png#lightbox)

In questo modo, ogni Controller di visualizzazione è un'associazione indipendente e riutilizzabile di rappresentazione visiva dell'informazione (visualizzazione) e la logica per consentire di controllare le informazioni.

All'interno di una scena specificata, è possibile eseguire tutte le operazioni che verrebbero normalmente sono state gestite da singoli `.xib` file: 

 - Posizione subviews e controlli (ad esempio i pulsanti e caselle di testo).
 - Definire le posizioni dell'elemento e i vincoli per il layout automatico.
 - Wireup azioni e gli Outlet per esporre gli elementi dell'interfaccia utente al codice.

<a name="Working-with-Segues" />

### <a name="working-with-segues"></a>Utilizzo con gli elementi segue

Come indicato in precedenza, segue forniscono le relazioni tra tutte le scene di cui definiscono l'interfaccia utente dell'app. Se si ha familiarità con l'utilizzo degli storyboard in iOS, si è certi che gli elementi segue per iOS in genere definiscono transizioni tra le viste schermo intero. Questo comportamento è diverso da macOS, quando si segue in genere definito "contenimento" (dove una scena rappresenta l'elemento figlio di un elemento padre scena).

In macOS, la maggior parte delle App tendono a raggruppare le visualizzazioni insieme all'interno della stessa finestra usando gli elementi dell'interfaccia utente, ad esempio schede e viste di divisione. A differenza di iOS, in cui le visualizzazioni dovranno eseguire la transizione accensione e spegnimento dello schermo, visualizzare lo spazio a causa di fisica limitata.

Dato utenti di macOS verso il contenimento, esistono situazioni in cui _presentazione gli elementi segue_ vengono usati, ad esempio Windows modale, foglio le visualizzazioni e Popovers.

Quando si usano gli elementi segue presentazione, è possibile eseguire l'override di `PrepareForSegue` metodo del Controller di visualizzazione padre per la presentazione per inizializzare e le variabili e fornire i dati per il Controller di visualizzazione viene presentato.

<a name="Design-and-Run-Times" />

### <a name="design-and-run-times"></a>Progettazione e tempi di esecuzione

In fase di progettazione (quando layout orizzontale l'interfaccia utente in Interface Builder di Xcode), ogni elemento dell'interfaccia utente dell'app è suddivisa in elementi costitutivi è:

- **Le scene** , ovvero sono costituiti da:
    - **Consente di visualizzare Controller** -che definiscono le relazioni tra le visualizzazioni e i dati che li supportano.
    - **Le viste e le visualizzazioni secondarie** -gli elementi effettivi che costituiscono l'interfaccia utente.
    - **Contenimento segue** -che definiscono le relazioni padre-figlio tra le scene.
- **Presentazione segue** -che definiscono le modalità di presentazione singoli. 

Definendo ogni elemento in questo modo, consente il caricamento lazy di ogni elemento solo quando è necessario in fase di esecuzione. In macOS, l'intero processo è stato progettato per consentire allo sviluppatore di creare interfacce utente complesse e flessibile che richiedono un minimo di un backup di codice per farli funzionare, tutti pur essendo efficienti con le risorse di sistema possibili.

<a name="Storyboard-Quick-Start" />

## <a name="storyboard-quick-start"></a>Avvio rapido di storyboard

Nel [avvio rapido di Storyboard](~/mac/platform/storyboards/quickstart.md) Guida, si creerà una semplice app xamarin. Mac che introduce i concetti chiave di utilizzo degli storyboard per creare un'interfaccia utente. L'app di esempio sarà costituito da una vista consente di dividere contenente un _Area di contenuto_ e un _Inspector Area_ e presenta una semplice finestra di dialogo Preferenze. Segue verrà usato per unire tutti gli elementi dell'interfaccia utente.

<a name="Working-with-Storyboards" />

## <a name="working-with-storyboards"></a>Utilizzo degli storyboard

Questa sezione vengono illustrati i dettagli approfonditi del [funziona con gli storyboard](~/mac/platform/storyboards/indepth.md) in un'app xamarin. Mac. Prendiamo uno sguardo approfondito sui scene e come sono composti di controller di visualizzazione e visualizzazione. Quindi, prenderemo in esame la modalità in background sono unite da segue. Infine, Daremo un'occhiata a lavorare con i tipi di elemento Segue personalizzati. 

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Esempio di Storyboard complessi

Per un esempio di un esempio complesso di lavorare con gli storyboard in un'app xamarin. Mac, vedere la [App di esempio SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter è un editor di codice sorgente semplice che offre supporto per il completamento del codice e informazioni sulla sintassi.

Il codice SourceWriter include tutti i commenti e, dove possibile, sono stati indicati i collegamenti delle tecnologie e dei metodi principali alle informazioni corrispondenti nelle guide di Xamarin.Mac.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha assunto un esame rapido di utilizzo degli storyboard in un'app xamarin. Mac. È stato illustrato come creare una nuova app usando gli storyboard e come definire un'interfaccia utente. È stato anche illustrato come spostarsi tra le diverse finestre e gli stati di visualizzazione tramite gli elementi segue.


## <a name="related-links"></a>Collegamenti correlati

- [Hello, Mac (sample)](https://developer.xamarin.com/samples/mac/Hello_Mac/) (Hello, Mac - Esempio)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
