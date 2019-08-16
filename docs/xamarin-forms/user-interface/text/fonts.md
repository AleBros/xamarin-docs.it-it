---
title: Tipi di carattere in xamarin. Forms
description: Questo articolo illustra come specificare informazioni sui caratteri nei controlli che visualizzano testo nelle applicazioni xamarin. Forms.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/28/2019
ms.openlocfilehash: c18c4e63831a03cbe28accfe10f4c7da31130803
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529311"
---
# <a name="fonts-in-xamarinforms"></a>Tipi di carattere in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

Questo articolo descrive la modalità di xamarin. Forms che consente di specificare gli attributi dei caratteri (compresi peso e dimensioni) nei controlli che visualizzano testo. Informazioni di carattere possono essere [specificato nel codice](#Setting_Font_in_Code) oppure [specificato nel XAML](#Setting_Font_in_Xaml). È anche possibile usare un [tipo di carattere personalizzato](#Using_a_Custom_Font)e visualizzare le [icone dei tipi di carattere](#display-font-icons).

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

Novell. Forms definisce anche i campi [`NamedSize`](xref:Xamarin.Forms.NamedSize) nell'enumerazione che rappresentano dimensioni specifiche dei tipi di carattere. Per ulteriori informazioni sulle dimensioni dei tipi di carattere denominate, vedere [dimensioni dei tipi di carattere](#named-font-sizes).

<a name="FontAttributes" />

### <a name="font-attributes"></a>Attributi del tipo di carattere

Tipo di carattere, ad esempio stili **grassetto** e *corsivo* può essere impostata sul `FontAttributes` proprietà. I valori seguenti sono attualmente supportati:

- **None**
- **Grassetto**
- **Corsivo**

Il `FontAttribute` enumerazione può essere utilizzata come indicato di seguito (è possibile specificare un singolo attributo o `OR` uno a altro):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>Imposta informazioni sui tipi di carattere per piattaforma

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

È un convertitore di tipi incorporati per il `FontSize` proprietà che consente tutte le impostazioni del tipo di carattere può essere espresso come valore stringa in XAML. Inoltre, la proprietà `FontAttributes` può essere utilizzata per specificare gli attributi del tipo di carattere:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-specific-values) è anche utilizzabile in XAML per il rendering di un carattere diverso in ogni piattaforma. L'esempio seguente usa un tipo di carattere personalizzato in iOS (MarkerFelt-Thin) e specifica solo le dimensioni e gli attributi sulle altre piattaforme:

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

## <a name="named-font-sizes"></a>Dimensioni del carattere denominate

Novell. Forms definisce i campi [`NamedSize`](xref:Xamarin.Forms.NamedSize) nell'enumerazione che rappresentano dimensioni specifiche dei tipi di carattere. La tabella seguente illustra i `NamedSize` membri e le relative dimensioni predefinite in iOS, Android e il piattaforma UWP (Universal Windows Platform) (UWP):

| Member | iOS | Android | UWP |
| --- | --- | --- | --- |
| `Default` | 16 | 14 | 14 |
| `Micro` | 11 | 10 | 15,667 |
| `Small` | 13 | 14 | 18,667 |
| `Medium` | 16 | 17 | 22,667 |
| `Large` | 20 | 22 | 32 |
| `Body` | 17 | 16 | 14 |
| `Header` | 17 | 96 | 46 |
| `Title` | 28 | 24 | 24 |
| `Subtitle` | 22 | 16 | 20 |
| `Caption` | 12 | 12 | 12 |

