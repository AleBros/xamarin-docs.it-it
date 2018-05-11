---
title: Richiamo di eventi da effetti
description: Un effetto è possibile definire e richiamare un evento, che segnala le modifiche nella visualizzazione nativa sottostante. Questo articolo illustra come implementare un dito di basso livello Multi-touch di rilevamento e come generare eventi che indicano l'attività di tocco.
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: e363cae4dd72a25e4768395410d4e56a8db30eba
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="invoking-events-from-effects"></a>Richiamo di eventi da effetti

_Un effetto è possibile definire e richiamare un evento, che segnala le modifiche nella visualizzazione nativa sottostante. Questo articolo illustra come implementare un dito di basso livello Multi-touch di rilevamento e come generare eventi che indicano l'attività di tocco._

L'effetto descritto in questo articolo fornisce l'accesso agli eventi tocco di basso livello. Questi eventi di basso livello non sono disponibili tramite esistente `GestureRecognizer` classi, ma sono fondamentali per alcuni tipi di applicazioni. Ad esempio, un finger-paint esigenze dell'applicazione per tenere traccia delle dita singoli durante lo spostamento sullo schermo. Una tastiera musica deve rilevare le scelte e rilascia su singole chiavi, nonché un dito gliding da una chiave a un altro in un glissando.

Un effetto è ideale per Multi-touch dito rilevamento perché può essere collegato a qualsiasi elemento di xamarin. Forms.

## <a name="platform-touch-events"></a>Eventi tocco della piattaforma

Piattaforma UWP includono un'API di basso livello che consente alle applicazioni di rilevare tutti, iOS e Android toccare attività. Eventi di tocco queste piattaforme che tutti distinguere tra i tre tipi di base:

- *Premuto*, quando un dito tocca lo schermo
- *Spostato*, quando viene spostato un dito tocca lo schermo
- *Rilasciato*, quando viene rilasciato il dito dallo schermo

In un ambiente multi-touch dita più possano toccare lo schermo contemporaneamente. Varie piattaforme includono un numero di identificazione (ID) che le applicazioni possono utilizzare per distinguere tra più dita.

In iOS, il `UIView` classe definisce tre metodi sottoponibili a override, `TouchesBegan`, `TouchesMoved`, e `TouchesEnded` corrispondenti a questi tre eventi di base. L'articolo [multi-Touch dito rilevamento](~/ios/app-fundamentals/touch/touch-tracking.md) viene descritto come utilizzare questi metodi. Tuttavia, un programma iOS non è necessario eseguire l'override di una classe che deriva da `UIView` utilizzare questi metodi. IOS `UIGestureRecognizer` definisce anche questi stessi tre metodi ed è possibile collegare un'istanza di una classe che deriva da `UIGestureRecognizer` a qualsiasi `UIView` oggetto.

In Android, il `View` classe definisce un metodo sottoponibile a override denominato `OnTouchEvent` per elaborare tutte le attività per il tocco. Il tipo dell'attività touch è definito dai membri di enumerazione `Down`, `PointerDown`, `Move`, `Up`, e `PointerUp` come descritto nell'articolo [multi-Touch dito rilevamento](~/android/app-fundamentals/touch/touch-tracking.md). Il Android `View` definisce inoltre un evento denominato `Touch` che consente un gestore eventi da collegare a qualsiasi `View` oggetto.

In Universal Windows Platform (UWP), il `UIElement` classe definisce eventi denominati `PointerPressed`, `PointerMoved`, e `PointerReleased`. Questi elementi sono descritti nell'articolo [articolo Handle Input puntatore su MSDN](/windows/uwp/input-and-devices/handle-pointer-input/) e la documentazione dell'API per la [ `UIElement` ](/uwp/api/windows.ui.xaml.uielement/) classe.

Il `Pointer` API nella piattaforma Windows universale è progettata per unificare mouse, tocco e input penna. Per questo motivo, il `PointerMoved` evento viene richiamato quando il mouse viene spostato in un elemento, anche quando non si preme un pulsante del mouse. Il `PointerRoutedEventArgs` oggetto associato a questi eventi è una proprietà denominata `Pointer` che include una proprietà denominata `IsInContact` che indica se viene premuto un pulsante del mouse o un dito in contatto con la schermata.

