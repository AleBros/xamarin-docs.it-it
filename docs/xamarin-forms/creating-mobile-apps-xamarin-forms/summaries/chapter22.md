---
title: Riassunto del capitolo 22. Animazione
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 22. Animazione'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 935be5bd6696600644463eb4ec26410b546f42a0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771004"
---
# <a name="summary-of-chapter-22-animation"></a>Riassunto del capitolo 22. Animazione

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

Si è visto che è possibile creare animazioni personalizzate utilizzando `Task.Delay`il timer Xamarin.Forms o , ma è in genere più semplice utilizzando le funzionalità di animazione fornite da Xamarin.Forms. Tre classi implementano queste animazioni:Three classes implement these animations:

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions), l'approccio di alto livello
- [`Animation`](xref:Xamarin.Forms.Animation), più versatile ma più duro
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions), l'approccio più versatile e di livello più basso

In genere, le animazioni hanno come destinazione le proprietà supportate dalle proprietà associabili. Questo non è un requisito, ma queste sono le uniche proprietà che reagiscono dinamicamente alle modifiche.

Non esiste un'interfaccia XAML per queste animazioni, ma è possibile integrare le animazioni in XAML usando le tecniche descritte nel [**capitolo 23. Trigger e comportamenti**](chapter23.md).

## <a name="exploring-basic-animations"></a>Esplorazione delle animazioni di base

Le funzioni di animazione di [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) base sono metodi di estensione disponibili nella classe. Questi metodi si applicano a `VisualElement`qualsiasi oggetto che deriva da . Le animazioni più semplici sono [`Chapter 21. Transforms`](chapter21.md)destinate alle proprietà delle trasformazioni descritte in .

Il [**AnimationTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) viene `Clicked` illustrato come `Button` il [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) gestore eventi per un può chiamare il metodo di estensione per far girare il pulsante in un cerchio.

Il `RotateTo` metodo `Rotation` modifica la `Button` proprietà di 0 a 360 nel corso di un quarto di secondo (per impostazione predefinita). Se `Button` l'oggetto viene toccato di nuovo, tuttavia, non esegue alcuna operazione perché la `Rotation` proprietà è già di 360 gradi.

### <a name="setting-the-animation-duration"></a>Impostazione della durata dell'animazione

Il secondo `RotateTo` argomento di è una durata in millisecondi. Se impostato su un valore `Button` elevato, toccando il durante un'animazione inizia una nuova animazione a partire dall'angolo corrente.

### <a name="relative-animations"></a>Animazioni relative

Il `RelRotateTo` metodo esegue una rotazione relativa aggiungendo un valore specificato al valore esistente. Questo metodo `Button` consente di toccare più volte l'oggetto e ogni volta aumenta la `Rotation` proprietà di 360 gradi.

### <a name="awaiting-animations"></a>In attesa di animazioni

Tutti i metodi `ViewExtensions` `Task<bool>` di animazione negli oggetti restituiti. Ciò significa che è possibile definire `ContinueWith` `await`una serie di animazioni sequenziali utilizzando o . Il `bool` valore restituito `false` di completamento è `true` se l'animazione [`CancelAnimation`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) è stata completata senza interruzioni `ViewExtensions` o se è stata annullata dal metodo , che annulla tutte le animazioni avviate dall'altro metodo in che sono impostate sullo stesso elemento.

### <a name="composite-animations"></a>Animazioni composite

È possibile combinare animazioni attese e non attese per creare animazioni composite. Di seguito sono `ViewExtensions` riportate `TranslationX` `TranslationY`le `Scale` animazioni destinate alle proprietà , e transform:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))

Si `TranslateTo` noti che `TranslationX` potenzialmente influisce su entrambe le proprietà e `TranslationY` .

### <a name="taskwhenall-and-taskwhenany"></a>Task.WhenAll e Task.WhenAny

È anche possibile gestire animazioni simultanee utilizzando [`Task.WhenAll`](xref:System.Threading.Tasks.Task.WhenAll*), che [`Task.WhenAny`](xref:System.Threading.Tasks.Task.WhenAny*)segnala quando più attività sono state tutte concluse e , che segnala quando la prima di diverse attività è conclusa.

### <a name="rotation-and-anchors"></a>Rotazione e ancore

Quando si `ScaleTo` `RelScaleTo`chiamano `RotateTo`i `RelRotateTo` metodi , , [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) e , è possibile impostare le proprietà e per indicare il centro di ridimensionamento e rotazione.

Il [**CircleButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) viene illustrata `Button` questa tecnica ruotando a intorno al centro della pagina.

### <a name="easing-functions"></a>Funzioni di interpolazione

In genere le animazioni sono lineari da un valore iniziale a un valore finale. Le funzioni di accelerazione possono causare l'accelerazione o il rallentamento delle animazioni nel corso. L'ultimo argomento facoltativo per [`Easing`](xref:Xamarin.Forms.Easing)i metodi di animazione è di tipo `Easing`, una classe che definisce 11 campi statici di sola lettura di tipo :

