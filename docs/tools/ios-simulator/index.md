---
title: Simulatore iOS remoto per Windows
description: Remoted iOS Simulator per Windows consente di testare le app in un simulatore iOS visualizzato in Windows insieme a Visual Studio 2019.The Remoted iOS Simulator for Windows allows you to test your apps on an iOS simulator displayed in Windows alongside Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: davidortinau
ms.author: daortin
ms.date: 04/26/2019
ms.openlocfilehash: d5898f9c6ee30eb1f12bf6480b93a609e762e6ea
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "75886593"
---
# <a name="remoted-ios-simulator-for-windows"></a>Simulatore iOS remoto per Windows

Remoted iOS Simulator per Windows consente di testare le app in un simulatore iOS visualizzato in Windows insieme a Visual Studio 2019 e Visual Studio 2017.

[![Simulatore iOS in esecuzione su Windows](images/hero-sml.png "Simulatore iOS in esecuzione su Windows")](images/hero.png#lightbox)

## <a name="getting-started"></a>Introduzione

Remoted iOS Simulator per Windows viene installato automaticamente come parte di Xamarin in Visual Studio 2019 e Visual Studio 2017. Per utilizzarlo, attenersi alla seguente procedura:

1. [Associare Visual 2019 a un host di compilazione Mac.](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
2. In Visual Studio avviare il debug di un progetto iOS o tvOS.In Visual Studio, start debugging an iOS or tvOS project. Il simulatore iOS remoto per Windows verrà visualizzato nel computer Windows.

Guarda [questo video](deploy.md) per una guida passo-passo.

## <a name="simulator-window"></a>Finestra simulatore

La barra degli strumenti nella parte superiore della finestra del simulatore contiene una serie di pulsanti utili:

- **Home:** simula il pulsante Home su un dispositivo iOS.
- **Blocca** – Blocca il simulatore (scorri per sbloccare).
- **Screenshot:** consente di salvare uno screenshot del simulatore (memorizzato in Pictures - **Xamarin - IOS Simulator\\**).
- [**Impostazioni**](#settings) – Visualizza tastiera, posizione e altre impostazioni.
- [**Altre opzioni:**](#other-options) visualizza varie opzioni del simulatore, ad esempio rotazione, gesti di agitazione e Touch ID.

    [![Esempio di mappe simulatore iOS](images/maps-app-sml.png "Esempio di mappe simulatore iOS")](images/maps-app.png#lightbox)

## <a name="settings"></a>Impostazioni

Facendo clic sull'icona a forma di ingranaggio della barra degli strumenti si apre la finestra **Impostazioni:**

[![Impostazioni del simulatore iOS](images/settings-sml.png "Impostazioni del simulatore iOS")](images/settings.png#lightbox)

Queste impostazioni consentono di abilitare la tastiera hardware, scegliere un percorso che il dispositivo deve segnalare (le posizioni statiche e in movimento sono entrambe supportate), abilitare Touch ID e reimpostare il contenuto e le impostazioni per il simulatore.

## <a name="other-options"></a>Altre opzioni

Il pulsante con i ellissi della barra degli strumenti rivela altre opzioni, ad esempio la rotazione, i gesti di agitazione e il riavvio. Queste stesse opzioni possono essere visualizzate come elenco facendo clic con il pulsante destro del mouse in un punto qualsiasi della finestra del simulatore:

[![Impostazioni aggiuntive del simulatore iOS](images/more-sml.png "Impostazioni aggiuntive del simulatore iOS")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>Supporto per touchscreen

La maggior parte dei computer Windows moderni dispone di touch screen. Poiché Remoted iOS Simulator per Windows supporta le interazioni tramite tocco, puoi testare l'app con gli stessi movimenti di tocco con pizzicate, scorrimento rapido e con più dita che usi con i dispositivi iOS fisici.

Analogamente, remoted iOS Simulator per Windows considera l'input di Windows Stylus come input Apple Pencil.

## <a name="sound-handling"></a>Gestione del suono

I suoni riprodotti dal simulatore provengono dagli altoparlanti del Mac dell'host.
I suoni iOS non vengono ascoltati sul computer Windows.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>Disabilitazione del simulatore iOS remoto per Windows

Per disabilitare Remoted iOS Simulator per Windows, passare a **Strumenti > Opzioni > Xamarin > Impostazioni iOS** e deselezionare Remote Simulator di **Windows**.

[![casella di controllo per utilizzare simulatore](images/options-sml.png "casella di controllo per utilizzare simulatore")](images/options.png#lightbox)

Con questa opzione disabilitata, il debug apre il simulatore iOS sull'host di compilazione Mac connesso.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verificano problemi con Remoted iOS Simulator, è possibile visualizzare i log nelle posizioni seguenti:If you issues with the Remoted iOS Simulator, you can view the logs in these locations:

- **Mac** –`~/Library/Logs/Xamarin/Simulator.Server`
- **Windows** –`%LOCALAPPDATA%\Xamarin\Logs\Xamarin.Simulator`

Se si [segnala un problema in Visual Studio](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio), il collegamento di questi log potrebbe essere utile (sono disponibili opzioni per mantenere privati i caricamenti).
