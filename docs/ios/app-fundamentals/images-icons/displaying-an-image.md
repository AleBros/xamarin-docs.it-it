---
title: Visualizzazione di un'immagine in xamarin. IOS
description: Questo articolo illustra tra cui un asset di immagine in un'app xamarin. IOS e visualizzazione di tale immagine usando codice c# o tramite l'assegnazione a un controllo in iOS Designer.
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/24/2018
ms.openlocfilehash: 6d584c45288d7e7341a4423c32a6966c6c7f21f3
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827907"
---
# <a name="displaying-an-image-in-xamarinios"></a>Visualizzazione di un'immagine in xamarin. IOS

_Questo articolo illustra tra cui un asset di immagine in un'app xamarin. IOS e visualizzazione di tale immagine usando codice c# o tramite l'assegnazione a un controllo in iOS Designer._

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>Aggiunta e l'organizzazione immagini in un'app xamarin. IOS

Quando si aggiunge un'immagine per l'uso in un'app xamarin. IOS, lo sviluppatore userà un' _catalogo di Asset_ per supportare tutti i dispositivi iOS e richiesti da un'app per la risoluzione.

Aggiunto in iOS 7 **set di immagini di cataloghi Asset** contengono tutte le versioni o rappresentazioni di un'immagine che sono necessari per supportare vari tipi di dispositivi e fattori per un'app di scala. Anziché basarsi sul nome di file, gli asset immagine **set di immagini** usare un file Json per specificare quale immagine appartiene a quali dispositivi e/o la risoluzione. Questo è il modo migliore per gestire e supportare le immagini in iOS (da iOS 9 o versione successiva).

## <a name="adding-images-to-an-asset-catalog-image-set"></a>Aggiunta di immagini di un'immagine di catalogo di asset set

Come indicato in precedenza, un' **set di immagini di cataloghi Asset** contengono tutte le versioni o rappresentazioni di un'immagine che sono necessari per supportare vari tipi di dispositivi e fattori per un'app di scala. Anziché basarsi sul nome di file, gli asset immagine **set di immagini** usare un file Json per specificare quale immagine appartiene a quali dispositivi e/o la risoluzione.

Per creare un nuovo set di immagini e aggiungervi immagini, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nel **Esplora soluzioni**, fare doppio clic il `Assets.xcassets` file per aprirlo e modificarlo:

    ![](displaying-an-image-images/imageset01.png "L'assets. xcassets in Esplora soluzioni")
2. Fare clic sui **elenco Assets** e selezionare **nuova immagine Set**:

    ![](displaying-an-image-images/imageset02.png "Aggiunta di un nuovo Set di immagini")
3. Selezionare il nuovo set di immagini e verrà visualizzato l'editor:

    ![](displaying-an-image-images/imageset03.png "L'editor Set di immagini")
4. A questo punto, trascinare nelle immagini per ognuno dei diversi dispositivi e risoluzioni necessarie. 
5. Fare doppio clic sul nuovo set di immagini **Name** nel **elenco Assets** modificarla: ![](displaying-an-image-images/imageset04.png "Nome del nuovo set di immagini di modifica")

Quando si usa un' **Set di immagini** in iOS Designer, è sufficiente selezionare il nome del set di nell'Editor di proprietà nell'elenco a discesa:

![](displaying-an-image-images/imageset06.png "Selezionare nome del set di un'immagine dall'elenco a discesa")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Aprire il catalogo di Asset dal **Esplora soluzioni**e in alto a sinistra, fare clic sui **Plus** pulsante:

    ![](displaying-an-image-images/asset5.png "Fare clic sul segno più pulsante")

2. Selezionare **Aggiungi immagine insieme** e verrà visualizzato l'editor Set di immagini per il nuovo set di immagini. A questo punto, trascinare nelle immagini per ognuno dei diversi dispositivi e risoluzioni necessarie. 

    ![](displaying-an-image-images/asset7.png "L'editor set di immagini")

### <a name="renaming-an-image-set"></a>Ridenominazione di un set di immagini

Per rinominare un Set di immagini, eseguire le operazioni seguenti:

