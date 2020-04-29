---
title: Flag sperimentali Novell. Forms
description: I flag sperimentali Novell. Forms consentono al team di progettazione di fornire nuove funzionalità agli utenti in modo più rapido, pur continuando a modificare le API delle funzionalità prima di passare a una versione stabile.
ms.prod: xamarin
ms.assetid: AF4BDD27-89F6-48AE-A8CD-D7E4DDA2CCA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2020
ms.openlocfilehash: cca377a7a88599bc34fd66695ad303162e6be200
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82516542"
---
# <a name="xamarinforms-experimental-flags"></a>Flag sperimentali Novell. Forms

Quando viene implementata una nuova funzionalità di Novell. Forms, a volte si trova dietro un flag sperimentale. Questo consente al team di progettazione di fornire nuove funzionalità in modo più rapido, pur continuando a modificare le API delle funzionalità prima di passare a una versione stabile. Il flag sperimentale viene quindi rimosso quando la funzionalità viene spostata in una versione stabile.

Novell. Forms include i flag sperimentali seguenti:

- `AppTheme_Experimental`
- `CarouselView_Experimental`
- `Expander_Experimental`
- `IndicatorView_Experimental`
- `Markup_Experimental`
- `MediaElement_Experimental`
- `RadioButton_Experimental`
- `Shell_UWP_Experimental`
- `StateTriggers_Experimental`
- `SwipeView_Experimental`

L'uso di funzionalità dietro un flag sperimentale richiede l'abilitazione del flag, o dei flag, nell'applicazione. Sono disponibili due approcci per l'abilitazione dei flag sperimentali:

- Abilitare il flag o i flag sperimentali nei progetti della piattaforma.
- Abilitare il flag o i flag sperimentali nella `App` classe.

> [!WARNING]
> Se si utilizza una funzionalità che si trova dietro un flag sperimentale, senza abilitare il flag, l'applicazione genera un'eccezione che indica quale flag deve essere abilitato.

## <a name="enable-flags-in-platform-projects"></a>Abilita flag nei progetti di piattaforma

Il `Xamarin.Forms.Forms.SetFlags` metodo può essere usato per abilitare un flag sperimentale nei progetti della piattaforma:

```csharp
Xamarin.Forms.Forms.SetFlags("CarouselView_Experimental");
```

Il `SetFlags` metodo deve essere richiamato nella `AppDelegate` classe in iOS, nella `MainActivity` classe in Android e nella `App` classe in UWP.

> [!IMPORTANT]
> L'abilitazione di un flag sperimentale nei progetti della piattaforma `Forms.Init` deve verificarsi prima che il metodo venga richiamato.

Il `Xamarin.Forms.Forms.SetFlags` metodo accetta un `string` argomento di matrice, che rende possibile l'abilitazione di più flag sperimentali in una singola chiamata al metodo:

```csharp
Xamarin.Forms.Forms.SetFlags(new string[] { "CarouselView_Experimental", "IndicatorView_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> Non chiamare mai `SetFlags` il metodo più di una volta, in quanto le chiamate successive sovrascriveranno il risultato delle chiamate precedenti.

## <a name="enable-flags-in-your-app-class"></a>Abilitare i flag nella classe dell'app

Il `Device.SetFlags` metodo può essere usato per abilitare un flag sperimentale nella `App` classe nel progetto di codice condiviso:

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

Il `Device.SetFlags` metodo accetta un `IReadOnlyList<string>` argomento, che rende possibile l'abilitazione di più flag sperimentali in una singola chiamata al metodo:

```csharp
Device.SetFlags(new string[]{ "CarouselView_Experimental", "MediaElement_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> Non chiamare mai `SetFlags` il metodo più di una volta, in quanto le chiamate successive sovrascriveranno il risultato delle chiamate precedenti.
