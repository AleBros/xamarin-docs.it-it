---
title: Visualizzazione Controller transizioni
ms.topic: article
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 88849d3007c007a5ac8820ca84083aa01459c4ce
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="view-controller-transitions"></a>Visualizzazione Controller transizioni

UIKit aggiunge il supporto per la personalizzazione la transizione di animazione che si verifica quando la presentazione di controller di visualizzazione. Questo supporto è incluso con controller incorporati, nonché tutti i controller personalizzati che ereditano direttamente dalla `UIViewController`. Inoltre, `UICollectionViewController` si avvale di personalizzazione di transizione controller per sfruttare le transizioni animate nei layout visualizzazione raccolta.

## <a name="custom-transitions"></a>Transizioni personalizzate

La transizione animata tra controller di visualizzazione di iOS 7 è completamente personalizzabile. `UIViewController` include ora un `TransitioningDelegate` proprietà che fornisce una classe personalizzata animatore al sistema quando si verifica una transizione.

Per utilizzare una transizione personalizzata con `PresentViewController`:

1.  Impostare il `ModalPresentationStyle` a `UIModalPresentationStyle.Custom` nel controller da presentare.
2.  Implementare `UIViewControllerTransitioningDelegate` per creare una classe Animatore, ovvero un'istanza di `UIViewControllerAnimatedTransitioning` .
3.  Impostare il `TransitioningDelegate` proprietà a un'istanza di `UIViewControllerTransitioningDelegate` , anche nel controller da presentare.
4.  Presentare il controller di visualizzazione.


Ad esempio, il codice seguente offre un controller di visualizzazione di tipo `ControllerTwo` : `UIViewController` sottoclasse:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

L'esecuzione dell'app e toccare il pulsante fa sì che l'animazione predefinito di visualizzazione del secondo controller da cui iniziare l'animazione nella parte inferiore, come illustrato di seguito:

 ![](transitions-images/no-custom-transition.png "L'esecuzione dell'app e toccare il pulsante fa sì che l'animazione predefinito della vista secondo controller da cui iniziare l'animazione nella parte inferiore")

Tuttavia, l'impostazione di `ModalPresentationStyle` e `TransitioningDelegate` comporta un'animazione personalizzata per la transizione:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo () {
        ModalPresentationStyle = UIModalPresentationStyle.Custom;
        };

    transitioningDelegate = new TransitioningDelegate ();
    controllerTwo.TransitioningDelegate = transitioningDelegate;

    this.PresentViewController (controllerTwo, true, null);
};
```

Il `TransitioningDelegate` è responsabile per la creazione di un'istanza di `UIViewControllerAnimatedTransitioning` sottoclasse - chiamato `CustomAnimator` nell'esempio seguente:

```csharp
public class TransitioningDelegate : UIViewControllerTransitioningDelegate
{
    CustomTransitionAnimator animator;

    public override IUIViewControllerAnimatedTransitioning PresentingController (UIViewController presented, UIViewController presenting, UIViewController source)
    {
        animator = new CustomTransitionAnimator ();
        return animator;
    }
}
```

Quando avviene la transizione, il sistema crea un'istanza di `IUIViewControllerContextTransitioning`, che passato ai metodi dell'animatore. `IUIViewControllerContextTransitioning` contiene il `ContainerView` in cui si verifica l'animazione, nonché il controller di visualizzazione della transizione di avvio e il controller di visualizzazione in fase di transizione.

La `UIViewControllerAnimatedTransitioning` classe gestisce l'animazione effettivo. Implementare due metodi:

1.  `TransitionDuration` : restituisce la durata dell'animazione in secondi.
1.  `AnimateTransition` : esegue l'animazione effettivo.


Ad esempio, la classe seguente implementa `UIViewControllerAnimatedTransitioning` animare il frame di visualizzazione del controller:

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

A questo punto, quando si tocca il pulsante, l'animazione implementati nel `UIViewControllerAnimatedTransitioning` classe viene utilizzata:

 ![](transitions-images/custom-transition.png "Un esempio dello zoom attive in esecuzione")

## <a name="collection-view-transitions"></a>Transizioni di visualizzazione di raccolta

Viste di raccolta con un supporto predefinito per la creazione di transizioni animate:

-  **Navigazione controller** : animata transizione tra due `UICollectionViewController` istanze possono facoltativamente essere gestite automaticamente quando un `UINavigationController` li gestisce.
-  **Transizione Layout** : un nuovo `UICollectionViewTransitionLayout` consente interattiva in fase di transizione a un layout di classe.


### <a name="navigation-controller-transitions"></a>Transizioni di Controller di navigazione

Se utilizzata all'interno di un controller di navigazione, un `UICollectionViewController` include il supporto per le transizioni animate tra i controller. Questo supporto è integrato e richiede solo pochi semplici passaggi per implementare:

1.  Impostare `UseLayoutToLayoutNavigationTransitions` a `false` su un `UICollectionViewController` .
1.  Aggiungere un'istanza di `UICollectionViewController` nella radice dello stack del controller di navigazione.
1.  Creare un secondo `UICollectionViewController` e impostare il relativo `UseLayoutToLayoutNavigtionTransitions` proprietà `true` .
1.  Il secondo push `UICollectionViewController` nello stack del controller di navigazione.


Il codice seguente aggiunge un `UICollectionViewController` sottoclasse denominata `ImagesCollectionViewController` nella radice dello stack di un controller navigazione, con la `UseLayoutToLayoutNavigationTransitions` proprietà impostata su `false`:

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
            UseLayoutToLayoutNavigationTransitions = false;
        }

    navController = new UINavigationController (viewController);

    window.RootViewController = navController;
    window.MakeKeyAndVisible ();
    
    return true;
}
```

