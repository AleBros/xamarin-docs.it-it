---
title: Xamarin.FormsAbsoluteLayout
description: Questo articolo illustra come usare la Xamarin.Forms classe AbsoluteLayout per creare interfacce utente perfette per i pixel. Questa classe posiziona e ridimensiona gli elementi figlio proporzionali alle dimensioni e alla posizione o ai valori assoluti.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d81a80e1f1190cbdffd578024cf9c6db1e7737e1
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139243"
---
# <a name="xamarinforms-absolutelayout"></a>Xamarin.FormsAbsoluteLayout

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)posiziona e ridimensiona gli elementi figlio proporzionali alle dimensioni e alla posizione o ai valori assoluti. Le visualizzazioni figlio possono essere posizionate e dimensionate mediante valori proporzionali o valori statici, mentre i valori proporzionali e statici possono essere combinati.

[![](absolute-layout-images/layouts-sml.png "Xamarin.Forms Layouts")](absolute-layout-images/layouts.png#lightbox "Xamarin.Forms Layouts")

Questo articolo illustrerà quanto segue:

- **[Scopo](#Purpose)** &ndash; usi comuni per `AbsoluteLayout` .
- **[Utilizzo](#Usage)** &ndash; di come usare `AbsoluteLayout` per ottenere la progettazione desiderata.
  - Layout proporzionali **[Proportional Layouts](#Proportional_Layouts)** &ndash; informazioni sul funzionamento dei valori proporzionali in un oggetto `AbsoluteLayout` .
  - **[Specifica di valori](#Specifying_Values)** &ndash; comprendere il modo in cui vengono specificati i valori assoluti e proporzionali.
  - Valori proporzionali **[Proportional Values](#Proportional_Values)** &ndash; comprendere il funzionamento dei valori proporzionali.
    - **[Valori assoluti](#Absolute_Values)** &ndash; informazioni sul funzionamento dei valori assoluti.

<a name="Purpose" />

## <a name="purpose"></a>Scopo

A causa del modello di posizionamento di `AbsoluteLayout` , il layout rende relativamente semplice la posizione degli elementi in modo che vengano scaricati con qualsiasi lato del layout o centrati. Con dimensioni e posizioni proporzionali, gli elementi in un oggetto `AbsoluteLayout` possono essere ridimensionati automaticamente in qualsiasi dimensione di visualizzazione. Per gli elementi in cui è necessario ridimensionare solo la posizione ma non le dimensioni, è possibile combinare valori assoluti e proporzionali.

`AbsoluteLayout`può essere usato in qualsiasi punto in cui gli elementi devono essere posizionati all'interno di una visualizzazione ed è particolarmente utile quando si allineano elementi ai bordi.

<a name="Usage" />

## <a name="usage"></a>Utilizzo

<a name="Proportional_Layouts" />

### <a name="proportional-layouts"></a>Layout proporzionali

`AbsoluteLayout`dispone di un modello di ancoraggio univoco in base al quale l'ancoraggio dell'elemento è posizionato in relazione al relativo elemento quando l'elemento è posizionato in relazione al layout quando viene utilizzato il posizionamento proporzionale. Quando si usa il posizionamento assoluto, l'ancoraggio si trova in (0,0) all'interno della vista. Questo ha due importanti conseguenze:

- Non è possibile posizionare gli elementi fuori dallo schermo usando valori proporzionali.
- Gli elementi possono essere posizionati in modo affidabile lungo qualsiasi lato del layout o al centro, indipendentemente dalle dimensioni del layout o del dispositivo.

`AbsoluteLayout`, `RelativeLayout` ad esempio, è in grado di posizionare gli elementi in modo che si sovrappongano.

Si noti che nella schermata seguente l'ancoraggio della casella è un punto bianco. Si noti la relazione tra l'ancoraggio e la casella mentre viene spostata nel layout:

![](absolute-layout-images/anchor-start.png "Anchor at Start")
![](absolute-layout-images/anchor-center.png "Anchor at Center")
![](absolute-layout-images/anchor-end.png "Anchor at End")

<a name="Specifying_Values" />

### <a name="specifying-values"></a>Specifica di valori

Le visualizzazioni all'interno di `AbsoluteLayout` vengono posizionate usando quattro valori:

- **X** &ndash; la posizione x (orizzontale) dell'ancoraggio della visualizzazione
- **Y** &ndash; Posizione y (verticale) dell'ancoraggio della visualizzazione
- **Larghezza** &ndash; larghezza della visualizzazione
- **Altezza** &ndash; altezza della visualizzazione

Ognuno di questi valori può essere impostato come un valore [proporzionale](#Proportional_Values) o un valore [assoluto](#Absolute_Values) .

I valori vengono specificati come una combinazione di limiti e un flag. `LayoutBounds`è [`Rectangle`](xref:Xamarin.Forms.Rectangle) costituito da quattro valori: `x` , `y` , `width` , `height` .

### <a name="absolutelayoutflags"></a>AbsoluteLayoutFlags

[`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)Specifica il modo in cui i valori verranno interpretati e le opzioni predefinite seguenti:

- **Nessuna** &ndash; interpreta tutti i valori come assoluti. Si tratta del valore predefinito se non viene specificato alcun flag di layout.
- **Tutto** &ndash; interpreta tutti i valori come proporzionali.
- **WidthProportional** &ndash; interpreta il `Width` valore come proporzionale e tutti gli altri valori come assoluti.
- **HeightProportional** &ndash; interpreta solo il valore di altezza come proporzionale con tutti gli altri valori assoluti.
- **XProportional** &ndash; interpreta il `X` valore come proporzionale, considerando tutti gli altri valori come assoluti.
- **YProportional** &ndash; interpreta il `Y` valore come proporzionale, considerando tutti gli altri valori come assoluti.
- **PositionProportional** &ndash; interpreta i `X` valori e `Y` come proporzionali, mentre i valori delle dimensioni sono interpretati come assoluti.
- **SizeProportional** &ndash; interpreta i `Width` valori e `Height` come proporzionali mentre i valori di posizione sono assoluti.

In XAML, i limiti e i flag vengono impostati come parte della definizione di viste all'interno del layout, usando la `AbsoluteLayout.LayoutBounds` Proprietà. I limiti vengono impostati come un elenco delimitato da virgole di valori,,, `X` `Y` `Width` e `Height` , in questo ordine. I flag vengono inoltre specificati nella dichiarazione di viste nel layout utilizzando la `AbsoluteLayout.LayoutFlags` Proprietà. Si noti che i flag possono essere combinati in XAML usando un elenco delimitato da virgole. Prendere in considerazione gli esempi seguenti:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.AbsoluteLayoutExploration"
Title="Absolute Layout Exploration">
    <ContentPage.Content>
        <AbsoluteLayout>
            <Label Text="I'm centered on iPhone 4 but no other device"
                AbsoluteLayout.LayoutBounds="115,150,100,100" LineBreakMode="WordWrap"  />
            <Label Text="I'm bottom center on every device."
                AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"
                LineBreakMode="WordWrap"  />
            <BoxView Color="Olive"  AbsoluteLayout.LayoutBounds="1,.5, 25, 100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Red" AbsoluteLayout.LayoutBounds="0,.5,25,100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Blue" AbsoluteLayout.LayoutBounds=".5,0,100,25"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

![](absolute-layout-images/exploration.png "AbsoluteLayout Examples")

Tenere presente quanto segue:

- L'etichetta al centro viene posizionata usando valori assoluti per le dimensioni e la posizione. Per questo motivo, appare centrato su iPhone 4S e versioni precedenti, ma non centrato su dispositivi più grandi.
- Il testo nella parte inferiore del layout viene posizionato utilizzando valori di posizione e dimensioni proporzionali. Viene sempre visualizzata nella parte centrale inferiore del layout, ma le dimensioni aumentano con dimensioni di layout maggiori.
- Tre oggetti colorati `BoxView` vengono posizionati in corrispondenza dei bordi superiore, sinistro e destro dello schermo, usando la posizione proporzionale e le dimensioni assolute.

Il codice seguente consente di ottenere lo stesso layout in C#:

```csharp
public class AbsoluteLayoutExplorationCode : ContentPage
{
    public AbsoluteLayoutExplorationCode ()
    {
        Title = "Absolute Layout Exploration - Code";
        var layout = new AbsoluteLayout();

        var centerLabel = new Label {
        Text = "I'm centered on iPhone 4 but no other device.",
        LineBreakMode = LineBreakMode.WordWrap};

        AbsoluteLayout.SetLayoutBounds (centerLabel, new Rectangle (115, 159, 100, 100));
        // No need to set layout flags, absolute positioning is the default

        var bottomLabel = new Label { Text = "I'm bottom center on every device.", LineBreakMode = LineBreakMode.WordWrap };
        AbsoluteLayout.SetLayoutBounds (bottomLabel, new Rectangle (.5, 1, .5, .1));
        AbsoluteLayout.SetLayoutFlags (bottomLabel, AbsoluteLayoutFlags.All);

        var rightBox = new BoxView{ Color = Color.Olive };
        AbsoluteLayout.SetLayoutBounds (rightBox, new Rectangle (1, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (rightBox, AbsoluteLayoutFlags.PositionProportional);

        var leftBox = new BoxView{ Color = Color.Red };
        AbsoluteLayout.SetLayoutBounds (leftBox, new Rectangle (0, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (leftBox, AbsoluteLayoutFlags.PositionProportional);

        var topBox = new BoxView{ Color = Color.Blue };
        AbsoluteLayout.SetLayoutBounds (topBox, new Rectangle (.5, 0, 100, 25));
        AbsoluteLayout.SetLayoutFlags (topBox, AbsoluteLayoutFlags.PositionProportional);

        layout.Children.Add (bottomLabel);
        layout.Children.Add (centerLabel);
        layout.Children.Add (rightBox);
        layout.Children.Add (leftBox);
        layout.Children.Add (topBox);

        Content = layout;
    }
}
```

<a name="Proportional_Values" />

### <a name="proportional-values"></a>Valori proporzionali

I valori proporzionali definiscono una relazione tra un layout e una visualizzazione. Questa relazione definisce una posizione o un valore di scala della visualizzazione figlio come una proporzione del valore corrispondente del layout padre. Questi valori sono espressi come `double` s con valori compresi tra 0 e 1.

I valori proporzionali vengono usati per posizionare e ridimensionare le visualizzazioni all'interno del layout. Quindi, quando la larghezza di una vista è impostata come proporzione, il valore della larghezza risultante è la proporzione moltiplicata per la `AbsoluteLayout` larghezza di. Ad esempio, con un oggetto `AbsoluteLayout` di larghezza `500` e una vista impostata in modo da avere una larghezza proporzionale di 0,5, la larghezza di rendering della visualizzazione sarà 250 (500 x. 5).

Per utilizzare valori proporzionali, impostare `LayoutBounds` utilizzando le proporzioni (x, y) e le dimensioni proporzionali, quindi impostare `LayoutFlags` su `All` .

In XAML:

```xaml
<Label Text="I'm bottom center on every device."
  AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"  />
```

In C#:

```csharp
var label = new Label {Text = "I'm bottom center on every device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(.5,1,.5,.1));
AbsoluteLayout.SetLayoutFlags(label, AbsoluteLayoutFlags.All);
```

<a name="Absolute_Values" />

### <a name="absolute-values"></a>Valori assoluti

I valori assoluti definiscono in modo esplicito le visualizzazioni da posizionare all'interno del layout. Diversamente dai valori proporzionali, i valori assoluti sono in grado di posizionare e ridimensionare una visualizzazione che non rientra nei limiti del layout.

L'utilizzo di valori assoluti per il posizionamento può essere pericoloso quando la dimensione del layout non è nota. Quando si utilizzano posizioni assolute, è possibile che un elemento al centro dello schermo a una dimensione venga sfalsato con qualsiasi altra dimensione. È importante testare l'app nelle diverse dimensioni dello schermo dei dispositivi supportati.

Per usare i valori di layout assoluti, impostare `LayoutBounds` usando le coordinate (x, y) e le dimensioni esplicite, quindi impostare `LayoutFlags` su `None` .

In XAML:

```xaml
<Label Text="I'm centered on iPhone 4 but no other device."
  AbsoluteLayout.LayoutBounds="115,150,100,100"  />
```

In C#:

```csharp
var label = new Label {Text = "I'm centered on iPhone 4 but no other device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(115,150,100,100));
```

## <a name="exploring-a-complex-layout"></a>Esplorazione di un layout complesso

Ogni layout presenta punti di forza e debolezze per la creazione di layout specifici. In questa serie di articoli di layout è stata creata un'app di esempio con lo stesso layout di pagina implementato con tre layout diversi.

Si consideri il codice XAML seguente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.AbsoluteLayoutPage"
Title="AbsoluteLayout">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Save" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <ScrollView>
            <AbsoluteLayout BackgroundColor="Maroon">
                <BoxView BackgroundColor="Gray" AbsoluteLayout.LayoutBounds="0
                    0,1,100" AbsoluteLayout.LayoutFlags="XProportional,YProportional,WidthProportional" />
                <Button BackgroundColor="Maroon"
                    AbsoluteLayout.LayoutBounds=".5,55,70,70" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="35" />
                <Button BackgroundColor="Red" AbsoluteLayout.LayoutBounds=".5
                    60,60,60" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="30" />
                <Label Text="User Name" FontAttributes="Bold" FontSize="26"
                    TextColor="Black" HorizontalTextAlignment="Center" AbsoluteLayout.LayoutBounds=".5,140,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <Entry Text="Bio + Hashtags" TextColor="White"
                    BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds=".5,180,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <AbsoluteLayout BackgroundColor="White"
                        AbsoluteLayout.LayoutBounds="0, 220, 1, 50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional">
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional">
                        <Button BackgroundColor="Black" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Accent Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="1,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional,XProportional">
                        <Button BackgroundColor="Maroon" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Primary Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,270,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Age:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="35" TextColor="White" BackgroundColor="Maroon"
                        AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,320,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Interests:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin.Forms" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,370,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Ask me about:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin, C#, .NET, Mono" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
            </AbsoluteLayout>
        </ScrollView>
    </ContentPage.Content>
</ContentPage>
```

Il codice precedente genera il layout seguente:

![](absolute-layout-images/abs.png "Complex AbsoluteLayout")

Si noti che `AbsoluteLayout` i sono annidati, perché in alcuni casi i layout di annidamento possono essere più semplici rispetto alla presentazione di tutti gli elementi all'interno dello stesso layout.

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di app per dispositivi mobili con Xamarin.Forms , capitolo 14](https://developer.xamarin.com/r/xamarin-forms/book/chapter14.pdf)
- [AbsoluteLayout](xref:Xamarin.Forms.AbsoluteLayout)
- [Layout (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Esempio di BusinessTumble (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
