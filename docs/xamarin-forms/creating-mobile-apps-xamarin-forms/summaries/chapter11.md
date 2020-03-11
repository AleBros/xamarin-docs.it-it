---
title: Riepilogo del capitolo 11. Infrastruttura associabile
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 11. Infrastruttura associabile'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: f9e3326c0f55469cfa84a019a674679d82dfc007
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334366"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Riepilogo del capitolo 11. Infrastruttura associabile

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

Ogni C# programmatore ha familiarità C# con le *proprietà*. Le proprietà contengono una funzione di accesso *set* e/o una funzione di accesso *Get* . Spesso sono denominate *proprietà CLR* per Common Language Runtime.

Novell. Forms definisce una definizione di proprietà avanzata denominata *proprietà associabile* incapsulata dalla classe [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) e supportata dalla classe [`BindableObject`](xref:Xamarin.Forms.BindableObject) . Queste classi sono correlate ma piuttosto distinte: il `BindableProperty` viene usato per definire la proprietà stessa; `BindableObject` è come `object` in quanto si tratta di una classe di base per le classi che definiscono proprietà associabili.

## <a name="the-xamarinforms-class-hierarchy"></a>La gerarchia di classi xamarin. Forms

Nell'esempio [**ClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) viene utilizzata la reflection per visualizzare una gerarchia di classi di Novell. Forms e viene illustrato il ruolo cruciale svolto da `BindableObject` in questa gerarchia. `BindableObject` deriva da `Object` e è la classe padre per [`Element`](xref:Xamarin.Forms.Element) da cui deriva [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Si tratta della classe padre per [`Page`](xref:Xamarin.Forms.Page) e [`View`](xref:Xamarin.Forms.View), che è la classe padre da [`Layout`](xref:Xamarin.Forms.Layout):

