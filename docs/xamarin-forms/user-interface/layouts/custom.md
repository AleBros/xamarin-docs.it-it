---
title: Creare un layout personalizzato in Novell. Forms
description: Questo articolo illustra come scrivere una classe di layout personalizzato e viene illustrata una classe WrapLayout orientamento minuscole che dispone i relativi elementi figlio orizzontalmente orizzontalmente nella pagina e quindi esegue il wrapping la visualizzazione degli elementi figlio successivi per le righe aggiuntive.
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
ms.openlocfilehash: 0828d780ed075a6e3b18ba5020f5908fb8c06189
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292579"
---
# <a name="create-a-custom-layout-in-xamarinforms"></a>Creare un layout personalizzato in Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)

_Xamarin. Forms definisce quattro classi di layout: StackLayout, AbsoluteLayout, RelativeLayout e griglia, e ognuno dispone i relativi elementi figlio in modo diverso. Tuttavia, talvolta è necessario organizzare i contenuti di pagina utilizzando un layout non fornito da xamarin. Forms. Questo articolo illustra come scrivere una classe di layout personalizzato e viene illustrata una classe WrapLayout orientamento minuscole che dispone i relativi elementi figlio orizzontalmente orizzontalmente nella pagina e quindi esegue il wrapping la visualizzazione degli elementi figlio successivi per le righe aggiuntive._

In xamarin. Forms, derivano da tutte le classi di layout la [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) classe e il tipo generico per vincolare [ `View` ](xref:Xamarin.Forms.View) e i tipi derivati. A sua volta, il `Layout<T>` deriva dalla classe la [ `Layout` ](xref:Xamarin.Forms.Layout) classe, che fornisce un meccanismo per il posizionamento e ridimensionamento figlio elementi.

Ogni elemento visivo è responsabile di determinare la propria dimensione preferita, che è noto come il *richiesto* dimensioni. [`Page`](xref:Xamarin.Forms.Page), [ `Layout` ](xref:Xamarin.Forms.Layout), e [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) tipi derivati sono tenuti a determinare la posizione e dimensione figlio o elementi figlio, rispetto a se stessi. Layout prevede quindi una relazione padre-figlio, in cui l'elemento padre determina quali devono essere le dimensioni dei relativi elementi figlio, ma verrà effettuato un tentativo di contenere le dimensioni richieste dell'elemento figlio.

Per creare un layout personalizzato è necessario conoscere a fondo i layout e invalidamento dei cicli di xamarin. Forms. Verranno ora illustrati questi cicli.

## <a name="layout"></a>Layout

Layout in cui inizia la parte superiore della struttura visiva con una pagina e procede attraverso tutti i rami dell'albero visuale per includere ogni elemento visivo in una pagina. Gli elementi che sono elementi padre agli altri elementi sono responsabili per il ridimensionamento e posizionamento relativi elementi figlio rispetto a se stessi.

Il [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe definisce un [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) metodo che misura un elemento per le operazioni di layout e un [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) metodo che specifica l'area rettangolare che dell'elemento verrà eseguito il rendering. Quando viene avviata un'applicazione e viene visualizzata la prima pagina, un *ciclo di layout* costituite da prima delle `Measure` chiamate e quindi `Layout` chiama, viene avviato sul [ `Page` ](xref:Xamarin.Forms.Page) oggetto:

1. Durante il ciclo di layout, ogni elemento padre è responsabile della chiamata di `Measure` metodo sui propri elementi figlio.
1. Dopo la misurazione di elementi figlio, ogni elemento padre è responsabile della chiamata di `Layout` metodo sui propri elementi figlio.

Questo ciclo assicura che ogni elemento visivo nella pagina riceve le chiamate per il `Measure` e `Layout` metodi. Il processo è illustrato nel diagramma seguente:

![](custom-images/layout-cycle.png "Ciclo di Layout di xamarin. Forms")

> [!NOTE]
> Si noti che i cicli di layout possono verificarsi anche in un subset della struttura visiva se si apportano modifiche per influire sul layout. Ciò include gli elementi aggiunto o rimosso da una raccolta, ad esempio in un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), una modifica nel [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible) proprietà di un elemento o di una modifica delle dimensioni di un elemento.

Ogni classe di xamarin. Forms che ha un `Content` o un `Children` proprietà ha un sottoponibile a override [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) (metodo). Classi di layout personalizzati che derivano da [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) deve eseguire l'override di questo metodo e assicurarsi che il [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) e [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) sono metodi chiamato su tutti i figli dell'elemento, per fornire il layout personalizzato.

