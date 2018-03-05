---
title: Introduzione a iOS 7
description: "Questo articolo vengono illustrate le principali nuove API introdotte in iOS 7, incluse le transizioni View Controller, miglioramenti alle animazioni UIView, UIKit Dynamics e il Kit di testo. Vengono inoltre illustrate alcune delle modifiche per l'interfaccia utente e le nuove funzionalità multitasking migliorata."
ms.topic: article
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a7bebc2b73ecb564028a92340c726bd5c1f1c54b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-ios-7"></a>Introduzione a iOS 7

_Questo articolo vengono illustrate le principali nuove API introdotte in iOS 7, incluse le transizioni View Controller, miglioramenti alle animazioni UIView, UIKit Dynamics e il Kit di testo. Vengono inoltre illustrate alcune delle modifiche per l'interfaccia utente e le nuove funzionalità multitasking migliorata._

iOS 7 è un aggiornamento principale per iOS. Introduce una completamente nuova progettazione dell'interfaccia utente che riporti lo stato attivo sul riquadro del contenuto, anziché dell'applicazione. Insieme di modifiche visive, iOS 7 aggiunge moltissime nuove API per creare esperienze e interazioni più complete. Questo documento i sondaggi nuove tecnologie introdotto con iOS 7 e funge da punto di partenza per un'ulteriore analisi.

## <a name="uiview-animation-enhancements"></a>Miglioramenti delle animazioni UIView

iOS 7 aggiunge il supporto di animazione in UIKit, consentendo alle applicazioni di eseguire operazioni che richiedevano in precedenza eliminazione direttamente nel framework di animazione Core. Ad esempio, `UIView` ora è possibile eseguire animazioni con effetto molla, nonché le animazioni di fotogrammi chiave, che in precedenza un `CAKeyframeAnimation` applicato a un `CALayer`.

### <a name="spring-animations"></a>Animazioni spring

 `UIView` ora supporta le modifiche alle proprietà di animazione con un effetto molla. Per aggiungere questo elemento, chiamare il `AnimateNotify` o `AnimateNotifyAsync` , passando i valori per il rapporto di smorzamento molla e la velocità iniziale della primavera, come descritto di seguito:

-  `springWithDampingRatio` : Un valore compreso tra 0 e 1, in cui oscillazione aumenta per il valore più piccolo.
-  `initialSpringVelocity` – La velocità iniziale della primavera come percentuale della distanza del totale dell'animazione in un secondo.


Il codice seguente produce un effetto molla quando cambia il centro della vista immagine:

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

Questo effetto molla provoca la visualizzazione di immagini sembri rimbalzare man mano che completerà l'animazione in un nuovo percorso di area, come illustrato di seguito:

 ![](images/spring-animation.png "Questo effetto molla provoca la visualizzazione di immagini sembri rimbalzare man mano che completerà l'animazione in un nuovo percorso di area")

### <a name="keyframe-animations"></a>Animazioni di fotogrammi chiave

Il `UIView` classe include ora il `AnimateWithKeyframes` metodo per la creazione di animazioni di fotogrammi chiave su un `UIView`. Questo metodo è simile ad altri `UIView` metodi di animazione, a meno che un altro `NSAction` viene passato come parametro per includere i fotogrammi chiave. All'interno di `NSAction`, i fotogrammi chiave vengono aggiunti chiamando `UIView.AddKeyframeWithRelativeStartTime`.

Ad esempio, il frammento di codice seguente crea un'animazione con fotogramma chiave per animare nonché centro di una vista da ruotare la vista:

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

I primi due parametri per il `AddKeyframeWithRelativeStartTime` metodo specificare l'ora di inizio e la durata del fotogramma chiave, rispettivamente, come percentuale della lunghezza totale di animazione. Nell'esempio sopra produce l'immagine vista animazione per il nuovo centro nel secondo prima, seguita da ruotare di 90 gradi tramite il secondo successivo. Poiché l'animazione specifica `UIViewKeyframeAnimationOptions.Autoreverse` come opzione, entrambi i fotogrammi chiave animare in ordine inverso anche. Infine, i valori finali sono impostare lo stato iniziale nel gestore di completamento.

