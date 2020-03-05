---
title: Scegliere un layout di Novell. Forms
description: Le classi di layout Novell. Forms consentono di disporre e raggruppare i controlli dell'interfaccia utente nell'applicazione.
ms.prod: xamarin
ms.assetid: 05A39752-A174-447E-A30D-3CC9EF98CB96
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
ms.openlocfilehash: d029e679400b9523df8e03d509230849fa0c96c4
ms.sourcegitcommit: 9ae537efc106f56aeec562773004c6f708704ae9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78291791"
---
# <a name="choose-a-xamarinforms-layout"></a>Scegliere un layout di Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

Le classi di layout Novell. Forms consentono di disporre e raggruppare i controlli dell'interfaccia utente nell'applicazione. La scelta di una classe di layout richiede la conoscenza del modo in cui il layout posiziona gli elementi figlio e il modo in cui il layout ridimensiona gli elementi figlio. Inoltre, potrebbe essere necessario annidare i layout per creare il layout desiderato.

Nell'immagine seguente vengono illustrati i layout tipici che è possibile ottenere con le classi di layout Novell. Forms principali:

[![Classi di layout principali in Novell. Forms](images/layouts.png "Classi di layout Novell. Forms")](images/layouts-large.png#lightbox "Classi di layout Novell. Forms")

## <a name="stacklayout"></a>StackLayout

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) organizza gli elementi in uno stack unidimensionale, orizzontalmente o verticalmente. La proprietà [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) specifica la direzione degli elementi e l'orientamento predefinito è [`Vertical`](xref:Xamarin.Forms.StackOrientation). `StackLayout` viene in genere usato per disporre una sottosezione dell'interfaccia utente in una pagina.

Nel codice XAML seguente viene illustrato come creare un [`StackLayout`](xref:Xamarin.Forms.StackLayout) verticale contenente tre oggetti [`Label`](xref:Xamarin.Forms.Label) :

```xaml
<StackLayout Margin="20,35,20,25">
    <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
    <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
    <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
</StackLayout>
```

In una [`StackLayout`](xref:Xamarin.Forms.StackLayout), se la dimensione di un elemento non è impostata in modo esplicito, si espande per riempire la larghezza disponibile o l'altezza se la proprietà [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) è impostata su [`Horizontal`](xref:Xamarin.Forms.StackOrientation).

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) viene spesso usato come layout padre, che contiene altri layout figlio. Tuttavia, non è consigliabile utilizzare un `StackLayout` per riprodurre un layout di [`Grid`](xref:Xamarin.Forms.Grid) utilizzando una combinazione di oggetti di `StackLayout`. Il codice seguente illustra un esempio di questa procedura non valida:

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

L'operazione risulta dispendiosa poiché vengono eseguiti calcoli di layout non necessari. Al contrario, è possibile ottenere il layout desiderato utilizzando un [`Grid`](xref:Xamarin.Forms.Grid).

> [!TIP]
> Quando si usa un [`StackLayout`](xref:Xamarin.Forms.StackLayout), assicurarsi che sia impostato un solo elemento figlio su [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands). Questa proprietà garantisce che l'elemento figlio specificato occupi lo spazio più ampio che `StackLayout` è in grado di assegnare ed è dispendioso eseguire questi calcoli più di una volta.

Per altre informazioni, vedere [Novell. Forms StackLayout](stack-layout.md).

## <a name="grid"></a>Grid

Una [`Grid`](xref:Xamarin.Forms.Grid) viene utilizzata per la visualizzazione di elementi in righe e colonne, che possono avere dimensioni proporzionali o assolute. Le righe e le colonne di una griglia vengono specificate con le proprietà [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) e [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) .

Per posizionare gli elementi in celle [`Grid`](xref:Xamarin.Forms.Grid) specifiche, utilizzare le proprietà [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) e [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) associate. Per fare in modo che gli elementi si estendano su più righe e colonne, utilizzare il [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) e [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) proprietà associate.