[![Schermata tripla della condivisione della gerarchia di classi](images/ch11fg01-small.png "Condivisione gerarchia classi")](images/ch11fg01-large.png#lightbox "Condivisione gerarchia classi")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Un'anteprima in BindableObject e BindableProperty

Nelle classi che derivano da `BindableObject` molte proprietà CLR sono dette "supportate da" proprietà associabili. Ad esempio, la proprietà [`Text`](xref:Xamarin.Forms.Label.Text) della classe `Label` è una proprietà CLR, ma la classe `Label` definisce anche un campo statico pubblico di sola lettura denominato [`TextProperty`](xref:Xamarin.Forms.Label.TextProperty) di tipo `BindableProperty`.

Un'applicazione può impostare o ottenere la proprietà `Text` di `Label` normalmente oppure l'applicazione può impostare il `Text` chiamando il metodo [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) definito da `BindableObject` con un argomento `Label.TextProperty`. Analogamente, un'applicazione può ottenere il valore della proprietà `Text` chiamando il metodo [`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) , nuovamente con un argomento `Label.TextProperty`. Questa operazione è illustrata nell'esempio [**PropertySettings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings) .

Il `Text` proprietà CLR viene infatti implementato interamente mediante i metodi `SetValue` e `GetValue` definiti da `BindableObject` insieme alla proprietà statica `Label.TextProperty`.

`BindableObject` e `BindableProperty` forniscono supporto per:

- Fornire i valori predefiniti delle proprietà
- Archiviare i relativi valori correnti
- Fornire meccanismi per la convalida dei valori delle proprietà
- Mantenimento della coerenza tra le proprietà correlate in una singola classe
- Rispondere alle modifiche delle proprietà
- Attivare le notifiche quando una proprietà sta per essere modificata o è stato modificato
- Supporto dell'associazione dati
- Il supporto di stili
- Supporto di risorse dinamiche

Ogni volta che una proprietà supportata da una proprietà associabile viene modificata, `BindableObject` genera un evento di [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) che identifica la proprietà che è stata modificata. Questo evento non viene generato quando la proprietà è impostata sullo stesso valore.

Alcune proprietà non sono supportate da proprietà associabili e alcune classi Novell. Forms &mdash; come `Span` &mdash; non derivano da `BindableObject`. Solo una classe che deriva da `BindableObject` può supportare proprietà associabili perché `BindableObject` definisce i metodi di `SetValue` e `GetValue`.

Poiché `Span` non deriva da `BindableObject`, nessuna delle proprietà &mdash; come `Text` &mdash; è supportata da una proprietà associabile. Questo è il motivo per cui un'impostazione `DynamicResource` nella proprietà `Text` di `Span` genera un'eccezione nell'esempio [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) del capitolo precedente. L'esempio [**DynamicVsStaticCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) illustra come impostare le risorse dinamiche nel codice usando il metodo [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource(Xamarin.Forms.BindableProperty,System.String)) definito da `Element`. Il primo argomento è un oggetto di tipo `BindableProperty`.

Analogamente, il metodo [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) definito da `BindableObject` dispone di un primo argomento di tipo `BindableProperty`.

## <a name="defining-bindable-properties"></a>La definizione di proprietà associabili

È possibile definire proprietà associabili personalizzate utilizzando il metodo statico [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) per creare un campo statico di sola lettura di tipo `BindableProperty`.

Questa operazione viene illustrata nella classe [`AltLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . La classe deriva da `Label` e consente di specificare le dimensioni del carattere in punti. Viene illustrato nell'esempio [**PointSizedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText) .

Sono necessari quattro argomenti del metodo `BindableProperty.Create`:

- `propertyName`: il nome di testo della proprietà (uguale al nome della proprietà CLR)
- `returnType`: tipo della proprietà CLR
- `declaringType`: tipo della classe che dichiara la proprietà.
- `defaultValue`: valore predefinito della proprietà.

Poiché `defaultValue` è di tipo `object`, il compilatore deve essere in grado di determinare il tipo del valore predefinito. Se, ad esempio, il `returnType` è `double`, il `defaultValue` deve essere impostato su un valore come 0,0 anziché solo su 0 oppure il tipo non corrispondente attiverà un'eccezione in fase di esecuzione.

È anche molto comune per una proprietà associabile da includere:

- `propertyChanged`: metodo statico chiamato quando la proprietà cambia valore. Il primo argomento è l'istanza della classe di cui è stata modificata.

Gli altri argomenti per `BindableProperty.Create` non sono comuni:

- `defaultBindingMode`: utilizzato in relazione al data binding, come illustrato nel [**capitolo 16. Data Binding**](chapter16.md))
- `validateValue`: callback per verificare la presenza di un valore valido
- `propertyChanging`: un callback per indicare quando la proprietà sta per essere modificata
- `coerceValue`: un callback per forzare un valore impostato su un altro valore
- `defaultValueCreate`: un callback per creare un valore predefinito che non può essere condiviso tra le istanze della classe (ad esempio, una raccolta)

### <a name="the-read-only-bindable-property"></a>La proprietà associabile di sola lettura

Sola lettura, può essere una proprietà associabile. La creazione di una proprietà associabile di sola lettura richiede la chiamata al metodo statico [`BindableProperty.CreateReadOnly`](xref:Xamarin.Forms.BindableProperty.CreateReadOnly(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) per definire un campo statico privato di sola lettura di tipo [`BindablePropertyKey`](xref:Xamarin.Forms.BindablePropertyKey).

Definire quindi la proprietà CLR `set` accesore come `private` per chiamare un overload [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindablePropertyKey,System.Object)) con l'oggetto `BindablePropertyKey`. Ciò impedisce che la proprietà viene impostata all'esterno della classe.

Questa operazione viene illustrata nella classe [`CountedLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) utilizzata nell'esempio [**BaskervillesCount**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount) .

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 11 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Esempi del capitolo 11](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [Proprietà associabili](~/xamarin-forms/xaml/bindable-properties.md)
