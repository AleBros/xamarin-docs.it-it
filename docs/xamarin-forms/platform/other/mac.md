---
title: Programma di installazione piattaforma Mac
description: Questo articolo illustra come aggiungere un progetto Mac a un progetto Xamarin.Forms, che produrrà un'app in grado di eseguire in macOS Sierra e macOS El Capitan.
ms.prod: xamarin
ms.assetid: EEC549E0-F182-4F9C-B2BA-B31D19569AA5
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2017
ms.openlocfilehash: 7ed2cb66524ab3380f65d6d0be74a66c7d01226b
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978021"
---
# <a name="mac-platform-setup"></a>Programma di installazione piattaforma Mac

![Anteprima](~/media/shared/preview.png)

Prima di iniziare, creare (o usarne un esistente) progetto Xamarin.Forms. È possibile aggiungere solo le app Mac usando Visual Studio per Mac.

> [!VIDEO https://youtube.com/embed/mvQ7jzaNseM]

**Aggiunta di un progetto di macOS a Xamarin.Forms video**

## <a name="adding-a-mac-app"></a>Aggiunta di un'App Mac

Seguire queste istruzioni per aggiungere un'app Mac che verrà eseguito su macOS Sierra e macOS El Capitan:

1. In Visual Studio per Mac, fare clic sulla soluzione Xamarin.Forms esistente e scegliere **Aggiungi > Aggiungi nuovo progetto...**

2. Nel **nuovo progetto** finestra scegliere **Mac > App > App Cocoa** , quindi premere **Next**.

3. Tipo di un' **nome dell'App** (e, facoltativamente, scegliere un nome diverso per l'elemento di ancoraggio), quindi premere **successivo**.

4. Esaminare la configurazione e premere **Create**. Questa procedura viene mostrata di seguito:

    ![Istruzioni animate che illustra come aggiungere un'app Cocoa](mac-images/add-macos-proj.gif)

5. Nel progetto Mac, fare clic su **pacchetti > Aggiungi pacchetti...**  per aggiungere i [Xamarin.Forms](https://www.nuget.org/packages/Xamarin.Forms/) NuGet. È anche necessario aggiornare gli altri progetti per usare la stessa versione del pacchetto NuGet di Xamarin.Forms.

6. Nel progetto Mac, fare clic su **riferimenti** e aggiungere un riferimento al progetto Xamarin.Forms (progetto di libreria a progetto condiviso o a .NET Standard).

    ![Aggiungere un riferimento al progetto di codice condiviso di Xamarin.Forms](mac-images/references-sml.png)

7. Update **Main.cs** per inizializzare il `AppDelegate`:

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

8. Update `AppDelegate` per inizializzare Xamarin.Forms, creare una finestra e caricare l'applicazione Xamarin.Forms (ricordando di impostare un'apposita `Title`). _Se sono presenti altre dipendenze che devono essere inizializzati, farlo qui anche._

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

9. Fare doppio clic su **Main. Storyboard** per modifica in Xcode. Selezionare il **finestra** e _deselezionare_ il **Controller iniziale è** casella di controllo (ovvero dal momento che il codice precedente crea una finestra):

    [![Deselezionare la casella di controllo è iniziale Controller in Xcode](mac-images/xcode-init-controller-sml.png)](mac-images/xcode-init-controller.png#lightbox)

    È possibile modificare il sistema di menu nello storyboard per rimuovere gli elementi indesiderati.

10. Infine, aggiungere qualsiasi risorsa locale (ad es. file di immagine) dai progetti della piattaforma esistenti che sono necessari.

11. Il progetto Mac deve ora eseguire il codice Xamarin.Forms in macOS.

## <a name="next-steps"></a>Passaggi successivi

### <a name="styling"></a>Stile

Con le modifiche recenti apportate al `OnPlatform` è ora possibile scegliere un numero qualsiasi di piattaforme. Che include macOS.

```xml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White"/>
        <On Platform="macOS" Value="White"/>
        <On Platform="Android" Value="Black"/>
    </OnPlatform>
</Button.TextColor>
```

Si noti anche può raddoppiare su piattaforme simile al seguente: `<On Platform="iOS, macOS" ...>`.

### <a name="window-size-and-position"></a>Posizione e dimensioni della finestra

È possibile regolare le dimensioni iniziali e la posizione della finestra del `AppDelegate`:

```csharp
var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);  // x, y, width, height
```

## <a name="known-issues"></a>Problemi noti

Si tratta di un'anteprima, pertanto è opportuno prevedere che non siano pronte per la produzione. Di seguito sono alcune cose che si verifichino quando si aggiungono macOS per i progetti:

### <a name="not-all-nugets-are-ready-for-macos"></a>Non tutti i pacchetti NuGet sono pronti per macOS

È possibile che alcune librerie che è utilizzare non supportano ancora macOS. In questo caso, è necessario inviare una richiesta al gestore del progetto per aggiungerlo. Fino a quando non si dispone di supporto, potrebbe essere necessario cercare alternative.

### <a name="missing-xamarinforms-features"></a>Mancano le funzionalità di Xamarin.Forms

Non tutte le funzionalità di Xamarin.Forms sono state completate in questa versione di anteprima. Per altre informazioni, vedere [supporto per la piattaforma macOS, lo stato](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support-macOS-Status) nel [Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms) repository GitHub.

## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.Mac](~/mac/index.yml)
