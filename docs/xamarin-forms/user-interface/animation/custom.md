---
title: Animazioni personalizzate inXamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4fb9c94c39823e4ce6d60be6b9dbef1294321a63
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137228"
---
# <a name="custom-animations-in-xamarinforms"></a>Animazioni personalizzate inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)

_La classe Animation è il blocco predefinito di tutte le Xamarin.Forms animazioni, con i metodi di estensione nella classe ViewExtensions che creano uno o più oggetti animazione. Questo articolo illustra come usare la classe Animation per creare e annullare animazioni, sincronizzare più animazioni e creare animazioni personalizzate che animano le proprietà che non sono animate dai metodi di animazione esistenti._

È necessario specificare un numero di parametri quando si crea un `Animation` oggetto, inclusi i valori iniziale e finale della proprietà a cui si sta aggiungendo un'animazione e un callback che modifica il valore della proprietà. Un `Animation` oggetto può anche gestire una raccolta di animazioni figlio che possono essere eseguite e sincronizzate. Per altre informazioni, vedere [animazioni figlio](#child).

L'esecuzione di un'animazione creata con la [`Animation`](xref:Xamarin.Forms.Animation) classe, che può includere o meno animazioni figlio, viene eseguita chiamando [ `Commit` ] (xrif: Xamarin.Forms . Animation. Commit ( Xamarin.Forms . IAnimatable, System. String, System. UInt32, System. UInt32, Xamarin.Forms . Metodo di interpolazione, System. Action {System. Double, System. Boolean}, System. Func {System. Boolean}). Questo metodo specifica la durata dell'animazione e, tra gli altri elementi, un callback che controlla se ripetere l'animazione.

Inoltre, la [`Animation`](xref:Xamarin.Forms.Animation) classe dispone di una `IsEnabled` proprietà che può essere esaminata per determinare se le animazioni sono state disabilitate dal sistema operativo, ad esempio quando è attivata la modalità di risparmio energia.

## <a name="create-an-animation"></a>Creare un'animazione

Quando si crea un [`Animation`](xref:Xamarin.Forms.Animation) oggetto, in genere sono necessari almeno tre parametri, come illustrato nell'esempio di codice seguente:

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

Questo codice definisce un'animazione della [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) proprietà di un' [`Image`](xref:Xamarin.Forms.Image) istanza di da un valore 1 a un valore di 2. Il valore animato, che è derivato da Xamarin.Forms , viene passato al callback specificato come primo argomento, dove viene usato per modificare il valore della `Scale` Proprietà.

L'animazione viene avviata con una chiamata a [ `Commit` ] (xrif: Xamarin.Forms . Animation. Commit ( Xamarin.Forms . IAnimatable, System. String, System. UInt32, System. UInt32, Xamarin.Forms . Metodo di interpolazione, System. Action {System. Double, System. Boolean}, System. Func {System. Boolean}), come illustrato nell'esempio di codice seguente:

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

Si noti che [ `Commit` ] (xrif: Xamarin.Forms . Animation. Commit ( Xamarin.Forms . IAnimatable, System. String, System. UInt32, System. UInt32, Xamarin.Forms . Il metodo di interpolazione, System. Action {System. Double, System. Boolean}, System. Func {System. Boolean})) non restituisce un `Task` oggetto. Le notifiche vengono invece fornite tramite metodi di callback.

Nel metodo vengono specificati gli argomenti seguenti `Commit` :

- Il primo argomento (*proprietario*) identifica il proprietario dell'animazione. Può trattarsi dell'elemento visivo a cui è applicata l'animazione o di un altro elemento visivo, ad esempio la pagina.
- Il secondo argomento (*Name*) identifica l'animazione con un nome. Il nome viene combinato con il proprietario per identificare in modo univoco l'animazione. Questa identificazione univoca può quindi essere utilizzata per determinare se l'animazione è in esecuzione ([ `AnimationIsRunning` ] (xrif: Xamarin.Forms . AnimationExtensions. AnimationIsRunning ( Xamarin.Forms . IAnimatable, System. String)) o per annullarlo ([ `AbortAnimation` ] (xrif: Xamarin.Forms . AnimationExtensions. AbortAnimation ( Xamarin.Forms . IAnimatable, System. String))).
- Il terzo argomento (*frequenza*) indica il numero di millisecondi tra ciascuna chiamata al metodo di callback definito nel [`Animation`](xref:Xamarin.Forms.Animation) Costruttore
- Il quarto argomento (*length*) indica la durata dell'animazione, in millisecondi.
- Il quinto argomento (*interpolazione*) definisce la funzione di interpolazione da utilizzare nell'animazione. In alternativa, la funzione di interpolazione può essere specificata come argomento per il [`Animation`](xref:Xamarin.Forms.Animation) costruttore. Per ulteriori informazioni sulle funzioni di interpolazione, vedere [funzioni di interpolazione](~/xamarin-forms/user-interface/animation/easing.md).
- Il sesto argomento (*terminato*) è un callback che verrà eseguito al termine dell'animazione. Questo callback accetta due argomenti, con il primo argomento che indica un valore finale e il secondo argomento è `bool` impostato su `true` se l'animazione è stata annullata. In alternativa, è possibile specificare il callback *completato* come argomento del [`Animation`](xref:Xamarin.Forms.Animation) costruttore. Tuttavia, con un'unica animazione, se i callback *finiti* vengono specificati sia nel `Animation` costruttore che nel `Commit` metodo, verrà eseguito solo il callback specificato nel `Commit` metodo.
- Il settimo argomento (*Repeat*) è un callback che consente di ripetere l'animazione. Viene chiamato alla fine dell'animazione e la restituzione `true` indica che l'animazione deve essere ripetuta.

L'effetto complessivo consiste nel creare un'animazione che aumenta la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) proprietà di un [`Image`](xref:Xamarin.Forms.Image) da 1 a 2, più di 2 secondi (2000 millisecondi), usando la [`Linear`](xref:Xamarin.Forms.Easing.Linear) funzione di interpolazione. Ogni volta che l'animazione viene completata, la relativa `Scale` proprietà viene reimpostata su 1 e l'animazione viene ripetuta.

