---
title: Introduzione a Xamarin.Essentials
description: Xamarin.Essentials offre una singola API multipiattaforma supportata da qualsiasi applicazione iOS, Android o UWP accessibile da codice condiviso, indipendentemente dal modo in cui viene creata l'interfaccia utente.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.custom: video
ms.date: 05/11/2020
ms.openlocfilehash: 944b01d67fb09f9a21a19fb2ede9eb217d89732a
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/13/2020
ms.locfileid: "83149978"
---
# <a name="get-started-with-xamarinessentials"></a>Introduzione a Xamarin.Essentials

Xamarin.Essentials offre una singola API multipiattaforma supportata da qualsiasi applicazione iOS, Android o UWP accessibile da codice condiviso, indipendentemente dal modo in cui viene creata l'interfaccia utente. Per altre informazioni sui sistemi operativi supportati, vedere la [guida al supporto delle piattaforme e delle funzionalità](platform-feature-support.md).

## <a name="installation"></a>Installazione

Novell. Essentials è disponibile come pacchetto NuGet ed è incluso in ogni nuovo progetto in Visual Studio. Può anche essere aggiunto a qualsiasi esistente usando Visual Studio con i passaggi seguenti.

1. Scaricare e installare [Visual Studio](https://visualstudio.microsoft.com/) con [Visual Studio Tools per Xamarin](~/get-started/installation/index.md).

2. Aprire un progetto esistente o creare un nuovo progetto usando il modello Applicazione vuota in **Visual Studio C# ** (Android, iPhone e iPad o multipiattaforma).

    > [!IMPORTANT]
    > in caso di aggiunta a un progetto della piattaforma UWP assicurarsi che nelle proprietà del progetto sia impostata la build 16299 o versione successiva.

3. Aggiungere il pacchetto NuGet [**Novell. Essentials**](https://www.nuget.org/packages/Xamarin.Essentials/) a ogni progetto:

    <!--markdownlint-disable MD023 -->
    # <a name="visual-studio"></a>[Visual Studio](#tab/windows)

    Nel pannello Esplora soluzioni fare clic con il pulsante destro del mouse sul nome della soluzione e scegliere **Gestisci pacchetti NuGet**. Cercare **Xamarin.Essentials** e installare il pacchetto in **TUTTI** i progetti, incluse le librerie Android, iOS, UWP e .NET Standard.

    # <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

    Nel pannello Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **aggiungi > Aggiungi pacchetti NuGet...**. Cercare **Novell. Essentials** e installare il pacchetto in **tutti i** progetti, incluse le librerie Android, iOS e .NET standard.

    -----

4. Aggiungere un riferimento a Xamarin.Essentials in qualsiasi classe C# per fare riferimento alle API.

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials richiede una configurazione specifica della piattaforma:

    # <a name="android"></a>[Android](#tab/android)

    Novell. Essentials supporta una versione minima di Android 4,4, corrispondente al livello API 19, ma la versione di destinazione di Android per la compilazione deve essere 9,0 o 10,0, corrispondente al livello API 28 e al livello 29. In Visual Studio queste due versioni sono impostate nella finestra di dialogo delle proprietà del progetto per il progetto Android, nella scheda Manifesto Android. In Visual Studio per Mac sono impostati nella finestra di dialogo Opzioni progetto per il progetto Android, nella scheda applicazione Android.

    Quando si esegue la compilazione in Android 9,0, Novell. Essentials installa la versione 28.0.0.3 di Novell. Android. le librerie di supporto richieste. Tutte le altre librerie di Novell. Android. support richieste dall'applicazione devono essere aggiornate anche alla versione 28.0.0.3 usando Gestione pacchetti NuGet. Tutte le librerie Novell. Android. support utilizzate dall'applicazione devono essere uguali e devono essere almeno la versione 28.0.0.3. Fare riferimento alla [pagina di risoluzione dei problemi](troubleshooting.md) in caso di problemi durante l'aggiunta dei pacchetti NuGet di Xamarin.Essentials o l'aggiornamento dei pacchetti NuGet nella soluzione.

    A partire dalla versione 1.5.0 durante la compilazione in Android 10,0, Novell. Essentials installa le librerie di supporto di AndroidX necessarie. Leggere la [documentazione di AndroidX](https://docs.microsoft.com/xamarin/android/platform/androidx) se la transizione non è ancora stata eseguita.

    Nel progetto Android o in `MainLauncher` qualsiasi `Activity` che viene avviato, Novell. Essentials deve essere inizializzato nel `OnCreate` Metodo:

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState) {
        //...
        base.OnCreate(savedInstanceState);
        Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code, it may also be called: bundle
        //...
    ```

    Per gestire le autorizzazioni di runtime per Android, Xamarin.Essentials deve ricevere qualsiasi `OnRequestPermissionsResult`. Aggiungere il codice seguente a tutte le classi `Activity`:

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

6. Seguire le [guide di Xamarin.Essentials](index.md) che consentono di copiare e incollare frammenti di codice per ogni funzionalità.

## <a name="xamarinessentials---cross-platform-apis-for-mobile-apps-video"></a>Video Xamarin.Essentials - Cross-Platform APIs for Mobile Apps (API multipiattaforma per le app per dispositivi mobili)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-XamarinEssentials-Cross-Platform-APIs-for-Mobile-Apps/player]

## <a name="other-resources"></a>Risorse aggiuntive

Si consiglia agli sviluppatori nuovi di Novell di visitare [la Guida introduttiva allo sviluppo di Novell](~/cross-platform/getting-started/index.md).

Visitare il [repository GitHub Xamarin.Essentials](https://github.com/xamarin/Essentials) per vedere il codice sorgente corrente, le novità previste, eseguire gli esempi e clonare il repository. I contributi della community sono benvenuti.

Esplorare la [documentazione delle API](xref:Xamarin.Essentials) per informazioni su tutte le funzionalità di Xamarin.Essentials.
