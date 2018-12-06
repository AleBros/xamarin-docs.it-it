---
title: Localizzazione di Xamarin.Forms
description: Il framework di localizzazione .NET incorporato può essere usato per compilare applicazioni multilingue e multipiattaforma con Xamarin.Forms. Il testo e le immagini possono essere localizzati e le applicazioni possono supportare la direzione del flusso da destra a sinistra.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
ms.openlocfilehash: 71033e935a2d3a4be88dbcc5d975938771484640
ms.sourcegitcommit: b60a37587aad8a0bfa8a522d88d22fa672002443
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285573"
---
# <a name="xamarinforms-localization"></a>Localizzazione di Xamarin.Forms

_Il framework di localizzazione .NET incorporato può essere usato per compilare applicazioni multilingue e multipiattaforma con Xamarin.Forms._

## <a name="string-and-image-localizationtextmd"></a>[Localizzazione di stringhe e immagini](text.md)

Il meccanismo incorporato per la localizzazione delle applicazioni .NET usa i [file con estensione resx](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) e le classi negli spazi dei nomi `System.Resources` e `System.Globalization`. I file con estensione resx che contengono le stringhe tradotte sono incorporati nell'assembly Xamarin.Forms, insieme a una classe generata dal compilatore che fornisce l'accesso fortemente tipizzato alle traduzioni. Il testo tradotto può quindi essere recuperato nel codice.

## <a name="right-to-left-localizationright-to-leftmd"></a>[Localizzazione da destra a sinistra](right-to-left.md)

La direzione del flusso è la direzione in cui gli elementi dell'interfaccia utente della pagina vengono analizzati dall'occhio del lettore. La localizzazione da destra a sinistra aggiunge il supporto per la direzione del flusso da destra a sinistra nelle applicazioni Xamarin.Forms.
