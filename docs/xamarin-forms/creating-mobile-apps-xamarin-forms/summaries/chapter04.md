---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 4. Scrolling the stack''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9890ecdf286ee33d7af28fd20847788b063df8f5
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136890"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Riepilogo del capitolo 4. Scorrimento dello stack

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

Questo capitolo è destinato principalmente all'introduzione del concetto di *layout*, che rappresenta il termine generale per le classi e le tecniche Xamarin.Forms utilizzate da per organizzare la visualizzazione visiva di più visualizzazioni nella pagina.

Il layout include diverse classi che derivano da [`Layout`](xref:Xamarin.Forms.Layout) e [`Layout<T>`](xref:Xamarin.Forms.Layout`1) . Questo capitolo è incentrato su [`StackLayout`](xref:Xamarin.Forms.StackLayout) .

> [!NOTE]
> La [`FlexLayout`](~/xamarin-forms/user-interface/layouts/flex-layout.md) introdotta in Xamarin.Forms 3,0 può essere usata in modi simili a `StackLayout` , ma con maggiore flessibilità.

Sono stati introdotti anche in questo capitolo le [`ScrollView`](xref:Xamarin.Forms.ScrollView) [`Frame`](xref:Xamarin.Forms.Frame) classi, e [`BoxView`](xref:Xamarin.Forms.BoxView) .

## <a name="stacks-of-views"></a>Stack di visualizzazioni

[`StackLayout`](xref:Xamarin.Forms.StackLayout)deriva da `Layout<View>` ed eredita una [`Children`](xref:Xamarin.Forms.Layout`1) proprietà di tipo `IList<View>` . È possibile aggiungere più elementi della visualizzazione a questa raccolta e `StackLayout` visualizzarli in uno stack orizzontale o verticale.

Impostare la [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) proprietà di `StackLayout` su un membro dell' [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) enumerazione, [`Vertical`](xref:Xamarin.Forms.StackOrientation.Vertical) ovvero o [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal) . Il valore predefinito è `Vertical`.

Impostare la [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) proprietà di `StackLayout` su un `double` valore per specificare una spaziatura tra gli elementi figlio. Il valore predefinito è 6.

Nel codice è possibile aggiungere elementi alla `Children` raccolta di `StackLayout` in un `for` `foreach` ciclo o, come illustrato nell'esempio [**ColorLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) , oppure è possibile inizializzare la `Children` raccolta con un elenco di visualizzazioni singole, come illustrato in [**color**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList)List. Gli elementi figlio devono derivare da `View` ma possono includere altri `StackLayout` oggetti.

## <a name="scrolling-content"></a>Scorrimento del contenuto

Se un oggetto `StackLayout` contiene troppi elementi figlio da visualizzare in una pagina, è possibile inserire `StackLayout` in un oggetto [`ScrollView`](xref:Xamarin.Forms.ScrollView) per consentire lo scorrimento.

Impostare la [`Content`](xref:Xamarin.Forms.ScrollView.Content) proprietà di sulla `ScrollView` visualizzazione che si desidera scorrere. Si tratta spesso di un `StackLayout` , ma può trattarsi di qualsiasi visualizzazione.

Impostare la [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) proprietà di `ScrollView` su un membro della [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) proprietà,, [`Vertical`](xref:Xamarin.Forms.ScrollOrientation.Vertical) [`Horizontal`](xref:Xamarin.Forms.ScrollOrientation.Horizontal) o [`Both`](xref:Xamarin.Forms.ScrollOrientation.Both) . Il valore predefinito è `Vertical`. Se il contenuto di un oggetto `ScrollView` è un `StackLayout` , i due orientamenti devono essere coerenti.

Nell'esempio [**ReflectedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) viene illustrato l'utilizzo di `ScrollView` e `StackLayout` per visualizzare i colori disponibili. Nell'esempio viene inoltre illustrato come utilizzare la reflection .NET per ottenere tutte le proprietà statiche pubbliche e i campi della `Color` struttura senza la necessità di elencarli in modo esplicito.

## <a name="the-expands-option"></a>L'opzione expands

Quando un oggetto `StackLayout` impila gli elementi figlio, ogni elemento figlio occupa uno slot specifico nell'altezza totale di `StackLayout` che dipende dalle dimensioni del figlio e dalle impostazioni delle `HorizontalOptions` `VerticalOptions` proprietà e. A queste proprietà vengono assegnati valori di tipo [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) .

