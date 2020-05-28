---
title: Xamarin.FormsCiclo di vita della shell
description: Le applicazioni shell rispettano il ciclo di vita Xamarin.Forms e viene generato un evento di visualizzazione quando una pagina sta per essere visualizzata sullo schermo e viene generato un evento di scomparsa quando una pagina sta per scomparire dalla schermata.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3a7a46187d861098b61f638a3fb460d890b081dd
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138723"
---
# <a name="xamarinforms-shell-lifecycle"></a>Xamarin.FormsCiclo di vita della shell

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Le applicazioni shell rispettano il ciclo di vita Xamarin.Forms e un `Appearing` evento viene generato quando una pagina sta per essere visualizzata sullo schermo e `Disappearing` viene generato un evento quando una pagina sta per scomparire dalla schermata. Questi eventi vengono propagati alle pagine e possono essere gestiti eseguendo l'override dei [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) metodi o nella pagina.

> [!NOTE]
> In un'applicazione shell, gli eventi `Appearing` e `Disappearing` vengono generati dal codice multipiattaforma, prima che il codice della piattaforma renda visibile una pagina o rimuova una pagina dalla schermata.

Per altre informazioni sul ciclo di vita dell' Xamarin.Forms app, vedere [ Xamarin.Forms ciclo](~/xamarin-forms/app-fundamentals/app-lifecycle.md)di vita dell'app.

## <a name="hierarchical-navigation"></a>Spostamento gerarchico

In un'applicazione shell, il push di una pagina nello stack di navigazione avrà come risultato l'oggetto `ShellContent` attualmente visibile e il relativo contenuto di pagina, generando l'evento `Disappearing`. Analogamente, il prelievo dell'ultima pagina dallo stack di navigazione avrà come risultato il nuovo oggetto visibile `ShellContent` e il relativo contenuto di pagina, generando l'evento `Appearing`.

Per ulteriori informazioni sulla navigazione gerarchica, vedere la pagina relativa alla [ Xamarin.Forms navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="modal-navigation"></a>Navigazione modale

In un'applicazione shell, il push di una pagina modale nello stack di navigazione modale avrà come risultato tutti gli oggetti visibili della shell, generando l'evento `Disappearing`. Analogamente, il prelievo dell'ultima pagina modale dallo stack di navigazione modale avrà come risultato tutti gli oggetti visibili della shell, generando l'evento `Appearing`.

Per ulteriori informazioni sull'esplorazione modale, vedere [ Xamarin.Forms pagine modali](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.FormsCiclo di vita dell'app](~/xamarin-forms/app-fundamentals/app-lifecycle.md)
- [Xamarin.FormsPagine modali](~/xamarin-forms/app-fundamentals/navigation/modal.md)
