---
title: Riepilogo del capitolo 13. Bitmap
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 13. Bitmap'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 737e242e14778f38405845541b2ca30d27c3cf5a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059600"
---
# <a name="summary-of-chapter-13-bitmaps"></a>Riepilogo del capitolo 13. Bitmap

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui xamarin. Forms è diversa dal materiale presentato nel libro.

Xamarin. Forms [ `Image` ](xref:Xamarin.Forms.Image) elemento consente di visualizzare una bitmap. Tutte le piattaforme di xamarin. Forms supportano i formati di file JPEG, PNG, GIF e BMP.

Le bitmap in xamarin. Forms provengono da quattro posizioni:

- Tramite il web come specificato da un URL
- Incorporato come risorsa nella libreria condivisa
- Incorporato come risorsa nei progetti di applicazione della piattaforma
- Da qualsiasi luogo in cui possono fare riferimento .NET `Stream` dell'oggetto, tra cui `MemoryStream`

Le risorse di bitmap nella libreria condivisa sono indipendenti dalla piattaforma, mentre le risorse di bitmap in progetti di piattaforma sono specifiche della piattaforma.

> [!NOTE] 
> Il testo del libro viene fatto riferimento a librerie di classi portabili, che sono stati sostituiti dalle librerie .NET Standard. Tutto il codice di esempio dal libro è stato convertito per utilizzare le librerie .NET standard.

La bitmap viene specificata impostando il [ `Source` ](xref:Xamarin.Forms.Image.Source) proprietà della `Image` a un oggetto di tipo [ `ImageSource` ](xref:Xamarin.Forms.ImageSource), una classe astratta con tre derivati:

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) per l'accesso a una mappa di bit tramite il sito web basato su un `Uri` oggetto impostato su relativi [ `Uri` ](xref:Xamarin.Forms.UriImageSource.Uri) proprietà
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) per l'accesso a una mappa di bit archiviato in un progetto di applicazione della piattaforma basato su una cartella e il percorso impostato su relativi [ `File` ](xref:Xamarin.Forms.FileImageSource.File) proprietà
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource) per il caricamento di una bitmap con .NET `Stream` oggetto specificato, restituendo un `Stream` da un `Func` impostato su relativo [ `Stream` ](xref:Xamarin.Forms.StreamImageSource.Stream) proprietà

In alternativa (e più comunemente) è possibile usare i metodi statici seguenti del `ImageSource` classe, che restituiscono `ImageSource` oggetti:

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) per l'accesso a una mappa di bit tramite il sito web basato su un `Uri` oggetto
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) per l'accesso a un'immagine bitmap archiviata come una risorsa incorporata nell'applicazione di libreria di classi Portabile; [ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type)) oppure [ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) per accedere a una mappa di bit in un altro assembly di origine
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) per l'accesso a una bitmap da un progetto di applicazione della piattaforma
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) per il caricamento di una mappa di bit basata su un `Stream` oggetto

È disponibile un equivalente della classe di `Image.FromResource` metodi. Il `UriImageSource` classe è utile se è necessario controllare la memorizzazione nella cache. Il `FileImageSource` classe è utile in XAML. `StreamImageSource` è utile per il caricamento asincrono di `Stream` oggetti, mentre `ImageSource.FromStream` è sincrona.

## <a name="platform-independent-bitmaps"></a>Bitmap indipendente dalla piattaforma

Il [ **WebBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) progetto carica una bitmap nel web tramite `ImageSource.FromUri`. Il `Image` elemento è impostato sul `Content` proprietà del `ContentPage`, in modo che è vincolato alla dimensione della pagina. Indipendentemente dalle dimensioni della mappa di bit, un vincolata `Image` elemento viene adattato alla dimensione del relativo contenitore e la mappa di bit viene visualizzato in dimensioni massime all'interno di `Image` elemento mantenendo le proporzioni della bitmap. Aree dei `Image` bitmap possono essere impostate con oltre [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor).

Il [ **WebBitmapXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) esempio è simile, ma imposta semplicemente la `Source` proprietà all'URL. La conversione viene gestita dal [ `ImageSourceConverter` ](xref:Xamarin.Forms.ImageSourceConverter) classe.

### <a name="fit-and-fill"></a>Adattamento e riempimento

È possibile controllare come la bitmap viene estesa impostando il [ `Aspect` ](xref:Xamarin.Forms.Image.Aspect) proprietà della `Image` a uno dei seguenti membri del [ `Aspect` ](xref:Xamarin.Forms.Aspect) enumerazione:

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): rispetta proporzioni (impostazione predefinita)
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): riempie l'area, non rispetta le proporzioni
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): riempie l'area, ma rispetta proporzioni, operazione eseguita da parte della bitmap di ritaglio

