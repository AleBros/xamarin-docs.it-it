---
title: Xamarin.Essentials
description: Xamarin.Essentials fornisce un'API multipiattaforma singolo che funziona con qualsiasi iOS, Android o UWP dell'applicazione che è possibile accedere da codice indipendentemente dalla modalità di creazione dell'interfaccia utente condiviso.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 960682c4b7776214ab303e861be4e3a8bfa9388a
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-xamarinessentials"></a>Guida introduttiva Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Xamarin.Essentials fornisce un'API multipiattaforma singolo che funziona con qualsiasi iOS, Android o UWP dell'applicazione che è possibile accedere da codice indipendentemente dalla modalità di creazione dell'interfaccia utente condiviso.

## <a name="platform-support"></a>Supporto per piattaforme

Xamarin.Essentials supporta le piattaforme e sistemi operativi seguenti:

| Piattaforma | Versione |
| --- | --- |
| Android | 4.4 (API 19) o versione successiva |
| iOS |10:0 o superiore |
| UWP | 10.0.6299.0 o versione successiva |

## <a name="installation"></a>Installazione

Xamarin.Essentials è disponibile come pacchetto NuGet che può essere aggiunto a qualsiasi progetto nuovo o esistente utilizzando Visual Studio.

1. Scaricare e installare [Visual Studio](http://visualstudio.com) con il [Visual Studio tools per Xamarin](~/cross-platform/get-started/installation/index.md).

2. Aprire un progetto esistente o creare un nuovo progetto utilizzando il modello applicazione vuota sotto **Visual Studio c#** (Android, iPhone e iPad o multipiattaforma). **Importante**: se l'aggiunta a un progetto UWP assicurarsi compilazione 16299 o versione successiva è impostato nelle proprietà del progetto.

3. Aggiungere il **Xamarin.Essentials** pacchetto NuGet per ogni progetto:

    # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    Nel riquadro Esplora soluzioni, fare clic con il pulsante destro sul nome della soluzione e selezionare **Gestisci pacchetti NuGet**. Cercare **Xamarin.Essentials** e installare il pacchetto in **tutte** progetti incluse le librerie di Android, iOS, UWP e .NET Standard.

    > [!TIP]
    > Controllare la **Includi versione provvisoria** durante la [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) è disponibile in anteprima.

    # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

    Nel riquadro Esplora soluzioni, fare clic con il pulsante destro sul nome del progetto e selezionare **Aggiungi > aggiungere pacchetti NuGet...** . Cercare **Xamarin.Essentials** e installare il pacchetto in **tutte** progetti incluse le librerie di Android, iOS e .NET Standard.

    > [!TIP]
    > Controllare la **Mostra i pacchetti pre-release** durante la [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) è disponibile in anteprima.

    -----

4. Aggiungere un riferimento a Xamarin.Essentials in alcuna classe c# per le API di riferimento.

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials richiede l'installazione specifico della piattaforma:

    # <a name="androidtabandroid"></a>[Android](#tab/android)

    Nel progetto Android `MainLauncher` o qualsiasi `Activity` vale a dire Xamarin.Essentials avviato devono essere inizializzati nel `OnCreate` metodo:

    ```csharp
    Xamarin.Essentials.Platform.Init(this, bundle);
    ```

    Per gestire le autorizzazioni di runtime per Android, Xamarin.Essentials deve ricevere qualsiasi `OnRequestPermissionsResult`. Aggiungere il codice seguente a tutti i `Activity` classi:

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
    {
        Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
    ```

    # <a name="iostabios"></a>[iOS](#tab/ios)

    Non è necessaria alcuna configurazione aggiuntiva.

    # <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

    Non è necessaria alcuna configurazione aggiuntiva.

    -----

6. Seguire le [Xamarin.Essentials Guide](index.md) che consentono di copiare e incollare i frammenti di codice per ogni funzionalità.

## <a name="other-resources"></a>Altre risorse

Si consiglia agli sviluppatori familiarità con Xamarin visita [Introduzione allo sviluppo di Xamarin](~/cross-platform/getting-started/index.md).

Visitare il [repository GitHub Xamarin.Essentials](http://github.com/xamarin/Essentials) visualizzare corrente codice origine, sviluppi successivamente, eseguire gli esempi e chiudere il repository. I contributi della community sono Benvenuti!

Esplorare le [documentazione dell'API](xref:Xamarin.Essentials) per tutte le funzionalità di Xamarin.Essentials.
