---
title: Aggiornamento dell'App per iOS 11
description: Questo documento include collegamenti alle varie guide che descrivono le nuove funzionalità disponibili per gli sviluppatori di xamarin. IOS con la versione di iOS 11. Ad esempio, gli aggiornamenti di progettazione visiva, App Store cambia, e Aggiorna icona dell'App.
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: 3193f27c30080a4335abfe969acb3c8b33516469
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110778"
---
# <a name="updating-your-app-to-ios-11"></a>Aggiornamento dell'App per iOS 11

In iOS 11, Apple ha introdotto architettura aggiornamenti, nuove modifiche visive e un processo di aggiornato iTunes Connect. Questa guida illustra ognuna di queste modifiche, ciò aiuta a ottenere le app xamarin. IOS aggiornata per iOS 11.

## <a name="architecture-changesarchitecture-changesmd"></a>[Modifiche all'architettura](architecture-changes.md)

Una delle principali modifiche che è necessario essere consapevoli di IOS 11 è la rimozione del supporto di 32 bit per le app, come descritto nei [Apple](https://developer.apple.com/news/?id=06282017b) comunicati stampa.

Questa guida illustra l'aggiornamento dell'app per 64 bit.

## <a name="visual-design-updatesvisual-designmd"></a>[Aggiornamenti della progettazione visiva](visual-design.md)

In iOS 11, Apple ha introdotto nuove modifiche visive tra cui gli aggiornamenti per la barra di spostamento, barra di ricerca e le visualizzazioni di tabelle. Inoltre, sono stati apportati miglioramenti per consentire una maggiore flessibilità su margini e il contenuto a schermo intero. Queste modifiche sono descritte in questa Guida.

## <a name="app-store-changesapp-store-changesmd"></a>[Modifiche di App Store](app-store-changes.md)

L'App Store iOS ha avuto una riprogettazione completa, che non solo consente agli utenti di passare in modo efficiente l'archivio, ma consente inoltre, gli sviluppatori per promuovere l'app agli utenti. Le promozioni includono gli aggiornamenti per gli acquisti in-app e aggiornamenti per la pagina del prodotto. iOS 11 aggiunge anche gli aggiornamenti relativi come comunicare con gli utenti, come aggiungere all'icona dell'app e come rilasciare l'app al pubblico.

## <a name="app-icon-updates"></a>Aggiorna icona App

> [!NOTE]
> Le icone dell'App devono ora essere distribuite da un _catalogo di Asset_. 

Per informazioni sull'uso di cataloghi asset consultare il [icona dell'App Store](~/ios/app-fundamentals/images-icons/app-store-icon.md) Guida. Per assistenza con la migrazione di icone da un file Info. plist per un catalogo di Asset, vedere la [la migrazione da Info. plist a cataloghi Asset](~/ios/app-fundamentals/images-icons/app-icons.md) Guida.

Icona richiesta nel catalogo di Asset è denominato **App Store** e deve essere di 1024 x 1024 nella dimensione. Secondo le istruzioni di Apple l'icona per l'App Store nel catalogo asset non deve essere trasparente e non deve contenere un canale alfa.

![Posizione dell'icona store di App nel catalogo di asset.](images/image1.png)

## <a name="related-links"></a>Collegamenti correlati

- [What ' s New in iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Pagina del prodotto aggiornato App Store (Apple)](https://developer.apple.com/app-store/product-page/)
- [Aggiornamento dell'App per iOS 11 (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/204/)
