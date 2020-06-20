---
title: Xamarin.FormsLocalizzazione
description: Il Framework di localizzazione .NET incorporato può essere usato per creare applicazioni multilingue multipiattaforma con Xamarin.Forms . Il testo e le immagini possono essere localizzati e le applicazioni possono supportare la direzione del flusso da destra a sinistra.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dddb80e8fb683547d2bf6ba0b74e870fe240695c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137566"
---
# <a name="xamarinforms-localization"></a>Xamarin.FormsLocalizzazione

_Il Framework di localizzazione .NET incorporato può essere usato per creare applicazioni multilingue multipiattaforma con Xamarin.Forms ._

## <a name="xamarinforms-string-and-image-localizationtextmd"></a>[Xamarin.FormsLocalizzazione di stringhe e immagini](text.md)

Il meccanismo incorporato per la localizzazione delle applicazioni .NET usa i [file con estensione resx](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) e le classi negli spazi dei nomi `System.Resources` e `System.Globalization`. I file RESX contenenti stringhe tradotte sono incorporati nell' Xamarin.Forms assembly, insieme a una classe generata dal compilatore che fornisce accesso fortemente tipizzato alle traduzioni. Il testo tradotto può quindi essere recuperato nel codice.

## <a name="right-to-left-localization"></a>[Localizzazione da destra a sinistra](right-to-left.md)

La direzione del flusso è la direzione in cui gli elementi dell'interfaccia utente della pagina vengono analizzati dall'occhio del lettore. La localizzazione da destra a sinistra aggiunge il supporto per la direzione del flusso da destra a sinistra alle Xamarin.Forms applicazioni.
