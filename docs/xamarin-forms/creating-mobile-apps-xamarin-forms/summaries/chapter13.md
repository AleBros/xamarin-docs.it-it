---
title: Riepilogo del capitolo 13. Bitmap
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 13. Bitmap'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: e4746ed94a008d382ce15bb9cd7c52365d9ba574
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78291530"
---
# <a name="summary-of-chapter-13-bitmaps"></a>Riepilogo del capitolo 13. Bitmap

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE]
> Le note in questa pagina indicano le aree in cui xamarin. Forms è diversa dal materiale presentato nel libro.

L'elemento [`Image`](xref:Xamarin.Forms.Image) Novell. Forms Visualizza una bitmap. Tutte le piattaforme di xamarin. Forms supportano i formati di file JPEG, PNG, GIF e BMP.

Le bitmap in xamarin. Forms provengono da quattro posizioni:

- Tramite il web come specificato da un URL
- Incorporato come risorsa nella libreria condivisa
- Incorporato come risorsa nei progetti di applicazione della piattaforma
- Da qualsiasi posizione a cui è possibile fare riferimento da un oggetto `Stream` .NET, incluso `MemoryStream`

Le risorse di bitmap nella libreria condivisa sono indipendenti dalla piattaforma, mentre le risorse di bitmap in progetti di piattaforma sono specifiche della piattaforma.

> [!NOTE]
> Il testo del libro viene fatto riferimento a librerie di classi portabili, che sono stati sostituiti dalle librerie .NET Standard. Tutto il codice di esempio dal libro è stato convertito per utilizzare le librerie .NET standard.

La bitmap viene specificata impostando la proprietà [`Source`](xref:Xamarin.Forms.Image.Source) di `Image` su un oggetto di tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), una classe astratta con tre derivati:

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) per l'accesso a una bitmap sul Web basata su un oggetto `Uri` impostato sulla relativa proprietà [`Uri`](xref:Xamarin.Forms.UriImageSource.Uri)
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) per l'accesso a una bitmap archiviata in un progetto di applicazione della piattaforma in base a una cartella e un percorso di file impostati sulla relativa proprietà [`File`](xref:Xamarin.Forms.FileImageSource.File)
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource) per il caricamento di una bitmap utilizzando un oggetto .NET `Stream` specificato restituendo un `Stream` da un `Func` impostato sulla relativa proprietà [`Stream`](xref:Xamarin.Forms.StreamImageSource.Stream)

In alternativa, e più comunemente, è possibile usare i metodi statici seguenti della classe `ImageSource`, che restituiscono tutti `ImageSource` oggetti:

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) per l'accesso a una bitmap sul Web in base a un oggetto `Uri`
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) per l'accesso a una bitmap archiviata come risorsa incorporata nell'applicazione PCL; [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type)) o [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) per accedere a una bitmap in un altro assembly di origine
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) per l'accesso a una bitmap da un progetto di applicazione della piattaforma
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) per il caricamento di una bitmap basata su un oggetto `Stream`

Non esiste alcuna classe equivalente ai metodi `Image.FromResource`. La classe `UriImageSource` è utile se è necessario controllare la memorizzazione nella cache. La classe `FileImageSource` è utile in XAML. `StreamImageSource` è utile per il caricamento asincrono di oggetti `Stream`, mentre `ImageSource.FromStream` è sincrono.

## <a name="platform-independent-bitmaps"></a>Bitmap indipendente dalla piattaforma

Il progetto [**WebBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) carica una bitmap sul web usando `ImageSource.FromUri`. L'elemento `Image` viene impostato sulla proprietà `Content` della `ContentPage`, quindi è vincolato alle dimensioni della pagina. Indipendentemente dalle dimensioni della bitmap, un elemento `Image` vincolato viene esteso alle dimensioni del relativo contenitore e la bitmap viene visualizzata nella dimensione massima all'interno dell'elemento `Image` mantenendo le proporzioni della bitmap. Le aree del `Image` oltre la bitmap possono essere colorate con [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor).

L'esempio [**WebBitmapXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) è simile, ma imposta semplicemente la proprietà `Source` sull'URL. La conversione viene gestita dalla classe [`ImageSourceConverter`](xref:Xamarin.Forms.ImageSourceConverter) .

