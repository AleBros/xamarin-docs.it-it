---
title: Animazione principale in Novell. iOS
description: Questo articolo esamina il Framework di animazione principale, mostrando come consente animazioni ad alte prestazioni e fluide in UIKit, oltre a come usarlo direttamente per il controllo di animazione di livello inferiore.
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 60bab56440fc7227e14d31875a8b6108cd1a86f3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032489"
---
# <a name="core-animation-in-xamarinios"></a>Animazione principale in Novell. iOS

_Questo articolo esamina il Framework di animazione principale, mostrando come consente animazioni ad alte prestazioni e fluide in UIKit, oltre a come usarlo direttamente per il controllo di animazione di livello inferiore._

iOS include l' [*animazione di base*](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html) per fornire il supporto dell'animazione per le visualizzazioni nell'applicazione.
Tutte le animazioni estremamente uniformi in iOS, ad esempio lo scorrimento di tabelle e l'esecuzione di scorrimenti tra le diverse visualizzazioni, eseguono la stessa operazione perché si basano internamente sull'animazione di base.

L'animazione principale e i Framework di grafica principale possono collaborare per creare grafica 2D accattivante e animata. In realtà, l'animazione core può anche trasformare grafica 2D nello spazio 3D, creando esperienze cinematografiche straordinarie. Tuttavia, per creare immagini 3D reali, è necessario usare un elemento come OpenGL ES o per i giochi a un'API, ad esempio monogame, sebbene il 3D esula dall'ambito di questo articolo.

<a name="Using_Core_Animation" />

## <a name="core-animation"></a>Core Animation

iOS usa il Framework di animazione principale per creare effetti di animazione come la transizione tra le visualizzazioni, i menu scorrevoli e gli effetti di scorrimento per citarne alcuni. Esistono due modi per lavorare con l'animazione:

- [Tramite UIKit](#Using_UIKit_Animation), che include animazioni basate sulla visualizzazione e transizioni animate tra i controller.
- [Tramite l'animazione principale](#Using_Core_Animation), che consente di definire direttamente i livelli, consentendo un controllo più granulare.

<a name="Using_UIKit_Animation" />

## <a name="using-uikit-animation"></a>Uso dell'animazione UIKit

UIKit offre diverse funzionalità che semplificano l'aggiunta di animazioni a un'applicazione. Sebbene usi internamente l'animazione core, la estrae in modo da lavorare solo con le visualizzazioni e i controller.

Questa sezione illustra le funzionalità di animazione UIKit, tra cui:

- Transizioni tra controller
- Transizioni tra viste
- Visualizza animazione proprietà

### <a name="view-controller-transitions"></a>Transizioni dei controller di visualizzazione

 `UIViewController` fornisce supporto incorporato per la transizione tra i controller di visualizzazione tramite il metodo `PresentViewController`. Quando si usa `PresentViewController`, è possibile scegliere di animare la transizione al secondo controller.

Si consideri, ad esempio, un'applicazione con due controller, in cui toccare un pulsante nel primo controller chiama `PresentViewController` per visualizzare un secondo controller. Per controllare quale animazione di transizione viene utilizzata per visualizzare il secondo controller, è sufficiente impostare la relativa proprietà [`ModalTransitionStyle`](xref:UIKit.UIModalTransitionStyle) come illustrato di seguito:

```csharp
SecondViewController vc2 = new SecondViewController {
  ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

In questo caso viene usata un'animazione `PartialCurl`, sebbene siano disponibili diverse altre, tra cui:

- `CoverVertical`: scorre verso l'alto dalla parte inferiore dello schermo
- `CrossDissolve`: la visualizzazione precedente si dissolve & la nuova visualizzazione si dissolve in
- `FlipHorizontal`: un capovolgimento orizzontale da destra a sinistra. In fase di chiusura la transizione viene invertita da sinistra a destra.

Per aggiungere un'animazione alla transizione, passare `true` come secondo argomento per `PresentViewController`:

```csharp
PresentViewController (vc2, true, null);
```

Lo screenshot seguente mostra l'aspetto della transizione per il `PartialCurl` caso:

 ![](core-animation-images/06-view-transitions.png "This screenshot shows the PartialCurl transition")

### <a name="view-transitions"></a>Visualizza transizioni

Oltre alle transizioni tra controller, UIKit supporta anche l'animazione delle transizioni tra le visualizzazioni per scambiare una visualizzazione per un'altra.

Si immagini, ad esempio, di avere un controller con `UIImageView`, in cui il tocco sull'immagine dovrebbe visualizzare un secondo `UIImageView`. Per aggiungere un'animazione alla visualizzazione della visualizzazione immagine per passare alla seconda visualizzazione immagine, è sufficiente chiamare `UIView.Transition`, passando la `toView` e `fromView` come illustrato di seguito:

```csharp
UIView.Transition (
  fromView: view1,
  toView: view2,
  duration: 2,
  options: UIViewAnimationOptions.TransitionFlipFromTop |
    UIViewAnimationOptions.CurveEaseInOut,
  completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` accetta anche un parametro di `duration` che controlla il tempo di esecuzione dell'animazione, nonché [`options`](xref:UIKit.UIViewAnimationOptions) per specificare elementi quali l'animazione da usare e la funzione di interpolazione. Inoltre, è possibile specificare un gestore di completamento che verrà chiamato al termine dell'animazione.

La schermata seguente mostra la transizione animata tra le visualizzazioni immagine quando si usa `TransitionFlipFromTop`:

 ![](core-animation-images/07-animated-transition.png "This screenshot shows the animated transition between the image views when TransitionFlipFromTop is used")

### <a name="view-property-animations"></a>Visualizza animazioni proprietà

UIKit supporta l'animazione gratuita di un'ampia gamma di proprietà nella classe `UIView`, tra cui:

- Frame
- Limiti
- Center
- Alfa
- Transform
- Colore

Queste animazioni si verificano in modo implicito specificando le modifiche alle proprietà in un delegato `NSAction` passato al metodo `UIView.Animate` statico. Il codice seguente, ad esempio, anima il punto centrale di un `UIImageView`:

```csharp
pt = imgView.Center;

UIView.Animate (
  duration: 2, 
  delay: 0, 
  options: UIViewAnimationOptions.CurveEaseInOut | 
    UIViewAnimationOptions.Autoreverse,
  animation: () => {
    imgView.Center = new CGPoint (View.Bounds.GetMaxX () 
      - imgView.Frame.Width / 2, pt.Y);},
  completion: () => {
    imgView.Center = pt; }
);
```

In questo modo si ottiene un'immagine di animazione avanti e indietro nella parte superiore della schermata, come illustrato di seguito:

 ![](core-animation-images/08-animate-center.png "An image animating back and forth across the top of the screen as the output")

Come per il metodo `Transition`, `Animate` consente l'impostazione della durata, insieme alla funzione di interpolazione. Questo esempio usa anche l'opzione `UIViewAnimationOptions.Autoreverse`, che fa in modo che l'animazione venga animata dal valore di nuovo a quella iniziale. Tuttavia, il codice imposta anche il `Center` di nuovo sul valore iniziale in un gestore di completamento. Mentre un'animazione esegue l'interpolazione dei valori delle proprietà nel tempo, il valore effettivo del modello della proprietà è sempre il valore finale impostato. In questo esempio, il valore è un punto vicino al lato destro della SuperView. Senza impostare il `Center` sul punto iniziale, che è il punto in cui l'animazione viene completata a causa dell'impostazione del `Autoreverse`, l'immagine si blocca a destra dopo il completamento dell'animazione, come illustrato di seguito:

 ![](core-animation-images/09-animation-complete.png "Without setting the Center to the initial point, the image would snap back to the right side after the animation completes")

## <a name="using-core-animation"></a>Uso dell'animazione Core

 `UIView` animazioni consentono una grande quantità di funzionalità e devono essere utilizzate, se possibile, a causa della semplicità di implementazione. Come indicato in precedenza, le animazioni UIView usano il Framework di animazione principale. Tuttavia, alcune operazioni non possono essere eseguite con `UIView` animazioni, ad esempio l'animazione di proprietà aggiuntive che non possono essere animate con una visualizzazione o l'interpolazione lungo un percorso non lineare. In questi casi, in cui è necessario un controllo più preciso, è possibile usare direttamente l'animazione di base.

### <a name="layers"></a>Livelli

Quando si lavora con l'animazione di base, l'animazione avviene tramite *livelli*, che sono di tipo `CALayer`. Un livello è concettualmente simile a una vista in quanto è presente una gerarchia di livelli, molto simile a una gerarchia di visualizzazione. In realtà, i livelli delle visualizzazioni, con la visualizzazione, aggiungono il supporto per l'interazione dell'utente. È possibile accedere al livello di qualsiasi visualizzazione tramite la proprietà `Layer` della visualizzazione. In realtà, il contesto utilizzato nel `Draw` metodo di `UIView` viene effettivamente creato dal livello. Internamente, il livello che supporta un `UIView` dispone del delegato impostato sulla visualizzazione stessa, ovvero sulle chiamate che `Draw`. Quindi, quando si disegna in una `UIView`, si sta effettivamente disegnando sul livello.

Le animazioni livello possono essere implicite o esplicite. Le animazioni implicite sono dichiarative. È sufficiente dichiarare le proprietà del livello che devono essere modificate e l'animazione funziona semplicemente. Le animazioni esplicite d'altra parte vengono create tramite una classe di animazione aggiunta a un livello. Le animazioni esplicite consentono un controllo aggiuntivo sulla modalità di creazione di un'animazione. Le sezioni seguenti illustrano in dettaglio le animazioni implicite ed esplicite.

### <a name="implicit-animations"></a>Animazioni implicite

Un modo per animare le proprietà di un livello è tramite un'animazione implicita. `UIView` animazioni creano animazioni implicite. Tuttavia, è possibile creare animazioni implicite direttamente anche su un livello.

Il codice seguente, ad esempio, imposta la `Contents` di un livello da un'immagine, imposta la larghezza e il colore del bordo e aggiunge il livello come sottolivello del livello della visualizzazione:

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();

  layer = new CALayer ();
  layer.Bounds = new CGRect (0, 0, 50, 50);
  layer.Position = new CGPoint (50, 50);
  layer.Contents = UIImage.FromFile ("monkey2.png").CGImage;
  layer.ContentsGravity = CALayer.GravityResize;
  layer.BorderWidth = 1.5f;
  layer.BorderColor = UIColor.Green.CGColor;

  View.Layer.AddSublayer (layer);
}
```

Per aggiungere un'animazione implicita per il livello, è sufficiente eseguire il wrapping delle modifiche delle proprietà in una `CATransaction`. Ciò consente di animare le proprietà che non possono essere animate con un'animazione di visualizzazione, ad esempio il `BorderWidth` e `BorderColor`, come illustrato di seguito:

```csharp
public override void ViewDidAppear (bool animated)
{
  base.ViewDidAppear (animated);

  CATransaction.Begin ();
  CATransaction.AnimationDuration = 10;
  layer.Position = new CGPoint (50, 400);
  layer.BorderWidth = 5.0f;
  layer.BorderColor = UIColor.Red.CGColor;
  CATransaction.Commit ();
}
```

Questo codice aggiunge anche un'animazione all'`Position`del livello, ovvero la posizione del punto di ancoraggio del livello misurato dall'angolo superiore sinistro delle coordinate del superlivello. Il punto di ancoraggio di un livello è un punto normalizzato all'interno del sistema di coordinate del livello.

Nella figura seguente vengono illustrati la posizione e il punto di ancoraggio:

 ![](core-animation-images/10-postion-anchorpt.png "This figure shows the position and anchor point")

Quando si esegue l'esempio, il `Position`, `BorderWidth` e `BorderColor` un'animazione, come illustrato nelle schermate seguenti:

 ![](core-animation-images/11-implicit-animation.png "When the example is run, the Position, BorderWidth and BorderColor animate as shown")

### <a name="explicit-animations"></a>Animazioni esplicite

Oltre alle animazioni implicite, l'animazione principale include un'ampia gamma di classi che ereditano da `CAAnimation` che consentono di incapsulare le animazioni che vengono aggiunte in modo esplicito a un livello. Consentono un controllo più granulare sulle animazioni, ad esempio la modifica del valore iniziale di un'animazione, il raggruppamento di animazioni e la specifica di fotogrammi chiave per consentire percorsi non lineari.

Il codice seguente illustra un esempio di un'animazione esplicita che usa un `CAKeyframeAnimation` per il livello illustrato in precedenza (nella sezione Animazione implicita):

```csharp
public override void ViewDidAppear (bool animated)
{
  base.ViewDidAppear (animated);
  
  // get the initial value to start the animation from
  CGPoint fromPt = layer.Position;
  
  /* set the position to coincide with the final animation value
  to prevent it from snapping back to the starting position
  after the animation completes*/
  layer.Position = new CGPoint (200, 300);
  
  // create a path for the animation to follow
  CGPath path = new CGPath ();
  path.AddLines (new CGPoint[] { fromPt, new CGPoint (50, 300), new CGPoint (200, 50), new CGPoint (200, 300) });
  
  // create a keyframe animation for the position using the path
  CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
  animPosition.Path = path;
  animPosition.Duration = 2;
  
  // add the animation to the layer.
  /* the "position" key is used to overwrite the implicit animation created
  when the layer positino is set above*/
  layer.AddAnimation (animPosition, "position");
}
```

Questo codice modifica la `Position` del livello creando un percorso che viene quindi usato per definire un'animazione del fotogramma chiave. Si noti che il `Position` del livello è impostato sul valore finale della `Position` dall'animazione. In caso contrario, il livello tornerà improvvisamente alla relativa `Position` prima dell'animazione perché l'animazione modifica solo il valore di presentazione e non il valore effettivo del modello. Impostando il valore del modello sul valore finale dall'animazione, il livello rimane sul posto alla fine dell'animazione.

Gli screenshot seguenti mostrano il livello contenente l'animazione dell'immagine nel percorso specificato:

 ![](core-animation-images/12-explicit-animation.png "This screenshot shows the layer containing the image animating through the specified path")

## <a name="summary"></a>Riepilogo

In questo articolo sono state esaminate le funzionalità di animazione fornite tramite i Framework di *animazione di base* . È stata esaminata l'animazione principale, che mostra sia il modo in cui le animazioni in UIKit e come può essere utilizzata direttamente per il controllo di animazione di basso livello.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di animazione principale](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation)
- [Core Graphics](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Procedura dettagliata per grafica e animazione](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core Animation](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
