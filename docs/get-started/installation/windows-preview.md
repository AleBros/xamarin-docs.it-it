---
title: Installazione di Xamarin Preview in Windows
description: Questo documento descrive come installare una versione di anteprima di Xamarin in Visual Studio 2019 tramite il canale della versione di anteprima.
ms.prod: xamarin
ms.assetid: 9F730444-06E8-4B3F-8A19-CA95CD484FFA
author: asb3993
ms.author: amburns
ms.date: 03/20/2018
ms.openlocfilehash: 9ce56891eeab73f661a9c22cc047c4d0bcb10337
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58854795"
---
# <a name="installing-xamarin-preview-on-windows"></a>Installazione di Xamarin Preview in Windows

Visual Studio 2019 e Visual Studio 2017 non supporta canali stabili, beta e alfa nello stesso modo delle versioni precedenti. Sono invece disponibili solo due opzioni:

- **Release** - equivalente al canale _stabile_ in Visual Studio per Mac
- **Preview** - equivalente ai canali _alfa_ e _beta_ in Visual Studio per Mac

> [!TIP]
> Per provare le funzionalità di versioni non definitive, dovrebbe [scaricare il programma di installazione di Visual Studio Preview](https://visualstudio.microsoft.com/vs/preview/), che offre la possibilità di installare **anteprima** versioni di Visual Studio side-by-Side con il (stabile Versione di rilascio). Altre informazioni sulle novità in Visual Studio 2019 sono reperibile nel [note sulla versione](https://docs.microsoft.com/visualstudio/releases/2019/release-notes).

La versione Preview di Visual Studio può includere le versioni di anteprima corrispondenti delle funzionalità di Xamarin, tra cui:

- Xamarin.Forms
- Xamarin.iOS
- Xamarin.Android
- Xamarin Profiler
- Xamarin Inspector
- Xamarin Remoted iOS Simulator

Lo screenshot seguente del **programma di installazione della versione Preview** mostra entrambe le opzioni Preview e Release (notare i numeri di versione in grigio: la versione 15.0 è Release e la versione 15.1 è Preview):

![Programma di installazione che mostra le opzioni di anteprima](windows-images/vs2017-installer.jpg)

Durante il processo di installazione è possibile applicare un **Nome alternativo dell'installazione** all'installazione side-by-side, in modo da poter distinguere le installazioni nel menu Start, come illustrato di seguito:

[![enome alternativo DIT prima di installare](windows-images/vs2017-nickname-sml.png "modificare il nome alternativo prima dell'installazione")](windows-images/vs2017-nickname.png#lightbox)

### <a name="uninstalling-visual-studio-2019-preview"></a>Disinstallazione di Visual Studio 2019 Preview

Il **programma di installazione di Visual Studio** deve inoltre essere utilizzato per disinstallare le versioni di anteprima di Visual Studio 2019. Per altre informazioni, leggere la [guida alla disinstallazione di Xamarin](uninstalling-xamarin.md#uninstallvs2017).
