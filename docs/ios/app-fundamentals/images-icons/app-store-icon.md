---
title: Icone di App Store in xamarin. IOS
description: Questo documento descrive come usare cataloghi asset per gestire un'icona di App Store per un'applicazione xamarin. IOS. In precedenza, venivano gestite le icone di App Store di iTunes Connect.
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/26/2017
ms.openlocfilehash: 53e25ae9f4650254f2aaaa03dc8727fae674c9f0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258091"
---
# <a name="app-store-icons-in-xamarinios"></a>Icone di App Store in xamarin. IOS

Prima di Xcode 9 tramite iTunes Connect sono state aggiunte tutte le icone di App Store. Tuttavia, ciò non è più il case. Le icone di App Store devono ora essere incluso come parte del bundle del progetto e aggiunta all'interno di un catalogo asset. Apple rifiuterà le app che non contengono un'icona di App Store.

L'icona dell'App Store è il volto dell'applicazione a utenti, in modo che deve essere facile da ricordare e ben visualizzabile in una dimensione piccola. Sono facilmente ricordabili le icone basiche, semplici e immediatamente riconoscibili.

Per la progettazione dell'icona di un'applicazione, attenersi alle linee guida seguenti suggerite da Apple:

- Creare un'icona appropriata per l'applicazione.
- Creare un'icona semplice che sia in linea con la progettazione dell'applicazione.
- Non usare parole all'interno dell'icona.
- Considerare un pubblico globale: un'unica icona dell'app viene usata in tutti i territori dello Store.

Per un'icona dell'app che sarà visualizzata nell'App Store, è necessaria un'immagine di 1024 x 1024 pixel.  Secondo le istruzioni di Apple l'icona per l'App Store nel catalogo asset non deve essere trasparente e non deve contenere un canale alfa.

Per altre informazioni, vedere di Apple [iOS Human Interface Guidelines](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/image-size-and-resolution/).

## <a name="adding-an-app-store-icon"></a>Aggiunta di un'icona di App Store

Le icone per l'App Store devono raggruppate in un catalogo asset. 

Per aggiungere un'icona di App Store eseguire le operazioni seguenti:

1. Individuare il **AppIcon** impostata immagine il **assets. xcassets** file del progetto. 
    - Dovrebbero essere dotati di tutti i nuovi progetti un un' **assets. xcassets** file che contiene un set di immagini AppIcon.
    - Per aggiungere un nuovo catalogo asset, pulsante destro del mouse sul progetto, quindi scegliere **Aggiungi > Nuovo File > catalogo di Asset**.
    - Per aggiungere un nuovo un set di immagini icona di app, fare doppio clic nell'area di set di icone e selezionare **icone delle App e immagini di avvio > nuova icona App**:
    
    ![Aggiungi nuova opzione di set di immagini](app-store-icon-images/image1.png)

2. Scorrere fino al **App Store** icona nell'elenco:

    ![Icona di App Store](app-store-icon-images/image2.png)

3. Fare clic sull'icona e cercare l'immagine di 1024 x 1024 pixel. Salvare il catalogo di Asset.




## <a name="related-links"></a>Collegamenti correlati

- [La gestione delle icone con cataloghi Asset](~/ios/app-fundamentals/images-icons/app-icons.md#managing)
