---
title: Utilizzo di tvOS visualizzazioni raccolta in Xamarin
description: Questo documento descrive come usare le visualizzazioni di raccolta in un'app tvOS compilate con Xamarin. Viene descritto come layout di visualizzazione di raccolta, la creazione di celle e supplementari. tali viste risposta agli eventi utente e altro ancora.
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f815afa6b1abb15348019b0c53333b4acb054008
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60933882"
---
# <a name="working-with-tvos-collection-views-in-xamarin"></a>Utilizzo di tvOS visualizzazioni raccolta in Xamarin

Le visualizzazioni di raccolta consentono un gruppo di contenuto da visualizzare utilizzando i layout arbitrari. Con il supporto incorporato, consentono il layout di griglia o lineare la creazione semplificata, supportando anche i layout personalizzati.

[![](collection-views-images/collection01.png "Visualizzazione di raccolta di esempio")](collection-views-images/collection01.png#lightbox)

La visualizzazione di raccolta mantiene una raccolta di elementi usando un delegato sia un'origine dati per fornire l'interazione dell'utente e il contenuto della raccolta. Poiché la visualizzazione di raccolta è basata su un sottosistema di Layout che è indipendente dalla vista stessa, che fornisce un Layout diverso è possibile modificare facilmente la presentazione dei dati su immediatamente della vista di raccolta.

<a name="About-Collection-Views" />

## <a name="about-collection-views"></a>Informazioni sulle viste di raccolta

Come indicato in precedenza, una visualizzazione di raccolta (`UICollectionView`) gestisce una raccolta ordinata di elementi e presenta gli elementi di layout personalizzabili. Visualizzazioni raccolta funzionano in modo simile alle visualizzazioni di tabella (`UITableView`), ad eccezione di poter usare i layout di elementi presenti in più di una sola colonna.

Quando si usa una visualizzazione di raccolta in tvOS, l'app è responsabile di fornire i dati associati all'insieme utilizzando un'origine dati (`UICollectionViewDataSource`). I dati di visualizzazione raccolta possono facoltativamente essere organizzati e presentati in gruppi diversi (sezioni).

La visualizzazione di raccolta presenta i singoli elementi sullo schermo utilizzando una cella (`UICollectionViewCell`) che fornisce la presentazione di un'informazione specificata dalla raccolta (ad esempio, un'immagine e il relativo titolo).

Facoltativamente, è possibile aggiungere visualizzazioni supplementari alla presentazione della vista di raccolta per fungere da intestazione e piè di pagina per le sezioni e celle. Layout della vista raccolta è responsabile per la definizione della posizione di queste visualizzazioni e le singole celle.

La visualizzazione di raccolta possono rispondere all'interazione dell'utente usando un delegato (`UICollectionViewDelegate`). Questo delegato è anche responsabile di determinare se una cella specificata può ottenere lo stato attivo, se è stata evidenziata una cella o se uno è stato selezionato. In alcuni casi, il delegato determina le dimensioni delle singole celle.

<a name="Collection-View-Layouts" />

## <a name="collection-view-layouts"></a>Layout di visualizzazione raccolta

Una funzionalità chiave di una visualizzazione di raccolta è la separazione tra i dati presentati e il relativo Layout. Un Layout di visualizzazione di raccolta (`UICollectionViewLayout`) è responsabile di fornire l'organizzazione e la posizione in cui le celle (e tutte le viste supplementari) con nella presentazione sullo schermo della vista di raccolta.

Le singole celle vengono create dalla visualizzazione raccolta dalla relativa origine dati collegata sono disposti e visualizzate per il Layout di visualizzazione di raccolta specificato.

Il Layout di visualizzazione di raccolta vengono in genere fornito quando viene creata la visualizzazione di raccolta. Tuttavia, è possibile modificare il Layout di visualizzazione raccolta in qualsiasi momento e la presentazione dei dati della visualizzazione raccolta sullo schermo verrà aggiornata automaticamente con il nuovo layout fornito.

