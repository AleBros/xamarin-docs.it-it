---
title: Riepilogo del capitolo 22. Animazione
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 22. Animazione'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 935be5bd6696600644463eb4ec26410b546f42a0
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771004"
---
# <a name="summary-of-chapter-22-animation"></a>Riepilogo del capitolo 22. Animazione

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

Si è visto che è possibile creare animazioni personalizzate usando il timer Novell. Forms o `Task.Delay`, ma in genere è più facile usare le funzionalità di animazione fornite da Novell. Forms. Tre classi implementano queste animazioni:

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions), l'approccio di alto livello
- [`Animation`](xref:Xamarin.Forms.Animation), più versatile ma più difficile
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions), l'approccio più versatile e di livello più basso

In genere, le animazioni riguardano le proprietà che sono supportate da proprietà associabili. Questo non è un requisito, ma queste sono le uniche proprietà in modo dinamico di reagire alle modifiche.

Non è disponibile alcuna interfaccia XAML per queste animazioni, ma è possibile integrare le animazioni in XAML usando le tecniche descritte nel [**capitolo 23. Trigger e comportamenti**](chapter23.md).

## <a name="exploring-basic-animations"></a>Esplorazione delle animazioni di base

Le funzioni di animazione di base sono metodi di estensione trovati nella classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) . Questi metodi si applicano a qualsiasi oggetto che deriva da `VisualElement`. Le animazioni più semplici hanno come destinazione le proprietà delle trasformazioni descritte in [`Chapter 21. Transforms`](chapter21.md).

[**AnimationTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) illustra come il gestore eventi `Clicked` per un `Button` può chiamare il metodo di estensione [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) per ruotare il pulsante in un cerchio.

Il metodo `RotateTo` modifica la proprietà `Rotation` del `Button` da 0 a 360 nel corso di un secondo trimestre (per impostazione predefinita). Se il `Button` viene nuovamente toccato, tuttavia, non esegue alcuna operazione perché la proprietà `Rotation` è già 360 gradi.

### <a name="setting-the-animation-duration"></a>Impostazione della durata di animazione

Il secondo argomento per `RotateTo` è una durata in millisecondi. Se è impostato su un valore elevato, toccando il `Button` durante un'animazione viene avviata una nuova animazione a partire dall'angolo corrente.

### <a name="relative-animations"></a>Animazioni relative

Il metodo `RelRotateTo` esegue una rotazione relativa aggiungendo un valore specificato al valore esistente. Questo metodo consente di toccare più volte il `Button` e ogni volta aumenta di 360 gradi la proprietà `Rotation`.

### <a name="awaiting-animations"></a>In attesa di animazioni

Tutti i metodi di animazione in `ViewExtensions` restituiscono `Task<bool>` oggetti. Ciò significa che è possibile definire una serie di animazioni sequenziali utilizzando `ContinueWith` o `await`. Il valore restituito `bool` completamento viene `false` se l'animazione è stata completata senza interruzioni o `true` se è stata annullata dal metodo [`CancelAnimation`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) , che annulla tutte le animazioni avviate dall'altro metodo in `ViewExtensions` impostate per lo stesso elemento.

### <a name="composite-animations"></a>Animazioni composite

È possibile combinare le animazioni di attese e non atteso per creare animazioni composite. Queste sono le animazioni in `ViewExtensions` destinate alle proprietà di trasformazione `TranslationX`, `TranslationY`e `Scale`:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))

Si noti che `TranslateTo` potenzialmente influiscono sulle proprietà `TranslationX` e `TranslationY`.

### <a name="taskwhenall-and-taskwhenany"></a>Task.WhenAny e Task. whenall

È anche possibile gestire animazioni simultanee usando [`Task.WhenAll`](xref:System.Threading.Tasks.Task.WhenAll*), che segnala quando sono state terminate più attività e [`Task.WhenAny`](xref:System.Threading.Tasks.Task.WhenAny*), che segnala il momento in cui è stata conclusa la prima delle diverse attività.

### <a name="rotation-and-anchors"></a>Rotazione e punti di ancoraggio

Quando si chiamano i metodi `ScaleTo`, `RelScaleTo`, `RotateTo`e `RelRotateTo`, è possibile impostare le proprietà [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) e [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) per indicare il centro della scala e della rotazione.

[**CircleButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) illustra questa tecnica eseguendo la rotazione di un `Button` intorno al centro della pagina.

### <a name="easing-functions"></a>Funzioni di interpolazione

In genere le animazioni sono lineare da un valore iniziale a un valore finale. Funzioni di interpolazione possono causare animazioni velocizzare o rallentare il corso. L'ultimo argomento facoltativo per i metodi di animazione è di tipo [`Easing`](xref:Xamarin.Forms.Easing), una classe che definisce 11 campi statici di sola lettura di tipo `Easing`:

