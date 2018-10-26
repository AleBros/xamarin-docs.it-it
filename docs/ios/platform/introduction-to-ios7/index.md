---
title: Introduzione a iOS 7
description: Questo articolo illustra le principali nuove API introdotte in iOS 7, tra cui le transizioni di Controller di visualizzazione, miglioramenti alle animazioni UIView, UIKit Dynamics e i Kit di testo. Vengono anche presentate alcune delle modifiche per l'interfaccia utente e le nuove funzionalità di multitasking migliorata.
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: db2ce779962947e2121ff03280544a080e193e2e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118045"
---
# <a name="introduction-to-ios-7"></a>Introduzione a iOS 7

_Questo articolo illustra le principali nuove API introdotte in iOS 7, tra cui le transizioni di Controller di visualizzazione, miglioramenti alle animazioni UIView, UIKit Dynamics e i Kit di testo. Vengono anche presentate alcune delle modifiche per l'interfaccia utente e le nuove funzionalità di multitasking migliorata._

iOS 7 è un aggiornamento principale per iOS. Introduce una progettazione dell'interfaccia utente completamente nuova che assegna lo stato attivo su chrome contenuti piuttosto che dell'applicazione. Insieme l'oggetto visivo cambia, iOS 7 aggiunge una vasta gamma di nuove API per creare esperienze e le interazioni più complete. Questo documento i sondaggi le nuove tecnologie introdotte con iOS 7 e funge da punto di partenza per esplorare ulteriormente.

## <a name="uiview-animation-enhancements"></a>Miglioramenti delle animazioni UIView

iOS 7 aggiunge il supporto di animazione in UIKit, consentendo alle applicazioni di eseguire operazioni che richiedevano in precedenza eliminazione direttamente nel framework di Core Animation. Ad esempio, `UIView` ora è possibile eseguire le animazioni di spring, nonché le animazioni di fotogrammi chiave, che in precedenza una `CAKeyframeAnimation` applicati a un `CALayer`.

### <a name="spring-animations"></a>Animazioni di Spring

 `UIView` supporta ora le modifiche alle proprietà di animazione con effetto molla. Per aggiungere questo elemento, chiamare il `AnimateNotify` o `AnimateNotifyAsync` , passando i valori per il rapporto di smorzamento spring e la velocità di spring iniziale, come descritto di seguito:

-  `springWithDampingRatio` : Un valore compreso tra 0 e 1, dove l'oscillazione aumenta per il valore più piccolo.
-  `initialSpringVelocity` – La velocità di spring iniziale come percentuale della distanza totale dell'animazione al secondo.


Il codice seguente genera un effetto molla quando cambia il centro della visualizzazione immagine:

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

Questo effetto molla provoca la visualizzazione di immagini da visualizzare a oscillare man mano che completerà la relativa animazione a una nuova posizione centrale, come illustrato di seguito:

 ![](images/spring-animation.png "Questo effetto molla provoca la visualizzazione di immagini da visualizzare a oscillare man mano che completerà l'animazione in un nuovo percorso di center")

### <a name="keyframe-animations"></a>Animazioni di fotogrammi chiave

Il `UIView` classe include ora le `AnimateWithKeyframes` metodo per la creazione di animazioni di fotogrammi chiave in un `UIView`. Questo metodo è simile ad altri `UIView` metodi di animazione, a meno che un altro `NSAction` viene passato come parametro per includere i fotogrammi chiave. All'interno di `NSAction`, i fotogrammi chiave vengono aggiunti tramite la chiamata `UIView.AddKeyframeWithRelativeStartTime`.

Ad esempio, il frammento di codice seguente crea un'animazione con fotogrammi chiave per aggiungere un'animazione centro della visualizzazione anche per quanto riguarda la vista ruota:

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

I primi due parametri per il `AddKeyframeWithRelativeStartTime` metodo specificare l'ora di inizio e la durata del fotogramma chiave, rispettivamente, come percentuale della lunghezza totale di animazione. Nell'esempio sopra produce l'immagine Visualizza l'animazione per il nuovo centro tramite il secondo prima, seguita dalla rotazione di 90 gradi tramite il secondo successivo. Poiché l'animazione specifica `UIViewKeyframeAnimationOptions.Autoreverse` come opzione, animare in ordine inverso anche entrambi i fotogrammi chiave. Infine, i valori finali vengono impostati per lo stato iniziale nel gestore di completamento.

