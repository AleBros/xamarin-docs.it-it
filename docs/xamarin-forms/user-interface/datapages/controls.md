---
title: Riferimento ai controlli DataPages
description: Questo articolo presenta i controlli disponibili nel Xamarin.Forms pacchetto NuGet di DataPages.
ms.prod: xamarin
ms.assetid: 891615D0-E8BD-4ACC-A7F0-4C3725FBCC31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 86b526fff305b195221aca3fb6a86ad0823cb145
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84569400"
---
# <a name="datapages-controls-reference"></a>Riferimento ai controlli DataPages

![](~/media/shared/preview.png "This API is currently in preview")

> [!IMPORTANT]
> Per il rendering delle pagine Web è necessario un Xamarin.Forms riferimento al tema. Questa operazione comporta l'installazione di [ Xamarin.Forms . Tema.](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) pacchetto NuGet di base nel progetto, seguito da [ Xamarin.Forms . Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) o [ Xamarin.Forms . Pacchetti NuGet Theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

Il Xamarin.Forms NuGet DataPages include una serie di controlli che possono sfruttare l'associazione dell'origine dati.

Per usare questi controlli in XAML, verificare che lo spazio dei nomi sia stato incluso, ad esempio, vedere la `xmlns:pages` dichiarazione seguente:

```xaml
<ContentPage
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:pages="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
    x:Class="DataPagesDemo.Detail">
```

