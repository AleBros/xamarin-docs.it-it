---
title: Ciclo di vita delle app Xamarin.Forms
description: Questo articolo illustra come rispondere al ciclo di vita dell'applicazione, inclusi i metodi del ciclo di vita, gli eventi di spostamento tra le pagine e gli eventi di spostamento modale.
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 5bdce8d1752b3d7273ffec233b120266909999c4
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675120"
---
# <a name="xamarinforms-app-lifecycle"></a>Ciclo di vita delle app Xamarin.Forms

La classe di base [`Application`](xref:Xamarin.Forms.Application) offre le funzionalità seguenti:

* [Metodi del ciclo di vita](#Lifecycle_Methods) `OnStart`, `OnSleep` e `OnResume`.
* [Eventi di spostamento tra le pagine](#page) [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing), [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing).
* [Eventi di spostamento modale](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping` e `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Metodi del ciclo di vita

La classe [`Application`](xref:Xamarin.Forms.Application) contiene tre metodi virtuali che possono essere sottoposti a override per gestire i metodi del ciclo di vita:

* **OnStart** - Chiamato all'avvio dell'applicazione.

* **OnSleep** - Chiamato ogni volta che l'applicazione passa in background.

* **OnResume** - Chiamato alla ripresa dell'applicazione, dopo il passaggio in background.

Si noti che *non* è disponibile alcun metodo per la terminazione dell'applicazione.
In circostanze normali, ovvero non in caso di arresto anomalo, la terminazione dell'applicazione verrà eseguita dallo stato *OnSleep*, senza l'invio di notifiche aggiuntive al codice.

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

Quando si aggiornano applicazioni Xamarin.Forms *meno recenti*, ad esempio create con Xamarin.Forms 1.3 o versioni precedenti), assicurarsi che l'attività principale Android includa `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` nell'attributo `[Activity()]`. Se non è presente, si noterà che il metodo `OnStart` viene chiamato a rotazione nonché al primo avvio dell'applicazione. Questo attributo viene incluso automaticamente nei modelli di app Xamarin.Forms correnti.

<a name="page" />

## <a name="page-navigation-events"></a>Eventi di spostamento tra le pagine

La classe [`Application`](xref:Xamarin.Forms.Application) include due eventi che inviano notifica della comparsa e della scomparsa delle pagine:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) - Evento generato quando una pagina sta per apparire sullo schermo.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) - Evento generato quando una pagina sta per scomparire dallo schermo.

Questi eventi possono essere usati in scenari in cui si vuole tenere traccia delle pagine man mano che vengono visualizzate sullo schermo.

> [!NOTE]
> Gli eventi [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) e [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) vengono generati dalla classe di base [`Page`](xref:Xamarin.Forms.Page) subito dopo gli eventi [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) e [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing), rispettivamente.

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventi di spostamento modale

Esistono quattro eventi nella classe [`Application`](xref:Xamarin.Forms.Application), ognuno con argomenti propri, che consentono di rispondere alla visualizzazione e alla chiusura di pagine modali:

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** - La classe `ModalPoppingEventArgs` contiene una proprietà `Cancel`. Quando `Cancel` è impostata su `true` il prelievo modale viene annullato.
* **ModalPopped** - `ModalPoppedEventArgs`

> [!NOTE]
> Per implementare i metodi del ciclo di vita delle applicazioni e gli eventi di spostamento modale, tutti i metodi pre-`Application` di creazione di un'app Xamarin.Forms (ad esempio le applicazioni scritte nella versione 1.2 o meno recenti che usano un metodo `GetMainPage` statico) sono stati aggiornati per creare una classe `Application` predefinita impostata come padre di `MainPage`.
>
> Le applicazioni Xamarin.Forms che usano questo comportamento legacy devono essere aggiornate a una sottoclasse `Application` come descritto nella pagina [Classe Application](~/xamarin-forms/app-fundamentals/application-class.md).
