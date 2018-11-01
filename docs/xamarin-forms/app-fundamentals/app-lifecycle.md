---
title: Ciclo di vita App xamarin. Forms
description: Questo articolo illustra come rispondere al ciclo di vita dell'applicazione, inclusi i metodi del ciclo di vita, gli eventi di navigazione pagina ed eventi di navigazione modale.
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 5bdce8d1752b3d7273ffec233b120266909999c4
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675120"
---
# <a name="xamarinforms-app-lifecycle"></a>Ciclo di vita App xamarin. Forms

Il [ `Application` ](xref:Xamarin.Forms.Application) classe di base offre le funzionalità seguenti:

* [I metodi del ciclo di vita](#Lifecycle_Methods) `OnStart`, `OnSleep`, e `OnResume`.
* [Pagina eventi di spostamento](#page) [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing), [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing).
* [Gli eventi di navigazione modale](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping`, e `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Metodi del ciclo di vita

Il [ `Application` ](xref:Xamarin.Forms.Application) classe contiene tre metodi virtuali che possono essere sottoposto a override per gestire i metodi del ciclo di vita:

* **OnStart** -chiamato all'avvio dell'applicazione.

* **OnSleep** -chiamato ogni volta che l'applicazione passa allo sfondo.

* **OnResume** -viene chiamato quando l'applicazione viene ripreso, dopo l'invio allo sfondo.

Si noti che è presente *alcun* metodo per la chiusura dell'applicazione.
In circostanze normali (ovvero non un arresto anomalo) la terminazione dell'applicazione verrà eseguito dal *OnSleep* dello stato, senza eventuali notifiche aggiuntive che il codice.

Per osservare quando questi metodi vengono chiamati, implementare un `WriteLine` chiamare in ognuno (come illustrato di seguito) e di test in ogni piattaforma.

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

Quando si aggiornano *meno recente* (ad es le applicazioni xamarin. Forms. creare con xamarin. Forms 1.3 o versioni precedenti), assicurarsi che l'attività principale Android includa `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` nella `[Activity()]` attributo. Se non è presente osserveranno i `OnStart` metodo viene chiamato in rotazione nonché al primo avvio dell'applicazione. Questo attributo viene inclusa automaticamente nei modelli di app xamarin. Forms correnti.

<a name="page" />

## <a name="page-navigation-events"></a>Eventi di navigazione a pagina

Sono disponibili due eventi nel [ `Application` ](xref:Xamarin.Forms.Application) classi che forniscono una notifica di pagine Capture e visualizzati:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) -generato quando una pagina sta per essere visualizzati sullo schermo.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) -generato quando una pagina sta per essere scompare dallo schermo.

Questi eventi possono essere usati in scenari in cui si desidera tenere traccia delle pagine man mano che vengono visualizzati sullo schermo.

> [!NOTE]
> Il [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing) e [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing) gli eventi vengono generati dal [ `Page` ](xref:Xamarin.Forms.Page) classe di base immediatamente dopo il [ `Page.Appearing` ](xref:Xamarin.Forms.Page.Appearing) e [ `Page.Disappearing` ](xref:Xamarin.Forms.Page.Disappearing) eventi, rispettivamente.

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventi di navigazione modale

Esistono quattro eventi sul [ `Application` ](xref:Xamarin.Forms.Application) (classe), ognuno con i propri argomenti dell'evento, che consentono di rispondere alle pagine modali viene visualizzato e verrà eliminato:

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** - il `ModalPoppingEventArgs` classe contiene un `Cancel` proprietà. Quando `Cancel` è impostata su `true` popup modale è stato annullato.
* **ModalPopped** - `ModalPoppedEventArgs`

> [!NOTE]
> Per implementare i metodi del ciclo di vita delle applicazioni e gli eventi di navigazione modale, tutti i pre-`Application` metodi di creazione di un'app xamarin. Forms (ad esempio le applicazioni scritte nella versione 1.2 o meno recenti che usano un valore statico `GetMainPage` metodo) sono stati aggiornati per creare un impostazione predefinita `Application` che è impostato come padre di `MainPage`.
>
> Le applicazioni xamarin. Forms che usano questo comportamento legacy devono essere aggiornate a un' `Application` sottoclasse come descritto nel [classe Application](~/xamarin-forms/app-fundamentals/application-class.md) pagina.