Inoltre, la piattaforma UWP definisce due ulteriori eventi denominati `PointerEntered` e `PointerExited`. Queste informazioni indicano quando il mouse o un dito viene spostata da un elemento a un altro. Si consideri ad esempio due elementi adiacenti, denominati A e b. Entrambi gli elementi sono installati i gestori per gli eventi puntatore. Quando si preme un dito su A, il `PointerPressed` evento viene richiamato. Quando si sposta, la classe richiama `PointerMoved` eventi. Se si sposta da A B, A richiama un `PointerExited` a e B evento richiama un `PointerEntered` evento. Se il dito viene quindi rilasciato, B richiama un `PointerReleased` evento.

Le piattaforme iOS e Android sono diverse da alla piattaforma UWP: la vista che ottiene prima di tutto la chiamata a `TouchesBegan` o `OnTouchEvent` quando un dito tocca la visualizzazione continua ottenere tutti i tocco attività anche se se si sposta in diverse visualizzazioni. La piattaforma UWP possa comportarsi in modo analogo se l'applicazione acquisisce il puntatore: nel `PointerEntered` gestore eventi, le chiamate di elemento `CapturePointer` e ottiene tutte le attività touch da tale dito.

L'approccio UWP si rivela molto utile per alcuni tipi di applicazioni, ad esempio, una tastiera musica. Ogni chiave è possibile gestire gli eventi di tocco per la chiave e rilevare quando un dito ha fatto scorrere da una chiave a un altro utilizzando il `PointerEntered` e `PointerExited` eventi.

Per questo motivo, l'effetto di tocco rilevamento descritto in questo articolo implementa l'approccio UWP.

## <a name="the-touch-tracking-effect-api"></a>L'API di effetto di rilevamento di tocco

Il [ **tocco demo effetto rilevamento** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/) esempio contiene le classi e un'enumerazione implementare il rilevamento di tocco basso livello. Questi tipi appartengono allo spazio dei nomi `TouchTracking` e iniziare con la parola `Touch`. Il **TouchTrackingEffectDemos** progetto di libreria .NET Standard include il `TouchActionType` enumerazione per il tipo di eventi tocco:

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

Tutte le piattaforme includono anche un evento che indica che l'evento tocco è stata annullata.

Il `TouchEffect` classe nella libreria .NET Standard deriva da `RoutingEffect` e definisce un evento denominato `TouchAction` e un metodo denominato `OnTouchAction` che richiama la `TouchAction` evento:

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

Si noti inoltre la `Capture` proprietà. Per acquisire gli eventi tocco, un'applicazione deve impostare questa proprietà su `true` prima di un `Pressed` evento. In caso contrario, gli eventi tocco si comportano come quelle nella piattaforma Windows universale.

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

Un'applicazione può utilizzare il `Id` proprietà per tenere traccia delle dita singoli. Si noti il `IsInContact` proprietà. Questa proprietà è sempre `true` per `Pressed` gli eventi e `false` per `Released` eventi. È inoltre sempre `true` per `Moved` gli eventi in iOS e Android. Il `IsInContact` proprietà potrebbe essere `false` per `Moved` eventi sulla piattaforma Windows Universal quando l'esecuzione del programma sul desktop e il puntatore del mouse viene spostato senza un pulsante premuto.

È possibile usare il `TouchEffect` classe nelle proprie applicazioni includendo il file nel progetto di libreria Standard di .NET della soluzione e mediante l'aggiunta di un'istanza per il `Effects` raccolta di elementi di xamarin. Forms. Collegare un gestore per il `TouchAction` evento per ottenere gli eventi tocco.

Per utilizzare `TouchEffect` nella propria applicazione, è necessario anche le implementazioni di piattaforma incluso in **TouchTrackingEffectDemos** soluzione.

## <a name="the-touch-tracking-effect-implementations"></a>Le implementazioni di rilevamento di tocco effetto

