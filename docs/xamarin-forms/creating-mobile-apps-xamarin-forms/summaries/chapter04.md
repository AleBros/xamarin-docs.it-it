---
title: Riassunto del capitolo 4. Scorrimento dello stack
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 4. Scorrimento dello stack'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: bda9d5cb323524981bed9c3bb55998513dd69aab
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73032868"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Riassunto del capitolo 4. Scorrimento dello stack

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

Questo capitolo è dedicato principalmente all'introduzione del concetto di *layout*, che è il termine generale per le classi e le tecniche utilizzate da Xamarin.Forms per organizzare la visualizzazione di più visualizzazioni nella pagina.

Il layout coinvolge diverse [`Layout`](xref:Xamarin.Forms.Layout) [`Layout<T>`](xref:Xamarin.Forms.Layout`1)classi che derivano da e . Questo capitolo è [`StackLayout`](xref:Xamarin.Forms.StackLayout)incentrato su .

> [!NOTE]
> L'introduzione [`FlexLayout`](~/xamarin-forms/user-interface/layouts/flex-layout.md) in Xamarin.Forms 3.0 può essere `StackLayout` utilizzato in modi simili ma con maggiore flessibilità.

In questo capitolo sono [`ScrollView`](xref:Xamarin.Forms.ScrollView) [`Frame`](xref:Xamarin.Forms.Frame)state [`BoxView`](xref:Xamarin.Forms.BoxView) introdotte anche le classi , e .

## <a name="stacks-of-views"></a>Stack di viste

[`StackLayout`](xref:Xamarin.Forms.StackLayout)deriva da `Layout<View>` ed eredita [`Children`](xref:Xamarin.Forms.Layout`1) una `IList<View>`proprietà di tipo . Aggiungere più elementi visualizzazione a `StackLayout` questa raccolta e visualizzarli in una pila orizzontale o verticale.

Impostare [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) la `StackLayout` proprietà di [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) su un [`Vertical`](xref:Xamarin.Forms.StackOrientation.Vertical) [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal)membro dell'enumerazione, o . Il valore predefinito è `Vertical`.

Impostare [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) la `StackLayout` proprietà `double` di su un valore per specificare una spaziatura tra gli elementi figlio. Il valore predefinito è 6.

Nel codice è possibile aggiungere `Children` elementi `StackLayout` alla `for` `foreach` raccolta di in un ciclo o come illustrato `Children` nell'esempio [**ColorLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) oppure inizializzare la raccolta con un elenco delle singole visualizzazioni, come illustrato in [**ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). Gli elementi figlio `View` devono derivare da ma possono includere altri `StackLayout` oggetti.

## <a name="scrolling-content"></a>Scorrimento del contenuto

Se `StackLayout` un oggetto contiene troppi elementi figlio da `StackLayout` visualizzare [`ScrollView`](xref:Xamarin.Forms.ScrollView) in una pagina, è possibile inserire l'oggetto in a per consentire lo scorrimento.

Impostare [`Content`](xref:Xamarin.Forms.ScrollView.Content) la `ScrollView` proprietà della visualizzazione che si desidera scorrere. Si tratta `StackLayout`spesso di un oggetto , ma può essere qualsiasi vista.

Impostare [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) la `ScrollView` proprietà di [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) su [`Vertical`](xref:Xamarin.Forms.ScrollOrientation.Vertical)un [`Horizontal`](xref:Xamarin.Forms.ScrollOrientation.Horizontal)membro [`Both`](xref:Xamarin.Forms.ScrollOrientation.Both)della proprietà , , , o . Il valore predefinito è `Vertical`. Se il contenuto `ScrollView` di `StackLayout`a è un , i due orientamenti devono essere coerenti.

[**Nell'esempio ReflectedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) viene `ScrollView` `StackLayout` illustrato l'utilizzo di e per visualizzare i colori disponibili. Nell'esempio viene inoltre illustrato come utilizzare la reflection .NET `Color` per ottenere tutte le proprietà statiche pubbliche e i campi della struttura senza la necessità di elencarli in modo esplicito.

## <a name="the-expands-option"></a>L'opzione Espandi

Quando `StackLayout` un elemento figlio esegue lo stack dei relativi elementi `StackLayout` figlio, ogni elemento figlio occupa un `HorizontalOptions` particolare `VerticalOptions` slot all'interno dell'altezza totale che dipende dalle dimensioni dell'elemento figlio e dalle impostazioni delle relative proprietà e . A queste proprietà vengono [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions)assegnati valori di tipo .

La `LayoutOptions` struttura definisce due proprietà:

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment)[`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment) del tipo di enumerazione [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start) [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center)con [`End`](xref:Xamarin.Forms.LayoutAlignment.End)quattro membri, , , e[`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)di tipo`bool`

Per comodità, `LayoutOptions` la struttura definisce anche otto `LayoutOptions` campi statici di sola lettura di tipo che comprendono tutte le combinazioni delle due proprietà di istanza:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

La discussione `StackLayout` seguente prevede un con un orientamento verticale predefinito. L'orizzontale `StackLayout` è analogo.

Per un `StackLayout`oggetto `HorizontalOptions` verticale , l'impostazione determina il `StackLayout`modo in cui un elemento figlio viene posizionato orizzontalmente all'interno della larghezza dell'oggetto . `Alignment` Un'impostazione `Center`di `End` `Start`, o fa sì che l'elemento figlio sia orizzontalmente non vincolato. Il figlio determina la propria larghezza ed è posizionato a `StackLayout`sinistra, al centro o a destra del file . L'opzione `Fill` fa sì che il figlio sia `StackLayout`vincolato orizzontalmente e riempie la larghezza del file .

