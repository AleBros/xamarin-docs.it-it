---
ms.openlocfilehash: 247e75435f42a49d5d1ea01a4d0ec3da67866156
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "67277288"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Per completare questa esercitazione è necessario Visual Studio 2019 (la versione più recente) con installato il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**. È inoltre necessario un Mac associato per compilare l'applicazione dell'esercitazione per iOS. Per informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md). Per informazioni sulla connessione di Visual Studio 2019 a un host di compilazione Mac, vedere [Associa a Mac per lo sviluppo di Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Avviare Visual Studio e creare una nuova app Xamarin.Forms vuota denominata **AppLifecycleTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa esercitazione richiedono che la soluzione sia denominata **AppLifecycleTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione Xamarin.Forms](~/get-started/first-app/index.md) in [Approfondimenti per le guide di avvio rapido di Xamarin.Forms](~/get-started/first-app/index.md).

1. In **Esplora soluzioni**, nel progetto **AppLifecycleTutorial**, espandere **App.xaml** e fare doppio clic su **App.xaml.cs** per aprirlo. In **App.xaml.cs** aggiornare quindi gli override `OnStart`, `OnSleep` e `OnResume` come indicato di seguito:

    ```csharp
    protected override void OnStart()
    {
        Console.WriteLine("OnStart");
    }

    protected override void OnSleep()
    {
        Console.WriteLine("OnSleep");
    }

    protected override void OnResume()
    {
        Console.WriteLine("OnResume");
    }
    ```

    Questo codice aggiorna gli override dei metodi del ciclo di vita dell'applicazione, con istruzioni `Console.WriteLine` che indicano quando è stato richiamato ogni metodo:

    - Il metodo `OnStart` viene richiamato all'avvio dell'applicazione.
    - Il metodo `OnSleep` viene richiamato quando l'applicazione passa in background.
    - Il metodo `OnResume` viene richiamato alla ripresa dell'applicazione dal background.

    > [!NOTE]
    > Non è disponibile alcun metodo per la terminazione dell'applicazione. In circostanze normali, la terminazione dell'applicazione verrà eseguita dal metodo `OnSleep`.

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto. All'avvio dell'applicazione viene richiamato il metodo `OnStart` e viene restituito **OnStart** come output nella finestra **Output** di Visual Studio:

    ```
    [Mono] Found as 'java_interop_jnienv_get_object_array_element'.
    OnStart
    [OpenGLRenderer] HWUI GL Pipeline
    ```

    Quando l'applicazione passa in background (toccando il pulsante Home in iOS o Android), viene richiamato il metodo `OnSleep`:

    ```
    [EGL_emulation] eglMakeCurrent: 0x83ee2920: ver 3 0 (tinfo 0x8357eff0)
    OnSleep
    [Mono] Image addref System.Runtime.Serialization[0x83ee19c0] -> System.Runtime.Serialization.dll[0x83f57b00]: 2
    ```

    Alla ripresa dell'applicazione (toccare l'icona dell'applicazione in iOS, toccare il pulsante Panoramica in Android e selezionare l'applicazione AppLifecycleTutorial), viene richiamato il metodo `OnResume`:

    ```
    Thread finished: <Thread Pool> #5
    OnResume
    [EGL_emulation] eglMakeCurrent: 0x83ee2920: ver 3 0 (tinfo 0x8357eff0)
    ```

    > [!NOTE]
    > Questi blocchi di codice mostrano l'output di esempio quando si esegue l'applicazione in Android.

    Per altre informazioni sul ciclo di vita dell'app Xamarin.Forms, vedere [Ciclo di vita delle app Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/vsmac)

Per completare questa esercitazione è necessario Visual Studio per Mac, la versione più recente, con installato il supporto della piattaforma Android e iOS. Sarà necessario anche Xcode, la versione più recente. Per altre informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md).

1. Avviare Visual Studio per Mac e creare una nuova app Xamarin.Forms vuota denominata **AppLifecycleTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa esercitazione richiedono che la soluzione sia denominata **AppLifecycleTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione Xamarin.Forms](~/get-started/first-app/index.md) in [Approfondimenti per le guide di avvio rapido di Xamarin.Forms](~/get-started/first-app/index.md).

1. Nel **riquadro della soluzione**, nel progetto **AppLifecycleTutorial**, espandere **App.xaml** e fare doppio clic su **App.xaml.cs** per aprirlo. In **App.xaml.cs** aggiornare quindi gli override `OnStart`, `OnSleep` e `OnResume` come indicato di seguito:

    ```csharp
    protected override void OnStart()
    {
        Console.WriteLine("OnStart");
    }

    protected override void OnSleep()
    {
        Console.WriteLine("OnSleep");
    }

    protected override void OnResume()
    {
        Console.WriteLine("OnResume");
    }
    ```

    Questo codice aggiorna gli override dei metodi del ciclo di vita dell'applicazione, con istruzioni `Console.WriteLine` che indicano quando è stato richiamato ogni metodo:

    - Il metodo `OnStart` viene richiamato all'avvio dell'applicazione.
    - Il metodo `OnSleep` viene richiamato quando l'applicazione passa in background.
    - Il metodo `OnResume` viene richiamato alla ripresa dell'applicazione dal background.

    > [!NOTE]
    > Non è disponibile alcun metodo per la terminazione dell'applicazione. In circostanze normali, la terminazione dell'applicazione verrà eseguita dal metodo `OnSleep`.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto. All'avvio dell'applicazione viene richiamato il metodo `OnStart` e viene restituito **OnStart** come output nella finestra **Output applicazione** di Visual Studio per Mac:

    ```
    2019-02-11 12:05:23.164761+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4089
    2019-02-11 12:05:23.165297+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskCopyDebugDescription: AppLifecycleTuto[4089]/0#-1 LF=0
    2019-02-11 12:05:23.685430+0000 AppLifecycleTutorial.iOS[4089:361037] OnStart
    ```

    Quando l'applicazione passa in background (toccando il pulsante Home in iOS o Android), viene richiamato il metodo `OnSleep`:

    ```
    2019-02-11 12:06:12.394301+0000 AppLifecycleTutorial.iOS[4089:361037] OnSleep
    Thread started: <Thread Pool> #3
    Thread started: <Thread Pool> #4
    Thread started: <Thread Pool> #5
    Thread started: <Thread Pool> #6
    2019-02-11 12:06:13.056968+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4089
    2019-02-11 12:06:13.057264+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskCopyDebugDescription: AppLifecycleTuto[4089]/0#-1 LF=0
    ```

    Alla ripresa dell'applicazione (toccare l'icona dell'applicazione in iOS, toccare il pulsante Panoramica in Android e selezionare l'applicazione AppLifecycleTutorial), viene richiamato il metodo `OnResume`:

    ```
    2019-02-11 12:07:10.321905+0000 AppLifecycleTutorial.iOS[4130:365695] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4130
    2019-02-11 12:07:10.322557+0000 AppLifecycleTutorial.iOS[4130:365695] SecTaskCopyDebugDescription: AppLifecycleTuto[4130]/0#-1 LF=0
    2019-02-11 12:07:17.110938+0000 AppLifecycleTutorial.iOS[4130:365695] OnResume
    ```

    > [!NOTE]
    > Questi blocchi di codice mostrano l'output di esempio quando si esegue l'applicazione in iOS.

    Per altre informazioni sul ciclo di vita dell'app Xamarin.Forms, vedere [Ciclo di vita delle app Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md).
