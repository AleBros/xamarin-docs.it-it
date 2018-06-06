---
title: Icone dell'archivio di App in xamarin. IOS
description: Questo documento viene descritto come usare i cataloghi di asset per gestire un'icona di App Store per un'applicazione di xamarin. IOS. Icone dell'archivio di App in precedenza, veniva gestite con iTunes Connect.
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/26/2017
ms.openlocfilehash: 749dbf01af382a54fe24652706f6a605ac7b20b4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783610"
---
# <a name="app-store-icons-in-xamarinios"></a>Icone dell'archivio di App in xamarin. IOS

Prima di Xcode 9 tutte le icone di App Store sono stati aggiunti tramite iTunes Connect. Tuttavia, ciò non è più il caso. Icone dell'archivio di App devono ora essere inclusi come parte del bundle del progetto e aggiunta all'interno di un catalogo di asset. Le applicazioni che non contengono un'icona di App Store verranno rifiutate da Apple.

L'icona di archivio di App è il tipo di carattere dell'applicazione a utenti, pertanto devono essere facili da ricordare e visualizzato correttamente di piccole dimensioni. Sono facilmente ricordabili le icone basiche, semplici e immediatamente riconoscibili.

Per la progettazione dell'icona di un'applicazione, attenersi alle linee guida seguenti suggerite da Apple:

- Creare un'icona appropriata per l'applicazione.
- Creare un'icona semplice che sia in linea con la progettazione dell'applicazione.
- Non usare parole all'interno dell'icona.
- Pensare a livello globale: un'unica icona dell'app in tutti i territori dello Store.

Per un'icona dell'app che sarà visualizzata nell'App Store, è necessaria un'immagine di 1024 x 1024 pixel.  Secondo le istruzioni di Apple l'icona per l'App Store nel catalogo asset non deve essere trasparente e non deve contenere un canale alfa.

Per ulteriori informazioni, vedere Apple [iOS linee guida dell'interfaccia umana](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/image-size-and-resolution/).

## <a name="adding-an-app-store-icon"></a>Aggiunta di un'icona di App Store

Le icone per l'App Store devono raggruppate in un catalogo asset. 

Per aggiungere un'icona di App Store eseguire le operazioni seguenti:

1. Individuare il **AppIcon** impostata immagine di **Assets.xcassets** file del progetto. 
    - Tutti i nuovi progetti devono disporre di un un **Assets.xcassets** file che contiene un set di immagini AppIcon.
    - Per aggiungere un nuovo catalogo di asset, fare clic sul progetto e selezionare **Aggiungi > Nuovo > catalogo di Asset**.
    - Per aggiungere un nuovo set di immagini icona un'app, fare doppio clic nell'area di set di icone di selezionare **icone App & immagini di avvio > nuova icona App**:
    
    ![Aggiungere una nuova opzione di set di immagini](app-store-icon-images/image1.png)

2. Scorrere verso il **App Store** icona dell'elenco:

    ![Icona App Store](app-store-icon-images/image2.png)

3. Fare clic sull'icona e selezionare l'immagine di 1024 x 1024 pixel. Salvare il catalogo di Asset.




## <a name="related-links"></a>Collegamenti correlati

- [La gestione delle icone con i cataloghi Asset](~/ios/app-fundamentals/images-icons/app-icons.md#managing)
