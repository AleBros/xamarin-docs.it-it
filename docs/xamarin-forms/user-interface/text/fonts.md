---
title: Tipi di carattere inXamarin.Forms
description: Questo articolo illustra come specificare informazioni sui tipi di carattere sui controlli che visualizzano testo nelle Xamarin.Forms applicazioni.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3201340c8056fb1a7e36240eb329df14bd960ca3
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136214"
---
# <a name="fonts-in-xamarinforms"></a>Tipi di carattere inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

Questo articolo descrive in che modo Xamarin.Forms è possibile specificare gli attributi del tipo di carattere (inclusi il peso e le dimensioni) sui controlli che visualizzano il testo. Le informazioni sul tipo di carattere possono essere [specificate nel codice](#Setting_Font_in_Code) o [specificate in XAML](#Setting_Font_in_Xaml). È anche possibile usare un [tipo di carattere personalizzato](#use-a-custom-font)e visualizzare le [icone dei tipi di carattere](#display-font-icons).

<a name="Setting_Font_in_Code" />

## <a name="set-the-font-in-code"></a>Impostare il tipo di carattere nel codice

Usare le tre proprietà correlate al tipo di carattere di tutti i controlli che visualizzano il testo:

- **FontFamily** &ndash; `string`nome del tipo di carattere.
- **FontSize** &ndash; dimensioni del carattere come `double` .
- **FontAttributes** &ndash; stringa che specifica informazioni sullo stile, ad esempio *corsivo* e **grassetto** (usando l' `FontAttributes` enumerazione in C#).

Questo codice Mostra come creare un'etichetta e specificare le dimensioni e il peso del carattere da visualizzare:

```csharp
var about = new Label
{
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>Dimensioni del carattere

La `FontSize` proprietà può essere impostata su un valore Double, ad esempio:

```csharp
label.FontSize = 24;
```

Il valore delle dimensioni viene misurato in unità indipendenti dal dispositivo. Per altre informazioni, vedere [unità di misura](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Xamarin.Formsdefinisce inoltre i campi nell' [`NamedSize`](xref:Xamarin.Forms.NamedSize) enumerazione che rappresentano dimensioni specifiche dei tipi di carattere. Per ulteriori informazioni sulle dimensioni dei tipi di carattere denominate, vedere [dimensioni dei tipi di carattere](#named-font-sizes).

<a name="FontAttributes" />

### <a name="font-attributes"></a>Attributi del tipo di carattere

Gli stili dei tipi di carattere, ad esempio **grassetto** e *corsivo* , possono essere impostati sulla `FontAttributes` Proprietà. Attualmente sono supportati i valori seguenti:

- **Nessuno**
- **Grassetto**
- **Corsivo**

L' `FontAttribute` enumerazione può essere utilizzata come indicato di seguito (è possibile specificare un singolo attributo o `OR` insieme):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>Imposta informazioni sui tipi di carattere per piattaforma

In alternativa, `Device.RuntimePlatform` è possibile usare la proprietà per impostare nomi di tipi di carattere diversi in ogni piattaforma, come illustrato nel codice seguente:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

Una fonte efficace di informazioni sui tipi di carattere per iOS è [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="set-the-font-in-xaml"></a>Impostare il tipo di carattere in XAML

Xamarin.Formsi controlli che visualizzano tutti il testo hanno una `FontSize` proprietà che può essere impostata in XAML. Il modo più semplice per impostare il tipo di carattere in XAML consiste nell'usare i valori di enumerazione delle dimensioni denominate, come illustrato nell'esempio seguente:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

È disponibile un convertitore predefinito per la `FontSize` proprietà che consente di esprimere tutte le impostazioni dei tipi di carattere come valore stringa in XAML. Inoltre, la `FontAttributes` proprietà può essere utilizzata per specificare gli attributi del tipo di carattere:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

La [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#provide-platform-specific-values) proprietà può essere utilizzata anche in XAML per eseguire il rendering di un tipo di carattere diverso in ogni piattaforma. Nell'esempio seguente vengono usati tipi di carattere diversi in ogni piattaforma:

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

Xamarin.Formsdefinisce i campi nell' [`NamedSize`](xref:Xamarin.Forms.NamedSize) enumerazione che rappresentano dimensioni specifiche dei tipi di carattere. La tabella seguente illustra i `NamedSize` membri e le relative dimensioni predefinite in iOS, Android e il piattaforma UWP (Universal Windows Platform) (UWP):

| Membro | iOS | Android | Piattaforma UWP |
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

Le dimensioni del carattere denominate possono essere impostate tramite XAML e il codice. Inoltre, `Device.GetNamedSize` è possibile chiamare il metodo per restituire un oggetto `double` che rappresenta la dimensione del carattere denominata:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> In iOS e Android, le dimensioni del carattere denominate vengono ridimensionate automaticamente in base alle opzioni di accessibilità del sistema operativo. Questo comportamento può essere disabilitato in iOS con una piattaforma specifica. Per altre informazioni, vedere [ridimensionamento dell'accessibilità per le dimensioni del carattere denominate in iOS](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

## <a name="use-a-custom-font"></a>Usare un tipo di carattere personalizzato

I tipi di carattere personalizzati possono essere aggiunti al Xamarin.Forms progetto condiviso e utilizzati dai progetti della piattaforma senza alcuna attività aggiuntiva. A tale scopo, seguire questa procedura:

1. Aggiungere il tipo di carattere al Xamarin.Forms progetto condiviso come risorsa incorporata (**azione di compilazione: EmbeddedResource**).
1. Registrare il file del tipo di carattere con l'assembly, in un file, ad esempio **AssemblyInfo.cs**, usando l' `ExportFont` attributo. È possibile specificare anche un alias facoltativo.

> [!IMPORTANT]
> I tipi di carattere incorporati richiedono l'uso di Xamarin.Forms 4.5.0.530 o versione successiva.

Nell'esempio seguente viene illustrato il tipo di carattere Lobster-Regular registrato con l'assembly, insieme a un alias:

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf", Alias = "Lobster")]
```

> [!NOTE]
> Il tipo di carattere può risiedere in qualsiasi cartella del progetto condiviso, senza dover specificare il nome della cartella quando si registra il tipo di carattere con l'assembly.

Il tipo di carattere può quindi essere utilizzato in ogni piattaforma facendo riferimento al relativo nome, senza l'estensione di file:

```xaml
<!-- Use font name -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster-Regular" />
```

In alternativa, può essere utilizzato in ogni piattaforma facendo riferimento al relativo alias:

```xaml
<!-- Use font alias -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster" />
```

Il codice C# equivalente è il seguente:

```csharp
// Use font name
Label label1 = new Label
{
    Text = "Hello Xamarin.Forms!",
    FontFamily = "Lobster-Regular"
};

// Use font alias
Label label2 = new Label
{
    Text = "Hello Xamarin.Forms!",
    FontFamily = "Lobster"
};
```

Gli screenshot seguenti mostrano il tipo di carattere personalizzato:

[![Tipo di carattere personalizzato in iOS e Android](fonts-images/custom-sml.png "Esempio di tipi di carattere personalizzati")](fonts-images/custom.png#lightbox "Esempio di tipi di carattere personalizzati")

> [!IMPORTANT]
> In Windows, il nome del file del tipo di carattere e il nome del tipo di carattere potrebbero essere diversi. Per individuare il nome del tipo di carattere in Windows, fare clic con il pulsante destro del mouse sul file con estensione ttf e selezionare **Anteprima**. Il nome del tipo di carattere può quindi essere determinato dalla finestra di anteprima.

## <a name="display-font-icons"></a>Visualizzare le icone dei tipi di carattere

Le icone dei tipi di carattere possono essere visualizzate dalle Xamarin.Forms applicazioni specificando i dati dell'icona del tipo di carattere in un `FontImageSource` oggetto. Questa classe, che deriva dalla [`ImageSource`](xref:Xamarin.Forms.ImageSource) classe, presenta le proprietà seguenti:

- `Glyph`: valore del carattere Unicode dell'icona del tipo di carattere, specificato come `string` .
- `Size`: `double` valore che indica le dimensioni, in unità indipendenti dal dispositivo, dell'icona del tipo di carattere di cui è stato eseguito il rendering. Il valore predefinito è 30. Inoltre, questa proprietà può essere impostata su una dimensione del tipo di carattere denominata.
- `FontFamily`: oggetto `string` che rappresenta la famiglia di caratteri a cui appartiene l'icona del tipo di carattere.
- `Color`: valore facoltativo [`Color`](xref:Xamarin.Forms.Color) da usare quando si visualizza l'icona del tipo di carattere.

Questi dati vengono usati per creare un PNG, che può essere visualizzato da qualsiasi visualizzazione in grado di visualizzare un `ImageSource` . Questo approccio consente di visualizzare le icone dei tipi di carattere, ad esempio emoji, da più visualizzazioni, anziché limitare la visualizzazione dell'icona del tipo di carattere a un'unica visualizzazione di presentazione del testo, ad esempio [`Label`](xref:Xamarin.Forms.Label) .

> [!IMPORTANT]
> Le icone dei tipi di carattere possono attualmente essere specificate solo dalla rappresentazione di caratteri Unicode.

Nell'esempio di codice XAML seguente viene visualizzata una sola icona del tipo di carattere in una [`Image`](xref:Xamarin.Forms.Image) visualizzazione:

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

Questo codice Visualizza un'icona XBox, dalla famiglia di caratteri Ionicons, in una [`Image`](xref:Xamarin.Forms.Image) vista. Si noti che, mentre il carattere Unicode per questa icona è, deve essere preceduto da un carattere `\uf30c` di escape in XAML e quindi diventa `&#xf30c;` . Il codice C# equivalente è il seguente:

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
