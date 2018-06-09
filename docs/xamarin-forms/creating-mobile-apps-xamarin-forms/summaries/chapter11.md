---
title: Riepilogo del capitolo 11. L'infrastruttura associabile
description: "Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 11. L'infrastruttura associabile"
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 7a878be4cf43e2168c37fdfd8e6fcdb79feb1602
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241539"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Riepilogo del capitolo 11. L'infrastruttura associabile

Ogni programmatore c# ha familiarità con c# *proprietà*. Proprietà contengono un *impostare* della funzione di accesso e/o un *ottenere* della funzione di accesso. Vengono spesso chiamati *proprietà CLR* per Common Language Runtime.

Xamarin. Forms definisce una definizione di proprietà avanzate chiamata un *proprietà associabile* incapsulate dal [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) classe e supportato dal [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)classe. Queste classi sono correlate ma piuttosto distinte: il `BindableProperty` viene utilizzato per definire la proprietà stessa. `BindableObject` è simile a `object` in quanto si tratta di una classe base per le classi che definiscono le proprietà associabili.

## <a name="the-xamarinforms-class-hierarchy"></a>La gerarchia di classi di xamarin. Forms

Il [ **ClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) esempio utilizza la reflection per visualizzare una gerarchia di classi di xamarin. Forms e dimostrare fondamentale ruolo svolto da `BindableObject` in questa gerarchia. `BindableObject` deriva da `Object` ed è la classe padre da [ `Element` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/) da cui [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) deriva. Questa è la classe padre per [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) e [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/), che è la classe padre da [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/):

[![Schermata di triplo della gerarchia di classi di condivisione](images/ch11fg01-small.png "classe gerarchia condivisione")](images/ch11fg01-large.png#lightbox "condivisione gerarchia di classe")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Una visualizzazione in BindableObject e BindableProperty

Nelle classi che derivano da `BindableObject` molte proprietà CLR si parla di essere "supportata da" proprietà associabili. Ad esempio, il [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) proprietà del `Label` classe è una proprietà CLR, ma la `Label` classe definisce inoltre un campo statico pubblico sola lettura denominato [ `TextProperty` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextProperty/) di tipo `BindableProperty`.

Un'applicazione può impostare o ottenere il `Text` proprietà di `Label` in genere, o l'applicazione può impostare il `Text` chiamando il [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) definito dal metodo `BindableObject` con un `Label.TextProperty` argomento. Analogamente, un'applicazione può ottenere il valore del `Text` proprietà chiamando il [ `GetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.GetValue/p/Xamarin.Forms.BindableProperty/) (metodo), con un `Label.TextProperty` argomento. Ciò viene dimostrata la [ **PropertySettings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings) esempio.

In effetti, il `Text` proprietà CLR è interamente implementato mediante la `SetValue` e `GetValue` metodi definiti dal `BindableObject` in combinazione con la `Label.TextProperty` proprietà statica.

`BindableObject` e `BindableProperty` forniscono il supporto per:

- Assegnando i valori predefiniti delle proprietà
- Archiviare i relativi valori correnti
- Fornire meccanismi per convalidare i valori delle proprietà
- Mantenere la coerenza fra proprietà correlate in una classe singola
- Risposta alle modifiche di proprietà
- Attivare le notifiche quando sta per essere modificata o è stata modificata una proprietà
- Supporto dell'associazione dati
- Il supporto di stili
- Supporto di risorse dinamiche

Ogni volta che una proprietà che è supportata da modifica una proprietà associabile, `BindableObject` viene attivato un [ `PropertyChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.BindableObject.PropertyChanged/) evento che identifica la proprietà che è stato modificato. Questo evento non viene generato quando la proprietà è impostata sullo stesso valore.

Alcune proprietà non sono supportate da proprietà associabili e alcune classi di xamarin. Forms &mdash; , ad esempio `Span` &mdash; non derivano da `BindableObject`. Solo una classe che deriva da `BindableObject` può supportare le proprietà associabili perché `BindableObject` definisce il `SetValue` e `GetValue` metodi.

Poiché `Span` non deriva da `BindableObject`, nessuna delle proprietà &mdash; , ad esempio `Text` &mdash; sono supportate da una proprietà associabile. Perché un `DynamicResource` impostazione il `Text` proprietà di `Span` genera un'eccezione nel [ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) sample nel capitolo precedente. Il [ **DynamicVsStaticCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) esempio viene illustrato come impostare una risorsa dinamica nel codice utilizzando il [ `SetDynamicResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Element.SetDynamicResource/p/Xamarin.Forms.BindableProperty/System.String/) definito dal metodo `Element`. Il primo argomento è un oggetto di tipo `BindableProperty`.

Analogamente, il [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) definito dal metodo `BindableObject` è un primo argomento di tipo `BindableProperty`.

## <a name="defining-bindable-properties"></a>Definizione di proprietà associabili

È possibile definire le proprietà associabili mediante il [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) metodo o il valore leggermente inferiore [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/) overload per creare un campo statico di sola lettura di tipo `BindableProperty`.

Questa funzionalità viene illustrata la [ `AltLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) classe nel [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria. La classe deriva da `Label` e consente di specificare una dimensione del carattere in punti. Viene illustrata la [ **PointSizedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText) esempio.

Quattro argomenti del `BindableProperty.Create` metodo sono necessari:

- `propertyName`: il nome di testo della proprietà (come per il nome della proprietà CLR)
- `returnType`: il tipo della proprietà CLR
- `declaringType`: il tipo della classe dichiara la proprietà
- `defaultValue`: il valore della proprietà predefinito

Poiché `defaultValue` è di tipo `object`, il compilatore deve essere in grado di determinare il tipo del valore predefinito. Ad esempio, se il `returnType` è `double`, `defaultValue` deve essere impostata su simile 0,0, anziché solo 0 o la mancata corrispondenza tra tipi generano un'eccezione in fase di esecuzione.

È inoltre molto comune per una proprietà associabile da includere:

- `propertyChanged`: un metodo statico chiamato quando il valore della proprietà. Il primo argomento è l'istanza della classe con la proprietà è stata modificata.

Altri argomenti `BindableProperty.Create` non sono più comuni:

- `defaultBindingMode`: utilizzato in relazione di associazione dati (come descritto in [ **capitolo 16. Associazione dati**](chapter16.md))
- `validateValue`: un callback per verificare la presenza di un valore valido
- `propertyChanging`: un callback per indicare quando la proprietà è sta per essere modificata
- `coerceValue`: un callback per assegnare un valore impostato su un altro valore
- `defaultValueCreate`: un callback per creare un valore predefinito che non può essere condivisi tra le istanze della classe (ad esempio, una raccolta)

### <a name="the-read-only-bindable-property"></a>La proprietà associabile di sola lettura

Una proprietà associabile può essere di sola lettura. Creazione di una proprietà associabile di sola lettura è necessario chiamare il metodo statico [ `BindableProperty.CreateReadOnly` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateReadOnly/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) o un breve [ `BindableProperty.CreateReadOnly` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateReadOnly/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/) per definire un campo di sola lettura statico privato di tipo [ `BindablePropertyKey` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindablePropertyKey/).

Quindi, definire la proprietà CLR `set` come funzione di accesso agli `private` per chiamare un [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindablePropertyKey/System.Object/) eseguire l'overload con il `BindablePropertyKey` oggetto. Ciò impedisce che la proprietà viene impostata all'esterno della classe.

Questa funzionalità viene illustrata la [ `CountedLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) classe usata nel [ **BaskervillesCount** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount) esempio.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 11 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Esempi di capitolo 11](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
