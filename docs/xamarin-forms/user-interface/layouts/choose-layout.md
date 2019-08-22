---
title: Scegliere un layout di Novell. Forms
description: Le classi di layout Novell. Forms consentono di disporre e raggruppare i controlli dell'interfaccia utente nell'applicazione.
ms.prod: xamarin
ms.assetid: 05A39752-A174-447E-A30D-3CC9EF98CB96
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
ms.openlocfilehash: 161da8948f356fef997a411855598bc99d2f49b7
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69893998"
---
# <a name="choose-a-xamarinforms-layout"></a>Scegliere un layout di Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

Le classi di layout Novell. Forms consentono di disporre e raggruppare i controlli dell'interfaccia utente nell'applicazione. La scelta di una classe di layout richiede la conoscenza del modo in cui il layout posiziona gli elementi figlio e il modo in cui il layout ridimensiona gli elementi figlio. Inoltre, potrebbe essere necessario annidare i layout per creare il layout desiderato.

Nell'immagine seguente vengono illustrati i layout tipici che è possibile ottenere con le classi di layout Novell. Forms principali:

[ ![Classi di layout principali nelle classi di layout Novell. Forms](images/layouts.png "Novell. Forms") ] (images/layouts-large.png#lightbox "Classi di layout Novell. Forms")

## <a name="stacklayout"></a>StackLayout

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) oggetto organizza gli elementi in uno stack unidimensionale, orizzontalmente o verticalmente. La [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) proprietà specifica la direzione degli elementi e l'orientamento predefinito è [`Vertical`](xref:Xamarin.Forms.StackOrientation). `StackLayout`viene in genere usato per disporre una sottosezione dell'interfaccia utente in una pagina.

Nel codice XAML seguente viene illustrato come creare un [`StackLayout`](xref:Xamarin.Forms.StackLayout) oggetto verticale [`Label`](xref:Xamarin.Forms.Label) contenente tre oggetti:

```xaml
<StackLayout Margin="20,35,20,25">
    <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
    <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
    <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
</StackLayout>
```

In un [`StackLayout`](xref:Xamarin.Forms.StackLayout), se la dimensione di un elemento non è impostata in modo esplicito, si espande per riempire la larghezza disponibile oppure l'altezza [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) se la proprietà è [`Horizontal`](xref:Xamarin.Forms.StackOrientation)impostata su.

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) oggetto viene spesso usato come layout padre, che contiene altri layout figlio. Tuttavia, non `StackLayout` è consigliabile utilizzare un oggetto per riprodurre un [`Grid`](xref:Xamarin.Forms.Grid) layout utilizzando una combinazione di `StackLayout` oggetti. Il codice seguente illustra un esempio di questa procedura non valida:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Name:" />
            <Entry Placeholder="Enter your name" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Age:" />
            <Entry Placeholder="Enter your age" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Occupation:" />
            <Entry Placeholder="Enter your occupation" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Address:" />
            <Entry Placeholder="Enter your address" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

L'operazione risulta dispendiosa poiché vengono eseguiti calcoli di layout non necessari. Al contrario, è possibile ottenere il layout desiderato utilizzando un oggetto [`Grid`](xref:Xamarin.Forms.Grid).

> [!TIP]
> Quando si usa [`StackLayout`](xref:Xamarin.Forms.StackLayout)un oggetto, verificare che solo un elemento figlio sia [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)impostato su. Questa proprietà garantisce che l'elemento figlio specificato occupi lo spazio più ampio che `StackLayout` è in grado di assegnare ed è dispendioso eseguire questi calcoli più di una volta.

Per altre informazioni, vedere [Novell. Forms StackLayout](stack-layout.md).

## <a name="grid"></a>Grid

Un [`Grid`](xref:Xamarin.Forms.Grid) oggetto viene utilizzato per la visualizzazione di elementi in righe e colonne, che possono avere dimensioni proporzionali o assolute. Le righe e le colonne di una griglia vengono specificate [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) con [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) le proprietà e.

Per posizionare gli elementi in [`Grid`](xref:Xamarin.Forms.Grid) celle specifiche, utilizzare [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) le [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) proprietà associate e. Per fare in modo che gli elementi si estendano su [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) più [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) righe e colonne, utilizzare le proprietà associate e.

> [!NOTE]
> Un [`Grid`](xref:Xamarin.Forms.Grid) layout non deve essere confuso con le tabelle e non è destinato a presentare dati tabulari. A differenza delle tabelle HTML, `Grid` un è progettato per il layout del contenuto. Per la visualizzazione dei dati tabulari, è consigliabile usare [ListView](~/xamarin-forms/user-interface/listview/index.md), [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)o [TableView](~/xamarin-forms/user-interface/tableview.md).

Nel codice XAML seguente viene illustrato come creare [`Grid`](xref:Xamarin.Forms.Grid) un oggetto con due righe e due colonne:

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="50" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto" />
        <ColumnDefinition />
    </Grid.ColumnDefinitions>    
    <Label Text="Column 0, Row 0"
           Width="200" />
    <Label Grid.Column="1"
           Text="Column 1, Row 0" />
    <Label Grid.Row="1"
           Text="Column 0, Row 1" />
    <Label Grid.Column="1"
           Grid.Row="1"
           Text="Column 1, Row 1" />
