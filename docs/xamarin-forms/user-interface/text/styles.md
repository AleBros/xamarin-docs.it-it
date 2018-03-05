---
title: Stili
description: Testo di stile in xamarin. Forms
ms.topic: article
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 764fb77bedf9e00427348e95b4ecf029ae94741f
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="styles"></a>Stili

_Testo di stile in xamarin. Forms_


Stili consente di modificare l'aspetto dell'editor, le voci e le etichette. Stili possono essere definiti una volta e utilizzati da numerose visualizzazioni, ma uno stile può essere utilizzato solo con le visualizzazioni di un tipo.
Stili possono essere assegnati un `Key` e applicati in modo selettivo usando un controllo specifico `Style` proprietà.

In questo articolo vengono illustrati gli argomenti seguenti:

- **[Gli stili predefiniti](#Built-In_Styles)**  &ndash; utilizzare gli stili predefiniti per le viste basate su testo stile in tutta l'app.
- **[Gli stili personalizzati](#Custom_Styles)**  &ndash; definiscono gli stili personalizzati quando le opzioni predefinite non sono sufficienti.
- **[Applicazione di stili](#Applying_Styles)**  &ndash; si applicano gli stili predefiniti e personalizzati alle visualizzazioni.
- **[Accessibilità](#Accessibility)**  &ndash; assicurarsi che il testo rispetta le impostazioni di accessibilità.

<a name="Built-In_Styles" />

## <a name="built-in-styles"></a>Stili predefiniti

Xamarin. Forms include numerose [incorporato](http://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/) stili per scenari comuni:

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

Per applicare uno degli stili incorporati di, utilizzare il `DynamicResource` estensione di markup per specificare lo stile:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

In c#, gli stili predefiniti vengono selezionati da `Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

![](styles-images/builtinstyles.png "Esempio di stili di dispositivo")

<a name="Custom_Styles" />

## <a name="custom-styles"></a>Stili personalizzati

Stili sono costituiti da set e set sono costituiti da proprietà e i valori di proprietà verranno impostati su.

In c#, uno stile personalizzato per un'etichetta con testo di colore rosso delle dimensioni 30 verrebbe definito come segue:

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

Si noti che le risorse (inclusi tutti gli stili) vengono definite all'interno di `ContentPage.Resources`, che è di pari livello di familiarità `ContentPage.Content` elemento.

![](styles-images/customstyle.png "Esempio di stili personalizzati")

<a name="Applying_Styles" />

## <a name="applying-styles"></a>Applicazione di stili

Dopo aver creato uno stile, può essere applicato a qualsiasi corrispondenza della vista relativa `TargetType`.

In XAML, gli stili personalizzati vengono applicati alle visualizzazioni fornendo loro `Style` proprietà con un `StaticResource` estensione di markup che fa riferimento lo stile desiderato:

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

In c#, stili possono essere applicati direttamente a una vista o aggiunti e recuperati da una pagina `ResourceDictionary`. Per aggiungere direttamente:

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

Per aggiungere e recuperare la pagina `ResourceDictionary`:

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

Gli stili predefiniti vengono applicati in modo diverso, perché è necessario rispondere per le impostazioni di accessibilità. Per applicare gli stili predefiniti in XAML, il `DynamicResource` viene utilizzata l'estensione di markup:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

In c#, gli stili predefiniti vengono selezionati da `Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>Accessibilità

Gli stili predefiniti disponibili per semplificare rispettano le preferenze di accessibilità. Quando si utilizza uno degli stili incorporati di, le dimensioni dei caratteri aumenterà automaticamente se un utente ha impostato le preferenze di accessibilità di conseguenza.

Si consideri l'esempio seguente della stessa pagina di viste con le impostazioni di accessibilità abilitati e disabilitati in stile con gli stili predefiniti:

Disabilitato:

![](styles-images/pre-access.png "Stili di dispositivi con accessibilità disabilitato")

Abilitato:

![](styles-images/post-access.png "Stili di dispositivi con accessibilità abilitato")

Per garantire l'accessibilità, assicurarsi che gli stili predefiniti vengono utilizzati come base per tutti gli stili correlati al testo all'interno dell'app e che si siano utilizzando gli stili in modo coerente. Vedere [stili](~/xamarin-forms/user-interface/styles/index.md) per ulteriori informazioni sull'estensione e utilizzo degli stili in generale.


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di App per dispositivi mobili con xamarin. Forms, capitolo 12](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [Stili](~/xamarin-forms/user-interface/styles/index.md)
- [Testo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Stile](http://developer.xamarin.comhttps://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
