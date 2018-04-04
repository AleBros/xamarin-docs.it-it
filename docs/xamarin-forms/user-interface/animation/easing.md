---
title: Funzioni di interpolazione
description: Xamarin. Forms include una variazione velocità classe che consente di specificare una funzione di trasferimento che determina la modalità di animazioni velocizzare o rallentano come vengono eseguiti. In questo articolo viene illustrato come utilizzare le funzioni di interpolazione predefinite e come creare funzioni di interpolazione personalizzate.
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: e9171b885bdf5958b6969719301a1d7dad51d95b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="easing-functions"></a>Funzioni di interpolazione

_Xamarin. Forms include una variazione velocità classe che consente di specificare una funzione di trasferimento che determina la modalità di animazioni velocizzare o rallentano come vengono eseguiti. In questo articolo viene illustrato come utilizzare le funzioni di interpolazione predefinite e come creare funzioni di interpolazione personalizzate._


Il [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) classe definisce un numero di funzioni di interpolazione che possono essere utilizzati da animazioni:

- Il [ `BounceIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceIn/) la funzione di interpolazione aperture l'animazione all'inizio.
- Il [ `BounceOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceOut/) la funzione di interpolazione aperture l'animazione alla fine.
- Il [ `CubicIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicIn/) la funzione di interpolazione lenta accelera l'animazione.
- Il [ `CubicInOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicInOut/) accelera l'animazione all'inizio e la funzione di interpolazione rallenta l'animazione alla fine.
- Il [ `CubicOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicOut/) rapidamente la funzione di interpolazione rallenta l'animazione.
- Il [ `Linear` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/) la funzione di interpolazione Usa una velocità costante ed è il valore predefinito per funzione di interpolazione.
- Il [ `SinIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinIn/) la funzione di interpolazione lineare accelera l'animazione.
- Il [ `SinInOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinInOut/) accelera l'animazione all'inizio e la funzione di interpolazione lineare agevolmente rallenta l'animazione alla fine.
- Il [ `SinOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinOut/) la funzione di interpolazione lineare rallenta l'animazione.
- Il [ `SpringIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) la funzione di interpolazione provoca accelerare molto rapidamente verso la fine dell'animazione.
- Il [ `SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/) la funzione di interpolazione di causa decelerazione rapidamente verso la fine dell'animazione.

Il `In` e `Out` suffissi indicano se l'effetto fornito dalla funzione di interpolazione è evidente all'inizio dell'animazione, alla fine o entrambi.

Inoltre, le funzioni di interpolazione personalizzate possono essere create. Per ulteriori informazioni, vedere [funzioni di interpolazione personalizzata](#customeasing).

## <a name="consuming-an-easing-function"></a>Utilizzo di una funzione di interpolazione

I metodi di estensione animazione di [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe consente a una funzione di interpolazione essere specificato come parametro del metodo finale, come illustrato nell'esempio di codice seguente:

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

Se si specifica una funzione di interpolazione per un'animazione, la velocità dell'animazione diventa non lineare e produce l'effetto fornito dalla funzione di interpolazione. L'omissione di una funzione di interpolazione durante la creazione di un'animazione fa sì che l'animazione di utilizzare il valore predefinito [ `Linear` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/) interpolazione funzione che produce una velocità lineare.

Per ulteriori informazioni sull'utilizzo di metodi di estensione animazione di [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe, vedere [semplici animazioni](~/xamarin-forms/user-interface/animation/simple.md). Funzioni di interpolazione possono essere utilizzate anche dal [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) classe. Per ulteriori informazioni, vedere [animazione personalizzata](~/xamarin-forms/user-interface/animation/custom.md).

<a name="customeasing" />

## <a name="custom-easing-functions"></a>Funzioni di interpolazione personalizzata

Esistono tre approcci principali per la creazione di una funzione di interpolazione personalizzata:

1. Creare un metodo che accetta un `double` argomento e restituisce un `double` risultato.
1. Creare un oggetto `Func<double, double>`.
1. Specificare la funzione di interpolazione come argomento per il [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) costruttore.

In tutti e tre i casi, la funzione di interpolazione personalizzata deve restituire 0 per un argomento di 0 e 1 per un argomento di 1. Tuttavia, qualsiasi valore può essere restituito compreso tra i valori degli argomenti pari a 0 e 1. Ogni approccio verrà ora essere descritti in dettaglio.

### <a name="custom-easing-method"></a>Metodo di interpolazione personalizzata

Una funzione di interpolazione personalizzata può essere definita come un metodo che accetta un `double` argomento e restituisce un `double` risultato, come illustrato nell'esempio di codice seguente:

```csharp
await image.TranslateTo(0, 200, 2000, CustomEase);

double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}
```

Il `CustomEase` metodo tronca il valore in ingresso ai valori 0, 0,2, 0,4, 0,6, 0,8 e 1. Pertanto, il [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) istanza viene convertita in collegamenti discreti, anziché in modo uniforme.

### <a name="custom-easing-func"></a>Funzione di interpolazione personalizzata

Una funzione di interpolazione personalizzata può anche essere definita come un `Func<double, double>`, come illustrato nell'esempio di codice seguente:

```csharp
Func<double, double> CustomEase = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEase));
```

Il `CustomEase` `Func` rappresenta una funzione di interpolazione che inizia rallenta la velocità, inverte corso e quindi inverte corso nuovamente per accelerare rapidamente verso la fine. Pertanto, mentre lo spostamento globale del [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) istanza è verso il basso, viene invertito anche temporaneamente corso a metà dell'animazione.

### <a name="custom-easing-constructor"></a>Costruttore di interpolazione personalizzata

Una funzione di interpolazione personalizzata può essere definita anche come argomento per il [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) costruttore, come illustrato nell'esempio di codice seguente:

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

Funzione di interpolazione personalizzata viene specificata come argomento di una funzione lambda per il [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) costruttore e viene utilizzato il `Math.Cos` metodo per creare un effetto lenta che misura è attenuato dal `Math.Exp` metodo. Pertanto, il [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) istanza viene convertita in modo che venga visualizzato per rilasciare la posizione finale.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare le funzioni di interpolazione predefinite e come creare funzioni di interpolazione personalizzate. Xamarin. Forms include un [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) classe che consente di specificare una funzione di trasferimento che controlla come animazioni velocizzare o rallentare come vengono eseguiti.



## <a name="related-links"></a>Collegamenti correlati

- [Panoramica del supporto asincrono](~/cross-platform/platform/async.md)
- [Funzioni di interpolazione (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/easing/)
- [Interpolazione](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)
- [ViewExtensions](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)
