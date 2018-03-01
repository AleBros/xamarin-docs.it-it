---
title: Installazione di piattaforma Mac
description: Xamarin. Forms include ora il supporto di anteprima per la piattaforma Mac
ms.topic: article
ms.prod: xamarin
ms.assetid: EEC549E0-F182-4F9C-B2BA-B31D19569AA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2017
ms.openlocfilehash: bda207796d1019f8188176acce055d782cb9e32d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="mac-platform-setup"></a>Installazione di piattaforma Mac

![Anteprima](~/media/shared/preview.png)

Prima di iniziare, creare (o utilizzare un'esistente) progetto xamarin. Forms.
È possibile aggiungere solo le app Mac usando Visual Studio per Mac.

## <a name="adding-a-mac-app"></a>Aggiunta di un'App Mac

Seguire queste istruzioni per aggiungere un'app Mac che verrà eseguito su macOS Sierra e montagna di El Capitan Mac OS X:

1. In Visual Studio per Mac, fare clic su una soluzione xamarin. Forms esistente e scegliere **Aggiungi > Aggiungi nuovo progetto...**

2. Nel **nuovo progetto** finestra scegliere **Mac > App > App Cocoa** e premere **Avanti**.

3. Tipo di un **nome App** (e, facoltativamente, scegliere un nome diverso per l'elemento ancoraggio), quindi premere **Avanti**.

4. Esaminare la configurazione e premere **crea**. Questi passaggi vengono visualizzati seguito:

  ![Istruzioni animate che mostra come aggiungere un'app Cocoa](mac-images/add-macos-proj.gif)

5. Nel progetto Mac, fare clic su **pacchetti > Aggiungi pacchetti...**  per aggiungere il [Xamarin.Forms/2.3.5.235-pre2](https://www.nuget.org/packages/Xamarin.Forms/2.3.5.235-pre2) NuGet. Per questa versione, è necessario aggiornare anche gli altri progetti.

6. Nel progetto Mac, fare clic su **riferimenti** e aggiungere un riferimento al progetto xamarin. Forms (progetto condiviso o PCL).

  ![Aggiungere un riferimento al progetto di codice condiviso xamarin. Forms](mac-images/references-sml.png)

7. Aggiornamento **Main.cs** per inizializzare il `AppDelegate`:

    ```csharp
    static class MainClass
    {
        static void Main(string[] args)
        {
            NSApplication.Init();
            NSApplication.SharedApplication.Delegate = new AppDelegate(); // add this line
            NSApplication.Main(args);
        }
    }
    ```

8. Aggiornamento `AppDelegate` per inizializzare xamarin. Forms, creare una finestra e caricare l'applicazione di xamarin. Forms (ricordando di impostare un appropriato `Title`). _Se si dispone di altre dipendenze che devono essere inizializzati, farlo qui anche._

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.MacOS;
    // also add a using for the Xamarin.Forms project, if the namespace is different to this file
    ...
    [Register("AppDelegate")]
    public class AppDelegate : FormsApplicationDelegate
    {
        NSWindow window;
        public AppDelegate()
        {
            var style = NSWindowStyle.Closable | NSWindowStyle.Resizable | NSWindowStyle.Titled;

            var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);
            window = new NSWindow(rect, style, NSBackingStore.Buffered, false);
            window.Title = "Xamarin.Forms on Mac!"; // choose your own Title here
            window.TitleVisibility = NSWindowTitleVisibility.Hidden;
        }

        public override NSWindow MainWindow
        {
            get { return window; }
        }

        public override void DidFinishLaunching(NSNotification notification)
        {
            Forms.Init();
            LoadApplication(new App());
            base.DidFinishLaunching(notification);
        }
    }
    ```

9. Fare doppio clic su **Main** per modificare in Xcode. Selezionare il **finestra** e _deselezionare_ il **Controller iniziale è** casella di controllo (in questo modo il codice precedente crea una finestra):

  [ ![Deselezionare la casella di controllo è iniziale Controller in Xcode](mac-images/xcode-init-controller-sml.png)](mac-images/xcode-init-controller.png)

  È possibile modificare il sistema di menu dello storyboard per rimuovere gli elementi indesiderati.

10. Infine, aggiungere risorse locali (ad es. file di immagine) nei progetti di piattaforma esistenti che sono necessari.

11. Il progetto Mac dovrebbe essere eseguito il codice di xamarin. Forms in macOS!

## <a name="next-steps"></a>Passaggi successivi

### <a name="styling"></a>Stile

Con le modifiche recenti apportate a `OnPlatform` è ora possibile scegliere come destinazione un numero qualsiasi di piattaforme. Che include macOS.

```xml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White"/>
        <On Platform="macOS" Value="White"/>
        <On Platform="Android" Value="Black"/>
    </OnPlatform>
</Button.TextColor>
```

Si noti può anche raddoppiare piattaforme simile al seguente: `<On Platform="iOS, macOS" ...>`.

### <a name="window-size-and-position"></a>Posizione e dimensioni della finestra

È possibile regolare la dimensione e posizione della finestra di `AppDelegate`:

```csharp
var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);  // x, y, width, height
```

## <a name="known-issues"></a>Problemi noti

Si tratta di un'anteprima, pertanto è opportuno prevedere che non siano pronti di produzione. Di seguito sono riportati alcuni elementi che potrebbero verificarsi quando si aggiungono macOS ai progetti:

### <a name="not-all-nugets-are-ready-for-macos"></a>Non tutti i NuGets sono pronti per macOS

Per utilizzare in un progetto macOS pacchetti devono essere destinati "xamarinmac20". È possibile che alcune delle librerie di che utilizzare non supporta ancora macOS.

In questo caso, è necessario inviare una richiesta al gestore del progetto per aggiungerlo. Fino a quando non si dispone di supporto, è necessario cercare alternative.

### <a name="missing-xamarinforms-features"></a>Funzionalità di xamarin. Forms mancanti

Non tutte le funzionalità di xamarin. Forms vengono completate in questa versione di anteprima; di seguito è riportato un elenco di alcune delle funzionalità che non è ancora implementato:

* Piè di pagina
* Immagine: proporzioni
* Il supporto UnevenRows ListView – ScrollTo, aggiornamento, SeparatorColor, SeparatorVisibility
* MasterDetailPage: BackgroundColor
* Navigazione-InsertPageBefore
* OpenGLRenderer
* Selezione: implementazione di associabile/Observable
* TabbedPage – BarBackgroundColor, BarTextColor
* TableView – UnevenRows
* ForceUpdateSize ViewCell: IsEnabled,
* WebView: la maggior parte delle WebNavigationEvents


## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.Mac](~/mac/index.yml)
