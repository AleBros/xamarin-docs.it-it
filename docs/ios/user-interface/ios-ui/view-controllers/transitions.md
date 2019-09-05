---
title: Visualizzare le transizioni del controller in Novell. iOS
description: Questo documento descrive come personalizzare le transizioni animate tra i controller di visualizzazione nelle applicazioni Novell. iOS.
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/14/2017
ms.openlocfilehash: 45d9580899ac42dba300086b5d5694c07d177871
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287826"
---
# <a name="view-controller-transitions-in-xamarinios"></a>Visualizzare le transizioni del controller in Novell. iOS

UIKit aggiunge il supporto per la personalizzazione della transizione animata che si verifica quando si presentano i controller di visualizzazione. Questo supporto è incluso nei controller incorporati, nonché in qualsiasi controller personalizzato che eredita direttamente da `UIViewController`. `UICollectionViewController` Sfrutta inoltre la personalizzazione della transizione del controller per sfruttare le transizioni animate nei layout delle visualizzazioni di raccolta.

## <a name="custom-transitions"></a>Transizioni personalizzate

La transizione animata tra i controller di visualizzazione in iOS 7 è completamente personalizzabile. `UIViewController`include ora una `TransitioningDelegate` proprietà che fornisce una classe di animazione personalizzata al sistema quando si verifica una transizione.

Per usare una transizione personalizzata con `PresentViewController`:

1. `ModalPresentationStyle` Impostaresu`UIModalPresentationStyle.Custom` sul controller da presentare.
2. Implementare `UIViewControllerTransitioningDelegate` per creare una classe animatar, che è un'istanza di `UIViewControllerAnimatedTransitioning` .
3. Impostare la `TransitioningDelegate` proprietà su un'istanza di `UIViewControllerTransitioningDelegate` , anche sul controller da presentare.
4. Presentare il controller di visualizzazione.


Il codice seguente, ad esempio, presenta un controller di visualizzazione `ControllerTwo` di tipo `UIViewController` una sottoclasse:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

Eseguendo l'app e toccando il pulsante, l'animazione predefinita della visualizzazione del secondo controller viene animata dalla parte inferiore, come illustrato di seguito:

 ![](transitions-images/no-custom-transition.png "Eseguendo l'app e toccando il pulsante, l'animazione predefinita della visualizzazione del secondo controller viene animata in dalla parte inferiore")

Tuttavia, l' `ModalPresentationStyle` impostazione di `TransitioningDelegate` e comporta un'animazione personalizzata per la transizione:

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

È responsabile della creazione `UIViewControllerAnimatedTransitioning` di un'istanza della sottoclasse, denominata `CustomAnimator` nell'esempio seguente: `TransitioningDelegate`

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

Quando viene effettuata la transizione, il sistema crea un'istanza di `IUIViewControllerContextTransitioning`, che viene passata ai metodi dell'animazione. `IUIViewControllerContextTransitioning`contiene l' `ContainerView` oggetto in cui si verifica l'animazione, nonché il controller di visualizzazione che avvia la transizione e la transizione del controller di visualizzazione a.

La `UIViewControllerAnimatedTransitioning` classe gestisce l'animazione effettiva. È necessario implementare due metodi:

1. `TransitionDuration`: restituisce la durata dell'animazione in secondi.
1. `AnimateTransition`: esegue l'animazione effettiva.


Ad esempio, la classe seguente implementa `UIViewControllerAnimatedTransitioning` per animare il frame della visualizzazione del controller:

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

A questo punto, quando si tocca il pulsante, viene utilizzata l'animazione `UIViewControllerAnimatedTransitioning` implementata nella classe:

 ![](transitions-images/custom-transition.png "Esempio di zoom attivo in esecuzione")

## <a name="collection-view-transitions"></a>Transizioni visualizzazione raccolta

Le visualizzazioni di raccolta includono il supporto incorporato per la creazione di transizioni animate:

- **Controller di spostamento** : la transizione animata tra `UICollectionViewController` due istanze può essere facoltativamente gestita automaticamente quando `UINavigationController` un oggetto li gestisce.
- **Layout di transizione** : una `UICollectionViewTransitionLayout` nuova classe consente la transizione interattiva tra i layout.


