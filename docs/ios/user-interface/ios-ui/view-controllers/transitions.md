---
title: Transizioni dei Controller di visualizzazione in xamarin. IOS
description: Questo documento descrive come personalizzare le transizioni animate tra i controller di visualizzazione nelle applicazioni xamarin. IOS.
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 6711d3af06619aa54a2d735cb83862ed64abacec
ms.sourcegitcommit: 06f88979db160fb8dd1c9ee0d5000d8749107489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2018
ms.locfileid: "53806899"
---
# <a name="view-controller-transitions-in-xamarinios"></a>Transizioni dei Controller di visualizzazione in xamarin. IOS

UIKit aggiunge il supporto per la personalizzazione la transizione animata che si verifica quando la presentazione di controller di visualizzazione. Questo supporto è incluso con i controller predefiniti, nonché qualsiasi controller personalizzato che eredita direttamente da `UIViewController`. Inoltre, `UICollectionViewController` sfrutta i vantaggi di personalizzazione di transizione di controller per sfruttare le transizioni animate nel layout di visualizzazione raccolta.

## <a name="custom-transitions"></a>Transizioni personalizzate

La transizione animata tra i controller di visualizzazione in iOS 7 è completamente personalizzabile. `UIViewController` include ora un `TransitioningDelegate` proprietà che fornisce una classe animatore personalizzato al sistema quando si verifica una transizione.

Usare una transizione personalizzata con `PresentViewController`:

1.  Impostare il `ModalPresentationStyle` a `UIModalPresentationStyle.Custom` nel controller da presentare.
2.  Implementare `UIViewControllerTransitioningDelegate` per creare una classe Animatore, ovvero un'istanza di `UIViewControllerAnimatedTransitioning` .
3.  Impostare il `TransitioningDelegate` proprietà a un'istanza di `UIViewControllerTransitioningDelegate` , anche nel controller da presentare.
4.  Presentare il controller di visualizzazione.


Ad esempio, il codice seguente offre un controller di visualizzazione di tipo `ControllerTwo` : un `UIViewController` sottoclasse:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

L'esecuzione dell'app e toccare il pulsante fa sì che l'animazione predefinito di visualizzazione del controller al secondo da cui iniziare l'animazione nella parte inferiore, come illustrato di seguito:

 ![](transitions-images/no-custom-transition.png "L'esecuzione dell'app e toccare il pulsante fa sì che l'animazione predefinita della seconda visualizzazione controller da cui iniziare l'animazione nella parte inferiore")

Tuttavia, impostando il `ModalPresentationStyle` e `TransitioningDelegate` comporta un'animazione personalizzata per la transizione:

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

Il `TransitioningDelegate` è responsabile della creazione di un'istanza del `UIViewControllerAnimatedTransitioning` sottoclasse - denominato `CustomAnimator` nell'esempio seguente:

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

Quando avviene la transizione, il sistema crea un'istanza di `IUIViewControllerContextTransitioning`, che passato ai metodi del animatore. `IUIViewControllerContextTransitioning` contiene il `ContainerView` in cui si verifica l'animazione, nonché il controller di visualizzazione avvia la transizione e il controller di visualizzazione in fase di transizione a.

Il `UIViewControllerAnimatedTransitioning` classe gestisce l'animazione effettivo. Devono essere implementati due metodi:

1.  `TransitionDuration` : restituisce la durata dell'animazione in pochi secondi.
1.  `AnimateTransition` : esegue l'animazione effettivo.


Ad esempio, la classe seguente implementa `UIViewControllerAnimatedTransitioning` per animare il frame di visualizzazione del controller:

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

A questo punto, quando tocco del pulsante, l'animazione implementati nel `UIViewControllerAnimatedTransitioning` classe viene usata:

 ![](transitions-images/custom-transition.png "Un esempio dello zoom in vigore in esecuzione")

## <a name="collection-view-transitions"></a>Transizioni di visualizzazione raccolta

Le visualizzazioni di raccolta dispongono di supporto incorporato per la creazione di transizioni animate:

-  **I controller di spostamento** : l'animazione di transizione tra due `UICollectionViewController` istanze possono facoltativamente essere gestite automaticamente quando un `UINavigationController` li gestisce.
-  **Layout di transizione** -un nuovo `UICollectionViewTransitionLayout` classe consente interattiva in fase di transizione da un layout.


