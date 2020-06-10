---
title: "immagini in Xamarin.Forms " Description: "le immagini possono essere condivise tra più piattaforme con Xamarin.Forms , possono essere caricate in modo specifico per ogni piattaforma oppure possono essere scaricate per la visualizzazione".
ms. prod: Novell MS. AssetID: C025AB53-05CC-49BA-9815-75D6DF9E40B7 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 12/04/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="images-in-xamarinforms"></a>Immagini inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)

_Le immagini possono essere condivise tra più piattaforme con Xamarin.Forms , possono essere caricate in modo specifico per ogni piattaforma oppure possono essere scaricate per la visualizzazione._

Le immagini sono una parte essenziale della navigazione, dell'usabilità e della personalizzazione delle applicazioni. Xamarin.Formsle applicazioni devono poter condividere immagini in tutte le piattaforme, ma anche potenzialmente visualizzare immagini diverse in ogni piattaforma.

Le immagini specifiche della piattaforma sono necessarie anche per le icone e le schermate iniziali; che devono essere configurate in base alla piattaforma.

## <a name="display-images"></a>Visualizza immagini

Xamarin.FormsUsa la [`Image`](xref:Xamarin.Forms.Image) visualizzazione per visualizzare le immagini in una pagina. Dispone di due proprietà importanti:

- [`Source`](xref:Xamarin.Forms.Image.Source): Un' [`ImageSource`](xref:Xamarin.Forms.ImageSource) istanza, ovvero file, URI o risorsa, che imposta l'immagine da visualizzare.
- [`Aspect`](xref:Xamarin.Forms.Image.Aspect)-Come ridimensionare l'immagine all'interno dei limiti in cui viene visualizzata (se allungare, ritagliare o letterbox).

[`ImageSource`](xref:Xamarin.Forms.ImageSource)le istanze possono essere ottenute usando metodi statici per ogni tipo di origine dell'immagine:

- [`FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String))-Richiede un nome file o FilePath che può essere risolto in ogni piattaforma.
- [`FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)): Richiede un oggetto Uri, ad esempio.  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)-Richiede un identificatore di risorsa a un file di immagine incorporato nel progetto di libreria .NET Standard o dell'applicazione, con un' **azione di compilazione: EmbeddedResource**.
- [`FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream}))-Richiede un flusso che fornisca dati di immagine.

La [`Aspect`](xref:Xamarin.Forms.Image.Aspect) proprietà determina il modo in cui l'immagine verrà adattata all'area di visualizzazione:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): Estende l'immagine in modo da riempire completamente l'area di visualizzazione. Questa operazione può comportare la distorsione dell'immagine.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): Ritaglia l'immagine in modo da riempire l'area di visualizzazione mantenendo l'aspetto (ad esempio, nessuna distorsione).
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)-Buca l'immagine (se necessaria) in modo che l'intera immagine si trovi nell'area di visualizzazione, con uno spazio vuoto aggiunto alla parte superiore/inferiore o ai lati a seconda che l'immagine sia di grandi dimensioni o di altezza.

Le immagini possono essere caricate da un [file locale](#local-images), da una [risorsa incorporata](#embedded-images), [scaricate](#download-images)o caricate da un flusso. Inoltre, le icone dei tipi di carattere possono essere visualizzate dalla [`Image`](xref:Xamarin.Forms.Image) visualizzazione specificando i dati dell'icona del tipo di carattere in un `FontImageSource` oggetto. Per altre informazioni, vedere [visualizzare le icone](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) dei tipi di carattere nella Guida per i [tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="local-images"></a>Immagini locali

È possibile aggiungere file di immagine a ogni progetto di applicazione e farvi riferimento dal Xamarin.Forms codice condiviso. Questo metodo di distribuzione delle immagini è necessario quando le immagini sono specifiche della piattaforma, ad esempio quando si usano risoluzioni diverse su piattaforme diverse o progettazioni leggermente differenti.

Per usare una singola immagine in tutte le app, è *necessario usare lo stesso nome di file in ogni piattaforma*e deve essere un nome di risorsa Android valido (ad esempio, solo lettere minuscole, numeri, il carattere di sottolineatura e il periodo sono consentiti).

- **iOS** : il modo migliore per gestire e supportare le immagini perché IOS 9 consiste nell'usare **set di immagini del catalogo asset**, che devono contenere tutte le versioni di un'immagine necessarie per supportare vari dispositivi e i fattori di scalabilità per un'applicazione. Per altre informazioni, vedere [aggiunta di immagini a un set di immagini del catalogo asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- **Android** -posizionare le immagini nella directory **Resources/di tipo disegnatore** con l' **azione di compilazione: AndroidResource**. È anche possibile fornire versioni con DPI alta e bassa di un'immagine (nelle sottodirectory di **risorse** denominate in modo appropriato, ad esempio **ldpi**, **HDPI**e **xhdpi**).
- **Piattaforma UWP (Universal Windows Platform) (UWP)** : per impostazione predefinita, le immagini devono essere inserite nella directory radice dell'applicazione con **azione di compilazione: contenuto**. In alternativa, le immagini possono essere inserite in una directory diversa, che viene quindi specificata con una specifica della piattaforma. Per ulteriori informazioni, vedere [default image directory in Windows](~/xamarin-forms/platform/windows/default-image-directory.md).

> [!IMPORTANT]
> Prima di iOS 9, le immagini in genere venivano inserite nella cartella **Resources** con l' **azione di compilazione: BundleResource**. Tuttavia, questo metodo di uso delle immagini in un'app iOS è stato deprecato da Apple. Per altre informazioni, vedere [dimensioni delle immagini e nomi di file](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Se si rispettano queste regole per la denominazione e la selezione dei file, il codice XAML seguente consente di caricare e visualizzare l'immagine su tutte le piattaforme:

```xaml
<Image Source="waterfront.jpg" />
```

Il codice C# equivalente è il seguente:

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

Gli screenshot seguenti mostrano il risultato della visualizzazione di un'immagine locale in ogni piattaforma:

[![Applicazione di esempio che visualizza un'immagine locale](images-images/local-sml.png)](images-images/local.png#lightbox)

Per maggiore flessibilità `Device.RuntimePlatform` , è possibile usare la proprietà per selezionare un file di immagine o un percorso diverso per alcune o tutte le piattaforme, come illustrato nell'esempio di codice seguente:

```csharp
image.Source = Device.RuntimePlatform == Device.Android
                ? ImageSource.FromFile("waterfront.jpg")
                : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Per usare lo stesso nome file di immagine in tutte le piattaforme, il nome deve essere valido in tutte le piattaforme. Android drawables ha restrizioni di denominazione: sono consentiti solo lettere minuscole, numeri, caratteri di sottolineatura e periodo e per la compatibilità multipiattaforma questa operazione deve essere seguita anche in tutte le altre piattaforme. Il nome file di esempio **waterfront.png** segue le regole, ma alcuni esempi di nomi file non validi includono "Water front.png", "WaterFront.png", "water-front.png" e "wåterfront.png".

### <a name="native-resolutions-retina-and-high-dpi"></a>Risoluzioni native (retina e DPI elevato)

iOS, Android e UWP includono il supporto per risoluzioni di immagini diverse, in cui il sistema operativo sceglie l'immagine appropriata in fase di esecuzione in base alle funzionalità del dispositivo. Xamarin.FormsUsa le API delle piattaforme native per il caricamento di immagini locali, pertanto supporta automaticamente le risoluzioni alternative se i file sono denominati correttamente e si trovano nel progetto.

Il modo migliore per gestire le immagini perché iOS 9 è trascinare le immagini per ogni risoluzione necessaria per il set di immagini del catalogo asset appropriato. Per altre informazioni, vedere [aggiunta di immagini a un set di immagini del catalogo asset](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Prima di iOS 9, le versioni dell'immagine potrebbero essere posizionate nella cartella **delle risorse** , due e tre volte la risoluzione con **@2x** **@3x** suffissi o sul nome file prima dell'estensione di file, ad esempio **myimage@2x.png**). Tuttavia, questo metodo di uso delle immagini in un'app iOS è stato deprecato da Apple. Per altre informazioni, vedere [dimensioni delle immagini e nomi di file](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Le immagini di risoluzione alternativa Android devono essere inserite in [Directory con nome specifico](https://developer.android.com/guide/practices/screens_support.html) nel progetto Android, come illustrato nello screenshot seguente:

[![Percorso immagine a risoluzione multipla Android](images-images/xs-highdpisolution-sml.png)](images-images/xs-highdpisolution.png#lightbox)

I nomi dei file di immagine UWP possono essere sottoposti a [suffisso `.scale-xxx` prima dell'estensione del file](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast), dove `xxx` è la percentuale di scalabilità applicata all'asset, ad esempio **myimage.scale-200.png**. È quindi possibile fare riferimento alle immagini nel codice o in XAML senza il modificatore di scala, ad esempio solo **myimage.png**. La piattaforma selezionerà la scala delle risorse appropriata più vicina in base al valore DPI corrente della visualizzazione.

### <a name="additional-controls-that-display-images"></a>Controlli aggiuntivi che visualizzano le immagini

Alcuni controlli hanno proprietà che visualizzano un'immagine, ad esempio:

- [`Button`](xref:Xamarin.Forms.Button)dispone di una [`ImageSource`](xref:Xamarin.Forms.Button.ImageSource) proprietà che può essere impostata su un'immagine bitmap da visualizzare nell'oggetto `Button` . Per ulteriori informazioni, vedere [utilizzo di bitmap con i pulsanti](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).
- [`ImageButton`](xref:Xamarin.Forms.Button)dispone di una [`Source`](xref:Xamarin.Forms.ImageButton.Source) proprietà che può essere impostata sull'immagine da visualizzare in `ImageButton` . Per ulteriori informazioni, vedere [impostazione dell'origine dell'immagine](~/xamarin-forms/user-interface/imagebutton.md#setting-the-image-source).
- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)dispone [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) di una proprietà che può essere impostata su un'immagine caricata da un file, una risorsa incorporata, un URI o un flusso.
- [`ImageCell`](xref:Xamarin.Forms.ImageCell)dispone [`ImageSource`](xref:Xamarin.Forms.ImageCell.ImageSource) di una proprietà che può essere impostata su un'immagine recuperata da un file, una risorsa incorporata, un URI o un flusso.
- [`Page`](xref:Xamarin.Forms.Page). Qualsiasi tipo di pagina che deriva da `Page` dispone [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) di [`BackgroundImageSource`](xref:Xamarin.Forms.Page.BackgroundImageSource) proprietà e, a cui è possibile assegnare un file, una risorsa incorporata, un URI o un flusso. In determinate circostanze, ad esempio quando un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) Visualizza un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) , l'icona viene visualizzata se supportata dalla piattaforma.

  > [!IMPORTANT]
  > In iOS, la [`Page.IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) proprietà non può essere popolata da un'immagine in un set di immagini del catalogo asset. Al contrario, caricare le immagini icona per la `Page.IconImageSource` proprietà da un file, una risorsa incorporata, un URI o un flusso.

## <a name="embedded-images"></a>Immagini incorporate

Anche le immagini incorporate vengono fornite con un'applicazione, come le immagini locali, ma invece di avere una copia dell'immagine nella struttura dei file di ogni applicazione, il file di immagine viene incorporato nell'assembly come risorsa. Questo metodo di distribuzione delle immagini è consigliato quando si usano immagini identiche in ogni piattaforma ed è particolarmente adatto alla creazione di componenti, in quanto l'immagine viene inclusa nel codice.

Per incorporare un'immagine in un progetto, fare clic con il pulsante destro del mouse per aggiungere nuovi elementi e selezionare l'immagine/i che si desidera aggiungere. Per impostazione predefinita, l'immagine avrà **un'azione di compilazione: None**; è necessario impostare l'azione di **compilazione: EmbeddedResource**.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Imposta azione di compilazione su risorsa incorporata](images-images/vs-buildaction-sml.png)](images-images/vs-buildaction.png#lightbox)

L' **azione di compilazione** può essere visualizzata e modificata nella finestra **Proprietà** di un file.

In questo esempio l'ID risorsa è **WorkingWithImages.beach.jpg**.
L'IDE ha generato questa impostazione predefinita concatenando lo **spazio dei nomi predefinito** per il progetto con il nome del file, usando un punto (.) tra ogni valore.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

![](images-images/xs-buildaction.png "Set Build Action: EmbeddedResource")

È anche possibile visualizzare e modificare l' **azione di compilazione** nel riquadro delle **Proprietà** di un file.
Questo riquadro Mostra l' **ID risorsa** usato per fare riferimento alla risorsa nel codice. Nella schermata seguente l' **ID risorsa** è **WorkingWithImages.beach.jpg**.
L'IDE ha generato questa impostazione predefinita concatenando lo **spazio dei nomi predefinito** per il progetto con il nome del file, usando un punto (.) tra ogni valore.
Questo ID può essere modificato nel riquadro **Proprietà** , ma per questi esempi verrà usato il valore **WorkingWithImages.beach.jpg** .

[![Riquadro delle proprietà delle risorse incorporate](images-images/xs-embeddedproperties-sml.png)](images-images/xs-embeddedproperties.png#lightbox)

-----

Se si inseriscono le immagini incorporate in cartelle all'interno del progetto, i nomi delle cartelle sono separati anche da punti (.) nell'ID risorsa. Se si trasferisce l'immagine **beach.jpg** in una cartella denominata **Immagini** , si ottiene un ID risorsa di **WorkingWithImages.MyImages.beach.jpg**

Il codice per caricare un'immagine incorporata passa semplicemente l' **ID risorsa** al [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) metodo, come illustrato di seguito:

```csharp
var embeddedImage = new Image {
      Source = ImageSource.FromResource(
        "WorkingWithImages.beach.jpg",
        typeof(EmbeddedImages).GetTypeInfo().Assembly
      ) };
```

> [!NOTE]
> Per supportare la visualizzazione di immagini incorporate in modalità versione sul piattaforma UWP (Universal Windows Platform), è necessario usare l'overload di `ImageSource.FromResource` che specifica l'assembly di origine in cui cercare l'immagine.

Attualmente non esiste alcuna conversione implicita per gli identificatori di risorsa. È invece necessario utilizzare [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) o `new ResourceImageSource()` per caricare immagini incorporate.

Gli screenshot seguenti mostrano il risultato della visualizzazione di un'immagine incorporata in ogni piattaforma:

[![Applicazione di esempio che visualizza un'immagine incorporata](images-images/resource-sml.png)](images-images/resource.png#lightbox)

### <a name="xaml"></a>XAML

Poiché non esiste alcun convertitore di tipi incorporato da `string` a `ResourceImageSource` , questi tipi di immagini non possono essere caricati in modo nativo da XAML. È invece possibile scrivere un'estensione di markup XAML personalizzata per caricare immagini usando un **ID risorsa** specificato in XAML:

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

Per usare questa estensione, aggiungere un oggetto personalizzato `xmlns` a XAML, usando lo spazio dei nomi e i valori degli assembly corretti per il progetto. È quindi possibile impostare l'origine dell'immagine usando la sintassi seguente: `{local:ImageResource WorkingWithImages.beach.jpg}` . Di seguito è riportato un esempio di XAML completo:

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

Poiché è talvolta difficile comprendere perché una determinata risorsa immagine non viene caricata, è possibile aggiungere temporaneamente il codice di debug seguente a un'applicazione per verificare che le risorse siano configurate correttamente. Tutte le risorse note incorporate nell'assembly specificato vengono restituite alla **console** per facilitare il debug dei problemi di caricamento delle risorse.

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

Per impostazione predefinita, il `ImageSource.FromResource` metodo cerca solo le immagini nello stesso assembly del codice che chiama il `ImageSource.FromResource` metodo. Utilizzando il codice di debug precedente è possibile determinare quali assembly contengono una risorsa specifica modificando l' `typeof()` istruzione in un oggetto `Type` noto come in ogni assembly.

Tuttavia, l'assembly di origine cercato per un'immagine incorporata può essere specificato come argomento del `ImageSource.FromResource` Metodo:

```csharp
var imageSource = ImageSource.FromResource("filename.png",
            typeof(MyClass).GetTypeInfo().Assembly);
```

## <a name="download-images"></a>Scaricare immagini

Le immagini possono essere scaricate automaticamente per la visualizzazione, come illustrato nel codice XAML seguente:

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

Il codice C# equivalente è il seguente:

```csharp
var webImage = new Image {
     Source = ImageSource.FromUri(
        new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")
     ) };
```

Il [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) metodo richiede un `Uri` oggetto e restituisce un nuovo oggetto [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) che legge da `Uri` .

È anche disponibile una conversione implicita per le stringhe URI, quindi anche l'esempio seguente funzionerà:

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

Gli screenshot seguenti mostrano il risultato della visualizzazione di un'immagine remota in ogni piattaforma:

[![Applicazione di esempio che visualizza un'immagine scaricata](images-images/download-sml.png)](images-images/download.png#lightbox)

### <a name="downloaded-image-caching"></a>Memorizzazione nella cache delle immagini scaricata

Un [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) supporta inoltre la memorizzazione nella cache delle immagini scaricate, configurate tramite le proprietà seguenti:

- [`CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled)-Se la memorizzazione nella cache è abilitata ( `true` per impostazione predefinita).
- [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity)-Oggetto `TimeSpan` che definisce per quanto tempo l'immagine verrà archiviata localmente.

