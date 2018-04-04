---
title: Riepilogo del capitolo 13. Bitmap
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 76551057abc1abdd150591c0a1be39e9f68c4278
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-13-bitmaps"></a>Riepilogo del capitolo 13. Bitmap

Di xamarin. Forms [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento consente di visualizzare una bitmap. Tutte le piattaforme di xamarin. Forms supportano i formati di file JPEG, PNG, GIF e BMP.

Le bitmap in xamarin. Forms provengono da quattro posizioni:

- Sul web come specificato da un URL
- Incorporato come risorsa nella libreria di classi portabile comuni
- Incorporato come risorsa nei progetti di applicazione della piattaforma
- Da qualsiasi posizione in cui possono fare riferimento .NET `Stream` dell'oggetto, tra cui `MemoryStream`

Risorse bitmap nella libreria di classi Portabile sono indipendenti dalla piattaforma, mentre le risorse di bitmap nei progetti di piattaforma sono specifici della piattaforma.

La bitmap viene specificata impostando il [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) proprietà di `Image` a un oggetto di tipo [ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/), una classe astratta con tre derivati:

- [`UriImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) per l'accesso a una bitmap sul web in base a un `Uri` oggetto impostato per il relativo [ `Uri` ](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.Uri/) proprietà
- [`FileImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.FileImageSource/) per l'accesso a una mappa di bit archiviato in un progetto di applicazione della piattaforma basate su un percorso di file e cartelle, impostato il relativo [ `File` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FileImageSource.File/) proprietà
- [`StreamImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.StreamImageSource/) per il caricamento di una bitmap utilizzando .NET `Stream` l'oggetto specificato, restituendo un `Stream` da un `Func` impostato su relativo [ `Stream` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StreamImageSource.Stream/) proprietà

In alternativa (e più comunemente) è possibile utilizzare i seguenti metodi statici del `ImageSource` classe tutti che restituiscono `ImageSource` oggetti:

- [`ImageSource.FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) per l'accesso a una bitmap sul web in base a un `Uri` oggetto
- [`ImageSource.FromResource`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) per l'accesso a una mappa di bit archiviato come una risorsa incorporata nell'applicazione PCL, o [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/System.Type/) o [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/System.Reflection.Assembly/) per accedere a una bitmap in un altro assembly di origine
- [`ImageSource.FromFile`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromFile/p/System.String/) per l'accesso a una bitmap da un progetto di applicazione della piattaforma
- [`ImageSource.FromStream`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromStream/p/System.Func%7BSystem.IO.Stream%7D/) per il caricamento di una bitmap in base a un `Stream` oggetto

È disponibile un equivalente della classe di `Image.FromResource` metodi. La `UriImageSource` classe è utile se si desidera controllare la memorizzazione nella cache. La `FileImageSource` classe è utile in XAML. `StreamImageSource` è utile per il caricamento asincrono di `Stream` oggetti, mentre `ImageSource.FromStream` è sincrono.

## <a name="platform-independent-bitmaps"></a>Bitmap indipendente dalla piattaforma

Il [ **WebBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) progetto carica un'immagine bitmap tramite il web utilizzando `ImageSource.FromUri`. Il `Image` è impostato sul `Content` proprietà del `ContentPage`, pertanto è vincolato alla dimensione della pagina. Indipendentemente dalle dimensioni della bitmap, un vincolata `Image` elemento viene estesa per le dimensioni del relativo contenitore e la bitmap viene visualizzata in dimensioni massime all'interno di `Image` elemento mantenendo proporzioni della bitmap. Aree del `Image` oltre la bitmap può essere colorata con [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/).

Il [ **WebBitmapXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) esempio è simile, ma imposta semplicemente il `Source` proprietà all'URL. La conversione viene gestita dal [ `ImageSourceConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSourceConverter/) classe.

### <a name="fit-and-fill"></a>Adattamento e riempimento

È possibile controllare come la bitmap viene estesa impostando il [ `Aspect` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) proprietà del `Image` a uno dei seguenti membri del [ `Aspect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Aspect/) enumerazione:

- [`AspectFit`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFit/): rispetta proporzioni (impostazione predefinita)
- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.Fill/): riempie l'area, non rispetta le proporzioni
- [`AspectFill`](https://developer.xamarin.com/api/type/Xamarin.Forms.Aspect.AspectFill/): riempie l'area ma rispetta proporzioni, eseguita da parte della bitmap di ritaglio

### <a name="embedded-resources"></a>Risorse incorporate

Per una libreria di classi Portabile o in una cartella nella libreria di classi Portabile, è possibile aggiungere un file bitmap. Assegnargli un **azione di compilazione** di **EmbeddedResource**. Il [ **ResourceBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) esempio viene illustrato come utilizzare `ImageSource.FromResource` per caricare il file. Il nome della risorsa passato al metodo è costituito il nome dell'assembly, seguito da un punto, seguiti dal nome di cartella facoltativa e un punto, seguiti dal nome di file.

I set di programma il `VerticalOptions` e `HorizontalOptions` le proprietà del `Image` a `LayoutOptions.Center`, rendendo il `Image` elemento non vincolato. Il `Image` e la mappa di bit sono le stesse dimensioni:

- In iOS e Android, il `Image` è la dimensione in pixel della bitmap. È presente un mapping uno a uno tra bitmap e pixel sullo schermo.
- Nelle piattaforme Windows Runtime, il `Image` è la dimensione in pixel della bitmap in unità indipendenti dal dispositivo. Nella maggior parte dei dispositivi, ciascun pixel della bitmap occupa più pixel sullo schermo.

Il [ **StackedBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) esempio inserisce un `Image` in verticale `StackLayout` in XAML. Un'estensione di markup denominata [ `ImageResourceExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) consente di fare riferimento alla risorsa incorporata in XAML. Questa classe consente di caricare solo le risorse dall'assembly in cui si trova, pertanto non può essere inserito in una libreria.

### <a name="more-on-sizing"></a>Ulteriori informazioni sul ridimensionamento

È spesso opportuno bitmap di dimensioni in modo coerente tra tutte le piattaforme.
Sperimentare **StackedBitmap**, è possibile impostare un `WidthRequest` sul `Image` elemento verticale `StackLayout` alle dimensioni coerenti tra le piattaforme, ma è possibile solo ridurre le dimensioni utilizzando questa tecnica.

È inoltre possibile impostare il `HeightRequest` per rendere l'immagine di dimensioni coerente nelle piattaforme, ma la larghezza della bitmap vincolata limiterà la versatilità di questa tecnica. Per un'immagine in verticale `StackLayout`, `HeightRequest` deve essere evitato.

L'approccio migliore consiste nell'iniziare con una bitmap maggiore della larghezza del telefono in unità indipendenti dal dispositivo e impostare `WidthRequest` la larghezza desiderata in unità indipendenti dal dispositivo. Questa funzionalità viene illustrata la [ **DeviceIndBitmapSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize) esempio.

Il [ **MadTeaParty** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) Visualizza capitolo 7 di Lewis Carroll *operazioni di Alice in meraviglie* con le illustrazioni originale da John Tenniel:

[![Tripla schermata dell'entità di tè mad](images/ch13fg16-small.png "Mad testo del libro tè entità Hatters")](images/ch13fg16-large.png#lightbox "Mad testo del libro tè entità Hatters")

### <a name="browsing-and-waiting"></a>Esplorazione e in attesa

Il [ **ImageBrowser** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) esempio consente all'utente di esplorare le immagini predefinite memorizzate sul sito web di Xamarin. Viene utilizzato .NET `WebRequest` classe scaricare un file JSON con l'elenco di immagini bitmap.

Il programma utilizza una [ `ActivityIndicator` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/) per indicare che un elemento in corso. Poiché ogni bitmap sta caricando, di sola lettura [ `IsLoading` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.IsLoading/) proprietà di `Image` è `true`. Il `IsLoading` proprietà è supportata da una proprietà associabile, pertanto un `PropertyChanged` evento viene generato quando cambia la proprietà. Associa un gestore per questo evento, il programma e Usa l'impostazione corrente di `IsLoaded` per impostare il [ `IsRunning` ](https://api/property/Xamarin.Forms.ActivityIndicator.IsRunning/) proprietà del `ActivityIndicator`.

## <a name="streaming-bitmaps"></a>Streaming di bitmap

Il `ImageSource.FromStream` metodo crea un `ImageSource` basato su .NET `Stream`. Il metodo deve essere passato un `Func` oggetto restituito da un `Stream` oggetto.

### <a name="accessing-the-streams"></a>I flussi di accesso

Il [ **BitmapStreams** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) esempio viene illustrato come utilizzare il `ImaageSource.FromStream` metodo per caricare una bitmap archiviata come una risorsa incorporata e per caricare un'immagine bitmap attraverso il web.

### <a name="generating-bitmaps-at-run-time"></a>Generazione di immagini bitmap in fase di esecuzione

Tutte le piattaforme di xamarin. Forms supportano il formato di file BMP non compresso, facili da creare nel codice e quindi archiviare un `MemoryStream`. Questa tecnica consente di creare modo algoritmico bitmap in fase di esecuzione, come implementato nel [ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) classe il **Xamrin.FormsBook.Toolkit** libreria.

Il "si è manualmente" [ **DiyGradientBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) esempio viene illustrato l'utilizzo di `BmpMaker` per creare una bitmap con un'immagine di sfumatura.

## <a name="platform-specific-bitmaps"></a>Bitmap specifico della piattaforma

Tutte le piattaforme di xamarin. Forms consentono l'archiviazione delle bitmap negli assembly di applicazione della piattaforma. Quando recuperati da un'applicazione di xamarin. Forms, tali bitmap piattaforma sono di tipo [ `FileImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FileImageSource/). Viene utilizzato per:

- il [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Icon/) proprietà di [`MenuItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/)
- il [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) proprietà di [`ToolbarItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/)
- il [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) proprietà di `Button`

Gli assembly di piattaforma contengono già le bitmap per le icone e schermate iniziali:

- Nel progetto iOS nel **risorse** cartella
- Nel progetto Android, nelle sottocartelle del **risorse** cartella
- Nei progetti Windows, nel **asset** cartella (anche se le piattaforme Windows non si limitano le bitmap in tale cartella)

Il [ **PlatformBitmaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) esempio Usa codice per visualizzare un'icona dai progetti di applicazione della piattaforma.

### <a name="bitmap-resolutions"></a>Soluzioni di bitmap

Tutte le piattaforme per consentire l'archiviazione di più versioni delle immagini bitmap per le soluzioni di dispositivi diversi. In fase di esecuzione, la versione corretta viene caricata in base alla risoluzione del dispositivo dello schermo.

In iOS, tali bitmap si differenzino per un suffisso nel nome file:

- Alcun suffisso per i dispositivi di 160 DPI (1 pixel all'unità indipendenti dal dispositivo)
- '@2x' suffisso per i dispositivi di 320 DPI (2 pixel per il DIU)
- '@3x' suffisso per i dispositivi DPI 480 (3 pixel per il DIU)

Una bitmap da visualizzare come un pollice quadrato sarebbe infatti disponibile in tre versioni:

- Immagine.jpg 160 pixel quadrati
- MyImage@2x.jpg quadrato di 320 pixel
- MyImage@3x.jpg 480 pixel quadrati

Riferimento a questa bitmap come immagine.jpg il programma, ma la versione corretta viene recuperata in fase di esecuzione in base alla risoluzione dello schermo. Quando non è vincolato, la bitmap verrà sempre il rendering in 160 unità indipendenti dal dispositivo.

Per Android bitmap vengono archiviate in varie sottocartelle del **risorse** cartella:

- drawable-ldpi (bassa DPI) per i dispositivi di 120 DPI (0,75 pixel per il DIU)
- drawable-mdpi (medium) per i dispositivi di 160 DPI (1 pixel per il DIU)
- drawable-hdpi (in alto) per i dispositivi di 240 DPI (1,5 pixel per il DIU)
- drawable-xhdpi (molto alta) per i dispositivi di 320 DPI (2 pixel per il DIU)
- drawable-xxhdpi (extra molto alta) per i dispositivi DPI 480 (3 pixel per il DIU)
- drawable-xxxhdpi (tre aggiuntivo i valori massimi) per i dispositivi DPI 640 (4 pixel per il DIU)

Per una bitmap deve essere sottoposto a rendering in un quadrato di pollice, le diverse versioni della bitmap avranno lo stesso nome ma dimensioni diverse e archiviate in queste cartelle:

- drawable-ldpi/immagine.jpg quadrato di 120 pixel
- drawable-mdpi/immagine.jpg 160 pixel quadrati
- drawable-hdpi/immagine.jpg 240 pixel quadrati
- drawable-xhdpi/immagine.jpg 320 pixel quadrati
- drawable-xxhdpi/immagine.jpg 480 pixel quadrati
- drawable-xxxhdpi/immagine.jpg 640 pixel quadrati

La bitmap verrà sempre eseguito il rendering 160 unità indipendenti dal dispositivo. (Il modello di soluzione xamarin. Forms standard include solo il hdpi xhdpi e le cartelle xxhdpi.)

I progetti di Windows Runtime supportano una bitmap denominazione costituito da un fattore di scala espressa in pixel per unità indipendenti dal dispositivo come una percentuale, ad esempio:

- MyImage.scale-200.jpg 320 pixel quadrati

Solo alcuni percentuali sono valide. I programmi di esempio di questo documento includono solo immagini con **scala 200** suffissi, ma i modelli di soluzioni xamarin. Forms correnti includono **scala 100**, **scala 125**, **scala 150**, e **scala 400**. 

Durante l'aggiunta di bitmap per i progetti di piattaforma, il **azione di compilazione** deve essere:

- iOS: **BundleResource**
- Android: **AndroidResource**
- Windows Runtime: **contenuto**

Il [ **ImageTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) esempio crea due oggetti di tipo pulsante costituito `Image` gli elementi con un `TapGestureRecognizer` installato. Lo scopo è che gli oggetti sia quadrata di un pollice. Il `Source` proprietà di `Image` viene impostato utilizzando `OnPlatform` e `On` gli oggetti a cui fare riferimento a nomi di file potenzialmente diversi nelle piattaforme. Le immagini bitmap includono numeri che indica le dimensioni in pixel, in modo da visualizzare le bitmap di dimensioni viene recuperato e il rendering.

### <a name="toolbars-and-their-icons"></a>Barre degli strumenti e le icone

Uno degli usi principali delle bitmap specifico della piattaforma è barra degli strumenti di xamarin. Forms, che viene costruita aggiungendo [ `ToolbarItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) oggetti per il [ `ToolbarItems` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.ToolbarItems/) insieme definito dal `Page`. `ToobarItem` deriva da [ `MenuItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/) da cui eredita alcune proprietà.

La più importante `ToolbarItem` proprietà sono:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Text/) per il testo che potrebbero essere visualizzati a seconda della piattaforma e `Order`
- [`Icon`](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) di tipo `FileImageSource` per l'immagine che potrebbero essere visualizzati a seconda della piattaforma e `Order`
- [`Order`](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Order/) di tipo [ `ToolbarItemOrder` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItemOrder/), un'enumerazione con tre membri, [ `Default` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ToolbarItemOrder.Default/), [ `Primary` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ToolbarItemOrder.Primary/), e [ `Secondary` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ToolbarItemOrder.Secondary/).

Il numero di `Primary` gli elementi devono essere limitati a tre o quattro. È necessario includere un `Text` impostazione per tutti gli elementi. Per la maggior parte delle piattaforme, solo il `Primary` elementi richiedono un `Icon` ma richiede Windows 8.1 un `Icon` per tutti gli elementi. Le icone devono essere 32 device independent unit quadrato. Il `FileImageSource` tipo indica che sono specifiche della piattaforma.

Il `ToolbarItem` viene attivato un [ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.MenuItem.Clicked/) evento quando si tocca, analogamente a un `Button`. `ToolbarItem` supporta inoltre [ `Command` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Command/) e [ `CommandParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.CommandParameter/) proprietà spesso utilizzata in relazione MVVM. (Vedere [capitolo 18, MVVM](chapter18.md)).

IOS e Android è necessario che una pagina che visualizza una barra degli strumenti siano un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) o una pagina mediante un `NavigationPage`. Il [ **ToolbarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) programma imposta la `MainPage` proprietà del relativo `App` classe per il [ `NavigationPage` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.NavigationPage.NavigationPage/p/Xamarin.Forms.Page/) con un `ContentPage` argomento e viene illustrata il costruzione e il gestore eventi di una barra degli strumenti.

### <a name="button-images"></a>Immagini dei pulsanti

È anche possibile utilizzare bitmap specifico della piattaforma per impostare il [ `Image` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Image/) proprietà di `Button` in una bitmap del quadrato 32 unità indipendenti dal dispositivo, come illustrato di [ **ButtonImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) esempio.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 13 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Esempi di capitolo 13](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Uso delle immagini](~/xamarin-forms/user-interface/images.md)
