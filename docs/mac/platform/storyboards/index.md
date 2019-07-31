---
title: Introduzione agli storyboard in Novell. Mac
description: Questo articolo fornisce un'introduzione all'uso degli storyboard in un'app Novell. Mac. Viene descritto come creare e gestire l'interfaccia utente dell'app usando gli storyboard e Interface Builder di Xcode.
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 75affbdb6e919c15c644a68ae45e7ff657a53f6a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652287"
---
# <a name="introduction-to-storyboards-in-xamarinmac"></a>Introduzione agli storyboard in Novell. Mac

_Questo articolo fornisce un'introduzione all'uso degli storyboard in un'app Novell. Mac. Viene illustrata la creazione e la gestione dell'interfaccia utente dell'app usando gli storyboard e la Interface Builder di Xcode._

Gli storyboard consentono di sviluppare un'interfaccia utente per l'app Novell. Mac che non include solo le definizioni e i controlli della finestra, ma contiene anche i collegamenti tra le diverse finestre (tramite gli elementi segue) e gli Stati di visualizzazione.

[![](images/intro01.png "Interfaccia utente di esempio in Xcode")](images/intro01.png#lightbox)

Questo articolo fornisce un'introduzione all'uso degli storyboard per definire l'interfaccia utente di un'app Novell. Mac.

<a name="What-are-Storyboards" />

## <a name="what-are-storyboards"></a>Che cosa sono gli storyboard?

Usando gli storyboard, è possibile definire l'interfaccia utente di un'app Novell. Mac in un'unica posizione con tutta la navigazione tra i singoli elementi e le interfacce utente. Gli storyboard per Novell. Mac funzionano in modo molto simile agli storyboard per Novell. iOS. Tuttavia, contengono un set diverso di _tipi segue_ a causa dei diversi idiomi dell'interfaccia.

<a name="Working-with-Scenes" />

### <a name="working-with-scenes"></a>Uso delle scene

Come indicato in precedenza, uno storyboard definisce tutta l'interfaccia utente per una determinata app suddivisa in una panoramica funzionale dei _controller di visualizzazione_. In Interface Builder di Xcode, ognuno di questi controller si trova in una propria _scena_.

[![](images/intro02.png "Un controller di visualizzazione di esempio")](images/intro02.png#lightbox)

Ogni scena rappresenta una coppia visualizzazione e controller di visualizzazione specificata con un set di righe (denominato gli elementi segue) che connette ogni scena nell'interfaccia utente, mostrando così le relazioni. Alcuni gli elementi segue definiscono il modo in cui un controller di visualizzazione contiene una o più visualizzazioni figlio o controller di visualizzazione. Gli altri gli elementi segue definiscono le transizioni tra il controller di visualizzazione, ad esempio la visualizzazione di una finestra di dialogo o un messaggio. 

[![](images/intro03.png "Segue di esempio")](images/intro03.png#lightbox)

L'aspetto più importante da notare è che ogni segue rappresenta il flusso di una forma di dati tra l'elemento specificato dell'interfaccia utente dell'app.

<a name="Working-with-View-Controllers" />

### <a name="working-with-view-controllers"></a>Uso dei controller di visualizzazione

I controller di visualizzazione definiscono le relazioni tra una determinata visualizzazione di informazioni all'interno di un'app Mac e il modello di dati che fornisce tali informazioni. Ogni scena di primo livello nello storyboard rappresenta un controller di visualizzazione nel codice dell'app Novell. Mac.

[![](images/intro04.png "Un esempio di scorrimento di un controller di visualizzazione")](images/intro04.png#lightbox)

In questo modo, ogni controller di visualizzazione è un'associazione autonoma e riutilizzabile della rappresentazione visiva (visualizzazione) delle informazioni e della logica per presentare e controllare tali informazioni.

All'interno di una determinata scena, è possibile eseguire tutte le operazioni normalmente gestite da singoli `.xib` file: 

- Posizionare le sottovisualizzazioni e i controlli, ad esempio pulsanti e caselle di testo.
- Definire le posizioni degli elementi e i vincoli di layout automatico.
- Azioni di collegamento e Outlet per esporre gli elementi dell'interfaccia utente al codice.

<a name="Working-with-Segues" />

### <a name="working-with-segues"></a>Uso di gli elementi segue

Come indicato in precedenza, gli elementi segue fornisce le relazioni tra tutte le scene che definiscono l'interfaccia utente dell'app. Se si ha familiarità con l'uso degli storyboard in iOS, si sa che gli elementi segue per iOS definisce in genere le transizioni tra le visualizzazioni a schermo intero. Questo comportamento è diverso da macOS, quando gli elementi segue in genere definiscono "containment" (dove una scena è l'elemento figlio di una scena padre).

In macOS la maggior parte delle app tende a raggruppare le visualizzazioni all'interno della stessa finestra usando gli elementi dell'interfaccia utente, ad esempio le visualizzazioni e le schede suddivise. A differenza di iOS, in cui le visualizzazioni devono essere sottoposte a transizione sullo schermo, a causa dello spazio di visualizzazione fisico limitato.

Date le tendenze di macOS all'indipendenza, esistono situazioni in cui vengono usate le _gli elementi segue di presentazione_ , ad esempio le finestre modali, le visualizzazioni dei fogli e i Popov.

Quando si usa Presentation gli elementi segue, è possibile eseguire `PrepareForSegue` l'override del metodo del controller di visualizzazione padre per la presentazione per inizializzare e variabili e fornire i dati al controller di visualizzazione visualizzato.

<a name="Design-and-Run-Times" />

### <a name="design-and-run-times"></a>Progettazione ed esecuzione

In fase di progettazione (quando si crea il layout dell'interfaccia utente nel Interface Builder di Xcode), ogni elemento dell'interfaccia utente dell'app viene suddiviso in elementi costitutivi:

- **Scene** , che sono costituite da:
    - **Controller di visualizzazione** : che definiscono le relazioni tra le visualizzazioni e i dati che li supportano.
    - **Visualizzazioni e sottoviste** : gli elementi effettivi che costituiscono l'interfaccia utente.
    - **Gli elementi segue** di contenimento che definiscono le relazioni padre-figlio tra le scene.
- **Gli elementi segue di presentazione** : definiscono le singole modalità di presentazione. 

Definendo ogni elemento in questo modo, consente il caricamento lazy di ogni elemento solo quando è necessario in fase di esecuzione. In macOS, l'intero processo è stato progettato in modo da consentire allo sviluppatore di creare interfacce utente complesse e flessibili che richiedono un minimo di codice di supporto per poterle lavorare, il tutto con la massima efficienza delle risorse di sistema.

<a name="Storyboard-Quick-Start" />

## <a name="storyboard-quick-start"></a>Avvio rapido storyboard

Nella Guida [avvio rapido storyboard](~/mac/platform/storyboards/quickstart.md) verrà creata una semplice app Novell. Mac che introduce i concetti chiave dell'utilizzo degli storyboard per creare un'interfaccia utente. L'app di esempio è costituita da una visualizzazione a dispari contenente un' _area di contenuto_ e un' _area di controllo_ e presenta una finestra di dialogo di preferenze semplice. Si utilizzerà gli elementi segue per associare tutti gli elementi dell'interfaccia utente.

<a name="Working-with-Storyboards" />

## <a name="working-with-storyboards"></a>Utilizzo degli storyboard

In questa sezione vengono illustrati i dettagli approfonditi sull' [utilizzo degli storyboard](~/mac/platform/storyboards/indepth.md) in un'app Novell. Mac. Vengono esaminate in dettaglio le scene e il modo in cui sono composte dai controller e dalle visualizzazioni di visualizzazione. Verrà quindi esaminata la correlazione tra le scene e gli elementi segue. Infine, verranno esaminate le operazioni con i tipi di segue personalizzati. 

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Esempio di storyboard complesso

Per un esempio di un esempio complesso di utilizzo degli storyboard in un'app Novell. Mac, vedere l'app di [esempio SourceWriter](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter). SourceWriter è un editor di codice sorgente semplice che offre supporto per il completamento del codice e informazioni sulla sintassi.

Il codice SourceWriter include tutti i commenti e, dove possibile, sono stati indicati i collegamenti delle tecnologie e dei metodi principali alle informazioni corrispondenti nelle guide di Xamarin.Mac.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato rapidamente l'uso degli storyboard in un'app Novell. Mac. Abbiamo visto come creare una nuova app usando gli storyboard e come definire un'interfaccia utente. È stato inoltre illustrato come spostarsi tra le diverse finestre e gli Stati di visualizzazione utilizzando gli elementi segue.


## <a name="related-links"></a>Collegamenti correlati

- [Hello, Mac (sample)](https://docs.microsoft.com/samples/xamarin/mac-samples/hello-mac) (Hello, Mac - Esempio)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
