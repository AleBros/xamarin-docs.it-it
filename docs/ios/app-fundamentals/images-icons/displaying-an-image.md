---
title: Visualizzazione di un'immagine in Xamarin.iOS
description: Questo articolo illustra l'inclusione di un asset immagine in un'app Xamarin.iOS e la visualizzazione dell' C# immagine usando il codice o assegnando il codice a un controllo in iOS designer.
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/24/2018
ms.openlocfilehash: cda45f01dae2dc17c2517a7f013acacde7906a4b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004489"
---
# <a name="displaying-an-image-in-xamarinios"></a>Visualizzazione di un'immagine in Xamarin.iOS

_Questo articolo illustra l'inclusione di un asset immagine in un'app Xamarin.iOS e la visualizzazione dell' C# immagine usando il codice o assegnando il codice a un controllo in iOS designer._

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>Aggiunta e organizzazione di immagini in un'app Xamarin.iOS

Quando si aggiunge un'immagine da usare in un'app Xamarin.iOS, lo sviluppatore userà un _Catalogo asset_ per supportare ogni dispositivo iOS e la risoluzione richiesta da un'app.

Aggiunto in iOS 7, i **set di immagini dei cataloghi asset** contengono tutte le versioni o rappresentazioni di un'immagine che sono necessarie per supportare i vari dispositivi e i fattori di scalabilità per un'app. Invece di basarsi sul nome file delle risorse immagine, i **set di immagini** usano un file JSON per specificare quale immagine appartiene a quale dispositivo e/o risoluzione. Questo è il modo migliore per gestire e supportare le immagini in iOS (da iOS 9 o versione successiva).

## <a name="adding-images-to-an-asset-catalog-image-set"></a>Aggiunta di immagini a un set di immagini del catalogo asset

Come indicato in precedenza, i **set di immagini di cataloghi asset** contengono tutte le versioni o rappresentazioni di un'immagine che sono necessarie per supportare i vari dispositivi e i fattori di scalabilità per un'app. Invece di basarsi sul nome file delle risorse immagine, i **set di immagini** usano un file JSON per specificare quale immagine appartiene a quale dispositivo e/o risoluzione.

Per creare un nuovo set di immagini e aggiungervi immagini, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nella **Esplora soluzioni**fare doppio clic sul file `Assets.xcassets` per aprirlo per la modifica:

    ![](displaying-an-image-images/imageset01.png "The Assets.xcassets in the Solution Explorer")
2. Fare clic con il pulsante destro del mouse sull' **elenco asset** e scegliere **nuovo set di immagini**:

    ![](displaying-an-image-images/imageset02.png "Adding a New Image Set")
3. Selezionare il nuovo set di immagini e l'editor verrà visualizzato:

    ![](displaying-an-image-images/imageset03.png "The Image Set editor")
4. Da qui, trascinare le immagini per ognuno dei diversi dispositivi e risoluzioni necessarie.
5. Fare doppio clic sul **nome** del nuovo set di immagini nell' **elenco asset** per modificarlo:![](displaying-an-image-images/imageset04.png "Modifica del nome del nuovo set di immagini")

Quando si usa un **set di immagini** in iOS designer, è sufficiente selezionare il nome del set dall'elenco a discesa nell'editor delle proprietà:

![](displaying-an-image-images/imageset06.png "Select an image set's name from the dropdown list")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Aprire il catalogo asset dal **Esplora soluzioni**e nell'angolo superiore sinistro fare clic sul pulsante con il **segno più** :

    ![](displaying-an-image-images/asset5.png "Click the Plus button")

2. Selezionare **Aggiungi set di immagini** e l'editor del set di immagini verrà visualizzato per il nuovo set di immagini. Da qui, trascinare le immagini per ognuno dei diversi dispositivi e risoluzioni necessarie.

    ![](displaying-an-image-images/asset7.png "The image set editor")

### <a name="renaming-an-image-set"></a>Ridenominazione di un set di immagini

Per rinominare un set di immagini, eseguire le operazioni seguenti:

1. Nella **Esplora soluzioni**fare doppio clic sul file del **Catalogo asset** per aprirlo per la modifica:

    ![](displaying-an-image-images/rename01.png "The Asset Catalog in the Solution Explorer")
2. Selezionare il **set di immagini** da rinominare:

    ![](displaying-an-image-images/rename02.png "Select the Image Set to rename")
3. In **Esplora proprietà**scorrere fino alla fine e selezionare **nome**(nella sezione **varie** ):

    ![](displaying-an-image-images/rename03.png "Select Name under the Misc section")
4. Immettere un nuovo **nome** per il **set di immagini** e salvare le modifiche.

-----

Quando si usa un' **immagine impostata** nel codice, farvi riferimento tramite il nome chiamando il metodo `FromBundle` della classe `UIImage`. Di seguito è riportato un esempio:

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> Se le immagini assegnate a un set di immagini non vengono visualizzate correttamente, assicurarsi che venga usato il nome file corretto con il metodo `FromBundle` (il **set di immagini** e non il nome del **Catalogo asset** padre). Per le immagini PNG è possibile omettere l'estensione `.png`. Per altri formati di immagine, è necessaria l'estensione, ad esempio `PurpleMonkey.jpg`).

### <a name="using-vector-images-in-asset-catalogs"></a>Uso di immagini vettoriali in cataloghi asset

A partire da iOS 8, è stata aggiunta una speciale classe di **vettori** ai **set di immagini** che consente allo sviluppatore di includere un'immagine vettoriale formattata in **formato PDF** nel vassoio, includendo invece singoli file bitmap con le diverse risoluzioni. Usando questo metodo, specificare un singolo file Vector per la risoluzione del `@1x` (formattato come file PDF vettoriale) e le versioni `@2x` e `@3x` del file verranno generate in fase di compilazione e incluse nel bundle dell'applicazione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](displaying-an-image-images/imageset05.png "Vector Images in the Asset Catalogs editor")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/asset8.png "Vector Images in the Asset Catalogs editor")

-----

Se, ad esempio, lo sviluppatore include un file di `MonkeyIcon.pdf` come vettore di un catalogo asset con una risoluzione di 150px x 150px, le risorse bitmap seguenti verranno incluse nel bundle dell'app finale al momento della compilazione:

- `MonkeyIcon@1x.png`-150px x 150px risoluzione.
- `MonkeyIcon@2x.png`-300px x 300px risoluzione.
- `MonkeyIcon@3x.png`-450px x 450px risoluzione.

Quando si usano le immagini vettoriali PDF nei cataloghi di asset, è necessario tenere in considerazione quanto segue:

- Non si tratta di un supporto vettoriale completo poiché il formato PDF verrà rasterizzato in una bitmap in fase di compilazione e le bitmap fornite nell'applicazione finale.
- Non è possibile modificare le dimensioni dell'immagine dopo che è stata impostata nel catalogo asset. Se lo sviluppatore tenta di ridimensionare l'immagine (nel codice o usando il layout automatico e le classi di dimensioni), l'immagine verrà distorta esattamente come qualsiasi altra bitmap.
- I cataloghi asset sono compatibili solo con iOS 7 e versioni successive, se un'app deve supportare iOS 6 o versioni precedenti, non può usare cataloghi asset.

## <a name="working-with-template-images"></a>Uso delle immagini modello

In base alla progettazione di un'app iOS, in alcuni casi lo sviluppatore deve personalizzare un'icona o un'immagine all'interno dell'interfaccia utente in modo che corrisponda a una modifica nella combinazione di colori, ad esempio in base alle preferenze dell'utente.

Per ottenere facilmente questo effetto, impostare la _modalità di rendering_ dell'asset immagine sull' **immagine modello**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](displaying-an-image-images/templateimage01.png "The Render Mode set to Template Image")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage01vs.png "The Render Mode set to Template")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

Dalla finestra di progettazione di iOS assegnare l'asset immagine a un controllo dell'interfaccia utente, quindi impostare la **tinta** per colorare l'immagine:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](displaying-an-image-images/templateimage03.png "Set the Tint to colorize the image")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage03vs.png "Set the Tint to colorize the image")](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

