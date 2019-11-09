---
title: Funzioni di interpolazione in Novell. Forms
description: Novell. Forms include una classe di interpolazione che consente di specificare una funzione di trasferimento che controlla il modo in cui le animazioni accelerano o rallentano durante l'esecuzione. In questo articolo viene illustrato come utilizzare le funzioni di interpolazione predefinite e come creare funzioni di interpolazione personalizzate.
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 56ea31d1e1be8bbad4a27dd7ffd844aa03f75bbb
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842825"
---
# <a name="easing-functions-in-xamarinforms"></a>Funzioni di interpolazione in Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)

_Novell. Forms include una classe di interpolazione che consente di specificare una funzione di trasferimento che controlla il modo in cui le animazioni accelerano o rallentano durante l'esecuzione. In questo articolo viene illustrato come utilizzare le funzioni di interpolazione predefinite e come creare funzioni di interpolazione personalizzate._

La classe [`Easing`](xref:Xamarin.Forms.Easing) definisce numerose funzioni di interpolazione che possono essere utilizzate dalle animazioni:

- La funzione di interpolazione [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) rimbalza all'inizio dell'animazione.
- La funzione di interpolazione [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut) rimbalza l'animazione alla fine.
- La funzione di interpolazione [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn) accelera lentamente l'animazione.
- La funzione di interpolazione [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut) accelera l'animazione all'inizio e decelera l'animazione alla fine.
- La funzione di interpolazione [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut) rallenta rapidamente l'animazione.
- La funzione di interpolazione [`Linear`](xref:Xamarin.Forms.Easing.Linear) utilizza una velocità costante e è la funzione di interpolazione predefinita.
- La funzione di interpolazione [`SinIn`](xref:Xamarin.Forms.Easing.SinIn) accelera in modo semplice l'animazione.
- La funzione di interpolazione [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut) accelera in modo semplice l'animazione all'inizio e rallenta in modo uniforme l'animazione alla fine.
- La funzione di interpolazione [`SinOut`](xref:Xamarin.Forms.Easing.SinOut) rallenta in modo graduale l'animazione.
- La funzione di interpolazione [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) fa sì che l'animazione acceleri molto rapidamente verso la fine.
- La funzione di interpolazione [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) fa in modo che l'animazione rallenti rapidamente verso la fine.

I suffissi `In` e `Out` indicano se l'effetto fornito dalla funzione di interpolazione è evidente all'inizio dell'animazione, alla fine o a entrambi.

Inoltre, è possibile creare funzioni di interpolazione personalizzate. Per altre informazioni, vedere [funzioni di interpolazione personalizzate](#customeasing).

## <a name="consuming-an-easing-function"></a>Utilizzo di una funzione di interpolazione

I metodi di estensione dell'animazione nella classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) consentono di specificare una funzione di interpolazione come parametro finale del metodo, come illustrato nell'esempio di codice seguente:

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

Specificando una funzione di interpolazione per un'animazione, la velocità dell'animazione diventa non lineare e produce l'effetto fornito dalla funzione di interpolazione. Se si omette una funzione di interpolazione quando si crea un'animazione, l'animazione utilizzerà la funzione di interpolazione [`Linear`](xref:Xamarin.Forms.Easing.Linear) predefinita, che produce una velocità lineare.

Per ulteriori informazioni sull'utilizzo dei metodi di estensione dell'animazione nella classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) , vedere [animazioni semplici](~/xamarin-forms/user-interface/animation/simple.md). Le funzioni di interpolazione possono anche essere utilizzate dalla classe [`Animation`](xref:Xamarin.Forms.Animation) . Per altre informazioni, vedere [animazioni personalizzate](~/xamarin-forms/user-interface/animation/custom.md).

<a name="customeasing" />

## <a name="custom-easing-functions"></a>Funzioni di interpolazione personalizzate

Sono disponibili tre approcci principali per la creazione di una funzione di interpolazione personalizzata:

1. Creare un metodo che accetta un argomento `double` e restituisce un risultato `double`.
1. Creare un oggetto `Func<double, double>`.
1. Specificare la funzione di interpolazione come argomento per il costruttore [`Easing`](xref:Xamarin.Forms.Easing) .

In tutti e tre i casi, la funzione di interpolazione personalizzata deve restituire 0 per un argomento di 0 e 1 per un argomento di 1. Tuttavia, qualsiasi valore può essere restituito tra i valori di argomento 0 e 1. Ogni approccio verrà ora discusso a turno.

### <a name="custom-easing-method"></a>Metodo di interpolazione personalizzato

Una funzione di interpolazione personalizzata può essere definita come metodo che accetta un argomento `double` e restituisce un risultato `double`, come illustrato nell'esempio di codice seguente:

```csharp
double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}

await image.TranslateTo(0, 200, 2000, (Easing)CustomEase);
```

Il metodo `CustomEase` tronca il valore in ingresso ai valori 0, 0,2, 0,4, 0,6, 0,8 e 1. Pertanto, l'istanza di [`Image`](xref:Xamarin.Forms.Image) viene convertita in salti discreti, anziché in modo semplice.

### <a name="custom-easing-func"></a>Funzione di interpolazione personalizzata

È anche possibile definire una funzione di interpolazione personalizzata come `Func<double, double>`, come illustrato nell'esempio di codice seguente:

```csharp
Func<double, double> CustomEaseFunc = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEaseFunc);
```

Il `CustomEaseFunc` rappresenta una funzione di interpolazione che inizia velocemente, rallenta e inverte il corso, quindi inverte di nuovo il corso per accelerare rapidamente verso la fine. Pertanto, mentre lo spostamento generale dell'istanza di [`Image`](xref:Xamarin.Forms.Image) è verso il basso, viene anche invertito temporaneamente il corso a metà dell'animazione.

### <a name="custom-easing-constructor"></a>Costruttore di interpolazione personalizzato

È anche possibile definire una funzione di interpolazione personalizzata come argomento del costruttore [`Easing`](xref:Xamarin.Forms.Easing) , come illustrato nell'esempio di codice seguente:

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

La funzione di interpolazione personalizzata viene specificata come argomento della funzione lambda per il costruttore di [`Easing`](xref:Xamarin.Forms.Easing) e usa il metodo `Math.Cos` per creare un effetto di rilascio lento che è smorzato dal metodo di `Math.Exp`. Pertanto, l'istanza di [`Image`](xref:Xamarin.Forms.Image) viene convertita in modo che sembri rilasciarla nel punto di riposo finale.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come utilizzare le funzioni di interpolazione predefinite e come creare funzioni di interpolazione personalizzate. Novell. Forms include una classe [`Easing`](xref:Xamarin.Forms.Easing) che consente di specificare una funzione di trasferimento che controlla il modo in cui le animazioni accelerano o rallentano durante l'esecuzione.

## <a name="related-links"></a>Collegamenti correlati

- [Panoramica del supporto asincrono](~/cross-platform/platform/async.md)
- [Funzioni di interpolazione (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)
- [Interpolazione](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