> [!NOTE]
> Le animazioni simultanee, che vengono eseguite in modo indipendente l'una dall'altra, possono essere costruite creando un `Animation` oggetto per ogni animazione, quindi chiamando il `Commit` metodo su ogni animazione.

<a name="child" />

### <a name="child-animations"></a>Animazioni figlio

La [`Animation`](xref:Xamarin.Forms.Animation) classe supporta anche animazioni figlio, che comporta la creazione `Animation` di un oggetto a cui `Animation` vengono aggiunti altri oggetti. In questo modo è possibile eseguire e sincronizzare una serie di animazioni. Nell'esempio di codice seguente viene illustrata la creazione e l'esecuzione di animazioni figlio:

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

In entrambi gli esempi di codice [`Animation`](xref:Xamarin.Forms.Animation) viene creato un oggetto padre a cui `Animation` vengono aggiunti altri oggetti. I primi due argomenti per [ `Add` ] (xrif: Xamarin.Forms . Animazione. Add (System. Double, System. Double, Xamarin.Forms . Animazione)) specificare quando iniziare e terminare l'animazione figlio. I valori degli argomenti devono essere compresi tra 0 e 1 e rappresentano il periodo relativo all'interno dell'animazione padre che l'animazione figlio specificata sarà attiva. Pertanto, in questo esempio l'oggetto sarà `scaleUpAnimation` attivo per la prima metà dell'animazione, `scaleDownAnimation` sarà attivo per la seconda metà dell'animazione e l'oggetto sarà `rotateAnimation` attivo per l'intera durata.