1. Nel **Esplora soluzioni**, fare doppio clic il **catalogo Asset** file per aprirlo e modificarlo:

    ![](displaying-an-image-images/rename01.png "Il catalogo di Asset in Esplora soluzioni")
2. Selezionare il **Set di immagini** da rinominare:

    ![](displaying-an-image-images/rename02.png "Selezionare il Set di immagini per rinominare")
3. Nel **riquadro delle proprietà**, scorrere verso il basso e selezionare **Name**(sotto il **varie** sezione):

    ![](displaying-an-image-images/rename03.png "Seleziona nome nella sezione varie")
4. Immettere un nuovo **Name** per il **immagine Set** e salvare le modifiche.

-----

Quando si usa un' **immagine impostata** nel codice, farvi riferimento in base al nome tramite la chiamata il `FromBundle` metodo il `UIImage` classe. Di seguito è riportato un esempio:

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> Se le immagini assegnate a un Set di immagini non appaiono in modo corretto, verificare che il nome corretto del file è in uso con il `FromBundle` (metodo) (la **immagine Set** e non il padre **catalogo Asset** nome). Per le immagini PNG, il `.png` estensione può essere omesso. Per altri formati di immagine, è necessaria l'estensione (ad es. `PurpleMonkey.jpg`).

### <a name="using-vector-images-in-asset-catalogs"></a>Uso di immagini vettoriali in cataloghi asset

A partire da iOS 8, speciale **vettore** aggiunte alla classe **set di immagini** che consente agli sviluppatori di includere un **PDF** formattato immagine vettoriale nel cassetto invece inclusi file bitmap singoli con risoluzioni diverse. Usa questo metodo, fornire un file singolo vettore per la `@1x` risoluzione (formattata come un file PDF di vettore) e il `@2x` e `@3x` versioni del file verranno generate in fase di compilazione e inclusi nel bundle dell'applicazione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](displaying-an-image-images/imageset05.png "Immagini vettoriali nell'editor di cataloghi Asset")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/asset8.png "Immagini vettoriali nell'editor di cataloghi Asset")

-----

Ad esempio, se lo sviluppatore include un `MonkeyIcon.pdf` file come vettore di un catalogo Asset con una risoluzione di x 150px 150 px, la bitmap seguente asset sarà incluso nel bundle dell'app finale durante la compilazione:

- `MonkeyIcon@1x.png` -150 px x 150px risoluzione.
- `MonkeyIcon@2x.png` -resolution x 300px 300 px.
- `MonkeyIcon@3x.png` -450px x 450px risoluzione.

Di seguito deve prendere in considerazione quando si usano immagini vettoriali PDF nei cataloghi Asset:

- Questo non è supporto vettore completo come il formato PDF sarà rasterizzato a una bitmap in fase di compilazione e le bitmap fornite con l'applicazione finale.
- Le dimensioni dell'immagine non possono essere modificata dopo che è stata impostata nel catalogo di Asset. Se lo sviluppatore tenta di ridimensionamento dell'immagine (nel codice oppure utilizzando Layout automatico e le classi di dimensioni) l'immagine verrà distorta esattamente come qualsiasi altra immagine bitmap.
- Cataloghi asset sono solo compatibile con iOS 7 e versioni successive, se un'app necessario supportare iOS 6 o più basso, non può usare cataloghi Asset.

## <a name="working-with-template-images"></a>Utilizzo delle immagini modello

