---
title: Visualizzazioni raccolta in xamarin. IOS
description: Le visualizzazioni di raccolta consentono il contenuto deve essere visualizzato utilizzando layout arbitrario. Consentono di creare con facilità i layout di griglia per impostazione predefinita, supportando anche i layout personalizzati.
ms.prod: xamarin
ms.assetid: F4B85F25-0CB5-4FEA-A3B5-D22FCDC81AE4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: f57f2a2bc17690b7a1e0a72c583b0e94519ca4db
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61079443"
---
# <a name="collection-views-in-xamarinios"></a>Visualizzazioni raccolta in xamarin. IOS

_Le visualizzazioni di raccolta consentono il contenuto deve essere visualizzato utilizzando layout arbitrario. Consentono di creare con facilità i layout di griglia per impostazione predefinita, supportando anche i layout personalizzati._

Visualizzazioni di raccolta, disponibile nel `UICollectionView` di classi, sono un nuovo concetto in iOS 6 che introducono la presentazione di più elementi sullo schermo utilizzando i layout. I modelli per fornire dati a un `UICollectionView` per creare elementi e interagire con i seguenti elementi la delega stesso e i dati di origine i modelli comunemente usati nello sviluppo di iOS.

Tuttavia, le visualizzazioni di raccolta funzionano con un sottosistema di layout che è indipendente dal `UICollectionView` stesso. Pertanto, è sufficiente fornire un layout diverso possibile modificare facilmente la presentazione di una visualizzazione di raccolta.

iOS fornisce una classe di layout denominata `UICollectionViewFlowLayout` che consente la riga in base al layout, ad esempio una griglia deve essere creato con alcuna attività aggiuntiva. Inoltre, i layout personalizzati possono anche venga creati che consentono qualsiasi presentazione che si può immaginare.

## <a name="uicollectionview-basics"></a>Nozioni di base UICollectionView

Il `UICollectionView` classe è costituita da tre elementi diversi:

-  **Le celle** – visualizzazioni basate sui dati per ogni elemento
-  **Viste supplementari** – visualizzazioni basate sui dati associate a una sezione.
-  **Le viste Decoration** : Non-data driven le visualizzazioni create da un layout

## <a name="cells"></a>Celle

