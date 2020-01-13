---
title: Uso delle viste di raccolta tvOS in Novell
description: Questo documento descrive come usare le visualizzazioni di raccolta in un'app tvOS compilata con Novell. Vengono illustrati i layout della visualizzazione raccolta, la creazione di celle e visualizzazioni supplementari, la risposta agli eventi utente e altro ancora.
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: be0208accfdc287f93cf635a22c6409cd03483e9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030498"
---
# <a name="working-with-tvos-collection-views-in-xamarin"></a>Uso delle viste di raccolta tvOS in Novell

Le visualizzazioni di raccolta consentono di visualizzare un gruppo di contenuti usando layout arbitrari. Grazie al supporto incorporato, consentono di creare layout semplici, ad esempio griglia o lineare, supportando al tempo stesso layout personalizzati.

[![](collection-views-images/collection01.png "Sample collection view")](collection-views-images/collection01.png#lightbox)

La visualizzazione raccolta gestisce una raccolta di elementi che usano sia un delegato che un'origine dati per fornire l'interazione dell'utente e il contenuto della raccolta. Poiché la visualizzazione raccolta si basa su un sottosistema di layout indipendente dalla visualizzazione stessa, fornire un layout diverso può facilmente modificare la presentazione dei dati della visualizzazione raccolta in tempo reale.

<a name="About-Collection-Views" />

## <a name="about-collection-views"></a>Informazioni sulle visualizzazioni di raccolta

Come indicato in precedenza, una visualizzazione raccolta (`UICollectionView`) gestisce una raccolta ordinata di elementi e presenta tali elementi con layout personalizzabili. Le visualizzazioni di raccolta funzionano in modo simile alle visualizzazioni di tabella (`UITableView`), ad eccezione del fatto che possono usare i layout per presentare elementi in più di una sola colonna.

Quando si usa una visualizzazione di raccolta in tvOS, l'app è responsabile di fornire i dati associati alla raccolta usando un'origine dati (`UICollectionViewDataSource`). I dati della visualizzazione raccolta possono essere organizzati e presentati in gruppi diversi (sezioni).

La visualizzazione raccolta Visualizza i singoli elementi sullo schermo usando una cella (`UICollectionViewCell`) che fornisce la presentazione di una determinata parte di informazioni dalla raccolta (ad esempio un'immagine e il relativo titolo).

Facoltativamente, è possibile aggiungere visualizzazioni supplementari alla presentazione della visualizzazione raccolta in modo che fungano da intestazione e piè di pagina per le sezioni e le celle. Il layout della visualizzazione raccolta è responsabile della definizione del posizionamento di queste viste insieme alle singole celle.

La visualizzazione raccolta può rispondere all'interazione dell'utente usando un delegato (`UICollectionViewDelegate`). Questo delegato è inoltre responsabile di determinare se una determinata cella può ottenere lo stato attivo, se una cella è stata evidenziata o se ne è stata selezionata una. In alcuni casi, il delegato determina la dimensione delle singole celle.

<a name="Collection-View-Layouts" />

## <a name="collection-view-layouts"></a>Layout della visualizzazione raccolta

Una funzionalità chiave di una visualizzazione di raccolta è la separazione tra i dati che presenta e il relativo layout. Un layout di visualizzazione raccolta (`UICollectionViewLayout`) è responsabile di fornire l'organizzazione e il percorso delle celle (e di eventuali visualizzazioni aggiuntive) con nella presentazione sullo schermo della visualizzazione raccolta.

Le singole celle vengono create dalla visualizzazione di raccolta dalla relativa origine dati associata e vengono quindi disposte e visualizzate dal layout di visualizzazione raccolta specificato.

Il layout della visualizzazione raccolta viene in genere specificato quando viene creata la visualizzazione raccolta. Tuttavia, è possibile modificare il layout della visualizzazione raccolta in qualsiasi momento e la presentazione sullo schermo dei dati della visualizzazione raccolta verrà aggiornata automaticamente usando il nuovo layout fornito.

Il layout della visualizzazione raccolta fornisce diversi metodi che possono essere usati per aggiungere un'animazione alla transizione tra due layout diversi (per impostazione predefinita, non viene eseguita alcuna animazione). Inoltre, i layout di visualizzazione raccolta possono funzionare con i riconoscitori di movimento per aggiungere un'ulteriore animazione all'interazione dell'utente che comporta una modifica del layout.

<a name="Creating-Cells-and-Supplementary-Views" />

## <a name="creating-cells-and-supplementary-views"></a>Creazione di celle e visualizzazioni supplementari

L'origine dati di una visualizzazione di raccolta non è responsabile solo di fornire i dati che eseguono il backup dell'elemento della raccolta, ma anche le celle utilizzate per visualizzare il contenuto.

Poiché le visualizzazioni di raccolta sono state progettate per gestire grandi raccolte di elementi, le singole celle possono essere rilasciate dalla coda e riutilizzate per evitare la sovraesecuzione delle limitazioni di memoria. Sono disponibili due metodi diversi per la rimozione dalla coda delle visualizzazioni:

- `DequeueReusableCell`: crea o restituisce una cella del tipo specificato (come specificato nello storyboard dell'app).
- `DequeueReusableSupplementaryView`: crea o restituisce una visualizzazione supplementare del tipo specificato (come specificato nello storyboard dell'app).

Prima di chiamare uno di questi metodi, è necessario registrare la classe, lo storyboard o il file di `.xib` utilizzato per creare la visualizzazione della cella con la visualizzazione della raccolta. Esempio:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

Dove `typeof(CityCollectionViewCell)` fornisce la classe che supporta la visualizzazione e `CityViewDatasource.CardCellId` fornisce l'ID utilizzato quando la cella (o la vista) viene rimessa in coda.

Dopo la rimozione dalla coda della cella, è necessario configurarla con i dati per l'elemento che rappresenta e tornare alla visualizzazione della raccolta per la visualizzazione.

<a name="About-Collection-View-Controllers" />

## <a name="about-collection-view-controllers"></a>Informazioni sui controller di visualizzazione raccolta

Un controller di visualizzazione raccolta (`UICollectionViewController`) è un controller di visualizzazione specializzato (`UIViewController`) che fornisce il comportamento seguente:

- È responsabile del caricamento della visualizzazione raccolta dal relativo storyboard o `.xib` file e creazione di un'istanza della vista. Se viene creato nel codice, viene creata automaticamente una nuova visualizzazione di raccolta non configurata.
- Una volta caricata la visualizzazione raccolta, il controller tenta di caricare l'origine dati e il delegato dal file storyboard o `.xib`. Se non ne è disponibile alcuno, viene impostato come origine di entrambi.
- Garantisce che i dati vengano caricati prima che la visualizzazione raccolta venga popolata per la prima volta e ricarichi e cancelli l'operazione SELECT in ogni visualizzazione successiva.

Inoltre, il controller di visualizzazione della raccolta fornisce metodi sottoponibili a override che possono essere utilizzati per gestire il ciclo di vita della visualizzazione della raccolta, ad esempio `AwakeFromNib` e `ViewWillDisplay`.

<a name="Collection-Views-and-Storyboards" />

## <a name="collection-views-and-storyboards"></a>Viste e storyboard della raccolta

Il modo più semplice per usare una visualizzazione di raccolta nell'app Xamarin.tvOS consiste nell'aggiungerne uno allo storyboard. Come esempio rapido, verrà creata un'app di esempio che presenta un'immagine, un titolo e un pulsante di selezione. Se l'utente fa clic sul pulsante Seleziona, viene visualizzata una visualizzazione raccolta che consente all'utente di scegliere una nuova immagine. Quando si sceglie un'immagine, la visualizzazione della raccolta viene chiusa e verrà visualizzata la nuova immagine e il titolo.

Eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Avviare una nuova **app tvOS di visualizzazione singola** in Visual Studio per Mac.
1. Nella **Esplora soluzioni**fare doppio clic sul file `Main.storyboard` e aprirlo in iOS designer.
1. Aggiungere una visualizzazione immagine, un'etichetta e un pulsante alla visualizzazione esistente e configurarli in modo che abbiano un aspetto simile al seguente: 

    [![](collection-views-images/collection02.png "Sample layout")](collection-views-images/collection02.png#lightbox)
1. Assegnare un **nome** alla visualizzazione immagine e l'etichetta nella **scheda Widget** di **Esplora proprietà**. Esempio: 

    [![](collection-views-images/collection03.png "Setting the name")](collection-views-images/collection03.png#lightbox)
1. Trascinare quindi un controller di visualizzazione raccolta nello storyboard: 

    [![](collection-views-images/collection04.png "A Collection View Controller")](collection-views-images/collection04.png#lightbox)
1. Control-trascinare dal pulsante al controller di visualizzazione raccolta e selezionare **push** dal popup: 

    [![](collection-views-images/collection05.png "Select Push from the popup")](collection-views-images/collection05.png#lightbox)
1. Quando si esegue l'app, la visualizzazione della raccolta verrà visualizzata ogni volta che l'utente fa clic sul pulsante.
1. Selezionare la visualizzazione raccolta e immettere i valori seguenti nella **scheda layout** di **Esplora proprietà**: 

    [![](collection-views-images/collection06.png "The Properties Explorer")](collection-views-images/collection06.png#lightbox)
1. Consente di controllare le dimensioni delle singole celle e i bordi tra le celle e il bordo esterno della visualizzazione raccolta.
1. Selezionare il controller di visualizzazione raccolta e impostare la relativa classe su `CityCollectionViewController` nella **scheda Widget**: 

    [![](collection-views-images/collection07.png "Set the class to CityCollectionViewController")](collection-views-images/collection07.png#lightbox)
1. Selezionare la visualizzazione raccolta e impostare la relativa classe su `CityCollectionView` nella **scheda Widget**: 

    [![](collection-views-images/collection08.png "Set the class to CityCollectionView")](collection-views-images/collection08.png#lightbox)
1. Selezionare la cella di visualizzazione raccolta e impostare la relativa classe su `CityCollectionViewCell` nella **scheda Widget**: 

    [![](collection-views-images/collection09.png "Set the class to CityCollectionViewCell")](collection-views-images/collection09.png#lightbox)
1. Nella **scheda Widget** verificare che il **layout** sia `Flow` e che la **direzione di scorrimento** sia `Vertical` per la visualizzazione raccolta: 

    [![](collection-views-images/collection10.png "The Widget Tab")](collection-views-images/collection10.png#lightbox)
1. Selezionare la cella di visualizzazione raccolta e impostarne l' **identità** su `CityCell` nella **scheda Widget**: 

    [![](collection-views-images/collection11.png "Set the Identity to CityCell")](collection-views-images/collection11.png#lightbox)
1. Salvare le modifiche.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Avviare una nuova **app tvOS di visualizzazione singola** in Visual Studio.
1. Nella **Esplora soluzioni**fare doppio clic sul file `Main.storyboard` e aprirlo in iOS designer.
1. Aggiungere una visualizzazione immagine, un'etichetta e un pulsante alla visualizzazione esistente e configurarli in modo che abbiano un aspetto simile al seguente: 

    [![](collection-views-images/collection02vs.png "Configure the layout")](collection-views-images/collection02vs.png#lightbox)
1. Assegnare un **nome** alla visualizzazione immagine e l'etichetta nella **scheda Widget** di **Esplora proprietà**. Esempio: 

    [![](collection-views-images/collection03vs.png "The Properties Explorer")](collection-views-images/collection03vs.png#lightbox)
1. Trascinare quindi un controller di visualizzazione raccolta nello storyboard: 

    [![](collection-views-images/collection04vs.png "A Collection View Controller")](collection-views-images/collection04vs.png#lightbox)
1. Control-trascinare dal pulsante al controller di visualizzazione raccolta e selezionare **push** dal popup: 

    [![](collection-views-images/collection05vs.png "Select Push from the popup")](collection-views-images/collection05vs.png#lightbox)
1. Quando si esegue l'app, la visualizzazione della raccolta verrà visualizzata ogni volta che l'utente fa clic sul pulsante.
1. Selezionare la visualizzazione raccolta e nella **scheda layout** di **Esplora proprietà** immettere la **larghezza** _361_ e l' **altezza** _256_ 
1. Consente di controllare le dimensioni delle singole celle e i bordi tra le celle e il bordo esterno della visualizzazione raccolta.
1. Selezionare il controller di visualizzazione raccolta e impostare la relativa classe su `CityCollectionViewController` nella **scheda Widget**: 

    [![](collection-views-images/collection07vs.png "Set the class to CityCollectionViewController")](collection-views-images/collection07vs.png#lightbox)
1. Selezionare la visualizzazione raccolta e impostare la relativa classe su `CityCollectionView` nella **scheda Widget**: 

    [![](collection-views-images/collection08vs.png "Set the class to CityCollectionView")](collection-views-images/collection08vs.png#lightbox)
1. Selezionare la cella di visualizzazione raccolta e impostare la relativa classe su `CityCollectionViewCell` nella **scheda Widget**: 

    [![](collection-views-images/collection09vs.png "Set the class to CityCollectionViewCell")](collection-views-images/collection09vs.png#lightbox)
1. Nella **scheda Widget** verificare che il **layout** sia `Flow` e che la **direzione di scorrimento** sia `Vertical` per la visualizzazione raccolta: 

    [![](collection-views-images/collection10vs.png "Tthe Widget Tab")](collection-views-images/collection10vs.png#lightbox)
1. Selezionare la cella di visualizzazione raccolta e impostarne l' **identità** su `CityCell` nella **scheda Widget**: 

    [![](collection-views-images/collection11vs.png "Set the Identity to CityCell")](collection-views-images/collection11vs.png#lightbox)
1. Salvare le modifiche.

-----

Se avessimo scelto `Custom` per il **layout**della visualizzazione raccolta, avremmo potuto specificare un layout personalizzato. Apple fornisce un `UICollectionViewFlowLayout` incorporato e `UICollectionViewDelegateFlowLayout` che possono presentare facilmente i dati in un layout basato su griglia (questi vengono usati dallo stile di layout `flow`). 

Per ulteriori informazioni sull'utilizzo degli storyboard, vedere la [Guida introduttiva Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Providing-Data-for-the-Collection-View" />

## <a name="providing-data-for-the-collection-view"></a>Fornire dati per la visualizzazione raccolta

Ora che è stata aggiunta la visualizzazione raccolta (e il controller di visualizzazione raccolta) allo storyboard, è necessario fornire i dati per la raccolta. 

<a name="The-Data-Model" />

### <a name="the-data-model"></a>Modello di dati

In primo luogo, verrà creato un modello per i dati che contengono il nome file per l'immagine da visualizzare, il titolo e un flag per consentire la selezione della città.

Creare una classe `CityInfo` e renderla simile alla seguente:

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

### <a name="the-collection-view-cell"></a>Cella di visualizzazione raccolta

A questo punto è necessario definire come verranno presentati i dati per ogni cella. Modificare il file di `CityCollectionViewCell.cs` (creato automaticamente dal file Storyboard) e renderlo simile al seguente:

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

Per l'app tvOS verrà visualizzata un'immagine e un titolo facoltativo. Se non è possibile selezionare la città specificata, la visualizzazione immagine verrà disabilitata usando il codice seguente:

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

Quando la cella che contiene l'immagine viene messa a fuoco dall'utente, si vuole usare l'effetto di parallasse predefinito su di esso impostando la proprietà seguente:

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

Per ulteriori informazioni sull'esplorazione e lo stato attivo, vedere la documentazione relativa all' [utilizzo dello spostamento e dello stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) e di [Siri remote e Bluetooth Controllers](~/ios/tvos/platform/remote-bluetooth.md) .

<a name="The-Collection-View-Data-Provider" />

### <a name="the-collection-view-data-provider"></a>Visualizzazione raccolta provider di dati

Con il modello di dati creato e il layout di cella definito, creiamo un'origine dati per la visualizzazione di raccolta. L'origine dati sarà responsabile non solo di fornire i dati di supporto, ma anche di rimuovere dalla coda le celle per visualizzare le singole celle sullo schermo.

Creare una classe `CityViewDatasource` e renderla simile alla seguente:

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

Esaminiamo questa classe in modo dettagliato. In primo luogo, si eredita da `UICollectionViewDataSource` e si fornisce un collegamento all'ID celle (assegnato in iOS designer):

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

A questo punto si fornisce l'archiviazione per i dati della raccolta e si fornisce una classe per popolare i dati:

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

Viene quindi eseguito l'override del metodo `NumberOfSections` e viene restituito il numero di sezioni (gruppi di elementi) presenti nella vista di raccolta. In questo caso, è presente un solo:

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    return 1;
}
```

Viene quindi restituito il numero di elementi nella raccolta usando il codice seguente:

```csharp
public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    return Cities.Count;
}
```

Infine, viene rimossa la coda di una cella riutilizzabile quando la vista della raccolta richiede il codice seguente:

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

Dopo aver ottenuto una cella di visualizzazione raccolta del tipo di `CityCollectionViewCell`, viene popolata con l'elemento specificato.

<a name="Responding-to-User-Events" />

## <a name="responding-to-user-events"></a>Risposta agli eventi utente

Poiché si desidera che l'utente sia in grado di selezionare un elemento dalla raccolta, è necessario fornire un delegato di visualizzazione raccolta per gestire questa interazione. Ed è necessario fornire un modo per consentire alla visualizzazione chiamante di conoscere l'elemento selezionato dall'utente.

<a name="The-App-Delegate" />

### <a name="the-app-delegate"></a>Delegato dell'app

È necessario un modo per correlare l'elemento attualmente selezionato dalla visualizzazione della raccolta alla visualizzazione chiamante. Nel `AppDelegate`verrà usata una proprietà personalizzata. Modificare il file di `AppDelegate.cs` e aggiungere il codice seguente:

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

Definisce la proprietà e imposta la città predefinita che verrà visualizzata inizialmente. In seguito verrà utilizzata questa proprietà per visualizzare la selezione dell'utente e consentire la modifica di Select.

<a name="The-Collection-View-Delegate" />

### <a name="the-collection-view-delegate"></a>Delegato della visualizzazione raccolta

Successivamente, aggiungere una nuova classe `CityViewDelegate` al progetto e renderla simile alla seguente:

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

Esaminiamo in dettaglio questa classe. Prima di tutto, viene ereditato da `UICollectionViewDelegateFlowLayout`. Il motivo per cui viene ereditata da questa classe e non da `UICollectionViewDelegate` è che si sta usando il `UICollectionViewFlowLayout` predefinito per presentare gli elementi e non un tipo di layout personalizzato.

Si restituiranno quindi le dimensioni per i singoli elementi usando il codice seguente:

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

Quindi, decidiamo se una determinata cella può ottenere lo stato attivo usando il codice seguente: 

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

Si verifica se un determinato elemento di dati di supporto ha il flag di `CanSelect` impostato su `true` e restituisce tale valore. Per ulteriori informazioni sull'esplorazione e lo stato attivo, vedere la documentazione relativa all' [utilizzo dello spostamento e dello stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) e di [Siri remote e Bluetooth Controllers](~/ios/tvos/platform/remote-bluetooth.md) .

Infine, si risponde all'utente selezionando un elemento con il codice seguente:

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    var controller = collectionView as CityCollectionView;
    App.SelectedCity = controller.Source.Cities [indexPath.Row];

    // Close Collection
    controller.ParentController.DismissViewController(true,null);
}
```

Qui si imposta la proprietà `SelectedCity` della `AppDelegate` sull'elemento selezionato dall'utente e si chiude il controller di visualizzazione raccolta, tornando alla visualizzazione che ha chiamato. Non è stata ancora definita la proprietà `ParentController` della visualizzazione raccolta. questa operazione verrà eseguita successivamente.

<a name="Configuring-the-Collection-View" />

## <a name="configuring-the-collection-view"></a>Configurazione della visualizzazione raccolta

A questo punto è necessario modificare la visualizzazione della raccolta e assegnare l'origine dati e il delegato. Modificare il file di `CityCollectionView.cs` (creato automaticamente dallo storyboard) e renderlo simile al seguente:

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

Per prima cosa, viene fornito un collegamento per accedere ai `AppDelegate`: 

```csharp
public static AppDelegate App {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

Viene quindi fornito un collegamento all'origine dati della visualizzazione raccolta e una proprietà per accedere al controller di visualizzazione raccolta (usato dal delegato precedente per chiudere la raccolta quando l'utente effettua una selezione):

```csharp
public CityViewDatasource Source {
    get { return DataSource as CityViewDatasource;}
}

public CityCollectionViewController ParentController { get; set;}
```

Viene quindi usato il codice seguente per inizializzare la visualizzazione della raccolta e assegnare la classe cella, l'origine dati e il delegato:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    DataSource = new CityViewDatasource (this);
    Delegate = new CityViewDelegate ();
}
```

Infine, si vuole che il titolo sotto l'immagine sia visibile solo quando l'utente lo evidenzia (in stato attivo). Questa operazione viene eseguita con il codice seguente:

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

Si imposta la trasparenza dell'elemento precedente che perde lo stato attivo a zero (0) e la trasparenza dell'elemento successivo ottiene lo stato attivo al 100%. Anche queste transizioni vengono animate.

## <a name="configuring-the-collection-view-controller"></a>Configurazione del controller di visualizzazione raccolta

A questo punto è necessario eseguire la configurazione finale nella visualizzazione raccolta e consentire al controller di impostare la proprietà definita in modo che la visualizzazione raccolta possa essere chiusa dopo che l'utente effettua una selezione.

Modificare il file di `CityCollectionViewController.cs` (creato automaticamente dallo storyboard) e renderlo simile al seguente:

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

## <a name="putting-it-all-together"></a>Riunendoli 

Ora che tutte le parti sono state unite per popolare e controllare la visualizzazione della raccolta, è necessario apportare le modifiche finali alla visualizzazione principale per riunire tutti gli elementi.

Modificare il file di `ViewController.cs` (creato automaticamente dallo storyboard) e renderlo simile al seguente:

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

Il codice seguente visualizza inizialmente l'elemento selezionato dalla proprietà `SelectedCity` della `AppDelegate` e lo visualizza nuovamente quando l'utente ha effettuato una selezione dalla visualizzazione della raccolta:

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

Con tutti gli elementi, se si compila ed esegue l'app, la visualizzazione principale viene visualizzata con la città predefinita:

[![](collection-views-images/run01.png "The main screen")](collection-views-images/run01.png#lightbox)

Se l'utente fa clic sul pulsante **Seleziona visualizzazione** , verrà visualizzata la visualizzazione raccolta:

[![](collection-views-images/run02.png "The collection view")](collection-views-images/run02.png#lightbox)

Qualsiasi città la cui proprietà `CanSelect` impostata su `false` verrà visualizzata in grigio e l'utente non sarà in grado di impostare lo stato attivo. Quando l'utente evidenzia un elemento (impostarlo come attivo), il titolo viene visualizzato e può usare l'effetto di parallasse per inclinare l'immagine in 3D.

Quando l'utente fa clic su un'immagine di selezione, la visualizzazione della raccolta viene chiusa e la visualizzazione principale viene visualizzata nuovamente con la nuova immagine:

[![](collection-views-images/run03.png "A new image on the home screen")](collection-views-images/run03.png#lightbox)

<a name="Creating-Custom-Layout-and-Reordering-Items" />

## <a name="creating-custom-layout-and-reordering-items"></a>Creazione del layout personalizzato e riordinamento degli elementi

Una delle funzionalità principali dell'utilizzo di una visualizzazione di raccolta è la possibilità di creare layout personalizzati. Poiché tvOS eredita da iOS, il processo di creazione di un layout personalizzato è lo stesso. Per ulteriori informazioni, vedere la documentazione [introduttiva per le visualizzazioni di raccolta](~/ios/user-interface/controls/uicollectionview.md) .

Aggiunto di recente alle visualizzazioni di raccolta per iOS 9 è stata la possibilità di consentire facilmente il riordinamento degli elementi nella raccolta. Anche in questo caso, poiché tvOS 9 è un subset di iOS 9, questa operazione viene eseguita nello stesso modo. Per altri dettagli, vedere il documento relativo alle [modifiche della visualizzazione raccolta](~/ios/user-interface/controls/uicollectionview.md) .

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stata illustrata la progettazione e l'uso delle visualizzazioni di raccolta all'interno di un'app Xamarin.tvOS. In primo luogo, sono stati discussi tutti gli elementi che costituiscono la visualizzazione di raccolta. Successivamente, è stato illustrato come progettare e implementare una visualizzazione di raccolta utilizzando uno storyboard. Infine, vengono forniti collegamenti alle informazioni sulla creazione di layout personalizzati e sul riordinamento degli elementi.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
