---
title: Riepilogo del capitolo 11. Infrastruttura associabile
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: Riepilogo del capitolo 11. Infrastruttura associabile'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: f9e3326c0f55469cfa84a019a674679d82dfc007
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334366"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Riepilogo del capitolo 11. Infrastruttura associabile

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

Ogni programmatore in c# abbia familiarità con c# *proprietà*. Proprietà contengono una *impostata* funzione di accesso e/o una *ottenere* della funzione di accesso. Vengono spesso chiamati *le proprietà CLR* per Common Language Runtime.

Xamarin. Forms definisce una definizione di proprietà avanzate chiamata un *la proprietà associabile* incapsulato dal [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) classe e supportata dal [ `BindableObject` ](xref:Xamarin.Forms.BindableObject)classe. Queste classi sono sostanzialmente distinte ma correlate: Il `BindableProperty` viene usato per definire la proprietà stessa. `BindableObject` è simile a `object` in quanto si tratta di una classe di base per le classi che definiscono le proprietà associabili.

## <a name="the-xamarinforms-class-hierarchy"></a>La gerarchia di classi xamarin. Forms

Il [ **ClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) esempio Usa la reflection per visualizzare una gerarchia di classi di xamarin. Forms e illustrano il ruolo fondamentale `BindableObject` in questa gerarchia. `BindableObject` deriva da `Object` ed è la classe principale [ `Element` ](xref:Xamarin.Forms.Element) da cui [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) deriva. Si tratta della classe padre per [ `Page` ](xref:Xamarin.Forms.Page) e [ `View` ](xref:Xamarin.Forms.View), che è la classe padre da [ `Layout` ](xref:Xamarin.Forms.Layout):

