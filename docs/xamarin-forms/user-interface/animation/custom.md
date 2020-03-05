---
title: Animazioni personalizzate in xamarin. Forms
description: Questo articolo illustra come usare la classe di animazione di xamarin. Forms per creare e annullare le animazioni, sincronizzare le animazioni più e creare animazioni personalizzate per animare le proprietà che non sono animate da metodi di animazione esistenti.
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2019
ms.openlocfilehash: 405d7990b622b890aa3d66bd632662f086441666
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292586"
---
# <a name="custom-animations-in-xamarinforms"></a>Animazioni personalizzate in xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)

_La classe Animation è il blocco predefinito di tutte le animazioni Novell. Forms, con i metodi di estensione nella classe ViewExtensions che creano uno o più oggetti animazione. Questo articolo illustra come usare la classe Animation per creare e annullare animazioni, sincronizzare più animazioni e creare animazioni personalizzate che animano le proprietà che non sono animate dai metodi di animazione esistenti._

È necessario specificare un numero di parametri quando si crea un oggetto `Animation`, inclusi i valori iniziale e finale della proprietà a cui si sta aggiungendo un'animazione e un callback che modifica il valore della proprietà. Un oggetto `Animation` può inoltre gestire una raccolta di animazioni figlio che possono essere eseguite e sincronizzate. Per altre informazioni, vedere [animazioni figlio](#child).

L'esecuzione di un'animazione creata con la classe [`Animation`](xref:Xamarin.Forms.Animation) , che può includere o meno animazioni figlio, viene eseguita chiamando il metodo [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) . Questo metodo consente di specificare la durata dell'animazione e tra altri elementi, un callback che consente di controllare se ripetere l'animazione.

Inoltre, la classe [`Animation`](xref:Xamarin.Forms.Animation) dispone di una proprietà `IsEnabled` che può essere esaminata per determinare se le animazioni sono state disabilitate dal sistema operativo, ad esempio quando è attivata la modalità di risparmio energia.

## <a name="create-an-animation"></a>Creare un'animazione

Quando si crea un oggetto [`Animation`](xref:Xamarin.Forms.Animation) , in genere sono necessari almeno tre parametri, come illustrato nell'esempio di codice seguente:

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

Questo codice definisce un'animazione della proprietà [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) di un'istanza di [`Image`](xref:Xamarin.Forms.Image) da un valore 1 a un valore di 2. Il valore animato, che è derivato da Novell. Forms, viene passato al callback specificato come primo argomento, dove viene usato per modificare il valore della proprietà `Scale`.

L'animazione viene avviata con una chiamata al metodo [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) , come illustrato nell'esempio di codice seguente:

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

Si noti che il metodo [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) non restituisce un oggetto `Task`. Al contrario, vengono fornite tramite i metodi di callback.

Nel metodo `Commit` vengono specificati gli argomenti seguenti:

- Il primo argomento (*proprietario*) identifica il proprietario dell'animazione. Ciò può essere l'elemento visivo in cui viene applicata l'animazione o un altro elemento visivo, ad esempio la pagina.
- Il secondo argomento (*Name*) identifica l'animazione con un nome. Il nome viene combinato con il proprietario per identificare in modo univoco l'animazione. Questa identificazione univoca può quindi essere usata per determinare se l'animazione è in esecuzione ([`AnimationIsRunning`](xref:Xamarin.Forms.AnimationExtensions.AnimationIsRunning(Xamarin.Forms.IAnimatable,System.String))) o per annullarla ([`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String))).
- Il terzo argomento (*rate*) indica il numero di millisecondi tra ciascuna chiamata al metodo di callback definito nel costruttore [`Animation`](xref:Xamarin.Forms.Animation)
- Il quarto argomento (*length*) indica la durata dell'animazione, in millisecondi.
- Il quinto argomento (*interpolazione*) definisce la funzione di interpolazione da utilizzare nell'animazione. In alternativa, la funzione di interpolazione può essere specificata come argomento per il costruttore [`Animation`](xref:Xamarin.Forms.Animation) . Per ulteriori informazioni sulle funzioni di interpolazione, vedere [funzioni di interpolazione](~/xamarin-forms/user-interface/animation/easing.md).
- Il sesto argomento (*terminato*) è un callback che verrà eseguito al termine dell'animazione. Questo callback accetta due argomenti, con il primo argomento che indica un valore finale e il secondo argomento che è un `bool` impostato su `true` se l'animazione è stata annullata. In alternativa, è possibile specificare il callback *completato* come argomento per il costruttore [`Animation`](xref:Xamarin.Forms.Animation) . Tuttavia, con un'unica animazione, se i callback *finiti* vengono specificati sia nel costruttore `Animation` che nel metodo `Commit`, verrà eseguito solo il callback specificato nel metodo `Commit`.
- Il settimo argomento (*Repeat*) è un callback che consente di ripetere l'animazione. Viene chiamato alla fine dell'animazione e la restituzione di `true` indica che l'animazione deve essere ripetuta.