- [`Linear`](xref:Xamarin.Forms.Easing.Linear), l'impostazione predefinita
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn), [`SinOut`](xref:Xamarin.Forms.Easing.SinOut), e[`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn), [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut), e[`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn)E[`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn)E[`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

Il `In` suffisso indica che l'effetto si `Out` trova all'inizio `InOut` dell'animazione, significa alla fine e indica che si trova all'inizio e alla fine dell'animazione.

[**Nell'esempio BounceButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) viene illustrato l'utilizzo delle funzioni di eamitura.

### <a name="your-own-easing-functions"></a>Le tue funzioni di andamento

È inoltre possibile definire funzioni di andamento personalizzate passando a [`Func<double, double>`](xref:System.Func`2) al [ `Easing` costruttore](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})). `Easing`definisce anche una `Func<double, double>` conversione implicita da a `Easing`. L'argomento della funzione di interpolazione è sempre compreso nell'intervallo da 0 a 1 poiché l'animazione procede in modo lineare dall'inizio alla fine. La funzione in *genere* restituisce un valore compreso tra 0 e 1, ma potrebbe `SpringIn` essere `SpringOut` brevemente negativo o maggiore di 1 (come nel caso delle funzioni e) o potrebbe infrangere le regole se si sa cosa si sta facendo.

Nell'esempio [**UneasyScale**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) viene illustrata una funzione di alleggerimento personalizzata e [**CustomCubicEase**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) ne illustra un'altra.

Il [**SwingButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) esempio viene inoltre illustrato una funzione di andamento personalizzato e anche una tecnica di modifica delle `AnchorX` proprietà e `AnchorY` all'interno di una sequenza di animazioni di rotazione.

La libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) dispone di una [`JiggleButton`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) classe che utilizza una funzione di allentamento personalizzata per eseguire il jiggle di un pulsante quando si fa clic su di esso. [**L'esempio JiggleButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) lo dimostra.

### <a name="entrance-animations"></a>Animazioni di ingresso

Un tipo popolare di animazione si verifica quando una pagina viene visualizzata per la prima volta. Tale animazione può essere [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) avviata nell'override della pagina. Per queste animazioni, è consigliabile configurare il codice XAML per la modalità di visualizzazione della pagina *dopo* l'animazione e quindi inizializzare e animare il layout dal codice.

Il [**FadingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) esempio [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) usa il metodo di estensione per dissolvenza nel contenuto della pagina.

Il [**SlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) esempio [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) usa il metodo di estensione per scorrere il contenuto della pagina dai lati.

Il [**SwingingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) esempio [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) usa il `RotationY` metodo di estensione per animare la proprietà. È [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) disponibile anche un metodo.

### <a name="forever-animations"></a>Animazioni per sempre

All'altro estremo, le animazioni "per sempre" vengono eseguite fino a quando il programma non viene terminato. Questi sono generalmente destinati a scopi dimostrativi.

L'esempio [**FadingTextAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) usa [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) l'animazione per applicare una dissolvenza in entrata e in uscita a due parti di testo.

[**PalindromeAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) visualizza un palindromo, quindi ruota in sequenza le singole lettere di 180 gradi in modo che siano tutte capovolta. Quindi l'intera stringa viene capovolta di 180 gradi per leggere la stessa stringa originale.

L'esempio [**CopterAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) ruota `BoxView` un semplice elicottero ruotandolo intorno al centro dello schermo.

[**RotazioneSpokes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) ruota `BoxView` raggi intorno al centro dello schermo, e poi ruota ogni spoke stesso per creare modelli interessanti:

[![Triplo screenshot di Rotating Spokes](images/ch22fg21-small.png "Raggi rotanti")](images/ch22fg21-large.png#lightbox "Raggi rotanti")

Tuttavia, l'aumento progressivo della proprietà di un elemento potrebbe non funzionare a lungo termine, come illustrato nell'esempio `Rotation` [**RotationBreakdown.However,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown) progressively increasing the property of an element might not work in the long term, as the RotationBreakdown sample demonstrates.

Nell'esempio [**SpinningImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) vengono utilizzati [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), e [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) per far sembrare che una bitmap stia ruotando nello spazio 3D.

### <a name="animating-the-bounds-property"></a>Animazione della proprietà bounds

L'unico metodo `ViewExtensions` di estensione [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing))non ancora illustrato è , [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) che anima [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) in modo efficace la proprietà di sola lettura chiamando il metodo . Questo metodo è `Layout` normalmente chiamato dai derivati come verrà discusso nel [**capitolo 26. CustomLayouts**](chapter26.md).

Il `LayoutTo` metodo dovrebbe essere limitato a scopi speciali. Il programma [**BouncingBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) lo utilizza `BoxView` per comprimere ed espandere un come rimbalza sui lati di una pagina.

L'esempio [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) usa `LayoutTo` per spostare le tessere in un'implementazione del classico puzzle 15-16 che visualizza un'immagine criptato piuttosto che piastrelle numerate:

[![Triplo screenshot di Xamarin Xuzzle](images/ch22fg26-small.png "Gioco di Xuzzle Puzzle")](images/ch22fg26-large.png#lightbox "Gioco di Xuzzle Puzzle")

### <a name="your-own-awaitable-animations"></a>Le tue animazioni awaitable

Il [**TryAwaitableAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) esempio crea un'animazione awaitable. La classe cruciale `Task` che può restituire un oggetto dal [`TaskCompletionSource`](xref:System.Threading.Tasks.TaskCompletionSource`1)metodo e segnale quando l'animazione viene completata è .

## <a name="deeper-into-animations"></a>Approfondire le animazioni

Il sistema di animazione Xamarin.Forms può essere un po 'di confusione. Oltre alla `Easing` classe , il sistema `ViewExtensions`di `Animation`animazione comprende le classi , , e `AnimationExtension` .

### <a name="viewextensions-class"></a>Classe ViewExtensions

Hai già visto [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions). Definisce nove metodi `Task<bool>` che [`CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))restituiscono e . Sette dei nove metodi hanno come destinazione le proprietà di trasformazione. Gli altri [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))due sono [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) , che [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing))ha come [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) destinazione la proprietà, e , che chiama il metodo .

### <a name="animation-class"></a>Classe di animazione

La [`Animation`](xref:Xamarin.Forms.AnimationExtensions) classe dispone di un [costruttore](xref:Xamarin.Forms.Animation.%23ctor(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Action)) con cinque argomenti per definire i metodi callback e finished e i parametri dell'animazione.

Le animazioni figlio [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) [`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation))possono [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))essere aggiunte con , , e l'overload di [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Double,System.Double)).