UWP, iOS e Android le implementazioni del `TouchEffect` sono descritti di seguito che inizia con l'implementazione più semplice (UWP) e termina con l'implementazione di iOS è strutturalmente più complesso rispetto alle altre.

### <a name="the-uwp-implementation"></a>L'implementazione di piattaforma UWP

L'implementazione di piattaforma UWP di `TouchEffect` è la più semplice. Come al solito, la classe deriva da `PlatformEffect` e include due attributi di assembly:

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

Il `OnAttached` override alcune informazioni vengono salvate come gestori di campi e viene allegato a tutti gli eventi di puntatore:

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

`OnPointerPressed` Controlla anche il valore di `Capture` proprietà nella classe effetto la libreria Standard di .NET e chiama `CapturePointer` se è `true`.

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

Le implementazioni di Android e iOS sono necessariamente più complesse poiché devono implementare il `Exited` e `Entered` eventi quando un dito sposta da un elemento a un altro. Entrambe le implementazioni sono strutturate in modo analogo.

Il Android `TouchEffect` classe consente di installare un gestore per il `Touch` evento:

```csharp
view = Control == null ? Container : Control;
...
view.Touch += OnTouch;
```

La classe definisce inoltre due dizionari statici:

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

Il `viewDictionary` Ottiene una nuova voce ogni volta il `OnAttached` viene chiamato l'override:

```csharp
viewDictionary.Add(view, this);
```

La voce viene rimossa dal dizionario in `OnDetached`. Ogni istanza di `TouchEffect` è associata a una visualizzazione specifica che l'effetto è collegata. Il dizionario statico consente qualsiasi `TouchEffect` istanza per enumerare tutte le altre visualizzazioni e le relative `TouchEffect` istanze. Ciò è necessario per consentire il trasferimento di eventi da una vista a altro.

Android assegna un codice ID tocco gli eventi che consente a un'applicazione tenere traccia delle dita singoli. Il `idToEffectDictionary` associa questo codice ID con un `TouchEffect` istanza. Un elemento viene aggiunto al dizionario quando il `Touch` gestore viene chiamato per la pressione di un dito:

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

L'elemento viene rimosso dal `idToEffectDictionary` quando il dito viene rilasciato dalla schermata. Il `FireEvent` metodo accumula semplicemente tutte le informazioni necessarie per chiamare il `OnTouchAction` metodo:

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

Tutti gli altri tipi di tocco vengono elaborati in due modi diversi: se il `Capture` proprietà `true`, l'evento tocco è una semplice conversione il `TouchEffect` informazioni. Ottiene più complesso quando `Capture` è `false` perché gli eventi tocco debbano essere spostati da una visualizzazione a un altro. Questa è la responsabilità del `CheckForBoundaryHop` metodo, che viene chiamato durante gli eventi di spostamento. Questo metodo si avvalgono di entrambi i dizionari statici. Enumera tramite il `viewDictionary` per determinare la visualizzazione attualmente tocca il dito e Usa `idToEffectDictionary` per archiviare corrente `TouchEffect` istanza (e di conseguenza, la visualizzazione corrente) associata a un ID specifico:

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

Se è stata apportata una modifica nel `idToEffectionDictionary`, il metodo viene chiamato potenzialmente `FireEvent` per `Exited` e `Entered` trasferimento da una vista a altra. Tuttavia, si potrebbe sono stati spostati in un'area occupata da una visualizzazione senza un allegato `TouchEffect`, o da tale area per una vista con l'effetto associata.

Si noti il `try` e `catch` blocco quando si accede alla visualizzazione. In una pagina che si accede che quindi torna alla home page di `OnDetached` non viene chiamato e gli elementi rimangono nella `viewDictionary` ma Android considera eliminato.

### <a name="the-ios-implementation"></a>L'implementazione di iOS

L'implementazione di iOS è simile all'implementazione di Android con la differenza che iOS `TouchEffect` classe deve creare un'istanza di un derivato di `UIGestureRecognizer`. Si tratta di una classe nel progetto iOS denominato `TouchRecognizer`. Questa classe gestisce due dizionari statici che archiviano `TouchRecognizer` istanze:

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

