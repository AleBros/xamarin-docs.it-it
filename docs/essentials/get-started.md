---
title: Introduzione a Xamarin.Essentials
description: Xamarin.Essentials offre una singola API multipiattaforma supportata da qualsiasi applicazione iOS, Android o UWP accessibile da codice condiviso, indipendentemente dal modo in cui viene creata l'interfaccia utente.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.custom: video
ms.date: 11/04/2018
ms.openlocfilehash: c40762ad4d165c1e5a29bdf9dc6d3c0fdb8581f7
ms.sourcegitcommit: cb484bd529bf2d8e48e5b3d086bdfc31895ec209
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53411726"
---
# <a name="get-started-with-xamarinessentials"></a>Introduzione a Xamarin.Essentials

Xamarin.Essentials offre una singola API multipiattaforma supportata da qualsiasi applicazione iOS, Android o UWP accessibile da codice condiviso, indipendentemente dal modo in cui viene creata l'interfaccia utente.

## <a name="platform-support"></a>Supporto per piattaforme

Xamarin.Essentials supporta le piattaforme e i sistemi operativi seguenti:

| Piattaforma | Versione |
| --- | --- |
| Android | 4.4 (API 19) o versione successiva |
| iOS |10.0 o versione successiva |
| UWP | 10.0.16299.0 o versione successiva |

## <a name="installation"></a>Installazione

Xamarin.Essentials è disponibile come pacchetto NuGet che può essere aggiunto a qualsiasi progetto nuovo o esistente con Visual Studio.

1. Scaricare e installare [Visual Studio](http://visualstudio.com) con [Visual Studio Tools per Xamarin](~/cross-platform/get-started/installation/index.md).

2. Aprire un progetto esistente o creare un nuovo progetto usando il modello Applicazione vuota in **Visual Studio C#**  (Android, iPhone e iPad o multipiattaforma). **Importante**: in caso di aggiunta a un progetto della piattaforma UWP assicurarsi che nelle proprietà del progetto sia impostata la build 16299 o versione successiva.

3. Aggiungere il pacchetto NuGet **Xamarin.Essentials** a ogni progetto:

    # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    Nel pannello Esplora soluzioni fare clic con il pulsante destro del mouse sul nome della soluzione e scegliere **Gestisci pacchetti NuGet**. Cercare **Xamarin.Essentials** e installare il pacchetto in **TUTTI** i progetti, incluse le librerie Android, iOS, UWP e .NET Standard.

    > [!TIP]
    > Selezionare la casella di controllo **Includi versione preliminare** durante il periodo di anteprima di [**Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials).

    # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

    Nel pannello Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Aggiungi > Aggiungi pacchetti NuGet**. Cercare **Xamarin.Essentials** e installare il pacchetto in **TUTTI** i progetti, incluse le librerie Android, iOS e .NET Standard.

    -----

4. Aggiungere un riferimento a Xamarin.Essentials in qualsiasi classe C# per fare riferimento alle API.

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials richiede una configurazione specifica della piattaforma:

    # <a name="androidtabandroid"></a>[Android](#tab/android)

    Xamarin.Essentials supporta la versione minima di Android 4.4, corrispondente al livello API 19, ma la versione Android di destinazione per la compilazione deve essere 8.1, corrispondente al livello API 27. (In Visual Studio, queste due versioni vengono impostate nella finestra di dialogo Proprietà del progetto per il progetto Android, nella scheda Manifesto Android. In Visual Studio per Mac, l'impostazione viene eseguita nella finestra di dialogo Opzioni progetto per il progetto Android, nella scheda Applicazione Android.) 

    Xamarin.Essentials installa la versione 27.0.2.1 delle librerie Xamarin.Android.Support richieste. Anche qualsiasi altra libreria Xamarin.Android.Support richiesta dall'applicazione deve essere aggiornata alla versione 27.0.2.1 usando Gestione pacchetti NuGet. Tutte le librerie Xamarin.Android.Support usate dall'applicazione deve essere uguali e almeno nella versione 27.0.2.1. Fare riferimento alla [pagina di risoluzione dei problemi](troubleshooting.md) in caso di problemi durante l'aggiunta dei pacchetti NuGet di Xamarin.Essentials o l'aggiornamento dei pacchetti NuGet nella soluzione.

    In `MainLauncher` o per qualsiasi `Activity` avviata del progetto Android, Xamarin.Essentials deve essere inizializzato nel metodo `OnCreate`:

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState) {
        //...
        base.OnCreate(savedInstanceState);
        Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code
        //...
    ```

    Per gestire le autorizzazioni di runtime per Android, Xamarin.Essentials deve ricevere qualsiasi `OnRequestPermissionsResult`. Aggiungere il codice seguente a tutte le classi `Activity`:

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
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

Visitare il [repository GitHub Xamarin.Essentials](http://github.com/xamarin/Essentials) per vedere il codice sorgente corrente, le novità previste, eseguire gli esempi e clonare il repository. I contributi della community sono benvenuti.

Esplorare la [documentazione delle API](xref:Xamarin.Essentials) per informazioni su tutte le funzionalità di Xamarin.Essentials.