L'effetto complessivo consiste nel creare un'animazione che aumenta la proprietà [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) di un [`Image`](xref:Xamarin.Forms.Image) da 1 a 2, oltre 2 secondi (2000 millisecondi), usando la funzione di interpolazione [`Linear`](xref:Xamarin.Forms.Easing.Linear) . Ogni volta che l'animazione viene completata, la relativa proprietà `Scale` viene reimpostata su 1 e l'animazione viene ripetuta.

> [!NOTE]
> Le animazioni simultanee, che vengono eseguite in modo indipendente l'una dall'altra, possono essere costruite creando un oggetto `Animation` per ogni animazione, quindi chiamando il metodo `Commit` per ogni animazione.

<a name="child" />

### <a name="child-animations"></a>Animazioni figlio

La classe [`Animation`](xref:Xamarin.Forms.Animation) supporta anche animazioni figlio, che comporta la creazione di un oggetto `Animation` a cui vengono aggiunti altri oggetti di `Animation`. In questo modo una serie di animazioni deve essere eseguita e sincronizzate. Esempio di codice seguente illustra la creazione e l'esecuzione di animazioni figlio:

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

In entrambi gli esempi di codice, viene creato un oggetto [`Animation`](xref:Xamarin.Forms.Animation) padre a cui vengono aggiunti gli oggetti `Animation` aggiuntivi. I primi due argomenti per il metodo [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) specificano quando iniziare e terminare l'animazione figlio. I valori dell'argomento devono essere compreso tra 0 e 1 e rappresentano il periodo relativo all'interno dell'animazione padre che l'animazione figlio specificato saranno attivo. Pertanto, in questo esempio il `scaleUpAnimation` sarà attivo per la prima metà dell'animazione, il `scaleDownAnimation` sarà attivo per la seconda metà dell'animazione e il `rotateAnimation` sarà attivo per l'intera durata.

L'effetto generale è che l'animazione viene eseguita più di 4 secondi (in 4000 millisecondi). Il `scaleUpAnimation` aggiunge un'animazione alla proprietà [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) da 1 a 2, più di 2 secondi. Il `scaleDownAnimation` aggiunge quindi un'animazione alla proprietà `Scale` da 2 a 1, più di 2 secondi. Quando si verificano entrambe le animazioni di ridimensionamento, il `rotateAnimation` aggiunge un'animazione alla proprietà [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) da 0 a 360, più di 4 secondi. Si noti che le animazioni ridimensionamento anche usano funzioni di interpolazione. Con la funzione di interpolazione [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) il [`Image`](xref:Xamarin.Forms.Image) viene inizialmente compattato prima di essere più grande e la funzione di interpolazione [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) fa sì che il `Image` diventi più piccolo della dimensione effettiva fino alla fine dell'animazione completa.

Esistono diverse differenze tra un oggetto [`Animation`](xref:Xamarin.Forms.Animation) che usa animazioni figlio e uno che non:

- Quando si usano animazioni figlio, il callback *finito* su un'animazione figlio indica quando l'elemento figlio è stato completato e il callback *terminato* passato al metodo `Commit` indica quando l'intera animazione è stata completata.
- Quando si usano animazioni figlio, la restituzione di `true` dal callback di *ripetizione* sul metodo `Commit` non comporta la ripetizione dell'animazione, ma l'esecuzione dell'animazione continuerà senza nuovi valori.
- Quando si include una funzione di interpolazione nel metodo `Commit` e la funzione di interpolazione restituisce un valore maggiore di 1, l'animazione verrà terminata. Se la funzione di interpolazione restituisce un valore minore di 0, il valore è impostato su 0. Per usare una funzione di interpolazione che restituisce un valore minore di 0 o maggiore di 1, deve essere specificata in una delle animazioni figlio anziché nel metodo `Commit`.

La classe [`Animation`](xref:Xamarin.Forms.Animation) include anche [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double)) metodi che possono essere usati per aggiungere animazioni figlio a un oggetto `Animation` padre. Tuttavia, i valori degli argomenti *Begin* e *Finish* non sono limitati a 0 e 1, ma solo quella parte dell'animazione figlio che corrisponde a un intervallo compreso tra 0 e 1 sarà attiva. Se, ad esempio, una chiamata al metodo `WithConcurrent` definisce un'animazione figlio destinata a una proprietà di [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) da 1 a 6, ma con i valori di *inizio* e *fine* di-2 e 3, il valore di *inizio* di-2 corrisponde a un valore di `Scale` 1 e il valore *finale* di 3 corrisponde a un valore `Scale` pari a 6. Poiché i valori non compresi nell'intervallo compreso tra 0 e 1 non fanno parte di un'animazione, la proprietà `Scale` verrà animata solo da 3 a 6.

