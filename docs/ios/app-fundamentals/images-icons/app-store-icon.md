---
title: Icona App Store
description: Questo articolo viene illustrato come inclusi e la gestione di un asset di immagine in un'app xamarin da utilizzare come icona App Store.
ms.topic: article
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/26/2017
ms.openlocfilehash: 6ec4328f08859c5331a6250bf44ee705a7fd0744
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="app-store-icon"></a>Icona App Store

_Questo articolo viene illustrato come inclusi e la gestione di un asset di immagine in un'app xamarin da utilizzare come icona App Store._

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
