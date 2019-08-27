---
title: Layout della shell Xamarin.Forms
description: Le applicazioni shell rispettano il ciclo di vita di Xamarin.Forms. Quando una pagina sta per essere visualizzata sullo schermo, viene generato un evento Appearing. Quando invece una pagina sta per scomparire dallo schermo, viene generato un evento Disappearing.
ms.prod: xamarin
ms.assetid: 4E4EE50E-3BB4-441D-8355-CD9CD26ED1D0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2019
ms.openlocfilehash: c008cc29d2ceae073459766597040af653329d71
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69893957"
---
# <a name="xamarinforms-shell-lifecycle"></a>Layout della shell Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

Le applicazioni shell rispettano il ciclo di vita di Xamarin.Forms. Quando una pagina sta per essere visualizzata sullo schermo, viene generato un evento `Appearing`. Quando invece una pagina sta per scomparire dallo schermo, viene generato un evento `Disappearing`. Questi eventi vengono propagati alle pagine e possono essere gestiti tramite l'override del metodo [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) o [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) nella pagina.

> [!NOTE]
> In un'applicazione shell, gli eventi `Appearing` e `Disappearing` vengono generati dal codice multipiattaforma, prima che il codice della piattaforma renda visibile una pagina o rimuova una pagina dalla schermata.

Per altre informazioni sul ciclo di vita dell'app Xamarin.Forms, vedere [Ciclo di vita delle app Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

## <a name="hierarchical-navigation"></a>Navigazione gerarchica

In un'applicazione shell, il push di una pagina nello stack di navigazione avrà come risultato l'oggetto `ShellContent` attualmente visibile e il relativo contenuto di pagina, generando l'evento `Disappearing`. Analogamente, il prelievo dell'ultima pagina dallo stack di navigazione avrà come risultato il nuovo oggetto visibile `ShellContent` e il relativo contenuto di pagina, generando l'evento `Appearing`.

Per altre informazioni sulla navigazione gerarchica, vedere [Navigazione gerarchica in Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="modal-navigation"></a>Navigazione modale

In un'applicazione shell, il push di una pagina modale nello stack di navigazione modale avrà come risultato tutti gli oggetti visibili della shell, generando l'evento `Disappearing`. Analogamente, il prelievo dell'ultima pagina modale dallo stack di navigazione modale avrà come risultato tutti gli oggetti visibili della shell, generando l'evento `Appearing`.

Per altre informazioni sulla navigazione modale, vedere [Pagine modali Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
- [Ciclo di vita delle app Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md)
- [Pagine modali Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md)
