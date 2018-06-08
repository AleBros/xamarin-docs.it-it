---
title: Ciclo di vita App
description: Come rispondere a ciclo di vita dell'applicazione
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: a22ad8f3f272212f5c7f088ba2112f2771ff4a7f
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846344"
---
# <a name="app-lifecycle"></a>Ciclo di vita App

Il [ `Application` ](xref:Xamarin.Forms.Application) classebase offre le funzionalità seguenti:

* [I metodi del ciclo di vita](#Lifecycle_Methods) `OnStart`, `OnSleep`, e `OnResume`.
* [Pagina eventi di spostamento](#page) [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing), [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing).
* [Gli eventi di spostamento modale](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping`, e `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Metodi del ciclo di vita

Il [ `Application` ](xref:Xamarin.Forms.Application) classe contiene tre metodi virtuali che possono essere sostituiti per gestire i metodi del ciclo di vita:

* **OnStart** -chiamato all'avvio dell'applicazione.

* **OnSleep** -chiamato ogni volta che l'applicazione passa allo sfondo.

* **OnResume** -chiamato quando l'applicazione viene ripreso, dopo l'invio allo sfondo.

Si noti che è *non* metodo per la chiusura dell'applicazione.
In circostanze normali (ie. non è un arresto anomalo) la terminazione dell'applicazione avrà luogo dal *OnSleep* dello stato, senza alcuna notifica aggiuntive al codice.

Per osservare quando questi metodi vengono chiamati, implementare un `WriteLine` chiamata in ogni (come illustrato di seguito) e al test in ogni piattaforma.

```csharp
protected override void OnStart()
{
    Debug.WriteLine ("OnStart");
}
protected override void OnSleep()
{
    Debug.WriteLine ("OnSleep");
}
protected override void OnResume()
{
    Debug.WriteLine ("OnResume");
}
```

Quando si aggiorna *precedente* xamarin. Forms applicazioni (ad es. creare con xamarin. Forms 1.3 o versioni precedenti), assicurarsi che includa l'attività principale Android `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` nel `[Activity()]` attributo. Se non è presente sarà possibile osservare il `OnStart` metodo viene chiamato sul rotazione e al primo avvio dell'applicazione. Questo attributo viene inclusa automaticamente nei modelli di app xamarin. Forms correnti.

<a name="page" />

## <a name="page-navigation-events"></a>Eventi di navigazione a pagina

Esistono due eventi nel [ `Application` ](xref:Xamarin.Forms.Application) classe che forniscono la notifica di pagine Capture e visualizzati:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) -generato quando una pagina viene visualizzato sullo schermo.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) -generato quando una pagina sta per essere scompaiano dalla schermata.

Questi eventi possono essere utilizzati in scenari in cui si desidera tenere traccia delle pagine come essi vengono visualizzati sullo schermo.

> [!NOTE]
> Il [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing) e [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing) da cui vengono generati eventi il [ `Page` ](xref:Xamarin.Forms.Page) classe di base immediatamente dopo il [ `Page.Appearing` ](xref:Xamarin.Forms.Page.Appearing) e [ `Page.Disappearing` ](xref:Xamarin.Forms.Page.Disappearing) degli eventi, rispettivamente.

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventi di spostamento modale

Esistono quattro eventi sul [ `Application` ](xref:Xamarin.Forms.Application) (classe), ciascuno con i propri argomenti dell'evento, che consentono di rispondere alle pagine modale viene visualizzato e verrà eliminato:

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** - `ModalPoppingEventArgs` classe contiene un `Cancel` proprietà. Quando `Cancel` è impostato su `true` pop modale è stata annullata.
* **ModalPopped** - `ModalPoppedEventArgs`

> [!NOTE]
> Per implementare i metodi del ciclo di vita delle applicazioni e gli eventi di spostamento modale, tutti pre-`Application` metodi di creazione di un'app xamarin. Forms (ie. le applicazioni scritte nella versione 1.2 o precedente che utilizzano un valore statico `GetMainPage` metodo) sono stati aggiornati per creare un predefinito `Application` questo valore è impostato come elemento padre di `MainPage`.
>
> Le applicazioni di xamarin. Forms che utilizzano questo comportamento legacy devono essere aggiornate a un `Application` sottoclasse come descritto nel [classe Application](~/xamarin-forms/app-fundamentals/application-class.md) pagina.
