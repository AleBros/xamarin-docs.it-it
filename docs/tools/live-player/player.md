---
title: App Xamarin Live Player
description: Questo documento descrive l'app Xamarin Live Player, che è possibile usare per visualizzare in anteprima le modifiche del codice in tempo reale nel dispositivo. Vengono illustrati l'installazione, gli esempi, i log, le impostazioni, la gestione dei dispositivi e altro ancora.
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: davidortinau
ms.author: daortin
ms.date: 06/13/2019
ms.openlocfilehash: f1d3adfad3b9d2a64990ff11f81bf3a1a39825bb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029650"
---
# <a name="xamarin-live-player-app"></a>App Xamarin Live Player

![Funzionalità di anteprima](~/media/shared/preview.png)

> [!WARNING]
> L'anteprima del Xamarin Live Player è terminata. L'app non è più disponibile. Le istruzioni riportate di seguito vengono fornite ai clienti che continuano a usare l'anteprima con Visual Studio 2017.

> [!TIP]
> È possibile usare il visualizzatore [Anteprima XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) in Visual Studio 2019 o Visual Studio per Mac per visualizzare le progettazioni dello schermo durante la modifica.

All'avvio, l'app Xamarin Live Player ha un aspetto simile al seguente:

![Schermata dell'app Android Live Player](player-images/app-android-sml.png)

Quando si preme **associa a Visual Studio**, usare la fotocamera per analizzare il codice a barre visualizzato nel computer:

![Screenshot dello scanner di codice a barre Android](player-images/scan-android-sml.png)

Se la connessione ha esito positivo, il codice deve essere eseguito quasi immediatamente nel dispositivo (ad esempio, l' [esempio di calcolatrice](https://github.com/xamarin/mobile-samples/tree/master/LivePlayer/BasicCalculator):

![App di calcolo di esempio in esecuzione nel dispositivo](player-images/basic-calculator-sml.png)

## <a name="options"></a>Opzioni

Premere il pulsante informazioni **(i)** nella parte inferiore dell'app per visualizzare il menu **Opzioni** :

[![screenshot del menu opzioni](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>Log

Visualizzare i log per diagnosticare i problemi.

### <a name="settings"></a>Impostazioni

- Consente di visualizzare o disabilitare gli errori di compilazione e di Runtime.
- Informazioni sulla versione.
- Invia commenti e suggerimenti.

[![screenshot delle impostazioni](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>Gestione dei dispositivi

Per connettere un dispositivo per la prima volta, seguire le istruzioni riportate in [requisiti & installazione](~/tools/live-player/install.md). È possibile associare più dispositivi e gestirli tramite l'IDE.

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

In Visual Studio scegliere **strumenti > Xamarin Live Player > Gestisci dispositivi...**

![Finestra Gestisci dispositivi](player-images/manage-tools-menu-vs.png)

Questa finestra consente di eseguire le operazioni seguenti:

- Associare un nuovo dispositivo eseguendo la scansione del codice
- In alternativa, abbinare un dispositivo digitando il codice visualizzato sullo schermo
- Rimuovi i dispositivi esistenti dall'elenco

È anche possibile accedere a questa finestra dall'elenco dei dispositivi.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

In Visual Studio per Mac scegliere **strumenti > (Xamarin Live Player) Gestisci dispositivi...**

![Finestra Gestisci dispositivi](player-images/manage-tools-menu.png)

Questa finestra consente di eseguire le operazioni seguenti:

- Associare un nuovo dispositivo eseguendo la scansione del codice
- In alternativa, abbinare un dispositivo digitando il codice visualizzato sullo schermo
- Rimuovi i dispositivi esistenti dall'elenco

![Finestra Gestisci dispositivi](player-images/manage.png)

È anche possibile accedere a questa finestra dall'elenco dei dispositivi:

![Scegliere Xamarin Live Player dispositivi dall'elenco dei dispositivi](player-images/manage-device-menu.png)

-----

Se si verificano problemi [, vedere limitazioni e risoluzione dei problemi](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Collegamenti correlati

- [Risoluzione dei problemi](~/tools/live-player/troubleshooting.md)