L'effetto complessivo è che l'animazione si verifica in più di 4 secondi (4000 millisecondi). Il `scaleUpAnimation` aggiunge un'animazione alla [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) proprietà da 1 a 2, più di 2 secondi. Il `scaleDownAnimation` quindi aggiunge un'animazione alla `Scale` proprietà da 2 a 1, più di 2 secondi. Quando si verificano entrambe le animazioni di ridimensionamento, il `rotateAnimation` aggiunge un'animazione alla [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) proprietà da 0 a 360, in più di 4 secondi. Si noti che le animazioni di ridimensionamento usano anche le funzioni di interpolazione. La [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) funzione di interpolazione causa la [`Image`](xref:Xamarin.Forms.Image) compattazione iniziale di prima di ottenere una dimensione maggiore e la [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) funzione di interpolazione fa sì che l'oggetto `Image` diventi più piccolo della dimensione effettiva verso la fine dell'animazione completa.

Esistono diverse differenze tra un [`Animation`](xref:Xamarin.Forms.Animation) oggetto che usa animazioni figlio e uno che non:

- Quando si usano animazioni figlio, il callback *finito* su un'animazione figlio indica quando l'elemento figlio è stato completato e il callback *terminato* passato al `Commit` metodo indica quando l'intera animazione è stata completata.
- Quando si usano animazioni figlio, la restituzione `true` dal callback di *ripetizione* sul `Commit` metodo non comporta la ripetizione dell'animazione, ma l'esecuzione dell'animazione continuerà senza nuovi valori.
- Quando si include una funzione di interpolazione nel `Commit` metodo e la funzione di interpolazione restituisce un valore maggiore di 1, l'animazione verrà terminata. Se la funzione di interpolazione restituisce un valore minore di 0, il valore viene impostato su 0. Per usare una funzione di interpolazione che restituisce un valore minore di 0 o maggiore di 1, deve essere specificata in una delle animazioni figlio anziché nel `Commit` metodo.

La [`Animation`](xref:Xamarin.Forms.Animation) classe include anche [ `WithConcurrent` ] (xrif: Xamarin.Forms . Animation. WithConcurrent ( Xamarin.Forms . Metodi Animation, System. Double, System. Double)) che possono essere usati per aggiungere animazioni figlio a un `Animation` oggetto padre. Tuttavia, i valori degli argomenti *Begin* e *Finish* non sono limitati a 0 e 1, ma solo quella parte dell'animazione figlio che corrisponde a un intervallo compreso tra 0 e 1 sarà attiva. Se, ad esempio, una `WithConcurrent` chiamata al metodo definisce un'animazione figlio destinata a una [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) proprietà da 1 a 6, ma con i valori *Begin* e *Finish* di-2 e 3, il valore di *inizio* di-2 corrisponde al `Scale` valore 1 e il valore *finale* di 3 corrisponde a un `Scale` valore pari a 6. Poiché i valori non compresi nell'intervallo compreso tra 0 e 1 non fanno parte di un'animazione, la `Scale` proprietà verrà animata solo da 3 a 6.

## <a name="cancel-an-animation"></a>Annulla un'animazione

