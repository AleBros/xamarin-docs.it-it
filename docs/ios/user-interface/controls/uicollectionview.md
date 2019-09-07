---
title: Viste di raccolta in Novell. iOS
description: Le visualizzazioni di raccolta consentono di visualizzare il contenuto utilizzando layout arbitrari. Consentono di creare facilmente layout simili a griglia, supportando al tempo stesso layout personalizzati.
ms.prod: xamarin
ms.assetid: F4B85F25-0CB5-4FEA-A3B5-D22FCDC81AE4
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/20/2017
ms.openlocfilehash: 29483ca184786f8f0d2a7d0d9345608542d30e61
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768849"
---
# <a name="collection-views-in-xamarinios"></a>Viste di raccolta in Novell. iOS

_Le visualizzazioni di raccolta consentono di visualizzare il contenuto utilizzando layout arbitrari. Consentono di creare facilmente layout simili a griglia, supportando al tempo stesso layout personalizzati._

Le `UICollectionView` visualizzazioni di raccolta, disponibili nella classe, sono un nuovo concetto di iOS 6 che introduce la presentazione di più elementi sullo schermo usando i layout. Gli schemi per fornire i dati a `UICollectionView` un oggetto per creare elementi e interagire con tali elementi seguono gli stessi modelli di delega e origine dati usati comunemente nello sviluppo iOS.

Tuttavia, le visualizzazioni di raccolta funzionano con un sottosistema di layout indipendente dallo `UICollectionView` stesso. Pertanto, la semplice creazione di un layout diverso può facilmente modificare la presentazione di una visualizzazione di raccolta.

iOS fornisce una classe layout denominata `UICollectionViewFlowLayout` che consente la creazione di layout basati su riga, ad esempio una griglia senza ulteriori operazioni. Inoltre, è possibile creare layout personalizzati che consentano qualsiasi presentazione che si possa immaginare.

## <a name="uicollectionview-basics"></a>Nozioni fondamentali su UICollectionView

La `UICollectionView` classe è costituita da tre elementi diversi:

- **Celle** : viste basate sui dati per ogni elemento
- **Visualizzazioni aggiuntive** : viste basate sui dati associate a una sezione.
- **Visualizzazioni di decorazione** : viste non basate sui dati create da un layout

## <a name="cells"></a>Celle