Il Layout di visualizzazione raccolta fornisce diversi metodi che possono essere utilizzati per aggiungere un'animazione di transizione tra due layout diversi (per impostazione predefinita che viene applicata alcuna animazione non viene eseguita). Inoltre, i layout di visualizzazione raccolta può rivolgersi i riconoscitori di movimento ulteriormente animare l'interazione dell'utente che produce una modifica nel layout.

<a name="Creating-Cells-and-Supplementary-Views" />

## <a name="creating-cells-and-supplementary-views"></a>Creazione di celle e visualizzazioni supplementari

Origine dati della vista di raccolta non è solo responsabile di fornire i dati di backup della raccolta elementi, ma anche le celle che consentono di visualizzare il contenuto.

Poiché le visualizzazioni di raccolta sono state progettate per gestire grandi raccolte di elementi, le singole celle possono essere rimossi dalla coda e riutilizzate per evitare di sovraccaricare i limiti di memoria. Esistono due metodi diversi per le visualizzazioni di rimozione:

- `DequeueReusableCell` : Crea o restituisce una cella del tipo specificato (come specificato nello Storyboard dell'app).
- `DequeueReusableSupplementaryView` : Crea o restituisce una visualizzazione supplementare del tipo specificato (come specificato nello Storyboard dell'app).

Prima di chiamare uno di questi metodi, è necessario registrare la classe, uno Storyboard o `.xib` file utilizzato per creare la visualizzazione della cella con visualizzazione dell'insieme. Ad esempio:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

In cui `typeof(CityCollectionViewCell)` fornisce la classe che supporta la visualizzazione e `CityViewDatasource.CardCellId` fornisce l'ID utilizzato quando la cella (o la vista) viene rimosso dalla coda.

Dopo la cella viene rimosso dalla coda, è configurato con i dati per l'elemento che rappresenta e tornare alla visualizzazione di raccolta per la visualizzazione.

<a name="About-Collection-View-Controllers" />

## <a name="about-collection-view-controllers"></a>Sui controller di visualizzazione raccolta

Un Controller di visualizzazione di raccolta (`UICollectionViewController`) è un Controller di visualizzazione specializzato (`UIViewController`) che fornisce il comportamento seguente:

- È responsabile per il caricamento di visualizzazione dell'insieme dello Storyboard o `.xib` file e creazione di un'istanza della visualizzazione. Se è stato creato nel codice, viene creato automaticamente una nuova visualizzazione raccolta non configurato.
- Dopo aver caricata la visualizzazione di raccolta, il controller tenta di caricare relativa origine dati e il delegato da uno Storyboard o `.xib` file. Se non sono disponibili, viene impostato come origine di entrambi.
- Garantisce che i dati vengono caricati prima che la visualizzazione di raccolta viene popolata nel primo visualizzato e ricarica e Cancella di select per ogni successiva visualizzazione.

Inoltre, il Controller di visualizzazione raccolta fornisce metodi sottoponibili a override che consente di gestire il ciclo di vita della visualizzazione di raccolta, ad esempio `AwakeFromNib` e `ViewWillDisplay`.

<a name="Collection-Views-and-Storyboards" />

## <a name="collection-views-and-storyboards"></a>Storyboard e le visualizzazioni di raccolta

Il modo più semplice per lavorare con una visualizzazione di raccolta nell'app xamarin. tvos, consiste nell'aggiungere uno a dello Storyboard. Un rapido esempio, si intende creare un'app di esempio che presenta un'immagine, titolo e un pulsante di selezione. Se l'utente fa clic sul pulsante Seleziona, verrà visualizzata una visualizzazione di raccolta che consentono all'utente di scegliere una nuova immagine. Quando si sceglie un'immagine, la visualizzazione di raccolta viene chiusa e verrà visualizzate la nuova immagine e il titolo.

È possibile eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

    
1. Avviare una nuova **App visualizzazione singola tvOS** in Visual Studio per Mac.
1. Nel **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file e aprirlo in iOS Designer.
1. Aggiungere una visualizzazione di immagini, un'etichetta e un pulsante alla visualizzazione esistente e configurarli in modo simile al seguente: 

    [![](collection-views-images/collection02.png "Layout di esempio")](collection-views-images/collection02.png#lightbox)
1. Assegnare un **Name** per la visualizzazione di immagini e l'etichetta nel **scheda Widget** del **Esplora proprietà**. Ad esempio: 

    [![](collection-views-images/collection03.png "Impostazione del nome")](collection-views-images/collection03.png#lightbox)
1. Successivamente, trascinare un Controller di visualizzazione raccolta dello Storyboard: 

    [![](collection-views-images/collection04.png "Un Controller di visualizzazione raccolta")](collection-views-images/collection04.png#lightbox)
1. Controllo e trascinare dal pulsante per il Controller di visualizzazione di raccolta e selezionare **Push** dal popup della: 

    [![](collection-views-images/collection05.png "Selezionare Push dal popup della")](collection-views-images/collection05.png#lightbox)
1. Quando si esegue l'app, in questo modo la visualizzazione di raccolta da Mostra ogni volta che l'utente fa clic sul pulsante.
1. Selezionare la visualizzazione di raccolta e immettere i valori seguenti nel **scheda Layout** delle **Esplora proprietà**: 

    [![](collection-views-images/collection06.png "Il riquadro delle proprietà")](collection-views-images/collection06.png#lightbox)
1. Consente di controllare le dimensioni delle singole celle e i bordi tra le celle e il bordo esterno della visualizzazione della raccolta.
1. Selezionare il Controller di visualizzazione di raccolta e impostare la relativa classe su `CityCollectionViewController` nella **Widget scheda**: 

    [![](collection-views-images/collection07.png "Impostare la classe a CityCollectionViewController")](collection-views-images/collection07.png#lightbox)
1. Selezionare la visualizzazione di raccolta e impostare la relativa classe su `CityCollectionView` nella **Widget scheda**: 

    [![](collection-views-images/collection08.png "Impostare la classe a CityCollectionView")](collection-views-images/collection08.png#lightbox)
1. Selezionare la cella di visualizzazione di raccolta e impostare la relativa classe su `CityCollectionViewCell` nella **Widget scheda**: 

    [![](collection-views-images/collection09.png "Impostare la classe a CityCollectionViewCell")](collection-views-images/collection09.png#lightbox)
1. Nel **scheda Widget** assicurarsi che il **Layout** viene `Flow` e la **direzione di scorrimento** è `Vertical` per la visualizzazione di raccolta: 

    [![](collection-views-images/collection10.png "La scheda di Widget")](collection-views-images/collection10.png#lightbox)
1. Selezionare la cella di visualizzazione di raccolta e impostare relativi **Identity** al `CityCell` nel **Widget scheda**: 

    [![](collection-views-images/collection11.png "Impostare l'identità su CityCell")](collection-views-images/collection11.png#lightbox)
1. Salvare le modifiche.
    

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    
1. Avviare una nuova **App visualizzazione singola tvOS** in Visual Studio.
1. Nel **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file e aprirlo in iOS Designer.
1. Aggiungere una visualizzazione di immagini, un'etichetta e un pulsante alla visualizzazione esistente e configurarli in modo simile al seguente: 

    [![](collection-views-images/collection02vs.png "Configurare il layout")](collection-views-images/collection02vs.png#lightbox)
1. Assegnare un **Name** per la visualizzazione di immagini e l'etichetta nel **scheda Widget** del **Esplora proprietà**. Ad esempio: 

    [![](collection-views-images/collection03vs.png "Il riquadro delle proprietà")](collection-views-images/collection03vs.png#lightbox)
1. Successivamente, trascinare un Controller di visualizzazione raccolta dello Storyboard: 

    [![](collection-views-images/collection04vs.png "Un Controller di visualizzazione raccolta")](collection-views-images/collection04vs.png#lightbox)
1. Controllo e trascinare dal pulsante per il Controller di visualizzazione di raccolta e selezionare **Push** dal popup della: 

    [![](collection-views-images/collection05vs.png "Selezionare Push dal popup della")](collection-views-images/collection05vs.png#lightbox)
1. Quando si esegue l'app, in questo modo la visualizzazione di raccolta da Mostra ogni volta che l'utente fa clic sul pulsante.
1. Selezionare la visualizzazione di raccolta e nel **scheda Layout** del **riquadro delle proprietà** immettere il **larghezza** come _361_ e  **Altezza** come _256_ 
1. Consente di controllare le dimensioni delle singole celle e i bordi tra le celle e il bordo esterno della visualizzazione della raccolta.
1. Selezionare il Controller di visualizzazione di raccolta e impostare la relativa classe su `CityCollectionViewController` nella **Widget scheda**: 

    [![](collection-views-images/collection07vs.png "Impostare la classe a CityCollectionViewController")](collection-views-images/collection07vs.png#lightbox)
1. Selezionare la visualizzazione di raccolta e impostare la relativa classe su `CityCollectionView` nella **Widget scheda**: 

    [![](collection-views-images/collection08vs.png "Impostare la classe a CityCollectionView")](collection-views-images/collection08vs.png#lightbox)
1. Selezionare la cella di visualizzazione di raccolta e impostare la relativa classe su `CityCollectionViewCell` nella **Widget scheda**: 

    [![](collection-views-images/collection09vs.png "Impostare la classe a CityCollectionViewCell")](collection-views-images/collection09vs.png#lightbox)
1. Nel **scheda Widget** assicurarsi che il **Layout** viene `Flow` e la **direzione di scorrimento** è `Vertical` per la visualizzazione di raccolta: 

    [![](collection-views-images/collection10vs.png "TImpossibile Widget scheda")](collection-views-images/collection10vs.png#lightbox)
1. Selezionare la cella di visualizzazione di raccolta e impostare relativi **Identity** al `CityCell` nel **Widget scheda**: 

    [![](collection-views-images/collection11vs.png "Impostare l'identità su CityCell")](collection-views-images/collection11vs.png#lightbox)
1. Salvare le modifiche.
    

-----

Se fosse stata scelta `Custom` per la visualizzazione di raccolta **Layout**, è possibile specificare un layout personalizzato. Apple offre un oggetto incorporato `UICollectionViewFlowLayout` e `UICollectionViewDelegateFlowLayout` facilmente che possono presentare i dati in un layout griglia (questi vengono usati per il `flow` stile di layout). 

Per altre informazioni sull'utilizzo degli storyboard, vedere la [App Tvos Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md).

<a name="Providing-Data-for-the-Collection-View" />

## <a name="providing-data-for-the-collection-view"></a>Fornire i dati per la visualizzazione di raccolta

Ora che abbiamo la visualizzazione di raccolta (e Controller di visualizzazione raccolta) aggiunto al nostro Storyboard, è necessario fornire i dati per la raccolta. 

<a name="The-Data-Model" />

### <a name="the-data-model"></a>Il modello di dati

In primo luogo, si intende creare un modello per i dati che contiene il nome del file per l'immagine da visualizzare, il titolo e un flag per consentire la città da selezionare.

Creare un `CityInfo` classe e renderlo simile al seguente:

```csharp
using System;

namespace tvCollection
{
    public class CityInfo
    {
        #region Computed Properties
        public string ImageFilename { get; set; }
        public string Title { get; set; }
        public bool CanSelect{ get; set; }
        #endregion

        #region Constructors
        public CityInfo (string filename, string title, bool canSelect)
        {
            // Initialize
            this.ImageFilename = filename;
            this.Title = title;
            this.CanSelect = canSelect;
        }
        #endregion
    }
}
```

### <a name="the-collection-view-cell"></a>La cella di visualizzazione raccolta

A questo punto è necessario definire come verranno presentati i dati per ogni cella. Modificare il `CityCollectionViewCell.cs` file (creato automaticamente dal file di Storyboard) e renderlo simile al seguente:

```csharp
using System;
using Foundation;
using UIKit;
using CoreGraphics;

namespace tvCollection
{
    public partial class CityCollectionViewCell : UICollectionViewCell
    {
        #region Private Variables
        private CityInfo _city;
        #endregion

        #region Computed Properties
        public UIImageView CityView { get ; set; }
        public UILabel CityTitle { get; set; }

        public CityInfo City {
            get { return _city; }
            set {
                _city = value;
                CityView.Image = UIImage.FromFile (City.ImageFilename);
                CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
                CityTitle.Text = City.Title;
            }
        }
        #endregion

        #region Constructors
        public CityCollectionViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            CityView = new UIImageView(new CGRect(22, 19, 320, 171));
            CityView.AdjustsImageWhenAncestorFocused = true;
            AddSubview (CityView);

            CityTitle = new UILabel (new CGRect (22, 209, 320, 21)) {
                TextAlignment = UITextAlignment.Center,
                TextColor = UIColor.White,
                Alpha = 0.0f
            };
            AddSubview (CityTitle);
        }
        #endregion
    

    }
}
```

Per l'app tvOS, si prevede di visualizzare un'immagine e un titolo facoltativo. Se non è possibile selezionare quella determinata città, si sta attenuazione la visualizzazione di immagini usando il codice seguente:

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

Quando la cella contenente l'immagine viene portata messa a fuoco dall'utente, si desidera utilizzare l'elemento predefinito effetto di parallasse su di esso impostando la proprietà seguente:

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

Per altre informazioni sulla navigazione e lo stato attivo, vedere la [utilizzo di navigazione e lo stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) e [controller Bluetooth e remoti per Siri](~/ios/tvos/platform/remote-bluetooth.md) documentazione.


<a name="The-Collection-View-Data-Provider" />

### <a name="the-collection-view-data-provider"></a>Il Provider di dati di visualizzazione raccolta

Con il modello di dati creato e il layout della cella definita, creiamo un'origine dati per la nostra visualizzazione raccolta. L'origine dati sarà responsabile di fornire non solo il backup dei dati, ma anche rimozione le celle per visualizzare le singole celle sullo schermo.

Creare un `CityViewDatasource` classe e renderlo simile al seguente:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using ObjCRuntime;

namespace tvCollection
{
    public class CityViewDatasource : UICollectionViewDataSource
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Static Constants
        public static NSString CardCellId = new NSString ("CityCell");
        #endregion

        #region Computed Properties
        public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
        public CityCollectionView ViewController { get; set; }
        #endregion

        #region Constructors
        public CityViewDatasource (CityCollectionView controller)
        {
            // Initialize
            this.ViewController = controller;
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities() {

            // Clear existing cities
            Cities.Clear();

            // Add new cities
            Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
            Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
            Cities.Add(new CityInfo("City03.jpg", "Skyline at Night", true));
            Cities.Add(new CityInfo("City04.jpg", "Golden Gate Bridge", true));
            Cities.Add(new CityInfo("City05.jpg", "Roads by Night", true));
            Cities.Add(new CityInfo("City06.jpg", "Church Domes", true));
            Cities.Add(new CityInfo("City07.jpg", "Mountain Lights", true));
            Cities.Add(new CityInfo("City08.jpg", "City Scene", false));
            Cities.Add(new CityInfo("City09.jpg", "House in Winter", true));
            Cities.Add(new CityInfo("City10.jpg", "By the Lake", true));
            Cities.Add(new CityInfo("City11.jpg", "At the Dome", true));
            Cities.Add(new CityInfo("City12.jpg", "Cityscape", true));
            Cities.Add(new CityInfo("City13.jpg", "Model City", true));
            Cities.Add(new CityInfo("City14.jpg", "Taxi, Taxi!", true));
            Cities.Add(new CityInfo("City15.jpg", "On the Sidewalk", true));
            Cities.Add(new CityInfo("City16.jpg", "Midnight Walk", true));
            Cities.Add(new CityInfo("City17.jpg", "Lunchtime Cafe", true));
            Cities.Add(new CityInfo("City18.jpg", "Coffee Shop", true));
            Cities.Add(new CityInfo("City19.jpg", "Rustic Tavern", true));
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            return Cities.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
            var city = Cities [indexPath.Row];

            // Initialize city
            cityCell.City = city;

            return cityCell;
        }
        #endregion
    }
}
```

Ora esaminare questa classe in modo dettagliato. In primo luogo, si ereditano da `UICollectionViewDataSource` e fornire un collegamento all'ID di celle (che abbiamo assegnato in iOS Designer):

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

Passaggio successivo è offrire archiviazione per i dati di raccolta e fornire una classe per popolare i dati:

```csharp
public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
...

public void PopulateCities() {

    // Clear existing cities
    Cities.Clear();

    // Add new cities
    Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
    Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
    ...
}
```

Quindi viene eseguito l'override di `NumberOfSections` (metodo) e restituiscono il numero di sezioni (gruppi di elementi) che consente di visualizzare la raccolta. In questo caso, è presente un solo:

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    return 1;
}
```

Successivamente, viene restituito il numero di elementi insieme usando il codice seguente:

```csharp
public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    return Cities.Count;
}
```

Infine, abbiamo di rimozione dalla coda una cella riutilizzabile quando richiede la visualizzazione di raccolta con il codice seguente:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
    var city = Cities [indexPath.Row];

    // Initialize city
    cityCell.City = city;

    return cityCell;
}
```

Dopo avere ottenuto una cella di visualizzazione di raccolta del nostro `CityCollectionViewCell` tipo, si viene popolato con l'elemento specificato.

<a name="Responding-to-User-Events" />

## <a name="responding-to-user-events"></a>Risposta agli eventi utente

Dal momento che l'utente sia in grado di selezionare un elemento dalla raccolta, è necessario fornire un delegato di visualizzazione raccolta per gestire questa interazione. E abbiamo bisogno per offrire un modo per consentire la visualizzazione chiamante conosce l'utente di selezionare l'elemento è selezionato.

<a name="The-App-Delegate" />

### <a name="the-app-delegate"></a>Delegato dell'App

È necessario un modo per correlare l'elemento attualmente selezionato dalla visualizzazione raccolta tornare alla visualizzazione chiama. Si userà una proprietà personalizzata nel nostro `AppDelegate`. Modificare il `AppDelegate.cs` file e aggiungere il codice seguente:

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

Questo definisce le proprietà e imposta la città predefinito che verrà inizialmente visualizzata. In un secondo momento, si sarà utilizzano questa proprietà per visualizzare la selezione dell'utente e consente di selezionare da modificare.

<a name="The-Collection-View-Delegate" />

### <a name="the-collection-view-delegate"></a>Il delegato di visualizzazione raccolta

Successivamente, aggiungere un nuovo `CityViewDelegate` classe al progetto e renderlo simile al seguente:


```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace tvCollection
{
    public class CityViewDelegate : UICollectionViewDelegateFlowLayout
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public CityViewDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
        {
            return new CGSize (361, 256);
        }

        public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
        {
            if (indexPath == null) {
                return false;
            } else {
                var controller = collectionView as CityCollectionView;
                return controller.Source.Cities[indexPath.Row].CanSelect;
            }
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var controller = collectionView as CityCollectionView;
            App.SelectedCity = controller.Source.Cities [indexPath.Row];

            // Close Collection
            controller.ParentController.DismissViewController(true,null);
        }
        #endregion
    }
}
```

Diamo uno sguardo a questa classe. In primo luogo, si ereditano da `UICollectionViewDelegateFlowLayout`. Il motivo per cui abbiamo ereditano da questa classe e non il `UICollectionViewDelegate` è che si sta usando l'elemento predefinito `UICollectionViewFlowLayout` per presentare gli elementi e non è un tipo di layout personalizzato.

Successivamente, viene restituito le dimensioni per i singoli elementi con questo codice:

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

Quindi, decidere se una cella specificata è possibile ottenere lo stato attivo usando il codice seguente: 

```csharp
public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
{
    if (indexPath == null) {
        return false;
    } else {
        var controller = collectionView as CityCollectionView;
        return controller.Source.Cities[indexPath.Row].CanSelect;
    }
}
```

È possibile controllare se dispone di una determinata parte dei dati di backup relativi `CanSelect` flag impostato su `true` e restituiscono tale valore. Per altre informazioni sulla navigazione e lo stato attivo, vedere la [utilizzo di navigazione e lo stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) e [controller Bluetooth e remoti per Siri](~/ios/tvos/platform/remote-bluetooth.md) documentazione.

Infine, Microsoft risponde all'utente la selezione di un elemento con il codice seguente:

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    var controller = collectionView as CityCollectionView;
    App.SelectedCity = controller.Source.Cities [indexPath.Row];

    // Close Collection
    controller.ParentController.DismissViewController(true,null);
}
```

Qui vengono impostati i `SelectedCity` proprietà del nostro `AppDelegate` all'elemento che l'utente ha selezionato e si chiude il Controller di visualizzazione di raccolta, tornare alla visualizzazione che ha chiamato ci. Non è stata definita la `ParentController` proprietà di visualizzazione raccolta ancora, faremo che successivamente.

<a name="Configuring-the-Collection-View" />

## <a name="configuring-the-collection-view"></a>Configurare la visualizzazione di raccolta

A questo punto è necessario modificare la visualizzazione di raccolta e assegnare l'origine dati e il delegato. Modificare il `CityCollectionView.cs` file (create automaticamente dal nostro Storyboard) e renderlo simile al seguente:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionView : UICollectionView
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public CityViewDatasource Source {
            get { return DataSource as CityViewDatasource;}
        }

        public CityCollectionViewController ParentController { get; set;}
        #endregion

        #region Constructors
        public CityCollectionView (IntPtr handle) : base (handle)
        {
            // Initialize
            RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
            DataSource = new CityViewDatasource (this);
            Delegate = new CityViewDelegate ();
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections ()
        {
            return 1;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
            if (previousItem != null) {
                Animate (0.2, () => {
                    previousItem.CityTitle.Alpha = 0.0f;
                });
            }

            var nextItem = context.NextFocusedView as CityCollectionViewCell;
            if (nextItem != null) {
                Animate (0.2, () => {
                    nextItem.CityTitle.Alpha = 1.0f;
                });
            }
        }
        #endregion
    }
}
```

In primo luogo, viene fornito un collegamento per accedere a nostro `AppDelegate`: 

```csharp
public static AppDelegate App {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

È quindi necessario fornire un collegamento all'origine di dati della vista di raccolta e una proprietà per accedere al Controller di visualizzazione di raccolta (usato per chiudere la raccolta quando l'utente effettua una selezione dal nostro delegato precedente):

```csharp
public CityViewDatasource Source {
    get { return DataSource as CityViewDatasource;}
}

public CityCollectionViewController ParentController { get; set;}
```

Quindi, utilizziamo il codice seguente per inizializzare la visualizzazione di raccolta e assegnare la classe di cella, origine dati e il delegato:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    DataSource = new CityViewDatasource (this);
    Delegate = new CityViewDelegate ();
}
```

Infine, si decide il titolo sotto l'immagine sia visibile solo quando l'utente lo ha evidenziato (messa a fuoco). Tale scopo, eseguire il codice seguente:

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
    if (previousItem != null) {
        Animate (0.2, () => {
            previousItem.CityTitle.Alpha = 0.0f;
        });
    }

    var nextItem = context.NextFocusedView as CityCollectionViewCell;
    if (nextItem != null) {
        Animate (0.2, () => {
            nextItem.CityTitle.Alpha = 1.0f;
        });
    }
}
```

Impostiamo il transparence del precedente elemento perde lo stato attivo a zero (0) e transparence dell'elemento successivo vengono attivati al 100%. Introduzione animata anche questi transizione.


## <a name="configuring-the-collection-view-controller"></a>Configurazione del Controller di visualizzazione raccolta

A questo punto è necessario eseguire la configurazione finale nella visualizzazione raccolta e consentire al controller di impostare la proprietà che è stato definito in modo che la visualizzazione di raccolta può essere chiusa dopo che l'utente effettua una selezione.

Modificare il `CityCollectionViewController.cs` file (creato automaticamente dal nostro Storyboard) e renderlo simile al seguente:

```csharp
// This file has been autogenerated from a class added in the UI designer.

using System;

using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionViewController : UICollectionViewController
    {
        #region Computed Properties
        public CityCollectionView Collection {
            get { return CollectionView as CityCollectionView; }
        }
        #endregion

        #region Constructors
        public CityCollectionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Save link to controller
            Collection.ParentController = this;
        }
        #endregion
    }
}

```

## <a name="putting-it-all-together"></a>Riepilogo 

Ora che abbiamo tutte le parti raccolto per popolare e controllare la visualizzazione di raccolta, è necessario apportare le modifiche finale per la visualizzazione principale per riunire tutti gli elementi.

Modificare il `ViewController.cs` file (creato automaticamente dal nostro Storyboard) e renderlo simile al seguente:

```csharp
using System;
using Foundation;
using UIKit;
using tvCollection;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update image with the currently selected one
            CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
            BackgroundView.Image = CityView.Image;
            CityTitle.Text = App.SelectedCity.Title;
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

Il codice seguente viene visualizzato inizialmente l'elemento selezionato dal `SelectedCity` proprietà del `AppDelegate` e viene visualizzata nuovamente quando l'utente ha effettuato una selezione dalla visualizzazione di raccolta:

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update image with the currently selected one
    CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
    BackgroundView.Image = CityView.Image;
    CityTitle.Text = App.SelectedCity.Title;
}
```

<a name="Testing-the-app" />

## <a name="testing-the-app"></a>Test dell'app

Con tutti gli elementi sul posto, se si compila e si esegue l'app, la visualizzazione principale viene visualizzata con la città predefinito:

[![](collection-views-images/run01.png "Nella schermata principale")](collection-views-images/run01.png#lightbox)

Se l'utente fa clic il **selezionare una visualizzazione** verrà visualizzato sul pulsante, la visualizzazione di raccolta:

[![](collection-views-images/run02.png "La visualizzazione di raccolta")](collection-views-images/run02.png#lightbox)

Qualsiasi città con relativo `CanSelect` proprietà impostata su `false` verranno visualizzati in grigio e l'utente non sarà in grado di impostare lo stato attivo a esso. Quando l'utente viene evidenziato un elemento (renderlo messa a fuoco) viene visualizzato il titolo e possono usare l'effetto di parallasse per inclinazione importante sottolineare che l'immagine in 3D.

Quando l'utente fa clic su un'immagine di select, la visualizzazione di raccolta viene chiusa e la visualizzazione principale viene visualizzata di nuovo con la nuova immagine:

[![](collection-views-images/run03.png "Una nuova immagine della schermata iniziale")](collection-views-images/run03.png#lightbox)

<a name="Creating-Custom-Layout-and-Reordering-Items" />

## <a name="creating-custom-layout-and-reordering-items"></a>La creazione di Layout personalizzato e il riordinamento degli elementi

Una delle funzionalità principali dell'uso di una visualizzazione di raccolta è la possibilità di creare layout personalizzati. Poiché tvOS eredita da iOS, il processo per la creazione di un layout personalizzato è lo stesso. Vedere la [Introduzione alle visualizzazioni di raccolta](~/ios/user-interface/controls/uicollectionview.md) per altre informazioni.

Ha recentemente aggiunto alle visualizzazioni di raccolta per iOS 9 è la possibilità di consentire facilmente il riordinamento degli elementi nella raccolta. Anche in questo caso, poiché tvOS 9 è un subset di iOS 9, questa operazione viene eseguita essi nello stesso modo. Vedere la [le modifiche alla vista raccolta](~/ios/user-interface/controls/uicollectionview.md) documento per altri dettagli.


<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la progettazione e utilizzo delle visualizzazioni raccolta all'interno di un'app xamarin. tvos. In primo luogo, è stato illustrato tutti gli elementi che costituiscono la visualizzazione di raccolta. Successivamente, è stato illustrato come progettare e implementare una vista di raccolta utilizzando uno Storyboard. Infine, viene fornito collegamenti per informazioni su come creare layout personalizzati e riordinamento degli elementi.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
