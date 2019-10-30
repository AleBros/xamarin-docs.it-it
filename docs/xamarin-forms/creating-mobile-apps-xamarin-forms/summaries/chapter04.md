---
title: Riepilogo del capitolo 4. Scorrimento dello stack
description: 'Creazione di app per dispositivi mobili con Novell. Forms: riepilogo del capitolo 4. Scorrimento dello stack'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: bda9d5cb323524981bed9c3bb55998513dd69aab
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032868"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Riepilogo del capitolo 4. Scorrimento dello stack

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

Questo capitolo è destinato principalmente all'introduzione del concetto di *layout*, che rappresenta il termine generale per le classi e le tecniche utilizzate da Novell. Forms per organizzare la visualizzazione visiva di più visualizzazioni nella pagina.

Il layout prevede diverse classi che derivano da [`Layout`](xref:Xamarin.Forms.Layout) e [`Layout<T>`](xref:Xamarin.Forms.Layout`1). Questo capitolo è incentrato sulla [`StackLayout`](xref:Xamarin.Forms.StackLayout).

> [!NOTE]
> La [`FlexLayout`](~/xamarin-forms/user-interface/layouts/flex-layout.md) introdotta in Novell. forms 3,0 può essere usata in modi simili a `StackLayout` ma con maggiore flessibilità.

Sono stati introdotti anche in questo capitolo le classi [`ScrollView`](xref:Xamarin.Forms.ScrollView), [`Frame`](xref:Xamarin.Forms.Frame)e [`BoxView`](xref:Xamarin.Forms.BoxView) .

## <a name="stacks-of-views"></a>Stack di visualizzazioni

[`StackLayout`](xref:Xamarin.Forms.StackLayout) deriva da `Layout<View>` ed eredita una proprietà [`Children`](xref:Xamarin.Forms.Layout`1) di tipo `IList<View>`. Si aggiungono più elementi della visualizzazione a questa raccolta e `StackLayout` li visualizza in uno stack orizzontale o verticale.

Impostare la proprietà [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) di `StackLayout` su un membro dell'enumerazione [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) , [`Vertical`](xref:Xamarin.Forms.StackOrientation.Vertical) o [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal). Il valore predefinito è `Vertical`.

Impostare la proprietà [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) di `StackLayout` su un valore `double` per specificare una spaziatura tra gli elementi figlio. Il valore predefinito è 6.

Nel codice è possibile aggiungere elementi alla raccolta di `Children` di `StackLayout` in un ciclo di `for` o `foreach`, come illustrato nell'esempio [**ColorLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) , oppure è possibile inizializzare la raccolta di `Children` con un elenco di visualizzazioni singole, come illustrato in [**color List** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). Gli elementi figlio devono derivare da `View` ma possono includere altri oggetti `StackLayout`.

## <a name="scrolling-content"></a>Scorrimento del contenuto

Se un `StackLayout` contiene troppi elementi figlio da visualizzare in una pagina, è possibile inserire i `StackLayout` in un [`ScrollView`](xref:Xamarin.Forms.ScrollView) per consentire lo scorrimento.

Impostare la proprietà [`Content`](xref:Xamarin.Forms.ScrollView.Content) di `ScrollView` sulla visualizzazione che si desidera scorrere. Si tratta spesso di un `StackLayout`, ma può trattarsi di qualsiasi visualizzazione.

Impostare la proprietà [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) di `ScrollView` su un membro della proprietà [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) , [`Vertical`](xref:Xamarin.Forms.ScrollOrientation.Vertical), [`Horizontal`](xref:Xamarin.Forms.ScrollOrientation.Horizontal)o [`Both`](xref:Xamarin.Forms.ScrollOrientation.Both). Il valore predefinito è `Vertical`. Se il contenuto di un `ScrollView` è un `StackLayout`, i due orientamenti devono essere coerenti.

Nell'esempio [**ReflectedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) viene illustrato l'utilizzo di `ScrollView` e `StackLayout` per visualizzare i colori disponibili. Nell'esempio viene inoltre illustrato come utilizzare la reflection .NET per ottenere tutte le proprietà statiche pubbliche e i campi della struttura `Color` senza la necessità di elencarli in modo esplicito.

## <a name="the-expands-option"></a>L'opzione expands

Quando un `StackLayout` impila gli elementi figlio, ogni elemento figlio occupa uno slot specifico nell'altezza totale del `StackLayout` che dipende dalle dimensioni del figlio e dalle impostazioni delle proprietà `HorizontalOptions` e `VerticalOptions`. A queste proprietà vengono assegnati valori di tipo [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions).

La struttura `LayoutOptions` definisce due proprietà:

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) del tipo di enumerazione [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment) con quattro membri, [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start), [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center), [`End`](xref:Xamarin.Forms.LayoutAlignment.End)e [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) di tipo `bool`

Per praticità, la struttura di `LayoutOptions` definisce anche otto campi statici di sola lettura di tipo `LayoutOptions` che includono tutte le combinazioni delle due proprietà dell'istanza:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

La discussione seguente include un `StackLayout` con un orientamento verticale predefinito. Il `StackLayout` orizzontale è analogo.

Per un `StackLayout`verticale, l'impostazione del `HorizontalOptions` determina il modo in cui un elemento figlio viene posizionato orizzontalmente nella larghezza della `StackLayout`. Un'impostazione `Alignment` di `Start`, `Center`o `End` causa la non vincolata orizzontale del figlio. L'elemento figlio determina la propria larghezza ed è posizionato a sinistra, al centro o a destra della `StackLayout`. L'opzione `Fill` fa in modo che l'elemento figlio sia vincolato orizzontalmente e riempia la larghezza del `StackLayout`.

