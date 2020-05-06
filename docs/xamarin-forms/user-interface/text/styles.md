---
title: Stili del testo di Xamarin.Forms
description: Questo articolo illustra come stile del testo nelle applicazioni Xamarin.Forms. Stili possono essere definiti una sola volta e utilizzati da numerose visualizzazioni, ma uno stile può essere utilizzato solo con le viste di un tipo.
ms.prod: xamarin
ms.assetid: 57C0CFD6-A568-46B8-ADA1-BF25681893CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 66d7ae722281d9034cb4cdf1501662a7de396c2d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770243"
---
# <a name="xamarinforms-text-styles"></a>Stili del testo di Xamarin.Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Stile del testo in Xamarin.Forms_

Gli Stili sono utilizzabili per regolare l'aspetto di etichette, di voci e degli editor. Gli Stili possono essere definiti una sola volta e utilizzati in numerose visualizzazioni, ma uno stile può essere utilizzato solo con le viste di un tipo.
Agli Stili può essere assegnata una `Key` e applicati in modo selettivo usando la proprietà `Style` nel controllo.

<a name="Built-In_Styles" />

## <a name="built-in-styles"></a>Stili predefiniti

Xamarin.Forms include numerosi stili [incorporati](xref:Xamarin.Forms.Device.Styles) per scenari comuni:

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

Per applicare uno degli stili predefiniti, usare il `DynamicResource` estensione di markup per specificare lo stile:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

In c#, gli stili predefiniti vengono selezionati dal `Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

![Esempio di stili del dispositivo](styles-images/builtinstyles.png)

<a name="Custom_Styles" />

## <a name="custom-styles"></a>Stili personalizzati

Gli stili di Setter e setter costituiti da proprietà e verrà automaticamente impostati i valori di proprietà.

In c#, uno stile personalizzato per un'etichetta con testo di colore rosso delle dimensioni 30 deve essere definito come segue:

```csharp
var LabelStyle = new Style (typeof(Label)) {
    Setters = {
        new Setter {Property = Label.TextColorProperty, Value = Color.Red},
        new Setter {Property = Label.FontSizeProperty, Value = 30}
    }
};

var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

In XAML:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style x:Key="LabelStyle" TargetType="Label">
            <Setter Property="TextColor" Value="Red"/>
            <Setter Property="FontSize" Value="30"/>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>

<ContentPage.Content>
    <StackLayout>
        <Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
    </StackLayout>
</ContentPage.Content>
```

Si noti che le risorse (inclusi tutti gli stili) vengono definite all'interno `ContentPage.Resources`, che è un elemento di pari livello della più familiare `ContentPage.Content` elemento.

![Esempio di stili personalizzati](styles-images/customstyle.png)

<a name="Applying_Styles" />

## <a name="applying-styles"></a>Applicazione di stili

Dopo aver creato uno stile, può essere applicato a qualsiasi corrispondenza della vista relativa `TargetType`.

In XAML, gli stili personalizzati vengono applicati alle viste fornendo loro `Style` proprietà con un `StaticResource` estensione di markup che fa riferimento lo stile desiderato:

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

In c#, stili possono essere applicati direttamente a una vista o aggiunto a e recuperati da una pagina `ResourceDictionary`. Per aggiungere direttamente:

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

Per aggiungere e recuperare la pagina `ResourceDictionary`:

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

Gli stili predefiniti vengono applicati in modo diverso, perché sono necessarie per rispondere alle impostazioni di accessibilità. Per applicare gli stili predefiniti in XAML, il `DynamicResource` viene usata l'estensione di markup:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

In c#, gli stili predefiniti vengono selezionati dal `Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>Accessibilità

Gli stili predefiniti esistono per renderne più semplice rispettare le preferenze di accessibilità. Quando si usa uno degli stili predefiniti, le dimensioni dei caratteri aumenteranno automaticamente se un utente imposta le preferenze di accessibilità di conseguenza.

Si consideri l'esempio seguente della stessa pagina delle viste uno stile definito con gli stili predefiniti con le impostazioni di accessibilità abilitati e disabilitati:

Disabilitato:

![Stili del dispositivo con accessibilità disabilitata](styles-images/pre-access.png)

Abilitato:

![Stili del dispositivo con accessibilità abilitata](styles-images/post-access.png)

Per garantire l'accessibilità, assicurarsi che gli stili predefiniti vengono utilizzati come base per tutti gli stili correlate al testo all'interno dell'app e che si siano utilizzando gli stili in modo coerente. Visualizzare [stili](~/xamarin-forms/user-interface/styles/index.md) per altri dettagli sull'estensione e lavorare con gli stili in generale.

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di App per dispositivi mobili con Xamarin.Forms, capitolo 12](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [Stili](~/xamarin-forms/user-interface/styles/index.md)
- [Testo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Stile di visualizzazione](xref:Xamarin.Forms.Style)
