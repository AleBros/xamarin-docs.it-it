---
title: Come è possibile riabilitare opzioni per gli sviluppatori dopo l'aggiornamento a iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F38BD21E-0C21-43FF-80A6-BB4A88DB88A5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 9eb25676cc4a3a0679d6a02fc64fef5632f76e33
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30775484"
---
# <a name="how-can-i-reenable-developer-options-after-updating-ios"></a>Come è possibile riabilitare opzioni per gli sviluppatori dopo l'aggiornamento a iOS?

Un bug di iOS può causare opzioni per gli sviluppatori a scomparire dopo l'aggiornamento delle versioni di iOS, questa è stata osservata quando si passa a iOS 8. x. Queste opzioni possono essere riabilitate con i passaggi seguenti:

1. Connettere il dispositivo iOS al computer di sviluppo Mac.
2. Avviare Xcode.
3. Aprire **finestra > dispositivi** & Selezionare il dispositivo.
4. Attendere Xcode completare la copia dei simboli e quindi controllare Settings.app del dispositivo per le impostazioni di sviluppo.
5. Riavviare il dispositivo.
