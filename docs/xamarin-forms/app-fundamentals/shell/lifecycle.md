---
title: Layout della shell Xamarin.Forms
description: Le applicazioni shell rispettano il ciclo di vita di Xamarin.Forms. Quando una pagina sta per essere visualizzata sullo schermo, viene generato un evento Appearing. Quando invece una pagina sta per scomparire dallo schermo, viene generato un evento Disappearing.
ms.prod: xamarin
ms.assetid: 4E4EE50E-3BB4-441D-8355-CD9CD26ED1D0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2019
ms.openlocfilehash: 2ed51763b5866c15e91d88a6a1a58c7285fb5973
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "72749761"
---
# <a name="xamarinforms-shell-lifecycle"></a>Layout della shell Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Le applicazioni shell rispettano il ciclo di vita di Xamarin.Forms. Quando una pagina sta per essere visualizzata sullo schermo, viene generato un evento `Appearing`. Quando invece una pagina sta per scomparire dallo schermo, viene generato un evento `Disappearing`. Questi eventi vengono propagati alle pagine e possono essere [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) gestiti eseguendo l'override dei metodi o nella pagina.

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

- [Xaminals (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Ciclo di vita delle app Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md)
- [Pagine modali Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md)