> [!NOTE]
> Un layout di [`Grid`](xref:Xamarin.Forms.Grid) non deve essere confuso con le tabelle e non è destinato a presentare dati tabulari. Diversamente dalle tabelle HTML, una `Grid` è destinata al layout del contenuto. Per la visualizzazione dei dati tabulari, è consigliabile usare [ListView](~/xamarin-forms/user-interface/listview/index.md), [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)o [TableView](~/xamarin-forms/user-interface/tableview.md).

Nel codice XAML seguente viene illustrato come creare un [`Grid`](xref:Xamarin.Forms.Grid) con due righe e due colonne:

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
           WidthRequest="200" />
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

Lo spazio può essere distribuito all'interno di una colonna o di una riga usando il ridimensionamento automatico, che consente alle dimensioni delle colonne e delle righe di adattarsi al contenuto. Questa operazione viene eseguita impostando l'altezza di un [`RowDefinition`](xref:Xamarin.Forms.RowDefinition)o la larghezza di un [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) [`Auto`](xref:Xamarin.Forms.GridLength.Auto). Il dimensionamento proporzionale può essere utilizzato anche per distribuire lo spazio disponibile tra le righe e le colonne della griglia in base alle proporzioni ponderate. Questa operazione viene eseguita impostando l'altezza di un `RowDefinition`o la larghezza di un `ColumnDefinition`su un valore che utilizza l'operatore `*`.

> [!CAUTION]
> Provare a verificare che il minor numero possibile di righe e colonne sia impostato su [`Auto`](xref:Xamarin.Forms.GridLength.Auto) dimensioni. Ogni riga o colonna ridimensionata automaticamente causerà l'esecuzione di ulteriori calcoli di layout da parte del motore di layout. Usare invece righe e colonne con dimensioni fisse, se possibile. In alternativa, impostare righe e colonne in modo da occupare una quantità proporzionale di spazio con il valore di enumerazione [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) .

Per ulteriori informazioni, vedere [Novell. Forms Grid](grid.md).

## <a name="flexlayout"></a>FlexLayout

Una [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) è simile a una [`StackLayout`](xref:Xamarin.Forms.StackLayout) perché Visualizza gli elementi figlio orizzontalmente o verticalmente in uno stack. Tuttavia, un `FlexLayout` può anche eseguire il wrapping dei relativi elementi figlio se sono presenti troppi per adattarsi a una singola riga o colonna e consente inoltre un controllo più granulare delle dimensioni, dell'orientamento e dell'allineamento degli elementi figlio.

Nel codice XAML seguente viene illustrato come creare un [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) che visualizza le visualizzazioni in una singola colonna:

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

- La proprietà [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) è impostata su `Column`, che fa sì che gli elementi figlio del `FlexLayout` siano disposti in una singola colonna di elementi.
- La proprietà [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) è impostata su `Center`, che fa sì che ogni elemento sia centrato orizzontalmente.
- La proprietà [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) è impostata su `SpaceEvenly`, che alloca tutti gli spazi verticali rimanenti equamente tra tutti gli elementi e sopra il primo elemento e sotto l'ultimo elemento.

Per altre informazioni, vedere [Novell. Forms FlexLayout](flex-layout.md).

## <a name="relativelayout"></a>RelativeLayout

Una [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) viene utilizzata per posizionare e ridimensionare gli elementi relativi alle proprietà del layout o degli elementi di pari livello. Per impostazione predefinita, un elemento viene posizionato nell'angolo superiore sinistro del layout. Un `RelativeLayout` può essere usato per creare interfacce utente che si ridimensionano proporzionalmente tra le dimensioni dei dispositivi.

All'interno di un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout), le posizioni e le dimensioni vengono specificate come vincoli. I vincoli hanno proprietà [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor) e [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant) , che possono essere usate per definire posizioni e dimensioni come multipli (o frazioni) delle proprietà di altri oggetti, più una costante. Inoltre, le costanti possono essere negative.

