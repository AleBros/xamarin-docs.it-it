---
title: Configurazione di Xamarin Live Player Visual Studio
description: Questo documento descrive come usare Xamarin Live Player per apportare modifiche in tempo reale a un'applicazione in esecuzione.
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: lobrien
ms.author: laobri
ms.date: 06/13/2019
ms.openlocfilehash: a29a637526c2829b44ae89d505dac37a648dee77
ms.sourcegitcommit: 93b1e2255d59c8ca6674485938f26bd425740dd1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67157749"
---
# <a name="xamarin-live-player-visual-studio-configuration"></a>Configurazione di Xamarin Live Player Visual Studio

![Funzionalità di anteprima](~/media/shared/preview.png)

> [!WARNING]
> Xamarin Live Player Preview è stata terminata. L'app non è più disponibile. Le istruzioni riportate di seguito vengono fornite per i clienti di continuare a usare l'anteprima con Visual Studio 2017.

> [!TIP]
> È possibile usare la [Visualizzatore anteprima XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) in Visual Studio 2019 o Visual Studio per Mac per visualizzare la progettazione dello schermo durante la modifica.

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

## <a name="using-xamarin-live-player"></a>Uso di Xamarin Live Player

Hai già l'app Xamarin Live Player nel dispositivo. Non è più disponibile per il download.

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

    Se non è associato alcun dispositivo, verrà visualizzato un codice a matrice con istruzioni su come associare un dispositivo:

    ![Coppia di una finestra del dispositivo](install-images/manage-empty-windows.png)

    Se il dispositivo non può essere contattato presupposti per appaiare, potrebbe apparire un errore.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="using-xamarin-live-player"></a>Uso di Xamarin Live Player

Hai già l'app Xamarin Live Player nel dispositivo. Non è più disponibile per il download.

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

- [Risoluzione dei problemi](~/tools/live-player/troubleshooting.md)
