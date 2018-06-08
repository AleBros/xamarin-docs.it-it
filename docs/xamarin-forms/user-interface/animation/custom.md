---
title: Animazioni personalizzate
description: La classe di animazione è il blocco predefinito di tutte le animazioni di xamarin. Forms, con i metodi di estensione nella classe ViewExtensions la creazione di uno o più oggetti di animazione. In questo articolo viene illustrato come utilizzare la classe di animazione per creare e annullare le animazioni, sincronizzare più animazioni e creare animazioni personalizzate che animare le proprietà che non sono animate dai metodi di animazione esistente.
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 238268a3ad2b82494f1d096d0cbeba97edb90366
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847784"
---
# <a name="custom-animations"></a>Animazioni personalizzate

_La classe di animazione è il blocco predefinito di tutte le animazioni di xamarin. Forms, con i metodi di estensione nella classe ViewExtensions la creazione di uno o più oggetti di animazione. In questo articolo viene illustrato come utilizzare la classe di animazione per creare e annullare le animazioni, sincronizzare più animazioni e creare animazioni personalizzate che animare le proprietà che non sono animate dai metodi di animazione esistente._


Un numero di parametri deve essere specificato durante la creazione di un `Animation` oggetto, inclusi i valori di inizio e di fine della proprietà viene aggiunta un'animazione e un callback che modifica il valore della proprietà. Un `Animation` oggetto inoltre possibile mantenere una raccolta di animazioni figlio che possono essere eseguiti e sincronizzate. Per ulteriori informazioni, vedere [animazioni figlio](#child).

Esecuzione di un'animazione creata con il [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) (classe), che possono o non può includere le animazioni figlio, viene ottenuto chiamando il [ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{System.Double,System.Boolean}/System.Func{System.Boolean}/) metodo. Questo metodo specifica la durata dell'animazione e tra altri elementi di un callback che controlla se ripetere l'animazione.

## <a name="creating-an-animation"></a>Creazione di un'animazione

Quando si crea un [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) dell'oggetto, in genere, un minimo di tre parametri sono obbligatori, come illustrato nell'esempio di codice seguente:

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

Questo codice definisce un'animazione del [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) proprietà di un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) istanza da un valore pari a 1 con un valore pari a 2. Il valore di animazione, che è derivato da xamarin. Forms, viene passato al metodo di callback specificato come primo argomento, in cui viene utilizzato per modificare il valore della `Scale` proprietà.

L'animazione viene avviata con una chiamata al [ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{System.Double,System.Boolean}/System.Func{System.Boolean}/) (metodo), come illustrato nell'esempio di codice seguente:

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

Si noti che il [ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{System.Double,System.Boolean}/System.Func{System.Boolean}/) metodo non restituisce un `Task` oggetto. Al contrario, le notifiche vengono fornite tramite i metodi di callback.

Gli argomenti seguenti vengono specificati nel `Commit` metodo:

- Il primo argomento (*proprietario*) identifica il proprietario dell'animazione. Può trattarsi di elemento visivo in cui viene applicata l'animazione o un altro elemento visivo, ad esempio la pagina.
- Il secondo argomento (*nome*) identifica l'animazione con un nome. Il nome viene combinato con il proprietario per identificare in modo univoco l'animazione. Questa identificazione univoca quindi può essere utilizzata per determinare se l'animazione è in esecuzione ([`AnimationIsRunning`](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.AnimationIsRunning/p/Xamarin.Forms.IAnimatable/System.String/)), o annullare ([`AbortAnimation`](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.AbortAnimation/p/Xamarin.Forms.IAnimatable/System.String/)).
- Il terzo argomento (*frequenza*) indica il numero di millisecondi tra ogni chiamata al metodo di callback definito nel [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) costruttore
- Il quarto argomento (*lunghezza*) indica la durata dell'animazione, in millisecondi.
- Il quinto argomento (*interpolazione*) definisce la funzione di interpolazione da utilizzare per l'animazione. In alternativa, la funzione di interpolazione può essere specificata come argomento per il [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) costruttore. Per ulteriori informazioni sulle funzioni di interpolazione, vedere [funzioni di interpolazione](~/xamarin-forms/user-interface/animation/easing.md).
- Il sesto argomento (*terminato*) è un callback che verrà eseguito quando l'animazione è stata completata. Questo callback accetta due argomenti, con il primo argomento che indica un valore finale e il secondo argomento da un `bool` che è impostato su `true` se l'animazione è stata annullata. In alternativa, il *terminato* callback può essere specificato come argomento per il [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) costruttore. Tuttavia, con una sola animazione, se *completato* i callback vengono specificati in entrambi il `Animation` costruttore e `Commit` solo il callback specificato nel metodo il `Commit` metodo verrà eseguito.
- Il settimo argomento (*ripetere*) è un callback che consente l'animazione da ripetere. Viene chiamato alla fine dell'animazione e la restituzione `true` indica che l'animazione deve essere ripetuto.

