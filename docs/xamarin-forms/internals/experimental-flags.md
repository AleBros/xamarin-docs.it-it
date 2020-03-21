---
title: Flag sperimentali Novell. Forms
description: I flag sperimentali Novell. Forms consentono al team di progettazione di fornire nuove funzionalità agli utenti in modo più rapido, pur continuando a modificare le API delle funzionalità prima di passare a una versione stabile.
ms.prod: xamarin
ms.assetid: AF4BDD27-89F6-48AE-A8CD-D7E4DDA2CCA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2020
ms.openlocfilehash: cebb996da992058616f9cf96ef3212c9ce27022a
ms.sourcegitcommit: 6c60914b380ff679bbffd7790edd4d5e18005d0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2020
ms.locfileid: "80112589"
---
# <a name="xamarinforms-experimental-flags"></a>Flag sperimentali Novell. Forms

Quando viene implementata una nuova funzionalità di Novell. Forms, a volte si trova dietro un flag sperimentale. Questo consente al team di progettazione di fornire nuove funzionalità in modo più rapido, pur continuando a modificare le API delle funzionalità prima di passare a una versione stabile. Il flag sperimentale viene quindi rimosso quando la funzionalità viene spostata in una versione stabile.

Novell. Forms include i flag sperimentali seguenti:

- `CarouselView_Experimental`
- `IndicatorView_Experimental`
- `Markup_Experimental`
- `MediaElement_Experimental`
- `Shell_UWP_Experimental`
- `StateTriggers_Experimental`
- `SwipeView_Experimental`

L'uso di funzionalità dietro un flag sperimentale richiede l'abilitazione del flag, o dei flag, nell'applicazione. Sono disponibili due approcci per l'abilitazione dei flag sperimentali:

- Abilitare il flag o i flag sperimentali nei progetti della piattaforma.
- Abilitare il flag sperimentale o i flag nella classe `App`.

> [!WARNING]
> Se si utilizza una funzionalità che si trova dietro un flag sperimentale, senza abilitare il flag, l'applicazione genera un'eccezione che indica quale flag deve essere abilitato.

## <a name="enable-flags-in-platform-projects"></a>Abilita flag nei progetti di piattaforma

Il metodo `Xamarin.Forms.Forms.SetFlags` può essere usato per abilitare un flag sperimentale nei progetti della piattaforma:

```csharp
Xamarin.Forms.Forms.SetFlags("CarouselView_Experimental");
```

Il metodo di `SetFlags` deve essere richiamato nella classe `AppDelegate` in iOS, nella classe di `MainActivity` in Android e nella classe `App` in UWP.

> [!IMPORTANT]
> L'abilitazione di un flag sperimentale nei progetti della piattaforma deve essere eseguita prima che venga richiamato il metodo `Forms.Init`.

Il metodo `Xamarin.Forms.Forms.SetFlags` accetta un argomento di matrice `string`, che rende possibile l'abilitazione di più flag sperimentali in una singola chiamata al metodo:

```csharp
Xamarin.Forms.Forms.SetFlags(new string[] { "CarouselView_Experimental", "IndicatorView_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> Non chiamare mai il metodo `SetFlags` più di una volta, in quanto le chiamate successive sovrascriveranno il risultato delle chiamate precedenti.

## <a name="enable-flags-in-your-app-class"></a>Abilitare i flag nella classe dell'app

Il metodo `Device.SetFlags` può essere usato per abilitare un flag sperimentale nella classe `App` nel progetto di codice condiviso:

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

Il metodo `Device.SetFlags` accetta un argomento `IReadOnlyList<string>`, che rende possibile l'abilitazione di più flag sperimentali in una singola chiamata al metodo:

```csharp
Device.SetFlags(new string[]{ "CarouselView_Experimental", "MediaElement_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> Non chiamare mai il metodo `SetFlags` più di una volta, in quanto le chiamate successive sovrascriveranno il risultato delle chiamate precedenti.
