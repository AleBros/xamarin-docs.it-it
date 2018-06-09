---
title: Localizzazione di xamarin. Forms
description: Il framework di localizzazione .NET incorporato consente di compilare applicazioni multilingue multipiattaforma con xamarin. Forms. Testo e immagini possono essere localizzate e le applicazioni possono supportare una direzione di flusso da destra a sinistra.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/13/2018
ms.openlocfilehash: 78731924324a1ddd34c0d197070699e2998c1513
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240593"
---
# <a name="xamarinforms-localization"></a>Localizzazione di xamarin. Forms

_Il framework di localizzazione .NET incorporato consente di compilare applicazioni multilingue multipiattaforma con xamarin. Forms._

## <a name="string-and-image-localizationtextmd"></a>[Localizzazione di stringhe e immagini](text.md)

Il meccanismo predefinito per la localizzazione di applicazioni .NET viene utilizzato [file RESX](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) e le classi di `System.Resources` e `System.Globalization` spazi dei nomi. I file RESX che contiene le stringhe tradotte sono incorporati nell'assembly xamarin. Forms, insieme a una classe generata dal compilatore che fornisce accesso fortemente tipizzato per le traduzioni. Il testo tradotto può quindi essere recuperato nel codice.

## <a name="right-to-left-localizationright-to-leftmd"></a>[Localizzazione da destra a sinistra](right-to-left.md)

Direzione del flusso è la direzione in cui gli elementi dell'interfaccia utente della pagina vengono analizzati dall'occhio. Da destra a sinistra localizzazione aggiunge il supporto per la direzione di flusso da destra a sinistra per le applicazioni di xamarin. Forms.
