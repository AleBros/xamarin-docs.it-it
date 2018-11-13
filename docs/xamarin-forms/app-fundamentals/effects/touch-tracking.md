---
title: Richiamo di eventi dagli effetti
description: Un effetto possa definire e richiamare un evento, che segnala le modifiche nella visualizzazione nativa sottostante. Questo articolo illustra come implementare di basso livello con un dito multitocco di rilevamento e come generare gli eventi che segnalano attività touch.
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 0a5e2c1a7a7807da91fd98e617467ea251a25bc0
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527404"
---
# <a name="invoking-events-from-effects"></a>Richiamo di eventi dagli effetti

_Un effetto possa definire e richiamare un evento, che segnala le modifiche nella visualizzazione nativa sottostante. Questo articolo illustra come implementare di basso livello con un dito multitocco di rilevamento e come generare gli eventi che segnalano attività touch._

L'effetto descritta in questo articolo fornisce l'accesso agli eventi di tocco di basso livello. Questi eventi di basso livello non sono disponibili tramite l'oggetto esistente `GestureRecognizer` classi, ma sono fondamentali per alcuni tipi di applicazioni. Ad esempio, un finger-paint esigenze dell'applicazione per tenere traccia delle singole dita durante lo spostamento sullo schermo. Una tastiera musica deve rilevare dispositivi TAP e rilascia in singole chiavi, nonché un dito gliding da una chiave a altra in un glissando.

Un effetto è ideale per il multitocco dito rilevamento perché può essere collegato a qualsiasi elemento di xamarin. Forms.

## <a name="platform-touch-events"></a>Eventi di tocco di piattaforma

IOS, Android e Universal Windows Platform, includono un'API di basso livello che consente alle applicazioni di rilevare attività di tocco. Queste piattaforme di che tutti distinguere tra tre tipi principali eventi di tocco:

- *Premuto*, quando un dito tocca lo schermo
- *Spostato*, quando si sposta un dito toccare lo schermo
- *Rilasciato*, quando viene rilasciato il dito dallo schermo

In un ambiente multi-touch, dita più possono di toccare la schermata nello stesso momento. Le varie piattaforme includono un numero di identificazione (ID) che le applicazioni possono usare per distinguere tra più dita.

In iOS, il `UIView` classe definisce tre metodi sottoponibili a override `TouchesBegan`, `TouchesMoved`, e `TouchesEnded` corrispondenti a questi tre eventi di base. L'articolo [multi-Touch dito rilevamento](~/ios/app-fundamentals/touch/touch-tracking.md) viene descritto come utilizzare questi metodi. Tuttavia, un programma iOS non è necessario eseguire l'override di una classe che deriva da `UIView` utilizzare questi metodi. IOS `UIGestureRecognizer` definisce anche questi stessi tre metodi ed è possibile collegare un'istanza di una classe che deriva da `UIGestureRecognizer` a qualsiasi `UIView` oggetto.

In Android, il `View` classe definisce un metodo sostituibile denominato `OnTouchEvent` per elaborare tutte le attività di tocco. I membri di enumerazione è definito il tipo di attività touch `Down`, `PointerDown`, `Move`, `Up`, e `PointerUp` come descritto nell'articolo [multi-Touch dito rilevamento](~/android/app-fundamentals/touch/touch-tracking.md). Android `View` definisce inoltre un evento denominato `Touch` che consente a un gestore eventi da collegare a qualsiasi `View` oggetto.

In di Windows della piattaforma UWP (Universal), il `UIElement` classe definisce eventi chiamati `PointerPressed`, `PointerMoved`, e `PointerReleased`. Questi elementi sono descritti nell'articolo [articolo gestiscono Input puntatore su MSDN](/windows/uwp/input-and-devices/handle-pointer-input/) e la documentazione dell'API per il [ `UIElement` ](/uwp/api/windows.ui.xaml.uielement/) classe.

Il `Pointer` API nella piattaforma Windows universale consente di unificare mouse, tocco e input penna. Per questo motivo, il `PointerMoved` evento viene richiamato quando il mouse viene spostato di un elemento anche quando non si preme un pulsante del mouse. Il `PointerRoutedEventArgs` oggetto che accompagna questi eventi ha una proprietà denominata `Pointer` che include una proprietà denominata `IsInContact` che indica se viene premuto un pulsante del mouse o un dito è a contatto con la schermata.

Inoltre, la piattaforma UWP definisce due ulteriori eventi denominati `PointerEntered` e `PointerExited`. Queste informazioni indicano quando il mouse o un dito viene spostata da un elemento a un altro. Ad esempio, prendere in considerazione due elementi adiacenti, denominati A e b. Entrambi gli elementi sono installati i gestori per gli eventi puntatore. Quando si preme un dito su A, il `PointerPressed` evento viene richiamato. Come si sposta il dito, la classe richiama `PointerMoved` gli eventi. Se il dito viene spostato da A B, la classe richiama una `PointerExited` eventi e B richiama un `PointerEntered` evento. Se il dito viene quindi rilasciato, B richiama un `PointerReleased` evento.

