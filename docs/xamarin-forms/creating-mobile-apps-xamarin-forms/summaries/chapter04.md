---
title: Riepilogo del capitolo 4. Scorrimento dello stack
description: 'Creazione di app per dispositivi mobili con Novell. Forms: Riepilogo del capitolo 4. Scorrimento dello stack'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 66e4f52e87a4398dd2e09d2d128f43de9a71a665
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760834"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Riepilogo del capitolo 4. Scorrimento dello stack

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

In questo capitolo è principalmente dedicato per introdurre il concetto di *layout*, ovvero il termine generale per le classi e le tecniche che usa xamarin. Forms per organizzare la visualizzazione di più visualizzazioni nella pagina.

Layout comporta diverse classi che derivano da [ `Layout` ](xref:Xamarin.Forms.Layout) e [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1). Questo capitolo illustra [ `StackLayout` ](xref:Xamarin.Forms.StackLayout).

> [!NOTE]
> Il [ `FlexLayout` ](~/xamarin-forms/user-interface/layouts/flex-layout.md) introdotta in xamarin. Forms 3.0 sono utilizzabili in modi che sono simili a `StackLayout` ma con una maggiore flessibilità.

Inoltre introdotto in questo capitolo sono le [ `ScrollView` ](xref:Xamarin.Forms.ScrollView), [ `Frame` ](xref:Xamarin.Forms.Frame), e [ `BoxView` ](xref:Xamarin.Forms.BoxView) classi.

## <a name="stacks-of-views"></a>Stack di viste

