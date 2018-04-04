---
title: Riepilogo del capitolo 4. Lo stack di scorrimento
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 09a9d21b7979e0eb3f12d3b1207f2185e059f65c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Riepilogo del capitolo 4. Lo stack di scorrimento

Questo capitolo è dedicato principalmente per introdurre il concetto di *layout*, che è il termine generale per le classi e le tecniche utilizzate per organizzare la visualizzazione di più visualizzazioni nella pagina di xamarin. Forms.

Layout prevede diverse classi che derivano da [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) e [ `Layout<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/). Questo capitolo illustra [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/).

Sono stati introdotti in questo capitolo sono le [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/), [ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/), e [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) classi.

## <a name="stacks-of-views"></a>Stack di viste

[`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) deriva da `Layout<View>` ed eredita un [ `Children` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) proprietà di tipo `IList<View>`. Si aggiungono più voci di visualizzazione per questa raccolta, e `StackLayout` li visualizza in uno stack orizzontale o verticale.

Impostare il [ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Orientation/) proprietà di `StackLayout` a un membro del [ `StackOrientation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackOrientation/) enumerazione entrambi [ `Vertical` ](https://developer.xamarin.com/api/field/Xamarin.Forms.StackOrientation.Vertical/) o [ `Horizontal`](https://developer.xamarin.com/api/field/Xamarin.Forms.StackOrientation.Horizontal/). Il valore predefinito è `Vertical`.

Impostare il [ `Spacing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Spacing/) proprietà di `StackLayout` per un `double` valore per specificare una spaziatura tra gli elementi figlio. Il valore predefinito è 6.

Nel codice, è possibile aggiungere elementi per il `Children` insieme di `StackLayout` in un `for` o `foreach` ciclo, come illustrato nel [ **ColorLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) esempio oppure è possibile inizializzare il `Children` insieme con un elenco delle visualizzazioni singole come illustrato in [ **ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). Gli elementi figlio devono derivare da `View` ma può includere altri `StackLayout` oggetti.

## <a name="scrolling-content"></a>Scorrimento di contenuto

Se un `StackLayout` contiene troppi elementi figlio per visualizzare una pagina, è possibile inserire il `StackLayout` in un [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) per consentire lo scorrimento.

Impostare il [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Content/) proprietà `ScrollView` per la visualizzazione in cui si desidera scorrere. Questo è spesso un `StackLayout`, ma può essere qualsiasi visualizzazione.

Impostare il [ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Orientation/) proprietà di `ScrollView` a un membro del [ `ScrollOrientation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollOrientation/) proprietà [ `Vertical` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ScrollOrientation.Vertical/), [ `Horizontal` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ScrollOrientation.Horizontal/), o [ `Both` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ScrollOrientation.Both/). Il valore predefinito è `Vertical`. Se il contenuto di un `ScrollView` è un `StackLayout`, due orientamenti devono essere coerenti.

Il [ **ReflectedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) esempio viene illustrato l'utilizzo di `ScrollView` e `StackLayout` per visualizzare i colori disponibili. Nell'esempio viene inoltre illustrato come utilizzare la reflection .NET per ottenere tutte le proprietà statica pubblica e a tutti i campi del `Color` struttura senza la necessità per elencarli in modo esplicito.

## <a name="the-expands-option"></a>L'opzione viene espansa

Quando un `StackLayout` stack relativi elementi figlio, ogni elemento figlio occupa uno slot specifico all'interno dell'altezza totale del `StackLayout` che dipende dalla dimensione del bambino e le impostazioni del relativo `HorizontalOptions` e `VerticalOptions` proprietà. Queste proprietà vengono assegnate i valori di tipo [ `LayoutOptions` ](http://developer.xamstage.com/api/type/Xamarin.Forms.LayoutOptions/).

Il `LayoutOptions` struttura definisce due proprietà:

- [`Alignment`](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Alignment/) del tipo di enumerazione [ `LayoutAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutAlignment/) con quattro membri, [ `Start` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Start/), [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Center/), [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.End/), e [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Fill/)
- [`Expands`](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Expands/) di tipo `bool`

Per praticità, la `LayoutOptions` struttura definisce inoltre otto statici campi di sola lettura di tipo `LayoutOptions` che includono tutte le combinazioni delle proprietà dell'istanza di due:

