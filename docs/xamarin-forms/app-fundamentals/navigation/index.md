---
title: Navigazione in Xamarin.Forms
description: Questa guida illustra come spostarsi nelle applicazioni Xamarin.Forms. Xamarin.Forms offre diverse esperienze di navigazione tra le pagine, a seconda del tipo di pagina in uso.
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 202f044ebd7dd5b110b94d2aa60eeb7151150607
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994728"
---
# <a name="xamarinforms-navigation"></a>Navigazione in Xamarin.Forms

_Xamarin.Forms offre diverse esperienze di navigazione tra le pagine, a seconda del tipo di pagina in uso._

![](images/page-types.png "Tipi di pagina di Xamarin.Forms")

## <a name="hierarchical-navigationhierarchicalmd"></a>[Navigazione gerarchica](hierarchical.md)

La classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) offre un'esperienza di navigazione gerarchica in cui l'utente è in grado di scorrere le pagine avanti e indietro in base alle esigenze. La classe implementa la navigazione come stack LIFO (Last-In, First-Out) di oggetti [`Page`](xref:Xamarin.Forms.Page).

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

Il tipo [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) di Xamarin.Forms è costituito da un elenco di schede e un'area più grande dei dettagli, dove ogni scheda carica contenuto nell'area dei dettagli.

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

Il tipo [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) di Xamarin.Forms è una pagina che gli utenti possono scorrere rapidamente da un lato all'altro per spostarsi nelle pagine di contenuto, ad esempio una raccolta.

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

Il tipo [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) di Xamarin.Forms è una pagina che gestisce due pagine di informazioni correlate: una pagina master che presenta gli elementi e una pagina di dettaglio che presenta i dettagli relativi agli elementi della pagina master.

## <a name="modal-pagesmodalmd"></a>[Pagine modali](modal.md)

Xamarin.Forms offre anche il supporto per le pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata.

## <a name="displaying-pop-upspop-upsmd"></a>[Visualizzazione di popup](pop-ups.md)

Xamarin.Forms offre due elementi dell'interfaccia utente di tipo popup: un avviso e una finestra delle azioni. Questi elementi dell'interfaccia possono essere usati per porre semplici domande agli utenti e per guidare gli utenti nelle procedure.
