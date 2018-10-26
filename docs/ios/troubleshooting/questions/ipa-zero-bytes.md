---
title: Il file IPA è pari a 0 byte
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 376BBA27-8694-4E63-9976-BF60349D42D8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4835c980b6b11c92ec1c81dea69f229aa5652275
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102659"
---
# <a name="ipa-file-is-0-bytes"></a>Il file IPA è pari a 0 byte

> [!IMPORTANT]
> Questo problema è stato risolto nelle versioni recenti di Xamarin. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.



Si sono verificati alcuni problemi noti nelle versioni precedenti di Xamarin che potrebbe causare il file IPA nel Windows sia pari a 0 byte. 

### <a name="fixed-in-xamarin-for-visual-studio-311584"></a>Risolto in Xamarin per Visual Studio 3.11.584 
- [Bug 24416 - configurazione di compilazione "Ad Hoc" dalla riga di comando esegue non copia IPA file Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24416)
- [Bug 24417 - modifica "proprietà progetto -> iOS IPA Opzioni -> nome del pacchetto" impedisce IPA che vengono copiati nuovamente in Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24417)
- [Bug 29822 - [XVS.iOS 3.11] impostazione "Build" numero diverso da "Version" numerica cause IPA non devono essere copiati per Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=29822)

### <a name="fixed-in-xamarin-for-visual-studio-410496"></a>Risolto in Xamarin per Visual Studio 4.1.0.496
- [Bug 27989 - Mostra file ipa nel caso di errore di compilazione server se il nome dell'Assembly non corrisponde al nome di progetto](https://bugzilla.xamarin.com/show_bug.cgi?id=27989)