Le piattaforme iOS e Android sono diverse dalla piattaforma UWP: la vista che ottiene prima di tutto la chiamata a `TouchesBegan` o `OnTouchEvent` quando un dito tocca la visualizzazione continua ottenere tutte le attività di tocco, anche se si sposta il dito a visualizzazioni diverse. La piattaforma UWP possa comportarsi in modo analogo se l'applicazione acquisisce il puntatore del mouse: nel `PointerEntered` gestore dell'evento, le chiamate elemento `CapturePointer` e ottiene tutte le attività di tocco da tale dito.

L'approccio UWP si rivela molto utile per alcuni tipi di applicazioni, ad esempio, una tastiera musica. Ogni chiave può gestire gli eventi di tocco per la chiave e rilevare quando un dito ha fatto scorrere da una chiave in un altro usando il `PointerEntered` e `PointerExited` eventi.

Per questo motivo, l'effetto di tocco di rilevamento descritto in questo articolo implementa l'approccio della piattaforma UWP.

## <a name="the-touch-tracking-effect-api"></a>L'API di effetto di tocco di rilevamento

Il [ **Touch demo effetto rilevamento** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/) esempio contiene le classi (e un'enumerazione) che implementano il rilevamento delle touch a basso livello. Questi tipi appartengano allo spazio dei nomi `TouchTracking` e iniziare con la parola `Touch`. Il **TouchTrackingEffectDemos** progetto di libreria .NET Standard include la `TouchActionType` enumerazione per il tipo di eventi di tocco:

```csharp
public enum TouchActionType
{
    Entered,
    Pressed,
    Moved,
    Released,
    Exited,
    Cancelled
}
```

Tutte le piattaforme includono anche un evento che indica che l'evento di tocco è stata annullata.

Il `TouchEffect` deriva dalla classe nella libreria .NET Standard `RoutingEffect` e definisce un evento denominato `TouchAction` e un metodo denominato `OnTouchAction` che richiama la `TouchAction` evento:

```csharp
public class TouchEffect : RoutingEffect
{
    public event TouchActionEventHandler TouchAction;

    public TouchEffect() : base("XamarinDocs.TouchEffect")
    {
    }

    public bool Capture { set; get; }

    public void OnTouchAction(Element element, TouchActionEventArgs args)
    {
        TouchAction?.Invoke(element, args);
    }
}
```

Si noti inoltre il `Capture` proprietà. Per acquisire gli eventi di tocco, un'applicazione deve impostare questa proprietà su `true` precedenti a un `Pressed` evento. In caso contrario, gli eventi di tocco si comportano come quelle nella piattaforma Windows universale.

Il `TouchActionEventArgs` classe nella libreria .NET Standard contiene tutte le informazioni che accompagna ogni evento:

```csharp
public class TouchActionEventArgs : EventArgs
{
    public TouchActionEventArgs(long id, TouchActionType type, Point location, bool isInContact)
    {
        Id = id;
        Type = type;
        Location = location;
        IsInContact = isInContact;
    }

    public long Id { private set; get; }

    public TouchActionType Type { private set; get; }

    public Point Location { private set; get; }

    public bool IsInContact { private set; get; }
}
```

Un'applicazione può utilizzare il `Id` proprietà per tenere traccia delle singole dita. Si noti che il `IsInContact` proprietà. Questa proprietà è sempre `true` per `Pressed` degli eventi e `false` per `Released` gli eventi. È inoltre sempre `true` per `Moved` gli eventi in iOS e Android. Il `IsInContact` proprietà potrebbe essere `false` per `Moved` eventi sulla piattaforma Windows Universal quando l'esecuzione del programma sul desktop e il puntatore del mouse viene spostato senza un pulsante premuto.

È possibile usare la `TouchEffect` classe nelle proprie applicazioni, includendo il file nel progetto di libreria .NET Standard della soluzione e mediante l'aggiunta di un'istanza per il `Effects` raccolta di qualsiasi elemento di xamarin. Forms. Associare un gestore di `TouchAction` evento per ottenere gli eventi di tocco.

Per utilizzare `TouchEffect` nella propria applicazione, è necessario anche le implementazioni della piattaforma incluse nella **TouchTrackingEffectDemos** soluzione.

## <a name="the-touch-tracking-effect-implementations"></a>Le implementazioni effetti Touch di rilevamento

IOS, Android e UWP le implementazioni del `TouchEffect` sono descritti di seguito inizia con l'implementazione più semplice (UWP) e termina con l'implementazione di iOS perché è strutturalmente più complessa rispetto agli altri.

### <a name="the-uwp-implementation"></a>L'implementazione di UWP

L'implementazione di UWP di `TouchEffect` è la più semplice. Come di consueto, la classe deriva da `PlatformEffect` e include due attributi di assembly:

```csharp
[assembly: ResolutionGroupName("XamarinDocs")]
[assembly: ExportEffect(typeof(TouchTracking.UWP.TouchEffect), "TouchEffect")]

namespace TouchTracking.UWP
{
    public class TouchEffect : PlatformEffect
    {
        ...
    }
}
```

Il `OnAttached` override alcune informazioni vengono salvate come campi e consente di collegare i gestori per tutti gli eventi puntatore:

```csharp
public class TouchEffect : PlatformEffect
{
    FrameworkElement frameworkElement;
    TouchTracking.TouchEffect effect;
    Action<Element, TouchActionEventArgs> onTouchAction;

    protected override void OnAttached()
    {
        // Get the Windows FrameworkElement corresponding to the Element that the effect is attached to
        frameworkElement = Control == null ? Container : Control;

        // Get access to the TouchEffect class in the .NET Standard library
        effect = (TouchTracking.TouchEffect)Element.Effects.
                    FirstOrDefault(e => e is TouchTracking.TouchEffect);

        if (effect != null && frameworkElement != null)
        {
            // Save the method to call on touch events
            onTouchAction = effect.OnTouchAction;

            // Set event handlers on FrameworkElement
            frameworkElement.PointerEntered += OnPointerEntered;
            frameworkElement.PointerPressed += OnPointerPressed;
            frameworkElement.PointerMoved += OnPointerMoved;
            frameworkElement.PointerReleased += OnPointerReleased;
            frameworkElement.PointerExited += OnPointerExited;
            frameworkElement.PointerCanceled += OnPointerCancelled;
        }
    }
    ...
}    
```

Il `OnPointerPressed` gestore richiama l'evento effetto chiamando il `onTouchAction` campo il `CommonHandler` metodo:

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerPressed(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Pressed, args);

        // Check setting of Capture property
        if (effect.Capture)
        {
            (sender as FrameworkElement).CapturePointer(args.Pointer);
        }
    }
    ...
    void CommonHandler(object sender, TouchActionType touchActionType, PointerRoutedEventArgs args)
    {
        PointerPoint pointerPoint = args.GetCurrentPoint(sender as UIElement);
        Windows.Foundation.Point windowsPoint = pointerPoint.Position;  

        onTouchAction(Element, new TouchActionEventArgs(args.Pointer.PointerId,
                                                        touchActionType,
                                                        new Point(windowsPoint.X, windowsPoint.Y),
                                                        args.Pointer.IsInContact));
    }
}
```

`OnPointerPressed` Controlla anche il valore della `Capture` proprietà nella classe effetto nella libreria .NET Standard e chiama `CapturePointer` se si tratta di `true`.

 Altri gestori di eventi UWP sono ancora più semplice:

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerEntered(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Entered, args);
    }
    ...
}
```

