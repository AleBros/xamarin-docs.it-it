---
title: Localizzazione di Xamarin.Forms
description: Il framework di localizzazione .NET incorporato può essere usato per compilare applicazioni multilingue e multipiattaforma con Xamarin.Forms. Il testo e le immagini possono essere localizzati e le applicazioni possono supportare la direzione del flusso da destra a sinistra.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
ms.openlocfilehash: b580c6e41aa689ff8fcea698c40d7aaf5f2ca050
ms.sourcegitcommit: 233aaa1ac3d8f40c09b6daf6d944ea0b4cbee381
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74135269"
---
# <a name="xamarinforms-localization"></a>Localizzazione di Xamarin.Forms

_Il framework di localizzazione .NET incorporato può essere usato per compilare applicazioni multilingue e multipiattaforma con Xamarin.Forms._

## <a name="xamarinforms-string-and-image-localizationtextmd"></a>[Stringa Novell. Forms e localizzazione di immagini](text.md)

Il meccanismo incorporato per la localizzazione delle applicazioni .NET usa i [file con estensione resx](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) e le classi negli spazi dei nomi `System.Resources` e `System.Globalization`. I file con estensione resx che contengono le stringhe tradotte sono incorporati nell'assembly Xamarin.Forms, insieme a una classe generata dal compilatore che fornisce l'accesso fortemente tipizzato alle traduzioni. Il testo tradotto può quindi essere recuperato nel codice.

## <a name="right-to-left-localizationright-to-leftmd"></a>[Localizzazione da destra a sinistra](right-to-left.md)

La direzione del flusso è la direzione in cui gli elementi dell'interfaccia utente della pagina vengono analizzati dall'occhio del lettore. La localizzazione da destra a sinistra aggiunge il supporto per la direzione del flusso da destra a sinistra nelle applicazioni Xamarin.Forms.