Gli esempi seguenti includono `DynamicResource` riferimenti che dovrebbero esistere nel dizionario delle risorse del progetto per funzionare. È inoltre disponibile un esempio di come compilare un [controllo personalizzato](#custom-control-example).

## <a name="built-in-controls"></a>Controlli predefiniti

* [HeroImage](#heroimage)
* [ListItem](#listitem)

### <a name="heroimage"></a>HeroImage

Il `HeroImage` controllo dispone di quattro proprietà:

* Text
* Dettaglio
* ImageSource
* Aspetto

```xaml
<pages:HeroImage
    ImageSource="{ DynamicResource HeroImageImage }"
    Text="Keith Ballinger"
    Detail="Xamarin"
/>
```

**Android**

![](controls-images/heroimage-light-android.png "Controllo HeroImage in Android") ![](controls-images/heroimage-dark-android.png "Controllo HeroImage in Android")

**iOS**

![](controls-images/heroimage-light-ios.png "Controllo HeroImage in iOS") ![](controls-images/heroimage-dark-ios.png "Controllo HeroImage in iOS")

### <a name="listitem"></a>ListItem

Il `ListItem` layout del controllo è simile a quello delle righe della tabella o dell'elenco iOS e Android nativo, ma può anche essere usato come visualizzazione normale. Nel codice di esempio riportato di seguito viene visualizzato ospitato all'interno di un `StackLayout` , ma può anche essere usato in controlli elenco scolling associati a dati.

Sono disponibili cinque proprietà:

* Titolo
* Dettaglio
* ImageSource
* PlaceholdImageSource
* Aspetto

```xaml
<StackLayout Spacing="0">
    <pages:ListItemControl
        Detail="Xamarin"
        ImageSource="{ DynamicResource UserImage }"
        Title="Miguel de Icaza"
        PlaceholdImageSource="{ DynamicResource IconImage }"
    />
```

Queste schermate mostrano le `ListItem` piattaforme iOS e Android con i temi chiaro e scuro:

**Android**

![](controls-images/listitem-light-android.png "Controllo ListItem in Android") ![](controls-images/listitem-dark-android.png "Controllo ListItem in Android")

**iOS**

![](controls-images/listitem-light-ios.png "Controllo ListItem in iOS") ![](controls-images/listitem-dark-ios.png "Controllo ListItem in iOS")

## <a name="custom-control-example"></a>Esempio di controllo personalizzato

L'obiettivo di questo `CardView` controllo personalizzato è simile al CardView Android nativo.

Conterrà tre proprietà:

* Text
* Dettaglio
* ImageSource

L'obiettivo è un controllo personalizzato che apparirà come il codice seguente (si noti che `xmlns:local` è necessario un oggetto personalizzato che fa riferimento all'assembly corrente):

```xaml
<local:CardView
  ImageSource="{ DynamicResource CardViewImage }"
  Text="CardView Text"
  Detail="CardView Detail"
/>
```

Dovrebbe apparire come gli screenshot seguenti usando i colori che corrispondono ai temi predefiniti chiaro e scuro:

**Android**

![](controls-images/cardview-light-android.png "Controllo personalizzato CardView in Android") ![](controls-images/cardview-dark-android.png "Controllo personalizzato CardView in Android")

**iOS**

![](controls-images/cardview-light-ios.png "Controllo personalizzato CardView in iOS") ![](controls-images/cardview-dark-ios.png "Controllo personalizzato CardView in iOS")

### <a name="building-the-custom-cardview"></a>Creazione del CardView personalizzato

1. [Sottoclasse DataView](#1-dataview-subclass)
2. [Definire il tipo di carattere, il layout e i margini](#2-define-font-layout-and-margins)
3. [Crea stili per gli elementi figlio del controllo](#3-create-styles-for-the-controls-children)
4. [Creazione del modello di layout del controllo](#4-create-the-control-layout-template)
5. [Aggiungere le risorse specifiche del tema](#5-add-the-theme-specific-resources)
6. [Impostare ControlTemplate per la classe CardView](#6-set-the-controltemplate-for-the-cardview-class)
7. [Aggiungere il controllo a una pagina](#7-add-the-control-to-a-page)

#### <a name="1-dataview-subclass"></a>1. sottoclasse DataView

La sottoclasse C# di `DataView` definisce le proprietà associabili per il controllo.

```csharp
public class CardView : DataView
{
    public static readonly BindableProperty TextProperty =
        BindableProperty.Create ("Text", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Text
    {
        get { return (string)GetValue (TextProperty); }
        set { SetValue (TextProperty, value); }
    }

    public static readonly BindableProperty DetailProperty =
        BindableProperty.Create ("Detail", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Detail
    {
        get { return (string)GetValue (DetailProperty); }
        set { SetValue (DetailProperty, value); }
    }

    public static readonly BindableProperty ImageSourceProperty =
        BindableProperty.Create ("ImageSource", typeof (ImageSource), typeof (CardView), null, BindingMode.TwoWay);

    public ImageSource ImageSource
    {
        get { return (ImageSource)GetValue (ImageSourceProperty); }
        set { SetValue (ImageSourceProperty, value); }
    }

    public CardView()
    {
    }
}
```

#### <a name="2-define-font-layout-and-margins"></a>2. definire il tipo di carattere, il layout e i margini

Nella finestra di progettazione controlli questi valori verranno considerati come parte della progettazione dell'interfaccia utente per il controllo personalizzato. Se sono necessarie specifiche specifiche della piattaforma, `OnPlatform` viene usato l'elemento.

Si noti che alcuni valori fanno riferimento a `StaticResource` s, che verranno definiti nel [passaggio 5](#5-add-the-theme-specific-resources).

```xml
<!-- CARDVIEW FONT SIZES -->
<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewTextFontSize">
        <On Platform="iOS, Android" Value="15" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewDetailFontSize">
        <On Platform="iOS, Android" Value="13" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewTextTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewTextTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewTextTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewTextlMargin">
        <On Platform="iOS" Value="12,10,12,4" />
        <On Platform="Android" Value="20,0,20,5" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewDetailTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewDetailTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewDetailTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewDetailMargin">
        <On Platform="iOS" Value="12,0,10,12" />
        <On Platform="Android" Value="20,0,20,20" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewBackgroundColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewBackgroundColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewBackgroundColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewShadowSize">
        <On Platform="iOS" Value="2" />
        <On Platform="Android" Value="5" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewCornerRadius">
        <On Platform="iOS" Value="0" />
        <On Platform="Android" Value="4" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewShadowColor">
        <On Platform="iOS, Android" Value="#CDCDD1" />
</OnPlatform>
```

#### <a name="3-create-styles-for-the-controls-children"></a>3. creare stili per gli elementi figlio del controllo

Fare riferimento a tutti gli elementi definiti in per creare gli elementi figlio che verranno utilizzati nel controllo personalizzato:

```xml
<!-- EXPLICIT STYLES (will be Classes) -->
<Style TargetType="Label" x:Key="CardViewTextStyle">
    <Setter Property="FontSize" Value="{ StaticResource CardViewTextFontSize }" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewTextTextColor }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewTextlMargin }" />
    <Setter Property="HorizontalTextAlignment" Value="Start" />
</Style>

<Style TargetType="Label" x:Key="CardViewDetailStyle">
    <Setter Property="HorizontalTextAlignment" Value="Start" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewDetailTextColor }" />
    <Setter Property="FontSize" Value="{ StaticResource CardViewDetailFontSize }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewDetailMargin }" />
</Style>

<Style TargetType="Image" x:Key="CardViewImageImageStyle">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="Center" />
    <Setter Property="WidthRequest" Value="220"/>
    <Setter Property="HeightRequest" Value="165"/>
</Style>
```

#### <a name="4-create-the-control-layout-template"></a>4. creare il modello di layout del controllo

La progettazione visiva del controllo personalizzato viene dichiarata in modo esplicito nel modello di controllo, usando le risorse definite in precedenza:

```xml
<!--- CARDVIEW -->
<ControlTemplate x:Key="CardViewControlControlTemplate">
  <StackLayout
    Spacing="0"
    BackgroundColor="{ TemplateBinding BackgroundColor }"
  >

    <!-- CARDVIEW IMAGE -->
    <Image
      Source="{ TemplateBinding ImageSource }"
      HorizontalOptions="FillAndExpand"
      VerticalOptions="StartAndExpand"
      Aspect="AspectFill"
      Style="{ StaticResource CardViewImageImageStyle }"
    />

    <!-- CARDVIEW TEXT -->
    <Label
      Text="{ TemplateBinding Text }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewTextStyle }"
    />

    <!-- CARDVIEW DETAIL -->
    <Label
      Text="{ TemplateBinding Detail }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewDetailStyle }" />

  </StackLayout>

</ControlTemplate>
```

#### <a name="5-add-the-theme-specific-resources"></a>5. aggiungere le risorse specifiche del tema

Poiché si tratta di un controllo personalizzato, aggiungere le risorse che corrispondono al tema usato nel dizionario risorse:

##### <a name="light-theme-colors"></a>Colori tema chiaro

```xaml
<Color x:Key="iOSCardViewBackgroundColor">#FFFFFF</Color>
<Color x:Key="AndroidCardViewBackgroundColor">#FFFFFF</Color>

<Color x:Key="AndroidCardViewTextTextColor">#030303</Color>
<Color x:Key="iOSCardViewTextTextColor">#030303</Color>

<Color x:Key="AndroidCardViewDetailTextColor">#8F8E94</Color>
<Color x:Key="iOSCardViewDetailTextColor">#8F8E94</Color>
```

##### <a name="dark-theme-colors"></a>Colori del tema scuro

```xaml
<!-- CARD VIEW COLORS -->
            <Color x:Key="iOSCardViewBackgroundColor">#404040</Color>
            <Color x:Key="AndroidCardViewBackgroundColor">#404040</Color>

            <Color x:Key="AndroidCardViewTextTextColor">#FFFFFF</Color>
            <Color x:Key="iOSCardViewTextTextColor">#FFFFFF</Color>

            <Color x:Key="AndroidCardViewDetailTextColor">#B5B4B9</Color>
            <Color x:Key="iOSCardViewDetailTextColor">#B5B4B9</Color>
```

#### <a name="6-set-the-controltemplate-for-the-cardview-class"></a>6. impostare ControlTemplate per la classe CardView

Assicurarsi infine che la classe C# creata nel [passaggio 1](#1-dataview-subclass) usi il modello di controllo definito nel [passaggio 4](#4-create-the-control-layout-template) usando un `Style` `Setter` elemento

```xml
<Style TargetType="local:CardView">
    <Setter Property="ControlTemplate" Value="{ StaticResource CardViewControlControlTemplate }" />
  ... some custom styling omitted
  <Setter Property="BackgroundColor" Value="{ StaticResource CardViewBackgroundColor }" />
</Style>
```

#### <a name="7-add-the-control-to-a-page"></a>7. aggiungere il controllo a una pagina

Il `CardView` controllo può ora essere aggiunto a una pagina. L'esempio seguente mostra che è ospitato in un `StackLayout` :

```xaml
<StackLayout Spacing="0">
  <local:CardView
    Margin="12,6"
    ImageSource="{ DynamicResource CardViewImage }"
    Text="CardView Text"
    Detail="CardView Detail"
  />
</StackLayout>

```