Un'applicazione può annullare un'animazione con una chiamata a [ `AbortAnimation` ] (xrif: Xamarin.Forms . AnimationExtensions. AbortAnimation ( Xamarin.Forms . Metodo di estensione IAnimatable, System. String), come illustrato nell'esempio di codice seguente:

```csharp
this.AbortAnimation ("SimpleAnimation");
```

Si noti che le animazioni vengono identificate in modo univoco da una combinazione del proprietario dell'animazione e dal nome dell'animazione. Pertanto, è necessario specificare il proprietario e il nome specificati durante l'esecuzione dell'animazione per annullare l'animazione. Pertanto, l'esempio di codice annulla immediatamente l'animazione denominata di `SimpleAnimation` proprietà della pagina.

## <a name="create-a-custom-animation"></a>Creare un'animazione personalizzata

Gli esempi illustrati finora hanno dimostrato animazioni che potrebbero essere ugualmente realizzate con i metodi della [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe. Tuttavia, il vantaggio della [`Animation`](xref:Xamarin.Forms.Animation) classe è che può accedere al metodo di callback, che viene eseguito quando il valore animato viene modificato. Ciò consente al callback di implementare qualsiasi animazione desiderata. Ad esempio, l'esempio di codice seguente aggiunge un'animazione alla [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) proprietà di una pagina impostando i [`Color`](xref:Xamarin.Forms.Color) valori creati dal [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) metodo, con valori di tonalità compresi tra 0 e 1:

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

L'animazione risultante fornisce l'aspetto dell'avanzamento dello sfondo della pagina tramite i colori dell'arcobaleno.

Per altri esempi di creazione di animazioni complesse, inclusa un'animazione a curva di Bezier, vedere il [capitolo 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf) della pagina relativa alla [creazione di app Xamarin.Forms per dispositivi mobili con ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="create-a-custom-animation-extension-method"></a>Creare un metodo di estensione dell'animazione personalizzata

I metodi di estensione nella [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe animano una proprietà dal relativo valore corrente a un valore specificato. Questo rende difficile creare, ad esempio, un metodo di `ColorTo` animazione che può essere usato per animare un colore da un valore a un altro, perché:

- L'unica [`Color`](xref:Xamarin.Forms.Color) proprietà definita dalla [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe è [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) , che non è sempre la `Color` proprietà desiderata a cui aggiungere un'animazione.
- Spesso il valore corrente di una [`Color`](xref:Xamarin.Forms.Color) proprietà è [`Color.Default`](xref:Xamarin.Forms.Color.Default) , che non è un colore reale e che non può essere usato nei calcoli di interpolazione.

La soluzione a questo problema consiste nel non fare in modo che il metodo abbia come `ColorTo` destinazione una particolare [`Color`](xref:Xamarin.Forms.Color) Proprietà. È invece possibile scriverlo con un metodo di callback che passa di nuovo il valore interpolato `Color` al chiamante. Inoltre, il metodo accetta gli argomenti iniziale e finale `Color` .

Il `ColorTo` metodo può essere implementato come metodo di estensione che usa il [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) metodo nella [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) classe per fornirne le funzionalità. Questo è dovuto al fatto `Animate` che il metodo può essere usato per le proprietà di destinazione che non sono di tipo `double` , come illustrato nell'esempio di codice seguente:

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

Il [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) metodo richiede un argomento *Transform* , che è un metodo di callback. L'input per questo callback è sempre un valore `double` compreso tra 0 e 1. Pertanto, il `ColorTo` metodo definisce la propria trasformazione `Func` che accetta un `double` valore compreso tra 0 e 1 e che restituisce un [`Color`](xref:Xamarin.Forms.Color) valore corrispondente a tale valore. Il `Color` valore viene calcolato tramite l'interpolazione dei [`R`](xref:Xamarin.Forms.Color.R) [`G`](xref:Xamarin.Forms.Color.G) valori,, [`B`](xref:Xamarin.Forms.Color.B) e [`A`](xref:Xamarin.Forms.Color.A) dei due `Color` argomenti forniti. Il `Color` valore viene quindi passato al metodo di callback per l'applicazione a una particolare proprietà.

Questo approccio consente al `ColorTo` metodo di animare qualsiasi [`Color`](xref:Xamarin.Forms.Color) proprietà, come illustrato nell'esempio di codice seguente:

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

In questo esempio di codice, il `ColorTo` Metodo anima le [`TextColor`](xref:Xamarin.Forms.Label.TextColor) [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) proprietà e di un oggetto [`Label`](xref:Xamarin.Forms.Label) , la `BackgroundColor` proprietà di una pagina e la [`Color`](xref:Xamarin.Forms.BoxView.Color) proprietà di un oggetto [`BoxView`](xref:Xamarin.Forms.BoxView) .

## <a name="related-links"></a>Collegamenti correlati

- [Animazioni personalizzate (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)
- [API animazione](xref:Xamarin.Forms.Animation)
- [API AnimationExtensions](xref:Xamarin.Forms.AnimationExtensions)
