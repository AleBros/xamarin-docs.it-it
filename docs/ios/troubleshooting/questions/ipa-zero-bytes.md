---
title: Il file IPA è di 0 byte
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 376BBA27-8694-4E63-9976-BF60349D42D8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 3ef916dbd277544309f1803f923cde302494e4f1
ms.sourcegitcommit: 61a35d0643eb3bf5adb8f8831da54771d8dde626
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033499"
---
# <a name="ipa-file-is-0-bytes"></a>Il file IPA è di 0 byte

> [!IMPORTANT]
> Questo problema è stato risolto nelle versioni recenti di Novell. Tuttavia, se il problema si verifica nella versione più recente del software, inserire un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con le informazioni complete sul controllo delle versioni e l'output del log di compilazione completo.

Si sono verificati alcuni problemi noti nelle versioni precedenti di Novell che potevano causare 0 byte per il file IPA in Windows. 

### <a name="fixed-in-xamarin-for-visual-studio-311584"></a>Correzione in Novell per Visual Studio 3.11.584 

- [Bug 24416: la creazione della configurazione "ad-hoc" dalla riga di comando non copia il file IPA in Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24416)
- [Bug 24417-modifica di "Proprietà progetto-> Opzioni IPA iOS-> nome pacchetto" impedisce che IPA venga copiato in Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24417)
- [Bug 29822-[XVS. iOS 3,11] l'impostazione del numero "Build" diverso dal numero "Version" impedisce la copia del pacchetto IPA in Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=29822)

### <a name="fixed-in-xamarin-for-visual-studio-410496"></a>Correzione in Novell per Visual Studio 4.1.0.496

- [Bug 27989-Mostra il file IPA nel server di compilazione non riesce se il nome dell'assembly non corrisponde al nome del progetto](https://bugzilla.xamarin.com/show_bug.cgi?id=27989)
