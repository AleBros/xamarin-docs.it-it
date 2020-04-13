---
title: Richiamo di eventi da effetti
description: Un effetto può definire e richiamare un evento, segnalando le modifiche nella visualizzazione nativa sottostante. Questo articolo illustra come implementare il rilevamento del tocco nel multitouch di basso livello e come generare eventi che segnalino attività tocco.
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2018
ms.openlocfilehash: cf5a97bca7c827101db951a440863839539c7e48
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725260"
---
# <a name="invoking-events-from-effects"></a>Richiamo di eventi da effetti

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)

_Un effetto può definire e richiamare un evento, segnalando le modifiche nella visualizzazione nativa sottostante. Questo articolo illustra come implementare il rilevamento delle dita multitocco di basso livello e come generare eventi che segnalano l'attività di tocco._

L'effetto descritto in questo articolo consente l'accesso agli eventi di tocco di basso livello. Questi eventi di basso livello non sono disponibili tramite le classi `GestureRecognizer` esistenti, ma sono fondamentali per alcuni tipi di applicazioni. Un'applicazione di disegno con le dita, ad esempio, deve tenere traccia di ognuna delle dita man mano che queste si spostano sullo schermo. Una tastiera musicale deve rilevare il tocco e il rilascio dei singoli tasti, nonché lo scorrimento di un dito da un tasto all'altro in un glissando.

Gli effetti sono ideali per il rilevamento del tocco nel multitouch, perché possono essere associati a qualsiasi elemento Xamarin.Forms.

## <a name="platform-touch-events"></a>Eventi di tocco delle piattaforme

Le piattaforme iOS, Android e Universal Windows Platform includono tutte un'API di basso livello che consente alle applicazioni di rilevare l'attività tocco. Queste piattaforme fanno tutte distinzione tra tre tipi principali di eventi di tocco:

- *Pressed*, quando un dito tocca lo schermo
- *Moved*, quando un dito che tocca lo schermo si sposta
- *Released*, quando il dito si allontana dallo schermo

In un ambiente multitouch, più dita possono toccare lo schermo nello stesso momento. Le diverse piattaforme includono un numero di identificazione (ID) che le applicazioni possono usare per distinguere tra più dita.

In iOS, la classe `UIView` definisce tre metodi sottoponibili a override, `TouchesBegan`, `TouchesMoved` e `TouchesEnded`, corrispondenti ai tre eventi di base. L'articolo [Rilevamento del tocco nel multitouch](~/ios/app-fundamentals/touch/touch-tracking.md) descrive come usare questi metodi. Per usare questi metodi, tuttavia, un programma iOS non deve necessariamente eseguire l'override di una classe che deriva da `UIView`. Gli stessi tre metodi sono definiti anche nella classe `UIGestureRecognizer` di iOS ed è possibile collegare un'istanza di una classe che deriva da `UIGestureRecognizer` a qualsiasi oggetto `UIView`.

In Android, la classe `View` definisce un metodo sottoponibile a override, `OnTouchEvent`, per elaborare tutte le attività tocco. Il tipo dell'attività tocco viene definita dai membri di enumerazione `Down`, `PointerDown`, `Move`, `Up` e `PointerUp` come descritto nell'articolo [Rilevamento del tocco nel multitouch](~/android/app-fundamentals/touch/touch-tracking.md). La classe `View` di Android definisce anche l'evento `Touch`, che consente di associare un gestore dell'evento a qualsiasi oggetto `View`.

Nella piattaforma UWP (Universal Windows Platform) la classe `UIElement` definisce gli eventi `PointerPressed`, `PointerMoved` e `PointerReleased`. Questi sono descritti nell'articolo [Gestire l'input del puntatore](/windows/uwp/input-and-devices/handle-pointer-input/) in MSDN e nella documentazione dell'API per la classe [`UIElement`](/uwp/api/windows.ui.xaml.uielement/).

L'API `Pointer` nella piattaforma UWP ha lo scopo di unificare mouse, tocco e input penna. Per questo motivo, l'evento `PointerMoved` viene richiamato quando il mouse viene spostato su un elemento, anche se non si preme alcun pulsante del mouse. L'oggetto `PointerRoutedEventArgs` che accompagna questi eventi ha una proprietà denominata `Pointer` che a sua volta ha una proprietà denominata `IsInContact` che indica se un pulsante del mouse è premuto o se un dito è a contatto con lo schermo.