### <a name="fit-and-fill"></a>Adattamento e riempimento

È possibile controllare il modo in cui la bitmap viene estesa impostando la proprietà [`Aspect`](xref:Xamarin.Forms.Image.Aspect) della `Image` su uno dei seguenti membri dell'enumerazione [`Aspect`](xref:Xamarin.Forms.Aspect) :

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): rispetta le proporzioni (impostazione predefinita)
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): riempie l'area, non rispetta le proporzioni
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): riempie l'area ma rispetta le proporzioni, eseguite mediante il ritaglio di una parte della bitmap

### <a name="embedded-resources"></a>Risorse incorporate

È possibile aggiungere un file bitmap a una libreria di classi Portabile o in una cartella nella libreria di classi Portabile. Assegnare un' **azione di compilazione** di **EmbeddedResource**. Nell'esempio [**ResourceBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) viene illustrato come utilizzare `ImageSource.FromResource` per caricare il file. Il nome della risorsa passato al metodo è costituito il nome dell'assembly, seguito da un punto, seguito dal nome della cartella facoltativa e un punto, seguito dal nome di file.

Il programma imposta le proprietà `VerticalOptions` e `HorizontalOptions` del `Image` su `LayoutOptions.Center`, rendendo l'elemento `Image` non vincolato. Le dimensioni del `Image` e della bitmap sono le stesse:

- In iOS e Android, il `Image` è la dimensione in pixel della bitmap. È presente un mapping uno a uno tra bitmap e pixel sullo schermo.
- In piattaforma UWP (Universal Windows Platform), il `Image` è la dimensione in pixel della bitmap in unità indipendenti dal dispositivo. Nella maggior parte dei dispositivi, ogni pixel bitmap occupa più pixel sullo schermo.

