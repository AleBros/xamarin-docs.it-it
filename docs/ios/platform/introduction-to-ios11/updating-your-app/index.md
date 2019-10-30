---
title: Aggiornamento dell'app a iOS 11
description: Questo documento contiene collegamenti a diverse guide che descrivono le nuove funzionalità disponibili per gli sviluppatori Novell. iOS con la versione di iOS 11. Ad esempio, aggiornamenti della progettazione visiva, modifiche dell'app Store e aggiornamenti delle icone delle app.
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 81d863b12aa7748acc6876929fb5d6361a20b507
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032125"
---
# <a name="updating-your-app-to-ios-11"></a>Aggiornamento dell'app a iOS 11

In iOS 11 Apple ha introdotto aggiornamenti dell'architettura, nuove modifiche visive e un processo aggiornato di iTunes Connect. Questa guida esamina ognuna di queste modifiche per consentire l'aggiornamento dell'app Novell. iOS per iOS 11.

## <a name="architecture-changesarchitecture-changesmd"></a>[Modifiche all'architettura](architecture-changes.md)

Una delle modifiche più importanti che è necessario conoscere con iOS 11 è la deprecazione del supporto a 32 bit per le app, come descritto in dettaglio in [Apple](https://developer.apple.com/news/?id=06282017b) Press Release.

Questa guida illustra l'aggiornamento dell'app per 64 bit.

## <a name="visual-design-updatesvisual-designmd"></a>[Aggiornamenti della progettazione visiva](visual-design.md)

In iOS 11 Apple ha introdotto nuove modifiche visive, inclusi gli aggiornamenti alla barra di spostamento, alla barra di ricerca e alle visualizzazioni di tabella. Sono stati inoltre apportati miglioramenti per consentire una maggiore flessibilità rispetto ai margini e al contenuto a schermo intero. Queste modifiche sono descritte in questa guida.

## <a name="app-store-changesapp-store-changesmd"></a>[Modifiche di App Store](app-store-changes.md)

IOS App Store ha avuto una riprogettazione completa, che non solo consente agli utenti di spostarsi in modo efficiente nello Store, ma consente anche agli sviluppatori di promuovere l'app agli utenti. Queste promozioni includono aggiornamenti per gli acquisti in-app e gli aggiornamenti della pagina del prodotto. iOS 11 aggiunge anche aggiornamenti per la comunicazione con gli utenti, come aggiungere l'icona dell'app e come rilasciare l'app al pubblico.

## <a name="app-icon-updates"></a>Aggiornamenti dell'icona dell'app

> [!NOTE]
> Le icone dell'app dovrebbero ora essere fornite da un _Catalogo asset_. 

Per informazioni sull'uso di cataloghi asset, vedere la guida dell' [icona dell'app Store](~/ios/app-fundamentals/images-icons/app-store-icon.md) . Per informazioni sulla migrazione delle icone da un file INFO. plist a un catalogo asset, vedere la pagina relativa alla [migrazione da info. plist a cataloghi asset](~/ios/app-fundamentals/images-icons/app-icons.md) .

L'icona obbligatoria nel catalogo asset è denominata **App Store** e deve avere una dimensione di 1024 x 1024. Secondo le istruzioni di Apple l'icona per l'App Store nel catalogo asset non deve essere trasparente e non deve contenere un canale alfa.

![Posizione dell'icona dell'app Store nel catalogo asset.](images/image1.png)

## <a name="related-links"></a>Collegamenti correlati

- [Novità di iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Pagina del prodotto App Store aggiornata (Apple)](https://developer.apple.com/app-store/product-page/)
- [Aggiornamento dell'app per iOS 11 (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/204/)
