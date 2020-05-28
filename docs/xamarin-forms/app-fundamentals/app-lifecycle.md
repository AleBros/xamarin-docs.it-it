---
title: Xamarin.FormsCiclo di vita dell'app
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3793a54f04b2c028752e18e2a5a238c275c2958a
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84129675"
---
# <a name="xamarinforms-app-lifecycle"></a>Xamarin.FormsCiclo di vita dell'app

La [`Application`](xref:Xamarin.Forms.Application) classe base fornisce le funzionalità seguenti:

- [Metodi del ciclo](#Lifecycle_Methods) di vita `OnStart` , `OnSleep` e `OnResume` .
- [Eventi di navigazione pagina](#page) [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) , [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) .
- [Eventi di navigazione modale](#modal) `ModalPushing` ,, `ModalPushed` `ModalPopping` e `ModalPopped` .

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Metodi del ciclo di vita

La [`Application`](xref:Xamarin.Forms.Application) classe contiene tre metodi virtuali di cui è possibile eseguire l'override per rispondere alle modifiche del ciclo di vita:

- `OnStart` - chiamato all'avvio dell'applicazione.
- `OnSleep` - chiamato ogni volta che l'applicazione passa in background.
- `OnResume` - chiamato alla ripresa dell'applicazione, dopo il passaggio in background.

> [!NOTE]
> *Non* è disponibile alcun metodo per la terminazione dell'applicazione. In circostanze normali, ovvero non in caso di arresto anomalo, la terminazione dell'applicazione verrà eseguita dallo stato *OnSleep*, senza l'invio di notifiche aggiuntive al codice.

Per rilevare quando questi metodi vengono chiamati, implementare una chiamata `WriteLine` in ognuno (come illustrato di seguito) ed eseguire test in ogni piattaforma.

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

> [!IMPORTANT]
> In Android il metodo `OnStart` verrà chiamato a rotazione oltre che al primo avvio dell'applicazione, se l'attività principale non include `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` nell'attributo `[Activity()]`.

<a name="page" />

## <a name="page-notification-events"></a>Eventi di notifica per le pagine

Nella classe sono presenti due eventi [`Application`](xref:Xamarin.Forms.Application) che forniscono la notifica di pagine che appaiono e scompaiono:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing): generato quando una pagina sta per essere visualizzata sullo schermo.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing): generato quando una pagina sta per scomparire dalla schermata.

Questi eventi possono essere usati in scenari in cui si vuole tenere traccia delle pagine man mano che vengono visualizzate sullo schermo.

> [!NOTE]
> Gli [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) eventi e vengono generati dalla [`Page`](xref:Xamarin.Forms.Page) classe base immediatamente dopo gli [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) eventi e [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) , rispettivamente.

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventi di spostamento modale

Sono presenti quattro eventi sulla [`Application`](xref:Xamarin.Forms.Application) classe, ognuno con i rispettivi argomenti dell'evento, che consentono di rispondere alle pagine modali visualizzate e rilasciate:

- `ModalPushing` - generato in caso di push modale di una pagina.
- `ModalPushed` - generato dopo il push modale di una pagina.
- `ModalPopping` - generato in caso di prelievo modale di una pagina.
- `ModalPopped` - generato dopo il prelievo modale di una pagina.

> [!NOTE]
> Gli argomenti dell'evento `ModalPopping`, di tipo `ModalPoppingEventArgs`, contengono una proprietà `Cancel`. Quando `Cancel` è impostata su `true` il prelievo modale viene annullato.