La `LayoutOptions` struttura definisce due proprietà:

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment)del tipo di enumerazione [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment) con quattro membri,,, [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start) [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center) [`End`](xref:Xamarin.Forms.LayoutAlignment.End) e[`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)di tipo`bool`

Per praticità, la `LayoutOptions` struttura definisce anche otto campi statici di sola lettura di tipo `LayoutOptions` che includono tutte le combinazioni delle due proprietà dell'istanza:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

La discussione seguente include un oggetto `StackLayout` con orientamento verticale predefinito. Il piano orizzontale `StackLayout` è analogo.

Per un oggetto verticale `StackLayout` , l' `HorizontalOptions` impostazione determina il modo in cui un elemento figlio è posizionato orizzontalmente all'interno della larghezza di `StackLayout` . Un' `Alignment` impostazione di `Start` , `Center` o `End` fa in modo che l'elemento figlio non venga vincolato orizzontalmente. L'elemento figlio determina la propria larghezza ed è posizionato a sinistra, al centro o a destra di `StackLayout` . L' `Fill` opzione fa sì che l'elemento figlio sia vincolato orizzontalmente e riempia la larghezza di `StackLayout` .

Per un oggetto verticale `StackLayout` , ogni elemento figlio viene non vincolato verticalmente e ottiene uno slot verticale a seconda dell'altezza del figlio, nel qual caso l' `VerticalOptions` impostazione è irrilevante.

Se il valore della proprietà Vertical `StackLayout` è non vincolato, &mdash; ovvero se l' `VerticalOptions` impostazione è `Start` , `Center` o `End` , l'altezza di `StackLayout` è l'altezza totale dei relativi elementi figlio.

Tuttavia, se la verticale `StackLayout` è vincolata verticalmente &mdash; se l' `VerticalOptions` impostazione è `Fill` &mdash; , l'altezza di `StackLayout` sarà l'altezza del relativo contenitore, che potrebbe essere maggiore dell'altezza totale dei figli. In tal caso e se almeno un elemento figlio dispone di un' `VerticalOptions` impostazione con un `Expands` flag di `true` , lo spazio aggiuntivo nell'oggetto `StackLayout` viene allocato equamente tra tutti gli elementi figlio con un `Expands` flag `true` . L'altezza totale dei figli sarà quindi uguale all'altezza di `StackLayout` e la `Alignment` parte dell' `VerticalOptions` impostazione determina il modo in cui l'elemento figlio viene posizionato verticalmente nello slot.

Questa operazione è illustrata nell'esempio [**VerticalOptionsDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo) .

## <a name="frame-and-boxview"></a>Frame e BoxView

Queste due visualizzazioni rettangolari vengono spesso utilizzate a scopo di presentazione.

La [`Frame`](xref:Xamarin.Forms.Frame) vista consente di visualizzare una cornice rettangolare intorno a un'altra visualizzazione, che può essere un layout, ad esempio `StackLayout` . `Frame`eredita una [`Content`](xref:Xamarin.Forms.ContentView.Content) proprietà da [`ContentView`](xref:Xamarin.Forms.ContentView) impostata sulla visualizzazione da visualizzare all'interno di `Frame` . `Frame`Per impostazione predefinita, è trasparente. Impostare le tre proprietà seguenti per personalizzare l'aspetto del frame:

- [`OutlineColor`](xref:Xamarin.Forms.Frame.OutlineColor)Proprietà per renderla visibile. È normale impostare `OutlineColor` su `Color.Accent` quando non si conosce la combinazione di colori sottostante.
- La [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) proprietà può essere impostata su `true` per visualizzare un'ombreggiatura nera nei dispositivi iOS.
- Impostare la [`Padding`](xref:Xamarin.Forms.Layout.Padding) proprietà su un `Thickness` valore per lasciare uno spazio tra il frame e il contenuto del frame. Il valore predefinito è 20 unità su tutti i lati.

Il `Frame` valore di è e il valore predefinito è `HorizontalOptions` `VerticalOptions` `LayoutOptions.Fill` , che indica che `Frame` riempirà il relativo contenitore. Con altre impostazioni, le dimensioni di `Frame` si basano sulle dimensioni del contenuto.

`Frame`Viene illustrato nell'esempio [**FramedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText) .

[`BoxView`](xref:Xamarin.Forms.BoxView)Visualizza un'area rettangolare di colore specificata dalla relativa [`Color`](xref:Xamarin.Forms.BoxView.Color) Proprietà.

Se `BoxView` è vincolato (le relative `HorizontalOptions` proprietà e `VerticalOptions` hanno le impostazioni predefinite di `LayoutOptions.Fill` ), `BoxView` riempie lo spazio disponibile. Se l'oggetto `BoxView` non è vincolato ( `HorizontalOptions` con `LayoutOptions` le impostazioni e di `Start` , `Center` o `End` ), ha una dimensione predefinita di 40 unità quadrate. Un oggetto `BoxView` può essere vincolato in una dimensione e non vincolato nell'altro.

Spesso è necessario impostare le [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) proprietà e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) di `BoxView` per assegnarle una dimensione specifica. Questa operazione è illustrata nell'esempio [**SizedBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView) .

È possibile utilizzare diverse istanze di `StackLayout` per combinare una `BoxView` e più `Label` istanze di un oggetto `Frame` per visualizzare un colore specifico e quindi inserire ognuna di queste viste in un oggetto `StackLayout` in un oggetto `ScrollView` per creare l'elenco di colori interessante visualizzato nell'esempio [**ColorBlocks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks) :

[![Schermata tripla dei blocchi di colore](images/ch04fg11-small.png "Elenco di colori")](images/ch04fg11-large.png#lightbox "Elenco di colori")

## <a name="a-scrollview-in-a-stacklayout"></a>Un ScrollView in una StackLayout?

L'inserimento di un oggetto `StackLayout` in un oggetto `ScrollView` è comune, ma anche l'inserimento `ScrollView` di un in un oggetto `StackLayout` può risultare utile. In teoria, questo non dovrebbe essere possibile perché gli elementi figlio di un oggetto verticale `StackLayout` sono non vincolati verticalmente. Un oggetto `ScrollView` deve essere tuttavia vincolato verticalmente. Deve disporre di un'altezza specifica in modo da poter determinare le dimensioni del relativo figlio per lo scorrimento.

Il trucco consiste nel fornire al `ScrollView` figlio di `StackLayout` un' `VerticalOptions` impostazione di `FillAndExpand` . Questa operazione è illustrata nell'esempio [**BlackCat**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat) .

Nell'esempio **BlackCat** viene inoltre illustrato come definire e accedere alle risorse del programma incorporate nella libreria condivisa. Questa operazione può essere eseguita anche con i progetti di asset condivisi (SAP), ma il processo è leggermente più complicato, come illustrato nell'esempio [**BlackCatSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap) .

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 4 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Esempi del capitolo 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Esempi del capitolo 4 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