Le dimensioni del carattere denominate possono essere impostate tramite XAML e il codice. Inoltre, è possibile `Device.GetNamedSize` chiamare il metodo per restituire un oggetto `double` che rappresenta la dimensione del carattere denominata:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> In iOS e Android, le dimensioni del carattere denominate vengono ridimensionate automaticamente in base alle opzioni di accessibilità del sistema operativo. Questo comportamento può essere disabilitato in iOS con una piattaforma specifica. Per altre informazioni, vedere [ridimensionamento dell'accessibilità per le dimensioni del carattere denominate in iOS](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

<a name="Using_a_Custom_Font" />

## <a name="use-a-custom-font"></a>Usare un tipo di carattere personalizzato

L'uso di un tipo di carattere diverso da caratteri tipografici predefiniti richiede alcune specifiche della piattaforma di codifica. In questo screenshot appare il tipo di carattere personalizzato **Lobster** dalla [tipi di carattere open source di Google](https://www.google.com/fonts) sottoposto a rendering con xamarin. Forms.

 [![Tipo di carattere personalizzato in iOS e Android](fonts-images/custom-sml.png "personalizzato-esempio i tipi di carattere")](fonts-images/custom.png#lightbox "i tipi di carattere personalizzato-esempio")

Di seguito sono illustrati i passaggi necessari per ogni piattaforma. Se i file di tipo di carattere personalizzato con un'applicazione, assicurarsi di verificare che consenta la licenza del tipo di carattere per la distribuzione.

### <a name="ios"></a>iOS

È possibile visualizzare un tipo di carattere personalizzato prima di tutto verificare che sia stata caricata, quindi facendovi riferimento per nome usando xamarin. Forms `Font` metodi.
Seguire le istruzioni in [questo post di blog](https://blog.xamarin.com/custom-fonts-in-ios/):

1. Aggiungere il file del tipo **di carattere con l'azione di compilazione: BundleResource**e
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

Xamarin. Forms per Android può fare riferimento a un tipo di carattere personalizzato che è stato aggiunto al progetto seguendo uno standard di denominazione specifico. Aggiungere innanzitutto il file del tipo di carattere alla cartella assets del progetto dell' *applicazione e impostare l'azione di compilazione: AndroidAsset*. Quindi usare il percorso completo e *nome del tipo di carattere* separati da un cancelletto (#) come nome del tipo di carattere in xamarin. Forms, come illustrato di seguito il frammento di codice riportato di seguito:

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>WINDOWS

Xamarin. Forms per le piattaforme Windows può fare riferimento a un tipo di carattere personalizzato che è stato aggiunto al progetto seguendo uno standard di denominazione specifico. Prima di tutto aggiungere il file del tipo di carattere per il **/Assets/tipi di carattere/** cartella nel progetto di applicazione e impostare il **contenuto: azione di compilazione**. Quindi usare il nome di percorso e il tipo di carattere completo, seguito da un cancelletto (#) e il **nome del tipo di carattere**, come illustrato di seguito il frammento di codice riportato di seguito:

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

È anche possibile usare [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) in XAML per il rendering di un tipo di carattere personalizzato:

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

## <a name="display-font-icons"></a>Visualizzare le icone dei tipi di carattere

Le icone dei tipi di carattere possono essere visualizzate dalle applicazioni Novell. Forms specificando i `FontImageSource` dati dell'icona del tipo di carattere in un oggetto. Questa classe, che deriva dalla [`ImageSource`](xref:Xamarin.Forms.ImageSource) classe, presenta le proprietà seguenti:

- `Glyph`: valore del carattere Unicode dell'icona del tipo di carattere, specificato `string`come.
- `Size``double` : valore che indica le dimensioni, in unità indipendenti dal dispositivo, dell'icona del tipo di carattere di cui è stato eseguito il rendering. Il valore predefinito è 30.
- `FontFamily`: oggetto `string` che rappresenta la famiglia di caratteri a cui appartiene l'icona del tipo di carattere.
- `Color`: valore facoltativo [`Color`](xref:Xamarin.Forms.Color) da usare quando si visualizza l'icona del tipo di carattere.

Questi dati vengono usati per creare un PNG, che può essere visualizzato da qualsiasi visualizzazione in grado di visualizzare `ImageSource`un. Questo approccio consente di visualizzare le icone dei tipi di carattere, ad esempio emoji, da più visualizzazioni, anziché limitare la visualizzazione dell'icona del tipo di carattere a un'unica visualizzazione di presentazione del [`Label`](xref:Xamarin.Forms.Label)testo, ad esempio.

> [!IMPORTANT]
> Le icone dei tipi di carattere possono attualmente essere specificate solo dalla rappresentazione di caratteri Unicode.

Nell'esempio di codice XAML seguente viene visualizzata una sola icona del tipo [`Image`](xref:Xamarin.Forms.Image) di carattere in una visualizzazione:

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

Questo codice Visualizza un'icona Xbox, dalla famiglia di caratteri Ionicons, in una [`Image`](xref:Xamarin.Forms.Image) vista. Si noti che, mentre il carattere Unicode per questa `\uf30c`icona è, deve essere preceduto da un carattere di escape `&#xf30c;`in XAML e quindi diventa. Il codice C# equivalente è:

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

Gli screenshot seguenti, dall'esempio di [layout associabili](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts) , mostrano diverse icone del tipo di carattere visualizzate da un layout associabile:

![Screenshot delle icone dei tipi di carattere visualizzate in iOS e Android](fonts-images/font-image-source.png "Icone del tipo di carattere visualizzate in una visualizzazione immagine")

## <a name="related-links"></a>Collegamenti correlati

- [FontsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [Testo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Layout associabili (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Layout associabili](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
