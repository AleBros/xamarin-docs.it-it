---
title: Riepilogo del capitolo 22. Animazione
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: df0647e869fcf37a3a1dfdeb6f3dbf1e7d07ad95
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-22-animation"></a>Riepilogo del capitolo 22. Animazione

Si è visto che è possibile creare la propria animazioni mediante il timer di xamarin. Forms o `Task.Delay`, ma in genere più semplice utilizzando le funzionalità di animazione fornite da xamarin. Forms. Le tre classi implementano tali animazioni:

- [`ViewExtensions`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/), l'approccio generale
- [`Animation`](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/), più versatile difficile
- [`AnimationExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/), un approccio più versatile, di livello più basso

In genere, le animazioni destinate le proprietà che sono supportate da proprietà associabili. Questo non è un requisito, ma queste sono le uniche proprietà che reagisce dinamicamente alle modifiche apportate.

Nessuna interfaccia di XAML per le animazioni, ma è possibile integrare le animazioni in XAML con le tecniche descritte [ **capitolo 23. I trigger e i comportamenti**](chapter23.md).

## <a name="exploring-basic-animations"></a>Esplorazione delle animazioni di base

Le funzioni di animazione di base sono metodi di estensione, vedere il [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe. Questi metodi si applicano a qualsiasi oggetto che deriva da `VisualElement`. Le trasformazioni illustrate nella proprietà di destinazione le animazioni più semplice [ `Chapter 21. Transforms` ](chapter21.md).

Il [ **AnimationTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) viene illustrato come la `Clicked` gestore eventi per un `Button` può chiamare il [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) il metodo di estensione per ruotare il pulsante in un cerchio.

Il `RotateTo` le modifiche al metodo di `Rotation` proprietà del `Button` da 0 a 360 nel corso di un quarto di secondo (per impostazione predefinita). Se il `Button` vengono scelti nuovamente, tuttavia, non esegue alcuna operazione perché il `Rotation` proprietà è già 360 gradi.

### <a name="setting-the-animation-duration"></a>Impostazione della durata di animazione

Il secondo argomento `RotateTo` corrisponde a una durata in millisecondi. Se impostato su un valore elevato, toccando il `Button` durante un'animazione viene avviata una nuova animazione iniziando l'angolo corrente.

### <a name="relative-animations"></a>Animazioni relative

Il `RelRotateTo` metodo esegue una rotazione relativa aggiungendo un valore specificato al valore esistente. Questo metodo consente il `Button` a scelte più volte e ogni volta aumenta il `Rotation` proprietà di 360 gradi.

### <a name="awaiting-animations"></a>In attesa di animazioni

Tutti i metodi di animazione in `ViewExtensions` restituire `Task<bool>` oggetti. Ciò significa che è possibile definire una serie di animazioni sequenziali utilizzando `ContinueWith` o `await`. Il `bool` completamento valore restituito è `false` se l'animazione completato senza interruzione o `true` se è stato annullato dal [ `CancelAnimation` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/) metodo, che annulla tutte le animazioni avviate dal gli altri metodi nella `ViewExtensions` che vengono impostate sullo stesso elemento.

### <a name="composite-animations"></a>Animazioni composite

È possibile combinare le animazioni di attese e non atteso per creare animazioni composite. Queste sono le animazioni in `ViewExtensions` che hanno come destinazione il `TranslatonX`, `TranslationY`, e `Scale` trasforma le proprietà:

- [`TranslateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/)
- [`ScaleTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.ScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)
- [`RelScaleTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)

Si noti che `TranslateTo` potrebbe potenzialmente influire su entrambi i `TranslationX` e `TranslationY` proprietà.

### <a name="taskwhenall-and-taskwhenany"></a>Task. whenall e Task.WhenAny

È inoltre possibile gestire le animazioni simultanee utilizzando [ `Task.WhenAll` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.WhenAll%7BTResult%7D/p/System.Collections.Generic.IEnumerable%7BSystem.Threading.Tasks.Task%7BTResult%7D%7D/), che segnala quando hanno concluso tutti più attività, e [ `Task.WhenAny` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.WhenAny%7BTResult%7D/p/System.Collections.Generic.IEnumerable%7BSystem.Threading.Tasks.Task%7BTResult%7D%7D/), che segnala quando il primo di più ha concluso l'attività.

