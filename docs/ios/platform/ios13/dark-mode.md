---
title: Modalità scura in Novell. iOS
description: La modalità scura è una nuova opzione a livello di sistema per i temi chiaro e scuro. l'utente iOS può ora scegliere un tema o consentire a iOS di modificare l'aspetto dinamicamente.
ms.prod: xamarin
ms.assetid: 4F44446E-36B6-4743-9B4D-32278D1D3D66
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/28/2019
ms.openlocfilehash: be487ab839e2fb4d21b85719a56dc34303317a5f
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71206396"
---
# <a name="dark-mode-in-xamarinios"></a>Modalità scura in Novell. iOS

La modalità scura è un'opzione a livello di sistema per i temi chiaro e scuro. Gli utenti iOS possono ora scegliere il tema o consentire a iOS di modificare l'aspetto in modo dinamico in base all'ambiente e all'ora del giorno.

Questo documento introduce la modalità scura e supporta la modalità scura nelle applicazioni iOS 13.

## <a name="requirements"></a>Requisiti

Per la modalità Dark sono necessari iOS 13 e Xcode 11, Novell. iOS 12,99 e Visual Studio 2019 o Visual Studio 2019 per Mac con supporto per Xcode 11.

## <a name="turning-on-dark-mode"></a>Attivazione della modalità scura

Apple fornisce un menu per sviluppatori in iOS 13 per passare da una modalità scura a una modalità leggera e viceversa. Nel simulatore iOS 13 aprire **Impostazioni** e scegliere la sezione **Developer (sviluppo** ), quindi scorrere fino al cambio di **aspetto scuro** . La modifica verrà applicata nell'intero ambiente simulatore:

![Attivazione della modalità scura](dark-mode-images/LightAndDark_DeveloperSetting.png)

## <a name="assets-for-light-and-dark-modes"></a>Asset per le modalità chiaro e scuro

Il catalogo asset in Visual Studio supporta ora immagini e colori facoltativi per ogni modalità di visualizzazione: Universale, scuro e chiaro. Quando si definiscono le immagini e i colori in questo modo, iOS sceglie automaticamente l'immagine e il colore appropriati.

Aprire il file **assets. xcassets** nel progetto iOS e aggiungere un nuovo set di immagini. Si noti che è possibile specificare immagini universali, scure e chiare in una qualsiasi delle risoluzioni di destinazione. Nella schermata seguente è presente un'immagine per il colore scuro e per la luce con il nome "MicrosoftLogo":

![Asset per le modalità chiaro e scuro](dark-mode-images/LightAndDark_AssetCatalog2.png)

**Assets. xcassets** contiene anche le voci per **BackgroundColor** e **TitleColor**, che sono definizioni dei colori. Questi colori sono ora disponibili in base al nome da usare nell'applicazione. Il **BackgroundColor** è stato assegnato allo sfondo della visualizzazione e **TitleColor** all'etichetta, come illustrato in questo screenshot:

![Asset per le modalità chiaro e scuro](dark-mode-images/LightAndDark_01.png)

## <a name="dynamic-system-colors"></a>Colori di sistema dinamici

Apple ha introdotto nuovi colori semantici che regolano l'aspetto in modo dinamico in base alla nuova impostazione della modalità scura.

## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto la modalità Dark per iOS e specifica le immagini e i colori per ogni modalità usando il catalogo asset.

## <a name="related-links"></a>Collegamenti correlati

- [Linee guida per la progettazione in modalità scura](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/dark-mode/)
- [Colori semantici](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/#dynamic-system-colors)
