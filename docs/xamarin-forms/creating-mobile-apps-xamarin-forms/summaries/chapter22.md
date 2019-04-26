---
title: Riepilogo del capitolo 22. Animazione
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: Riepilogo del capitolo 22. Animazione'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 7b3695ce145c2ca58238e2c9a601923cbcefa182
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61333101"
---
# <a name="summary-of-chapter-22-animation"></a>Riepilogo del capitolo 22. Animazione

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

Si è visto che è possibile creare il proprio animazioni tramite il timer di xamarin. Forms o `Task.Delay`, ma è in genere più semplice usando le funzionalità di animazione fornite da xamarin. Forms. Tre classi implementano queste animazioni:

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions), l'approccio generale
- [`Animation`](xref:Xamarin.Forms.Animation), più versatile ma più difficile
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions), approccio più versatile, livello più basso

In genere, le animazioni riguardano le proprietà che sono supportate da proprietà associabili. Questo non è un requisito, ma queste sono le uniche proprietà in modo dinamico di reagire alle modifiche.

Non è disponibile alcuna interfaccia XAML per queste animazioni, ma è possibile integrare le animazioni in XAML utilizzando le tecniche descritte [ **capitolo 23. I trigger e comportamenti**](chapter23.md).

## <a name="exploring-basic-animations"></a>Esplorazione delle animazioni di base

Le funzioni di animazione di base sono metodi di estensione trovati nel [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe. Questi metodi si applicano a qualsiasi oggetto che deriva da `VisualElement`. Le animazioni più semplice di destinazione le trasformazioni proprietà descritte nel [ `Chapter 21. Transforms` ](chapter21.md).

Il [ **AnimationTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) viene illustrato come il `Clicked` gestore eventi per un `Button` può chiamare il [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo di estensione per ruotare il pulsante in un cerchio.

Il `RotateTo` metodo viene modificato il `Rotation` proprietà del `Button` compreso tra 0 e 360 nel corso di un quarto secondo (per impostazione predefinita). Se il `Button` toccando anche in questo caso, tuttavia, non esegue alcuna operazione perché il `Rotation` proprietà è già 360 gradi.

### <a name="setting-the-animation-duration"></a>Impostazione della durata di animazione

Il secondo argomento `RotateTo` corrisponde a una durata in millisecondi. Se impostato su un valore elevato, toccando il `Button` durante un'animazione viene avviata una nuova funzionalità introdotta animazione all'angolo corrente.

### <a name="relative-animations"></a>Animazioni relative

Il `RelRotateTo` metodo esegue una rotazione relativa mediante l'aggiunta di un valore specificato al valore esistente. Questo metodo consente il `Button` da scegliere più volte, mentre ogni ora aumenta la `Rotation` proprietà di 360 gradi.

### <a name="awaiting-animations"></a>In attesa di animazioni

Tutti i metodi di animazione nelle `ViewExtensions` restituire `Task<bool>` oggetti. Ciò significa che è possibile definire una serie di animazioni sequenziale tramite `ContinueWith` o `await`. Il `bool` completamento valore restituito è `false` se l'animazione è terminata senza interruzioni o `true` se è stato annullato dal [ `CancelAnimation` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) metodo, che annulla tutte le animazioni avviate dal gli altri metodi nella `ViewExtensions` impostati sullo stesso elemento.

### <a name="composite-animations"></a>Animazioni composite

È possibile combinare le animazioni di attese e non atteso per creare animazioni composite. Queste sono le animazioni nel `ViewExtensions` che hanno come destinazione il `TranslationX`, `TranslationY`, e `Scale` trasforma le proprietà:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))

Si noti che `TranslateTo` potenzialmente influisce su entrambe le `TranslationX` e `TranslationY` proprietà.

### <a name="taskwhenall-and-taskwhenany"></a>Task.WhenAny e Task. whenall

