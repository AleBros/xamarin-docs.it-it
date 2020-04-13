---
title: Configurazione delle pagine per Xamarin.Forms Shell
description: La classe Shell definisce le proprietà associate che possono essere usate per configurare l'aspetto delle pagine nelle applicazioni shell Xamarin.Forms. Ciò include l'impostazione dei colori delle pagine, la disabilitazione della barra di spostamento, la disabilitazione della barra delle schede e la visualizzazione delle viste nella barra di spostamento.
ms.prod: xamarin
ms.assetid: 3FC2FBD1-C30B-4408-97B2-B04E3A2E4F03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2020
ms.openlocfilehash: 411c87c25701521bf27fbb863b02a90f8e523574
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305052"
---
# <a name="xamarinforms-shell-page-configuration"></a>Configurazione delle pagine per Xamarin.Forms Shell

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

La classe `Shell` definisce le proprietà associate che possono essere usate per configurare l'aspetto delle pagine nelle applicazioni shell Xamarin.Forms. Ciò include l'impostazione dei colori della pagina, l'impostazione della modalità di presentazione della pagina, la disabilitazione della barra di spostamento, la disabilitazione della barra delle schede e la visualizzazione delle visualizzazioni nella barra di spostamento.

## <a name="set-page-colors"></a>Impostare i colori delle pagine

La classe `Shell` definisce le proprietà associate seguenti, che possono essere usate per impostare i colori delle pagine in un'applicazione shell:

- `BackgroundColor`, di tipo `Color`, che definisce il colore di sfondo nel riquadro della Shell. Il colore non verrà inserito dietro il contenuto della shell.
- `DisabledColor`, di tipo `Color`, che definisce il colore per l'ombreggiatura di icone e testo disabilitati.
- `ForegroundColor`, di tipo `Color`, che definisce il colore per l'ombreggiatura di icone e testo.
- `TitleColor`, di tipo `Color`, che definisce il colore usato per il titolo della pagina corrente.
- `UnselectedColor`, di tipo `Color`, che definisce il colore per le icone e il testo non selezionati nel riquadro della shell.

Tutte queste proprietà sono [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) supportate da oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati e stili utilizzando gli stili XAML. Queste proprietà possono essere impostate anche usando fogli di stile CSS (Cascading Style Sheet). Per altre informazioni, vedere [Xamarin.Forms Shell specific properties](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties) (Proprietà specifiche della shell Xamarin.Forms).