## <a name="cancel-an-animation"></a>Annulla un'animazione

Un'applicazione può annullare un'animazione con una chiamata al metodo di estensione [`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String)) , come illustrato nell'esempio di codice seguente:

```csharp
this.AbortAnimation ("SimpleAnimation");
```

Si noti che le animazioni vengono identificate in modo univoco da una combinazione del proprietario dell'animazione e il nome dell'animazione. Pertanto, il proprietario e il nome specificato quando esegue l'animazione deve essere specificato per annullare l'animazione. Pertanto, l'esempio di codice annulla immediatamente l'animazione denominata `SimpleAnimation` di proprietà della pagina.

## <a name="create-a-custom-animation"></a>Creare un'animazione personalizzata

Gli esempi illustrati finora hanno dimostrato animazioni che potrebbero essere ugualmente realizzate con i metodi della classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) . Tuttavia, il vantaggio della classe [`Animation`](xref:Xamarin.Forms.Animation) è che può accedere al metodo di callback, che viene eseguito quando il valore animato viene modificato. In questo modo il callback implementare qualsiasi animazione desiderato. L'esempio di codice seguente, ad esempio, aggiunge un'animazione alla proprietà [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) di una pagina impostandolo su [`Color`](xref:Xamarin.Forms.Color) valori creati dal metodo [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) , con valori di tonalità compresi tra 0 e 1:

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

L'animazione risulta fornisce l'aspetto del miglioramento dello sfondo della pagina tramite i colori dell'arcobaleno.

Per altri esempi di creazione di animazioni complesse, inclusa un'animazione a curva di Bezier, vedere il [capitolo 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf) della pagina relativa alla [creazione di app per dispositivi mobili con Novell. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="create-a-custom-animation-extension-method"></a>Creare un metodo di estensione dell'animazione personalizzata

I metodi di estensione nella classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) animano una proprietà dal relativo valore corrente a un valore specificato. Questo rende difficile creare, ad esempio, un metodo di animazione `ColorTo` che può essere usato per animare un colore da un valore a un altro, perché:

- L'unica proprietà [`Color`](xref:Xamarin.Forms.Color) definita dalla classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) è [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor), che non è sempre la proprietà `Color` desiderata a cui aggiungere un'animazione.
- Spesso il valore corrente di una proprietà di [`Color`](xref:Xamarin.Forms.Color) è [`Color.Default`](xref:Xamarin.Forms.Color.Default), che non è un colore reale e che non può essere usato nei calcoli di interpolazione.

La soluzione a questo problema consiste nell'evitare che il metodo `ColorTo` abbia come destinazione una particolare proprietà di [`Color`](xref:Xamarin.Forms.Color) . È invece possibile scriverlo con un metodo di callback che passa il valore di `Color` interpolato di nuovo al chiamante. Inoltre, il metodo accetta gli argomenti `Color` iniziale e finale.

Il metodo `ColorTo` può essere implementato come metodo di estensione che usa il metodo [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) nella classe [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) per fornirne le funzionalità. Questo perché il metodo `Animate` può essere usato per le proprietà di destinazione che non sono di tipo `double`, come illustrato nell'esempio di codice seguente:

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

Il metodo [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) richiede un argomento *Transform* , che è un metodo di callback. L'input per questo callback è sempre un `double` compreso tra 0 e 1. Pertanto, il metodo `ColorTo` definisce il proprio `Func` di trasformazione che accetta un `double` compreso tra 0 e 1 e che restituisce un valore [`Color`](xref:Xamarin.Forms.Color) corrispondente a tale valore. Il valore `Color` viene calcolato tramite l'interpolazione dei valori [`R`](xref:Xamarin.Forms.Color.R), [`G`](xref:Xamarin.Forms.Color.G), [`B`](xref:Xamarin.Forms.Color.B)e [`A`](xref:Xamarin.Forms.Color.A) dei due argomenti `Color` specificati. Il valore `Color` viene quindi passato al metodo di callback per l'applicazione a una particolare proprietà.

Questo approccio consente al metodo `ColorTo` di animare qualsiasi proprietà di [`Color`](xref:Xamarin.Forms.Color) , come illustrato nell'esempio di codice seguente:

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

In questo esempio di codice, il metodo `ColorTo` aggiunge un'animazione alle proprietà [`TextColor`](xref:Xamarin.Forms.Label.TextColor) e [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) di un [`Label`](xref:Xamarin.Forms.Label), alla proprietà `BackgroundColor` di una pagina e alla proprietà [`Color`](xref:Xamarin.Forms.BoxView.Color) di un [`BoxView`](xref:Xamarin.Forms.BoxView).

## <a name="related-links"></a>Collegamenti correlati

- [Animazioni personalizzate (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)
- [API animazione](xref:Xamarin.Forms.Animation)
- [API AnimationExtensions](xref:Xamarin.Forms.AnimationExtensions)
