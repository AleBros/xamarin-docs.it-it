---
title: Spostamento di xamarin. Forms
description: Questa guida illustra come eseguire navigazione nelle App xamarin. Forms. Xamarin. Forms fornisce una serie di esperienze di navigazione pagina diversa, in base al tipo di pagina utilizzato.
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 90aedee42af7ed1788110e832fb3b435d870ee77
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241956"
---
# <a name="xamarinforms-navigation"></a>Spostamento di xamarin. Forms

_Xamarin. Forms fornisce una serie di esperienze di navigazione pagina diversa, in base al tipo di pagina utilizzato._

![](images/page-types.png "Tipi di pagine di xamarin. Forms")

## <a name="hierarchical-navigationhierarchicalmd"></a>[Navigazione gerarchica](hierarchical.md)

La classe [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) offre un'esperienza di navigazione gerarchica in cui l'utente è in grado di scorrere le pagine avanti e indietro in base alle esigenze. La classe implementa la navigazione come stack LIFO (Last-In, First-Out) di oggetti [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/).

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

Di xamarin. Forms [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) è costituito da un elenco di schede e un'area più grande di dettaglio, con ogni scheda, il caricamento del contenuto nell'area dei dettagli.

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

Di xamarin. Forms [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) è una pagina che gli utenti possono scorrere verso destra per spostarsi tra le pagine di contenuto, ad esempio una raccolta.

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

Di xamarin. Forms [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) è una pagina che gestisce due pagine di informazioni correlate: una pagina master che presenta elementi e una pagina di dettaglio che visualizza i dettagli sugli elementi della pagina master.

## <a name="modal-pagesmodalmd"></a>[Pagine modali](modal.md)

Xamarin. Forms fornisce anche supporto per le pagine modale. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata.

## <a name="displaying-pop-upspop-upsmd"></a>[Visualizzazione di popup](pop-ups.md)

Xamarin. Forms fornisce due elementi dell'interfaccia utente simile pop configurazione: un avviso e un foglio di azione. Per richiedere agli utenti semplici domande e per guidare gli utenti attraverso le attività, è possono utilizzare questi elementi di interfaccia.
