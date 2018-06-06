---
title: App di Windows Media Player in tempo reale di Xamarin
description: Questo documento descrive il giocatore Live Xamarin app, che può essere usato per visualizzare in anteprima le modifiche al codice in tempo reale sul dispositivo. Viene descritto il programma di installazione, gli esempi, i registri, le impostazioni, gestione dei dispositivi e altro ancora.
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: topgenorth
ms.author: toopge
ms.date: 05/14/2017
ms.openlocfilehash: 88f7f62650484007c221aa7baaa684f872e0a8e9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794150"
---
# <a name="xamarin-live-player-app"></a>App di Windows Media Player in tempo reale di Xamarin

![Funzionalità di anteprima](~/media/shared/preview.png)

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