[`StackLayout`](xref:Xamarin.Forms.StackLayout) deriva da `Layout<View>` ed eredita un [ `Children` ](xref:Xamarin.Forms.Layout`1) vlastnosti typu `IList<View>`. Si aggiungono più voci di visualizzazione per questa raccolta, e `StackLayout` li visualizza in uno stack orizzontale o verticale.

Impostare il [ `Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation) proprietà della `StackLayout` a un membro del [ `StackOrientation` ](xref:Xamarin.Forms.StackOrientation) enumerazione, entrambi [ `Vertical` ](xref:Xamarin.Forms.StackOrientation.Vertical) o [ `Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal). Il valore predefinito è `Vertical`.

Impostare il [ `Spacing` ](xref:Xamarin.Forms.StackLayout.Spacing) proprietà della `StackLayout` a un `double` valore da specificare una spaziatura tra gli elementi figlio. Il valore predefinito è 6.

Nel codice, è possibile aggiungere elementi per il `Children` raccolta di `StackLayout` in un `for` o `foreach` ciclo, come illustrato nel [ **ColorLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) esempio oppure è possibile inizializzare il `Children` insieme con un elenco delle singole viste come illustrato nella [ **ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). Gli elementi figlio devono derivare da `View` , ma può includere altri `StackLayout` oggetti.

## <a name="scrolling-content"></a>Scorrimento di contenuto

Se un `StackLayout` contiene troppi elementi figlio da visualizzare in una pagina, è possibile inserire il `StackLayout` in un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) per consentire lo scorrimento.

Impostare il [ `Content` ](xref:Xamarin.Forms.ScrollView.Content) proprietà della `ScrollView` alla visualizzazione di cui si desidera scorrere. Questo è spesso un `StackLayout`, ma può trattarsi di qualsiasi visualizzazione.

Impostare il [ `Orientation` ](xref:Xamarin.Forms.ScrollView.Orientation) proprietà del `ScrollView` a un membro del [ `ScrollOrientation` ](xref:Xamarin.Forms.ScrollOrientation) proprietà [ `Vertical` ](xref:Xamarin.Forms.ScrollOrientation.Vertical), [ `Horizontal` ](xref:Xamarin.Forms.ScrollOrientation.Horizontal), o [ `Both` ](xref:Xamarin.Forms.ScrollOrientation.Both). Il valore predefinito è `Vertical`. Se il contenuto di un `ScrollView` è un `StackLayout`, i due orientamenti dovrebbero essere coerenti.

Il [ **ReflectedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) esempio dimostra l'uso di `ScrollView` e `StackLayout` per visualizzare i colori disponibili. L'esempio illustra anche come usare la reflection .NET per ottenere tutte le proprietà statiche pubbliche e campi del `Color` struttura senza la necessità di elencarli in maniera esplicita.

## <a name="the-expands-option"></a>L'opzione viene espansa

Quando un `StackLayout` stack relativi elementi figlio, ogni elemento figlio occupa uno slot specifico all'interno dell'altezza totale del `StackLayout` che varia a seconda delle dimensioni del bambino e le impostazioni del relativo `HorizontalOptions` e `VerticalOptions` proprietà. Queste proprietà vengono assegnate i valori di tipo [ `LayoutOptions` ](http://developer.xamstage.com/api/type/Xamarin.Forms.LayoutOptions/).

Il `LayoutOptions` struttura definisce due proprietà:

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) del tipo di enumerazione [ `LayoutAlignment` ](xref:Xamarin.Forms.LayoutAlignment) con quattro membri [ `Start` ](xref:Xamarin.Forms.LayoutAlignment.Start), [ `Center` ](xref:Xamarin.Forms.LayoutAlignment.Center), [ `End` ](xref:Xamarin.Forms.LayoutAlignment.End), e [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) di tipo `bool`

Per praticità, la `LayoutOptions` struttura definisce anche otto campi statici con sola lettura di tipo `LayoutOptions` che comprende tutte le combinazioni delle proprietà dell'istanza di due:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

La discussione seguente comporta un `StackLayout` con orientamento verticale predefinito. Orizzontale `StackLayout` è analogo.

Per un parametro vertical `StackLayout`, il `HorizontalOptions` impostazione determina come un elemento figlio è posizionato orizzontalmente entro la larghezza del `StackLayout`. Un' `Alignment` impostazione `Start`, `Center`, o `End` fa sì che l'elemento figlio sia orizzontalmente non vincolato. L'elemento figlio determina la propria larghezza ed è posizionato in corrispondenza di sinistra, al centro o a destra del `StackLayout`. Il `Fill` opzione fa sì che l'elemento figlio da vincolare in senso orizzontale e riempie la larghezza del `StackLayout`.

Per un parametro vertical `StackLayout`, ogni elemento figlio è verticale non vincolato e ottiene un parametro vertical slot a seconda di altezza del bambino, nel qual caso il `VerticalOptions` impostazione non è rilevante.

Se verticale `StackLayout` stesso non è vincolato&mdash;vale a dire se relativi `VerticalOptions` impostazione è `Start`, `Center`, o `End`, quindi l'altezza del `StackLayout` corrisponde all'altezza totale dei relativi elementi figlio.

Tuttavia, se verticale `StackLayout` verticalmente è vincolato&mdash;se relativi `VerticalOptions` impostazione è `Fill` &mdash;quindi l'altezza del `StackLayout` sarà l'altezza del relativo contenitore, che può essere maggiore del totale altezza dei relativi elementi figlio. Se in questo caso e se almeno un elemento figlio è un `VerticalOptions` impostazione con un `Expands` flag di `true`, quindi lo spazio aggiuntivo nel `StackLayout` viene allocato in modo uniforme tra tutti gli elementi figlio con un `Expands` flag di `true`. L'altezza totale degli elementi figlio verrà quindi uguale all'altezza del `StackLayout`e il `Alignment` in parte il `VerticalOptions` impostazione determina come l'elemento figlio è posizionato verticalmente nello slot.

Questa funzionalità viene illustrata la [ **VerticalOptionsDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo) esempio.

## <a name="frame-and-boxview"></a>Frame e BoxView

Queste due viste rettangolare vengono spesso usate per scopi di presentazione.

Il [ `Frame` ](xref:Xamarin.Forms.Frame) Vista sono riportati un frame intorno a un'altra visualizzazione, che può essere, ad esempio un layout rettangolare `StackLayout`. `Frame` eredita un [ `Content` ](xref:Xamarin.Forms.ContentView.Content) proprietà dal [ `ContentView` ](xref:Xamarin.Forms.ContentView) che è impostato per la visualizzazione deve essere visualizzato all'interno di `Frame`. Il `Frame` è trasparente per impostazione predefinita. Impostare le seguenti tre proprietà per personalizzare l'aspetto del frame:

- Il [ `OutlineColor` ](xref:Xamarin.Forms.Frame.OutlineColor) proprietà per renderla visibile. È comune per impostare `OutlineColor` a `Color.Accent` quando non si conosce lo schema di colore sottostante.
- Il [ `HasShadow` ](xref:Xamarin.Forms.Frame.HasShadow) può essere impostata su `true` per visualizzare un'ombreggiatura di colore nera nei dispositivi iOS.
- Impostare il [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) proprietà su un `Thickness` contenuto del valore di lasciare uno spazio tra il frame e il frame. Il valore predefinito è 20 unità di misura su tutti i lati.

Il `Frame` ha un valore predefinito `HorizontalOptions` e `VerticalOptions` valori di `LayoutOptions.Fill`, che significa che il `Frame` compilerà il relativo contenitore. Con le altre impostazioni, le dimensioni del `Frame` si basa sulle dimensioni del relativo contenuto.

Il `Frame` viene illustrata la [ **FramedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText) esempio.

Il [ `BoxView` ](xref:Xamarin.Forms.BoxView) verrà visualizzata un'area rettangolare di colore specificato dai relativi [ `Color` ](xref:Xamarin.Forms.BoxView.Color) proprietà.

Se il `BoxView` è limitato (relativo `HorizontalOptions` e `VerticalOptions` proprietà hanno valori predefiniti dei `LayoutOptions.Fill`), il `BoxView` riempie lo spazio disponibile per tale. Se il `BoxView` non è vincolata (con `HorizontalOptions` e `LayoutOptions` le impostazioni del `Start`, `Center`, o `End`), ha una dimensione predefinita del quadrato 40 unità. Oggetto `BoxView` può essere vincolato in una dimensione e non vincolato in altro.

Spesso, si imposterà il [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) e [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) le proprietà di `BoxView` per assegnargli una dimensione specifica. Questo comportamento è illustrato il [ **SizedBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView) esempio.

È possibile usare più istanze di `StackLayout` per combinare un `BoxView` e diverse `Label` istanze in un `Frame` per visualizzare un determinato colore e quindi inserire ognuna di queste visualizzazioni in un `StackLayout` in un `ScrollView` per creare le interessanti elenco di colori nella [ **ColorBlocks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks) esempio:

[![Schermata triplo dei blocchi di colore](images/ch04fg11-small.png "elenco di colori")](images/ch04fg11-large.png#lightbox "elenco di colori")

## <a name="a-scrollview-in-a-stacklayout"></a>ScrollView in un StackLayout?

Inserimento di un `StackLayout` in un `ScrollView` è comune, ma l'inserimento una `ScrollView` in un `StackLayout` talvolta è utile. In teoria, questo non dovrebbe essere possibile in quanto gli elementi figlio di un parametro vertical `StackLayout` sono verticalmente non vincolato. Ma un `ScrollView` deve essere vincolato in senso verticale. È necessario assegnargli un'altezza specifica in modo che possa quindi determinare la dimensione del relativo elemento figlio per lo scorrimento.

Lo stratagemma consiste nell'assegnare il `ScrollView` figlio il `StackLayout` una `VerticalOptions` impostazione di `FillAndExpand`. Questa funzionalità viene illustrata la [ **BlackCat** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat) esempio.

Il **BlackCat** esempio viene inoltre illustrato come definire e accedere alle risorse di programma che sono incorporate nella libreria condivisa. Ciò può anche essere ottenuto con progetti di Asset condivisi (SAPs) ma il processo è un po' più complicato, come le [ **BlackCatSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap) esempio viene illustrato.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 4 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Esempi di capitolo 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Capitolo 4 F# esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
