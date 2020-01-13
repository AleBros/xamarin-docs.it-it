---
title: Riferimento per i controlli
description: Descrizione di tutti gli elementi dell'interfaccia utente utilizzati per creare un'applicazione Xamarin.Forms. Questo articolo elenca i gruppi di controllo che costituiscono l'interfaccia utente di un'applicazione Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2019
ms.openlocfilehash: b9436603c17eb8008f470e75a52a93e8e122034f
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488115"
---
# <a name="controls-reference"></a>Riferimento per i controlli

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

L'interfaccia utente di un'applicazione Xamarin.Forms è costituita da oggetti mappati ai controlli nativi di ogni piattaforma di destinazione. Ciò consente alle applicazioni specifiche della piattaforma per iOS, Android e al piattaforma UWP (Universal Windows Platform) di usare il codice Xamarin.Forms contenuto in una [libreria di .NET standard](~/cross-platform/app-fundamentals/net-standard.md).

I quattro gruppi di controllo principali usati per creare l'interfaccia utente di un'applicazione Xamarin.Forms sono i seguenti:

- [**Pagine**](pages.md)
- [**Layout**](layouts.md)
- [**Visualizzazioni**](views.md)
- [**Celle**](cells.md)

In genere, una pagina di Xamarin.Forms occupa l'intera schermata. La pagina contiene in genere un layout, che contiene le viste e possibilmente altri layout. Le celle sono componenti specializzati utilizzati in combinazione con [ `TableView` ](views.md#tableview) e [ `ListView` ](views.md#listview). Un diagramma classi che mostra la gerarchia dei tipi che vengono in genere usati per compilare un'interfaccia utente in Xamarin.Forms è disponibile nella [gerarchia di classi di controlli Xamarin.Forms](~/xamarin-forms/internals/class-hierarchy.md).

Negli articoli su quattro [ **pagine**](pages.md), [ **layout**](layouts.md), [ **viste** ](views.md), e [ **celle**](cells.md), con collegamenti alla relativa documentazione dell'API, un articolo che descrive l'uso (se presente) e uno o più programmi di esempio viene descritto ciascun tipo di controllo (se presenti). Ogni tipo di controllo è anche accompagnato da una schermata che mostra una pagina dell'esempio [**FormsGallery**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) in esecuzione su dispositivi iOS e Android. Ogni schermata seguente sono riportati collegamenti al codice sorgente per la pagina in c#, la pagina XAML equivalente e (se appropriato) il file code-behind c# per la pagina XAML.

> [!NOTE]
> Le pagine, i layout e le visualizzazioni derivano dalla classe `VisualElement`. La classe `VisualElement` fornisce un'ampia gamma di proprietà, metodi ed eventi utili per la derivazione delle classi. Per altre informazioni, vedere [proprietà, metodi ed eventi di visualElement](common-properties.md).

Oltre ai controlli forniti con Xamarin.Forms, sono disponibili controlli di terze parti. Per altre informazioni, vedere [controlli di terze parti](thirdparty.md).

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Xamarin.Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Gerarchia di classi di controlli Xamarin.Forms](~/xamarin-forms/internals/class-hierarchy.md)
- [Documentazione delle API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
