---
title: Localizzazione di xamarin. Forms
description: Il framework di localizzazione .NET incorporato consente di compilare applicazioni multilingue multipiattaforma con xamarin. Forms.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/13/2018
ms.openlocfilehash: 29624eeccc8542b3296774f6b77480b664bee478
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="localization"></a>Localizzazione

_Il framework di localizzazione .NET incorporato consente di compilare applicazioni multilingue multipiattaforma con xamarin. Forms._

## <a name="string-and-image-localizationtextmd"></a>[Stringa e la localizzazione di immagine](text.md)

Il meccanismo predefinito per la localizzazione di applicazioni .NET viene utilizzato [file RESX](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) e le classi di `System.Resources` e `System.Globalization` spazi dei nomi. I file RESX che contiene le stringhe tradotte sono incorporati nell'assembly xamarin. Forms, insieme a una classe generata dal compilatore che fornisce accesso fortemente tipizzato per le traduzioni. Il testo tradotto può quindi essere recuperato nel codice.

## <a name="right-to-left-localizationright-to-leftmd"></a>[Localizzazione da destra a sinistra](right-to-left.md)

Direzione del flusso è la direzione in cui gli elementi dell'interfaccia utente della pagina vengono analizzati dall'occhio. Da destra a sinistra localizzazione aggiunge il supporto per la direzione di flusso da destra a sinistra per le applicazioni di xamarin. Forms.