Schermate riportate di seguito viene illustrato l'animazione combinato tramite i fotogrammi chiave:

 ![](images/keyframes.png "In questo screenshot illustra l'animazione combinato tramite i fotogrammi chiave")

## <a name="uikit-dynamics"></a>Dynamics UIKit

UIKit Dynamics è un nuovo set di API in UIKit che consentono alle applicazioni di creare interazioni animate in base a fisica. UIKit Dynamics incapsula un motore fisica 2D a questo scopo.

L'API è dichiarativa in natura. Dichiarare il comportano di interazioni fisica creando oggetti - denominati *comportamenti* : per i concetti di express fisica, quali gravità conflitti, springs, e così via. Quindi si collega il behavior(s) a un altro oggetto, chiamato un *animatore dinamica*, che incapsula una vista. L'animatore dinamica accetta resta di applicare i comportamenti di fisica dichiarato da *elementi dinamici* -gli elementi che implementano `IUIDynamicItem`, ad esempio un `UIView`.

Esistono diversi primitivi comportamenti diversi disponibili per attivare interazioni complesse, tra cui:

-  `UIAttachmentBehavior` – Collega due elementi dinamici in modo che vengano spostati insieme o associa un elemento dinamico a un punto di attacco.
-  `UICollisionBehavior` : Consente di elementi dinamici deve far parte di conflitti.
-  `UIDynamicItemBehavior` : Specifica un set di proprietà da applicare alle voci dinamiche, ad esempio elasticità, densità e le forze di attrito generale.
-  `UIGravityBehavior` -Gravità si applica a un elemento dinamico, causando elementi ad accelerare la direzione gravitazionale.
-  `UIPushBehavior` – Force si applica a un elemento dinamico.
-  `UISnapBehavior` : Consente di agganciare una posizione con un effetto molla un elemento dinamico.


Anche se sono presenti molti primitive, il processo generale per l'aggiunta di interazioni basate su fisica a una visualizzazione utilizzando UIKit Dynamics è coerenza tra i comportamenti:

1.  Creare un animatore dinamica.
1.  Creare behavior(s).
1.  Aggiungere i comportamenti di animatore dinamica.


### <a name="dynamics-example"></a>Esempio di Dynamics

Esaminiamo un esempio che aggiunge la gravità e un limite di conflitti per un `UIView`.

#### <a name="uigravitybehavior"></a>UIGravityBehavior

Aggiunta di gravità per visualizzazione di un'immagine segue i 3 passaggi descritti sopra.

È necessario lavorare `ViewDidLoad` metodo per questo esempio. Aggiungere innanzitutto un `UIImageView` istanza come indicato di seguito:

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

Questo modo viene creata la visualizzazione di un'immagine al centro nella parte superiore della schermata. Per rendere l'immagine "autunno" con la gravità, creare un'istanza di un `UIDynamicAnimator`:

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

Il `UIDynamicAnimator` accetta un'istanza di un riferimento `UIView` o `UICollectionViewLayout`, che contiene gli elementi che verranno animati per behavior(s) l'allegato.

Creare quindi un `UIGravityBehavior` istanza. È possibile passare uno o più oggetti che implementano il `IUIDynamicItem`, ad esempio un `UIView`:

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

Il comportamento di viene passata una matrice di `IUIDynamicItem`, che in questo caso contiene il singolo `UIImageView` istanza ci stiamo animazione.

Infine, aggiungere il comportamento di animatore dinamico:

```csharp
dynAnimator.AddBehavior (gravity);
```

Ciò comporta l'immagine di animazione verso il basso con gravità, come illustrato di seguito:

![](images/gravity2.png "Il percorso dell'immagine iniziale") 
![](images/gravity3.png "il percorso dell'immagine finale")

Poiché non c'è niente vincolare i limiti dello schermo, la visualizzazione di immagini passa semplicemente nella parte inferiore. Per limitare la visualizzazione in modo che l'immagine è in conflitto con i bordi della schermata, è possibile aggiungere un `UICollisionBehavior`. Tratteremo nella sezione successiva.

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

Iniziare creando un `UICollisionBehavior` e aggiungerlo alla animatore dinamico, esattamente come è stato fatto per il `UIGravityBehavior`.

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

