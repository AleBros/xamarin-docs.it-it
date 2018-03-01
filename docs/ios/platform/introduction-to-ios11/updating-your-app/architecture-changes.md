---
title: Modifiche all'architettura
description: "Esplorare le nuove funzionalità di iOS 11"
ms.topic: article
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 0c4b0571d9254edba15177d5f98fd6718f1c6bcb
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="architecture-changes"></a>Modifiche all'architettura

_Esplorare le nuove funzionalità di iOS 11_

Una delle principali modifiche che è necessario essere consapevoli di con iOS 11 è della deprecazione del supporto a 32 bit per le app, come descritto in dettaglio nella [Apple](https://developer.apple.com/news/?id=06282017b) comunicato. Tutte le nuove app e aggiornamenti alle App esistenti devono supportare a 64 bit. applicazioni a 32 bit **non verranno avviate** in iOS 11.

Per aggiornare l'app in Visual Studio per Mac, usare la procedura seguente:

1. Fare doppio clic sul nome del progetto per aprire **opzioni progetto**.
2. Individuare il **compilazione iOS** scheda.
3. Impostare il **architetture supportate** elenco a discesa per **x86_64** per il **Debug | iPhoneSimulator** e **versione | iPhoneSimulator**:

    ![Modificare le architetture simulatore elenco a discesa](architecture-changes-images/image1.png)

4. Per i dispositivi iOS, modificare la configurazione su **Debug | iPhone** e impostare il **architetture supportate** elenco a discesa per **ARM64**:

    ![Modificare l'elenco a discesa le architetture di dispositivo](architecture-changes-images/image2.png)

5. Modificare la configurazione su **versione | iPhone** e impostare il **architetture supportate** elenco a discesa per **ARM64**.

Per ulteriori informazioni su architetture a 32 bit e 64 bit, vedere il [considerazioni relative alla piattaforma a 32/64 bit](~/cross-platform/macios/32-and-64.md#ios) Guida.

## <a name="related-links"></a>Collegamenti correlati

- [Novità in iOS 11 (mela)](https://developer.apple.com/ios/)
- [Pagina del prodotto aggiornato App Store (mela)](https://developer.apple.com/app-store/product-page/)
- [L'aggiornamento dell'App per iOS 11 (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/204/)
