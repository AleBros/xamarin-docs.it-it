---
title: App di Windows Media Player in tempo reale di Xamarin
description: Modificare e testare le App in tempo reale sul dispositivo iOS o Android
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: topgenorth
ms.author: toopge
ms.date: 05/10/2017
ms.openlocfilehash: 2e2a3e80c121688f9a8bfff0afe4184f19fc6619
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="xamarin-live-player-app"></a>App di Windows Media Player in tempo reale di Xamarin

![Funzionalità di anteprima](~/media/shared/preview.png)

## <a name="get-the-app"></a>Ottenere l'App

# <a name="androidtabandroid"></a>[Android](#tab/android)

Xamarin Player in tempo reale è disponibile per Android da Google Play:

[![Disponibile su Google Play](images/google-play-badge.png)](https://play.google.com/store/apps/details?id=com.xamarin.live)

Per i dispositivi Android senza Google Play Live Xamarin Player è disponibile tramite [HockeyApp](https://aka.ms/xlp-hockeyapp) distribuzione. Inoltre, l'anteprima compilazioni per Android possono essere installati direttamente da Google Play dalla scelta di utilizzare il [programma beta aperto](https://play.google.com/apps/testing/com.xamarin.live)

# <a name="iostabios"></a>[iOS](#tab/ios)

Che incoraggia la collaborazione agli utenti di creare un join di [app Xamarin Player Live _iOS anteprima_ ](https://aka.ms/liveplayeralpha) per accedere rapidamente ai miglioramenti più aggiornati tramite TestFlight.

-----

## <a name="using-the-app"></a>Utilizzo dell'App

Dopo aver installato l'app sul telefono, seguire la [istruzioni di installazione](~/tools/live-player/install.md) per connettersi al computer. Provare una del [app di esempio](~/tools/live-player/samples.md) per ottenere lo stesso risultato.

All'avvio, l'app Xamarin Player in tempo reale è simile al seguente (in iOS e Android rispettivamente):

![Schermata di app iOS Player in tempo reale](player-images/app-iphone-sml.png) ![Schermata di app Android Player in tempo reale](player-images/app-android-sml.png)

Quando si preme **coppia a Visual Studio**, utilizzare la fotocamera per analizzare il codice a barre che mostra nel computer in uso:

![Schermata dello scanner di codici a barre iOS](player-images/scan-iphone-sml.png) ![Schermata dello scanner di codici a barre Android](player-images/scan-android-sml.png)

Se la connessione ha esito positivo, il codice deve essere eseguito sul dispositivo quasi immediatamente (come le [esempio Calculator](https://developer.xamarin.com/samples/mobile/LivePlayer/BasicCalculator)):

![Applicazione Calcolatrice di esempio in esecuzione sul dispositivo](player-images/basic-calculator-iphone-sml.png)

## <a name="options"></a>Opzioni

Premere il pulsante informazioni **(i)** nella parte inferiore dell'app per rivelare il **opzioni** menu:

[![Schermata di opzioni del menu di scelta](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>Log

Visualizzare i log per diagnosticare i problemi.

### <a name="settings"></a>Impostazioni

- Attiva/disattiva la visualizzazione degli errori di compilazione e runtime.
- Informazioni sulla versione.
- Inviare commenti e suggerimenti.

[![Schermata delle impostazioni](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>La gestione dei dispositivi

Per connettere un dispositivo per la prima volta, seguire le istruzioni in [Setup & requisiti](~/tools/live-player/install.md). È possibile associare più dispositivi (ad esempio iOS e un Android) e gestirli tramite l'IDE.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

In Visual Studio, scegliere **strumenti > Xamarin Player in tempo reale > gestire i dispositivi...**

![Gestire i dispositivi finestra](player-images/manage-tools-menu-vs.png)

Questa finestra consente di eseguire le operazioni seguenti:

- Associa un nuovo dispositivo tramite l'analisi codice
- In alternativa associa un dispositivo digitando il codice visualizzato nella relativa schermata.
- Rimuovere i dispositivi esistenti dall'elenco

È inoltre possibile accedere a questa finestra dall'elenco dei dispositivi.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

In Visual Studio per Mac, scegliere **strumenti > (Xamarin Player in tempo reale) di gestire i dispositivi...**

![Gestire i dispositivi finestra](player-images/manage-tools-menu.png)

Questa finestra consente di eseguire le operazioni seguenti:

- Associa un nuovo dispositivo tramite l'analisi codice
- In alternativa associa un dispositivo digitando il codice visualizzato nella relativa schermata.
- Rimuovere i dispositivi esistenti dall'elenco

![Gestire i dispositivi finestra](player-images/manage.png)

È inoltre possibile accedere a questa finestra dall'elenco dei dispositivi:

![Scegliere i dispositivi di Windows Media Player Live Xamarin dall'elenco dei dispositivi](player-images/manage-device-menu.png)

-----

Se si verifica eventuali problemi di vedere [limitazioni e risoluzione dei problemi](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Collegamenti correlati

- [Limitazioni](~/tools/live-player/limitations.md)
- [Risoluzione dei problemi](~/tools/live-player/troubleshooting.md)
- [Esempi di Xamarin Player in tempo reale](samples.md)