- [`Linear`](xref:Xamarin.Forms.Easing.Linear), il valore predefinito
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn), [`SinOut`](xref:Xamarin.Forms.Easing.SinOut)e [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn), [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut)e [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) e [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) e [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

Il suffisso `In` indica che l'effetto si trova all'inizio dell'animazione, `Out` significa alla fine e `InOut` significa che è all'inizio e alla fine dell'animazione.

Nell'esempio [**BounceButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) viene illustrato l'utilizzo di funzioni di interpolazione.

### <a name="your-own-easing-functions"></a>Funzioni di interpolazione

È anche possibile definire le proprie funzioni di interpolazione passando un [`Func<double, double>`](xref:System.Func`2) al [costruttore di`Easing`](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})). `Easing` definisce anche una conversione implicita da `Func<double, double>` a `Easing`. L'argomento alla funzione di interpolazione è sempre nell'intervallo compreso tra 0 e 1 come procede l'animazione in modo lineare dall'inizio alla fine. La funzione restituisce in *genere* un valore compreso tra 0 e 1, ma può essere brevemente negativo o maggiore di 1 (come nel caso delle funzioni `SpringIn` e `SpringOut`) o potrebbe interrompere le regole se si è certi di cosa si sta facendo.

Nell'esempio [**UneasyScale**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) viene illustrata una funzione di interpolazione personalizzata e [**CustomCubicEase**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) ne illustra un'altra.

L'esempio [**SwingButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) illustra anche una funzione di interpolazione personalizzata e una tecnica per modificare le proprietà `AnchorX` e `AnchorY` all'interno di una sequenza di animazioni di rotazione.

La libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) include una classe [`JiggleButton`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) che usa una funzione di interpolazione personalizzata per jiggle un pulsante quando viene selezionato. Viene illustrato l'esempio [**JiggleButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) .

### <a name="entrance-animations"></a>Animazioni di ingresso

Un tipo comune di animazione si verifica quando viene visualizzata prima di tutto una pagina. Un'animazione di questo tipo può essere avviata nell'override [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) della pagina. Per queste animazioni, è consigliabile configurare il codice XAML per la modalità di visualizzazione della pagina *dopo* l'animazione, quindi inizializzare e animare il layout dal codice.

L'esempio [**FadingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) usa il metodo di estensione [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) per dissolvere il contenuto della pagina.

L'esempio [**SlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) usa il metodo di estensione [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) per scorrere il contenuto della pagina dai lati.

L'esempio [**SwingingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) usa il metodo di estensione [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) per animare la proprietà `RotationY`. È disponibile anche un metodo [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) .

### <a name="forever-animations"></a>Animazioni per sempre

Da altra parte, "forever" animazioni eseguire fino a quando il programma viene terminato. In genere questi sono destinati a scopo dimostrativo.

Nell'esempio [**FadingTextAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) viene utilizzata [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) animazione per dissolvere due parti di testo in entrata e in uscita.

[**PalindromeAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) Visualizza un palindromo e quindi ruota in sequenza le singole lettere di 180 gradi, in modo che siano tutti capovolti. Quindi l'intera stringa viene capovolto 180 gradi per leggere lo stesso come la stringa originale.

L'esempio [**CopterAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) ruota un semplice elicottero `BoxView` mentre lo ruota attorno al centro dello schermo.

[**RotatingSpokes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) ruota `BoxView` spoke intorno al centro dello schermo e quindi ruota ogni spoke per creare modelli interessanti:

[![Schermata tripla della rotazione dei spoke](images/ch22fg21-small.png "Rotazione di spoke")](images/ch22fg21-large.png#lightbox "Rotazione di spoke")

Tuttavia, l'aumento progressivo della proprietà `Rotation` di un elemento potrebbe non funzionare a lungo termine, come illustrato nell'esempio [**RotationBreakdown**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown) .

Nell'esempio [**SpinningImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) vengono utilizzati [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))e [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) per far sembrare che una bitmap ruoti nello spazio 3D.

### <a name="animating-the-bounds-property"></a>Animazione della proprietà limiti

L'unico metodo di estensione in `ViewExtensions` non ancora dimostrato è [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), che aggiunge un'animazione alla proprietà [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) di sola lettura chiamando il metodo [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) . Questo metodo viene in genere chiamato da `Layout` derivati, come verrà illustrato nel [**capitolo 26. CustomLayouts**](chapter26.md).

Il metodo di `LayoutTo` deve essere limitato a scopi speciali. Il programma [**BouncingBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) lo usa per comprimere ed espandere un `BoxView` mentre rimbalza sui lati di una pagina.

L'esempio [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) USA `LayoutTo` per spostare i riquadri in un'implementazione del rompicapo 15-16 classico che visualizza un'immagine con Scramble anziché i riquadri numerati:

[![Schermata tripla di Novell Xuzzle](images/ch22fg26-small.png "Gioco puzzle Xuzzle")](images/ch22fg26-large.png#lightbox "Gioco puzzle Xuzzle")

### <a name="your-own-awaitable-animations"></a>Animazioni awaitable

L'esempio [**TryAwaitableAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) crea un'animazione awaitable. La classe fondamentale che può restituire un oggetto `Task` dal metodo e segnale quando l'animazione viene completata è [`TaskCompletionSource`](xref:System.Threading.Tasks.TaskCompletionSource`1).

## <a name="deeper-into-animations"></a>Dettagli della gestione delle animazioni

Il sistema di animazione di xamarin. Forms può essere un po' di confusione. Oltre alla classe `Easing`, il sistema di animazione comprende le classi `ViewExtensions`, `Animation`e `AnimationExtension`.

### <a name="viewextensions-class"></a>Classe ViewExtensions

Si è già visto [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions). Definisce nove metodi che restituiscono `Task<bool>` e [`CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)). Sette della destinazione di nove metodi proprietà di trasformazione. Gli altri due sono [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), che ha come destinazione la proprietà [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) e [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), che chiama il metodo [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) .

### <a name="animation-class"></a>Classe di animazione

La classe [`Animation`](xref:Xamarin.Forms.AnimationExtensions) dispone di un [Costruttore](xref:Xamarin.Forms.Animation.%23ctor(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Action)) con cinque argomenti per definire i metodi di callback e finiti e i parametri dell'animazione.

È possibile aggiungere animazioni figlio con [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)), [`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)), [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))e l'overload di [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Double,System.Double)).

