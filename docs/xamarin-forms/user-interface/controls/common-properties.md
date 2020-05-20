---
title: Proprietà, metodi ed eventi dei controlli comuni di Xamarin.Forms
description: Questo articolo descrive le proprietà, i metodi e gli eventi comuni definiti nella classe visualElement, comunemente utilizzati nelle classi derivate.
ms.prod: xamarin
ms.assetId: 85A0CCF5-C1D8-40BB-927F-A4D944E5534D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/21/2019
ms.openlocfilehash: 7d8f5309d14c2a99afff0998a7013a9ebec338c1
ms.sourcegitcommit: bc0c1740aa0708459729c0e671ab3ff7de3e2eee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/15/2020
ms.locfileid: "83425842"
---
# <a name="xamarinforms-common-control-properties-methods-and-events"></a>Proprietà, metodi ed eventi dei controlli comuni di Xamarin.Forms

La classe Novell. Forms `VisualElement` è la classe di base per la maggior parte dei controlli utilizzati in un'applicazione Novell. Forms. La `VisualElement` classe definisce numerose [Proprietà](#properties), [Metodi](#methods)ed [eventi](#events) utilizzati nelle classi derivate.

## <a name="properties"></a>Proprietà

Le proprietà seguenti sono disponibili nelle `VisualElement` istanze di. Per un elenco completo, vedere le [proprietà dell'API di visualElement](xref:Xamarin.Forms.VisualElement#properties).

### [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)

La `AnchorX` proprietà è un `double` valore che definisce il punto centrale sull'asse X per le trasformazioni, ad esempio la scala e la rotazione. Il valore predefinito è 0,5.

### [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

La `AnchorY` proprietà è un `double` valore che definisce il punto centrale sull'asse X per le trasformazioni, ad esempio la scala e la rotazione. Il valore predefinito è 0,5.

### [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)

La `BackgroundColor` proprietà è un oggetto `Color` che determina il colore di sfondo del controllo. Se non impostata, lo sfondo sarà l' `Color` oggetto predefinito, che viene visualizzato come trasparente.

### [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)

La `Behaviors` proprietà è un oggetto `List` di `Behavior` oggetti. I comportamenti consentono di alleghi le funzionalità riutilizzabili agli elementi aggiungendoli all' `Behaviors` elenco. Per ulteriori informazioni sulla `Behavior` classe, vedere [comportamenti di Novell. Forms](~/xamarin-forms/app-fundamentals/behaviors/index.md).

### [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)

La `Bounds` proprietà è un `Rectangle` oggetto di sola lettura che rappresenta lo spazio occupato dal controllo. Il `Bounds` valore della proprietà viene assegnato durante il ciclo di layout. `Rectangle` `struct` Contiene proprietà e metodi utili per il test dell'intersezione e del contenimento dei rettangoli. Per altre informazioni, vedere l' [API Rectangle Novell. Forms](xref:Xamarin.Forms.Rectangle).

### [`Effects`](xref:Xamarin.Forms.Element.Effects)

La `Effects` proprietà è un oggetto `List` di `Effect` oggetti, ereditato dalla `Element` classe (xrif: Novell. Forms. Element). Gli effetti consentono la personalizzazione dei controlli nativi e vengono in genere utilizzati per piccole modifiche di stile. Per ulteriori informazioni sulla `Effect` classe, vedere [Novell. Forms Effects](~/xamarin-forms/app-fundamentals/effects/index.md).

### [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)

La `FlowDirection` proprietà è un `FlowDirection` valore enum. La direzione del flusso può essere impostata su `MatchParent` , `LeftToRight` o `RightToLeft` e determina l'ordine e la direzione del layout. La `FlowDirection` proprietà viene in genere utilizzata per supportare lingue con lettura da destra a sinistra.

### [`Height`](xref:Xamarin.Forms.VisualElement.Height)

La `Height` proprietà è un `double` valore di sola lettura che descrive l'altezza di rendering del controllo. La `Height` proprietà viene calcolata durante il ciclo di layout e non può essere impostata direttamente. L'altezza di un controllo può essere richiesta utilizzando la [Proprietà HeightRequest](#heightrequest).

### [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)

La `HeightRequest` proprietà è un `double` valore che determina l'altezza desiderata del controllo. L'altezza assoluta del controllo potrebbe non corrispondere al valore richiesto. Per altre informazioni, vedere [proprietà della richiesta](#request-properties).

### [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent)

La `InputTransparent` proprietà è un oggetto `bool` che determina se il controllo riceve l'input dell'utente. Il valore predefinito è `false` , assicurando che l'elemento riceva input. Questa proprietà viene trasferita agli elementi figlio quando è impostata. Se si imposta la `InputTransparent` proprietà `true` su in una classe layout, tutti gli elementi all'interno del layout non riceveranno input.

### [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)

La `IsEnabled` proprietà è un `bool` valore che determina se il controllo reagisce all'input dell'utente. Il valore predefinito è `true`. Impostando questa proprietà su false si impedisce al controllo di accettare l'input dell'utente.

### [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused)

La `IsFocused` proprietà è un `bool` valore che descrive se il controllo è attualmente l'oggetto con lo stato attivo. Se si chiama il [`Focus`](#focus) metodo sul controllo `IsFocused` , il valore verrà impostato su true. Se si chiama il [`Unfocus`](#unfocus) metodo, questa proprietà viene impostata su false.

### [`IsTabStop`](xref:Xamarin.Forms.VisualElement.IsTabStop)

La `IsTabStop` proprietà è un `bool` valore che definisce se il controllo riceve lo stato attivo quando l'utente avanza attraverso i controlli con il tasto TAB. Se questa proprietà è false, la `TabIndex` proprietà non avrà alcun effetto.

### [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible)

La `IsVisible` proprietà è un `bool` valore che determina se viene eseguito il rendering del controllo. I controlli con la `IsVisible` proprietà impostata su false non verranno visualizzati e non verranno considerati per i calcoli dello spazio durante il ciclo di layout e non potranno accettare l'input dell'utente.

### [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)

La `MinimumHeightRequest` proprietà è un `double` valore che determina il modo in cui viene gestito l'overflow quando due elementi sono in competizione per lo spazio limitato. L'impostazione della `MinimumHeightRequest` proprietà consente al processo di layout di ridimensionare l'elemento fino alla dimensione minima richiesta. Se non `MinimumHeightRequest` viene specificato alcun valore, il valore predefinito è-1 e il processo di layout considererà come `HeightRequest` valore minimo. Questo significa che gli elementi senza `MinimumHeightRequest` valore non avranno altezza scalabile.

Per ulteriori informazioni, vedere [proprietà della richiesta minima](#minimum-request-properties).

### [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)

La `MinimumWidthRequest` proprietà è un `double` valore che determina il modo in cui viene gestito l'overflow quando due elementi sono in competizione per lo spazio limitato. L'impostazione della `MinimumWidthRequest` proprietà consente al processo di layout di ridimensionare l'elemento fino alla dimensione minima richiesta. Se non `MinimumWidthRequest` viene specificato alcun valore, il valore predefinito è-1 e il processo di layout considererà come `WidthRequest` valore minimo. Ciò significa che gli elementi senza `MinimumWidthRequest` valore non avranno larghezza scalabile.

Per ulteriori informazioni, vedere [proprietà della richiesta minima](#minimum-request-properties).

### [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)

La `Opacity` proprietà è un `double` valore compreso tra zero e uno che determina l'opacità del controllo durante il rendering. Il valore predefinito per questa proprietà è 1,0. I valori non compresi nell'intervallo compreso tra 0 e 1 verranno bloccati. La `Opacity` proprietà viene applicata solo se la [`IsVisible`](#isvisible) proprietà è `true` . L'opacità viene applicata in modo iterativo. Se pertanto un controllo padre ha un'opacità 0,5 e il relativo elemento figlio ha una opacità 0,5, viene eseguito il rendering dell'elemento figlio con un valore di opacità 0,25 effettivo. L'impostazione della `Opacity` proprietà di un controllo di input su 0 ha un comportamento non definito.

### [`Parent`](xref:Xamarin.Forms.Element.Parent)

La proprietà `Parent` viene ereditata dalla classe `Element`. Questa proprietà è un `Element` oggetto padre del controllo. La `Parent` proprietà viene in genere impostata automaticamente su un elemento quando viene aggiunta come figlio di un altro elemento.

### [`Resources`](xref:Xamarin.Forms.VisualElement.Resources)

La `Resources` proprietà è un' `ResourceDictionary` istanza popolata con coppie chiave/valore che vengono in genere popolate in fase di esecuzione da XAML. Questo dizionario consente agli sviluppatori di applicazioni di riusare gli oggetti definiti in XAML sia in fase di compilazione che in fase di esecuzione. Le chiavi nel dizionario vengono popolate dall' `x:Key` attributo del tag XAML. L'oggetto creato da XAML viene inserito in `ResourceDictionary` per la chiave specificata. una volta inizializzato.

Per altre informazioni, vedere [dizionari risorse](~/xamarin-forms/xaml/resource-dictionaries.md).

### [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)

La `Rotation` proprietà è un `double` valore compreso tra zero e 360 che definisce la rotazione in gradi dell'asse Z. Il valore predefinito di questa proprietà è 0. Viene applicata la rotazione relativa ai [`AnchorX`](#anchorx) [`AnchorY`](#anchory) valori e.

### [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)

La `RotationX` proprietà è un `double` valore compreso tra zero e 360 che definisce la rotazione sull'asse X in gradi. Il valore predefinito di questa proprietà è 0. Viene applicata la rotazione relativa ai [`AnchorX`](#anchorx) [`AnchorY`](#anchory) valori e.

### [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

La `RotationY` proprietà è un `double` valore compreso tra zero e 360 che definisce la rotazione sull'asse Y in gradi. Il valore predefinito di questa proprietà è 0. Viene applicata la rotazione relativa ai [`AnchorX`](#anchorx) [`AnchorY`](#anchory) valori e.

### [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)

La `Scale` proprietà è un `double` valore che definisce la scala del controllo. Il valore predefinito di questa proprietà è 1,0. Viene applicata la scala relativa ai [`AnchorX`](#anchorx) [`AnchorY`](#anchory) valori e.

### [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX)

La `ScaleX` proprietà è un `double` valore che definisce la scala del controllo lungo l'asse X. Il valore predefinito di questa proprietà è 1,0. La `ScaleX` proprietà viene applicata in relazione al [`AnchorX`](#anchorx) valore.

### [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY)

La `ScaleY` proprietà è un `double` valore che definisce la scala del controllo lungo l'asse Y. Il valore predefinito di questa proprietà è 1,0. La `ScaleY` proprietà viene applicata in relazione al [`AnchorY`](#anchory) valore.

### [`Style`](xref:Xamarin.Forms.NavigableElement.Style)

La proprietà `Style` viene ereditata dalla classe `NavigableElement`. Questa proprietà è un'istanza della `Style` classe. La `Style` classe contiene trigger, setter e comportamenti che definiscono l'aspetto e il comportamento degli elementi visivi. Per altre informazioni, vedere [Novell. Forms XAML Styles](~/xamarin-forms/user-interface/styles/xaml/index.md).

### [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass)

La `StyleClass` proprietà è un elenco di `string` oggetti che rappresentano i nomi delle `Style` classi. Questa proprietà viene ereditata dalla classe `NavigableElement`. La `StyleClass` proprietà consente di applicare più attributi di stile a un' `VisualElement` istanza di. Per altre informazioni, vedere [classi di stile Novell. Forms](~/xamarin-forms/user-interface/styles/xaml/style-class.md).

### [`TabIndex`](xref:Xamarin.Forms.VisualElement.TabIndex)

La `TabIndex` proprietà è un `int` valore che definisce l'ordine di controllo quando si avanzano i controlli con il tasto TAB. La `TabIndex` proprietà è l'implementazione per la proprietà definita nell' `ITabStopElement` interfaccia, `VisualElement` implementata dalla classe.

### [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)

La `TranslationX` proprietà è un `double` valore che definisce la traslazione delta da applicare sull'asse X. La traduzione viene applicata dopo il layout e viene in genere usata per l'applicazione di animazioni. La conversione di un elemento all'esterno dei limiti del contenitore padre impedisce il funzionamento degli input.

Per altre informazioni, vedere [Animation in Novell. Forms](~/xamarin-forms/user-interface/animation/index.md).

### [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)

La `TranslationY` proprietà è un `double` valore che definisce la traslazione delta da applicare sull'asse Y. La traduzione viene applicata dopo il layout e viene in genere usata per l'applicazione di animazioni. La conversione di un elemento all'esterno dei limiti del contenitore padre impedisce il funzionamento degli input.

Per altre informazioni, vedere [Animation in Novell. Forms](~/xamarin-forms/user-interface/animation/index.md).

### [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)

La `Triggers` proprietà è di sola lettura `List` di `TriggerBase` oggetti. I trigger consentono agli sviluppatori di applicazioni di esprimere azioni in XAML che modificano l'aspetto visivo dei controlli in risposta a modifiche a eventi o proprietà. Per altre informazioni, vedere [trigger Novell. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

### [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)

La `Visual` proprietà è un' `IVisual` istanza di che consente la creazione e l'applicazione selettiva dei renderer alle `VisualElement` istanze di. La `Visual` proprietà è impostata in modo da corrispondere al padre, quindi la definizione di un renderer in un componente verrà applicata anche a tutti gli elementi figlio di tale componente. Se non è impostato alcun renderer personalizzato per un controllo o i relativi predecessori, verrà usato il renderer Novell. Forms predefinito. Per altre informazioni, vedere [Novell. Forms Visual](~/xamarin-forms/user-interface/visual/index.md).

### [`Width`](xref:Xamarin.Forms.VisualElement.Width)

La `Width` proprietà è un `double` valore di sola lettura che descrive la larghezza di rendering del controllo. La `Width` proprietà viene calcolata durante il ciclo di layout e non può essere impostata direttamente. La larghezza di un controllo può essere richiesta utilizzando la [Proprietà WidthRequest](#widthrequest).

### [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)

La `WidthRequest` proprietà è un `double` valore che determina la larghezza desiderata del controllo. La larghezza assoluta del controllo potrebbe non corrispondere al valore richiesto. Per altre informazioni, vedere [proprietà della richiesta](#request-properties).

### [`X`](xref:Xamarin.Forms.VisualElement.X)

La `X` proprietà è un `double` valore di sola lettura che descrive la posizione X corrente del controllo.

### [`Y`](xref:Xamarin.Forms.VisualElement.Y)

La `Y` proprietà è un `double` valore di sola lettura che descrive la posizione Y corrente del controllo.

## <a name="methods"></a>Metodi

I metodi seguenti sono disponibili nella `VisualElement` classe. Per un elenco completo, vedere [metodi dell'API di visualElement](xref:Xamarin.Forms.VisualElement#methods).

### [`FindByName`](xref:Xamarin.Forms.Element.FindByName*)

Il `FindByName` metodo viene ereditato dalla `Element` classe e ha la firma seguente:

```csharp
public object FindByName (string name)
```

Questo metodo esegue la ricerca di tutti gli elementi figlio per l' `name` argomento fornito e restituisce l'elemento con il nome specificato. Se la ricerca ha esito negativo, viene restituito il valore `null`.

### [`Focus`](xref:Xamarin.Forms.VisualElement.Focus)

Il `Focus` metodo tenta di impostare lo stato attivo sull'elemento. Questo metodo ha la firma seguente:

```csharp
public bool Focus ()
```

Il `Focus` metodo restituisce `true` se lo stato attivo della tastiera è stato impostato correttamente e `false` se la chiamata al metodo non ha causato una modifica dello stato attivo. L'elemento deve essere in grado di ricevere lo stato attivo per il funzionamento di questo metodo. La chiamata al `Focus` metodo su elementi Offscreen o non realizzati presenta un comportamento non definito.

### [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus)

Il `Unfocus` metodo tenta di rimuovere lo stato attivo sull'elemento. Questo metodo ha la firma seguente:

```csharp
public void Unfocus ()
```

Per il corretto funzionamento di questo metodo, l'elemento deve avere già lo stato attivo.

## <a name="events"></a>Eventi

Nella classe sono disponibili gli eventi seguenti `VisualElement` . Per un elenco completo, vedere [eventi visivi di Novell. Forms](xref:Xamarin.Forms.VisualElement#events).

### [`Focused`](xref:Xamarin.Forms.VisualElement.Focused)

L' `Focused` evento viene generato ogni volta che l' `VisualElement` istanza riceve lo stato attivo. Questo evento non viene propagato attraverso lo stack Novell. Forms e viene ricevuto direttamente dal controllo nativo. Questo evento viene generato dal metodo di [`IsFocused`](#isfocused) impostazione della proprietà.

### [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

L' `SizeChanged` evento viene generato ogni volta che l' `VisualElement` istanza `Height` o le `Width` proprietà cambiano. Se gli sviluppatori desiderano rispondere direttamente alla modifica delle dimensioni, anziché rispondere all'evento post-modifica, devono invece implementare il [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated*) metodo virtuale.

### [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)

L' `Unfocused` evento viene generato ogni volta che l' `VisualElement` istanza perde lo stato attivo. Questo evento non viene propagato attraverso lo stack Novell. Forms e viene ricevuto direttamente dal controllo nativo. Questo evento viene generato dal metodo di [`IsFocused`](#isfocused) impostazione della proprietà.

## <a name="units-of-measurement"></a>Unità di misura

Le piattaforme Android, iOS e UWP dispongono di unità di misura diverse che possono variare tra i dispositivi. Novell. Forms usa un'unità di misura indipendente dalla piattaforma che normalizza le unità tra dispositivi e piattaforme. In Novell. Forms sono disponibili 160 unità per pollice o 64 unità per centimetro.

## <a name="request-properties"></a>Proprietà delle richieste

Le proprietà i cui nomi contengono "Request" definiscono un valore desiderato, che potrebbe non corrispondere al valore di cui è stato eseguito il rendering effettivo. Ad esempio, `HeightRequest` potrebbe essere impostato su 150, ma se il layout consente solo spazio per le unità 100, il rendering `Height` del controllo sarà 100. Le dimensioni sottoposte a rendering sono influenzate dallo spazio disponibile e dai componenti contenuti.

## <a name="minimum-request-properties"></a>Proprietà della richiesta minima

Le proprietà minime della richiesta includono `MinimumHeightRequest` e e `MinimumWidthRequest` sono progettate per consentire un controllo più preciso sul modo in cui gli elementi gestiscono l'overflow in relazione. Tuttavia, il comportamento del layout correlato a queste proprietà presenta alcune considerazioni importanti.

### <a name="unspecified-minimum-property-values"></a>Valori minimi non specificati per le proprietà

Se non è impostato un valore minimo, il valore predefinito della proprietà Minimum è-1. Il processo di layout ignora questo valore e considera il valore assoluto come minimo. La conseguenza pratica di questo comportamento è che un elemento senza un valore minimo specificato **non verrà** compattato. Un elemento con un valore minimo specificato **viene** compattato.

Il codice XAML seguente mostra due `BoxView` elementi in orizzontale `StackLayout` :

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView HeightRequest="100" BackgroundColor="Purple" WidthRequest="500"></BoxView>
    <BoxView HeightRequest="100" BackgroundColor="Green" WidthRequest="500" MinimumWidthRequest="250"></BoxView>
</StackLayout>
```

La prima `BoxView` istanza richiede una larghezza di 500 e non specifica una larghezza minima. La seconda `BoxView` istanza richiede una larghezza di 500 e una larghezza minima di 250. Se l' `StackLayout` elemento padre non è sufficientemente ampio da contenere entrambi i componenti alla larghezza richiesta, la prima `BoxView` istanza verrà considerata dal processo di layout per avere una larghezza minima di 500 perché non è specificato altro valore minimo valido. La seconda `BoxView` istanza è consentita per la riduzione fino a 250 e si ridurrà fino a raggiungere il raggiungimento della larghezza 250 unità.

Se il comportamento desiderato è che la prima `BoxView` istanza venga ridotta senza larghezza minima, è `MinimumWidthRequest` necessario impostare su un valore valido, ad esempio 0.

### <a name="minimum-and-absolute-property-values"></a>Valori delle proprietà minime ed assolute

Il comportamento non è definito quando il valore minimo è maggiore del valore assoluto. Se, ad esempio, `WidthRequest` è impostato su 100, la `MinimumWidthRequest` proprietà non deve mai superare 100. Quando si specifica un valore minimo per la proprietà, è necessario specificare sempre un valore assoluto per verificare che il valore assoluto sia maggiore del valore minimo.

### <a name="minimum-properties-within-a-grid"></a>Proprietà minime all'interno di una griglia

`Grid`i layout hanno il proprio sistema per il dimensionamento relativo di righe e colonne. `MinimumWidthRequest`L'utilizzo `MinimumHeightRequest` di o all'interno di un `Grid` layout non avrà alcun effetto. Per ulteriori informazioni, vedere [Novell. Forms Grid](~/xamarin-forms/user-interface/layouts/grid.md).

## <a name="related-links"></a>Collegamenti correlati

* [Documentazione dell'API di visualElement](xref:Xamarin.Forms.VisualElement)
