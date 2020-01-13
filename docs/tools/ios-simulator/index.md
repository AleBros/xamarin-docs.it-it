---
title: Simulatore iOS remoto per Windows
description: Il simulatore iOS remoto per Windows consente di testare le app in un simulatore iOS visualizzato in Windows insieme a Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: davidortinau
ms.author: daortin
ms.date: 04/26/2019
ms.openlocfilehash: d5898f9c6ee30eb1f12bf6480b93a609e762e6ea
ms.sourcegitcommit: ec62e2624295aa502ec35ac782031d61d61c3aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75886593"
---
# <a name="remoted-ios-simulator-for-windows"></a>Simulatore iOS remoto per Windows

Il simulatore iOS remoto per Windows consente di testare le app in un simulatore iOS visualizzato in Windows insieme a Visual Studio 2019 e Visual Studio 2017.

[![simulatore iOS in esecuzione in Windows](images/hero-sml.png "simulatore iOS in esecuzione in Windows")](images/hero.png#lightbox)

## <a name="getting-started"></a>Per iniziare

Il simulatore iOS remoto per Windows viene installato automaticamente come parte di Novell in Visual Studio 2019 e Visual Studio 2017. Per usarlo, seguire questa procedura:

1. [Associare Visual 2019 a un host di compilazione Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. In Visual Studio avviare il debug di un progetto iOS o tvOS. Il simulatore iOS remoto per Windows verrà visualizzato nel computer Windows.

Guardare [questo video](deploy.md) per una guida dettagliata.

## <a name="simulator-window"></a>Finestra del simulatore

La barra degli strumenti nella parte superiore della finestra del simulatore contiene una serie di pulsanti utili:

- **Home** : simula il pulsante Home in un dispositivo iOS.
- **Lock** : blocca il simulatore (scorrere rapidamente).
- **Screenshot** : Salva una schermata del simulatore (archiviata in **Pictures\Xamarin\iOS Simulator\\** ).
- [**Impostazioni**](#settings) : Visualizza la tastiera, la posizione e altre impostazioni.
- [**Altre opzioni**](#other-options) : Visualizza varie opzioni del simulatore, ad esempio rotazione, movimenti di agitazione e ID tocco.

    [![esempio di Maps per simulatori iOS](images/maps-app-sml.png "esempio di Maps per simulatori iOS")](images/maps-app.png#lightbox)

## <a name="settings"></a>Impostazioni

Facendo clic sull'icona a forma di ingranaggio della barra degli strumenti viene visualizzata **la finestra**

[![impostazioni del simulatore iOS](images/settings-sml.png "impostazioni del simulatore iOS")](images/settings.png#lightbox)

Queste impostazioni consentono di abilitare la tastiera hardware, scegliere una posizione che il dispositivo deve segnalare (i percorsi statici e mobili sono entrambi supportati), abilitare l'ID tocco e reimpostare il contenuto e le impostazioni per il simulatore.

## <a name="other-options"></a>Altre opzioni

Il pulsante con i puntini di sospensione della barra degli strumenti rivela altre opzioni, ad esempio rotazione, movimenti di agitazione e riavvio. Queste stesse opzioni possono essere visualizzate come un elenco facendo clic con il pulsante destro del mouse in un punto qualsiasi della finestra del simulatore:

[![impostazioni aggiuntive del simulatore iOS](images/more-sml.png "impostazioni aggiuntive del simulatore iOS")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>Supporto per touchscreen

La maggior parte dei moderni computer Windows presenta schermi touch. Poiché il simulatore iOS remoto per Windows supporta le interazioni con il tocco, è possibile testare l'app con gli stessi movimenti di tocco, scorrimento e multidito usati con i dispositivi iOS fisici.

Analogamente, il simulatore iOS remoto per Windows considera l'input dello stilo Windows come input penna Apple.

## <a name="sound-handling"></a>Gestione audio

I suoni riprodotti dal simulatore proverranno dagli speaker del Mac host.
i suoni iOS non vengono uditi nel computer Windows.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>Disabilitazione del simulatore iOS remoto per Windows

Per disabilitare il simulatore iOS remoto per Windows, passare a **strumenti > opzioni > novell > impostazioni iOS** e deselezionare il **simulatore remoto per Windows**.

[![casella di controllo per l'utilizzo del simulatore](images/options-sml.png "casella di controllo per l'utilizzo del simulatore")](images/options.png#lightbox)

Se questa opzione è disabilitata, il debug apre il simulatore iOS nell'host di compilazione Mac connesso.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verificano problemi con il simulatore iOS remoto, è possibile visualizzare i log nei percorsi seguenti:

- **Mac** -`~/Library/Logs/Xamarin/Simulator.Server`
- **Windows** : `%LOCALAPPDATA%\Xamarin\Logs\Xamarin.Simulator`

Se si [segnala un problema in Visual Studio](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio), è possibile che i log vengano collegati (sono disponibili opzioni per la conservazione dei caricamenti privati).
