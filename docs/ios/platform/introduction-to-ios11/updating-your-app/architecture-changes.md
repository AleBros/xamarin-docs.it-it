---
title: Modifiche all'architettura in iOS 11
description: Questo documento descrive la deprecazione delle App a 32 bit in iOS 11. Illustra come aggiornare le applicazioni alle architetture a 64 bit di destinazione.
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 09d528ceb0654debd7c0ac8818f19c622775eac2
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787435"
---
# <a name="architecture-changes-in-ios-11"></a>Modifiche all'architettura in iOS 11

Una delle principali modifiche che è necessario essere consapevoli di con iOS 11 è della deprecazione del supporto a 32 bit per le app, come descritto in dettaglio nella [Apple](https://developer.apple.com/news/?id=06282017b) comunicato. Tutte le nuove app e aggiornamenti alle App esistenti devono supportare a 64 bit. applicazioni a 32 bit **non verranno avviate** in iOS 11.

Per aggiornare l'app in Visual Studio per Mac, usare la procedura seguente:

1. Fare doppio clic sul nome del progetto per aprire **opzioni progetto**.
2. Individuare il **compilazione iOS** scheda.
3. Impostare il **architetture supportate** elenco a discesa per **x86_64** per il **Debug | iPhoneSimulator** e **versione | iPhoneSimulator**:

    ![Modificare le architetture simulatore elenco a discesa](architecture-changes-images/image1.png)

4. Per i dispositivi iOS, modificare la configurazione su **Debug | iPhone** e impostare il **architetture supportate** elenco a discesa per **ARM64**:

    ![Modificare l'elenco a discesa le architetture di dispositivo](architecture-changes-images/image2.png)

5. Modificare la configurazione su **versione | iPhone** e impostare il **architetture supportate** elenco a discesa per **ARM64**.

Per ulteriori informazioni su architetture a 32 bit e 64 bit, vedere il [considerazioni relative alla piattaforma a 32/64 bit](~/cross-platform/macios/32-and-64/index.md#ios) Guida.

## <a name="related-links"></a>Collegamenti correlati

- [Novità in iOS 11 (mela)](https://developer.apple.com/ios/)
- [Pagina del prodotto aggiornato App Store (mela)](https://developer.apple.com/app-store/product-page/)
- [L'aggiornamento dell'App per iOS 11 (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/204/)
