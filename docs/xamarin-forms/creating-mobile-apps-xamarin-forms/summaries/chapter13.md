---
title: Riassunto del capitolo 13. Bitmap
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 13. Bitmap'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: e4746ed94a008d382ce15bb9cd7c52365d9ba574
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291530"
---
# <a name="summary-of-chapter-13-bitmaps"></a>Riassunto del capitolo 13. Bitmap

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE]
> Le note in questa pagina indicano le aree in cui Xamarin.Forms si è discostata dal materiale presentato nel libro.

L'elemento Xamarin.Forms [`Image`](xref:Xamarin.Forms.Image) visualizza una bitmap. Tutte le piattaforme Xamarin.Forms supportano i formati di file JPEG, PNG, GIF e BMP.

Le bitmap in Xamarin.Forms provengono da quattro punti:

- Sul Web come specificato da un URL
- Incorporato come risorsa nella libreria condivisa
- Incorporato come risorsa nei progetti di applicazione della piattaforma
- Da qualsiasi luogo a cui può `Stream` fare riferimento un oggetto .NET,`MemoryStream`

Le risorse bitmap nella libreria condivisa sono indipendenti dalla piattaforma, mentre le risorse bitmap nei progetti della piattaforma sono specifiche della piattaforma.

> [!NOTE]
> Il testo del libro fa riferimento a librerie di classi portabili, che sono state sostituite dalle librerie .NET Standard. Tutto il codice di esempio del libro è stato convertito per utilizzare librerie standard .NET.

La bitmap viene specificata [`Source`](xref:Xamarin.Forms.Image.Source) impostando `Image` la proprietà [`ImageSource`](xref:Xamarin.Forms.ImageSource)di su un oggetto di tipo , una classe astratta con tre derivate:

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource)per accedere a una bitmap sul `Uri` Web in [`Uri`](xref:Xamarin.Forms.UriImageSource.Uri) base a un oggetto impostato sulla relativa proprietà
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource)per accedere a una bitmap archiviata in un progetto di [`File`](xref:Xamarin.Forms.FileImageSource.File) applicazione della piattaforma in base a una cartella e un percorso di file impostato sulla relativa proprietà
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource)per il caricamento di `Stream` una bitmap utilizzando `Stream` un `Func` oggetto .NET specificato restituendo un oggetto da un set alla relativa [`Stream`](xref:Xamarin.Forms.StreamImageSource.Stream) proprietà

In alternativa (e più comunemente) è possibile `ImageSource` utilizzare i seguenti `ImageSource` metodi statici della classe, tutti gli oggetti restituiti:

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))per accedere a una bitmap sul `Uri` Web in base a un oggetto
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)per accedere a una bitmap archiviata come risorsa incorporata nella libreria di classi Porta colori system dell'applicazione; [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type)) o [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) per accedere a una bitmap in un altro assieme di origine
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String))per accedere a una bitmap da un progetto di applicazione della piattaforma
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream}))per il caricamento di `Stream` una bitmap basata su un oggetto

Non esiste alcun equivalente `Image.FromResource` di classe dei metodi. La `UriImageSource` classe è utile se è necessario controllare la memorizzazione nella cache. La `FileImageSource` classe è utile in XAML. `StreamImageSource`è utile per il `Stream` caricamento `ImageSource.FromStream` asincrono di oggetti, mentre è sincrono.

## <a name="platform-independent-bitmaps"></a>Bitmap indipendenti dalla piattaforma

Il progetto [**WebBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) carica una `ImageSource.FromUri`bitmap sul Web utilizzando . L'elemento `Image` viene `Content` impostato sulla `ContentPage`proprietà di , pertanto è vincolato alle dimensioni della pagina. Indipendentemente dalle dimensioni della bitmap, `Image` un elemento vincolato viene esteso alle dimensioni del relativo `Image` contenitore e la bitmap viene visualizzata nella sua dimensione massima all'interno dell'elemento mantenendo le proporzioni della bitmap. Le aree oltre `Image` la bitmap [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)possono essere colorate con .

Il [**WebBitmapXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) esempio è simile, ma imposta semplicemente la `Source` proprietà per l'URL. La conversione viene [`ImageSourceConverter`](xref:Xamarin.Forms.ImageSourceConverter) gestita dalla classe .

### <a name="fit-and-fill"></a>Adatta e riempi

È possibile controllare la modalità [`Aspect`](xref:Xamarin.Forms.Image.Aspect) di allungamento della bitmap impostando la proprietà dell'oggetto `Image` su uno dei seguenti membri dell'enumerazione: [`Aspect`](xref:Xamarin.Forms.Aspect)

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): rispetta le proporzioni (impostazione predefinita)
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): riempie l'area, non rispetta le proporzioni
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): riempie l'area ma rispetta le proporzioni, eseguite ritagliando parte della bitmap

