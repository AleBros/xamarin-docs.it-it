---
title: Effetti di Xamarin.Forms
description: Gli effetti consentono di personalizzare i controlli nativi di ogni piattaforma senza dover ricorrere all'implementazione di un renderer personalizzato.
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 9d859377c40c6fca07e140c50da46d8f30aaae04
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994458"
---
# <a name="xamarinforms-effects"></a>Effetti di Xamarin.Forms

_In Xamarin.Forms il rendering delle interfacce utente viene eseguito con i controlli nativi della piattaforma di destinazione, consentendo alle applicazioni Xamarin.Forms di mantenere l'aspetto appropriato per ogni piattaforma. Gli effetti consentono di personalizzare i controlli nativi di ogni piattaforma senza dover ricorrere all'implementazione di un renderer personalizzato._

## <a name="introduction-to-effectsintroductionmd"></a>[Introduzione agli effetti](introduction.md)

Gli effetti consentono di personalizzare i controlli nativi di ogni piattaforma e in genere vengono usati per apportare piccole modifiche allo stile. Questo articolo offre un'introduzione agli effetti, delinea il limite tra gli effetti e i renderer personalizzati e descrive la classe `PlatformEffect`.

## <a name="creating-an-effectcreatingmd"></a>[Creazione di un effetto](creating.md)

Gli effetti semplificano la personalizzazione di un controllo. Questo articolo spiega come creare un effetto che cambia il colore di sfondo del controllo [`Entry`](xref:Xamarin.Forms.Entry) quando il controllo riceve lo stato attivo.

## <a name="passing-parameters-to-an-effectpassing-parametersindexmd"></a>[Passaggio dei parametri a un effetto](passing-parameters/index.md)

La creazione di un effetto configurato con i parametri consente di usare nuovamente l'effetto. Questi articoli spiegano come usare le proprietà per passare i parametri a un effetto e come modificare un parametro in fase di esecuzione.

## <a name="invoking-events-from-an-effecttouch-trackingmd"></a>[Richiamo di eventi da un effetto](touch-tracking.md)

Gli effetti possono richiamare gli eventi. Questo articolo illustra come creare un evento che implementa il rilevamento del tocco nel multitouch di basso livello e segnala a un'applicazione le azioni di pressione, spostamento e rilascio con tocco.