> [!NOTE]
> Esistono anche proprietà che consentono di definire i colori delle schede. Per altre informazioni, vedere [Aspetto delle schede](tabs.md#tab-appearance).

Il codice XAML seguente illustra come impostare le proprietà per i colori in una classe `Shell` sottoclassata:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       x:Class="Xaminals.AppShell"
       BackgroundColor="#455A64"
       ForegroundColor="White"
       TitleColor="White"
       DisabledColor="#B4FFFFFF"
       UnselectedColor="#95FFFFFF">

</Shell>
```

In questo esempio, i valori dei colori verranno applicati a tutte le pagine nell'applicazione shell, a meno che non vengano sostituiti a livello di pagina.

Dato che le proprietà per i colori sono proprietà associate, possono anche essere impostate nelle singole pagine, per impostare i colori per pagine specifiche:

```xaml
<ContentPage ...
             Shell.BackgroundColor="Gray"
             Shell.ForegroundColor="White"
             Shell.TitleColor="Blue"
             Shell.DisabledColor="#95FFFFFF"
             Shell.UnselectedColor="#B4FFFFFF">

</ContentPage>
```

In alternativa, è possibile impostare le proprietà per i colori con uno stile XAML:

```xaml
<Style x:Key="DomesticShell"
       TargetType="Element" >
    <Setter Property="Shell.BackgroundColor"
            Value="#039BE6" />
    <Setter Property="Shell.ForegroundColor"
            Value="White" />
    <Setter Property="Shell.TitleColor"
            Value="White" />
    <Setter Property="Shell.DisabledColor"
            Value="#B4FFFFFF" />
    <Setter Property="Shell.UnselectedColor"
            Value="#95FFFFFF" />
</Style>
```

Per altre informazioni sugli stili XAML, vedere [Applicazione di stili alle app Xamarin.Forms con gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

## <a name="set-page-presentation-mode"></a>Impostare la modalità di presentazione della pagina

Per impostazione predefinita, una piccola animazione di `GoToAsync` spostamento si verifica quando si passa a una pagina con il metodo . Tuttavia, questo comportamento può `Shell.PresentationMode` essere modificato impostando [`ContentPage`](xref:Xamarin.Forms.ContentPage) la proprietà `PresentationMode` associata su un su uno dei membri dell'enumerazione:However, this behavior can be changed by setting the attached property on a to a of the enumeration members:

- `NotAnimated`indica che la pagina verrà visualizzata senza un'animazione di navigazione.
- `Animated`indica che la pagina verrà visualizzata con un'animazione di navigazione. Questo è il valore `Shell.PresentationMode` predefinito della proprietà associata.
- `Modal`indica che la pagina verrà visualizzata come pagina modale.
- `ModalAnimated`indica che la pagina verrà visualizzata come pagina modale, con un'animazione di navigazione.
- `ModalNotAnimated`indica che la pagina verrà visualizzata come pagina modale, senza un'animazione di navigazione.

> [!IMPORTANT]
> Il `PresentationMode` tipo è un'enumerazione di flag. Ciò significa che una combinazione di membri di enumerazione può essere applicata nel codice. Tuttavia, per facilità d'uso in XAML, `ModalAnimated` il membro è una combinazione di `Animated` e `Modal` membri e il `ModalNotAnimated` membro è una combinazione di `NotAnimated` e `Modal` membri. Per ulteriori informazioni sulle enumerazioni dei flag, vedere [Tipi di enumerazione come flag](/dotnet/csharp/language-reference/builtin-types/enum#enumeration-types-as-bit-flags)di bit .

L'esempio XAML `Shell.PresentationMode` seguente imposta la [`ContentPage`](xref:Xamarin.Forms.ContentPage)proprietà associata in un oggetto :

```xaml
<ContentPage ...
             Shell.PresentationMode="Modal">
    ...             
</ContentPage>
```

In questo esempio, l'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) è impostato per essere visualizzato come pagina `GoToAsync` modale, quando si passa alla pagina con il metodo .

## <a name="enable-navigation-bar-shadow"></a>Abilitare l'ombreggiatura della barra di navigazione

La `Shell` classe definisce la `NavBarHasShadow` proprietà `bool`associata, di tipo , che controlla se la barra di spostamento dispone di un'ombreggiatura. Per impostazione predefinita, `false` il valore della `true` proprietà è in iOS e in Android.By default the value of the property is on iOS, and on Android.

Mentre questa proprietà può essere impostata su un oggetto sottoclassed, può anche essere impostata `Shell` su tutte le pagine che desiderano abilitare l'ombreggiatura della barra di spostamento. Ad esempio, il codice XAML seguente mostra [`ContentPage`](xref:Xamarin.Forms.ContentPage)l'abilitazione dell'ombreggiatura della barra di spostamento da un oggetto :

```xaml
<ContentPage ...
             Shell.NavBarHasShadow="true">
    ...
</ContentPage>
```

In questo modo l'ombreggiatura della barra di navigazione viene abilitata.

## <a name="disable-the-navigation-bar"></a>Disabilitare la barra di spostamento

La classe `Shell` definisce la proprietà associata `NavBarIsVisible`, di tipo `bool`, che controlla se la barra di spostamento è visibile quando viene visualizzata una pagina. Il valore predefinito della proprietà è `true`.

Anche se questa proprietà può essere impostata per un oggetto `Shell` sottoclassato, viene in genere impostata per qualsiasi pagina in cui si vuole rendere invisibile la barra di spostamento. Ad esempio, il codice XAML seguente mostra [`ContentPage`](xref:Xamarin.Forms.ContentPage)la disabilitazione della barra di spostamento da un oggetto :

```xaml
<ContentPage ...
             Shell.NavBarIsVisible="false">
    ...
</ContentPage>
```

La barra di spostamento diventa così invisibile quando viene visualizzata la pagina:

![Screenshot della pagina Shell con una barra di spostamento invisibile, su iOS e Android](configuration-images/navigationbar-invisible.png "Pagina della shell con barra di spostamento invisibile")

## <a name="disable-the-tab-bar"></a>Disabilitare la barra di schede

La classe `Shell` definisce la proprietà associata `TabBarIsVisible`, di tipo `bool`, che controlla se la barra delle schede è visibile quando viene visualizzata una pagina. Il valore predefinito della proprietà è `true`.

Anche se questa proprietà può essere impostata per un oggetto `Shell` sottoclassato, viene in genere impostata per qualsiasi pagina in cui si vuole rendere invisibile la barra delle schede. Ad esempio, il codice XAML seguente mostra [`ContentPage`](xref:Xamarin.Forms.ContentPage)di disabilitare la barra delle schede da un oggetto :

```xaml
<ContentPage ...
             Shell.TabBarIsVisible="false">
    ...
</ContentPage>
```

La barra delle schede diventa così invisibile quando viene visualizzata la pagina:

![Screenshot della pagina Shell con una barra delle schede invisibile, in iOS e Android](configuration-images/tabbar-invisible.png "Pagina della shell con barra delle schede invisibile")

## <a name="display-views-in-the-navigation-bar"></a>Visualizzare viste nella barra di spostamento

La `Shell` classe definisce la `TitleView` proprietà `View`associata , di tipo , [`View`](xref:Xamarin.Forms.View) che consente la visualizzazione di qualsiasi xamarin.Forms nella barra di spostamento.

Anche se questa proprietà può essere impostata per un oggetto `Shell` sottoclassato, è possibile impostarla anche per qualsiasi pagina in cui si vuole visualizzare una vista nella barra di spostamento. Ad esempio, il codice XAML [`Image`](xref:Xamarin.Forms.Image) seguente mostra la [`ContentPage`](xref:Xamarin.Forms.ContentPage)visualizzazione di un nella barra di spostamento di un oggetto :

```xaml
<ContentPage ...>
    <Shell.TitleView>
        <Image Source="xamarin_logo.png"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Shell.TitleView>
    ...
</ContentPage>
```

Il risultato è la visualizzazione di un'immagine nella barra di spostamento nella pagina:

![Screenshot della pagina Shell con una visualizzazione titolo, in iOS e Android](configuration-images/titleview.png "Pagina Shell con una visualizzazione titolo")

> [!IMPORTANT]
> Se la barra di spostamento è stata resa invisibile, con la proprietà associata `NavBarIsVisible`, la vista del titolo non verrà visualizzata.

Molte visualizzazioni non verranno visualizzate nella barra di spostamento [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) a [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) meno che le dimensioni della visualizzazione [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) non siano specificate con le proprietà e o la posizione della visualizzazione con le proprietà e .

Poiché [`Layout`](xref:Xamarin.Forms.Layout) la classe [`View`](xref:Xamarin.Forms.View) deriva dalla `TitleView` classe , la proprietà associata può essere impostata per visualizzare una classe di layout che contiene più visualizzazioni. Analogamente, [`ContentView`](xref:Xamarin.Forms.ContentView) poiché la classe [`View`](xref:Xamarin.Forms.View) deriva in `TitleView` ultima analisi dalla classe, `ContentView` la proprietà associata può essere impostata per visualizzare un che contiene una singola visualizzazione.

## <a name="page-visibility"></a>Visibilità della pagina

Shell rispetta la visibilità [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) della pagina, impostata con la proprietà . Pertanto, quando la `IsVisible` proprietà di `false` una pagina è impostata su di essa non sarà visibile nell'applicazione Shell e non sarà possibile accedervi.

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Applicazione di stili alle app Xamarin.Forms con gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Xamarin.Forms CSS Shell specific properties](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties) (Proprietà specifiche della shell CSS Xamarin.Forms)