### <a name="rotation-and-anchors"></a>Rotazione e punti di ancoraggio

Quando si chiama il `ScaleTo`, `RelScaleTo`, `RotateTo`, e `RelRotateTo` metodi, è possibile impostare il [ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/) e [ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/) proprietà per indicare il centro della scala e la rotazione.

Il [ **CircleButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) questa tecnica è dimostrata ruotando un `Button` attorno al centro della pagina.

### <a name="easing-functions"></a>Funzioni di interpolazione

In genere le animazioni vengono lineare da un valore iniziale a un valore finale. Funzioni di interpolazione possono causare animazioni velocizzare o rallentare il corso. L'ultimo argomento facoltativo per i metodi di animazione è di tipo [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/), una classe che definisca 11 statici campi di sola lettura di tipo `Easing`:

- [`Linear`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/), il valore predefinito
- [`SinIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinIn/), [ `SinOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinOut/), e [`SinInOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinInOut/)
- [`CubicIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicIn/), [ `CubicOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicOut/), e [`CubicInOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicInOut/)
- [`BounceIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceIn/) e [`BounceOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceOut/)
- [`SpringIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) e [`SpringOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/)

Il `In` suffisso indica che l'effetto è all'inizio dell'animazione, `Out` significa alla fine, e `InOut` significa che è all'inizio e alla fine dell'animazione.

Il [ **BounceButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) esempio viene illustrato l'utilizzo di funzioni di interpolazione.

### <a name="your-own-easing-functions"></a>Funzioni di interpolazione

