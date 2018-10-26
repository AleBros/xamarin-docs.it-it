---
title: Utilizzo di controller di visualizzazione divisa in Xamarin tvOS
description: Questo documento descrive come usare tvOS doppia visualizzazione in un'app compilata con Xamarin. Fornisce una panoramica di alto livello dei controller di visualizzazione divisa, come usarli con gli storyboard, accedere alle visualizzazioni master e di dettaglio e visualizzare e nascondere la visualizzazione master.
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 9f1bd48378faa9ae6a4853083c93377268c38f01
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122192"
---
# <a name="working-with-tvos-split-view-controllers-in-xamarin"></a>Utilizzo di controller di visualizzazione divisa in Xamarin tvOS

Un Controller doppia visualizzazione Visualizza e gestisce un Master e il Controller di visualizzazione di dettaglio side-by-side, sullo schermo contemporaneamente. Controller di visualizzazione Split sono utilizzati per presentare contenuto persistente con stato attivabile nella visualizzazione Master (la sezione inferiore a sinistra) e relativi dettagli nella visualizzazione dettagli (la sezione più grande a destra).

[![](split-views-images/intro01.png "Esempio doppia visualizzazione")](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>Sui controller di visualizzazione suddivisa

Come indicato in precedenza, un Controller doppia visualizzazione gestisce un Master e un Controller di visualizzazione di dettagli che vengono presentati side-by-side con il Master in corso la vista più limitata a sinistra, il livello di dettaglio maggiore sulla destra. 

Inoltre, l'oggetto può di Controller di visualizzazione Master stato nascosto o visualizzato in base alle necessità: 

[![](split-views-images/intro02.png "Il Controller di visualizzazione Master nascosti")](split-views-images/intro02.png#lightbox)

Split viste controller sono spesso usano per presentare un elenco di contenuto filtrabile, con le categorie della visualizzazione Master e i risultati filtrati nella visualizzazione dettagli. Ciò viene solitamente presentato come una visualizzazione tabella a sinistra e un [visualizzazione di raccolta](~/ios/tvos/user-interface/collection-views.md) sulla destra.

Quando si progetta un'interfaccia utente che richiede un Controller di visualizzazione divisa, suggerite da Apple con Master e controller di visualizzazione di dettaglio che non vengono modificati (solo le modifiche del contenuto, non la struttura). Se è necessario lo scambio orizzontale i controller di visualizzazione, è consigliabile usare un Controller di spostamento come base del Controller di visualizzazione che è necessario modificare (Master o dettagli).

Apple ha i seguenti suggerimenti per l'utilizzo di controller di visualizzazione di suddivisione:

- **Usare la corretta percentuale Split** -per impostazione predefinita il Controller doppia visualizzazione Usa un terzo dello schermo, per il Controller di visualizzazione Master e due terzi per il Controller di visualizzazione di dettaglio. Facoltativamente, è possibile utilizzare una divisione 50/50. Scegliere la corretta percentuale per visualizzare il contenuto bilanciato nella schermata.
- **Rendere persistente la selezione principale** - mentre il contenuto in can la visualizzazione Dettagli modifica è risposta alla selezione di un utente nella visualizzazione Master, il contenuto della visualizzazione Master deve essere corretto. Inoltre, si dovrebbe mostrano chiaramente l'elemento attualmente selezionato nella visualizzazione Master.
- **Usare una singola unificata titolo** -in genere, è opportuno usare un titolo singolo, centrato nella visualizzazione dettagli, invece di un titolo in entrambi i dettagli e la visualizzazione Master.

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>Suddividere i controller di visualizzazione e storyboard

Il modo più semplice per lavorare con i controller di visualizzazione suddivisa in un'app xamarin. tvos è per aggiungerli all'interfaccia utente dell'app usando iOS Designer.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nel **riquadro della soluzione**, fare doppio clic il `Main.storyboard` file e aprirlo e modificarlo.
1. Trascinare un **controller di visualizzazione divisa** dal **della casella degli strumenti** e rilasciarlo nella vista: 

    [![](split-views-images/activity01.png "Un Controller di visualizzazione suddivisa")](split-views-images/activity01.png#lightbox)
1. Per impostazione predefinita, iOS Designer verrà installato un Controller di visualizzazione e un Controller di spostamento della visualizzazione Master. Se ciò non corrispondente ai requisiti dell'app, è sufficiente di eliminarli.
1. Se si rimuove il valore predefinito di visualizzazione Master, trascinare un nuovo Controller di visualizzazione nell'area di progettazione: 

    [![](split-views-images/activity02.png "Un Controller di visualizzazione")](split-views-images/activity02.png#lightbox)
1. CTRL + clic e trascinare dal Controller di visualizzazione divisa per il nuovo Controller di visualizzazione Master. 
1. Selezionare **Master** dalle **Menu Popup**: 

    [![](split-views-images/activity03.png "Seleziona Master dal Menu a comparsa")](split-views-images/activity03.png#lightbox)
1. Progettare il contenuto del Master e visualizzazioni Dettagli: 

    [![](split-views-images/activity04.png "Layout di esempio")](split-views-images/activity04.png#lightbox)
1. Assegnare **nomi** nel **scheda Widget** del **riquadro delle proprietà** per lavorare con i controlli dell'interfaccia utente in C# codice.
1. Salvare le modifiche e tornare a Visual Studio per Mac.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Nel **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file e aprirlo e modificarlo.
1. Trascinare un **controller di visualizzazione divisa** dal **della casella degli strumenti** e rilasciarlo nella vista: 

    [![](split-views-images/activity01-vs.png "Un Controller di visualizzazione suddivisa")](split-views-images/activity01-vs.png#lightbox)
1. Per impostazione predefinita, iOS Designer aggiungerà un Controller di spostamento e un Controller di visualizzazione della visualizzazione Master. Se ciò non corrispondente ai requisiti dell'app, è sufficiente di eliminarli.
1. Se si rimuove il valore predefinito di visualizzazione Master, trascinare un nuovo Controller di visualizzazione nell'area di progettazione: 

    [![](split-views-images/activity02-vs.png "Un Controller di visualizzazione")](split-views-images/activity02-vs.png#lightbox)
1. CTRL + clic e trascinare dal Controller di visualizzazione divisa per il nuovo Controller di visualizzazione Master. 
1. Selezionare **Master** dalle **Menu Popup**: 

    [![](split-views-images/activity03-vs.png "Seleziona Master dal Menu a comparsa")](split-views-images/activity03-vs.png#lightbox)
1. Progettare il contenuto del Master e visualizzazioni Dettagli: 

    [![](split-views-images/activity04.png "Layout del contenuto")](split-views-images/activity04.png#lightbox)
1. Assegnare **nomi** nel **scheda Widget** del **Esplora proprietà** per lavorare con i controlli dell'interfaccia utente in C# codice.
1. Salvare le modifiche.
    
-----

Per altre informazioni sull'utilizzo degli storyboard, vedere la [App Tvos Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md).

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>Utilizzo di controller di visualizzazione suddivisa

Come indicato in precedenza, un Controller di visualizzazione Split viene spesso usato in situazioni in cui si visualizzano contenuto filtrato all'utente. Le categorie principali vengono visualizzate a sinistra della visualizzazione Master, e i risultati filtrati sul lato destro nella visualizzazione dettagli in base alla selezione dell'utente.

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>L'accesso a Master e dettaglio

Se è necessario accedere al Master e i controller di visualizzazione dettagli a livello di codice, usare il `ViewControllers ` proprietà del Controller di visualizzazione di divisione. Ad esempio:

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

Viene presentato come una matrice, dove il primo elemento (0) nel Controller di visualizzazione Master e il secondo elemento (1) è il livello di dettaglio.

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>L'accesso ai dettagli dal Master

Poiché in genere per visualizzare informazioni dettagliate nella visualizzazione dettagli in base alla selezione dell'utente nel Master, è necessario un modo per i dettagli di accesso dal server Master.

Il modo più semplice per eseguire questa operazione consiste nell'esporre una proprietà nella classe Controller di visualizzazione Master, ad esempio:

```csharp
public DetailViewController DetailController { get; set;}
```

Nel Controller di visualizzazione divisa, eseguire l'override di `ViewDidLoad` metodo tie le due visualizzazioni e tra loro. Ad esempio:

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

È possibile esporre proprietà e metodi nel Controller di visualizzazione di dettaglio che il Master può utilizzare per presentare i nuovi dati in base alle esigenze.

<a name="Showing-and-Hiding-Master" />

### <a name="showing-and-hiding-master"></a>Mostrare e nascondere Master

Facoltativamente, è possibile visualizzare e nascondere il Controller di visualizzazione Master usando il `PreferredDisplayMode` proprietà del Controller di visualizzazione di divisione. Ad esempio:

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

Il `UISplitViewControllerDisplayMode` enum definisce come il Controller di visualizzazione Master verrà visualizzato uno dei seguenti:

- **Automatica** -tvOS controllerà la presentazione dei database Master e visualizzazioni dettagli.
- **PrimaryHidden** -in tal modo, il Controller di visualizzazione Master.
- **AllVisible** -vengono visualizzati lo schema sia il controller di visualizzazione di dettaglio side-by-side. Si tratta della normale, presentazione predefinita.
- **PrimaryOverlay** -il Controller di visualizzazione di dettaglio si estende in ed è coperto dal Master.

Per ottenere lo stato di presentazione corrente, usare il `DisplayMode` proprietà del Controller di visualizzazione di divisione.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la progettazione e l'utilizzo di controller di visualizzazione di divisione all'interno di un'app xamarin. tvos.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