Inoltre, ogni classe che deriva da [ `Layout` ](xref:Xamarin.Forms.Layout) o [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) devono eseguire l'override di [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) metodo, ovvero in una classe di layout Determina le dimensioni che deve essere effettuando chiamate per il [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) metodi dei relativi elementi figlio.

> [!NOTE]
> Elementi consentono di determinare le dimensioni di base *vincoli*, che indicano la quantità di spazio disponibile per un elemento padre dell'elemento. I vincoli passati per il [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) e [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) metodi possono variare da 0 a `Double.PositiveInfinity`. È un elemento *vincolata*, o *completamente vincolata*, quando riceve una chiamata al relativo [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) metodo con argomenti non infinito - l'elemento è limitato per una determinata dimensione. È un elemento *non vincolato*, o *parzialmente vincolata*, quando riceve una chiamata al relativo `Measure` metodo con almeno un argomento uguale a `Double.PositiveInfinity` : il vincolo infinito può essere considerato che indica di ridimensionamento automatico.

## <a name="invalidation"></a>Invalidamento

Invalidamento è il processo mediante il quale una modifica in un elemento in una pagina attiva un nuovo ciclo di layout. Gli elementi vengono considerati non validi quando le dimensioni corrette o la posizione non è più necessario. Ad esempio, se il [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize) proprietà di un [ `Button` ](xref:Xamarin.Forms.Button) modifiche, il `Button` è detto non è valido perché non sarà più possibile le dimensioni corrette. Ridimensionamento di `Button` può quindi disporre di un effetto domino delle modifiche nel layout con il resto della pagina.

Gli elementi invalidano stesse richiamando il [ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) metodo, in genere quando una proprietà dell'elemento cambia che potrebbe comportare una nuova dimensione dell'elemento. Questo metodo viene attivato il [ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) evento, che gestisce il padre dell'elemento per attivare un nuovo ciclo di layout.

Il [ `Layout` ](xref:Xamarin.Forms.Layout) classe imposta un gestore per il [ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) eventi su ogni elemento figlio aggiunto al relativo `Content` proprietà o `Children` raccolta e consente di scollegare il gestore quando la elemento figlio viene rimosso. Di conseguenza, ogni elemento nell'albero visuale che contiene elementi figlio è un avviso ogni volta che uno dei relativi elementi figlio cambia le dimensioni. Il diagramma seguente illustra come una modifica delle dimensioni di un elemento nella struttura visiva può causare modifiche che si propagano nell'albero di:

![](custom-images/invalidation.png "Invalidamento nell'albero visuale")

Tuttavia, il `Layout` classe tenta di limitare l'impatto di una modifica nelle dimensioni del figlio sul layout di una pagina. Se il layout è vincolato, una modifica di dimensioni figlio non riguarderà tutti i valori superiori del layout del padre nell'albero visuale. Tuttavia, in genere una modifica delle dimensioni di un layout influisce su come il layout dispone i relativi elementi figlio. Pertanto, qualsiasi modifica nelle dimensioni del layout verrà avviato un ciclo di layout per il layout e il layout riceverà chiamate al relativo [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) e [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) metodi.

Il [ `Layout` ](xref:Xamarin.Forms.Layout) classe definisce anche un [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) metodo con uno scopo simile al [ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) (metodo). Il `InvalidateLayout` metodo deve essere richiamato ogni volta che viene apportata una modifica che influisce sulla modalità con cui il layout posizione e dimensioni relativi elementi figlio. Ad esempio, il `Layout` classe richiama il `InvalidateLayout` metodo ogni volta che un elemento figlio viene aggiunto o rimosso da un layout.

