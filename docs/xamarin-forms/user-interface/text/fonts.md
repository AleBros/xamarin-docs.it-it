---
title: Tipi di carattere in xamarin. Forms
description: Questo articolo illustra come specificare informazioni sui caratteri nei controlli che visualizzano testo nelle applicazioni xamarin. Forms.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/20/2020
ms.openlocfilehash: 3798e3612547d36905dd62e6314f158958782874
ms.sourcegitcommit: 5b6d3bddf7148f8bb374de5657bdedc125d72ea7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78292091"
---
# <a name="fonts-in-xamarinforms"></a>Tipi di carattere in xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

Questo articolo descrive la modalità di xamarin. Forms che consente di specificare gli attributi dei caratteri (compresi peso e dimensioni) nei controlli che visualizzano testo. Le informazioni sul tipo di carattere possono essere [specificate nel codice](#Setting_Font_in_Code) o [specificate in XAML](#Setting_Font_in_Xaml). È anche possibile usare un [tipo di carattere personalizzato](#Using_a_Custom_Font)e visualizzare le [icone dei tipi di carattere](#display-font-icons).

<a name="Setting_Font_in_Code" />

## <a name="set-the-font-in-code"></a>Impostare il tipo di carattere nel codice

Usare le tre proprietà correlate al carattere di tutti i controlli che visualizzano testo:

- **FontFamily** &ndash; il `string` nome del tipo di carattere.
- **FontSize** &ndash; le dimensioni del carattere come `double`.
- **FontAttributes** &ndash; una stringa che specifica informazioni sullo stile, ad esempio *corsivo* e **grassetto** ( C#usando l'enumerazione `FontAttributes` in).

Questo codice viene illustrato come creare un'etichetta e specificare le dimensioni del carattere e il peso da visualizzare:

```csharp
var about = new Label
{
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>Dimensioni carattere

La proprietà `FontSize` può essere impostata su un valore Double, ad esempio:

```csharp
label.FontSize = 24;
```

Il valore delle dimensioni viene misurato in unità indipendenti dal dispositivo. Per altre informazioni, vedere [unità di misura](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Novell. Forms definisce anche i campi nell'enumerazione [`NamedSize`](xref:Xamarin.Forms.NamedSize) che rappresentano dimensioni specifiche dei tipi di carattere. Per ulteriori informazioni sulle dimensioni dei tipi di carattere denominate, vedere [dimensioni dei tipi di carattere](#named-font-sizes).

<a name="FontAttributes" />

### <a name="font-attributes"></a>Attributi del tipo di carattere

Gli stili dei tipi di carattere, ad esempio **grassetto** e *corsivo* , possono essere impostati sulla proprietà `FontAttributes`. I valori seguenti sono attualmente supportati:

- **Nessuno**
- **Grassetto**
- **Corsivo**

L'enumerazione `FontAttribute` può essere usata come indicato di seguito (è possibile specificare un singolo attributo o `OR` insieme):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>Imposta informazioni sui tipi di carattere per piattaforma

In alternativa, è possibile usare la proprietà `Device.RuntimePlatform` per impostare nomi di tipi di carattere diversi in ogni piattaforma, come illustrato nel codice seguente:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

Una fonte efficace di informazioni sui tipi di carattere per iOS è [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="set-the-font-in-xaml"></a>Impostare il tipo di carattere in XAML

I controlli Novell. Forms che visualizzano tutti il testo hanno una proprietà `FontSize` che può essere impostata in XAML. Il modo più semplice per impostare il tipo di carattere in XAML è usare i valori di enumerazione denominato dimensioni, come illustrato in questo esempio:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Per la proprietà `FontSize` è disponibile un convertitore incorporato che consente di esprimere tutte le impostazioni dei tipi di carattere come valore stringa in XAML. Inoltre, è possibile usare la proprietà `FontAttributes` per specificare gli attributi del tipo di carattere:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

È inoltre possibile utilizzare la proprietà [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-specific-values) in XAML per eseguire il rendering di un tipo di carattere diverso in ogni piattaforma. Nell'esempio seguente vengono usati tipi di carattere diversi in ogni piattaforma:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

## <a name="named-font-sizes"></a>Dimensioni del carattere denominate

Novell. Forms definisce i campi nell'enumerazione [`NamedSize`](xref:Xamarin.Forms.NamedSize) che rappresentano dimensioni specifiche dei tipi di carattere. La tabella seguente illustra i membri `NamedSize` e le relative dimensioni predefinite in iOS, Android e il piattaforma UWP (Universal Windows Platform) (UWP):

| Membro | iOS | Android | UWP |
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

I valori delle dimensioni sono misurati in unità indipendenti dal dispositivo. Per altre informazioni, vedere [unità di misura](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Le dimensioni del carattere denominate possono essere impostate tramite XAML e il codice. Inoltre, è possibile chiamare il metodo `Device.GetNamedSize` per restituire una `double` che rappresenta le dimensioni del carattere denominate:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> In iOS e Android, le dimensioni del carattere denominate vengono ridimensionate automaticamente in base alle opzioni di accessibilità del sistema operativo. Questo comportamento può essere disabilitato in iOS con una piattaforma specifica. Per altre informazioni, vedere [ridimensionamento dell'accessibilità per le dimensioni del carattere denominate in iOS](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

<a name="Using_a_Custom_Font" />

## <a name="use-a-custom-font"></a>Usare un tipo di carattere personalizzato

L'uso di un tipo di carattere diverso da caratteri tipografici predefiniti richiede alcune specifiche della piattaforma di codifica. Questa schermata mostra l' **aragosta** del tipo di carattere personalizzato dei [tipi di carattere Open Source di Google di cui è](https://www.google.com/fonts) stato eseguito il rendering con Novell. Form

 [![Tipo di carattere personalizzato in iOS e Android](fonts-images/custom-sml.png "Esempio di tipi di carattere personalizzati")](fonts-images/custom.png#lightbox "Esempio di tipi di carattere personalizzati")

Di seguito sono illustrati i passaggi necessari per ogni piattaforma. Se i file di tipo di carattere personalizzato con un'applicazione, assicurarsi di verificare che consenta la licenza del tipo di carattere per la distribuzione.

### <a name="ios"></a>iOS

È possibile visualizzare un tipo di carattere personalizzato assicurandosi prima di tutto che venga caricato, quindi facendo riferimento al nome mediante i metodi `Font` Novell. Forms.
Seguire le istruzioni riportate in [questo post di Blog](https://devblogs.microsoft.com/xamarin/custom-fonts-in-ios/):

1. Aggiungere il file del tipo di carattere con l' **azione di compilazione: BundleResource**e
2. Aggiornare il file **info. plist** (**tipi di carattere forniti dall'applicazione**o `UIAppFonts`chiave), quindi
3. Farvi riferimento in base al nome quando si definisce un tipo di carattere in xamarin. Forms.

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

Xamarin. Forms per Android può fare riferimento a un tipo di carattere personalizzato che è stato aggiunto al progetto seguendo uno standard di denominazione specifico. Aggiungere innanzitutto il file del tipo di carattere alla cartella **assets** del progetto dell'applicazione e impostare l' *azione di compilazione: AndroidAsset*. Usare quindi il percorso completo e il *nome del tipo di carattere* separati da un hash (#) come nome del tipo di carattere in Novell. Forms, come illustrato nel frammento di codice seguente:

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>WINDOWS

Xamarin. Forms per le piattaforme Windows può fare riferimento a un tipo di carattere personalizzato che è stato aggiunto al progetto seguendo uno standard di denominazione specifico. Aggiungere innanzitutto il file del tipo di carattere alla cartella **/assets/Fonts/** nel progetto dell'applicazione e impostare l' **azione di compilazione: content**. Usare quindi il percorso completo e il nome file del tipo di carattere, seguito da un hash (#) e dal **nome del tipo di carattere**, come illustrato nel frammento di codice seguente:

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> Si noti che il nome del file del tipo di carattere e il nome del tipo di carattere possono essere diversi. Per individuare il nome del tipo di carattere in Windows, fare clic con il pulsante destro del mouse sul file con estensione ttf e selezionare **Anteprima**. Il nome del tipo di carattere può essere determinato quindi nella finestra di anteprima.

### <a name="xaml"></a>XAML

È anche possibile usare [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) in XAML per eseguire il rendering di un tipo di carattere personalizzato:

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

## <a name="use-a-custom-font-preview"></a>Usare un tipo di carattere personalizzato (anteprima)

I tipi di carattere personalizzati possono essere aggiunti al progetto condiviso Novell. Forms e utilizzati dai progetti della piattaforma senza alcuna attività aggiuntiva. A tale scopo, seguire questa procedura:

1. Aggiungere il tipo di carattere al progetto condiviso Novell. Forms come risorsa incorporata (**azione di compilazione: EmbeddedResource**).
1. Registrare il file del tipo di carattere con l'assembly, in un file, ad esempio **AssemblyInfo.cs**, usando l'attributo `ExportFont`.

Nell'esempio seguente viene illustrato il tipo di carattere Lobster-Regular registrato con l'assembly:

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf")]
```

> [!NOTE]
> Il tipo di carattere può risiedere in qualsiasi cartella del progetto condiviso, senza dover specificare il nome della cartella quando si registra il tipo di carattere con l'assembly.

Il tipo di carattere può quindi essere utilizzato in ogni piattaforma facendo riferimento al relativo nome, senza l'estensione di file:

```xaml
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster-Regular" />
```

Il codice C# equivalente è il seguente:

```csharp
Label label = new Label
{
    Text = "Hello Xamarin.Forms!",
    FontFamily = "Lobster-Regular"
};
```

Gli screenshot seguenti mostrano il tipo di carattere personalizzato:

[![Tipo di carattere personalizzato in iOS e Android](fonts-images/custom-sml.png "Esempio di tipi di carattere personalizzati")](fonts-images/custom.png#lightbox "Esempio di tipi di carattere personalizzati")

## <a name="display-font-icons"></a>Visualizzare le icone dei tipi di carattere

Le icone dei tipi di carattere possono essere visualizzate dalle applicazioni Novell. Forms specificando i dati dell'icona del tipo di carattere in un oggetto `FontImageSource`. Questa classe, che deriva dalla classe [`ImageSource`](xref:Xamarin.Forms.ImageSource) , presenta le proprietà seguenti:

- `Glyph`: valore del carattere Unicode dell'icona del tipo di carattere, specificato come `string`.
- `Size`: valore `double` che indica le dimensioni, in unità indipendenti dal dispositivo, dell'icona del tipo di carattere di cui è stato eseguito il rendering. Il valore predefinito è 30. Inoltre, questa proprietà può essere impostata su una dimensione del tipo di carattere denominata.
- `FontFamily`: `string` che rappresenta la famiglia di caratteri a cui appartiene l'icona del tipo di carattere.
- `Color`: valore facoltativo [`Color`](xref:Xamarin.Forms.Color) da usare quando si visualizza l'icona del tipo di carattere.

Questi dati vengono usati per creare un PNG, che può essere visualizzato da qualsiasi visualizzazione in grado di visualizzare un `ImageSource`. Questo approccio consente di visualizzare le icone dei tipi di carattere, ad esempio emoji, da più visualizzazioni, anziché limitare la visualizzazione dell'icona del tipo di carattere a un'unica visualizzazione di presentazione del testo, ad esempio un [`Label`](xref:Xamarin.Forms.Label).

> [!IMPORTANT]
> Le icone dei tipi di carattere possono attualmente essere specificate solo dalla rappresentazione di caratteri Unicode.

Nell'esempio di codice XAML seguente viene visualizzata una sola icona del tipo di carattere da una visualizzazione [`Image`](xref:Xamarin.Forms.Image) :

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

Questo codice Visualizza un'icona XBox, dalla famiglia di caratteri Ionicons, in una visualizzazione [`Image`](xref:Xamarin.Forms.Image) . Si noti che, mentre il carattere Unicode per questa icona è `\uf30c`, deve essere preceduto da un carattere di escape in XAML e pertanto viene `&#xf30c;`. Il codice C# equivalente è il seguente:

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