- [`LayoutOptions.Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`LayoutOptions.Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`LayoutOptions.End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`LayoutOptions.StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`LayoutOptions.CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`LayoutOptions.EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`LayoutOptions.FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

La seguente discussione implica un `StackLayout` con orientamento verticale predefinito. Orizzontale `StackLayout` è analoga.

Per un oggetto verticale `StackLayout`, `HorizontalOptions` determina come un elemento figlio è posizionato orizzontalmente all'interno della larghezza del `StackLayout`. Un `Alignment` l'impostazione di `Start`, `Center`, o `End` figlio sia orizzontalmente senza vincoli. L'elemento figlio determina la propria larghezza ed è posizionato in corrispondenza di sinistra, al centro o destra del `StackLayout`. Il `Fill` opzione fa sì che l'elemento figlio di essere vincolato in senso orizzontale e riempie la larghezza del `StackLayout`.

Per un oggetto verticale `StackLayout`, ogni elemento figlio non è vincolata verticalmente e ottiene un oggetto verticale slot a seconda di altezza del bambino, nel qual caso il `VerticalOptions` impostazione non è rilevante.

Se verticale `StackLayout` stesso non è vincolata&mdash;vale a dire se relativo `VerticalOptions` impostazione è `Start`, `Center`, o `End`, quindi l'altezza del `StackLayout` è l'altezza totale dei relativi elementi figlio.

Tuttavia, se verticale `StackLayout` verticalmente è vincolato&mdash;se relativo `VerticalOptions` è `Fill` &mdash;quindi l'altezza del `StackLayout` sarà l'altezza del contenitore, potrebbe essere maggiore del totale altezza dei relativi elementi figlio. Se è questo il caso e se almeno un elemento figlio è un `VerticalOptions` impostazione con un `Expands` flag di `true`, quindi lo spazio aggiuntivo nel `StackLayout` viene allocato in modo uniforme tra tutti gli elementi figlio con un `Expands` flag di `true`. L'altezza totale degli elementi figlio verrà quindi uguale all'altezza del `StackLayout`e `Alignment` in parte il `VerticalOptions` determina come elemento figlio in senso verticale è posizionato nello slot.

Questa funzionalità viene illustrata la [ **VerticalOptionsDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo) esempio.

## <a name="frame-and-boxview"></a>Frame e BoxView

Queste due visualizzazioni rettangolare vengono spesso utilizzate per scopi di presentazione.

Il [ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/) Visualizza una cornice intorno a un'altra visualizzazione, che può essere, ad esempio un layout rettangolare `StackLayout`. `Frame` eredita un [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) proprietà [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) impostata per la visualizzazione da visualizzare all'interno di `Frame`. Il `Frame` è trasparente per impostazione predefinita. Impostare le seguenti tre proprietà per personalizzare l'aspetto del frame:

- Il [ `OutlineColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.OutlineColor/) proprietà per renderlo visibile. È comune impostare `OutlineColor` a `Color.Accent` quando non si conosce la combinazione di colori sottostante.
- Il [ `HasShadow` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.HasShadow/) può essere impostata su `true` per visualizzare un'ombreggiatura di colore nera nei dispositivi iOS.
- Impostare il [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) proprietà per un `Thickness` contenuto del valore per lasciare uno spazio tra il frame e il frame. Il valore predefinito è 20 unità su tutti i lati.

Il `Frame` predefinito `HorizontalOptions` e `VerticalOptions` valori di `LayoutOptions.Fill`, vale a dire che il `Frame` compilerà il relativo contenitore. Con altre impostazioni, le dimensioni del `Frame` si basa sulle dimensioni del relativo contenuto.

Il `Frame` viene illustrata la [ **FramedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText) esempio.

Il [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) consente di visualizzare un'area rettangolare di colore specificato dal relativo [ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) proprietà.

Se il `BoxView` è vincolato (relativo `HorizontalOptions` e `VerticalOptions` proprietà hanno valori predefiniti di `LayoutOptions.Fill`), il `BoxView` riempie lo spazio disponibile per tale. Se il `BoxView` vincolata (con `HorizontalOptions` e `LayoutOptions` le impostazioni di `Start`, `Center`, o `End`), ha una dimensione predefinita del quadrato 40 unità. Oggetto `BoxView` può essere vincolato in una dimensione e non vincolato in altro.

Spesso, verrà impostata l'il [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) e [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) le proprietà di `BoxView` per assegnargli una dimensione specifica. Questo comportamento è illustrato il [ **SizedBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView) esempio.

È possibile usare istanze diverse di `StackLayout` per combinare un `BoxView` e diversi `Label` le istanze in un `Frame` per visualizzare un determinato colore e quindi inserire ciascuna di queste visualizzazioni in un `StackLayout` in un `ScrollView` per creare l'interessante elenco di colori visualizzato nella finestra di [ **ColorBlocks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks) esempio:

[![Schermata di triplo dei blocchi di colore](images/ch04fg11-small.png "elenco di colori")](images/ch04fg11-large.png#lightbox "elenco di colori")

## <a name="a-scrollview-in-a-stacklayout"></a>Un elemento ScrollView in un StackLayout?

Inserimento di un `StackLayout` in un `ScrollView` è comune, ma la messa un `ScrollView` in un `StackLayout` talvolta è utile. In teoria, questo non dovrebbe essere possibile in quanto gli elementi figlio di un oggetto verticale `StackLayout` sono verticalmente non vincolato. Ma un `ScrollView` deve essere vincolato in senso verticale. È necessario assegnare un'altezza specifica in modo che è possibile quindi determinare la dimensione del relativo elemento figlio per lo scorrimento.

Lo stratagemma consiste nell'assegnare il `ScrollView` figlio di `StackLayout` un `VerticalOptions` l'impostazione di `FillAndExpand`. Questa funzionalità viene illustrata la [ **BlackCat** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat) esempio.

Il **BlackCat** esempio viene inoltre illustrato come definire e accedere alle risorse di programma che sono incorporate nella libreria di classe portabile (PCL). Possono essere visualizzato anche con i progetti di Asset condivisi (SAP), ma il processo è leggermente più complicato, come il [ **BlackCatSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap) viene illustrato l'esempio.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 4 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Esempi di capitolo 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Esempi di capitolo 4 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
