---
title: Riepilogo del capitolo 12. cella
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 12. cella'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 408f171a3c7c690b700f7be21a3dcaff503467d9
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "65926914"
---
# <a name="summary-of-chapter-12-styles"></a>Riepilogo del capitolo 12. cella

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

In xamarin. Forms, gli stili di consentano più visualizzazioni condividere una raccolta di impostazioni delle proprietà. Ciò riduce markup e consente di mantenere coerenti temi visivi.

Gli stili sono quasi sempre definiti e utilizzati nel markup. Viene creata un'istanza di un oggetto di tipo [`Style`](xref:Xamarin.Forms.Style) in un dizionario risorse e quindi impostato sulla proprietà [`Style`](xref:Xamarin.Forms.NavigableElement.Style) di un elemento visivo utilizzando un'estensione di markup `StaticResource` o `DynamicResource`.

## <a name="the-basic-style"></a>Lo stile di base

Per un `Style` è necessario che il relativo [`TargetType`](xref:Xamarin.Forms.Style.TargetType) sia impostato sul tipo di oggetto visivo a cui si applica. Quando viene creata un'istanza di un `Style` in un dizionario risorse (come è comune), è necessario anche un attributo `x:Key`.

Il `Style` dispone di una proprietà di contenuto di tipo [`Setters`](xref:Xamarin.Forms.Style.Setters), ovvero una raccolta di oggetti di [`Setter`](xref:Xamarin.Forms.Setter) . Ogni `Setter` associa una [`Property`](xref:Xamarin.Forms.Setter.Property) a un [`Value`](xref:Xamarin.Forms.Setter.Value).

In XAML l'impostazione `Property` è il nome di una proprietà CLR, ad esempio la proprietà `Text` di `Button`, ma la proprietà con stile deve essere supportata da una proprietà associabile. Inoltre, la proprietà deve essere definita nella classe indicata dall'impostazione del `TargetType` o ereditata da tale classe.

È possibile specificare l'impostazione `Value` usando l'elemento Property `<Setter.Value>`. In questo modo è possibile impostare `Value` su un oggetto che non può essere espresso in una stringa di testo o su un oggetto `OnPlatform` o su un oggetto di cui è stata creata un'istanza utilizzando `x:Arguments` o `x:FactoryMethod`. La proprietà `Value` può essere impostata anche con un'espressione `StaticResource` a un altro elemento nel dizionario.

Nel programma [**BasicStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) viene illustrata la sintassi di base e viene illustrato come fare riferimento all'`Style` con un'estensione di markup `StaticResource`:

[![Schermata tripla dello stile di base](images/ch12fg01-small.png "Stili di base")](images/ch12fg01-large.png#lightbox "Stili di base")

L'oggetto `Style` e qualsiasi oggetto creato nell'oggetto `Style` come impostazione `Value` vengono condivisi tra tutte le visualizzazioni che fanno riferimento a tale `Style`. Il `Style` non può contenere alcun elemento che non può essere condiviso, ad esempio un `View` derivato.

Impossibile impostare i gestori eventi in un `Style`. Impossibile impostare la proprietà `GestureRecognizers` in un `Style` perché non è supportata da una proprietà associabile.

## <a name="styles-in-code"></a>Stili di codice

Sebbene non sia comune, è possibile creare un'istanza e inizializzare `Style` oggetti nel codice. Questa operazione è illustrata nell'esempio [**BasicStyleCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode) .

## <a name="style-inheritance"></a>Ereditarietà degli stili

`Style` dispone di una proprietà [`BasedOn`](xref:Xamarin.Forms.Style.BasedOn) che è possibile impostare su un'estensione di markup `StaticResource` che fa riferimento a un altro stile. In questo modo gli stili ereditare da stili precedenti e aggiungere o sostituire le impostazioni delle proprietà. Questa operazione è illustrata nell'esempio [**StyleInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) .

Se `Style2` è basato su `Style1`, il `TargetType` di `Style2` deve essere uguale a `Style1` o derivato da `Style1`. Il dizionario risorse in cui è archiviato `Style1` deve essere lo stesso dizionario risorse `Style2` o un dizionario risorse più in alto nella struttura ad albero visuale.

## <a name="implicit-styles"></a>Stili impliciti

Se un `Style` in un dizionario risorse non dispone di un'impostazione `x:Key` attributo, viene assegnata automaticamente una chiave del dizionario e l'oggetto `Style` diventa uno *stile implicito*. Una visualizzazione senza un'impostazione di `Style` e il cui tipo corrisponde al `TargetType` esattamente troverà tale stile, come illustrato nell'esempio [**ImplicitStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle) .

Uno stile implicito può derivare da una `Style` con un'impostazione `x:Key` ma non viceversa. È possibile farvi riferimento in modo esplicito uno stile implicito.

È possibile implementare tre tipi di gerarchia con stili e `BasedOn`:

- Dagli stili definiti nel `Application` e `Page` fino a stili definiti nei layout più in basso nella struttura ad albero visuale.
- Dagli stili definiti per le classi di base, ad esempio `VisualElement` e `View` agli stili definiti per classi specifiche.
- Dagli stili con chiavi del dizionario esplicite per gli stili impliciti.

Queste gerarchie sono illustrate nell'esempio [**StyleHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy) .

## <a name="dynamic-styles"></a>Stili dinamici

È possibile fare riferimento a uno stile in un dizionario risorse `DynamicResource` anziché `StaticResource`. In questo modo lo stile diventa uno *stile dinamico*. Se lo stile viene sostituito nel dizionario risorse da un altro stile con la stessa chiave, le visualizzazioni che fanno riferimento a tale stile con `DynamicResource` cambiano automaticamente. Inoltre, l'assenza di una voce del dizionario con la chiave specificata causerà la generazione di un'eccezione da parte di `StaticResource`, ma non di `DynamicResource`.

È possibile utilizzare questa tecnica per modificare in modo dinamico lo stile o i temi come illustrato nell'esempio [**DynamicStyles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) .

Tuttavia, non è possibile impostare la proprietà `BasedOn` su un'estensione `DynamicResource` makeup perché `BasedOn` non è supportata da una proprietà associabile. Per derivare uno stile dinamicamente, non impostare `BasedOn`. Al contrario, impostare la proprietà [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) sulla chiave del dizionario dello stile da cui si desidera eseguire la derivazione. Questa tecnica è illustrata nell'esempio [**DynamicStylesInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) .

## <a name="device-styles"></a>Stili di dispositivo

La classe annidata [`Device.Styles`](xref:Xamarin.Forms.Device.Styles) definisce dodici campi statici di sola lettura per sei stili con una `TargetType` di `Label` che è possibile usare per i tipi comuni di utilizzo del testo.

Sei di questi campi sono di tipo `Style` che è possibile impostare direttamente su una proprietà `Style` nel codice:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

Gli altri sei campi sono di tipo `string` e possono essere usati come chiavi del dizionario per gli stili dinamici:

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey) uguale a "BodyStyle"
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey) uguale a "TitleStyle"
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey) uguale a "SubtitleStyle"
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey) uguale a "CaptionStyle"
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey) uguale a "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey) uguale a "ListItemDetailTextStyle"

Questi stili sono illustrati nell'esempio [**DeviceStylesList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList) .

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 12 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Esempi del capitolo 12](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Stili](~/xamarin-forms/user-interface/styles/index.md)
