---
title: Riferimento ai controlli DataPages
ms.topic: article
ms.prod: xamarin
ms.assetid: 891615D0-E8BD-4ACC-A7F0-4C3725FBCC31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: fbbc7e8c93f8ed562381d9203060c862960f44b9
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="datapages-controls-reference"></a>Riferimento ai controlli DataPages

![](~/media/shared/preview.png "Questa API è attualmente in anteprima")

> [!IMPORTANT]
> DataPages richiede un [xamarin. Forms tema](~/xamarin-forms/user-interface/themes/index.md) riferimento per il rendering.


DataPages Nuget xamarin. Forms include un numero di controlli che possono sfruttare i vantaggi dell'associazione all'origine dati.

Per utilizzare questi controlli in XAML, verificare lo spazio dei nomi è stato incluso, ad esempio vedere il `xmlns:pages` dichiarazione riportata di seguito:

```xaml
<ContentPage
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:pages="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
    x:Class="DataPagesDemo.Detail">
```

Gli esempi seguenti includono `DynamicResource` riferimenti che sono necessario presente nel dizionario di risorse del progetto di lavoro. È inoltre disponibile un esempio di come compilare un [controllo personalizzato](#custom)

## <a name="built-in-controls"></a>Controlli predefiniti

* [HeroImage](#heroimage)
* [ListItem](#listitem)

<a name="heroimage" />

### <a name="heroimage"></a>HeroImage

Il `HeroImage` controllo dispone di quattro proprietà:

* Testo
* Dettagli
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

![](controls-images/heroimage-light-android.png "Controllo HeroImage in Android") ![ ] (controls-images/heroimage-dark-android.png "controllo HeroImage in Android")

**iOS**

![](controls-images/heroimage-light-ios.png "Controllo HeroImage in iOS") ![ ] (controls-images/heroimage-dark-ios.png "controllo HeroImage in iOS")


<a name="listitem" />

### <a name="listitem"></a>ListItem

Il `ListItem` layout del controllo è simile a native per iOS e Android elenco o una tabella di righe, tuttavia può essere utilizzato anche come una visualizzazione normale. Nell'esempio viene visualizzato ospitato all'interno di codice riportato di seguito è un `StackLayout`, ma può anche essere usato nei controlli list scolling associati a dati.

Sono presenti cinque proprietà:

* Titolo
* Dettagli
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

Questi screenshot mostrano il `ListItem` su piattaforme iOS e Android utilizzando sia il chiaro e scuro temi:

**Android**

![](controls-images/listitem-light-android.png "Controllo ListItem in Android") ![ ] (controls-images/listitem-dark-android.png "controllo ListItem in Android")

**iOS**

![](controls-images/listitem-light-ios.png "Controllo ListItem in iOS") ![ ] (controls-images/listitem-dark-ios.png "controllo ListItem in iOS")


## <a name="custom-control-example"></a>Esempio di controllo personalizzato

L'obiettivo di questo personalizzato `CardView` controllo è simile a quella di CardView Android nativo.

Contiene tre proprietà:

* Testo
* Dettagli
* ImageSource

L'obiettivo è un controllo personalizzato che avrà un aspetto simile al codice riportato di seguito (si noti che un oggetto personalizzato `xmlns:local` è necessario che fa riferimento all'assembly corrente):

```xaml
<local:CardView
  ImageSource="{ DynamicResource CardViewImage }"
  Text="CardView Text"
  Detail="CardView Detail"
/>
```

Dovrebbe essere simile la schermata riportata di seguito utilizzando colori corrispondente per i temi chiaro e scuro incorporati:

**Android**

![](controls-images/cardview-light-android.png "Controllo personalizzato CardView in Android") ![ ] (controls-images/cardview-dark-android.png "controllo personalizzato CardView in Android")

**iOS**

![](controls-images/cardview-light-ios.png "Controllo personalizzato CardView in iOS") ![ ] (controls-images/cardview-dark-ios.png "controllo personalizzato CardView in iOS")

<a name="custom" />

### <a name="building-the-custom-cardview"></a>Compilazione di CardView personalizzato

1. [Sottoclasse DataView](#1)
2. [Definire i tipi di carattere, Layout e i margini](#2)
3. [Creare gli stili per gli elementi figlio del controllo](#3)
4. [Creare il modello di Layout di controllo](#4)
5. [Aggiungere le risorse specifiche del tema](#5)
6. [Impostare il ControlTemplate per la classe CardView](#6)
7. [Aggiungere il controllo a una pagina](#7)

<a name="1" />

#### <a name="1-dataview-subclass"></a>1. Sottoclasse DataView

La sottoclasse di c# `DataView` definisce le proprietà associabili per il controllo.

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

<a name="2" />

#### <a name="2-define-font-layout-and-margins"></a>2. Definire i tipi di carattere, Layout e i margini

La finestra di progettazione potrebbe individuare questi valori come parte della progettazione dell'interfaccia utente per il controllo personalizzato. In cui le specifiche di specifica della piattaforma sono necessari, il `OnPlatform` elemento viene usato.

Si noti che alcuni valori fanno riferimento a `StaticResource`s, questi verranno definiti in [passaggio 5](#5).

```xml
<!-- CARDVIEW FONT SIZES -->
<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewTextFontSize">
        <On Platform="iOS, Android" Value="15" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewDetailFontSize">
        <On Platform="iOS, Android" Value="13" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color" x:Key="CardViewTextTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewTextTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewTextTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness" x:Key="CardViewTextlMargin">
        <On Platform="iOS" Value="12,10,12,4" />
        <On Platform="Android" Value="20,0,20,5" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color" x:Key="CardViewDetailTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewDetailTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewDetailTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness" x:Key="CardViewDetailMargin">
        <On Platform="iOS" Value="12,0,10,12" />
        <On Platform="Android" Value="20,0,20,20" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color" x:Key="CardViewBackgroundColor">
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

<OnPlatform x:TypeArguments="Color" x:Key="CardViewShadowColor">
        <On Platform="iOS, Android" Value="#CDCDD1" />
</OnPlatform>
```

<a name="3" />

#### <a name="3-create-styles-for-the-controls-children"></a>3. Creare gli stili per gli elementi figlio del controllo

Fare riferimento a tutti gli elementi definiti per creare gli elementi figlio che verranno utilizzati nel controllo personalizzato:

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

<a name="4" />

#### <a name="4-create-the-control-layout-template"></a>4. Creare il modello di Layout di controllo

La progettazione visiva del controllo personalizzato è dichiarata in modo esplicito nel modello di controllo, usando le risorse definite in precedenza:

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

<a name="5" />

#### <a name="5-add-the-theme-specific-resources"></a>5. Aggiungere le risorse specifiche del tema

Poiché si tratta di un controllo personalizzato, aggiungere le risorse corrispondenti al tema del dizionario risorse in uso:

##### <a name="light-theme-colors"></a>Colori del tema chiaro

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

<a name="6" />

#### <a name="6-set-the-controltemplate-for-the-cardview-class"></a>6. Impostare il ControlTemplate per la classe CardView

Infine, verificare che la classe c# creata in [passaggio 1](#1) Usa il modello di controllo definito [passaggio 4](#4) utilizzando un `Style` `Setter` elemento

```xml
<Style TargetType="local:CardView">
    <Setter Property="ControlTemplate" Value="{ StaticResource CardViewControlControlTemplate }" />
  ... some custom styling omitted
  <Setter Property="BackgroundColor" Value="{ StaticResource CardViewBackgroundColor }" />
</Style>
```

<a name="7" />

#### <a name="7-add-the-control-to-a-page"></a>7. Aggiungere il controllo a una pagina

Il `CardView` controllo può ora essere aggiunto a una pagina. Nell'esempio seguente viene illustrato come viene ospitato in un `StackLayout`:

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