L'effetto generale consiste nel creare un'animazione che aumenta la [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) proprietà di un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) compreso tra 1 e 2, maggiore di 2 secondi (2000 millisecondi), utilizzando il [ `Linear` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/) la funzione di interpolazione. Ogni volta che il completamento dell'animazione, il relativo `Scale` viene reimpostata su 1 e la ripetizione dell'animazione.

> [!NOTE]
> Le animazioni simultanee, eseguiti indipendentemente uno da altro possono essere costruite creando un `Animation` per ogni animazione dell'oggetto e chiamando quindi il `Commit` metodo su ogni animazione.

<a name="child" />

### <a name="child-animations"></a>Animazioni figlio

Il [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) classe supporta anche le animazioni figlio, che implica la creazione di un `Animation` oggetto a cui `Animation` gli oggetti vengono aggiunti. In questo modo una serie di animazioni deve essere eseguita e sincronizzate. Esempio di codice seguente illustra la creazione e l'esecuzione di animazioni figlio:

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

In alternativa, l'esempio di codice può essere scritti in modo più conciso, come illustrato nell'esempio di codice seguente:

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

In entrambi gli esempi di codice, un elemento padre [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) oggetto viene creato, a cui altri `Animation` gli oggetti vengono quindi aggiunti. I primi due argomenti per il [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Add/p/System.Double/System.Double/Xamarin.Forms.Animation/) metodo specificare quando deve iniziare e terminare l'animazione figlio. I valori dell'argomento devono essere compreso tra 0 e 1 e rappresentano il periodo relativo all'interno dell'animazione padre che l'animazione figlio specificato sarà attivo. Pertanto, in questo esempio il `scaleUpAnimation` saranno attive per la prima metà dell'animazione, la `scaleDownAnimation` saranno attive per la seconda metà dell'animazione e `rotateAnimation` sarà attiva per tutta la durata.

Di conseguenza è che l'animazione viene eseguita più di 4 secondi (4000 millisecondi). Il `scaleUpAnimation` aggiunge un'animazione di [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) proprietà da 1 a 2, maggiore di 2 secondi. Il `scaleDownAnimation` quindi aggiunge un'animazione di `Scale` proprietà da 2 a 1, maggiore di 2 secondi. Mentre si verificano entrambe le animazioni di scala, il `rotateAnimation` aggiunge un'animazione di [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) proprietà compreso tra 0 e 360, oltre a 4 secondi. Si noti che le animazioni scalabilità anche le funzioni di interpolazione. Il [ `SpringIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) la funzione di interpolazione provoca il [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) compattare inizialmente prima di ottenere maggiore e [ `SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/) la funzione di interpolazione Determina il `Image` per la minore del valore delle dimensioni effettive verso la fine dell'animazione completo.

Esistono alcune differenze tra un [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) oggetto che utilizza le animazioni figlio e uno che non:

- Quando si utilizzano le animazioni figlio, il *completato* callback in un'animazione figlio indica quando è stata completata, l'elemento figlio e *completato* callback passato al `Commit` metodo indica quando il intera animazione è stata completata.
- Quando si usa animazioni figlio, restituendo `true` dal *ripetere* richiamata sul `Commit` (metodo) non causa la ripetizione dell'animazione, ma continua a essere eseguita senza nuovi valori dell'animazione.
- Quando si include una funzione di interpolazione nel `Commit` metodo e la funzione di interpolazione restituisce un valore maggiore di 1, l'animazione verrà terminata. Se la funzione di interpolazione restituisce un valore minore di 0, il valore viene impostato su 0. Per utilizzare una funzione di interpolazione che restituisce un valore minore di 0 o maggiore di 1, è necessario specificato in una delle animazioni figlio, piuttosto che nel `Commit` metodo.