La memorizzazione nella cache è abilitata per impostazione predefinita e archivia l'immagine in locale per 24 ore. Per disabilitare la memorizzazione nella cache per una particolare immagine, creare un'istanza dell'origine dell'immagine come indicato di seguito:

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri = new Uri("http://server.com/image") };
```

Per impostare un periodo di memorizzazione nella cache specifico, ad esempio 5 giorni, creare un'istanza dell'origine dell'immagine come indicato di seguito:

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

La memorizzazione nella cache incorporata rende molto facile supportare scenari come lo scorrimento di elenchi di immagini, in cui è possibile impostare (o associare) un'immagine in ogni cella e lasciare che la cache predefinita si occupi di ricaricare l'immagine quando viene eseguito lo scorrimento della cella nella visualizzazione.

## <a name="animated-gifs"></a>Gif animate

Xamarin.Formsinclude il supporto per la visualizzazione di gif animate di piccole dimensioni. Questa operazione viene eseguita impostando la [`Image.Source`](xref:Xamarin.Forms.Image.Source) proprietà su un file GIF animato:

```xaml
<Image Source="demo.gif" />
```

> [!IMPORTANT]
> Sebbene il supporto GIF animato in Xamarin.Forms includa la possibilità di scaricare file, non supporta la memorizzazione nella cache o il flusso di gif animate.

Per impostazione predefinita, quando un GIF animato viene caricato, non verrà riprodotto. Questo perché la `IsAnimationPlaying` proprietà, che controlla se un GIF animato viene riprodotto o arrestato, ha un valore predefinito di `false` . Questa proprietà, di tipo `bool` , è supportata da un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetto, il che significa che può essere la destinazione di un data binding e lo stile.

Pertanto, quando viene caricata un'immagine GIF animata, questa non verrà riprodotta finché la `IsAnimationPlaying` proprietà non viene impostata su `true` . È quindi possibile arrestare la riproduzione impostando la `IsAnimationPlaying` proprietà su `false` . Si noti che questa proprietà non ha alcun effetto quando si visualizza un'origine di immagine non GIF.

> [!NOTE]
> In Android, il supporto GIF animato richiede che l'applicazione usi renderer veloci e non funzioni se si è scelto di usare i renderer legacy.
> In UWP il supporto GIF animato richiede una versione minima dell'aggiornamento dell'anniversario di Windows 10 (versione 1607).

## <a name="icons-and-splash-screens"></a>Icone e schermate iniziali

Sebbene non sia correlato alla [`Image`](xref:Xamarin.Forms.Image) visualizzazione, le icone dell'applicazione e le schermate iniziali rappresentano anche un importante utilizzo delle immagini nei Xamarin.Forms progetti.

L'impostazione delle icone e delle schermate iniziali per Xamarin.Forms le app viene eseguita in ogni progetto di applicazione. Ciò significa generare immagini dimensionate correttamente per iOS, Android e UWP. Queste immagini devono essere denominate e situate in base ai requisiti di ogni piattaforma.

## <a name="icons"></a>Icone

Per altre informazioni sulla creazione di queste risorse dell'applicazione, vedere [iOS uso](~/ios/app-fundamentals/images-icons/index.md)delle linee guida relative a immagini, [iconografia Google](https://developer.android.com/design/style/iconography.html)e [UWP per asset di riquadro e icone](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) .

Inoltre, le icone dei tipi di carattere possono essere visualizzate dalla [`Image`](xref:Xamarin.Forms.Image) visualizzazione specificando i dati dell'icona del tipo di carattere in un `FontImageSource` oggetto. Per altre informazioni, vedere [visualizzare le icone](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) dei tipi di carattere nella Guida per i [tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="splash-screens"></a>Schermate iniziali

Solo le applicazioni iOS e UWP richiedono una schermata iniziale (detta anche schermata iniziale o immagine predefinita).

Vedere la documentazione per [iOS uso di immagini](~/ios/app-fundamentals/images-icons/index.md) e [schermate](/windows/uwp/launch-resume/splash-screens/) iniziali in Windows Dev Center.

## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithImages (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)
- [iOS uso delle immagini](~/ios/app-fundamentals/images-icons/index.md)
- [Iconografia Android](https://developer.android.com/design/style/iconography.html)
- [Linee guida per asset riquadro e asset icona](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