La piattaforma UWP definisce anche altri due eventi, `PointerEntered` e `PointerExited`, che indicano quando il mouse o un dito si sposta da un elemento a un altro. Si considerino, ad esempio, due elementi adiacenti, denominati A e B. Per entrambi gli elementi sono installati gestori per gli eventi del puntatore. Se un dito preme A, viene richiamato l'evento `PointerPressed`. Se il dito si sposta, A richiama eventi `PointerMoved`. Se il dito si sposta da A a B, A richiama un evento `PointerExited` e B richiama un evento `PointerEntered`. Se il dito viene quindi sollevato, B richiama un evento `PointerReleased`.

Le piattaforme iOS e Android sono diverse dalla piattaforma UWP, poiché la visualizzazione che riceve per prima la chiamata a `TouchesBegan` o a `OnTouchEvent` quando viene toccata da un dito continua a ricevere tutte le attività tocco, anche se il dito passa a visualizzazioni diverse. La piattaforma UWP può comportarsi in modo analogo se l'applicazione acquisisce il puntatore: nel gestore dell'evento `PointerEntered`, l'elemento chiama `CapturePointer` e quindi ottiene tutte le attività tocco dal dito corrispondente.

L'approccio della piattaforma UWP si rivela molto utile per alcuni tipi di applicazioni, ad esempio per le tastiere musicali. Ogni tasto può gestire gli eventi di tocco che lo interessano e rilevare quando un dito scorre da un tasto a un altro usando gli eventi `PointerEntered` e `PointerExited`.

Per questo motivo, l'effetto di rilevamento del tocco descritto in questo articolo implementa l'approccio della piattaforma UWP.

## <a name="the-touch-tracking-effect-api"></a>API dell'effetto di rilevamento del tocco

L'esempio [**Touch Tracking Effect Demos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) (Demo dell'effetto di rilevamento del tocco) contiene le classi e un'enumerazione che implementano il rilevamento del tocco di basso livello. Questi tipi appartengono allo spazio dei nomi `TouchTracking` e iniziano con la parola `Touch`. Il progetto di libreria .NET Standard **TouchTrackingEffectDemos** include l'enumerazione `TouchActionType` per il tipo di eventi di tocco:

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

Tutte le piattaforme includono anche un evento che indica che l'evento di tocco è stato annullato.

La classe `TouchEffect` nella libreria .NET Standard, che deriva dalla classe `RoutingEffect`, definisce un evento denominato `TouchAction` e un metodo denominato `OnTouchAction` che richiama l'evento `TouchAction`:

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

Si noti anche la proprietà `Capture`. Per acquisire eventi di tocco, un'applicazione deve impostare questa proprietà su `true` prima di un evento `Pressed`. In caso contrario, gli eventi di tocco si comportano come quelli della piattaforma UWP.

La classe `TouchActionEventArgs` nella libreria .NET Standard contiene tutte le informazioni che accompagnano ogni evento:

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

Un'applicazione può usare la proprietà `Id` per rilevare le singole dita. Si noti la proprietà `IsInContact`. Questa proprietà è sempre `true` per eventi `Pressed` e `false` per eventi `Released`. È poi sempre `true` per eventi `Moved` in iOS e Android. La proprietà `IsInContact` può corrispondere a `false` per eventi `Moved` nella piattaforma UWP se il programma viene eseguito sul desktop e il puntatore del mouse viene spostato senza un pulsante premuto.

Per usare la classe `TouchEffect` nelle proprie applicazioni, è possibile includere il file nel progetto di libreria .NET Standard della soluzione e aggiungere un'istanza alla raccolta `Effects` di un qualsiasi elemento Xamarin.Forms. Associare un gestore all'evento `TouchAction` per ottenere gli eventi di tocco.

Per usare `TouchEffect` nella propria applicazione, sono anche necessarie le implementazioni della piattaforma incluse nella soluzione **TouchTrackingEffectDemos**.

## <a name="the-touch-tracking-effect-implementations"></a>Implementazioni dell'effetto di rilevamento del tocco

Le implementazioni iOS, Android e UWP di `TouchEffect` sono descritte di seguito, a partire dall'implementazione più semplice (UWP) fino all'implementazione iOS, strutturalmente più complessa rispetto alle altre.

