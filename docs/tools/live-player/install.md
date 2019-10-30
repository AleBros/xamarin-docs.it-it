---
title: Xamarin Live Player la configurazione di Visual Studio
description: Questo documento descrive come usare la Xamarin Live Player per eseguire modifiche in tempo reale in un'applicazione in esecuzione.
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: davidortinau
ms.author: daortin
ms.date: 06/13/2019
ms.openlocfilehash: 3dfe63cf3cf87a99d15879a0d4791248fa06f195
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029681"
---
# <a name="xamarin-live-player-visual-studio-configuration"></a>Xamarin Live Player la configurazione di Visual Studio

![Funzionalità di anteprima](~/media/shared/preview.png)

> [!WARNING]
> L'anteprima del Xamarin Live Player è terminata. L'app non è più disponibile. Le istruzioni riportate di seguito vengono fornite ai clienti che continuano a usare l'anteprima con Visual Studio 2017.

> [!TIP]
> È possibile usare il visualizzatore [Anteprima XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) in Visual Studio 2019 o Visual Studio per Mac per visualizzare le progettazioni dello schermo durante la modifica.

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

## <a name="using-xamarin-live-player"></a>Utilizzo di Xamarin Live Player

È necessario avere già l'app Xamarin Live Player nel dispositivo. Non è più disponibile per il download.

1. Aprire **Visual Studio 2017**.
2. Passare a **strumenti > opzioni...** e selezionare la **Novell > altra** scheda.
3. Seleziona **Xamarin Live Player Abilita**:

    ![Selezionare la casella Abilita Xamarin Live Player nella finestra Opzioni](install-images/vs2017-options.png)

4. Creare o aprire un progetto Novell (o un [esempio](~/tools/live-player/samples.md)).
5. Scegliere **Live Player** nell'elenco dei dispositivi:

    ![L'elenco dei dispositivi include un'opzione di Xamarin Live Player](install-images/devices-empty-windows.png)

    - Se un dispositivo è già stato associato, sarà disponibile come opzione.
    - In caso contrario, verrà richiesto di associare un dispositivo quando richiesto.

6. Premere il pulsante **Esegui** oppure selezionare una delle opzioni seguenti dal menu **Esegui** o fare clic con il pulsante destro del mouse:

    - **Avvia senza eseguire debug** : è possibile modificare l'app e visualizzare le modifiche apportate nel dispositivo. l'app viene riavviata quando vengono apportate modifiche e il file viene salvato.
    - **Avviare il debug** : è possibile impostare punti di interruzione ed esaminare le variabili, ma il codice non può essere modificato.

    In alternativa, selezionare **strumenti > Xamarin Live Player > Live Run Current View**, che consente di modificare l'app e visualizzare le modifiche apportate nel dispositivo. Viene visualizzata la visualizzazione corrente, invece della schermata principale dell'applicazione.

7. Se un dispositivo è già associato e l'app Xamarin Live Player è in esecuzione nel dispositivo, il codice viene eseguito immediatamente.

    Se nessun dispositivo è associato, verrà visualizzato un codice a matrice con le istruzioni su come associare un dispositivo:

    ![Associare una finestra del dispositivo](install-images/manage-empty-windows.png)

    Se non è possibile contattare il dispositivo per l'associazione, potrebbe essere visualizzato un errore.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="using-xamarin-live-player"></a>Utilizzo di Xamarin Live Player

È necessario avere già l'app Xamarin Live Player nel dispositivo. Non è più disponibile per il download.

1. Aprire **Visual Studio per Mac**.
2. Passare a **Visual Studio > preferenze** e selezionare la scheda **progetti > Xamarin Live Player (anteprima)** .
3. Seleziona **Xamarin Live Player Abilita**:

    [![selezionare la casella Abilita Xamarin Live Player nella finestra Opzioni](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

4. Creare o aprire un progetto Novell (o un [esempio](~/tools/live-player/samples.md)).
5. Scegliere **Live Player** nell'elenco dei dispositivi.

    ![L'elenco dei dispositivi include un'opzione di Xamarin Live Player](install-images/devices.png)

    - Se un dispositivo è già stato associato, sarà disponibile come opzione.
    - In caso contrario, verrà richiesto di associare un dispositivo quando richiesto.
    - Scegliere **Xamarin Live Player dispositivi** per gestire i dispositivi che si desidera utilizzare con Xamarin Live Player.

6. Premere il pulsante **Esegui** oppure selezionare una delle opzioni seguenti dal menu **Esegui** o fare clic con il pulsante destro del mouse:

    ![Opzioni del menu Esegui](install-images/run-menu.png)

    - **Avvia senza eseguire debug** : è possibile modificare l'app e visualizzare le modifiche apportate nel dispositivo. l'app viene riavviata quando vengono apportate modifiche e il file viene salvato.
    - **Avviare il debug** : è possibile impostare punti di interruzione ed esaminare le variabili, ma il codice non può essere modificato.
    - **Visualizzazione corrente Live Run** : è possibile modificare l'app e visualizzare le modifiche apportate nel dispositivo. Viene visualizzata la visualizzazione corrente, invece della schermata principale dell'applicazione.

7. Se un dispositivo è già associato e l'app Xamarin Live Player è in esecuzione nel dispositivo, il codice viene eseguito immediatamente.

    Se nessun dispositivo è associato, verrà visualizzato un codice a matrice con le istruzioni su come associare un dispositivo:

    ![Associare una finestra del dispositivo](install-images/manage-empty.png)

    Se non è possibile contattare il dispositivo per l'associazione, verrà visualizzato un errore:

    ![Impossibile connettersi al messaggio di errore del dispositivo](install-images/error-cannot-connect.png)

-----

Se si verificano problemi o non è possibile connettersi, vedere [limitazioni e risoluzione dei problemi](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Collegamenti correlati

- [Risoluzione dei problemi](~/tools/live-player/troubleshooting.md)
