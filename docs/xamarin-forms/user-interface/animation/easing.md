---
title: Funzioni di interpolazione in xamarin. Forms
description: Xamarin. Forms include una classe di interpolazione che consente di specificare una funzione di trasferimento che controlla la modalità animazioni velocizzare o rallentano poiché sono in esecuzione. Questo articolo illustra come usare le funzioni di interpolazione predefinite e come creare funzioni di interpolazione personalizzate.
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 211f56e0d9f96383670be1d60421d3ac28eabe46
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051996"
---
# <a name="easing-functions-in-xamarinforms"></a>Funzioni di interpolazione in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/easing/)

_Xamarin. Forms include una classe di interpolazione che consente di specificare una funzione di trasferimento che controlla la modalità animazioni velocizzare o rallentano poiché sono in esecuzione. Questo articolo illustra come usare le funzioni di interpolazione predefinite e come creare funzioni di interpolazione personalizzate._


Il [ `Easing` ](xref:Xamarin.Forms.Easing) classe definisce una serie di funzioni di interpolazione che possono essere usati da animazioni:

- Il [ `BounceIn` ](xref:Xamarin.Forms.Easing.BounceIn) funzione di interpolazione bounces all'inizio dell'animazione.
- Il [ `BounceOut` ](xref:Xamarin.Forms.Easing.BounceOut) funzione di interpolazione bounces alla fine dell'animazione.
- Il [ `CubicIn` ](xref:Xamarin.Forms.Easing.CubicIn) lenta alla funzione di interpolazione accelera l'animazione.
- Il [ `CubicInOut` ](xref:Xamarin.Forms.Easing.CubicInOut) funzione di interpolazione accelera l'animazione all'inizio e rallenta l'animazione alla fine.
- Il [ `CubicOut` ](xref:Xamarin.Forms.Easing.CubicOut) rapidamente la funzione di interpolazione rallenta l'animazione.
- Il [ `Linear` ](xref:Xamarin.Forms.Easing.Linear) funzione di interpolazione Usa una velocità costante ed è l'impostazione predefinita la funzione di interpolazione.
- Il [ `SinIn` ](xref:Xamarin.Forms.Easing.SinIn) la funzione di interpolazione graduale accelera l'animazione.
- Il [ `SinInOut` ](xref:Xamarin.Forms.Easing.SinInOut) la funzione di interpolazione graduale accelera l'animazione all'inizio e rallenta senza problemi alla fine dell'animazione.
- Il [ `SinOut` ](xref:Xamarin.Forms.Easing.SinOut) la funzione di interpolazione graduale rallenta l'animazione.
- Il [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn) funzione di interpolazione fa sì che l'animazione accelerare rapidamente verso la fine.
- Il [ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) funzione di interpolazione fa sì che la decelerare rapidamente verso la fine dell'animazione.

Il `In` e `Out` suffissi indicano se l'effetto fornito dalla funzione di interpolazione è evidente all'inizio dell'animazione, alla fine, o entrambi.

Inoltre, è possibile creare funzioni di interpolazione personalizzate. Per altre informazioni, vedere [funzioni di interpolazione personalizzata](#customeasing).

## <a name="consuming-an-easing-function"></a>Utilizzo di una funzione di interpolazione

Metodi di estensione animazione la [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe consente a una funzione di interpolazione essere specificato come parametro del metodo finale, come illustrato nell'esempio di codice seguente:

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

Se si specifica una funzione di interpolazione per un'animazione, la velocità dell'animazione diventa non lineari e produce l'effetto fornito dalla funzione di interpolazione. L'omissione di una funzione di interpolazione durante la creazione di un'animazione fa sì che l'animazione per usare il valore predefinito [ `Linear` ](xref:Xamarin.Forms.Easing.Linear) interpolazione funzione, che produce una velocità lineare.

Per altre informazioni sull'uso di metodi di estensione animazione la [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe, vedere [semplici animazioni](~/xamarin-forms/user-interface/animation/simple.md). Funzioni di interpolazione possono essere usate anche per il [ `Animation` ](xref:Xamarin.Forms.Animation) classe. Per altre informazioni, vedere [animazioni personalizzate](~/xamarin-forms/user-interface/animation/custom.md).

<a name="customeasing" />

## <a name="custom-easing-functions"></a>Funzioni di interpolazione personalizzata

Esistono tre approcci principali alla creazione di una funzione di interpolazione personalizzata:

1. Creare un metodo che accetta un `double` argomento e restituisce un `double` risultato.
1. Creare un oggetto `Func<double, double>`.
1. Specificare la funzione di interpolazione come argomento per il [ `Easing` ](xref:Xamarin.Forms.Easing) costruttore.

In tutti i tre casi, la funzione di interpolazione personalizzata deve restituire 0 per un argomento pari a 0 e 1 per un argomento pari a 1. Tuttavia, qualsiasi valore può essere restituito compreso tra i valori degli argomenti pari a 0 e 1. Verrà ora illustrata a sua volta ciascun approccio.

### <a name="custom-easing-method"></a>Metodo di interpolazione personalizzata

Una funzione di interpolazione personalizzata può essere definita come un metodo che accetta un `double` argomento e restituisce un `double` risultato, come illustrato nell'esempio di codice seguente:

```csharp
await image.TranslateTo(0, 200, 2000, CustomEase);

double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}
```

Il `CustomEase` metodo tronca il valore in ingresso per i valori 0, 0,2, 0,4, 0.6, 0.8 e 1. Pertanto, il [ `Image` ](xref:Xamarin.Forms.Image) istanza viene convertita in collegamenti discreti, anziché in modo uniforme.

### <a name="custom-easing-func"></a>Funzione di interpolazione personalizzata

Una funzione di interpolazione personalizzata può anche essere definita come un `Func<double, double>`, come illustrato nell'esempio di codice seguente:

```csharp
Func<double, double> CustomEase = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEase));
```

Il `CustomEase` `Func` rappresenta una funzione di interpolazione che inizia veloci e rallenta e capovolge corso, quindi inverte corso nuovamente per accelerare rapidamente verso la fine. Pertanto, mentre lo spostamento generale del [ `Image` ](xref:Xamarin.Forms.Image) istanza è verso il basso, inverte temporaneamente corso metà dell'animazione.

### <a name="custom-easing-constructor"></a>Costruttore di interpolazione personalizzata

Una funzione di interpolazione personalizzata può anche essere definita come argomento per il [ `Easing` ](xref:Xamarin.Forms.Easing) costruttore, come illustrato nell'esempio di codice seguente:

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

La funzione di interpolazione personalizzata viene specificata come argomento di funzione lambda per il [ `Easing` ](xref:Xamarin.Forms.Easing) costruttore e viene usato il `Math.Cos` metodo per creare un effetto di rallentamento che è attenuato dal `Math.Exp` (metodo). Pertanto, il [ `Image` ](xref:Xamarin.Forms.Image) istanza viene convertita in modo che venga visualizzato per scendere al suo posto di riposo finale.

## <a name="summary"></a>Riepilogo

L'articolo descrive come usare le funzioni di interpolazione predefinite e come creare funzioni di interpolazione personalizzate. Xamarin. Forms include un' [ `Easing` ](xref:Xamarin.Forms.Easing) classe che consente di specificare una funzione di trasferimento che consente di controllare come le animazioni velocizzare o rallentare poiché sono in esecuzione.



## <a name="related-links"></a>Collegamenti correlati

- [Panoramica del supporto asincrono](~/cross-platform/platform/async.md)
- [Funzioni di interpolazione (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/easing/)
- [Interpolazione](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
