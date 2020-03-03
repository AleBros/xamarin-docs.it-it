---
title: Configurazione delle pagine per Xamarin.Forms Shell
description: La classe Shell definisce le proprietà associate che possono essere usate per configurare l'aspetto delle pagine nelle applicazioni shell Xamarin.Forms. Ciò include l'impostazione dei colori delle pagine, la disabilitazione della barra di spostamento, la disabilitazione della barra delle schede e la visualizzazione delle viste nella barra di spostamento.
ms.prod: xamarin
ms.assetid: 3FC2FBD1-C30B-4408-97B2-B04E3A2E4F03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2020
ms.openlocfilehash: dfa452addd7cfb838091afdfb350484998d0cc9d
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77636087"
---
# <a name="xamarinforms-shell-page-configuration"></a>Configurazione delle pagine per Xamarin.Forms Shell

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

La classe `Shell` definisce le proprietà associate che possono essere usate per configurare l'aspetto delle pagine nelle applicazioni shell Xamarin.Forms. Ciò include l'impostazione dei colori delle pagine, l'impostazione della modalità di presentazione della pagina, la disabilitazione della barra di spostamento, la disabilitazione della barra delle schede e la visualizzazione delle viste nella barra di spostamento.

## <a name="set-page-colors"></a>Impostare i colori delle pagine

La classe `Shell` definisce le proprietà associate seguenti, che possono essere usate per impostare i colori delle pagine in un'applicazione shell:

- `BackgroundColor`, di tipo `Color`, che definisce il colore di sfondo nel riquadro della Shell. Il colore non verrà inserito dietro il contenuto della shell.
- `DisabledColor`, di tipo `Color`, che definisce il colore per l'ombreggiatura di icone e testo disabilitati.
- `ForegroundColor`, di tipo `Color`, che definisce il colore per l'ombreggiatura di icone e testo.
- `TitleColor`, di tipo `Color`, che definisce il colore usato per il titolo della pagina corrente.
- `UnselectedColor`, di tipo `Color`, che definisce il colore per le icone e il testo non selezionati nel riquadro della shell.

Poiché tutte queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), le proprietà possono essere destinazioni di data binding ed è possibile applicarvi stili XAML. Queste proprietà possono essere impostate anche usando fogli di stile CSS (Cascading Style Sheet). Per altre informazioni, vedere [Xamarin.Forms Shell specific properties](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties) (Proprietà specifiche della shell Xamarin.Forms).

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

Per impostazione predefinita, quando si passa a una pagina tramite il metodo `GoToAsync`, viene visualizzata una piccola animazione dello spostamento. Questo comportamento può essere tuttavia modificato impostando la proprietà associata `Shell.PresentationMode` in [`ContentPage`](xref:Xamarin.Forms.ContentPage) su uno dei membri dell'enumerazione `PresentationMode`:

- `NotAnimated` indica che la pagina verrà visualizzata senza un'animazione dello spostamento.
- `Animated` indica che la pagina verrà visualizzata con un'animazione dello spostamento. Questo è il valore predefinito della proprietà associata `Shell.PresentationMode`.
- `Modal` indica che la pagina verrà visualizzata come pagina modale.
- `ModalAnimated` indica che la pagina verrà visualizzata come pagina modale con un'animazione dello spostamento.
- `ModalNotAnimated` indica che la pagina verrà visualizzata come pagina modale senza un'animazione dello spostamento.

