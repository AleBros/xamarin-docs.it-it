---
title: Immagini in xamarin. Forms
description: Le immagini possono essere condivisi tra piattaforme con xamarin. Forms, possano essere caricate in modo specifico per ogni piattaforma, o possono essere scaricati per la visualizzazione.
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 255d3f2f532e4899b1a890405af942a7ca2da8ea
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912249"
---
# <a name="images-in-xamarinforms"></a>Immagini in xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)

_Le immagini possono essere condivise tra piattaforme con Novell. Forms, possono essere caricate in modo specifico per ogni piattaforma oppure possono essere scaricate per la visualizzazione._

Le immagini sono una parte fondamentale della navigazione tramite applicazione, usabilità e personalizzazione. Le applicazioni xamarin. Forms devono essere in grado di condividere le immagini in tutte le piattaforme, ma anche potenzialmente visualizzare immagini diverse in ogni piattaforma.

Immagini specifiche della piattaforma sono necessari anche per le icone e schermate iniziali; Questi elementi dovranno essere configurate per ogni piattaforma.

## <a name="display-images"></a>Visualizza immagini

Novell. Forms usa la visualizzazione [`Image`](xref:Xamarin.Forms.Image) per visualizzare le immagini in una pagina. Ha due proprietà importanti:

- [`Source`](xref:Xamarin.Forms.Image.Source) : un'istanza [`ImageSource`](xref:Xamarin.Forms.ImageSource) , ovvero file, URI o risorsa, che imposta l'immagine da visualizzare.
- [`Aspect`](xref:Xamarin.Forms.Image.Aspect) : come ridimensionare l'immagine all'interno dei limiti in cui viene visualizzata (se allungare, ritagliare o letterbox).

le istanze di [`ImageSource`](xref:Xamarin.Forms.ImageSource) possono essere ottenute usando metodi statici per ogni tipo di origine dell'immagine:

- [`FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) : richiede un nome file o FilePath che può essere risolto in ogni piattaforma.
- [`FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) : richiede un oggetto Uri, ad esempio.  `new Uri("http://server.com/image.jpg")`.
- [`FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) : richiede un identificatore di risorsa a un file di immagine incorporato nel progetto di libreria dell'applicazione o di .NET standard, con un' **azione di compilazione: EmbeddedResource**.
- [`FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) : richiede un flusso che fornisca dati di immagine.

La proprietà [`Aspect`](xref:Xamarin.Forms.Image.Aspect) determina il modo in cui l'immagine verrà ridimensionata in base all'area di visualizzazione:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) : estende l'immagine in modo da riempire completamente l'area di visualizzazione. Questo può comportare l'immagine venga distorta.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) l'immagine viene ritagliata in modo da riempire l'area di visualizzazione mantenendo l'aspetto (ad esempio, nessuna distorsione).
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -letterbox l'immagine, se necessario, in modo che l'intera immagine si trovi nell'area di visualizzazione, con uno spazio vuoto aggiunto alla parte superiore/inferiore o ai lati a seconda che l'immagine sia larga o alta.

