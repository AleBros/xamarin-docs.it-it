---
title: Requisiti di sistema
description: Questo documento elenca i requisiti di sistema per la compilazione di app con Xamarin nei computer Windows e Mac. Contiene inoltre i collegamenti alle istruzioni di installazione.
ms.prod: xamarin
ms.assetid: dd344d57-18e2-42a5-8c15-3f5be4123c72
author: davidortinau
ms.author: daortin
ms.date: 10/16/2019
ms.openlocfilehash: 093369010d9327cd2b19fdac09f77697532bfb75
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016431"
---
# <a name="system-requirements"></a>Requisiti di sistema

I prodotti Xamarin si basano sugli SDK di piattaforma di Apple e Google per le piattaforme iOS o Android di destinazione. I requisiti di sistema Microsoft pertanto corrispondono a quelli di queste aziende. Questa pagina illustra la compatibilità di sistema per la piattaforma Xamarin, l'ambiente di sviluppo consigliato e le versioni degli SDK.

Per altre informazioni su come ottenere il software e gli SDK necessari, vedere le [istruzioni di installazione](#installation-instructions).

## <a name="development-environments"></a>Ambienti di sviluppo

In questa tabella sono indicate le piattaforme che è possibile compilare con le diverse combinazioni di strumento di sviluppo e sistema operativo:

[!include[](~/cross-platform/includes/development-environment.md)]

> [!NOTE]
> Lo sviluppo per iOS nei computer Windows richiede un [computer Mac accessibile in rete](~/ios/get-started/installation/windows/connecting-to-mac/index.md) per la compilazione remota e il debug. È anche possibile usare Visual Studio in esecuzione all'interno di una macchina virtuale Windows in un computer Mac.

## <a name="macos-requirements"></a>Requisiti macOS

L'uso di un computer Mac per lo sviluppo con Xamarin richiede le versioni software/SDK indicate di seguito. Controllare la versione del sistema operativo in uso e seguire le istruzioni per il [programma di installazione di Xamarin](#installation-instructions).

[!include[](~/cross-platform/includes/macos-requirements.md)]

> [!NOTE]
> È possibile installare (e aggiornare) Xcode in [developer.apple.com](https://developer.apple.com/xcode/download/) o tramite Mac App Store.

### <a name="testing--debugging-on-macos"></a>Test e debug in macOS

- Per le attività di test e debug è possibile distribuire le applicazioni Xamarin per dispositivi mobili ai dispositivi fisici tramite USB. Le app Apple Watch vengono prima distribuite al dispositivo iPhone associato.
- Le app Xamarin.Mac possono essere testate direttamente nel computer di sviluppo.

[!include[](~/cross-platform/includes/macos-testing.md)]

> [!WARNING]
> Xamarin.Mac 4.8 supporta solo macOS 10.9 (Mavericks) o versione successiva.
> Le versioni precedenti di Xamarin.Mac supportavano macOS 10.7 o versione successiva, ma queste versioni precedenti di macOS non dispongono di un'infrastruttura TLS sufficiente per il supporto di TLS 1.2. Per scegliere come destinazione macOS 10.7 o macOS 10.8, usare Xamarin.Mac 4.6 o versioni precedenti.

## <a name="windows-requirements"></a>Requisiti Windows

L'uso di un computer Windows per lo sviluppo con Xamarin richiede le versioni software/SDK indicate di seguito.
Controllare la versione del sistema operativo in uso e verificare che non si tratti di una versione *Express* di Visual Studio. In caso affermativo, valutare l'aggiornamento a un'edizione *Community*.
Il programma di installazione di Visual Studio 2019 e Visual Studio 2017 include un'opzione per installare automaticamente Xamarin (il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**).

[!include[](~/cross-platform/includes/windows-requirements.md)]

> [!NOTE]
>
> - Xamarin per Visual Studio supporta qualsiasi edizione di Visual Studio 2019 o Visual Studio 2017 (Community, Professional ed Enterprise).
> - Per usare gli SDK più recenti di Android e iOS, è necessaria la versione più recente di Visual Studio. Per i requisiti di versione specifici, vedere le note sulla versione di [Xamarin.Android](/xamarin/android/release-notes/) e le note sulla versione di [Xamarin. iOS](/xamarin/ios/release-notes/).
> - Lo sviluppo di app Xamarin.Forms per la piattaforma UWP (Universal Windows Platform) richiede Windows 10 con Visual Studio 2017. È consigliato Visual Studio 2019.

### <a name="testing--debugging-on-windows"></a>Test e debug in Windows

Per le attività di test e debug è possibile distribuire le applicazioni Xamarin per dispositivi mobili ai dispositivi fisici tramite USB o in modalità wireless. I dispositivi iOS devono essere connessi al computer Mac, non al computer con Visual Studio.

[!include[](~/cross-platform/includes/windows-testing.md)]

## <a name="installation-instructions"></a>Istruzioni di installazione

È possibile scaricare la versione più recente di Xamarin per macOS con [Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/installation). Per Windows seguire le [istruzioni di installazione di Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio).

Nella [pagina delle novità](~/whats-new/index.yml) è disponibile un elenco completo delle attuali versioni del prodotto. Questa pagina include anche collegamenti alle note sulla versione.

Istruzioni di [installazione](~/get-started/installation/index.md) specifiche per ogni piattaforma sono disponibili qui:

- [Xamarin.iOS](~/ios/get-started/installation/index.md)
- [Xamarin.Android](~/android/get-started/installation/index.md)
- [Xamarin.Mac](~/mac/get-started/installation.md)

Sono anche disponibili altre informazioni sui [requisiti di Xamarin.Forms e le piattaforme supportate](~/get-started/requirements.md).

## <a name="related-links"></a>Collegamenti correlati

- [Download di Xamarin](https://visualstudio.microsoft.com/xamarin/)
- [Note sulla versione di Xamarin.Forms](/xamarin/xamarin-forms/release-notes/)
- [Note sulla versione di Xamarin.Android](/xamarin/android/release-notes/)
- [Note sulla versione di Xamarin.iOS](/xamarin/ios/release-notes/)