> [!IMPORTANT]
> Il tipo `PresentationMode` è un'enumerazione Flags. Ciò significa che è possibile applicare una combinazione di membri di enumerazione nel codice. Tuttavia, per semplificare l'utilizzo in XAML, il membro `ModalAnimated` è una combinazione dei membri `Animated` e `Modal` e il membro `ModalNotAnimated` è una combinazione dei membri `NotAnimated` e `Modal`. Per altre informazioni sulle enumerazioni flag, vedere [Tipi di enumerazione come flag di bit](/dotnet/csharp/language-reference/builtin-types/enum#enumeration-types-as-bit-flags).

L'esempio XAML seguente imposta la proprietà associata `Shell.PresentationMode` su [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.PresentationMode="Modal">
    ...             
</ContentPage>
```

In questo esempio [`ContentPage`](xref:Xamarin.Forms.ContentPage) è impostato per essere visualizzato come pagina modale, quando viene eseguito il passaggio alla pagina tramite il metodo `GoToAsync`.

## <a name="enable-navigation-bar-shadow"></a>Abilitare l'ombreggiatura della barra di spostamento

La classe `Shell` definisce la proprietà associata `NavBarHasShadow`, di tipo `bool`, che controlla se la barra di spostamento ha un'ombreggiatura. Il valore predefinito della proprietà è `false`.

Sebbene questa proprietà possa essere impostata in un oggetto `Shell` in una sottoclasse, è possibile impostarla anche in qualsiasi pagina in cui si vuole abilitare l'ombreggiatura della barra di spostamento. Ad esempio, il codice XAML seguente illustra l'abilitazione dell'ombreggiatura della barra di spostamento da [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.NavBarHasShadow="true">
    ...
</ContentPage>
```

Ciò comporta l'abilitazione dell'ombreggiatura della barra di spostamento.

## <a name="disable-the-navigation-bar"></a>Disabilitare la barra di spostamento

La classe `Shell` definisce la proprietà associata `NavBarIsVisible`, di tipo `bool`, che controlla se la barra di spostamento è visibile quando viene visualizzata una pagina. Il valore predefinito della proprietà è `true`.

Anche se questa proprietà può essere impostata per un oggetto `Shell` sottoclassato, viene in genere impostata per qualsiasi pagina in cui si vuole rendere invisibile la barra di spostamento. Ad esempio, il codice XAML illustra la disabilitazione della barra di spostamento da [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.NavBarIsVisible="false">
    ...
</ContentPage>
```

La barra di spostamento diventa così invisibile quando viene visualizzata la pagina:

![Screenshot della pagina della shell con una barra di spostamento invisibile in iOS e Android](configuration-images/navigationbar-invisible.png "Pagina della shell con barra di spostamento invisibile")

## <a name="disable-the-tab-bar"></a>Disabilitare la barra di schede

La classe `Shell` definisce la proprietà associata `TabBarIsVisible`, di tipo `bool`, che controlla se la barra delle schede è visibile quando viene visualizzata una pagina. Il valore predefinito della proprietà è `true`.

Anche se questa proprietà può essere impostata per un oggetto `Shell` sottoclassato, viene in genere impostata per qualsiasi pagina in cui si vuole rendere invisibile la barra delle schede. Ad esempio, il codice XAML seguente illustra la disabilitazione della barra delle schede da [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.TabBarIsVisible="false">
    ...
</ContentPage>
```

La barra delle schede diventa così invisibile quando viene visualizzata la pagina:

![Screenshot della pagina della shell con una barra di spostamento invisibile in iOS e Android](configuration-images/tabbar-invisible.png "Pagina della shell con barra delle schede invisibile")

## <a name="display-views-in-the-navigation-bar"></a>Visualizzare viste nella barra di spostamento

La classe `Shell` definisce la proprietà associata `TitleView`, di tipo `View`, che consente la visualizzazione di qualsiasi elemento [`View`](xref:Xamarin.Forms.View) Xamarin.Forms nella barra di spostamento.

Anche se questa proprietà può essere impostata per un oggetto `Shell` sottoclassato, è possibile impostarla anche per qualsiasi pagina in cui si vuole visualizzare una vista nella barra di spostamento. Ad esempio, il codice XAML seguente illustra la visualizzazione di un elemento [`Image`](xref:Xamarin.Forms.Image) nella barra di spostamento di [`ContentPage`](xref:Xamarin.Forms.ContentPage):

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

![Screenshot della pagina della shell con una vista del titolo in iOS e Android](configuration-images/titleview.png "Pagina della shell con vista del titolo")

> [!IMPORTANT]
> Se la barra di spostamento è stata resa invisibile, con la proprietà associata `NavBarIsVisible`, la vista del titolo non verrà visualizzata.

Molte viste non saranno più visualizzate nella barra di spostamento, a meno che non vengano specificate le dimensioni della vista con le proprietà [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) oppure a meno che non venga specificata la posizione della vista con le proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions).

Dato che la classe [`Layout`](xref:Xamarin.Forms.Layout) deriva dalla classe [`View`](xref:Xamarin.Forms.View), la proprietà associata `TitleView` può essere impostata per visualizzare una classe di layout contenente più viste. In modo analogo, dato che la classe [`ContentView`](xref:Xamarin.Forms.ContentView) deriva in fondo dalla classe [`View`](xref:Xamarin.Forms.View), la proprietà associata `TitleView` può essere impostata per visualizzare un elemento `ContentView` contenente una singola vista.

## <a name="page-visibility"></a>Visibilità della pagina

La shell rispetta la visibilità della pagina, impostata con la proprietà [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible). Di conseguenza, quando la proprietà `IsVisible` di una pagina è impostata su `false`, la pagina non sarà visibile nell'applicazione shell e non sarà possibile accedervi.

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Applicazione di stili alle app Xamarin.Forms con gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Xamarin.Forms CSS Shell specific properties](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties) (Proprietà specifiche della shell CSS Xamarin.Forms)
