---
title: Modifiche all'architettura in iOS 11
description: Questo documento descrive la deprecazione delle app a 32 bit in iOS 11. Viene illustrato come aggiornare le applicazioni per le architetture a 64 bit di destinazione.
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: 15cd6139cc83639146e6044d2b791d94ee30f2d9
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286356"
---
# <a name="architecture-changes-in-ios-11"></a>Modifiche all'architettura in iOS 11

Una delle modifiche più importanti che è necessario conoscere con iOS 11 è la deprecazione del supporto a 32 bit per le app, come descritto in dettaglio in [Apple](https://developer.apple.com/news/?id=06282017b) Press Release. Tutte le nuove app e gli aggiornamenti per le app esistenti devono supportare 64 bit. le app a 32 bit **non vengono avviate** in iOS 11.

Per aggiornare l'app in Visual Studio per Mac, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul nome del progetto per aprire **Opzioni progetto**.
2. Passare alla scheda **compilazione iOS** .
3. Impostare l'elenco a discesa **architetture supportate** su **x86_64** per **debug | iPhoneSimulator** e **Release | iPhoneSimulator**:

    ![Modificare l'elenco a discesa architetture simulatori](architecture-changes-images/image1.png)

4. Per i dispositivi iOS, modificare la configurazione in **debug | iPhone** e impostare l'elenco a discesa **architetture supportate** su **arm64**:

    ![Modificare l'elenco a discesa architetture dispositivi](architecture-changes-images/image2.png)

5. Modificare la configurazione in **release | iPhone** e impostare l'elenco a discesa **architetture supportate** su **arm64**.

Per ulteriori informazioni sulle architetture a 32 bit e a 64 bit, vedere la Guida alle [considerazioni sulla piattaforma di bit 32/64](~/cross-platform/macios/32-and-64/index.md#ios) .

## <a name="related-links"></a>Collegamenti correlati

- [Novità di iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Pagina del prodotto App Store aggiornata (Apple)](https://developer.apple.com/app-store/product-page/)
- [Aggiornamento dell'app per iOS 11 (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/204/)