### <a name="the-android-implementation"></a>L'implementazione di Android

Le implementazioni di Android e iOS sono necessariamente più complesse poiché devono implementare il `Exited` e `Entered` eventi quando si sposta un dito da un elemento a un altro. Entrambe le implementazioni sono strutturate in modo analogo.

Android `TouchEffect` classe consente di installare un gestore per il `Touch` evento:

```csharp
view = Control == null ? Container : Control;
...
view.Touch += OnTouch;
```

La classe definisce anche due dizionari statici:

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    static Dictionary<Android.Views.View, TouchEffect> viewDictionary =
        new Dictionary<Android.Views.View, TouchEffect>();

    static Dictionary<int, TouchEffect> idToEffectDictionary =
        new Dictionary<int, TouchEffect>();
    ...
```

Il `viewDictionary` Ottiene una nuova voce ogni volta che il `OnAttached` override viene chiamato:

```csharp
viewDictionary.Add(view, this);
```

La voce viene rimossa dal dizionario in `OnDetached`. Tutte le istanze di `TouchEffect` è associato a una determinata visualizzazione cui è collegata l'effetto. Il dizionario statico consente a qualsiasi `TouchEffect` istanza per enumerare tutte le altre visualizzazioni e i relativi `TouchEffect` istanze. Ciò è necessario per consentire il trasferimento di eventi da una visualizzazione a un altro.

Android viene assegnato un codice ID a eventi di tocco che consente a un'applicazione tenere traccia delle singole dita. Il `idToEffectDictionary` associa questo codice di ID con un `TouchEffect` istanza. Un elemento viene aggiunto a questo dizionario quando il `Touch` gestore viene chiamato per la pressione di un dito:

```csharp
void OnTouch(object sender, Android.Views.View.TouchEventArgs args)
{
    ...
    switch (args.Event.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:
            FireEvent(this, id, TouchActionType.Pressed, screenPointerCoords, true);

            idToEffectDictionary.Add(id, this);

            capture = libTouchEffect.Capture;
            break;

```

L'elemento viene rimosso dal `idToEffectDictionary` quando viene rilasciato il dito dallo schermo. Il `FireEvent` metodo accumula semplicemente tutte le informazioni necessarie per chiamare il `OnTouchAction` metodo:

```csharp
void FireEvent(TouchEffect touchEffect, int id, TouchActionType actionType, Point pointerLocation, bool isInContact)
{
    // Get the method to call for firing events
    Action<Element, TouchActionEventArgs> onTouchAction = touchEffect.libTouchEffect.OnTouchAction;

    // Get the location of the pointer within the view
    touchEffect.view.GetLocationOnScreen(twoIntArray);
    double x = pointerLocation.X - twoIntArray[0];
    double y = pointerLocation.Y - twoIntArray[1];
    Point point = new Point(fromPixels(x), fromPixels(y));

    // Call the method
    onTouchAction(touchEffect.formsElement,
        new TouchActionEventArgs(id, actionType, point, isInContact));
}
```

Tutti gli altri tipi di tocco vengono elaborati in due modi diversi: se il `Capture` proprietà viene `true`, l'evento touch è una conversione in un'operazione abbastanza semplice il `TouchEffect` informazioni. Ottiene più complesso quando `Capture` è `false` perché gli eventi di tocco potrebbero dover essere spostati da un'unica visualizzazione a altra. Questo è compito del `CheckForBoundaryHop` metodo, che viene chiamato durante gli eventi di spostamento. Questo metodo Usa entrambi i dizionari statici. Enumera tramite il `viewDictionary` per determinare la vista che sta attualmente toccando il dito e Usa `idToEffectDictionary` per archiviare l'oggetto corrente `TouchEffect` dell'istanza (e di conseguenza, la visualizzazione corrente) associata a un ID specifico:

```csharp
void CheckForBoundaryHop(int id, Point pointerLocation)
{
    TouchEffect touchEffectHit = null;

    foreach (Android.Views.View view in viewDictionary.Keys)
    {
        // Get the view rectangle
        try
        {
            view.GetLocationOnScreen(twoIntArray);
        }
        catch // System.ObjectDisposedException: Cannot access a disposed object.
        {
            continue;
        }
        Rectangle viewRect = new Rectangle(twoIntArray[0], twoIntArray[1], view.Width, view.Height);

        if (viewRect.Contains(pointerLocation))
        {
            touchEffectHit = viewDictionary[view];
        }
    }

    if (touchEffectHit != idToEffectDictionary[id])
    {
        if (idToEffectDictionary[id] != null)
        {
            FireEvent(idToEffectDictionary[id], id, TouchActionType.Exited, pointerLocation, true);
        }
        if (touchEffectHit != null)
        {
            FireEvent(touchEffectHit, id, TouchActionType.Entered, pointerLocation, true);
        }
        idToEffectDictionary[id] = touchEffectHit;
    }
}
```

Se è stata apportata una modifica nel `idToEffectDictionary`, potenzialmente chiama il metodo `FireEvent` per `Exited` e `Entered` trasferimento da una vista a altra. Tuttavia, il dito siano stato spostato in un'area occupata da una vista senza un oggetto associato `TouchEffect`, o da tale area da una vista con l'effetto collegato.

Si noti che il `try` e `catch` bloccare quando si accede alla visualizzazione. In una pagina che si accede al che quindi si passa nuovamente alla home page, il `OnDetached` non viene chiamato e gli elementi rimangono nella `viewDictionary` ma Android li considererà eliminato.

### <a name="the-ios-implementation"></a>L'implementazione di iOS

L'implementazione di iOS è simile all'implementazione di Android con la differenza che iOS `TouchEffect` classe deve creare un'istanza di un derivato di `UIGestureRecognizer`. Si tratta di una classe nel progetto iOS denominato `TouchRecognizer`. Questa classe gestisce due dizionari statici che archiviano `TouchRecognizer` istanze:

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

Molto della struttura di questo `TouchRecognizer` classe è simile a Android `TouchEffect` classe.

## <a name="putting-the-touch-effect-to-work"></a>Inserire l'effetto di tocco per l'utilizzo

Il [ **TouchTrackingEffectDemos** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/) programma contiene cinque pagine che testare l'effetto di tocco di rilevamento per attività comuni.

Il **BoxView trascinando** pagina consente di aggiungere `BoxView` elementi da un `AbsoluteLayout` e quindi trascinare tali attorno alla schermata. Il [file XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml) un'istanza di due `Button` visualizzazioni per l'aggiunta di `BoxView` elementi per il `AbsoluteLayout` e la cancellazione il `AbsoluteLayout`.

Il metodo nel [file code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml.cs) che aggiunge un nuovo `BoxView` per il `AbsoluteLayout` aggiunge anche un `TouchEffect` dell'oggetto per il `BoxView` e associa un gestore eventi per l'effetto:

```csharp
void AddBoxViewToLayout()
{
    BoxView boxView = new BoxView
    {
        WidthRequest = 100,
        HeightRequest = 100,
        Color = new Color(random.NextDouble(),
                          random.NextDouble(),
                          random.NextDouble())
    };

    TouchEffect touchEffect = new TouchEffect();
    touchEffect.TouchAction += OnTouchEffectAction;
    boxView.Effects.Add(touchEffect);
    absoluteLayout.Children.Add(boxView);
}
```

Il `TouchAction` gestore eventi elabora tutti gli eventi di tocco per tutti i `BoxView` elementi, ma è necessario prestare particolare attenzione: non può supportare due dita su una singola `BoxView` perché il programma implementa solo il trascinamento e restituirebbe due dita interferiscono tra loro. Per questo motivo, la pagina definisce una classe incorporata per ogni dito non viene rilevato:

```csharp
class DragInfo
{
    public DragInfo(long id, Point pressPoint)
    {
        Id = id;
        PressPoint = pressPoint;
    }

    public long Id { private set; get; }

    public Point PressPoint { private set; get; }
}

Dictionary<BoxView, DragInfo> dragDictionary = new Dictionary<BoxView, DragInfo>();
```

Il `dragDictionary` contiene una voce per ogni `BoxView` attualmente trascinato.

Il `Pressed` touch azione aggiunge un elemento al dizionario e il `Released` azione lo rimuove. Il `Pressed` per la logica deve verificare se è già presente un elemento nel dizionario per che `BoxView`. In questo caso, il `BoxView` è già in corso operazioni di trascinamento e il nuovo evento è un secondo finger su esso stesso `BoxView`. Per il `Moved` e `Released` le azioni, il gestore eventi deve controllare se il dizionario è una voce per tale `BoxView` e che il tocco `Id` proprietà per trascinare `BoxView` corrisponda a quella voce di dizionario:

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    BoxView boxView = sender as BoxView;

    switch (args.Type)
    {
        case TouchActionType.Pressed:
            // Don't allow a second touch on an already touched BoxView
            if (!dragDictionary.ContainsKey(boxView))
            {
                dragDictionary.Add(boxView, new DragInfo(args.Id, args.Location));

                // Set Capture property to true
                TouchEffect touchEffect = (TouchEffect)boxView.Effects.FirstOrDefault(e => e is TouchEffect);
                touchEffect.Capture = true;
            }
            break;

        case TouchActionType.Moved:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                Rectangle rect = AbsoluteLayout.GetLayoutBounds(boxView);
                Point initialLocation = dragDictionary[boxView].PressPoint;
                rect.X += args.Location.X - initialLocation.X;
                rect.Y += args.Location.Y - initialLocation.Y;
                AbsoluteLayout.SetLayoutBounds(boxView, rect);
            }
            break;

        case TouchActionType.Released:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                dragDictionary.Remove(boxView);
            }
            break;
    }
}
```

Il `Pressed` set per la logica di `Capture` proprietà del `TouchEffect` oggetto `true`. Questo ha l'effetto della distribuzione di tutti gli eventi successivi per tale dito al gestore dell'evento stesso.

Il `Moved` spostamenti per la logica di `BoxView` modificando il `LayoutBounds` proprietà associata. Il `Location` proprietà degli argomenti dell'evento è sempre relativo al `BoxView` trascinati e se il `BoxView` trascinato a una velocità costante, il `Location` proprietà degli eventi consecutivi sarà approssimativamente lo stesso. Ad esempio, se un dito preme il `BoxView` nel proprio centro, il `Pressed` archivi azione un `PressPoint` proprietà di (50, 50), che rimane invariato per gli eventi successivi. Se il `BoxView` viene trascinato in diagonale a una velocità costante, i successiva `Location` proprietà durante il `Moved` azione potrebbe essere i valori di (55, 55), nel qual caso il `Moved` per la logica aggiunge 5 per la posizione orizzontale e verticale del `BoxView`. Questa operazione Sposta il `BoxView` in modo che il centro è di nuovo direttamente sotto il dito.

È possibile spostare più `BoxView` elementi contemporaneamente utilizzando le dita diversi.

[![](touch-tracking-images/boxviewdragging-small.png "Tripla screenshot della pagina BoxView trascinando")](touch-tracking-images/boxviewdragging-large.png#lightbox "tripla screenshot della pagina BoxView trascinamento")

### <a name="subclassing-the-view"></a>Creazione di una sottoclasse della visualizzazione

Spesso è più semplice per un elemento di xamarin. Forms gestire i propri eventi di tocco. Il **trascinabile BoxView trascinando** pagina funziona esattamente come le **BoxView trascinando** pagina, ma gli elementi che l'utente trascina è istanze di un [ `DraggableBoxView` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/DraggableBoxView.cs) classe che deriva da `BoxView`:

```csharp
class DraggableBoxView : BoxView
{
    bool isBeingDragged;
    long touchId;
    Point pressPoint;

    public DraggableBoxView()
    {
        TouchEffect touchEffect = new TouchEffect
        {
            Capture = true
        };
        touchEffect.TouchAction += OnTouchEffectAction;
        Effects.Add(touchEffect);
    }

    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!isBeingDragged)
                {
                    isBeingDragged = true;
                    touchId = args.Id;
                    pressPoint = args.Location;
                }
                break;

            case TouchActionType.Moved:
                if (isBeingDragged && touchId == args.Id)
                {
                    TranslationX += args.Location.X - pressPoint.X;
                    TranslationY += args.Location.Y - pressPoint.Y;
                }
                break;

            case TouchActionType.Released:
                if (isBeingDragged && touchId == args.Id)
                {
                    isBeingDragged = false;
                }
                break;
        }
    }
}
```

Costruttore crea e collega i `TouchEffect`e imposta il `Capture` proprietà quando tale oggetto viene innanzitutto creata un'istanza. Non è un dizionario è necessario perché la classe Archivia `isBeingDragged`, `pressPoint`, e `touchId` valori associati a ogni dito. Il `Moved` viene modificata la gestione del `TranslationX` e `TranslationY` delle proprietà in modo funziona la logica anche se l'elemento padre del `DraggableBoxView` non è un `AbsoluteLayout`.

### <a name="integrating-with-skiasharp"></a>L'integrazione con SkiaSharp

Le due dimostrazioni richiedono grafica, e usano SkiaSharp per questo scopo. Si potrebbe voler conoscere [uso di SkiaSharp in xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) prima di esaminare questi esempi. I primi due articoli ("Informazioni di base di SkiaSharp disegno" e "Percorsi e le linee di SkiaSharp") coprono tutto ciò che occorre qui.

Il **ellisse disegno** pagina consente di disegnare un'ellisse scorrendo rapidamente verso il dito sullo schermo. A seconda di come si sposta il dito, è possibile disegnare l'ellisse da alto a sinistra da basso a destra o da qualsiasi altro angolo all'angolo opposto. Ellisse viene disegnata con colori casuali e opacità.

[![](touch-tracking-images/ellipsedrawing-small.png "Tripla screenshot della pagina di disegno ellisse")](touch-tracking-images/ellipsedrawing-large.png#lightbox "tripla screenshot della pagina di disegno ellisse")

Se si tocca quindi uno dei puntini di sospensione, è possibile trascinarlo in un'altra posizione. Ciò richiede una tecnica nota come "hit testing," che implica la ricerca per l'oggetto grafico in un momento specifico. I puntini di sospensione di SkiaSharp non sono elementi di xamarin. Forms, pertanto non possono eseguire le proprie `TouchEffect` l'elaborazione. Il `TouchEffect` necessario applicare all'intera `SKCanvasView` oggetto.

Il [EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml) file creare un'istanza di `SKCanvasView` in una cella singola `Grid`. Il `TouchEffect` oggetto è connesso a quella `Grid`:

```xaml
<Grid x:Name="canvasViewGrid"
        Grid.Row="1"
        BackgroundColor="White">

    <skia:SKCanvasView x:Name="canvasView"
                        PaintSurface="OnCanvasViewPaintSurface" />
    <Grid.Effects>
        <tt:TouchEffect Capture="True"
                        TouchAction="OnTouchEffectAction" />
    </Grid.Effects>
</Grid>
```

In Android e la piattaforma Windows universale, la `TouchEffect` possono essere collegati direttamente al `SKCanvasView`, ma in iOS che non funziona. Si noti che il `Capture` è impostata su `true`.

Ogni ellisse che esegue il rendering di SkiaSharp è rappresentato da un oggetto di tipo `EllipseDrawingFigure`:

```csharp
class EllipseDrawingFigure
{
    SKPoint pt1, pt2;

    public EllipseDrawingFigure()
    {
    }

    public SKColor Color { set; get; }

    public SKPoint StartPoint
    {
        set
        {
            pt1 = value;
            MakeRectangle();
        }
    }

    public SKPoint EndPoint
    {
        set
        {
            pt2 = value;
            MakeRectangle();
        }
    }

    void MakeRectangle()
    {
        Rectangle = new SKRect(pt1.X, pt1.Y, pt2.X, pt2.Y).Standardized;
    }

    public SKRect Rectangle { set; get; }

    // For dragging operations
    public Point LastFingerLocation { set; get; }

    // For the dragging hit-test
    public bool IsInEllipse(SKPoint pt)
    {
        SKRect rect = Rectangle;

        return (Math.Pow(pt.X - rect.MidX, 2) / Math.Pow(rect.Width / 2, 2) +
                Math.Pow(pt.Y - rect.MidY, 2) / Math.Pow(rect.Height / 2, 2)) < 1;
    }
}
```

Il `StartPoint` e `EndPoint` proprietà vengono usate quando il programma sta elaborando il tocco di input; il `Rectangle` proprietà viene utilizzata per disegnare l'ellisse. Il `LastFingerLocation` proprietà entra in gioco quando viene trascinato l'ellisse e il `IsInEllipse` facilita metodo hit testing. Il metodo restituisce `true` se il punto è all'interno dell'ellisse.

Il [file code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml.cs) mantiene tre raccolte:

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

Il `draggingFigure` dizionario contiene un subset del `completedFigures` raccolta. Di SkiaSharp `PaintSurface` gestore dell'evento è semplicemente esegue il rendering di oggetti in questi i `completedFigures` e `inProgressFigures` raccolte:

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Fill
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (EllipseDrawingFigure figure in completedFigures)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
    foreach (EllipseDrawingFigure figure in inProgressFigures.Values)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
}
```

La parte più difficile dell'elaborazione touch è il `Pressed` la gestione. Si tratta di dove l'hit testing è stata eseguita, ma se il codice rileva un'ellisse sotto il dito dell'utente, tale puntini di sospensione può essere trascinato solo se non è attualmente trascinato da un altro con un dito. Se è presente alcun ellisse sotto il dito dell'utente, il codice inizia il processo di creazione di un'ellisse nuovo:

```csharp
case TouchActionType.Pressed:
    bool isDragOperation = false;

    // Loop through the completed figures
    foreach (EllipseDrawingFigure fig in completedFigures.Reverse<EllipseDrawingFigure>())
    {
        // Check if the finger is touching one of the ellipses
        if (fig.IsInEllipse(ConvertToPixel(args.Location)))
        {
            // Tentatively assume this is a dragging operation
            isDragOperation = true;

            // Loop through all the figures currently being dragged
            foreach (EllipseDrawingFigure draggedFigure in draggingFigures.Values)
            {
                // If there's a match, we'll need to dig deeper
                if (fig == draggedFigure)
                {
                    isDragOperation = false;
                    break;
                }
            }

            if (isDragOperation)
            {
                fig.LastFingerLocation = args.Location;
                draggingFigures.Add(args.Id, fig);
                break;
            }
        }
    }

    if (isDragOperation)
    {
        // Move the dragged ellipse to the end of completedFigures so it's drawn on top
        EllipseDrawingFigure fig = draggingFigures[args.Id];
        completedFigures.Remove(fig);
        completedFigures.Add(fig);
    }
    else // start making a new ellipse
    {
        // Random bytes for random color
        byte[] buffer = new byte[4];
        random.NextBytes(buffer);

        EllipseDrawingFigure figure = new EllipseDrawingFigure
        {
            Color = new SKColor(buffer[0], buffer[1], buffer[2], buffer[3]),
            StartPoint = ConvertToPixel(args.Location),
            EndPoint = ConvertToPixel(args.Location)
        };
        inProgressFigures.Add(args.Id, figure);
    }
    canvasView.InvalidateSurface();
    break;
```

Un altro esempio di SkiaSharp è il **dito Paint** pagina. È possibile selezionare un colore del tratto e spessore del tratto da due `Picker` quindi disegnare con uno o più DITA e delle visualizzazioni:

[![](touch-tracking-images/fingerpaint-small.png "Tripla screenshot della pagina di disegno con un dito")](touch-tracking-images/fingerpaint-large.png#lightbox "tripla screenshot della pagina di disegno con un dito")

In questo esempio richiede anche una classe separata per rappresentare ogni riga disegnata sullo schermo:

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new SKPath();
    }

    public SKPath Path { set; get; }

    public Color StrokeColor { set; get; }

    public float StrokeWidth { set; get; }
}
```

Un `SKPath` oggetto viene usato per eseguire il rendering di ogni riga. Il [FingerPaint.xaml.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/FingerPaintPage.xaml.cs) file gestisce due raccolte di questi oggetti, uno per le polilinee da disegnare e altro per le polilinee completate:

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Il `Pressed` elaborazione crea un nuovo `FingerPaintPolyline`, le chiamate `MoveTo` sull'oggetto percorso per memorizzare il punto iniziale e aggiunge tale oggetto per il `inProgressPolylines` dizionario. Il `Moved` elaborare chiamate `LineTo` sull'oggetto percorso con la nuova posizione di un dito e il `Released` elaborazione trasferisce la polilinea completata dal `inProgressPolylines` a `completedPolylines`. Ancora una volta, l'effettivo SkiaSharp codice di disegno è relativamente semplice:

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    StrokeCap = SKStrokeCap.Round,
    StrokeJoin = SKStrokeJoin.Round
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (FingerPaintPolyline polyline in completedPolylines)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }

    foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }
}
```

### <a name="tracking-view-to-view-touch"></a>Per visualizzare Touch di rilevamento

Tutti gli esempi precedenti è sono impostato il `Capture` proprietà del `TouchEffect` al `true`, quando il `TouchEffect` è stato creato o quando il `Pressed` si è verificato un evento. Ciò garantisce che lo stesso elemento riceva tutti gli eventi associati con il dito che innanzitutto premuto la visualizzazione. L'esempio finale *non* impostare `Capture` a `true`. Ciò determina un comportamento diverso quando si sposta un dito in contatto con la schermata da un elemento a un altro. L'elemento che si sposta il dito dal riceve un evento con un `Type` impostata su `TouchActionType.Exited` e il secondo elemento riceve un evento con un `Type` impostazione `TouchActionType.Entered`.

Questo tipo di elaborazione touch è molto utile per la tastiera musica. Una chiave deve essere in grado di rilevare quando viene premuto, ma anche quando un dito diapositive da una chiave a altra.

Il **invisibile all'utente della tastiera** pagina definisce piccoli [ `WhiteKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/WhiteKey.cs) e [ `BlackKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BlackKey.cs) classi che derivano da [ `Key` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/Key.cs), che deriva da `BoxView`.

Il `Key` classe è pronto per essere usato in un programma musica effettivo. Definisce proprietà pubbliche denominate `IsPressed` e `KeyNumber`, che deve essere impostata per il codice tasto stabilito dallo standard MIDI. Il `Key` classe definisce anche un evento denominato `StatusChanged`, che viene richiamato quando il `IsPressed` le modifiche alle proprietà.

Sono consentite più dita su ciascuna chiave. Per questo motivo, il `Key` classe mantiene una `List` dei numeri ID tocco di tutte le dita attualmente tocca tale chiave:

```csharp
List<long> ids = new List<long>();
```

Il `TouchAction` gestore dell'evento aggiunge un ID per il `ids` elenco per entrambi un `Pressed` tipo di evento e un' `Entered` tipo, ma solo quando il `IsInContact` proprietà è `true` per il `Entered` evento. L'ID viene rimosso dal `List` per un `Released` o `Exited` evento:

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    switch (args.Type)
    {
      case TouchActionType.Pressed:
          AddToList(args.Id);
          break;

        case TouchActionType.Entered:
            if (args.IsInContact)
            {
                AddToList(args.Id);
            }
            break;

        case TouchActionType.Moved:
            break;

        case TouchActionType.Released:
        case TouchActionType.Exited:
            RemoveFromList(args.Id);
            break;
    }
}

```

Il `AddToList` e `RemoveFromList` entrambi i metodi controllano se il `List` è stato modificato tra vuoto e non è vuoto e in caso affermativo, richiama il `StatusChanged` evento.

I vari `WhiteKey` e `BlackKey` gli elementi vengono disposti in della pagina [file XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/SilentKeyboardPage.xaml), che offre risultati ottimali quando il telefono viene posizionato in una modalità orizzontale:

[![](touch-tracking-images/silentkeyboard-small.png "Tripla screenshot della pagina invisibile all'utente della tastiera")](touch-tracking-images/silentkeyboard-large.png#lightbox "tripla screenshot della pagina invisibile all'utente della tastiera")

Se si durante lo sweep il dito attraverso le chiavi, scoprirai le modifiche di lieve a colori che gli eventi di tocco vengono trasferiti da una chiave a altra.

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come richiamare gli eventi in un effetto e come scrivere e usare un effetto che implementa l'elaborazione di basso livello Multi-touch.


## <a name="related-links"></a>Collegamenti correlati

- [Multi-Touch di rilevamento con un dito in iOS](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Con un dito multitouch in Android di rilevamento](~/android/app-fundamentals/touch/touch-tracking.md)
- [Effetto di rilevamento tocco (esempio)](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)
