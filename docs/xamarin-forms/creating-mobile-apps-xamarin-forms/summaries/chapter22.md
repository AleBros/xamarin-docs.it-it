---
title: "Riepilogo del capitolo 22. Animazione "Descrizione:" creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 22. Animazione "ms. prod: Novell MS. Technology: Novell-Forms ms. AssetID: 47C2B9AB-E688-4412-8AF5-9F633B3DA695 Author: davidbritch ms. Author: dabritch ms. Date: 11/07/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-22-animation"></a>Riepilogo del capitolo 22. Animazione

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

Si è visto che è possibile creare animazioni personalizzate usando il Xamarin.Forms timer o `Task.Delay` , ma in genere è più facile usare le funzionalità di animazione fornite da Xamarin.Forms . Tre classi implementano queste animazioni:

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions), l'approccio di alto livello
- [`Animation`](xref:Xamarin.Forms.Animation), più versatile ma più difficile
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions), l'approccio più versatile e di livello più basso

In genere, le animazioni di destinazione sono proprietà supportate da proprietà associabili. Questo non è un requisito, ma queste sono le uniche proprietà che reagiscono dinamicamente alle modifiche.

Non è disponibile alcuna interfaccia XAML per queste animazioni, ma è possibile integrare le animazioni in XAML usando le tecniche descritte nel [**capitolo 23. Trigger e comportamenti**](chapter23.md).

## <a name="exploring-basic-animations"></a>Esplorazione delle animazioni di base

Le funzioni di animazione di base sono metodi di estensione trovati nella [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe. Questi metodi si applicano a qualsiasi oggetto che deriva da `VisualElement` . Le animazioni più semplici hanno come destinazione le proprietà delle trasformazioni descritte in [`Chapter 21. Transforms`](chapter21.md) .

[**AnimationTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) illustra come il `Clicked` gestore eventi per un oggetto `Button` può chiamare [ `RotateTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) il metodo di estensione per ruotare il pulsante in un cerchio.

Il `RotateTo` metodo modifica la `Rotation` proprietà di `Button` da 0 a 360 nel corso di un secondo trimestre (per impostazione predefinita). Se `Button` viene ritoccata, tuttavia, non esegue alcuna operazione perché la `Rotation` proprietà è già 360 gradi.

### <a name="setting-the-animation-duration"></a>Impostazione della durata dell'animazione

Il secondo argomento di `RotateTo` è una durata in millisecondi. Se impostato su un valore elevato, toccando l'oggetto `Button` durante un'animazione viene avviata una nuova animazione a partire dall'angolo corrente.

### <a name="relative-animations"></a>Animazioni relative

Il `RelRotateTo` metodo esegue una rotazione relativa aggiungendo un valore specificato al valore esistente. Questo metodo consente `Button` di toccare più volte l'oggetto e ogni volta aumenta la `Rotation` proprietà di 360 gradi.

### <a name="awaiting-animations"></a>Animazioni in attesa

Tutti i metodi di animazione `ViewExtensions` negli `Task<bool>` oggetti restituiti. Ciò significa che è possibile definire una serie di animazioni sequenziali usando `ContinueWith` o `await` . Il `bool` valore restituito di completamento è `false` se l'animazione è terminata senza interruzioni o `true` se è stata annullata da [ `CancelAnimation` ] (xrif: Xamarin.Forms . ViewExtensions. CancelAnimations ( Xamarin.Forms . Metodo visualElement), che annulla tutte le animazioni avviate dall'altro metodo in `ViewExtensions` impostate per lo stesso elemento.

### <a name="composite-animations"></a>Animazioni composite

È possibile combinare animazioni attese e non attese per creare animazioni composite. Si tratta delle animazioni in `ViewExtensions` destinate alle `TranslationX` `TranslationY` proprietà di trasformazione, e `Scale` :

- [ `TranslateTo` ] (xrif: Xamarin.Forms . ViewExtensions. TranslateTo ( Xamarin.Forms . VisualElement, System. Double, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)
- [ `ScaleTo` ] (xrif: Xamarin.Forms . ViewExtensions. ScaleTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)
- [ `RelScaleTo` ] (xrif: Xamarin.Forms . ViewExtensions. RelScaleTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)

Si noti che `TranslateTo` potenzialmente influiscono `TranslationX` sulle `TranslationY` proprietà e.

### <a name="taskwhenall-and-taskwhenany"></a>Task. WhenAll e Task. WhenAny

È anche possibile gestire animazioni simultanee mediante [`Task.WhenAll`](xref:System.Threading.Tasks.Task.WhenAll*) , che segnala il momento in cui sono state terminate più attività e [`Task.WhenAny`](xref:System.Threading.Tasks.Task.WhenAny*) , che segnala il momento in cui è stata conclusa la prima delle diverse attività.

### <a name="rotation-and-anchors"></a>Rotazione e ancoraggi

Quando si chiamano `ScaleTo` i `RelScaleTo` metodi,, `RotateTo` e `RelRotateTo` , è possibile impostare le [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) proprietà e per indicare il centro della scala e della rotazione.

[**CircleButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) illustra questa tecnica ruotando un oggetto `Button` intorno al centro della pagina.

### <a name="easing-functions"></a>Funzioni di interpolazione

In genere le animazioni sono lineari da un valore iniziale a un valore finale. Con le funzioni di interpolazione è possibile velocizzare o rallentare il corso delle animazioni. L'ultimo argomento facoltativo per i metodi di animazione è di tipo [`Easing`](xref:Xamarin.Forms.Easing) , una classe che definisce 11 campi statici di sola lettura di tipo `Easing` :

- [`Linear`](xref:Xamarin.Forms.Easing.Linear), il valore predefinito
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn), [`SinOut`](xref:Xamarin.Forms.Easing.SinOut) e[`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn), [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut) e[`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn)e[`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn)e[`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

Il `In` suffisso indica che l'effetto è all'inizio dell'animazione, `Out` significa alla fine e `InOut` indica che si trova all'inizio e alla fine dell'animazione.

Nell'esempio [**BounceButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) viene illustrato l'utilizzo di funzioni di interpolazione.

### <a name="your-own-easing-functions"></a>Funzioni di interpolazione personalizzate

È anche possibile definire le proprie funzioni di interpolazione passando un oggetto [`Func<double, double>`](xref:System.Func`2) al [ `Easing` Costruttore](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})). `Easing`definisce inoltre una conversione implicita da `Func<double, double>` a `Easing` . L'argomento della funzione di interpolazione è sempre compreso nell'intervallo tra 0 e 1 perché l'animazione procede in modo lineare dall'inizio alla fine. La funzione restituisce in *genere* un valore compreso tra 0 e 1, ma potrebbe essere brevemente negativo o maggiore di 1 (come nel caso delle `SpringIn` `SpringOut` funzioni e) oppure potrebbe interrompere le regole se si è certi di cosa si sta facendo.

