---
title: Simulatore iOS remoto per Windows
description: IOS remoto simulatore per Windows consente di testare le app nel simulatore di iOS visualizzate nelle finestre insieme a Visual Studio 2017.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: topgenorth
ms.author: toopge
ms.date: 05/11/2018
ms.openlocfilehash: b07cc24e63f4aa3ce4451e3bdb5819f1df1058c6
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
ms.locfileid: "34149807"
---
# <a name="remoted-ios-simulator-for-windows"></a>Simulatore iOS remoto per Windows

IOS remoto simulatore per Windows consente di testare le app nel simulatore di iOS visualizzate nelle finestre insieme a Visual Studio 2017.

[![](ios-simulator-images/hero-sml.png "Simulatore iOS in esecuzione in Windows")](ios-simulator-images/hero.png#lightbox)

## <a name="getting-started"></a>Per iniziare

Simulatore iOS remoto per Windows viene installato automaticamente come parte di Xamarin in Visual Studio 2017. Per utilizzarlo, seguire questi passaggi:

1. [Coppia Visual 2017 a un host di compilazione Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. In Visual Studio 2017, avviare il debug di un progetto iOS o tvOS. Simulatore iOS remoto per Windows verrà visualizzato nel computer Windows.

## <a name="simulator-window"></a>Finestra del simulatore

Barra degli strumenti nella parte superiore della finestra del simulatore contiene un numero di pulsanti utili:

- **Home** – simula il pulsante home in un dispositivo iOS
- **Blocco** – blocca il simulatore (scorrere rapidamente per sbloccare)
- **Schermata di** – Salva uno screenshot del simulatore
- [**Le impostazioni** ](#settings) – Visualizza tastiera, percorso e altre impostazioni
- [**Altre opzioni** ](#other-options) – consente di visualizzare varie opzioni simulatore, ad esempio i movimenti di rotazione e agitare

    [![](ios-simulator-images/maps-app-sml.png "simulatore iOS esegue il mapping di esempio")](ios-simulator-images/maps-app.png#lightbox)

## <a name="settings"></a>Impostazioni

Facendo clic sull'icona a forma di ingranaggio della barra degli strumenti consente di aprire la **impostazioni** finestra:

[![](ios-simulator-images/settings-sml.png "impostazioni di simulatore iOS")](ios-simulator-images/settings.png#lightbox)

Queste impostazioni consentono di abilitare la tastiera hardware, scegliere un percorso che deve essere il dispositivo da report (indirizzi statici e mobile sono entrambi supportati), abilitare l'ID tocco e reimpostare il contenuto e le impostazioni per il simulatore.

## <a name="other-options"></a>Altre opzioni

Pulsante puntini di sospensione della barra degli strumenti consente di visualizzare altre opzioni, ad esempio la rotazione, i movimenti di agitazione e riavviando il computer. Queste opzioni stesso possono essere considerate come un elenco facendo clic su un punto qualsiasi della finestra del simulatore:

[![](ios-simulator-images/more-sml.png "impostazioni aggiuntive di simulatore iOS")](ios-simulator-images/more.png#lightbox)

## <a name="touchscreen-support"></a>Supporto touchscreen

I computer Windows più recenti dispongono touch screen. Poiché iOS remoto simulatore per Windows supporta le interazioni tocco, è possibile testare l'app con la stessa zoom indietro, scorrere rapidamente e i movimenti tocco con un multi-dito utilizzati con i dispositivi iOS fisici.

Analogamente, iOS remoto simulatore per Windows considera input con stilo Windows come input a forma di matita Apple.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>La disabilitazione di iOS remoto simulatore per Windows

Per disabilitare iOS remoto simulatore per Windows, passare a **strumenti > Opzioni > Xamarin > Impostazioni di iOS** e deselezionare **simulatore remoto a Windows**.

[![](ios-simulator-images/options-sml.png "casella di controllo per utilizzare simulatore")](ios-simulator-images/options.png#lightbox)

Questa opzione è disabilitata, il debug viene visualizzata la simulatore iOS su Mac connesso creare dell'host.
