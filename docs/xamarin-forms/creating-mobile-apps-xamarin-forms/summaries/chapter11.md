---
title: Riassunto del capitolo 11. L'infrastruttura bindable
description: "Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 11. L'infrastruttura bindable"
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: f9e3326c0f55469cfa84a019a674679d82dfc007
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334366"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Riassunto del capitolo 11. L'infrastruttura bindable

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

Tutti i programmatori di C' hanno familiarità con *le proprietà*di C. Le proprietà contengono una funzione di accesso *set* e/o una funzione di accesso *get.* Sono spesso chiamati *proprietà CLR* per Common Language Runtime.

Xamarin.Forms definisce una definizione di proprietà avanzata [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) denominata proprietà [`BindableObject`](xref:Xamarin.Forms.BindableObject) *associabile* incapsulata dalla classe e supportata dalla classe . Queste classi sono correlate ma `BindableProperty` piuttosto distinte: l'oggetto viene utilizzato per definire la proprietà stessa; `BindableObject` è `object` come in quanto è una classe di base per le classi che definiscono le proprietà associabili.

## <a name="the-xamarinforms-class-hierarchy"></a>Gerarchia delle classi Xamarin.Forms

[**Nell'esempio ClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) viene utilizzata la reflection per visualizzare una gerarchia di `BindableObject` classi di Xamarin.Forms e illustrare il ruolo cruciale svolto in questa gerarchia. `BindableObject`deriva da `Object` ed è la [`Element`](xref:Xamarin.Forms.Element) classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) padre da cui deriva. Si tratta della [`Page`](xref:Xamarin.Forms.Page) classe [`View`](xref:Xamarin.Forms.View)padre a e [`Layout`](xref:Xamarin.Forms.Layout), che è la classe padre di :

[![Tripla schermata della condivisione della gerarchia di classi](images/ch11fg01-small.png "Condivisione della gerarchia di classiClass Hierarchy Sharing")](images/ch11fg01-large.png#lightbox "Condivisione della gerarchia di classiClass Hierarchy Sharing")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Una visualizzazione in BindableObject e BindableProperty

Nelle classi che `BindableObject` derivano da molte proprietà CLR vengono dette proprietà associabili "supportate da". Ad esempio, [`Text`](xref:Xamarin.Forms.Label.Text) la `Label` proprietà della classe è `Label` una proprietà CLR , ma la [`TextProperty`](xref:Xamarin.Forms.Label.TextProperty) classe `BindableProperty`definisce anche un campo statico pubblico di sola lettura denominato di tipo .

Un'applicazione può impostare o ottenere la `Text` proprietà di `Label` normalmente, oppure l'applicazione può impostare il `Text` chiamando [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) il metodo definito da `BindableObject` con un `Label.TextProperty` argomento. Analogamente, un'applicazione può `Text` ottenere il [`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) valore della proprietà `Label.TextProperty` chiamando il metodo, sempre con un argomento . Ciò è dimostrato dall'esempio [**PropertySettings.This**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings) is demonstrated by the PropertySettings sample.

Infatti, `Text` la proprietà CLR `SetValue` viene `GetValue` interamente `BindableObject` implementata `Label.TextProperty` utilizzando i metodi e definiti insieme alla proprietà statica.

`BindableObject`e `BindableProperty` fornire supporto per:

- Dare alle proprietà i valori predefiniti
- Memorizzazione dei valori correnti
- Fornire meccanismi per la convalida dei valori delle proprietà
- Mantenimento della coerenza tra le proprietà correlate in una singola classe
- Risposta alle modifiche alle proprietà
- Attivazione di notifiche quando una proprietà sta per essere modificata o è stata modificataTriggering notifications when a property is about to change or has changed
- Supporto dell'associazione datiSupporting data binding
- Stili di supporto
- Supporto delle risorse dinamicheSupporting dynamic resources

Ogni volta che una proprietà supportata `BindableObject` da una [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) proprietà associabile viene modificata, genera un evento che identifica la proprietà che è stata modificata. Questo evento non viene generato quando la proprietà è impostata sullo stesso valore.

Alcune proprietà non sono supportate da proprietà associabili e &mdash; alcune `Span` &mdash; classi Xamarin.Forms, ad esempio non derivano da `BindableObject`. Solo una classe che `BindableObject` deriva da può `BindableObject` supportare `SetValue` `GetValue` le proprietà associabili perché definisce i metodi e .

Poiché `Span` non `BindableObject`deriva da , &mdash; nessuna `Text` &mdash; delle relative proprietà, ad esempio supportata da una proprietà associabile. Questo è `DynamicResource` il motivo per cui un'impostazione `Text` sulla proprietà di `Span` genera un'eccezione nell'esempio [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) nel capitolo precedente. Nell'esempio [**DynamicVsStaticCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) viene illustrato come impostare [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource(Xamarin.Forms.BindableProperty,System.String)) risorse `Element`dinamiche nel codice utilizzando il metodo definito da . Il primo argomento è `BindableProperty`un oggetto di tipo .

Analogamente, [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) il `BindableObject` metodo definito da `BindableProperty`dispone di un primo argomento di tipo .

## <a name="defining-bindable-properties"></a>Definizione delle proprietà associabiliDefining bindable properties

È possibile definire proprietà associabili [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) personalizzate utilizzando il metodo statico per `BindableProperty`creare un campo statico di sola lettura di tipo .

Ciò è illustrato [`AltLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) nella classe nella libreria [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) La classe deriva `Label` da e consente di specificare una dimensione del carattere in punti. Viene illustrato nell'esempio [**PointSizedText.It**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText) is demonstrated in the PointSizedText sample.

Sono necessari `BindableProperty.Create` quattro argomenti del metodo:

- `propertyName`: il nome di testo della proprietà (uguale al nome della proprietà CLR)
- `returnType`: il tipo della proprietà CLR
- `declaringType`: il tipo della classe che dichiara la proprietà
- `defaultValue`: valore predefinito della proprietà

Poiché `defaultValue` è `object`di tipo , il compilatore deve essere in grado di determinare il tipo del valore predefinito. Ad esempio, `returnType` se `double`il `defaultValue` è , l'oggetto deve essere impostato su un valore simile a 0.0 anziché solo 0 oppure la mancata corrispondenza del tipo genererà un'eccezione in fase di esecuzione.

È anche molto comune che una proprietà associabile includa:It is also very common for a bindable property to include:

- `propertyChanged`: un metodo statico chiamato quando la proprietà cambia valore. Il primo argomento è l'istanza della classe la cui proprietà è stata modificata.

Gli altri `BindableProperty.Create` argomenti per non sono così comuni:

- `defaultBindingMode`: utilizzato in relazione all'associazione dati (come descritto nel [**capitolo 16. Associazione dati**](chapter16.md))
- `validateValue`: callback per verificare la presenza di un valore valido
- `propertyChanging`: un callback per indicare quando la proprietà sta per cambiare
- `coerceValue`: un callback per costringere un valore impostato a un altro valore
- `defaultValueCreate`: un callback per creare un valore predefinito che non può essere condiviso tra istanze della classe (ad esempio, una raccolta)

### <a name="the-read-only-bindable-property"></a>La proprietà associabile di sola lettura

Una proprietà associabile può essere di sola lettura. La creazione di una proprietà associabile [`BindableProperty.CreateReadOnly`](xref:Xamarin.Forms.BindableProperty.CreateReadOnly(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) di sola lettura richiede la chiamata [`BindablePropertyKey`](xref:Xamarin.Forms.BindablePropertyKey)al metodo statico per definire un campo statico privato di sola lettura di tipo .

Definire quindi la `set` proprietà CLR `private` accesor [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindablePropertyKey,System.Object)) come `BindablePropertyKey` per chiamare un overload con l'oggetto. In questo modo si impedisce che la proprietà venga impostata all'esterno della classe.

Ciò è illustrato [`CountedLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) nella classe utilizzata nell'esempio [**BaskervillesCount.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount)

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 11 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Esempi del capitolo 11](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [Proprietà associabili](~/xamarin-forms/xaml/bindable-properties.md)
