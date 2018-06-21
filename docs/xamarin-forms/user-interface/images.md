---
title: Immagini in xamarin. Forms
description: Le immagini possono essere condivisi tra piattaforme con xamarin. Forms, possono essere caricati in modo specifico per ogni piattaforma o può essere scaricati per la visualizzazione.
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: b0fd644f1f3b49a949a3a9ba9aca4c0770f17013
ms.sourcegitcommit: c2d1249cb67b877ee0d9cb8d095ec66fd51d8c31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36291351"
---
# <a name="images-in-xamarinforms"></a>Immagini in xamarin. Forms

_Le immagini possono essere condivisi tra piattaforme con xamarin. Forms, possono essere caricati in modo specifico per ogni piattaforma o può essere scaricati per la visualizzazione._

Le immagini sono una parte fondamentale di navigazione dell'applicazione, usabilità e personalizzazione. Xamarin. Forms che le applicazioni debbano essere in grado di condividere immagini tra tutte le piattaforme, ma anche potenzialmente visualizzare immagini differenti in ogni piattaforma.

Le immagini specifiche della piattaforma sono necessari anche per le icone e schermate iniziali; questi devono essere configurate per ogni piattaforma.

Questo documento vengono illustrati gli argomenti seguenti:

- [ **Le immagini locale** ](#Local_Images) -visualizzazione di immagini fornito con l'applicazione, comprensivo risoluzione risoluzioni native, ad esempio iOS Retina, Android o UWP versioni ad alta risoluzione di un'immagine.
- [ **Immagini incorporate** ](#Embedded_Images) -visualizzazione di immagini incorporato come risorsa di assembly.
- [ **Download di immagini** ](#Downloading_Images) : download e la visualizzazione di immagini.
- [ **Icone e schermate iniziali** ](#Icons_and_splashscreens) -icone specifiche della piattaforma e le immagini di avvio.

## <a name="displaying-images"></a>Visualizzazione di immagini

Xamarin. Forms Usa il [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) visualizzazione per visualizzare le immagini in una pagina. Ha due proprietà importanti:

- [`Source`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) -Un [ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) istanza, File, Uri o risorsa, che imposta l'immagine da visualizzare.
- [`Aspect`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) -Procedura di ridimensionare l'immagine all'interno dei limiti che vengano visualizzati all'interno (se stretch, Ritaglia o letterbox).

[`ImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) le istanze possono essere ottenute utilizzando metodi statici per ogni tipo di origine dell'immagine:

- [`FromFile`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromFile/p/System.String/) -Richiede un nome file o percorso file che può essere risolto in ciascuna piattaforma.
- [`FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) -Richiede un oggetto Uri, ad esempio.  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) -Richiede un identificatore di risorsa a un file di immagine incorporato all'applicazione o un progetto di libreria .NET Standard, con un **EmbeddedResource: azione di compilazione**.
- [`FromStream`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromStream/p/System.Func%7BSystem.IO.Stream%7D/) -Richiede un flusso che fornisce i dati dell'immagine.

Il [ `Aspect` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) proprietà determina come l'immagine verrà adattata in dimensioni dell'area di visualizzazione:

- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.Fill/) -Estende l'immagine per completamente ed esattamente riempire l'area di visualizzazione. Questo può comportare l'immagine venga distorta.
- [`AspectFill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFill/) -Ritaglia l'immagine in modo da riempire l'area di visualizzazione mantenendo l'aspetto (ie. Nessuna distorsione).
- [`AspectFit`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFit/) -Consegna l'immagine (se richiesto) in modo che l'intera immagine rientri nell'area di visualizzazione, uno spazio vuoto aggiunto dall'alto al basso o i lati, a seconda se l'immagine è larghezza e un'altezza.

Le immagini possono essere caricate da un [file locale](#Local_Images_in_Xaml), un [risorsa incorporata](#embedded_images), o [scaricato](#Downloading_Images).

<a name="Local_Images" />

## <a name="local-images"></a>Immagini locale

File di immagine possono essere aggiunti a ogni progetto di applicazione e a cui fa riferimento da codice condiviso di xamarin. Forms. Usare una singola immagine in tutte le app, *lo stesso nome file deve essere utilizzato in ogni piattaforma*, e deve essere un nome di risorsa di Android valido (ie. sono consentiti solo lettere minuscole, numeri, il carattere di sottolineatura e il periodo).

- **iOS** : il preferito per gestire e supportare immagini poiché iOS 9 consiste nell'utilizzare **insiemi di immagini di catalogo di Asset**, che deve contenere tutte le versioni di un'immagine che sono necessari per supportare vari dispositivi e fattori per ridimensionare un applicazione. Per ulteriori informazioni, vedere [aggiunta di immagini per un Asset immagine cataloghi](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- **Android** -inserimento di immagini nel **risorse/drawable** directory con **azione di compilazione: AndroidResource**. È possibile fornire anche versioni ad alta e bassa risoluzione di un'immagine (in denominato in modo appropriato **risorse** le sottodirectory, ad esempio **drawable ldpi**, **drawable hdpi**e **drawable xhdpi**).
- **Piattaforma UWP (Universal Windows)** -inserimento di immagini nella directory radice dell'applicazione con **azione di compilazione: contenuto**.

> [!IMPORTANT]
> Prima di iOS 9, le immagini in genere sono state inserite nel **risorse** cartella con **azione di compilazione: BundleResource**. Tuttavia, questo metodo di utilizzo delle immagini in un'app per iOS è stato deprecato da Apple. Per ulteriori informazioni, vedere [dimensioni delle immagini e i nomi file](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Rispettare le regole per la denominazione dei file e il posizionamento consente il codice XAML seguente caricare e visualizzare l'immagine in tutte le piattaforme:

```xaml
<Image Source="waterfront.jpg" />
```

Il codice c# equivalente è il seguente:

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

Le schermate seguenti mostrano il risultato della visualizzazione di un'immagine locale in ogni piattaforma:

[![ImageSource locale](images-images/local-sml.png "visualizzare un'immagine locale applicazione di esempio")](images-images/local.png#lightbox "visualizzare un'immagine locale applicazione di esempio")

Per una maggiore flessibilità di `Device.RuntimePlatform` proprietà può essere utilizzata per selezionare un file di immagine diverso o un percorso per alcune o tutte le piattaforme, come illustrato nell'esempio di codice:

```csharp
image.Source = Device.RuntimePlatform == Device.Android ? ImageSource.FromFile("waterfront.jpg") : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Per utilizzare lo stesso nome di file di immagine per tutte le piattaforme, il nome deve essere valido in tutte le piattaforme. Drawables Android hanno limitazioni di denominazione: sono consentiti solo lettere minuscole, numeri, caratteri di sottolineatura e periodo – e per compatibilità multipiattaforma deve essere seguito su tutte le altre piattaforme troppo. Il nome del file di esempio **waterfront.png** segue le regole, ma gli esempi di nomi di file non validi includono "acqua front.png", "WaterFront.png", "acqua front.png" e "wåterfront.png".

<a name="Native_Resolutions" />

### <a name="native-resolutions-retina-and-high-dpi"></a>Risoluzioni native (Retina e ad alta risoluzione)

iOS, Android e UWP includono il supporto per diverse risoluzioni disponibili, in cui il sistema operativo sceglie l'immagine appropriata in fase di esecuzione in base alle funzionalità del dispositivo. Xamarin. Forms Usa API le piattaforme native per il caricamento di immagini locale, pertanto supporta le soluzioni alternative automaticamente se i file sono correttamente denominati e si trova nel progetto.

Il modo migliore per gestire le immagini da iOS 9 consiste nel trascinare le immagini per singole risoluzioni necessarie al set di immagini catalogo di asset appropriato. Per ulteriori informazioni, vedere [aggiunta di immagini per un Asset immagine cataloghi](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Prima di iOS 9, versioni retina dell'immagine è possibile posizionare il **risorse** cartella - due e tre volte alla risoluzione con un **@2x** o **@3x**suffissi, il nome del file prima dell'estensione di file (ad es. **myimage@2x.png**). Tuttavia, questo metodo di utilizzo delle immagini in un'app per iOS è stato deprecato da Apple. Per ulteriori informazioni, vedere [dimensioni delle immagini e i nomi file](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Immagini Android risoluzione alternativo devono essere inserite in [directory speciale denominata](http://developer.android.com/guide/practices/screens_support.html) nel progetto Android, come illustrato nella schermata seguente:

[![Percorso dell'immagine di risoluzione più Android](images-images/xs-highdpisolution-sml.png "percorso dell'immagine di risoluzione più Android")](images-images/xs-highdpisolution.png#lightbox "percorso dell'immagine di risoluzione più Android")

I nomi di file immagine di piattaforma UWP [può essere aggiunto il suffisso `.scale-xxx` prima dell'estensione](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast), dove `xxx` è la percentuale di scala applicato all'asset, ad esempio **myimage.scale 200.png**. Le immagini sono quindi possibile fare riferimento nel codice o XAML, senza il modificatore di scala, ad esempio semplicemente **myimage.png**. La piattaforma selezionerà la scala asset appropriato più vicino in base a DPI corrente della visualizzazione.

### <a name="additional-controls-that-display-images"></a>Controlli aggiuntivi che consentono di visualizzare immagini

Alcuni controlli sono di proprietà che consentono di visualizzare un'immagine, ad esempio:

- [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) -Qualsiasi pagina di tipo che deriva da `Page` è [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) e [ `BackgroundImage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.BackgroundImage/) proprietà, è possibile assegnare un riferimento al file locale. In determinate circostanze, ad esempio quando un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) viene visualizzato un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/), l'icona verrà visualizzata se è supportata dalla piattaforma.

  > [!IMPORTANT]
  > In iOS, il [ `Page.Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) proprietà non può essere popolata da un'immagine in un set di immagini di catalogo di asset. Invece di caricare le immagini icona per il `Page.Icon` proprietà il **risorse** cartella nel progetto iOS.

- [`ToolbarItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) -Ha un [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) proprietà che può essere impostato su un riferimento file locale.
- [`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/) -Ha un [ `ImageSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ImageCell.ImageSource/) recuperare proprietà che può essere impostata su un'immagine da un file locale, una risorsa incorporata o un URI.

<a name="embedded_images" />

## <a name="embedded-images"></a>Immagini incorporate

Immagini incorporate vengono anche fornite con un'applicazione (ad esempio immagini locale), ma anziché una copia dell'immagine nella struttura di file dell'applicazione ogni l'immagine del file è incorporato nell'assembly come risorsa. Questo metodo di distribuzione delle immagini è particolarmente indicato per la creazione di componenti, come l'immagine viene fornito con il codice.

Per incorporare un'immagine in un progetto, fare doppio clic per aggiungere nuovi elementi e selezionare l'immagine/s che si desidera aggiungere. Per impostazione predefinita l'immagine abbia **azione di compilazione: nessuna**; questa operazione deve essere impostato su **azione di compilazione: EmbeddedResource**.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](images-images/vs-buildaction.png "Impostare l'azione di compilazione: EmbeddedResource")

Il **azione di compilazione** possono essere visualizzate e modificate nel **proprietà** finestra per un file.

In questo esempio è l'ID risorsa **WorkingWithImages.beach.jpg**.
L'IDE ha generato il valore predefinito tramite la concatenazione di **Namespace predefinito** per questo progetto con il nome del file, utilizzando un punto (.) tra ogni valore.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![](images-images/xs-buildaction.png "Impostare l'azione di compilazione: EmbeddedResource")

**Azione di compilazione** possono inoltre essere visualizzate e modificate nel **proprietà** riempimento per un file.
Questo riquadro mostra il **ID risorsa** utilizzato per fare riferimento alla risorsa nel codice. Nella schermata seguente, il **ID risorsa** è **WorkingWithImages.beach.jpg**.
L'IDE ha generato il valore predefinito tramite la concatenazione di **Namespace predefinito** per questo progetto con il nome del file, utilizzando un punto (.) tra ogni valore.
Questo ID può essere modificato nel **proprietà** riempimento, ma per questi esempi il valore **WorkingWithImages.beach.jpg** verrà utilizzato.

![](images-images/xs-embeddedproperties.png "Riquadro Proprietà risorsa incorporata")

-----

Se si inseriscono immagini incorporate in cartelle all'interno del progetto, i nomi delle cartelle sono anche separati da punti (.) nell'ID di risorsa. Lo spostamento di **Beach** immagine in una cartella denominata **MyImages** darà origine a una risorsa con ID di **WorkingWithImages.MyImages.beach.jpg**

Il codice per caricare un'immagine incorporata passa semplicemente il **ID risorsa** per il [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) metodo come illustrato di seguito:

```csharp
var embeddedImage = new Image { Source = ImageSource.FromResource("WorkingWithImages.beach.jpg", typeof(EmbeddedImages).GetTypeInfo().Assembly) };
```

> [!NOTE]
> Per supportare la visualizzazione di immagini incorporate in modalità di rilascio sulla piattaforma Windows universale, è necessario utilizzare l'overload del `ImageSource.FromResource` che specifica l'assembly di origine in cui eseguire la ricerca per l'immagine.

Non è attualmente presente alcuna conversione implicita per gli identificatori di risorsa. In alternativa, è necessario utilizzare [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) o `new ResourceImageSource()` per caricare immagini incorporate.

Le schermate seguenti mostrano il risultato della visualizzazione di un'immagine incorporata in ogni piattaforma:

[![ResourceImageSource](images-images/resource-sml.png "la visualizzazione di un'immagine incorporata di applicazione di esempio")](images-images/resource.png#lightbox "la visualizzazione di un'immagine incorporata di applicazione di esempio")

<a name="Embedded_Images_in_Xaml" />

### <a name="using-xaml"></a>Utilizzo di XAML

Poiché non esiste alcun convertitore di tipi incorporati da `string` a `ResourceImageSource`, questi tipi di immagini non è possibile caricare in modo nativo da XAML. Al contrario, una semplice estensione di markup XAML personalizzata può essere scritti per caricare immagini con un **ID risorsa** specificato in XAML:

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
> Per supportare la visualizzazione di immagini incorporate in modalità di rilascio sulla piattaforma Windows universale, è necessario utilizzare l'overload del `ImageSource.FromResource` che specifica l'assembly di origine in cui eseguire la ricerca per l'immagine.

Utilizzare questa estensione aggiunge un oggetto personalizzato `xmlns` in XAML, usando i valori corretti di spazio dei nomi e assembly per il progetto. È quindi possibile impostare l'origine dell'immagine utilizzando questa sintassi: `{local:ImageResource WorkingWithImages.beach.jpg}`. Seguito è riportato un esempio completo in XAML:

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

### <a name="troubleshooting-embedded-images"></a>Risoluzione dei problemi delle immagini incorporate

<a name="Debugging_Embedded_Images" />

#### <a name="debugging-code"></a>Debug del codice

Poiché talvolta è difficile comprendere perché una risorsa particolare immagine non è caricata, il seguente codice di debug aggiungere temporaneamente a un'applicazione per verificare che le risorse siano configurate correttamente. Verranno visualizzate tutte note risorse incorporate nell'assembly specificato per il <span class="UIItem">Console</span> per facilitare il debug di problemi di caricamento delle risorse.

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

Per impostazione predefinita, il `ImageSource.FromResource` metodo esegue la ricerca solo per le immagini nello stesso assembly a chiamare il codice di `ImageSource.FromResource` metodo. Utilizzando il debug di codice sopra riportato è possibile determinare gli assembly che contengono una risorsa specifica modificando il `typeof()` istruzione a un `Type` noti come in ogni assembly.

L'assembly di origine da cercare un'immagine incorporata può tuttavia essere specificato come argomento per il `ImageSource.FromResource` metodo:

```csharp
var imageSource = ImageSource.FromResource("filename.png", typeof(MyClass).GetTypeInfo().Assembly);
```

<a name="Downloading_Images" />

## <a name="downloading-images"></a>Download di immagini

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

Il codice c# equivalente è il seguente:

```csharp
var webImage = new Image { Source = ImageSource.FromUri(new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")) };
```

Il [ `ImageSource.FromUri` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) metodo richiede un `Uri` dell'oggetto e restituisce un nuovo [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) che legge dal `Uri`.

È disponibile una conversione implicita per le stringhe URI, pertanto funzionerà anche nell'esempio seguente:

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

Le schermate seguenti mostrano il risultato della visualizzazione di un'immagine remota in ogni piattaforma:

[![Scaricato ImageSource](images-images/download-sml.png "la visualizzazione di un'immagine scaricata applicazione di esempio")](images-images/download.png#lightbox "la visualizzazione di un'immagine scaricata applicazione di esempio")

<a name="Image_Caching" />

### <a name="downloaded-image-caching"></a>La memorizzazione nella cache di immagine scaricato

Oggetto [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) supporta inoltre la memorizzazione nella cache delle immagini scaricate, configurate tramite le proprietà seguenti:

- [`CachingEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CachingEnabled/) -Se è abilitata la memorizzazione nella cache (`true` per impostazione predefinita).
- [`CacheValidity`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CacheValidity/) -A `TimeSpan` che definisce quanto tempo verrà archiviata in locale l'immagine.

La memorizzazione nella cache è abilitata per impostazione predefinita e archivierà l'immagine localmente per 24 ore. Per disabilitare la memorizzazione nella cache per una particolare immagine, creare un'istanza di origine dell'immagine come indicato di seguito:

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

La memorizzazione nella cache predefinito è molto semplice supportare gli scenari ad esempio lo scorrimento di elenchi di immagini, in cui è possibile impostare (o associare) un'immagine in ogni cella e fare in modo che la cache predefinita nuovamente il caricamento dell'immagine quando la cella viene scorso nuovamente nella visualizzazione.

<a name="Icons_and_splashscreens" />

## <a name="icons-and-splashscreens"></a>Icone e schermate iniziali

Mentre non è correlata la [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) vista, applicazione di icone e schermate iniziali sono inoltre un utilizzo importante delle immagini in progetti di xamarin. Forms.

Impostazione di icone e schermate iniziali per le app xamarin. Forms viene svolto in ognuno dei progetti di applicazione. Ciò significa correttamente la generazione di immagini per iOS, Android e UWP di dimensioni. Queste immagini devono essere denominate e si trova in base ai requisiti di ogni piattaforma.

## <a name="icons"></a>Icone

Vedere il [iOS utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md), [Google visualizzato](http://developer.android.com/design/style/iconography.html), e [linee guida per gli asset di sezioni e icone](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) per ulteriori informazioni sulla creazione di queste risorse dell'applicazione.

## <a name="splashscreens"></a>Schermate iniziali

Solo iOS e le applicazioni UWP richiedono un splashscreen (detto anche un'immagine di avvio dello schermo o predefinito).

Consultare la documentazione per [iOS utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md) e [schermate iniziali](/windows/uwp/launch-resume/splash-screens/) in Windows Dev Center.

## <a name="summary"></a>Riepilogo

Xamarin. Forms offre una serie di modi per includere le immagini in un'applicazione multipiattaforma, consentendo di per la stessa immagine da utilizzare tra le piattaforme o per le immagini specifiche della piattaforma essere specificato. Immagini scaricate vengono anche automaticamente memorizzati nella cache, l'automazione in uno scenario comune di codifica.

Le immagini icona e schermata iniziale dell'applicazione sono di configurazione e configurato per le applicazioni non xamarin. Forms - seguono le stesse linee guida usata per le app specifiche della piattaforma.

## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithImages (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/)
- [iOS utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md)
- [Android visualizzato](http://developer.android.com/design/style/iconography.html)
- [Linee guida per gli asset di sezioni e icone](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