### <a name="embedded-resources"></a>Risorse incorporate

È possibile aggiungere un file bitmap a una libreria di classi Portabile o in una cartella nella libreria di classi Portabile. Assegnargli un **Build Action** dei **EmbeddedResource**. Il [ **ResourceBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) esempio viene illustrato come usare `ImageSource.FromResource` per caricare il file. Il nome della risorsa passato al metodo è costituito il nome dell'assembly, seguito da un punto, seguito dal nome della cartella facoltativa e un punto, seguito dal nome di file.

I set di programma il `VerticalOptions` e `HorizontalOptions` proprietà del `Image` al `LayoutOptions.Center`, che rende il `Image` elemento non vincolato. Il `Image` e la mappa di bit sono della stessa dimensione:

- In iOS e Android, il `Image` è la dimensione in pixel della bitmap. È presente un mapping uno a uno tra bitmap e pixel sullo schermo.
- Nella piattaforma Windows universale, la `Image` è la dimensione in pixel della bitmap in unità indipendenti dal dispositivo. Nella maggior parte dei dispositivi, ogni pixel bitmap occupa più pixel sullo schermo.

Il [ **StackedBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) esempio inserisce un `Image` in un parametro vertical `StackLayout` in XAML. Un'estensione di markup denominata [ `ImageResourceExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) consente di fare riferimento alla risorsa incorporata in XAML. Questa classe consente di caricare solo le risorse dall'assembly in cui si trova attualmente, in modo che non può essere inserito in una libreria.

### <a name="more-on-sizing"></a>Ulteriori informazioni sul ridimensionamento

È spesso preferibile dimensione bitmap in modo coerente tra tutte le piattaforme.
Sperimentare **StackedBitmap**, è possibile impostare un `WidthRequest` sul `Image` elemento in un parametro vertical `StackLayout` alle dimensioni coerenti tra le piattaforme, ma è solo possibile ridurre le dimensioni utilizzando questa tecnica.

È anche possibile impostare il `HeightRequest` per creare l'immagine con dimensioni coerenti nelle piattaforme, ma la larghezza della bitmap vincolata limiterà la versatilità di questa tecnica. Per un'immagine in un parametro vertical `StackLayout`, `HeightRequest` deve essere evitata.

L'approccio migliore consiste nell'iniziare con una bitmap maggiore della larghezza di telefono in unità indipendenti dal dispositivo e impostare `WidthRequest` la larghezza desiderata in unità indipendenti dal dispositivo. Questa funzionalità viene illustrata la [ **DeviceIndBitmapSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize) esempio.

Il [ **MadTeaParty** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) Visualizza capitolo 7 di Lewis Carroll *Alice Adventures nel meraviglie* con le illustrazioni originale da John Tenniel:

[![Screenshot tripla della corsa party tè](images/ch13fg16-small.png "Mad testo del libro tè Party Hatters")](images/ch13fg16-large.png#lightbox "testo del libro tè Party Hatters corsa")

### <a name="browsing-and-waiting"></a>Esplorazione e in attesa

Il [ **ImageBrowser** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) esempio consente all'utente di esplorare le immagini predefinite archiviate sul sito web di Xamarin. Usa .NET [ `WebRequest` ](xref:System.Net.WebRequest) classe per scaricare un file JSON con l'elenco delle bitmap.

> [!NOTE]
> Programmi di xamarin. Forms devono utilizzare [ `HttpClient` ](xref:System.Net.Http.HttpClient) anziché [ `WebRequest` ](xref:System.Net.WebRequest) per accedere ai file tramite internet. 

Il programma Usa un' [ `ActivityIndicator` ](xref:Xamarin.Forms.ActivityIndicator) per indicare che un elemento sta succedendo. Quando viene caricato ogni bitmap, di sola lettura [ `IsLoading` ](xref:Xamarin.Forms.Image.IsLoading) proprietà della `Image` è `true`. Il `IsLoading` proprietà è supportata da una proprietà associabile, pertanto un `PropertyChanged` evento viene generato quando tale proprietà viene modificata. Associa un gestore per questo evento, il programma e Usa l'impostazione corrente di `IsLoaded` per impostare il [ `IsRunning` ](https://api/property/Xamarin.Forms.ActivityIndicator.IsRunning/) proprietà del `ActivityIndicator`.

## <a name="streaming-bitmaps"></a>Le bitmap di streaming

Il `ImageSource.FromStream` metodo crea un' `ImageSource` basato su .NET `Stream`. Il metodo deve essere passato un `Func` oggetto restituito da una `Stream` oggetto.

### <a name="accessing-the-streams"></a>L'accesso a flussi

Il [ **BitmapStreams** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) esempio viene illustrato come usare il `ImaageSource.FromStream` metodo per caricare un'immagine bitmap archiviata come una risorsa incorporata e per caricare una bitmap attraverso il web.

### <a name="generating-bitmaps-at-run-time"></a>Generazione di immagini bitmap in fase di esecuzione

Tutte le piattaforme di xamarin. Forms supportano il formato di file BMP non compresso, facili da creare nel codice e quindi archiviare in un `MemoryStream`. Questa tecnica consente di creare modo algoritmico bitmap in fase di esecuzione, come implementato nel [ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) classe la **Xamrin.FormsBook.Toolkit** libreria.

Il "eseguire in prima persona" [ **DiyGradientBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) esempio dimostra l'uso di `BmpMaker` per creare una bitmap con un'immagine della sfumatura.

## <a name="platform-specific-bitmaps"></a>Bitmap specifiche della piattaforma

Tutte le piattaforme di xamarin. Forms consentono di archiviare le immagini bitmap negli assembly di applicazione della piattaforma. Quando è recuperato da un'applicazione xamarin. Forms, tali bitmap piattaforma sono di tipo [ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource). Viene utilizzato per:

- il [ `Icon` ](xref:Xamarin.Forms.MenuItem.Icon) proprietà di [`MenuItem`](xref:Xamarin.Forms.MenuItem)
- il [ `Icon` ](xref:Xamarin.Forms.MenuItem.Icon) proprietà di [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- il [ `Image` ](xref:Xamarin.Forms.Button) proprietà di `Button`

Gli assembly di piattaforma contengono già le bitmap per le icone e schermate iniziali:

- Nel progetto iOS nel **risorse** cartella
- Nel progetto Android, nelle sottocartelle della **risorse** cartella
- Nei progetti di Windows, nelle **asset** cartella (anche se le piattaforme Windows non si limitano le bitmap in tale cartella)

Il [ **PlatformBitmaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) esempio Usa codice per visualizzare un'icona dai progetti della piattaforma dell'applicazione.

### <a name="bitmap-resolutions"></a>Risoluzioni di bitmap

Tutte le piattaforme per consentire l'archiviazione di più versioni delle immagini bitmap per le soluzioni di dispositivo diverse. In fase di esecuzione, la versione corretta viene caricata in base alla risoluzione di dispositivo dello schermo.

In iOS, tali bitmap si differenziano per un suffisso nel nome file:

- Senza suffisso per i dispositivi di 160 DPI (1 pixel nell'unità indipendenti dal dispositivo)
- '@2x' suffisso per i dispositivi 320 DPI (2 pixel per il DIU)
- '@3x' suffisso per i dispositivi di 480 DPI (3 pixel per il DIU)

Una mappa di bit deve essere visualizzato come quadrato da 1 esisterebbe in tre versioni:

- Jpg 160 pixel quadrati
- MyImage@2x.jpg 320 pixel quadrati
- MyImage@3x.jpg 480 pixel quadrati

Il programma per fare riferimento a questa bitmap come jpg, ma la versione corretta è recuperata in fase di esecuzione in base alla risoluzione dello schermo. Quando non è vincolato, la bitmap verrà sempre il rendering in 160 unità indipendenti dal dispositivo.

Per Android, bitmap vengono archiviate in varie sottocartelle del **risorse** cartella:

- drawable-ldpi (bassa DPI) per i dispositivi 120 DPI (0,75 pixel per il DIU)
- drawable-mdpi (medium) per i dispositivi di 160 DPI (1 pixel per il DIU)
- drawable-hdpi (in alto) per i dispositivi di 240 DPI (1,5 pixel per il DIU)
- drawable-xhdpi (molto alta) per i dispositivi 320 DPI (2 pixel per il DIU)
- drawable-xxhdpi (molto elevato) per i dispositivi di 480 DPI (3 pixel per il DIU)
- drawable-xxxhdpi (tre valori massimi extra) per i dispositivi di 640 DPI (4 pixel per il DIU)

Per una mappa di bit deve essere sottoposto a rendering in un quadrato di pollice, le diverse versioni della bitmap avranno lo stesso nome ma una dimensione diversa e archiviate in queste cartelle:

- drawable-ldpi o jpg a 120 pixel quadrati
- drawable-mdpi/jpg 160 pixel quadrati
- drawable-hdpi o jpg 240 pixel quadrati
- drawable-xhdpi/jpg 320 pixel quadrati
- drawable-xxhdpi/jpg 480 pixel quadrati
- drawable-xxxhdpi o jpg a 640 pixel quadrati

La mappa di bit sarà sempre il rendering in 160 unità indipendenti dal dispositivo. (Il modello di soluzione xamarin. Forms standard include solo il hdpi xhdpi e le cartelle xxhdpi.)

Il progetto UWP supporta uno schema di denominazione mappa di bit costituita da un fattore di scala espressa in pixel per ogni unità indipendenti dal dispositivo espressa in percentuale, ad esempio:

- MyImage.scale-200.jpg 320 pixel quadrati

Solo alcuni percentuali sono valide. I programmi di esempio per questo libro includono solo le immagini con **scalabilità-200** suffissi, ma i modelli di soluzione xamarin. Forms correnti includono **scala 100**, **scala-125**, **scalabilità-150**, e **scalabilità-400**.

Quando si aggiungono le bitmap per i progetti di piattaforma, il **azione di compilazione** deve essere:

- iOS: **BundleResource**
- Android: **AndroidResource**
- Piattaforma UWP: **contenuto**

Il [ **ImageTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) esempio crea due oggetti a pulsante costituiti `Image` gli elementi con un `TapGestureRecognizer` installato. Lo scopo è che gli oggetti di essere quadrata di un pollice. Il `Source` proprietà di `Image` viene impostato utilizzando `OnPlatform` e `On` oggetti per fare riferimento a nomi di file potenzialmente diversi sulle piattaforme. Le immagini bitmap includono numeri che indica le dimensioni in pixel, in modo da visualizzare le bitmap di dimensione viene recuperato e viene eseguito il rendering.

### <a name="toolbars-and-their-icons"></a>Le barre degli strumenti e le icone

Uno degli usi principali delle bitmap specifiche della piattaforma è la barra degli strumenti xamarin. Forms, che viene costruita aggiungendo [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem) oggetti per il [ `ToolbarItems` ](xref:Xamarin.Forms.Page.ToolbarItems) insieme definito dal `Page`. `ToobarItem` deriva da [ `MenuItem` ](xref:Xamarin.Forms.MenuItem) da cui eredita alcune proprietà.

La più importante `ToolbarItem` proprietà sono:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) per il testo che potrebbe essere visualizzato in base alla piattaforma e `Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) di tipo `FileImageSource` per l'immagine che potrebbe essere visualizzato in base alla piattaforma e `Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) typu [ `ToolbarItemOrder` ](xref:Xamarin.Forms.ToolbarItemOrder), un'enumerazione con tre membri [ `Default` ](xref:Xamarin.Forms.ToolbarItemOrder.Default), [ `Primary` ](xref:Xamarin.Forms.ToolbarItemOrder.Primary), e [ `Secondary` ](xref:Xamarin.Forms.ToolbarItemOrder.Secondary).

Il numero di `Primary` dovrebbero essere limitati a tre o quattro elementi. È necessario includere un `Text` impostazione per tutti gli elementi. Per la maggior parte delle piattaforme, solo il `Primary` elementi richiedono un' `Icon` ma richiede Windows 8.1 un `Icon` per tutti gli elementi. Le icone devono essere 32 device independent unit quadrati. Il `FileImageSource` tipo indica che essi sono specifiche della piattaforma.

Il `ToolbarItem` viene attivato un [ `Clicked` ](xref:Xamarin.Forms.MenuItem.Clicked) eventi quando si tocca, molto simile a un `Button`. `ToolbarItem` supporta inoltre [ `Command` ](xref:Xamarin.Forms.MenuItem.Command) e [ `CommandParameter` ](xref:Xamarin.Forms.MenuItem.CommandParameter) proprietà spesso usate in relazione a MVVM. (Vedere [capitolo 18, MVVM](chapter18.md)).

IOS e Android richiedono che una pagina che visualizzi una barra degli strumenti sia un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) o una pagina di navigazione da un `NavigationPage`. Il [ **ToolbarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) programmare set il `MainPage` proprietà del relativo `App` classe per il [ `NavigationPage` costruttore](xref:Xamarin.Forms.NavigationPage.%23ctor(Xamarin.Forms.Page)) con un `ContentPage` argomento e viene illustrato il gestore di evento e la costruzione di una barra degli strumenti.

### <a name="button-images"></a>Icone dei pulsanti

È anche possibile usare bitmap specifiche della piattaforma per impostare il [ `Image` ](xref:Xamarin.Forms.Button.Image) proprietà della `Button` in una bitmap del quadrato 32 device independent unit, come dimostrato dal [ **ButtonImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) esempio.

> [!NOTE]
> È stata migliorata l'uso delle immagini sui pulsanti. Visualizzare [usando le bitmap con pulsanti](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 13 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Esempi di capitolo 13](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Uso delle immagini](~/xamarin-forms/user-interface/images.md)
- [Utilizzo di immagini bitmap con i pulsanti](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