È inoltre possibile definire per funzioni di interpolazione propri passando un [ `Func<double, double>` ](https://developer.xamarin.com/api/type/System.Func%3CT,TResult%3E/) per il [ `Easing` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Easing.Easing/p/System.Func%7BSystem.Double,System.Double%7D/). `Easing` definisce inoltre una conversione implicita da `Func<double, double>` a `Easing`. L'argomento della funzione di interpolazione è sempre nell'intervallo compreso tra 0 e 1 come procede l'animazione in modo lineare dall'inizio alla fine. La funzione *in genere* restituisce un valore compreso nell'intervallo da 0 a 1, ma potrebbe essere brevemente negativo o maggiore di 1 (come accade con la `SpringIn` e `SpringOut` funzioni) o potrebbe interrompere le regole se si sta eseguendo.

Il [ **UneasyScale** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) illustrata una funzione di interpolazione personalizzata e [ **CustomCubicEase** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) viene illustrato un altro.

Il [ **SwingButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) esempio illustra inoltre una funzione di interpolazione personalizzata e una tecnica di modifica di `AnchorX` e `AnchorY` proprietà all'interno di una sequenza di animazione di rotazione.

Il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria ha un [ `JiggleButton` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) funzione di classe che utilizza un andamento personalizzato per jiggle un pulsante quando viene selezionato. Il [ **JiggleButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) esempio viene illustrato come.

### <a name="entrance-animations"></a>Animazioni di entrata

Un tipo comune di animazione quando viene visualizzata prima di tutto una pagina. Questo tipo un'animazione può essere avviata nel [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/) eseguire l'override della pagina. Per le animazioni, è consigliabile impostare il codice XAML per la modalità della pagina che venga visualizzata *dopo* l'animazione, inizializzare e aggiungere un'animazione il layout dal codice.

Il [ **FadingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) esempio viene utilizzata la [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo di estensione per il contenuto della pagina di dissolvenza.

Il [ **SlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) esempio viene utilizzata la [ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo di estensione per scorrere il contenuto della pagina dai lati.

Il [ **SwingingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) esempio viene utilizzata la [ `RotateYTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) il metodo di estensione per l'animazione di `RotationY` proprietà. Oggetto [ `RotateXTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo inoltre è disponibile.

### <a name="forever-animations"></a>Per le animazioni

A altra estremità, "forever" esecuzione di animazioni fino a quando il programma viene terminato. Questi sono in genere a scopo dimostrativo.

Il [ **FadingTextAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) Usa [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) animazione di dissolvenza in entrata e in uscita di due parti di testo.

[**PalindromeAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) consente di visualizzare un palindrome e quindi in modo sequenziale ruota singole lettere di 180 gradi in modo che siano tutti capovolto. Quindi, l'intera stringa viene capovolta 180 gradi per leggere lo stesso della stringa originale.

Il [ **CopterAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) esempio Ruota un semplice `BoxView` elicottero durante la rivoluzione, attorno al centro dello schermo.

[**RotatingSpokes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) deve affrontare `BoxView` raggi attorno al centro dello schermo e quindi Ruota ogni spoke per creare modelli interessanti:

[![Schermata triplo della rotazione raggi](images/ch22fg21-small.png "rotazione raggi")](images/ch22fg21-large.png#lightbox "rotazione raggi")

Tuttavia, aumentando progressivamente il `Rotation` proprietà di un elemento potrebbe non funzionare a lungo termine, come il [ **RotationBreakdown** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown) esempio viene illustrato.

Il [ **SpinningImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) Usa [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/), [ `RotateXTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/), e [ `RotateYTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) per far sembrare come se rotazione di una bitmap nello spazio 3D.

### <a name="animating-the-bounds-property"></a>Un'animazione alla proprietà di limiti

L'unico metodo di estensione in `ViewExtensions` non ancora dimostrato è [ `LayoutTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.LayoutTo/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/System.UInt32/Xamarin.Forms.Easing/), che aggiunge un'animazione in modo efficace di sola lettura [ `Bounds` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Bounds/) proprietà chiamando il [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) metodo. Questo metodo viene chiamato in genere da `Layout` derivati come verranno trattati [ **capitolo 26. CustomLayouts**](chapter26.md).

Il `LayoutTo` metodo deve essere limitato a scopi specifici. Il [ **BouncingBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) utilizzata per comprimere ed espandere un `BoxView` come aperture disattivare i lati di una pagina.

Il [ **XamagonXuzzle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) Usa `LayoutTo` per spostare i riquadri in un'implementazione di classica puzzle 15-16 che consente di visualizzare un'immagine codificata anziché numerati riquadri:

[![La schermata di Xamarin Xuzzle](images/ch22fg26-small.png "Xuzzle Puzzle gioco")](images/ch22fg26-large.png#lightbox "Xuzzle Puzzle gioco")

### <a name="your-own-awaitable-animations"></a>Animazioni awaitable

Il [ **TryAwaitableAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) esempio crea un'animazione awaitable. La classe fondamentale che può restituire un `Task` oggetto dal metodo e segnale al completamento dell'animazione è [ `TaskCompletionSource` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.TaskCompletionSource%3CTResult%3E/).

## <a name="deeper-into-animations"></a>Più approfondito in animazioni

Il sistema di animazione xamarin. Forms può essere intuitive. Oltre al `Easing` (classe), il sistema di animazione comprende il `ViewExtensions`, `Animation`, e `AnimationExtension` classses.

### <a name="viewextensions-class"></a>Classe ViewExtensions

Abbiamo già visto [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/). Definisce i metodi che restituiscono nove `Task<bool>` e [ `CancelAnimations` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/). Sette della destinazione nove metodi proprietà di trasformazione. Le altre due sono [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/), delle destinazioni di [ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/) , proprietà e [ `LayoutTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.LayoutTo/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/System.UInt32/Xamarin.Forms.Easing/), che chiama il [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) metodo.

### <a name="animation-class"></a>Classe di animazione

Il [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/) classe dispone di un [costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Animation.Animation/p/System.Action%7BSystem.Double%7D/System.Double/System.Double/Xamarin.Forms.Easing/System.Action/) con cinque argomenti per definire callback e terminato di metodi e i parametri dell'animazione.

Le animazioni figlio possono essere aggiunti con [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Add/p/System.Double/System.Double/Xamarin.Forms.Animation/), [ `Insert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Insert/p/System.Double/System.Double/Xamarin.Forms.Animation/), [ `WithConcurrent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.WithConcurrent/p/Xamarin.Forms.Animation/System.Double/System.Double/), ed e overload di [ `WithConcurrent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.WithConcurrent/p/System.Action%7BSystem.Double%7D/System.Double/System.Double/Xamarin.Forms.Easing/System.Double/System.Double/).

L'oggetto di animazione viene quindi avviato con una chiamata al [ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action%7BSystem.Double,System.Boolean%7D/System.Func%7BSystem.Boolean%7D/) metodo.

### <a name="animationextensions-class"></a>Classe AnimationExtensions

Il [ `AnimationExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/) classe contiene principalmente i metodi di estensione. Esistono diverse versioni di un `Animate` (metodo) e generico [ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate%7BT%7D/p/Xamarin.Forms.IAnimatable/System.String/System.Func%7BSystem.Double,T%7D/System.Action%7BT%7D/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action%7BT,System.Boolean%7D/System.Func%7BSystem.Boolean%7D/) metodo è talmente versatile che può essere davvero la funzione di animazione solo è necessario.

## <a name="working-with-the-animation-class"></a>Utilizzo della classe di animazione

Il [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) esempio viene illustrato il [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) classe con numerose animazioni diversi.

### <a name="child-animations"></a>Animazioni figlio

Il [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) viene inoltre illustrato come utilizzano animazioni, assicurarsi di (molto simile) figlio [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Add/p/System.Double/System.Double/Xamarin.Forms.Animation/) e [ `Insert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Insert/p/System.Double/System.Double/Xamarin.Forms.Animation/) metodi.

### <a name="beyond-the-high-level-animation-methods"></a>Oltre ai metodi di alto livello di animazione

Il [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) esempio viene inoltre illustrato come eseguire le animazioni che vanno oltre le proprietà a cui è destinato il `ViewExtensions` metodi. In un esempio, una serie di periodi di ottenere più; in un altro esempio, un `BackgroundColor` animazione alla proprietà.

### <a name="more-of-your-own-awaitable-methods"></a>Più i propri metodi awaitable

Il [ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo `ViewExtensions` non funziona con il [ `Easing.SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/) (funzione). Viene arrestato in corrispondenza l'output di interpolazione Ottiene superiori a 1.

Il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria contiene un [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe con [ `TranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) e [ `TranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) metodi di estensione che non dispongono di questo problema, così come [ `CancelTranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) e [ `CancelTranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) metodi per l'annullamento di quelli animazioni.

Il [ **SpringSlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) viene illustrato il `TranslateXTo` metodo.

Il `MoreExtensions` classe contiene inoltre un [ `TranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) metodo di estensione che combina la traduzione di X e Y, e un [ `CancelTranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) metodo.

### <a name="implementing-a-bezier-animation"></a>Implementazione di un'animazione in curva di Bezier

È inoltre possibile sviluppare un'animazione che si sposta un elemento nel percorso di una spline di Bézier. Il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria contiene un [ `BezierSpline` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) struttura che incapsula una spline di Bézier e [ `BezierTangent` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) enumerazione per l'orientamento del controllo.

Il [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe contiene un [ `BezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) metodo di estensione e un [ `CancelBezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) metodo.

Il [ **BezierLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) esempio viene illustrato un elemento lungo un percorso Beizer l'animazione.

## <a name="working-with-animationextensions"></a>Utilizzo di AnimationExtensions

Un tipo di animazione mancante dalla raccolta standard è un'animazione di colore. Il problema è che non è corretto per l'interpolazione tra due `Color` valori. È possibile interpolare i singoli valori RGB, ma i valori HSL è interpolazione solo come valido.

Per questo motivo, il [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria contiene due `Color` metodi animazione: [ `RgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166)e [ `HslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188). (Sono disponibili due metodi di annullamento: [ `CancelRgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) e [ `CancelHslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206)).

Entrambi i metodi usano [ `ColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211), che esegue l'animazione chiamando generica completa [ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate%7BT%7D/p/Xamarin.Forms.IAnimatable/System.String/System.Func%7BSystem.Double,T%7D/System.Action%7BT%7D/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action%7BT,System.Boolean%7D/System.Func%7BSystem.Boolean%7D/) metodo [ `AnimationExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/).

Il [ **ColorAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) esempio viene illustrato l'utilizzo di questi due tipi di animazioni di colore.

## <a name="structuring-your-animations"></a>Strutturare le animazioni

È talvolta utile esprimere le animazioni in XAML e usarle in combinazione con MVVM. Questa attività viene descritta nel capitolo successivo, [ **capitolo 23. I trigger e i comportamenti**](chapter23.md).



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 22 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Esempi di capitolo 22](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animazione](~/xamarin-forms/user-interface/animation/index.md)
