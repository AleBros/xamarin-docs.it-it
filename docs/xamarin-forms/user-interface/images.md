---
title: Immagini in xamarin. Forms
description: Le immagini possono essere condivisi tra piattaforme con xamarin. Forms, possano essere caricate in modo specifico per ogni piattaforma, o possono essere scaricati per la visualizzazione.
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: 5c1d73acfef7a4384db7220daf7319251eb61f85
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670987"
---
# <a name="images-in-xamarinforms"></a>Immagini in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/)

_Le immagini possono essere condivisi tra piattaforme con xamarin. Forms, possano essere caricate in modo specifico per ogni piattaforma, o possono essere scaricati per la visualizzazione._

Le immagini sono una parte fondamentale della navigazione tramite applicazione, usabilità e personalizzazione. Le applicazioni xamarin. Forms devono essere in grado di condividere le immagini in tutte le piattaforme, ma anche potenzialmente visualizzare immagini diverse in ogni piattaforma.

Immagini specifiche della piattaforma sono necessari anche per le icone e schermate iniziali; Questi elementi dovranno essere configurate per ogni piattaforma.

## <a name="displaying-images"></a>Visualizzazione di immagini

Xamarin. Forms Usa il [ `Image` ](xref:Xamarin.Forms.Image) vista per visualizzare le immagini in una pagina. Ha due proprietà importanti:

- [`Source`](xref:Xamarin.Forms.Image.Source) -An [ `ImageSource` ](xref:Xamarin.Forms.ImageSource) istanza, File, Uri o risorsa, che imposta l'immagine da visualizzare.
- [`Aspect`](xref:Xamarin.Forms.Image.Aspect) -Come impostare le dimensioni dell'immagine entro i limiti che vengano visualizzati all'interno (se stretch, ritagliare o letterbox).

[`ImageSource`](xref:Xamarin.Forms.ImageSource) le istanze possono essere ottenute utilizzando metodi statici per ogni tipo di origine dell'immagine:

- [`FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) -Richiede un nome file o percorso file che può essere risolto in ogni piattaforma.
- [`FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) -Richiede un oggetto di Uri, ad es.  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) -Richiede un identificatore di risorsa in un file di immagine incorporato nell'applicazione o del progetto di libreria .NET Standard, con un **EmbeddedResource: azione di compilazione**.
- [`FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) -Richiede un flusso che fornisce i dati di immagine.

Il [ `Aspect` ](xref:Xamarin.Forms.Image.Aspect) proprietà determina come l'immagine verrà adattata in base all'area di visualizzazione:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -Si estende sull'immagine per completamente ed esattamente riempire l'area di visualizzazione. Questo può comportare l'immagine venga distorta.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -Ritaglia l'immagine in modo da riempire l'area di visualizzazione mantenendo le proporzioni (ie. Nessun distorsioni).
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -Consegna l'immagine, se necessario, in modo che l'intera immagine rientri nell'area di visualizzazione, con uno spazio vuoto aggiunto per i primi/ultimi o i lati a seconda che l'immagine è ampia e un'altezza.

È possibile caricare le immagini da un [file locale](#local-images), un [risorsa incorporata](#embedded-images), o [scaricato](#downloading-images). Inoltre, le icone del tipo di carattere possono essere visualizzate dal [ `Image` ](xref:Xamarin.Forms.Image) visualizzazione specificando i dati dell'icona del tipo di carattere in un `FontImageSource` oggetto. Per altre informazioni, vedere [visualizzare le icone del tipo di carattere](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) nel [tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md) Guida.

## <a name="local-images"></a>Immagini locali

I file di immagine possono essere aggiunti a ogni progetto di applicazione e fare riferimento dal codice condiviso xamarin. Forms. Questo metodo di distribuzione delle immagini è necessario quando le immagini sono specifiche della piattaforma, ad esempio quando si usa risoluzioni diverse su piattaforme diverse o leggermente diverse progettazioni.

Usare una singola immagine in tutte le app, *lo stesso nome file deve essere usato in qualsiasi piattaforma*, e deve essere un nome di risorsa di Android valido (ie. sono consentiti solo lettere minuscole, numeri, il carattere di sottolineatura e il periodo).

- **iOS** - il preferito consente di gestire e supportare immagini poiché iOS 9 consiste nell'usare **set di immagini di catalogo di Asset**, che deve contenere tutte le versioni di un'immagine che sono necessari per supportare vari tipi di dispositivi e per fattori di scala di un applicazione. Per altre informazioni, vedere [aggiunta di immagini da un catalogo Asset immagine Set](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- **Android** -collocare le immagini nel **risorse/drawable** directory con **azione di compilazione: AndroidResource**. È possibile fornire anche le versioni ad alta e bassa risoluzione di un'immagine (in denominato in modo appropriato **le risorse** sottodirectory, ad esempio **drawable ldpi**, **drawable hdpi**e **drawable xhdpi**).
- **Universal Windows Platform (UWP)** -collocare le immagini nella directory radice dell'applicazione con **azione di compilazione: Content**.

> [!IMPORTANT]
> Prima di iOS 9, le immagini in genere inserite nel **le risorse** cartella con **azione di compilazione: BundleResource**. Tuttavia, questo metodo di utilizzo di immagini in un'app per iOS è stato deprecato da Apple. Per altre informazioni, vedere [le dimensioni delle immagini e i nomi file](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Rispettano le regole per la denominazione dei file e il posizionamento consente il XAML da caricare e visualizzare l'immagine in tutte le piattaforme seguenti:

```xaml
<Image Source="waterfront.jpg" />
```

Il codice c# equivalente è come segue:

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

Gli screenshot seguenti mostrano il risultato della visualizzazione di un'immagine locale in ogni piattaforma:

[![Locale ImageSource](images-images/local-sml.png "che visualizzano un'immagine locale dell'applicazione di esempio")](images-images/local.png#lightbox "che visualizzano un'immagine locale dell'applicazione di esempio")

Per una maggiore flessibilità di `Device.RuntimePlatform` proprietà può essere utilizzata per selezionare un file di immagine diverso o un percorso per alcune o tutte le piattaforme, come illustrato nell'esempio di codice:

```csharp
image.Source = Device.RuntimePlatform == Device.Android ? ImageSource.FromFile("waterfront.jpg") : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Per usare lo stesso nome di file di immagine in tutte le piattaforme, il nome deve essere valido in tutte le piattaforme. Drawable Android hanno limitazioni di denominazione: sono consentiti solo lettere minuscole, numeri, carattere di sottolineatura e punto – e per la compatibilità multipiattaforma deve essere seguito in tutte le altre piattaforme troppo. Il nome del file di esempio **waterfront.png** segue le regole, ma gli esempi di nomi di file non validi includono "acqua front.png", "WaterFront.png", "acqua-front.png" e "wåterfront.png".

### <a name="native-resolutions-retina-and-high-dpi"></a>Risoluzioni native (Retina e ad alta risoluzione)

iOS, Android e UWP includono il supporto per le soluzioni di immagine diversa, in cui il sistema operativo scelga l'immagine appropriata in fase di esecuzione in base alle funzionalità del dispositivo. Xamarin. Forms Usa le API le piattaforme native per il caricamento di immagini locali, in modo che esso supporta automaticamente le soluzioni alternative se i file vengono denominati correttamente e che si trova nel progetto.

Il modo migliore per gestire le immagini poiché iOS 9 consiste nel trascinare le immagini per singole risoluzioni necessarie per il set di immagini di catalogo asset appropriati. Per altre informazioni, vedere [aggiunta di immagini da un catalogo Asset immagine Set](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Prima di iOS 9, le versioni retina dell'immagine può essere inserite nel **le risorse** cartella - due e tre volte la risoluzione con un **@2x** oppure **@3x**suffissi il nome del file prima dell'estensione di file (ad es. **myimage@2x.png**). Tuttavia, questo metodo di utilizzo di immagini in un'app per iOS è stato deprecato da Apple. Per altre informazioni, vedere [le dimensioni delle immagini e i nomi file](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Le immagini di Android risoluzione alternativo devono essere inserite in [opportunamente denominato Directory](https://developer.android.com/guide/practices/screens_support.html) nel progetto Android, come illustrato nello screenshot seguente:

[![Percorso dell'immagine multiplo con risoluzione Android](images-images/xs-highdpisolution-sml.png "percorso di Android immagine multiplo con risoluzione")](images-images/xs-highdpisolution.png#lightbox "percorso dell'immagine multiplo con risoluzione Android")

Nomi di file di immagine di piattaforma UWP [può essere aggiunto il suffisso `.scale-xxx` prima dell'estensione](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast), dove `xxx` è la percentuale di applicare all'asset, ad esempio, la scalabilità **myimage.scale 200.png**. Le immagini possono includere riferimenti nel codice o XAML senza il modificatore di scalabilità, ad esempio semplicemente **myimage.png**. La piattaforma selezionerà la scala asset appropriata più vicina corrente DPI della visualizzazione in base.

### <a name="additional-controls-that-display-images"></a>Controlli aggiuntivi che consentono di visualizzare le immagini

Alcuni controlli dispongono di proprietà che consentono di visualizzare un'immagine, ad esempio:

- [`Page`](xref:Xamarin.Forms.Page) -Qualsiasi pagina di tipo da cui deriva `Page` ha [ `Icon` ](xref:Xamarin.Forms.Page.Icon) e [ `BackgroundImage` ](xref:Xamarin.Forms.Page.BackgroundImage) proprietà, che può essere assegnata un riferimento al file locale. In determinate circostanze, ad esempio quando un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) viene visualizzato un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage), verrà visualizzata l'icona se supportato dalla piattaforma.

  > [!IMPORTANT]
  > In iOS, il [ `Page.Icon` ](xref:Xamarin.Forms.Page.Icon) proprietà non può essere popolata da un'immagine in un set di immagini di catalogo di asset. In alternativa, caricare le immagini icona per il `Page.Icon` proprietà dal **risorse** cartella nel progetto iOS.

- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) -Ha un [ `Icon` ](xref:Xamarin.Forms.MenuItem.Icon) proprietà che può essere impostata su un riferimento di file locale.
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) -Ha un [ `ImageSource` ](xref:Xamarin.Forms.ImageCell.ImageSource) recuperare proprietà che può essere impostato su un'immagine da un file locale, una risorsa incorporata o un URI.

## <a name="embedded-images"></a>Immagini incorporate

Immagini incorporate vengono anche fornite con un'applicazione (ad esempio immagini locali), ma invece di avere una copia dell'immagine nella struttura di file di ogni applicazione l'immagine del file è incorporato nell'assembly come risorsa. Questo metodo di distribuzione delle immagini è consigliato quando vengono utilizzate immagini identiche in ogni piattaforma ed è particolarmente appropriato per la creazione di componenti, come l'immagine è in bundle con il codice.

Per incorporare un'immagine in un progetto, fare doppio clic per aggiungere nuovi elementi e selezionare l'immagine/s che si desidera aggiungere. Per impostazione predefinita l'immagine presenterà **azione di compilazione: None**; questa operazione deve essere impostato su **azione di compilazione: EmbeddedResource**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](images-images/vs-buildaction.png "Impostare l'azione di compilazione: EmbeddedResource")

Il **Build Action** possono essere visualizzate e modificate nel **proprietà** finestra per un file.

In questo esempio è l'ID risorsa **WorkingWithImages.beach.jpg**.
L'IDE ha generato questa impostazione predefinita tramite la concatenazione di **Namespace predefinito** per questo progetto con il nome del file, usando un punto (.) tra ogni valore.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](images-images/xs-buildaction.png "Impostare l'azione di compilazione: EmbeddedResource")

**Azione di compilazione** possono anche essere visualizzate e modificate nel **proprietà** riempimento per un file.
Questo riquadro mostra il **ID risorsa** utilizzato per fare riferimento alla risorsa nel codice. Nella schermata seguente, il **l'ID di risorsa** viene **WorkingWithImages.beach.jpg**.
L'IDE ha generato questa impostazione predefinita tramite la concatenazione di **Namespace predefinito** per questo progetto con il nome del file, usando un punto (.) tra ogni valore.
Questo ID può essere modificato nella **le proprietà** riquadro, ma per questi esempi il valore **WorkingWithImages.beach.jpg** verrà usato.

![](images-images/xs-embeddedproperties.png "Riquadro delle proprietà di risorsa incorporata")

-----

Se si inseriscono le immagini incorporate in cartelle all'interno del progetto, i nomi delle cartelle anche separati da punti (.) nell'ID di risorsa. Spostare il **Beach** immagine in una cartella denominata **MyImages** comporterebbe un ID risorsa della **WorkingWithImages.MyImages.beach.jpg**

Passa semplicemente il codice che carica un'immagine incorporata la **l'ID di risorsa** per il [ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource*) metodo come illustrato di seguito:

```csharp
var embeddedImage = new Image { Source = ImageSource.FromResource("WorkingWithImages.beach.jpg", typeof(EmbeddedImages).GetTypeInfo().Assembly) };
```

> [!NOTE]
> Per supportare la visualizzazione di immagini incorporate in modalità di rilascio sulla piattaforma Windows universale, è necessario usare l'overload di `ImageSource.FromResource` che specifica l'assembly di origine in cui cercare l'immagine.

Attualmente non è nessuna conversione implicita per gli identificatori di risorsa. È necessario usare invece [ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource*) o `new ResourceImageSource()` caricare immagini incorporate.

Gli screenshot seguenti mostrano il risultato della visualizzazione di un'immagine incorporata in ogni piattaforma:

[![ResourceImageSource](images-images/resource-sml.png "esempio di applicazione con un'immagine incorporata")](images-images/resource.png#lightbox "applicazione visualizza un'immagine incorporata di esempio")

### <a name="using-xaml"></a>Usando XAML

Poiché non esiste alcun convertitore di tipi predefiniti da `string` a `ResourceImageSource`, questi tipi di immagini non possono essere caricati in modo nativo da XAML. Al contrario, una semplice estensione di markup XAML personalizzata può essere scritti per caricare immagini usando un **ID risorsa** specificato in XAML:

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
> Per supportare la visualizzazione di immagini incorporate in modalità di rilascio sulla piattaforma Windows universale, è necessario usare l'overload di `ImageSource.FromResource` che specifica l'assembly di origine in cui cercare l'immagine.

Per utilizzare questa estensione, aggiungere una classe personalizzata `xmlns` per XAML, usando i valori corretti di spazio dei nomi e assembly per il progetto. È quindi possibile impostare l'origine dell'immagine usando questa sintassi: `{local:ImageResource WorkingWithImages.beach.jpg}`. Seguito è riportato un esempio completo in XAML:

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

### <a name="troubleshooting-embedded-images"></a>Risoluzione dei problemi relativi a immagini incorporate

#### <a name="debugging-code"></a>Debug del codice

Perché a volte è difficile da comprendere perché una risorsa particolare immagine non viene caricata, il seguente codice di debug può essere aggiunto temporaneamente a un'applicazione per verificare che le risorse siano configurate correttamente. Verranno visualizzate tutte le note risorse incorporate nell'assembly specificato per il <span class="UIItem">Console</span> per facilitare il debug di problemi di caricamento delle risorse.

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

Per impostazione predefinita, il `ImageSource.FromResource` metodo cerca solo immagini dello stesso assembly come la chiamata del codice di `ImageSource.FromResource` (metodo). Usando il debug di codice sopra riportato è possibile determinare gli assembly che contengono una risorsa specifica modificando il `typeof()` istruzione a un `Type` noti come in ogni assembly.

Tuttavia, l'assembly di origine da cercare un'immagine incorporata può essere specificato come argomento al `ImageSource.FromResource` metodo:

```csharp
var imageSource = ImageSource.FromResource("filename.png", typeof(MyClass).GetTypeInfo().Assembly);
```

## <a name="downloading-images"></a>Download di immagini

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
var webImage = new Image { Source = ImageSource.FromUri(new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")) };
```

Il [ `ImageSource.FromUri` ](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) metodo richiede una `Uri` dell'oggetto e restituisce un nuovo [ `UriImageSource` ](xref:Xamarin.Forms.UriImageSource) che legge dal `Uri`.

È anche una conversione implicita per le stringhe URI, pertanto funzionerà anche nell'esempio seguente:

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

Gli screenshot seguenti mostrano il risultato della visualizzazione di un'immagine remota in ogni piattaforma:

[![Scaricato ImageSource](images-images/download-sml.png "visualizzazione scaricare un'immagine di applicazione di esempio")](images-images/download.png#lightbox "visualizzazione scaricare un'immagine di applicazione di esempio")

### <a name="downloaded-image-caching"></a>La memorizzazione nella cache di immagine scaricato

Oggetto [ `UriImageSource` ](xref:Xamarin.Forms.UriImageSource) supporta anche la memorizzazione nella cache delle immagini scaricate, configurate tramite le proprietà seguenti:

- [`CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) : Indica se è abilitata la memorizzazione nella cache (`true` per impostazione predefinita).
- [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) -A `TimeSpan` che definisce quanto tempo verrà archiviato in locale l'immagine.

La memorizzazione nella cache è abilitata per impostazione predefinita e archivierà l'immagine in locale per 24 ore. Per disabilitare la memorizzazione nella cache per una determinata immagine, creare un'istanza di origine dell'immagine come indicato di seguito:

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri="http://server.com/image" };
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

## <a name="icons-and-splash-screens"></a>Icone e schermate iniziali

Anche se non è correlata la [ `Image` ](xref:Xamarin.Forms.Image) visualizzazione, le icone dell'applicazione e schermate iniziali sono anche un utilizzo importante delle immagini nei progetti xamarin. Forms.

L'impostazione delle icone e schermate iniziali per le app xamarin. Forms viene eseguita in ognuno dei progetti dell'applicazione. Ciò significa che la generazione di correttamente dimensioni immagini per iOS, Android e UWP. Queste immagini devono essere denominate e che si trova in base ai requisiti di ogni piattaforme.

## <a name="icons"></a>Icone

Vedere le [iOS utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md), [Google visualizzato](https://developer.android.com/design/style/iconography.html), e [linee guida per gli asset riquadro e icona](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) per altre informazioni sulla creazione di queste risorse dell'applicazione.

Inoltre, le icone del tipo di carattere possono essere visualizzate dal [ `Image` ](xref:Xamarin.Forms.Image) visualizzazione specificando i dati dell'icona del tipo di carattere in un `FontImageSource` oggetto. Per altre informazioni, vedere [visualizzare le icone del tipo di carattere](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) nel [tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md) Guida.

## <a name="splash-screens"></a>Schermate iniziali

Solo iOS e le applicazioni UWP richiedono una schermata iniziale (detto anche un'immagine di avvio dello schermo o predefinito).

Vedere la documentazione per [iOS utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md) e [schermate iniziali](/windows/uwp/launch-resume/splash-screens/) in Windows Dev Center.

## <a name="summary"></a>Riepilogo

Xamarin. Forms offre una serie di modi diversi per includere le immagini in un'applicazione multi-piattaforma, consentendo per la stessa immagine da utilizzare tra le piattaforme o immagini specifiche della piattaforma da specificare. Immagini scaricate sono anche automaticamente memorizzato nella cache, automazione di uno scenario comune di codifica.

Immagini della schermata iniziale e l'icona dell'applicazione sono configurazione e configurato per le applicazioni non xamarin. Forms - seguono le stesse linee guida utilizzate per le app specifiche della piattaforma.

## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithImages (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/)
- [iOS utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md)
- [Android visualizzato](https://developer.android.com/design/style/iconography.html)
- [Linee guida per gli asset riquadro e icona](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