### <a name="navigation-controller-transitions"></a>Transizioni dei Controller di spostamento

Se utilizzata all'interno di un controller di spostamento, un `UICollectionViewController` include il supporto per le transizioni animate tra i controller. Questo supporto è integrato e richiede solo pochi semplici passaggi per implementare:

1.  Impostare `UseLayoutToLayoutNavigationTransitions` al `false` su un `UICollectionViewController` .
1.  Aggiungere un'istanza del `UICollectionViewController` nella radice dello stack del controller di spostamento.
1.  Creare una seconda `UICollectionViewController` e impostare relativi `UseLayoutToLayoutNavigtionTransitions` proprietà `true` .
1.  Eseguire il push della seconda `UICollectionViewController` nello stack del controller di spostamento.


Il codice seguente aggiunge un `UICollectionViewController` sottoclasse denominata `ImagesCollectionViewController` alla radice dello stack del controller di spostamento, con la `UseLayoutToLayoutNavigationTransitions` impostata su `false`:

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

Quando l'elemento è selezionato, una seconda istanza del `ImagesController` creato, solo questa volta usando una classe di un layout diverso. Per questo controller `UseLayoutToLayoutNavigtionTransitions` è impostata su `true`, come illustrato di seguito:

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

Il `UseLayoutToLayoutNavigationTransitions` deve essere impostata prima di aggiungere il controller allo stack di navigazione. Con questa proprietà è impostata, la transizione di scorrimento orizzontale normale viene sostituita con una transizione animata tra il layout dei due controller, come illustrato di seguito:

![](transitions-images/nav2.png "Una transizione tra il layout dei due controller animata")

### <a name="transition-layout"></a>Layout di transizione

Oltre al supporto di transizione di layout all'interno di controller di spostamento, un nuovo layout chiamato `UICollectionViewTransitionLayout` è ora disponibile. Questa classe layout consente il controllo interattivo durante il processo di transizione di layout, consentendo la `TransitionProgress` a essere impostata dal codice. `UICollectionViewTransitionLayout` è diverso da - e non una sostituzione per - la `SetCollectionViewLayout` metodo da iOS 6 che ha causato una transizione layout animata. Tale metodo non ha fornito il supporto incorporato per controllare lo stato di avanzamento della transizione animata.

 `UICollectionViewTransitionLayout` consente, ad esempio, un riconoscitore di movimento da configurare per controllare la transizione da un layout in risposta all'interazione dell'utente, gestendo il layout originale, nonché il layout desiderato per la transizione a.

I passaggi per implementare una transizione interattiva all'interno di un riconoscimento di movimento utilizzando `UICollectionViewTransitionLayout` sono i seguenti:

1.  Creare un riconoscitore di movimento.
1.  Chiamare il `StartInteractiveTransition` metodo di `UICollectionView` , passandogli il layout di destinazione e un gestore di completamento.
1.  Impostare il `TransitionProgress` proprietà del `UICollectionViewTransitionLayout` istanza restituita dal `StartInteractiveTransition` (metodo).
1.  Invalida il layout.
1.  Chiamare il `FinishInteractiveTransition` metodo per il `UICollectionView` per completare la transizione o `CancelInteractiveTransition` metodo per annullare l'operazione.  `FinishInteractiveTransition` fa sì che l'animazione completare la transizione al layout di destinazione, mentre `CancelInteractiveTransition` comporta l'animazione restituendo il layout originale.
1.  Gestire il completamento della transizione nel gestore di completamento il `StartInteractiveTransition` (metodo).
1.  Aggiungere il riconoscitore di movimento per la visualizzazione di raccolta.


Il codice seguente implementa una transizione di layout interattiva all'interno di un sistema di riconoscimento di movimento zoom indietro:

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

Quando l'utente pinches la visualizzazione di raccolta, il `TransitionProgress` è impostata rispetto alla scala di zoom indietro. In questa implementazione, se l'utente termina la con avvicinamento delle dita prima la transizione è 50% completato, la transizione viene annullata. In caso contrario, viene completata la transizione.




## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a iOS 7 (esempio)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Panoramica dell'interfaccia utente di iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Elaborazione in background](~/ios/app-fundamentals/backgrounding/index.md)