Le celle sono oggetti che rappresentano un singolo elemento nel set di dati che viene presentato per la visualizzazione di raccolta. Ogni cella è un'istanza del `UICollectionViewCell` (classe), composto da tre diverse visualizzazioni, come illustrato nella figura seguente:

 [![](uicollectionview-images/01-uicollectionviewcell.png "Ogni cella è composto da tre diverse visualizzazioni, come illustrato di seguito")](uicollectionview-images/01-uicollectionviewcell.png#lightbox)

Il `UICollectionViewCell` classe presenta le proprietà seguenti per ognuna di queste viste:

-   `ContentView` : Questa vista contiene il contenuto che fornisca la cella. Ne viene eseguito il rendering nell'ordine z in primo piano sullo schermo.
-   `SelectedBackgroundView` -Le celle sono compilate in modalità di supporto per la selezione. Questa vista viene utilizzata per indicare visivamente che viene selezionata una cella. Viene visualizzato sotto il `ContentView` quando è selezionata una cella.
-   `BackgroundView` – Celle possono anche visualizzare uno sfondo, presentata dal `BackgroundView` . In questa vista viene eseguito il rendering di sotto di `SelectedBackgroundView` .


Impostando il `ContentView` , che risulta minore di `BackgroundView` e `SelectedBackgroundView`, il `BackgroundView` può essere utilizzato per frame visivamente il contenuto, durante il `SelectedBackgroundView` verrà visualizzato quando viene selezionata una cella, come illustrato di seguito:

 [![](uicollectionview-images/02-cells.png "Gli elementi di cella diversa")](uicollectionview-images/02-cells.png#lightbox)

Le celle nella schermata precedente vengono create ereditando dal `UICollectionViewCell` e impostando le `ContentView`, `SelectedBackgroundView` e `BackgroundView` proprietà, rispettivamente, come illustrato nel codice seguente:

```csharp
public class AnimalCell : UICollectionViewCell
{
        UIImageView imageView;

        [Export ("initWithFrame:")]
        public AnimalCell (CGRect frame) : base (frame)
        {
            BackgroundView = new UIView{BackgroundColor = UIColor.Orange};

            SelectedBackgroundView = new UIView{BackgroundColor = UIColor.Green};

            ContentView.Layer.BorderColor = UIColor.LightGray.CGColor;
            ContentView.Layer.BorderWidth = 2.0f;
            ContentView.BackgroundColor = UIColor.White;
            ContentView.Transform = CGAffineTransform.MakeScale (0.8f, 0.8f);

            imageView = new UIImageView (UIImage.FromBundle ("placeholder.png"));
            imageView.Center = ContentView.Center;
            imageView.Transform = CGAffineTransform.MakeScale (0.7f, 0.7f);

            ContentView.AddSubview (imageView);
        }

        public UIImage Image {
            set {
                imageView.Image = value;
            }
        }
}
```

 <a name="Supplementary_Views" />


## <a name="supplementary-views"></a>Viste supplementari

Le visualizzazioni supplementari sono visualizzazioni che contengono informazioni associate a ogni sezione di un `UICollectionView`. Come celle, le visualizzazioni supplementari sono basati sui dati. In cui le celle presentano i dati dell'elemento da un'origine dati, viste supplementari presentare i dati di sezione, ad esempio le categorie del libro su uno scaffale o il genere della musica in un catalogo musicale.

Ad esempio, è stato possibile è possibile utilizzare una vista supplementari per presentare un'intestazione per una particolare sezione, come illustrato nella figura seguente:

 [![](uicollectionview-images/02a-supplementary-view.png "Utilizzare una vista supplementari per presentare un'intestazione per una particolare sezione, come illustrato di seguito")](uicollectionview-images/02a-supplementary-view.png#lightbox)

Per utilizzare una vista supplementari, deve prima essere registrata nel `ViewDidLoad` metodo:

```csharp
CollectionView.RegisterClassForSupplementaryView (typeof(Header), UICollectionElementKindSection.Header, headerId);
```

Quindi, deve essere restituito tramite la vista `GetViewForSupplementaryElement`, creato utilizzando `DequeueReusableSupplementaryView`e che eredita da `UICollectionReusableView`. Il seguente frammento di codice genererà la SupplementaryView illustrato nella schermata precedente:

```csharp
public override UICollectionReusableView GetViewForSupplementaryElement (UICollectionView collectionView, NSString elementKind, NSIndexPath indexPath)
        {
            var headerView = (Header)collectionView.DequeueReusableSupplementaryView (elementKind, headerId, indexPath);
            headerView.Text = "Supplementary View";
            return headerView;
        }

```

Le visualizzazioni supplementari sono più generiche solo le intestazioni e piè di pagina.
Che può essere posizionate in un punto qualsiasi nella visualizzazione raccolta e può essere costituiti da tutte le viste, rendendo l'aspetto completamente personalizzabile.

 <a name="Decoration_Views" />


## <a name="decoration-views"></a>Viste di effetto

La decorazione visualizzazioni sono puramente visual che possono essere visualizzati in un `UICollectionView`. A differenza delle celle e le viste supplementari, essi non sono basate sui dati. Essi vengono sempre creati all'interno di sottoclasse di un layout e successivamente modificare come layout del contenuto. Ad esempio, è stato possibile utilizzare una vista di decorazione per offrire una visualizzazione in background che consente di far scorrere il contenuto di `UICollectionView`, come illustrato di seguito:

 [![](uicollectionview-images/02c-decoration-view.png "La decorazione visualizzazione con uno sfondo rosso")](uicollectionview-images/02c-decoration-view.png#lightbox)

 Il frammento di codice seguente modifica lo sfondo in rosso negli esempi `CircleLayout` classe:

 ```csharp
 public class MyDecorationView : UICollectionReusableView
    {
        [Export ("initWithFrame:")]
        public MyDecorationView (CGRect frame) : base (frame)
        {
            BackgroundColor = UIColor.Red;
        }
    }
 ```


## <a name="data-source"></a>origine dati

Come con altre parti di iOS, ad esempio `UITableView` e `MKMapView`, `UICollectionView` Ottiene i dati da un *zdroj dat*, che viene esposto in xamarin. IOS tramite il **`UICollectionViewDataSource`** classe. Questa classe è responsabile di fornire contenuto per il `UICollectionView` , ad esempio:

-  **Le celle** – restituito da `GetCell` (metodo).
-  **Viste supplementari** – restituito da `GetViewForSupplementaryElement` (metodo).
-  **Numero di sezioni** – restituito da `NumberOfSections` (metodo). Il valore predefinito è 1 se non è implementata.
-  **Numero di elementi per ogni sezione** – restituito da `GetItemsCount` (metodo).

### <a name="uicollectionviewcontroller"></a>UICollectionViewController
Per praticità, di `UICollectionViewController` classe è disponibile. Ciò viene configurato automaticamente per essere sia il delegato, che è descritti nella sezione successiva, e i dati di origine per la relativa `UICollectionView` visualizzazione.

Come per gli `UITableView`, il `UICollectionView` classe chiamerà solo la propria origine dati per ottenere le celle per gli elementi sullo schermo.
Le celle che scorre fuori dallo schermo vengono inserite in una coda per il riutilizzo, come illustrato nell'immagine seguente:

 [![](uicollectionview-images/03-cell-reuse.png "Le celle che scorrono fuori dallo schermo vengono inserite a una coda per il riutilizzo, come illustrato di seguito")](uicollectionview-images/03-cell-reuse.png#lightbox)

Riutilizzo di cella è stato semplificato con `UICollectionView` e `UITableView`. È non è più necessario creare una cella direttamente nell'origine dati se non è disponibile nella coda di riutilizzo, come le celle vengono registrate con il sistema. Se una cella non è disponibile quando si effettua la chiamata a rimuovere dalla coda cella dalla coda del riutilizzo, iOS creerà automaticamente in base al tipo o nib che è stato registrato.
La stessa tecnica è disponibile anche per le visualizzazioni supplementari.

Ad esempio, si consideri il codice seguente che registra il `AnimalCell` classe:

```csharp
static NSString animalCellId = new NSString ("AnimalCell");
CollectionView.RegisterClassForCell (typeof(AnimalCell), animalCellId);
```

Quando un `UICollectionView` necessita di una cella in quanto l'elemento è visualizzato sullo schermo, il `UICollectionView` chiama la relativa origine di dati `GetCell` (metodo). Come per il funzionamento con UITableView, questo metodo è responsabile per la configurazione di una cella di dati sottostante, che dovrebbe essere un `AnimalCell` classe in questo caso.

Il codice seguente viene illustrata un'implementazione di `GetCell` che restituisce un `AnimalCell` istanza:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, Foundation.NSIndexPath indexPath)
{
        var animalCell = (AnimalCell)collectionView.DequeueReusableCell (animalCellId, indexPath);

        var animal = animals [indexPath.Row];

        animalCell.Image = animal.Image;

        return animalCell;
}
```

La chiamata a `DequeReusableCell` è la cella verrà sia deprovisioning accodata dalla coda di riutilizzo oppure, se non è disponibile nella coda, creata in base al tipo registrato nella chiamata a una cella `CollectionView.RegisterClassForCell`.

In questo caso, registrando il `AnimalCell` (classe), iOS verrà creata una nuova `AnimalCell` internamente e lo restituirà quando viene effettuata una chiamata a una cella di rimuovere dalla coda, dopo il quale viene configurato con l'immagine contenuta nella classe di polizia e restituiti per la visualizzazione per il `UICollectionView`.

 <a name="Delegate" />


### <a name="delegate"></a>delegato

Il `UICollectionView` classe Usa un delegato del tipo `UICollectionViewDelegate` per supportare l'interazione con il contenuto nel `UICollectionView`. Ciò consente il controllo di:

-  **Selezione delle celle** – determina se è selezionata una cella.
-  **Evidenziazione delle celle** – determina se una cella è attualmente interessata.
-  **Menu di scelta delle celle** : dal Menu visualizzato per una cella in risposta a un'azione di pressione prolungata.


Come con l'origine dati, il `UICollectionViewController` è configurato per impostazione predefinita sia il delegato per il `UICollectionView`.

 <a name="Cell_HighLighting" />


#### <a name="cell-highlighting"></a>Evidenziazione delle celle

Quando si preme una cella, le transizioni di cella in uno stato evidenziato, e non è selezionata fino a che l'utente solleva il dito rispetto alla cella. In questo modo una temporanea modifica dell'aspetto della cella prima che venga effettivamente selezionato. Al momento, la cella della selezione `SelectedBackgroundView` viene visualizzato. La figura seguente mostra lo stato evidenziato appena prima che si verifichi la selezione:

 [![](uicollectionview-images/04-cell-highlight.png "La figura seguente mostra lo stato evidenziato appena prima che si verifichi la selezione")](uicollectionview-images/04-cell-highlight.png#lightbox)

Per implementare l'evidenziazione, la `ItemHighlighted` e `ItemUnhighlighted` metodi del `UICollectionViewDelegate` può essere utilizzato. Ad esempio, il codice riportato di seguito verranno applicate uno sfondo giallo del `ContentView` quando la cella è evidenziata e uno sfondo bianco quando non è evidenziata, come illustrato nell'immagine precedente:

```csharp
public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.Yellow;
}

public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.White;
}
```

 <a name="Disabling_Selection" />


#### <a name="disabling-selection"></a>La disabilitazione di selezione

La selezione è abilitata per impostazione predefinita in `UICollectionView`. Per disabilitare la selezione, eseguire l'override `ShouldHighlightItem` e restituiscono false come illustrato di seguito:

```csharp
public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath)
{
        return false;
}
```

Quando l'evidenziazione è disattivata, è disattivato anche il processo di selezione di una cella. Inoltre, è inoltre disponibile un' `ShouldSelectItem` metodo che controlla la selezione direttamente, anche se se `ShouldHighlightItem` viene implementata e restituisce false, `ShouldSelectItem` non viene chiamato.

 `ShouldSelectItem` Consente di attivare o disattivare un singolo elemento per elemento, la selezione quando `ShouldHighlightItem` non implementato. Consente inoltre l'evidenziazione senza selezione, se `ShouldHighlightItem` viene implementata e restituisce true, mentre `ShouldSelectItem` restituisce false.

 <a name="Cell_Menus" />


#### <a name="cell-menus"></a>Menu di cella

Ogni cella di un `UICollectionView` è in grado di visualizzare un menu che consente le operazioni Taglia, copia e Incolla facoltativamente essere supportati. Per creare un menu di modifica in una cella:

1.  Eseguire l'override `ShouldShowMenu` e restituire true se l'elemento deve essere visualizzato un menu.
1.  Eseguire l'override `CanPerformAction` e restituire true per ogni azione che può eseguire l'elemento, che sarà una delle operazioni Taglia, copia o Incolla.
1.  Eseguire l'override `PerformAction` per eseguire la modifica, copia dell'operazione Incolla.


Lo screenshot seguente mostra il menu alla pressione prolungata di una cella:

 [![](uicollectionview-images/04a-menu.png "Questo screenshot Mostra il menu alla pressione prolungata di una cella")](uicollectionview-images/04a-menu.png#lightbox)

 <a name="Layout" />


## <a name="layout"></a>Layout

`UICollectionView` supporta un sistema di layout che consente il posizionamento di tutti i relativi elementi, celle, supplementari visualizzazioni e visualizzazioni di effetto, a essere gestiti indipendentemente dal `UICollectionView` stesso.
Usa il sistema di layout, un'applicazione può supportare i layout, ad esempio quella simile a griglia è stato illustrato in questo articolo, nonché fornire i layout personalizzati.

 <a name="Layout_Basics" />


### <a name="layout-basics"></a>Nozioni di base di layout

Layout in un `UICollectionView` sono definiti in una classe che eredita da `UICollectionViewLayout`. L'implementazione di layout è responsabile per la creazione di attributi di layout per tutti gli elementi di `UICollectionView`. Esistono due modi per creare un layout:

-  Utilizzare l'elemento predefinito `UICollectionViewFlowLayout` .
-  Specificare un layout personalizzato ereditando da `UICollectionViewLayout` .


 <a name="Flow_Layout" />


### <a name="flow-layout"></a>Layout di flusso

Il `UICollectionViewFlowLayout` classe offre un layout basato sulla riga che è adatto per la disposizione del contenuto in una griglia di celle come abbiamo visto.

Per usare un layout di flusso:

-  Creare un'istanza di `UICollectionViewFlowLayout` :


```csharp
var layout = new UICollectionViewFlowLayout ();
```

-  Passare al costruttore di istanza di `UICollectionView` :


```csharp
simpleCollectionViewController = new SimpleCollectionViewController (layout);
```

Questo è tutto ciò che serve per il layout contenuto in una griglia. Inoltre, quando l'orientamento viene modificato, il `UICollectionViewFlowLayout` gestisce riordinarne il contenuto in modo appropriato, come illustrato di seguito:

 [![](uicollectionview-images/05-layout-orientation.png "Ad esempio le modifiche apportate orientamento")](uicollectionview-images/05-layout-orientation.png#lightbox)

 <a name="Section_Inset" />


#### <a name="section-inset"></a>Sezione Inset

Per fornire spazio intorno al `UIContentView`, layout dispongono di un `SectionInset` vlastnosti typu `UIEdgeInsets`. Ad esempio, il codice seguente fornisce un buffer di 50 pixel relativo a ogni sezione del `UIContentView` quando si è disposti in base un `UICollectionViewFlowLayout`:

```csharp
var layout = new UICollectionViewFlowLayout ();
layout.SectionInset = new UIEdgeInsets (50,50,50,50);
```

Ciò comporta la spaziatura intorno alla sezione come illustrato di seguito:

 [![](uicollectionview-images/06-sectioninset.png "Spaziatura intorno alla sezione, come illustrato di seguito")](uicollectionview-images/06-sectioninset.png#lightbox)

 <a name="Subclassing_UICollectionViewFlowLayout" />


#### <a name="subclassing-uicollectionviewflowlayout"></a>Creazione di una sottoclasse UICollectionViewFlowLayout

Nell'edizione a usando `UICollectionViewFlowLayout` direttamente, si può anche essere sottoclassato per personalizzare ulteriormente il layout del contenuto lungo una linea. Ad esempio, può essere utilizzato per creare un layout che non va a capo le celle in una griglia, ma viene invece creata una singola riga con un effetto di scorrimento orizzontale, come illustrato di seguito:

 [![](uicollectionview-images/07-line-layout.png "Una singola riga con un effetto di scorrimento orizzontale")](uicollectionview-images/07-line-layout.png#lightbox)

Per implementare questa creando sottoclassi `UICollectionViewFlowLayout` richiede:

-  L'inizializzazione di qualsiasi proprietà di layout che si applicano al layout di se stesso o tutti gli elementi del layout nel costruttore.
-  Viene sottoposto a override `ShouldInvalidateLayoutForBoundsChange` , restituendo true, che, quando i limiti del `UICollectionView` vengono ricalcolate le modifiche, il layout delle celle. Questa opzione viene utilizzata in questo caso assicurarsi che il codice di trasformazione applicato alla cella vicino verrà applicati durante lo scorrimento.
-  Si esegue l'override `TargetContentOffset` per rendere il vicino snap al centro della cella di `UICollectionView` come lo scorrimento viene arrestata.
-  Si esegue l'override `LayoutAttributesForElementsInRect` per restituire una matrice di `UICollectionViewLayoutAttributes` . Ciascuna `UICollectionViewLayoutAttribute` contiene informazioni su come l'elemento specifico, incluse le proprietà, ad esempio layout relativo `Center` , `Size` , `ZIndex` e `Transform3D` .


Il codice seguente mostra un'implementazione di questo tipo:

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;
using CoreGraphics;
using CoreAnimation;

namespace SimpleCollectionView
{
    public class LineLayout : UICollectionViewFlowLayout
    {
        public const float ITEM_SIZE = 200.0f;
        public const int ACTIVE_DISTANCE = 200;
        public const float ZOOM_FACTOR = 0.3f;

        public LineLayout ()
        {
            ItemSize = new CGSize (ITEM_SIZE, ITEM_SIZE);
            ScrollDirection = UICollectionViewScrollDirection.Horizontal;
            SectionInset = new UIEdgeInsets (400,0,400,0);
            MinimumLineSpacing = 50.0f;
        }

        public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
        {
            return true;
        }

        public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
        {
            var array = base.LayoutAttributesForElementsInRect (rect);
            var visibleRect = new CGRect (CollectionView.ContentOffset, CollectionView.Bounds.Size);

            foreach (var attributes in array) {
                if (attributes.Frame.IntersectsWith (rect)) {
                    float distance = (float)(visibleRect.GetMidX () - attributes.Center.X);
                    float normalizedDistance = distance / ACTIVE_DISTANCE;
                    if (Math.Abs (distance) < ACTIVE_DISTANCE) {
                        float zoom = 1 + ZOOM_FACTOR * (1 - Math.Abs (normalizedDistance));
                        attributes.Transform3D = CATransform3D.MakeScale (zoom, zoom, 1.0f);
                        attributes.ZIndex = 1;
                    }
                }
            }
            return array;
        }

        public override CGPoint TargetContentOffset (CGPoint proposedContentOffset, CGPoint scrollingVelocity)
        {
            float offSetAdjustment = float.MaxValue;
            float horizontalCenter = (float)(proposedContentOffset.X + (this.CollectionView.Bounds.Size.Width / 2.0));
            CGRect targetRect = new CGRect (proposedContentOffset.X, 0.0f, this.CollectionView.Bounds.Size.Width, this.CollectionView.Bounds.Size.Height);
            var array = base.LayoutAttributesForElementsInRect (targetRect);
            foreach (var layoutAttributes in array) {
                float itemHorizontalCenter = (float)layoutAttributes.Center.X;
                if (Math.Abs (itemHorizontalCenter - horizontalCenter) < Math.Abs (offSetAdjustment)) {
                    offSetAdjustment = itemHorizontalCenter - horizontalCenter;
                }
            }
            return new CGPoint (proposedContentOffset.X + offSetAdjustment, proposedContentOffset.Y);
        }

    }
}
```

 <a name="Custom_Layout" />


### <a name="custom-layout"></a>Layout personalizzato

Oltre a usare `UICollectionViewFlowLayout`, i layout anche possono essere completamente personalizzati ereditando direttamente dalla `UICollectionViewLayout`.

I metodi principali per eseguire l'override sono:

-   `PrepareLayout` : Usato per l'esecuzione di calcoli geometrici iniziali che verranno usati nel corso del processo di layout.
-   `CollectionViewContentSize` : Restituisce le dimensioni dell'area usata per visualizzare il contenuto.
-   `LayoutAttributesForElementsInRect` : Quando l'esempio UICollectionViewFlowLayout illustrato in precedenza, questo metodo viene utilizzato per fornire informazioni per il `UICollectionView` relative al layout di ogni elemento. Tuttavia, a differenza di `UICollectionViewFlowLayout` , quando si crea un layout personalizzato, è possibile posizionare gli elementi ma scelto.


Lo stesso contenuto, ad esempio, può essere presentato in un layout circolare, come illustrato di seguito:

 [![](uicollectionview-images/08-circle-layout.png "Un oggetto personalizzato circolare layout, come illustrato di seguito")](uicollectionview-images/08-circle-layout.png#lightbox)

La cosa potente sui layout è che per passare dal layout griglia, a un layout di scorrimento orizzontale e successivamente a questo layout circolare richiede solo la classe di layout fornita per il `UICollectionView` essere modificato. Niente di `UICollectionView`, il delegato o origine dati delle modifiche al codice affatto.


## <a name="changes-in-ios-9"></a>Modifiche in iOS 9


In iOS 9, la visualizzazione di raccolta (`UICollectionView`) supporta trascina ora il riordinamento degli elementi predefiniti aggiungendo un nuovo riconoscitore di movimento predefiniti e diversi nuovi metodi di supporti.

Mediante questi nuovi metodi, è possibile implementare facilmente trascinare per riordinare nella propria visualizzazione insieme e hanno la possibilità di personalizzazione dell'aspetto degli elementi in qualsiasi fase del processo di riordinamento.

[![](uicollectionview-images/intro01.png "Un esempio del processo di riordinamento")](uicollectionview-images/intro01.png#lightbox)

In questo articolo prenderemo in illustrata l'implementazione di trascinamento-a-Riordina in un'applicazione xamarin. IOS, nonché alcune altre modifiche iOS 9 ha apportato al controllo di visualizzazione raccolta:

- [Facile riordinamento degli elementi](#Easy-Reordering-of-Items)
    - [Esempio di riordinamento semplice](#Simple-Reordering-Example)
    - [Utilizzando un riconoscimento di movimenti personalizzati](#Using-a-Custom-Gesture-Recognizer)
    - [Layout personalizzati e riordinamento](#Custom-Layouts-and-Reording)
- [Modifiche alla vista raccolta](#collection-view-changes)

<a name="Easy-Reordering-of-Items" />

## <a name="reordering-of-items"></a>Riordinamento degli elementi

Come indicato in precedenza, una delle modifiche più significative per la visualizzazione di raccolta in iOS 9 è stata l'aggiunta di funzionalità di trascinamento-a-Riordina facile predefinite.

In iOS 9, il modo più rapido per aggiungere il riordino a una visualizzazione raccolta consiste nell'utilizzare un `UICollectionViewController`.
Il controller di visualizzazione di raccolta ha ora un `InstallsStandardGestureForInteractiveMovement` proprietà, che aggiunge uno standard *riconoscitore di movimento* che supporta il trascinamento per riordinare gli elementi della raccolta.
Poiché il valore predefinito è `true`, è necessario implementare solo le `MoveItem` metodo del `UICollectionViewDataSource` classe per supportare il trascinamento-a-riordinamento. Ad esempio:

```csharp
public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    // Reorder our list of items
    ...
}
```
<a name="Simple-Reordering-Example" />

### <a name="simple-reordering-example"></a>Esempio di riordinamento semplice

Un rapido esempio, avviare un nuovo progetto xamarin. IOS e modificare il **Main. Storyboard** file. Trascinare un `UICollectionViewController` nell'area di progettazione:

[![](uicollectionview-images/quick01.png "Aggiunta di un UICollectionViewController")](uicollectionview-images/quick01.png#lightbox)

Selezionare la visualizzazione di raccolta (potrebbe essere più semplice eseguire questa operazione dalla struttura del documento). Nella scheda layout del riquadro delle proprietà, impostare le dimensioni seguenti, come illustrato nello screenshot seguente:

- **Dimensioni della cella**: Width: 60 | Height: 60
- **Dimensioni massime dell'intestazione**: Width: 0 | Height: 0
- **Dimensioni del piè di pagina**: Width: 0 | Height: 0
- **Spaziatura min**: Per le celle – 8 | Per le righe, 8
- **Sezione Inset**: Top: 16 | Nella parte inferiore – 16 | Left: 16 | Right-16

[![](uicollectionview-images/quick04.png "Impostare le dimensioni di visualizzazione raccolta")](uicollectionview-images/quick04.png#lightbox)

Successivamente, modificare il valore predefinito di celle:
    - Modificare il colore di sfondo su blu
    - Aggiungere un'etichetta da usare come titolo per la cella
    - Impostare l'identificatore di riutilizzo su **cella**

[![](uicollectionview-images/quick02.png "Modificare il valore predefinito di celle")](uicollectionview-images/quick02.png#lightbox)

Aggiungere vincoli per mantenere l'etichetta centrata all'interno della cella come cambia dimensioni:

Nel **riquadro della proprietà** per il _CollectionViewCell_ e impostare il **classe** a `TextCollectionViewCell`:

[![](uicollectionview-images/quick05.png "Impostare la classe a TextCollectionViewCell")](uicollectionview-images/quick05.png#lightbox)

Impostare il **visualizzazione riutilizzabili insieme** a `Cell`:

[![](uicollectionview-images/quick06.png "Impostare la visualizzazione di raccolta riutilizzabile alla cella")](uicollectionview-images/quick06.png#lightbox)

Infine, selezionare l'etichetta e denominarlo `TextLabel`:

[![](uicollectionview-images/quick07.png "etichetta nome TextLabel")](uicollectionview-images/quick07.png#lightbox)

Modificare il `TextCollectionViewCell` classe e aggiungere le proprietà seguenti.:

```csharp
using System;
using Foundation;
using UIKit;

namespace CollectionView
{
    public partial class TextCollectionViewCell : UICollectionViewCell
    {
        #region Computed Properties
        public string Title {
            get { return TextLabel.Text; }
            set { TextLabel.Text = value; }
        }
        #endregion

        #region Constructors
        public TextCollectionViewCell (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

In questo caso il `Text` proprietà dell'etichetta viene visualizzata come titolo della cella, in modo che può essere impostata dal codice.

Aggiungere un nuovo C# classe al progetto e denominarlo `WaterfallCollectionSource`. Modificare il file e renderlo simile al seguente:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
    public class WaterfallCollectionSource : UICollectionViewDataSource
    {
        #region Computed Properties
        public WaterfallCollectionView CollectionView { get; set;}
        public List<int> Numbers { get; set; } = new List<int> ();
        #endregion

        #region Constructors
        public WaterfallCollectionSource (WaterfallCollectionView collectionView)
        {
            // Initialize
            CollectionView = collectionView;

            // Init numbers collection
            for (int n = 0; n < 100; ++n) {
                Numbers.Add (n);
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView) {
            // We only have one section
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section) {
            // Return the number of items
            return Numbers.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a reusable cell and set {~~it's~>its~~} title from the item
            var cell = collectionView.DequeueReusableCell ("Cell", indexPath) as TextCollectionViewCell;
            cell.Title = Numbers [(int)indexPath.Item].ToString();

            return cell;
        }

        public override bool CanMoveItem (UICollectionView collectionView, NSIndexPath indexPath) {
            // We can always move items
            return true;
        }

        public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
        {
            // Reorder our list of items
            var item = Numbers [(int)sourceIndexPath.Item];
            Numbers.RemoveAt ((int)sourceIndexPath.Item);
            Numbers.Insert ((int)destinationIndexPath.Item, item);
        }
        #endregion
    }
}
```

Questa classe verrà l'origine dati per la visualizzazione di raccolta e specificare le informazioni per ogni cella nella raccolta.
Si noti che il `MoveItem` metodo implementato per consentire a trascinare gli elementi della raccolta per essere riordinati.

Aggiungere un altro nuovo C# classe al progetto e denominarlo `WaterfallCollectionDelegate`. Modificare il file, renderlo simile al seguente:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
    public class WaterfallCollectionDelegate : UICollectionViewDelegate
    {
        #region Computed Properties
        public WaterfallCollectionView CollectionView { get; set;}
        #endregion

        #region Constructors
        public WaterfallCollectionDelegate (WaterfallCollectionView collectionView)
        {

            // Initialize
            CollectionView = collectionView;

        }
        #endregion

        #region Overrides Methods
        public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath) {
            // Always allow for highlighting
            return true;
        }

        public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get cell and change to green background
            var cell = collectionView.CellForItem(indexPath);
            cell.ContentView.BackgroundColor = UIColor.FromRGB(183,208,57);
        }

        public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get cell and return to blue background
            var cell = collectionView.CellForItem(indexPath);
            cell.ContentView.BackgroundColor = UIColor.FromRGB(164,205,255);
        }
        #endregion
    }
}
```

Questo fungerà da delegato per la visualizzazione di raccolta. I metodi sottoposti a override per evidenziare una cella quando l'utente interagisce con essa nella visualizzazione raccolta.

Aggiungerà un ultimo C# classe al progetto e denominarlo `WaterfallCollectionView`. Modificare il file, renderlo simile al seguente:

```csharp
using System;
using UIKit;
using System.Collections.Generic;
using Foundation;

namespace CollectionView
{
    [Register("WaterfallCollectionView")]
    public class WaterfallCollectionView : UICollectionView
    {

        #region Constructors
        public WaterfallCollectionView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Initialize
            DataSource = new WaterfallCollectionSource(this);
            Delegate = new WaterfallCollectionDelegate(this);

        }
        #endregion
    }
}
```

Si noti che `DataSource` e `Delegate` creati in precedenza vengono impostati quando la visualizzazione di raccolta viene costruita da dello storyboard (o **xib** file).

Modificare il **Main. Storyboard** nuovamente file e selezionare la visualizzazione di raccolta e passare alle **proprietà**. Impostare il **classe** alla personalizzata `WaterfallCollectionView` classe definito sopra:

Salvare le modifiche apportate all'interfaccia utente ed eseguire l'app.
Se l'utente seleziona un elemento dall'elenco e lo trascina in una nuova posizione, gli altri elementi verranno automaticamente animare durante lo spostamento dall'area di lavoro dell'elemento.
Quando l'utente rilascia l'elemento in un nuovo percorso, verrà usato lo strumento in tale posizione. Ad esempio:

[![](uicollectionview-images/intro01.png "Un esempio di trascinamento di un elemento in una nuova posizione")](uicollectionview-images/intro01.png#lightbox)

<a name="Using-a-Custom-Gesture-Recognizer" />

### <a name="using-a-custom-gesture-recognizer"></a>Utilizzando un riconoscimento di movimenti personalizzati

Nei casi in cui non è possibile usare una `UICollectionViewController` e deve usare una normale `UIViewController`, o se si desidera maggiore controllo sul movimento di trascinamento e rilascio, è possibile creare il proprio riconoscitore di movimento personalizzato e aggiungerlo alla visualizzazione dell'insieme, quando viene caricata la visualizzazione. Ad esempio:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Create a custom gesture recognizer
    var longPressGesture = new UILongPressGestureRecognizer ((gesture) => {

        // Take action based on state
        switch(gesture.State) {
        case UIGestureRecognizerState.Began:
            var selectedIndexPath = CollectionView.IndexPathForItemAtPoint(gesture.LocationInView(View));
            if (selectedIndexPath !=null) {
                CollectionView.BeginInteractiveMovementForItem(selectedIndexPath);
            }
            break;
        case UIGestureRecognizerState.Changed:
            CollectionView.UpdateInteractiveMovementTargetPosition(gesture.LocationInView(View));
            break;
        case UIGestureRecognizerState.Ended:
            CollectionView.EndInteractiveMovement();
            break;
        default:
            CollectionView.CancelInteractiveMovement();
            break;
        }

    });

    // Add the custom recognizer to the collection view
    CollectionView.AddGestureRecognizer(longPressGesture);
}
```

Qui utilizziamo numerosi nuovi metodi aggiunti alla visualizzazione dell'insieme per implementare e controllare l'operazione di trascinamento:

 - `BeginInteractiveMovementForItem` -Contrassegna l'inizio di un'operazione di spostamento.
 - `UpdateInteractiveMovementTargetPosition` -Viene inviato come la posizione dell'elemento è aggiornata.
 - `EndInteractiveMovement` -Contrassegna la fine di spostamento di un elemento.
 - `CancelInteractiveMovement` : Contrassegna l'utente annulla l'operazione di spostamento.

Quando l'applicazione viene eseguita, l'operazione di trascinamento funzionerà esattamente come il valore predefinito trascinare riconoscitore di movimento che viene fornito con la visualizzazione di raccolta.

<a name="Custom-Layouts-and-Reording" />

### <a name="custom-layouts-and-reordering"></a>Layout personalizzati e riordinamento

In iOS 9, sono stati aggiunti diversi nuovi metodi per lavorare con layout-trascinare per riordinare e personalizzate in una visualizzazione di raccolta. Per esplorare questa funzionalità, aggiungere un layout personalizzato alla raccolta.

In primo luogo, aggiungere un nuovo C# classe denominata `WaterfallCollectionLayout` al progetto. Apportare le modifiche e renderlo simile al seguente:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;
using CoreGraphics;

namespace CollectionView
{
    [Register("WaterfallCollectionLayout")]
    public class WaterfallCollectionLayout : UICollectionViewLayout
    {
        #region Private Variables
        private int columnCount = 2;
        private nfloat minimumColumnSpacing = 10;
        private nfloat minimumInterItemSpacing = 10;
        private nfloat headerHeight = 0.0f;
        private nfloat footerHeight = 0.0f;
        private UIEdgeInsets sectionInset = new UIEdgeInsets(0, 0, 0, 0);
        private WaterfallCollectionRenderDirection itemRenderDirection = WaterfallCollectionRenderDirection.ShortestFirst;
        private Dictionary<nint,UICollectionViewLayoutAttributes> headersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
        private Dictionary<nint,UICollectionViewLayoutAttributes> footersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
        private List<CGRect> unionRects = new List<CGRect>();
        private List<nfloat> columnHeights = new List<nfloat>();
        private List<UICollectionViewLayoutAttributes> allItemAttributes = new List<UICollectionViewLayoutAttributes>();
        private List<List<UICollectionViewLayoutAttributes>> sectionItemAttributes = new List<List<UICollectionViewLayoutAttributes>>();
        private nfloat unionSize = 20;
        #endregion

        #region Computed Properties
        [Export("ColumnCount")]
        public int ColumnCount {
            get { return columnCount; }
            set {
                WillChangeValue ("ColumnCount");
                columnCount = value;
                DidChangeValue ("ColumnCount");

                InvalidateLayout ();
            }
        }

        [Export("MinimumColumnSpacing")]
        public nfloat MinimumColumnSpacing {
            get { return minimumColumnSpacing; }
            set {
                WillChangeValue ("MinimumColumnSpacing");
                minimumColumnSpacing = value;
                DidChangeValue ("MinimumColumnSpacing");

                InvalidateLayout ();
            }
        }

        [Export("MinimumInterItemSpacing")]
        public nfloat MinimumInterItemSpacing {
            get { return minimumInterItemSpacing; }
            set {
                WillChangeValue ("MinimumInterItemSpacing");
                minimumInterItemSpacing = value;
                DidChangeValue ("MinimumInterItemSpacing");

                InvalidateLayout ();
            }
        }

        [Export("HeaderHeight")]
        public nfloat HeaderHeight {
            get { return headerHeight; }
            set {
                WillChangeValue ("HeaderHeight");
                headerHeight = value;
                DidChangeValue ("HeaderHeight");

                InvalidateLayout ();
            }
        }

        [Export("FooterHeight")]
        public nfloat FooterHeight {
            get { return footerHeight; }
            set {
                WillChangeValue ("FooterHeight");
                footerHeight = value;
                DidChangeValue ("FooterHeight");

                InvalidateLayout ();
            }
        }

        [Export("SectionInset")]
        public UIEdgeInsets SectionInset {
            get { return sectionInset; }
            set {
                WillChangeValue ("SectionInset");
                sectionInset = value;
                DidChangeValue ("SectionInset");

                InvalidateLayout ();
            }
        }

        [Export("ItemRenderDirection")]
        public WaterfallCollectionRenderDirection ItemRenderDirection {
            get { return itemRenderDirection; }
            set {
                WillChangeValue ("ItemRenderDirection");
                itemRenderDirection = value;
                DidChangeValue ("ItemRenderDirection");

                InvalidateLayout ();
            }
        }
        #endregion

        #region Constructors
        public WaterfallCollectionLayout ()
        {
        }

        public WaterfallCollectionLayout(NSCoder coder) : base(coder) {

        }
        #endregion

        #region Public Methods
        public nfloat ItemWidthInSectionAtIndex(int section) {

            var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
            return (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);
        }
        #endregion

        #region Override Methods
        public override void PrepareLayout ()
        {
            base.PrepareLayout ();

            // Get the number of sections
            var numberofSections = CollectionView.NumberOfSections();
            if (numberofSections == 0)
                return;

            // Reset collections
            headersAttributes.Clear ();
            footersAttributes.Clear ();
            unionRects.Clear ();
            columnHeights.Clear ();
            allItemAttributes.Clear ();
            sectionItemAttributes.Clear ();

            // Initialize column heights
            for (int n = 0; n < ColumnCount; n++) {
                columnHeights.Add ((nfloat)0);
            }

            // Process all sections
            nfloat top = 0.0f;
            var attributes = new UICollectionViewLayoutAttributes ();
            var columnIndex = 0;
            for (nint section = 0; section < numberofSections; ++section) {
                // Calculate section specific metrics
                var minimumInterItemSpacing = (MinimumInterItemSpacingForSection == null) ? MinimumColumnSpacing :
                    MinimumInterItemSpacingForSection (CollectionView, this, section);

                // Calculate widths
                var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
                var itemWidth = (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);

                // Calculate section header
                var heightHeader = (HeightForHeader == null) ? HeaderHeight :
                    HeightForHeader (CollectionView, this, section);

                if (heightHeader > 0) {
                    attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Header, NSIndexPath.FromRowSection (0, section));
                    attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, heightHeader);
                    headersAttributes.Add (section, attributes);
                    allItemAttributes.Add (attributes);

                    top = attributes.Frame.GetMaxY ();
                }

                top += SectionInset.Top;
                for (int n = 0; n < ColumnCount; n++) {
                    columnHeights [n] = top;
                }

                // Calculate Section Items
                var itemCount = CollectionView.NumberOfItemsInSection(section);
                List<UICollectionViewLayoutAttributes> itemAttributes = new List<UICollectionViewLayoutAttributes> ();

                for (nint n = 0; n < itemCount; n++) {
                    var indexPath = NSIndexPath.FromRowSection (n, section);
                    columnIndex = NextColumnIndexForItem (n);
                    var xOffset = SectionInset.Left + (itemWidth + MinimumColumnSpacing) * (nfloat)columnIndex;
                    var yOffset = columnHeights [columnIndex];
                    var itemSize = (SizeForItem == null) ? new CGSize (0, 0) : SizeForItem (CollectionView, this, indexPath);
                    nfloat itemHeight = 0.0f;

                    if (itemSize.Height > 0.0f && itemSize.Width > 0.0f) {
                        itemHeight = (nfloat)Math.Floor (itemSize.Height * itemWidth / itemSize.Width);
                    }

                    attributes = UICollectionViewLayoutAttributes.CreateForCell (indexPath);
                    attributes.Frame = new CGRect (xOffset, yOffset, itemWidth, itemHeight);
                    itemAttributes.Add (attributes);
                    allItemAttributes.Add (attributes);
                    columnHeights [columnIndex] = attributes.Frame.GetMaxY () + MinimumInterItemSpacing;
                }
                sectionItemAttributes.Add (itemAttributes);

                // Calculate Section Footer
                nfloat footerHeight = 0.0f;
                columnIndex = LongestColumnIndex();
                top = columnHeights [columnIndex] - MinimumInterItemSpacing + SectionInset.Bottom;
                footerHeight = (HeightForFooter == null) ? FooterHeight : HeightForFooter(CollectionView, this, section);

                if (footerHeight > 0) {
                    attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Footer, NSIndexPath.FromRowSection (0, section));
                    attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, footerHeight);
                    footersAttributes.Add (section, attributes);
                    allItemAttributes.Add (attributes);
                    top = attributes.Frame.GetMaxY ();
                }

                for (int n = 0; n < ColumnCount; n++) {
                    columnHeights [n] = top;
                }
            }

            var i =0;
            var attrs = allItemAttributes.Count;
            while(i < attrs) {
                var rect1 = allItemAttributes [i].Frame;
                i = (int)Math.Min (i + unionSize, attrs) - 1;
                var rect2 = allItemAttributes [i].Frame;
                unionRects.Add (CGRect.Union (rect1, rect2));
                i++;
            }

        }

        public override CGSize CollectionViewContentSize {
            get {
                if (CollectionView.NumberOfSections () == 0) {
                    return new CGSize (0, 0);
                }

                var contentSize = CollectionView.Bounds.Size;
                contentSize.Height = columnHeights [0];
                return contentSize;
            }
        }

        public override UICollectionViewLayoutAttributes LayoutAttributesForItem (NSIndexPath indexPath)
        {
            if (indexPath.Section >= sectionItemAttributes.Count) {
                return null;
            }

            if (indexPath.Item >= sectionItemAttributes [indexPath.Section].Count) {
                return null;
            }

            var list = sectionItemAttributes [indexPath.Section];
            return list [(int)indexPath.Item];
        }

        public override UICollectionViewLayoutAttributes LayoutAttributesForSupplementaryView (NSString kind, NSIndexPath indexPath)
        {
            var attributes = new UICollectionViewLayoutAttributes ();

            switch (kind) {
            case "header":
                attributes = headersAttributes [indexPath.Section];
                break;
            case "footer":
                attributes = footersAttributes [indexPath.Section];
                break;
            }

            return attributes;
        }

        public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
        {
            var begin = 0;
            var end = unionRects.Count;
            List<UICollectionViewLayoutAttributes> attrs = new List<UICollectionViewLayoutAttributes> ();


            for (int i = 0; i < end; i++) {
                if (rect.IntersectsWith(unionRects[i])) {
                    begin = i * (int)unionSize;
                }
            }

            for (int i = end - 1; i >= 0; i--) {
                if (rect.IntersectsWith (unionRects [i])) {
                    end = (int)Math.Min ((i + 1) * (int)unionSize, allItemAttributes.Count);
                    break;
                }
            }

            for (int i = begin; i < end; i++) {
                var attr = allItemAttributes [i];
                if (rect.IntersectsWith (attr.Frame)) {
                    attrs.Add (attr);
                }
            }

            return attrs.ToArray();
        }

        public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
        {
            var oldBounds = CollectionView.Bounds;
            return (newBounds.Width != oldBounds.Width);
        }
        #endregion

        #region Private Methods
        private int ShortestColumnIndex() {
            var index = 0;
            var shortestHeight = nfloat.MaxValue;
            var n = 0;

            // Scan each column for the shortest height
            foreach (nfloat height in columnHeights) {
                if (height < shortestHeight) {
                    shortestHeight = height;
                    index = n;
                }
                ++n;
            }

            return index;
        }

        private int LongestColumnIndex() {
            var index = 0;
            var longestHeight = nfloat.MinValue;
            var n = 0;

            // Scan each column for the shortest height
            foreach (nfloat height in columnHeights) {
                if (height > longestHeight) {
                    longestHeight = height;
                    index = n;
                }
                ++n;
            }

            return index;
        }

        private int NextColumnIndexForItem(nint item) {
            var index = 0;

            switch (ItemRenderDirection) {
            case WaterfallCollectionRenderDirection.ShortestFirst:
                index = ShortestColumnIndex ();
                break;
            case WaterfallCollectionRenderDirection.LeftToRight:
                index = ColumnCount;
                break;
            case WaterfallCollectionRenderDirection.RightToLeft:
                index = (ColumnCount - 1) - ((int)item / ColumnCount);
                break;
            }

            return index;
        }
        #endregion

        #region Events
        public delegate CGSize WaterfallCollectionSizeDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, NSIndexPath indexPath);
        public delegate nfloat WaterfallCollectionFloatDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);
        public delegate UIEdgeInsets WaterfallCollectionEdgeInsetsDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);

        public event WaterfallCollectionSizeDelegate SizeForItem;
        public event WaterfallCollectionFloatDelegate HeightForHeader;
        public event WaterfallCollectionFloatDelegate HeightForFooter;
        public event WaterfallCollectionEdgeInsetsDelegate InsetForSection;
        public event WaterfallCollectionFloatDelegate MinimumInterItemSpacingForSection;
        #endregion
    }
}
```

Ciò può essere usato di classe per fornire una colonna due personalizzata, layout di tipo grafico a cascata alla visualizzazione dell'insieme.
Il codice Usa la codifica di chiave-valore (tramite il `WillChangeValue` e `DidChangeValue` metodi) per fornire il data binding per la proprietà calcolate in questa classe.

Modificare quindi il `WaterfallCollectionSource` e apportare le modifiche e le aggiunte seguenti:

```csharp
private Random rnd = new Random();
...

public List<nfloat> Heights { get; set; } = new List<nfloat> ();
...

public WaterfallCollectionSource (WaterfallCollectionView collectionView)
{
    // Initialize
    CollectionView = collectionView;

    // Init numbers collection
    for (int n = 0; n < 100; ++n) {
        Numbers.Add (n);
        Heights.Add (rnd.Next (0, 100) + 40.0f);
    }
}
```

Verrà creata un'altezza casuale per ognuno degli elementi che verranno visualizzati nell'elenco.

Modificare quindi il `WaterfallCollectionView` classe e aggiungere la proprietà helper seguente:

```csharp
public WaterfallCollectionSource Source {
    get { return (WaterfallCollectionSource)DataSource; }
}
```

Ciò renderà più facile ottenere in origine dati (e l'altezza dell'elemento) dal layout personalizzato.

Infine, modificare il controller di visualizzazione e aggiungere il codice seguente:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    var waterfallLayout = new WaterfallCollectionLayout ();

    // Wireup events
    waterfallLayout.SizeForItem += (collectionView, layout, indexPath) => {
        var collection = collectionView as WaterfallCollectionView;
        return new CGSize((View.Bounds.Width-40)/3,collection.Source.Heights[(int)indexPath.Item]);
    };

    // Attach the custom layout to the collection
    CollectionView.SetCollectionViewLayout(waterfallLayout, false);
}
```

Ciò crea un'istanza di layout nostro personalizzato, imposta l'evento da fornire la dimensione di ciascun elemento e associa il nuovo layout per la visualizzazione di raccolta.

Se eseguiamo nuovamente l'app xamarin. IOS, la visualizzazione di raccolta avrà un aspetto simile al seguente:

[![](uicollectionview-images/custom01.png "La visualizzazione di raccolta avrà un aspetto simile al seguente")](uicollectionview-images/custom01.png#lightbox)

È possibile comunque trascinare per riordinare gli elementi come prima, ma gli elementi verranno modificato dimensioni per adattarle al nuovo percorso quando vengono rilasciati.

## <a name="collection-view-changes"></a>Modifiche alla vista raccolta

Nelle sezioni seguenti, prenderemo in un quadro dettagliato le modifiche apportate a ogni classe nella visualizzazione raccolta IOS 9.

### <a name="uicollectionview"></a>UICollectionView

Le seguenti modifiche o aggiunte sono state apportate per il `UICollectionView` classe per iOS 9:

 - `BeginInteractiveMovementForItem` : Contrassegna l'inizio di un'operazione di trascinamento.
 - `CancelInteractiveMovement` : Indica la raccolta Visualizza che l'utente ha annullato un'operazione di trascinamento.
 - `EndInteractiveMovement` : Indica la raccolta Visualizza che l'utente ha terminato un'operazione di trascinamento.
 - `GetIndexPathsForVisibleSupplementaryElements` : Restituisce il `indexPath` di un'intestazione o piè di pagina in una sezione di visualizzazione raccolta.
 - `GetSupplementaryView` : Restituisce l'intestazione specificata o nel piè di pagina.
 - `GetVisibleSupplementaryViews` : Restituisce un elenco di tutti i visibile intestazione e piè di pagina.
 - `UpdateInteractiveMovementTargetPosition` : Indica la raccolta Visualizza che l'utente è stato spostato o si sposta un elemento durante un'operazione di trascinamento.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController

Le seguenti modifiche o aggiunte sono state apportate per il `UICollectionViewController` classe in iOS 9:

 - `InstallsStandardGestureForInteractiveMovement` -Se `true` verrà usato il nuovo riconoscitore di movimento che supporta automaticamente-trascinare per riordinare.
 - `CanMoveItem` : Indica la visualizzazione di raccolta se un elemento specificato è possibile trascinare riordinati.
 - `GetTargetContentOffset` : Usato per ottenere l'offset di un elemento della visualizzazione raccolta specificata.
 - `GetTargetIndexPathForMove` : Ottiene il `indexPath` di un elemento specificato per un'operazione di trascinamento.
 - `MoveItem` – Sposta l'ordine di un elemento specificato nell'elenco.


### <a name="uicollectionviewdatasource"></a>UICollectionViewDataSource

Le seguenti modifiche o aggiunte sono state apportate per il `UICollectionViewDataSource` classe in iOS 9:

 - `CanMoveItem` : Indica la visualizzazione di raccolta se un elemento specificato è possibile trascinare riordinati.
 - `MoveItem` – Sposta l'ordine di un elemento specificato nell'elenco.

### <a name="uicollectionviewdelegate"></a>UICollectionViewDelegate

Le seguenti modifiche o aggiunte sono state apportate per il `UICollectionViewDelegate` classe in iOS 9:

 - `GetTargetContentOffset` : Usato per ottenere l'offset di un elemento della visualizzazione raccolta specificata.
 - `GetTargetIndexPathForMove` : Ottiene il `indexPath` di un elemento specificato per un'operazione di trascinamento.

### <a name="uicollectionviewflowlayout"></a>UICollectionViewFlowLayout

Le seguenti modifiche o aggiunte sono state apportate per il `UICollectionViewFlowLayout` classe in iOS 9:

 - `SectionFootersPinToVisibleBounds` : Non riesco a dimenticare i piè di pagina di sezione per i limiti di visualizzazione raccolta visibile.
 - `SectionHeadersPinToVisibleBounds` : Non riesco a dimenticare le intestazioni di sezione per i limiti di visualizzazione raccolta visibile.

### <a name="uicollectionviewlayout"></a>UICollectionViewLayout

Le seguenti modifiche o aggiunte sono state apportate per il `UICollectionViewLayout` classe in iOS 9:

 - `GetInvalidationContextForEndingInteractiveMovementOfItems` : Restituisce il contesto di invalidamento alla fine di un'operazione di trascinamento quando l'utente termina l'operazione di trascinamento o annullarlo.
 - `GetInvalidationContextForInteractivelyMovingItems` : Restituisce il contesto di invalidamento all'inizio di un'operazione di trascinamento.
 - `GetLayoutAttributesForInteractivelyMovingItem` : Ottiene gli attributi di Layout per un determinato elemento mentre si trascina un elemento.
 - `GetTargetIndexPathForInteractivelyMovingItem` : Restituisce il `indexPath` dell'elemento che si trova il punto specificato quando si trascina un elemento.

### <a name="uicollectionviewlayoutattributes"></a>UICollectionViewLayoutAttributes

Le seguenti modifiche o aggiunte sono state apportate per il `UICollectionViewLayoutAttributes` classe in iOS 9:

 - `CollisionBoundingPath` : Restituisce il percorso di collisione di due elementi durante un'operazione di trascinamento.
 - `CollisionBoundsType` : Restituisce il tipo di collisione (come un `UIDynamicItemCollisionBoundsType`) che si è verificata durante un'operazione di trascinamento.

### <a name="uicollectionviewlayoutinvalidationcontext"></a>UICollectionViewLayoutInvalidationContext

Le seguenti modifiche o aggiunte sono state apportate per il `UICollectionViewLayoutInvalidationContext` classe in iOS 9:

 - `InteractiveMovementTarget` : Restituisce l'elemento di destinazione di un'operazione di trascinamento.
 - `PreviousIndexPathsForInteractivelyMovingItems` : Restituisce il `indexPaths` di altri elementi coinvolti in un trascinare per riordinare l'operazione.
 - `TargetIndexPathsForInteractivelyMovingItems` : Restituisce il `indexPaths` degli elementi che verranno riordinati in seguito a un'operazione di trascinamento-a-riordino.

### <a name="uicollectionviewsource"></a>UICollectionViewSource

Le seguenti modifiche o aggiunte sono state apportate per il `UICollectionViewSource` classe in iOS 9:

 - `CanMoveItem` : Indica la visualizzazione di raccolta se un elemento specificato è possibile trascinare riordinati.
 - `GetTargetContentOffset` : Restituisce l'offset di elementi che verranno spostati tramite un'operazione di trascinamento-a-riordino.
 - `GetTargetIndexPathForMove` : Restituisce il `indexPath` di un elemento che verrà spostato durante un'operazione di trascinamento-a-riordino.
 - `MoveItem` – Sposta l'ordine di un elemento specificato nell'elenco.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato le modifiche apportate alle visualizzazioni di raccolta in iOS 9 e fornite informazioni su come implementarli in xamarin. IOS.
Sia nascosto che implementa una semplice operazione di trascinamento-a-Riordina in una visualizzazione della raccolta. uso di un riconoscimento di movimento personalizzato con trascinare per riordinare; e l'influenza di un layout di visualizzazione di raccolta personalizzato-trascinare per riordinare.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Esempio di visualizzazione di raccolta](https://developer.xamarin.com/samples/monotouch/ios9/CollectionView/)
- [SimpleCollectionView (esempio)](https://developer.xamarin.com/samples/SimpleCollectionView/)
- [Eventi, protocolli e delegati](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Utilizzo di tabelle e celle](~/ios/user-interface/controls/tables/index.md)
