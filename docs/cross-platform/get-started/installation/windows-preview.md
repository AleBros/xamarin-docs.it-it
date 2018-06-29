---
title: Installazione di Xamarin Preview in Windows
description: Questo documento descrive come installare una versione di anteprima di Xamarin in Visual Studio 2017 usando il canale della versione di anteprima.
ms.prod: xamarin
ms.assetid: 9F730444-06E8-4B3F-8A19-CA95CD484FFA
author: asb3993
ms.author: amburns
ms.date: 03/20/2018
ms.openlocfilehash: 12a661a7cb0e92046e56c5c2ece6e8504252ca10
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066858"
---
# <a name="installing-xamarin-preview-on-windows"></a>Installazione di Xamarin Preview in Windows

Visual Studio 2017 non supporta canali alfa, beta e stabili nello stesso modo delle versioni precedenti. Sono invece disponibili solo due opzioni:

- **Release** - equivalente al canale _stabile_ in Visual Studio per Mac
- **Preview** - equivalente ai canali _alfa_ e _beta_ in Visual Studio per Mac

> [!TIP] 
> Per provare le funzionalità non definitive, è necessario [scaricare il programma di installazione di Visual Studio 2017 Preview](https://visualstudio.microsoft.com/vs/preview/), che offre la possibilità di installare le versioni **Preview** di Visual Studio side-by-side con la versione stabile (Release). Altre informazioni sulle novità in Visual Studio 2017 sono disponibili nelle [note sulla versione](/visualstudio/releasenotes/vs2017-preview-relnotes).

La versione Preview di Visual Studio può includere le versioni di anteprima corrispondenti delle funzionalità di Xamarin, tra cui:

- Xamarin.Forms
- Xamarin.iOS
- Xamarin.Android
- Xamarin Profiler
- Xamarin Workbooks
- Xamarin Inspector
- Xamarin Remoted iOS Simulator

Lo screenshot seguente del **programma di installazione della versione Preview** mostra entrambe le opzioni Preview e Release (notare i numeri di versione in grigio: la versione 15.0 è Release e la versione 15.1 è Preview):

![Programma di installazione che mostra le opzioni di anteprima](windows-images/vs2017-installer.jpg)

Durante il processo di installazione è possibile applicare un **Nome alternativo dell'installazione** all'installazione side-by-side, in modo da poter distinguere le installazioni nel menu Start, come illustrato di seguito:

[![modificare il nome alternativo prima dell'installazione](windows-images/vs2017-nickname-sml.png "modificare il nome alternativo prima dell'installazione")](windows-images/vs2017-nickname.png#lightbox)

### <a name="uninstalling-visual-studio-2017-preview"></a>Disinstallazione di Visual Studio Preview 2017

Il **programma di installazione di Visual Studio** deve essere usato anche per disinstallare le versioni di anteprima di Visual Studio 2017. Per altre informazioni, leggere la [guida alla disinstallazione di Xamarin](uninstalling-xamarin.md#uninstallvs2017).
