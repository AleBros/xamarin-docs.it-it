---
title: Riassunto del capitolo 10. Estensioni di markup XAML
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 10. Estensioni di markup XAML'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 076e9f5155492e5a69d906c587b24495fe39d3f1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334328"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Riassunto del capitolo 10. Estensioni di markup XAML

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

In genere, il parser XAML converte qualsiasi stringa impostata come valore di attributo nel tipo della [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) proprietà in base alle conversioni [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute)standard per i tipi di dati .NET di base o a un derivaassociato alla proprietà o al relativo tipo con un oggetto .

Ma a volte è conveniente impostare un attributo da un'origine diversa, ad esempio, un elemento in un dizionario, o il valore di una proprietà o un campo statico o da un calcolo di qualche tipo.

Questo è il lavoro di *un'estensione*di markup XAML. Nonostante il nome, le estensioni di markup XAML *non* sono un'estensione di XML. XAML è sempre XML legale.

## <a name="the-code-infrastructure"></a>L'infrastruttura del codice

Un'estensione di markup XAML [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) è una classe che implementa l'interfaccia. Tale classe ha spesso `Extension` la parola alla fine del nome, ma di solito appare in XAML senza tale suffisso.

Le estensioni di markup XAML seguenti sono supportate da tutte le implementazioni di XAML:The following XAML markup extensions are supported by all implementations of XAML:

- `x:Static`supportato da[`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference`supportato da[`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type`supportato da[`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null`supportato da[`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array`supportato da[`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

Queste quattro estensioni di markup XAML sono supportate da molte implementazioni di XAML, tra cui Xamarin.Forms:These four XAML markup extensions are supported by many implementations of XAML, including Xamarin.Forms:

- `StaticResource`supportato da[`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource`supportato da[`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding`[`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) &mdash;con il conto della discussione nel [capitolo 16. Associazione dati](chapter16.md)
- `TemplateBinding`supportato [`TemplateBindingExtension`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) &mdash;da non coperto nel libro

Un'estensione di markup XAML aggiuntiva è inclusa [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)in Xamarin.Forms in relazione a :

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;non coperto nel libro

## <a name="accessing-static-members"></a>Accesso ai membri staticiAccessing static members

Utilizzare [`x:Static`](xref:Xamarin.Forms.Xaml.StaticExtension) l'elemento per impostare un attributo sul valore di una proprietà statica pubblica, un campo o un membro di enumerazione. Impostare [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) la proprietà sul membro statico. In genere è più `x:Static` semplice specificare e il nome del membro tra parentesi graffe. Non è `Member` necessario includere il nome della proprietà, ma solo il membro stesso. Questa sintassi comune è illustrata nell'esempio [**SharedStatics.This**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) common syntax is shown in the SharedStatics sample. I campi statici stessi [`AppConstants`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) sono definiti nella classe. Questa tecnica consente di stabilire costanti utilizzate tramite un programma.

Con una dichiarazione dello spazio dei nomi XML aggiuntiva, è possibile fare riferimento a proprietà statiche pubbliche, campi o membri di enumerazione definiti nel framework .NET, come illustrato nell'esempio [**SystemStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics) .

## <a name="resource-dictionaries"></a>Dizionari di risorse

La `VisualElement` classe definisce [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) una proprietà denominata che [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)è possibile impostare su un oggetto di tipo . All'interno di XAML, è possibile archiviare `x:Key` gli elementi in questo dizionario e identificarli con l'attributo. Gli elementi archiviati nel dizionario risorse vengono condivisi tra tutti i riferimenti all'elemento.

### <a name="staticresource-for-most-purposes"></a>StaticResource per la maggior parte degli scopi

Nella maggior parte dei [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) casi si userà l'estensione di markup per fare riferimento a un elemento dal dizionario risorse, come illustrato nell'esempio [**ResourceSharing.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) È possibile `StaticResourceExtension` utilizzare `StaticResource` un elemento o tra parentesi graffe:You can use a element or within curly braces:

[![Triplo screenshot della condivisione delle risorse](images/ch10fg03-small.png "Condivisione di risorse")](images/ch10fg03-large.png#lightbox "Condivisione di risorse")

Non confondere `x:Static` l'estensione `StaticResource` di markup e l'estensione di markup.

### <a name="a-tree-of-dictionaries"></a>Un albero di dizionari

Quando il parser XAML `StaticResource`rileva un oggetto , inizia la ricerca di una `ResourceDictionary` chiave corrispondente `App` nella struttura ad albero visuale, quindi cerca nella classe dell'applicazione. Ciò consente agli elementi in un dizionario risorse più in profondità nella struttura ad albero visuale di eseguire l'override di un dizionario risorse più in alto nella struttura ad albero visuale. Ciò è illustrato nell'esempio [**ResourceTrees.This**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees) is demonstrated in the ResourceTrees sample.

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource per scopi speciali

L'estensione `StaticResource` di markup determina il recupero di un elemento `InitializeComponent` dal dizionario quando una struttura ad albero visuale viene compilata durante la chiamata. Un'alternativa `StaticResource` [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)a è , che mantiene un collegamento alla chiave del dizionario e aggiorna la destinazione quando l'elemento a cui fa riferimento la chiave viene modificato.

La differenza `StaticResource` `DynamicResource` tra e viene illustrata nel [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) esempio.

Una proprietà `DynamicResource` impostata da deve essere supportata da una proprietà associabile come descritto nel [capitolo 11, L'infrastruttura associabile](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Estensioni di markup meno utilizzate

Utilizzare [`x:Null`](xref:Xamarin.Forms.Xaml.NullExtension) l'estensione di markup `null`per impostare una proprietà su .

Utilizzare [`x:Type`](xref:Xamarin.Forms.Xaml.TypeExtension) l'estensione di markup per `Type` impostare una proprietà su un oggetto .NET.

Utilizzare [`x:Array`](xref:Xamarin.Forms.Xaml.ArrayExtension) per definire una matrice. Specificare il tipo dei membri`Type`della matrice `x:Type` impostando la proprietà [ ] su un'estensione di markup.

## <a name="a-custom-markup-extension"></a>Un'estensione di markup personalizzata

Puoi creare estensioni di markup XAML personalizzate [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) scrivendo una [`ProvideValue`](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider)) classe che implementa l'interfaccia con un metodo.

La [`HslColorExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) classe soddisfa tali requisiti. Viene creato un `Color` valore di tipo `H`in `S` `L`base `A`ai valori delle proprietà denominate , , , e . Questa classe è il primo elemento in una libreria Xamarin.Forms [**denominata Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) che viene costruito e utilizzato nel corso di questo libro.

[**Nell'esempio CustomExtensionDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) viene illustrato come fare riferimento a questa libreria e utilizzare l'estensione di markup personalizzata.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 10 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Capitolo 10 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [Estensioni di markup XAMLXAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md)
