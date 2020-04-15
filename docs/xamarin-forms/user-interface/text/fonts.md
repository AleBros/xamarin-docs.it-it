---
title: Tipi di carattere in Xamarin.Forms
description: In questo articolo viene illustrato come specificare informazioni sui tipi di carattere nei controlli che visualizzano testo nelle applicazioni Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
ms.openlocfilehash: ca4d3b242fcc73bb73e8d6ab1f817eefcc2ade4d
ms.sourcegitcommit: 89b3e383a37db5b940f0c63bbfe9cb806dc7d5d1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81388810"
---
# <a name="fonts-in-xamarinforms"></a>Tipi di carattere in Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

In questo articolo viene descritto come Xamarin.Forms consente di specificare gli attributi del tipo di carattere (inclusi spessore e dimensione) nei controlli che visualizzano testo. Le informazioni sui tipi di carattere possono essere [specificate nel codice](#Setting_Font_in_Code) o [in XAML.](#Setting_Font_in_Xaml) È anche possibile utilizzare un tipo di [carattere personalizzato](#use-a-custom-font)e [visualizzare le icone](#display-font-icons)dei caratteri .

<a name="Setting_Font_in_Code" />

## <a name="set-the-font-in-code"></a>Impostare il tipo di carattere nel codiceSet the font in code

Utilizzare le tre proprietà relative al tipo di carattere di tutti i controlli che visualizzano testo:

- **FontFamiglia** &ndash; `string` il nome del tipo di carattere.
- **FontSize** &ndash; la dimensione `double`del carattere come file .
- **FontAttributes** &ndash; una stringa che specifica le informazioni `FontAttributes` di stile, ad esempio *Corsivo* e **Grassetto** (utilizzando l'enumerazione in C .

Questo codice mostra come creare un'etichetta e specificare la dimensione e lo spessore del carattere da visualizzare:This code shows how to create a label and specify the font size and weight to display:

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

La `FontSize` proprietà può essere impostata su un valore double, ad esempio:The property can be set to a double value, for esempio:

```csharp
label.FontSize = 24;
```

Il valore delle dimensioni viene misurato in unità indipendenti dal dispositivo. Per ulteriori informazioni, consultate [Unità di misura.](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement)

Xamarin.Forms definisce anche [`NamedSize`](xref:Xamarin.Forms.NamedSize) i campi nell'enumerazione che rappresentano dimensioni di carattere specifiche. Per ulteriori informazioni sulle dimensioni dei caratteri con nome, consultate [Dimensioni dei caratteri con nome.](#named-font-sizes)

<a name="FontAttributes" />

### <a name="font-attributes"></a>Attributi dei caratteri

Gli stili di carattere, ad `FontAttributes` esempio **grassetto** e *corsivo,* possono essere impostati sulla proprietà. Attualmente sono supportati i seguenti valori:

- **Nessuno**
- **Grassetto**
- **Corsivo**

L'enumerazione `FontAttribute` può essere utilizzata come segue `OR` (è possibile specificare un singolo attributo o insieme):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>Impostare le informazioni sui tipi di carattere per piattaforma

In alternativa, `Device.RuntimePlatform` la proprietà può essere utilizzata per impostare nomi di tipi di carattere diversi in ogni piattaforma, come illustrato nel codice seguente:Alternatively, the property can be used to set different font names on each platform, as demonstrated in this code:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

Una buona fonte di informazioni sui font per iOS è [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="set-the-font-in-xaml"></a>Impostare il tipo di carattere in XAMLSet the font in XAML

I controlli Xamarin.Forms che `FontSize` visualizzano testo dispongono di una proprietà che può essere impostata in XAML. Il modo più semplice per impostare il tipo di carattere in XAML consiste nell'usare i valori di enumerazione delle dimensioni denominate, come illustrato nell'esempio seguente:The simplest way to set the font in XAML is to use the named size enumeration values, as shown in this example:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Esiste un convertitore incorporato `FontSize` per la proprietà che consente di esprimere tutte le impostazioni del tipo di carattere come valore stringa in XAML. Inoltre, la `FontAttributes` proprietà può essere utilizzata per specificare gli attributi del carattere:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

La [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-specific-values) proprietà può essere utilizzata anche in XAML per eseguire il rendering di un tipo di carattere diverso in ogni piattaforma. L'esempio seguente usa tipi di carattere diversi in ogni piattaforma:The example below uses different fonts on each platform:

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

Xamarin.Forms definisce i [`NamedSize`](xref:Xamarin.Forms.NamedSize) campi nell'enumerazione che rappresentano dimensioni di carattere specifiche. La tabella seguente `NamedSize` mostra i membri e le relative dimensioni predefinite in iOS, Android e la piattaforma Windows universale (UWP):

| Membro | iOS | Android | UWP |
| --- | --- | --- | --- |
| `Default` | 16 | 14 | 14 |
| `Micro` | 11 | 10 | 15.667 |
| `Small` | 13 | 14 | 18.667 |
| `Medium` | 16 | 17 | 22.667 |
| `Large` | 20 | 22 | 32 |
| `Body` | 17 | 16 | 14 |
| `Header` | 17 | 96 | 46 |
| `Title` | 28 | 24 | 24 |
| `Subtitle` | 22 | 16 | 20 |
| `Caption` | 12 | 12 | 12 |

I valori delle dimensioni vengono misurati in unità indipendenti dal dispositivo. Per ulteriori informazioni, consultate [Unità di misura.](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement)

Le dimensioni dei caratteri denominati possono essere impostate tramite XAML e codice. Inoltre, il `Device.GetNamedSize` metodo può essere `double` chiamato per restituire un che rappresenta la dimensione del carattere denominata:In addition, the method can be called to return a that represents the named font size:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> In iOS e Android, le dimensioni dei caratteri denominati verranno ridimensionate automaticamente in base alle opzioni di accessibilità del sistema operativo. Questo comportamento può essere disabilitato in iOS con una piattaforma specifica. Per ulteriori informazioni, consultate [Ridimensionamento dell'accessibilità per dimensioni dei caratteri denominati in iOS](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

## <a name="use-a-custom-font"></a>Usare un tipo di carattere personalizzato

I font personalizzati possono essere aggiunti al progetto condiviso Xamarin.Forms e utilizzati dai progetti di piattaforma senza alcun lavoro aggiuntivo. A tale scopo, seguire questa procedura:

1. Aggiungere il tipo di carattere al progetto condiviso Xamarin.Forms come risorsa incorporata (**Operazione di compilazione: EmbeddedResource**).
1. Registrare il file del tipo di **AssemblyInfo.cs**carattere con `ExportFont` l'assembly, in un file come AssemblyInfo.cs , utilizzando l'attributo . È inoltre possibile specificare un alias facoltativo.

> [!IMPORTANT]
> I font incorporati richiedono l'uso di Xamarin.Forms 4.5.0.530 o superiore.

L'esempio seguente mostra il tipo di carattere Lobster-Regular registrato con l'assembly, insieme a un alias:

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf", Alias = "Lobster")]
```

> [!NOTE]
> Il tipo di carattere può risiedere in qualsiasi cartella del progetto condiviso, senza dover specificare il nome della cartella durante la registrazione del tipo di carattere con l'assembly.

Il tipo di carattere può quindi essere utilizzato su ogni piattaforma facendo riferimento al suo nome, senza l'estensione del file:

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

Le schermate seguenti mostrano il tipo di carattere personalizzato:The following screenshots show the custom font:

[![Carattere personalizzato su iOS e Android](fonts-images/custom-sml.png "Esempio di tipi di carattere personalizzati")](fonts-images/custom.png#lightbox "Esempio di tipi di carattere personalizzati")

> [!IMPORTANT]
> In Windows, il nome del file del tipo di carattere e il nome del tipo di carattere potrebbero essere diversi. Per individuare il nome del tipo di carattere in Windows, fare clic con il pulsante destro del mouse sul file .ttf e selezionare **Anteprima**. Il nome del carattere può quindi essere determinato dalla finestra di anteprima.

## <a name="display-font-icons"></a>Visualizzare le icone dei tipi di carattere

Le icone dei tipi di carattere possono essere visualizzate dalle applicazioni Xamarin.Forms specificando i dati dell'icona del tipo di carattere in un `FontImageSource` oggetto. Questa classe, che deriva [`ImageSource`](xref:Xamarin.Forms.ImageSource) dalla classe , ha le proprietà seguenti:This class, which derives from the class, has the following properties:

- `Glyph`– il valore del carattere unicode dell'icona del carattere, specificato come . `string`
- `Size`– `double` un valore che indica la dimensione, in unità indipendenti dal dispositivo, dell'icona del carattere di cui è stato eseguito il rendering. Il valore predefinito è 30. Inoltre, questa proprietà può essere impostata su una dimensione del carattere denominata.
- `FontFamily`– `string` un che rappresenta la famiglia di caratteri a cui appartiene l'icona del carattere.
- `Color`– un [`Color`](xref:Xamarin.Forms.Color) valore opzionale da utilizzare quando si visualizza l'icona del carattere.

Questi dati vengono utilizzati per creare un file PNG, che `ImageSource`può essere visualizzato da qualsiasi vista che può visualizzare un file . Questo approccio consente di visualizzare le icone dei caratteri, ad esempio le emoji, da più visualizzazioni, anziché [`Label`](xref:Xamarin.Forms.Label)limitare la visualizzazione dell'icona del carattere a una singola visualizzazione di presentazione del testo, ad esempio un file .

> [!IMPORTANT]
> Le icone dei caratteri attualmente possono essere specificate solo dalla rappresentazione dei caratteri unicode.

L'esempio XAML seguente include una singola icona [`Image`](xref:Xamarin.Forms.Image) del tipo di carattere visualizzata da una visualizzazione:The following XAML example has a single font icon being displayed by an view:

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

Questo codice visualizza un'icona XBox, dalla famiglia di [`Image`](xref:Xamarin.Forms.Image) caratteri Ionicons, in una vista. Si noti che mentre il `\uf30c`carattere unicode per questa icona `&#xf30c;`è , deve essere sottoposto a escape in XAML e così diventa . Il codice C# equivalente è il seguente:

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

Le schermate seguenti, trattate nell'esempio Layout associabili, mostrano diverse icone dei tipi di carattere visualizzate da un layout associabile:The following screenshots, from the [Bindable Layouts](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts) sample, show several font icons being displayed by a bindable layout:

![Screenshot delle icone dei caratteri visualizzati, su iOS e Android](fonts-images/font-image-source.png "Icone dei font visualizzate in una vista Immagine")

## <a name="related-links"></a>Collegamenti correlati

- [Tipi di carattereEsempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [Testo (esempio)Text (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Layout associabili (esempio)Bindable Layouts (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Layout associabili](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
