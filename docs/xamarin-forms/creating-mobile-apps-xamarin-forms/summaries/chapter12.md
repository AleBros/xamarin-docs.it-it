---
title: Riassunto del capitolo 12. Stili
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 12. Stili'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 408f171a3c7c690b700f7be21a3dcaff503467d9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "65926914"
---
# <a name="summary-of-chapter-12-styles"></a>Riassunto del capitolo 12. Stili

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

In Xamarin.Forms, gli stili consentono a più visualizzazioni di condividere una raccolta di impostazioni delle proprietà. In questo modo si riduce il markup e si ottengono problemi visivi coerenti.

Gli stili sono quasi sempre definiti e utilizzati nel markup. Viene creata [`Style`](xref:Xamarin.Forms.Style) un'istanza di un oggetto di [`Style`](xref:Xamarin.Forms.NavigableElement.Style) tipo in un dizionario risorse e quindi impostato sulla proprietà di un elemento visivo utilizzando un'estensione `StaticResource` di markup o `DynamicResource` .

## <a name="the-basic-style"></a>Lo stile di base

Un `Style` richiede [`TargetType`](xref:Xamarin.Forms.Style.TargetType) che sia impostato sul tipo dell'oggetto visivo a cui si applica. Quando `Style` viene creata un'istanza di un oggetto in `x:Key` un dizionario risorse (come è comune) richiede anche un attributo.

Dispone `Style` di una proprietà [`Setters`](xref:Xamarin.Forms.Style.Setters)content di tipo [`Setter`](xref:Xamarin.Forms.Setter) , che è un insieme di oggetti. Ognuno `Setter` associa [`Property`](xref:Xamarin.Forms.Setter.Property) a [`Value`](xref:Xamarin.Forms.Setter.Value)a un oggetto .

In XAML `Property` l'impostazione è il nome `Text` di `Button`una proprietà CLR , ad esempio la proprietà di ) ma la proprietà con stile deve essere supportata da una proprietà associabile. Inoltre, la proprietà deve essere definita nella `TargetType` classe indicata dall'impostazione o ereditata da tale classe.

È possibile `Value` specificare l'impostazione utilizzando l'elemento `<Setter.Value>`proprietà . In questo `Value` modo è possibile impostare su un oggetto che `OnPlatform` non può essere espresso in `x:Arguments` `x:FactoryMethod`una stringa di testo, su un oggetto o su un oggetto di cui è stata creata un'istanza utilizzando o . La `Value` proprietà può anche `StaticResource` essere impostata con un'espressione su un altro elemento nel dizionario.

Il BasicStyle programma viene illustrata la `Style` sintassi `StaticResource` di base e viene illustrato come fare riferimento a con un'estensione di markup:The [**BasicStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) program demonstrates the basic syntax and demonstrates how to reference the with a markup extension:

[![Tripla schermata dello stile di base](images/ch12fg01-small.png "Stili di base")](images/ch12fg01-large.png#lightbox "Stili di base")

L'oggetto `Style` e qualsiasi `Style` oggetto creato `Value` nell'oggetto come impostazione `Style`vengono condivisi tra tutte le viste che fanno riferimento a tale oggetto. L'oggetto `Style` non può contenere elementi `View` che non possono essere condivisi, ad esempio una derivata.

I gestori eventi non `Style`possono essere impostati in un oggetto . La `GestureRecognizers` proprietà non può `Style` essere impostata in un oggetto perché non è supportata da una proprietà associabile.

## <a name="styles-in-code"></a>Stili nel codice

Anche se non è comune, è `Style` possibile creare un'istanza e inizializzare gli oggetti nel codice. Ciò è dimostrato dall'esempio [**BasicStyleCode.This**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode) is demonstrated by the BasicStyleCode sample.

## <a name="style-inheritance"></a>Ereditarietà degli stili

`Style`dispone [`BasedOn`](xref:Xamarin.Forms.Style.BasedOn) di una proprietà che `StaticResource` è possibile impostare su un'estensione di markup che fa riferimento a un altro stile. Ciò consente agli stili di ereditare dagli stili precedenti e di aggiungere o sostituire le impostazioni delle proprietà. Il [**StyleInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) esempio viene illustrato questo.

Se `Style2` è `Style1`basato `TargetType` `Style2` su , l'oggetto di deve essere uguale `Style1` o derivato da `Style1`. Il dizionario `Style1` risorse in cui è archiviato deve essere lo stesso dizionario risorse `Style2` o un dizionario risorse più in alto nella struttura ad albero visuale.

## <a name="implicit-styles"></a>Stili impliciti

Se `Style` un oggetto in un `x:Key` dizionario risorse non dispone di un'impostazione di attributo, gli viene assegnata automaticamente una chiave del dizionario e l'oggetto `Style` diventa uno stile *implicito.* Una visualizzazione `Style` senza un'impostazione `TargetType` e il cui tipo corrisponde esattamente allo stile troverà tale stile, come illustrato nell'esempio [**ImplicitStyle.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle)

Uno stile implicito `Style` può `x:Key` derivare da un con un'impostazione, ma non viceversa. Non è possibile fare riferimento in modo esplicito a uno stile implicito.

È possibile implementare tre tipi `BasedOn`di gerarchia con stili e:

- Dagli stili definiti `Application` `Page` nella struttura ad albero visuale e in basso.
- Dagli stili definiti per `VisualElement` le `View` classi di base, ad esempio e agli stili definiti per classi specifiche.
- Dagli stili con chiavi di dizionario esplicite agli stili impliciti.

Queste gerarchie sono illustrate nell'esempio [**StyleHierarchy.These**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy) hierarchies are demonstrated in the StyleHierarchy sample.

## <a name="dynamic-styles"></a>Stili dinamici

È possibile fare riferimento a uno `DynamicResource` stile `StaticResource`in un dizionario risorse anziché da . Questo rende lo stile uno *stile dinamico.* Se tale stile viene sostituito nel dizionario risorse da un altro stile `DynamicResource` con la stessa chiave, le viste che fanno riferimento a tale stile cambiano automaticamente. Inoltre, l'assenza di una voce `StaticResource` di dizionario con `DynamicResource`la chiave specificata causerà la generazione di un'eccezione ma non .

È possibile utilizzare questa tecnica per modificare dinamicamente lo stile o i temi come illustrato nell'esempio [**DynamicStyles.You**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) can use this technique to dynamically change styling or themes as the DynamicStyles sample demonstrates.

Tuttavia, non `BasedOn` è possibile `DynamicResource` impostare `BasedOn` la proprietà su un'estensione di trucco perché non è supportata da una proprietà associabile. Per derivare uno stile `BasedOn`in modo dinamico, non impostare . Impostare invece la proprietà sulla [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) chiave del dizionario dello stile da cui si desidera derivare. [**Nell'esempio DynamicStylesInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) viene illustrata questa tecnica.

## <a name="device-styles"></a>Stili dispositivo

La [`Device.Styles`](xref:Xamarin.Forms.Device.Styles) classe annidata definisce dodici campi statici `TargetType` `Label` di sola lettura per sei stili con un che è possibile utilizzare per i tipi comuni di utilizzo del testo.

Sei di questi campi `Style` sono di tipo `Style` che è possibile impostare direttamente su una proprietà nel codice:Six of these fields are of type that you can set directly to a property in code:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

Gli altri sei campi `string` sono di tipo e possono essere utilizzati come chiavi del dizionario per gli stili dinamici:

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey)uguale a "BodyStyle"
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey)uguale a "TitleStyle"
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey)uguale a "SubtitleStyle"
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey)uguale a "CaptionStyle"
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey)uguale a "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey)è uguale a "ListItemDetailTextStyle"

Questi stili sono illustrati nell'esempio [**DeviceStylesList.These styles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList) are illustrated by the DeviceStylesList sample.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 12 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Esempi del capitolo 12](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Stili](~/xamarin-forms/user-interface/styles/index.md)
