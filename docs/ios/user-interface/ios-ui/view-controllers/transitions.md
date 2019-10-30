---
title: Visualizzare le transizioni del controller in Novell. iOS
description: Questo documento descrive come personalizzare le transizioni animate tra i controller di visualizzazione nelle applicazioni Novell. iOS.
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: f0886ac9d47e7ab08a6f74365bcc25163b803e11
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002400"
---
# <a name="view-controller-transitions-in-xamarinios"></a>Visualizzare le transizioni del controller in Novell. iOS

UIKit aggiunge il supporto per la personalizzazione della transizione animata che si verifica quando si presentano i controller di visualizzazione. Questo supporto è incluso nei controller incorporati, nonché in qualsiasi controller personalizzato che eredita direttamente da `UIViewController`. Inoltre, `UICollectionViewController` sfrutta i vantaggi della personalizzazione della transizione del controller per sfruttare le transizioni animate nei layout delle visualizzazioni di raccolta.

## <a name="custom-transitions"></a>Transizioni personalizzate

La transizione animata tra i controller di visualizzazione in iOS 7 è completamente personalizzabile. `UIViewController` include ora una proprietà `TransitioningDelegate` che fornisce una classe di Animator personalizzata al sistema quando si verifica una transizione.

Per usare una transizione personalizzata con `PresentViewController`:

1. Impostare il `ModalPresentationStyle` su `UIModalPresentationStyle.Custom` sul controller da presentare.
2. Implementare `UIViewControllerTransitioningDelegate` per creare una classe di animazione, che è un'istanza di `UIViewControllerAnimatedTransitioning`.
3. Impostare la proprietà `TransitioningDelegate` su un'istanza di `UIViewControllerTransitioningDelegate`, anche sul controller da presentare.
4. Presentare il controller di visualizzazione.

Il codice seguente, ad esempio, presenta un controller di visualizzazione di tipo `ControllerTwo`-a `UIViewController` sottoclasse:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

Eseguendo l'app e toccando il pulsante, l'animazione predefinita della visualizzazione del secondo controller viene animata dalla parte inferiore, come illustrato di seguito:

 ![](transitions-images/no-custom-transition.png "Running the app and tapping the button causes the default animation of the second controllers view to animate in from the bottom")

Tuttavia, l'impostazione della `ModalPresentationStyle` e della `TransitioningDelegate` comporta un'animazione personalizzata per la transizione:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo () {
        ModalPresentationStyle = UIModalPresentationStyle.Custom
        };

    transitioningDelegate = new TransitioningDelegate ();
    controllerTwo.TransitioningDelegate = transitioningDelegate;

    this.PresentViewController (controllerTwo, true, null);
};
```

Il `TransitioningDelegate` è responsabile della creazione di un'istanza della sottoclasse `UIViewControllerAnimatedTransitioning`, denominata `CustomAnimator` nell'esempio seguente:

```csharp
public class TransitioningDelegate : UIViewControllerTransitioningDelegate
{
    CustomTransitionAnimator animator;

    public override IUIViewControllerAnimatedTransitioning GetAnimationControllerForPresentedController (UIViewController presented, UIViewController presenting, UIViewController source)
    {
        animator = new CustomTransitionAnimator ();
        return animator;
    }
}
```

Quando viene effettuata la transizione, il sistema crea un'istanza di `IUIViewControllerContextTransitioning`, passata ai metodi dell'animatore. `IUIViewControllerContextTransitioning` contiene la `ContainerView` in cui si verifica l'animazione, nonché il controller di visualizzazione che avvia la transizione e la transizione del controller di visualizzazione a.

La classe `UIViewControllerAnimatedTransitioning` gestisce l'animazione effettiva. È necessario implementare due metodi:

1. `TransitionDuration`: restituisce la durata dell'animazione in secondi.
1. `AnimateTransition`: esegue l'animazione effettiva.

La classe seguente, ad esempio, implementa `UIViewControllerAnimatedTransitioning` per animare il frame della visualizzazione del controller:

```csharp
public class CustomTransitionAnimator : UIViewControllerAnimatedTransitioning
{
    public CustomTransitionAnimator ()
    {
    }