L'esempio [**StackedBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) inserisce un `Image` in un `StackLayout` verticale in XAML. Un'estensione di markup denominata [`ImageResourceExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) consente di fare riferimento alla risorsa incorporata in XAML. Questa classe consente di caricare solo le risorse dall'assembly in cui si trova attualmente, in modo che non può essere inserito in una libreria.

### <a name="more-on-sizing"></a>Ulteriori informazioni sul ridimensionamento

È spesso preferibile dimensione bitmap in modo coerente tra tutte le piattaforme.
Con la sperimentazione di **StackedBitmap**, è possibile impostare un `WidthRequest` sull'elemento `Image` in un `StackLayout` verticale per rendere le dimensioni coerenti tra le piattaforme, ma è possibile ridurre solo le dimensioni utilizzando questa tecnica.

È anche possibile impostare l'`HeightRequest` per rendere le dimensioni delle immagini coerenti sulle piattaforme, ma la larghezza vincolata della bitmap limiterà la versatilità di questa tecnica. Per un'immagine in un `StackLayout`verticale, è consigliabile evitare `HeightRequest`.

L'approccio migliore consiste nell'iniziare con una bitmap più ampia della larghezza del telefono in unità indipendenti dal dispositivo e impostare `WidthRequest` sulla larghezza desiderata in unità indipendenti dal dispositivo. Questa operazione è illustrata nell'esempio [**DeviceIndBitmapSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize) .

Il [**MadTeaParty**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) Visualizza il capitolo 7 delle avventure di Alice di Lewis Carroll *in Wonderland* con le illustrazioni originali di John Tenniel:

[![Schermata tripla della parte pazza del tè](images/ch13fg16-small.png "Testo del libro di Mad Hatters Tea Party")](images/ch13fg16-large.png#lightbox "Testo del libro di Mad Hatters Tea Party")

### <a name="browsing-and-waiting"></a>Esplorazione e in attesa

L'esempio [**ImageBrowser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) consente all'utente di spostarsi tra le immagini predefinite archiviate nel sito Web Novell. Usa la classe .NET [`WebRequest`](xref:System.Net.WebRequest) per scaricare un file JSON con l'elenco delle bitmap.

> [!NOTE]
> I programmi Novell. Forms devono usare [`HttpClient`](xref:System.Net.Http.HttpClient) anziché [`WebRequest`](xref:System.Net.WebRequest) per l'accesso ai file tramite Internet.

Il programma usa un [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) per indicare che l'operazione è in corso. Quando viene caricata ogni bitmap, viene `true`la proprietà di sola lettura [`IsLoading`](xref:Xamarin.Forms.Image.IsLoading) di `Image`. La proprietà `IsLoading` è supportata da una proprietà associabile, pertanto viene generato un evento `PropertyChanged` quando tale proprietà viene modificata. Il programma connette un gestore a questo evento e usa l'impostazione corrente di `IsLoaded` per impostare la proprietà [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) dell'`ActivityIndicator`.

## <a name="streaming-bitmaps"></a>Le bitmap di streaming

Il metodo `ImageSource.FromStream` crea una `ImageSource` basata su un `Stream`.NET. Al metodo deve essere passato un oggetto `Func` che restituisce un oggetto `Stream`.

### <a name="accessing-the-streams"></a>L'accesso a flussi

Nell'esempio [**BitmapStreams**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) viene illustrato come utilizzare il metodo `ImaageSource.FromStream` per caricare una bitmap archiviata come risorsa incorporata e caricare una bitmap sul Web.

### <a name="generating-bitmaps-at-run-time"></a>Generazione di immagini bitmap in fase di esecuzione

Tutte le piattaforme Novell. Forms supportano il formato di file BMP non compresso, che è facile da costruire nel codice e quindi archiviare in un `MemoryStream`. Questa tecnica consente a algoritmicamente di creare bitmap in fase di esecuzione, implementate nella classe [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) nella libreria **Xamarin. FormsBook. Toolkit** .

Nell'esempio [**DiyGradientBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) "fai da te" viene illustrato l'uso di `BmpMaker` per creare una bitmap con un'immagine di sfumatura.

## <a name="platform-specific-bitmaps"></a>Bitmap specifiche della piattaforma

Tutte le piattaforme di xamarin. Forms consentono di archiviare le immagini bitmap negli assembly di applicazione della piattaforma. Quando vengono recuperate da un'applicazione Novell. Forms, le bitmap della piattaforma sono di tipo [`FileImageSource`](xref:Xamarin.Forms.FileImageSource). Viene utilizzato per:

- proprietà [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) di [`MenuItem`](xref:Xamarin.Forms.MenuItem)
- proprietà [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) di [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- proprietà [`Image`](xref:Xamarin.Forms.Button) di `Button`

Gli assembly di piattaforma contengono già le bitmap per le icone e schermate iniziali:

- Nel progetto iOS, nella cartella **risorse**
- Nel progetto Android, in sottocartelle della cartella **risorse**
- Nei progetti Windows, nella cartella **Asset** (anche se le piattaforme Windows non limitano le bitmap a tale cartella)

L'esempio [**PlatformBitmaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) usa il codice per visualizzare un'icona dai progetti di applicazione della piattaforma.

### <a name="bitmap-resolutions"></a>Risoluzioni di bitmap

Tutte le piattaforme per consentire l'archiviazione di più versioni delle immagini bitmap per le soluzioni di dispositivo diverse. In fase di esecuzione, la versione corretta viene caricata in base alla risoluzione di dispositivo dello schermo.

In iOS, tali bitmap si differenziano per un suffisso nel nome file:

- Senza suffisso per i dispositivi di 160 DPI (1 pixel nell'unità indipendenti dal dispositivo)
- suffisso '@2x' per i dispositivi 320 DPI (2 pixel a DIU)
- suffisso '@3x' per i dispositivi 480 DPI (da 3 pixel a DIU)

Una mappa di bit deve essere visualizzato come quadrato da 1 esisterebbe in tre versioni:

- Jpg 160 pixel quadrati
- MyImage@2x.jpg al quadrato di 320 pixel
- MyImage@3x.jpg al quadrato di 480 pixel

Il programma per fare riferimento a questa bitmap come jpg, ma la versione corretta è recuperata in fase di esecuzione in base alla risoluzione dello schermo. Quando non è vincolato, la bitmap verrà sempre il rendering in 160 unità indipendenti dal dispositivo.

Per Android, le bitmap vengono archiviate in varie sottocartelle della cartella **Resources** :

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

Solo alcuni percentuali sono valide. I programmi di esempio per questo libro includono solo immagini con suffissi **scale-200** , ma i modelli di soluzione Novell. Forms correnti includono **scale-100**, **scale-125**, **scale-150**e **Scale-400**.

Quando si aggiungono bitmap ai progetti della piattaforma, l' **azione di compilazione** deve essere la seguente:

- iOS: **BundleResource**
- Android: **AndroidResource**
- UWP: **contenuto**

L'esempio [**ImageTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) crea due oggetti di tipo Button costituiti da elementi di `Image` con un `TapGestureRecognizer` installato. Lo scopo è che gli oggetti di essere quadrata di un pollice. La proprietà `Source` di `Image` viene impostata utilizzando `OnPlatform` e `On` oggetti per fare riferimento a nomi file potenzialmente diversi sulle piattaforme. Le immagini bitmap includono numeri che indica le dimensioni in pixel, in modo da visualizzare le bitmap di dimensione viene recuperato e viene eseguito il rendering.

### <a name="toolbars-and-their-icons"></a>Le barre degli strumenti e le icone

Uno degli utilizzi principali delle bitmap specifiche della piattaforma è la barra degli strumenti Novell. Forms, costruita aggiungendo [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) oggetti alla raccolta [`ToolbarItems`](xref:Xamarin.Forms.Page.ToolbarItems) definita da `Page`. `ToobarItem` deriva da [`MenuItem`](xref:Xamarin.Forms.MenuItem) da cui eredita alcune proprietà.

Le proprietà `ToolbarItem` più importanti sono:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) per il testo che potrebbe apparire a seconda della piattaforma e della `Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) di tipo `FileImageSource` per l'immagine che potrebbe apparire a seconda della piattaforma e `Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) di tipo [`ToolbarItemOrder`](xref:Xamarin.Forms.ToolbarItemOrder), un'enumerazione con tre membri, [`Default`](xref:Xamarin.Forms.ToolbarItemOrder.Default), [`Primary`](xref:Xamarin.Forms.ToolbarItemOrder.Primary)e [`Secondary`](xref:Xamarin.Forms.ToolbarItemOrder.Secondary).