[![Schermata triplo della gerarchia di classi di condivisione](images/ch11fg01-small.png "gerarchia di classe condivisione")](images/ch11fg01-large.png#lightbox "condivisione gerarchia di classe")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Un'anteprima in BindableObject e BindableProperty

Nelle classi che derivano da `BindableObject` molte proprietà CLR si parla di "supportata da" proprietà associabili. Ad esempio, il [ `Text` ](xref:Xamarin.Forms.Label.Text) proprietà del `Label` classe è una proprietà CLR, ma la `Label` classe definisce anche un campo statico pubblico sola lettura denominato [ `TextProperty` ](xref:Xamarin.Forms.Label.TextProperty) di tipo `BindableProperty`.

Un'applicazione può impostare o ottenere il `Text` proprietà di `Label` normalmente, o l'applicazione può impostare il `Text` chiamando il [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) metodo definito dal `BindableObject` con un `Label.TextProperty` argomento. In modo analogo, un'applicazione può ottenere il valore del `Text` proprietà chiamando i [ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) metodo, con un `Label.TextProperty` argomento. Ciò viene dimostrata la [ **PropertySettings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings) esempio.

In effetti, il `Text` proprietà CLR è interamente implementato mediante il `SetValue` e `GetValue` metodi definiti dal `BindableObject` in combinazione con il `Label.TextProperty` proprietà statica.

`BindableObject` e `BindableProperty` forniscono il supporto per:

- Fornire i valori predefiniti delle proprietà
- Archiviare i relativi valori correnti
- Fornire meccanismi per la convalida dei valori delle proprietà
- Mantenimento della coerenza tra le proprietà correlate in una singola classe
- Rispondere alle modifiche delle proprietà
- Attivare le notifiche quando una proprietà sta per essere modificata o è stato modificato
- Supporto dell'associazione dati
- Il supporto di stili
- Supporto di risorse dinamiche

Ogni volta che una proprietà supportata da modifica una proprietà associabile, `BindableObject` viene attivato un [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) eventi che identifica la proprietà che è stato modificato. Questo evento non viene generato quando la proprietà è impostata sullo stesso valore.

Alcune proprietà non sono supportate da proprietà associabile e alcune classi di xamarin. Forms &mdash; , ad esempio `Span` &mdash; non derivano da `BindableObject`. Solo una classe che deriva da `BindableObject` può supportare proprietà associabili poiché `BindableObject` definisce le `SetValue` e `GetValue` metodi.

In quanto `Span` non deriva da `BindableObject`, nessuna delle proprietà &mdash; , ad esempio `Text` &mdash; sono supportati da una proprietà associabile. È per questo motivo un `DynamicResource` impostazione nella `Text` proprietà di `Span` genera un'eccezione nel [ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) esempio nel capitolo precedente. Il [ **DynamicVsStaticCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) esempio viene illustrato come impostare una risorsa dinamica nel codice utilizzando il [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource(Xamarin.Forms.BindableProperty,System.String)) definito dal metodo `Element`. Il primo argomento è un oggetto di tipo `BindableProperty`.

Analogamente, il [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) metodo definito da `BindableObject` è un primo argomento di tipo `BindableProperty`.

## <a name="defining-bindable-properties"></a>La definizione di proprietà associabili

È possibile definire le proprietà associabili usando il metodo statico [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) metodo per creare un campo statico di sola lettura di tipo `BindableProperty`.

Come illustrato nel [ `AltLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria. La classe deriva da `Label` e consente di specificare una dimensione del carattere in punti. È dimostrata nel [ **PointSizedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText) esempio.

Quattro argomenti del `BindableProperty.Create` metodo sono necessari:

- `propertyName`: il nome di testo della proprietà (lo stesso come il nome della proprietà CLR)
- `returnType`: il tipo della proprietà CLR
- `declaringType`: il tipo della classe dichiara la proprietà
- `defaultValue`: il valore della proprietà predefinito

In quanto `defaultValue` JE typu `object`, il compilatore deve essere in grado di determinare il tipo del valore predefinito. Ad esempio, se il `returnType` viene `double`, il `defaultValue` deve essere impostato su un valore come 0,0, anziché solo 0 o la mancata corrispondenza tra tipi generano un'eccezione in fase di esecuzione.

È anche molto comune per una proprietà associabile da includere:

- `propertyChanged`: quando la proprietà cambia valore chiamato un metodo statico. Il primo argomento è l'istanza della classe di cui è stata modificata.

Altri argomenti `BindableProperty.Create` sono meno frequenti:

- `defaultBindingMode`: utilizzato in combinazione con l'associazione dati (come descritto nella [ **capitolo 16. Associazione dati**](chapter16.md))
- `validateValue`: un callback per verificare la presenza di un valore valido
- `propertyChanging`: un callback per indicare quando la proprietà sta per essere modificata
- `coerceValue`: un callback da assegnare un valore impostato su un altro valore
- `defaultValueCreate`: un callback per creare un valore predefinito che non può essere condivisi tra le istanze della classe (ad esempio, una raccolta)

### <a name="the-read-only-bindable-property"></a>La proprietà associabile di sola lettura

Sola lettura, può essere una proprietà associabile. Creazione di una proprietà associabile di sola lettura è necessario chiamare il metodo statico [ `BindableProperty.CreateReadOnly` ](xref:Xamarin.Forms.BindableProperty.CreateReadOnly(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) per definire un campo di sola lettura statico privato di tipo [ `BindablePropertyKey` ](xref:Xamarin.Forms.BindablePropertyKey).

Quindi, definire la proprietà CLR `set` funzione di accesso agli come `private` per chiamare un [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindablePropertyKey,System.Object)) overload con il `BindablePropertyKey` oggetto. Ciò impedisce che la proprietà viene impostata all'esterno della classe.

Ciò è dimostrato nel [ `CountedLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) classe usata nel [ **BaskervillesCount** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount) esempio.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 11 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Esempi di capitolo 11](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [Proprietà associabili](~/xamarin-forms/xaml/bindable-properties.md)
