---
title: Animazione di core in xamarin. IOS
description: Questo articolo viene esaminato il framework di Core Animation, che mostra come creare ad alte prestazioni e le animazioni fluide in UIKit, e come usarlo direttamente per il controllo animazione di livello inferiore.
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: a40d0911b7dabc900a4c6e50c692e4f091f22be9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61206260"
---
# <a name="core-animation-in-xamarinios"></a>Animazione di core in xamarin. IOS

_Questo articolo viene esaminato il framework di Core Animation, che mostra come creare ad alte prestazioni e le animazioni fluide in UIKit, e come usarlo direttamente per il controllo animazione di livello inferiore._

iOS comprende [ *Core Animation* ](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html) per fornire supporto per l'animazione per le visualizzazioni nell'applicazione.
Tutte le animazioni Ultra-smooth in iOS, ad esempio lo scorrimento delle tabelle e scorrendo rapidamente tra diverse visualizzazioni eseguire anche come avviene perché si basano su Core Animation internamente.

I framework di grafica di Core e Core Animation possono lavorare insieme per creare eccezionali, grafica 2D animata. In realtà Core Animation possono anche trasformare la grafica 2D nello spazio 3D, la creazione di straordinarie esperienze cinematografiche. Tuttavia, per creare la grafica 3D true, è necessario usare un nome, ad esempio OpenGL ES, o per giochi turno per un'API, ad esempio MonoGame, sebbene 3D non rientra nell'ambito di questo articolo.

<a name="Using_Core_Animation" />

## <a name="core-animation"></a>Core Animation

iOS Usa il framework di Core Animation per creare effetti di animazione, ad esempio la transizione tra le visualizzazioni, i menu scorrevole e scorrimento effetti per citarne alcuni. Esistono due modi per lavorare con animazione:

