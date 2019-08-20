---
title: Animazione principale in Novell. iOS
description: Questo articolo esamina il Framework di animazione principale, mostrando come consente animazioni ad alte prestazioni e fluide in UIKit, oltre a come usarlo direttamente per il controllo di animazione di livello inferiore.
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 4a5e4766321babebed9a84b37590ef1d50bfc77e
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69621119"
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

 `UIViewController`fornisce supporto incorporato per la transizione tra i controller di visualizzazione tramite il `PresentViewController` metodo. Quando si `PresentViewController`USA, la transizione al secondo controller può essere facoltativamente animata.

Si consideri, ad esempio, un'applicazione con due controller, in cui toccare un pulsante `PresentViewController` nel primo controller chiama per visualizzare un secondo controller. Per controllare quale animazione di transizione viene utilizzata per visualizzare il secondo controller, è sufficiente [`ModalTransitionStyle`](xref:UIKit.UIModalTransitionStyle) impostare la proprietà come illustrato di seguito:

```csharp
SecondViewController vc2 = new SecondViewController {
  ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

In questo caso viene `PartialCurl` usata un'animazione, sebbene siano disponibili diverse altre, tra cui:

- `CoverVertical`: Scorre verso l'alto dalla parte inferiore dello schermo
- `CrossDissolve`-La visualizzazione precedente si dissolve & la nuova visualizzazione si dissolve in
- `FlipHorizontal`-Un flip orizzontale da destra a sinistra. In fase di chiusura la transizione viene invertita da sinistra a destra.


Per aggiungere un'animazione alla transizione, `true` passare come secondo argomento a `PresentViewController`:

```csharp
PresentViewController (vc2, true, null);
```

Lo screenshot seguente mostra l'aspetto della transizione per il `PartialCurl` caso:

 ![](core-animation-images/06-view-transitions.png "Questa schermata mostra la transizione PartialCurl")

### <a name="view-transitions"></a>Visualizza transizioni

Oltre alle transizioni tra controller, UIKit supporta anche l'animazione delle transizioni tra le visualizzazioni per scambiare una visualizzazione per un'altra.

Si immagini, ad esempio, di disporre di `UIImageView`un controller con, in cui il tocco sull'immagine dovrebbe `UIImageView`visualizzare un secondo. Per aggiungere un'animazione alla visualizzazione della visualizzazione immagine per passare alla seconda visualizzazione immagine, è sufficiente chiamare `UIView.Transition`, passandogli `toView` e `fromView` come illustrato di seguito:

```csharp
UIView.Transition (
  fromView: view1,
  toView: view2,
  duration: 2,
  options: UIViewAnimationOptions.TransitionFlipFromTop |
    UIViewAnimationOptions.CurveEaseInOut,
  completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition`accetta anche un `duration` parametro che controlla il tempo di esecuzione dell'animazione, [`options`](xref:UIKit.UIViewAnimationOptions) oltre a specificare elementi quali l'animazione da usare e la funzione di interpolazione. Inoltre, è possibile specificare un gestore di completamento che verrà chiamato al termine dell'animazione.

La schermata seguente mostra la transizione animata tra le visualizzazioni immagine quando `TransitionFlipFromTop` si usa:

 ![](core-animation-images/07-animated-transition.png "Questa schermata mostra la transizione animata tra le visualizzazioni immagine quando si usa TransitionFlipFromTop")

### <a name="view-property-animations"></a>Visualizza animazioni proprietà

UIKit supporta l' `UIView` animazione gratuita di un'ampia gamma di proprietà nella classe, tra cui:

- Frame
- Limiti
- Center
- Alfa
- Transform
- Colore


Queste animazioni si verificano in modo implicito specificando `NSAction` le modifiche alle proprietà in `UIView.Animate` un delegato passato al metodo statico. Il codice seguente, ad esempio, anima il punto centrale di un `UIImageView`oggetto:

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

 ![](core-animation-images/08-animate-center.png "Un'immagine che si anima avanti e indietro nella parte superiore della schermata come output")

Come nel `Transition` metodo, `Animate` consente di impostare la durata, insieme alla funzione di interpolazione. Questo esempio usa anche l' `UIViewAnimationOptions.Autoreverse` opzione, che fa in modo che l'animazione venga animata dal valore di nuovo a quella iniziale. Tuttavia, il codice imposta anche di `Center` nuovo il valore iniziale in un gestore di completamento. Mentre un'animazione esegue l'interpolazione dei valori delle proprietà nel tempo, il valore effettivo del modello della proprietà è sempre il valore finale impostato. In questo esempio, il valore è un punto vicino al lato destro della SuperView. Senza impostare l' `Center` oggetto sul punto iniziale, che è il punto in cui l'animazione viene completata `Autoreverse` a causa dell'impostazione di, l'immagine si blocca sul lato destro dopo il completamento dell'animazione, come illustrato di seguito:

 ![](core-animation-images/09-animation-complete.png "Senza impostare il centro sul punto iniziale, l'immagine si blocca al lato destro dopo il completamento dell'animazione")

## <a name="using-core-animation"></a>Uso dell'animazione Core

 `UIView`le animazioni consentono una grande quantità di funzionalità e dovrebbero essere utilizzate, se possibile, a causa della semplicità di implementazione. Come indicato in precedenza, le animazioni UIView usano il Framework di animazione principale. Tuttavia, alcune operazioni non possono essere eseguite `UIView` con animazioni, ad esempio l'animazione di proprietà aggiuntive che non possono essere animate con una visualizzazione o l'interpolazione lungo un percorso non lineare. In questi casi, in cui è necessario un controllo più preciso, è possibile usare direttamente l'animazione di base.

### <a name="layers"></a>Livelli

Quando si lavora con l'animazione di base, l'animazione avviene tramite *livelli*, `CALayer`che sono di tipo. Un livello è concettualmente simile a una vista in quanto è presente una gerarchia di livelli, molto simile a una gerarchia di visualizzazione. In realtà, i livelli delle visualizzazioni, con la visualizzazione, aggiungono il supporto per l'interazione dell'utente. È possibile accedere al livello di qualsiasi visualizzazione tramite la `Layer` proprietà della visualizzazione. Infatti, il contesto utilizzato nel `Draw` metodo di `UIView` viene effettivamente creato dal livello. Internamente, il livello a cui `UIView` è associato un delegato è impostato sulla visualizzazione stessa, ovvero sulle chiamate `Draw`. Quindi, quando si disegna `UIView`in un oggetto, si sta effettivamente disegnando sul livello.

Le animazioni livello possono essere implicite o esplicite. Le animazioni implicite sono dichiarative. È sufficiente dichiarare le proprietà del livello che devono essere modificate e l'animazione funziona semplicemente. Le animazioni esplicite d'altra parte vengono create tramite una classe di animazione aggiunta a un livello. Le animazioni esplicite consentono un controllo aggiuntivo sulla modalità di creazione di un'animazione. Le sezioni seguenti illustrano in dettaglio le animazioni implicite ed esplicite.

### <a name="implicit-animations"></a>Animazioni implicite

Un modo per animare le proprietà di un livello è tramite un'animazione implicita. `UIView`animazioni creare animazioni implicite. Tuttavia, è possibile creare animazioni implicite direttamente anche su un livello.

Il codice seguente, ad esempio, imposta un livello `Contents` da un'immagine, imposta la larghezza e il colore del bordo e aggiunge il livello come sottolivello del livello della visualizzazione:

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

Per aggiungere un'animazione implicita per il livello, è sufficiente eseguire il wrapping `CATransaction`delle modifiche alle proprietà in un oggetto. Ciò consente di animare le `BorderWidth` proprietà che non possono essere animate con un'animazione di visualizzazione, ad esempio e `BorderColor` come illustrato di seguito:

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

Questo codice aggiunge anche un'animazione al livello `Position`, ovvero la posizione del punto di ancoraggio del livello misurato dall'angolo superiore sinistro delle coordinate del superlivello. Il punto di ancoraggio di un livello è un punto normalizzato all'interno del sistema di coordinate del livello.

Nella figura seguente vengono illustrati la posizione e il punto di ancoraggio:

 ![](core-animation-images/10-postion-anchorpt.png "Questa figura mostra la posizione e il punto di ancoraggio")

Quando l'esempio viene eseguito, `Position` `BorderWidth` e `BorderColor` animano come illustrato nelle schermate seguenti:

 ![](core-animation-images/11-implicit-animation.png "Quando si esegue l'esempio, la posizione, BorderWidth e BorderColor animano come illustrato")

### <a name="explicit-animations"></a>Animazioni esplicite

Oltre alle animazioni implicite, l'animazione principale include un'ampia gamma di classi che `CAAnimation` ereditano da che consentono di incapsulare le animazioni che vengono aggiunte in modo esplicito a un livello. Consentono un controllo più granulare sulle animazioni, ad esempio la modifica del valore iniziale di un'animazione, il raggruppamento di animazioni e la specifica di fotogrammi chiave per consentire percorsi non lineari.

Il codice seguente illustra un esempio di un'animazione esplicita che `CAKeyframeAnimation` usa un oggetto per il livello illustrato in precedenza (nella sezione Animazione implicita):

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

Questo codice modifica la `Position` del livello creando un percorso che viene quindi usato per definire un'animazione del fotogramma chiave. `Position` Si noti che il livello è impostato sul valore finale `Position` di dall'animazione. In caso contrario, il livello tornerà improvvisamente al relativo `Position` prima dell'animazione perché l'animazione modifica solo il valore di presentazione e non il valore effettivo del modello. Impostando il valore del modello sul valore finale dall'animazione, il livello rimane sul posto alla fine dell'animazione.

Gli screenshot seguenti mostrano il livello contenente l'animazione dell'immagine nel percorso specificato:

 ![](core-animation-images/12-explicit-animation.png "Questa schermata mostra il livello che contiene l'immagine che aggiunge l'animazione al percorso specificato")
 
## <a name="summary"></a>Riepilogo

In questo articolo sono state esaminate le funzionalità di animazione fornite tramite i Framework di *animazione di base* . È stata esaminata l'animazione principale, che mostra sia il modo in cui le animazioni in UIKit e come può essere utilizzata direttamente per il controllo di animazione di basso livello.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di animazione principale](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation)
- [Core Graphics](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Procedura dettagliata per grafica e animazione](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core Animation](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
