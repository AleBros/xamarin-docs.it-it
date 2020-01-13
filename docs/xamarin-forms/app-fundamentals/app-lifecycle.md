---
title: Ciclo di vita delle app Xamarin.Forms
description: Questo articolo illustra come rispondere al ciclo di vita dell'applicazione, inclusi i metodi del ciclo di vita, gli eventi di notifica per le pagine e gli eventi di spostamento modale.
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 41e8d073982bf7963b3a77a939bf28e52e86feaa
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675186"
---
# <a name="xamarinforms-app-lifecycle"></a>Ciclo di vita delle app Xamarin.Forms

La classe di base [`Application`](xref:Xamarin.Forms.Application) offre le funzionalità seguenti:

- [Metodi del ciclo di vita](#Lifecycle_Methods) `OnStart`, `OnSleep` e `OnResume`.
- [Eventi di spostamento tra le pagine](#page) [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing), [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing).
- [Eventi di spostamento modale](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping` e `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Metodi del ciclo di vita

La classe [`Application`](xref:Xamarin.Forms.Application) contiene tre metodi virtuali che possono essere sottoposti a override per rispondere alle modifiche del ciclo di vita:

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

La classe [`Application`](xref:Xamarin.Forms.Application) include due eventi che inviano notifica della comparsa e della scomparsa delle pagine:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) - Evento generato quando una pagina sta per apparire sullo schermo.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) - Evento generato quando una pagina sta per scomparire dallo schermo.

Questi eventi possono essere usati in scenari in cui si vuole tenere traccia delle pagine man mano che vengono visualizzate sullo schermo.

> [!NOTE]
> Gli eventi [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) e [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) vengono generati dalla classe di base [`Page`](xref:Xamarin.Forms.Page) subito dopo gli eventi [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) e [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing), rispettivamente.

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventi di spostamento modale

Esistono quattro eventi nella classe [`Application`](xref:Xamarin.Forms.Application), ognuno con argomenti propri, che consentono di rispondere alla visualizzazione e alla chiusura di pagine modali:

- `ModalPushing` - generato in caso di push modale di una pagina.
- `ModalPushed` - generato dopo il push modale di una pagina.
- `ModalPopping` - generato in caso di prelievo modale di una pagina.
- `ModalPopped` - generato dopo il prelievo modale di una pagina.

> [!NOTE]
> Gli argomenti dell'evento `ModalPopping`, di tipo `ModalPoppingEventArgs`, contengono una proprietà `Cancel`. Quando `Cancel` è impostata su `true` il prelievo modale viene annullato.