L'oggetto animation viene quindi avviato con una chiamata al metodo [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) .

### <a name="animationextensions-class"></a>Classe AnimationExtensions

La classe [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) contiene principalmente metodi di estensione. Sono disponibili diverse versioni di un metodo `Animate` e il metodo [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) generico è talmente versatile che è effettivamente l'unica funzione di animazione necessaria.

## <a name="working-with-the-animation-class"></a>Utilizzo della classe di animazione

L'esempio [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) illustra la classe [`Animation`](xref:Xamarin.Forms.Animation) con diverse animazioni.

### <a name="child-animations"></a>Animazioni figlio

L'esempio [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) illustra anche le animazioni figlio, che usano i metodi (molto simili) [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) e [`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)) .

### <a name="beyond-the-high-level-animation-methods"></a>Oltre ai metodi di animazione di alto livello

Nell'esempio [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) viene inoltre illustrato come eseguire animazioni che vanno oltre le proprietà di destinazione dei metodi `ViewExtensions`. In un esempio, una serie di periodi viene più lunga; in un altro esempio, viene animata una proprietà `BackgroundColor`.

### <a name="more-of-your-own-awaitable-methods"></a>Più i propri metodi awaitable

Il metodo [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) di `ViewExtensions` non funziona con la funzione [`Easing.SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) . Si arresta quando l'output di interpolazione Ottiene superiori a 1.

La libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene una classe [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) con [`TranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) e [`TranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) metodi di estensione che non presentano questo problema, oltre ai metodi [`CancelTranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) e [`CancelTranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) per l'annullamento di tali animazioni.

[**SpringSlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) illustra il metodo `TranslateXTo`.

La classe `MoreExtensions` contiene anche un [`TranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) metodo di estensione che combina la conversione X e Y e un metodo di [`CancelTranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) .

### <a name="implementing-a-bezier-animation"></a>Implementazione di un'animazione di Bezier

È anche possibile sviluppare un'animazione che sposta un elemento nel percorso di una spline di Bézier. La libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene una struttura [`BezierSpline`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) che incapsula una spline di Bezier e un'enumerazione [`BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) per controllare l'orientamento.

La classe [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) contiene un metodo di estensione [`BezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) e un metodo di [`CancelBezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) .

L'esempio [**BezierLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) illustra l'animazione di un elemento lungo un percorso Beizer.

## <a name="working-with-animationextensions"></a>Utilizzo di AnimationExtensions

Un tipo di animazione mancante dalla raccolta di standard è un'animazione di colore. Il problema è che non esiste un modo giusto per interpolare tra due valori `Color`. È possibile eseguire l'interpolazione i singoli valori RGB, ma solo come validi è interpolazione i valori HSL.

Per questo motivo, la classe [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene due metodi di animazione `Color`: [`RgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166) e [`HslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188). Sono disponibili anche due metodi di annullamento: [`CancelRgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) e [`CancelHslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206).

Entrambi i metodi usano [`ColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211), che esegue l'animazione chiamando il metodo di [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) generico esteso in [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions).

Nell'esempio [**ColorAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) viene illustrato l'utilizzo di questi due tipi di animazioni colori.

## <a name="structuring-your-animations"></a>Strutturazione di animazioni

È talvolta utile esprimere le animazioni in XAML e usarle in combinazione con MVVM. Questo passaggio è trattato nel capitolo 23 del prossimo capitolo [ **. Trigger e comportamenti**](chapter23.md).

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 22 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Capitolo 22 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animazione](~/xamarin-forms/user-interface/animation/index.md)
