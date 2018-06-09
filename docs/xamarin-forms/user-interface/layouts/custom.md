---
title: Creazione di un Layout personalizzato
description: In questo articolo viene illustrato come scrivere una classe di layout personalizzato e viene illustrata una classe di WrapLayout orientamento dipendente che dispone i relativi elementi figlio orizzontalmente attraverso la pagina e quindi esegue il wrapping la visualizzazione di elementi figlio successivi a righe aggiuntive.
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
ms.openlocfilehash: f225a80a1c386668b71d1cdd47409eb017f19033
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244935"
---
# <a name="creating-a-custom-layout"></a>Creazione di un Layout personalizzato

_Xamarin. Forms definisce quattro classi layout – StackLayout, AbsoluteLayout, RelativeLayout e griglia, e ognuno dispone i relativi elementi figlio in modo diverso. Tuttavia, talvolta è necessario organizzare il contenuto di pagina utilizzando un layout non fornito da xamarin. Forms. In questo articolo viene illustrato come scrivere una classe di layout personalizzato e viene illustrata una classe di WrapLayout orientamento dipendente che dispone i relativi elementi figlio orizzontalmente attraverso la pagina e quindi esegue il wrapping la visualizzazione di elementi figlio successivi a righe aggiuntive._

## <a name="overview"></a>Panoramica

In xamarin. Forms, tutte le classi di layout derivano il [ `Layout<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) classe e applicare un vincolo di tipo generico da cui [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) e i tipi derivati. A sua volta, il `Layout<T>` deriva dalla classe di [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) (classe), che fornisce un meccanismo per il posizionamento e il figlio di ridimensionamento elementi.

Ogni elemento visivo è responsabile della determinazione della propria dimensione preferita, che è noto come il *richiesto* dimensioni. [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/), e [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) tipi derivati sono responsabili per determinare la posizione e le dimensioni del codice, figlio o elementi figlio, se stessi. Layout di conseguenza, implica una relazione padre-figlio, in cui l'elemento padre determina quale deve essere la dimensione dei relativi elementi figlio, ma verrà eseguito un tentativo di supportare la dimensione dell'elemento figlio richiesta.

Per creare un layout personalizzato, è necessario una conoscenza approfondita dei cicli di layout e invalidamento di xamarin. Forms. Verranno ora illustrati questi cicli.

## <a name="layout"></a>Layout

Layout inizia nella parte superiore dell'albero visuale con una pagina e ne vengono tutti i rami dell'albero visuale per delimitare ciascun elemento visivo in una pagina. Gli elementi padre agli altri elementi sono responsabili di ridimensionamento e posizionamento autonomamente i relativi elementi figlio.

Il [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) classe definisce un [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) metodo che consente di misurare un elemento per le operazioni di layout e un [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) metodo che specifica l'area rettangolare che verrà visualizzato all'interno dell'elemento. Quando viene avviata un'applicazione e viene visualizzata la prima pagina, un *ciclo di layout* composta da primo di `Measure` chiamate e quindi `Layout` chiama, viene avviato sul [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) oggetto:

1. Durante il ciclo di layout, ogni elemento padre è responsabile della chiamata di `Measure` metodo sui propri elementi figlio.
1. Dopo la misurazione, gli elementi figlio di ogni elemento padre è responsabile della chiamata di `Layout` metodo sui propri elementi figlio.

Questo ciclo assicura che ogni elemento nella pagina riceve le chiamate al `Measure` e `Layout` metodi. Il processo è illustrato nel diagramma seguente:

![](custom-images/layout-cycle.png "Ciclo di Layout di xamarin. Forms")

> [!NOTE]
> Si noti che i cicli di layout possono verificarsi anche in un subset dell'albero visuale se vengono apportate modifiche per influire sul layout. Sono inclusi elementi aggiunti o rimossi da una raccolta, ad esempio in un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), una modifica di [ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/) proprietà di un elemento o una modifica delle dimensioni di un elemento.

Ogni classe di xamarin. Forms che ha un `Content` o `Children` proprietà ha un sottoponibile a override [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) metodo. Classi di layout personalizzato che derivano da [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) deve eseguire l'override di questo metodo e verificare che il [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) e [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) sono metodi chiamato su tutti i figli dell'elemento, per fornire il layout personalizzato desiderato.

Inoltre, ogni classe che deriva da [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) o [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) deve eseguire l'override di [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) metodo, ovvero in cui una classe di layout Determina le dimensioni che deve essere effettuando chiamate per il [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) metodi dei relativi elementi figlio.

> [!NOTE]
> Elementi determinano le dimensioni in base a *vincoli*, che indicano la quantità di spazio disponibile per un elemento padre dell'elemento. Vincoli passato per il [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) e [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) metodi possono variare da 0 a `Double.PositiveInfinity`. È un elemento *vincolata*, o *completamente vincolata*, quando riceve una chiamata al relativo [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) metodo con argomenti non infinito, l'elemento è vincolato per una determinata dimensione. È un elemento *non vincolato*, o *parzialmente vincolato*, quando riceve una chiamata al relativo `Measure` con almeno un argomento uguale a `Double.PositiveInfinity` : il vincolo infinito può essere considerato che indica di ridimensionamento automatico.

## <a name="invalidation"></a>Invalidamento

Invalidamento è il processo mediante il quale una modifica in un elemento in una pagina attiva un nuovo ciclo di layout. Quando le dimensioni corrette o la posizione non è più necessario, gli elementi vengono considerati non validi. Ad esempio, se il [ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontSize/) proprietà di un [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) modifiche, il `Button` viene considerato non valido perché non è più le dimensioni corrette. Ridimensionamento di `Button` può quindi disporre di una reazione delle modifiche nel layout attraverso il resto di una pagina.

Gli elementi stessi invalidano richiamando il [ `InvalidateMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.InvalidateMeasure()/) (metodo), in genere quando l'elemento viene modificata una proprietà che potrebbe causare una nuova dimensione dell'elemento. Questo metodo genera il [ `MeasureInvalidated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.MeasureInvalidated/) evento, che gestisce il padre dell'elemento per avviare un nuovo ciclo di layout.

Il [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) classe imposta un gestore per il [ `MeasureInvalidated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.MeasureInvalidated/) evento su ogni elemento figlio aggiunto al relativo `Content` proprietà o `Children` insieme e rimuove il gestore quando la elemento figlio viene rimosso. Ogni elemento nella struttura visiva che include elementi figlio, pertanto, viene generato un avviso ogni volta che uno dei relativi figli cambia di dimensione. Il diagramma seguente illustra come una modifica delle dimensioni di un elemento di struttura ad albero visuale può provocare modifiche che si propagano fino alla struttura:

