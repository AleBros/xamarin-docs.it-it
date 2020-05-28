---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 13. Bitmaps''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 43caf088ad6cb816f049e7862a287c17839c2170
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136773"
---
# <a name="summary-of-chapter-13-bitmaps"></a>Riepilogo del capitolo 13. Bitmap

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE]
> Le note in questa pagina indicano Xamarin.Forms le aree in cui è stato divergente rispetto al materiale presentato nel libro.

L' Xamarin.Forms [`Image`](xref:Xamarin.Forms.Image) elemento Visualizza una bitmap. Tutte le Xamarin.Forms piattaforme supportano i formati di file JPEG, PNG, gif e BMP.

Le bitmap in Xamarin.Forms provengono da quattro posizioni:

- Sul Web come specificato da un URL
- Incorporato come risorsa nella libreria condivisa
- Incorporata come risorsa nei progetti di applicazione della piattaforma
- Da qualsiasi posizione a cui è possibile fare riferimento da un `Stream` oggetto .NET, tra cui`MemoryStream`

Le risorse bitmap nella libreria condivisa sono indipendenti dalla piattaforma, mentre le risorse bitmap nei progetti della piattaforma sono specifiche della piattaforma.

> [!NOTE]
> Il testo del libro fa riferimento a librerie di classi portabili, che sono state sostituite dalle librerie .NET Standard. Tutto il codice di esempio del libro è stato convertito in modo da usare le librerie .NET standard.

La bitmap viene specificata impostando la [`Source`](xref:Xamarin.Forms.Image.Source) proprietà di `Image` su un oggetto di tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) , una classe astratta con tre derivati:

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource)per accedere a una bitmap sul Web in base a un `Uri` oggetto impostato sulla relativa [`Uri`](xref:Xamarin.Forms.UriImageSource.Uri) Proprietà
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource)per accedere a una bitmap archiviata in un progetto di applicazione della piattaforma in base a una cartella e a un percorso di file impostati sulla relativa [`File`](xref:Xamarin.Forms.FileImageSource.File) Proprietà
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource)per caricare una bitmap usando un `Stream` oggetto .NET specificato restituendo un oggetto `Stream` da un `Func` set alla relativa [`Stream`](xref:Xamarin.Forms.StreamImageSource.Stream) Proprietà

In alternativa, e più comunemente, è possibile usare i metodi statici seguenti della `ImageSource` classe, che restituiscono tutti `ImageSource` gli oggetti:

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))per accedere a una bitmap sul Web in base a un `Uri` oggetto
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)per accedere a una bitmap archiviata come risorsa incorporata nell'applicazione PCL; [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type))o [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) per accedere a una bitmap in un altro assembly di origine
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String))per accedere a una bitmap da un progetto di applicazione della piattaforma
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream}))per il caricamento di una bitmap basata su un `Stream` oggetto

Non esiste alcuna classe equivalente dei `Image.FromResource` metodi. La `UriImageSource` classe è utile se è necessario controllare la memorizzazione nella cache. La `FileImageSource` classe è utile in XAML. `StreamImageSource`è utile per il caricamento asincrono degli `Stream` oggetti, mentre `ImageSource.FromStream` è sincrono.

## <a name="platform-independent-bitmaps"></a>Bitmap indipendenti dalla piattaforma

Il progetto [**WebBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) carica una bitmap sul Web usando `ImageSource.FromUri` . L' `Image` elemento viene impostato sulla `Content` proprietà di `ContentPage` , pertanto è vincolato alle dimensioni della pagina. Indipendentemente dalle dimensioni della bitmap, un elemento soggetto a vincoli `Image` viene esteso alle dimensioni del relativo contenitore e la bitmap viene visualizzata nella dimensione massima all'interno dell' `Image` elemento mantenendo le proporzioni della bitmap. Le aree di `Image` oltre la bitmap possono essere colorate con [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) .

L'esempio [**WebBitmapXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) è simile, ma semplicemente imposta la `Source` proprietà sull'URL. La conversione viene gestita dalla [`ImageSourceConverter`](xref:Xamarin.Forms.ImageSourceConverter) classe.

### <a name="fit-and-fill"></a>Adatta e riempi

È possibile controllare il modo in cui la bitmap viene estesa impostando la [`Aspect`](xref:Xamarin.Forms.Image.Aspect) proprietà di `Image` su uno dei seguenti membri dell' [`Aspect`](xref:Xamarin.Forms.Aspect) enumerazione:

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): rispetta le proporzioni (impostazione predefinita)
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): riempie l'area, non rispetta le proporzioni
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): riempie l'area ma rispetta le proporzioni, eseguite mediante il ritaglio di una parte della bitmap

