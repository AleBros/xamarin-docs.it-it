---
title: Riepilogo del capitolo 12. Stili
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 12. Stili'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: bb2cd1c97cc588923e0da1a8793f16445c111f0e
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058847"
---
# <a name="summary-of-chapter-12-styles"></a>Riepilogo del capitolo 12. Stili

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

In xamarin. Forms, gli stili di consentano più visualizzazioni condividere una raccolta di impostazioni delle proprietà. Ciò riduce markup e consente di mantenere coerenti temi visivi.

Gli stili sono quasi sempre definiti e utilizzati nel markup. Un oggetto di tipo [ `Style` ](xref:Xamarin.Forms.Style) viene creata un'istanza in un dizionario risorse e quindi impostare il [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) proprietà di un elemento visivo utilizzando un `StaticResource` o `DynamicResource` markup estensione.

## <a name="the-basic-style"></a>Lo stile di base

Oggetto `Style` richiede che il [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) da impostare per il tipo dell'oggetto visivo si applica a. Quando un `Style` viene creata un'istanza in un dizionario risorse (come avviene spesso) richiede inoltre un `x:Key` attributo.

Il `Style` ha una proprietà di contenuto di tipo [ `Setters` ](xref:Xamarin.Forms.Style.Setters), che è una raccolta di [ `Setter` ](xref:Xamarin.Forms.Setter) oggetti. Ciascuna `Setter` associa un [ `Property` ](xref:Xamarin.Forms.Setter.Property) con un [ `Value` ](xref:Xamarin.Forms.Setter.Value).

In XAML il `Property` impostazione è il nome di una proprietà CLR (ad esempio il `Text` proprietà di `Button`) ma la proprietà di stile deve essere supportata da una proprietà associabile. Inoltre, la proprietà deve essere definita nella classe indicata dal `TargetType` impostazione o ereditati da tale classe.

È possibile specificare il `Value` impostazione utilizzando l'elemento proprietà `<Setter.Value>`. Ciò consente di impostare `Value` a un oggetto che non può essere espressi in una stringa di testo o a un `OnPlatform` dell'oggetto o a un oggetto istanza creata utilizzando `x:Arguments` o `x:FactoryMethod`. Il `Value` può anche essere impostata con un `StaticResource` espressione da un altro elemento nel dizionario.

Il [ **BasicStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) programma illustra la sintassi di base e Mostra come fare riferimento il `Style` con un `StaticResource` estensione di markup:

[![Schermata tripla dello stile di base](images/ch12fg01-small.png "base stili")](images/ch12fg01-large.png#lightbox "stili di base")

Il `Style` oggetto e tutti gli oggetti creati nel `Style` dell'oggetto come una `Value` impostazione vengono condivisi tra tutte le viste che fanno riferimento a cui `Style`. Il `Style` non può contenere qualsiasi elemento che non possono essere condivisi, ad esempio un `View` derivato.

Non è possibile impostare gestori di eventi un `Style`. Il `GestureRecognizers` non può essere impostata un `Style` perché non è supportata da una proprietà associabile.

## <a name="styles-in-code"></a>Stili di codice

Sebbene non sia comune, è possibile creare un'istanza e inizializzare `Style` gli oggetti nel codice. Ciò viene dimostrata la [ **BasicStyleCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode) esempio.

## <a name="style-inheritance"></a>Ereditarietà degli stili

`Style` è un [ `BasedOn` ](xref:Xamarin.Forms.Style.BasedOn) proprietà che è possibile impostare su un `StaticResource` estensione di markup che fanno riferimento a un altro stile. In questo modo gli stili ereditare da stili precedenti e aggiungere o sostituire le impostazioni delle proprietà. Il [ **StyleInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) esempio viene illustrata questa.

Se `Style2` dipende `Style1`, il `TargetType` del `Style2` deve essere identico `Style1` o derivato da `Style1`. Il dizionario risorse in cui `Style1` viene archiviato deve essere il dizionario di risorse stesso come `Style2` o un dizionario di risorse superiore nell'albero visuale.

## <a name="implicit-styles"></a>Stili impliciti

Se un `Style` in una risorsa di dizionario non dispone di un `x:Key` impostazione, dell'attributo una chiave del dizionario viene assegnato automaticamente e il `Style` oggetto diventa un *stile implicito*. Una vista senza un `Style` impostazione e il cui tipo corrisponde il `TargetType` esattamente troveranno lo stile, come le [ **ImplicitStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle) esempio viene illustrato.

Uno stile implicito può derivare da una `Style` con un `x:Key` impostazione ma non viceversa. È possibile farvi riferimento in modo esplicito uno stile implicito.

È possibile implementare tre tipi di gerarchia con gli stili e `BasedOn`:

- Gli stili definiti nel `Application` e `Page` down gli stili definiti di layout inferiore nell'albero visuale.
- Gli stili definiti per le classi di base, ad esempio `VisualElement` e `View` per gli stili definiti per le classi specifiche.
- Dagli stili con chiavi del dizionario esplicite per gli stili impliciti.

Queste gerarchie sono illustrate nel [ **StyleHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy) esempio.

## <a name="dynamic-styles"></a>Stili dinamici

Uno stile in un dizionario risorse può farvi `DynamicResource` anziché `StaticResource`. In questo modo, lo stile di un *stile dinamico*. Se lo stile viene sostituito nel dizionario risorse da un altro stile con la stessa chiave, le viste che fanno riferimento a tale stile con `DynamicResource` modificare automaticamente. Inoltre, l'assenza di una voce del dizionario con la chiave specificata causerà `StaticResource` per generare un'eccezione, ma non `DynamicResource`.

È possibile usare questa tecnica consente di modificare in modo dinamico lo stile o temi come le [ **DynamicStyles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) esempio viene illustrato.

Tuttavia, non è possibile impostare il `BasedOn` proprietà per un `DynamicResource` estensione di composizione perché `BasedOn` non è supportata da una proprietà associabile. Per derivare uno stile in modo dinamico, non impostare `BasedOn`. Al contrario, impostare il [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) proprietà dello stile desiderato da cui derivare la chiave del dizionario. Il [ **DynamicStylesInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) illustra questa tecnica.

## <a name="device-styles"></a>Stili di dispositivo

Il [ `Device.Styles` ](xref:Xamarin.Forms.Device.Styles) classe annidata definisce dodici statici campi di sola lettura per sei stili con un `TargetType` di `Label` che è possibile usare per i tipi comuni di utilizzi di testo.

Sei di questi campi sono di tipo `Style` che è possibile impostare direttamente a un `Style` proprietà nel codice:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

Gli altri sei campi sono di tipo `string` e possono essere utilizzati come chiavi del dizionario per stili dinamici:

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey) uguale a "BodyStyle"
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey) uguale a "TitleStyle"
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey) uguale a "SubtitleStyle"
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey) uguale a "CaptionStyle"
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey) uguale a "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey) uguale a "ListItemDetailTextStyle"

Questi stili sono illustrati i [ **DeviceStylesList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList) esempio.

## <a name="related-links"></a>Collegamenti correlati

- [Il capitolo 12 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Esempi di capitolo 12](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Stili](~/xamarin-forms/user-interface/styles/index.md)