Per un `StackLayout`verticale, ogni elemento figlio viene non vincolato verticalmente e ottiene uno slot verticale a seconda dell'altezza del figlio, nel qual caso l'impostazione del `VerticalOptions` è irrilevante.

Se la `StackLayout` verticale stessa è vincolata&mdash;che è se la relativa impostazione `VerticalOptions` è `Start`, `Center`o `End`, l'altezza del `StackLayout` è l'altezza totale dei relativi elementi figlio.

Tuttavia, se la `StackLayout` verticale è vincolata verticalmente&mdash;se la relativa impostazione `VerticalOptions` è `Fill`&mdash;, l'altezza del `StackLayout` sarà l'altezza del relativo contenitore, che potrebbe essere maggiore dell'altezza totale dei relativi elementi figlio. In tal caso e se almeno un elemento figlio ha un'impostazione di `VerticalOptions` con un flag di `Expands` di `true`, lo spazio aggiuntivo nel `StackLayout` verrà allocato equamente tra tutti gli elementi figlio con un flag di `Expands` `true`. L'altezza totale dei figli sarà quindi uguale all'altezza del `StackLayout`e la parte `Alignment` dell'impostazione `VerticalOptions` determina il modo in cui l'elemento figlio viene posizionato verticalmente nello slot.

Questa operazione è illustrata nell'esempio [**VerticalOptionsDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo) .

## <a name="frame-and-boxview"></a>Frame e BoxView

Queste due visualizzazioni rettangolari vengono spesso utilizzate a scopo di presentazione.

La visualizzazione [`Frame`](xref:Xamarin.Forms.Frame) Visualizza un frame rettangolare intorno a un'altra visualizzazione, che può essere un layout, ad esempio `StackLayout`. `Frame` eredita una proprietà [`Content`](xref:Xamarin.Forms.ContentView.Content) da [`ContentView`](xref:Xamarin.Forms.ContentView) impostata sulla visualizzazione da visualizzare all'interno del `Frame`. Per impostazione predefinita, il `Frame` è trasparente. Impostare le tre proprietà seguenti per personalizzare l'aspetto del frame:

- Proprietà [`OutlineColor`](xref:Xamarin.Forms.Frame.OutlineColor) per renderla visibile. È normale impostare `OutlineColor` su `Color.Accent` quando non si conosce la combinazione di colori sottostante.
- È possibile impostare la proprietà [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) su `true` per visualizzare un'ombreggiatura nera nei dispositivi iOS.
- Impostare la proprietà [`Padding`](xref:Xamarin.Forms.Layout.Padding) su un valore `Thickness` per lasciare uno spazio tra il frame e il contenuto del frame. Il valore predefinito è 20 unità su tutti i lati.

Il `Frame` dispone di valori predefiniti di `HorizontalOptions` e `VerticalOptions` di `LayoutOptions.Fill`, il che significa che il `Frame` riempirà il relativo contenitore. Con altre impostazioni, le dimensioni del `Frame` si basano sulle dimensioni del contenuto.

Il `Frame` è illustrato nell'esempio [**FramedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText) .

Il [`BoxView`](xref:Xamarin.Forms.BoxView) Visualizza un'area rettangolare di colore specificata dalla relativa proprietà [`Color`](xref:Xamarin.Forms.BoxView.Color) .

Se il `BoxView` è vincolato (le proprietà `HorizontalOptions` e `VerticalOptions` hanno le impostazioni predefinite di `LayoutOptions.Fill`), il `BoxView` riempie lo spazio disponibile. Se il `BoxView` non è vincolato (con `HorizontalOptions` e `LayoutOptions` le impostazioni di `Start`, `Center`o `End`), ha una dimensione predefinita di 40 unità quadrate. Un `BoxView` può essere vincolato in una dimensione e non vincolato nell'altro.

Spesso è necessario impostare le proprietà [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) di `BoxView` per assegnarle una dimensione specifica. Questa operazione è illustrata nell'esempio [**SizedBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView) .

È possibile utilizzare diverse istanze di `StackLayout` per combinare una `BoxView` e diverse istanze di `Label` in un `Frame` per visualizzare un colore specifico e quindi inserire ognuna di queste viste in un `StackLayout` in una `ScrollView` per creare l'elenco di colori interessante mostrata nell'esempio [**ColorBlocks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks) :

[![Schermata tripla dei blocchi di colore](images/ch04fg11-small.png "Elenco di colori")](images/ch04fg11-large.png#lightbox "Elenco di colori")

## <a name="a-scrollview-in-a-stacklayout"></a>Un ScrollView in una StackLayout?

L'inserimento di un `StackLayout` in un `ScrollView` è comune, ma anche l'inserimento di un `ScrollView` in un `StackLayout` è molto utile. In teoria, questo non dovrebbe essere possibile perché gli elementi figlio di un `StackLayout` verticale sono non vincolati verticalmente. Tuttavia, un `ScrollView` deve essere vincolato verticalmente. Deve disporre di un'altezza specifica in modo da poter determinare le dimensioni del relativo figlio per lo scorrimento.

Il trucco consiste nel fornire al `ScrollView` figlio del `StackLayout` un'impostazione `VerticalOptions` di `FillAndExpand`. Questa operazione è illustrata nell'esempio [**BlackCat**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat) .

Nell'esempio **BlackCat** viene inoltre illustrato come definire e accedere alle risorse del programma incorporate nella libreria condivisa. Questa operazione può essere eseguita anche con i progetti di asset condivisi (SAP), ma il processo è leggermente più complicato, come illustrato nell'esempio [**BlackCatSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap) .

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 4 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Esempi del capitolo 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Esempi del F# capitolo 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
