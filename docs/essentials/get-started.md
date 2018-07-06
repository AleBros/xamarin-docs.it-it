---
title: Introduzione a Xamarin.Essentials
description: Xamarin.Essentials fornisce un'API multipiattaforma singolo che funziona con qualsiasi iOS, Android o UWP dell'applicazione che sia accessibile dal codice, indipendentemente dal modo in cui viene creata l'interfaccia utente condiviso.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 7e371a6125d223d354b75ce7e09dcc28efb3dffa
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855234"
---
# <a name="get-started-with-xamarinessentials"></a>Introduzione a Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Xamarin.Essentials fornisce un'API multipiattaforma singolo che funziona con qualsiasi iOS, Android o UWP dell'applicazione che sia accessibile dal codice, indipendentemente dal modo in cui viene creata l'interfaccia utente condiviso.

## <a name="platform-support"></a>Supporto per piattaforme

Xamarin.Essentials supporta le piattaforme e i sistemi operativi seguenti:

| Piattaforma | Versione |
| --- | --- |
| Android | 4.4 (API 19) o versione successiva |
| iOS |10.0 o versione successiva |
| UWP | 10.0.16299.0 o versione successiva |

## <a name="installation"></a>Installazione

Xamarin.Essentials è disponibile come pacchetto NuGet che può essere aggiunto a qualsiasi progetto nuovo o esistente con Visual Studio.

1. Scaricare e installare [Visual Studio](http://visualstudio.com) con il [Visual Studio tools per Xamarin](~/cross-platform/get-started/installation/index.md).

2. Aprire un progetto esistente o creare un nuovo progetto usando il modello applicazione vuota sotto **Visual Studio c#** (Android, iPhone e iPad o Cross-Platform). **Importante**: se l'aggiunta a un progetto UWP assicura Build 16299 o versione successiva è impostato nelle proprietà del progetto.

3. Aggiungere il **Xamarin.Essentials** pacchetto NuGet per ogni progetto:

    # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    Nel Pannello di Esplora soluzioni, fare clic con il pulsante destro sul nome della soluzione e selezionare **Gestisci pacchetti NuGet**. Cercare **Xamarin.Essentials** e installare il pacchetto **tutte** progetti, incluse le librerie di Android, iOS, piattaforma UWP e .NET Standard.

    > [!TIP]
    > Verificare i **Includi versione preliminare** finestra durante il [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) è disponibile in anteprima.

    # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

    Nel Pannello di Esplora soluzioni, fare clic con il pulsante destro sul nome del progetto e selezionare **Aggiungi > Aggiungi pacchetti NuGet...** . Cercare **Xamarin.Essentials** e installare il pacchetto **tutte** progetti, incluse le librerie di Android, iOS e .NET Standard.

    > [!TIP]
    > Controllare la **Mostra i pacchetti di versioni non definitive** finestra durante il [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) è disponibile in anteprima.

    -----

4. Aggiungere un riferimento a Xamarin.Essentials in qualsiasi classe c# per le API di riferimento.

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials richiede il programma di installazione specifico della piattaforma:

    # <a name="androidtabandroid"></a>[Android](#tab/android)

    Xamarin.Essentials supporta una versione minima di Android 4.4, corrispondente al livello API 19, ma la versione Android di destinazione per la compilazione deve essere 8.1, corrispondente al livello API 27. (In Visual Studio, queste due versioni vengono impostate nella finestra di dialogo proprietà del progetto per il progetto Android, nella scheda manifesto Android. In Visual Studio per Mac, essi è impostati nella finestra di dialogo Opzioni progetto per il progetto Android, nella scheda applicazione Android). 
    
    Xamarin.Essentials installa versione 27.0.2.1 delle librerie deselezionati richiesti. Qualsiasi altra libreria deselezionati richieste dall'applicazione deve inoltre essere aggiornata alla versione 27.0.2.1 usando Gestione pacchetti NuGet. Tutte le librerie di deselezionati usate dall'applicazione deve essere uguale a e deve essere almeno versione 27.0.2.1. Fare riferimento al [risoluzione dei problemi pagina](troubleshooting.md) nel caso di problemi durante l'aggiunta di Xamarin.Essentials NuGet o l'aggiornamento di pacchetti NuGet della soluzione.

    Al progetto Android `MainLauncher` o qualsiasi `Activity` vale a dire Xamarin.Essentials lanciato devono essere inizializzati nel `OnCreate` metodo:

    ```csharp
    Xamarin.Essentials.Platform.Init(this, bundle);
    ```

    Per gestire le autorizzazioni di runtime per Android, Xamarin.Essentials deve ricevere qualsiasi `OnRequestPermissionsResult`. Aggiungere il codice seguente a tutti `Activity` classi:

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

6. Seguire le [Guide Xamarin.Essentials](index.md) che consentono di copiare e incollare i frammenti di codice per ogni funzionalità.

## <a name="other-resources"></a>Altre risorse

Si consiglia agli sviluppatori familiarità con Xamarin visita [Introduzione allo sviluppo di Xamarin](~/cross-platform/getting-started/index.md).

Visitare il [repository GitHub Xamarin.Essentials](http://github.com/xamarin/Essentials) per vedere corrente nel codice sorgente, novità successiva, eseguire gli esempi e chiudere il repository. I contributi della community sono Benvenuti.

Esplorare le [documentazione dell'API](xref:Xamarin.Essentials) per tutte le funzionalità di Xamarin.Essentials.