Facoltativamente, l'asset di immagine e la tinta possono essere impostati direttamente nel codice:

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

Per usare completamente un'immagine modello dal codice, eseguire le operazioni seguenti:

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

Poiché la proprietà `RenderMode` di un `UIImage` è di sola lettura, utilizzare il metodo `ImageWithRenderingMode` per creare una nuova istanza dell'immagine con l'impostazione della modalità di rendering desiderata.

Esistono tre possibili impostazioni per `UIImage.RenderMode` tramite l'enumerazione `UIImageRenderingMode`:

- `AlwaysOriginal`: forza il rendering dell'immagine come file di immagine di origine originale senza alcuna modifica.
- `AlwaysTemplate`: forza il rendering dell'immagine come immagine modello colorando i pixel con il colore di `Tint` specificato.
- `Automatic`: esegue il rendering dell'immagine come modello o originale in base all'ambiente in cui viene usato. Se, ad esempio, l'immagine viene usata in un `UIToolBar`, `UINavigationBar`, `UITabBar` o `UISegmentControl` verrà considerato come un modello.

## <a name="adding-new-assets-collections"></a>Aggiunta di nuove raccolte di asset

Quando si lavora con le immagini nei cataloghi asset, a volte è possibile che venga richiesta una nuova raccolta, anziché aggiungere tutte le immagini dell'app alla raccolta di `Assets.xcassets`. Ad esempio, quando si progettano risorse su richiesta.

Per aggiungere un nuovo catalogo asset al progetto:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare clic con il pulsante destro del mouse sul **nome del progetto** nella **Esplora soluzioni** e scegliere **Aggiungi** > **nuovo file...**
2. Selezionare **iOS** > **Asset Catalog**, immettere un **nome** per la raccolta e fare clic sul pulsante **nuovo** :

    ![](displaying-an-image-images/asset01.png "Creating a new Asset Catalog")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Nella Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella **cataloghi asset** e scegliere **Aggiungi > nuovo catalogo asset**.
2. Assegnare un nome e fare clic su **Aggiungi**:

    ![](displaying-an-image-images/asset1.png "Creating a new Asset Catalog")

-----

Da qui, è possibile usare la raccolta in modo analogo alla raccolta `Assets.xcassets` predefinita inclusa automaticamente nel progetto.

## <a name="using-images-with-controls"></a>Utilizzo di immagini con controlli

Oltre a usare le immagini per supportare un'app, iOS usa anche immagini con tipi di controllo delle app, ad esempio barre di tabulazione, barre degli strumenti, barre di navigazione, tabelle e pulsanti. Un modo semplice per visualizzare un'immagine in un controllo consiste nell'assegnare un'istanza di `UIImage` alla proprietà `Image` del controllo.

### <a name="frombundle"></a>FromBundle

La chiamata al metodo `FromBundle` è una chiamata sincrona (blocco) che dispone di una serie di funzionalità di gestione e caricamento di immagini predefinite, ad esempio il supporto per la memorizzazione nella cache e la gestione automatica dei file di immagine per diverse soluzioni.

Nell'esempio seguente viene illustrato come impostare l'immagine di un `UITabBarItem` in un `UITabBar`:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Supponendo che `MyImage` sia il nome di un asset immagine aggiunto a un catalogo asset sopra riportato. Quando si utilizzano immagini del catalogo asset, è sufficiente specificare il nome del set di immagini nel metodo `FromBundle` per le immagini formattate **png** :

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Per qualsiasi altro formato di immagine, includere l'estensione con il nome. Esempio:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

Per altre informazioni sulle icone e le immagini, vedere la documentazione di Apple sulle [linee guida personalizzate](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)per la creazione di icone e immagini.

## <a name="displaying-an-image-in-a-storyboards"></a>Visualizzazione di un'immagine in uno storyboard

Dopo che un'immagine è stata aggiunta a un progetto Xamarin.iOS usando un catalogo asset, può essere visualizzata facilmente in uno storyboard usando un `UIImageView` in iOS designer. Ad esempio, se è stato aggiunto il seguente asset immagine:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](displaying-an-image-images/display01.png "A sample Image Asset has been added")