### <a name="the-uwp-implementation"></a>Implementazione nella piattaforma UWP

L'implementazione di `TouchEffect` nella piattaforma UWP è la più semplice. Come di consueto, la classe deriva da `PlatformEffect` e include due attributi di assembly:

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

L'override `OnAttached` salva alcune informazioni come campi e associa gestori a tutti gli eventi del puntatore:

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

Il gestore `OnPointerPressed` richiama l'evento dell'effetto chiamando il campo `onTouchAction` nel metodo `CommonHandler`:

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

`OnPointerPressed` controlla anche il valore della proprietà `Capture` nella classe dell'effetto nella libreria .NET Standard e chiama `CapturePointer` se corrisponde a `true`.

 Gli altri gestori eventi della piattaforma UWP sono ancora più semplici:

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

### <a name="the-android-implementation"></a>Implementazione Android

Le implementazioni Android e iOS sono necessariamente più complesse, poiché devono implementare gli eventi `Exited` ed `Entered` quando un dito si sposta da un elemento a un altro. Entrambe le implementazioni sono strutturate in modo simile.

La classe `TouchEffect` Android installa un gestore per l'evento `Touch`:

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

`viewDictionary` ottiene una nuova voce ogni volta che viene chiamato l'override `OnAttached`:

```csharp
viewDictionary.Add(view, this);
```

La voce viene rimossa dal dizionario in `OnDetached`. Tutte le istanze di `TouchEffect` sono associate a una visualizzazione specifica a cui è associato l'effetto. Il dizionario statico consente a qualsiasi istanza di `TouchEffect` di enumerare tutte le altre visualizzazioni e le istanze di `TouchEffect` corrispondenti. Questa operazione è necessaria per consentire il trasferimento degli eventi da una visualizzazione a un'altra.

Android assegna agli eventi di tocco un codice ID che consente a un'applicazione di rilevare le singole dita. `idToEffectDictionary` associa questo codice ID a un'istanza di `TouchEffect`. Un elemento viene aggiunto a questo dizionario quando il gestore di `Touch` viene chiamato per la pressione di un dito:

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

L'elemento viene rimosso da `idToEffectDictionary` quando il dito si allontana dallo schermo. Il metodo `FireEvent` accumula semplicemente tutte le informazioni necessarie per chiamare il metodo `OnTouchAction`:

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

Tutti gli altri tipi di tocco vengono elaborati in due modi diversi: se la proprietà `Capture` corrisponde a `true`, l'evento di tocco è una conversione abbastanza semplice in informazioni `TouchEffect`. L'operazione diventa più complessa se `Capture` corrisponde a `false`, perché può essere necessario spostare gli eventi di tocco da una visualizzazione a un'altra. Questo è compito del metodo `CheckForBoundaryHop`, che viene chiamato durante gli eventi di spostamento. Questo metodo usa entrambi i dizionari statici, eseguendo l'enumerazione attraverso `viewDictionary` per determinare la visualizzazione che il dito sta toccando e usa `idToEffectDictionary` per archiviare l'istanza di `TouchEffect` corrente (e, di conseguenza, la visualizzazione corrente) associata a un ID specifico:

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

Se è stata apportata una modifica in `idToEffectDictionary`, il metodo può chiamare `FireEvent` per `Exited` e `Entered` per il trasferimento da una visualizzazione a un'altra. Il dito, tuttavia, può essersi spostato in un'area occupata da una visualizzazione senza un `TouchEffect` associato, oppure, viceversa, può essersi spostato da un'area di questo tipo a una visualizzazione con l'effetto associato.

Si noti il blocco `try` e `catch` quando si accede alla visualizzazione. In una pagina a cui si accede e dalla quale quindi si passa nuovamente alla home page, il metodo `OnDetached` non viene chiamato e gli elementi rimangono in `viewDictionary` ma Android li considera eliminati.

### <a name="the-ios-implementation"></a>Implementazione iOS

L'implementazione iOS è simile all'implementazione Android, con la differenza che la classe `TouchEffect` di iOS deve creare un'istanza di un elemento derivato di `UIGestureRecognizer`. Si tratta della classe `TouchRecognizer` nel progetto iOS. Questa classe gestisce due dizionari statici che archiviano istanze di `TouchRecognizer`:

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

Buona parte della struttura della classe `TouchRecognizer` è simile alla classe Android `TouchEffect`.

