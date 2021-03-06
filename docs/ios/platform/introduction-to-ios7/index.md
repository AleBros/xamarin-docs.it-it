---
title: Introduzione a iOS 7
description: Questo articolo illustra le nuove API principali introdotte in iOS 7, incluse le transizioni del controller di visualizzazione, i miglioramenti apportati alle animazioni UIView, UIKit Dynamics e Text Kit. Vengono inoltre illustrate alcune delle modifiche apportate all'interfaccia utente e le nuove funzionalità di multitasking.
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: b405643096699e1d965f485bdc590afa178881d6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031826"
---
# <a name="introduction-to-ios-7"></a>Introduzione a iOS 7

_Questo articolo illustra le nuove API principali introdotte in iOS 7, incluse le transizioni del controller di visualizzazione, i miglioramenti apportati alle animazioni UIView, UIKit Dynamics e Text Kit. Vengono inoltre illustrate alcune delle modifiche apportate all'interfaccia utente e le nuove funzionalità di multitasking._

iOS 7 è un aggiornamento principale di iOS. Introduce una progettazione dell'interfaccia utente completamente nuova che mette lo stato attivo sul contenuto anziché sull'applicazione Chrome. Insieme alle modifiche visive, iOS 7 aggiunge una pletora di nuove API per creare interazioni ed esperienze più avanzate. Questo documento illustra le nuove tecnologie introdotte con iOS 7 e funge da punto di partenza per l'esplorazione.

## <a name="uiview-animation-enhancements"></a>Miglioramenti dell'animazione UIView

iOS 7 aumenta il supporto dell'animazione in UIKit, consentendo alle applicazioni di eseguire operazioni che in precedenza richiedevano l'eliminazione diretta nel Framework di animazione principale. Ad esempio, `UIView` possibile ora eseguire animazioni spring, nonché animazioni di fotogramma chiave, che in precedenza venivano applicate a una `CAKeyframeAnimation` `CALayer`.

### <a name="spring-animations"></a>Animazioni spring

 `UIView` supporta ora l'animazione delle modifiche delle proprietà con effetto elastico. Per aggiungerlo, chiamare il metodo `AnimateNotify` o `AnimateNotifyAsync`, passando i valori per il rapporto di smorzamento della molla e la velocità iniziale della molla, come descritto di seguito:

- `springWithDampingRatio`: valore compreso tra 0 e 1, in cui l'oscillazione aumenta per un valore inferiore.
- `initialSpringVelocity`: la velocità iniziale della molla come percentuale della distanza di animazione totale al secondo.

Il codice seguente produce un effetto primaverile quando il centro della visualizzazione immagine cambia:

```csharp
void AnimateWithSpring ()
{
    float springDampingRatio = 0.25f;
    float initialSpringVelocity = 1.0f;

    UIView.AnimateNotify (3.0, 0.0, springDampingRatio, initialSpringVelocity, 0, () => {

        imageView.Center = new CGPoint (imageView.Center.X, 400);

    }, null);
}
```

Questo effetto elastico fa sì che la visualizzazione dell'immagine venga visualizzata quando viene completata l'animazione in una nuova posizione centrale, come illustrato di seguito:

 ![](images/spring-animation.png "This spring effect causes the image view to appear to bounce as it completes its animation to a new center location")

### <a name="keyframe-animations"></a>Animazioni di fotogramma chiave

La classe `UIView` ora include il metodo `AnimateWithKeyframes` per la creazione di animazioni di fotogramma chiave in un `UIView`. Questo metodo è simile ad altri metodi di animazione `UIView`, ad eccezione del fatto che un `NSAction` aggiuntivo viene passato come parametro per includere i fotogrammi chiave. All'interno del `NSAction`, i fotogrammi chiave vengono aggiunti chiamando `UIView.AddKeyframeWithRelativeStartTime`.

Ad esempio, il frammento di codice seguente crea un'animazione del fotogramma chiave per animare il centro di una visualizzazione e per ruotare la visualizzazione:

```csharp
void AnimateViewWithKeyframes ()
{
    var initialTransform = imageView.Transform;
    var initialCeneter = imageView.Center;

    // can now use keyframes directly on UIView without needing to drop directly into Core Animation

    UIView.AnimateKeyframes (2.0, 0, UIViewKeyframeAnimationOptions.Autoreverse, () => {
        UIView.AddKeyframeWithRelativeStartTime (0.0, 0.5, () => {
            imageView.Center = new CGPoint (200, 200);
        });

        UIView.AddKeyframeWithRelativeStartTime (0.5, 0.5, () => {
            imageView.Transform = CGAffineTransform.MakeRotation ((float)Math.PI / 2);
        });
    }, (finished) => {
        imageView.Center = initialCeneter;
        imageView.Transform = initialTransform;

        AnimateWithSpring ();
    });
}
```

