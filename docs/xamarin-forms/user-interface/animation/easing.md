---
title: "funzioni di interpolazione in Xamarin.Forms " Description: " Xamarin.Forms include una classe di interpolazione che consente di specificare una funzione di trasferimento che controlla il modo in cui le animazioni accelerano o rallentano durante l'esecuzione. In questo articolo viene illustrato come utilizzare le funzioni di interpolazione predefinite e come creare funzioni di interpolazione personalizzate.
ms. prod: Novell MS. AssetID: E6F124C7-A161-4C1F-AF40-52F0935E54DE ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 07/14/2016 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="easing-functions-in-xamarinforms"></a>Funzioni di interpolazioneXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)

_Novell. Forms include una classe di interpolazione che consente di specificare una funzione di trasferimento che controlla il modo in cui le animazioni accelerano o rallentano durante l'esecuzione. In questo articolo viene illustrato come utilizzare le funzioni di interpolazione predefinite e come creare funzioni di interpolazione personalizzate._

La [`Easing`](xref:Xamarin.Forms.Easing) classe definisce numerose funzioni di interpolazione che possono essere utilizzate dalle animazioni:

- La [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) funzione di interpolazione rimbalza all'inizio dell'animazione.
- La [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut) funzione di interpolazione rimbalza l'animazione alla fine.
- La [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn) funzione di interpolazione accelera lentamente l'animazione.
- La [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut) funzione di interpolazione accelera l'animazione all'inizio e decelera l'animazione alla fine.
- La [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut) funzione di interpolazione rallenta rapidamente l'animazione.
- La [`Linear`](xref:Xamarin.Forms.Easing.Linear) funzione di interpolazione utilizza una velocità costante e è la funzione di interpolazione predefinita.
- La [`SinIn`](xref:Xamarin.Forms.Easing.SinIn) funzione di interpolazione accelera in modo semplice l'animazione.
- La [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut) funzione di interpolazione accelera in modo semplice l'animazione all'inizio e rallenta in modo uniforme l'animazione alla fine.
- La [`SinOut`](xref:Xamarin.Forms.Easing.SinOut) funzione di interpolazione rallenta in modo semplice l'animazione.
- La [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) funzione di interpolazione fa sì che l'animazione acceleri molto rapidamente verso la fine.
- La [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) funzione di interpolazione fa in modo che l'animazione rallenti rapidamente verso la fine.

I `In` `Out` suffissi e indicano se l'effetto fornito dalla funzione di interpolazione è evidente all'inizio dell'animazione, alla fine o a entrambi.

Inoltre, è possibile creare funzioni di interpolazione personalizzate. Per altre informazioni, vedere [funzioni di interpolazione personalizzate](#custom-easing-functions).

## <a name="consuming-an-easing-function"></a>Utilizzo di una funzione di interpolazione

I metodi di estensione dell'animazione nella [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe consentono di specificare una funzione di interpolazione come parametro finale del metodo, come illustrato nell'esempio di codice seguente:

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

Specificando una funzione di interpolazione per un'animazione, la velocità dell'animazione diventa non lineare e produce l'effetto fornito dalla funzione di interpolazione. Se si omette una funzione di interpolazione quando si crea un'animazione, l'animazione utilizzerà la [`Linear`](xref:Xamarin.Forms.Easing.Linear) funzione di interpolazione predefinita, che produce una velocità lineare.

Per ulteriori informazioni sull'utilizzo dei metodi di estensione dell'animazione nella [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe, vedere [animazioni semplici](~/xamarin-forms/user-interface/animation/simple.md). Le funzioni di interpolazione possono essere utilizzate anche dalla [`Animation`](xref:Xamarin.Forms.Animation) classe. Per altre informazioni, vedere [animazioni personalizzate](~/xamarin-forms/user-interface/animation/custom.md).

## <a name="custom-easing-functions"></a>Funzioni di interpolazione personalizzate

Sono disponibili tre approcci principali per la creazione di una funzione di interpolazione personalizzata:

1. Creare un metodo che accetta un `double` argomento e restituisce un `double` risultato.
1. Creare un oggetto `Func<double, double>`.
1. Specificare la funzione di interpolazione come argomento per il [`Easing`](xref:Xamarin.Forms.Easing) costruttore.

In tutti e tre i casi, la funzione di interpolazione personalizzata deve restituire 0 per un argomento di 0 e 1 per un argomento di 1. Tuttavia, qualsiasi valore può essere restituito tra i valori di argomento 0 e 1. Ogni approccio verrà ora discusso a turno.

### <a name="custom-easing-method"></a>Metodo di interpolazione personalizzato

Una funzione di interpolazione personalizzata può essere definita come metodo che accetta un `double` argomento e restituisce un `double` risultato, come illustrato nell'esempio di codice seguente:

```csharp
double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}

await image.TranslateTo(0, 200, 2000, (Easing)CustomEase);
```

Il `CustomEase` Metodo tronca il valore in ingresso ai valori 0, 0,2, 0,4, 0,6, 0,8 e 1. Pertanto, l' [`Image`](xref:Xamarin.Forms.Image) istanza viene convertita in salti discreti, anziché in modo semplice.

### <a name="custom-easing-func"></a>Funzione di interpolazione personalizzata

Una funzione di interpolazione personalizzata può anche essere definita come `Func<double, double>` , come illustrato nell'esempio di codice seguente:

```csharp
Func<double, double> CustomEaseFunc = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEaseFunc);
```

L'oggetto `CustomEaseFunc` rappresenta una funzione di interpolazione che inizia velocemente, rallenta e inverte il corso, quindi inverte di nuovo il corso per accelerare rapidamente verso la fine. Pertanto, mentre lo spostamento generale dell' [`Image`](xref:Xamarin.Forms.Image) istanza è verso il basso, viene anche invertito temporaneamente il corso a metà dell'animazione.

### <a name="custom-easing-constructor"></a>Costruttore di interpolazione personalizzato

Una funzione di interpolazione personalizzata può essere definita anche come argomento del [`Easing`](xref:Xamarin.Forms.Easing) costruttore, come illustrato nell'esempio di codice seguente:

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

La funzione di interpolazione personalizzata viene specificata come argomento della funzione lambda per il [`Easing`](xref:Xamarin.Forms.Easing) costruttore e usa il `Math.Cos` metodo per creare un effetto di rilascio lento che è smorzato dal `Math.Exp` metodo. Pertanto, l' [`Image`](xref:Xamarin.Forms.Image) istanza viene convertita in modo che sembri rilasciarla nel punto di riposo finale.

## <a name="summary"></a>Summary

In questo articolo è stato illustrato come utilizzare le funzioni di interpolazione predefinite e come creare funzioni di interpolazione personalizzate. Xamarin.Formsinclude una [`Easing`](xref:Xamarin.Forms.Easing) classe che consente di specificare una funzione di trasferimento che controlla il modo in cui le animazioni accelerano o rallentano durante l'esecuzione.

## <a name="related-links"></a>Collegamenti correlati

- [Panoramica del supporto asincrono](~/cross-platform/platform/async.md)
- [Funzioni di interpolazione (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)
- [Interpolazione](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
