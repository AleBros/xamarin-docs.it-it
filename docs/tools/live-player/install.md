---
title: Programma di installazione di Xamarin Player in tempo reale
description: Modificare e testare le App in tempo reale sul dispositivo iOS o Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/22/2017
ms.openlocfilehash: f3fa2502f55ad7ebf59d015923ff4008103b08bd
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="xamarin-live-player-setup"></a>Programma di installazione di Xamarin Player in tempo reale

Xamarin Player in tempo reale consente di apportare modifiche in tempo reale all'App e le modifiche riflesse in tempo reale sul dispositivo. Il codice viene eseguito all'interno dell'app Xamarin Player Live: non è necessario impostare gli emulatori o usare cavi per distribuire! In questo articolo viene descritto come impostare Xamarin Player in tempo reale.

![Funzionalità di anteprima](~/media/shared/preview.png)

## <a name="1-get-the-app"></a>1. Ottenere l'App

### <a name="xamarin-live-player-for-android"></a>Xamarin Player in tempo reale per Android
Xamarin Player in tempo reale è disponibile per Android da Google Play:

[ ![Disponibile su Google Play](install-images/google-play-badge.png)](https://play.google.com/store/apps/details?id=com.xamarin.live)

Per i dispositivi Android senza Google Play Live Xamarin Player è disponibile tramite [HockeyApp](https://aka.ms/xlp-hockeyapp) distribuzione. Inoltre, l'anteprima compilazioni per Android possono essere installati direttamente da Google Play dalla scelta di utilizzare il [programma beta aperto](https://play.google.com/apps/testing/com.xamarin.live)

### <a name="xamarin-live-player-for-ios"></a>Xamarin Player in tempo reale per iOS
Che incoraggia la collaborazione agli utenti di creare un join di [app Xamarin Player Live _iOS anteprima_ ](https://aka.ms/liveplayeralpha) per accedere rapidamente ai miglioramenti più aggiornati tramite TestFlight.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="2-get-visual-studio-2017-preview-on-windows-or-for-mactabsvsmac"></a>2. Ottenere Visual Studio 2017 Preview in Windows (o [per Mac](?tabs=vsmac))

Xamarin Player in tempo reale, è necessario:

- Visual Studio 2017 [15.4](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/#visualstudio2017) o versione successiva.
- Un computer di Visual Studio e un dispositivo nella stessa rete Wi-Fi

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3. Con Xamarin Player in tempo reale per la prima volta

1. Aprire **Visual Studio 2017**.
2. Passare a **strumenti > Opzioni...**  e selezionare il **Xamarin > altri** scheda.
3. Segni di graduazione **abilitare Xamarin Player in tempo reale**:

  ![Selezionare la casella Abilita Xamarin Player in tempo reale nella finestra di dialogo Opzioni](install-images/vs2017-options.png)

2. Creare o aprire un progetto Xamarin (o un [esempio](~/tools/live-player/samples.md)).
3. Scegliere **Live Player** nell'elenco dei dispositivi:

  ![Elenco dei dispositivi include un'opzione Xamarin Player in tempo reale](install-images/devices-empty-windows.png)

  * Se si dispone già associato un dispositivo, sarà disponibile come opzione.
  * In caso contrario verrà richiesto per l'associazione di un dispositivo quando necessario.
4. Premere il **eseguire** pulsante o selezionare una delle seguenti opzioni di **eseguire** o menu di scelta rapida:

  - **Avvia senza eseguire debug** : è possibile modificare l'applicazione e vedere le modifiche si verificano nel dispositivo (app viene riavviata quando vengono apportate modifiche e salvare il file).
  - **Avvia debug** : è possibile impostare punti di interruzione e controllare le variabili, ma è Impossibile modificare il codice.

  In alternativa, selezionare **strumenti > Xamarin Player in tempo reale > Live visualizzazione corrente eseguire**, che consente di modificare l'applicazione e vedere le modifiche si verificano nel dispositivo. La visualizzazione corrente (anziché schermata principale dell'applicazione).

5. Se è già associato un dispositivo e l'app Xamarin Player in tempo reale è in esecuzione sul dispositivo, il codice viene eseguito subito!

  Se nessuna periferica viene abbinata, verrà visualizzato un codice a matrice con istruzioni su come per l'associazione di un dispositivo:

    ![Coppia di una finestra del dispositivo](install-images/manage-empty-windows.png)

  Se non è possibile contattare il dispositivo per l'associazione, potrebbe verificarsi un errore.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

## <a name="2-get-visual-studio-for-mac-or-for-windowstabsvswin"></a>2. Ottenere Visual Studio per Mac (o [per Windows](?tabs=vswin))

Xamarin Player in tempo reale, è necessario:

- OS X 10.11, macOS 10.12 o superiore
- Visual Studio per Mac
- Un Mac e un dispositivo nella stessa rete Wi-Fi

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3. Con Xamarin Player in tempo reale per la prima volta

1. Aprire **Visual Studio per Mac**.
2. Passare a **Visual Studio > Preferenze...**  e selezionare il **progetti > Xamarin Player in tempo reale (anteprima)** scheda.
3. Segni di graduazione **abilitare Xamarin Player in tempo reale**:

  [ ![Selezionare la casella Abilita Xamarin Player in tempo reale nella finestra di dialogo Opzioni](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png)

2. Creare o aprire un progetto Xamarin (o un [esempio](~/tools/live-player/samples.md)).
3. Scegliere **Live Player** nell'elenco dei dispositivi.

  ![Elenco dei dispositivi include un'opzione Xamarin Player in tempo reale](install-images/devices.png)

  * Se si dispone già associato un dispositivo, sarà disponibile come opzione.
  * In caso contrario verrà richiesto per l'associazione di un dispositivo quando necessario.
  * Scegliere **Xamarin Player in tempo reale dispositivi...**  per gestire i dispositivi che si desidera utilizzare con Xamarin Player in tempo reale.

4. Premere il **eseguire** pulsante o selezionare una delle seguenti opzioni di **eseguire** o menu di scelta rapida:

  ![Opzioni di menu di esecuzione](install-images/run-menu.png)

  - **Avvia senza eseguire debug** : È possibile modificare l'applicazione e vedere le modifiche si verificano nel dispositivo (app viene riavviata quando vengono apportate modifiche e salvare il file).
  - **Avvia debug** : È possibile impostare punti di interruzione e controllare le variabili, ma è Impossibile modificare il codice.
  - **Visualizzazione corrente di esecuzione di Live** : È possibile modificare l'applicazione e vedere le modifiche si verificano nel dispositivo. La visualizzazione corrente (anziché schermata principale dell'applicazione).

5. Se è già associato un dispositivo e l'app Xamarin Player in tempo reale è in esecuzione sul dispositivo, il codice viene eseguito subito!

  Se non è associato alcun dispositivo, verrà visualizzato un codice a matrice con istruzioni su come associare un dispositivo:

    ![Coppia di una finestra del dispositivo](install-images/manage-empty.png)

  Se il dispositivo non può essere contattato per l'associazione, verrà visualizzato un errore:

    ![Impossibile connettersi al messaggio di errore dispositivo](install-images/error-cannot-connect.png)


-----

Se si verificano problemi, vedere [limitazioni e risoluzione dei problemi](~/tools/live-player/troubleshooting.md).


## <a name="related-links"></a>Collegamenti correlati

- [Limitazioni](~/tools/live-player/limitations.md)
- [Risoluzione dei problemi](~/tools/live-player/troubleshooting.md)
- [Esempi di Xamarin Player in tempo reale](~/tools/livehttps://developer.xamarin.com/samples.md)
