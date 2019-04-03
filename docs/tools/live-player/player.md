---
title: App Xamarin Live Player
description: Questo documento descrive Xamarin Live Player app, che può essere utilizzato per visualizzare in anteprima le modifiche al codice in tempo reale nel dispositivo. Viene illustrato il programma di installazione, esempi, i log, impostazioni, gestione dei dispositivi e altro ancora.
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: lobrien
ms.author: laobri
ms.date: 08/08/2017
ms.openlocfilehash: 89795e5df00b426c0f11c04a0844993071df1e25
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58855198"
---
# <a name="xamarin-live-player-app"></a>App Xamarin Live Player

![Funzionalità di anteprima](~/media/shared/preview.png)

> [!NOTE]
> Live Player anteprima è disponibile solo in Visual Studio 2017.

Dopo aver installato l'app sul telefono, seguire le [istruzioni di installazione](~/tools/live-player/install.md) per connettersi al computer in uso. Provare una delle [app di esempio](~/tools/live-player/samples.md) per renderla funzionante.

All'avvio, l'app Xamarin Live Player aspetto simile al seguente:

![Schermata di app Android Player in tempo reale](player-images/app-android-sml.png)

Quando si preme **coppia da Visual Studio**, usare la fotocamera per analizzare il codice a barre che mostra nel computer in uso:

![Screenshot dello scanner di codici a barre Android](player-images/scan-android-sml.png)

Se la connessione ha esito positivo, il codice deve eseguire quasi immediatamente nel dispositivo (ad esempio la [esempio della Calcolatrice](https://developer.xamarin.com/samples/mobile/LivePlayer/BasicCalculator)):

![Applicazione Calcolatrice di esempio in esecuzione sul dispositivo](player-images/basic-calculator-sml.png)

## <a name="options"></a>Opzioni

Premere il pulsante informazioni **(i)** nella parte inferiore dell'app per visualizzare i **opzioni** menu:

[![Screenshot del menu Opzioni](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>Log

Visualizzare i log per diagnosticare i problemi.

### <a name="settings"></a>Impostazioni

- Attiva/disattiva visualizzazione degli errori di compilazione e runtime.
- Informazioni sulla versione.
- Inviare commenti e suggerimenti.

[![Screenshot delle impostazioni](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>La gestione dei dispositivi

Per connettere un dispositivo per la prima volta, seguire le istruzioni riportate in [Setup & requisiti](~/tools/live-player/install.md). È possibile associare più dispositivi e gestirli tramite l'IDE.

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

In Visual Studio, scegliere **strumenti > Xamarin Live Player > Gestione dei dispositivi...**

![Gestire i dispositivi finestra](player-images/manage-tools-menu-vs.png)

In questa finestra consente di eseguire le operazioni seguenti:

- Associare un nuovo dispositivo analizzando il codice
- In alternativa associare un dispositivo digitando il codice visualizzato nella relativa schermata
- Rimuovere i dispositivi esistenti dall'elenco

È anche possibile accedere a questa finestra dall'elenco dei dispositivi.

# [<a name="visual-studio-for-mac"></a>Visual Studio per Mac](#tab/macos)

In Visual Studio per Mac, scegliere **strumenti > (Xamarin Live Player) gestire i dispositivi...**

![Gestire i dispositivi finestra](player-images/manage-tools-menu.png)

In questa finestra consente di eseguire le operazioni seguenti:

- Associare un nuovo dispositivo analizzando il codice
- In alternativa associare un dispositivo digitando il codice visualizzato nella relativa schermata
- Rimuovere i dispositivi esistenti dall'elenco

![Gestire i dispositivi finestra](player-images/manage.png)

È anche possibile accedere a questa finestra dall'elenco dei dispositivi:

![Scegliere i dispositivi di Xamarin Live Player dall'elenco dei dispositivi](player-images/manage-device-menu.png)

-----

Se si verifica eventuali vedere problemi [limitazioni e risoluzione dei problemi](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Collegamenti correlati

- [Risoluzione dei problemi](~/tools/live-player/troubleshooting.md)
- [Campioni da utilizzare con Live Player](https://developer.xamarin.com/samples/xamarin-live-player/all/)