Il numero di elementi di `Primary` deve essere limitato a tre o quattro. È necessario includere un'impostazione `Text` per tutti gli elementi. Per la maggior parte delle piattaforme, solo gli elementi `Primary` richiedono un `Icon` ma Windows 8.1 richiede un `Icon` per tutti gli elementi. Le icone devono essere 32 device independent unit quadrati. Il tipo di `FileImageSource` indica che sono specifici della piattaforma.

Il `ToolbarItem` genera un evento di [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) quando viene toccato, molto simile a un `Button`. `ToolbarItem` supporta anche le proprietà [`Command`](xref:Xamarin.Forms.MenuItem.Command) e [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) spesso utilizzate per la connessione a MVVM. (Vedere il [capitolo 18, MVVM](chapter18.md)).

Sia per iOS che per Android è necessario che una pagina che visualizzi una barra degli strumenti sia un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) o una pagina a cui si accede da un `NavigationPage`. Il programma [**ToolbarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) imposta la proprietà `MainPage` della relativa classe `App` sul [Costruttore`NavigationPage`](xref:Xamarin.Forms.NavigationPage.%23ctor(Xamarin.Forms.Page)) con un argomento `ContentPage` e illustra la costruzione e il gestore eventi di una barra degli strumenti.

### <a name="button-images"></a>Icone dei pulsanti

È anche possibile usare bitmap specifiche della piattaforma per impostare la proprietà [`Image`](xref:Xamarin.Forms.Button.Image) di `Button` su una bitmap di 32 unità indipendenti dal dispositivo, come illustrato nell'esempio [**ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) .

> [!NOTE]
> È stata migliorata l'uso delle immagini sui pulsanti. Vedere [uso di bitmap con i pulsanti](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 13 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Esempi capitolo 13](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Uso delle immagini](~/xamarin-forms/user-interface/images.md)
- [Uso di bitmap con pulsanti](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
