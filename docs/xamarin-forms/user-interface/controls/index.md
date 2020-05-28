---
title: ''
description: Descrizione di tutti gli elementi dell'interfaccia utente utilizzati per la costruzione di un' Xamarin.Forms applicazione. Questo articolo elenca i gruppi di controllo che costituiscono l'interfaccia utente di un' Xamarin.Forms applicazione.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e843f0e42f4f66a6ce4e60c2f5d8a233d19f1df6
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136396"
---
# <a name="controls-reference"></a>Riferimento per i controlli

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

L'interfaccia utente di un' Xamarin.Forms applicazione è costituita da oggetti mappati ai controlli nativi di ogni piattaforma di destinazione. Ciò consente alle applicazioni specifiche della piattaforma per iOS, Android e al piattaforma UWP (Universal Windows Platform) di usare il Xamarin.Forms codice contenuto in una [libreria di .NET standard](~/cross-platform/app-fundamentals/net-standard.md).

I quattro gruppi di controllo principali utilizzati per creare l'interfaccia utente di un' Xamarin.Forms applicazione sono i seguenti:

- [**Pagine**](pages.md)
- [**Layout**](layouts.md)
- [**Viste**](views.md)
- [**Celle**](cells.md)

Una Xamarin.Forms pagina in genere occupa l'intera schermata. La pagina contiene in genere un layout, che contiene visualizzazioni e possibilmente altri layout. Le celle sono componenti specializzati utilizzati per la connessione con [`TableView`](views.md#tableview) e [`ListView`](views.md#listview) . Un diagramma classi che mostra la gerarchia dei tipi che vengono in genere usati per compilare un'interfaccia utente in è Xamarin.Forms disponibile nella [ Xamarin.Forms gerarchia di classi di controlli](~/xamarin-forms/internals/class-hierarchy.md).

Nei quattro articoli su [**pagine**](pages.md), [**layout**](layouts.md), [**visualizzazioni**](views.md)e [**celle**](cells.md), ogni tipo di controllo viene descritto con i collegamenti alla relativa documentazione API, un articolo che ne descrive l'uso (se esistente) e uno o più programmi di esempio, se esistenti. Ogni tipo di controllo è anche accompagnato da una schermata che mostra una pagina dell'esempio [**FormsGallery**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) in esecuzione su dispositivi iOS e Android. Di seguito sono riportati i collegamenti al codice sorgente per la pagina C#, la pagina XAML equivalente e, quando appropriato, il file code-behind C# per la pagina XAML.

> [!NOTE]
> Le pagine, i layout e le visualizzazioni derivano dalla `VisualElement` classe. La `VisualElement` classe fornisce un'ampia gamma di proprietà, metodi ed eventi utili per la derivazione delle classi. Per altre informazioni, vedere [proprietà, metodi ed eventi di visualElement](common-properties.md).

Oltre ai controlli forniti con, i Xamarin.Forms controlli di terze parti sono disponibili. Per altre informazioni, vedere [controlli di terze parti](thirdparty.md).

## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.FormsEsempio FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsGerarchia di classi di controlli](~/xamarin-forms/internals/class-hierarchy.md)
- [Documentazione dell'API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
