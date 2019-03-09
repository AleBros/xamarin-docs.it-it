---
title: Tipi di carattere in xamarin. Forms
description: Questo articolo illustra come specificare informazioni sui caratteri nei controlli che visualizzano testo nelle applicazioni xamarin. Forms.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/04/2019
ms.openlocfilehash: 530fcf638454373ae68391e4e11bca85dd2fff63
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669557"
---
# <a name="fonts-in-xamarinforms"></a>Tipi di carattere in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)

Questo articolo descrive la modalità di xamarin. Forms che consente di specificare gli attributi dei caratteri (compresi peso e dimensioni) nei controlli che visualizzano testo. Informazioni di carattere possono essere [specificato nel codice](#Setting_Font_in_Code) oppure [specificato nel XAML](#Setting_Font_in_Xaml). Ha ' anche possibile usare una [tipo di carattere personalizzato](#Using_a_Custom_Font), e [visualizzare le icone del tipo di carattere](#display-font-icons).

<a name="Setting_Font_in_Code" />

## <a name="set-the-font-in-code"></a>Impostare il tipo di carattere nel codice

Usare le tre proprietà correlate al carattere di tutti i controlli che visualizzano testo:

- **FontFamily** &ndash; il `string` nome tipo di carattere.
- **FontSize** &ndash; le dimensioni del carattere come un `double`.
- **FontAttributes** &ndash; una stringa che specifica le informazioni sullo stile, ad esempio *corsivo* e **grassetto** (usando il `FontAttributes` enumerazione in c#).

Questo codice viene illustrato come creare un'etichetta e specificare le dimensioni del carattere e il peso da visualizzare:

```csharp
var about = new Label {
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>Dimensione carattere

Il `FontSize` proprietà può essere impostata su un valore double, ad esempio:

```csharp
label.FontSize = 24;
```

È anche possibile usare il `NamedSize` enumerazione che è disponibili quattro opzioni predefinite; Xamarin. Forms sceglie la dimensione ottimale per ogni piattaforma.

-  **Micro**
-  **Small**
-  **Medium**
-  **Large**

Il `NamedSize` enumerazione può essere utilizzato ovunque un `FontSize` può essere specificato usando la `Device.GetNamedSize` metodo per convertire il valore da un `double`:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

<a name="FontAttributes" />

### <a name="font-attributes"></a>Attributi dei caratteri

Tipo di carattere, ad esempio stili **grassetto** e *corsivo* può essere impostata sul `FontAttributes` proprietà. I valori seguenti sono attualmente supportati:

-  **None**
-  **Grassetto**
-  **Corsivo**

Il `FontAttribute` enumerazione può essere utilizzata come indicato di seguito (è possibile specificare un singolo attributo o `OR` uno a altro):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>Imposta informazioni di carattere per ogni piattaforma

In alternativa, il `Device.RuntimePlatform` proprietà può essere utilizzata per impostare i tipi di carattere diversi in ogni piattaforma, come illustrato nel codice seguente:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/Lobster-Regular.ttf#Lobster",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

È un'ottima fonte di informazioni sui caratteri per iOS [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="set-the-font-in-xaml"></a>Impostare il tipo di carattere in XAML

Xamarin. Forms consente di controllare che il testo visualizzato tutti hanno un `FontSize` proprietà che può essere impostata in XAML. Il modo più semplice per impostare il tipo di carattere in XAML è usare i valori di enumerazione denominato dimensioni, come illustrato in questo esempio:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

È un convertitore di tipi incorporati per il `FontSize` proprietà che consente tutte le impostazioni del tipo di carattere può essere espresso come valore stringa in XAML. Inoltre, il `FontAttributes` proprietà può essere utilizzata per specificare gli attributi del tipo di carattere:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-values) è anche utilizzabile in XAML per il rendering di un carattere diverso in ogni piattaforma. L'esempio seguente usa un tipo di carattere personalizzato in iOS (<span style="font-family:MarkerFelt-Thin">Thin MarkerFelt</span>) e specifica solo le dimensioni e attributi nelle altre piattaforme:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

Quando si specifica un tipo di carattere personalizzato, è sempre una buona idea utilizzare `OnPlatform`, perché è difficile trovare un tipo di carattere che è disponibile in tutte le piattaforme.

<a name="Using_a_Custom_Font" />

## <a name="use-a-custom-font"></a>Usare un tipo di carattere personalizzato

L'uso di un tipo di carattere diverso da caratteri tipografici predefiniti richiede alcune specifiche della piattaforma di codifica. In questo screenshot appare il tipo di carattere personalizzato **Lobster** dalla [tipi di carattere open source di Google](https://www.google.com/fonts) sottoposto a rendering con xamarin. Forms.

 [![Tipo di carattere personalizzato in iOS e Android](fonts-images/custom-sml.png "personalizzato-esempio i tipi di carattere")](fonts-images/custom.png#lightbox "i tipi di carattere personalizzato-esempio")

Di seguito sono illustrati i passaggi necessari per ogni piattaforma. Se i file di tipo di carattere personalizzato con un'applicazione, assicurarsi di verificare che consenta la licenza del tipo di carattere per la distribuzione.

### <a name="ios"></a>iOS

È possibile visualizzare un tipo di carattere personalizzato prima di tutto verificare che sia stata caricata, quindi facendovi riferimento per nome usando xamarin. Forms `Font` metodi.
Seguire le istruzioni in [questo post di blog](https://blog.xamarin.com/custom-fonts-in-ios/):

1. Aggiungere il file del tipo di carattere con **azione di compilazione: BundleResource**, e
2. Aggiorna il **Info. plist** file (**tipi di carattere forniti dall'applicazione**, o `UIAppFonts`, key), quindi
3. Farvi riferimento in base al nome quando si definisce un tipo di carattere in xamarin. Forms.

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

Xamarin. Forms per Android può fare riferimento a un tipo di carattere personalizzato che è stato aggiunto al progetto seguendo uno standard di denominazione specifico. Prima di tutto aggiungere il file del tipo di carattere per il **asset** cartella nel progetto di applicazione e impostare *azione di compilazione: AndroidAsset*. Quindi usare il percorso completo e *nome del tipo di carattere* separati da un cancelletto (#) come nome del tipo di carattere in xamarin. Forms, come illustrato di seguito il frammento di codice riportato di seguito:

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>WINDOWS

Xamarin. Forms per le piattaforme Windows può fare riferimento a un tipo di carattere personalizzato che è stato aggiunto al progetto seguendo uno standard di denominazione specifico. Prima di tutto aggiungere il file del tipo di carattere per il **/Assets/tipi di carattere/** cartella nel progetto di applicazione e impostare il <span class="UIItem">contenuto: azione di compilazione</span>. Quindi usare il nome di percorso e il tipo di carattere completo, seguito da un cancelletto (#) e il <span class="UIItem">nome del tipo di carattere</span>, come illustrato di seguito il frammento di codice riportato di seguito:

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> Si noti che il nome del file del tipo di carattere e il nome del tipo di carattere possono essere diversi. Per individuare il nome del tipo di carattere su Windows, il file ttf e scegliere **Preview**. Il nome del tipo di carattere può essere determinato quindi nella finestra di anteprima.

Il codice comune per l'applicazione è ora completo. Il codice di composizione telefonica specifico della piattaforma verrà ora implementato come oggetto [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

### <a name="xaml"></a>XAML

È anche possibile usare [ `Device.RuntimePlatform` ](~/xamarin-forms/platform/device.md#providing-platform-values) in XAML per il rendering di un tipo di carattere personalizzato:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="Lobster-Regular" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

## <a name="display-font-icons"></a>Visualizzare le icone del tipo di carattere

Le icone del tipo di carattere visualizzabili mediante le applicazioni xamarin. Forms, specificando i dati dell'icona del tipo di carattere in un `FontImageSource` oggetto. Questa classe, che deriva dal [ `ImageSource` ](xref:Xamarin.Forms.ImageSource) di classi, presenta le proprietà seguenti:

- `Glyph` : il valore del carattere unicode dell'icona del tipo di carattere, specificato come un `string`.
- `Size` -un `double` valore che indica la dimensione, in unità indipendenti dal dispositivo, dell'icona del tipo di carattere sottoposto a rendering. Il valore predefinito è 30.
- `FontFamily` -un `string` che rappresenta la famiglia di caratteri a cui appartiene l'icona del tipo di carattere.
- `Color` – facoltativo [ `Color` ](xref:Xamarin.Forms.Color) valore da utilizzare quando si visualizza l'icona del tipo di carattere.

Questi dati vengono utilizzati per creare un file PNG, che possono essere visualizzate da qualsiasi visualizzazione in grado di visualizzare un `ImageSource`. Questo approccio consente le icone del tipo di carattere, ad esempio emoji, deve essere visualizzato da più viste, invece di limitare la visualizzazione dell'icona del tipo di carattere per un singolo testo presentazione di visualizzazione, ad esempio un [ `Label` ](xref:Xamarin.Forms.Label).

> [!IMPORTANT]
> È possibile specificare le icone del tipo di carattere attualmente solo dalla relativa rappresentazione di caratteri unicode.

Nell'esempio XAML seguente ha un'icona singolo tipo di carattere viene visualizzata da un [ `Image` ](xref:Xamarin.Forms.Image) Vista:

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

Questo codice viene visualizzata un'icona di XBox, dalla famiglia di caratteri Ionicons, in un' [ `Image` ](xref:Xamarin.Forms.Image) visualizzazione. Si noti che mentre la versione unicode di caratteri per questa icona è `\uf30c`, dispone di caratteri di escape in XAML e quindi diventa `&#xf30c;`. Il codice c# equivalente è:

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

Gli screenshot seguenti, dal [associabile layout](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/) di esempio, Mostra varie icone del tipo di carattere viene visualizzate da un layout di associabile:

![Screenshot delle icone del tipo di carattere viene visualizzate, in iOS e Android](fonts-images/font-image-source.png "icone del tipo di carattere viene visualizzate nella visualizzazione di un'immagine")

## <a name="related-links"></a>Collegamenti correlati

- [FontsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)
- [Testo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/)
- [Layout associabile (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/)
- [Layout associabili](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
