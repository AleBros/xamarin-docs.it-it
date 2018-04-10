---
title: Effetti
description: Vengono visualizzate le interfacce utente xamarin. Forms usando i controlli nativi della piattaforma di destinazione, consentendo alle applicazioni di xamarin. Forms mantenere l'aspetto appropriato per ogni piattaforma. Effetti consentono controlli nativi in ciascuna piattaforma per essere personalizzati senza dover ricorrere all'implementazione di un renderer personalizzato.
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 63d7750294321a28dbde833f72fe6b7277ada397
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="effects"></a>Effetti

_Vengono visualizzate le interfacce utente xamarin. Forms usando i controlli nativi della piattaforma di destinazione, consentendo alle applicazioni di xamarin. Forms mantenere l'aspetto appropriato per ogni piattaforma. Effetti consentono controlli nativi in ciascuna piattaforma per essere personalizzati senza dover ricorrere all'implementazione di un renderer personalizzato._

## <a name="introduction-to-effectsintroductionmd"></a>[Introduzione agli effetti](introduction.md)

Effetti consentono controlli nativi in ciascuna piattaforma da personalizzare e vengono in genere utilizzati per lo stile piccole modifiche. In questo articolo viene fornita un'introduzione agli effetti, descrive il limite tra renderer personalizzati e gli effetti e descrive il `PlatformEffect` classe.

## <a name="creating-an-effectcreatingmd"></a>[Creazione di un effetto](creating.md)

Gli effetti di semplificare la personalizzazione di un controllo. In questo articolo viene illustrato come creare un effetto che modifica il colore di sfondo di [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllare quando il controllo riceve lo stato attivo.

## <a name="passing-parameters-to-an-effectpassing-parametersindexmd"></a>[Passaggio di parametri a un effetto](passing-parameters/index.md)

Creazione di un effetto che viene configurato tramite parametri consente l'effetto di riutilizzo. Questi articoli illustrano l'utilizzo di proprietà per passare i parametri in effetti e la modifica di un parametro in fase di esecuzione.

## <a name="invoking-events-from-an-effecttouch-trackingmd"></a>[Richiamo di eventi da un effetto](touch-tracking.md)

Effetti possono richiamare gli eventi. In questo articolo viene illustrato come creare un evento che implementa il rilevamento di basso livello con un dito Multi-touch e segnala a un'applicazione per le pressioni tocco, si sposta e le versioni.