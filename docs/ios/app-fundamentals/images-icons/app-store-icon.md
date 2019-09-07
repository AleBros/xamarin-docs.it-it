---
title: Icone di App Store in Novell. iOS
description: Questo documento descrive come usare i cataloghi asset per gestire un'icona dell'app Store per un'applicazione Novell. iOS. In precedenza, le icone dell'app Store venivano gestite con iTunes Connect.
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/26/2017
ms.openlocfilehash: e0b899e69405874f381c3ab6f9a9200a856d7402
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767302"
---
# <a name="app-store-icons-in-xamarinios"></a>Icone di App Store in Novell. iOS

Prima di Xcode 9 tutte le icone dell'app Store sono state aggiunte tramite iTunes Connect. Tuttavia, questo non è più il caso. Le icone dell'app Store devono ora essere incluse come parte del bundle di progetto e aggiunte all'interno di un catalogo asset. Le app che non contengono un'icona dell'app Store verranno rifiutate da Apple.

L'icona dell'app Store è la faccia dell'applicazione per gli utenti, quindi deve essere memorabile e visualizzare bene a piccole dimensioni. Sono facilmente ricordabili le icone basiche, semplici e immediatamente riconoscibili.

Per la progettazione dell'icona di un'applicazione, attenersi alle linee guida seguenti suggerite da Apple:

- Creare un'icona appropriata per l'applicazione.
- Creare un'icona semplice che sia in linea con la progettazione dell'applicazione.
- Non usare parole all'interno dell'icona.
- Considerare un pubblico globale: un'unica icona dell'app viene usata in tutti i territori dello Store.

Per un'icona dell'app che sarà visualizzata nell'App Store, è necessaria un'immagine di 1024 x 1024 pixel.  Secondo le istruzioni di Apple l'icona per l'App Store nel catalogo asset non deve essere trasparente e non deve contenere un canale alfa.

Per altre informazioni, vedere le [linee guida sull'interfaccia umana iOS](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/image-size-and-resolution/)di Apple.

## <a name="adding-an-app-store-icon"></a>Aggiunta di un'icona dell'app Store

Le icone per l'App Store devono raggruppate in un catalogo asset. 

Per aggiungere un'icona dell'app Store, procedere come segue:

1. Individuare l'immagine **AppIcon** impostata nel file **assets. xcassets** del progetto. 
    - Tutti i nuovi progetti devono essere dotati di un file **assets. xcassets** che contiene un set di immagini AppIcon.
    - Per aggiungere un nuovo catalogo asset, fare clic con il pulsante destro del mouse sul progetto e scegliere **aggiungi > nuovo File > Catalogo asset**.
    - Per aggiungere un nuovo set di immagini icona dell'app, fare clic con il pulsante destro del mouse nell'area del set di icone e selezionare **icone app & avviare immagini > nuova icona dell'app**:

    ![Opzione Aggiungi nuovo set di immagini](app-store-icon-images/image1.png)

2. Scorrere fino all'icona dell' **App Store** nell'elenco:

    ![Icona di App Store](app-store-icon-images/image2.png)

3. Fare clic sull'icona e cercare l'immagine del pixel 1024 x 1024. Salvare il catalogo asset.

## <a name="related-links"></a>Collegamenti correlati

- [Gestione delle icone con i cataloghi asset](~/ios/app-fundamentals/images-icons/app-icons.md#managing)