Il [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) possono eseguire l'override per implementare una cache per ridurre al minimo le chiamate ricorrenti del [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) metodi degli elementi figlio del layout. Si esegue l'override di `InvalidateLayout` metodo fornirà una notifica di quando gli elementi figlio vengono aggiunti o rimossi dal layout. Analogamente, il [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) metodo può essere sostituito per fornire una notifica quando viene modificato uno degli elementi figlio del layout delle dimensioni. Per entrambi gli override dei metodi, cancellando la cache deve rispondere un layout personalizzato. Per altre informazioni, vedere [calcolo e la memorizzazione nella cache dati](#caching).

## <a name="create-a-custom-layout"></a>Creazione di un layout personalizzato

Il processo per la creazione di un layout personalizzato è come segue:

1. Creare una classe che derivi dalla classe `Layout<View>`. Per altre informazioni, vedere [creando un WrapLayout](#creating).
1. [*facoltativi*] aggiungere le proprietà, supportate da proprietà associabile, per qualsiasi parametro che deve essere impostata sulla classe di layout. Per altre informazioni, vedere [aggiunta di proprietà supportate da proprietà associabili](#adding_properties).
1. Eseguire l'override di [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) metodo da richiamare il [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) metodo su elementi figlio del layout e restituire una dimensione richiesta per il layout. Per altre informazioni, vedere [override del metodo OnMeasure](#onmeasure).
1. Eseguire l'override di [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) metodo da richiamare il [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) metodo nell'elemento figlio del layout. Impossibilità di richiamare il [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) metodo su ogni elemento figlio in un layout comporterà l'elemento figlio mai la ricezione di una dimensione appropriata o una posizione, e pertanto l'elemento figlio non diventerà visibile nella pagina. Per altre informazioni, vedere [override del metodo LayoutChildren](#layoutchildren).

    > [!NOTE]
    > Durante l'enumerazione di elementi figlio nel [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) e [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) sostituzioni, ignorare qualsiasi figlio il cui [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible) è impostata su `false`. Ciò garantisce che il layout personalizzato non lascia spazio per gli elementi figlio invisibili.

1. [*facoltativi*] eseguire l'Override di [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) metodo ricevere una notifica quando gli elementi figlio vengono aggiunti o rimossi dal layout. Per altre informazioni, vedere [override del metodo InvalidateLayout](#invalidatelayout).
1. [*facoltativi*] eseguire l'Override di [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) metodo per ricevere una notifica quando viene modificato uno degli elementi figlio del layout delle dimensioni. Per altre informazioni, vedere [override del metodo OnChildMeasureInvalidated](#onchildmeasureinvalidated).

> [!NOTE]
> Si noti che il [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) override non verranno chiamato se le dimensioni del layout sono disciplinata dal relativo elemento padre, anziché i relativi elementi figlio. Tuttavia, la sostituzione verrà richiamata se uno o entrambi i vincoli sono infiniti o se la classe di layout ha diverso [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) oppure [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) i valori delle proprietà. Per questo motivo, il [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) override non può basarsi su dimensioni figlio ottenute durante il [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) chiamata al metodo. Al contrario, `LayoutChildren` necessario richiamare il [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) metodo nell'elemento figlio del layout, prima di richiamare il [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) (metodo). In alternativa, le dimensioni degli elementi figlio ottenuto nel `OnMeasure` override può essere memorizzati nella cache per evitare futuri `Measure` chiamate nel `LayoutChildren` override, ma la classe del layout necessario conoscere quando necessitano ottenere anche in questo caso le dimensioni. Per altre informazioni, vedere [calcolo e la memorizzazione nella cache i dati di Layout](#caching).

La classe di layout può essere quindi usata, aggiungerlo a un [ `Page` ](xref:Xamarin.Forms.Page)e aggiungendo gli elementi figlio nel layout. Per altre informazioni, vedere [utilizzano il WrapLayout](#consuming).

<a name="creating" />

### <a name="create-a-wraplayout"></a>Creare un WrapLayout

L'applicazione di esempio viene illustrato un orientamento minuscole `WrapLayout` classe che dispone gli elementi figlio in senso orizzontale attraverso la pagina e quindi esegue il wrapping la visualizzazione degli elementi figlio successivi per le righe aggiuntive.

Il `WrapLayout` classe alloca la stessa quantità di spazio per ogni elemento figlio, noto come il *dimensioni della cella*, in base alla dimensione massima degli elementi figlio. Gli elementi figlio minori di quella cella può essere posizionata all'interno della cella in base loro [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) e [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) i valori delle proprietà.

Il `WrapLayout` definizione di classe è illustrata nell'esempio di codice seguente:

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

<a name="caching" />

#### <a name="calculate-and-cache-layout-data"></a>Calcolare e memorizzare nella cache i dati di layout

Il `LayoutData` struttura consente di archiviare dati relativi a una raccolta di elementi figlio in un numero di proprietà:

- `VisibleChildCount` -il numero di nodi figlio sono visibili nel layout.
- `CellSize` – la dimensione massima di tutti i nodi figlio, adattate alle dimensioni del layout.
- `Rows` -il numero di righe.
- `Columns` -il numero di colonne.

Il `layoutDataCache` campo viene usato per archiviare più `LayoutData` valori. All'avvio dell'applicazione, due `LayoutData` oggetti verranno memorizzati nel `layoutDataCache` dizionario per l'orientamento corrente: uno per gli argomenti di vincolo per il `OnMeasure` override e uno per il `width` e `height` argomenti per il `LayoutChildren` eseguire l'override. Per ruotare un oggetto dispositivo in orientamento verticale, il `OnMeasure` eseguire l'override e il `LayoutChildren` sostituzione verrà nuovamente richiamata, verrà visualizzato un risultato in un altro due `LayoutData` oggetti viene memorizzato nella cache nel dizionario. Tuttavia, la restituzione del dispositivo per l'orientamento verticale, nessuna ulteriore sono necessari calcoli perché i `layoutDataCache` già contiene i dati necessari.

Nell'esempio di codice riportato di seguito viene illustrato il `GetLayoutData` metodo, che calcola le proprietà del `LayoutData` strutturati basato su una determinata dimensione:

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

Il `GetLayoutData` metodo esegue le operazioni seguenti:

- Determina se un calcolato `LayoutData` valore si trova già nella cache e lo restituisce, se disponibile.
- In caso contrario, enumera tutti gli elementi figlio, richiamo il `Measure` metodo su ogni elemento figlio con una larghezza infinita e l'altezza e determina le dimensioni massime figlio.
- Purché non vi sia almeno un elemento figlio visibile, calcola il numero di righe e colonne necessari e quindi calcola le dimensioni di una cella per gli elementi figlio in base alle dimensioni del `WrapLayout`. Si noti che la dimensione della cella è in genere leggermente più ampia rispetto alla dimensione massima figlio, ma che potrebbe anche essere di dimensioni minore se la `WrapLayout` non è sufficientemente ampio per figlio più largo o talla abbastanza per l'elemento figlio più alto.
- Archivia il nuovo `LayoutData` valore nella cache.

<a name="adding_properties" />

#### <a name="add-properties-backed-by-bindable-properties"></a>Aggiungere proprietà supportate da proprietà associabili

Il `WrapLayout` classe definisce `ColumnSpacing` e `RowSpacing` proprietà, i cui valori vengono utilizzati per separare le righe e colonne nel layout, e che sono supportate da proprietà associabili. Le proprietà associabili sono illustrate nell'esempio di codice seguente:

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

Richiama il gestore di modifica delle proprietà di ciascuna proprietà associabili il `InvalidateLayout` override del metodo per attivare un nuovo layout passare il `WrapLayout`. Per altre informazioni, vedere [override del metodo InvalidateLayout](#invalidatelayout) e [l'override del metodo OnChildMeasureInvalidated](#onchildmeasureinvalidated).

<a name="onmeasure" />

#### <a name="override-the-onmeasure-method"></a>Eseguire l'override del metodo onmeasure

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

Richiama l'override di `GetLayoutData` metodo e costrutti di un `SizeRequest` oggetto dai dati restituiti, tenendo inoltre conto il `RowSpacing` e `ColumnSpacing` i valori delle proprietà. Per altre informazioni sul `GetLayoutData` metodo, vedere [calcolo e la memorizzazione nella cache dati](#caching).

> [!IMPORTANT]
> Il [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) e [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) metodi mai necessario richiedere una dimensione di tipo infinito, restituendo un [ `SizeRequest` ](xref:Xamarin.Forms.SizeRequest) valore con una proprietà impostata su `Double.PositiveInfinity`. Tuttavia, almeno uno degli argomenti di vincolo `OnMeasure` può essere `Double.PositiveInfinity`.

<a name="layoutchildren" />

#### <a name="override-the-layoutchildren-method"></a>Eseguire l'override del metodo LayoutChildren

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

Inizia la sostituzione con una chiamata al `GetLayoutData` (metodo) e quindi enumera tutti gli elementi figlio da ridimensionare e posizionare all'interno delle celle di ogni elemento figlio. Questo risultato viene ottenuto richiamando la [ `LayoutChildIntoBoundingRegion` ](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle)) metodo, che consente di posizionare un elemento figlio all'interno di un rettangolo in base relativa [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) e [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)i valori delle proprietà. Ciò equivale a eseguire una chiamata all'elemento figlio [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) (metodo).

> [!NOTE]
> Si noti che il rettangolo passato al `LayoutChildIntoBoundingRegion` metodo include l'intera area in cui può risiedere l'elemento figlio.

Per altre informazioni sul `GetLayoutData` metodo, vedere [calcolo e la memorizzazione nella cache dati](#caching).

<a name="invalidatelayout" />

#### <a name="overridethe-invalidatelayout-method"></a>Metodo Overridethe InvalidateLayout

Il [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) override viene richiamato quando gli elementi figlio vengono aggiunti o rimossi dal layout o quando uno del `WrapLayout` valore della proprietà modificato, come illustrato nell'esempio di codice seguente:

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

La sostituzione invalida il layout e rimuove tutte le informazioni di layout memorizzato nella cache.

> [!NOTE]
> Per arrestare il [ `Layout` ](xref:Xamarin.Forms.Layout) classe richiamando il [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) l'override del metodo ogni volta che un elemento figlio viene aggiunto o rimosso da un layout, il [ `ShouldInvalidateOnChildAdded` ](xref:Xamarin.Forms.Layout.ShouldInvalidateOnChildAdded(Xamarin.Forms.View)) e [ `ShouldInvalidateOnChildRemoved` ](xref:Xamarin.Forms.Layout.ShouldInvalidateOnChildRemoved(Xamarin.Forms.View)) metodi e restituire `false`. La classe layout possa quindi implementare un processo personalizzato quando gli elementi figlio vengono aggiunti o rimossi.

<a name="onchildmeasureinvalidated" />

#### <a name="override-the-onchildmeasureinvalidated-method"></a>Eseguire l'override del metodo OnChildMeasureInvalidated

Il [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) override viene richiamato quando uno degli elementi figlio del layout consente di modificare dimensioni e viene illustrato nell'esempio di codice seguente:

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

La sostituzione invalida il layout del figlio e rimuove tutte le informazioni di layout memorizzato nella cache.

<a name="consuming" />

### <a name="consume-the-wraplayout"></a>Utilizzare WrapLayout

Il `WrapLayout` classe può essere usata da posizionarlo su un [ `Page` ](xref:Xamarin.Forms.Page) derivato a tipo, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

Seguito è riportato il codice c# equivalente:

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

Gli elementi figlio possono quindi essere aggiunte alle `WrapLayout` in base alle necessità. Il codice seguente esempio illustra [ `Image` ](xref:Xamarin.Forms.Image) elementi viene pertanto aggiunta al `WrapLayout`:

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    var images = await GetImageListAsync();
    if (images != null)
    {
        foreach (var photo in images.Photos)
        {
            var image = new Image
            {
                Source = ImageSource.FromUri(new Uri(photo))
            };
            wrapLayout.Children.Add(image);
        }
    }
}

async Task<ImageList> GetImageListAsync()
{
    try
    {
        string requestUri = "https://raw.githubusercontent.com/xamarin/docs-archive/master/Images/stock/small/stock.json";
        string result = await _client.GetStringAsync(requestUri);
        return JsonConvert.DeserializeObject<ImageList>(result);
    }
    catch (Exception ex)
    {
        Debug.WriteLine($"\tERROR: {ex.Message}");
    }

    return null;
}
```

Quando la pagina contenente le `WrapLayout` viene visualizzata, l'applicazione di esempio accede a un file JSON remoto contenente un elenco di foto in modo asincrono, crea un' [ `Image` ](xref:Xamarin.Forms.Image) per ogni elemento di foto e lo aggiunge al `WrapLayout`. Ciò comporta l'aspetto illustrato negli screenshot seguenti:

![](custom-images/portait-screenshots.png "Schermate verticale dell'applicazione di esempio")

Gli screenshot seguenti viene illustrato il `WrapLayout` dopo che è stato ruotato l'orientamento orizzontale:

![](custom-images/landscape-ios.png "IOS Screenshot orizzontale dell'applicazione di esempio")
![](custom-images/landscape-android.png "Screenshot orizzontale dell'applicazione Android di esempio")
![](custom-images/landscape-uwp.png " Screenshot di orizzontale dell'applicazione di esempio UWP")

Il numero di colonne in ogni riga dipende la dimensione di foto, la larghezza dello schermo e il numero di pixel per ogni unità indipendenti dal dispositivo. Il [ `Image` ](xref:Xamarin.Forms.Image) elementi caricare in modo asincrono le foto e pertanto il `WrapLayout` classe riceverà chiamate frequenti al relativo [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) metodo come ognuno `Image` elemento riceve una nuova dimensione di base la foto caricata.

## <a name="related-links"></a>Collegamenti correlati

- [WrapLayout (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)
- [Layout personalizzati](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [Creazione di layout personalizzati in xamarin. Forms (video)](https://evolve.xamarin.com/session/56e20f83bad314273ca4d81c)
- [Layout\<T >](xref:Xamarin.Forms.Layout`1)
- [Layout](xref:Xamarin.Forms.Layout)
- [VisualElement](xref:Xamarin.Forms.VisualElement)
