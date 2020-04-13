---
title: Effetti di Xamarin.Forms
description: Gli effetti consentono di personalizzare i controlli nativi di ogni piattaforma senza dover ricorrere all'implementazione di un renderer personalizzato.
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 2de1d1dd065a01bb457ebf03acdc0c01529abf7b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73083829"
---
# <a name="xamarinforms-effects"></a>Effetti di Xamarin.Forms

_Il rendering delle interfacce utente di Xamarin.Forms viene eseguito utilizzando i controlli nativi della piattaforma di destinazione, consentendo alle applicazioni Xamarin.Forms di mantenere l'aspetto appropriato per ogni piattaforma. Gli effetti consentono di personalizzare i controlli nativi in ogni piattaforma senza dover ricorrere a un'implementazione del renderer personalizzata._

## <a name="introduction-to-effects"></a>[Introduzione agli effetti](introduction.md)

Gli effetti consentono di personalizzare i controlli nativi di ogni piattaforma e in genere vengono usati per apportare piccole modifiche allo stile. Questo articolo offre un'introduzione agli effetti, delinea il limite tra gli effetti e i renderer personalizzati e descrive la classe `PlatformEffect`.

## <a name="creating-an-effect"></a>[Creazione di un effetto](creating.md)

Gli effetti semplificano la personalizzazione di un controllo. In questo articolo viene illustrato come creare un [`Entry`](xref:Xamarin.Forms.Entry) effetto che modifica il colore di sfondo del controllo quando il controllo ottiene lo stato attivo.

## <a name="passing-parameters-to-an-effect"></a>[Passaggio dei parametri a un effetto](passing-parameters/index.md)

La creazione di un effetto configurato con i parametri consente di usare nuovamente l'effetto. Questi articoli spiegano come usare le proprietà per passare i parametri a un effetto e come modificare un parametro in fase di esecuzione.

## <a name="invoking-events-from-an-effect"></a>[Richiamo di eventi da un effetto](touch-tracking.md)

Gli effetti possono richiamare gli eventi. Questo articolo illustra come creare un evento che implementa il rilevamento del tocco nel multitouch di basso livello e segnala a un'applicazione le azioni di pressione, spostamento e rilascio con tocco.

## <a name="reusable-roundeffect"></a>[RoundEffect riutilizzabile](reusable-roundeffect.md)

RoundEffect è un effetto riutilizzabile che può essere applicato a qualsiasi controllo derivato da VisualElement per eseguire il rendering del controllo come un cerchio. Questo effetto può essere utilizzato per creare immagini circolari, pulsanti circolari o altri controlli circolari.