Il [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) include anche [ `WithConcurrent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.WithConcurrent/p/Xamarin.Forms.Animation/System.Double/System.Double/) metodi che consentono di aggiungere le animazioni figlio a un elemento padre `Animation` oggetto. Tuttavia, i relativi *iniziare* e *fine* valori degli argomenti non sono limitati a 0 e 1, ma sarà attivi solo tale parte dell'animazione figlio che corrisponde a un intervallo da 0 a 1. Ad esempio, se un `WithConcurrent` chiamata al metodo definisce un'animazione figlio che ha come destinazione un [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) proprietà da 1 a 6, ma con *iniziare* e *fine* valori di -2 e 3, il *iniziare* valore -2 corrisponde a un `Scale` valore pari a 1 e *fine* valore 3 corrisponde a un `Scale` valore 6. Poiché i valori non compreso nell'intervallo tra 0 e 1 è cruciale senza la parte di un'animazione, la `Scale` proprietà verrà solo animata da 3 a 6.

## <a name="canceling-an-animation"></a>Annullamento di un'animazione

Per annullare un'animazione con una chiamata a un'applicazione di [ `AbortAnimation` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.AbortAnimation/p/Xamarin.Forms.IAnimatable/System.String/) il metodo di estensione, come illustrato nell'esempio di codice seguente:

```csharp
this.AbortAnimation ("SimpleAnimation");
```

Si noti che le animazioni vengono identificate in modo univoco da una combinazione del proprietario di animazione e il nome dell'animazione. Pertanto, il proprietario e il nome specificato quando in esecuzione l'animazione deve essere specificato per annullare l'animazione. Pertanto, l'esempio di codice immediatamente annullerà l'animazione denominata `SimpleAnimation` di proprietà dalla pagina.

## <a name="creating-a-custom-animation"></a>Creazione di un'animazione personalizzata

Gli esempi illustrati fino a questo punto hanno dimostrato animazioni che sarebbe ugualmente ottenute con i metodi di [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe. Tuttavia, il vantaggio del [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) classe è che abbia accesso al metodo di callback, viene eseguito quando cambia il valore di animazione. In questo modo il callback implementare qualsiasi animazione desiderato. Ad esempio, l'esempio di codice seguente aggiunge un'animazione di [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/) proprietà di una pagina impostando su [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) valori creati dal [ `Color.FromHsla` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHsla/p/System.Double/System.Double/System.Double/System.Double/)(metodo), con valori di tonalità comprese tra 0 e 1:

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

L'animazione risulta fornisce l'aspetto di avanzamento tramite i colori dell'arcobaleno lo sfondo della pagina.

Per ulteriori esempi di creazione di animazioni complesse, tra cui un'animazione curva di Bezier, vedere [capitolo 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf) di [la creazione di App per dispositivi mobili con xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="creating-a-custom-animation-extension-method"></a>Creazione di un metodo di estensione animazione personalizzata

I metodi di estensione di [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe aggiungere un'animazione a una proprietà rispetto al valore corrente a un valore specificato. Questo rende difficile creare, ad esempio, un `ColorTo` metodo animazione che può essere utilizzato per aggiungere un'animazione a un colore da un valore a un altro, in quanto:

- L'unico [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) definita dalla proprietà di [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) classe è [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/), che non sempre desiderato `Color` proprietà Per aggiungere un'animazione.
- Spesso il valore corrente di un [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) proprietà [ `Color.Default` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/), che non è un colore reale e non può essere utilizzato nei calcoli di interpolazione.

Per risolvere questo problema è non di `ColorTo` un particolare metodo come destinazione [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) proprietà. Invece, può essere scritto con un metodo di callback che passa l'interpolata `Color` valore al chiamante. Inoltre, il metodo richiedere inizio e fine `Color` argomenti.

Il `ColorTo` metodo può essere implementato come un metodo di estensione che usa il [ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate{T}/p/Xamarin.Forms.IAnimatable/System.String/System.Func{System.Double,T}/System.Action{T}/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{T,System.Boolean}/System.Func{System.Boolean}/) metodo il [ `AnimationExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/) per fornire la funzionalità. In questo modo il `Animate` metodo può essere utilizzato per le proprietà di destinazione che non sono di tipo `double`, come illustrato nell'esempio di codice seguente:

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

Il [ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate{T}/p/Xamarin.Forms.IAnimatable/System.String/System.Func{System.Double,T}/System.Action{T}/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{T,System.Boolean}/System.Func{System.Boolean}/) metodo richiede un *trasformare* argomento, che è un metodo di callback. L'input per il callback è sempre un `double` compreso tra 0 e 1. Pertanto, il `ColorTo` metodo consente di definire il proprio trasformazione `Func` che accetta un `double` compreso tra 0 e 1 e che restituisce un [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) valore corrispondente a tale valore. Il `Color` valore viene calcolato dall'interpolazione di [ `R` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.R/), [ `G` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.G/), [ `B` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.B/), e [ `A` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.A/) i valori dei due fornito `Color` argomenti. Il `Color` valore viene quindi passato al metodo di callback per una proprietà specifica dell'applicazione.

Questo approccio consente la `ColorTo` metodo per aggiungere un'animazione a qualsiasi [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) proprietà, come illustrato nell'esempio di codice seguente:

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

In questo esempio di codice, il `ColorTo` metodo aggiunge un'animazione di [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) e [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/) le proprietà di un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/), il `BackgroundColor`proprietà di una pagina e [ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) proprietà di un [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/).

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare il [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) classe per creare e annullare le animazioni, sincronizzare più animazioni e creare animazioni personalizzate che animare le proprietà che non sono animate da animazione esistente metodi. La `Animation` classe è il blocco predefinito di tutte le animazioni di xamarin. Forms.


## <a name="related-links"></a>Collegamenti correlati

- [Animazioni personalizzate (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/custom/)
- [Animazione](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)
- [AnimationExtensions](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/)