### <a name="navigation-controller-transitions"></a>Transizioni del controller di spostamento

Quando viene usato in un controller di spostamento `UICollectionViewController` , un include il supporto per le transizioni animate tra i controller. Questo supporto è integrato e richiede solo pochi semplici passaggi da implementare:

1. Impostare `UseLayoutToLayoutNavigationTransitions` su su un `UICollectionViewController`oggetto. `false`
1. Aggiungere un'istanza di `UICollectionViewController` alla radice dello stack del controller di spostamento.
1. Creare un secondo `UICollectionViewController` e impostare la `UseLayoutToLayoutNavigtionTransitions` relativa proprietà `true` su.
1. Eseguire il push `UICollectionViewController` del secondo nello stack del controller di spostamento.


Il codice seguente aggiunge una `UICollectionViewController` sottoclasse `ImagesCollectionViewController` denominata alla radice di uno stack del controller di spostamento, con `UseLayoutToLayoutNavigationTransitions` la proprietà impostata `false`su:

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

Quando viene selezionato un elemento, `ImagesController` viene creata una seconda istanza di, solo questa volta utilizzando una classe layout diversa. Per questo controller, `UseLayoutToLayoutNavigtionTransitions` viene impostato su `true`, come illustrato di seguito:

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

È `UseLayoutToLayoutNavigationTransitions` necessario impostare la proprietà prima di aggiungere il controller allo stack di navigazione. Se questa proprietà è impostata, la normale transizione orizzontale viene sostituita da una transizione animata tra i layout dei due controller, come illustrato di seguito:

![](transitions-images/nav2.png "Transizione animata tra i layout dei due controller")

### <a name="transition-layout"></a>Layout di transizione

Oltre al supporto della transizione del layout all'interno di controller di spostamento, `UICollectionViewTransitionLayout` è ora disponibile un nuovo layout denominato. Questa classe layout consente il controllo interattivo durante il processo di transizione del layout, `TransitionProgress` consentendo l'impostazione del codice. `UICollectionViewTransitionLayout`è diverso da e non è una sostituzione per il metodo `SetCollectionViewLayout` di iOS 6 che ha causato la transizione di un layout animato. Questo metodo non forniva supporto incorporato per il controllo dello stato della transizione animata.

 `UICollectionViewTransitionLayout`consente, ad esempio, di configurare un riconoscitore di movimento per controllare la transizione tra i layout in risposta all'interazione dell'utente, gestendo il layout originale e il layout previsto per la transizione a.

I passaggi per implementare una transizione interattiva in un riconoscimento di movimento usando `UICollectionViewTransitionLayout` sono i seguenti:

1. Creare un riconoscitore di movimento.
1. Chiamare il `StartInteractiveTransition` metodo dell'oggetto `UICollectionView` , passandogli il layout di destinazione e un gestore di completamento.
1. Impostare la `TransitionProgress` proprietà `UICollectionViewTransitionLayout` dell'istanza restituita dal `StartInteractiveTransition` metodo.
1. Invalidare il layout.
1. Chiamare il `FinishInteractiveTransition` metodo dell'oggetto `UICollectionView` per completare la transizione o il `CancelInteractiveTransition` metodo per annullarlo.  `FinishInteractiveTransition`fa in modo che l'animazione completi la transizione al layout di destinazione `CancelInteractiveTransition` , mentre i risultati nell'animazione restituiscono il layout originale.
1. Gestire il completamento della transizione nel gestore di completamento del `StartInteractiveTransition` metodo.
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

Quando l'utente pizzica la visualizzazione della raccolta, l' `TransitionProgress` oggetto viene impostato in relazione alla scala del pizzico. In questa implementazione, se l'utente termina il pizzico prima che la transizione sia completata al 50%, la transizione viene annullata. In caso contrario, la transizione è terminata.




## <a name="related-links"></a>Collegamenti correlati

- [Introduzione ad iOS 7 (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [Panoramica dell'interfaccia utente di iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Elaborazione in background](~/ios/app-fundamentals/backgrounding/index.md)