    public override double TransitionDuration (IUIViewControllerContextTransitioning transitionContext)
    {
        return 1.0;
    }

    public override void AnimateTransition (IUIViewControllerContextTransitioning transitionContext)
    {
        var inView = transitionContext.ContainerView;
        var toVC = transitionContext.GetViewControllerForKey (UITransitionContext.ToViewControllerKey);
        var toView = toVC.View;

        inView.AddSubview (toView);

        var frame = toView.Frame;
        toView.Frame = CGRect.Empty;

        UIView.Animate (TransitionDuration (transitionContext), () => {
            toView.Frame = new CGRect (20, 20, frame.Width - 40, frame.Height - 40);
        }, () => {
            transitionContext.CompleteTransition (true);
        });
    }
}
```

A questo punto, quando si tocca il pulsante, viene utilizzata l'animazione implementata nella classe `UIViewControllerAnimatedTransitioning`:

 ![](transitions-images/custom-transition.png "An example of the zoom in effect running")

## <a name="collection-view-transitions"></a>Transizioni visualizzazione raccolta

Le visualizzazioni di raccolta includono il supporto incorporato per la creazione di transizioni animate:

- **Controller di spostamento** : la transizione animata tra due istanze di `UICollectionViewController` può facoltativamente essere gestita automaticamente quando una `UINavigationController` la gestisce.
- **Layout di transizione** : una nuova classe `UICollectionViewTransitionLayout` consente la transizione interattiva tra i layout.

### <a name="navigation-controller-transitions"></a>Transizioni del controller di spostamento

Quando viene usato in un controller di spostamento, un `UICollectionViewController` include il supporto per le transizioni animate tra i controller. Questo supporto è integrato e richiede solo pochi semplici passaggi da implementare:

1. Impostare `UseLayoutToLayoutNavigationTransitions` su `false` per un `UICollectionViewController`.
1. Aggiungere un'istanza del `UICollectionViewController` alla radice dello stack del controller di spostamento.
1. Creare una seconda `UICollectionViewController` e impostare la relativa proprietà `UseLayoutToLayoutNavigtionTransitions` su `true`.
1. Eseguire il push della seconda `UICollectionViewController` nello stack del controller di spostamento.

Il codice seguente aggiunge una sottoclasse `UICollectionViewController` denominata `ImagesCollectionViewController` alla radice di uno stack del controller di spostamento, con la proprietà `UseLayoutToLayoutNavigationTransitions` impostata su `false`:

```csharp
UIWindow window;
ImagesCollectionViewController viewController;
UICollectionViewFlowLayout layout;
UINavigationController navController;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
    window = new UIWindow (UIScreen.MainScreen.Bounds);

    // create and initialize a UICollectionViewFlowLayout
    layout = new UICollectionViewFlowLayout (){
        SectionInset = new UIEdgeInsets (10,5,10,5),
        MinimumInteritemSpacing = 5,
        MinimumLineSpacing = 5,
        ItemSize = new CGSize (100, 100)
    };

    viewController = new ImagesCollectionViewController (layout) {
            UseLayoutToLayoutNavigationTransitions = false
        };

    navController = new UINavigationController (viewController);

    window.RootViewController = navController;
    window.MakeKeyAndVisible ();

    return true;
}
```

Quando viene selezionato un elemento, viene creata una seconda istanza del `ImagesController`, solo questa volta usando una classe layout diversa. Per questo controller, `UseLayoutToLayoutNavigtionTransitions` è impostato su `true`, come illustrato di seguito:

```csharp
CircleLayout circleLayout;
ImagesCollectionViewController controller2;

...

