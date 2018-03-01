---
title: "Come è possibile riabilitare opzioni per gli sviluppatori dopo l'aggiornamento a iOS?"
ms.topic: article
ms.prod: xamarin
ms.assetid: F38BD21E-0C21-43FF-80A6-BB4A88DB88A5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: f943132def65de04172d5547113ef85ecdbd036a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="how-can-i-reenable-developer-options-after-updating-ios"></a>Come è possibile riabilitare opzioni per gli sviluppatori dopo l'aggiornamento a iOS?

Un bug di iOS può causare opzioni per gli sviluppatori a scomparire dopo l'aggiornamento delle versioni di iOS, questa è stata osservata quando si passa a iOS 8. x. Queste opzioni possono essere riabilitate con i passaggi seguenti:

1. Connettere il dispositivo iOS al computer di sviluppo Mac.
2. Avviare Xcode.
3. Aprire **finestra > dispositivi** & Selezionare il dispositivo.
4. Attendere Xcode completare la copia dei simboli e quindi controllare Settings.app del dispositivo per le impostazioni di sviluppo.
5. Riavviare il dispositivo.