Nell'esempio [**UneasyScale**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) viene illustrata una funzione di interpolazione personalizzata e [**CustomCubicEase**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) ne illustra un'altra.

L'esempio [**SwingButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) illustra anche una funzione di interpolazione personalizzata e una tecnica per modificare `AnchorX` le `AnchorY` proprietà e in una sequenza di animazioni di rotazione.

La libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) include una [`JiggleButton`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) classe che usa una funzione di interpolazione personalizzata per jiggle un pulsante quando viene selezionato. Viene illustrato l'esempio [**JiggleButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) .

### <a name="entrance-animations"></a>Animazioni di ingresso

Un tipo di animazione comune si verifica quando viene visualizzata una pagina per la prima volta. Un'animazione di questo tipo può essere avviata nell' [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) override della pagina. Per queste animazioni, è consigliabile configurare il codice XAML per la modalità di visualizzazione della pagina *dopo* l'animazione, quindi inizializzare e animare il layout dal codice.

L'esempio [**FadingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) USA [ `FadeTo` ] (xrif: Xamarin.Forms . ViewExtensions. FadeTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) il metodo di estensione per dissolvere il contenuto della pagina.

L'esempio [**SlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) USA [ `TranslateTo` ] (xrif: Xamarin.Forms . ViewExtensions. TranslateTo ( Xamarin.Forms . VisualElement, System. Double, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) il metodo di estensione per scorrere il contenuto della pagina dai lati.

L'esempio [**SwingingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) USA [ `RotateYTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) il metodo di estensione per aggiungere un'animazione alla `RotationY` Proprietà. [ `RotateXTo` ] (Xrif: Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . È anche disponibile il metodo di interpolazione).

### <a name="forever-animations"></a>Animazioni Forever

Al contrario, le animazioni "Forever" vengono eseguite fino a quando il programma non viene terminato. Questi sono in genere destinati a scopi dimostrativi.

Nell'esempio [**FadingTextAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) viene utilizzato [ `FadeTo` ] (xrif: Xamarin.Forms . ViewExtensions. FadeTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) per sfumare due parti di testo in entrata e in uscita.

[**PalindromeAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) Visualizza un palindromo e quindi ruota in sequenza le singole lettere di 180 gradi, in modo che siano tutti capovolti. Quindi, l'intera stringa viene capovolta 180 gradi per leggere lo stesso nome della stringa originale.

