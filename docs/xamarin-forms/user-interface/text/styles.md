---
title: Xamarin.FormsStili di testo
description: Questo articolo illustra come applicare stili di testo nelle Xamarin.Forms applicazioni. Gli stili possono essere definiti una volta e usati da molte visualizzazioni, ma uno stile può essere usato solo con visualizzazioni di un solo tipo.
ms.prod: xamarin
ms.assetid: 57C0CFD6-A568-46B8-ADA1-BF25681893CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7d82348231c4b4905f2f70b80f73c45f2f0bf66b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84572000"
---
# <a name="xamarinforms-text-styles"></a>Xamarin.FormsStili di testo

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Stile del testo in Novell. Forms_

Gli stili possono essere utilizzati per regolare l'aspetto di etichette, voci ed editor. Gli stili possono essere definiti una volta e usati da molte visualizzazioni, ma uno stile può essere usato solo con visualizzazioni di un solo tipo.
Gli stili possono essere specificati `Key` e applicati in modo selettivo utilizzando la proprietà di un controllo specifico `Style` .

## <a name="built-in-styles"></a>Stili predefiniti

Xamarin.Formsinclude diversi stili [predefiniti](xref:Xamarin.Forms.Device.Styles) per gli scenari comuni:

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

Per applicare uno degli stili predefiniti, utilizzare l' `DynamicResource` estensione di markup per specificare lo stile:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

In C# gli stili predefiniti vengono selezionati da `Device.Styles` :

```csharp
label.Style = Device.Styles.TitleStyle;
```

![Esempio di stili del dispositivo](styles-images/builtinstyles.png)

## <a name="custom-styles"></a>Stili personalizzati

Gli stili sono costituiti da Setter e setter costituiti da proprietà e dai valori su cui verranno impostate le proprietà.

In C#, uno stile personalizzato per un'etichetta con testo rosso di dimensioni 30 verrebbe definito come segue:

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

Si noti che le risorse (inclusi tutti gli stili) vengono definite all'interno `ContentPage.Resources` di, che è un elemento di pari livello dell'elemento più familiare `ContentPage.Content` .

![Esempio di stili personalizzati](styles-images/customstyle.png)

## <a name="applying-styles"></a>Applicazione di stili

Una volta creato uno stile, può essere applicato a qualsiasi visualizzazione corrispondente `TargetType` .

In XAML gli stili personalizzati vengono applicati alle viste fornendo la relativa `Style` proprietà con un'estensione di markup che fa `StaticResource` riferimento allo stile desiderato:

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

In C# gli stili possono essere applicati direttamente a una vista o aggiunti a e recuperati da una pagina `ResourceDictionary` . Per aggiungere direttamente:

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

Per aggiungere e recuperare dall'oggetto della pagina `ResourceDictionary` :

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

Gli stili predefiniti vengono applicati in modo diverso, perché devono rispondere alle impostazioni di accessibilità. Per applicare gli stili predefiniti in XAML, `DynamicResource` viene utilizzata l'estensione di markup:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

In C# gli stili predefiniti vengono selezionati da `Device.Styles` :

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>Accessibilità

Gli stili predefiniti sono disponibili per semplificare il rispetto delle preferenze di accessibilità. Quando si usa uno degli stili predefiniti, le dimensioni dei caratteri aumentano automaticamente se un utente imposta le preferenze di accessibilità di conseguenza.

Si consideri l'esempio seguente della stessa pagina di viste con lo stile predefinito con le impostazioni di accessibilità abilitata e disabilitata:

Disabilitato:

![Stili del dispositivo con accessibilità disabilitata](styles-images/pre-access.png)

Abilitato:

![Stili del dispositivo con accessibilità abilitata](styles-images/post-access.png)

Per garantire l'accessibilità, assicurarsi che gli stili predefiniti vengano usati come base per qualsiasi stile correlato al testo all'interno dell'app e che si usi coerentemente gli stili. Per ulteriori informazioni sull'estensione e sull'utilizzo degli stili in generale, vedere [stili](~/xamarin-forms/user-interface/styles/index.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di app per dispositivi mobili con Xamarin.Forms , capitolo 12](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [Stili](~/xamarin-forms/user-interface/styles/index.md)
- [Testo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Style](xref:Xamarin.Forms.Style)
