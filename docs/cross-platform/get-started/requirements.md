---
title: Requisiti di sistema
description: Prerequisiti per l'uso di Xamarin
ms.prod: xamarin
ms.assetid: dd344d57-18e2-42a5-8c15-3f5be4123c72
author: asb3993
ms.author: amburns
ms.date: 08/28/2017
ms.openlocfilehash: eb0fabf55362888b766c13d1ead3f82db3e680b4
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="system-requirements"></a>Requisiti di sistema

_Prerequisiti per l'uso di Xamarin_

I prodotti Xamarin si basano sugli SDK di piattaforma di Apple e Google per le piattaforme iOS o Android di destinazione. I requisiti di sistema Microsoft pertanto corrispondono a quelli di queste aziende. Questa pagina illustra la compatibilità di sistema per la piattaforma Xamarin, l'ambiente di sviluppo consigliato e le versioni degli SDK.

- [Ambienti di sviluppo](#devenv)
- [Requisiti macOS](#mac)
- [Requisiti Windows](#windows)

Per altre informazioni su come ottenere il software e gli SDK necessari, vedere le [istruzioni di installazione](#install).

<a name="devenv" />

## <a name="development-environments"></a>Ambienti di sviluppo

In questa tabella sono indicate le piattaforme che è possibile compilare con le diverse combinazioni di strumento di sviluppo e sistema operativo:

[!include[](~/cross-platform/includes/development-environment.md)]


> [!NOTE]
> Lo sviluppo per iOS nei computer Windows richiede un [computer Mac accessibile in rete](~/ios/get-started/installation/windows/connecting-to-mac/index.md) per la compilazione remota e il debug. È anche possibile usare Visual Studio in esecuzione all'interno di una macchina virtuale Windows in un computer Mac.

<a name="mac" />

## <a name="macos-requirements"></a>Requisiti macOS

L'uso di un computer Mac per lo sviluppo con Xamarin richiede le versioni software/SDK indicate di seguito. Controllare la versione del sistema operativo in uso e seguire le istruzioni per il [programma di installazione di Xamarin](#install).

[!include[](~/cross-platform/includes/macos-requirements.md)]

> [!NOTE]
> È possibile installare (e aggiornare) Xcode in [developer.apple.com](https://developer.apple.com/xcode/download/) o tramite Mac App Store.

### <a name="testing--debugging-on-macos"></a>Test e debug in macOS

Per le attività di test e debug è possibile distribuire le applicazioni Xamarin per dispositivi mobili ai dispositivi fisici tramite USB. Le app Xamarin.Mac possono essere testate direttamente nel computer di sviluppo, mentre le app Apple Watch vengono prima distribuite al dispositivo iPhone associato.

[!include[](~/cross-platform/includes/macos-testing.md)]


<a name="windows" />

## <a name="windows-requirements"></a>Requisiti Windows

L'uso di un computer Windows per lo sviluppo con Xamarin richiede le versioni software/SDK indicate di seguito.
Controllare la versione del sistema operativo in uso e verificare che non si tratti di una versione *Express* di Visual Studio. In caso affermativo, valutare l'aggiornamento a un'edizione *Community*.
I programmi di installazione di Visual Studio 2015 e 2017 includono un'opzione per installare automaticamente Xamarin.

[!include[](~/cross-platform/includes/windows-requirements.md)]


> [!NOTE]
>
>* Xamarin per Visual Studio supporta qualsiasi edizione di Visual Studio 2015 o 2017 (Community, Professional ed Enterprise).
>
>* Lo sviluppo di app Xamarin.Forms per la piattaforma UWP (Universal Windows Platform) richiede Windows 10 con Visual Studio 2015 o 2017.


### <a name="testing--debugging-on-windows"></a>Test e debug in Windows

Per le attività di test e debug è possibile distribuire le applicazioni Xamarin per dispositivi mobili ai dispositivi fisici tramite USB. I dispositivi iOS devono essere connessi al computer Mac, non al computer con Visual Studio.

[!include[](~/cross-platform/includes/windows-testing.md)]


> [!NOTE]
>
>* [Download dell'emulatore Windows Phone 8.1](https://www.microsoft.com/download/details.aspx?id=43719).
>* L'emulatore Windows Phone 10 è incluso in Visual Studio 2015 UWP SDK.

<a name="install" />

## <a name="installation-instructions"></a>Istruzioni di installazione

È possibile scaricare la versione più recente di Xamarin per macOS da [xamarin.com/download](http://xamarin.com/download). Per Windows seguire le istruzioni di installazione di [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio).

Nella [pagina delle versioni correnti](http://developer.xamarin.com/releases/current/) è disponibile un elenco completo delle attuali versioni del prodotto. La pagina include anche le singole versioni del prodotto e i collegamenti alle note sulla versione per i canali alfa e beta.

Istruzioni di [installazione](~/cross-platform/get-started/installation/index.md) specifiche per ogni piattaforma sono disponibili qui:

- [Xamarin.iOS](~/ios/get-started/installation/index.md)
- [Xamarin.Android](~/android/get-started/installation/index.md)
- [Xamarin.Mac](~/mac/get-started/installation.md)

Sono anche disponibili altre informazioni sui [requisiti di Xamarin.Forms e le piattaforme supportate](~/xamarin-forms/get-started/installation.md).


## <a name="related-links"></a>Collegamenti correlati

- [Download di Xamarin](https://xamarin.com/download/)
- [Current Releases](https://developer.xamarin.com/releases/current/) (Versioni correnti)
