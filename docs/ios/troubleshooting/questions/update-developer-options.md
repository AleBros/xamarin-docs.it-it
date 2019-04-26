---
title: Come è possibile a riabilitare le opzioni per sviluppatori dopo l'aggiornamento di iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F38BD21E-0C21-43FF-80A6-BB4A88DB88A5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 42f7f63c1da54bc7596d52c78cbcc1ed76931424
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61417682"
---
# <a name="how-can-i-reenable-developer-options-after-updating-ios"></a>Come è possibile a riabilitare le opzioni per sviluppatori dopo l'aggiornamento di iOS?

Un bug di iOS può causare le opzioni per gli sviluppatori a scomparire dopo aver aggiornato le versioni di iOS, questa è stata osservata quando si passa a iOS 8. x. Queste opzioni possono essere riabilitate con i passaggi seguenti:

1. Connettere il dispositivo iOS al computer di sviluppo Mac.
2. Avviare Xcode.
3. Aprire **finestra > dispositivi** & Selezionare il dispositivo.
4. Attendere per Xcode completare la copia dei simboli e quindi controllare Settings.app del dispositivo per le impostazioni per gli sviluppatori.
5. Riavviare il dispositivo.
