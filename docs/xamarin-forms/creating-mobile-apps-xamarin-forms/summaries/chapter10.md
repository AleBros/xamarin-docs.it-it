---
title: Riepilogo del capitolo 10. Estensioni di markup XAML
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 10. Estensioni di markup XAML'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 076e9f5155492e5a69d906c587b24495fe39d3f1
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334328"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Riepilogo del capitolo 10. Estensioni di markup XAML

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

In genere, il parser XAML converte qualsiasi stringa impostata come valore di attributo nel tipo della proprietà in base alle conversioni standard per i tipi di dati .NET di base o un [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) derivato associato alla proprietà o al relativo tipo con un [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute).

Ma in alcuni casi è consigliabile impostare un attributo da un'origine diversa, ad esempio, un elemento in un dizionario o il valore di una proprietà statica o un campo, o da un calcolo di qualche tipo.

Si tratta del processo di un' *estensione di markup XAML*. Nonostante il nome, le estensioni di markup XAML *non* sono un'estensione di XML. XAML è sempre XML validi.

## <a name="the-code-infrastructure"></a>L'infrastruttura di codice

Un'estensione di markup XAML è una classe che implementa l'interfaccia [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) . Una classe di questo tipo ha spesso la parola `Extension` alla fine del nome, ma in genere viene visualizzata in XAML senza tale suffisso.

Le seguenti estensioni di markup XAML sono supportate da tutte le implementazioni di XAML:

- `x:Static` supportato da [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference` supportato da [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type` supportato da [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null` supportato da [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array` supportato da [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

Queste quattro estensioni di markup XAML sono supportate da molte implementazioni di XAML, inclusi xamarin. Forms:

- `StaticResource` supportato da [`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource` supportato da [`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding` supportato da [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension)&mdash;descritto nel [capitolo 16. Data Binding](chapter16.md)
- `TemplateBinding` supportato da [`TemplateBindingExtension`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension)&mdash;non trattato nel libro

Un'estensione di markup XAML aggiuntiva è inclusa in Novell. Forms in connessione con [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout):

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) non&mdash;trattati nel libro

## <a name="accessing-static-members"></a>L'accesso ai membri statici

Usare l'elemento [`x:Static`](xref:Xamarin.Forms.Xaml.StaticExtension) per impostare un attributo sul valore di una proprietà statica pubblica, di un campo o di un membro di enumerazione. Impostare la proprietà [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) sul membro statico. In genere è più facile specificare `x:Static` e il nome del membro tra parentesi graffe. Non è necessario includere il nome della proprietà `Member`, bensì solo il membro stesso. Questa sintassi comune è illustrata nell'esempio [**SharedStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) . I campi statici vengono definiti nella classe [`AppConstants`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) . Questa tecnica consente di definire le costanti usate tramite un programma.

Con una dichiarazione di spazio dei nomi XML aggiuntiva, è possibile fare riferimento a proprietà statiche pubbliche, campi o membri di enumerazione definiti in .NET Framework, come illustrato nell'esempio [**SystemStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics) .

## <a name="resource-dictionaries"></a>Dizionari di risorse

La classe `VisualElement` definisce una proprietà denominata [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) che è possibile impostare su un oggetto di tipo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). In XAML è possibile archiviare gli elementi in questo dizionario e identificarli con l'attributo `x:Key`. Gli elementi archiviati nel dizionario risorse sono condivise tra tutti i riferimenti all'elemento.

### <a name="staticresource-for-most-purposes"></a>StaticResource maggior parte dei casi

Nella maggior parte dei casi si userà l'estensione di markup [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) per fare riferimento a un elemento del dizionario risorse, come illustrato nell'esempio [**ResourceSharing**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) . È possibile usare un elemento `StaticResourceExtension` o `StaticResource` tra parentesi graffe:

[![Schermata tripla della condivisione delle risorse](images/ch10fg03-small.png "Condivisione delle risorse")](images/ch10fg03-large.png#lightbox "Condivisione delle risorse")

Non confondere il `x:Static` estensione di markup e l'`StaticResource` estensione di markup.

### <a name="a-tree-of-dictionaries"></a>Un albero di dizionari

Quando il parser XAML rileva un `StaticResource`, inizia a cercare una chiave corrispondente nella struttura ad albero visuale e quindi Cerca nel `ResourceDictionary` della classe `App` dell'applicazione. In questo modo gli elementi in un dizionario di risorse più approfondito la struttura ad albero visuale per eseguire l'override di un dizionario di risorse superiore nell'albero visuale. Questa operazione è illustrata nell'esempio [**ResourceTrees**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees) .

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource per scopi speciali

Il `StaticResource` estensione di markup fa sì che un elemento venga recuperato dal dizionario quando viene compilata una struttura ad albero visuale durante la chiamata di `InitializeComponent`. Un'alternativa a `StaticResource` è [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension), che mantiene un collegamento alla chiave del dizionario e aggiorna la destinazione quando l'elemento a cui fa riferimento la chiave viene modificato.

Nell'esempio [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) viene illustrata la differenza tra `StaticResource` e `DynamicResource`.

Una proprietà impostata da `DynamicResource` deve essere supportata da una proprietà associabile, come illustrato nel [capitolo 11, l'infrastruttura associabile](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Estensioni di markup meno utilizzata

Utilizzare l'estensione di markup [`x:Null`](xref:Xamarin.Forms.Xaml.NullExtension) per impostare una proprietà su `null`.

Utilizzare l'estensione di markup [`x:Type`](xref:Xamarin.Forms.Xaml.TypeExtension) per impostare una proprietà su un oggetto .NET `Type`.

Usare [`x:Array`](xref:Xamarin.Forms.Xaml.ArrayExtension) per definire una matrice. Specificare il tipo di membri della matrice impostando la proprietà [`Type`] su un'estensione di markup `x:Type`.

## <a name="a-custom-markup-extension"></a>Un'estensione di markup personalizzata

È possibile creare estensioni di markup XAML scrivendo una classe che implementa l'interfaccia [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) con un metodo di [`ProvideValue`](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider)) .

La classe [`HslColorExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) soddisfa tali requisiti. Viene creato un valore di tipo `Color` in base ai valori delle proprietà denominate `H`, `S`, `L`e `A`. Questa classe è il primo elemento di una libreria Novell. Forms denominato [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) , che è stato compilato e utilizzato nel corso di questo libro.

Nell'esempio [**CustomExtensionDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) viene illustrato come fare riferimento a questa libreria e utilizzare l'estensione di markup personalizzata.

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 10 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Esempi del capitolo 10](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [Estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
