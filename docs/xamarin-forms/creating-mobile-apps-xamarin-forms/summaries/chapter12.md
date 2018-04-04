---
title: Riepilogo del capitolo 12. Stili
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: bb26c5d93bc9945ad43ed62d7feba2bc851e510e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-12-styles"></a>Riepilogo del capitolo 12. Stili

In xamarin. Forms, gli stili di consentono più visualizzazioni condividere una raccolta di impostazioni delle proprietà. Questo riduce markup e consente la gestione coerenti temi visivi.

Stili sono quasi sempre definiti e utilizzati nel markup. Un oggetto di tipo [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) viene creata un'istanza in un dizionario risorse e quindi impostare il [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà di un elemento visivo usando un `StaticResource` o `DyanamicResource` markup estensione.

## <a name="the-basic-style"></a>Lo stile di base

Oggetto `Style` richiede che il relativo [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) impostare il tipo dell'oggetto visivo si applica a. Quando un `Style` viene creata un'istanza in un dizionario risorse (come avviene spesso) richiede inoltre un `x:Key` attributo.

Il `Style` ha una proprietà di contenuto di tipo [ `Setters` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.Setters/), che è una raccolta di [ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/) oggetti. Ogni `Setter` associa un [ `Property` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Property/) con un [ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Value/).

In XAML il `Property` impostazione corrisponde al nome di una proprietà CLR (ad esempio il `Text` proprietà di `Button`), ma la proprietà di stile deve essere supportata da una proprietà associabile. Inoltre, la proprietà deve essere definita nella classe indicata dal `TargetType` impostazione o ereditati da tale classe.

È possibile specificare il `Value` impostazione utilizzando l'elemento proprietà `<Setter.Value>`. Consente di impostare `Value` a un oggetto che non può essere espressi in una stringa di testo o a un `OnPlatform` dell'oggetto o a un oggetto istanza creata utilizzando `x:Arguments` o `x:FactoryMethod`. Il `Value` può anche essere impostata con un `StaticResource` espressione a un altro elemento nel dizionario.

Il [ **BasicStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) programma illustra la sintassi di base e Mostra come fare riferimento il `Style` con un `StaticResource` estensione di markup:

[![La schermata di stile di base](images/ch12fg01-small.png "stili di base")](images/ch12fg01-large.png#lightbox "stili di base")

Il `Style` oggetto e tutti gli oggetti creati nel `Style` oggetto come un `Value` impostazione vengono condivisi tra tutte le viste che fanno riferimento a che `Style`. Il `Style` non può contenere qualsiasi elemento che non possono essere condivisi, ad esempio un `View` derivato.

Non è possibile impostare gestori eventi un `Style`. Il `GestureRecognizers` non può essere impostata un `Style` perché non è supportata da una proprietà associabile.

## <a name="styles-in-code"></a>Stili di codice

Anche se non è comune, è possibile creare un'istanza e inizializzazione `Style` gli oggetti nel codice. Ciò viene dimostrata la [ **BasicStyleCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode) esempio.

## <a name="style-inheritance"></a>Ereditarietà degli stili

`Style` è un [ `BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/) proprietà che è possibile impostare un `StaticResource` estensione di markup che fanno riferimento a un altro stile. In questo modo gli stili ereditare gli stili precedenti e aggiungere o sostituire le impostazioni delle proprietà. Il [ **StyleInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) esempio viene illustrata questa.

Se `Style2` si basa sul `Style1`, `TargetType` di `Style2` deve corrispondere a `Style1` o derivato da `Style1`. Il dizionario risorse in cui `Style1` viene archiviato deve essere lo stessa dizionario risorse come `Style2` o un dizionario risorse più in alto nell'albero visuale.

## <a name="implicit-styles"></a>Stili impliciti

Se un `Style` in una risorsa di dizionario non dispone di un `x:Key` , impostazione dell'attributo viene assegnato automaticamente, una chiave del dizionario e `Style` oggetto diventa un *impliciti allo stile*. Una vista senza un `Style` impostazione e il cui tipo corrisponde il `TargetType` esattamente troverà lo stile selezionato, come il [ **ImplicitStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle) viene illustrato l'esempio.

Uno stile implicito può derivare da un `Style` con un `x:Key` impostazione ma non viceversa. È in modo esplicito non può fare riferimento a uno stile implicito.

È possibile implementare i tre tipi di gerarchia con stili e `BasedOn`:

- Gli stili definiti nel `Application` e `Page` down gli stili definiti layout inferiore la struttura ad albero visuale.
- Gli stili definiti per le classi base, ad esempio `VisualElement` e `View` per gli stili definiti per le classi specifiche.
- Gli stili con chiavi del dizionario esplicite per gli stili impliciti.

Tali gerarchie vengono illustrate il [ **StyleHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy) esempio.

## <a name="dynamic-styles"></a>Stili dinamici

Uno stile in un dizionario risorse può fare riferimento `DynamicResource` anziché `StaticResource`. In questo modo lo stile di un *stile dinamico*. Se lo stile selezionato viene sostituito nel dizionario risorse da un altro stile con la stessa chiave, le viste che fanno riferimento a tale stile con `DynamicResource` modificare automaticamente. Inoltre, l'assenza di una voce di dizionario con la chiave specificata causa `StaticResource` per generare un'eccezione ma non `DynamicResource`.

È possibile utilizzare questa tecnica consente di modificare dinamicamente lo stile o temi come il [ **DynamicStyles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) viene illustrato l'esempio.

Tuttavia, non è possibile impostare il `BasedOn` proprietà per un `DynamicResource` estensione composizione perché `BasedOn` non è supportato da una proprietà associabile. Per derivare uno stile in modo dinamico, non impostare `BasedOn`. Al contrario, impostare il [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) proprietà dello stile desiderato da cui derivare la chiave del dizionario. Il [ **DynamicStylesInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) illustra questa tecnica.

## <a name="device-styles"></a>Stili di dispositivo

Il [ `Device.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/) classe annidata definisce dodici statici campi di sola lettura per i sei stili con un `TargetType` di `Label` che è possibile utilizzare tipi comuni di utilizzo di testo.

Sei di questi campi sono di tipo `Style` che è possibile impostare direttamente a un `Style` proprietà nel codice:

- [`BodyStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/)
- [`TitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.TitleStyle/)
- [`SubtitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.SubtitleStyle/)
- [`CaptionStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.CaptionStyle/)
- [`ListItemTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemTextStyle/)
- [`ListItemDetailTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemDetailTextStyle/)

Sei campi sono di tipo `string` e possono essere utilizzati come chiavi del dizionario per gli stili dinamici:

- [`BodyStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyleKey/) uguale a "BodyStyle"
- [`TitleStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.TitleStyleKey/) uguale a "TitleStyle"
- [`SubtitleStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.SubtitleStyleKey/) uguale a "SubtitleStyle"
- [`CaptionStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.CaptionStyleKey/) uguale a "CaptionStyle"
- [`ListItemTextStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemTextStyleKey/) uguale a "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemDetailTextStyleKey/) uguale a "ListItemDetailTextStyle"

Questi stili sono identificati dal [ **DeviceStylesList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList) esempio.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 12 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Capitolo 12 campioni](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Stili](~/xamarin-forms/user-interface/styles/index.md)
