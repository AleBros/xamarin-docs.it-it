---
title: Spostamento di xamarin. Forms
description: Questa guida illustra come eseguire la navigazione nelle App xamarin. Forms. Xamarin. Forms fornisce una serie di esperienze di navigazione pagina diversa, in base al tipo di pagina utilizzato.
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 202f044ebd7dd5b110b94d2aa60eeb7151150607
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994728"
---
# <a name="xamarinforms-navigation"></a>Spostamento di xamarin. Forms

_Xamarin. Forms fornisce una serie di esperienze di navigazione pagina diversa, in base al tipo di pagina utilizzato._

![](images/page-types.png "Tipi di pagine di xamarin. Forms")

## <a name="hierarchical-navigationhierarchicalmd"></a>[Navigazione gerarchica](hierarchical.md)

La classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) offre un'esperienza di navigazione gerarchica in cui l'utente è in grado di scorrere le pagine avanti e indietro in base alle esigenze. La classe implementa la navigazione come stack LIFO (Last-In, First-Out) di oggetti [`Page`](xref:Xamarin.Forms.Page).

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

Xamarin. Forms [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) è costituito da un elenco di schede e un'area più grande di dettaglio, con ogni scheda di caricamento del contenuto nell'area dei dettagli.

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

Xamarin. Forms [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) è una pagina che gli utenti possono scorrere verso destra per scorrere le pagine di contenuto, ad esempio una raccolta.

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

Xamarin. Forms [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) è una pagina che gestisce due pagine di informazioni correlate, ovvero una pagina master che presenta elementi e una pagina di dettaglio che presenta i dettagli relativi agli elementi della pagina master.

## <a name="modal-pagesmodalmd"></a>[Pagine modali](modal.md)

Xamarin. Forms fornisce inoltre supporto per le pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata.

## <a name="displaying-pop-upspop-upsmd"></a>[Visualizzazione di popup](pop-ups.md)

Xamarin. Forms offre due elementi dell'interfaccia utente a comparsa-iscrizione: un avviso e un foglio di azione. Questi elementi dell'interfaccia utilizzabile per porre semplici domande degli utenti e per guidare gli utenti tramite le attività.
