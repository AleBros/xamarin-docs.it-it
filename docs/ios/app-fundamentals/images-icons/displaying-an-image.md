---
title: Visualizzazione di un'immagine in xamarin. IOS
description: Questo articolo descrive incluso un asset di immagine in un'app xamarin. IOS e la visualizzazione di quell'immagine usando codice c# o tramite l'assegnazione a un controllo nella finestra di progettazione iOS.
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/24/2018
ms.openlocfilehash: 3ae63bb30c7759a1915939a2199d5ffc7dc75d15
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784273"
---
# <a name="displaying-an-image-in-xamarinios"></a>Visualizzazione di un'immagine in xamarin. IOS

_Questo articolo descrive incluso un asset di immagine in un'app xamarin. IOS e la visualizzazione di quell'immagine usando codice c# o tramite l'assegnazione a un controllo nella finestra di progettazione iOS._

In questo articolo vengono trattati gli argomenti seguenti in modo dettagliato:

- [Aggiunta e organizzare le immagini in un'App Xamarin.iOS](#adding-assets) : vengono illustrati gli asset delle immagini e come possono essere inclusi, organizzati e gestiti all'interno di un progetto xamarin. IOS.
- [Aggiunta di immagini a cataloghi Asset](#asset-catalogs) -la gestione delle immagini con i cataloghi di Asset.
    - [Utilizzo di immagini vettoriali nei cataloghi di Asset](#Using-Vector-Images-in-Asset-Catalogs) -fornendo tutte le dimensioni dell'immagine con un singolo vettore.
- [Utilizzo di immagini modello](#Working-with-Template-Images) -impostando un Asset di immagine da un'immagine modello, lo sviluppatore può colorare facilmente in modo che corrisponda modifiche al tema dell'interfaccia utente di un'app impostando l'immagine `Tint` proprietà.
- [Utilizzo di immagini con controlli](#controls) è illustrato l'utilizzo inclusi in un progetto xamarin. IOS con i controlli dell'interfaccia utente, ad esempio gli asset delle immagini: `UIButton` e `UIImageView` e come utilizzare le immagini in c# tramite la `UIImage` dell'oggetto [FromBundle](#frombundle) metodo.
- [Visualizzazione di un'immagine in un storyboard](#Displaying-an-Image-in-a-Storyboards) -fornisce un esempio di visualizzazione di un'immagine utilizzando uno Storyboard.
- [Visualizzazione di un'immagine nel codice](#Displaying-an-Image-in-Code) -fornisce un esempio di visualizzazione di un'immagine usando codice c#.

<a name="adding-assets" />

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>Aggiunta e l'organizzazione delle immagini in un'App xamarin

Quando si aggiunge un'immagine per l'utilizzo in un'app xamarin. IOS, lo sviluppatore utilizzerà un _catalogo di Asset_ per supportare tutti i dispositivi iOS e la risoluzione dei richiesta da un'app.

Aggiunto in iOS 7, **Asset cataloghi immagine set** contengono tutte le versioni o le rappresentazioni di un'immagine che sono necessari per supportare vari dispositivi e fattori per un'app di scala. Anziché basarsi sul nome di file di asset immagine (vedere [immagini indipendente dalla risoluzione e immagine nomenclatura](~/ios/app-fundamentals/images-icons/displaying-an-image.md)), **set immagine** utilizzare un file Json per specificare quale immagine appartiene a quali dispositivi e/o risoluzione . Questo è il modo migliore per gestire e supportare le immagini in iOS (da iOS 9 o versione successiva).

<a name="asset-catalogs" />

## <a name="adding-images-to-an-asset-catalog-image-set"></a>Aggiunta di immagini a un'immagine di catalogo di Asset impostato

Come descritto in precedenza, un **Asset cataloghi immagine set** contengono tutte le versioni o le rappresentazioni di un'immagine che sono necessari per supportare vari dispositivi e fattori per un'app di scala. Anziché basarsi sul nome di file di asset immagine, **set immagine** utilizzare un file Json per specificare quale immagine appartiene a quali dispositivi e/o risoluzione.

Per creare un nuovo set di immagine e aggiungere immagini, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Nel **Esplora**, fare doppio clic su di `Assets.xcassets` file per aprirlo e modificarlo:

    ![](displaying-an-image-images/imageset01.png "Assets.xcassets in Esplora soluzioni")
2. Fare clic su di **elenco risorse** e selezionare **nuova immagine impostata**:

    ![](displaying-an-image-images/imageset02.png "Aggiunta di un nuovo Set di immagini")
3. Selezionare il nuovo set di immagini e verrà visualizzato l'editor:

    ![](displaying-an-image-images/imageset03.png "L'editor Set di immagini")
4. A questo punto, trascinare nelle immagini per ognuno dei diversi dispositivi e risoluzioni necessarie e. (Nota: corrispondenti queste risoluzioni per le risoluzioni specificate nella [dimensioni delle immagini e i nomi file](~/ios/app-fundamentals/images-icons/displaying-an-image.md) documento.)
5. Fare doppio clic sul nuovo set di immagini **nome** nel **elenco risorse** modificarlo: ![ ] (displaying-an-image-images/imageset04.png "modifica il nome del nuovo set di immagini")

Quando si utilizza un **immagine impostata** nella finestra di progettazione di iOS, è sufficiente selezionare il nome del set di nell'elenco a discesa nell'Editor di proprietà:

![](displaying-an-image-images/imageset06.png "Selezionare il nome del set di un'immagine nell'elenco a discesa")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Aprire il catalogo di Asset dal **Esplora**, nell'angolo superiore sinistro, fare clic su di **più** pulsante:

    ![](displaying-an-image-images/asset5.png "Fare clic sul segno più pulsante")

2. Selezionare **Aggiungi immagine insieme** e verrà visualizzato l'editor di immagini impostato per il nuovo set di immagini. A questo punto, trascinare nelle immagini per ognuno dei diversi dispositivi e risoluzioni necessarie e. (Nota: corrispondenti queste risoluzioni per le risoluzioni specificate nella [dimensioni delle immagini e i nomi file](~/ios/app-fundamentals/images-icons/displaying-an-image.md) documento):

    ![](displaying-an-image-images/asset7.png "Editor set di immagini")

### <a name="renaming-an-image-set"></a>Ridenominazione di un Set di immagini

Per rinominare un Set di immagini, eseguire le operazioni seguenti:

1. Nel **Esplora**, fare doppio clic su di **catalogo di Asset** file per aprirlo e modificarlo:

    ![](displaying-an-image-images/rename01.png "Il catalogo di Asset in Esplora soluzioni")
2. Selezionare il **immagine impostata** rinominare:

    ![](displaying-an-image-images/rename02.png "Selezionare il Set di immagini da rinominare")
3. Nel **Esplora proprietà**, scorrere fino alla fine e selezionare **nome**(sotto il **varie** sezione):

    ![](displaying-an-image-images/rename03.png "Selezionare un nome nella sezione varie")
4. Immettere un nuovo **nome** per il **immagine impostata** e salvare le modifiche.

-----

Quando si utilizza un **immagine impostata** nel codice, farvi riferimento in base al nome tramite la chiamata di `FromBundle` metodo la `UIImage` classe. Di seguito è riportato un esempio:

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> Se le immagini assegnate a un Set di immagini non vengono visualizzati correttamente, verificare che il nome corretto del file è in uso con la `FromBundle` metodo (la **immagine impostata** e non il padre **catalogo di Asset** nome). Per le immagini PNG, il `.png` estensione può essere omesso. Per altri formati di immagine, l'estensione è necessaria (ad es. `PurpleMonkey.jpg`).

<a name="Using-Vector-Images-in-Asset-Catalogs" />

### <a name="using-vector-images-in-asset-catalogs"></a>Utilizzo di immagini vettoriali nei cataloghi di Asset

A partire da iOS 8, speciale **vettore** come è stato aggiunto a **set immagine** che consente agli sviluppatori di includere un **PDF** formato immagine vettore nel cassetto invece inclusi file bitmap singoli per le diverse risoluzioni. Questo metodo, fornire un file singolo vettore per il `@1x` risoluzione (formattata come file PDF vettore) e `@2x` e `@3x` versioni del file verranno generate in fase di compilazione e inclusi nel bundle dell'applicazione.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![](displaying-an-image-images/imageset05.png "Immagini vettoriali nell'editor di cataloghi Asset")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](displaying-an-image-images/asset8.png "Immagini vettoriali nell'editor di cataloghi Asset")

-----

Ad esempio, se lo sviluppatore include un `MonkeyIcon.pdf` file come vettore di un catalogo di Asset con una risoluzione di 150 px x 150px, la mappa di bit seguente asset sono incluso nel bundle di app finale durante la compilazione:

- `MonkeyIcon@1x.png` -150 px x 150px risoluzione.
- `MonkeyIcon@2x.png` -300px x 300px risoluzione.
- `MonkeyIcon@3x.png` -450px x 450px risoluzione.

Di seguito deve essere prese in considerazione quando si usano immagini vettoriali PDF nei cataloghi di Asset:

- Non è supporto vettore completo come PDF saranno rasterizzato a una bitmap in fase di compilazione e la bitmap fornite nell'applicazione finale.
- Le dimensioni dell'immagine non possono essere modificate dopo che è stata impostata nel catalogo di Asset. Se lo sviluppatore tenta di ridimensionamento dell'immagine (nel codice o con Layout automatico e classi di dimensione) l'immagine verrà distorta esattamente come qualsiasi altro tipo di bitmap.
- Asset cataloghi sono compatibili con iOS 7 e versioni successive, se un'app necessaria supportare iOS 6 o inferiore, in grado di utilizzare Asset cataloghi.

<a name="Working-with-Template-Images" />

## <a name="working-with-template-images"></a>Utilizzo di immagini modello

In base alla struttura di un'app iOS, talvolta potrebbe essere quando lo sviluppatore deve per personalizzare un'icona o immagine all'interno dell'interfaccia utente per corrispondere a una modifica nello schema di colore (ad esempio, in base alle preferenze dell'utente).

Per ottenere facilmente questo effetto, passare il _modalità rendering_ dell'Asset di immagine per **immagine modello**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](displaying-an-image-images/templateimage01.png "Impostare la modalità di rendering immagine modello")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](displaying-an-image-images/templateimage01vs.png "La modalità di rendering è impostata su modello")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

La finestra di progettazione IOS, assegnare l'Asset di immagine a un controllo dell'interfaccia utente, quindi impostare il **Tint** per colorare l'immagine:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](displaying-an-image-images/templateimage03.png "Impostare la tonalità per colorare l'immagine")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](displaying-an-image-images/templateimage03vs.png "Impostare la tonalità per colorare l'immagine")](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

Facoltativamente, è possibile impostare l'Asset di immagine e Tint direttamente nel codice:

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

Per utilizzare un'immagine modello completamente dal codice, eseguire le operazioni seguenti:

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

Poiché il `RenderMode` proprietà di un `UIImage` è di sola lettura, usare il `ImageWithRenderingMode` metodo per creare una nuova istanza dell'immagine con l'impostazione della modalità di rendering desiderato.

Sono disponibili tre probabilmente le impostazioni per `UIImage.RenderMode` tramite il `UIImageRenderingMode` enum:

* `AlwaysOriginal` -Impone l'immagine da sottoporre a rendering come file di immagine di origine originale senza modifiche.
* `AlwaysTemplate` -Impone l'immagine deve essere eseguito come un'immagine modello per colorare i pixel con l'oggetto specificato `Tint` colore.
* `Automatic` -Esegue il rendering dell'immagine come un modello o originale in base all'ambiente che viene utilizzato in. Ad esempio, se l'immagine viene utilizzata un `UIToolBar`, `UINavigationBar`, `UITabBar` o `UISegmentControl` verrà considerato come un modello.

<a name="Adding-new-Assets-Collections" />

## <a name="adding-new-assets-collections"></a>Aggiunta di nuove raccolte di risorse

Quando si lavora con immagini nei cataloghi di risorse potrebbe essere quando una nuova raccolta verrà richiesta, anziché aggiungere tutte le immagini dell'app a volte il `Assets.xcassets` insieme. Ad esempio, quando si progettano le risorse su richiesta.

Per aggiungere un nuovo catalogo di asset per il progetto:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare clic su di **nome progetto** nel **Esplora** e selezionare **Aggiungi** > **nuovo File...**
2. Selezionare **iOS** > **catalogo di Asset**, immettere un **nome** per la raccolta e fare clic su di **New** pulsante:

    ![](displaying-an-image-images/asset01.png "Creazione di un nuovo catalogo di Asset")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In Esplora soluzioni, fare clic su **Asset cataloghi** cartella e selezionare **Aggiungi > nuovo catalogo di Asset**.
2. Assegnare un nome e fare clic su **Aggiungi**:

    ![](displaying-an-image-images/asset1.png "Creazione di un nuovo catalogo di Asset")

-----


Da qui, può essere gestito insieme nello stesso modo come impostazione predefinita `Assets.xcassets` raccolta automaticamente incluso nel progetto.

<a name="controls" />

## <a name="using-images-with-controls"></a>Utilizzo di immagini con controlli

Oltre a utilizzare immagini per supportare un'app, iOS Usa le immagini con tipi di controllo di app, ad esempio le barre di scheda, le barre degli strumenti, barre di navigazione, tabelle e i pulsanti. Un modo semplice per visualizzare un'immagine in un controllo consiste nell'assegnare un `UIImage` istanza per il controllo `Image` proprietà.

<a name="frombundle" />

### <a name="frombundle"></a>FromBundle

Il `FromBundle` chiamata al metodo è una chiamata sincrona (bloccante) che ha un numero di immagine durante il caricamento e funzioni di gestione predefiniti, ad esempio la memorizzazione nella cache di supporto e la gestione automatica dei file di immagine per diverse risoluzioni.  

Nell'esempio seguente viene illustrato come impostare l'immagine di un `UITabBarItem` su un `UITabBar`:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Supponendo che `MyImage` è il nome di una risorsa immagine aggiunta a un catalogo di Asset precedente. Quando si utilizzano le immagini di catalogo di Asset, è sufficiente specificare il nome del Set di immagini nel `FromBundle` metodo per **PNG** formattato immagini:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Per qualsiasi altro formato di immagine, includere l'estensione con il nome. Ad esempio:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

Per ulteriori informazioni sulle icone e immagini, vedere la documentazione di Apple in [icona personalizzata e linee guida per la creazione di immagini](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html).

<a name="Displaying-an-Image-in-a-Storyboards" />

## <a name="displaying-an-image-in-a-storyboards"></a>Visualizzazione di un'immagine in un storyboard

Dopo aver aggiunto un'immagine a un progetto di xamarin. IOS utilizzando un Asset di cataloghi, può essere facilmente visualizzati in base a uno Storyboard utilizzando un `UIImageView` nella finestra di progettazione iOS. Ad esempio, se è stato aggiunto l'Asset di immagine seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![](displaying-an-image-images/display01.png "Un esempio che è stato aggiunto l'Asset immagine")

Eseguire le operazioni seguenti per visualizzarla in uno Storyboard:

1. Fare doppio clic su di `Main.storyboard` file nel **Esplora** per aprirlo e modificarlo nella finestra di progettazione iOS.
2. Selezionare un **immagine vista** dal **della casella degli strumenti**:

     ![](displaying-an-image-images/display02.png "Selezionare una visualizzazione di un'immagine dalla casella degli strumenti")
3. Trascinare la visualizzazione immagine l'area di progettazione e la posizione e le dimensioni in base alle esigenze:

    ![](displaying-an-image-images/display03.png "Una nuova visualizzazione immagine nell'area di progettazione")
4. Nel **Widget** sezione la **Esplora proprietà** selezionare l'elemento **immagine** asset da visualizzare:

    ![](displaying-an-image-images/display04.png "Selezionare la risorsa immagine desiderata da visualizzare")
5. Nel **vista** sezione, utilizzare il **modalità** per controllare come l'immagine sarà ridimensionato quando la **visualizzazione immagine** viene ridimensionato.
6. Con il **visualizzazione immagine** selezionato, fare clic su Nuovo per aggiungere **vincoli**:

    ![](displaying-an-image-images/display05.png "Aggiunta di vincoli")
7. Trascinare il quadratino a forma di "T" su ciascun bordo del **visualizzazione immagine** sul lato dello schermo per l'immagine ai lati "aggiungere" corrispondente. In questo modo, il **visualizzazione immagine** verrà compatta e crescere man mano che lo schermo viene ridimensionato.
8. Salvare le modifiche allo Storyboard.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](displaying-an-image-images/display01vs.png "Un esempio che è stato aggiunto l'Asset immagine")

Eseguire le operazioni seguenti per visualizzarla in uno Storyboard:

1. Fare doppio clic su di `Main.storyboard` file nel **Esplora** per aprirlo e modificarlo nella finestra di progettazione iOS.
2. Selezionare un **immagine vista** dal **della casella degli strumenti**:

     ![](displaying-an-image-images/display02vs.png "Selezionare una visualizzazione di un'immagine dalla casella degli strumenti")
3. Trascinare la visualizzazione immagine l'area di progettazione e la posizione e le dimensioni in base alle esigenze:

    ![](displaying-an-image-images/display03vs.png "Una nuova visualizzazione immagine nell'area di progettazione")
4. Nel **Widget** sezione la **Esplora proprietà** selezionare l'elemento **immagine** asset da visualizzare:

    ![](displaying-an-image-images/display04vs.png "Selezionare la risorsa immagine desiderata da visualizzare")
5. Nel **vista** sezione, utilizzare il **modalità** per controllare come l'immagine sarà ridimensionato quando la **visualizzazione immagine** viene ridimensionato.
6. Con il **visualizzazione immagine** selezionato, fare clic su Nuovo per aggiungere **vincoli**:

    ![](displaying-an-image-images/display05vs.png "Aggiunta di vincoli")
7. Trascinare il quadratino a forma di "T" su ciascun bordo del **visualizzazione immagine** sul lato dello schermo per l'immagine ai lati "aggiungere" corrispondente. In questo modo, il **visualizzazione immagine** verrà compatta e crescere man mano che lo schermo viene ridimensionato.
8. Salvare le modifiche allo Storyboard.

-----


<a name="Displaying-an-Image-in-Code" />

## <a name="displaying-an-image-in-code"></a>Visualizzazione di un'immagine nel codice

Come visualizzare un'immagine in uno Storyboard, dopo l'aggiunta di un'immagine a un progetto di xamarin. IOS utilizzando cataloghi un Asset, può essere facilmente visualizzato usando codice c#.

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

Questo codice crea un nuovo `UIImageView` e gli assegna una dimensione iniziale e la posizione. Quindi carica l'immagine da una risorsa immagine aggiunto al progetto e aggiunge il `UIImageView` all'elemento padre `UIView` per visualizzarlo.

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di immagini (esempio)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icona personalizzata e linee guida per la creazione di immagini](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