I primi due parametri del metodo `AddKeyframeWithRelativeStartTime` specificano rispettivamente l'ora di inizio e la durata del fotogramma chiave, come percentuale della lunghezza complessiva dell'animazione. Nell'esempio precedente la visualizzazione immagine viene animata al nuovo centro sul primo secondo, seguita dalla rotazione di 90 gradi nel secondo successivo. Poiché l'animazione specifica `UIViewKeyframeAnimationOptions.Autoreverse` come opzione, entrambi i fotogrammi chiave vengono animati anche in ordine inverso. Infine, i valori finali vengono impostati sullo stato iniziale nel gestore di completamento.

Gli screenshot seguenti illustrano l'animazione combinata tramite i fotogrammi chiave:

 ![](images/keyframes.png "This screenshots illustrates the combined animation through the keyframes")

## <a name="uikit-dynamics"></a>UIKit Dynamics

UIKit Dynamics è un nuovo set di API in UIKit che consentono alle applicazioni di creare interazioni animate in base alla fisica. UIKit Dynamics incapsula un motore di fisica 2D per renderlo possibile.

L'API è di natura dichiarativa. Si dichiara il comportamento delle interazioni di fisica mediante la creazione di oggetti, chiamati *comportamenti* , per esprimere concetti di fisica, ad esempio gravità, collisioni, molle e così via. Quindi si alleghino i comportamenti a un altro oggetto, denominato *animatore dinamico*, che incapsula una visualizzazione. L'animatore dinamico si occupa dell'applicazione dei comportamenti di fisica dichiarati agli *elementi dinamici* , ovvero gli elementi che implementano `IUIDynamicItem`, ad esempio `UIView`.

Sono disponibili diversi comportamenti primitivi per attivare interazioni complesse, tra cui:

- `UIAttachmentBehavior`: collega due elementi dinamici in modo che vengano spostati insieme o allegano un elemento dinamico a un punto di collegamento.
- `UICollisionBehavior`: consente agli elementi dinamici di partecipare ai conflitti.
- `UIDynamicItemBehavior`: specifica un set generale di proprietà da applicare agli elementi dinamici, ad esempio elasticità, densità e attrito.
- `UIGravityBehavior`: applica la gravità a un elemento dinamico, causando l'accelerazione degli elementi nella direzione gravitazionale.
- `UIPushBehavior`: applica Force a un elemento dinamico.
- `UISnapBehavior`: consente a un elemento dinamico di bloccarsi in una posizione con effetto elastico.

Sebbene esistano molte primitive, il processo generale per l'aggiunta di interazioni basate su fisica a una vista con UIKit Dynamics è coerente nei comportamenti:

1. Creare un animatore dinamico.
1. Crea comportamenti.
1. Consente di aggiungere comportamenti a Dynamic Animator.

### <a name="dynamics-example"></a>Esempio di Dynamics

Verrà ora esaminato un esempio che aggiunge la gravità e un limite di collisione a un `UIView`.

#### <a name="uigravitybehavior"></a>UIGravityBehavior

L'aggiunta di gravità a una visualizzazione immagine segue i tre passaggi descritti in precedenza.

Per questo esempio verrà usato il metodo `ViewDidLoad`. Prima di tutto, aggiungere un'istanza di `UIImageView` come indicato di seguito:

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

In questo modo viene creata una visualizzazione immagine centrata sul bordo superiore dello schermo. Per fare in modo che l'immagine "cada" con gravità, creare un'istanza di un `UIDynamicAnimator`:

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

Il `UIDynamicAnimator` accetta un'istanza di un `UIView` di riferimento o un `UICollectionViewLayout` contenente gli elementi che verranno animati in base ai comportamenti collegati.

Successivamente, creare un'istanza di `UIGravityBehavior`. È possibile passare uno o più oggetti che implementano la `IUIDynamicItem`, ad esempio un `UIView`:

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

Al comportamento viene passata una matrice di `IUIDynamicItem`, che in questo caso contiene la singola istanza di `UIImageView` a cui si sta aggiungendo un'animazione.

Infine, aggiungere il comportamento all'animazione dinamica:

```csharp
dynAnimator.AddBehavior (gravity);
```

Questo comporta l'animazione dell'immagine verso il basso con gravità, come illustrato di seguito:

![](images/gravity2.png "The starting image location")
![](images/gravity3.png "The ending image location")

Poiché non c'è niente vincolante ai limiti dello schermo, la visualizzazione dell'immagine si trova semplicemente in basso. Per vincolare la visualizzazione in modo che l'immagine entri in conflitto con i bordi dello schermo, è possibile aggiungere un `UICollisionBehavior`. Questa operazione verrà illustrata nella sezione successiva.

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

