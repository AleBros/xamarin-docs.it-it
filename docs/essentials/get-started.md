---
title: "Introduzione a Xamarin.Essentials " Description: " Xamarin.Essentials fornisce una singola API multipiattaforma che funziona con qualsiasi applicazione iOS, Android o UWP a cui è possibile accedere dal codice condiviso, indipendentemente dalla modalità di creazione dell'interfaccia utente.
ms. AssetID: B2669C48-B659-4854-BD80-FEB0E876F5B9 Author: jamesmontemagno ms. Author: Jamont ms. Custom: video ms. Date: 05/11/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="get-started-with-xamarinessentials"></a>Introduzione aXamarin.Essentials

Xamarin.Essentialsfornisce una singola API multipiattaforma che funziona con qualsiasi applicazione iOS, Android o UWP a cui è possibile accedere dal codice condiviso indipendentemente dal modo in cui viene creata l'interfaccia utente. Per altre informazioni sui sistemi operativi supportati, vedere la [guida al supporto delle piattaforme e delle funzionalità](platform-feature-support.md).

## <a name="installation"></a>Installazione

Xamarin.Essentialsè disponibile come pacchetto NuGet ed è incluso in ogni nuovo progetto in Visual Studio. Può anche essere aggiunto a qualsiasi esistente usando Visual Studio con i passaggi seguenti.

1. Scaricare e installare [Visual Studio](https://visualstudio.microsoft.com/) con [Visual Studio Tools per Xamarin](~/get-started/installation/index.md).

2. Aprire un progetto esistente o creare un nuovo progetto usando il modello Applicazione vuota in **Visual Studio C# ** (Android, iPhone e iPad o multipiattaforma).

    > [!IMPORTANT]
    > in caso di aggiunta a un progetto della piattaforma UWP assicurarsi che nelle proprietà del progetto sia impostata la build 16299 o versione successiva.

3. Aggiungere il [**Xamarin.Essentials**](https://www.nuget.org/packages/Xamarin.Essentials/) pacchetto NuGet a ogni progetto:

    <!--markdownlint-disable MD023 -->
    # <a name="visual-studio"></a>[Visual Studio](#tab/windows)

    Nel pannello Esplora soluzioni fare clic con il pulsante destro del mouse sul nome della soluzione e scegliere **Gestisci pacchetti NuGet**. Cercare **Xamarin.Essentials** e installare il pacchetto in **tutti i** progetti, incluse le librerie Android, iOS, UWP e .NET standard.

    # <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

    Nel pannello Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **aggiungi > Aggiungi pacchetti NuGet...**. Cercare **Xamarin.Essentials** e installare il pacchetto in **tutti** i progetti, incluse le librerie Android, iOS e .NET standard.

    -----

4. Aggiungere un riferimento a Xamarin.Essentials in qualsiasi classe C# per fare riferimento alle API.

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentialsrichiede una configurazione specifica della piattaforma:

    # <a name="android"></a>[Android](#tab/android)

    Xamarin.Essentialssupporta una versione minima di Android di 4,4, corrispondente al livello API 19, ma la versione di Android di destinazione per la compilazione deve essere 9,0 o 10,0, corrispondente al livello API 28 e a livello 29. In Visual Studio queste due versioni sono impostate nella finestra di dialogo delle proprietà del progetto per il progetto Android, nella scheda Manifesto Android. In Visual Studio per Mac sono impostati nella finestra di dialogo Opzioni progetto per il progetto Android, nella scheda applicazione Android.

    Quando si esegue la compilazione in Android 9,0, Xamarin.Essentials installa la versione 28.0.0.3 delle librerie Novell. Android. Support necessarie. Tutte le altre librerie di Novell. Android. support richieste dall'applicazione devono essere aggiornate anche alla versione 28.0.0.3 usando Gestione pacchetti NuGet. Tutte le librerie Novell. Android. support utilizzate dall'applicazione devono essere uguali e devono essere almeno la versione 28.0.0.3. Se si verificano problemi durante l'aggiunta di NuGet o l'aggiornamento di NuGet nella soluzione, vedere la pagina relativa alla [risoluzione dei](troubleshooting.md) problemi Xamarin.Essentials .

    A partire dalla versione 1.5.0 durante la compilazione in Android 10,0, Xamarin.Essentials installare le librerie di supporto AndroidX necessarie. Leggere la [documentazione di AndroidX](https://docs.microsoft.com/xamarin/android/platform/androidx) se la transizione non è ancora stata eseguita.

    Nel progetto Android o in `MainLauncher` qualsiasi `Activity` che viene avviato, Xamarin.Essentials deve essere inizializzato nel `OnCreate` Metodo:

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState) {
        //...
        base.OnCreate(savedInstanceState);
        Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code, it may also be called: bundle
        //...
    ```

    Per gestire le autorizzazioni di runtime in Android, Xamarin.Essentials deve ricevere `OnRequestPermissionsResult` . Aggiungere il codice seguente a tutte le classi `Activity`:

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Android.Content.PM.Permission[] grantResults)
    {
        Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
    ```

    # <a name="ios"></a>[iOS](#tab/ios)

    Non è necessaria alcuna configurazione aggiuntiva.

    # <a name="uwp"></a>[UWP](#tab/uwp)

    Non è necessaria alcuna configurazione aggiuntiva.

    -----

6. Seguire le [ Xamarin.Essentials guide](index.md) che consentono di copiare e incollare frammenti di codice per ciascuna funzionalità.

## <a name="xamarinessentials---cross-platform-apis-for-mobile-apps-video"></a>Xamarin.Essentials-API multipiattaforma per app per dispositivi mobili (video)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-XamarinEssentials-Cross-Platform-APIs-for-Mobile-Apps/player]

## <a name="other-resources"></a>Risorse aggiuntive

Si consiglia agli sviluppatori nuovi di Novell di visitare [la Guida introduttiva allo sviluppo di Novell](~/cross-platform/getting-started/index.md).

Visitare il [ Xamarin.Essentials repository GitHub](https://github.com/xamarin/Essentials) per visualizzare il codice sorgente corrente, il risultato successivo, eseguire esempi e clonare il repository. I contributi della community sono benvenuti.

Esplorare la [documentazione dell'API](xref:Xamarin.Essentials) per tutte le funzionalità di Xamarin.Essentials .