public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // UseLayoutToLayoutNavigationTransitions when item is selected
    circleLayout = new CircleLayout (Monkeys.Instance.Count){
        ItemSize = new CGSize (100, 100)
    };

    controller2 = new ImagesCollectionViewController (circleLayout) {
        UseLayoutToLayoutNavigationTransitions = true
        };

    NavigationController.PushViewController (controller2, true);
}
```

Prima di aggiungere il controller allo stack di navigazione, è necessario impostare la proprietà `UseLayoutToLayoutNavigationTransitions`. Se questa proprietà è impostata, la normale transizione orizzontale viene sostituita da una transizione animata tra i layout dei due controller, come illustrato di seguito:

![](transitions-images/nav2.png "An animated transition between the layouts of the two controllers")

### <a name="transition-layout"></a>Layout di transizione

Oltre al supporto della transizione del layout all'interno di controller di spostamento, è ora disponibile un nuovo layout denominato `UICollectionViewTransitionLayout`. Questa classe di layout consente il controllo interattivo durante il processo di transizione del layout consentendo l'impostazione del `TransitionProgress` dal codice. `UICollectionViewTransitionLayout` è diverso da e non è una sostituzione per il metodo `SetCollectionViewLayout` da iOS 6 che ha causato la transizione del layout animato. Questo metodo non forniva supporto incorporato per il controllo dello stato della transizione animata.

 `UICollectionViewTransitionLayout` consente, ad esempio, di configurare un riconoscitore di movimento per controllare la transizione tra i layout in risposta all'interazione dell'utente, gestendo il layout originale e il layout previsto per la transizione a.

Di seguito sono riportati i passaggi per implementare una transizione interattiva in un sistema di riconoscimento dei movimenti usando `UICollectionViewTransitionLayout`:

1. Creare un riconoscitore di movimento.
1. Chiamare il metodo `StartInteractiveTransition` della `UICollectionView`, passandogli il layout di destinazione e un gestore di completamento.
1. Impostare la proprietà `TransitionProgress` dell'istanza di `UICollectionViewTransitionLayout` restituita dal metodo `StartInteractiveTransition`.
1. Invalidare il layout.
1. Chiamare il metodo `FinishInteractiveTransition` della `UICollectionView` per completare la transizione o il metodo `CancelInteractiveTransition` per annullarlo.  `FinishInteractiveTransition` fa in modo che l'animazione completi la transizione al layout di destinazione, mentre `CancelInteractiveTransition` comporta il ritorno dell'animazione al layout originale.
1. Gestire il completamento della transizione nel gestore di completamento del metodo `StartInteractiveTransition`.
1. Aggiungere il riconoscitore di movimento alla visualizzazione della raccolta.

Il codice seguente implementa una transizione interattiva del layout all'interno di un riconoscitore di movimenti di pizzico:

```csharp
imagesController = new ImagesCollectionViewController (flowLayout);

nfloat sf = 0.4f;
UICollectionViewTransitionLayout trLayout = null;
UICollectionViewLayout nextLayout;

pinch = new UIPinchGestureRecognizer (g => {

    var progress = Math.Abs(1.0f -  g.Scale)/sf;

    if(trLayout == null){
        if(imagesController.CollectionView.CollectionViewLayout is CircleLayout)
            nextLayout = flowLayout;
        else
            nextLayout = circleLayout;

        trLayout = imagesController.CollectionView.StartInteractiveTransition (nextLayout, (completed, finished) => {
            Console.WriteLine ("transition completed");
            trLayout = null;
        });
    }

    trLayout.TransitionProgress = (nfloat)progress;

    imagesController.CollectionView.CollectionViewLayout.InvalidateLayout ();

    if(g.State == UIGestureRecognizerState.Ended){
        if (trLayout.TransitionProgress > 0.5f)
            imagesController.CollectionView.FinishInteractiveTransition ();
        else
            imagesController.CollectionView.CancelInteractiveTransition ();
    }

});

imagesController.CollectionView.AddGestureRecognizer (pinch);

```

Quando l'utente pizzica la visualizzazione della raccolta, la `TransitionProgress` viene impostata in relazione alla scala del pizzico. In questa implementazione, se l'utente termina il pizzico prima che la transizione sia completata al 50%, la transizione viene annullata. In caso contrario, la transizione è terminata.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione ad iOS 7 (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [Panoramica dell'interfaccia utente di iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Elaborazione in background](~/ios/app-fundamentals/backgrounding/index.md)