Basato sulla progettazione di un'app per iOS, potrebbero esserci volte quando lo sviluppatore deve per personalizzare un'icona o un'immagine dentro l'interfaccia utente per la corrispondenza di una modifica nella combinazione di colori (ad esempio, in base alle preferenze dell'utente).

Per ottenere facilmente questo effetto, passare il _modalità di rendering_ dell'Asset di immagine al **immagine modello**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](displaying-an-image-images/templateimage01.png "Impostare la modalità di rendering all'immagine modello")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage01vs.png "Impostare la modalità di rendering al modello")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

Da iOS Designer, assegnare l'Asset di immagine a un controllo dell'interfaccia utente, quindi impostare il **Tint** per colorare l'immagine:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](displaying-an-image-images/templateimage03.png "Impostare la tonalità per colorare l'immagine")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage03vs.png "Impostare la tonalità per colorare l'immagine")](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

Facoltativamente, è possibile impostare l'Asset di immagine e tinta direttamente nel codice:

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

Per usare un'immagine modello completamente dal codice, eseguire le operazioni seguenti:

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

Poiché il `RenderMode` proprietà di un `UIImage` è di sola lettura, usare il `ImageWithRenderingMode` metodo per creare una nuova istanza dell'immagine con l'impostazione della modalità di rendering desiderato.

Sono disponibili tre probabilmente le impostazioni per `UIImage.RenderMode` tramite il `UIImageRenderingMode` enum:

* `AlwaysOriginal` -Impone l'immagine da sottoporre a rendering come file di immagine di origine originale senza alcuna modifica.
* `AlwaysTemplate` -Impone l'immagine da sottoporre a rendering come un'immagine modello da colorare i pixel con la proprietà specificata `Tint` colore.
* `Automatic` -Uno viene eseguito il rendering dell'immagine come un modello o originale in base all'ambiente che viene usato in. Ad esempio, se viene usata l'immagine un `UIToolBar`, `UINavigationBar`, `UITabBar` o `UISegmentControl` verrà considerato come un modello.

## <a name="adding-new-assets-collections"></a>Aggiunta di nuove raccolte di risorse

Quando si lavora con le immagini nei cataloghi asset a volte può essere una nuova raccolta verrà quando richiesta, invece di aggiungere tutte le immagini dell'app per la `Assets.xcassets` raccolta. Ad esempio, quando si progettano le risorse On Demand.

Per aggiungere un nuovo catalogo asset per il progetto:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare clic sui **nome progetto** nel **Esplora soluzioni** e selezionare **Add** > **nuovo File...**
2. Selezionare **iOS** > **catalogo Asset**, immettere un **nome** per la raccolta e fare clic sul **nuovo** pulsante:

    ![](displaying-an-image-images/asset01.png "Creazione di un nuovo catalogo Asset")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. In Esplora soluzioni, fare clic su **cataloghi Asset** cartella e selezionare **Aggiungi > nuovo catalogo Asset**.
2. Assegnare un nome e fare clic su **Add**:

    ![](displaying-an-image-images/asset1.png "Creazione di un nuovo catalogo Asset")

-----

A questo punto, può essere gestito insieme nello stesso modo come predefinito `Assets.xcassets` raccolta automaticamente incluso nel progetto.

## <a name="using-images-with-controls"></a>Uso di immagini con controlli

Oltre a usare le immagini per supportare un'app, iOS Usa anche le immagini con tipi di controllo delle app, ad esempio barre schede, le barre degli strumenti, barre di navigazione, tabelle e i pulsanti. Un modo semplice per visualizzare un'immagine in un controllo consiste nell'assegnare un `UIImage` istanza del controllo `Image` proprietà.

### <a name="frombundle"></a>FromBundle

Il `FromBundle` chiamata al metodo è una chiamata sincrona (bloccante) che ha un numero di immagine durante il caricamento e la gestione le funzionalità integrate, ad esempio gestione automatica dei file di immagine per varie risoluzioni e supporto di memorizzazione nella cache.  

Nell'esempio seguente viene illustrato come impostare l'immagine di una `UITabBarItem` su un `UITabBar`:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Supponendo che `MyImage` è il nome di un Asset immagine aggiunti a un catalogo di Asset precedente. Quando si usano le immagini di catalogo di Asset, è sufficiente specificare il nome del Set di immagini nel `FromBundle` metodo per **PNG** formattato immagini:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Per qualsiasi altro formato di immagine, includere l'estensione con il nome. Ad esempio:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

Per altre informazioni sulle icone e immagini, vedere la documentazione di Apple sul [icona personalizzata e linee guida per la creazione di immagini](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html).

## <a name="displaying-an-image-in-a-storyboards"></a>Visualizzazione di un'immagine in un storyboard

Dopo aver aggiunto un'immagine a un progetto xamarin. IOS tramite un cataloghi Asset, può essere facilmente visualizzati su un oggetto utilizzando uno Storyboard un `UIImageView` in iOS Designer. Ad esempio, se è stata aggiunta la risorsa di immagine seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](displaying-an-image-images/display01.png "Un esempio di Asset immagine è stata aggiunta")

Eseguire il comando seguente per visualizzarlo in uno Storyboard:

1. Fare doppio clic il `Main.storyboard` del file nei **Esplora soluzioni** per aprirlo e modificarlo in iOS Designer.
2. Selezionare un **Image View** dalle **della casella degli strumenti**:

     ![](displaying-an-image-images/display02.png "Selezionare visualizzazione di un'immagine dalla casella degli strumenti")
3. Trascinare la visualizzazione di immagini l'area di progettazione e la posizione e le dimensioni in base alle esigenze:

    ![](displaying-an-image-images/display03.png "Una nuova visualizzazione immagine nell'area di progettazione")
4. Nel **Widget** sezione il **Esplora proprietà** selezionare il valore desiderato **immagine** asset deve essere visualizzato:

    ![](displaying-an-image-images/display04.png "Selezionare l'asset desiderato di immagine da visualizzare")
5. Nel **vista** sezione, utilizzare il **modalità** per controllare come l'immagine sarà ridimensionato quando la **visualizzazione immagine** viene ridimensionato.
6. Con il **Visualizza immagine** selezionato, fare clic su Nuovo per aggiungere **vincoli**:

    ![](displaying-an-image-images/display05.png "Aggiunta di vincoli")
7. Trascinare il quadratino a forma di "T" su ciascun bordo del **visualizzazione immagine** corrispondente sul lato dello schermo per "aggiungere" l'immagine ai lati. In questo modo, il **visualizzazione immagine** verrà riduzione e aumento delle dimensioni viene ridimensionata la schermata.
8. Salvare le modifiche allo Storyboard.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/display01vs.png "Un esempio di Asset immagine è stata aggiunta")

