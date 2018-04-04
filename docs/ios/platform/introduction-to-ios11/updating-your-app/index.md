---
title: L'aggiornamento dell'App per iOS 11
description: Esplorare le nuove funzionalità di iOS 11
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 2581f729d85787021763f50f005e84d6bbb5db01
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="updating-your-app-to-ios-11"></a>L'aggiornamento dell'App per iOS 11

_Esplorare le nuove funzionalità di iOS 11_

In iOS 11, Apple ha introdotto un processo di connessione aggiornate iTunes architettura aggiornamenti e nuove modifiche di visual. Questa guida viene esaminato ognuna di queste modifiche, consentono di ottenere l'app xamarin aggiornato per iOS 11.

## <a name="architecture-changesarchitecture-changesmd"></a>[Modifiche all'architettura](architecture-changes.md)

Una delle principali modifiche che è necessario essere consapevoli di con iOS 11 è della deprecazione del supporto a 32 bit per le app, come descritto in dettaglio nella [Apple](https://developer.apple.com/news/?id=06282017b) comunicato.

Questa guida descrive l'aggiornamento dell'app per a 64 bit.

## <a name="visual-design-updatesvisual-designmd"></a>[Aggiornamenti della progettazione visiva](visual-design.md)

In iOS 11, Apple ha introdotto nuove modifiche visive tra cui gli aggiornamenti per la barra di spostamento, barra di ricerca e viste delle tabelle. Inoltre, sono stati apportati miglioramenti per consentire maggiore flessibilità su margini e il contenuto a schermo intero. Queste modifiche sono descritte in questa Guida.

## <a name="app-store-changesapp-store-changesmd"></a>[Modifiche di App Store](app-store-changes.md)

App Store iOS ha avuto una riprogettazione completa, che non solo consente agli utenti di esplorare in modo efficiente store, ma consente inoltre, gli sviluppatori, per promuovere l'app agli utenti. Queste promozioni includono aggiornamenti di acquisti in-app e gli aggiornamenti a pagina del prodotto. iOS 11 aggiunge anche gli aggiornamenti relativi a come rilasciare l'app al pubblico, come aggiungere l'icona dell'app e come comunicare con gli utenti.

## <a name="app-icon-updates"></a>Icona App aggiornamenti

> [!NOTE]
> Icone App devono ora essere recapitate tramite un _catalogo di Asset_. 

Per informazioni sull'utilizzo di cataloghi asset consultare il [App Store icona](~/ios/app-fundamentals/images-icons/app-store-icon.md) Guida. Per assistenza con la migrazione di icone da un file Info. plist a un catalogo di Asset, vedere il [la migrazione da Info. plist a cataloghi Asset](~/ios/app-fundamentals/images-icons/app-icons.md) Guida.

Icona obbligatorio nel catalogo di Asset è denominata **App Store** e deve essere 1024 x 1024 nella dimensione. Secondo le istruzioni di Apple l'icona per l'App Store nel catalogo asset non deve essere trasparente e non deve contenere un canale alfa.

![Posizione dell'icona store di App nel catalogo di asset.](images/image1.png)

## <a name="related-links"></a>Collegamenti correlati

- [Novità in iOS 11 (mela)](https://developer.apple.com/ios/)
- [Pagina del prodotto aggiornato App Store (mela)](https://developer.apple.com/app-store/product-page/)
- [L'aggiornamento dell'App per iOS 11 (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/204/)
