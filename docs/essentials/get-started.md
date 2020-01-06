---
title: Introduzione a Xamarin.Essentials
description: Xamarin.Essentials offre una singola API multipiattaforma supportata da qualsiasi applicazione iOS, Android o UWP accessibile da codice condiviso, indipendentemente dal modo in cui viene creata l'interfaccia utente.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.custom: video
ms.date: 07/10/2019
ms.openlocfilehash: e388df2b4c7201cad01789de1676f79bec8d351e
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488543"
---
# <a name="get-started-with-xamarinessentials"></a>Introduzione a Xamarin.Essentials

Xamarin.Essentials offre una singola API multipiattaforma supportata da qualsiasi applicazione iOS, Android o UWP accessibile da codice condiviso, indipendentemente dal modo in cui viene creata l'interfaccia utente. Per altre informazioni sui sistemi operativi supportati, vedere la [guida al supporto delle piattaforme e delle funzionalità](platform-feature-support.md).

## <a name="installation"></a>Installazione di

Novell. Essentials è disponibile come pacchetto NuGet ed è incluso in ogni nuovo progetto in Visual Studio. Può essere aggiunto a qualsiasi esistente usando Visual Studio con i passaggi seguenti.

1. Scaricare e installare [Visual Studio](https://visualstudio.microsoft.com/) con [Visual Studio Tools per Xamarin](~/get-started/installation/index.md).

2. Aprire un progetto esistente o creare un nuovo progetto usando il modello Applicazione vuota in **Visual Studio C#**  (Android, iPhone e iPad o multipiattaforma).

    > [!IMPORTANT]
    > in caso di aggiunta a un progetto della piattaforma UWP assicurarsi che nelle proprietà del progetto sia impostata la build 16299 o versione successiva.

3. Aggiungere il pacchetto NuGet [**Xamarin.Essentials**](https://www.nuget.org/packages/Xamarin.Essentials/) a ogni progetto:

    <!--markdownlint-disable MD023 -->
    # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    Nel pannello Esplora soluzioni fare clic con il pulsante destro del mouse sul nome della soluzione e scegliere **Gestisci pacchetti NuGet**. Cercare **Xamarin.Essentials** e installare il pacchetto in **TUTTI** i progetti, incluse le librerie Android, iOS, UWP e .NET Standard.

    # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

    Nel pannello Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **aggiungi > Aggiungi pacchetti NuGet...** . Cercare **Novell. Essentials** e installare il pacchetto in **tutti i** progetti, incluse le librerie Android, iOS e .NET standard.

    -----

4. Aggiungere un riferimento a Xamarin.Essentials in qualsiasi classe C# per fare riferimento alle API.

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials richiede una configurazione specifica della piattaforma:

    # <a name="androidtabandroid"></a>[Android](#tab/android)

    Xamarin.Essentials supporta come versione minima Android 4.4, corrispondente al livello API 19, ma la versione Android di destinazione per la compilazione deve essere 9.0, corrispondente al livello API 28. In Visual Studio queste due versioni sono impostate nella finestra di dialogo delle proprietà del progetto per il progetto Android, nella scheda Manifesto Android. In Visual Studio per Mac sono impostati nella finestra di dialogo Opzioni progetto per il progetto Android, nella scheda applicazione Android.

    Xamarin.Essentials installa la versione 28.0.0.1 delle librerie Xamarin.Android.Support richieste. Anche qualsiasi altra libreria Xamarin.Android.Support richiesta dall'applicazione deve essere aggiornata alla versione 28.0.0.1 usando Gestione pacchetti NuGet. Tutte le librerie Xamarin.Android.Support usate dall'applicazione deve essere uguali e la versione non deve essere inferiore a 28.0.0.1. Fare riferimento alla [pagina di risoluzione dei problemi](troubleshooting.md) in caso di problemi durante l'aggiunta dei pacchetti NuGet di Xamarin.Essentials o l'aggiornamento dei pacchetti NuGet nella soluzione.

    In `MainLauncher` o per qualsiasi `Activity` avviata del progetto Android, Xamarin.Essentials deve essere inizializzato nel metodo `OnCreate`:

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

    # <a name="iostabios"></a>[iOS](#tab/ios)

    Non è necessaria alcuna configurazione aggiuntiva.

    # <a name="uwptabuwp"></a>[UWP](#tab/uwp)

    Non è necessaria alcuna configurazione aggiuntiva.

    -----

6. Seguire le [guide di Xamarin.Essentials](index.md) che consentono di copiare e incollare frammenti di codice per ogni funzionalità.

## <a name="xamarinessentials---cross-platform-apis-for-mobile-apps-video"></a>Video Xamarin.Essentials - Cross-Platform APIs for Mobile Apps (API multipiattaforma per le app per dispositivi mobili)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-XamarinEssentials-Cross-Platform-APIs-for-Mobile-Apps/player]

## <a name="other-resources"></a>Altre risorse

Si consiglia agli sviluppatori che non hanno familiarità con Xamarin di leggere l'[introduzione allo sviluppo con Xamarin](~/cross-platform/getting-started/index.md).

Visitare il [repository GitHub Xamarin.Essentials](https://github.com/xamarin/Essentials) per vedere il codice sorgente corrente, le novità previste, eseguire gli esempi e clonare il repository. I contributi della community sono benvenuti.

Esplorare la [documentazione delle API](xref:Xamarin.Essentials) per informazioni su tutte le funzionalità di Xamarin.Essentials.
