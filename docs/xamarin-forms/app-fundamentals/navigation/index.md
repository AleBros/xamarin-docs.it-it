---
title: Xamarin.FormsNavigazione
description: Questa guida illustra come eseguire la navigazione nelle Xamarin.Forms app. Xamarin.Formsin sono disponibili diverse esperienze di navigazione tra le pagine, a seconda del tipo di pagina utilizzato.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c907cd8a4a1d14b936dee309610bffc67ef363f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137839"
---
# <a name="xamarinforms-navigation"></a>Xamarin.FormsNavigazione

_Novell. Forms fornisce una serie di diverse esperienze di navigazione tra le pagine, a seconda del tipo di pagina usato._

![](images/page-types.png "Xamarin.Forms Page Types")

In alternativa, Xamarin.Forms le applicazioni shell utilizzano un'esperienza di navigazione basata su URI che non impone una gerarchia di navigazione set. Per altre informazioni, vedere [ Xamarin.Forms navigazione della shell](~/xamarin-forms/app-fundamentals/shell/navigation.md).

## <a name="hierarchical-navigation"></a>[Navigazione gerarchica](hierarchical.md)

La [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe fornisce un'esperienza di navigazione gerarchica in cui l'utente è in grado di spostarsi tra le pagine, avanti e indietro, a seconda delle esigenze. La classe implementa la navigazione come stack LIFO (Last-in, First-out) di [`Page`](xref:Xamarin.Forms.Page) oggetti.

## <a name="tabbedpage"></a>[TabbedPage](tabbed-page.md)

Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) È costituito da un elenco di schede e da un'area di dettaglio più ampia, in cui ogni scheda carica contenuto nell'area dei dettagli.

## <a name="carouselpage"></a>[CarouselPage](carousel-page.md)

Xamarin.Forms [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) È una pagina che gli utenti possono scorrere da un lato all'altro per spostarsi tra le pagine di contenuto, ad esempio una raccolta.

## <a name="masterdetailpage"></a>[MasterDetailPage](master-detail-page.md)

Xamarin.Forms [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) È una pagina che gestisce due pagine di informazioni correlate, ovvero una pagina master che presenta elementi, e una pagina di dettaglio che presenta informazioni dettagliate sugli elementi della pagina master.

## <a name="modal-pages"></a>[Pagine modali](modal.md)

Xamarin.Formsfornisce inoltre il supporto per le pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata.
