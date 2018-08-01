---
title: Viste di raccolta in xamarin. IOS
description: Le viste di raccolta consentono di contenuto che deve essere visualizzato utilizzando i layout arbitrario. Consentono di creare con facilità il layout di griglia predefinita, supporta i layout personalizzati.
ms.prod: xamarin
ms.assetid: F4B85F25-0CB5-4FEA-A3B5-D22FCDC81AE4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: b9ba2f885364084d6bee67c460b4831c00c7ae55
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790627"
---
# <a name="collection-views-in-xamarinios"></a>Viste di raccolta in xamarin. IOS

_Le viste di raccolta consentono di contenuto che deve essere visualizzato utilizzando i layout arbitrario. Consentono di creare con facilità il layout di griglia predefinita, supporta i layout personalizzati._

Viste di raccolta, disponibile nel `UICollectionView` classe, sono un nuovo concetto in iOS 6 che introducono presenta più elementi sullo schermo utilizzando i layout. I modelli per fornire dati a un `UICollectionView` per creare elementi e interagire con i seguenti elementi la delega stesso e i dati di origine modelli comunemente usati nello sviluppo di iOS.

Tuttavia, le visualizzazioni di raccolta funzionano con un sottosistema di layout che è indipendente il `UICollectionView` stesso. Pertanto, è sufficiente fornire un layout diverso possibile modificare facilmente la presentazione di una visualizzazione di raccolta.

iOS fornisce una classe di layout chiamata `UICollectionViewFlowLayout` che consente di layout basato su riga, ad esempio una griglia da creare senza ulteriore lavoro. Inoltre, layout personalizzati possono anche essere creati consentire qualsiasi presentazione che è possibile immaginare.

## <a name="uicollectionview-basics"></a>Nozioni di base UICollectionView

La `UICollectionView` classe è costituita da tre elementi diversi:

-  **Celle** : visualizzazioni basate sui dati per ogni elemento
-  **Viste supplementari** – visualizzazioni basate sui dati associate a una sezione.
-  **Viste di decorazione** : dati Non basato su viste create da un layout

## <a name="cells"></a>Celle

