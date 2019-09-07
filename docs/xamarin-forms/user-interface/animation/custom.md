---
title: Animazioni personalizzate in xamarin. Forms
description: Questo articolo illustra come usare la classe di animazione di xamarin. Forms per creare e annullare le animazioni, sincronizzare le animazioni più e creare animazioni personalizzate per animare le proprietà che non sono animate da metodi di animazione esistenti.
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 86fac9dd0c2f9281a0c828ace68fbf77679dce5b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759833"
---
# <a name="custom-animations-in-xamarinforms"></a>Animazioni personalizzate in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)

_La classe di animazione è il blocco predefinito di tutte le animazioni di xamarin. Forms, con i metodi di estensione nella classe ViewExtensions la creazione di uno o più oggetti di animazione. Questo articolo illustra come usare la classe di animazione per creare e annullare le animazioni, sincronizzare le animazioni più e creare animazioni personalizzate per animare le proprietà che non sono animate da metodi di animazione esistenti._

Un numero di parametri deve essere specificato durante la creazione di un `Animation` oggetto, inclusi i valori di inizio e fine della proprietà animata e un callback che cambia il valore della proprietà. Un `Animation` oggetto inoltre possibile mantenere una raccolta di animazioni figlio che possono essere eseguiti e sincronizzate. Per altre informazioni, vedere [animazioni figlio](#child).

Esecuzione di un'animazione creata con il [ `Animation` ](xref:Xamarin.Forms.Animation) (classe), che può o non può includere le animazioni figlio, risultato viene ottenuto chiamando il [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) (metodo). Questo metodo consente di specificare la durata dell'animazione e tra altri elementi, un callback che consente di controllare se ripetere l'animazione.

## <a name="creating-an-animation"></a>Creazione di un'animazione

Quando si crea un' [ `Animation` ](xref:Xamarin.Forms.Animation) dell'oggetto, in genere, un minimo di tre parametri sono obbligatori, come illustrato nell'esempio di codice seguente:

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

Questo codice definisce un'animazione del [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) proprietà di un [ `Image` ](xref:Xamarin.Forms.Image) istanza da un valore pari a 1 a un valore pari a 2. Il valore animato, che è derivato da xamarin. Forms, viene passato al metodo di callback specificato come primo argomento, in cui viene usato per modificare il valore della `Scale` proprietà.

L'animazione viene avviata con una chiamata per il [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) metodo, come illustrato nell'esempio di codice seguente:

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

Si noti che il [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) metodo non restituisce un `Task` oggetto. Al contrario, vengono fornite tramite i metodi di callback.

Gli argomenti seguenti vengono specificati nel `Commit` metodo:

- Il primo argomento (*proprietario*) identifica il proprietario dell'animazione. Ciò può essere l'elemento visivo in cui viene applicata l'animazione o un altro elemento visivo, ad esempio la pagina.
- Il secondo argomento (*nome*) identifica l'animazione con un nome. Il nome viene combinato con il proprietario per identificare in modo univoco l'animazione. Questa identificazione univoca è quindi utilizzabile per determinare se l'animazione è in esecuzione ([`AnimationIsRunning`](xref:Xamarin.Forms.AnimationExtensions.AnimationIsRunning(Xamarin.Forms.IAnimatable,System.String))), o per annullare l'operazione ([`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String))).
- Il terzo argomento (*tasso*) indica il numero di millisecondi tra ogni chiamata al metodo di callback definito nel [ `Animation` ](xref:Xamarin.Forms.Animation) costruttore
- Il quarto argomento (*lunghezza*) indica la durata dell'animazione, in millisecondi.
- Il quinto argomento (*interpolazione*) definisce la funzione di interpolazione da utilizzare nell'animazione. In alternativa, la funzione di interpolazione può essere specificata come argomento per il [ `Animation` ](xref:Xamarin.Forms.Animation) costruttore. Per altre informazioni sulle funzioni di interpolazione, vedere [funzioni di interpolazione](~/xamarin-forms/user-interface/animation/easing.md).
- Il sesto argomento (*terminato*) è un callback che verrà eseguito quando l'animazione è stata completata. Questo callback accetta due argomenti, con il primo argomento che indica un valore finale e il secondo argomento da un `bool` che è impostato su `true` se l'animazione è stata annullata. In alternativa, il *terminata* callback può essere specificato come argomento per il [ `Animation` ](xref:Xamarin.Forms.Animation) costruttore. Tuttavia, con una singola animazione, se *terminato* i callback vengono specificati sia nel `Animation` costruttore e il `Commit` (metodo), solo il callback specificato nella `Commit` metodo verrà eseguito.
- Il settimo argomento (*ripetere*) è un callback che consente l'animazione per essere ripetuti. Viene chiamato alla fine dell'animazione e la restituzione `true` indica che l'animazione deve essere ripetuta.

L'effetto generale consiste nel creare un'animazione che aumenta la [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) proprietà di un [ `Image` ](xref:Xamarin.Forms.Image) compreso tra 1 e 2, maggiore di 2 secondi (2000 millisecondi), usando il [ `Linear` ](xref:Xamarin.Forms.Easing.Linear) funzione di interpolazione. Ogni volta che il completamento dell'animazione, relativo `Scale` viene reimpostata su 1 e l'animazione si ripete.

> [!NOTE]
> Le animazioni simultanee, eseguiti indipendentemente uno da altro possono essere create mediante la creazione di un' `Animation` dell'oggetto per ogni tipo di animazione e chiamando quindi il `Commit` metodo su ogni animazione.

<a name="child" />

### <a name="child-animations"></a>Animazioni figlio

Il [ `Animation` ](xref:Xamarin.Forms.Animation) classe supporta anche le animazioni figlio, il che implica la creazione di un `Animation` oggetto a quali altre `Animation` gli oggetti vengono aggiunti. In questo modo una serie di animazioni deve essere eseguita e sincronizzate. Esempio di codice seguente illustra la creazione e l'esecuzione di animazioni figlio:

```csharp
var parentAnimation = new Animation ();
var scaleUpAnimation = new Animation (v => image.Scale = v, 1, 2, Easing.SpringIn);
var rotateAnimation = new Animation (v => image.Rotation = v, 0, 360);
var scaleDownAnimation = new Animation (v => image.Scale = v, 2, 1, Easing.SpringOut);

parentAnimation.Add (0, 0.5, scaleUpAnimation);
parentAnimation.Add (0, 1, rotateAnimation);
parentAnimation.Add (0.5, 1, scaleDownAnimation);

parentAnimation.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

In alternativa, l'esempio di codice può essere scritto in modo più conciso, come illustrato nell'esempio di codice seguente:

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

In entrambi gli esempi di codice, un elemento padre [ `Animation` ](xref:Xamarin.Forms.Animation) oggetto viene creato, a cui altri `Animation` gli oggetti vengono quindi aggiunti. I primi due argomenti per il [ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) metodo specificare quando deve iniziare e terminare l'animazione figlio. I valori dell'argomento devono essere compreso tra 0 e 1 e rappresentano il periodo relativo all'interno dell'animazione padre che l'animazione figlio specificato saranno attivo. Pertanto, in questo esempio il `scaleUpAnimation` saranno attive per la prima metà dell'animazione, il `scaleDownAnimation` saranno attive per la seconda metà dell'animazione e il `rotateAnimation` sarà attiva per tutta la durata.

L'effetto generale è che l'animazione viene eseguita più di 4 secondi (in 4000 millisecondi). Il `scaleUpAnimation` anima la [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) proprietà da 1 a 2, maggiore di 2 secondi. Il `scaleDownAnimation` quindi aggiunge un'animazione di `Scale` proprietà da 2 a 1, maggiore di 2 secondi. Mentre si verificano entrambe le animazioni di scalabilità, la `rotateAnimation` anima la [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) proprietà compreso tra 0 e 360, più di 4 secondi. Si noti che le animazioni ridimensionamento anche usano funzioni di interpolazione. Il [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn) fa in modo che la funzione di interpolazione il [ `Image` ](xref:Xamarin.Forms.Image) compattazione inizialmente prima di ottenere più grande e il [ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) funzione di interpolazione fa sì che il `Image` diventi più piccole rispetto alle dimensioni effettive verso la fine dell'animazione completo.

Esistono alcune differenze tra un' [ `Animation` ](xref:Xamarin.Forms.Animation) oggetto che usa le animazioni figlio e uno che non:

- Quando si usano le animazioni figlio, il *terminata* callback in un'animazione figlio indica quando l'elemento figlio è stata completata e il *terminato* callback passato al `Commit` metodo indica quando il intera animazione è stata completata.
- Quando si usano le animazioni figlio, restituendo `true` dal *ripetere* callback sul `Commit` (metodo) non comporterà la ripetizione dell'animazione, ma l'animazione continuerà a eseguire senza i nuovi valori.
- Quando si include una funzione di interpolazione nel `Commit` metodo e la funzione di interpolazione restituisce un valore maggiore di 1, l'animazione verrà terminata. Se la funzione di interpolazione restituisce un valore minore di 0, il valore è impostato su 0. Per usare una funzione di interpolazione che restituisce un valore minore di 0 o maggiore di 1, è necessario specificato in una delle animazioni figlio, invece che nel `Commit` (metodo).

Il [ `Animation` ](xref:Xamarin.Forms.Animation) classe include anche [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double)) metodi che possono essere utilizzati per aggiungere le animazioni figlio a un elemento padre `Animation` oggetto. Tuttavia, i *begin* e *fine* valori degli argomenti non sono limitati a 0 e 1, ma sarà attivi solo tale parte dell'animazione figlio che corrisponde a un intervallo di 0 e 1. Ad esempio, se un `WithConcurrent` chiamata al metodo definisce un'animazione figlio destinato a un [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) proprietà da 1 a 6, ma con *begin* e *fine* i valori di -2 e 3, il *begin* il valore -2 corrisponde a un `Scale` pari a 1 e il *fine* valore 3 corrisponde a un `Scale` pari a 6. Poiché valori compreso nell'intervallo di 0 e 1 non play la parte di un'animazione, il `Scale` proprietà verrà solo essere animata da 3 a 6.

## <a name="canceling-an-animation"></a>Annullamento di un'animazione

Un'applicazione può annullare un'animazione con una chiamata per il [ `AbortAnimation` ](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String)) metodo di estensione, come illustrato nell'esempio di codice seguente:

```csharp
this.AbortAnimation ("SimpleAnimation");
```

Si noti che le animazioni vengono identificate in modo univoco da una combinazione del proprietario dell'animazione e il nome dell'animazione. Pertanto, il proprietario e il nome specificato quando esegue l'animazione deve essere specificato per annullare l'animazione. Di conseguenza, nell'esempio di codice verrà immediatamente annullata l'animazione denominata `SimpleAnimation` di proprietà della pagina.

## <a name="creating-a-custom-animation"></a>Creazione di un'animazione personalizzata

Gli esempi illustrati finora hanno dimostrato animazioni che è possibile raggiungere in modo uniforme con i metodi nel [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe. Tuttavia, il vantaggio del [ `Animation` ](xref:Xamarin.Forms.Animation) classe è che possa accedere al metodo di callback, che viene eseguito quando viene modificato il valore animato. In questo modo il callback implementare qualsiasi animazione desiderato. Ad esempio, il codice seguente aggiunge un'animazione la [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) proprietà di una pagina impostandola su [ `Color` ](xref:Xamarin.Forms.Color) valori creati dal [ `Color.FromHsla` ](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))metodo, con valori di tonalità compreso tra 0 e 1:

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

L'animazione risulta fornisce l'aspetto del miglioramento dello sfondo della pagina tramite i colori dell'arcobaleno.

Per altri esempi di creazione di animazioni complesse, tra cui un'animazione di curva di Bezier, vedere [capitolo 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf) dei [creazione di App per dispositivi mobili con xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="creating-a-custom-animation-extension-method"></a>Creazione di un metodo di estensione di animazione personalizzata

Metodi di estensione il [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe animare una proprietà dal relativo valore corrente a un valore specificato. Questo rende difficile da creare, ad esempio, un `ColorTo` metodo animazione che può essere utilizzato per aggiungere un'animazione a un colore da un valore a un altro, in quanto:

- L'unico [ `Color` ](xref:Xamarin.Forms.Color) proprietà definita per il [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe è [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor), che non è sempre il valore desiderato `Color` proprietà Per aggiungere un'animazione.
- Spesso il valore corrente di un [ `Color` ](xref:Xamarin.Forms.Color) è di proprietà [ `Color.Default` ](xref:Xamarin.Forms.Color.Default), che non è un colore reale e non può essere quindi utilizzato nei calcoli di interpolazione.

La soluzione al problema consiste nel non hanno le `ColorTo` metodo di destinazione un determinato [ `Color` ](xref:Xamarin.Forms.Color) proprietà. Al contrario, può essere scritto con un metodo di callback che passa l'interpolata `Color` valore al chiamante. Inoltre, il metodo verrà richiedere di avviare e terminare `Color` argomenti.

Il `ColorTo` metodo può essere implementato come metodo di estensione che usa la [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) metodo nella [ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions) classe per fornire la relativa funzionalità. Infatti, il `Animate` metodo può essere utilizzato per le proprietà di destinazione che non sono di tipo `double`, come illustrato nell'esempio di codice seguente:

```csharp
public static class ViewExtensions
{
  public static Task<bool> ColorTo(this VisualElement self, Color fromColor, Color toColor, Action<Color> callback, uint length = 250, Easing easing = null)
  {
    Func<double, Color> transform = (t) =>
      Color.FromRgba(fromColor.R + t * (toColor.R - fromColor.R),
                     fromColor.G + t * (toColor.G - fromColor.G),
                     fromColor.B + t * (toColor.B - fromColor.B),
                     fromColor.A + t * (toColor.A - fromColor.A));
    return ColorAnimation(self, "ColorTo", transform, callback, length, easing);
  }

  public static void CancelAnimation(this VisualElement self)
  {
    self.AbortAnimation("ColorTo");
  }

  static Task<bool> ColorAnimation(VisualElement element, string name, Func<double, Color> transform, Action<Color> callback, uint length, Easing easing)
  {
    easing = easing ?? Easing.Linear;
    var taskCompletionSource = new TaskCompletionSource<bool>();

    element.Animate<Color>(name, transform, callback, 16, length, easing, (v, c) => taskCompletionSource.SetResult(c));
    return taskCompletionSource.Task;
  }
}
```

Il [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) metodo richiede una *trasformare* argomento, che è un metodo di callback. L'input di questo callback è sempre un `double` compreso tra 0 e 1. Pertanto, il `ColorTo` metodo consente di definire la propria trasformazione `Func` che accetta un `double` compreso tra 0 e 1 e che restituisce un [ `Color` ](xref:Xamarin.Forms.Color) valore corrispondente a tale valore. Il `Color` valore viene calcolato dall'interpolazione il [ `R` ](xref:Xamarin.Forms.Color.R), [ `G` ](xref:Xamarin.Forms.Color.G), [ `B` ](xref:Xamarin.Forms.Color.B), e [ `A` ](xref:Xamarin.Forms.Color.A) i valori dei due fornito `Color` argomenti. Il `Color` valore viene quindi passato al metodo di callback per una particolare proprietà dell'applicazione.

Questo approccio consente la `ColorTo` metodo per aggiungere un'animazione a qualsiasi [ `Color` ](xref:Xamarin.Forms.Color) proprietà, come illustrato nell'esempio di codice seguente:

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

In questo esempio di codice, il `ColorTo` metodo aggiunge un'animazione il [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) e [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) le proprietà di un [ `Label` ](xref:Xamarin.Forms.Label), il `BackgroundColor`proprietà di una pagina e il [ `Color` ](xref:Xamarin.Forms.BoxView.Color) proprietà di un [ `BoxView` ](xref:Xamarin.Forms.BoxView).

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come usare il [ `Animation` ](xref:Xamarin.Forms.Animation) classe per creare e annullare le animazioni e sincronizzare le animazioni più creare animazioni personalizzate per animare le proprietà che non vengono animate dall'animazione esistente metodi. Il `Animation` classe è il blocco predefinito di tutte le animazioni di xamarin. Forms.

## <a name="related-links"></a>Collegamenti correlati

- [Animazioni personalizzate (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)
- [Animazione](xref:Xamarin.Forms.Animation)
- [AnimationExtensions](xref:Xamarin.Forms.AnimationExtensions)