L'oggetto di animazione viene [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) quindi avviato con una chiamata al metodo .

### <a name="animationextensions-class"></a>Classe AnimationExtensions

La [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) classe contiene principalmente metodi di estensione. Ci sono diverse `Animate` versioni di [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) un metodo e il metodo generico è così versatile che è davvero l'unica funzione di animazione di cui hai bisogno.

## <a name="working-with-the-animation-class"></a>Utilizzo della classe Animation

Il [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) esempio [`Animation`](xref:Xamarin.Forms.Animation) viene illustrata la classe con diverse animazioni diverse.

### <a name="child-animations"></a>Animazioni figlio

Il [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) esempio illustra anche le animazioni figlio, [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) [`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)) che fanno uso di (molto simile) e metodi.

### <a name="beyond-the-high-level-animation-methods"></a>Oltre ai metodi di animazione di alto livello

Il [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) esempio viene inoltre illustrato come eseguire animazioni che vanno oltre le proprietà di destinazione dai `ViewExtensions` metodi. In un esempio, una serie di periodi si allunga; in un altro `BackgroundColor` esempio, una proprietà viene animata.

### <a name="more-of-your-own-awaitable-methods"></a>Più dei tuoi metodi awaitable

Il [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo `ViewExtensions` di non funziona [`Easing.SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) con la funzione. Si ferma quando l'output di andatura supera 1.

La libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) una classe con [`TranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) metodi di estensione [`TranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) e [`CancelTranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) [`CancelTranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) che non presentano questo problema, nonché e metodi per annullare tali animazioni.

Il [**SpringSlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) `TranslateXTo` viene illustrato il metodo.

La `MoreExtensions` classe contiene [`TranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) anche un metodo di estensione che [`CancelTranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) combina la conversione X e Y e un metodo.

### <a name="implementing-a-bezier-animation"></a>Implementazione di un'animazione di BezierImplementing a Bezier animation

È anche possibile sviluppare un'animazione che sposta un elemento lungo il percorso di una spline di Bezier. La libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene una [`BezierSpline`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) struttura che incapsula [`BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) una spline di Bezier e un'enumerazione per controllare l'orientamento.

La [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe [`BezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) contiene un [`CancelBezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) metodo di estensione e un metodo.

[**Nell'esempio BezierLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) viene illustrata l'animazione di un elemento lungo un percorso Beizer.The BezierLoop sample demonstrates animating an element along a Beizer path.

## <a name="working-with-animationextensions"></a>Utilizzo di AnimationExtensionsWorking with AnimationExtensions

Un tipo di animazione mancante dalla raccolta standard è un'animazione a colori. Il problema è che non esiste un modo `Color` giusto per interpolare tra due valori. È possibile interpolare i singoli valori RGB, ma altrettanto valido è l'interpolazione dei valori HSL.

Per [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) questo motivo, la classe nella libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene due `Color` metodi di animazione: [`RgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166) e [`HslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188). (Ci sono anche due [`CancelRgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) [`CancelHslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206)metodi di annullamento: e ).

Entrambi i metodi [`ColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211)utilizzano , che esegue [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) l'animazione chiamando l'ampio metodo generico in [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions).

Il [**ColorAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) esempio viene illustrato l'utilizzo di questi due tipi di animazioni di colore.

## <a name="structuring-your-animations"></a>Strutturare le animazioni

A volte è utile esprimere animazioni in XAML e usarle insieme a MVVM. Questo argomento è trattato nel capitolo successivo, [**capitolo 23. Trigger e comportamenti**](chapter23.md).

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 22 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Esempi del capitolo 22](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animazione](~/xamarin-forms/user-interface/animation/index.md)