Si inizierà con la creazione di un `UICollisionBehavior` e l'aggiunta all'animazione dinamica, esattamente come per il `UIGravityBehavior`.

Modificare il codice in modo da includere la `UICollisionBehavior`:

```csharp
using (image = UIImage.FromFile ("monkeys.jpg")) {

    imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
        Image =  image
    };

    View.AddSubview (imageView);

    // 1. create the dynamic animator
    dynAnimator = new UIDynamicAnimator (this.View);

    // 2. create behavior(s)
    var gravity = new UIGravityBehavior (imageView);
    var collision = new UICollisionBehavior (imageView) {
        TranslatesReferenceBoundsIntoBoundary = true
    };

    // 3. add behaviors(s) to the dynamic animator
    dynAnimator.AddBehaviors (gravity, collision);
}
```

Il `UICollisionBehavior` dispone di una proprietà denominata `TranslatesReferenceBoundsIntoBoundry`. Impostando questa opzione su `true`, i limiti della visualizzazione di riferimento verranno utilizzati come limite di collisione.

A questo punto, quando l'immagine viene animata verso il basso con gravità, rimbalza leggermente dalla parte inferiore dello schermo prima di stabilirla.

<!--, as shown below:

 ![](images/bounce.png "Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there")-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

È possibile controllare ulteriormente il comportamento della visualizzazione immagine in calo con comportamenti aggiuntivi. Ad esempio, è possibile aggiungere una `UIDynamicItemBehavior` per aumentare l'elasticità, facendo in modo che la visualizzazione immagini rimbalzi più quando si scontra con la parte inferiore dello schermo.

L'aggiunta di un `UIDynamicItemBehavior` segue gli stessi passaggi degli altri comportamenti. Per prima cosa, creare il comportamento:

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

Aggiungere quindi il comportamento all'animatore dinamico:

 `dynAnimator.AddBehavior (dynBehavior);`

Con questo comportamento, la visualizzazione immagini rimbalza più quando si scontra con il limite.

## <a name="general-user-interface-changes"></a>Modifiche generali dell'interfaccia utente

Oltre alle nuove API UIKit, ad esempio UIKit Dynamics, le transizioni del controller e le animazioni UIView avanzate descritte in precedenza, iOS 7 introduce un'ampia gamma di modifiche visive all'interfaccia utente e le modifiche alle API correlate per varie visualizzazioni e controlli. Per altre informazioni, vedere [Panoramica dell'interfaccia utente di iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md).

## <a name="text-kit"></a>Kit di testo

Il kit di testo è una nuova API che offre funzionalità avanzate per il layout e il rendering del testo. Si basa sul Framework di testo principale di basso livello, ma è molto più semplice da usare rispetto al testo principale.

Per ulteriori informazioni, vedere la [TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>Multitasking

iOS 7 cambia quando e come viene eseguito il lavoro in background. Il completamento delle attività in iOS 7 non mantiene più le applicazioni sveglie quando le attività vengono eseguite in background e le applicazioni vengono riattivate per l'elaborazione in background in modo non contiguo. iOS 7 aggiunge anche tre nuove API per l'aggiornamento delle applicazioni con nuovo contenuto in background:

- Recupero in background: consente alle applicazioni di aggiornare il contenuto in background a intervalli regolari.
- Notifiche remote: consente alle applicazioni di aggiornare il contenuto quando riceve una notifica push. Le notifiche possono essere in modalità invisibile all'utente o visualizzare un banner nella schermata di blocco.
- Servizio trasferimento in background: consente il caricamento e il download di dati, ad esempio file di grandi dimensioni, senza un limite di tempo fisso.

Per ulteriori informazioni sulle nuove funzionalità multitasking, vedere le sezioni iOS della [Guida in background](~/ios/app-fundamentals/backgrounding/index.md)Xamarin.

## <a name="summary"></a>Riepilogo

In questo articolo vengono illustrate alcune novità principali di iOS. Viene innanzitutto illustrato come aggiungere transizioni personalizzate ai controller di visualizzazione. Viene quindi illustrato come utilizzare le transizioni nelle visualizzazioni di raccolta, sia all'interno di un controller di spostamento che in modo interattivo tra le visualizzazioni di raccolta. Successivamente, introduce diversi miglioramenti apportati alle animazioni UIView, mostrando in che modo le applicazioni usano UIKit per gli elementi che in precedenza richiedevano la programmazione direttamente nell'animazione di base. Infine, viene introdotta la nuova API UIKit Dynamics, che porta un motore di fisica a UIKit, insieme al supporto di testo RTF ora disponibile nel Framework del kit di testo.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione ad iOS 7 (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [Panoramica dell'interfaccia utente di iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Elaborazione in background](~/ios/app-fundamentals/backgrounding/index.md)
