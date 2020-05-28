---
title: Xamarin.FormsConfigurazione della pagina della shell
description: La classe shell definisce le proprietà associate che possono essere usate per configurare l'aspetto delle pagine nelle Xamarin.Forms applicazioni della shell. Ciò include l'impostazione dei colori delle pagine, la disabilitazione della barra di spostamento, la disabilitazione della barra delle schede e la visualizzazione delle viste nella barra di spostamento.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 102070fc478b42e9fbc0c7d0006197c81a49c9b8
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137501"
---
# <a name="xamarinforms-shell-page-configuration"></a>Xamarin.FormsConfigurazione della pagina della shell

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

La `Shell` classe definisce le proprietà associate che possono essere usate per configurare l'aspetto delle pagine nelle Xamarin.Forms applicazioni della shell. Ciò include l'impostazione dei colori della pagina, l'impostazione della modalità di presentazione della pagina, la disabilitazione della barra di spostamento, la disabilitazione della barra delle schede e la visualizzazione delle visualizzazioni nella barra di spostamento.

## <a name="set-page-colors"></a>Impostare i colori delle pagine

La classe `Shell` definisce le proprietà associate seguenti, che possono essere usate per impostare i colori delle pagine in un'applicazione shell:

- `BackgroundColor`, di tipo `Color`, che definisce il colore di sfondo nel riquadro della Shell. Il colore non verrà inserito dietro il contenuto della shell.
- `DisabledColor`, di tipo `Color`, che definisce il colore per l'ombreggiatura di icone e testo disabilitati.
- `ForegroundColor`, di tipo `Color`, che definisce il colore per l'ombreggiatura di icone e testo.
- `TitleColor`, di tipo `Color`, che definisce il colore usato per il titolo della pagina corrente.
- `UnselectedColor`, di tipo `Color`, che definisce il colore per le icone e il testo non selezionati nel riquadro della shell.

