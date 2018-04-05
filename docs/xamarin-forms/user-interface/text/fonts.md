---
title: Tipi di carattere
description: Impostazione tipi di carattere in xamarin. Forms
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 09328e1c6d884898aed86f2cb8ab1b84bf6d5cab
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2018
---
# <a name="fonts"></a>Tipi di carattere

Questo articolo descrive come xamarin. Forms consente di specificare gli attributi dei caratteri, compresi peso e dimensioni, i controlli che visualizzano il testo. Informazioni di carattere possono essere [specificate nel codice](#Setting_Font_in_Code) o [specificato in Xaml](#Setting_Font_in_Xaml).
È anche possibile usare un [tipo di carattere personalizzato](#Using_a_Custom_Font).

<a name="Setting_Font_in_Code" />

## <a name="setting-font-in-code"></a>Impostazione tipo di carattere nel codice

Utilizzare le tre proprietà relative ai caratteri di tutti i controlli che visualizzano il testo:

- **FontFamily** &ndash; il `string` nome tipo di carattere.
- **FontSize** &ndash; le dimensioni del carattere come un `double`.
- **FontAttributes** &ndash; una stringa che specifica le informazioni sullo stile come *corsivo* e **grassetto** (utilizzando la `FontAttributes` enumerazione in c#).

Questo codice viene illustrato come creare un'etichetta e specificare le dimensioni del carattere e il peso in modo da visualizzare:

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

È inoltre possibile utilizzare il `NamedSize` enumerazione che dispone di quattro opzioni incorporate; Xamarin. Forms sceglie la dimensione ottimale per ogni piattaforma.

-  **Micro**
-  **Piccola**
-  **Media**
-  **Grande**


Il `NamedSize` enumerazione può essere utilizzato ovunque un `FontSize` può essere specificato utilizzando il `Device.GetNamedSize` metodo per convertire il valore di un `double`:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

<a name="FontAttributes" />

### <a name="font-attributes"></a>Attributi dei caratteri

Stili di carattere, ad esempio **grassetto** e *corsivo* può essere impostata sul `FontAttributes` proprietà. Attualmente sono supportati i seguenti valori:

-  **None**
-  **Grassetto**
-  **Corsivo**

Il `FontAttribute` enumerazione può essere utilizzata come indicato di seguito (è possibile specificare un singolo attributo o `OR` essi):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="formattedstring"></a>FormattedString

Alcuni controlli di xamarin. Forms (ad esempio `Label`) supportano inoltre gli attributi di diverso tipo di carattere all'interno di una stringa utilizzando la `FormattedString` classe. Oggetto `FormattedString` è costituito da uno o più `Span`s, ognuno dei quali può avere un proprio formattazione attributi.

La `Span` classe presenta i seguenti attributi:

* **Testo** &ndash; il valore da visualizzare
* **FontFamily** &ndash; il nome del carattere
* **FontSize** &ndash; le dimensioni del carattere
* **FontAttributes** &ndash; informazioni sullo stile come *corsivo* e **grassetto**
* **ForegroundColor** &ndash; colore del testo
* **BackgroundColor** &ndash; colore di sfondo

Un esempio di creazione e la visualizzazione di un `FormattedString` di seguito è riportato &ndash; si noti che viene assegnato a etichette `FormattedText` proprietà e non il `Text` proprietà.

```csharp
var labelFormatted = new Label ();
var fs = new FormattedString ();
fs.Spans.Add (new Span { Text="Red, ", ForegroundColor = Color.Red, FontSize = 20, FontAttributes = FontAttributes.Italic });
fs.Spans.Add (new Span { Text=" blue, ", ForegroundColor = Color.Blue, FontSize = 32 });
fs.Spans.Add (new Span { Text=" and green!", ForegroundColor = Color.Green, FontSize = 12 });
labelFormatted.FormattedText = fs;
```


### <a name="setting-font-info-per-platform"></a>Impostazione delle informazioni di carattere per ogni piattaforma

In alternativa, il `Device.RuntimePlatform` proprietà può essere utilizzata per impostare i nomi di tipo di carattere diverso in ogni piattaforma, come illustrato in questo codice:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/Lobster-Regular.ttf#Lobster",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

È un'ottima fonte di informazioni di carattere per iOS [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="setting-the-font-in-xaml"></a>Impostazione del carattere in Xaml

Xamarin. Forms controlla che il testo visualizzato hanno un `Font` proprietà che può essere impostato in Xaml. Il modo più semplice per impostare il tipo di carattere in Xaml è consiste nell'utilizzare i valori di enumerazione dimensione denominata, come illustrato in questo esempio:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

È un convertitore di tipi incorporati per la `Font` proprietà che consente tutte le impostazioni del carattere essere espresso come valore stringa in Xaml. Gli esempi seguenti mostrano come è possibile specificare gli attributi dei caratteri e le dimensioni in Xaml:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

Per specificare più `Font` la combinazione di impostazioni, le impostazioni necessarie in una stringa di attributo del tipo di carattere singolo. La stringa dell'attributo di tipo di carattere deve essere formattata come `"[font-face],[attributes],[size]"`. L'ordine dei parametri è importante, tutti i parametri sono facoltativi e più `attributes` è possibile specificare, ad esempio:

```xaml
<Label Text="Small bold text" FontAttributes="Bold" FontSize="Micro" />
<Label Text="Really big italic text" FontAttributes="Italic" FontSize="72" />
```

La `FormattedString` classe può essere utilizzata anche in XAML, come illustrato di seguito:

```xaml
<Label>
    <Label.FormattedText>
        <FormattedString>
            <FormattedString.Spans>
                <Span Text="Red, " ForegroundColor="Red" FontAttributes="Italic" FontSize="20" />
                <Span Text=" blue, " ForegroundColor="Blue" FontSize="32" />
                <Span Text=" and green! " ForegroundColor="Green" FontSize="12"/>
            </FormattedString.Spans>
        </FormattedString>
    </Label.FormattedText>
</Label>
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-values) è inoltre utilizzabile in XAML per il rendering di un carattere diverso in ogni piattaforma. L'esempio seguente usa un tipo di carattere personalizzato in iOS (<span style="font-family:MarkerFelt-Thin">Thin MarkerFelt</span>) e specifica solo le dimensioni e attributi in altre piattaforme:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP, WinRT, WinPhone" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

Quando si specifica un tipo di carattere personalizzato, è sempre consigliabile utilizzare `OnPlatform`, come risulta difficile trovare un tipo di carattere che è disponibile in tutte le piattaforme.

<a name="Using_a_Custom_Font" />

## <a name="using-a-custom-font"></a>Utilizzo di un tipo di carattere personalizzato

Utilizza un carattere diverso da caratteri tipografici predefiniti richiede alcune specifiche della piattaforma di codifica. Questa schermata mostra il tipo di carattere personalizzato **astice** da [tipi di carattere open source di Google](https://www.google.com/fonts) sottoposti a rendering in iOS, Android e Windows Phone con xamarin. Forms.

 [![Tipo di carattere personalizzato in iOS e Android](fonts-images/custom-sml.png "esempio tipi di carattere personalizzato")](fonts-images/custom.png#lightbox "esempio tipi di carattere personalizzato")

Di seguito sono descritti i passaggi necessari per ogni piattaforma. Quando si includono i file di tipo di carattere personalizzato con un'applicazione, assicurarsi di verificare che il tipo di carattere licenza per la distribuzione.

### <a name="ios"></a>iOS

È possibile visualizzare un tipo di carattere personalizzato, verificare innanzitutto che viene caricato, quindi fatto riferimento in base al nome utilizzando il xamarin. Forms `Font` metodi.
Seguire le istruzioni in [questo post di blog](http://blog.xamarin.com/custom-fonts-in-ios/):

1. Aggiungere il file del tipo di carattere con **azione di compilazione: BundleResource**, e
2. Aggiornamento di **Info. plist** file (**tipi di carattere forniti dall'applicazione**, o `UIAppFonts`, key), quindi
3. Si fa riferimento in base al nome ogni volta che si definisce un tipo di carattere in xamarin. Forms.

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

Xamarin. Forms per Android possono fare riferimento a un tipo di carattere personalizzato che è stato aggiunto al progetto seguendo uno standard di denominazione specifico. Innanzitutto aggiungere il file di tipo di carattere per il **asset** cartella nel progetto di applicazione e set *azione di compilazione: AndroidAsset*. Quindi usare il percorso completo e *nome carattere* separati da un cancelletto (#) come nome del tipo di carattere in xamarin. Forms, come illustrato nel frammento di codice seguente:

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>WINDOWS

Xamarin. Forms per le piattaforme Windows può fare riferimento a un tipo di carattere personalizzato che è stato aggiunto al progetto seguendo uno standard di denominazione specifico. Innanzitutto aggiungere il file di tipo di carattere per il **/Assets/tipi di carattere/** cartella nel progetto di applicazione e i set di <span class="UIItem">contenuto: azione di compilazione</span>. Utilizzare quindi il nome di percorso e il tipo di carattere completo, seguito da un cancelletto (#) e <span class="UIItem">nome carattere</span>, come illustrato nel frammento di codice seguente:

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> Si noti che il nome di file di tipo di carattere e carattere potrebbe essere diversi. Per individuare il nome del carattere in Windows, il file ttf e scegliere **anteprima**. Il nome del carattere può quindi essere determinato dalla finestra di anteprima.

Il codice comune per l'applicazione è ora completo. Il codice di composizione telefonica specifico della piattaforma verrà ora implementato come oggetto [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

### <a name="xaml"></a>XAML

È inoltre possibile utilizzare [ `Device.RuntimePlatform` ](~/xamarin-forms/platform/device.md#providing-platform-values) in XAML per il rendering di un tipo di carattere personalizzato:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="Lobster-Regular" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP, WinRT, WinPhone" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Xamarin. Forms fornisce impostazioni predefinite semplice per consentono di dimensioni testo facilmente per tutte le piattaforme supportate. Consente inoltre di specificare tipo di carattere e dimensioni &ndash; anche in modo diverso per ogni piattaforma &ndash; quando un controllo più accurato è obbligatorio. Il `FormattedString` classe può essere utilizzata per costruire una stringa che contiene le specifiche di diverso tipo di carattere utilizzando il `Span` classe.

Informazioni di carattere possono essere specificate anche in Xaml utilizzando gli attributi di un tipo di carattere formattato correttamente o `FormattedString` elemento con `Span` gli elementi figlio.


## <a name="related-links"></a>Collegamenti correlati

- [FontsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)
- [Testo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/)