Le celle sono oggetti che rappresentano un singolo elemento nel set di dati che viene presentato dalla visualizzazione raccolta. Ogni cella è un'istanza di `UICollectionViewCell` (classe), che è composta da tre diverse visualizzazioni, come illustrato nella figura riportata di seguito:

 [![](uicollectionview-images/01-uicollectionviewcell.png "Ogni cella è costituito da tre diverse visualizzazioni, come illustrato di seguito")](uicollectionview-images/01-uicollectionviewcell.png#lightbox)

La `UICollectionViewCell` classe presenta le seguenti proprietà per ognuna di queste visualizzazioni:

-   `ContentView` : Questa vista contiene il contenuto che fornisca la cella. Viene visualizzato nell'ordine z in primo piano sullo schermo.
-   `SelectedBackgroundView` – Celle dispongono del supporto per la selezione incorporato. Questa vista viene utilizzata per indicare visivamente che è stata selezionata una cella. Viene visualizzato di sotto di `ContentView` quando viene selezionata una cella.
-   `BackgroundView` – Celle possono anche visualizzare uno sfondo, presentata dal `BackgroundView` . Questa vista viene eseguito il rendering di sotto di `SelectedBackgroundView` .


Impostando il `ContentView` modo che sia minore di `BackgroundView` e `SelectedBackgroundView`, il `BackgroundView` può essere usato per frame visivamente il contenuto, durante il `SelectedBackgroundView` verrà visualizzato quando è selezionata una cella, come illustrato di seguito:

 [![](uicollectionview-images/02-cells.png "Gli elementi di cella diversa")](uicollectionview-images/02-cells.png#lightbox)

Le celle nella schermata precedente vengono create tramite l'eredità da `UICollectionViewCell` e impostando il `ContentView`, `SelectedBackgroundView` e `BackgroundView` proprietà, rispettivamente, come illustrato nel codice seguente:

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

Le viste supplementari sono visualizzazioni che contengono informazioni associate a ogni sezione di un `UICollectionView`. Come per le celle, le visualizzazioni supplementari sono basati sui dati. Se le celle presentano i dati dell'elemento da un'origine dati, viste supplementari presentare i dati di sezione, ad esempio le categorie di libro su uno scaffale o il genere di musica in un catalogo musicale.

Ad esempio, una vista supplementari sarebbe possibile usare per presentare un'intestazione per una particolare sezione, come illustrato nella figura riportata di seguito:

 [![](uicollectionview-images/02a-supplementary-view.png "Una vista supplementari usato per presentare un'intestazione per una particolare sezione, come illustrato di seguito")](uicollectionview-images/02a-supplementary-view.png#lightbox)

Per utilizzare una vista supplementari, deve prima essere registrato nel `ViewDidLoad` metodo:

```csharp
CollectionView.RegisterClassForSupplementaryView (typeof(Header), UICollectionElementKindSection.Header, headerId);
```

Quindi, deve essere restituito tramite la vista `GetViewForSupplementaryElement`, creato tramite `DequeueReusableSupplementaryView`e che eredita da `UICollectionReusableView`. Il seguente frammento di codice produrrà il SupplementaryView illustrato nella schermata precedente:

```csharp
public override UICollectionReusableView GetViewForSupplementaryElement (UICollectionView collectionView, NSString elementKind, NSIndexPath indexPath)
        {
            var headerView = (Header)collectionView.DequeueReusableSupplementaryView (elementKind, headerId, indexPath);
            headerView.Text = "Supplementary View";
            return headerView;
        }

```

Viste supplementari sono più generiche solo le intestazioni e piè di pagina.
Può essere posizionate in un punto qualsiasi nella visualizzazione raccolta e può essere costituite da visualizzazioni, rendendo il loro aspetto completamente personalizzabile.

 <a name="Decoration_Views" />


## <a name="decoration-views"></a>Decorazione viste

Decorazione visualizzazioni sono puramente visual che possono essere visualizzati in un `UICollectionView`. A differenza delle celle e viste supplementari, essi non sono basate sui dati. Essi vengono sempre creati all'interno di sottoclasse di un layout e successivamente possibile modificare il layout del contenuto. Ad esempio, potrebbe utilizzare una visualizzazione decorazione per presentare una visualizzazione di background che consente di scorrere il contenuto di `UICollectionView`, come illustrato di seguito:

 [![](uicollectionview-images/02c-decoration-view.png "Visualizzazione di decorazione con uno sfondo rosso")](uicollectionview-images/02c-decoration-view.png#lightbox)

 Nel frammento di codice seguente modifica lo sfondo a rosso campioni `CircleLayout` classe:

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

Come con altre parti di iOS, ad esempio `UITableView` e `MKMapView`, `UICollectionView` Ottiene i dati da un *origine dati*, che viene esposto in xamarin. IOS tramite il **`UICollectionViewDataSource`** classe. Questa classe è responsabile di fornire contenuto per il `UICollectionView` , ad esempio:

-  **Celle** – restituito da `GetCell` metodo.
-  **Viste supplementari** – restituito da `GetViewForSupplementaryElement` metodo.
-  **Numero di sezioni** – restituito da `NumberOfSections` metodo. Valore predefinito è 1 se non è implementato.
-  **Numero di elementi per ogni sezione** – restituito da `GetItemsCount` metodo.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController
Per praticità, la `UICollectionViewController` classe è disponibile. Ciò viene configurato automaticamente per essere sia il delegato, come illustrato nella sezione successiva, e l'origine dati per il relativo `UICollectionView` visualizzazione.

Come con `UITableView`, `UICollectionView` classe verrà chiamata solo la propria origine dati per ottenere le celle per gli elementi sullo schermo.
Le celle che scorrere lo schermo vengono inserite a una coda per il riutilizzo, come illustrato nell'immagine seguente:

 [![](uicollectionview-images/03-cell-reuse.png "Le celle che scorrere lo schermo vengono inserite a una coda per il riutilizzo, come illustrato di seguito")](uicollectionview-images/03-cell-reuse.png#lightbox)

Riutilizzo di cella è stato semplificato con `UICollectionView` e `UITableView`. Non è necessario creare una cella direttamente nell'origine dati se non è disponibile nella coda di riutilizzo, come le celle sono registrate con il sistema. Se una cella non è disponibile quando si effettua la chiamata a una cella dalla coda di riutilizzo annullato l'accodamento, iOS creerà automaticamente in base al tipo o nib che è stato registrato.
La stessa tecnica disponibile anche per le viste supplementari.

Ad esempio, si consideri il codice seguente che registra il `AnimalCell` classe:

```csharp
static NSString animalCellId = new NSString ("AnimalCell");
CollectionView.RegisterClassForCell (typeof(AnimalCell), animalCellId);
```

Quando un `UICollectionView` richiede una cella, in quanto l'elemento è visualizzato sullo schermo, il `UICollectionView` chiama la relativa origine di dati `GetCell` metodo. Come per il funzionamento con UITableView, questo metodo è responsabile della configurazione di una cella dai dati di backup, sarebbero un `AnimalCell` classe in questo caso.

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

La chiamata a `DequeReusableCell` è la cella verrà essere sia rimosso dalla coda dalla coda di riutilizzo oppure, se una cella non è disponibile nella coda, creata in base al tipo di cui è registrato nella chiamata a `CollectionView.RegisterClassForCell`.

In questo caso, registrando il `AnimalCell` (classe), iOS verrà creato un nuovo `AnimalCell` internamente e restituirlo quando viene effettuata una chiamata a una cella annullato l'accodamento, dopo il quale viene configurato con l'immagine contenuta nella classe di animali e restituiti per la visualizzazione per il `UICollectionView`.

 <a name="Delegate" />


### <a name="delegate"></a>delegato

Il `UICollectionView` classe Usa un delegato del tipo `UICollectionViewDelegate` per supportare l'interazione con il contenuto di `UICollectionView`. In questo modo di controllo di:

-  **Selezione delle celle** : determina se è selezionata una cella.
-  **Evidenziazione delle celle** : determina se una cella è attualmente interessata.
-  **Cella menu** : dal Menu visualizzato per una cella in risposta a un movimento premere lungo.


Come con l'origine dati, il `UICollectionViewController` è configurato per impostazione predefinita per il delegato per il `UICollectionView`.

 <a name="Cell_HighLighting" />


#### <a name="cell-highlighting"></a>Evidenziazione delle celle

Quando viene premuta una cella, le transizioni di cella in uno stato evidenziato, e non è selezionato fino a quando l'utente solleva il dito dalla cella. In questo modo una modifica temporanea l'aspetto della cella prima che venga effettivamente selezionato. Al momento, la cella della selezione `SelectedBackgroundView` viene visualizzato. La figura seguente mostra lo stato evidenziato appena prima della selezione:

 [![](uicollectionview-images/04-cell-highlight.png "Questa figura mostra lo stato evidenziato prima che si verifica la selezione")](uicollectionview-images/04-cell-highlight.png#lightbox)

Per implementare l'evidenziazione, il `ItemHighlighted` e `ItemUnhighlighted` metodi di `UICollectionViewDelegate` può essere utilizzato. Ad esempio, il codice seguente verrà applicata uno sfondo giallo del `ContentView` quando la cella è evidenziata e uno sfondo bianco quando non è evidenziato, come illustrato nell'immagine precedente:

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

La selezione è abilitata per impostazione predefinita in `UICollectionView`. Per disattivare la selezione, eseguire l'override `ShouldHighlightItem` e restituire false come illustrato di seguito:

```csharp
public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath)
{
        return false;
}
```

Quando l'evidenziazione viene disattivata, è disattivato anche il processo di selezione di una cella. Inoltre, è inoltre disponibile un `ShouldSelectItem` metodo che controlla la selezione direttamente, anche se se `ShouldHighlightItem` viene implementato e restituisce false, `ShouldSelectItem` non viene chiamato.

 `ShouldSelectItem` Consente di attivare o disattivare un'elemento per elemento di base, la selezione quando `ShouldHighlightItem` non implementato. Consente anche l'evidenziazione senza selezione, se `ShouldHighlightItem` viene implementato e restituisce true, mentre `ShouldSelectItem` restituisce false.

 <a name="Cell_Menus" />


#### <a name="cell-menus"></a>Menu di cella

Ogni cella di un `UICollectionView` è in grado di visualizzare un menu che consente le operazioni Taglia, copia e Incolla facoltativamente essere supportati. Per creare un menu di modifica in una cella:

1.  Eseguire l'override `ShouldShowMenu` e restituisce true se l'elemento deve essere visualizzato un menu.
1.  Eseguire l'override `CanPerformAction` e restituire true per ogni azione che è possibile eseguire l'elemento, che può essere una delle operazioni Taglia, copia o Incolla.
1.  Eseguire l'override `PerformAction` per eseguire la modifica, copia dell'operazione Incolla.


Nella schermata seguente mostra il menu di tempo pressione di una cella:

 [![](uicollectionview-images/04a-menu.png "Questa schermata mostra il menu di tempo pressione di una cella")](uicollectionview-images/04a-menu.png#lightbox)

 <a name="Layout" />


## <a name="layout"></a>Layout

`UICollectionView` supporta un sistema di layout che consente il posizionamento di tutti gli elementi, celle, viste supplementari e viste decorazione per essere gestito indipendentemente dal `UICollectionView` stesso.
Usa il sistema di layout, un'applicazione può supportare layout, ad esempio quello della griglia è stato illustrato in questo articolo, nonché fornire layout personalizzati.

 <a name="Layout_Basics" />


### <a name="layout-basics"></a>Nozioni di base di layout

Layout in un `UICollectionView` sono definiti in una classe che eredita da `UICollectionViewLayout`. L'implementazione del layout è responsabile per la creazione di attributi di layout per tutti gli elementi di `UICollectionView`. Esistono due modi per creare un layout:

-  Utilizzare l'oggetto incorporato `UICollectionViewFlowLayout` .
-  Fornire un layout personalizzato ereditando dalla `UICollectionViewLayout` .


 <a name="Flow_Layout" />


### <a name="flow-layout"></a>Layout di flusso

La `UICollectionViewFlowLayout` classe fornisce un layout che è adatto per la disposizione del contenuto in una griglia di celle come abbiamo visto basata su riga.

Per utilizzare un layout di flusso:

-  Creare un'istanza di `UICollectionViewFlowLayout` :


```csharp
var layout = new UICollectionViewFlowLayout ();
```

-  Passare al costruttore di istanza di `UICollectionView` :


```csharp
simpleCollectionViewController = new SimpleCollectionViewController (layout);
```

Questo è tutto ciò che è necessario per il layout contenuto in una griglia. Inoltre, quando l'orientamento viene modificata, il `UICollectionViewFlowLayout` gestisce ridisposizione il contenuto in modo appropriato, come illustrato di seguito:

 [![](uicollectionview-images/05-layout-orientation.png "Ad esempio le modifiche di orientamento")](uicollectionview-images/05-layout-orientation.png#lightbox)

 <a name="Section_Inset" />


#### <a name="section-inset"></a>Inserimento di sezione

Per fornire spazio attorno il `UIContentView`, layout dispongono di un `SectionInset` proprietà di tipo `UIEdgeInsets`. Ad esempio, il codice seguente fornisce un buffer di 50 pixel relativo a ogni sezione del `UIContentView` quando definito da un `UICollectionViewFlowLayout`:

```csharp
var layout = new UICollectionViewFlowLayout ();
layout.SectionInset = new UIEdgeInsets (50,50,50,50);
```

Ciò comporta la spaziatura intorno alla sezione come illustrato di seguito:

 [![](uicollectionview-images/06-sectioninset.png "Spaziatura intorno alla sezione, come illustrato di seguito")](uicollectionview-images/06-sectioninset.png#lightbox)

 <a name="Subclassing_UICollectionViewFlowLayout" />


#### <a name="subclassing-uicollectionviewflowlayout"></a>Creazione di sottoclassi UICollectionViewFlowLayout

Nell'edizione all'utilizzo `UICollectionViewFlowLayout` direttamente, possono anche essere sottoclassato per personalizzare ulteriormente il layout del contenuto lungo una linea. Ad esempio, utilizzabile per creare un layout che non va a capo le celle in una griglia, ma crea una singola riga con un effetto di scorrimento orizzontale, come illustrato di seguito:

 [![](uicollectionview-images/07-line-layout.png "Una singola riga con un effetto di scorrimento orizzontale")](uicollectionview-images/07-line-layout.png#lightbox)

Per implementare questa creando sottoclassi `UICollectionViewFlowLayout` richiede:

-  Inizializzazione di qualsiasi proprietà di layout che si applicano al layout di se stesso o tutti gli elementi di layout nel costruttore.
-  Si esegue l'override `ShouldInvalidateLayoutForBoundsChange` , con valore true, che quando limiti del `UICollectionView` verranno ricalcolate le modifiche, il layout delle celle. Viene utilizzato in questo caso verificare il codice di trasformazione applicato alla cella vicino verrà applicati durante lo scorrimento.
-  Si esegue l'override `TargetContentOffset` per rendere il vicino snap al centro della cella di `UICollectionView` come lo scorrimento viene arrestata.
-  Si esegue l'override `LayoutAttributesForElementsInRect` per restituire una matrice di `UICollectionViewLayoutAttributes` . Ogni `UICollectionViewLayoutAttribute` contiene informazioni su come layout di un determinato articolo, comprese le proprietà, ad esempio il `Center` , `Size` , `ZIndex` e `Transform3D` .


Il codice seguente viene illustrata un'implementazione di questo tipo:

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


### <a name="custom-layout"></a>Layout personalizzati

Oltre a utilizzare `UICollectionViewFlowLayout`, layout inoltre può essere completamente personalizzato ereditando direttamente da `UICollectionViewLayout`.

I metodi principali per eseguire l'override sono:

-   `PrepareLayout` -Viene usato per eseguire calcoli geometrici iniziali che verranno utilizzati durante il processo di layout.
-   `CollectionViewContentSize` : Restituisce le dimensioni dell'area utilizzata per visualizzare il contenuto.
-   `LayoutAttributesForElementsInRect` – Come con l'esempio UICollectionViewFlowLayout illustrato in precedenza, questo metodo viene utilizzato per fornire informazioni per il `UICollectionView` relative al layout di ogni elemento. Tuttavia, a differenza di `UICollectionViewFlowLayout` , quando si crea un layout personalizzato, è possibile posizionare gli elementi desiderato.


Ad esempio, può presentarsi lo stesso contenuto in un layout circolare come illustrato di seguito:

 [![](uicollectionview-images/08-circle-layout.png "Un oggetto personalizzato circolare layout, come illustrato di seguito")](uicollectionview-images/08-circle-layout.png#lightbox)

Il layout, potente interessante è che per modificare dal layout griglia, in un layout di scorrimento orizzontale e successivamente a questo layout circolare richiede solo la classe di layout fornita per il `UICollectionView` essere modificato. Niente di `UICollectionView`, il delegato o origine dati delle modifiche al codice affatto.


## <a name="changes-in-ios-9"></a>Modifiche in iOS 9


In iOS 9, la visualizzazione della raccolta (`UICollectionView`) trascina ora supporta riordinamento degli elementi predefinita aggiungendo un nuovo riconoscitore di movimento predefinito e nuovi metodi di supporto.

Usa questi nuovi metodi, è possibile implementare trascinare per riordinare la visualizzazione di raccolta e hanno la possibilità di personalizzazione dell'aspetto di elementi in qualsiasi fase del processo di riordinamento.

[![](uicollectionview-images/intro01.png "Un esempio del processo di riordino")](uicollectionview-images/intro01.png#lightbox)

In questo articolo, si verrà esaminato un implementazione trascinare-a-Riordina in un'applicazione di xamarin. IOS, nonché alcune delle modifiche iOS 9 apportate al controllo di visualizzazione di raccolta:

- [Facile riordinamento degli elementi](#Easy-Reordering-of-Items)
    - [Esempio semplice di riordino](#Simple-Reordering-Example)
    - [Utilizzo di un riconoscimento di movimenti personalizzati](#Using-a-Custom-Gesture-Recognizer)
    - [Layout personalizzati e riordinamento](#Custom-Layouts-and-Reording)
- [Visualizzazione di raccolta](#Collection-View-Changes)

<a name="Easy-Reordering-of-Items" />

## <a name="reordering-of-items"></a>Riordinamento degli elementi

Come indicato in precedenza, una delle modifiche più significative per la visualizzazione della raccolta in iOS 9 era l'aggiunta di funzionalità di trascinamento-a-Riordina semplice predefinita.

In iOS 9, il modo più rapido per aggiungere il riordinamento di una visualizzazione di raccolta consiste nell'utilizzare un `UICollectionViewController`.
Il controller di visualizzazione di raccolta ha ora un `InstallsStandardGestureForInteractiveMovement` proprietà, che aggiunge uno standard *riconoscitore di movimento* che supporta il trascinamento per riordinare gli elementi nella raccolta.
Poiché il valore predefinito è `true`, è necessario implementare la `MoveItem` metodo la `UICollectionViewDataSource` classe per il supporto di trascinamento-Riordina. Ad esempio:

```csharp
public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    // Reorder our list of items
    ...
}
```
<a name="Simple-Reordering-Example" />

### <a name="simple-reordering-example"></a>Esempio semplice di riordino

Come un semplice esempio, avviare un nuovo progetto xamarin e modificare il **Main** file. Trascinare un `UICollectionViewController` nell'area di progettazione:

[![](uicollectionview-images/quick01.png "Aggiunta di un UICollectionViewController")](uicollectionview-images/quick01.png#lightbox)

Selezionare la visualizzazione di raccolta (potrebbe essere più semplice eseguire questa operazione dalla struttura del documento). Nella scheda layout del riquadro Proprietà impostare le dimensioni seguenti, come illustrato nella schermata seguente:

- **Dimensioni della cella**: Width-60 | Altezza: 60
- **Dimensioni dell'intestazione**: larghezza: 0 | Height: 0
- **Dimensioni del piè di pagina**: larghezza: 0 | Height: 0
- **Spaziatura min**: per le celle – 8 | Per le righe, 8
- **Sezione Inset**: inizio – 16 | Fine: 16 | A sinistra: 16 | Right-16

[![](uicollectionview-images/quick04.png "Impostare le dimensioni di visualizzazione di raccolta")](uicollectionview-images/quick04.png#lightbox)

Successivamente, modificare il valore predefinito di cella:
    - Modificare il colore di sfondo su blu
    - Aggiungere un'etichetta come il titolo per la cella
    - Impostare l'identificatore di riutilizzo su **cella**

[![](uicollectionview-images/quick02.png "Modificare il valore predefinito di celle")](uicollectionview-images/quick02.png#lightbox)

Aggiungere vincoli per mantenere l'etichetta centrato all'interno della cella e le relative modifiche dimensioni:

Nel **proprietà riempimento** per il _CollectionViewCell_ e impostare il **classe** a `TextCollectionViewCell`:

[![](uicollectionview-images/quick05.png "Impostare la classe su TextCollectionViewCell")](uicollectionview-images/quick05.png#lightbox)

Impostare il **visualizzazione riutilizzabili raccolta** a `Cell`:

[![](uicollectionview-images/quick06.png "Impostare la visualizzazione di raccolta riutilizzabile di cella")](uicollectionview-images/quick06.png#lightbox)

Infine, selezionare l'etichetta e denominarlo `TextLabel`:

[![](uicollectionview-images/quick07.png "etichetta nome TextLabel")](uicollectionview-images/quick07.png#lightbox)

Modificare la `TextCollectionViewCell` classe e aggiungere le seguenti proprietà.:

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

In questo caso il `Text` proprietà dell'etichetta viene visualizzata come titolo della cella, pertanto può essere impostata dal codice.

Aggiungere una nuova classe c# per il progetto e chiamarlo `WaterfallCollectionSource`. Modificare il file e renderlo simile al seguente:

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
Si noti che il `MoveItem` metodo viene implementato per consentire trascinare gli elementi della raccolta per essere riordinati.

Aggiungere un'altra nuova classe c# per il progetto e chiamarlo `WaterfallCollectionDelegate`. Modificare il file e renderlo simile al seguente:

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

Questa assumerà il delegato per la visualizzazione della raccolta. Metodi sottoposti a override per evidenziare una cella, mentre l'utente interagisce con esso, nella visualizzazione raccolta.

Aggiungere un ultimo c# classe al progetto e chiamarlo `WaterfallCollectionView`. Modificare il file e renderlo simile al seguente:

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

Si noti che `DataSource` e `Delegate` creati in precedenza vengono impostate quando viene costruita la visualizzazione della raccolta dello storyboard (o **.xib** file).

Modificare il **Main** file nuovo e selezionare la visualizzazione di raccolta e passare il **proprietà**. Impostare il **classe** su un oggetto personalizzato `WaterfallCollectionView` classe definita in precedenza:

Salvare le modifiche apportate all'interfaccia utente ed eseguire l'app.
Se l'utente seleziona un elemento dall'elenco e trascina in un nuovo percorso, gli altri elementi verranno automaticamente animare durante lo spostamento in modo dell'elemento.
Quando l'utente rilascia l'elemento in un nuovo percorso, verrà soffermeremo tale percorso. Ad esempio:

[![](uicollectionview-images/intro01.png "Un esempio di trascinamento di un elemento in una nuova posizione")](uicollectionview-images/intro01.png#lightbox)

<a name="Using-a-Custom-Gesture-Recognizer" />

### <a name="using-a-custom-gesture-recognizer"></a>Utilizzo di un riconoscimento di movimenti personalizzati

Nei casi in cui non è possibile utilizzare un `UICollectionViewController` e deve utilizzare una normale `UIViewController`, o se si desidera maggiore controllo sui movimenti di trascinamento e rilascio, è possibile creare la propria riconoscitore di movimento personalizzato e aggiungerlo alla vista raccolta quando viene caricata la visualizzazione. Ad esempio:

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

Alcuni nuovi metodi aggiunti alla vista raccolta qui viene utilizzato per implementare e controllare l'operazione di trascinamento:

 - `BeginInteractiveMovementForItem` -Contrassegna l'inizio di un'operazione di spostamento.
 - `UpdateInteractiveMovementTargetPosition` -Viene inviato come la posizione dell'elemento viene aggiornata.
 - `EndInteractiveMovement` -Contrassegna la fine di spostamento di un elemento.
 - `CancelInteractiveMovement` -Contrassegna l'utente di annullare l'operazione di spostamento.

Quando l'applicazione viene eseguita, l'operazione di trascinamento funziona esattamente come il valore predefinito trascinare riconoscitore di movimento che viene fornito con la visualizzazione della raccolta.

<a name="Custom-Layouts-and-Reording" />

### <a name="custom-layouts-and-reordering"></a>Layout personalizzati e riordinamento

In iOS 9, alcuni nuovi metodi sono stati aggiunti con layout a trascinare-Riordina e personalizzate in una visualizzazione di raccolta. Per esplorare questa funzionalità, aggiungere un layout personalizzato alla raccolta.

Aggiungere innanzitutto una nuova classe c# denominata `WaterfallCollectionLayout` al progetto. Apportare le modifiche e renderlo simile al seguente:

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

Può essere utilizzata una classe di fornire un personalizzato due colonne, il layout del tipo di grafico a cascata per la visualizzazione della raccolta.
Il codice utilizza la codifica chiave-valore (tramite il `WillChangeValue` e `DidChangeValue` metodi) per fornire il data binding per la proprietà calcolate in questa classe.

Successivamente, modificare il `WaterfallCollectionSource` e apportare le modifiche e le aggiunte seguenti:

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

Verrà creata un'altezza casuale per ogni elemento che verrà visualizzato nell'elenco.

Successivamente, modificare il `WaterfallCollectionView` classe e aggiungere la proprietà di supporto seguenti:

```csharp
public WaterfallCollectionSource Source {
    get { return (WaterfallCollectionSource)DataSource; }
}
```

Questo rende più semplice ottenere l'origine dati (e l'altezza dell'elemento) da un layout personalizzato.

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

Questo crea un'istanza di questo layout personalizzato, imposta l'evento per fornire le dimensioni di ogni elemento e associa il nuovo layout per la visualizzazione della raccolta.

Se si esegue nuovamente l'app xamarin. IOS, la visualizzazione della raccolta sarà ora simile al seguente:

[![](uicollectionview-images/custom01.png "La visualizzazione raccolta avrà un aspetto simile al seguente")](uicollectionview-images/custom01.png#lightbox)

È possibile comunque trascinare per riordinare gli elementi come prima, ma gli elementi verranno modificata adatta al nuovo percorso quando vengono rilasciati.

## <a name="collection-view-changes"></a>Visualizzazione di raccolta

Nelle sezioni seguenti, prenderemo in un'analisi approfondita le modifiche apportate a ogni classe nella visualizzazione raccolta da iOS 9.

### <a name="uicollectionview"></a>UICollectionView

Le seguenti modifiche o le aggiunte apportate alla `UICollectionView` classe per iOS 9:

 - `BeginInteractiveMovementForItem` -Contrassegna l'inizio di un'operazione di trascinamento.
 - `CancelInteractiveMovement` : Indica la raccolta di visualizzazione che l'utente ha annullato l'operazione di trascinamento.
 - `EndInteractiveMovement` : Indica la raccolta di visualizzazione che l'utente ha terminato un'operazione di trascinamento.
 - `GetIndexPathsForVisibleSupplementaryElements` : Restituisce il `indexPath` di un'intestazione o piè di pagina in una sezione di visualizzazione di raccolta.
 - `GetSupplementaryView` : Restituisce l'intestazione specificata o il piè di pagina.
 - `GetVisibleSupplementaryViews` : Restituisce un elenco di tutti i visibile intestazione e piè di pagina.
 - `UpdateInteractiveMovementTargetPosition` : Indica la raccolta di visualizzazione che l'utente è stato spostato o viene spostato, un elemento durante un'operazione di trascinamento.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController

Le seguenti modifiche o le aggiunte apportate alla `UICollectionViewController` classe iOS 9:

 - `InstallsStandardGestureForInteractiveMovement` -Se `true` verrà utilizzato il nuovo riconoscitore di movimento che trascinare-a-Riordina sono automaticamente supportati.
 - `CanMoveItem` : Indica la visualizzazione di raccolta se un determinato elemento può essere trascinamento riordinato.
 - `GetTargetContentOffset` : Consente di ottenere l'offset di un elemento di visualizzazione di raccolta specificato.
 - `GetTargetIndexPathForMove` : Ottiene il `indexPath` di un elemento specificato per un'operazione di trascinamento.
 - `MoveItem` -Sposta l'ordine di un elemento specificato nell'elenco.


### <a name="uicollectionviewdatasource"></a>UICollectionViewDataSource

Le seguenti modifiche o le aggiunte apportate alla `UICollectionViewDataSource` classe iOS 9:

 - `CanMoveItem` : Indica la visualizzazione di raccolta se un determinato elemento può essere trascinamento riordinato.
 - `MoveItem` -Sposta l'ordine di un elemento specificato nell'elenco.

### <a name="uicollectionviewdelegate"></a>UICollectionViewDelegate

Le seguenti modifiche o le aggiunte apportate alla `UICollectionViewDelegate` classe iOS 9:

 - `GetTargetContentOffset` : Consente di ottenere l'offset di un elemento di visualizzazione di raccolta specificato.
 - `GetTargetIndexPathForMove` : Ottiene il `indexPath` di un elemento specificato per un'operazione di trascinamento.

### <a name="uicollectionviewflowlayout"></a>UICollectionViewFlowLayout

Le seguenti modifiche o le aggiunte apportate alla `UICollectionViewFlowLayout` classe iOS 9:

 - `SectionFootersPinToVisibleBounds` – Stick piè di pagina sezione ai limiti di visualizzazione visibile di raccolta.
 - `SectionHeadersPinToVisibleBounds` – Stick intestazioni di sezione per i limiti di visualizzazione visibile di raccolta.

### <a name="uicollectionviewlayout"></a>UICollectionViewLayout

Le seguenti modifiche o le aggiunte apportate alla `UICollectionViewLayout` classe iOS 9:

 - `GetInvalidationContextForEndingInteractiveMovementOfItems` : Restituisce il contesto di invalidamento alla fine di un'operazione di trascinamento quando l'utente non viene completata l'operazione di trascinamento o Annulla.
 - `GetInvalidationContextForInteractivelyMovingItems` : Restituisce il contesto di invalidamento all'inizio di un'operazione di trascinamento.
 - `GetLayoutAttributesForInteractivelyMovingItem` : Ottiene gli attributi di Layout per un determinato elemento mentre si trascina un elemento.
 - `GetTargetIndexPathForInteractivelyMovingItem` : Restituisce il `indexPath` dell'elemento che si trova il punto specificato quando si trascina un elemento.

### <a name="uicollectionviewlayoutattributes"></a>UICollectionViewLayoutAttributes

Le seguenti modifiche o le aggiunte apportate alla `UICollectionViewLayoutAttributes` classe iOS 9:

 - `CollisionBoundingPath` : Restituisce il percorso di conflitto di due elementi durante un'operazione di trascinamento.
 - `CollisionBoundsType` : Restituisce il tipo di conflitto (come un `UIDynamicItemCollisionBoundsType`) che si è verificato durante un'operazione di trascinamento.

### <a name="uicollectionviewlayoutinvalidationcontext"></a>UICollectionViewLayoutInvalidationContext

Le seguenti modifiche o le aggiunte apportate alla `UICollectionViewLayoutInvalidationContext` classe iOS 9:

 - `InteractiveMovementTarget` : Restituisce l'elemento di destinazione di un'operazione di trascinamento.
 - `PreviousIndexPathsForInteractivelyMovingItems` : Restituisce il `indexPaths` di altri elementi coinvolti in un trascinamento per riordinare l'operazione.
 - `TargetIndexPathsForInteractivelyMovingItems` : Restituisce il `indexPaths` di elementi che verranno riordinati in seguito al trascinamento di riordinamento.

### <a name="uicollectionviewsource"></a>UICollectionViewSource

Le seguenti modifiche o le aggiunte apportate alla `UICollectionViewSource` classe iOS 9:

 - `CanMoveItem` : Indica la visualizzazione di raccolta se un determinato elemento può essere trascinamento riordinato.
 - `GetTargetContentOffset` : Restituisce gli offset di elementi che verranno trasferiti tramite trascinamento di riordinamento.
 - `GetTargetIndexPathForMove` : Restituisce il `indexPath` di un elemento che verrà spostato durante il trascinamento di riordinamento.
 - `MoveItem` -Sposta l'ordine di un elemento specificato nell'elenco.

## <a name="summary"></a>Riepilogo

In questo articolo ha interessato le modifiche apportate alle visualizzazioni di raccolta di iOS 9 e descritto come implementarli in xamarin. IOS.
Che trattato l'implementazione di un'azione di trascinamento-a-Riordina semplice in una visualizzazione della raccolta. utilizzo di un riconoscimento di movimento personalizzato con trascinamento per riordinare. e l'influenza di un layout di visualizzazione di raccolta personalizzato per trascinare-riordinare.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Esempio di visualizzazione di raccolta](https://developer.xamarin.com/samples/monotouch/ios9/CollectionView/)
- [SimpleCollectionView (esempio)](https://developer.xamarin.com/samples/SimpleCollectionView/)
- [Eventi, protocolli e delegati](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Utilizzo di tabelle e le celle](~/ios/user-interface/controls/tables/index.md)