Eseguire il comando seguente per visualizzarlo in uno Storyboard:

1. Fare doppio clic il `Main.storyboard` del file nei **Esplora soluzioni** per aprirlo e modificarlo in iOS Designer.
2. Selezionare un **Image View** dalle **della casella degli strumenti**:

     ![](displaying-an-image-images/display02vs.png "Selezionare visualizzazione di un'immagine dalla casella degli strumenti")
3. Trascinare la visualizzazione di immagini l'area di progettazione e la posizione e le dimensioni in base alle esigenze:

    ![](displaying-an-image-images/display03vs.png "Una nuova visualizzazione immagine nell'area di progettazione")
4. Nel **Widget** sezione il **Esplora proprietà** selezionare il valore desiderato **immagine** asset deve essere visualizzato:

    ![](displaying-an-image-images/display04vs.png "Selezionare l'asset desiderato di immagine da visualizzare")
5. Nel **vista** sezione, utilizzare il **modalità** per controllare come l'immagine sarà ridimensionato quando la **visualizzazione immagine** viene ridimensionato.
6. Con il **Visualizza immagine** selezionato, fare clic su Nuovo per aggiungere **vincoli**:

    ![](displaying-an-image-images/display05vs.png "Aggiunta di vincoli")
7. Trascinare il quadratino a forma di "T" su ciascun bordo del **visualizzazione immagine** corrispondente sul lato dello schermo per "aggiungere" l'immagine ai lati. In questo modo, il **visualizzazione immagine** verrà riduzione e aumento delle dimensioni viene ridimensionata la schermata.
8. Salvare le modifiche allo Storyboard.

-----

## <a name="displaying-an-image-in-code"></a>Visualizzazione di un'immagine nel codice

Come visualizzare un'immagine in uno Storyboard, dopo aver aggiunto un'immagine a un progetto xamarin. IOS tramite un cataloghi Asset, può essere facilmente visualizzato usando codice c#.

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

Questo codice crea un nuovo `UIImageView` e gli assegna una dimensione iniziale e la posizione. Viene quindi carica l'immagine da un Asset immagine aggiunto al progetto e aggiunge il `UIImageView` all'elemento padre `UIView` per visualizzarlo.

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di immagini (esempio)](https://developer.xamarin.com/samples/monotouch/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Le dimensioni dell'immagine e la risoluzione (Apple)](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/image-size-and-resolution/)