È anche possibile gestire simultanee animazioni tramite [ `Task.WhenAll` ](xref:System.Threading.Tasks.Task.WhenAll*), che segnala quando hanno concluso tutte più attività, e [ `Task.WhenAny` ](xref:System.Threading.Tasks.Task.WhenAny*), che segnala quando il primo di diversi ha concluso l'attività.

### <a name="rotation-and-anchors"></a>Rotazione e punti di ancoraggio

Quando si chiama il `ScaleTo`, `RelScaleTo`, `RotateTo`, e `RelRotateTo` metodi, è possibile impostare il [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) e [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) le proprietà per indicare il centro della scala e la rotazione.

Il [ **CircleButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) questa tecnica è dimostrata ruotando un `Button` intorno al centro della pagina.

### <a name="easing-functions"></a>Funzioni di interpolazione

In genere le animazioni sono lineare da un valore iniziale a un valore finale. Funzioni di interpolazione possono causare animazioni velocizzare o rallentare il corso. L'ultimo argomento facoltativo per i metodi di animazione è di tipo [ `Easing` ](xref:Xamarin.Forms.Easing), una classe che definisca 11 ReadOnly campi statici di tipo `Easing`:

- [`Linear`](xref:Xamarin.Forms.Easing.Linear), il valore predefinito
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn), [ `SinOut` ](xref:Xamarin.Forms.Easing.SinOut), e [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn), [ `CubicOut` ](xref:Xamarin.Forms.Easing.CubicOut), e [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) e [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) e [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

Il `In` suffisso indica che si trova all'inizio dell'animazione, l'effetto `Out` significa che alla fine, e `InOut` significa che è all'inizio e alla fine dell'animazione.

Il [ **BounceButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) esempio dimostra l'uso di funzioni di interpolazione.

### <a name="your-own-easing-functions"></a>Funzioni di interpolazione

È anche possibile definire per funzioni di interpolazione proprie passando un [ `Func<double, double>` ](xref:System.Func`2) per il [ `Easing` costruttore](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})). `Easing` definisce inoltre una conversione implicita da `Func<double, double>` a `Easing`. L'argomento alla funzione di interpolazione è sempre nell'intervallo compreso tra 0 e 1 come procede l'animazione in modo lineare dall'inizio alla fine. La funzione *generalmente* restituisce un valore compreso nell'intervallo da 0 a 1, ma potrebbe essere brevemente negativo o maggiore di 1 (come avviene con la `SpringIn` e `SpringOut` funzioni) oppure è stato possibile interrompere le regole, se si conosce ciò che si sta eseguendo.

Il [ **UneasyScale** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) esempio illustra una funzione di interpolazione personalizzata, e [ **CustomCubicEase** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) viene illustrato un altro.

Il [ **SwingButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) esempio illustra anche una funzione di interpolazione personalizzata e anche una tecnica della modifica il `AnchorX` e `AnchorY` proprietà all'interno di una sequenza di animazione di rotazione.

Il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria dispone di un [ `JiggleButton` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) classe che usa una funzionalità di interpolazione personalizzata funziona per jiggle un pulsante quando viene selezionato. Il [ **JiggleButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) esempio illustra come fare.

### <a name="entrance-animations"></a>Animazioni di ingresso

Un tipo comune di animazione si verifica quando viene visualizzata prima di tutto una pagina. Un'animazione di questo tipo può essere avviata nel [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) eseguire l'override della pagina. Per queste animazioni, è consigliabile impostare il XAML per la modalità di pagina che venga visualizzata *dopo* l'animazione, inizializzare e quindi aggiungere un'animazione di layout dal codice.

Il [ **FadingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) nell'esempio il [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo di estensione per sfumare il contenuto della pagina.

Il [ **SlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) nell'esempio il [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo di estensione per scorrere il contenuto della pagina dai lati.

Il [ **SwingingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) nell'esempio il [ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo di estensione per aggiungere un'animazione di `RotationY` proprietà. Oggetto [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo inoltre è disponibile.

### <a name="forever-animations"></a>Animazioni per sempre

Da altra parte, "forever" animazioni eseguire fino a quando il programma viene terminato. In genere questi sono destinati a scopo dimostrativo.

Il [ **FadingTextAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) esempio Usa [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) animazione di dissolvenza in entrata e in uscita due porzioni di testo.

[**PalindromeAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) Visualizza un palindrome e quindi in modo sequenziale Ruota le lettere singole di 180 gradi in modo che siano tutti capovolto. Quindi l'intera stringa viene capovolto 180 gradi per leggere lo stesso come la stringa originale.

Il [ **CopterAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) esempio Ruota un semplice `BoxView` elicottero guidato da loro durante la rivoluzione intorno al centro dello schermo.

[**RotatingSpokes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) si basa `BoxView` raggi intorno al centro dello schermo e quindi Ruota ogni spoke per creare modelli interessanti:

[![Schermata triplo della rotazione raggi](images/ch22fg21-small.png "rotazione raggi")](images/ch22fg21-large.png#lightbox "rotazione raggi")

Tuttavia, aumentando progressivamente le `Rotation` proprietà di un elemento potrebbe non funzionare a lungo termine, come i [ **RotationBreakdown** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown) esempio viene illustrato.

Il [ **SpinningImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) esempio Usa [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), e [ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) per far sembrare come se Ruota un'immagine bitmap nello spazio 3D.

### <a name="animating-the-bounds-property"></a>Animazione della proprietà limiti

L'unico metodo di estensione nel `ViewExtensions` non ancora illustrato viene [ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), che aggiunge un'animazione in modo efficace di sola lettura [ `Bounds` ](xref:Xamarin.Forms.VisualElement.Bounds) proprietà chiamando i [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) (metodo). Questo metodo viene chiamato in genere `Layout` derivati come verranno trattati [ **capitolo 26. CustomLayouts**](chapter26.md).

Il `LayoutTo` metodo deve essere limitato a scopi speciali. Il [ **BouncingBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) verrà utilizzata per comprimere ed espandere una `BoxView` come rimbalza i lati di una pagina.

Il [ **XamagonXuzzle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) nell'esempio `LayoutTo` per spostare i riquadri in un'implementazione della distribuzione classica puzzle 15-16 che visualizza un'immagine codificati vengono invece di sezioni numerate:

[![Tripla screenshot di Xamarin Xuzzle](images/ch22fg26-small.png "Xuzzle Puzzle Game")](images/ch22fg26-large.png#lightbox "Xuzzle Puzzle gioco")

### <a name="your-own-awaitable-animations"></a>Animazioni awaitable

Il [ **TryAwaitableAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) esempio crea un'animazione awaitable. La classe fondamentale che può restituire un `Task` oggetto dal metodo e segnale al completamento dell'animazione è [ `TaskCompletionSource` ](xref:System.Threading.Tasks.TaskCompletionSource`1).

## <a name="deeper-into-animations"></a>Dettagli della gestione delle animazioni

Il sistema di animazione di xamarin. Forms può essere un po' di confusione. Oltre al `Easing` comprende il sistema di animazione (classe), il `ViewExtensions`, `Animation`, e `AnimationExtension` classi.

### <a name="viewextensions-class"></a>Classe ViewExtensions

Abbiamo già visto [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions). Definisce nove i metodi che restituiscono `Task<bool>` e [ `CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)). Sette della destinazione di nove metodi proprietà di trasformazione. Gli altri due [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), le destinazioni di [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) proprietà, e [ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), che chiama il [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) (metodo).

### <a name="animation-class"></a>Classe di animazione

Il [ `Animation` ](xref:Xamarin.Forms.AnimationExtensions) classe dispone di una [costruttore](xref:Xamarin.Forms.Animation.%23ctor(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Action)) con cinque argomenti per definire metodi finiti e callback, nonché i parametri dell'animazione.

È possibile aggiungere le animazioni figlio con [ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)), [ `Insert` ](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)), [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))e di eseguire l'overload di [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Double,System.Double)).

L'oggetto di animazione viene quindi avviato con una chiamata ai [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) (metodo).

### <a name="animationextensions-class"></a>Classe AnimationExtensions

Il [ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions) contiene principalmente i metodi di estensione. Esistono diverse versioni di un' `Animate` metodo e il tipo generico [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) metodo è pertanto versatile che può essere davvero la funzione di animazione solo necessaria.

## <a name="working-with-the-animation-class"></a>Utilizzo della classe di animazione

Il [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) esempio viene illustrato il [ `Animation` ](xref:Xamarin.Forms.Animation) classe con diverse animazioni diversi.

### <a name="child-animations"></a>Animazioni figlio

Il [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) figlio dalle animazioni fisiche, che rende possibile usano il (molto simile) vengono illustrare inoltre [ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) e [ `Insert` ](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)) metodi.

### <a name="beyond-the-high-level-animation-methods"></a>Oltre ai metodi di animazione di alto livello

Il [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) esempio viene inoltre illustrato come eseguire animazioni che vanno oltre le proprietà interessate dal `ViewExtensions` metodi. In un esempio, una serie di periodi di ottenere più tempo; in un altro esempio, un `BackgroundColor` proprietà è animata.

### <a name="more-of-your-own-awaitable-methods"></a>Più i propri metodi awaitable

Il [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo `ViewExtensions` non funziona con il [ `Easing.SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) (funzione). Si arresta quando l'output di interpolazione Ottiene superiori a 1.

Il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria contiene una [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe [ `TranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) e[ `TranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) metodi di estensione che non hanno questo problema, nonché [ `CancelTranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) e [ `CancelTranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) metodi per l'annullamento quelli animazioni.

Il [ **SpringSlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) illustra i `TranslateXTo` (metodo).

Il `MoreExtensions` classe contiene inoltre una [ `TranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) metodo di estensione che combina la traduzione di X e Y, e un [ `CancelTranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) (metodo).

### <a name="implementing-a-bezier-animation"></a>Implementazione di un'animazione di Bezier

È anche possibile sviluppare un'animazione che sposta un elemento nel percorso di una spline di Bézier. Il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria contiene una [ `BezierSpline` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) struttura che incapsula una spline di Bézier e un oggetto [ `BezierTangent` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) orientamento del controllo dell'enumerazione.

Il [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe contiene una [ `BezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) il metodo di estensione e un [ `CancelBezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) (metodo).

Il [ **BezierLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) esempio viene illustrato un elemento lungo un tracciato Beizer l'animazione.

## <a name="working-with-animationextensions"></a>Utilizzo di AnimationExtensions

Un tipo di animazione mancante dalla raccolta di standard è un'animazione di colore. Il problema è che non è possibile a destra per interpolare tra due `Color` valori. È possibile eseguire l'interpolazione i singoli valori RGB, ma solo come validi è interpolazione i valori HSL.

Per questo motivo, il [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria contiene due `Color` i metodi di animazione: [ `RgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166)e [ `HslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188). (Esistono anche due metodi di annullamento: [ `CancelRgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) e [ `CancelHslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206)).

Entrambi i metodi sfrutta [ `ColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211), che consente di eseguire l'animazione chiamando generica estesa [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) metodo nella [ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions).

Il [ **ColorAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) esempio viene illustrato l'utilizzo di questi due tipi di animazioni di colore.

## <a name="structuring-your-animations"></a>Strutturazione di animazioni

È talvolta utile esprimere le animazioni in XAML e usarle in combinazione con MVVM. Questa attività viene descritta nel capitolo successivo [ **capitolo 23. I trigger e comportamenti**](chapter23.md).



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 22 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Capitolo 22 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animazione](~/xamarin-forms/user-interface/animation/index.md)
