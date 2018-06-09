---
title: Riepilogo del capitolo 10. Estensioni di markup XAML
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 10. Estensioni di markup XAML'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: cc6c3154b7e6535fa7528032fb7a91ad90a0a7f8
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241100"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Riepilogo del capitolo 10. Estensioni di markup XAML

In genere, il parser XAML converte qualsiasi stringa impostata come valore di attributo per il tipo della proprietà in base a conversioni standard per i tipi di dati .NET di base, o un [ `TypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverter/) derivato associata alla proprietà o il tipo con un [`TypeConverterAttribute`](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverterAttribute/).

Ma in alcuni casi è consigliabile impostare un attributo da un'origine diversa, ad esempio, un elemento in un dizionario o il valore di una proprietà statica o un campo, o da un calcolo di qualche tipo.

Si tratta del processo di un *estensione di markup XAML*. Nonostante il nome, le estensioni di markup XAML sono *non* estensione XML. XAML è sempre XML valido.

## <a name="the-code-infrastructure"></a>L'infrastruttura di codice

Un'estensione di markup XAML è una classe che implementa il [ `IMarkupExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension/) interfaccia. Tale classe è spesso la parola `Extension` alla fine del relativo nome ma in genere viene visualizzato in XAML senza il suffisso.

Le seguenti estensioni di markup XAML sono supportate da tutte le implementazioni di XAML:

- `x:Static` Supportato da [`StaticExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/)
- `x:Reference` Supportato da [`ReferenceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/)
- `x:Type` Supportato da [`TypeExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TypeExtension/)
- `x:Null` Supportato da [`NullExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.NullExtension/)
- `x:Array` Supportato da [`ArrayExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ArrayExtension/)

Queste quattro estensioni di markup XAML sono supportate da molte implementazioni di XAML, tra cui xamarin. Forms:

- `StaticResource` Supportato da [`StaticResourceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticResourceExtension/)
- `DynamicResource` Supportato da [`DynamicResourceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.DynamicResourceExtension/)
- `Binding` supportato da [ `BindingExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/) &mdash;descritti in [capitolo 16. Data binding](#chapter16)
- `TemplateBinding` supportato da [ `TemplateBindingExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TemplateBindingExtension/) &mdash;non trattati nella Rubrica

Un'estensione di markup XAML aggiuntiva è incluso in xamarin. Forms in relazione a [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/):

- [`ConstraintExpression`](https://developer.xamarin.com/api/type/Xamarin.Forms.ConstraintExpression/)&mdash;non è coperto nella Rubrica

## <a name="accessing-static-members"></a>Accesso ai membri statici

Utilizzare il [ `x:Static` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/) elemento da impostare il valore di un membro dell'enumerazione, un campo o proprietà statico pubblico di un attributo. Impostare il [ `Member` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.StaticExtension.Member/) proprietà sul membro statico. È in genere più semplice specificare `x:Static` e il nome del membro tra parentesi graffe. Il nome della `Member` proprietà non è necessario includere, solo il membro stesso. Questo tipo di sintassi è illustrata nella [ **SharedStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) esempio. I campi statici stessi sono definiti nel [ `AppConstants` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) classe. Questa tecnica consente di definire le costanti utilizzate tramite un programma.

Con una dichiarazione di spazio dei nomi XML aggiuntiva, è possibile fare riferimento proprietà statica pubblica, campi o membri di enumerazione definiti in .NET framework, come dimostrato nel [ **SystemStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics) esempio .

## <a name="resource-dictionaries"></a>Dizionari delle risorse

Il `VisualElement` classe definisce una proprietà denominata [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) che è possibile impostare per un oggetto di tipo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/). All'interno di XAML, è possibile archiviare gli elementi in questo dizionario e identificarli con il `x:Key` attributo. Gli elementi archiviati nel dizionario risorse sono condivise da tutti i riferimenti all'elemento.

### <a name="staticresource-for-most-purposes"></a>StaticResource maggior parte dei casi

Nella maggior parte dei casi si userà il [ `StaticResource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticResourceExtension/) per fare riferimento a un elemento dal dizionario risorse, come illustrato dall'estensione di markup di [ **ResourceSharing** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) esempio . È possibile utilizzare un `StaticResourceExtension` elemento o `StaticResource` tra parentesi graffe:

[![Schermata triplo della condivisione delle risorse](images/ch10fg03-small.png "la condivisione delle risorse")](images/ch10fg03-large.png#lightbox "la condivisione delle risorse")

Non confondere il `x:Static` estensione di markup e `StaticResource` estensione di markup.

### <a name="a-tree-of-dictionaries"></a>Una struttura ad albero di dizionari

Quando il parser XAML rileva un `StaticResource`, inizia la ricerca di struttura ad albero visuale per una chiave corrispondente e successivamente analizza il `ResourceDictionary` dell'applicazione `App` classe. In questo modo gli elementi in un dizionario risorse approfondito la struttura ad albero visuale per eseguire l'override di un dizionario risorse più in alto nell'albero visuale. Questa funzionalità viene illustrata la [ **ResourceTrees** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees) esempio.

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource per scopi speciali

Il `StaticResource` estensione di markup, un elemento da recuperare dal dizionario quando un struttura ad albero visuale viene compilato durante la `InitializeComponent` chiamare. Un'alternativa al `StaticResource` è [ `DynamicResource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.DynamicResourceExtension/), che mantiene un collegamento per la chiave del dizionario e aggiornare la destinazione quando l'elemento a cui fa riferimento la modifica di chiavi.

La differenza tra `StaticResource` e `DynamicResource` viene illustrata la [ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) esempio.

Una proprietà impostata da `DynamicResource` deve essere supportata da una proprietà associabile, come descritto in [capitolo 11, l'infrastruttura associabile](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Estensioni di markup meno utilizzata

Utilizzare il [ `x:Null` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.NullExtension/) estensione di markup per impostare una proprietà `null`.

Utilizzare il [ `x:Type` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TypeExtension/) estensione di markup per impostare una proprietà .NET `Type` oggetto.

Utilizzare [ `x:Array` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ArrayExtension/) per definire una matrice. Specificare il tipo di membri della matrice impostando il [`Type`] proprietà da un `x:Type` estensione di markup.

## <a name="a-custom-markup-extension"></a>Un'estensione di markup personalizzata

È possibile creare estensioni di markup XAML scrivendo una classe che implementa il [ `IMarkupExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension/) interfacciarsi con un [ `ProvideValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue/p/System.IServiceProvider/) metodo.

Il [ `HslColorExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) classe soddisfa tali requisiti. Crea un valore di tipo `Color` in base ai valori di proprietà denominate `H`, `S`, `L`, e `A`. Questa classe è il primo elemento in una libreria di xamarin. Forms denominata [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) che viene generato e utilizzato nel corso di questo manuale.

Il [ **CustomExtensionDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) esempio viene illustrato come fare riferimento a questa libreria e usare l'estensione di markup personalizzata.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 10 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Esempi di capitolo 10](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