Le immagini possono essere caricate da un [file locale](#local-images), da una [risorsa incorporata](#embedded-images), [scaricate](#download-images)o caricate da un flusso. Inoltre, le icone dei tipi di carattere possono essere visualizzate dalla vista [`Image`](xref:Xamarin.Forms.Image) specificando i dati dell'icona del tipo di carattere in un oggetto `FontImageSource`. Per altre informazioni, vedere [visualizzare le icone](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) dei tipi di carattere nella Guida per i [tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="local-images"></a>Immagini locali

I file di immagine possono essere aggiunti a ogni progetto di applicazione e fare riferimento dal codice condiviso xamarin. Forms. Questo metodo di distribuzione delle immagini è necessario quando le immagini sono specifiche della piattaforma, ad esempio quando si usano risoluzioni diverse su piattaforme diverse o progettazioni leggermente differenti.

Per usare una singola immagine in tutte le app, è *necessario usare lo stesso nome di file in ogni piattaforma*e deve essere un nome di risorsa Android valido (ad esempio, solo lettere minuscole, numeri, il carattere di sottolineatura e il periodo sono consentiti).

- **iOS** : il modo migliore per gestire e supportare le immagini perché IOS 9 consiste nell'usare **set di immagini del catalogo asset**, che devono contenere tutte le versioni di un'immagine necessarie per supportare vari dispositivi e i fattori di scalabilità per un'applicazione. Per altre informazioni, vedere [aggiunta di immagini a un set di immagini del catalogo asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- **Android** -posizionare le immagini nella directory **Resources/di tipo disegnatore** con l' **azione di compilazione: AndroidResource**. È anche possibile fornire versioni con DPI alta e bassa di un'immagine (nelle sottodirectory di **risorse** denominate in modo appropriato, ad esempio **ldpi**, **HDPI**e **xhdpi**).
- **Piattaforma UWP (Universal Windows Platform) (UWP)** -posizionare le immagini nella directory radice dell'applicazione con l' **azione di compilazione: contenuto**.

> [!IMPORTANT]
> Prima di iOS 9, le immagini in genere venivano inserite nella cartella **Resources** con l' **azione di compilazione: BundleResource**. Tuttavia, questo metodo di utilizzo di immagini in un'app per iOS è stato deprecato da Apple. Per altre informazioni, vedere [dimensioni delle immagini e nomi di file](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Rispettano le regole per la denominazione dei file e il posizionamento consente il XAML da caricare e visualizzare l'immagine in tutte le piattaforme seguenti:

```xaml
<Image Source="waterfront.jpg" />
```

Il codice c# equivalente è come segue:

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

Gli screenshot seguenti mostrano il risultato della visualizzazione di un'immagine locale in ogni piattaforma:

[![applicazione di esempio che visualizza un'immagine locale](images-images/local-sml.png)](images-images/local.png#lightbox)

Per maggiore flessibilità, è possibile usare la proprietà `Device.RuntimePlatform` per selezionare un file di immagine o un percorso diverso per alcune o tutte le piattaforme, come illustrato nell'esempio di codice seguente:

```csharp
image.Source = Device.RuntimePlatform == Device.Android 
                ? ImageSource.FromFile("waterfront.jpg") 
                : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Per usare lo stesso nome di file di immagine in tutte le piattaforme, il nome deve essere valido in tutte le piattaforme. Drawable Android hanno limitazioni di denominazione: sono consentiti solo lettere minuscole, numeri, carattere di sottolineatura e punto – e per la compatibilità multipiattaforma deve essere seguito in tutte le altre piattaforme troppo. Il nome file di esempio **Waterfront. png** segue le regole, ma esempi di nomi file non validi includono "water front. png", "Waterfront. png", "water-front. png" e "wåterfront. png".

### <a name="native-resolutions-retina-and-high-dpi"></a>Risoluzioni native (retina e DPI elevato)

iOS, Android e UWP includono il supporto per le soluzioni di immagine diversa, in cui il sistema operativo scelga l'immagine appropriata in fase di esecuzione in base alle funzionalità del dispositivo. Xamarin. Forms Usa le API le piattaforme native per il caricamento di immagini locali, in modo che esso supporta automaticamente le soluzioni alternative se i file vengono denominati correttamente e che si trova nel progetto.

Il modo migliore per gestire le immagini poiché iOS 9 consiste nel trascinare le immagini per singole risoluzioni necessarie per il set di immagini di catalogo asset appropriati. Per altre informazioni, vedere [aggiunta di immagini a un set di immagini del catalogo asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Prima di iOS 9, le versioni dell'immagine potrebbero essere inserite nella cartella **Resources** , due e tre volte la risoluzione con un suffisso **@2x** o **@3x** sul nome file prima dell'estensione di file, ad esempio **myimage@2x.png** ). Tuttavia, questo metodo di utilizzo di immagini in un'app per iOS è stato deprecato da Apple. Per altre informazioni, vedere [dimensioni delle immagini e nomi di file](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Le immagini di risoluzione alternativa Android devono essere inserite in [Directory con nome specifico](https://developer.android.com/guide/practices/screens_support.html) nel progetto Android, come illustrato nello screenshot seguente:

[percorso dell'immagine a risoluzione multipla ![Android](images-images/xs-highdpisolution-sml.png)](images-images/xs-highdpisolution.png#lightbox)

Per i nomi dei file di immagine UWP è possibile usare un [suffisso `.scale-xxx` prima dell'estensione di file](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast), dove `xxx` è la percentuale di scala applicata all'asset, ad esempio, **Image. scale-200. png**. A questo punto è possibile fare riferimento alle immagini nel codice o in XAML senza il modificatore di scala, ad esempio solo **Image. png**. La piattaforma selezionerà la scala asset appropriata più vicina corrente DPI della visualizzazione in base.

### <a name="additional-controls-that-display-images"></a>Controlli aggiuntivi che visualizzano le immagini

Alcuni controlli dispongono di proprietà che consentono di visualizzare un'immagine, ad esempio:

- [`Button`](xref:Xamarin.Forms.Button) dispone di una proprietà [`ImageSource`](xref:Xamarin.Forms.Button.ImageSource) che può essere impostata su un'immagine bitmap da visualizzare nella `Button`. Per ulteriori informazioni, vedere [utilizzo di bitmap con i pulsanti](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).
- [`ImageButton`](xref:Xamarin.Forms.Button) dispone di una proprietà [`Source`](xref:Xamarin.Forms.ImageButton.Source) che può essere impostata sull'immagine da visualizzare nella `ImageButton`. Per ulteriori informazioni, vedere [impostazione dell'origine dell'immagine](~/xamarin-forms/user-interface/imagebutton.md#setting-the-image-source).
- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) dispone di una proprietà [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) che può essere impostata su un'immagine caricata da un file, una risorsa incorporata, un URI o un flusso.
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) dispone di una proprietà [`ImageSource`](xref:Xamarin.Forms.ImageCell.ImageSource) che può essere impostata su un'immagine recuperata da un file, una risorsa incorporata, un URI o un flusso.
- [`Page`](xref:Xamarin.Forms.Page). Qualsiasi tipo di pagina che deriva da `Page` dispone di proprietà [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) e [`BackgroundImageSource`](xref:Xamarin.Forms.Page.BackgroundImageSource) , a cui è possibile assegnare un file, una risorsa incorporata, un URI o un flusso. In determinate circostanze, ad esempio quando un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) visualizza una [`ContentPage`](xref:Xamarin.Forms.ContentPage), l'icona viene visualizzata se supportata dalla piattaforma.

  > [!IMPORTANT]
  > In iOS, la proprietà [`Page.IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) non può essere popolata da un'immagine in un set di immagini del catalogo asset. Al contrario, caricare le immagini icona per la proprietà `Page.IconImageSource` da un file, una risorsa incorporata, un URI o un flusso.

## <a name="embedded-images"></a>Immagini incorporate

Immagini incorporate vengono anche fornite con un'applicazione (ad esempio immagini locali), ma invece di avere una copia dell'immagine nella struttura di file di ogni applicazione l'immagine del file è incorporato nell'assembly come risorsa. Questo metodo di distribuzione delle immagini è consigliato quando vengono utilizzate immagini identiche in ogni piattaforma ed è particolarmente appropriato per la creazione di componenti, come l'immagine è in bundle con il codice.

Per incorporare un'immagine in un progetto, fare doppio clic per aggiungere nuovi elementi e selezionare l'immagine/s che si desidera aggiungere. Per impostazione predefinita, l'immagine avrà **un'azione di compilazione: None**; è necessario impostare l'azione di **compilazione: EmbeddedResource**.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![impostare l'azione di compilazione su risorsa incorporata](images-images/vs-buildaction-sml.png)](images-images/vs-buildaction.png#lightbox)

L' **azione di compilazione** può essere visualizzata e modificata nella finestra **Proprietà** di un file.

In questo esempio l'ID risorsa è **WorkingWithImages. Beach. jpg**.
L'IDE ha generato questa impostazione predefinita concatenando lo **spazio dei nomi predefinito** per il progetto con il nome del file, usando un punto (.) tra ogni valore.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

![](images-images/xs-buildaction.png "Set Build Action: EmbeddedResource")

È anche possibile visualizzare e modificare l' **azione di compilazione** nel riquadro delle **Proprietà** di un file.
Questo riquadro Mostra l' **ID risorsa** usato per fare riferimento alla risorsa nel codice. Nella schermata seguente l' **ID risorsa** è **WorkingWithImages. Beach. jpg**.
L'IDE ha generato questa impostazione predefinita concatenando lo **spazio dei nomi predefinito** per il progetto con il nome del file, usando un punto (.) tra ogni valore.
Questo ID può essere modificato nel riquadro **Proprietà** , ma per questi esempi verrà usato il valore **WorkingWithImages. Beach. jpg** .

[riquadro delle proprietà delle risorse incorporate ![](images-images/xs-embeddedproperties-sml.png)](images-images/xs-embeddedproperties.png#lightbox)

-----

Se si inseriscono le immagini incorporate in cartelle all'interno del progetto, i nomi delle cartelle anche separati da punti (.) nell'ID di risorsa. Se si trasferisce l'immagine di **Beach. jpg** in una cartella denominata **Immagini** , si ottiene un ID di risorsa di **WorkingWithImages. images. Beach. jpg**

Il codice per caricare un'immagine incorporata passa semplicemente l' **ID risorsa** al metodo [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) come illustrato di seguito:

```csharp
var embeddedImage = new Image { 
      Source = ImageSource.FromResource(
        "WorkingWithImages.beach.jpg", 
        typeof(EmbeddedImages).GetTypeInfo().Assembly
      ) };
```

> [!NOTE]
> Per supportare la visualizzazione di immagini incorporate in modalità versione sul piattaforma UWP (Universal Windows Platform), è necessario usare l'overload di `ImageSource.FromResource` che specifica l'assembly di origine in cui cercare l'immagine.

Attualmente non è nessuna conversione implicita per gli identificatori di risorsa. Al contrario, è necessario utilizzare [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) o `new ResourceImageSource()` per caricare le immagini incorporate.

Gli screenshot seguenti mostrano il risultato della visualizzazione di un'immagine incorporata in ogni piattaforma:

[![applicazione di esempio che visualizza un'immagine incorporata](images-images/resource-sml.png)](images-images/resource.png#lightbox)

### <a name="xaml"></a>XAML

Poiché non esiste alcun convertitore di tipi incorporato da `string` a `ResourceImageSource`, questi tipi di immagini non possono essere caricati in modo nativo da XAML. È invece possibile scrivere un'estensione di markup XAML personalizzata per caricare immagini usando un **ID risorsa** specificato in XAML:

```csharp
[ContentProperty (nameof(Source))]
public class ImageResourceExtension : IMarkupExtension
{
 public string Source { get; set; }

 public object ProvideValue (IServiceProvider serviceProvider)
 {
   if (Source == null)
   {
     return null;
   }

   // Do your translation lookup here, using whatever method you require
   var imageSource = ImageSource.FromResource(Source, typeof(ImageResourceExtension).GetTypeInfo().Assembly);

   return imageSource;
 }
}
```

> [!NOTE]
> Per supportare la visualizzazione di immagini incorporate in modalità versione sul piattaforma UWP (Universal Windows Platform), è necessario usare l'overload di `ImageSource.FromResource` che specifica l'assembly di origine in cui cercare l'immagine.

Per usare questa estensione, aggiungere un `xmlns` personalizzato al codice XAML, usando lo spazio dei nomi e i valori degli assembly corretti per il progetto. È quindi possibile impostare l'origine dell'immagine utilizzando questa sintassi: `{local:ImageResource WorkingWithImages.beach.jpg}`. Seguito è riportato un esempio completo in XAML:

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage
   xmlns="http://xamarin.com/schemas/2014/forms"
   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
   xmlns:local="clr-namespace:WorkingWithImages;assembly=WorkingWithImages"
   x:Class="WorkingWithImages.EmbeddedImagesXaml">
 <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
   <!-- use a custom Markup Extension -->
   <Image Source="{local:ImageResource WorkingWithImages.beach.jpg}" />
 </StackLayout>
</ContentPage>
```

### <a name="troubleshoot-embedded-images"></a>Risolvere i problemi relativi alle immagini incorporate

#### <a name="debug-code"></a>Debug del codice

Perché a volte è difficile da comprendere perché una risorsa particolare immagine non viene caricata, il seguente codice di debug può essere aggiunto temporaneamente a un'applicazione per verificare che le risorse siano configurate correttamente. Tutte le risorse note incorporate nell'assembly specificato vengono restituite alla **console** per facilitare il debug dei problemi di caricamento delle risorse.

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly;
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

#### <a name="images-embedded-in-other-projects"></a>Immagini incorporate in altri progetti

Per impostazione predefinita, il metodo `ImageSource.FromResource` Cerca solo le immagini nello stesso assembly del codice che chiama il metodo `ImageSource.FromResource`. Utilizzando il codice di debug sopra riportato è possibile determinare quali assembly contengono una risorsa specifica modificando l'istruzione `typeof()` in un `Type` noto come in ogni assembly.

Tuttavia, l'assembly di origine cercato per un'immagine incorporata può essere specificato come argomento per il metodo `ImageSource.FromResource`:

```csharp
var imageSource = ImageSource.FromResource("filename.png", 
            typeof(MyClass).GetTypeInfo().Assembly);
```

## <a name="download-images"></a>Scaricare immagini

Le immagini possono essere scaricate automaticamente per la visualizzazione, come illustrato in XAML i seguenti:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       x:Class="WorkingWithImages.DownloadImagesXaml">
  <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
    <Label Text="Image UriSource Xaml" />
    <Image Source="https://xamarin.com/content/images/pages/forms/example-app.png" />
    <Label Text="example-app.png gets downloaded from xamarin.com" />
  </StackLayout>
</ContentPage>
```

Il codice c# equivalente è come segue:

```csharp
var webImage = new Image { 
     Source = ImageSource.FromUri(
        new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")
     ) };
```

Il metodo [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) richiede un oggetto `Uri` e restituisce un nuovo [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) che legge dal `Uri`.

È anche una conversione implicita per le stringhe URI, pertanto funzionerà anche nell'esempio seguente:

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

Gli screenshot seguenti mostrano il risultato della visualizzazione di un'immagine remota in ogni piattaforma:

[![applicazione di esempio che visualizza un'immagine scaricata](images-images/download-sml.png)](images-images/download.png#lightbox)

### <a name="downloaded-image-caching"></a>Memorizzazione nella cache delle immagini scaricata

Un [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) supporta anche la memorizzazione nella cache delle immagini scaricate, configurate tramite le proprietà seguenti:

- [`CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) : indica se la memorizzazione nella cache è abilitata (`true` per impostazione predefinita).
- [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) : `TimeSpan` che definisce per quanto tempo l'immagine verrà archiviata localmente.

La memorizzazione nella cache è abilitata per impostazione predefinita e archivierà l'immagine in locale per 24 ore. Per disabilitare la memorizzazione nella cache per una determinata immagine, creare un'istanza di origine dell'immagine come indicato di seguito:

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri = new Uri("http://server.com/image") };
```

Per impostare un periodo di cache specifica (ad esempio, 5 giorni) creare un'istanza di origine dell'immagine come indicato di seguito:

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

La memorizzazione nella cache predefinito è molto semplice supportare scenari, ad esempio lo scorrimento di elenchi di immagini, in cui è possibile impostare (o associare) un'immagine in ogni cella e fare in modo che la cache predefinita nuovamente il caricamento dell'immagine quando la cella si scorre fino in vista.

## <a name="animated-gifs"></a>Gif animate

Novell. Forms include il supporto per la visualizzazione di gif animate di piccole dimensioni. Questa operazione viene eseguita impostando la proprietà [`Image.Source`](xref:Xamarin.Forms.Image.Source) su un file GIF animato:

```xaml
<Image Source="demo.gif" />
```

> [!IMPORTANT]
> Sebbene il supporto GIF animato in Novell. Forms includa la possibilità di scaricare i file, non supporta la memorizzazione nella cache o il flusso di gif animate.

Per impostazione predefinita, quando un GIF animato viene caricato, non verrà riprodotto. Questo è dovuto al fatto che la proprietà `IsAnimationPlaying`, che controlla se un GIF animato viene riprodotto o arrestato, il valore predefinito è `false`. Questa proprietà, di tipo `bool`, è supportata da un oggetto [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che può essere la destinazione di un data binding e lo stile.

Pertanto, quando viene caricata un'immagine GIF animata, non verrà riprodotta finché la proprietà `IsAnimationPlaying` non viene impostata su `true`. È quindi possibile arrestare la riproduzione impostando la proprietà `IsAnimationPlaying` su `false`. Si noti che questa proprietà non ha alcun effetto quando si visualizza un'origine di immagine non GIF.

> [!NOTE]
> In Android, il supporto GIF animato richiede che l'applicazione usi renderer veloci e non funzioni se si è scelto di usare i renderer legacy.
> In UWP il supporto GIF animato richiede una versione minima dell'aggiornamento dell'anniversario di Windows 10 (versione 1607).

## <a name="icons-and-splash-screens"></a>Icone e schermate iniziali

Sebbene non sia correlato alla visualizzazione [`Image`](xref:Xamarin.Forms.Image) , le icone dell'applicazione e le schermate iniziali rappresentano anche un uso importante delle immagini nei progetti Novell. Forms.

L'impostazione delle icone e schermate iniziali per le app xamarin. Forms viene eseguita in ognuno dei progetti dell'applicazione. Ciò significa che la generazione di correttamente dimensioni immagini per iOS, Android e UWP. Queste immagini devono essere denominate e che si trova in base ai requisiti di ogni piattaforme.

## <a name="icons"></a>Icone

Per altre informazioni sulla creazione di queste risorse dell'applicazione, vedere [iOS uso](~/ios/app-fundamentals/images-icons/index.md)delle linee guida relative a immagini, [iconografia Google](https://developer.android.com/design/style/iconography.html)e [UWP per asset di riquadro e icone](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) .

Inoltre, le icone dei tipi di carattere possono essere visualizzate dalla vista [`Image`](xref:Xamarin.Forms.Image) specificando i dati dell'icona del tipo di carattere in un oggetto `FontImageSource`. Per altre informazioni, vedere [visualizzare le icone](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) dei tipi di carattere nella Guida per i [tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="splash-screens"></a>Schermate iniziali

Solo iOS e le applicazioni UWP richiedono una schermata iniziale (detto anche un'immagine di avvio dello schermo o predefinito).

Vedere la documentazione per [iOS uso di immagini](~/ios/app-fundamentals/images-icons/index.md) e [schermate](/windows/uwp/launch-resume/splash-screens/) iniziali in Windows Dev Center.

## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithImages (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)
- [iOS uso delle immagini](~/ios/app-fundamentals/images-icons/index.md)
- [Iconografia Android](https://developer.android.com/design/style/iconography.html)
- [Linee guida per asset affiancati e icone](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
