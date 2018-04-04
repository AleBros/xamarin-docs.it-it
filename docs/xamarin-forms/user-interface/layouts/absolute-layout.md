---
title: AbsoluteLayout
description: Consente di creare interfacce utente pixel perfetto AbsoluteLayout.
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: d07026fbcc43a43a9f26d85ad15d5a4e3165e2ef
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="absolutelayout"></a>AbsoluteLayout

[`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) posizione e dimensioni proporzionale alle dimensioni e posizione o valori assoluti di elementi figlio. Visualizzazioni figlio possono essere posizionato e dimensioni utilizzando valori proporzionali o statico e proporzionale e valori statici possono essere combinati.

[![](absolute-layout-images/layouts-sml.png "Xamarin.Forms Layouts")](absolute-layout-images/layouts.png#lightbox "Xamarin.Forms Layouts")

In questo articolo illustra:

- **[Scopo](#Purpose)**  &ndash; utilizzi comuni di `AbsoluteLayout`.
- **[Utilizzo](#Usage)**  &ndash; illustrato come utilizzare `AbsoluteLayout` per ottenere la progettazione desiderata.
  - **[Layout proporzionale](#Proportional_Layouts)**  &ndash; sui valori proporzionali come funzionano in un `AbsoluteLayout`.
  - **[Specificare i valori](#Specifying_Values)**  &ndash; comprendere come vengono specificati proporzionale e valori assoluti.
  - **[I valori proporzionali](#Proportional_Values)**  &ndash; come proporzionale sui valori di lavoro.
    - **[Valori assoluti](#Absolute_Values)**  &ndash; comprendere il funzionamento di valori assoluti.

<a name="Purpose" />

## <a name="purpose"></a>Scopo

A causa del modello di posizionamento `AbsoluteLayout`, il layout rende relativamente semplice posizionare gli elementi in modo che siano centrato o allineato al lato qualsiasi del layout. Con proporzionale dimensioni e posizioni, gli elementi in un `AbsoluteLayout` possibile scalare automaticamente a qualsiasi dimensione di visualizzazione. Per gli elementi in cui deve ridimensionare solo la posizione, ma non la dimensione, è possibile combinare valori assoluti e proporzionali.

`AbsoluteLayout` è possibile utilizzare qualsiasi gli elementi devono essere posizionati all'interno di una vista e risulta particolarmente utile quando l'allineamento di elementi al bordo.

<a name="Usage" />

## <a name="usage"></a>Utilizzo

<a name="Proportional_Layouts" />

### <a name="proportional-layouts"></a>Layout proporzionale

`AbsoluteLayout` dispone di un modello di ancoraggio univoco in base al quale è posizionato il punto di ancoraggio dell'elemento relativo all'elemento come relativo il layout è posizionato l'elemento quando viene utilizzato il posizionamento proporzionale. Quando viene utilizzato il posizionamento assoluto, il punto di ancoraggio è a (0,0) all'interno della visualizzazione. Questo comportamento ha due conseguenze importanti:

- Gli elementi non possono essere posizionati fuori schermo utilizzando i valori proporzionali.
- Gli elementi possono essere posizionati in modo affidabile lato del layout o al centro, indipendentemente dalle dimensioni del layout o del dispositivo.

`AbsoluteLayout`, ad esempio `RelativeLayout`, è in grado di posizionare gli elementi in modo che si sovrappongono.

Nota Nella schermata seguente, il punto di ancoraggio della finestra è un punto bianco. Si noti la relazione tra il punto di ancoraggio e la casella all'interno del layout:

![](absolute-layout-images/anchor-start.png "Ancoraggio all'inizio")
![](absolute-layout-images/anchor-center.png "ancoraggio Center")
![](absolute-layout-images/anchor-end.png "ancoraggio alla fine")

<a name="Specifying_Values" />

### <a name="specifying-values"></a>Specifica di valori

Viste all'interno di un `AbsoluteLayout` vengono posizionati utilizzando quattro valori:

- **X** &ndash; la posizione x (orizzontale) di ancoraggio della vista
- **Y** &ndash; la posizione y (verticale) di ancoraggio della vista
- **Larghezza** &ndash; la larghezza della visualizzazione
- **Altezza** &ndash; l'altezza della visualizzazione

Tali valori possono essere impostati come un [proporzionale](#Proportional_Values) valore o un [assoluto](#Absolute_Values) valore.

I valori vengono specificati come una combinazione di limiti e un flag. `LayoutBounds` è un [ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/) costituito da quattro valori: `x`, `y`, `width`, `height`.

### <a name="absolutelayoutflags"></a>AbsoluteLayoutFlags

[`AbsoluteLayoutFlags`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayoutFlags/) Specifica la modalità di interpretazione di valori e sono disponibili le opzioni predefinite seguenti:

- **Nessuna** &ndash; tutti i valori vengono interpretati come assoluti. Questo è il valore predefinito se non è specificato alcun flag di layout.
- **Tutti** &ndash; interpreta tutti i valori come proporzionale.
- **WidthProportional** &ndash; interpreta il `Width` valore proporzionale e tutti gli altri valori come assoluti.
- **HeightProportional** &ndash; interpreta solo il valore di altezza come proporzionale con tutti gli altri valori assoluti.
- **XProportional** &ndash; interpreta il `X` valore proporzionale, considerando tutti gli altri valori come assoluti.
- **YProportional** &ndash; interpreta il `Y` valore proporzionale, considerando tutti gli altri valori come assoluti.
- **PositionProportional** &ndash; interpreta il `X` e `Y` i valori come proporzionale, mentre i valori di dimensione vengono interpretati come assoluti.
- **SizeProportional** &ndash; interpreta il `Width` e `Height` i valori come proporzionale mentre i valori di posizione sono assoluti.

In XAML, limiti e i flag vengono impostati come parte della definizione di viste all'interno del layout, utilizzando il `AbsoluteLayout.LayoutBounds` proprietà. Limiti vengono impostati come un elenco delimitato da virgole di valori, `X`, `Y`, `Width`, e `Height`, in questo ordine. Viene anche specificato nella dichiarazione di visualizzazioni nel layout utilizzando il `AbsoluteLayout.LayoutFlags` proprietà. Si noti che i flag possono essere combinati in XAML tramite un elenco delimitato da virgole. Si consideri l'esempio seguente:

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

![](absolute-layout-images/exploration.png "Esempi di AbsoluteLayout")

Tenere presente quanto segue:

- L'etichetta nell'area viene posizionata utilizzando i valori di dimensioni e la posizione assoluti. Per questo motivo, viene visualizzato al centro su iPhone 4S e inferiore, ma non al centro nei dispositivi più grandi.
- Il testo nella parte inferiore del layout è posizionato utilizzando i valori di dimensioni e posizione proporzionali. Viene sempre visualizzato in basso al centro del layout, ma aumenteranno le dimensioni con dimensioni maggiori del layout.
- Tre colori `BoxView`s sono posizionati i bordi superiore, destro e sinistro dello schermo usando proporzionale posizione e dimensioni assolute.

Di seguito consente di ottenere lo stesso layout in c#:

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

Valori proporzionali definiscono una relazione tra un layout e una vista. Questa relazione definisce posizione o il valore di scala di una visualizzazione figlio come una percentuale del valore corrispondente del layout del padre. Questi valori sono espressi come `double`s con valori compresi tra 0 e 1.

Valori proporzionali vengono utilizzati per posizione e le viste di dimensioni all'interno del layout. In tal caso, quando la larghezza di una visualizzazione è impostata come una proporzione, il valore di larghezza risultante è la proporzione moltiplicata per il `AbsoluteLayout`della larghezza. Ad esempio, con un `AbsoluteLayout` della larghezza `500` e una visualizzazione è impostata su larghezza proporzionale,5, la larghezza di rendering della visualizzazione sarà 250 (500 x,5).

Per utilizzare valori proporzionali, impostare `LayoutBounds` utilizzando (x, y) proporzioni e dimensioni proporzionale, quindi impostare `LayoutFlags` a `All`.

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

Valori assoluti definire in modo esplicito in viste dovrebbero essere posizionate all'interno del layout. A differenza dei valori proporzionali, i valori assoluti sono in grado di posizionamento e il ridimensionamento di una vista che non rientra nei limiti del layout.

Utilizzando i valori assoluti per il posizionamento può essere pericoloso se le dimensioni del layout non è noto. Quando si usano posizioni assolute, è Impossibile offset un elemento al centro dello schermo in corrispondenza di una dimensione in qualsiasi altra dimensione. È importante testare l'app tra le diverse dimensioni dello schermo dei dispositivi supportati.

Per utilizzare i valori del layout assoluto, impostare `LayoutBounds` utilizzando (x, y) coordinate e dimensioni esplicite, quindi impostare `LayoutFlags` a `None`.

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

## <a name="exploring-a-complex-layout"></a>Esplorazione di un Layout complesso

Ogni layout di disporre di vantaggi e svantaggi per la creazione di un layout specifico. Questa serie di articoli di layout, un'app di esempio è stata creata con lo stesso layout di pagina implementato utilizzando tre layout diversi.

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

Il codice precedente produce lo schema seguente:

![](absolute-layout-images/abs.png "AbsoluteLayout complessi")

Si noti che, a causa della differenza nella modalità di rendering dei pulsanti da Windows Phone, alcuni dei cerchi sono stati sostituiti da boxviews nella schermata di Windows Phone.

Si noti che `AbsoluteLayout`s sono annidate, perché in alcuni casi la nidificazione di layout può essere più semplice rispetto a tutti gli elementi all'interno del layout stesso presentazione.



## <a name="related-links"></a>Collegamenti correlati

- [Creazione di App per dispositivi mobili con xamarin. Forms, capitolo 14](https://developer.xamarin.com/r/xamarin-forms/book/chapter14.pdf)
- [AbsoluteLayout](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/)
- [Layout (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Esempio BusinessTumble (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
