---
title: Riepilogo del capitolo 11. Infrastruttura associabile
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 11. Infrastruttura associabile'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: edc3dfd97457fe93a04edd82574f6ed419f5fdc1
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136799"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Riepilogo del capitolo 11. Infrastruttura associabile

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

Ogni programmatore C# ha familiarità con le *Proprietà*c#. Le proprietà contengono una funzione di accesso *set* e/o una funzione di accesso *Get* . Spesso sono denominate *proprietà CLR* per Common Language Runtime.

Xamarin.Formsdefinisce una definizione di proprietà avanzata denominata una *proprietà associabile* incapsulata dalla [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) classe e supportata dalla [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe. Queste classi sono correlate ma piuttosto distinte: l'oggetto `BindableProperty` viene usato per definire la proprietà stessa; `BindableObject` è come `object` in quanto si tratta di una classe di base per le classi che definiscono proprietà associabili.

## <a name="the-xamarinforms-class-hierarchy"></a>Xamarin.FormsGerarchia di classi

Nell'esempio [**ClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) viene utilizzata la reflection per visualizzare una gerarchia di classi di Xamarin.Forms e viene illustrato il ruolo cruciale svolto da `BindableObject` in questa gerarchia. `BindableObject`deriva da `Object` e è la classe padre da [`Element`](xref:Xamarin.Forms.Element) cui [`VisualElement`](xref:Xamarin.Forms.VisualElement) deriva. Si tratta della classe padre di [`Page`](xref:Xamarin.Forms.Page) e [`View`](xref:Xamarin.Forms.View) , che è la classe padre per [`Layout`](xref:Xamarin.Forms.Layout) :

[![Schermata tripla della condivisione della gerarchia di classi](images/ch11fg01-small.png "Condivisione gerarchia classi")](images/ch11fg01-large.png#lightbox "Condivisione gerarchia classi")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Visualizzazione di BindableObject e BindableProperty

Nelle classi che derivano da `BindableObject` molte proprietà CLR si dice che le proprietà associabili sono "supportate da". Ad esempio, la [`Text`](xref:Xamarin.Forms.Label.Text) proprietà della `Label` classe è una proprietà CLR, ma la `Label` classe definisce anche un campo statico pubblico di sola lettura denominato [`TextProperty`](xref:Xamarin.Forms.Label.TextProperty) di tipo `BindableProperty` .

Un'applicazione può impostare o ottenere la `Text` proprietà di `Label` in modo normale oppure l'applicazione può impostare chiamando `Text` [ `SetValue` ] (xrif: Xamarin.Forms . BindableObject. SetValue ( Xamarin.Forms . Metodo BindableProperty, System. Object)) definito da `BindableObject` con un `Label.TextProperty` argomento. Analogamente, un'applicazione può ottenere il valore della `Text` proprietà chiamando [ `GetValue` ] (xrif: Xamarin.Forms . BindableObject. GetValue ( Xamarin.Forms . BindableProperty)), un nuovo metodo con un `Label.TextProperty` argomento. Questa operazione è illustrata nell'esempio [**PropertySettings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings) .

La `Text` proprietà CLR viene infatti implementata interamente utilizzando i `SetValue` `GetValue` metodi e definiti da `BindableObject` insieme alla `Label.TextProperty` proprietà statica.

`BindableObject`e `BindableProperty` forniscono supporto per:

- Assegnazione di valori predefiniti alle proprietà
- Archiviazione dei valori correnti
- Fornire meccanismi per la convalida dei valori delle proprietà
- Gestione della coerenza tra le proprietà correlate in una singola classe
- Risposta alle modifiche delle proprietà
- Attivazione di notifiche quando una proprietà sta per essere modificata o è cambiata
- Supporto di data binding
- Stili di supporto
- Supporto di risorse dinamiche

Ogni volta che una proprietà supportata da una proprietà associabile viene modificata, `BindableObject` genera un [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento che identifica la proprietà che è stata modificata. Questo evento non viene generato quando la proprietà è impostata sullo stesso valore.

Alcune proprietà non sono supportate da proprietà associabili e alcune Xamarin.Forms classi, &mdash; ad esempio, non `Span` &mdash; derivano da `BindableObject` . Solo una classe che deriva da `BindableObject` può supportare proprietà associabili perché `BindableObject` definisce i `SetValue` `GetValue` metodi e.

Poiché non `Span` deriva da `BindableObject` , nessuna delle proprietà, &mdash; ad esempio, `Text` &mdash; è supportata da una proprietà associabile. Questo è il motivo per cui un' `DynamicResource` impostazione della `Text` proprietà di `Span` genera un'eccezione nell'esempio [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) del capitolo precedente. Nell'esempio [**DynamicVsStaticCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) viene illustrato come impostare le risorse dinamiche nel codice utilizzando [ `SetDynamicResource` ] (xrif: Xamarin.Forms . Elemento. SetDynamicResource ( Xamarin.Forms . Metodo BindableProperty, System. String)) definito da `Element` . Il primo argomento è un oggetto di tipo `BindableProperty` .

Analogamente, [ `SetBinding` ] (xrif: Xamarin.Forms . BindableObject. SetBinding ( Xamarin.Forms . BindableProperty, Xamarin.Forms . BindingBase)) il metodo definito da `BindableObject` ha un primo argomento di tipo `BindableProperty` .

## <a name="defining-bindable-properties"></a>Definizione di proprietà associabili

È possibile definire proprietà associabili personalizzate utilizzando l'oggetto statico [ `BindableProperty.Create` ] (xrif: Xamarin.Forms . BindableProperty. Create (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . BindableProperty. ValidateValueDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangedDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangingDelegate, Xamarin.Forms . BindableProperty. CoerceValueDelegate, Xamarin.Forms . BindableProperty. CreateDefaultValueDelegate)) per creare un campo statico di sola lettura di tipo `BindableProperty` .

Questa operazione viene illustrata nella [`AltLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . La classe deriva da `Label` e consente di specificare le dimensioni del carattere in punti. Viene illustrato nell'esempio [**PointSizedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText) .

Sono necessari quattro argomenti del `BindableProperty.Create` Metodo:

- `propertyName`: nome di testo della proprietà (uguale al nome della proprietà CLR).
- `returnType`: tipo della proprietà CLR.
- `declaringType`: tipo della classe che dichiara la proprietà.
- `defaultValue`: valore predefinito della proprietà.

Poiché `defaultValue` è di tipo `object` , il compilatore deve essere in grado di determinare il tipo del valore predefinito. Se, ad esempio, `returnType` è `double` , `defaultValue` deve essere impostato su un valore simile a 0,0 anziché su 0 oppure la mancata corrispondenza del tipo attiverà un'eccezione in fase di esecuzione.

È inoltre molto comune che una proprietà associabile includa:

- `propertyChanged`: metodo statico chiamato quando la proprietà cambia valore. Il primo argomento è l'istanza della classe la cui proprietà è stata modificata.

Gli altri argomenti di `BindableProperty.Create` non sono comuni:

- `defaultBindingMode`: usato in connessione con data binding (come illustrato nel [**capitolo 16. Data Binding**](chapter16.md))
- `validateValue`: callback per verificare la presenza di un valore valido
- `propertyChanging`: callback per indicare quando la proprietà sta per essere modificata.
- `coerceValue`: callback per assegnare un valore impostato a un altro valore
- `defaultValueCreate`: callback per creare un valore predefinito che non può essere condiviso tra le istanze della classe (ad esempio, una raccolta)

### <a name="the-read-only-bindable-property"></a>Proprietà associabile di sola lettura

Una proprietà associabile può essere di sola lettura. Per creare una proprietà associabile di sola lettura è necessario chiamare il metodo statico [ `BindableProperty.CreateReadOnly` ] (xrif: Xamarin.Forms . BindableProperty. CreateReadOnly (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . BindableProperty. ValidateValueDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangedDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangingDelegate, Xamarin.Forms . BindableProperty. CoerceValueDelegate, Xamarin.Forms . BindableProperty. CreateDefaultValueDelegate)) per definire un campo privato statico di sola lettura di tipo [`BindablePropertyKey`](xref:Xamarin.Forms.BindablePropertyKey) .

Definire quindi l'accesso alla proprietà CLR `set` come `private` per chiamare [ `SetValue` ] (xrif: Xamarin.Forms . BindableObject. SetValue ( Xamarin.Forms . BindablePropertyKey, System. Object)) con l' `BindablePropertyKey` oggetto. In questo modo si impedisce l'impostazione della proprietà all'esterno della classe.

Questa operazione viene illustrata nella [`CountedLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) classe utilizzata nell'esempio [**BaskervillesCount**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount) .

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 11 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Esempi del capitolo 11](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [Proprietà associabili](~/xamarin-forms/xaml/bindable-properties.md)
