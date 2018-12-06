---
title: Simulatore iOS remoto per Windows
description: Il simulatore di Windows di iOS remoto consente di testare le App in un simulatore di iOS zobrazené Windows insieme a Visual Studio 2017.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: lobrien
ms.author: laobri
ms.date: 11/14/2018
ms.openlocfilehash: 4a45d4fb7aaad504c7bb5628f1e521a39463b06f
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899070"
---
# <a name="remoted-ios-simulator-for-windows"></a>Simulatore iOS remoto per Windows

Il simulatore di Windows di iOS remoto consente di testare le App in un simulatore di iOS zobrazené Windows insieme a Visual Studio 2017.

[![](images/hero-sml.png "Simulatore iOS in esecuzione su Windows")](images/hero.png#lightbox)

## <a name="getting-started"></a>Per iniziare

Il simulatore iOS remoto per Windows viene installato automaticamente come parte di Xamarin in Visual Studio 2017. Per usarlo, seguire questa procedura:

1. [Coppia di Visual Studio 2017 in un host di compilazione Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. In Visual Studio 2017, avviare il debug di un progetto iOS o tvOS. Il simulatore iOS remoto per Windows verrà visualizzato nel computer Windows.

Guarda [questo video](deploy.md) per una Guida dettagliata.

## <a name="simulator-window"></a>Finestra del simulatore

Barra degli strumenti nella parte superiore della finestra del simulatore contiene un numero di pulsanti utili:

- **Home** – simula il pulsante home in un dispositivo iOS.
- **Blocco** : blocca l'applicazione client simulator (scorrere rapidamente per sbloccare).
- **Screenshot** – consente di salvare uno screenshot del simulatore (archiviato in **simulatore Pictures\Xamarin\iOS\\**).
- [**Le impostazioni** ](#settings) – Visualizza della tastiera, la posizione e altre impostazioni.
- [**Altre opzioni** ](#other-options) : consente di visualizzare diverse opzioni di simulatore, ad esempio la rotazione, i movimenti muovendosi e ID tocco.

    [![](images/maps-app-sml.png "simulatore iOS esegue il mapping di esempio")](images/maps-app.png#lightbox)

## <a name="settings"></a>Impostazioni

Facendo clic sull'icona a forma di ingranaggio della barra degli strumenti consente di aprire la **impostazioni** finestra:

[![](images/settings-sml.png "impostazioni di iOS simulator")](images/settings.png#lightbox)

Queste impostazioni consentono di abilitare la tastiera hardware, scegliere un percorso a cui il dispositivo deve report (statiche e lo spostamento le posizioni sono entrambi supportate), abilitare Touch ID e reimpostare il contenuto e le impostazioni per il simulatore.

## <a name="other-options"></a>Altre opzioni

Pulsante puntini di sospensione della barra degli strumenti vengono visualizzate altre opzioni, ad esempio la rotazione, i movimenti muovendosi e il riavvio. Queste opzioni stesso possono essere considerate come un elenco facendo clic su un punto qualsiasi nella finestra del simulatore:

[![](images/more-sml.png "ulteriori impostazioni di iOS simulator")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>Supporto touchscreen

I computer Windows più recenti hanno touch screen. Poiché il simulatore di Windows di iOS remoto supporta le interazioni tramite tocco, è possibile testare l'app con la stessa con avvicinamento delle dita, scorrere rapidamente e i movimenti di tocco con un multi-dito utilizzabili con i dispositivi iOS fisici.

Allo stesso modo, il simulatore di Windows di iOS remoto vengono considerati input dello stilo Windows come input a forma di matita di Apple.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>La disabilitazione di iOS remoto simulatore per Windows

Per disabilitare il simulatore di Windows di iOS remoto, passare a **strumenti > Opzioni > Xamarin > Impostazioni iOS** e deselezionare **simulatore remoto per Windows**.

[![](images/options-sml.png "casella di controllo per usare simulatore")](images/options.png#lightbox)

Questa opzione è disabilitata, il debug viene visualizzata dal simulatore iOS su Mac connesso host di compilazione.
