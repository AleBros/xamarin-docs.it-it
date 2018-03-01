---
title: App di Windows Media Player in tempo reale di Xamarin
description: Modificare e testare le App in tempo reale sul dispositivo iOS o Android
ms.topic: article
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 05/10/2017
ms.openlocfilehash: 6ae0439566387e22d939ede83e6b4cb5c1a5a8fe
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="xamarin-live-player-app"></a>App di Windows Media Player in tempo reale di Xamarin

![Funzionalità di anteprima](~/media/shared/preview.png)

## <a name="get-the-app"></a>Ottenere l'App

Xamarin Player in tempo reale sono disponibili da iOS App Store e Google Play:

[ ![Scaricare l'App Store IOS](player-images/app-store-badge.svg)](https://itunes.apple.com/us/app/xamarin-live-player/id1228841832?ls=1&mt=8) [ ![disponibile in Google Play](player-images/google-play-badge.png)](https://play.google.com/store/apps/details?id=com.xamarin.live)



## <a name="using-the-app"></a>Utilizzo dell'App

Dopo aver installato l'app sul telefono, seguire la [istruzioni di installazione](~/tools/live-player/install.md) per connettersi al computer. Provare una del [app di esempio](~/tools/live-player/samples.md) per ottenere lo stesso risultato.

All'avvio, l'app Xamarin Player in tempo reale è simile al seguente (in iOS e Android rispettivamente):

![Schermata di app iOS Player in tempo reale](player-images/app-iphone-sml.png) ![Schermata di app Android Player in tempo reale](player-images/app-android-sml.png)

Quando si preme **coppia a Visual Studio**, utilizzare la fotocamera per analizzare il codice a barre che mostra nel computer in uso:

![Schermata dello scanner di codici a barre iOS](player-images/scan-iphone-sml.png) ![Schermata dello scanner di codici a barre Android](player-images/scan-android-sml.png)

Se la connessione ha esito positivo, il codice deve essere eseguito sul dispositivo quasi immediatamente (ad esempio, l'esempio di calcolo):

![Applicazione Calcolatrice di esempio in esecuzione sul dispositivo](player-images/basic-calculator-iphone-sml.png)

## <a name="options"></a>Opzioni

Premere il pulsante informazioni **(i)** nella parte inferiore dell'app per rivelare il **opzioni** menu:

![Schermata di opzioni del menu di scelta](player-images/options.png)

### <a name="logs"></a>Log

Visualizzare i log per diagnosticare i problemi.

### <a name="settings"></a>Impostazioni

* Attiva/disattiva la visualizzazione degli errori di compilazione e runtime.
* Informazioni sulla versione.
* Inviare commenti e suggerimenti.

![Schermata delle impostazioni](player-images/settings.png)

## <a name="managing-devices"></a>La gestione dei dispositivi

Per connettere un dispositivo per la prima volta, seguire le istruzioni in [Setup & requisiti](~/tools/live-player/install.md). È possibile associare più dispositivi (ad esempio iOS e un Android) e gestirli tramite l'IDE.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

In Visual Studio, scegliere **strumenti > Xamarin Player in tempo reale > gestire i dispositivi...**

![Gestire i dispositivi finestra](player-images/manage-tools-menu-vs.png)

Questa finestra consente di eseguire le operazioni seguenti:

- Associa un nuovo dispositivo tramite l'analisi codice
- In alternativa associa un dispositivo digitando il codice visualizzato nella relativa schermata.
- Rimuovere i dispositivi esistenti dall'elenco

È inoltre possibile accedere a questa finestra dall'elenco dei dispositivi.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

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
- [Esempi di Xamarin Player in tempo reale](~/tools/livehttps://developer.xamarin.com/samples.md)
