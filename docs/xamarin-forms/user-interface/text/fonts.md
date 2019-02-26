---
title: Tipi di carattere in xamarin. Forms
description: Questo articolo illustra come specificare informazioni sui caratteri nei controlli che visualizzano testo nelle applicazioni xamarin. Forms.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 9441522af53a1240707eeb21ff9f583501d2491d
ms.sourcegitcommit: 16a42b69176a40cde71e177079b11e15d300d042
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56795446"
---
# <a name="fonts-in-xamarinforms"></a>Tipi di carattere in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)

Questo articolo descrive la modalità di xamarin. Forms che consente di specificare gli attributi dei caratteri (compresi peso e dimensioni) nei controlli che visualizzano testo. Informazioni di carattere possono essere [specificato nel codice](#Setting_Font_in_Code) oppure [specificato nel XAML](#Setting_Font_in_Xaml).
È anche possibile usare una [tipo di carattere personalizzato](#Using_a_Custom_Font).

<a name="Setting_Font_in_Code" />

## <a name="setting-font-in-code"></a>Impostazione del tipo di carattere nel codice

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

### <a name="setting-font-info-per-platform"></a>Impostazione delle informazioni di carattere per ogni piattaforma

In alternativa, il `Device.RuntimePlatform` proprietà può essere utilizzata per impostare i tipi di carattere diversi in ogni piattaforma, come illustrato nel codice seguente:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/Lobster-Regular.ttf#Lobster",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

È un'ottima fonte di informazioni sui caratteri per iOS [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="setting-the-font-in-xaml"></a>Impostare il tipo di carattere in XAML

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

## <a name="using-a-custom-font"></a>Usando un tipo di carattere personalizzato

L'uso di un tipo di carattere diverso da caratteri tipografici predefiniti richiede alcune specifiche della piattaforma di codifica. In questo screenshot appare il tipo di carattere personalizzato **Lobster** dalla [tipi di carattere open source di Google](https://www.google.com/fonts) sottoposto a rendering con xamarin. Forms.

 [![Tipo di carattere personalizzato in iOS e Android](fonts-images/custom-sml.png "personalizzato-esempio i tipi di carattere")](fonts-images/custom.png#lightbox "i tipi di carattere personalizzato-esempio")

Di seguito sono illustrati i passaggi necessari per ogni piattaforma. Se i file di tipo di carattere personalizzato con un'applicazione, assicurarsi di verificare che consenta la licenza del tipo di carattere per la distribuzione.

### <a name="ios"></a>iOS

È possibile visualizzare un tipo di carattere personalizzato prima di tutto verificare che sia stata caricata, quindi facendovi riferimento per nome usando xamarin. Forms `Font` metodi.
Seguire le istruzioni in [questo post di blog](http://blog.xamarin.com/custom-fonts-in-ios/):

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

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Xamarin. Forms offre impostazioni predefinite semplice che consente di ridimensionare il testo con facilità per tutte le piattaforme supportate. Consente inoltre di specificare le dimensioni e tipo di carattere &ndash; anche in modo diverso per ogni piattaforma &ndash; quando è necessario un controllo più accurato.

Informazioni di carattere possono essere specificate anche in XAML usando gli attributi dei caratteri formattata correttamente.

## <a name="related-links"></a>Collegamenti correlati

- [FontsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)
- [Testo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/)
