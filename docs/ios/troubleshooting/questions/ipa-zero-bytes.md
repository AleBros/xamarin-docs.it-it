---
title: File IPA è di 0 byte
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 376BBA27-8694-4E63-9976-BF60349D42D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 40799d0b8b051459145f51671ae7f6143db9635a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30777785"
---
# <a name="ipa-file-is-0-bytes"></a>File IPA è di 0 byte

> [!IMPORTANT]
> Questo problema è stato risolto nelle versioni recenti di Xamarin. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.



Si sono verificati alcuni problemi noti nelle versioni precedenti di Xamarin che potrebbe causare il file IPA in Windows a 0 byte. 

### <a name="fixed-in-xamarin-for-visual-studio-311584"></a>Risolto in Xamarin per Visual Studio 3.11.584 
- [Bug 24416 - configurazione di compilazione "Ad Hoc" dalla riga di comando non non copia IPA file in Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24416)
- [Bug 24417 - modifica "proprietà del progetto -> IPA iOS Opzioni -> nome del pacchetto" impedisce IPA venga copiato a Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24417)
- [Bug 29822 - impostazione [XVS.iOS 3.11] "Build" numero diverso da "Versione" numero cause IPA non devono essere copiati in Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=29822)

### <a name="fixed-in-xamarin-for-visual-studio-410496"></a>Risolto in Xamarin per Visual Studio 4.1.0.496
- [Bug 27989 - Mostra file ipa in caso di errore di compilazione server se il nome dell'Assembly non corrisponde al nome del progetto](https://bugzilla.xamarin.com/show_bug.cgi?id=27989)
