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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "66835258"
---
# <a name="xamarinforms-navigation"></a>Navigazione in Xamarin.Forms

_In Xamarin.Forms sono disponibili diverse esperienze di spostamento tra le pagine, a seconda del tipo di Pagina in uso._

![](images/page-types.png "Xamarin.Forms Page Types")

In alternativa, le applicazioni Xamarin.Forms Shell usano un'esperienza di navigazione basata su URI che non impone una gerarchia di navigazione preimpostata. Per altre informazioni, vedere [Navigazione nella shell Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/navigation.md).

## <a name="hierarchical-navigation"></a>[Navigazione gerarchica](hierarchical.md)

La [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe offre un'esperienza di spostamento gerarchica in cui l'utente è in grado di spostarsi tra le pagine, avanti e indietro, come desiderato. La classe implementa la navigazione come stack di [`Page`](xref:Xamarin.Forms.Page) oggetti LIFO (Last-In, First-Out).

## <a name="tabbedpage"></a>[TabbedPage](tabbed-page.md)

Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) è costituito da un elenco di schede e un'area di dettaglio più grande, con ogni scheda che carica il contenuto nell'area di dettaglio.

## <a name="carouselpage"></a>[CarouselPage](carousel-page.md)

Xamarin.Forms [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) è una pagina che gli utenti possono scorrere da un lato all'altro per spostarsi tra le pagine di contenuto, ad esempio una galleria.

## <a name="masterdetailpage"></a>[MasterDetailPage](master-detail-page.md)

Il Xamarin.Forms [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) è una pagina che gestisce due pagine di informazioni correlate , una pagina master che presenta gli elementi e una pagina di dettaglio che presenta i dettagli sugli elementi nella pagina master.

## <a name="modal-pages"></a>[Pagine modali](modal.md)

Xamarin.Forms offre anche il supporto per le pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata.