### <a name="embedded-resources"></a>Risorse incorporate

È possibile aggiungere un file bitmap a una libreria di classi portabile o a una cartella nella libreria di classi portabile (PCL). Assegnare un' **azione di compilazione** di **EmbeddedResource**. Nell'esempio [**ResourceBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) viene illustrato come utilizzare `ImageSource.FromResource` per caricare il file. Il nome della risorsa passato al metodo è costituito dal nome dell'assembly, seguito da un punto, seguito dal nome della cartella facoltativa e da un punto, seguito dal nome del file.

Il programma imposta le `VerticalOptions` `HorizontalOptions` proprietà e di `Image` su `LayoutOptions.Center` , rendendo l'elemento non `Image` vincolato. Le `Image` dimensioni di e della bitmap sono le stesse:

- In iOS e Android, `Image` è la dimensione in pixel della bitmap. Esiste un mapping uno-a-uno tra pixel bitmap e pixel dello schermo.
- In piattaforma UWP (Universal Windows Platform), `Image` è la dimensione in pixel della bitmap in unità indipendenti dal dispositivo. Nella maggior parte dei dispositivi ogni pixel bitmap occupa più pixel dello schermo.

L'esempio [**StackedBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) inserisce un oggetto `Image` in un oggetto Vertical `StackLayout` in XAML. Un'estensione di markup denominata [`ImageResourceExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) consente di fare riferimento alla risorsa incorporata in XAML. Questa classe carica solo le risorse dall'assembly in cui si trova, quindi non può essere inserita in una libreria.

### <a name="more-on-sizing"></a>Ulteriori informazioni sul dimensionamento

Spesso è consigliabile ridimensionare in modo coerente le bitmap tra tutte le piattaforme.
Con la sperimentazione di **StackedBitmap**, è possibile impostare un oggetto `WidthRequest` sull' `Image` elemento in un oggetto verticale `StackLayout` per rendere le dimensioni coerenti tra le piattaforme, ma è possibile ridurre solo le dimensioni utilizzando questa tecnica.

È anche possibile impostare l'oggetto `HeightRequest` in modo che le dimensioni dell'immagine siano coerenti sulle piattaforme, ma la larghezza vincolata della bitmap limiterà la versatilità di questa tecnica. Per un'immagine in verticale `StackLayout` , `HeightRequest` è consigliabile evitare.

L'approccio migliore consiste nell'iniziare con una bitmap più ampia della larghezza del telefono in unità indipendenti dal dispositivo e impostare `WidthRequest` sulla larghezza desiderata in unità indipendenti dal dispositivo. Questa operazione è illustrata nell'esempio [**DeviceIndBitmapSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize) .

Il [**MadTeaParty**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) Visualizza il capitolo 7 delle avventure di Alice di Lewis Carroll *in Wonderland* con le illustrazioni originali di John Tenniel:

[![Schermata tripla della parte pazza del tè](images/ch13fg16-small.png "Testo del libro di Mad Hatters Tea Party")](images/ch13fg16-large.png#lightbox "Testo del libro di Mad Hatters Tea Party")

### <a name="browsing-and-waiting"></a>Esplorazione e attesa

L'esempio [**ImageBrowser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) consente all'utente di spostarsi tra le immagini predefinite archiviate nel sito Web Novell. Usa la classe .NET [`WebRequest`](xref:System.Net.WebRequest) per scaricare un file JSON con l'elenco delle bitmap.

> [!NOTE]
> Xamarin.Formsi programmi devono usare [`HttpClient`](xref:System.Net.Http.HttpClient) anziché [`WebRequest`](xref:System.Net.WebRequest) per accedere ai file tramite Internet.

Il programma usa un [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) per indicare che è in corso un evento. Quando ogni bitmap viene caricata, la [`IsLoading`](xref:Xamarin.Forms.Image.IsLoading) proprietà di sola lettura di `Image` è `true` . La proprietà `IsLoading` è supportata da una proprietà associabile, pertanto `PropertyChanged` viene generato un evento quando la proprietà viene modificata. Il programma connette un gestore a questo evento e usa l'impostazione corrente di `IsLoaded` per impostare la [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) proprietà dell'oggetto `ActivityIndicator` .

## <a name="streaming-bitmaps"></a>Bitmap di streaming

Il `ImageSource.FromStream` metodo crea un oggetto `ImageSource` basato su .NET `Stream` . Al metodo deve essere passato un `Func` oggetto che restituisce un `Stream` oggetto.

### <a name="accessing-the-streams"></a>Accesso ai flussi

Nell'esempio [**BitmapStreams**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) viene illustrato come utilizzare il `ImaageSource.FromStream` metodo per caricare una bitmap archiviata come risorsa incorporata e caricare una bitmap sul Web.

### <a name="generating-bitmaps-at-run-time"></a>Generazione di bitmap in fase di esecuzione

Tutte le Xamarin.Forms piattaforme supportano il formato di file BMP non compresso, che è facile da costruire nel codice e quindi archiviare in un `MemoryStream` . Questa tecnica consente a algoritmicamente di creare bitmap in fase di esecuzione, implementate nella [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) classe nella libreria **Xamarin. FormsBook. Toolkit** .

Nell'esempio [**DiyGradientBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) "fai da te" viene illustrato l'uso di `BmpMaker` per creare una bitmap con un'immagine di sfumatura.

## <a name="platform-specific-bitmaps"></a>Bitmap specifiche della piattaforma

Tutte le Xamarin.Forms piattaforme consentono di archiviare bitmap negli assembly di applicazioni della piattaforma. Quando vengono recuperate da un' Xamarin.Forms applicazione, le bitmap della piattaforma sono di tipo [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) . Vengono usati per:

- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)proprietà di[`MenuItem`](xref:Xamarin.Forms.MenuItem)
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)proprietà di[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- [`Image`](xref:Xamarin.Forms.Button)proprietà di`Button`

Gli assembly della piattaforma contengono già bitmap per le icone e le schermate iniziali:

- Nel progetto iOS, nella cartella **risorse**
- Nel progetto Android, in sottocartelle della cartella **risorse**
- Nei progetti Windows, nella cartella **Asset** (anche se le piattaforme Windows non limitano le bitmap a tale cartella)

L'esempio [**PlatformBitmaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) usa il codice per visualizzare un'icona dai progetti di applicazione della piattaforma.

### <a name="bitmap-resolutions"></a>Risoluzioni bitmap

Tutte le piattaforme consentono l'archiviazione di più versioni di immagini bitmap per risoluzioni del dispositivo diverse. In fase di esecuzione, la versione corretta viene caricata in base alla risoluzione del dispositivo dello schermo.

In iOS le bitmap sono differenziate in base a un suffisso nel nome file:

- Nessun suffisso per i dispositivi DPI 160 (1 pixel per l'unità indipendente dal dispositivo)
- @2xsuffisso '' per i dispositivi DPI 320 (2 pixel a Diu)
- @3xsuffisso '' per i dispositivi DPI 480 (da 3 pixel a Diu)

Una bitmap progettata per essere visualizzata come un quadrato da un pollice esiste in tre versioni:

- Image. jpg al quadrato di 160 pixel
- MyImage@2x.jpgal quadrato di 320 pixel
- MyImage@3x.jpgal quadrato di 480 pixel

Il programma fa riferimento a questa bitmap come Image. jpg, ma la versione corretta viene recuperata in fase di esecuzione in base alla risoluzione dello schermo. Quando non è vincolato, viene sempre eseguito il rendering della bitmap a 160 unità indipendenti dal dispositivo.

Per Android, le bitmap vengono archiviate in varie sottocartelle della cartella **Resources** :

- disegnatore-ldpi (DPI basso) per dispositivi 120 DPI (0,75 pixel a DIU)
- disegnatore-mdpi (medio) per i dispositivi DPI 160 (1 pixel per DIU)
- disegnatore-HDPI (alta) per dispositivi 240 DPI (1,5 pixel a DIU)
- disegnatore-xhdpi (molto elevato) per i dispositivi DPI 320 (2 pixel per DIU)
- disegnatore-XXHDPI (molto più alto) per i dispositivi 480 DPI (da 3 pixel a DIU)
- disegnatore-xxxhdpi (tre livelli aggiuntivi) per i dispositivi DPI 640 (da 4 pixel a DIU)

Per una bitmap progettata per essere sottoposta a rendering su un pollice quadrato, le varie versioni della bitmap avranno lo stesso nome ma una dimensione diversa e verranno archiviate in queste cartelle:

- drawable-ldpi/image. jpg al quadrato di 120 pixel
- drawable-mdpi/image. jpg al quadrato di 160 pixel
- drawable-HDPI/image. jpg al quadrato di 240 pixel
- drawable-xhdpi/image. jpg al quadrato di 320 pixel
- drawable-XXHDPI/image. jpg al quadrato di 480 pixel
- drawable-xxxhdpi/image. jpg al quadrato di 640 pixel

Viene sempre eseguito il rendering della bitmap a 160 unità indipendenti dal dispositivo. Il modello di Xamarin.Forms soluzione standard include solo le cartelle HDPI, xhdpi e XXHDPI.

Il progetto UWP supporta uno schema di denominazione bitmap costituito da un fattore di scala in pixel per unità indipendente dal dispositivo come percentuale, ad esempio:

- Image. scale-200. jpg al quadrato di 320 pixel

Sono valide solo alcune percentuali. I programmi di esempio per questo libro includono solo immagini con suffissi **scale-200** , ma i Xamarin.Forms modelli di soluzione correnti includono **scale-100**, **scale-125**, **scale-150**e **Scale-400**.

Quando si aggiungono bitmap ai progetti della piattaforma, l' **azione di compilazione** deve essere la seguente:

- iOS: **BundleResource**
- Android: **AndroidResource**
- UWP: **contenuto**

L'esempio [**ImageTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) crea due oggetti di tipo Button costituiti da `Image` elementi con `TapGestureRecognizer` installato. È previsto che gli oggetti siano quadrati da un pollice. La `Source` proprietà di `Image` viene impostata utilizzando `OnPlatform` `On` gli oggetti e per fare riferimento a nomi file potenzialmente diversi sulle piattaforme. Le immagini bitmap includono numeri che ne indicano le dimensioni in pixel, quindi è possibile vedere quali dimensioni bitmap vengono recuperate e sottoposte a rendering.

### <a name="toolbars-and-their-icons"></a>Barre degli strumenti e relative icone

Uno degli usi principali delle bitmap specifiche della piattaforma è la Xamarin.Forms barra degli strumenti, che viene costruita aggiungendo [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) oggetti alla [`ToolbarItems`](xref:Xamarin.Forms.Page.ToolbarItems) raccolta definita da `Page` . `ToobarItem`deriva da [`MenuItem`](xref:Xamarin.Forms.MenuItem) da cui eredita alcune proprietà.

Di seguito sono riportate le proprietà più importanti `ToolbarItem` :

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)per il testo che potrebbe apparire a seconda della piattaforma e`Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)di tipo `FileImageSource` per l'immagine che può apparire a seconda della piattaforma e`Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)di tipo [`ToolbarItemOrder`](xref:Xamarin.Forms.ToolbarItemOrder) , un'enumerazione con tre membri, [`Default`](xref:Xamarin.Forms.ToolbarItemOrder.Default) , [`Primary`](xref:Xamarin.Forms.ToolbarItemOrder.Primary) e [`Secondary`](xref:Xamarin.Forms.ToolbarItemOrder.Secondary) .

