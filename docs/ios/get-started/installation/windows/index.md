---
title: Installazione di Xamarin.iOS in Windows
description: Questo documento descrive come configurare un computer Windows, configurare un host di compilazione Mac e associare Windows al Mac per lo sviluppo di Xamarin.iOS.
zone_pivot_groups: platform-win
ms.prod: xamarin
ms.assetid: abf85d3e-a365-44a2-b1a4-6c572c7f76dd
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/16/2018
ms.openlocfilehash: 7c7a3e311a6c5f19242b51dd14f59bcdc9a75c64
ms.sourcegitcommit: 91a4fcb715506e18e8070bc89bf2cb14d079ad32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59574715"
---
# <a name="installing-xamarinios-on-windows"></a>Installazione di Xamarin.iOS in Windows

_Questo articolo descrive come configurare un computer Windows e un host di compilazione Mac per lo sviluppo con Xamarin.iOS._

::: zone pivot="windows"

## <a name="overview"></a>Panoramica

Per compilare le app Xamarin.iOS con Visual Studio 2019 in Windows, è necessario quanto segue:

- Un computer Windows con Visual Studio 2019 installato. Può trattarsi di un computer fisico o di una macchina virtuale.

  - [Requisiti di sistema Windows](~/cross-platform/get-started/requirements.md#windows-requirements)

- Un Mac accessibile dalla rete configurato con strumenti di compilazione Apple e Xamarin.iOS. Visual Studio 2019 accede a questa macchina tramite una connessione di rete per usare gli strumenti di compilazione Apple, necessari per la compilazione di applicazioni iOS native.

  - [Requisiti di sistema Mac](~/cross-platform/get-started/requirements.md#macos-requirements)

  > [!TIP]
  > Nessun accesso a un computer Mac
  >
  > Se non si ha accesso a un computer Mac, è possibile usare [MacinCloud](https://www.macincloud.com/pages/visual-studio-mac.html) oppure [MacStadium](https://www.macstadium.com/). Entrambi i servizi offrono hardware Mac basato sul cloud con il quale è possibile compilare progetti Xamarin.iOS.

## <a name="setup"></a>Configurazione

Per impostare la configurazione per lo sviluppo con Xamarin.iOS in Visual Studio 2019, seguire questa procedura:

1. Configurare Windows (installare Visual Studio 2019)

    Xamarin.iOS può essere usato con Visual Studio 2019 Community, Professional ed Enterprise Edition in un computer autonomo o una macchina virtuale.

    - [Installare Visual Studio 2019](~/get-started/installation/windows.md).

2. Configurare il Mac (Installare Xcode e Visual Studio per Mac)

    Per compilare, eseguire il debug e firmare le applicazioni iOS per la distribuzione, Visual Studio 2017 deve avere accesso tramite rete a un host di compilazione Mac configurato con gli strumenti di sviluppo Apple (Xcode) e Xamarin.iOS.

    - [Scaricare e installare Xcode da Mac App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12). 
    - [Installare Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/installation), che installa anche Xamarin.iOS.

    > [!NOTE]
    > Se si preferisce non installare Visual Studio per Mac, Visual Studio 2019 può [configurare automaticamente](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning) l'host di compilazione Mac con il software necessario per compilare applicazioni Xamarin.iOS. Per altre informazioni, vedere [Provisioning automatico del Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning).

3. Eseguire l'associazione al Mac (connettere Visual Studio 2019 al Mac)

    Per fare in modo che Visual Studio 2019 usi gli strumenti di compilazione iOS nel Mac, i due computer devono essere connessi in rete.

    - [Leggere la guida Associazione al Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

::: zone-end
::: zone pivot="win-vs2017"

## <a name="overview"></a>Panoramica

Per compilare le app Xamarin.iOS con Visual Studio 2017 in Windows, è necessario quanto segue:

-  Un computer Windows con Visual Studio 2017 installato. Può trattarsi di un computer fisico o di una macchina virtuale.
    - [Requisiti di sistema Windows](~/cross-platform/get-started/requirements.md#windows-requirements)
    
-  Un Mac accessibile dalla rete configurato con strumenti di compilazione Apple e Xamarin.iOS. Visual Studio 2017 accede a questa macchina tramite una connessione di rete per usare gli strumenti di compilazione Apple, necessari per la compilazione di applicazioni iOS native. 
    - [Requisiti di sistema Mac](~/cross-platform/get-started/requirements.md#macos-requirements)

## <a name="setup"></a>Configurazione

Per predisporre la configurazione per lo sviluppo con Xamarin.iOS in Visual Studio 2017, seguire questi passaggi:

1. Configurare Windows (Installare Visual Studio 2017)

    Xamarin.iOS può essere usato con Visual Studio 2017 Community, Professional ed Enterprise Edition, in un computer autonomo o una macchina virtuale.
    
    - [Installare Visual Studio 2017](~/get-started/installation/windows.md).

2. Configurare il Mac (Installare Xcode e Visual Studio per Mac)

    Per compilare, eseguire il debug e firmare le applicazioni iOS per la distribuzione, Visual Studio 2017 deve avere accesso tramite rete a un host di compilazione Mac configurato con gli strumenti di sviluppo Apple (Xcode) e Xamarin.iOS.

    - [Scaricare e installare Xcode da Mac App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12). 
    - [Installare Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/installation), che installa anche Xamarin.iOS.

    > [!NOTE]
    > Se si preferisce non installare Visual Studio per Mac, a partire da [Visual Studio 2017 versione 15.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning) Visual Studio 2017 può configurare automaticamente l'host di compilazione Mac con il software necessario per compilare applicazioni Xamarin.iOS. Per altre informazioni, vedere [Provisioning automatico del Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning).

3. Eseguire l'associazione al Mac (Connettere Visual Studio 2017 al Mac)

    Per fare in modo che Visual Studio 2017 usi gli strumenti di compilazione iOS nel Mac, i due computer devono essere connessi in rete.

    - [Leggere la guida Associazione al Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

::: zone-end

## <a name="summary"></a>Riepilogo

Questo articolo descrive come configurare un computer Windows e l'host di compilazione Mac associato per lo sviluppo con Xamarin.iOS.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Xamarin.iOS per Visual Studio](introduction-to-xamarin-ios-for-visual-studio.md)
- [Configurazione di Visual Studio per lo sviluppo iOS](config-options.md)
- [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md)