Il `UICollisionBehavior` ha una proprietà denominata `TranslatesReferenceBoundsIntoBoundry`. L'impostazione di `true` fa sì che il riferimento dei limiti della visualizzazione da utilizzare come limite di conflitto.

A questo punto, quando l'immagine aggiunge un'animazione verso il basso con gravità, aperture leggermente la parte inferiore della schermata prima con la consueta risoluzione per rest non esiste.

<!--, as shown below:

 ![](images/bounce.png "Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there")-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

È possibile controllare ulteriormente il comportamento della visualizzazione di immagini in diminuzione con comportamenti aggiuntivi. Ad esempio, è possibile aggiungere un `UIDynamicItemBehavior` per aumentare l'elasticità, causando la visualizzazione di immagini a più rimbalzo quando è in conflitto con la parte inferiore della schermata.

Aggiunta di un `UIDynamicItemBehavior` segue gli stessi passaggi come con altri tipi di comportamento. Creare innanzitutto il comportamento:

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

Quindi, aggiungere il comportamento per l'animatore dinamico:

 `dynAnimator.AddBehavior (dynBehavior);`

Con questo comportamento sul posto, la visualizzazione di immagini aperture più quando è in conflitto con il limite.

## <a name="general-user-interface-changes"></a>Modifiche generali dell'interfaccia utente

Oltre a nuove APIs UIKit quali UIKit Dynamics, le transizioni di Controller e avanzate animazioni UIView descritte in precedenza, iOS 7 introduce una serie di modifiche visive per l'interfaccia utente e le modifiche correlate apportate API per diverse viste e i controlli. Per ulteriori informazioni, vedere il [iOS 7 panoramica dell'interfaccia utente](~/ios/platform/introduction-to-ios7/ios7-ui.md).

## <a name="text-kit"></a>Text Kit

Kit di testo è una nuova API che offre funzionalità di layout e rendering di testo potente. Si basa su framework testo principale di livello basso, ma è molto più semplice da utilizzare rispetto ai componenti di base del testo.

Per ulteriori informazioni, vedere il nostro [TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>Multitasking

iOS 7 cambia quando e l'esecuzione di operazioni in background. Completamento dell'attività in iOS 7 non mantiene attivi applicazioni quando eseguono attività in background e le applicazioni vengono riattivate per l'elaborazione in modo non contiguo in background. iOS 7 aggiunge tre nuove API per l'aggiornamento di applicazioni con nuovo contenuto in background:

-  Recupero in background: applicazioni consente di aggiornare il contenuto in background a intervalli regolari.
-  Notifiche remote - consente alle applicazioni di aggiornare il contenuto quando si riceve una notifica push. Le notifiche possono essere invisibile all'utente o visualizzare l'intestazione nella schermata di blocco.
-  Servizio di trasferimento in background: consente di caricamento e scaricamento di dati, ad esempio file di grandi dimensioni, senza un limite di tempo fisso.


Per ulteriori informazioni sulle nuove funzionalità di elaborazione multitasking, vedere le sezioni iOS di Xamarin [Backgrounding Guida](~/ios/app-fundamentals/backgrounding/index.md).

## <a name="summary"></a>Riepilogo

Questo articolo vengono illustrate diverse nuove funzionalità principali per iOS. Innanzitutto, viene illustrato come aggiungere transizioni personalizzate al controller di visualizzazione. Quindi, viene illustrato come utilizzare le transizioni nelle visualizzazioni di raccolta, sia dall'interno di un controller di navigazione, nonché in modo interattivo tra le visualizzazioni di raccolta. Successivamente, introduce diversi miglioramenti apportati alle animazioni UIView, che mostrano come usare UIKit di applicazioni per operazioni che richiedevano in precedenza programmando direttamente con i componenti di base animazione. Infine, nuova API di Dynamics UIKit, che rende disponibile un motore fisica a UIKit, è stato introdotto con il supporto di testo RTF ora disponibile nel framework di Kit di testo.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a iOS 7 (esempio)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Panoramica dell'interfaccia utente di iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Elaborazione in background](~/ios/app-fundamentals/backgrounding/index.md)