Quando si seleziona un elemento, una seconda istanza del `ImagesController` viene creato solo questa volta utilizzando una classe di layout diverso. Per questo controller, `UseLayoutToLayoutNavigtionTransitions` è impostato su `true`, come illustrato di seguito:

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
        UseLayoutToLayoutNavigationTransitions = true;
        }

    NavigationController.PushViewController (controller2, true);
}
```

Il `UseLayoutToLayoutNavigationTransitions` deve essere impostata prima di aggiungere il controller di stack di navigazione. Questa proprietà è impostata, la transizione di scorrimento orizzontale normale viene sostituita con una transizione animata tra i layout dei due controller, come illustrato di seguito:

![](transitions-images/nav2.png "Una transizione tra i layout dei due controller animata")

### <a name="transition-layout"></a>Layout di transizione

Oltre a supporto di transizione di layout all'interno di controller di navigazione, un nuovo layout chiamato `UICollectionViewTransitionLayout` è ora disponibile. Questa classe di layout consente il controllo interattivo durante il processo di transizione di layout, consentendo il `TransitionProgress` per essere impostata dal codice. `UICollectionViewTransitionLayout` è diverso da - e non una sostituzione per - la `SetCollectionViewLayout` iOS 6 che ha causato una transizione animata layout di metodo. Tale metodo non ha fornito il supporto incorporato per controllare lo stato di avanzamento della transizione animata.

 `UICollectionViewTransitionLayout` consente, ad esempio, un riconoscimento di movimento sia configurato per controllare la transizione da un layout in risposta all'interazione dell'utente, gestendo il layout originale, nonché il layout desiderato per la transizione.

La procedura per implementare una transizione interattiva all'interno di un riconoscimento di movimento utilizzando `UICollectionViewTransitionLayout` sono i seguenti:

1.  Creare un riconoscimento di movimento.
1.  Chiamare il `StartInteractiveTransition` metodo il `UICollectionView` , passando il layout di destinazione e un gestore di completamento.
1.  Impostare il `TransitionProgress` proprietà del `UICollectionViewTransitionLayout` istanza restituita dal `StartInteractiveTransition` metodo.
1.  Invalida il layout.
1.  Chiamare il `FinishInteractiveTransition` metodo il `UICollectionView` per completare la transizione o `CancelInteractiveTransition` metodo per annullare l'operazione.  `FinishInteractiveTransition` causa dell'animazione completare la transizione per il layout di destinazione, mentre `CancelInteractiveTransition` comporta l'animazione restituendo il layout originale.
1.  Gestire il completamento della transizione nel gestore di completamento di `StartInteractiveTransition` metodo.
1.  Aggiungere il gestore di riconoscimento per la visualizzazione della raccolta.


Il codice seguente implementa una transizione di layout interattiva all'interno di un riconoscimento di movimento zoom indietro:

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

Come l'utente pinches la visualizzazione di raccolta, il `TransitionProgress` è impostata rispetto alla scala di zoom indietro. In questa implementazione, se l'utente termina di zoom indietro prima della transizione è il 50% completato, la transizione viene annullata. In caso contrario, viene completata la transizione.




## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a iOS 7 (esempio)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Panoramica dell'interfaccia utente di iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Elaborazione in background](~/ios/app-fundamentals/backgrounding/index.md)