> [!NOTE]
> Un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) supporta il posizionamento di elementi all'esterno dei propri limiti.

Il codice XAML seguente illustra come disporre gli elementi in un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout):

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

- Alla [`BoxView`](xref:Xamarin.Forms.BoxView) blu viene assegnata una dimensione esplicita delle unità 50x50 indipendenti dal dispositivo. Si trova nell'angolo superiore sinistro del layout, ovvero la posizione predefinita.
- Al [`BoxView`](xref:Xamarin.Forms.BoxView) rosso viene assegnata una dimensione esplicita delle unità 50x50 indipendenti dal dispositivo. Viene posizionata nell'angolo superiore destro del layout.
- Al [`BoxView`](xref:Xamarin.Forms.BoxView) grigio viene assegnata una larghezza esplicita di 15 unità indipendenti dal dispositivo e l'altezza è impostata su 75% dell'altezza del padre.
- Al [`BoxView`](xref:Xamarin.Forms.BoxView) verde non viene assegnata una dimensione esplicita. La relativa posizione viene impostata in relazione all'`BoxView` `pole`denominata.

> [!WARNING]
> Evitare di utilizzare un `RelativeLayout` quando possibile. Causa un notevole aumento del lavoro per la CPU.

Per altre informazioni, vedere [Novell. Forms sul relativelayout](relative-layout.md).

## <a name="absolutelayout"></a>AbsoluteLayout

Un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) viene usato per posizionare e ridimensionare gli elementi usando valori espliciti o i valori relativi alle dimensioni del layout. La posizione viene specificata dall'angolo superiore sinistro del figlio rispetto all'angolo superiore sinistro del `AbsoluteLayout`.

Un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) deve essere considerato come un layout per scopi specifici da usare solo quando è possibile imporre una dimensione agli elementi figlio o quando le dimensioni dell'elemento non influiscono sul posizionamento di altri elementi figlio. Un uso standard di questo layout consiste nel creare una sovrapposizione, che copre la pagina con altri controlli, forse per impedire all'utente di interagire con i normali controlli della pagina.

> [!IMPORTANT]
> Le proprietà `HorizontalOptions` e `VerticalOptions` non hanno effetto sugli elementi figlio di un `AbsoluteLayout`.

All'interno di un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)la proprietà associata [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) viene utilizzata per specificare la posizione orizzontale, la posizione verticale, la larghezza e l'altezza di un elemento. Inoltre, la proprietà [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) associata specifica il modo in cui verranno interpretati i limiti del layout.

Il codice XAML seguente illustra come disporre gli elementi in un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout):

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

- A ogni [`BoxView`](xref:Xamarin.Forms.BoxView) viene assegnata una dimensione esplicita 100x100 e viene visualizzata nella stessa posizione, centrata orizzontalmente.
- Il [`BoxView`](xref:Xamarin.Forms.BoxView) rosso viene ruotato di 30 gradi e il `BoxView` verde viene ruotato di 60 gradi.
- In ogni [`BoxView`](xref:Xamarin.Forms.BoxView)la proprietà associata [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) è impostata su `PositionProportional`, a indicare che la posizione è proporzionale allo spazio rimanente dopo che è stata contabilizzata la larghezza e l'altezza.

> [!CAUTION]
> Evitare di utilizzare la proprietà [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) quando possibile, perché il motore di layout eseguirà calcoli di layout aggiuntivi.

Per altre informazioni, vedere [Novell. Forms AbsoluteLayout](absolute-layout.md).

## <a name="input-transparency"></a>Trasparenza input

Ogni elemento visivo dispone di una proprietà [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) utilizzata per definire se l'elemento riceve input. Il valore predefinito è `false`, assicurando che l'elemento riceva input.

Quando questa proprietà viene impostata su una classe layout, il relativo valore viene trasferito agli elementi figlio. Pertanto, se si imposta la proprietà [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) su `true` in una classe layout, tutti gli elementi all'interno del layout non riceveranno input.

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
