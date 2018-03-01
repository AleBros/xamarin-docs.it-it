---
title: Ciclo di vita App
description: Come rispondere a ciclo di vita dell'applicazione
ms.topic: article
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: c374f261677a5bddc4ea9c73c066b69580ceedd5
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="app-lifecycle"></a>Ciclo di vita App

La `Application` classe di base offre le funzionalità seguenti:

* [I metodi del ciclo di vita](#Lifecycle_Methods) `OnStart`, `OnSleep`, e `OnResume`.
* [Gli eventi di spostamento modale](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping`, e `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Metodi del ciclo di vita

La `Application` classe contiene tre metodi virtuali che possono essere sostituiti per gestire i metodi del ciclo di vita:

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

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventi di spostamento modale

Non sono presenti quattro nuovi eventi nel `Application` classe 1.4 xamarin. Forms, ciascuno con i propri argomenti dell'evento:

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** - `ModalPoppingEventArgs` classe contiene un `Cancel` proprietà. Quando `Cancel` è impostato su `true` pop modale è stata annullata.
* **ModalPopped** - `ModalPoppedEventArgs`

Questi eventi consente di migliorare la gestione del ciclo di vita dell'applicazione, consentendo rispondere a modale pagine viene visualizzato e verrà eliminato.

> [!NOTE]
> Per implementare i metodi del ciclo di vita delle applicazioni e gli eventi di spostamento modale, tutti pre-`Application` metodi di creazione di un'app xamarin. Forms (ie. le applicazioni scritte nella versione 1.2 o precedente che utilizzano un valore statico `GetMainPage` metodo) sono stati aggiornati per creare un predefinito `Application` questo valore è impostato come elemento padre di `MainPage`.
>
> Le applicazioni di xamarin. Forms che utilizzano questo comportamento legacy devono essere aggiornate a un `Application` sottoclasse come descritto nel [classe Application](~/xamarin-forms/app-fundamentals/application-class.md) pagina.