> [!IMPORTANT]
> Per molte delle visualizzazioni in `UIKit` il tocco non è abilitato per impostazione predefinita. Il tocco può essere abilitato aggiungendo `view.UserInteractionEnabled = true;` all'override `OnAttached` nella classe `TouchEffect` nel progetto iOS. Questa operazione dovrebbe avvenire dopo aver ottenuto l'elemento `UIView` corrispondente all'elemento a cui è associato l'effetto.

## <a name="putting-the-touch-effect-to-work"></a>Mettere in azione l'effetto di tocco

Il programma [**TouchTrackingEffectDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) contiene cinque pagine di test dell'effetto di rilevamento del tocco per attività comuni.

La pagina **BoxView Dragging** consente di aggiungere elementi `BoxView` a un `AbsoluteLayout` e quindi di trascinare tali elementi all'interno dello schermo. Il [file XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BoxViewDraggingPage.xaml) crea un'istanza di due visualizzazioni `Button` per l'aggiunta di elementi `BoxView` ad `AbsoluteLayout` e la cancellazione di `AbsoluteLayout`.

Il metodo nel [file code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BoxViewDraggingPage.xaml.cs) che aggiunge un nuovo elemento `BoxView` ad `AbsoluteLayout` aggiunge anche un oggetto `TouchEffect` a `BoxView` e associa un gestore dell'evento all'effetto:

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

Il gestore dell'evento `TouchAction` elabora tutti gli eventi di tocco per tutti gli elementi `BoxView`, ma è necessario prestare attenzione: non può consentire due dita su un singolo elemento `BoxView` perché il programma implementa solo il trascinamento e due dita interferirebbero tra loro. Per questo motivo, la pagina definisce una classe incorporata per ogni dito in corso di rilevamento:

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

`dragDictionary` contiene una voce per ogni elemento `BoxView` in corso di trascinamento.

L'azione di tocco `Pressed` aggiunge un elemento a questo dizionario e l'azione `Released` lo rimuove. La logica di `Pressed` deve controllare se nel dizionario è già presente un elemento per tale `BoxView`. In caso affermativo, l'elemento `BoxView` è già in corso di trascinamento e il nuovo evento corrisponde a un secondo dito sullo stesso elemento `BoxView`. Per le azioni `Moved` e `Released`, il gestore dell'evento deve controllare se il dizionario ha una voce per tale `BoxView`. Deve anche controllare che la proprietà `Id` del tocco per l'elemento `BoxView` trascinato corrisponda a quella della voce del dizionario:

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

La logica di `Pressed` imposta la proprietà `Capture` dell'oggetto `TouchEffect` su `true`. Questa operazione ha l'effetto di distribuire di tutti gli eventi successivi per tale dito allo stesso gestore dell'evento.

La logica di `Moved` sposta `BoxView` modificando la proprietà associata `LayoutBounds`. La proprietà `Location` degli argomenti dell'evento è sempre relativa all'elemento `BoxView` trascinato. Se l'elemento `BoxView` viene trascinato a una velocità costante, le proprietà `Location` degli eventi consecutivi è approssimativamente la stessa. Se, ad esempio, un dito preme `BoxView` al centro, l'azione `Pressed` archivia un valore della proprietà `PressPoint` corrispondente a (50, 50), che rimane invariato per gli eventi successivi. Se l'elemento `BoxView` viene trascinato in diagonale a una velocità costante, le proprietà `Location` successive durante l'azione `Moved` possono avere valori pari a (55, 55). In questo caso, la logica di `Moved` aggiunge 5 alla posizione orizzontale e verticale dell'elemento `BoxView`. Ciò sposta l'elemento `BoxView` in modo che il centro di questo si trovi ancora sotto il dito.

È possibile spostare più elementi `BoxView` contemporaneamente usando dita diverse.