Per un `StackLayout`oggetto verticale, ogni elemento figlio è verticalmente non vincolato e `VerticalOptions` ottiene uno slot verticale a seconda dell'altezza del bambino, nel qual caso l'impostazione è irrilevante.

Se la `StackLayout` verticale&mdash;stessa non `VerticalOptions` è `Start`vincolata, ovvero se l'impostazione è , `Center`, o `End`, l'altezza dell'oggetto `StackLayout` è l'altezza totale dei relativi elementi figlio.

Tuttavia, se `StackLayout` il verticale è vincolato&mdash;verticalmente se `VerticalOptions` l'impostazione `Fill` &mdash;è quindi l'altezza del `StackLayout` sarà l'altezza del suo contenitore, che potrebbe essere maggiore dell'altezza totale dei suoi figli. In questo caso, e se almeno un `VerticalOptions` elemento `Expands` figlio `true`ha un'impostazione `StackLayout` con un flag di , `Expands` lo `true`spazio aggiuntivo nell'oggetto viene allocato equamente tra tutti quegli elementi figlio con un flag di . L'altezza totale degli elementi figlio `StackLayout`sarà quindi `Alignment` uguale `VerticalOptions` all'altezza di , e la parte dell'impostazione determina il modo in cui l'elemento figlio viene posizionato verticalmente nel relativo slot.

Ciò è illustrato nel [**VerticalOptionsDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo) esempio.

## <a name="frame-and-boxview"></a>Frame e BoxView

Queste due viste rettangolari sono spesso utilizzate per scopi di presentazione.

La [`Frame`](xref:Xamarin.Forms.Frame) vista mostra una cornice rettangolare attorno a `StackLayout`un'altra vista, che può essere un layout come . `Frame`eredita una [`Content`](xref:Xamarin.Forms.ContentView.Content) proprietà [`ContentView`](xref:Xamarin.Forms.ContentView) dall'impostazione della visualizzazione da `Frame`visualizzare all'interno dell'oggetto . L'oggetto `Frame` è trasparente per impostazione predefinita. Impostare le tre proprietà seguenti per personalizzare l'aspetto della cornice:

- Proprietà [`OutlineColor`](xref:Xamarin.Forms.Frame.OutlineColor) per renderla visibile. È comune `OutlineColor` impostare `Color.Accent` su quando non si conosce la combinazione di colori sottostante.
- La [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) proprietà può `true` essere impostata su per visualizzare un'ombreggiatura nera nei dispositivi iOS.The property can be set to display a black shadow on iOS devices.
- Impostate [`Padding`](xref:Xamarin.Forms.Layout.Padding) la `Thickness` proprietà su un valore per lasciare uno spazio tra il frame e il contenuto del frame. Il valore di default è 20 unità su tutti i lati.

Il `Frame` ha `HorizontalOptions` `VerticalOptions` default `LayoutOptions.Fill`e valori di `Frame` , il che significa che l'oggetto riempirà il relativo contenitore. Con altre impostazioni, la `Frame` dimensione del si basa sulla dimensione del suo contenuto.

L'oggetto viene illustrato nell'esempio `Frame` [**FramedText.The**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText) is demonstrated in the FramedText sample.

Visualizza [`BoxView`](xref:Xamarin.Forms.BoxView) un'area rettangolare di [`Color`](xref:Xamarin.Forms.BoxView.Color) colore specificata dalla relativa proprietà.

Se `BoxView` l'oggetto `HorizontalOptions` è `VerticalOptions` vincolato (le `LayoutOptions.Fill`proprietà `BoxView` e le sue hanno le impostazioni predefinite di ), lo spazio disponibile. Se `BoxView` l'oggetto non `HorizontalOptions` `LayoutOptions` è `Start`vincolato (con e le impostazioni di , `Center`, o `End`), ha una dimensione predefinita di 40 unità quadrate. Un `BoxView` oggetto può essere vincolato in una dimensione e non vincolato nell'altra.

Spesso, si impostano [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) le `BoxView` proprietà e di per assegnargli una dimensione specifica. Ciò è illustrato dall'esempio [**SizedBoxView.This**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView) is illustrated by the SizedBoxView sample.

È possibile utilizzare `StackLayout` diverse istanze di per combinare un `BoxView` e più `Label` `Frame` istanze in un `StackLayout` per `ScrollView` visualizzare un colore particolare e quindi inserire ognuna di queste viste in un in un per creare l'elenco attraente di colori mostrato nel [**ColorBlocks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks) esempio:

[![Tripla schermata dei blocchi di colore](images/ch04fg11-small.png "Elenco dei colori")](images/ch04fg11-large.png#lightbox "Elenco dei colori")

## <a name="a-scrollview-in-a-stacklayout"></a>Oggetto ScrollView in un StackLayout?

Mettere `StackLayout` un `ScrollView` in a è `ScrollView` comune, `StackLayout` ma mettere un in a è anche a volte conveniente. In teoria, questo non dovrebbe essere possibile `StackLayout` perché i figli di una verticale sono verticalmente non vincolati. Ma `ScrollView` un deve essere vincolato verticalmente. Deve essere assegnata un'altezza specifica in modo che possa quindi determinare la dimensione dell'elemento figlio per lo scorrimento.

Il trucco è `ScrollView` quello `StackLayout` di `VerticalOptions` dare `FillAndExpand`al bambino di un'impostazione di . Ciò è dimostrato nell'esempio [**BlackCat.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat)

Nell'esempio **BlackCat** viene inoltre illustrato come definire e accedere alle risorse del programma incorporate nella libreria condivisa. Questo può essere ottenuto anche con Shared Asset Projects (SAP), ma il processo è un po 'più complicato, come dimostra l'esempio [**BlackCatSap.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap)

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 4 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Esempi del capitolo 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Capitolo 4 Esempi di F](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
