---
title: Modifiche all'architettura in iOS 11
description: Questo documento descrive la deprecazione delle App a 32 bit in iOS 11. Illustra come aggiornare le applicazioni per le architetture a 64 bit di destinazione.
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: b7d1df6ed2a8bd480025681ebcbe48acd7592564
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102270"
---
# <a name="architecture-changes-in-ios-11"></a>Modifiche all'architettura in iOS 11

Una delle principali modifiche che è necessario essere consapevoli di IOS 11 è la rimozione del supporto di 32 bit per le app, come descritto nei [Apple](https://developer.apple.com/news/?id=06282017b) comunicati stampa. Tutte le nuove applicazioni e aggiornamenti per le app esistenti devono supportare a 64 bit. le app a 32 **non verranno avviate** in iOS 11.

Per aggiornare l'app in Visual Studio per Mac, usare la procedura seguente:

1. Fare doppio clic sul nome del progetto aprire **opzioni progetto**.
2. Selezionare il **compilazione iOS** scheda.
3. Impostare il **architetture supportate** elenco a discesa per **x86_64** per il **Debug | iPhoneSimulator** e **versione | iPhoneSimulator**:

    ![Modificare l'elenco a discesa architetture del simulatore](architecture-changes-images/image1.png)

4. Per i dispositivi iOS, modificare la configurazione su **Debug | iPhone** e impostare le **architetture supportate** elenco a discesa per **ARM64**:

    ![Modificare l'elenco a discesa architetture di dispositivo](architecture-changes-images/image2.png)

5. Modificare la configurazione su **versione | iPhone** e impostare le **architetture supportate** elenco a discesa per **ARM64**.

Per altre informazioni su architetture a 32 bit e 64 bit, vedere la [considerazioni sulle piattaforme a 32 o 64 bit](~/cross-platform/macios/32-and-64/index.md#ios) Guida.

## <a name="related-links"></a>Collegamenti correlati

- [What ' s New in iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Pagina del prodotto aggiornato App Store (Apple)](https://developer.apple.com/app-store/product-page/)
- [Aggiornamento dell'App per iOS 11 (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/204/)
