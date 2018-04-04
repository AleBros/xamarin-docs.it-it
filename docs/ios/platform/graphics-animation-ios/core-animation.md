---
title: Animazione di base
description: In questo articolo esamina il framework di animazione Core, che mostra come consente prestazioni elevate, animazioni fluide in UIKit, e come utilizzarlo direttamente per il controllo animazione di livello inferiore.
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: b89b93a90e13072e36140b79081b90dcde1cdb05
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="core-animation"></a>Animazione di base

_In questo articolo esamina il framework di animazione Core, che mostra come consente prestazioni elevate, animazioni fluide in UIKit, e come utilizzarlo direttamente per il controllo animazione di livello inferiore._

iOS include [ *animazione Core* ](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html) per fornire il supporto di animazione per le viste nell'applicazione.
Tutte le animazioni accurate in iOS, ad esempio lo scorrimento delle tabelle e il passaggio tra diverse visualizzazioni eseguire anche come avviene in quanto dipendono dal Core animazione internamente.

I framework Core animazione e grafica di base possono essere usati insieme per creare efficaci, grafica 2D animata. In realtà animazione Core può anche trasformare la grafica 2D nello spazio 3D, la creazione di esperienze cinematografiche sorprendenti. Tuttavia, per creare la grafica 3D true, è necessario utilizzare un elemento come OpenGL ES, oppure per giochi attiva a un'API, ad esempio MonoGame, sebbene 3D non rientra nell'ambito di questo articolo.

<a name="Using_Core_Animation" />

## <a name="core-animation"></a>Animazione di base

iOS utilizza il framework di animazione di base per creare effetti di animazione, ad esempio la transizione tra le visualizzazioni, scorrere i menu e lo scorrimento effetti per citarne alcune. Esistono due modi per lavorare con animazione:

- [Tramite UIKit](#Using_UIKit_Animation), che include le animazioni basate sulla visualizzazione, nonché transizioni animate tra i controller.
- [Tramite l'animazione Core](#Using_Core_Animation), quali livelli direttamente, consentendo di controllo più preciso.

<a name="Using_UIKit_Animation" />

## <a name="using-uikit-animation"></a>Utilizzo dell'animazione UIKit

UIKit fornisce diverse funzionalità che rendono più semplice aggiungere un'animazione a un'applicazione. Anche se viene usato internamente animazione Core, estrae, in modo da utilizzare solo con le visualizzazioni e controller.

In questa sezione vengono descritte le funzionalità di animazione UIKit tra cui:

-  Transizioni tra i controller
-  Transizioni tra le visualizzazioni
-  Animazione di proprietà di visualizzazione


### <a name="view-controller-transitions"></a>Visualizzazione Controller transizioni

 `UIViewController` fornisce supporto incorporato per la transizione tra il controller di visualizzazione tramite il `PresentViewController` metodo. Quando si utilizza `PresentViewController`, la transizione al secondo controller può facoltativamente essere animata.

Ad esempio, si consideri un'applicazione con due controller, dove toccando un pulsante nel primo controller chiama `PresentViewController` per visualizzare un secondo controller. Per controllare quali animazioni di transizione viene utilizzato per mostrare il secondo controller, è sufficiente impostare il relativo [ `ModalTransitionStyle` ](https://developer.xamarin.com/api/type/UIKit.UIModalTransitionStyle/) proprietà come illustrato di seguito:

```csharp
SecondViewController vc2 = new SecondViewController {
    ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

In questo caso un `PartialCurl` animazione viene utilizzata anche se molte altre sono disponibili, tra cui:

-  `CoverVertical` – Diapositive backup dalla parte inferiore della schermata
-  `CrossDissolve` – La vecchia vista in dissolvenza & la nuova vista con dissolvenza in entrata
-  `FlipHorizontal` -Capovolge orizzontale destra-sinistra. In licenziamento la transizione capovolge da sinistra a destra.


Per animare la transizione, passare `true` come secondo argomento per `PresentViewController`:

```csharp
PresentViewController (vc2, true, null);
```

La schermata seguente mostra l'aspetto della transizione il `PartialCurl` case:

 ![](core-animation-images/06-view-transitions.png "Questa schermata mostra la transizione PartialCurl")

### <a name="view-transitions"></a>Visualizzare le transizioni

Oltre alle transizioni tra i controller, UIKit supporta anche animazione transizioni tra le visualizzazioni per scambiare una visualizzazione per un altro.

Ad esempio, ad esempio si dispone di un controller con `UIImageView`, in cui selezionando l'immagine deve essere visualizzato un secondo `UIImageView`. Animare l'immagine è semplice come chiamare il metodo superview della vista per eseguire la transizione alla seconda visualizzazione immagine `UIView.Transition`, passandogli il `toView` e `fromView` come illustrato di seguito:

```csharp
UIView.Transition (
    fromView: view1,
    toView: view2,
    duration: 2,
    options: UIViewAnimationOptions.TransitionFlipFromTop |
        UIViewAnimationOptions.CurveEaseInOut,
    completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` accetta inoltre un `duration` parametro che controlla il tempo viene eseguita l'animazione, così come [ `options` ](https://developer.xamarin.com/api/type/UIKit.UIViewAnimationOptions/) per specificare, ad esempio, l'animazione da usare e la funzione di interpolazione. Inoltre, è possibile specificare un gestore di completamento che verrà chiamato quando il completamento dell'animazione.

Nella schermata seguente mostra la transizione tra l'immagine animata viste quando `TransitionFlipFromTop` viene utilizzato:

 ![](core-animation-images/07-animated-transition.png "Questa schermata mostra la transizione tra le visualizzazioni di immagine quando viene utilizzato TransitionFlipFromTop animata")

### <a name="view-property-animations"></a>Animazioni di proprietà di visualizzazione

UIKit supporta l'animazione in una serie di proprietà di `UIView` classe gratuitamente, tra cui:

-  Frame
-  Limiti
-  Center
-  Alfa
-  Transform
-  Colore


Tali animazioni verificarsi in modo implicito specificando le modifiche alle proprietà in un `NSAction` delegato passato al metodo statico `UIView.Animate` metodo. Ad esempio, il codice seguente aggiunge un'animazione il punto centrale di un `UIImageView`:

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

Risultati in un'immagine di animazione avanti e indietro nella parte superiore dello schermo, come illustrato di seguito:

 ![](core-animation-images/08-animate-center.png "Un'immagine di animazione avanti e indietro nella parte superiore della schermata come output")

Come con la `Transition` metodo `Animate` consente la durata impostare, con la funzione di interpolazione. In questo esempio viene inoltre utilizzato il `UIViewAnimationOptions.Autoreverse` opzione che causa la aggiungere un'animazione dal valore alla iniziale dell'animazione. Tuttavia, il codice imposta inoltre il `Center` il valore iniziale in un gestore di completamento. Durante un'animazione è nell'interpolazione dei valori di proprietà nel tempo, il valore del modello effettivo della proprietà è sempre il valore finale è stato impostato. In questo esempio, il valore è un punto accanto al lato destro della superview. Senza impostare il `Center` per il punto iniziale, ovvero in cui il completamento dell'animazione a causa del `Autoreverse` viene impostata, l'immagine sarebbe tornano a destra dopo il completamento dell'animazione, come illustrato di seguito:

 ![](core-animation-images/09-animation-complete.png "Senza impostare il centro e il punto iniziale, l'immagine sarebbe tornano a destra dopo il completamento dell'animazione")

## <a name="using-core-animation"></a>Utilizzo dell'animazione Core

 `UIView` le animazioni consentono molte funzionalità e devono essere utilizzate se possibile grazie alla facilità di implementazione. Come accennato in precedenza, le animazioni UIView utilizzano framework Core animazione. Tuttavia, alcune operazioni non possono essere eseguite con `UIView` animazioni, ad esempio l'animazione di proprietà aggiuntive che non possono essere animate con una vista o nell'interpolazione lungo un percorso non lineare. In tali casi in cui è necessario un controllo più accurato, il testo di animazione Core può essere usata direttamente anche.

### <a name="layers"></a>Livelli

Quando si lavora con animazione Core, animazione avviene tramite *livelli*, che sono di tipo `CALayer`. Un livello è concettualmente simile a una vista e non è presente una gerarchia di livello, molto c'è una gerarchia di visualizzazione. I livelli in realtà, eseguire il backup di visualizzazioni, con la visualizzazione aggiunta del supporto per l'interazione dell'utente. È possibile accedere a livello di qualsiasi visualizzazione mediante la visualizzazione `Layer` proprietà. In realtà, il contesto utilizzato `Draw` metodo `UIView` venga effettivamente creato dal livello. Internamente, il livello di esecuzione del backup un `UIView` è il delegato impostato per la vista stessa, ovvero cosa chiama `Draw`. In tal caso, durante il disegno a un `UIView`, si sta creando effettivamente al relativo livello.

Le animazioni di livello possono essere implicite o esplicite. Le animazioni implicite sono dichiarative. È sufficiente dichiarare ciò che è necessario modificare le proprietà dei livelli e funziona solo l'animazione. D'altro canto, animazioni esplicite vengono create tramite una classe di animazione che viene aggiunto a un livello. Le animazioni esplicite consentono di controllare inoltre la modalità di creazione di un'animazione. Nelle sezioni seguenti è approfondire le animazioni implicite ed esplicite in modo più approfondito.

### <a name="implicit-animations"></a>Animazioni implicite

È possibile animare la proprietà di un livello è tramite un'animazione implicita. `UIView` le animazioni creano animazioni implicite. Tuttavia, è possibile creare animazioni implicite direttamente su un livello oltre.

Ad esempio, il codice seguente imposta un livello `Contents` da un'immagine, imposta uno spessore del bordo e un colore e aggiunge il livello come un sottolivello del livello della vista:

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

Per aggiungere un'animazione implicita per il livello, eseguire semplicemente il wrapping di modifiche delle proprietà in un `CATransaction`. In questo modo l'animazione di proprietà che non sarebbe possibile animare con un'animazione di visualizzazione, ad esempio il `BorderWidth` e `BorderColor` come illustrato di seguito:

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

Questo codice aggiunge anche un'animazione del livello `Position`, che rappresenta la posizione del punto di ancoraggio del livello misurata dall'angolo superiore sinistro di coordinate del superlayer. Il punto di ancoraggio di un livello è un punto normalizzato all'interno di sistema di coordinate del livello.

La figura seguente illustra il punto di ancoraggio e di posizione:

 ![](core-animation-images/10-postion-anchorpt.png "Questa figura mostra il punto di posizione e ancoraggio")

Quando si esegue l'esempio, il `Position`, `BorderWidth` e `BorderColor` animare come illustrato nelle schermate seguenti:

 ![](core-animation-images/11-implicit-animation.png "Quando si esegue l'esempio, come illustrato animare la posizione, BorderWidth e BorderColor")

### <a name="explicit-animations"></a>Animazioni esplicite

Oltre alle animazioni implicite, animazione Core include una varietà di classi che ereditano da `CAAnimation` che consentono di incapsulare le animazioni che vengono quindi aggiunti in modo esplicito a un livello. Questi consentono il controllo preciso le animazioni, ad esempio modificando il valore di inizio di un'animazione, animazioni di raggruppamento e l'impostazione di fotogrammi chiave per consentire i percorsi non lineare.

Il codice seguente viene illustrato un esempio di un'animazione esplicita mediante un `CAKeyframeAnimation` per il livello illustrato in precedenza (nella sezione animazione implicita):

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

Questo codice viene modificato il `Position` del livello mediante la creazione di un percorso che viene quindi utilizzato per definire un'animazione con fotogramma chiave. Si noti che il livello `Position` è impostato il valore finale di `Position` dall'animazione. In caso contrario, il livello restituirebbe improvvisamente a relativo `Position` prima l'animazione perché l'animazione modifica solo il valore di presentazione e non il valore del modello effettivo. Impostando il valore del modello per il valore finale dall'animazione, il livello rimangono nella stessa posizione alla fine dell'animazione.

La schermata seguente mostra il livello contenente l'immagine di animazione tramite il percorso specificato:

 ![](core-animation-images/12-explicit-animation.png "Questa schermata mostra il livello contenente l'immagine di animazione tramite il percorso specificato")
 
## <a name="summary"></a>Riepilogo

In questo articolo è stato esaminato le funzionalità di animazione fornite tramite il *animazione Core* Framework. Viene esaminato l'animazione di Core, che mostra sia la modalità consente di creare le animazioni in UIKit e come può essere usato direttamente per il controllo animazione di livello inferiore.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di animazione core](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [Core Graphics](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Grafica e animazione di questa procedura dettagliata](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core Animation](https://developer.xamarin.com/recipes/ios/animation/coreanimation)