Il numero di `Primary` elementi deve essere limitato a tre o quattro. È necessario includere un' `Text` impostazione per tutti gli elementi. Per la maggior parte delle piattaforme, solo gli `Primary` elementi richiedono un, `Icon` ma Windows 8.1 richiede un `Icon` per tutti gli elementi. Le icone devono essere di 32 unità indipendenti dal dispositivo. Il `FileImageSource` tipo indica che sono specifici della piattaforma.

`ToolbarItem`Genera un [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) evento quando viene toccato, molto simile a un `Button` . `ToolbarItem`supporta inoltre [`Command`](xref:Xamarin.Forms.MenuItem.Command) le [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) proprietà e spesso utilizzate nella connessione a MVVM. (Vedere il [capitolo 18, MVVM](chapter18.md)).

Per iOS e Android è necessario che una pagina che visualizza una barra degli strumenti sia una [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) pagina o una pagina a cui è stato passato un oggetto `NavigationPage` . Il programma [**ToolbarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) imposta la `MainPage` proprietà della relativa `App` classe sul [ `NavigationPage` Costruttore] (xrif: Xamarin.Forms . NavigationPage .% 23ctor ( Xamarin.Forms . )) Con un `ContentPage` argomento e illustra la costruzione e il gestore eventi di una barra degli strumenti.

### <a name="button-images"></a>Immagini di pulsanti

È anche possibile usare bitmap specifiche della piattaforma per impostare la [`Image`](xref:Xamarin.Forms.Button.Image) proprietà di `Button` su una bitmap di 32 unità indipendenti dal dispositivo, come illustrato nell'esempio [**ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) .

> [!NOTE]
> L'uso di immagini sui pulsanti è stato migliorato. Vedere [uso di bitmap con i pulsanti](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 13 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Esempi capitolo 13](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Uso delle immagini](~/xamarin-forms/user-interface/images.md)
- [Uso di bitmap con pulsanti](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