[![](touch-tracking-images/boxviewdragging-small.png "Triple screenshot of the BoxView Dragging page")](touch-tracking-images/boxviewdragging-large.png#lightbox "Triple screenshot of the BoxView Dragging page")

### <a name="subclassing-the-view"></a>Creazione di una sottoclasse della visualizzazione

È spesso più semplice per un elemento Xamarin.Forms gestire i propri eventi di tocco. Il funzionamento della pagina **Draggable BoxView Dragging** è analogo a quello della pagina **BoxView Dragging**, ma gli elementi trascinati dell'utente sono istanze di una classe [`DraggableBoxView`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/DraggableBoxView.cs) che deriva da `BoxView`:

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

Il costruttore crea e associa `TouchEffect` e imposta la proprietà `Capture` quando viene creata la prima istanza di tale oggetto. Non è necessario alcun dizionario, perché la classe stessa archivia i valori `isBeingDragged`, `pressPoint` e `touchId` associati a ogni dito. La gestione di `Moved` modifica le proprietà `TranslationX` e `TranslationY` in modo che la logica funzioni anche se l'elemento padre di `DraggableBoxView` non è un `AbsoluteLayout`.

### <a name="integrating-with-skiasharp"></a>Integrazione con SkiaSharp

Le due prossime dimostrazioni richiedono elementi grafici e a tale scopo usano SkiaSharp. Prima di esaminare questi esempi, è necessario informarsi sull'[uso di SkiaSharp in Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md). I primi due articoli ("Nozioni di base sul disegno con SkiaSharp" e "Linee e tracciati in SkiaSharp") trattano tutto ciò che occorre qui.

La pagina **Ellipse Drawing** consente di disegnare un'ellisse scorrendo rapidamente il dito sullo schermo. A seconda di come si sposta il dito, è possibile disegnare l'ellisse dall'angolo in alto a sinistra all'angolo in basso a destra o da qualsiasi altro angolo all'angolo opposto. L'ellisse viene disegnata con colore e opacità casuali.

[![](touch-tracking-images/ellipsedrawing-small.png "Triple screenshot of the Ellipse Drawing page")](touch-tracking-images/ellipsedrawing-large.png#lightbox "Triple screenshot of the Ellipse Drawing page")

Se quindi si tocca una delle ellissi, è possibile trascinarla in un'altra posizione. Ciò richiede una tecnica nota come "hit testing" che implica la ricerca dell'oggetto grafico in un punto specifico. Le ellissi di SkiaSharp non sono elementi Xamarin.Forms e quindi non possono eseguire la propria elaborazione di `TouchEffect`. È necessario applicare `TouchEffect` all'intero oggetto `SKCanvasView`.

Il file [EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/EllipseDrawingPage.xaml) crea un'istanza di `SKCanvasView` in un elemento `Grid` a cella singola. L'oggetto `TouchEffect` è associato a tale elemento `Grid`:

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

In Android e nella piattaforma UWP, l'oggetto `TouchEffect` può essere associato direttamente a `SKCanvasView`, ma in iOS questo non funziona. Si noti che la proprietà `Capture` è impostata su `true`.

Ogni ellisse di cui SkiaSharp esegue il rendering è rappresentata da un oggetto di tipo `EllipseDrawingFigure`:

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

Le proprietà `StartPoint` e `EndPoint` vengono usate quando il programma sta elaborando l'input tocco. La proprietà `Rectangle` viene usata per disegnare l'ellisse. La proprietà `LastFingerLocation` entra in gioco quando l'ellisse viene trascinata e il metodo `IsInEllipse` facilita l'hit testing. Il metodo restituisce `true` se il punto è all'interno dell'ellisse.

Il [file code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/EllipseDrawingPage.xaml.cs) gestisce tre raccolte:

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

Il dizionario `draggingFigure` contiene un subset della raccolta `completedFigures`. Il gestore dell'evento `PaintSurface` di SkiaSharp esegue semplicemente il rendering degli oggetti nelle raccolte `completedFigures` e `inProgressFigures`:

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

La parte più difficile dell'elaborazione del tocco è la gestione di `Pressed`. In questa fase viene eseguito l'hit testing, ma se il codice rileva un'ellisse sotto il dito dell'utente, tale ellisse può essere trascinata solo se in quel momento non viene trascinata da un altro dito. Se sotto il dito dell'utente non è presente alcuna ellisse, il codice inizia il processo di disegno di una nuova ellisse:

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

L'altro esempio di SkiaSharp è la pagina **Finger Paint**. È possibile selezionare il colore e lo spessore del tratto da due visualizzazioni `Picker` e quindi disegnare con uno o più dita:

[![](touch-tracking-images/fingerpaint-small.png "Triple screenshot of the Finger Paint page")](touch-tracking-images/fingerpaint-large.png#lightbox "Triple screenshot of the Finger Paint page")

Questo esempio richiede anche una classe separata per rappresentare ciascuna riga disegnata sullo schermo:

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

Per eseguire il rendering di ogni riga, viene usato un oggetto `SKPath`. Il file [FingerPaint.xaml.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/FingerPaintPage.xaml.cs) gestisce due raccolte di questi oggetti, uno per le polilinee in corso di tracciamento e l'altro per le polilinee completate:

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

L'elaborazione di `Pressed` crea un nuovo elemento `FingerPaintPolyline`, chiama `MoveTo` per l'oggetto percorso per memorizzare il punto iniziale e aggiunge tale oggetto al dizionario `inProgressPolylines`. L'elaborazione di `Moved` chiama `LineTo` per l'oggetto percorso con la nuova posizione del dito e l'elaborazione di `Released` trasferisce la polilinea completata da `inProgressPolylines` a `completedPolylines`. Ancora una volta, il codice di disegno di SkiaSharp effettivo è relativamente semplice:

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

### <a name="tracking-view-to-view-touch"></a>Rilevamento del tocco da visualizzazione a visualizzazione

Tutti gli esempi precedenti hanno impostato la proprietà `Capture` di `TouchEffect` su `true` quando `TouchEffect` è stato creato o quando l'evento `Pressed` si è verificato. Ciò garantisce che lo stesso elemento riceva tutti gli eventi associati al dito che ha premuto la visualizzazione per primo. L'esempio finale *non* imposta `Capture` su `true`. Ciò determina un comportamento diverso quando si sposta un dito in contatto con lo schermo da un elemento a un altro. L'elemento da cui il dito si sposta riceve un evento con la proprietà `Type` impostata su `TouchActionType.Exited` e il secondo elemento riceve un evento con l'impostazione `Type` corrispondente a `TouchActionType.Entered`.

Questo tipo di elaborazione del tocco è molto utile per una tastiera musicale. Un tasto deve essere in grado di rilevare quando viene premuto, ma anche quando un dito scorre da un tasto a un altro.

La pagina **Silent Keyboard** definisce piccole classi, [`WhiteKey`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/WhiteKey.cs) e [`BlackKey`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BlackKey.cs), che derivano da [`Key`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/Key.cs), che deriva da `BoxView`.

La classe `Key` è pronta per l'uso in un vero programma di musica. Definisce le proprietà pubbliche `IsPressed` e `KeyNumber`, che devono essere impostate su un codice tasto definito dallo standard MIDI. La classe `Key` definisce anche l'evento `StatusChanged`, che viene richiamato quando la proprietà `IsPressed` cambia.

Su ogni tasto sono consentite più dita. Per questo motivo, la classe `Key` gestisce un elemento `List` dei numeri ID tocco di tutte le dita a contatto con il tasto in quel momento:

```csharp
List<long> ids = new List<long>();
```

Il gestore dell'evento `TouchAction` aggiunge un ID all'elenco `ids` sia per un tipo di evento `Pressed` che per un tipo `Entered`, ma solo quando la proprietà `IsInContact` è `true` per l'evento `Entered`. L'ID viene rimosso dall'elemento `List` per un evento `Released` o `Exited`:

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

I metodi `AddToList` e `RemoveFromList` controllano entrambi se l'elemento `List` è passato da vuoto a non vuoto. In caso affermativo, richiama l'evento `StatusChanged`.

I vari elementi `WhiteKey` e `BlackKey` sono disposti nel [file XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/SilentKeyboardPage.xaml) della pagina, che offre una visualizzazione ottimale quando il telefono viene orientato in posizione orizzontale:

[![](touch-tracking-images/silentkeyboard-small.png "Triple screenshot of the Silent Keyboard page")](touch-tracking-images/silentkeyboard-large.png#lightbox "Triple screenshot of the Silent Keyboard page")

Se si scorre rapidamente il dito sui tasti, si può vedere dal lieve cambiamento di colore che gli eventi di tocco vengono trasferiti da un tasto all'altro.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come chiamare eventi in un effetto e come scrivere e usare un effetto che implementi l'elaborazione multitouch di basso livello.

## <a name="related-links"></a>Collegamenti correlati

- [Rilevamento del tocco nel multitouch in iOS](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Rilevamento del tocco nel multitouch in Android](~/android/app-fundamentals/touch/touch-tracking.md)
- [Effetto di rilevamento del tocco (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