Tutte queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati e hanno lo stile usando gli stili XAML. Queste proprietà possono essere impostate anche usando fogli di stile CSS (Cascading Style Sheet). Per altre informazioni, vedere [ Xamarin.Forms proprietà specifiche della shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

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

Per altre informazioni sugli stili XAML, vedere [applicazione di stili Xamarin.Forms con stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

## <a name="set-page-presentation-mode"></a>Imposta modalità presentazione pagina

Per impostazione predefinita, si verifica un'animazione di spostamento ridotta quando si passa a una pagina con il `GoToAsync` metodo. Tuttavia, questo comportamento può essere modificato impostando la `Shell.PresentationMode` proprietà associata su un oggetto su [`ContentPage`](xref:Xamarin.Forms.ContentPage) uno dei `PresentationMode` membri dell'enumerazione:

- `NotAnimated`indica che la pagina verrà visualizzata senza un'animazione di navigazione.
- `Animated`indica che la pagina verrà visualizzata con un'animazione di navigazione. Si tratta del valore predefinito della `Shell.PresentationMode` proprietà associata.
- `Modal`indica che la pagina verrà visualizzata come pagina modale.
- `ModalAnimated`indica che la pagina verrà visualizzata come pagina modale con un'animazione di navigazione.
- `ModalNotAnimated`indica che la pagina verrà visualizzata come pagina modale, senza un'animazione di navigazione.

> [!IMPORTANT]
> Il `PresentationMode` tipo è un'enumerazione Flags. Ciò significa che è possibile applicare una combinazione di membri di enumerazione nel codice. Tuttavia, per semplificare l'utilizzo in XAML, il `ModalAnimated` membro è una combinazione dei `Animated` membri e `Modal` e il `ModalNotAnimated` membro è una combinazione dei `NotAnimated` `Modal` membri e. Per altre informazioni sulle enumerazioni dei flag, vedere [tipi di enumerazione come flag di bit](/dotnet/csharp/language-reference/builtin-types/enum#enumeration-types-as-bit-flags).

Nell'esempio di codice XAML riportato di seguito viene impostata la `Shell.PresentationMode` proprietà associata in un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<ContentPage ...
             Shell.PresentationMode="Modal">
    ...             
</ContentPage>
```

In questo esempio, l'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) viene impostato per essere visualizzato come pagina modale, quando la pagina viene spostata con il `GoToAsync` metodo.

## <a name="enable-navigation-bar-shadow"></a>Abilitare l'ombreggiatura della barra di navigazione

La `Shell` classe definisce la `NavBarHasShadow` proprietà associata, di tipo `bool` , che controlla se la barra di spostamento presenta un'ombreggiatura. Per impostazione predefinita, il valore della proprietà è `false` in iOS e `true` in Android.

Sebbene questa proprietà possa essere impostata su un oggetto sottoclassato `Shell` , può essere impostata anche in tutte le pagine che desiderano abilitare l'ombreggiatura della barra di navigazione. Ad esempio, il codice XAML seguente mostra come abilitare l'ombreggiatura della barra di navigazione da un [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<ContentPage ...
             Shell.NavBarHasShadow="true">
    ...
</ContentPage>
```

Ciò comporta l'abilitazione dell'ombreggiatura della barra di navigazione.

## <a name="disable-the-navigation-bar"></a>Disabilitare la barra di spostamento

La classe `Shell` definisce la proprietà associata `NavBarIsVisible`, di tipo `bool`, che controlla se la barra di spostamento è visibile quando viene visualizzata una pagina. Il valore predefinito della proprietà è `true`.

Anche se questa proprietà può essere impostata per un oggetto `Shell` sottoclassato, viene in genere impostata per qualsiasi pagina in cui si vuole rendere invisibile la barra di spostamento. Il codice XAML seguente, ad esempio, Mostra la disabilitazione della barra di spostamento da un [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

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

Anche se questa proprietà può essere impostata per un oggetto `Shell` sottoclassato, viene in genere impostata per qualsiasi pagina in cui si vuole rendere invisibile la barra delle schede. Il codice XAML seguente, ad esempio, Mostra la disabilitazione della barra scheda da un [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<ContentPage ...
             Shell.TabBarIsVisible="false">
    ...
</ContentPage>
```

La barra delle schede diventa così invisibile quando viene visualizzata la pagina:

![Screenshot della pagina della shell con una barra scheda invisibile, in iOS e Android](configuration-images/tabbar-invisible.png "Pagina della shell con barra scheda invisibile")

## <a name="display-views-in-the-navigation-bar"></a>Visualizzare viste nella barra di spostamento

La `Shell` classe definisce la `TitleView` proprietà associata, di tipo `View` , che consente la visualizzazione di qualsiasi Xamarin.Forms [`View`](xref:Xamarin.Forms.View) nella barra di navigazione.

Anche se questa proprietà può essere impostata per un oggetto `Shell` sottoclassato, è possibile impostarla anche per qualsiasi pagina in cui si vuole visualizzare una vista nella barra di spostamento. Il codice XAML seguente, ad esempio, Mostra la visualizzazione di un oggetto [`Image`](xref:Xamarin.Forms.Image) nella barra di navigazione di un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

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

Molte visualizzazioni non verranno visualizzate nella barra di navigazione, a meno che le dimensioni della vista non siano specificate con le [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) proprietà e oppure se la posizione della vista è specificata con le [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà e.

Poiché la [`Layout`](xref:Xamarin.Forms.Layout) classe deriva dalla [`View`](xref:Xamarin.Forms.View) classe, la `TitleView` proprietà associata può essere impostata in modo da visualizzare una classe layout contenente più visualizzazioni. Analogamente, poiché la [`ContentView`](xref:Xamarin.Forms.ContentView) classe deriva alla fine dalla [`View`](xref:Xamarin.Forms.View) classe, la `TitleView` proprietà associata può essere impostata in modo da visualizzare un oggetto `ContentView` che contiene una singola visualizzazione.

## <a name="page-visibility"></a>Visibilità della pagina

Shell rispetta la visibilità della pagina, impostata con la [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) Proprietà. Pertanto, quando la proprietà di una pagina `IsVisible` è impostata su `false` , non sarà visibile nell'applicazione shell e non sarà possibile accedervi.

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.FormsApplicazione di stili alle app usando gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Xamarin.FormsProprietà specifiche della shell CSS](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