La struttura di questa misura `TouchRecognizer` classe è simile per il Android `TouchEffect` classe.

## <a name="putting-the-touch-effect-to-work"></a>Inserire l'effetto di tocco per l'utilizzo

Il [ **TouchTrackingEffectDemos** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/) programma contiene cinque pagine che testare l'effetto di rilevamento di tocco per le attività comuni.

Il **BoxView trascinando** pagina consente di aggiungere `BoxView` elementi da un `AbsoluteLayout` e quindi trascinare tali sullo schermo. Il [file XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml) un'istanza di due `Button` viste per l'aggiunta di `BoxView` elementi per il `AbsoluteLayout` e la cancellazione di `AbsoluteLayout`.

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

Il `TouchAction` gestore elabora tutti gli eventi di tocco per tutti i `BoxView` elementi, ma è necessario prestare attenzione: quindi non consente due dita su un unico `BoxView` perché il programma implementa solo il trascinamento e due dita restituirebbe interferiscono tra loro. Per questo motivo, la pagina definisce una classe incorporata per ogni dito attualmente controllato:

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

Il `dragDictionary` contiene una voce per ogni `BoxView` trascinata.

Il `Pressed` azione tocco aggiunge un elemento al dizionario e `Released` azione rimuoverlo. Il `Pressed` logica deve controllare se è già presente un elemento nel dizionario per tale `BoxView`. In questo caso, il `BoxView` è già trascinato e il nuovo evento è un secondo finger su tale stesso `BoxView`. Per il `Moved` e `Released` azioni, il gestore dell'evento deve controllare se il dizionario dispone di una voce per tale `BoxView` e che il tocco `Id` proprietà per tale trascinato `BoxView` corrisponda a quella nella voce del dizionario:

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

Il `Pressed` set logica di `Capture` proprietà del `TouchEffect` oggetto `true`. Questo ha l'effetto del rilascio di tutti gli eventi successivi per tale dito al gestore dell'evento stesso.

Il `Moved` si sposta la logica di `BoxView` modificando il `LayoutBounds` proprietà associata. Il `Location` proprietà degli argomenti dell'evento è sempre relativo al `BoxView` trascinati e, se il `BoxView` viene trascinata a una velocità costante, il `Location` proprietà degli eventi consecutivi corrisponderà a circa. Ad esempio, se un dito preme il `BoxView` al centro, il `Pressed` azione archivi un `PressPoint` proprietà di (50, 50), che rimane lo stesso per gli eventi successivi. Se il `BoxView` viene trascinato in diagonale a una velocità costante, le successiva `Location` proprietà durante il `Moved` azione potrebbe essere valori di (55, 55), nel qual caso il `Moved` logica aggiunge 5 per la posizione orizzontale e verticale del `BoxView`. Ciò consente di spostare il `BoxView` in modo che il centro è nuovamente direttamente sotto il dito.

È possibile spostare più `BoxView` elementi contemporaneamente utilizzando dita diverse.