Le celle sono oggetti che rappresentano un singolo elemento nel set di dati presentato dalla visualizzazione della raccolta. Ogni cella è un'istanza della `UICollectionViewCell` classe, che è costituita da tre visualizzazioni diverse, come illustrato nella figura seguente:

 [![](uicollectionview-images/01-uicollectionviewcell.png "Ogni cella è costituita da tre visualizzazioni diverse, come illustrato di seguito.")](uicollectionview-images/01-uicollectionviewcell.png#lightbox)

La `UICollectionViewCell` classe dispone delle proprietà seguenti per ognuna di queste visualizzazioni:

- `ContentView`: Questa vista contiene il contenuto presente nella cella. Viene eseguito il rendering nell'ordine z in primo piano sullo schermo.
- `SelectedBackgroundView`: Le celle hanno incorporato il supporto per la selezione. Questa vista consente di indicare visivamente che è selezionata una cella. Ne viene eseguito il rendering immediatamente `ContentView` dopo la selezione di una cella.
- `BackgroundView`: Le celle possono anche visualizzare uno sfondo, presentato da `BackgroundView` . Questa vista viene sottoposta `SelectedBackgroundView` a rendering sotto.

Impostando su `ContentView` un valore inferiore `BackgroundView` rispetto a e `SelectedBackgroundView`, è possibile utilizzare `BackgroundView` l'oggetto per incorniciare visivamente il contenuto, mentre l' `SelectedBackgroundView` oggetto verrà visualizzato quando viene selezionata una cella, come illustrato di seguito:

 [![](uicollectionview-images/02-cells.png "Elementi di celle diversi")](uicollectionview-images/02-cells.png#lightbox)

Le celle dello screenshot precedente vengono `UICollectionViewCell` create ereditando da e impostando rispettivamente le `ContentView`proprietà, `BackgroundView` `SelectedBackgroundView` e, come illustrato nel codice seguente:

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

## <a name="supplementary-views"></a>Visualizzazioni supplementari

Le visualizzazioni supplementari sono viste che presentano informazioni associate a ogni sezione di un `UICollectionView`oggetto. Analogamente alle celle, le visualizzazioni supplementari sono basate sui dati. Dove le celle presentano i dati dell'elemento da un'origine dati, le visualizzazioni supplementari presentano i dati della sezione, ad esempio le categorie di libri in uno scaffale o il genere di musica in una raccolta musicale.

Ad esempio, è possibile usare una visualizzazione aggiuntiva per presentare un'intestazione per una particolare sezione, come illustrato nella figura seguente:

 [![](uicollectionview-images/02a-supplementary-view.png "Visualizzazione supplementare utilizzata per presentare un'intestazione per una particolare sezione, come illustrato di seguito.")](uicollectionview-images/02a-supplementary-view.png#lightbox)

Per usare una visualizzazione supplementare, è prima necessario registrarla nel `ViewDidLoad` metodo:

```csharp
CollectionView.RegisterClassForSupplementaryView (typeof(Header), UICollectionElementKindSection.Header, headerId);
```

Quindi, la vista deve essere restituita usando `GetViewForSupplementaryElement`, creato usando `DequeueReusableSupplementaryView`ed eredita da `UICollectionReusableView`. Il seguente frammento di codice produrrà il SupplementaryView illustrato nella schermata precedente:

```csharp
public override UICollectionReusableView GetViewForSupplementaryElement (UICollectionView collectionView, NSString elementKind, NSIndexPath indexPath)
        {
            var headerView = (Header)collectionView.DequeueReusableSupplementaryView (elementKind, headerId, indexPath);
            headerView.Text = "Supplementary View";
            return headerView;
        }

```

Le visualizzazioni supplementari sono più generiche rispetto a intestazioni e piè di pagina.
Possono essere posizionati in qualsiasi punto della visualizzazione di raccolta e possono essere costituiti da qualsiasi visualizzazione, rendendone l'aspetto completamente personalizzabile.

 <a name="Decoration_Views" />

## <a name="decoration-views"></a>Visualizzazioni di decorazione

Le visualizzazioni di decorazione sono visualizzazioni puramente visive che possono essere `UICollectionView`visualizzate in un oggetto. Diversamente dalle celle e dalle visualizzazioni supplementari, non sono basate sui dati. Vengono sempre creati all'interno di una sottoclasse del layout e successivamente possono cambiare come il layout del contenuto. Ad esempio, è possibile usare una visualizzazione decorativa per presentare una visualizzazione in background che scorre con il contenuto di `UICollectionView`, come mostrato di seguito:

 [![](uicollectionview-images/02c-decoration-view.png "Visualizzazione della decorazione con sfondo rosso")](uicollectionview-images/02c-decoration-view.png#lightbox)

 Il frammento di codice seguente modifica lo sfondo in rosso nella `CircleLayout` classe Samples:

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

Come per le altre parti di iOS, ad `UITableView` esempio `MKMapView`e `UICollectionView` , ottiene i dati da un' *origine dati*esposta in Novell. iOS tramite la **`UICollectionViewDataSource`** classe. Questa classe è responsabile della fornitura di contenuto a `UICollectionView` , ad esempio:

- **Celle** : restituite `GetCell` dal metodo.
- **Viste supplementari** : restituite dal `GetViewForSupplementaryElement` metodo.
- **Numero di sezioni** : restituite `NumberOfSections` dal metodo. Il valore predefinito è 1 se non è implementato.
- **Numero di elementi per sezione** : restituiti dal `GetItemsCount` metodo.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController
Per praticità, `UICollectionViewController` la classe è disponibile. Questa configurazione viene configurata automaticamente in modo che sia il delegato, illustrato nella sezione successiva, e l'origine dati `UICollectionView` per la visualizzazione.

Come con `UITableView`, la `UICollectionView` classe chiamerà solo l'origine dati per ottenere le celle per gli elementi presenti sullo schermo.
Le celle che scorrono sullo schermo vengono inserite in una coda per riutilizzarle, come illustrato nella figura seguente:

 [![](uicollectionview-images/03-cell-reuse.png "Le celle che scorrono dallo schermo vengono inserite in una coda per il riutilizzo come illustrato di seguito.")](uicollectionview-images/03-cell-reuse.png#lightbox)

Il riutilizzo delle celle è `UICollectionView` stato `UITableView`semplificato con e. Non è più necessario creare una cella direttamente nell'origine dati, se non è disponibile nella coda di riutilizzo, perché le celle sono registrate con il sistema. Se una cella non è disponibile quando si effettua la chiamata a rimuovere la coda della cella dalla coda di riutilizzo, iOS lo creerà automaticamente in base al tipo o al pennino registrato.
La stessa tecnica è disponibile anche per le visualizzazioni supplementari.

Si consideri, ad esempio, il codice `AnimalCell` seguente che registra la classe:

```csharp
static NSString animalCellId = new NSString ("AnimalCell");
CollectionView.RegisterClassForCell (typeof(AnimalCell), animalCellId);
```

Quando un `UICollectionView` richiede una cella perché il relativo elemento si trova sullo schermo, `UICollectionView` `GetCell` chiama il metodo dell'origine dati. Analogamente a quanto avviene con UITableView, questo metodo è responsabile della configurazione di una cella dai dati di supporto, che in questo caso `AnimalCell` sarebbe una classe.

Nel codice seguente viene illustrata un' `GetCell` implementazione di che `AnimalCell` restituisce un'istanza:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, Foundation.NSIndexPath indexPath)
{
        var animalCell = (AnimalCell)collectionView.DequeueReusableCell (animalCellId, indexPath);

        var animal = animals [indexPath.Row];

        animalCell.Image = animal.Image;

        return animalCell;
}
```

La chiamata a `DequeReusableCell` è la posizione in cui la cella verrà deselezionata dalla coda di riutilizzo o, se una cella non è disponibile nella coda, creata in base al tipo registrato nella chiamata a. `CollectionView.RegisterClassForCell`

In questo caso, registrando la `AnimalCell` classe, iOS creerà un nuovo `AnimalCell` oggetto internamente e lo restituirà quando viene effettuata una chiamata a rimuovere la coda di una cella, dopo la quale viene configurata con l'immagine contenuta nella classe Animal e restituita per la visualizzazioneal`UICollectionView`.

 <a name="Delegate" />

### <a name="delegate"></a>delegato

La `UICollectionView` classe usa un delegato di tipo `UICollectionViewDelegate` per supportare l'interazione con il `UICollectionView`contenuto in. Questo consente il controllo di:

- **Selezione cella** : determina se una cella è selezionata.
- **Evidenziazione celle** : determina se è in corso il tocco di una cella.
- Menu **delle celle** : menu visualizzato per una cella in risposta a un movimento di pressione lungo.

Come per l'origine dati, `UICollectionViewController` è configurato per impostazione predefinita come delegato per l'oggetto. `UICollectionView`

 <a name="Cell_HighLighting" />

#### <a name="cell-highlighting"></a>Evidenziazione celle

Quando si preme una cella, la cella passa a uno stato evidenziato e non viene selezionata fino a quando l'utente non solleva il dito dalla cella. In questo modo è possibile modificare temporaneamente l'aspetto della cella prima che sia effettivamente selezionata. Al momento della selezione, `SelectedBackgroundView` viene visualizzata la cella. La figura seguente mostra lo stato evidenziato appena prima della selezione:

 [![](uicollectionview-images/04-cell-highlight.png "Questa figura mostra lo stato evidenziato subito prima della selezione")](uicollectionview-images/04-cell-highlight.png#lightbox)

Per implementare l'evidenziazione, `ItemHighlighted` è `ItemUnhighlighted` `UICollectionViewDelegate` possibile usare i metodi e di. Il codice seguente, ad esempio, applica uno sfondo giallo di `ContentView` quando la cella viene evidenziata e uno sfondo bianco quando viene annullata l'evidenziazione, come illustrato nell'immagine precedente:

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

Per impostazione predefinita, la selezione `UICollectionView`è abilitata in. Per disabilitare la selezione, `ShouldHighlightItem` eseguire l'override di e restituire false, come illustrato di seguito:

```csharp
public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath)
{
        return false;
}
```

Quando l'evidenziazione è disabilitata, viene disabilitato anche il processo di selezione di una cella. Inoltre, esiste anche un metodo `ShouldSelectItem` che controlla direttamente la selezione, `ShouldHighlightItem` anche se è implementato e restituisce false, `ShouldSelectItem` non viene chiamato.

 `ShouldSelectItem`consente di attivare o disattivare la selezione in base all'elemento, quando `ShouldHighlightItem` non è implementato. Consente inoltre di evidenziare senza selezione, se `ShouldHighlightItem` è implementato e restituisce true, mentre `ShouldSelectItem` restituisce false.

 <a name="Cell_Menus" />

#### <a name="cell-menus"></a>Menu delle celle

Ogni cella in un `UICollectionView` è in grado di visualizzare un menu che consente facoltativamente di eseguire operazioni Taglia, copia e incolla. Per creare un menu modifica in una cella:

1. Eseguire `ShouldShowMenu` l'override di e restituire true se l'elemento deve visualizzare un menu.
1. Eseguire `CanPerformAction` l'override di e restituire true per ogni azione che l'elemento è in grado di eseguire, che sarà qualsiasi taglia, copia o Incolla.
1. Eseguire `PerformAction` l'override di per eseguire la modifica copia dell'operazione Incolla.

Lo screenshot seguente mostra il menu quando si preme lungo una cella:

 [![](uicollectionview-images/04a-menu.png "Questa schermata mostra il menu quando si preme lungo una cella")](uicollectionview-images/04a-menu.png#lightbox)

 <a name="Layout" />

## <a name="layout"></a>Layout

`UICollectionView`supporta un sistema di layout che consente il posizionamento di tutti gli elementi, le celle, le visualizzazioni supplementari e le visualizzazioni di decorazione, da gestire `UICollectionView` indipendentemente dalla stessa.
Utilizzando il sistema di layout, un'applicazione può supportare layout come la griglia, come illustrato in questo articolo, e fornire layout personalizzati.

 <a name="Layout_Basics" />

### <a name="layout-basics"></a>Nozioni fondamentali sul layout

I layout in un `UICollectionView` oggetto sono definiti in una classe che eredita `UICollectionViewLayout`da. L'implementazione del layout è responsabile della creazione degli attributi di layout per ogni elemento `UICollectionView`in. Esistono due modi per creare un layout:

- Utilizzare l'oggetto incorporato `UICollectionViewFlowLayout` .
- Fornire un layout personalizzato ereditando da `UICollectionViewLayout` .

 <a name="Flow_Layout" />

### <a name="flow-layout"></a>Layout di flusso

La `UICollectionViewFlowLayout` classe fornisce un layout basato su riga che è adatto per organizzare il contenuto in una griglia di celle come abbiamo visto.

Per usare un layout di flusso:

- Creare un'istanza di `UICollectionViewFlowLayout` :

```csharp
var layout = new UICollectionViewFlowLayout ();
```

- Passare l'istanza al costruttore dell'oggetto `UICollectionView` :

```csharp
simpleCollectionViewController = new SimpleCollectionViewController (layout);
```

Questo è tutto ciò che è necessario per il layout del contenuto in una griglia. Inoltre, in caso di modifica dell'orientamento `UICollectionViewFlowLayout` , il gestisce la ridisposizione corretta del contenuto, come illustrato di seguito:

 [![](uicollectionview-images/05-layout-orientation.png "Esempio di modifiche all'orientamento")](uicollectionview-images/05-layout-orientation.png#lightbox)

 <a name="Section_Inset" />

#### <a name="section-inset"></a>Sezione Insert

Per fornire spazio attorno a, `UIContentView`i layout hanno una `SectionInset` proprietà di tipo `UIEdgeInsets`. Il codice seguente, ad esempio, fornisce un buffer di 50 pixel intorno a ogni sezione `UIContentView` di quando è disposto da `UICollectionViewFlowLayout`un oggetto:

```csharp
var layout = new UICollectionViewFlowLayout ();
layout.SectionInset = new UIEdgeInsets (50,50,50,50);
```

Ciò comporta la spaziatura intorno alla sezione, come illustrato di seguito:

 [![](uicollectionview-images/06-sectioninset.png "Spaziatura intorno alla sezione, come illustrato di seguito")](uicollectionview-images/06-sectioninset.png#lightbox)

 <a name="Subclassing_UICollectionViewFlowLayout" />

#### <a name="subclassing-uicollectionviewflowlayout"></a>Sottoclasse UICollectionViewFlowLayout

Nell'edizione a cui `UICollectionViewFlowLayout` si utilizza direttamente, può anche essere sottoclassata per personalizzare ulteriormente il layout del contenuto lungo una riga. Ad esempio, può essere utilizzato per creare un layout che non esegue il wrapping delle celle in una griglia, ma crea invece una singola riga con effetto di scorrimento orizzontale, come illustrato di seguito:

 [![](uicollectionview-images/07-line-layout.png "Una singola riga con effetto di scorrimento orizzontale")](uicollectionview-images/07-line-layout.png#lightbox)

Per implementare questa operazione tramite la sottoclasse `UICollectionViewFlowLayout` è necessario:

- Inizializzazione di tutte le proprietà di layout valide per il layout stesso o per tutti gli elementi nel layout del costruttore.
- Eseguendo l' `ShouldInvalidateLayoutForBoundsChange` override di, restituendo true in modo che quando `UICollectionView` i limiti delle modifiche cambiano, il layout delle celle verrà ricalcolato. Questa operazione viene utilizzata in questo caso per garantire che il codice per la trasformazione applicato alla cella effettuare venga applicato durante lo scorrimento.
- Eseguire l' `TargetContentOffset` override di per fare in modo che lo snap della cella `UICollectionView` effettuare al centro di come si interrompe lo scorrimento.
- Override `LayoutAttributesForElementsInRect` per restituire una matrice di `UICollectionViewLayoutAttributes` . Ogni `UICollectionViewLayoutAttribute` contiene informazioni su come eseguire il layout di un particolare elemento, incluse proprietà quali `Center` , `Size` `ZIndex` e `Transform3D` .

Nel codice seguente viene illustrata un'implementazione di questo tipo:

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

Oltre a usare `UICollectionViewFlowLayout`, i layout possono anche essere completamente personalizzati ereditando direttamente da `UICollectionViewLayout`.

I metodi principali per eseguire l'override sono:

- `PrepareLayout`: Usato per l'esecuzione di calcoli geometrici iniziali che verranno usati durante il processo di layout.
- `CollectionViewContentSize`: Restituisce le dimensioni dell'area utilizzata per visualizzare il contenuto.
- `LayoutAttributesForElementsInRect`: Come per l'esempio UICollectionViewFlowLayout illustrato in precedenza, questo metodo viene usato per fornire informazioni alla `UICollectionView` relativa modalità di layout di ogni elemento. Tuttavia, a differenza di `UICollectionViewFlowLayout` , quando si crea un layout personalizzato, è possibile posizionare gli elementi comunque.

Ad esempio, lo stesso contenuto può essere presentato in un layout circolare come illustrato di seguito:

 [![](uicollectionview-images/08-circle-layout.png "Layout personalizzato circolare come illustrato di seguito")](uicollectionview-images/08-circle-layout.png#lightbox)

Il fatto che i layout siano modificati dal layout simile a quello della griglia, a un layout di scorrimento orizzontale e successivamente a questo layout circolare richiede la modifica solo della classe layout fornita a `UICollectionView` . Niente in `UICollectionView`, il delegato o il codice dell'origine dati viene modificato.

## <a name="changes-in-ios-9"></a>Modifiche in iOS 9

In iOS 9, la visualizzazione della raccolta`UICollectionView`() supporta ora il trascinamento del riordinamento degli elementi predefiniti aggiungendo un nuovo riconoscimento di movimento predefinito e diversi nuovi metodi di supporto.

Usando questi nuovi metodi, è possibile implementare facilmente il trascinamento per riordinarlo nella visualizzazione di raccolta e scegliere di personalizzare l'aspetto degli elementi durante qualsiasi fase del processo di riordinamento.

[![](uicollectionview-images/intro01.png "Esempio del processo di riordinamento")](uicollectionview-images/intro01.png#lightbox)

Questo articolo illustra come implementare il trascinamento per riordinare in un'applicazione Novell. iOS, oltre ad alcune delle altre modifiche apportate da iOS 9 al controllo di visualizzazione raccolta:

- [Facile riordinamento degli elementi](#Easy-Reordering-of-Items)
  - [Esempio di riordino semplice](#Simple-Reordering-Example)
  - [Uso di un riconoscimento di movimenti personalizzato](#Using-a-Custom-Gesture-Recognizer)
  - [Layout e riordino personalizzati](#Custom-Layouts-and-Reording)
- [Modifiche della visualizzazione raccolta](#collection-view-changes)

<a name="Easy-Reordering-of-Items" />

## <a name="reordering-of-items"></a>Riordinamento degli elementi

Come indicato in precedenza, una delle modifiche più significative alla visualizzazione raccolta in iOS 9 è stata l'aggiunta di una semplice funzionalità di trascinamento della riordino.

In iOS 9 il modo più rapido per aggiungere il riordino a una visualizzazione di raccolta consiste nell'usare un `UICollectionViewController`oggetto.
Il controller di visualizzazione raccolta dispone ora `InstallsStandardGestureForInteractiveMovement` di una proprietà che aggiunge un *riconoscimento di movimento* standard che supporta il trascinamento per riordinare gli elementi nella raccolta.
Poiché il valore predefinito è `true`, è necessario implementare solo il metodo `MoveItem` della `UICollectionViewDataSource` classe per supportare il trascinamento della riordino. Ad esempio:

```csharp
public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
  // Reorder our list of items
  ...
}
```

<a name="Simple-Reordering-Example" />

### <a name="simple-reordering-example"></a>Esempio di riordino semplice

Come esempio rapido, avviare un nuovo progetto Novell. iOS e modificare il file **Main. Storyboard** . Trascinare un `UICollectionViewController` oggetto nell'area di progettazione:

[![](uicollectionview-images/quick01.png "Aggiunta di un UICollectionViewController")](uicollectionview-images/quick01.png#lightbox)

Selezionare la visualizzazione della raccolta. potrebbe essere più semplice eseguire questa operazione dalla struttura del documento. Nella scheda layout del riquadro delle proprietà impostare le dimensioni seguenti, come illustrato nella schermata seguente:

- **Dimensioni cella**: Larghezza – 60 | Altezza: 60
- **Dimensioni intestazione**: Width-0 | Altezza-0
- **Dimensioni piè**di pagina: Width-0 | Altezza-0
- **Spaziatura minima**: Per le celle – 8 | Per le righe-8
- **Inset di sezioni**: Primi – 16 | In basso – 16 | A sinistra – 16 | Right-16

[![](uicollectionview-images/quick04.png "Impostazione delle dimensioni della visualizzazione raccolta")](uicollectionview-images/quick04.png#lightbox)

Modificare quindi la cella predefinita:
- Modificare il colore di sfondo in blu
- Aggiungere un'etichetta che funga da titolo per la cella
- Impostare l'identificatore di riutilizzo sulla **cella**

[![](uicollectionview-images/quick02.png "Modificare la cella predefinita")](uicollectionview-images/quick02.png#lightbox)

Aggiungere i vincoli per mantenerla centrata all'interno della cella quando cambiano le dimensioni:

Nel **riquadro delle proprietà** per _CollectionViewCell_ e impostare la **classe** su `TextCollectionViewCell`:

[![](uicollectionview-images/quick05.png "Impostare la classe su TextCollectionViewCell")](uicollectionview-images/quick05.png#lightbox)

Impostare la **visualizzazione riutilizzabile** della `Cell`raccolta su:

[![](uicollectionview-images/quick06.png "Imposta la visualizzazione riutilizzabile della raccolta sulla cella")](uicollectionview-images/quick06.png#lightbox)

Infine, selezionare l'etichetta e denominarla `TextLabel`:

[![](uicollectionview-images/quick07.png "etichetta nome TextLabel")](uicollectionview-images/quick07.png#lightbox)

Modificare la `TextCollectionViewCell` classe e aggiungere le proprietà seguenti:

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

Qui la `Text` proprietà dell'etichetta viene esposta come titolo della cella, quindi può essere impostata dal codice.

Aggiungere una nuova C# classe al progetto e chiamarla `WaterfallCollectionSource`. Modificare il file e renderlo simile al seguente:

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

Questa classe sarà l'origine dati per la visualizzazione raccolta e fornirà le informazioni per ogni cella della raccolta.
Si noti che `MoveItem` il metodo viene implementato per consentire il trascinamento di elementi della raccolta riordinati.

Aggiungere un'altra C# nuova classe al progetto e chiamarla `WaterfallCollectionDelegate`. Modificare il file e renderlo simile al seguente:

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

Questa funzione fungerà da delegato per la visualizzazione di raccolta. I metodi sono stati sottoposti a override per evidenziare una cella quando l'utente interagisce con esso nella visualizzazione della raccolta.

Aggiungere un'ultima C# classe al progetto e chiamarla `WaterfallCollectionView`. Modificare il file e renderlo simile al seguente:

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

Si noti `DataSource` che `Delegate` e che abbiamo creato in precedenza vengono impostati quando la visualizzazione della raccolta viene costruita dal relativo storyboard (o file **. xib** ).

Modificare di nuovo il file **Main. Storyboard** e selezionare la visualizzazione raccolta e passare alle **Proprietà**. Impostare la **classe** sulla classe personalizzata `WaterfallCollectionView` definita in precedenza:

Salvare le modifiche apportate all'interfaccia utente ed eseguire l'app.
Se l'utente seleziona un elemento dall'elenco e lo trascina in una nuova posizione, gli altri elementi verranno animati automaticamente mentre si spostano fuori dalla modalità dell'elemento.
Quando l'utente rilascia l'elemento in una nuova posizione, si attaccherà a tale posizione. Ad esempio:

[![](uicollectionview-images/intro01.png "Esempio di trascinamento di un elemento in una nuova posizione")](uicollectionview-images/intro01.png#lightbox)

<a name="Using-a-Custom-Gesture-Recognizer" />

### <a name="using-a-custom-gesture-recognizer"></a>Uso di un riconoscimento di movimenti personalizzato

Nei casi in cui non è possibile `UICollectionViewController` usare un oggetto e deve `UIViewController`usare un normale o se si desidera assumere maggiore controllo sul movimento di trascinamento della selezione, è possibile creare un sistema di riconoscimento dei movimenti personalizzato e aggiungerlo alla visualizzazione della raccolta quando viene caricata la vista. Ad esempio:

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

In questo esempio vengono utilizzati diversi nuovi metodi aggiunti alla visualizzazione raccolta per implementare e controllare l'operazione di trascinamento:

- `BeginInteractiveMovementForItem`: Contrassegna l'inizio di un'operazione di spostamento.
- `UpdateInteractiveMovementTargetPosition`-Viene inviato quando il percorso dell'elemento viene aggiornato.
- `EndInteractiveMovement`: Contrassegna la fine di uno spostamento di elemento.
- `CancelInteractiveMovement`: Contrassegna l'utente che annulla l'operazione di spostamento.

Quando l'applicazione viene eseguita, l'operazione di trascinamento funzionerà esattamente come il riconoscimento predefinito del movimento di trascinamento che viene incluso nella visualizzazione della raccolta.

<a name="Custom-Layouts-and-Reording" />

### <a name="custom-layouts-and-reordering"></a>Layout e riordino personalizzati

In iOS 9 sono stati aggiunti diversi nuovi metodi per lavorare con il trascinamento per riordinare e i layout personalizzati in una visualizzazione di raccolta. Per esplorare questa funzionalità, aggiungere un layout personalizzato alla raccolta.

In primo luogo, aggiungere C# una nuova `WaterfallCollectionLayout` classe denominata al progetto. Modificarla e renderla simile alla seguente:

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

Questa classe può essere utilizzata per fornire una colonna personalizzata a due colonne, ovvero un layout di tipo a cascata, per la visualizzazione della raccolta.
Il codice usa la codifica chiave-valore (tramite `WillChangeValue` i `DidChangeValue` metodi e) per fornire data binding per le proprietà calcolate in questa classe.

Modificare `WaterfallCollectionSource` quindi e apportare le modifiche e le aggiunte seguenti:

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

Modificare quindi la `WaterfallCollectionView` classe e aggiungere la seguente proprietà Helper:

```csharp
public WaterfallCollectionSource Source {
  get { return (WaterfallCollectionSource)DataSource; }
}
```

In questo modo sarà più semplice ottenere l'origine dati e le altezze degli elementi dal layout personalizzato.

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

In questo modo viene creata un'istanza del layout personalizzato, viene impostato l'evento per fornire le dimensioni di ogni elemento e il nuovo layout viene collegato alla visualizzazione di raccolta.

Se si esegue di nuovo l'app Novell. iOS, la visualizzazione della raccolta apparirà come segue:

[![](uicollectionview-images/custom01.png "La visualizzazione della raccolta sarà ora simile alla seguente.")](uicollectionview-images/custom01.png#lightbox)

È ancora possibile trascinare gli elementi come prima, ma le dimensioni degli elementi verranno modificate in base alla nuova posizione quando vengono rilasciate.

## <a name="collection-view-changes"></a>Modifiche della visualizzazione raccolta

Nelle sezioni seguenti verranno esaminate in dettaglio le modifiche apportate a ogni classe nella visualizzazione della raccolta di iOS 9.

### <a name="uicollectionview"></a>UICollectionView

Alla `UICollectionView` classe per iOS 9 sono state apportate le modifiche o le aggiunte seguenti:

- `BeginInteractiveMovementForItem`: Contrassegna l'inizio di un'operazione di trascinamento.
- `CancelInteractiveMovement`: Informa la visualizzazione della raccolta che l'utente ha annullato un'operazione di trascinamento.
- `EndInteractiveMovement`: Informa la visualizzazione della raccolta che l'utente ha terminato un'operazione di trascinamento.
- `GetIndexPathsForVisibleSupplementaryElements`: Restituisce l' `indexPath` oggetto di un'intestazione o di un piè di pagina in una sezione di visualizzazione raccolta.
- `GetSupplementaryView`: Restituisce l'intestazione o il piè di pagina specificato.
- `GetVisibleSupplementaryViews`: Restituisce un elenco di tutte le intestazioni e i piè di pagina visibili.
- `UpdateInteractiveMovementTargetPosition`: Informa la visualizzazione della raccolta che l'utente ha spostato o sta spostando un elemento durante un'operazione di trascinamento.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController

Le modifiche o le aggiunte seguenti sono state apportate `UICollectionViewController` alla classe in iOS 9:

- `InstallsStandardGestureForInteractiveMovement`: Se `true` verrà usato il nuovo riconoscimento di movimento che supporta automaticamente il trascinamento della riordino.
- `CanMoveItem`: Informa la visualizzazione della raccolta se è possibile trascinare un determinato elemento riordinato.
- `GetTargetContentOffset`: Usato per ottenere l'offset di un elemento della visualizzazione di raccolta specificato.
- `GetTargetIndexPathForMove`: Ottiene l' `indexPath` oggetto di un elemento specificato per un'operazione di trascinamento.
- `MoveItem`: Sposta l'ordine di un determinato elemento nell'elenco.

### <a name="uicollectionviewdatasource"></a>UICollectionViewDataSource

Le modifiche o le aggiunte seguenti sono state apportate `UICollectionViewDataSource` alla classe in iOS 9:

- `CanMoveItem`: Informa la visualizzazione della raccolta se è possibile trascinare un determinato elemento riordinato.
- `MoveItem`: Sposta l'ordine di un determinato elemento nell'elenco.

### <a name="uicollectionviewdelegate"></a>UICollectionViewDelegate

Le modifiche o le aggiunte seguenti sono state apportate `UICollectionViewDelegate` alla classe in iOS 9:

- `GetTargetContentOffset`: Usato per ottenere l'offset di un elemento della visualizzazione di raccolta specificato.
- `GetTargetIndexPathForMove`: Ottiene l' `indexPath` oggetto di un elemento specificato per un'operazione di trascinamento.

### <a name="uicollectionviewflowlayout"></a>UICollectionViewFlowLayout

Le modifiche o le aggiunte seguenti sono state apportate `UICollectionViewFlowLayout` alla classe in iOS 9:

- `SectionFootersPinToVisibleBounds`: Infila i piè di pagina della sezione ai limiti visibili della vista raccolta.
- `SectionHeadersPinToVisibleBounds`: Consente di visualizzare le intestazioni della sezione ai limiti visibili della visualizzazione raccolta.

### <a name="uicollectionviewlayout"></a>UICollectionViewLayout

Le modifiche o le aggiunte seguenti sono state apportate `UICollectionViewLayout` alla classe in iOS 9:

- `GetInvalidationContextForEndingInteractiveMovementOfItems`: Restituisce il contesto di invalidamento al termine di un'operazione di trascinamento quando l'utente completa il trascinamento o lo Annulla.
- `GetInvalidationContextForInteractivelyMovingItems`: Restituisce il contesto di invalidamento all'inizio di un'operazione di trascinamento.
- `GetLayoutAttributesForInteractivelyMovingItem`: Ottiene gli attributi di layout per un determinato elemento durante il trascinamento di un elemento.
- `GetTargetIndexPathForInteractivelyMovingItem`: Restituisce l' `indexPath` oggetto dell'elemento che si trova nel punto specificato durante il trascinamento di un elemento.

### <a name="uicollectionviewlayoutattributes"></a>UICollectionViewLayoutAttributes

Le modifiche o le aggiunte seguenti sono state apportate `UICollectionViewLayoutAttributes` alla classe in iOS 9:

- `CollisionBoundingPath`: Restituisce il percorso di conflitto di due elementi durante un'operazione di trascinamento.
- `CollisionBoundsType`: Restituisce il tipo di collisione (come `UIDynamicItemCollisionBoundsType`) che si è verificato durante un'operazione di trascinamento.

### <a name="uicollectionviewlayoutinvalidationcontext"></a>UICollectionViewLayoutInvalidationContext

Le modifiche o le aggiunte seguenti sono state apportate `UICollectionViewLayoutInvalidationContext` alla classe in iOS 9:

- `InteractiveMovementTarget`: Restituisce l'elemento di destinazione di un'operazione di trascinamento.
- `PreviousIndexPathsForInteractivelyMovingItems`: Restituisce l' `indexPaths` oggetto di altri elementi che coinvolgono un trascinamento per riordinare l'operazione.
- `TargetIndexPathsForInteractivelyMovingItems`: Restituisce l' `indexPaths` oggetto di elementi che verranno riordinati in seguito a un'operazione di trascinamento del riordino.

### <a name="uicollectionviewsource"></a>UICollectionViewSource

Le modifiche o le aggiunte seguenti sono state apportate `UICollectionViewSource` alla classe in iOS 9:

- `CanMoveItem`: Informa la visualizzazione della raccolta se è possibile trascinare un determinato elemento riordinato.
- `GetTargetContentOffset`: Restituisce gli offset degli elementi che verranno spostati tramite un'operazione di trascinamento del riordino.
- `GetTargetIndexPathForMove`: Restituisce l' `indexPath` oggetto di un elemento che verrà spostato durante un'operazione di trascinamento del riordino.
- `MoveItem`: Sposta l'ordine di un determinato elemento nell'elenco.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato le modifiche apportate alle visualizzazioni di raccolta in iOS 9 e come implementarle in Novell. iOS.
È stata analizzata l'implementazione di una semplice azione di trascinamento della riordinamento in una visualizzazione di raccolta. uso di un riconoscimento di movimenti personalizzato con trascinamento della ridisposizione; e il modo in cui il trascinamento riordino influisca su un layout di visualizzazione raccolta personalizzato.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Esempio di visualizzazione raccolta](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-collectionview)
- [SimpleCollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplecollectionview)
- [Eventi, protocolli e delegati](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Utilizzo di tabelle e celle](~/ios/user-interface/controls/tables/index.md)