Le schermate riportate di seguito viene illustrata l'animazione combinato tramite i fotogrammi chiave:

 ![](images/keyframes.png "In questo screenshot viene illustrata l'animazione combinato tramite i fotogrammi chiave")

## <a name="uikit-dynamics"></a>UIKit Dynamics

UIKit Dynamics è un nuovo set di API in UIKit, che consentono alle applicazioni di creare interazioni animate base fisica. UIKit Dynamics incapsula un motore di fisica 2D a tale scopo.

L'API è dichiarativa in natura. Si dichiara il comportano di interazioni fisica tramite la creazione di oggetti - denominati *comportamenti* : per i concetti di express fisica, ad esempio la gravità, conflitti, springs e così via. Quindi si collega i propri comportamenti a un altro oggetto, chiamato un' *animatore dinamica*, che incapsula una vista. L'animatore dinamica accetta sommo di applicare i comportamenti di fisica dichiarato da *elementi dinamici* -gli elementi che implementano `IUIDynamicItem`, ad esempio un `UIView`.

Esistono diversi primitivi comportamenti disponibili per l'attivazione di interazioni complesse, tra cui:

-  `UIAttachmentBehavior` – Collega due elementi dinamici in modo che vengano spostati insieme o associa un elemento dinamico a un punto di attacco.
-  `UICollisionBehavior` – Consente dinamica degli elementi fanno parte di conflitti.
-  `UIDynamicItemBehavior` : Specifica un set generale di proprietà da applicare agli elementi dinamici, ad esempio l'elasticità, densità e problemi.
-  `UIGravityBehavior` -Applica la gravità di un elemento dinamico, consentendo agli elementi ad accelerare la direzione gravitazionale.
-  `UIPushBehavior` – Force si applica a un elemento dinamico.
-  `UISnapBehavior` – Consente a un elemento dinamico bloccare in una posizione con un effetto molla.


Anche se ci sono molte le primitive, il processo generale per l'aggiunta di interazioni basate su fisica a una visualizzazione usando UIKit Dynamics è coerente tra i comportamenti:

1.  Creare un animatore dinamica.
1.  Creare i propri comportamenti.
1.  Aggiungere i comportamenti per l'animatore dinamica.


### <a name="dynamics-example"></a>Esempio di Dynamics

Verrà ora esaminato un esempio che aggiunge la gravità e un limite di conflitto per un `UIView`.

#### <a name="uigravitybehavior"></a>UIGravityBehavior

Aggiunta di gravità a una visualizzazione immagine segue i 3 passaggi descritti in precedenza.

È necessario lavorare `ViewDidLoad` metodo per questo esempio. In primo luogo, aggiungere un `UIImageView` istanza come indicato di seguito:

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

Verrà creata una visualizzazione immagine centrata sul bordo superiore dello schermo. Per configurare l'immagine "autunno" con la gravità, creare un'istanza di un `UIDynamicAnimator`:

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

Il `UIDynamicAnimator` accetta un'istanza di un riferimento `UIView` o un `UICollectionViewLayout`, che contiene gli elementi che verranno animati per il propri comportamenti collegati.

Creare quindi un `UIGravityBehavior` istanza. È possibile passare uno o più oggetti che implementa il `IUIDynamicItem`, ad esempio un `UIView`:

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

Il comportamento viene passato una matrice di `IUIDynamicItem`, che in questo caso contiene il singolo `UIImageView` istanza si sta l'animazione.

Infine, aggiungere il comportamento di animatore dinamico:

```csharp
dynAnimator.AddBehavior (gravity);
```

Ciò comporta l'immagine animata verso il basso con la gravità, come illustrato di seguito:

![](images/gravity2.png "Il percorso dell'immagine partenza") 
![](images/gravity3.png "il percorso dell'immagine finale")

Poiché nulla vincolare i limiti dello schermo, la visualizzazione immagine semplicemente si verifichi una riduzione inferiore. Per limitare la visualizzazione in modo che l'immagine è in conflitto con i bordi dello schermo, è possibile aggiungere un `UICollisionBehavior`. Questo aspetto verrà illustrato nella sezione successiva.

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