- [Tramite UIKit](#Using_UIKit_Animation), che include le animazioni basate su Vista, nonché le transizioni animate tra i controller.
- [Tramite Core Animation](#Using_Core_Animation), quali livelli direttamente, consentendo di controllo più granulare.

<a name="Using_UIKit_Animation" />

## <a name="using-uikit-animation"></a>Utilizzo dell'animazione UIKit

UIKit disponibili numerose funzionalità che rendono più semplice aggiungere un'animazione a un'applicazione. Anche se Usa internamente Core Animation, estrae assente per lavorare solo con le visualizzazioni e controller.

Questa sezione vengono illustrate funzionalità di animazione UIKit, tra cui:

-  Transizioni tra i controller
-  Transizioni tra le visualizzazioni
-  Animazione di proprietà di visualizzazione


### <a name="view-controller-transitions"></a>Transizioni dei controller di visualizzazione

 `UIViewController` fornisce supporto incorporato per la transizione tra i controller di visualizzazione tramite i `PresentViewController` (metodo). Quando si usa `PresentViewController`, la transizione al secondo controller può facoltativamente essere animata.

Ad esempio, si consideri un'applicazione con due controller, dove toccando un pulsante nel primo controller chiama `PresentViewController` per visualizzare un secondo controller. Per controllare quali animazione di transizione viene usata per illustrare il secondo controller, è sufficiente impostare relativi [ `ModalTransitionStyle` ](xref:UIKit.UIModalTransitionStyle) proprietà come illustrato di seguito:

```csharp
SecondViewController vc2 = new SecondViewController {
    ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

In questo caso un `PartialCurl` animazione viene utilizzata, anche se molte altre sono disponibili, tra cui:

-  `CoverVertical` -Diapositive backup nella parte inferiore della schermata
-  `CrossDissolve` : La visualizzazione precedente si dissolve & la nuova vista con dissolvenza in entrata
-  `FlipHorizontal` -Capovolgi orizzontale destra-sinistra. In licenziamento la transizione capovolge da sinistra a destra.


Per aggiungere un'animazione di transizione, passare `true` come secondo argomento per `PresentViewController`:

```csharp
PresentViewController (vc2, true, null);
```

Lo screenshot seguente mostra l'aspetto della transizione il `PartialCurl` case:

 ![](core-animation-images/06-view-transitions.png "Questo screenshot Mostra la transizione PartialCurl")

### <a name="view-transitions"></a>Visualizzare le transizioni

Oltre alle transizioni tra i controller, UIKit supporta anche le transizioni di animazione tra le visualizzazioni di scambiare un'unica visualizzazione per un altro.

Ad esempio, ad esempio disponi di un controller con `UIImageView`, in cui se si tocca l'immagine deve essere visualizzato un secondo `UIImageView`. Per animare l'immagine è sufficiente chiamare il metodo superview della visualizzazione per la transizione alla seconda visualizzazione immagine `UIView.Transition`, passando il `toView` e `fromView` come illustrato di seguito:

```csharp
UIView.Transition (
    fromView: view1,
    toView: view2,
    duration: 2,
    options: UIViewAnimationOptions.TransitionFlipFromTop |
        UIViewAnimationOptions.CurveEaseInOut,
    completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` accetta anche un `duration` parametro che consente di controllare quanto tempo l'animazione viene eseguita, nonché [ `options` ](xref:UIKit.UIViewAnimationOptions) specificare elementi quali l'animazione da usare e la funzione di interpolazione. Inoltre, è possibile specificare un gestore di completamento che verrà chiamato al completamento dell'animazione.

Lo screenshot seguente mostra la transizione tra l'immagine animata le viste quando `TransitionFlipFromTop` viene usato:

 ![](core-animation-images/07-animated-transition.png "Questo screenshot Mostra la transizione tra le visualizzazioni di immagine quando si usa TransitionFlipFromTop animata")

### <a name="view-property-animations"></a>Animazioni di proprietà di visualizzazione

UIKit supporta l'animazione di una varietà di proprietà nel `UIView` classe gratuitamente, tra cui:

-  Frame
-  Limiti
-  Center
-  Alfa
-  Transform
-  Colore


Queste animazioni si verificano in modo implicito specificando le modifiche alle proprietà in un' `NSAction` delegato passato al metodo statico `UIView.Animate` (metodo). Ad esempio, il codice seguente aggiunge un'animazione il punto centrale di un `UIImageView`:

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

Questo comporterà un'animazione di immagine e indietro nella parte superiore dello schermo, come illustrato di seguito:

 ![](core-animation-images/08-animate-center.png "Un'immagine animata avanti e indietro nella parte superiore della schermata come output")

Come con le `Transition` metodo `Animate` consente la durata da impostare, con la funzione di interpolazione. In questo esempio viene usato anche il `UIViewAnimationOptions.Autoreverse` opzione che fa sì che l'animazione aggiungere un'animazione dal valore di tornare a quello iniziale. Tuttavia, il codice imposta inoltre il `Center` il valore iniziale in un gestore di completamento. Mentre un'animazione è interpolazione i valori delle proprietà nel corso del tempo, il valore del modello effettivo della proprietà è sempre il valore finale è stato impostato. In questo esempio, il valore è un punto accanto al lato destro della superview. Senza impostare il `Center` al punto iniziale, ovvero in cui l'animazione viene completata a causa dell'errore di `Autoreverse` impostato, l'immagine allineava nuovamente sul lato destro dopo il completamento dell'animazione, come illustrato di seguito:

 ![](core-animation-images/09-animation-complete.png "Senza impostare il centro e il punto iniziale, l'immagine allineava nuovamente sul lato destro dopo il completamento dell'animazione")

## <a name="using-core-animation"></a>Utilizzo di Core Animation

 `UIView` le animazioni consentono numerose funzionalità e usare se possibile dovuta alla semplicità di implementazione. Come accennato in precedenza, le animazioni UIView usano il framework di Core Animation. Tuttavia, alcuni elementi non possono essere eseguite con `UIView` dalle animazioni fisiche, ad esempio l'animazione di proprietà aggiuntive che non può essere aggiunta un'animazione con una vista o interpolazione lungo un percorso non lineare. In questi casi in cui è necessario un controllo più preciso, Core Animation utilizzabile direttamente anche.

### <a name="layers"></a>Livelli

Quando si lavora con Core Animation, animazione avviene tramite *livelli*, che sono di tipo `CALayer`. Un livello è concettualmente simile a una vista non è presente una gerarchia di livello, molto c'è una gerarchia di visualizzazione. In realtà, livelli di eseguire il backup di visualizzazioni, con la visualizzazione aggiunta del supporto per l'interazione dell'utente. È possibile accedere al livello di qualsiasi visualizzazione tramite la vista `Layer` proprietà. In effetti, il contesto usato nel `Draw` metodo di `UIView` venga effettivamente creato dal livello. Internamente, il livello di backup un `UIView` ha il delegato impostato su Vista, che è ciò che chiama `Draw`. Quando si disegna un `UIView`, disegnare effettivamente al relativo livello.

Le animazioni di livello possono essere implicite o esplicite. Le animazioni implicite sono dichiarative. È sufficiente dichiarare ciò che è necessario modificare le proprietà di livello e l'animazione funziona. D'altra parte, animazioni esplicitare vengono create tramite una classe di animazione che viene aggiunto a un livello. Animazioni esplicitare consentono di controllare inoltre le modalità di creazione di un'animazione. Le sezioni seguenti approfondire le animazioni implicite ed esplicite in modo più approfondito.

### <a name="implicit-animations"></a>Animazioni implicite

Un modo per animare le proprietà di un livello è tramite un'animazione implicita. `UIView` le animazioni creano animazioni implicite. Tuttavia, è possibile creare animazioni implicite direttamente a fronte di un livello anche.

Ad esempio, il codice seguente imposta un livello `Contents` da un'immagine, imposta uno spessore del bordo e un colore e aggiunge il livello come un sottolivello del livello della visualizzazione:

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

Per aggiungere un'animazione implicita per il livello, è sufficiente eseguire il wrapping le modifiche alle proprietà in un `CATransaction`. In questo modo l'animazione di proprietà che non è possibile animare con un'animazione di visualizzazione, ad esempio la `BorderWidth` e `BorderColor` come illustrato di seguito:

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

Questo codice aggiunge anche un'animazione del livello `Position`, ovvero la posizione del punto di ancoraggio del livello misurata dall'angolo superiore sinistro di coordinate del superlayer. Il punto di ancoraggio di un livello è un punto normalizzato all'interno di sistema di coordinate del livello.

La figura seguente mostra la posizione e ancoraggio il punto:

 ![](core-animation-images/10-postion-anchorpt.png "La figura seguente mostra il punto di posizione e ancoraggio")

Quando si esegue l'esempio, il `Position`, `BorderWidth` e `BorderColor` animare come illustrato negli screenshot seguenti:

 ![](core-animation-images/11-implicit-animation.png "Quando si esegue l'esempio, come illustrato animare la posizione, stile e BorderColor")

### <a name="explicit-animations"></a>Animazioni esplicitare

Oltre alle animazioni implicite, Core Animation include un'ampia gamma di classi che ereditano da `CAAnimation` che consentono di incapsulare le animazioni che vengono aggiunti in modo esplicito a un livello. Questi consentono il controllo capillare dalle animazioni fisiche, ad esempio modifica il valore iniziale di un'animazione, le animazioni di raggruppamento e specificando i fotogrammi chiave per consentire i percorsi non lineare.

Il codice seguente illustra un esempio di un'animazione esplicita mediante un `CAKeyframeAnimation` per il livello indicato in precedenza (nella sezione implicita animazione):

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

Questo codice viene modificato il `Position` del livello mediante la creazione di un percorso che viene quindi usato per definire un'animazione con fotogramma chiave. Si noti che il livello `Position` è impostata sul valore finale del `Position` dall'animazione. In caso contrario, il livello improvvisamente ritornerà a relativo `Position` prima animazione perché l'animazione modifica solo il valore di presentazione e non l'effettivo valore del modello. Impostando il valore del modello per il valore finale di animazione, il livello rimangono invariate alla fine dell'animazione.

Gli screenshot seguenti mostrano il livello contenente l'immagine animata attraverso il percorso specificato:

 ![](core-animation-images/12-explicit-animation.png "In questo screenshot appare il livello contenente l'immagine animata attraverso il percorso specificato")
 
## <a name="summary"></a>Riepilogo

In questo articolo abbiamo esaminato le funzionalità di animazione fornite tramite il *Core Animation* Framework. Abbiamo esaminato Core Animation, che mostra sia la modalità alimenta le animazioni in UIKit e come può essere usato direttamente per il controllo animazione di livello inferiore.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di animazione core](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [Core Graphics](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Procedura dettagliata di animazione e grafica](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core Animation](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
