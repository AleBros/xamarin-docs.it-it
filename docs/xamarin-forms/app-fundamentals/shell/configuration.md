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
ms.sourcegitcommit: 2b6ef0147b9618dcab1c779523d7b6eeee2dcfd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78238561"
---
# <a name="xamarinforms-shell-page-configuration"></a>Configurazione delle pagine per Xamarin.Forms Shell

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

La classe `Shell` definisce le proprietà associate che possono essere usate per configurare l'aspetto delle pagine nelle applicazioni shell Xamarin.Forms. Ciò include l'impostazione dei colori della pagina, l'impostazione della modalità di presentazione della pagina, la disabilitazione della barra di spostamento, la disabilitazione della barra delle schede e la visualizzazione delle visualizzazioni nella barra di spostamento.

## <a name="set-page-colors"></a>Impostare i colori delle pagine

La classe `Shell` definisce le proprietà associate seguenti, che possono essere usate per impostare i colori delle pagine in un'applicazione shell:

- `BackgroundColor`, di tipo `Color`, che definisce il colore di sfondo nel riquadro della Shell. Il colore non verrà inserito dietro il contenuto della shell.
- `DisabledColor`, di tipo `Color`, che definisce il colore per l'ombreggiatura di icone e testo disabilitati.
- `ForegroundColor`, di tipo `Color`, che definisce il colore per l'ombreggiatura di icone e testo.
- `TitleColor`, di tipo `Color`, che definisce il colore usato per il titolo della pagina corrente.
- `UnselectedColor`, di tipo `Color`, che definisce il colore per le icone e il testo non selezionati nel riquadro della shell.

Tutte queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che le proprietà possono essere destinazioni di data binding e con stili XAML. Queste proprietà possono essere impostate anche usando fogli di stile CSS (Cascading Style Sheet). Per altre informazioni, vedere [Xamarin.Forms Shell specific properties](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties) (Proprietà specifiche della shell Xamarin.Forms).

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

## <a name="set-page-presentation-mode"></a>Imposta modalità presentazione pagina

Per impostazione predefinita, si verifica un'animazione di spostamento ridotta quando si passa a una pagina con il metodo `GoToAsync`. Tuttavia, questo comportamento può essere modificato impostando la proprietà associata `Shell.PresentationMode` su una [`ContentPage`](xref:Xamarin.Forms.ContentPage) su uno dei membri dell'enumerazione `PresentationMode`:

- `NotAnimated` indica che la pagina verrà visualizzata senza un'animazione di navigazione.
- `Animated` indica che la pagina verrà visualizzata con un'animazione di navigazione. Si tratta del valore predefinito del `Shell.PresentationMode` proprietà associata.
- `Modal` indica che la pagina verrà visualizzata come pagina modale.
- `ModalAnimated` indica che la pagina verrà visualizzata come pagina modale con un'animazione di navigazione.
- `ModalNotAnimated` indica che la pagina verrà visualizzata come pagina modale, senza un'animazione di navigazione.

> [!IMPORTANT]
> Il tipo di `PresentationMode` è un'enumerazione Flags. Ciò significa che è possibile applicare una combinazione di membri di enumerazione nel codice. Tuttavia, per semplificare l'utilizzo in XAML, il `ModalAnimated` membro è una combinazione dei membri `Animated` e `Modal` e il membro `ModalNotAnimated` è una combinazione dei membri `NotAnimated` e `Modal`. Per altre informazioni sulle enumerazioni dei flag, vedere [tipi di enumerazione come flag di bit](/dotnet/csharp/language-reference/builtin-types/enum#enumeration-types-as-bit-flags).

Nell'esempio di codice XAML riportato di seguito viene impostata la proprietà associata `Shell.PresentationMode` in un [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.PresentationMode="Modal">
    ...             
</ContentPage>
```

In questo esempio, la [`ContentPage`](xref:Xamarin.Forms.ContentPage) è impostata per essere visualizzata come pagina modale, quando la pagina viene spostata con il metodo `GoToAsync`.

## <a name="enable-navigation-bar-shadow"></a>Abilita ombreggiatura barra di navigazione

La classe `Shell` definisce la proprietà associata `NavBarHasShadow`, di tipo `bool`, che controlla se la barra di spostamento presenta un'ombreggiatura. Per impostazione predefinita, il valore della proprietà è `false` in iOS e `true` su Android.

Sebbene questa proprietà possa essere impostata su un oggetto `Shell` sottoclassato, può essere impostata anche in tutte le pagine che desiderano abilitare l'ombreggiatura della barra di navigazione. Ad esempio, il codice XAML seguente mostra come abilitare l'ombreggiatura della barra di navigazione da un [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.NavBarHasShadow="true">
    ...
</ContentPage>
```

Ciò comporta l'abilitazione dell'ombreggiatura della barra di navigazione.

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

![Screenshot della pagina della shell con una barra di spostamento invisibile, in iOS e Android](configuration-images/navigationbar-invisible.png "Pagina della shell con barra di navigazione invisibile")

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

![Screenshot della pagina della shell con una barra scheda invisibile, in iOS e Android](configuration-images/tabbar-invisible.png "Pagina della shell con barra scheda invisibile")

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

![Screenshot della pagina della shell con una visualizzazione del titolo, in iOS e Android](configuration-images/titleview.png "Pagina della shell con una visualizzazione del titolo")

> [!IMPORTANT]
> Se la barra di spostamento è stata resa invisibile, con la proprietà associata `NavBarIsVisible`, la vista del titolo non verrà visualizzata.

Molte viste non saranno più visualizzate nella barra di spostamento, a meno che non vengano specificate le dimensioni della vista con le proprietà [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) oppure a meno che non venga specificata la posizione della vista con le proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions).

Dato che la classe [`Layout`](xref:Xamarin.Forms.Layout) deriva dalla classe [`View`](xref:Xamarin.Forms.View), la proprietà associata `TitleView` può essere impostata per visualizzare una classe di layout contenente più viste. In modo analogo, dato che la classe [`ContentView`](xref:Xamarin.Forms.ContentView) deriva in fondo dalla classe [`View`](xref:Xamarin.Forms.View), la proprietà associata `TitleView` può essere impostata per visualizzare un elemento `ContentView` contenente una singola vista.

## <a name="page-visibility"></a>Visibilità della pagina

Shell rispetta la visibilità della pagina, impostata con la proprietà [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) . Pertanto, quando la proprietà `IsVisible` della pagina è impostata su `false` non sarà visibile nell'applicazione shell e non sarà possibile accedervi.

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Applicazione di stili alle app Xamarin.Forms con gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Xamarin.Forms CSS Shell specific properties](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties) (Proprietà specifiche della shell CSS Xamarin.Forms)