![](custom-images/invalidation.png "Invalidamento la struttura ad albero visuale")

Tuttavia, la `Layout` classe tenta di limitare l'impatto di una modifica nelle dimensioni del figlio sul layout di pagina. Se il layout è vincolata, quindi una modifica di dimensioni figlio non ha superiore del layout padre nell'albero visuale. Tuttavia, in genere una modifica delle dimensioni di un layout influisce su come il layout dispone i relativi elementi figlio. Pertanto, qualsiasi modifica apportata in dimensioni di un layout verrà avviato un ciclo di layout per il layout e il layout riceverà le chiamate alla relativa [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) e [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) metodi.

Il [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) classe definisce inoltre un [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/) metodo che ha uno scopo simile per il [ `InvalidateMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.InvalidateMeasure()/) metodo. Il `InvalidateLayout` metodo deve essere richiamato ogni volta che viene apportata una modifica che influisce sulla modalità con cui il layout di posizione e dimensioni nei relativi elementi figlio. Ad esempio, il `Layout` classe richiama il `InvalidateLayout` metodo ogni volta che un elemento figlio viene aggiunto o rimosso da un layout.

Il [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/) può essere sottoposto a override per implementare una cache per ridurre al minimo le chiamate ricorrenti del [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) metodi di elementi figlio del layout. Si esegue l'override di `InvalidateLayout` metodo fornirà una notifica quando gli elementi figlio vengono aggiunti o rimossi dal layout. Analogamente, il [ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/) può eseguire l'override di metodo per fornire una notifica quando uno degli elementi figlio del layout Modifica dimensioni. Per entrambi gli override di metodo, un layout personalizzato deve rispondere cancellando la cache. Per ulteriori informazioni, vedere [calcolo e la memorizzazione nella cache dati](#caching).

## <a name="creating-a-custom-layout"></a>Creazione di un Layout personalizzato

Il processo per la creazione di un layout personalizzato è come segue:

1. Creare una classe che derivi dalla classe `Layout<View>`. Per ulteriori informazioni, vedere [la creazione di un WrapLayout](#creating).
1. [*facoltativo*] aggiunta di proprietà, supportata da proprietà associabile, per i parametri che devono essere impostati nella classe del layout. Per ulteriori informazioni, vedere [aggiunta di proprietà supportato da proprietà associabili](#adding_properties).
1. Eseguire l'override di [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) metodo da richiamare il [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) metodo figli del layout e restituiscono una dimensione richiesta per il layout. Per ulteriori informazioni, vedere [l'override del metodo OnMeasure](#onmeasure).
1. Eseguire l'override di [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) metodo da richiamare il [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) metodo negli elementi figlio del layout. Impossibilità di richiamare il [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) metodo su ogni elemento figlio in un layout determinerà l'elemento figlio non riceve un dimensioni corrette o una posizione e pertanto l'elemento figlio non sarà più visibile nella pagina. Per ulteriori informazioni, vedere [l'override del metodo LayoutChildren](#layoutchildren).

  > [!NOTE]
>  Durante l'enumerazione di elementi figlio di [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) e [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) sostituzioni, ignorare qualsiasi figlio il cui [ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/) è impostata su `false`. Ciò garantisce che il layout personalizzato non lascia spazio per gli elementi figlio visibili.

1. [*facoltativo*] eseguire l'Override di [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/) metodo ricevere una notifica quando gli elementi figlio vengono aggiunti o rimossi dal layout. Per ulteriori informazioni, vedere [l'override del metodo InvalidateLayout](#invalidatelayout).
1. [*facoltativo*] eseguire l'Override di [ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/) metodo ricevere una notifica quando uno degli elementi figlio del layout modifica dimensione. Per ulteriori informazioni, vedere [l'override del metodo OnChildMeasureInvalidated](#onchildmeasureinvalidated).

> [!NOTE]
> Si noti che il [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) override non verrà richiamato se le dimensioni del layout sono disciplinata dal relativo elemento padre, anziché nei relativi elementi figlio. Tuttavia, la sostituzione verrà richiamata se uno o entrambi i vincoli sono infiniti, o se la classe di layout ha non predefinito [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) o [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) i valori delle proprietà. Per questo motivo, il [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) override non può basarsi su dimensioni figlio ottenute durante il [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) chiamata al metodo. In alternativa, `LayoutChildren` deve richiamare il [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) metodo negli elementi figlio del layout, prima di richiamare il [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) metodo. In alternativa, le dimensioni degli elementi figlio ottenuto il `OnMeasure` override può essere memorizzati nella cache per evitare futuri `Measure` chiamate nel `LayoutChildren` override, ma la classe di layout dovranno sapere quando è necessario ottenere nuovamente le dimensioni. Per ulteriori informazioni, vedere [calcolo e la memorizzazione nella cache dei dati di Layout](#caching).

La classe di layout può essere quindi usata aggiungendolo a una [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)e aggiungendo gli elementi figlio nel layout. Per ulteriori informazioni, vedere [utilizzano il WrapLayout](#consuming).

<a name="creating" />

### <a name="creating-a-wraplayout"></a>Creazione di un WrapLayout

L'applicazione di esempio viene illustrato un orientamento sensibili `WrapLayout` classe che dispone i relativi elementi figlio orizzontalmente attraverso la pagina e quindi esegue il wrapping la visualizzazione di elementi figlio successivi a righe aggiuntive.

Il `WrapLayout` classe alloca la stessa quantità di spazio per ogni elemento figlio, noto come il *dimensioni della cella*, in base alla dimensione massima degli elementi figlio. Gli elementi figlio di minori dimensioni la cella possono essere posizionata all'interno della cella in base alle loro [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) i valori delle proprietà.

Il `WrapLayout` definizione di classe è illustrata nell'esempio di codice seguente:

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

<a name="caching" />

#### <a name="calculating-and-caching-layout-data"></a>Il calcolo e la memorizzazione nella cache di dati di Layout

Il `LayoutData` struttura archivia i dati relativi a una raccolta di elementi figlio in un numero di proprietà:

- `VisibleChildCount` : il numero di elementi figlio sono visibili nel layout.
- `CellSize` : la dimensione massima di tutti gli elementi figlio, con la dimensione del layout.
- `Rows` : il numero di righe.
- `Columns` : il numero di colonne.

Il `layoutDataCache` campo viene usato per archiviare più `LayoutData` valori. All'avvio dell'applicazione, due `LayoutData` oggetti verranno memorizzati nella cache nel `layoutDataCache` dizionario per l'orientamento corrente: uno per gli argomenti di vincolo per il `OnMeasure` override e uno per il `width` e `height` argomenti per il `LayoutChildren` eseguire l'override. Quando la rotazione del dispositivo in orientamento orizzontale, la `OnMeasure` eseguire l'override e `LayoutChildren` sostituzione verrà nuovamente richiamata, sarà pertanto due `LayoutData` viene memorizzato nella cache nel dizionario di oggetti. Tuttavia, quando viene restituito il dispositivo per l'orientamento verticale, nessun ulteriore sono necessari calcoli perché il `layoutDataCache` già i dati richiesti.

Nell'esempio di codice riportato di seguito viene illustrato il `GetLayoutData` metodo, che calcola le proprietà del `LayoutData` strutturato in base a una determinata dimensione:

```csharp
LayoutData GetLayoutData(double width, double height)
{
  Size size = new Size(width, height);

  // Check if cached information is available.
  if (layoutDataCache.ContainsKey(size))
  {
    return layoutDataCache[size];
  }

  int visibleChildCount = 0;
  Size maxChildSize = new Size();
  int rows = 0;
  int columns = 0;
  LayoutData layoutData = new LayoutData();

  // Enumerate through all the children.
  foreach (View child in Children)
  {
    // Skip invisible children.
    if (!child.IsVisible)
      continue;

    // Count the visible children.
    visibleChildCount++;

    // Get the child's requested size.
    SizeRequest childSizeRequest = child.Measure(Double.PositiveInfinity, Double.PositiveInfinity);

    // Accumulate the maximum child size.
    maxChildSize.Width = Math.Max(maxChildSize.Width, childSizeRequest.Request.Width);
    maxChildSize.Height = Math.Max(maxChildSize.Height, childSizeRequest.Request.Height);
  }

  if (visibleChildCount != 0)
  {
    // Calculate the number of rows and columns.
    if (Double.IsPositiveInfinity(width))
    {
      columns = visibleChildCount;
      rows = 1;
    }
    else
    {
      columns = (int)((width + ColumnSpacing) / (maxChildSize.Width + ColumnSpacing));
      columns = Math.Max(1, columns);
      rows = (visibleChildCount + columns - 1) / columns;
    }

    // Now maximize the cell size based on the layout size.
    Size cellSize = new Size();

    if (Double.IsPositiveInfinity(width))
      cellSize.Width = maxChildSize.Width;
    else
      cellSize.Width = (width - ColumnSpacing * (columns - 1)) / columns;

    if (Double.IsPositiveInfinity(height))
      cellSize.Height = maxChildSize.Height;
    else
      cellSize.Height = (height - RowSpacing * (rows - 1)) / rows;

    layoutData = new LayoutData(visibleChildCount, cellSize, rows, columns);
  }

  layoutDataCache.Add(size, layoutData);
  return layoutData;
}
```

Il `GetLayoutData` esegue le operazioni seguenti:

- Determina se un calcolato `LayoutData` valore si trova già nella cache e restituisce se è disponibile.
- In caso contrario, enumera tutti gli elementi figlio, richiamare il `Measure` metodo su ogni elemento figlio con un infinito larghezza e altezza e determina le dimensioni massime figlio.
- Purché non vi sia almeno un figlio visibili, calcola il numero di righe e colonne necessarie e quindi calcola una dimensione di cella per gli elementi figlio in base alle dimensioni del `WrapLayout`. Si noti che le dimensioni della cella sono in genere leggermente più ampia rispetto alla dimensione massima figlio, ma che potrebbe essere più piccolo se il `WrapLayout` non è sufficiente per la più ampia figlio o abbastanza alto per l'oggetto figlio più alto.
- Archivia il nuovo `LayoutData` valore nella cache.

<a name="adding_properties" />

#### <a name="adding-properties-backed-by-bindable-properties"></a>Aggiunta di proprietà supportata da proprietà associabili

Il `WrapLayout` classe definisce `ColumnSpacing` e `RowSpacing` proprietà, i cui valori vengono utilizzati per separare le righe e colonne nel layout, e che sono supportate da proprietà associabili. Le proprietà associabili vengono visualizzate nell'esempio di codice seguente:

```csharp
public static readonly BindableProperty ColumnSpacingProperty = BindableProperty.Create(
  "ColumnSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });

public static readonly BindableProperty RowSpacingProperty = BindableProperty.Create(
  "RowSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });
```

Richiama il gestore di modifica della proprietà di ciascuna proprietà associabile il `InvalidateLayout` override del metodo per attivare un nuovo layout passare il `WrapLayout`. Per ulteriori informazioni, vedere [l'override del metodo InvalidateLayout](#invalidatelayout) e [l'override del metodo OnChildMeasureInvalidated](#onchildmeasureinvalidated).

<a name="onmeasure" />

#### <a name="overriding-the-onmeasure-method"></a>Override del metodo OnMeasure

Il `OnMeasure` override è illustrato nell'esempio di codice seguente:

```csharp
protected override SizeRequest OnMeasure(double widthConstraint, double heightConstraint)
{
  LayoutData layoutData = GetLayoutData(widthConstraint, heightConstraint);
  if (layoutData.VisibleChildCount == 0)
  {
    return new SizeRequest();
  }

  Size totalSize = new Size(layoutData.CellSize.Width * layoutData.Columns + ColumnSpacing * (layoutData.Columns - 1),
                layoutData.CellSize.Height * layoutData.Rows + RowSpacing * (layoutData.Rows - 1));
  return new SizeRequest(totalSize);
}
```

Richiama l'override di `GetLayoutData` (metodo) e i costrutti un `SizeRequest` oggetto dai dati restituiti, tenendo in considerazione anche il `RowSpacing` e `ColumnSpacing` i valori delle proprietà. Per ulteriori informazioni sul `GetLayoutData` metodo, vedere [calcolo e la memorizzazione nella cache dati](#caching).

> [!IMPORTANT]
> Il [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/) e [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/) metodi non devono richiedere mai una dimensione infinita restituendo un [ `SizeRequest` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SizeRequest/) valore con una proprietà impostata su `Double.PositiveInfinity`. Tuttavia, almeno uno degli argomenti di vincolo `OnMeasure` può essere `Double.PositiveInfinity`.

<a name="layoutchildren" />

#### <a name="overriding-the-layoutchildren-method"></a>Override del metodo LayoutChildren

Il `LayoutChildren` override è illustrato nell'esempio di codice seguente:

```csharp
protected override void LayoutChildren(double x, double y, double width, double height)
{
  LayoutData layoutData = GetLayoutData(width, height);

  if (layoutData.VisibleChildCount == 0)
  {
    return;
  }

  double xChild = x;
  double yChild = y;
  int row = 0;
  int column = 0;

  foreach (View child in Children)
  {
    if (!child.IsVisible)
    {
      continue;
    }

    LayoutChildIntoBoundingRegion(child, new Rectangle(new Point(xChild, yChild), layoutData.CellSize));
    if (++column == layoutData.Columns)
    {
      column = 0;
      row++;
      xChild = x;
      yChild += RowSpacing + layoutData.CellSize.Height;
    }
    else
    {
      xChild += ColumnSpacing + layoutData.CellSize.Width;
    }
  }
}
```

Inizia la sostituzione con una chiamata al `GetLayoutData` (metodo) e quindi enumera tutti gli elementi figlio per ridimensionare e posizionare all'interno delle celle di ogni elemento figlio. Questo risultato viene ottenuto richiamando la [ `LayoutChildIntoBoundingRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/) metodo, che viene utilizzato per posizionare un elemento figlio all'interno di un rettangolo in base alle relative [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)i valori delle proprietà. Ciò equivale all'esecuzione di una chiamata al figlio [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) metodo.

> [!NOTE]
> Si noti che il rettangolo passato al `LayoutChildIntoBoundingRegion` metodo include l'intera area in cui può risiedere l'elemento figlio.

Per ulteriori informazioni sul `GetLayoutData` metodo, vedere [calcolo e la memorizzazione nella cache dati](#caching).

<a name="invalidatelayout" />

#### <a name="overriding-the-invalidatelayout-method"></a>Override del metodo InvalidateLayout

Il [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/) override viene richiamato quando gli elementi figlio vengono aggiunti o rimossi dal layout o quando uno del `WrapLayout` le modifiche delle proprietà di valore, come illustrato nell'esempio di codice seguente:

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

La sostituzione consente di invalidare il layout ed Elimina tutte le informazioni di layout memorizzato nella cache.

> [!NOTE]
> Per arrestare il [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) classe richiamando il [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/) l'override del metodo ogni volta che un elemento figlio viene aggiunto o rimosso da un layout, il [ `ShouldInvalidateOnChildAdded` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.ShouldInvalidateOnChildAdded/p/Xamarin.Forms.View/) e [ `ShouldInvalidateOnChildRemoved` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.ShouldInvalidateOnChildRemoved/p/Xamarin.Forms.View/) metodi, quindi restituiscono `false`. La classe di layout possa quindi implementare un processo personalizzato, quando gli elementi figlio vengono aggiunti o rimossi.

<a name="onchildmeasureinvalidated" />

#### <a name="overriding-the-onchildmeasureinvalidated-method"></a>Override del metodo OnChildMeasureInvalidated

Il [ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/) override viene richiamato quando uno degli elementi figlio del layout modifica dimensione e viene illustrato nell'esempio di codice seguente:

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

La sostituzione invalida il layout figlio ed Elimina tutte le informazioni di layout memorizzato nella cache.

<a name="consuming" />

### <a name="consuming-the-wraplayout"></a>Utilizzo di WrapLayout

Il `WrapLayout` classe può essere utilizzata da posizionarlo su un [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) derivato a tipo, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

Il codice c# equivalente è illustrato di seguito:

```csharp
public class ImageWrapLayoutPageCS : ContentPage
{
  WrapLayout wrapLayout;

  public ImageWrapLayoutPageCS()
  {
    wrapLayout = new WrapLayout();

    Content = new ScrollView
    {
      Margin = new Thickness(0, 20, 0, 20),
      Content = wrapLayout
    };
  }
  ...
}
```

Possono quindi essere aggiunti come elementi figlio di `WrapLayout` come richiesto. Nell'esempio di codice riportato di seguito viene [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) l'aggiunta di elementi di `WrapLayout`:

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  var images = await GetImageListAsync();
  foreach (var photo in images.Photos)
  {
    var image = new Image
    {
      Source = ImageSource.FromUri(new Uri(photo + string.Format("?width={0}&height={0}&mode=max", Device.RuntimePlatform == Device.UWP ? 120 : 240)))
    };
    wrapLayout.Children.Add(image);
  }
}

async Task<ImageList> GetImageListAsync()
{
  var requestUri = "https://docs.xamarin.com/demo/stock.json";
  using (var client = new HttpClient())
  {
    var result = await client.GetStringAsync(requestUri);
    return JsonConvert.DeserializeObject<ImageList>(result);
  }
}
```

Quando la pagina contenente il `WrapLayout` viene visualizzata, l'applicazione di esempio accede a un file JSON remoto contenente un elenco di foto in modo asincrono, crea un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) foto, per ogni elemento e lo aggiunge al `WrapLayout`. Ciò comporta l'aspetto illustrato nelle schermate seguenti:

![](custom-images/portait-screenshots.png "Schermate dell'esempio applicazione verticale")

Le schermate seguenti viene illustrato il `WrapLayout` dopo la rotazione per l'orientamento orizzontale:

![](custom-images/landscape-ios.png "IOS schermata orizzontale dell'applicazione di esempio")
![](custom-images/landscape-android.png "schermata orizzontale dell'applicazione di Android esempio") 
 ![ ] (custom-images/landscape-uwp.png " Schermata di orizzontale dell'applicazione di esempio UWP")

Il numero di colonne in ogni riga dipende la dimensione di foto, la larghezza dello schermo e il numero di pixel per ogni unità indipendenti dal dispositivo. Il [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elementi caricare in modo asincrono le foto e pertanto il `WrapLayout` classe riceverà chiamate frequenti al relativo [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) ogni metodo `Image` l'elemento riceve una nuova dimensione in base la foto caricata.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come scrivere una classe di layout personalizzato e descritto un orientamento sensibili `WrapLayout` classe che dispone i relativi elementi figlio orizzontalmente attraverso la pagina e quindi esegue il wrapping la visualizzazione di elementi figlio successivi a righe aggiuntive.


## <a name="related-links"></a>Collegamenti correlati

- [WrapLayout (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/CustomLayout/WrapLayout/)
- [Layout personalizzati](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [Creazione di layout personalizzati in xamarin. Forms (video)](https://evolve.xamarin.com/session/56e20f83bad314273ca4d81c)
- [Layout<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)
- [Layout](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)
- [VisualElement](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)