</Grid>
```

In questo esempio, il dimensionamento funziona nel modo seguente:

- Ogni riga ha un'altezza esplicita di 50 unità indipendenti dal dispositivo.
- La larghezza della prima colonna è impostata su [`Auto`](xref:Xamarin.Forms.GridLength.Auto)ed è quindi il più ampio necessario per i relativi elementi figlio. In questo caso, si tratta di 200 unità indipendenti dal dispositivo per adattarsi alla larghezza della prima [`Label`](xref:Xamarin.Forms.Label).

Lo spazio può essere distribuito all'interno di una colonna o di una riga usando il ridimensionamento automatico, che consente alle dimensioni delle colonne e delle righe di adattarsi al contenuto. Questa operazione viene eseguita impostando l'altezza di [`RowDefinition`](xref:Xamarin.Forms.RowDefinition)un oggetto o la larghezza di [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition)un oggetto [`Auto`](xref:Xamarin.Forms.GridLength.Auto)su. Il dimensionamento proporzionale può essere utilizzato anche per distribuire lo spazio disponibile tra le righe e le colonne della griglia in base alle proporzioni ponderate. Questa operazione viene eseguita impostando l'altezza di `RowDefinition`un oggetto o la larghezza di `ColumnDefinition`un oggetto su un valore che utilizza `*` l'operatore.

> [!CAUTION]
> Provare a verificare che il minor numero di righe e colonne possibile sia impostato [`Auto`](xref:Xamarin.Forms.GridLength.Auto) su dimensioni. Ogni riga o colonna ridimensionata automaticamente causerà l'esecuzione di ulteriori calcoli di layout da parte del motore di layout. Usare invece righe e colonne con dimensioni fisse, se possibile. In alternativa, impostare righe e colonne in modo da occupare una quantità proporzionale di [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) spazio con il valore di enumerazione.

Per ulteriori informazioni, vedere [Novell. Forms Grid](grid.md).

## <a name="flexlayout"></a>FlexLayout

Un [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) oggetto è simile a [`StackLayout`](xref:Xamarin.Forms.StackLayout) un oggetto in quanto Visualizza gli elementi figlio orizzontalmente o verticalmente in uno stack. Tuttavia, un `FlexLayout` oggetto può anche eseguire il wrapping dei relativi elementi figlio se sono presenti troppi per adattarsi a una singola riga o colonna e consente inoltre un controllo più granulare delle dimensioni, dell'orientamento e dell'allineamento degli elementi figlio.

Nel codice XAML seguente viene illustrato come creare [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) un oggetto che visualizza le visualizzazioni in una singola colonna:

```xaml
<FlexLayout Direction="Column"
            AlignItems="Center"
            JustifyContent="SpaceEvenly">
    <Label Text="FlexLayout in Action" />
    <Button Text="Button" />
    <Label Text="Another Label" />
</FlexLayout>
```

In questo esempio il layout funziona nel modo seguente:

- La [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) proprietà è impostata su `Column`, che fa sì che gli elementi `FlexLayout` figlio dell'oggetto vengano disposti in una singola colonna di elementi.
- La [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) proprietà è impostata su `Center`, che fa sì che ogni elemento sia centrato orizzontalmente.
- La [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) proprietà è impostata su `SpaceEvenly`, che alloca tutto lo spazio verticale rimanente equamente tra tutti gli elementi e sopra il primo elemento e sotto l'ultimo elemento.

Per altre informazioni, vedere [Novell. Forms FlexLayout](flex-layout.md).

## <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) Viene utilizzato per posizionare e ridimensionare elementi relativi alle proprietà del layout o degli elementi di pari livello. Per impostazione predefinita, un elemento viene posizionato nell'angolo superiore sinistro del layout. Un `RelativeLayout` oggetto può essere usato per creare interfacce utente che si ridimensionano proporzionalmente tra le dimensioni dei dispositivi.

All'interno [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)di un, le posizioni e le dimensioni vengono specificate come vincoli. I vincoli [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor) hanno [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant) proprietà e, che possono essere usate per definire posizioni e dimensioni come multipli (o frazioni) di proprietà di altri oggetti, più una costante. Inoltre, le costanti possono essere negative.

> [!NOTE]
> Un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) oggetto supporta il posizionamento di elementi all'esterno dei propri limiti.

Nel codice XAML seguente viene illustrato come disporre gli elementi [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)in un oggetto:

```xaml
<RelativeLayout>
    <BoxView Color="Blue"
             HeightRequest="50"
             WidthRequest="50"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0}" />
    <BoxView Color="Red"
             HeightRequest="50"
             WidthRequest="50"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.85}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0}" />
    <BoxView x:Name="pole"
             Color="Gray"
             WidthRequest="15"
             RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.75}"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.45}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.25}" />
    <BoxView Color="Green"
             RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.10, Constant=10}"
             RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.2, Constant=20}"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=X, Constant=15}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=Y, Constant=0}" />
