---
title: Remoti simulatore iOS (per Windows)
description: Test e debug di App iOS completamente all'interno di Visual Studio in Windows
ms.topic: article
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 04/07/2017
ms.openlocfilehash: 707ba5874c939fbd25f4e25a7cefd3dc5fc75131
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="remoted-ios-simulator-for-windows"></a>Remoti simulatore iOS (per Windows)

_Test e debug di App iOS completamente all'interno di Visual Studio in Windows_

[ ![](ios-simulator-images/hero-sml.png "Simulatore iOS in esecuzione in Windows")](ios-simulator-images/hero.png)

## <a name="download-and-install"></a>Scaricare e installare

Scaricare il [installer](https://dl.xamarin.com/xamarin-simulator/Xamarin.Simulator.Installer.msi) e installare nel computer Windows. Deve essere già installato Visual Studio Tools per Xamarin.

> [!NOTE]
> Usare un iOS remoto simulatore richiede Visual Studio un Mac connesso alla rete con Xamarin installati.

## <a name="getting-started"></a>Introduzione

Per usare il simulatore iOS remota:

1. Assicurarsi che Visual Studio sia connesso al Mac almeno una volta prima di avviare il simulatore di iOS remoto.
2. Verificare che sia un'app iOS o tvOS il **progetto di avvio** e avviare il debug.

È possibile disabilitare il simulatore iOS remota da **strumenti > Opzioni > Xamarin > Impostazioni iOS** deselezionando la casella per **simulatore remoto a Windows** illustrato di seguito:

[ ![](ios-simulator-images/options-sml.png "casella di controllo per utilizzare simulatore")](ios-simulator-images/options.png)

Il simulatore iOS verrà quindi aperto nel computer connesso a Mac. Selezionare questa opzione per attivare il simulatore iOS remota.

## <a name="features"></a>Funzionalità

Il simulatore di iOS remoto fornisce un modo per test e debug di App iOS nel simulatore interamente da Visual Studio in Windows.

### <a name="simulator-window"></a>Finestra del simulatore

Barra degli strumenti finestra include una serie di pulsanti per interagire con il simulatore:

- **Home** – simula il pulsante principale del dispositivo.
- **Blocco** – blocca il simulatore (è possibile scorrere rapidamente per sbloccare).
- **Schermata di** – una schermata del simulatore viene salvato su disco.
- [**Impostazioni** ](#settings) : configurare la tastiera e il percorso.
 - Altri [ **opzioni** ](#options) : sono disponibile, ad esempio agitazione ruota, un'ampia gamma di opzioni simulatore o richiamare altri stati nel simulatore. Quando alcune opzioni sono nascosti, sono accessibili dall'icona di puntini di sospensione che viene visualizzata nella barra degli strumenti oppure facendo clic sulla finestra.

    [ ![](ios-simulator-images/maps-app-sml.png "simulatore iOS esegue il mapping di esempio")](ios-simulator-images/maps-app.png)


### <a name="settings"></a>Impostazioni

Verrà visualizzata l'icona "ingranaggio" il **impostazioni** finestra:

[ ![](ios-simulator-images/settings-sml.png "impostazioni di simulatore iOS")](ios-simulator-images/settings.png)

Ciò consente di abilitare la tastiera hardware nel simulatore e scegliere il percorso in cui viene segnalato al dispositivo (incluso un percorso statico o altre opzioni di posizione mobile).



### <a name="other-options"></a>Altre opzioni

Fare doppio clic in un punto qualsiasi nella finestra del simulatore per visualizzare tutte le opzioni disponibili nel simulatore, ad esempio la rotazione, l'attivazione di un movimento di agitazione e riavviare il simulatore:

[ ![](ios-simulator-images/more-sml.png "impostazioni aggiuntive di simulatore iOS")](ios-simulator-images/more.png)

### <a name="touchscreen-support"></a>Supporto touchscreen

I computer Windows più recenti hanno schermi di tocco e il simulatore iOS remota consente di toccare la finestra del simulatore per testare le interazioni dell'utente in app iOS.

Questo include compressione, passaggio e i movimenti tocco di un dito più - cose che in precedenza può solo essere facilmente testate su dispositivi fisici.

Supporto dello stilo in Windows viene convertito anche input Apple matita nel simulatore.

<!--
<a name="knownissues" />

# Known Issues

 - Apple Watch devices may show in the Visual Studio device list, but are not yet supported.
 - Launching in **Release** mode may also start Apple’s simulator on the networked Mac.
 - Closing the remote iOS Simulator on Windows will not immediately stop debugging in Visual Studio. Stop debugging manually from the menu or the red button.
 - Opening too many different simulators simultaneously will produce unexpected results.
 - Exception of type `Foundation.NSErrorException` may be thrown while launching Simulators. Workaround is to kill csproxy (server process) on the Mac host and re-deploy to the simulator.
 - Performance may be slower when using Xcode 8
-->
