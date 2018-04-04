---
title: Utilizzo delle visualizzazioni di raccolta
description: Questo articolo descrive la progettazione e l'utilizzo di viste di raccolta all'interno di un'app Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 7fa38aa81e5929bdc88ceebd153d86cfcd92f20e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-collection-views"></a>Utilizzo delle visualizzazioni di raccolta

_Questo articolo descrive la progettazione e l'utilizzo di viste di raccolta all'interno di un'app Xamarin.tvOS._

Viste di raccolta consentono di un gruppo di contenuto deve essere visualizzato utilizzando layout arbitrario. Con il supporto incorporato, consentono i layout di griglia o lineare semplice creazione, supporta i layout personalizzati.

[![](collection-views-images/collection01.png "Visualizzazione di raccolta di esempio")](collection-views-images/collection01.png#lightbox)

La visualizzazione raccolta gestisce una raccolta di elementi tramite un delegato e un'origine dati per fornire l'interazione dell'utente e il contenuto della raccolta. Poiché la visualizzazione della raccolta è basata su un sottosistema di Layout che è indipendente dalla vista stessa, che fornisce un Layout diverso è possibile modificare facilmente la presentazione di dati di volo della vista di raccolta.

<a name="About-Collection-Views" />

## <a name="about-collection-views"></a>Informazioni sulle visualizzazioni di raccolta

Come descritto in precedenza, una visualizzazione di raccolta (`UICollectionView`) gestisce una raccolta ordinata di elementi e presenta gli elementi di layout personalizzabile. Viste di raccolta di lavoro in modo simile alle visualizzazioni di tabella (`UITableView`), ma possono usare il layout di elementi presenti nella più di una sola colonna.

Quando si utilizza una visualizzazione di raccolta tvOS, l'applicazione è responsabile di fornire i dati associati alla raccolta utilizzando un'origine dati (`UICollectionViewDataSource`). Dati della visualizzazione raccolta possono facoltativamente essere organizzati e presentati in gruppi diversi (sezioni).

La visualizzazione di raccolta presenta i singoli elementi sullo schermo utilizzando una cella (`UICollectionViewCell`) che fornisce la presentazione di un'informazione specificata dalla raccolta (ad esempio un'immagine e il relativo titolo).

Facoltativamente, è possibile aggiungere visualizzazioni supplementari per la presentazione della visualizzazione della raccolta di agire come intestazione e piè di pagina per le sezioni e celle. Layout della visualizzazione della raccolta è responsabile per la definizione della posizione di queste visualizzazioni e le singole celle.

La visualizzazione raccolta può rispondere all'interazione dell'utente tramite un delegato (`UICollectionViewDelegate`). Questo delegato è inoltre responsabile per determinare se una cella specificata è possibile ottenere lo stato attivo, se è stata evidenziata una cella o se uno è stato selezionato. In alcuni casi, il delegato determina le dimensioni delle singole celle.

<a name="Collection-View-Layouts" />

## <a name="collection-view-layouts"></a>Layout della visualizzazione di raccolta

Una funzionalità chiave di una visualizzazione di raccolta è la separazione tra i dati presentati e il relativo Layout. Un Layout di visualizzazione di raccolta (`UICollectionViewLayout`) è responsabile di fornire l'organizzazione e la posizione delle celle (e le viste supplementari) con presentazione sullo schermo della visualizzazione raccolta.

Le singole celle vengono create la vista di raccolta dall'origine dati associata sono disposti e visualizzate per la visualizzazione di raccolta specificato.

La visualizzazione di raccolta vengono in genere fornita quando viene creata la visualizzazione della raccolta. Tuttavia, è possibile modificare il Layout di visualizzazione di raccolta in qualsiasi momento e la presentazione dei dati della visualizzazione della raccolta sullo schermo verrà aggiornata automaticamente con il nuovo layout fornito.

La visualizzazione di raccolta fornisce diversi metodi che possono essere utilizzati per animare la transizione tra due layout diversi (per impostazione predefinita che non viene eseguita alcuna animazione). Layout della visualizzazione raccolta può inoltre utilizzare i riconoscitori di movimento animare ulteriormente l'interazione dell'utente che comporta una modifica nel layout.

