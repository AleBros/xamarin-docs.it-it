---
title: ''
description: Questo articolo illustra come aggiungere un progetto Mac a un Xamarin.Forms progetto, che produrrà un'app in grado di funzionare in MacOS Sierra e MacOS El Capitan.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
ms.custom: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 11897d2d3b8b7ba0a62956f1dbe4d8b873352e7a
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139555"
---
# <a name="mac-platform-setup"></a>Installazione della piattaforma Mac

![Anteprima](~/media/shared/preview.png)

Prima di iniziare, creare (o usare un progetto esistente) Xamarin.Forms . È possibile aggiungere app Mac solo usando Visual Studio per Mac.

> [!VIDEO https://youtube.com/embed/mvQ7jzaNseM]

**Aggiunta di un progetto macOS al Xamarin.Forms video**

## <a name="adding-a-mac-app"></a>Aggiunta di un'app Mac

Seguire queste istruzioni per aggiungere un'app Mac che viene eseguita su macOS Sierra e macOS El Capitan:

1. In Visual Studio per Mac fare clic con il pulsante destro del mouse sulla Xamarin.Forms soluzione esistente e scegliere **Aggiungi > Aggiungi nuovo progetto...**

2. Nella finestra **nuovo progetto** scegliere **Mac > app > Cocoa app** e fare clic su **Avanti**.

3. Digitare un **nome** per l'app (e, facoltativamente, scegliere un nome diverso per l'elemento Dock), quindi fare clic su **Avanti**.

4. Esaminare la configurazione e premere **Crea**. Questi passaggi sono illustrati di seguito:

    ![Istruzioni animate che illustrano come aggiungere un'app Cocoa](mac-images/add-macos-proj.gif)

5. Nel progetto Mac, fare clic con il pulsante destro del mouse su **pacchetti > Aggiungi pacchetti...** per aggiungere [Xamarin.Forms](https://www.nuget.org/packages/Xamarin.Forms/) NuGet. È anche necessario aggiornare gli altri progetti per usare la stessa versione del Xamarin.Forms pacchetto NuGet.

6. Nel progetto Mac, fare clic con il pulsante destro del mouse su **riferimenti** e aggiungere un riferimento al progetto Xamarin.Forms (progetto condiviso o progetto libreria .NET standard).

    ![Aggiungere un riferimento al Xamarin.Forms progetto di codice condiviso](mac-images/references-sml.png)

7. Aggiornare **Main.cs** per inizializzare il `AppDelegate` :

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

8. Aggiornare `AppDelegate` per inizializzare Xamarin.Forms , creare una finestra e caricare l' Xamarin.Forms applicazione (ricordando di impostare un appropriato `Title` ). _Se sono presenti altre dipendenze che devono essere inizializzate, eseguire anche questa operazione._

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

9. Fare doppio clic su **Main. Storyboard** per modificare in Xcode. Selezionare la **finestra** e _deselezionare_ la casella di controllo **è il controller iniziale** (perché il codice precedente crea una finestra):

    [![Deselezionare la casella di controllo è il controller iniziale in Xcode](mac-images/xcode-init-controller-sml.png)](mac-images/xcode-init-controller.png#lightbox)

    È possibile modificare il sistema di menu nello storyboard per rimuovere gli elementi indesiderati.

10. Infine, aggiungere le risorse locali, ad esempio file di immagine) dai progetti della piattaforma esistenti richiesti.

11. Il progetto Mac dovrebbe ora eseguire il Xamarin.Forms codice in MacOS.

## <a name="next-steps"></a>Passaggi successivi

### <a name="styling"></a>Stile

Con le modifiche recenti apportate a `OnPlatform` , ora è possibile fare riferimento a un numero qualsiasi di piattaforme. Che include macOS.

```xml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White"/>
        <On Platform="macOS" Value="White"/>
        <On Platform="Android" Value="Black"/>
    </OnPlatform>
</Button.TextColor>
```

Si noti che è inoltre possibile raddoppiare le piattaforme in modo analogo al seguente: `<On Platform="iOS, macOS" ...>` .

### <a name="window-size-and-position"></a>Dimensioni e posizione della finestra

È possibile modificare le dimensioni e il percorso iniziali della finestra nel `AppDelegate` :

```csharp
var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);  // x, y, width, height
```

## <a name="known-issues"></a>Problemi noti

Si tratta di un'anteprima, quindi è necessario aspettarsi che non tutti gli elementi siano pronti per la produzione. Di seguito sono riportate alcune informazioni che possono verificarsi durante l'aggiunta di macOS ai progetti:

### <a name="not-all-nugets-are-ready-for-macos"></a>Non tutti i NuGet sono pronti per macOS

È possibile che alcune delle librerie usate non supportino ancora macOS. In questo caso, è necessario inviare una richiesta al gestore del progetto per aggiungerlo. Fino a quando non saranno supportati, potrebbe essere necessario cercare alternative.

### <a name="missing-xamarinforms-features"></a>Xamarin.FormsFunzionalità mancanti

Xamarin.FormsIn questa versione di anteprima non sono state completate tutte le funzionalità. Per altre informazioni, vedere [lo stato di supporto della piattaforma MacOS](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support-macOS-Status) nel [Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms) repository GitHub.

## <a name="related-links"></a>Collegamenti correlati

- [Novell. Mac](~/mac/index.yml)
