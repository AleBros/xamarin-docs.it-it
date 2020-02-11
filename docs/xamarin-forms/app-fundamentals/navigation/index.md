---
title: Navigazione in Xamarin.Forms
description: Questa guida illustra come spostarsi nelle applicazioni Xamarin.Forms. Xamarin.Forms offre diverse esperienze di navigazione tra le pagine, a seconda del tipo di pagina in uso.
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 682e3bd0ac4cdd651203496dd28586db2cef3165
ms.sourcegitcommit: c2bba24233624c2ec0e9ee9827310ca022212a2c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2019
ms.locfileid: "66835258"
---
# <a name="xamarinforms-navigation"></a>Navigazione in Xamarin.Forms

_Xamarin.Forms offre diverse esperienze di navigazione tra le pagine, a seconda del tipo di pagina in uso._

![](images/page-types.png "Tipi di pagina di Xamarin.Forms")

In alternativa, le applicazioni Xamarin.Forms Shell usano un'esperienza di navigazione basata su URI che non impone una gerarchia di navigazione preimpostata. Per altre informazioni, vedere [Navigazione nella shell Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/navigation.md).

## <a name="hierarchical-navigationhierarchicalmd"></a>[Navigazione gerarchica](hierarchical.md)

La classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) offre un'esperienza di navigazione gerarchica in cui l'utente è in grado di scorrere le pagine avanti e indietro in base alle esigenze. La classe implementa la navigazione come stack LIFO (Last-In, First-Out) di oggetti [`Page`](xref:Xamarin.Forms.Page).

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) di Xamarin.Forms è costituita da un elenco di schede e un'area dei dettagli più grande con ogni scheda che carica il contenuto nell'area dei dettagli.

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

Il tipo [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) di Xamarin.Forms è una pagina che gli utenti possono scorrere rapidamente da un lato all'altro per spostarsi nelle pagine di contenuto, ad esempio una raccolta.

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

Il tipo [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) di Xamarin.Forms è una pagina che gestisce due pagine di informazioni correlate: una pagina master che presenta gli elementi e una pagina di dettaglio che presenta i dettagli relativi agli elementi della pagina master.

## <a name="modal-pagesmodalmd"></a>[Pagine modali](modal.md)

Xamarin.Forms offre anche il supporto per le pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata.