### <a name="embedded-resources"></a>Risorse incorporate

È possibile aggiungere un file bitmap a una libreria di classi Portante o a una cartella nella libreria di classi Portante. Assegnare **un'azione** di compilazione di **EmbeddedResource**. Nell'esempio [**ResourceBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) viene `ImageSource.FromResource` illustrato come utilizzare per caricare il file. Il nome della risorsa passato al metodo è costituito dal nome dell'assembly, seguito da un punto, seguito dal nome della cartella facoltativa e da un punto, seguito dal nome del file.

Il programma `VerticalOptions` imposta `HorizontalOptions` le `Image` proprietà `LayoutOptions.Center`e di `Image` su , il che rende l'elemento non vincolato. La `Image` bitmap e la bitmap hanno le stesse dimensioni:

- In iOS e `Image` Android, la dimensione in pixel della bitmap è la dimensione in pixel della bitmap. Esiste un mapping uno-a-uno tra pixel bitmap e pixel dello schermo.
- Nella piattaforma Windows `Image` universale, la dimensione in pixel della bitmap in unità indipendenti dal dispositivo. Nella maggior parte dei dispositivi, ogni pixel bitmap occupa più pixel dello schermo.

Il [**StackedBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) esempio `Image` inserisce `StackLayout` un in verticale in XAML. Un'estensione [`ImageResourceExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) di markup denominata aiuta a fare riferimento alla risorsa incorporata in XAML. Questa classe carica solo le risorse dall'assembly in cui si trova, pertanto non può essere inserita in una libreria.

### <a name="more-on-sizing"></a>Ulteriori informazioni sul dimensionamento

Spesso è opportuno ridimensionare le bitmap in modo coerente tra tutte le piattaforme.
Sperimentando con **StackedBitmap**, `WidthRequest` è `Image` possibile impostare `StackLayout` un sull'elemento in verticale per rendere le dimensioni coerenti tra le piattaforme, ma è possibile solo ridurre le dimensioni utilizzando questa tecnica.

È anche possibile `HeightRequest` impostare l'oggetto per rendere le dimensioni dell'immagine coerenti sulle piattaforme, ma la larghezza vincolata della bitmap limiterà la versatilità di questa tecnica. Per un'immagine `StackLayout`in `HeightRequest` verticale, è necessario evitare.

L'approccio migliore consiste nell'iniziare con una bitmap più `WidthRequest` ampia della larghezza del telefono in unità indipendenti dal dispositivo e impostare la larghezza desiderata in unità indipendenti dal dispositivo. Ciò è illustrato nell'esempio [**DeviceIndBitmapSize.This**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize) is demonstrated in the DeviceIndBitmapSize sample.

Il [**MadTeaParty**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) mostra il capitolo 7 di *Alice's Adventures in Wonderland* di Lewis Carroll con le illustrazioni originali di John Tenniel:

