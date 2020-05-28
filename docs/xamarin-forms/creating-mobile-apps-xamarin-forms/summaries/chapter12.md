---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 12. Styles''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 22f7272751c30bed350fe7aada8d5dacf0acc809
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136786"
---
# <a name="summary-of-chapter-12-styles"></a>Riepilogo del capitolo 12. Stili

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

In gli Xamarin.Forms stili consentono a più visualizzazioni di condividere una raccolta di impostazioni delle proprietà. In questo modo si riduce il markup e si consente di gestire temi visivi coerenti.

Gli stili sono quasi sempre definiti e utilizzati nel markup. Viene creata un'istanza di un oggetto di tipo [`Style`](xref:Xamarin.Forms.Style) in un dizionario risorse e quindi impostato sulla [`Style`](xref:Xamarin.Forms.NavigableElement.Style) proprietà di un elemento visivo utilizzando `StaticResource` un' `DynamicResource` estensione di markup o.

## <a name="the-basic-style"></a>Stile di base

`Style`Per è necessario che la proprietà [`TargetType`](xref:Xamarin.Forms.Style.TargetType) sia impostata sul tipo di oggetto visivo a cui si applica. Quando `Style` viene creata un'istanza di un oggetto in un dizionario risorse (come è comune), è necessario anche un `x:Key` attributo.

`Style`Dispone di una proprietà di contenuto di tipo [`Setters`](xref:Xamarin.Forms.Style.Setters) , che è una raccolta di [`Setter`](xref:Xamarin.Forms.Setter) oggetti. Ogni `Setter` associa un oggetto a [`Property`](xref:Xamarin.Forms.Setter.Property) un oggetto [`Value`](xref:Xamarin.Forms.Setter.Value) .

In XAML l' `Property` impostazione è il nome di una proprietà CLR, ad esempio la `Text` proprietà di `Button` , ma la proprietà con stile deve essere supportata da una proprietà associabile. Inoltre, la proprietà deve essere definita nella classe indicata dall' `TargetType` impostazione o ereditata da tale classe.

È possibile specificare l' `Value` impostazione utilizzando l'elemento Property `<Setter.Value>` . In questo modo è possibile impostare `Value` su un oggetto che non può essere espresso in una stringa di testo o su un `OnPlatform` oggetto o su un oggetto di cui è stata creata un'istanza utilizzando `x:Arguments` o `x:FactoryMethod` . La `Value` proprietà può essere impostata anche con un' `StaticResource` espressione a un altro elemento nel dizionario.

Nel programma [**BasicStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) viene illustrata la sintassi di base e viene illustrato come fare riferimento a `Style` con un' `StaticResource` estensione di markup:

[![Schermata tripla dello stile di base](images/ch12fg01-small.png "Stili di base")](images/ch12fg01-large.png#lightbox "Stili di base")

L' `Style` oggetto e qualsiasi oggetto creato nell' `Style` oggetto come `Value` impostazione sono condivisi tra tutte le visualizzazioni che fanno riferimento a tale oggetto `Style` . `Style`Non può contenere alcun elemento che non può essere condiviso, ad esempio un `View` derivato.

I gestori eventi non possono essere impostati in un oggetto `Style` . `GestureRecognizers`Impossibile impostare la proprietà in un oggetto `Style` perché non è supportata da una proprietà associabile.

## <a name="styles-in-code"></a>Stili nel codice

Sebbene non sia comune, è possibile creare un'istanza e inizializzare `Style` gli oggetti nel codice. Questa operazione è illustrata nell'esempio [**BasicStyleCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode) .

## <a name="style-inheritance"></a>Ereditarietà dello stile

`Style`dispone [`BasedOn`](xref:Xamarin.Forms.Style.BasedOn) di una proprietà che può essere impostata su un'estensione di markup che fa riferimento a un `StaticResource` altro stile. Ciò consente agli stili di ereditare dagli stili precedenti e di aggiungere o sostituire le impostazioni delle proprietà. Questa operazione è illustrata nell'esempio [**StyleInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) .

Se `Style2` è basato su `Style1` , il `TargetType` di `Style2` deve essere uguale `Style1` o derivato da `Style1` . Il dizionario risorse in cui `Style1` viene archiviato deve essere lo stesso dizionario risorse `Style2` o un dizionario risorse più in alto nella struttura ad albero visuale.

## <a name="implicit-styles"></a>Stili impliciti

Se `Style` in un dizionario risorse non è presente un' `x:Key` impostazione di attributo, viene assegnata automaticamente una chiave del dizionario e l' `Style` oggetto diventa uno *stile implicito*. Una visualizzazione senza un' `Style` impostazione e il cui tipo corrisponde `TargetType` esattamente a troveranno tale stile, come illustrato nell'esempio [**ImplicitStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle) .

Uno stile implicito può derivare da un oggetto `Style` con un' `x:Key` impostazione ma non viceversa. Non è possibile fare riferimento in modo esplicito a uno stile implicito.

È possibile implementare tre tipi di gerarchia con stili e `BasedOn` :

- Dagli stili definiti in `Application` e `Page` fino agli stili definiti nei layout in basso nella struttura ad albero visuale.
- Da stili definiti per le classi di base, ad esempio `VisualElement` e, `View` per gli stili definiti per classi specifiche.
- Da stili con chiavi del dizionario esplicite a stili impliciti.

Queste gerarchie sono illustrate nell'esempio [**StyleHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy) .

## <a name="dynamic-styles"></a>Stili dinamici

È possibile fare riferimento a uno stile in un dizionario risorse `DynamicResource` anziché a `StaticResource` . In questo modo lo stile diventa uno *stile dinamico*. Se lo stile viene sostituito nel dizionario risorse da un altro stile con la stessa chiave, le visualizzazioni che fanno riferimento a tale stile `DynamicResource` cambiano automaticamente. Inoltre, l'assenza di una voce del dizionario con la chiave specificata causerà la `StaticResource` generazione di un'eccezione, ma non `DynamicResource` .

È possibile utilizzare questa tecnica per modificare in modo dinamico lo stile o i temi come illustrato nell'esempio [**DynamicStyles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) .

Tuttavia, non è possibile impostare la `BasedOn` proprietà su un' `DynamicResource` estensione del trucco perché `BasedOn` non è supportata da una proprietà associabile. Per derivare uno stile dinamicamente, non impostare `BasedOn` . In alternativa, impostare la [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) proprietà sulla chiave del dizionario dello stile da cui si desidera eseguire la derivazione. Questa tecnica è illustrata nell'esempio [**DynamicStylesInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) .

## <a name="device-styles"></a>Stili del dispositivo

La [`Device.Styles`](xref:Xamarin.Forms.Device.Styles) classe annidata definisce dodici campi statici di sola lettura per sei stili con una `TargetType` di `Label` che è possibile usare per i tipi comuni di utilizzo del testo.

Sei di questi campi sono di tipo `Style` che è possibile impostare direttamente su una `Style` proprietà nel codice:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

Gli altri sei campi sono di tipo `string` e possono essere usati come chiavi del dizionario per gli stili dinamici:

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey)uguale a "BodyStyle"
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey)uguale a "TitleStyle"
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey)uguale a "SubtitleStyle"
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey)uguale a "CaptionStyle"
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey)uguale a "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey)uguale a "ListItemDetailTextStyle"

Questi stili sono illustrati nell'esempio [**DeviceStylesList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList) .

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 12 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Esempi del capitolo 12](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Stili](~/xamarin-forms/user-interface/styles/index.md)
