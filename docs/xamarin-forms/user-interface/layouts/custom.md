---
title: Creare un layout personalizzato inXamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2beb00e0587a0e47a29d6f5628a5d6623514eade
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137332"
---
# <a name="create-a-custom-layout-in-xamarinforms"></a>Creare un layout personalizzato inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)

_Novell. Forms definisce cinque classi di layout: StackLayout, AbsoluteLayout, sul relativelayout, Grid e FlexLayout e ognuna dispone gli elementi figlio in modo diverso. Tuttavia, a volte è necessario organizzare il contenuto della pagina usando un layout non fornito da Xamarin.Forms . In questo articolo viene illustrato come scrivere una classe layout personalizzata e viene illustrata una classe WrapLayout sensibile all'orientamento che dispone gli elementi figlio orizzontalmente nella pagina, quindi esegue il wrapping della visualizzazione dei figli successivi a righe aggiuntive._

In Xamarin.Forms tutte le classi di layout derivano dalla [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe e vincolano il tipo generico a [`View`](xref:Xamarin.Forms.View) e ai relativi tipi derivati. A sua volta, la `Layout<T>` classe deriva dalla [`Layout`](xref:Xamarin.Forms.Layout) classe, che fornisce il meccanismo per posizionare e dimensionare gli elementi figlio.

Ogni elemento visivo è responsabile della determinazione della dimensione preferita, nota come dimensione *richiesta* . [`Page`](xref:Xamarin.Forms.Page)[`Layout`](xref:Xamarin.Forms.Layout) [`Layout<View>`](xref:Xamarin.Forms.Layout`1) i tipi derivati, e sono responsabili della determinazione della posizione e della dimensione del figlio, o dei figli, rispetto a se stessi. Il layout prevede pertanto una relazione padre-figlio, in cui l'elemento padre determina le dimensioni dei relativi elementi figlio, ma tenterà di adattare le dimensioni richieste del figlio.

Xamarin.FormsPer creare un layout personalizzato è necessaria una conoscenza approfondita del layout e dei cicli di invalidamento. Questi cicli verranno ora discussi.

## <a name="layout"></a>Layout

Il layout inizia dalla parte superiore della struttura ad albero visuale con una pagina e procede attraverso tutti i rami della struttura ad albero visuale per includere ogni elemento visivo in una pagina. Gli elementi padre di altri elementi sono responsabili del dimensionamento e del posizionamento degli elementi figlio relativi a se stessi.

La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe definisce [ `Measure` ] (xrif: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) metodo che misura un elemento per le operazioni di layout e un [ `Layout` ] (xrif: Xamarin.Forms . VisualElement. layout ( Xamarin.Forms . Rectangle)) che specifica l'area rettangolare in cui verrà eseguito il rendering dell'elemento. Quando un'applicazione viene avviata e viene visualizzata la prima pagina, un *ciclo di layout* costituito da prima delle `Measure` chiamate e quindi `Layout` chiama, inizia nell' [`Page`](xref:Xamarin.Forms.Page) oggetto:

1. Durante il ciclo di layout ogni elemento padre è responsabile della chiamata al `Measure` Metodo sugli elementi figlio.
1. Dopo che gli elementi figlio sono stati misurati, ogni elemento padre è responsabile della chiamata al `Layout` Metodo sugli elementi figlio.

Questo ciclo garantisce che tutti gli elementi visivi della pagina ricevano chiamate `Measure` ai `Layout` metodi e. Il processo è illustrato nel diagramma seguente:

![](custom-images/layout-cycle.png "Xamarin.Forms Layout Cycle")

> [!NOTE]
> Si noti che i cicli di layout possono verificarsi anche su un subset della struttura ad albero visuale se un elemento viene modificato in modo da influire sul layout. Sono inclusi gli elementi aggiunti o rimossi da una raccolta, ad esempio in un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) , una modifica nella [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) proprietà di un elemento o una modifica delle dimensioni di un elemento.

Ogni Xamarin.Forms classe che dispone di una `Content` `Children` proprietà o ha un metodo sottoponibile a override [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) . Le classi di layout personalizzate che derivano da [`Layout<View>`](xref:Xamarin.Forms.Layout`1) devono eseguire l'override di questo metodo e verificare che [ `Measure` ] (xrif: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) e [ `Layout` ] (xrif: Xamarin.Forms . VisualElement. layout ( Xamarin.Forms . Rectangle)) i metodi vengono chiamati su tutti gli elementi figlio dell'elemento, per fornire il layout personalizzato desiderato.

Inoltre, ogni classe che deriva da [`Layout`](xref:Xamarin.Forms.Layout) o [`Layout<View>`](xref:Xamarin.Forms.Layout`1) deve eseguire l'override del [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) metodo, che è il punto in cui una classe layout determina le dimensioni necessarie per effettuare chiamate a [ `Measure` ] (xrif: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) per i relativi metodi figlio.

> [!NOTE]
> Gli elementi ne determinano la dimensione in base ai *vincoli*, che indicano la quantità di spazio disponibile per un elemento all'interno dell'elemento padre dell'elemento. Vincoli passati a [ `Measure` ] (xrif: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) e i [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) metodi possono variare da 0 a `Double.PositiveInfinity` . Un elemento è *vincolato*o *completamente vincolato*quando riceve una chiamata al relativo [ `Measure` ] (xrif: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) con argomenti non infiniti: l'elemento è vincolato a una dimensione specifica. Un elemento non è *vincolato*o *parzialmente*vincolato quando riceve una chiamata al `Measure` metodo con almeno un argomento uguale a `Double.PositiveInfinity` : il vincolo infinito può essere considerato come il ridimensionamento automatico.

## <a name="invalidation"></a>Invalidamento

L'invalidamento è il processo mediante il quale una modifica in un elemento di una pagina attiva un nuovo ciclo di layout. Gli elementi vengono considerati non validi se non hanno più la dimensione o la posizione corretta. Se, ad esempio, la [`FontSize`](xref:Xamarin.Forms.Button.FontSize) proprietà di un oggetto [`Button`](xref:Xamarin.Forms.Button) cambia, viene detto che non è `Button` valido perché non avrà più la dimensione corretta. Il ridimensionamento `Button` di può quindi avere un effetto ondulato delle modifiche nel layout tramite il resto di una pagina.

Gli elementi vengono invalidati richiamando il [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) metodo, in genere quando una proprietà dell'elemento cambia che può comportare una nuova dimensione dell'elemento. Questo metodo genera l' [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) evento, che l'elemento padre dell'elemento gestisce per attivare un nuovo ciclo di layout.

La [`Layout`](xref:Xamarin.Forms.Layout) classe imposta un gestore per l' [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) evento su ogni elemento figlio aggiunto alla relativa `Content` proprietà o `Children` raccolta e scollega il gestore quando l'elemento figlio viene rimosso. Pertanto, ogni elemento nella struttura ad albero visuale con elementi figlio viene avvisato quando uno dei relativi elementi figlio cambia dimensione. Il diagramma seguente illustra come una modifica delle dimensioni di un elemento nella struttura ad albero visuale può causare modifiche che ripplero l'albero:

![](custom-images/invalidation.png "Invalidation in the Visual Tree")

Tuttavia, la `Layout` classe tenta di limitare l'effetto di una modifica nelle dimensioni di un figlio nel layout di una pagina. Se il layout è vincolato alle dimensioni, la modifica delle dimensioni figlio non influisce su un valore superiore rispetto al layout padre nella struttura ad albero visuale. Tuttavia, in genere le modifiche apportate alle dimensioni di un layout influiscono sul modo in cui il layout dispone i relativi elementi figlio. Pertanto, qualsiasi modifica apportata alle dimensioni di un layout avvierà un ciclo di layout per il layout e il layout riceverà le chiamate ai relativi [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) metodi e.

La [`Layout`](xref:Xamarin.Forms.Layout) classe definisce anche un [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) metodo con uno scopo simile al [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) metodo. Il `InvalidateLayout` metodo deve essere richiamato ogni volta che viene apportata una modifica che influiscono sul modo in cui le posizioni di layout e i relativi elementi figlio Ad esempio, la `Layout` classe richiama il `InvalidateLayout` Metodo ogni volta che un elemento figlio viene aggiunto o rimosso da un layout.

[`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout)È possibile eseguire l'override di per implementare una cache per ridurre al minimo le chiamate ripetitive di [ `Measure` ] (xrif: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) dei metodi degli elementi figlio del layout. L'override del `InvalidateLayout` metodo fornirà una notifica quando gli elementi figlio vengono aggiunti o rimossi dal layout. Analogamente, [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) è possibile eseguire l'override del metodo per fornire una notifica in caso di modifica delle dimensioni di uno degli elementi figlio del layout. Per entrambi gli override del metodo, un layout personalizzato deve rispondere cancellando la cache. Per ulteriori informazioni, vedere [calcolo e memorizzazione nella cache dei dati](#caching).

## <a name="create-a-custom-layout"></a>Creazione di un layout personalizzato

Il processo per la creazione di un layout personalizzato è il seguente:

1. Creare una classe che derivi dalla classe `Layout<View>`. Per ulteriori informazioni, vedere [creazione di un WrapLayout](#creating).
1. [*facoltativo*] Aggiungere proprietà, supportate da proprietà associabili, per tutti i parametri che devono essere impostati nella classe layout. Per ulteriori informazioni, vedere [aggiunta di proprietà supportate da proprietà associabili](#adding_properties).
1. Eseguire l'override del [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) metodo per richiamare [ `Measure` ] (xrif: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) su tutti gli elementi figlio del layout e restituiscono una dimensione richiesta per il layout. Per ulteriori informazioni, vedere [override del metodo onmeasure](#onmeasure).
1. Eseguire l'override del [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) metodo per richiamare [ `Layout` ] (xrif: Xamarin.Forms . VisualElement. layout ( Xamarin.Forms . Rectangle)) su tutti gli elementi figlio del layout. Impossibile richiamare [ `Layout` ] (xrif: Xamarin.Forms . VisualElement. layout ( Xamarin.Forms . Rectangle)) il metodo su ogni elemento figlio in un layout non riceverà mai una dimensione o una posizione corretta e pertanto l'elemento figlio non diventerà visibile nella pagina. Per ulteriori informazioni, vedere [override del metodo LayoutChildren](#layoutchildren).

    > [!NOTE]
    > Quando si enumerano gli elementi figlio nelle [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) sostituzioni e, ignorare qualsiasi elemento figlio la cui [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) proprietà è impostata su `false` . In questo modo, il layout personalizzato non lascerà spazio per gli elementi figlio invisibili.

1. [*facoltativo*] Eseguire l'override del [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) metodo per ricevere una notifica quando gli elementi figlio vengono aggiunti o rimossi dal layout. Per ulteriori informazioni, vedere [override del metodo InvalidateLayout](#invalidatelayout).
1. [*facoltativo*] Eseguire l'override del [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) metodo per ricevere una notifica quando una delle dimensioni degli elementi figlio del layout viene modificata. Per ulteriori informazioni, vedere [override del metodo OnChildMeasureInvalidated](#onchildmeasureinvalidated).

> [!NOTE]
> Si noti che la [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) sostituzione non verrà richiamata se le dimensioni del layout sono regolate dall'elemento padre anziché dai relativi elementi figlio. Tuttavia, l'override verrà richiamato se uno o entrambi i vincoli sono infiniti o se la classe layout ha [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) valori di proprietà o non predefiniti [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) . Per questo motivo, l' [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) override non può basarsi sulle dimensioni figlio ottenute durante la [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) chiamata al metodo. È invece `LayoutChildren` necessario richiamare [ `Measure` ] (xrif: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) sugli elementi figlio del layout, prima di richiamare [ `Layout` ] (xrif: Xamarin.Forms . VisualElement. layout ( Xamarin.Forms . Rectangle)). In alternativa, le dimensioni degli elementi figlio ottenuti nell' `OnMeasure` override possono essere memorizzate nella cache per evitare `Measure` chiamate successive nell' `LayoutChildren` override, ma la classe layout deve essere in grado di stabilire quando è necessario ottenere di nuovo le dimensioni. Per ulteriori informazioni, vedere [calcolo e memorizzazione nella cache dei dati di layout](#caching).

La classe layout può quindi essere utilizzata aggiungendola a un oggetto [`Page`](xref:Xamarin.Forms.Page) e aggiungendo elementi figlio al layout. Per ulteriori informazioni, vedere [utilizzo di WrapLayout](#consuming).

<a name="creating" />

### <a name="create-a-wraplayout"></a>Creare un WrapLayout

Nell'applicazione di esempio viene illustrata una classe sensibile all'orientamento `WrapLayout` che dispone i relativi elementi figlio orizzontalmente nella pagina, quindi esegue il wrapping della visualizzazione dei figli successivi a righe aggiuntive.

La `WrapLayout` classe alloca la stessa quantità di spazio per ogni elemento figlio, noto come *dimensione della cella*, in base alle dimensioni massime degli elementi figlio. Gli elementi figlio inferiori alle dimensioni della cella possono essere posizionati all'interno della cella in base ai relativi [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) valori di proprietà e.

La `WrapLayout` definizione della classe è illustrata nell'esempio di codice seguente:

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

<a name="caching" />

#### <a name="calculate-and-cache-layout-data"></a>Calcolare e memorizzare nella cache i dati di layout

La `LayoutData` struttura archivia i dati relativi a una raccolta di elementi figlio in una serie di proprietà:

- `VisibleChildCount`: numero di elementi figlio visibili nel layout.
- `CellSize`: dimensione massima di tutti gli elementi figlio, adattata alle dimensioni del layout.
- `Rows`: numero di righe.
- `Columns`: numero di colonne.

Il `layoutDataCache` campo viene usato per archiviare più `LayoutData` valori. All'avvio dell'applicazione, due `LayoutData` oggetti verranno memorizzati nella cache del `layoutDataCache` dizionario per l'orientamento corrente, uno per gli argomenti del vincolo per la `OnMeasure` sostituzione e uno per gli `width` `height` argomenti e per l' `LayoutChildren` override. Quando si ruota il dispositivo in orientamento orizzontale, verranno `OnMeasure` richiamati anche la sostituzione e la sostituzione, che comporteranno la `LayoutChildren` memorizzazione nella cache di altri due `LayoutData` oggetti. Tuttavia, quando si ripristina l'orientamento verticale del dispositivo, non sono necessari ulteriori calcoli perché il `layoutDataCache` dispone già dei dati necessari.

Nell'esempio di codice riportato di seguito viene illustrato il `GetLayoutData` metodo, che calcola le proprietà dell'oggetto `LayoutData` strutturato in base a una determinata dimensione:

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

- Determina se un valore calcolato `LayoutData` è già presente nella cache e lo restituisce se disponibile.
- In caso contrario, enumera tutti gli elementi figlio, richiamando il `Measure` metodo su ogni elemento figlio con una larghezza e un'altezza illimitate e determina la dimensione massima del figlio.
- A condizione che sia presente almeno un elemento figlio visibile, calcola il numero di righe e colonne necessarie, quindi calcola le dimensioni della cella per gli elementi figlio in base alle dimensioni dell'oggetto `WrapLayout` . Si noti che le dimensioni della cella sono in genere leggermente più larghe delle dimensioni massime figlio, ma che potrebbero anche essere più piccole se `WrapLayout` non sono sufficientemente larghe per l'elemento figlio più ampio o sufficientemente alta per il figlio più alto.
- Archivia il nuovo `LayoutData` valore nella cache.

<a name="adding_properties" />

#### <a name="add-properties-backed-by-bindable-properties"></a>Aggiungere proprietà supportate da proprietà associabili

La `WrapLayout` classe definisce `ColumnSpacing` le `RowSpacing` proprietà e, i cui valori vengono utilizzati per separare le righe e le colonne nel layout e supportate da proprietà associabili. Le proprietà associabili sono illustrate nell'esempio di codice seguente:

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

Il gestore modificato da proprietà di ogni proprietà associabile richiama l' `InvalidateLayout` override del metodo per attivare un nuovo passaggio di layout in `WrapLayout` . Per ulteriori informazioni, vedere [override del metodo InvalidateLayout](#invalidatelayout) ed [override del metodo OnChildMeasureInvalidated](#onchildmeasureinvalidated).

<a name="onmeasure" />

#### <a name="override-the-onmeasure-method"></a>Eseguire l'override del metodo onmeasure

L' `OnMeasure` override è illustrato nell'esempio di codice seguente:

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

L'override richiama il `GetLayoutData` metodo e costruisce un `SizeRequest` oggetto dai dati restituiti, prendendo in considerazione anche i `RowSpacing` valori delle proprietà e `ColumnSpacing` . Per ulteriori informazioni sul `GetLayoutData` metodo, vedere [calcolo e memorizzazione nella cache dei dati](#caching).

> [!IMPORTANT]
> [ `Measure` ] (Xrif: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) e i [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) metodi non devono mai richiedere una dimensione infinita restituendo un [`SizeRequest`](xref:Xamarin.Forms.SizeRequest) valore con una proprietà impostata su `Double.PositiveInfinity` . Tuttavia, almeno uno degli argomenti del vincolo a `OnMeasure` può essere `Double.PositiveInfinity` .

<a name="layoutchildren" />

#### <a name="override-the-layoutchildren-method"></a>Eseguire l'override del metodo LayoutChildren

L' `LayoutChildren` override è illustrato nell'esempio di codice seguente:

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

L'override inizia con una chiamata al `GetLayoutData` metodo, quindi enumera tutti gli elementi figlio per ridimensionarli e posizionarli all'interno della cella di ogni figlio. Questa operazione viene eseguita richiamando [ `LayoutChildIntoBoundingRegion` ] (xrif: Xamarin.Forms . Layout. LayoutChildIntoBoundingRegion ( Xamarin.Forms . Oggetti visivi, Xamarin.Forms . ), Utilizzato per posizionare un elemento figlio all'interno di un rettangolo in base ai relativi [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) valori di [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà e. Equivale a effettuare una chiamata al [] del figlio `Layout` (xrif: Xamarin.Forms . VisualElement. layout ( Xamarin.Forms . Rectangle)).

> [!NOTE]
> Si noti che il rettangolo passato al `LayoutChildIntoBoundingRegion` metodo include l'intera area in cui può risiedere l'elemento figlio.

Per ulteriori informazioni sul `GetLayoutData` metodo, vedere [calcolo e memorizzazione nella cache dei dati](#caching).

<a name="invalidatelayout" />

#### <a name="overridethe-invalidatelayout-method"></a>Metodo Overridethe InvalidateLayout

L' [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) override viene richiamato quando gli elementi figlio vengono aggiunti o rimossi dal layout o quando una delle `WrapLayout` proprietà modifica il valore, come illustrato nell'esempio di codice seguente:

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

L'override invalida il layout e ignora tutte le informazioni di layout memorizzate nella cache.

> [!NOTE]
> Per arrestare la [`Layout`](xref:Xamarin.Forms.Layout) classe che richiama il [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) Metodo ogni volta che un elemento figlio viene aggiunto o rimosso da un layout, eseguire l'override di [ `ShouldInvalidateOnChildAdded` ] (xrif: Xamarin.Forms . Layout. ShouldInvalidateOnChildAdded ( Xamarin.Forms . View)) e [ `ShouldInvalidateOnChildRemoved` ] (xrif: Xamarin.Forms . Layout. ShouldInvalidateOnChildRemoved ( Xamarin.Forms . ), E restituiscono `false` . La classe layout può quindi implementare un processo personalizzato quando vengono aggiunti o rimossi elementi figlio.

<a name="onchildmeasureinvalidated" />

#### <a name="override-the-onchildmeasureinvalidated-method"></a>Eseguire l'override del metodo OnChildMeasureInvalidated

L' [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) override viene richiamato quando uno degli elementi figlio del layout cambia e viene visualizzato nell'esempio di codice seguente:

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

L'override invalida il layout figlio e ignora tutte le informazioni di layout memorizzate nella cache.

<a name="consuming" />

### <a name="consume-the-wraplayout"></a>Utilizzare WrapLayout

La `WrapLayout` classe può essere utilizzata inserendola in un [`Page`](xref:Xamarin.Forms.Page) tipo derivato, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

Il codice C# equivalente è illustrato di seguito:

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

Gli elementi figlio possono quindi essere aggiunti a `WrapLayout` come richiesto. Nell'esempio di codice seguente vengono mostrati [`Image`](xref:Xamarin.Forms.Image) gli elementi aggiunti a `WrapLayout` :

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

Quando viene visualizzata la pagina contenente l'oggetto `WrapLayout` , l'applicazione di esempio accede in modo asincrono a un file JSON remoto contenente un elenco di foto, crea un [`Image`](xref:Xamarin.Forms.Image) elemento per ogni foto e lo aggiunge a `WrapLayout` . Il risultato è l'aspetto illustrato negli screenshot seguenti:

![](custom-images/portait-screenshots.png "Sample Application Portrait Screenshots")

Gli screenshot seguenti mostrano il `WrapLayout` dopo che è stato ruotato nell'orientamento orizzontale:

![](custom-images/landscape-ios.png "Sample iOS Application Landscape Screenshot")
![](custom-images/landscape-android.png "Sample Android Application Landscape Screenshot")
![](custom-images/landscape-uwp.png "Sample UWP Application Landscape Screenshot")

Il numero di colonne in ogni riga dipende dalla dimensione della foto, dalla larghezza dello schermo e dal numero di pixel per unità indipendente dal dispositivo. Gli [`Image`](xref:Xamarin.Forms.Image) elementi caricano in modo asincrono le foto e pertanto la classe riceverà `WrapLayout` chiamate frequenti al [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) metodo perché ogni `Image` elemento riceve una nuova dimensione in base alla foto caricata.

## <a name="related-links"></a>Collegamenti correlati

- [WrapLayout (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)
- [Layout personalizzati](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [Creazione di layout personalizzati in Xamarin.Forms (video)](https://www.youtube.com/watch?v=sxjOqNZFhKU)
- [Layout\<T>](xref:Xamarin.Forms.Layout`1)
- [Layout](xref:Xamarin.Forms.Layout)
- [Oggetti visivi](xref:Xamarin.Forms.VisualElement)
