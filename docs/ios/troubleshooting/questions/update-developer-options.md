---
title: Come è possibile riabilitare le opzioni per gli sviluppatori dopo l'aggiornamento di iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F38BD21E-0C21-43FF-80A6-BB4A88DB88A5
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 18651485ad53ca5493a0a47368608214e5f7c595
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292064"
---
# <a name="how-can-i-reenable-developer-options-after-updating-ios"></a>Come è possibile riabilitare le opzioni per gli sviluppatori dopo l'aggiornamento di iOS?

Un bug iOS può causare la scomparsa delle opzioni dello sviluppatore dopo l'aggiornamento delle versioni di iOS. questa osservazione è stata osservata quando si passa a iOS 8. x. Queste opzioni possono essere riabilitate con i passaggi seguenti:

1. Connettere il dispositivo iOS al computer di sviluppo Mac.
2. Avviare Xcode.
3. Aprire la **finestra dispositivi >** & selezionare il dispositivo.
4. Attendere che Xcode completi la copia dei simboli, quindi controllare le impostazioni del dispositivo. app per le impostazioni dello sviluppatore.
5. Riavviare il dispositivo.