Iniziamo creando un `UICollisionBehavior` e aggiungerlo alla animatore dinamico, come abbiamo fatto il `UIGravityBehavior`.

Modificare il codice per includere il `UICollisionBehavior`:

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

Il `UICollisionBehavior` ha una proprietà denominata `TranslatesReferenceBoundsIntoBoundry`. Se questa impostazione `true` fa sì che il riferimento ai limiti della visualizzazione da utilizzare come limite di collisione.

A questo punto, quando l'immagine animata verso il basso con la gravità, rimbalza leggermente la parte inferiore della schermata prima di stabilire per rest non esiste.

<!--, as shown below:

 ![](images/bounce.png "Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there")-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

Possiamo ulteriormente controllare il comportamento della visualizzazione immagine in diminuzione con comportamenti aggiuntivi. Ad esempio, è possibile aggiungere un `UIDynamicItemBehavior` per aumentare l'elasticità, causando la visualizzazione di immagini a oscillare informazioni quando si scontra con la parte inferiore della schermata.

Aggiunta di un `UIDynamicItemBehavior` segue gli stessi passaggi come con altri comportamenti. Creare prima il comportamento:

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

Aggiungere quindi il comportamento per l'animatore dinamico:

 `dynAnimator.AddBehavior (dynBehavior);`

Con questo comportamento posto, la visualizzazione immagine bounces informazioni quando si scontra con il limite.

## <a name="general-user-interface-changes"></a>Modifiche generali dell'interfaccia utente

Oltre a nuove APIs UIKit, ad esempio UIKit Dynamics, transizioni dei Controller e avanzata UIView animazioni, descritte in precedenza, iOS 7 introduce un'ampia gamma di modifiche visive per l'interfaccia utente e le modifiche API correlate per varie viste e i controlli. Per altre informazioni vedere la [iOS 7 panoramica dell'interfaccia utente](~/ios/platform/introduction-to-ios7/ios7-ui.md).

## <a name="text-kit"></a>Kit di testo

Testo Kit è una nuova API che offre funzionalità di layout e rendering del testo completa. Si basa su un livello basso Text Core framework, ma è molto più facile da usare rispetto al testo di base.

Per altre informazioni, vedere il [TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>Multitasking

iOS 7 cambia quando e modalità di esecuzione attività in background. Completamento dell'attività in iOS 7 non sono più mantiene applicazioni attivi quando si eseguono attività in background e le applicazioni vengono riattivate per l'elaborazione in modo non contigui in background. iOS 7 aggiunge anche tre nuove API per l'aggiornamento di applicazioni con nuovo contenuto in background:

-  Recupero in background: consente alle applicazioni di aggiornare il contenuto in background a intervalli regolari.
-  Notifiche remote - consente alle applicazioni di aggiornare il contenuto quando si riceve una notifica push. Le notifiche possono essere invisibile all'utente o visualizzare un banner nella schermata di blocco.
-  Servizio di trasferimento in background: consente di caricare e scaricare dati, ad esempio file di grandi dimensioni, senza un limite di tempo fisso.


Per altre informazioni sulle nuove funzionalità multitasking, vedere le sezioni di iOS di xamarin [Guida all'elaborazione in background in](~/ios/app-fundamentals/backgrounding/index.md).

## <a name="summary"></a>Riepilogo

Questo articolo illustra diverse nuove funzionalità principali per iOS. In primo luogo, viene illustrato come aggiungere le transizioni personalizzate ai controller di visualizzazione. Quindi, viene illustrato come utilizzare le transizioni nelle visualizzazioni di raccolta, entrambi gli elementi da all'interno di un controller di spostamento, nonché in modo interattivo tra le visualizzazioni di raccolta. Successivamente, introduce numerosi miglioramenti apportati alle animazioni UIView, che illustra come le applicazioni usano UIKit per situazioni che richiedevano in precedenza programmando direttamente in Core Animation. Infine, nuove API di Dynamics UIKit, che offre un motore di fisica UIKit, è stato introdotto con il supporto di testi con formattazione ora disponibile in framework Kit di testo.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a iOS 7 (esempio)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Panoramica dell'interfaccia utente di iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Elaborazione in background](~/ios/app-fundamentals/backgrounding/index.md)
