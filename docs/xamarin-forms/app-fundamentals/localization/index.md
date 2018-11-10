---
title: Localizzazione di xamarin. Forms
description: Il framework di localizzazione .NET incorporato è utilizzabile per compilare applicazioni multilingue multipiattaforma con xamarin. Forms. Testo e immagini possono essere localizzate e le applicazioni possono supportare una direzione di flusso da destra a sinistra.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
ms.openlocfilehash: 71033e935a2d3a4be88dbcc5d975938771484640
ms.sourcegitcommit: b60a37587aad8a0bfa8a522d88d22fa672002443
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285573"
---
# <a name="xamarinforms-localization"></a>Localizzazione di xamarin. Forms

_Il framework di localizzazione .NET incorporato è utilizzabile per compilare applicazioni multilingue multipiattaforma con xamarin. Forms._

## <a name="string-and-image-localizationtextmd"></a>[Localizzazione di stringhe e immagini](text.md)

Il meccanismo incorporato per la localizzazione .NET applications utilizzato [i file RESX](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) e le classi nel `System.Resources` e `System.Globalization` gli spazi dei nomi. I file RESX che contiene le stringhe tradotte vengono incorporati nell'assembly xamarin. Forms, insieme a una classe generata dal compilatore che fornisce accesso fortemente tipizzato per le traduzioni. Il testo tradotto può quindi essere recuperato nel codice.

## <a name="right-to-left-localizationright-to-leftmd"></a>[Localizzazione da destra a sinistra](right-to-left.md)

Direzione del flusso è la direzione in cui gli elementi dell'interfaccia utente nella pagina vengono analizzati dall'occhio del lettore. Localizzazione da destra a sinistra aggiunge il supporto per la direzione del flusso da destra a sinistra per le applicazioni xamarin. Forms.