[![Triplo screenshot di mad tea party](images/ch13fg16-small.png "Cappellai Pazzi Tea Party Libro Testo")](images/ch13fg16-large.png#lightbox "Cappellai Pazzi Tea Party Libro Testo")

### <a name="browsing-and-waiting"></a>Navigazione e attesa

L'esempio [**ImageBrowser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) consente all'utente di sfogliare le immagini predefinite memorizzate nel sito Web Xamarin. Usa la classe [`WebRequest`](xref:System.Net.WebRequest) .NET per scaricare un file JSON con l'elenco delle bitmap.

> [!NOTE]
> I programmi Xamarin.Forms devono utilizzare [`HttpClient`](xref:System.Net.Http.HttpClient) anziché [`WebRequest`](xref:System.Net.WebRequest) per accedere ai file tramite Internet.

Il programma [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) utilizza un per indicare che sta succedendo qualcosa. Durante il caricamento di ogni [`IsLoading`](xref:Xamarin.Forms.Image.IsLoading) bitmap, `Image` `true`la proprietà di sola lettura di is . La `IsLoading` proprietà è supportata da una `PropertyChanged` proprietà associabile, pertanto viene generato un evento quando tale proprietà viene modificata. Il programma associa un gestore a questo evento `IsLoaded` e utilizza [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) l'impostazione corrente di per impostare la proprietà dell'oggetto `ActivityIndicator`.

## <a name="streaming-bitmaps"></a>Flusso di bitmap

Il `ImageSource.FromStream` metodo `ImageSource` crea un oggetto `Stream`basato su un oggetto .NET . Il metodo deve `Func` essere passato `Stream` un oggetto che restituisce un oggetto.

### <a name="accessing-the-streams"></a>Accesso ai flussi

Nell'esempio [**BitmapStreams**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) viene `ImaageSource.FromStream` illustrato come utilizzare il metodo per caricare una bitmap archiviata come risorsa incorporata e per caricare una bitmap sul Web.

### <a name="generating-bitmaps-at-run-time"></a>Generazione di bitmap in fase di esecuzione

Tutte le piattaforme Xamarin.Forms supportano il formato di file BMP non `MemoryStream`compresso, che è facile da costruire nel codice e quindi memorizzare in un file . Questa tecnica consente di creare bitmap algoritmicamente [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) in fase di esecuzione, come implementato nella classe nella libreria **Xamrin.FormsBook.Toolkit.**

Nell'esempio [**DiyGradientBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) di "Do It `BmpMaker` Yourself" viene illustrato l'utilizzo di per creare una bitmap con un'immagine sfumata.

## <a name="platform-specific-bitmaps"></a>Bitmap specifiche della piattaforma

Tutte le piattaforme Xamarin.Forms consentono di archiviare le bitmap negli assembly dell'applicazione della piattaforma. Quando vengono recuperate da un'applicazione Xamarin.Forms, queste bitmap della piattaforma sono di tipo [`FileImageSource`](xref:Xamarin.Forms.FileImageSource). Li si utilizza per:

- proprietà [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) di[`MenuItem`](xref:Xamarin.Forms.MenuItem)
- proprietà [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) di[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- proprietà [`Image`](xref:Xamarin.Forms.Button) di`Button`

Gli assembly della piattaforma contengono già bitmap per icone e schermate iniziali:

- Nel progetto iOS, nella cartella **Risorse**
- Nel progetto Android, nelle sottocartelle della cartella **Risorse**
- Nei progetti Windows, nella cartella **Assets** (anche se le piattaforme Windows non limitano le bitmap a tale cartella)

[**L'esempio PlatformBitmaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) usa il codice per visualizzare un'icona dai progetti di applicazione della piattaforma.

### <a name="bitmap-resolutions"></a>Risoluzioni bitmap

Tutte le piattaforme consentono di archiviare più versioni di immagini bitmap per diverse risoluzioni del dispositivo. In fase di esecuzione, la versione corretta viene caricata in base alla risoluzione del dispositivo dello schermo.

In iOS, queste bitmap sono differenziate da un suffisso sul nome del file:

- Nessun suffisso per 160 dispositivi DPI (1 pixel per l'unità indipendente dal dispositivo)
- '@2xsuffisso per 320 dispositivi DPI (2 pixel al DIU)
- '@3xsuffisso per 480 dispositivi DPI (3 pixel al DIU)

Una bitmap destinata a essere visualizzata come quadrato di un pollice esisterebbe in tre versioni:

- MyImage.jpg a 160 pixel quadrati
- MyImage@2x.jpga 320 pixel quadrati
- MyImage@3x.jpga 480 pixel quadrati

Il programma farebbe riferimento a questa bitmap come MyImage.jpg, ma la versione corretta viene recuperata in fase di esecuzione in base alla risoluzione dello schermo. Quando non vincolata, la bitmap verrà sempre eseguito il rendering a 160 unità indipendenti dal dispositivo.

Per Android, le bitmap vengono archiviate in varie sottocartelle della cartella **Resources:**

- drawable-ldpi (basso DPI) per 120 dispositivi DPI (0,75 pixel al DIU)
- drawable-mdpi (medio) per 160 dispositivi DPI (1 pixel al DIU)
- drawable-hdpi (alto) per 240 dispositivi DPI (1,5 pixel al DIU)
- drawable-xhdpi (extra high) per 320 dispositivi DPI (2 pixel al DIU)
- drawable-xxhdpi (extra high) per 480 dispositivi DPI (3 pixel al DIU)
- drawable-xxxhdpi (tre massimi aggiuntivi) per 640 dispositivi DPI (4 pixel al DIU)

Per una bitmap destinata al rendering con un pollice quadrato, le varie versioni della bitmap avranno lo stesso nome ma di dimensioni diverse e verranno archiviate in queste cartelle:

- drawable-ldpi/MyImage.jpg a 120 pixel quadrati
- drawable-mdpi/MyImage.jpg a 160 pixel quadrati
- drawable-hdpi/MyImage.jpg a 240 pixel quadrati
- drawable-xhdpi/MyImage.jpg a 320 pixel quadrati
- drawable-xxhdpi/MyImage.jpg a 480 pixel quadrati
- drawable-xxxhdpi/MyImage.jpg a 640 pixel quadrati

Il rendering della bitmap verrà sempre eseguito a 160 unità indipendenti dal dispositivo. Il modello di soluzione Xamarin.Forms standard include solo le cartelle hdpi, xhdpi e xxhdpi.

Il progetto UWP supporta uno schema di denominazione bitmap costituito da un fattore di scala in pixel per unità indipendente dal dispositivo come percentuale, ad esempio:The UWP project supports a bitmap naming scheme that consists of a scaling factor in pixels per device-independent unit as a percentage, for example:

- MyImage.scale-200.jpg a 320 pixel quadrati

Sono valide solo alcune percentuali. I programmi di esempio per questo libro includono solo immagini con suffissi **di scala 200,** ma i modelli di soluzione Xamarin.Forms correnti includono **scale-100**, **scale-125**, **scale-150**e **scale-400**.

Quando si aggiungono bitmap ai progetti della piattaforma, l'azione di compilazione deve essere:When adding bitmaps to the platform **projects,** the Build Action should be:

- iOS: BundleResourceIOS: **BundleResource**
- Android: **AndroidResourceAndroid: AndroidResource**
- UWP: **Contenuto**

[**L'esempio ImageTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) crea due oggetti `Image` simili `TapGestureRecognizer` a pulsanti costituiti da elementi con un oggetto installato. Si prevede che gli oggetti siano quadrati di un pollice. La `Source` proprietà `Image` di `OnPlatform` viene `On` impostata utilizzando e gli oggetti per fare riferimento a nomi di file potenzialmente diversi sulle piattaforme. Le immagini bitmap includono numeri che ne indicano le dimensioni in pixel, in modo da poter vedere quale bitmap di dimensioni viene recuperata e sottoposta a rendering.

### <a name="toolbars-and-their-icons"></a>Barre degli strumenti e relative icone

Uno degli utilizzi principali delle bitmap specifiche della piattaforma è la barra degli [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) strumenti Xamarin.Forms, costruita aggiungendo oggetti all'insieme [`ToolbarItems`](xref:Xamarin.Forms.Page.ToolbarItems) definito da `Page`. `ToobarItem`deriva da [`MenuItem`](xref:Xamarin.Forms.MenuItem) cui eredita alcune proprietà.

Le proprietà `ToolbarItem` più importanti sono:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)per il testo che potrebbe apparire a seconda della piattaforma e`Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)di `FileImageSource` tipo per l'immagine che potrebbe apparire a seconda della piattaforma e`Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)di [`ToolbarItemOrder`](xref:Xamarin.Forms.ToolbarItemOrder)tipo , un'enumerazione [`Primary`](xref:Xamarin.Forms.ToolbarItemOrder.Primary)con [`Secondary`](xref:Xamarin.Forms.ToolbarItemOrder.Secondary)tre membri, [`Default`](xref:Xamarin.Forms.ToolbarItemOrder.Default), , e .

Il numero `Primary` di articoli deve essere limitato a tre o quattro. È necessario `Text` includere un'impostazione per tutti gli elementi. Per la maggior `Primary` parte delle `Icon` piattaforme, solo `Icon` gli elementi richiedono un ma Windows 8.1 richiede un per tutti gli elementi. Le icone devono essere 32 unità indipendenti dal dispositivo quadrato. Il `FileImageSource` tipo indica che sono specifici della piattaforma.

Il `ToolbarItem` genera [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) un evento quando viene `Button`toccato, proprio come un file . `ToolbarItem`supporti [`Command`](xref:Xamarin.Forms.MenuItem.Command) e [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) proprietà spesso utilizzati in connessione con MVVM. (Vedere [il capitolo 18, MVVM](chapter18.md)).

Sia iOS che Android richiedono che una [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) pagina che visualizza una `NavigationPage`barra degli strumenti sia una o una pagina su cui ci si sposta da un oggetto . Il programma [**ToolbarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) imposta `MainPage` `App` la proprietà della `ContentPage` relativa classe sul [ `NavigationPage` costruttore](xref:Xamarin.Forms.NavigationPage.%23ctor(Xamarin.Forms.Page)) con un argomento e illustra la costruzione e il gestore eventi di una barra degli strumenti.

### <a name="button-images"></a>Immagini dei pulsanti

È anche possibile usare bitmap specifiche [`Image`](xref:Xamarin.Forms.Button.Image) della `Button` piattaforma per impostare la proprietà di su una bitmap di 32 unità indipendenti dal dispositivo quadrato, come illustrato nel [**ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) esempio.

> [!NOTE]
> L'uso delle immagini sui pulsanti è stato migliorato. Consultate [Utilizzo di bitmap con pulsanti.](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 13 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Esempi del capitolo 13](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Utilizzo delle immagini](~/xamarin-forms/user-interface/images.md)
- [Utilizzo di bitmap con pulsanti](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