</RelativeLayout>
```

In questo esempio il layout funziona nel modo seguente:

- Al blu [`BoxView`](xref:Xamarin.Forms.BoxView) viene assegnata una dimensione esplicita delle unità 50x50 indipendenti dal dispositivo. Si trova nell'angolo superiore sinistro del layout, ovvero la posizione predefinita.
- Al rosso [`BoxView`](xref:Xamarin.Forms.BoxView) viene assegnata una dimensione esplicita delle unità 50x50 indipendenti dal dispositivo. Viene posizionata nell'angolo superiore destro del layout.
- Al grigio [`BoxView`](xref:Xamarin.Forms.BoxView) viene assegnata una larghezza esplicita di 15 unità indipendenti dal dispositivo e l'altezza è impostata su 75% dell'altezza del padre.
- Al verde [`BoxView`](xref:Xamarin.Forms.BoxView) non viene assegnata una dimensione esplicita. La relativa posizione viene impostata in relazione `BoxView` all' `pole`oggetto denominato.

> [!WARNING]
> Evitare di utilizzare `RelativeLayout` un quando possibile. Causa un notevole aumento del lavoro per la CPU.

Per altre informazioni, vedere [Novell. Forms sul relativelayout](relative-layout.md).

## <a name="absolutelayout"></a>AbsoluteLayout

Un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) oggetto viene usato per posizionare e ridimensionare gli elementi usando valori espliciti o i valori relativi alle dimensioni del layout. La posizione viene specificata dall'angolo superiore sinistro del figlio rispetto all'angolo superiore sinistro dell'oggetto `AbsoluteLayout`.

Un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) deve essere considerato come un layout per scopi specifici da usare solo quando è possibile imporre una dimensione agli elementi figlio o quando le dimensioni dell'elemento non influiscono sul posizionamento degli altri elementi figlio. Un uso standard di questo layout consiste nel creare una sovrapposizione, che copre la pagina con altri controlli, forse per impedire all'utente di interagire con i normali controlli della pagina.

> [!IMPORTANT]
> Il `HorizontalOptions` e `VerticalOptions` delle proprietà non hanno effetto sugli elementi figlio di un `AbsoluteLayout`.

All'interno [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)di un [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) oggetto, la proprietà associata viene utilizzata per specificare la posizione orizzontale, la posizione verticale, la larghezza e l'altezza di un elemento. Inoltre, la [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) proprietà associata specifica come verranno interpretati i limiti del layout.

Nel codice XAML seguente viene illustrato come disporre gli elementi [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)in un oggetto:

```xaml
<AbsoluteLayout Margin="40">
    <BoxView Color="Red"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
             Rotation="30" />
    <BoxView Color="Green"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
             Rotation="60" />
    <BoxView Color="Blue"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" />
</AbsoluteLayout>
```

In questo esempio il layout funziona nel modo seguente:

- A [`BoxView`](xref:Xamarin.Forms.BoxView) ciascuna viene assegnata una dimensione esplicita 100x100 e viene visualizzata nella stessa posizione, centrata orizzontalmente.
- Il rosso [`BoxView`](xref:Xamarin.Forms.BoxView) viene ruotato di 30 gradi e il verde `BoxView` viene ruotato di 60 gradi.
- In ogni [`BoxView`](xref:Xamarin.Forms.BoxView), la [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) proprietà `PositionProportional`associata è impostata su, a indicare che la posizione è proporzionale allo spazio rimanente dopo che è stata contabilizzata la larghezza e l'altezza.

> [!CAUTION]
> Evitare di utilizzare [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) la proprietà quando possibile, perché il motore di layout eseguirà calcoli di layout aggiuntivi.

Per altre informazioni, vedere [Novell. Forms AbsoluteLayout](absolute-layout.md).

## <a name="input-transparency"></a>Trasparenza input

Ogni elemento visivo ha una [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) proprietà usata per definire se l'elemento riceve input. Il valore predefinito è `false`, assicurando che l'elemento riceve l'input.

Quando questa proprietà viene impostata su una classe layout, il relativo valore viene trasferito agli elementi figlio. Se pertanto si imposta [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) la `true` proprietà su in una classe layout, tutti gli elementi all'interno del layout non riceveranno input.

## <a name="layout-performance"></a>Prestazioni del layout

Per ottenere le migliori prestazioni di layout possibili, seguire le linee guida in [ottimizzare le prestazioni del layout](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

Inoltre, le prestazioni di rendering della pagina possono essere migliorate anche usando la compressione del layout, che rimuove i layout specificati dalla struttura ad albero visuale. Per ulteriori informazioni, vedere [compressione del layout](layout-compression.md).

## <a name="related-links"></a>Collegamenti correlati

- [Layout (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Layout di Novell. Forms (video)](https://youtu.be/4HlLjTZQzjM)
- [Novell. Forms StackLayout](stack-layout.md)
- [Griglia Novell. Forms](grid.md)
- [Novell. Forms FlexLayout](flex-layout.md)
- [Novell. Forms AbsoluteLayout](absolute-layout.md)
- [Novell. Forms sul relativelayout](relative-layout.md)
- [Ottimizzare le prestazioni del layout](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)
- [Compressione del layout](layout-compression.md)