<a name="Creating-Cells-and-Supplementary-Views" />

## <a name="creating-cells-and-supplementary-views"></a>Creazione di celle e visualizzazioni supplementari

Origine dati della visualizzazione di una raccolta non è solo responsabile di fornire i dati di backup della raccolta elementi, ma anche le celle che consentono di visualizzare il contenuto.

Poiché le visualizzazioni di raccolta sono state progettate per gestire grandi raccolte di elementi, le singole celle possono essere rimosso dalla coda e riutilizzate per evitare di sovraccaricare i limiti di memoria. Esistono due metodi diversi per le visualizzazioni di annullamento dell'accodamento:

- `DequeueReusableCell` -Crea o restituisce una cella del tipo specificato (come specificato in uno Storyboard dell'app).
- `DequeueReusableSupplementaryView` -Crea o restituisce una visualizzazione supplementare del tipo specificato (come specificato in uno Storyboard dell'app).

Prima di chiamare uno di questi metodi, è necessario registrare la classe, uno Storyboard o `.xib` file utilizzato per creare la visualizzazione della cella con la visualizzazione della raccolta. Ad esempio:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

Dove `typeof(CityCollectionViewCell)` fornisce la classe che supporta la visualizzazione e `CityViewDatasource.CardCellId` fornisce l'ID utilizzato quando la cella (o vista) viene rimosso dalla coda.

Dopo la cella viene rimossa dalla coda, configurarlo con i dati per l'elemento rappresentato e tornare alla visualizzazione di raccolta per la visualizzazione.

<a name="About-Collection-View-Controllers" />

## <a name="about-collection-view-controllers"></a>Sui controller di visualizzazione di raccolta

Un Controller di visualizzazione di raccolta (`UICollectionViewController`) è un Controller di visualizzazione specializzate (`UIViewController`) che fornisce il comportamento seguente:

- È responsabile del caricamento la visualizzazione della raccolta dello Storyboard o `.xib` di file e di creazione della visualizzazione. Se è stato creato nel codice, viene creato automaticamente una nuova vista raccolta non configurato.
- Dopo aver caricata la visualizzazione della raccolta, il controller tenta di caricare relativa origine dati e il delegato da uno Storyboard o `.xib` file. Se non sono disponibili, viene impostato come origine di entrambi.
- Assicura che i dati vengono caricati prima che la visualizzazione della raccolta viene popolata visualizzato, primo e ricarica e cancellare l'istruzione select su ogni successiva visualizzazione.

Inoltre, il Controller di visualizzazione di raccolta fornisce metodi sottoponibili a override che possono essere usati per gestire il ciclo di vita della visualizzazione di raccolta, ad esempio `AwakeFromNib` e `ViewWillDisplay`.

<a name="Collection-Views-and-Storyboards" />

## <a name="collection-views-and-storyboards"></a>Viste di raccolta e di storyboard

Il modo più semplice per utilizzare una visualizzazione di raccolta nell'app Xamarin.tvOS è per aggiungerne uno al relativo Storyboard. Come esempio semplice, verrà per creare un'app di esempio che visualizza un'immagine, titolo e un pulsante Seleziona. Se l'utente fa clic sul pulsante Seleziona, verrà visualizzata una visualizzazione di raccolta che consentono all'utente di scegliere una nuova immagine. Quando si sceglie un'immagine, la visualizzazione della raccolta viene chiusa e verrà visualizzate la nuova immagine e il titolo.

Di seguito, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

    
1. Avviare una nuova **tvOS singola visualizzazione App** in Visual Studio per Mac.
1. Nel **Esplora**, fare doppio clic su di `Main.storyboard` e aprirlo nella finestra di progettazione iOS.
1. Aggiungere una visualizzazione di immagini, un'etichetta e un pulsante per la vista esistente e configurarli in modo simile al seguente: 

    [![](collection-views-images/collection02.png "Layout di esempio")](collection-views-images/collection02.png#lightbox)
1. Assegnare un **nome** per la visualizzazione di immagini e l'etichetta nel **scheda Widget** del **Esplora proprietà**. Ad esempio: 

    [![](collection-views-images/collection03.png "L'impostazione del nome")](collection-views-images/collection03.png#lightbox)
1. Successivamente, trascinare un Controller di visualizzazione di raccolta dello Storyboard: 

    [![](collection-views-images/collection04.png "Un Controller di visualizzazione di raccolta")](collection-views-images/collection04.png#lightbox)
1. Trascinamento del controllo pulsante al Controller di visualizzazione di raccolta e selezionare **Push** nel popup: 

    [![](collection-views-images/collection05.png "Selezionare la finestra popup di Push")](collection-views-images/collection05.png#lightbox)
1. Quando si esegue l'app, in questo modo la visualizzazione della raccolta da visualizzare quando l'utente fa clic sul pulsante.
1. Selezionare la visualizzazione di raccolta e immettere i valori seguenti nel **scheda Layout** del **Esplora proprietà**: 

    [![](collection-views-images/collection06.png "Esplora risorse di proprietà")](collection-views-images/collection06.png#lightbox)
1. Controlla le dimensioni delle singole celle e i bordi tra le celle e il bordo esterno della visualizzazione di raccolta.
1. Selezionare il Controller di visualizzazione di raccolta e impostare la relativa classe `CityCollectionViewController` nel **scheda Widget**: 

    [![](collection-views-images/collection07.png "Impostare la classe su CityCollectionViewController")](collection-views-images/collection07.png#lightbox)
1. Selezionare la visualizzazione di raccolta e impostare la relativa classe `CityCollectionView` nel **scheda Widget**: 

    [![](collection-views-images/collection08.png "Impostare la classe su CityCollectionView")](collection-views-images/collection08.png#lightbox)
1. Selezionare la cella di visualizzazione di raccolta e impostare la relativa classe `CityCollectionViewCell` nel **scheda Widget**: 

    [![](collection-views-images/collection09.png "Impostare la classe su CityCollectionViewCell")](collection-views-images/collection09.png#lightbox)
1. Nel **scheda Widget** assicurarsi che il **Layout** è `Flow` e **direzione di scorrimento** è `Vertical` per la visualizzazione di raccolta: 

    [![](collection-views-images/collection10.png "La scheda Widget")](collection-views-images/collection10.png#lightbox)
1. Selezionare la cella di visualizzazione di raccolta e impostare il relativo **identità** a `CityCell` nel **scheda Widget**: 

    [![](collection-views-images/collection11.png "Impostare l'identità su CityCell")](collection-views-images/collection11.png#lightbox)
1. Salvare le modifiche.
    

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

    
1. Avviare una nuova **tvOS singola visualizzazione App** in Visual Studio.
1. Nel **Esplora**, fare doppio clic su di `Main.storyboard` e aprirlo nella finestra di progettazione iOS.
1. Aggiungere una visualizzazione di immagini, un'etichetta e un pulsante per la vista esistente e configurarli in modo simile al seguente: 

    [![](collection-views-images/collection02vs.png "Configurare il layout")](collection-views-images/collection02vs.png#lightbox)
1. Assegnare un **nome** per la visualizzazione di immagini e l'etichetta nel **scheda Widget** del **Esplora proprietà**. Ad esempio: 

    [![](collection-views-images/collection03vs.png "Esplora risorse di proprietà")](collection-views-images/collection03vs.png#lightbox)
1. Successivamente, trascinare un Controller di visualizzazione di raccolta dello Storyboard: 

    [![](collection-views-images/collection04vs.png "Un Controller di visualizzazione di raccolta")](collection-views-images/collection04vs.png#lightbox)
1. Trascinamento del controllo pulsante al Controller di visualizzazione di raccolta e selezionare **Push** nel popup: 

    [![](collection-views-images/collection05vs.png "Selezionare la finestra popup di Push")](collection-views-images/collection05vs.png#lightbox)
1. Quando si esegue l'app, in questo modo la visualizzazione della raccolta da visualizzare quando l'utente fa clic sul pulsante.
1. Selezionare la visualizzazione di raccolta e il **scheda Layout** del **Esplora proprietà** immettere il **larghezza** come _361_ e  **Altezza** come _256_ 
1. Controlla le dimensioni delle singole celle e i bordi tra le celle e il bordo esterno della visualizzazione di raccolta.
1. Selezionare il Controller di visualizzazione di raccolta e impostare la relativa classe `CityCollectionViewController` nel **scheda Widget**: 

    [![](collection-views-images/collection07vs.png "Impostare la classe su CityCollectionViewController")](collection-views-images/collection07vs.png#lightbox)
1. Selezionare la visualizzazione di raccolta e impostare la relativa classe `CityCollectionView` nel **scheda Widget**: 

    [![](collection-views-images/collection08vs.png "Impostare la classe su CityCollectionView")](collection-views-images/collection08vs.png#lightbox)
1. Selezionare la cella di visualizzazione di raccolta e impostare la relativa classe `CityCollectionViewCell` nel **scheda Widget**: 

    [![](collection-views-images/collection09vs.png "Impostare la classe su CityCollectionViewCell")](collection-views-images/collection09vs.png#lightbox)
1. Nel **scheda Widget** assicurarsi che il **Layout** è `Flow` e **direzione di scorrimento** è `Vertical` per la visualizzazione di raccolta: 

    [![](collection-views-images/collection10vs.png "Scheda TImpossibile Widget")](collection-views-images/collection10vs.png#lightbox)
1. Selezionare la cella di visualizzazione di raccolta e impostare il relativo **identità** a `CityCell` nel **scheda Widget**: 

    [![](collection-views-images/collection11vs.png "Impostare l'identità su CityCell")](collection-views-images/collection11vs.png#lightbox)
1. Salvare le modifiche.
    

-----

Se fosse stata scelta `Custom` per la visualizzazione di raccolta **Layout**, è possibile specificare un layout personalizzato. Apple fornisce un oggetto incorporato `UICollectionViewFlowLayout` e `UICollectionViewDelegateFlowLayout` che può presentare facilmente i dati in un layout di griglia (questi vengono utilizzati dal `flow` lo stile di layout). 

Per ulteriori informazioni sull'uso di storyboard, consultare il nostro [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md).

<a name="Providing-Data-for-the-Collection-View" />

## <a name="providing-data-for-the-collection-view"></a>Fornisce dati per la visualizzazione di raccolta

Ora che è disponibile la visualizzazione di raccolta (e raccolta View Controller) aggiunto a questo Storyboard, è necessario fornire i dati per la raccolta. 

<a name="The-Data-Model" />

### <a name="the-data-model"></a>Il modello di dati

Verrà innanzitutto, creare un modello per i dati che contiene il nome del file per l'immagine da visualizzare, il titolo e un flag per consentire la città essere selezionato.

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

### <a name="the-collection-view-cell"></a>La cella di visualizzazione di raccolta

È necessario definire come i dati saranno presentati per ogni cella. Modificare il `CityCollectionViewCell.cs` file (creato automaticamente dal file di Storyboard) e renderlo simile al seguente:

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

Per l'app tvOS, si prevede di visualizzare un'immagine e il titolo di un parametro facoltativo. Se la città specificata non può essere selezionata, ci stiamo luminosità la visualizzazione di immagini tramite il codice seguente:

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

Quando la cella contenente l'immagine è attivata la modalità messa a fuoco dall'utente, si desidera utilizzare l'oggetto incorporato effetto di parallasse su di esso impostando la proprietà seguente:

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

Per ulteriori informazioni sulla navigazione e lo stato attivo, consultare il nostro [utilizzo di navigazione e lo stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) e [Siri remoto e i controller Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentazione.


<a name="The-Collection-View-Data-Provider" />

### <a name="the-collection-view-data-provider"></a>Il Provider di dati di visualizzazione di raccolta

Con il modello di dati creato e definito il layout della cella, creare un'origine dati per la visualizzazione della raccolta. L'origine dati sarà responsabile di fornire non solo i dati di backup, ma anche rimozione le celle per visualizzare le singole celle sullo schermo.

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

Consente di esaminare in dettaglio questa classe. In primo luogo, si ereditano da `UICollectionViewDataSource` e fornire un collegamento all'ID di celle (che è assegnati nella finestra di progettazione iOS):

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

È quindi fornire l'archiviazione per i dati di raccolta e fornire una classe per popolare i dati:

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

È quindi sostituire il `NumberOfSections` metodo e restituire il numero di sezioni (gruppi di elementi) che consente di visualizzare la raccolta ha. In questo caso, è solo uno:

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    return 1;
}
```

Successivamente, restituiamo il numero di elementi dell'insieme utilizzando il codice seguente:

```csharp
public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    return Cities.Count;
}
```

Infine, una cella riutilizzabile è dequeue quando richiede la visualizzazione della raccolta con il codice seguente:

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

Dopo avere una cella di visualizzazione di raccolta di nostri `CityCollectionViewCell` tipo, si viene popolato con l'elemento specificato.

<a name="Responding-to-User-Events" />

## <a name="responding-to-user-events"></a>Risposta agli eventi utente

Dal momento che l'utente sia in grado di selezionare un elemento dall'insieme, è necessario fornire un delegato di visualizzazione di raccolta per gestire questa interazione. Ed è necessario fornire un modo per consentire la visualizzazione chiamante sapere quale elemento l'utente ha selezionato.

<a name="The-App-Delegate" />

### <a name="the-app-delegate"></a>Il delegato di App

È necessario un modo per correlare l'elemento attualmente selezionato dalla visualizzazione raccolta tornare alla visualizzazione di chiamata. Una proprietà personalizzata verrà usato nel nostro `AppDelegate`. Modificare il `AppDelegate.cs` file e aggiungere il codice seguente:

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

Questo definisce le proprietà e si imposta la città predefinito che verrà inizialmente visualizzata. Si sarà in un secondo momento, utilizzare questa proprietà per visualizzare la selezione dell'utente e consente di selezionare da modificare.

<a name="The-Collection-View-Delegate" />

### <a name="the-collection-view-delegate"></a>Il delegato di visualizzazione di raccolta

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

Diamo un'occhiata a questa classe. In primo luogo, si ereditano da `UICollectionViewDelegateFlowLayout`. Il motivo per cui si eredita da questa classe e non il `UICollectionViewDelegate` è che si sta usando l'elemento predefinito `UICollectionViewFlowLayout` per presentare i elementi e non è un tipo di layout personalizzato.

Successivamente, si restituiscono le dimensioni per i singoli elementi con questo codice:

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

Quindi, si decide se una cella specificata è possibile ottenere lo stato attivo tramite il codice seguente: 

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

È possibile controllare se una determinata parte dei dati di backup è relativo `CanSelect` flag impostato su `true` e restituiscono tale valore. Per ulteriori informazioni sulla navigazione e lo stato attivo, consultare il nostro [utilizzo di navigazione e lo stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) e [Siri remoto e i controller Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentazione.

Infine, rispondere all'utente la selezione di un elemento con il codice seguente:

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    var controller = collectionView as CityCollectionView;
    App.SelectedCity = controller.Source.Cities [indexPath.Row];

    // Close Collection
    controller.ParentController.DismissViewController(true,null);
}
```

Qui viene impostato il `SelectedCity` proprietà del nostro `AppDelegate` all'elemento che l'utente ha selezionato e chiudere il Controller di visualizzazione di raccolta, tornare alla visualizzazione che ha chiamato ci. Non è stata definita la `ParentController` proprietà di ancora la visualizzazione di raccolta, faremo che successivamente.

<a name="Configuring-the-Collection-View" />

## <a name="configuring-the-collection-view"></a>Configurazione della vista di raccolta

A questo punto è necessario modificare la visualizzazione di raccolta e assegnare l'origine dati e il delegato. Modificare il `CityCollectionView.cs` file (create automaticamente da questo Storyboard) e renderlo simile al seguente:

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

In primo luogo, si fornisce un collegamento per accedere a questo `AppDelegate`: 

```csharp
public static AppDelegate App {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

È quindi necessario fornire un collegamento all'origine di dati della visualizzazione della raccolta e una proprietà per accedere al Controller di visualizzazione di raccolta (utilizzato per chiudere la raccolta quando l'utente effettua una selezione da questo delegato precedente):

```csharp
public CityViewDatasource Source {
    get { return DataSource as CityViewDatasource;}
}

public CityCollectionViewController ParentController { get; set;}
```

Quindi, è il codice seguente per inizializzare la visualizzazione della raccolta e assegnare la classe di cella, l'origine dati e delegato:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    DataSource = new CityViewDatasource (this);
    Delegate = new CityViewDelegate ();
}
```

Infine, è necessario il titolo sotto l'immagine sia visibile solo quando l'utente è evidenziato (attivo). Facciamo con il codice seguente:

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

Impostiamo transparence dell'elemento precedente perde lo stato attivo su zero (0) e transparence dell'elemento successivo ottenere lo stato attivo al 100%. Ottenere un'animazione a anche questi transizione.


## <a name="configuring-the-collection-view-controller"></a>Configurazione del Controller di visualizzazione di raccolta

A questo punto è necessario eseguire la configurazione finale la vista di raccolta e consentire al controller di impostare la proprietà che è stato definito in modo che la visualizzazione della raccolta può essere chiusa dopo che l'utente effettua una selezione.

Modificare il `CityCollectionViewController.cs` file (creato automaticamente da questo Storyboard) e renderlo simile al seguente:

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

Ora che si dispone di tutte le parti di mettere insieme per popolare e controllare la visualizzazione di raccolta, è necessario apportare le modifiche finale per la visualizzazione principale per tutto il raggruppamento.

Modificare il `ViewController.cs` file (creato automaticamente da questo Storyboard) e renderlo simile al seguente:

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

Il codice seguente viene visualizzato inizialmente l'elemento selezionato dal `SelectedCity` proprietà del `AppDelegate` e viene nuovamente visualizzato quando l'utente ha effettuato una selezione dalla visualizzazione di raccolta:

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

[![](collection-views-images/run01.png "La schermata principale")](collection-views-images/run01.png#lightbox)

Se l'utente sceglie il **selezionare una visualizzazione** pulsante, la visualizzazione della raccolta verrà visualizzati:

[![](collection-views-images/run02.png "La visualizzazione della raccolta")](collection-views-images/run02.png#lightbox)

Qualsiasi città con relativo `CanSelect` proprietà impostata su `false` verrà visualizzato in grigio e l'utente non sarà in grado di impostare lo stato attivo. Quando l'utente evidenziato un elemento (renderlo messa a fuoco) viene visualizzato il titolo e usano l'effetto di parallasse per inclinazione abbastanza particolare l'immagine in 3D.

Quando l'utente sceglie di selezionare l'immagine, la visualizzazione della raccolta viene chiusa e la visualizzazione principale viene visualizzata nuovamente con la nuova immagine:

[![](collection-views-images/run03.png "Una nuova immagine nella schermata iniziale")](collection-views-images/run03.png#lightbox)

<a name="Creating-Custom-Layout-and-Reordering-Items" />

## <a name="creating-custom-layout-and-reordering-items"></a>Creazione di un Layout personalizzato e il riordino degli elementi

Uno dei principali dell'utilizzo di una vista di raccolta è la possibilità di creare un layout personalizzato. Poiché tvOS eredita da iOS, il processo per la creazione di un layout personalizzato è lo stesso. Consultare il nostro [Introduzione alle visualizzazioni di raccolta](~/ios/user-interface/controls/uicollectionview.md) documentazione per ulteriori informazioni.

Recentemente aggiunte alle viste di raccolta per iOS 9 è la possibilità di consentire il raggruppamento degli elementi nella raccolta. Nuovamente, poiché tvOS 9 è un subset di iOS 9, questa operazione viene eseguita li allo stesso modo. Consultare il nostro [raccolta Visualizza modifiche](~/ios/user-interface/controls/uicollectionview.md) documento per altri dettagli.


<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è descritta la progettazione e l'utilizzo di viste di raccolta all'interno di un'app Xamarin.tvOS. In primo luogo, illustrati tutti gli elementi che costituiscono la visualizzazione della raccolta. Successivamente, è stato spiegato come progettare e implementare una vista di raccolta utilizzando uno Storyboard. Infine, viene fornito collegamenti a informazioni sulla creazione di layout personalizzati e riordinamento degli elementi.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
