---
title: Programma di installazione di Xamarin Live Player
description: Questo documento descrive come configurare Xamarin Live Player e usarlo per apportare modifiche in tempo reale a un'applicazione in esecuzione.
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: 50cec7fc59d31ec0312894821f8fd071a445ab8f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123677"
---
# <a name="xamarin-live-player-setup"></a>Programma di installazione di Xamarin Live Player

Xamarin Live Player consente di apportare modifiche all'App e che le modifiche inserite in tempo reale nel dispositivo. Il codice viene eseguito all'interno dell'app Xamarin Live Player: non è necessario configurare emulatori o usare i cavi per distribuire! Questo articolo descrive come configurare Xamarin Live Player.

![Funzionalità di anteprima](~/media/shared/preview.png)

## <a name="1-get-the-android-app"></a>1. Scaricare l'App Android

Xamarin Live Player è disponibile per Android da Google Play:

[ ![Disponibile su Google Play](install-images/google-play-badge.png)](https://play.google.com/store/apps/details?id=com.xamarin.live)

Per i dispositivi Android senza Google Play è disponibile tramite Xamarin Live Player [HockeyApp](https://aka.ms/xlp-hockeyapp) distribuzione. Inoltre, build di anteprima per Android può essere installato direttamente da Google Play per acconsentire esplicitamente al [programma beta aperto](https://play.google.com/apps/testing/com.xamarin.live)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="2-get-visual-studio-2017"></a>2. Ottenere Visual Studio 2017

Xamarin Live Player, è necessario:

- Visual Studio 2017 15.4 o versioni successive.
- Un computer di Visual Studio e un dispositivo nella stessa rete Wi-Fi.

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3. Uso di Xamarin Live Player per la prima volta

1. Aprire **Visual Studio 2017**.
2. Passare a **strumenti > Opzioni...**  e selezionare il **Xamarin > altro** scheda.
3. Segni di graduazione **Abilita Xamarin Live Player**:

    ![Selezionare la casella Abilita Xamarin Live Player nella finestra di dialogo Opzioni](install-images/vs2017-options.png)

4. Creare o aprire un progetto Xamarin (o un [esempio](~/tools/live-player/samples.md)).
5. Scegli **Live Player** nell'elenco dei dispositivi:

    ![Elenco dei dispositivi include un'opzione di Xamarin Live Player](install-images/devices-empty-windows.png)

    - Se si dispone già associato un dispositivo, sarà disponibile come opzione.
    - In caso contrario, verrà richiesto di associare un dispositivo quando richiesto.

6. Premere il **eseguiti** pulsante oppure selezionare una delle seguenti opzioni dal **eseguire** o menu di scelta rapida:

    - **Avvia senza eseguire debug** – è possibile modificare l'app e vedere che si verificano le modifiche nel dispositivo (riavvio dell'app quando vengono apportate modifiche e salvare il file).
    - **Avvia debug** : è possibile impostare punti di interruzione e ispezionare le variabili, ma non è possibile modificare codice.

    In alternativa, selezionare **strumenti > Xamarin Live Player > Vista corrente Live Run**, che consente di modificare l'app e vedere che si verificano le modifiche nel dispositivo. La visualizzazione corrente viene visualizzata (anziché schermata principale dell'applicazione).

7. Se è già associato un dispositivo e l'app Xamarin Live Player sia in esecuzione nel dispositivo, il codice verrà eseguito immediatamente.

    Se nessuna periferica è associato, verrà visualizzato un codice a matrice con istruzioni su come associare un dispositivo:

    ![Coppia di una finestra del dispositivo](install-images/manage-empty-windows.png)

    Se il dispositivo non può essere contattato presupposti per appaiare, potrebbe apparire un errore.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="2-get-visual-studio-for-mac"></a>2. Ottenere Visual Studio per Mac

Xamarin Live Player, è necessario:

- OS X 10.11, macOS 10.12 o versione successiva
- Visual Studio per Mac
- Un computer Mac e un dispositivo nella stessa rete Wi-Fi

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3. Uso di Xamarin Live Player per la prima volta

1. Aprire **Visual Studio per Mac**.
2. Passare a **Visual Studio > Preferenze...**  e selezionare il **progetti > Xamarin Live Player (anteprima)** scheda.
3. Segni di graduazione **Abilita Xamarin Live Player**:

    [![Selezionare la casella Abilita Xamarin Live Player nella finestra di dialogo Opzioni](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

4. Creare o aprire un progetto Xamarin (o un [esempio](~/tools/live-player/samples.md)).
5. Scegli **Live Player** nell'elenco dei dispositivi.

    ![Elenco dei dispositivi include un'opzione di Xamarin Live Player](install-images/devices.png)

    - Se si dispone già associato un dispositivo, sarà disponibile come opzione.
    - In caso contrario, verrà richiesto di associare un dispositivo quando richiesto.
    - Scegliere **dispositivi Xamarin Live Player...**  per gestire i dispositivi da usare con Xamarin Live Player.

6. Premere il **eseguiti** pulsante oppure selezionare una delle seguenti opzioni dal **eseguire** o menu di scelta rapida:

    ![Menu Opzioni di esecuzione](install-images/run-menu.png)

    - **Avvia senza eseguire debug** – è possibile modificare l'app e vedere che si verificano le modifiche nel dispositivo (riavvio dell'app quando vengono apportate modifiche e salvare il file).
    - **Avvia debug** : È possibile impostare punti di interruzione e ispezionare le variabili, ma non è possibile modificare codice.
    - **Eseguire Vista corrente Live** – è possibile modificare l'app e vedere che si verificano le modifiche nel dispositivo. La visualizzazione corrente viene visualizzata (anziché schermata principale dell'applicazione).

7. Se è già associato un dispositivo e l'app Xamarin Live Player sia in esecuzione nel dispositivo, il codice verrà eseguito immediatamente.

    Se non è associato alcun dispositivo, verrà visualizzato un codice a matrice con istruzioni su come associare un dispositivo:

    ![Coppia di una finestra del dispositivo](install-images/manage-empty.png)

    Se il dispositivo non può essere contattato presupposti per appaiare, verrà visualizzato un errore:

    ![Impossibile connettersi al messaggio di errore di dispositivo](install-images/error-cannot-connect.png)

-----

Se si riscontrano problemi o non è possibile connettersi, vedere [limitazioni e risoluzione dei problemi](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Collegamenti correlati

- [Limitazioni](~/tools/live-player/limitations.md)
- [Risoluzione dei problemi](~/tools/live-player/troubleshooting.md)
- [Esempi di Xamarin Live Player](~/tools/live-player/samples.md)