Per visualizzarlo in uno storyboard, eseguire le operazioni seguenti:

1. Fare doppio clic sul file `Main.storyboard` nel **Esplora soluzioni** per aprirlo per la modifica in iOS designer.
2. Selezionare una **visualizzazione immagine** dalla **casella degli strumenti**:

     ![](displaying-an-image-images/display02.png "Select an Image View from the Toolbox")
3. Trascinare la visualizzazione immagine nell'area di progettazione e posizionarla e ridimensionarla come richiesto:

    ![](displaying-an-image-images/display03.png "A new Image View on the Design Surface")
4. Nella sezione **widget** di **Esplora proprietà** selezionare l'asset **immagine** desiderato da visualizzare:

    ![](displaying-an-image-images/display04.png "Select the desired Image asset to be displayed")
5. Nella sezione **View** usare la **modalità** per controllare come verrà ridimensionata l'immagine quando la **visualizzazione immagine** viene ridimensionata.
6. Con la **visualizzazione immagine** selezionata, fare di nuovo clic su di essa per aggiungere i **vincoli**:

    ![](displaying-an-image-images/display05.png "Adding Constraints")
7. Trascinare il quadratino a forma di "T" su ogni bordo della **visualizzazione immagine** sul lato corrispondente dello schermo per "aggiungere" l'immagine ai lati. In questo modo, la **visualizzazione dell'immagine** verrà compattata e aumentata Man mano che lo schermo viene ridimensionato.
8. Salvare le modifiche apportate allo storyboard.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/display01vs.png "A sample Image Asset has been added")

Per visualizzarlo in uno storyboard, eseguire le operazioni seguenti:

1. Fare doppio clic sul file `Main.storyboard` nel **Esplora soluzioni** per aprirlo per la modifica in iOS designer.
2. Selezionare una **visualizzazione immagine** dalla **casella degli strumenti**:

     ![](displaying-an-image-images/display02vs.png "Select an Image View from the Toolbox")
3. Trascinare la visualizzazione immagine nell'area di progettazione e posizionarla e ridimensionarla come richiesto:

    ![](displaying-an-image-images/display03vs.png "A new Image View on the Design Surface")
4. Nella sezione **widget** di **Esplora proprietà** selezionare l'asset **immagine** desiderato da visualizzare:

    ![](displaying-an-image-images/display04vs.png "Select the desired Image asset to be displayed")
5. Nella sezione **View** usare la **modalità** per controllare come verrà ridimensionata l'immagine quando la **visualizzazione immagine** viene ridimensionata.
6. Con la **visualizzazione immagine** selezionata, fare di nuovo clic su di essa per aggiungere i **vincoli**:

    ![](displaying-an-image-images/display05vs.png "Adding Constraints")
7. Trascinare il quadratino a forma di "T" su ogni bordo della **visualizzazione immagine** sul lato corrispondente dello schermo per "aggiungere" l'immagine ai lati. In questo modo, la **visualizzazione dell'immagine** verrà compattata e aumentata Man mano che lo schermo viene ridimensionato.
8. Salvare le modifiche apportate allo storyboard.

-----

## <a name="displaying-an-image-in-code"></a>Visualizzazione di un'immagine nel codice

Proprio come la visualizzazione di un'immagine in uno storyboard, dopo che un'immagine è stata aggiunta a un progetto Xamarin.iOS usando un catalogo asset, può essere visualizzata facilmente C# usando il codice.

Vedere l'esempio seguente:

```csharp
// Create an image view that will fill the
// parent image view and set the image from
// an Image Asset

var imageView = new UIImageView (View.Frame);
imageView.Image = UIImage.FromBundle ("Kemah");

// Add the Image View to the parent view
View.AddSubview (imageView);
```

Questo codice crea una nuova `UIImageView` e le assegna una dimensione e una posizione iniziali. Quindi carica l'immagine da un asset immagine aggiunto al progetto e aggiunge il `UIImageView` al `UIView` padre per visualizzarlo.

## <a name="related-links"></a>Collegamenti correlati

- [Uso delle immagini (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Dimensioni e risoluzione delle immagini (Apple)](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/image-size-and-resolution/)