L'esempio [**CopterAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) ruota un elicottero semplice `BoxView` e lo ruota attorno al centro dello schermo.

[**RotatingSpokes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) ruota `BoxView` i raggi attorno al centro dello schermo e quindi ruota ogni spoke per creare modelli interessanti:

[![Schermata tripla della rotazione dei spoke](images/ch22fg21-small.png "Rotazione di spoke")](images/ch22fg21-large.png#lightbox "Rotazione di spoke")

Tuttavia, l'aumento progressivo della `Rotation` proprietà di un elemento potrebbe non funzionare a lungo termine, come illustrato nell'esempio [**RotationBreakdown**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown) .

Nell'esempio [**SpinningImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) viene utilizzato [ `RotateTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione), [ `RotateXTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione) e [ `RotateYTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) per far sembrare che una bitmap ruoti nello spazio 3D.

### <a name="animating-the-bounds-property"></a>Animazione della proprietà Bounds

L'unico metodo di estensione in `ViewExtensions` non ancora dimostrato è [ `LayoutTo` ] (xrif: Xamarin.Forms . ViewExtensions. LayoutTo ( Xamarin.Forms . Oggetti visivi, Xamarin.Forms . Rectangle, System. UInt32, Xamarin.Forms . Interpolazione), che consente di animare in modo efficace la proprietà di sola lettura chiamando [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) [ `Layout` ] (xrif: Xamarin.Forms . VisualElement. layout ( Xamarin.Forms . Rectangle)). Questo metodo viene in genere chiamato da `Layout` derivati, come verrà illustrato nel [**capitolo 26. CustomLayouts**](chapter26.md).

Il `LayoutTo` metodo deve essere limitato a scopi speciali. Il programma [**BouncingBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) lo usa per comprimere ed espandere un oggetto `BoxView` quando rimbalza sui lati di una pagina.

L'esempio [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) USA `LayoutTo` per spostare i riquadri in un'implementazione del rompicapo 15-16 classico che visualizza un'immagine scrambled anziché riquadri numerati:

[![Schermata tripla di Novell Xuzzle](images/ch22fg26-small.png "Gioco puzzle Xuzzle")](images/ch22fg26-large.png#lightbox "Gioco puzzle Xuzzle")

### <a name="your-own-awaitable-animations"></a>Animazioni in attesa

L'esempio [**TryAwaitableAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) crea un'animazione awaitable. La classe fondamentale che può restituire un `Task` oggetto dal metodo e segnale quando l'animazione è completata è [`TaskCompletionSource`](xref:System.Threading.Tasks.TaskCompletionSource`1) .

## <a name="deeper-into-animations"></a>Approfondimento sulle animazioni

Il Xamarin.Forms sistema di animazione può essere un po' confuso. Oltre alla `Easing` classe, il sistema di animazione comprende le `ViewExtensions` classi, `Animation` e `AnimationExtension` .

### <a name="viewextensions-class"></a>Classe ViewExtensions

Si è già visto [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) . Definisce nove metodi che restituiscono `Task<bool>` e [ `CancelAnimations` ] (xrif: Xamarin.Forms . ViewExtensions. CancelAnimations ( Xamarin.Forms . Oggetti visivi). Sette dei nove metodi di destinazione delle proprietà di trasformazione. Gli altri due sono [ `FadeTo` ] (xrif: Xamarin.Forms . ViewExtensions. FadeTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione), che ha come destinazione la [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) proprietà e [ `LayoutTo` ] (xrif: Xamarin.Forms . ViewExtensions. LayoutTo ( Xamarin.Forms . Oggetti visivi, Xamarin.Forms . Rectangle, System. UInt32, Xamarin.Forms . Interpolazione), che chiama [ `Layout` ] (xrif: Xamarin.Forms . VisualElement. layout ( Xamarin.Forms . Rectangle)).

### <a name="animation-class"></a>Classe Animation

La [`Animation`](xref:Xamarin.Forms.AnimationExtensions) classe dispone di un [Costruttore] (xrif: Xamarin.Forms . Animazione .% 23ctor (System. Action {System. Double}, System. Double, System. Double, Xamarin.Forms . Interpolazione, System. Action) con cinque argomenti per definire i metodi di callback e finiti e i parametri dell'animazione.

È possibile aggiungere animazioni figlio con [ `Add` ] (xrif: Xamarin.Forms . Animazione. Add (System. Double, System. Double, Xamarin.Forms . Animazione)), [ `Insert` ] (xrif: Xamarin.Forms . Animation. Insert (System. Double, System. Double, Xamarin.Forms . Animazione)), [ `WithConcurrent` ] (xrif: Xamarin.Forms . Animation. WithConcurrent ( Xamarin.Forms . Animazione, System. Double, System. Double) e overload di [ `WithConcurrent` ] (xrif: Xamarin.Forms . Animation. WithConcurrent (System. Action {System. Double}, System. Double, System. Double, Xamarin.Forms . Interpolazione, System. Double, System. Double).

L'oggetto animation viene quindi avviato con una chiamata a [ `Commit` ] (xrif: Xamarin.Forms . Animation. Commit ( Xamarin.Forms . IAnimatable, System. String, System. UInt32, System. UInt32, Xamarin.Forms . Metodo di interpolazione, System. Action {System. Double, System. Boolean}, System. Func {System. Boolean}).

### <a name="animationextensions-class"></a>Classe AnimationExtensions

La [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) classe contiene principalmente i metodi di estensione. Esistono diverse versioni di un `Animate` metodo e il metodo generico [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) è talmente versatile che è effettivamente l'unica funzione di animazione necessaria.

## <a name="working-with-the-animation-class"></a>Uso della classe Animation

L'esempio [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) illustra la [`Animation`](xref:Xamarin.Forms.Animation) classe con diverse animazioni.

### <a name="child-animations"></a>Animazioni figlio

L'esempio [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) illustra anche le animazioni figlio, che usano (molto simili) [ `Add` ] (xrif: Xamarin.Forms . Animazione. Add (System. Double, System. Double, Xamarin.Forms . Animazione)) e [ `Insert` ] (xrif: Xamarin.Forms . Animation. Insert (System. Double, System. Double, Xamarin.Forms . Animazione)).

### <a name="beyond-the-high-level-animation-methods"></a>Oltre ai metodi di animazione di alto livello

Nell'esempio [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) viene inoltre illustrato come eseguire animazioni che vanno oltre le proprietà di destinazione dei `ViewExtensions` metodi. In un esempio, una serie di periodi viene più lunga; in un altro esempio, `BackgroundColor` viene animata una proprietà.

### <a name="more-of-your-own-awaitable-methods"></a>Un maggior numero di metodi awaitable

[ `TranslateTo` ] (Xrif: Xamarin.Forms . ViewExtensions. TranslateTo ( Xamarin.Forms . VisualElement, System. Double, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) `ViewExtensions` il metodo non funziona con la [`Easing.SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) funzione. Si interrompe quando l'output di interpolazione è superiore a 1.

La libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene una [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe con [`TranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) i [`TranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) metodi di estensione e che non presentano questo problema, oltre [`CancelTranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) ai [`CancelTranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) metodi e per l'annullamento di tali animazioni.

[**SpringSlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) illustra il `TranslateXTo` metodo.

La `MoreExtensions` classe contiene anche un [`TranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) metodo di estensione che combina la conversione X e Y e un [`CancelTranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) metodo.

### <a name="implementing-a-bezier-animation"></a>Implementazione di un'animazione Bezier

È anche possibile sviluppare un'animazione che sposta un elemento lungo il percorso di una spline di Bezier. La libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene una [`BezierSpline`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) struttura che incapsula una spline di Bezier e un' [`BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) enumerazione per controllare l'orientamento.

La [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe contiene un [`BezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) metodo di estensione e un [`CancelBezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) metodo.

L'esempio [**BezierLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) illustra l'animazione di un elemento lungo un percorso Beizer.

## <a name="working-with-animationextensions"></a>Uso di AnimationExtensions

Un tipo di animazione mancante dalla raccolta standard è un'animazione a colori. Il problema è che non esiste un modo giusto per interpolare tra due `Color` valori. È possibile eseguire l'interpolazione dei singoli valori RGB, ma altrettanto validi è l'interpolazione dei valori HSL.

Per questo motivo, la [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene due `Color` metodi di animazione: [`RgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166) e [`HslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188) . Sono disponibili anche due metodi di annullamento: [`CancelRgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) e [`CancelHslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206) .

Entrambi i metodi usano [`ColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211) , che esegue l'animazione chiamando il metodo generico completo [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) in [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) .

Nell'esempio [**ColorAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) viene illustrato l'utilizzo di questi due tipi di animazioni colori.

## <a name="structuring-your-animations"></a>Strutturazione delle animazioni

A volte è utile esprimere animazioni in XAML e usarle insieme a MVVM. Questo passaggio è trattato nel capitolo 23 del prossimo capitolo [**. Trigger e comportamenti**](chapter23.md).

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 22 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Capitolo 22 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animazione](~/xamarin-forms/user-interface/animation/index.md)