[![](touch-tracking-images/boxviewdragging-small.png "Schermata triplo della pagina BoxView trascinando")](touch-tracking-images/boxviewdragging-large.png#lightbox "tripla schermata della pagina BoxView trascinando")

### <a name="subclassing-the-view"></a>La visualizzazione di creazione di sottoclassi

Spesso risulta più semplice per un elemento di xamarin. Forms gestire i propri eventi tocco. Il **Draggable BoxView trascinando** pagina funziona nello stesso modo di **BoxView trascinando** pagina, ma gli elementi che l'utente trascina è istanze di un [ `DraggableBoxView` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/DraggableBoxView.cs) classe che deriva da `BoxView`:

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

Il costruttore crea e collega il `TouchEffect`e imposta il `Capture` proprietà quando viene creata un'istanza dell'oggetto. Non è un dizionario è necessario perché la classe Archivia `isBeingDragged`, `pressPoint`, e `touchId` i valori associati a ogni dito. Il `Moved` gestione modifica il `TranslationX` e `TranslationY` proprietà in modo la logica funzioni anche se l'elemento padre del `DraggableBoxView` non è un `AbsoluteLayout`.

### <a name="integrating-with-skiasharp"></a>L'integrazione con SkiaSharp

Le due dimostrazioni richiedono grafica e usano SkiaSharp a questo scopo. Si potrebbe voler conoscere [utilizzando SkiaSharp in xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) prima di esaminare questi esempi. I primi due articoli ("SkiaSharp disegno nozioni di base" e "SkiaSharp righe e i percorsi") riguardano tutti gli elementi che dovranno essere qui.

Il **disegno ellisse** pagina consente di disegnare un'ellisse scorrendo rapidamente verso il dito sullo schermo. A seconda di come si sposta il dito, è possibile disegnare l'ellisse da alto a sinistra in basso a destra o da qualsiasi altro angolo nell'angolo opposto. L'ellisse viene disegnata con un colore casuale e opacità.

[![](touch-tracking-images/ellipsedrawing-small.png "Schermata triplo della pagina di disegno ellisse")](touch-tracking-images/ellipsedrawing-large.png#lightbox "tripla schermata della pagina di disegno ellisse")

Se si tocca quindi uno dei puntini di sospensione, è possibile trascinarlo in un'altra posizione. Questa operazione richiede una tecnica nota come "hit testing," che prevede la ricerca per l'oggetto grafico in un momento specifico. I puntini di sospensione SkiaSharp non sono elementi di xamarin. Forms, pertanto non possono eseguire le proprie `TouchEffect` l'elaborazione. Il `TouchEffect` deve applicare all'intero `SKCanvasView` oggetto.

Il [EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml) file crea un'istanza di `SKCanvasView` in una cella singola `Grid`. Il `TouchEffect` oggetto è associato a quella `Grid`:

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

In Android e la piattaforma Windows universale, la `TouchEffect` può essere collegato direttamente il `SKCanvasView`, ma in iOS che non funziona. Si noti che il `Capture` è impostata su `true`.

Ciascun ellisse che esegue il rendering SkiaSharp è rappresentato da un oggetto di tipo `EllipseDrawingFigure`:

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

Il `StartPoint` e `EndPoint` vengono utilizzate quando il programma di elaborazione di input; tocco di `Rectangle` proprietà viene utilizzata per disegnare l'ellisse. Il `LastFingerLocation` proprietà entra in gioco quando viene trascinata i puntini di sospensione e `IsInEllipse` facilita metodo hit test. Il metodo restituisce `true` se il punto è all'interno dell'ellisse.

Il [file code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml.cs) gestisce tre raccolte:

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

Il `draggingFigure` dizionario contiene un subset del `completedFigures` insieme. Il SkiaSharp `PaintSurface` gestore semplicemente il rendering di questi oggetti di `completedFigures` e `inProgressFigures` raccolte:

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

La parte dell'elaborazione tocco complessa è il `Pressed` gestione. Questa opzione è quando viene eseguito l'hit testing, ma se il codice rileva un'ellisse con il dito dell'utente, tale puntini di sospensione può essere trascinato solo se non è attualmente trascinato da un altro con un dito. Se è presente alcun ellisse in dito dell'utente, il codice inizia il processo di creazione di un'ellisse nuovo:

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

Un altro esempio SkiaSharp è il **Paint dito** pagina. È possibile selezionare un colore di traccia e spessore del tratto da due `Picker` Visualizza e quindi disegnare con le dita di una o più:

[![](touch-tracking-images/fingerpaint-small.png "Schermata triplo della pagina di disegno con un dito")](touch-tracking-images/fingerpaint-large.png#lightbox "tripla schermata della pagina di disegno con un dito")

In questo esempio richiede inoltre una classe separata per rappresentare ogni riga disegnato sullo schermo:

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

Un `SKPath` oggetto viene usato per eseguire il rendering di ogni riga. Il [FingerPaint.xaml.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/FingerPaintPage.xaml.cs) file gestisce due raccolte di questi oggetti, uno per tali polilinea in corso un e un altro per la polilinea completata:

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Il `Pressed` elaborazione crea un nuovo `FingerPaintPolyline`, chiamate `MoveTo` sull'oggetto di percorso per archiviare il punto iniziale e aggiunge l'oggetto per il `inProgressPolylines` dizionario. Il `Moved` elaborazione chiamate `LineTo` sull'oggetto di percorso con la nuova posizione e con un dito e `Released` elaborazione trasferisce la polilinea completata da `inProgressPolylines` a `completedPolylines`. In questo caso, l'effettivo SkiaSharp codice di disegno è relativamente semplice:

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

### <a name="tracking-view-to-view-touch"></a>Rilevamento-viste tocco

Gli esempi precedenti è sono impostata la `Capture` proprietà del `TouchEffect` a `true`, quando il `TouchEffect` è stato creato o quando il `Pressed` evento si è verificato. In questo modo si garantisce che lo stesso elemento riceve tutti gli eventi associati il dito che si preme la vista. Nell'esempio finale *non* impostare `Capture` a `true`. Ciò causa un comportamento diverso quando viene spostato un dito in contatto con la schermata da un elemento a un altro. L'elemento che si sposta da riceve un evento con un `Type` proprietà impostata su `TouchActionType.Exited` e il secondo elemento riceve un evento con un `Type` l'impostazione di `TouchActionType.Entered`.

Questo tipo di elaborazione tocco è molto utile per una tastiera musica. Una chiave deve essere in grado di rilevare quando viene premuto, ma anche quando un dito diapositive da una chiave a un altro.

Il **tastiera** pagina definisce ridotto [ `WhiteKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/WhiteKey.cs) e [ `BlackKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BlackKey.cs) classi che derivano da [ `Key` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/Key.cs), che deriva da `BoxView`.

La `Key` classe è pronto per essere utilizzato in un programma musica effettivo. Definisce le proprietà pubbliche denominate `IsPressed` e `KeyNumber`, che deve essere impostata per il codice stabilito dallo standard MIDI. Il `Key` classe definisce inoltre un evento denominato `StatusChanged`, che viene richiamato quando il `IsPressed` le modifiche alle proprietà.

Per ogni chiave sono consentite più dita. Per questo motivo, il `Key` classe mantiene una `List` dei numeri ID tocco delle dita attualmente tocca tale chiave:

```csharp
List<long> ids = new List<long>();
```

Il `TouchAction` gestore eventi aggiunge un ID per il `ids` elenco per entrambi un `Pressed` tipo di evento e un `Entered` tipo, ma solo quando il `IsInContact` proprietà `true` per il `Entered` evento. L'ID viene rimosso dal `List` per un `Released` o `Exited` evento:

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

Il `AddToList` e `RemoveFromList` entrambi i metodi controllano se il `List` è stato modificato tra vuoto e non è vuoto e in tal caso, richiama il `StatusChanged` evento.

I vari `WhiteKey` e `BlackKey` gli elementi sono disposti della pagina [file XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/SilentKeyboardPage.xaml), che ha un aspetto migliore quando il telefono viene mantenuto in modalità orizzontale:

[![](touch-tracking-images/silentkeyboard-small.png "Schermata triplo della pagina tastiera")](touch-tracking-images/silentkeyboard-large.png#lightbox "tripla schermata della pagina tastiera invisibile all'utente")

Se si durante lo sweep dito tra le chiavi, verrà visualizzato per le piccole modifiche a colori che gli eventi tocco vengono trasferiti da una chiave a un altro.

## <a name="summary"></a>Riepilogo

In questo articolo ha illustrato come richiamare gli eventi in un effetto e come scrivere e usare un effetto che implementa l'elaborazione di basso livello Multi-touch.


## <a name="related-links"></a>Collegamenti correlati

- [Multi-Touch dito Tracking in iOS](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Multi-Touch dito Tracking in Android](~/android/app-fundamentals/touch/touch-tracking.md)
- [Toccare l'effetto di rilevamento (esempio)](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)
