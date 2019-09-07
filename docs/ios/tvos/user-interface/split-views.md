---
title: Uso dei controller di visualizzazione Split tvOS in Novell
description: Questo documento descrive come usare le viste Split tvOS in un'app compilata con Novell. Viene fornita una panoramica di alto livello dei controller di visualizzazione suddivisi, come utilizzarli con gli storyboard, accedere alle visualizzazioni master e dettagli e visualizzare e nascondere la visualizzazione master.
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 5c147b43caf3018cde8870adb25bb73cb08f53ee
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768504"
---
# <a name="working-with-tvos-split-view-controllers-in-xamarin"></a>Uso dei controller di visualizzazione Split tvOS in Novell

Un controller di visualizzazione suddiviso presenta e gestisce contemporaneamente un controller di visualizzazione master e dettagli affiancato sullo schermo. I controller di visualizzazione divisa vengono usati per presentare contenuti persistenti e attivabili nella visualizzazione Master (la sezione più piccola a sinistra) e i dettagli correlati nella visualizzazione dettagli (la sezione più grande a destra).

[![](split-views-images/intro01.png "Visualizzazione Split di esempio")](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>Informazioni sui controller di visualizzazione divisa

Come indicato in precedenza, un controller di visualizzazione suddiviso gestisce un controller di visualizzazione master e dettagli che vengono presentati side-by-Side, con il master che rappresenta la visualizzazione più piccola a sinistra, il dettaglio più grande a destra. 

Inoltre, il controller di visualizzazione master può essere nascosto o visualizzato come richiesto: 

[![](split-views-images/intro02.png "Il controller di visualizzazione Master è nascosto")](split-views-images/intro02.png#lightbox)

I controller delle visualizzazioni suddivise spesso utilizzano per presentare un elenco di contenuto filtrabile, con le categorie nella visualizzazione master e i risultati filtrati nella visualizzazione dettagli. Viene in genere visualizzato come una visualizzazione tabella a sinistra e una [visualizzazione raccolta](~/ios/tvos/user-interface/collection-views.md) a destra.

Quando si progetta un'interfaccia utente che richiede un controller di visualizzazione suddiviso, Apple suggerisce di usare i controller di visualizzazione master e dettagli che non cambiano (solo le modifiche al contenuto, non la struttura). Se è necessario scambiare i controller di visualizzazione, è preferibile utilizzare un controller di spostamento come base del controller di visualizzazione che deve essere modificato (Master o dettaglio).

Apple presenta i suggerimenti seguenti per lavorare con i controller di visualizzazione divisa:

- **Usa la percentuale di suddivisione corretta** . per impostazione predefinita, il controller di visualizzazione divisa usa un terzo dello schermo per il controller di visualizzazione master e due terzi per il controller di visualizzazione dettagli. Facoltativamente, è possibile usare una divisione 50/50. Scegliere la percentuale corretta per far apparire il contenuto bilanciato sullo schermo.
- **Mantieni la selezione principale** : mentre il contenuto nella visualizzazione dettagli può cambiare è la risposta alla selezione di un utente nella visualizzazione master, il contenuto della visualizzazione master deve essere corretto. Inoltre, è necessario visualizzare chiaramente l'elemento attualmente selezionato nella visualizzazione master.
- **Usare un singolo titolo unificato** . in genere, è consigliabile usare un singolo titolo centrato nella visualizzazione dettagli, anziché un titolo sia nei dettagli che nella visualizzazione master.

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>Suddividere i controller di visualizzazione e gli storyboard

Il modo più semplice per lavorare con i controller Split View in un'app Novell. tvOS consiste nell'aggiungerli all'interfaccia utente dell'app usando iOS designer.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nella **riquadro della soluzione**fare doppio clic sul `Main.storyboard` file e aprirlo per la modifica.
1. Trascinare un **controller di visualizzazione divisa** dalla **casella degli strumenti** e rilasciarlo nella visualizzazione: 

    [![](split-views-images/activity01.png "Controller di visualizzazione divisa")](split-views-images/activity01.png#lightbox)
1. Per impostazione predefinita, iOS designer installerà un controller di spostamento e un controller di visualizzazione nella visualizzazione master. Se il problema non soddisfa i requisiti dell'app, è sufficiente eliminarli.
1. Se si rimuove la visualizzazione master predefinita, trascinare un nuovo controller di visualizzazione nell'area di progettazione: 

    [![](split-views-images/activity02.png "Controller di visualizzazione")](split-views-images/activity02.png#lightbox)
1. Controllare: fare clic e trascinare dal controller di visualizzazione Split al nuovo controller di visualizzazione master. 
1. Selezionare **Master** dal **menu popup**: 

    [![](split-views-images/activity03.png "Selezionare Master dal menu popup")](split-views-images/activity03.png#lightbox)
1. Progettare il contenuto delle visualizzazioni master e dettagli: 

    [![](split-views-images/activity04.png "Layout di esempio")](split-views-images/activity04.png#lightbox)
1. Assegnare i **nomi** nella **scheda Widget** del **riquadro delle proprietà** per lavorare con i controlli dell'interfaccia utente C# nel codice.
1. Salvare le modifiche e tornare a Visual Studio per Mac.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Nella **Esplora soluzioni**fare doppio clic sul `Main.storyboard` file e aprirlo per la modifica.
1. Trascinare un **controller di visualizzazione divisa** dalla **casella degli strumenti** e rilasciarlo nella visualizzazione: 

    [![](split-views-images/activity01-vs.png "Controller di visualizzazione divisa")](split-views-images/activity01-vs.png#lightbox)
1. Per impostazione predefinita, in iOS Designer verrà aggiunto un controller di spostamento e un controller di visualizzazione nella visualizzazione master. Se il problema non soddisfa i requisiti dell'app, è sufficiente eliminarli.
1. Se si rimuove la visualizzazione master predefinita, trascinare un nuovo controller di visualizzazione nell'area di progettazione: 

    [![](split-views-images/activity02-vs.png "Controller di visualizzazione")](split-views-images/activity02-vs.png#lightbox)
1. Controllare: fare clic e trascinare dal controller di visualizzazione Split al nuovo controller di visualizzazione master. 
1. Selezionare **Master** dal **menu popup**: 

    [![](split-views-images/activity03-vs.png "Selezionare Master dal menu popup")](split-views-images/activity03-vs.png#lightbox)
1. Progettare il contenuto delle visualizzazioni master e dettagli: 

    [![](split-views-images/activity04.png "Layout del contenuto")](split-views-images/activity04.png#lightbox)
1. Assegnare i **nomi** nella **scheda Widget** di **Esplora proprietà** per usare i controlli dell'interfaccia utente nel C# codice.
1. Salvare le modifiche.

-----

Per ulteriori informazioni sull'utilizzo degli storyboard, vedere la [Guida introduttiva Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>Utilizzo di controller di visualizzazione divisi

Come indicato in precedenza, un controller di visualizzazione suddiviso viene spesso usato nelle situazioni in cui viene visualizzato un contenuto filtrato per l'utente. Le categorie principali vengono visualizzate a sinistra nella visualizzazione master e i risultati filtrati a destra nella visualizzazione dettagli in base alla selezione dell'utente.

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>Accesso al master e ai dettagli

Se è necessario accedere ai controller di visualizzazione master e dettagli a livello di codice, `ViewControllers` utilizzare la proprietà del controller di visualizzazione divisa. Ad esempio:

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

Viene presentato come una matrice, dove il primo elemento (0) nel controller di visualizzazione master e il secondo elemento (1) sono i dettagli.

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>Accesso ai dettagli dal master

Poiché in genere vengono visualizzate informazioni dettagliate nella visualizzazione dettagli in base alla selezione dell'utente nel database master, è necessario un modo per accedere ai dettagli dal master.

Il modo più semplice per eseguire questa operazione consiste nell'esporre una proprietà nella classe del controller di visualizzazione master, ad esempio:

```csharp
public DetailViewController DetailController { get; set;}
```

Nel controller di visualizzazione divisa eseguire l'override `ViewDidLoad` del metodo e unire le due visualizzazioni. Ad esempio:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Gain access to master and detail view controllers
    var masterController = ViewControllers [0] as MasterViewController;
    var detailController = ViewControllers [1] as DetailViewController;

    // Wire-up views
    masterController.SplitViewController = this;
    masterController.DetailController = detailController;
    detailController.SplitViewController = this;
}
```

È possibile esporre le proprietà e i metodi sul controller di visualizzazione dettagli che il master può usare per presentare i nuovi dati come richiesto.

<a name="Showing-and-Hiding-Master" />

### <a name="showing-and-hiding-master"></a>Visualizzazione e occultamento del Master

Facoltativamente, è possibile mostrare e nascondere il controller di visualizzazione master utilizzando `PreferredDisplayMode` la proprietà del controller di visualizzazione divisa. Ad esempio:

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

L' `UISplitViewControllerDisplayMode` enumerazione definisce il modo in cui il controller di visualizzazione master verrà presentato come uno degli elementi seguenti:

- **Automatic** -tvOS controllerà la presentazione delle visualizzazioni master e dettagli.
- **PrimaryHidden** : nasconde il controller di visualizzazione master.
- **AllVisible** : Visualizza i controller di visualizzazione master e dettagli affiancati. Si tratta della normale presentazione predefinita.
- **PrimaryOverlay** : il controller di visualizzazione dettagli si estende in ed è coperto dal master.

Per ottenere lo stato di presentazione corrente, usare `DisplayMode` la proprietà del controller di visualizzazione divisa.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stata illustrata la progettazione e l'utilizzo di controller di visualizzazione divisi all'interno di un'app Novell. tvOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
