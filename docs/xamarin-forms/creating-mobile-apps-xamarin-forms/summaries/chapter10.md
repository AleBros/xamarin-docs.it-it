---
title: Riepilogo del capitolo 10. Estensioni di markup XAML
description: 'Creazione di App per dispositivi mobili con Xamarin.Forms: Riepilogo del capitolo 10. Estensioni di markup XAML'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 076e9f5155492e5a69d906c587b24495fe39d3f1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334328"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Riepilogo del capitolo 10. Estensioni di markup XAML

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

In genere, il parser XAML converte qualsiasi stringa impostata come valore di attributo per il tipo della proprietà in base a conversioni standard per i tipi di dati .NET di base, o un [ `TypeConverter` ](xref:Xamarin.Forms.TypeConverter) derivato associata alla proprietà o il relativo tipo con un [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute).

Ma in alcuni casi è consigliabile impostare un attributo da un'origine diversa, ad esempio, un elemento in un dizionario o il valore di una proprietà statica o un campo, o da un calcolo di qualche tipo.

Questo è il processo di un *estensione di markup XAML*. Nonostante il nome, le estensioni di markup XAML vengono *non* un'estensione XML. XAML è sempre XML validi.

## <a name="the-code-infrastructure"></a>L'infrastruttura di codice

Un'estensione di markup XAML è una classe che implementa il [ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension) interfaccia. Tale classe spesso contiene la parola `Extension` alla fine del relativo nome ma in genere viene visualizzato in XAML senza tale suffisso.

Le seguenti estensioni di markup XAML sono supportate da tutte le implementazioni di XAML:

- `x:Static` Supportato da [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference` Supportato da [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type` Supportato da [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null` Supportato da [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array` Supportato da [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

Queste quattro estensioni di markup XAML sono supportate da molte implementazioni di XAML, inclusi Xamarin.Forms:

- `StaticResource` Supportato da [`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource` Supportato da [`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding` supportato da [ `BindingExtension` ](xref:Xamarin.Forms.Xaml.BindingExtension) &mdash;illustrati in [capitolo 16. Data binding](chapter16.md)
- `TemplateBinding` supportato da [ `TemplateBindingExtension` ](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) &mdash;non incluse nel libro

Incluso in Xamarin.Forms in relazione a un'estensione di markup XAML aggiuntiva [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout):

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;non incluse nel libro

## <a name="accessing-static-members"></a>L'accesso ai membri statici

Usare la [ `x:Static` ](xref:Xamarin.Forms.Xaml.StaticExtension) elemento su cui impostare un attributo il valore di un membro dell'enumerazione, un campo o proprietà statico pubblico. Impostare il [ `Member` ](xref:Xamarin.Forms.Xaml.StaticExtension.Member) proprietà sul membro statico. È in genere più semplice specificare `x:Static` e il nome del membro tra parentesi graffe. Il nome del `Member` proprietà non deve essere incluso, solo il membro stesso. Questa sintassi comune che viene visualizzata nei [ **SharedStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) esempio. I campi statici stessi sono definiti nel [ `AppConstants` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) classe. Questa tecnica consente di definire le costanti usate tramite un programma.

Con una dichiarazione di spazio dei nomi XML aggiuntiva, è possibile fare riferimento a proprietà statiche pubbliche, campi o membri di enumerazione definiti in .NET framework, come illustrato nel [ **SystemStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics) esempio .

## <a name="resource-dictionaries"></a>Dizionari risorse

Il `VisualElement` classe definisce una proprietà denominata [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) che è possibile impostare per un oggetto di tipo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). In XAML, è possibile archiviare gli elementi in questo dizionario e identificarli con il `x:Key` attributo. Gli elementi archiviati nel dizionario risorse sono condivise tra tutti i riferimenti all'elemento.

### <a name="staticresource-for-most-purposes"></a>StaticResource maggior parte dei casi

Nella maggior parte dei casi si userà il [ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension) estensione di markup per fare riferimento a un elemento dal dizionario risorse, come dimostrato dal [ **ResourceSharing** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) esempio . È possibile usare una `StaticResourceExtension` elemento o `StaticResource` tra parentesi graffe:

[![Screenshot triplo della condivisione di risorse](images/ch10fg03-small.png "Resource Sharing, condivisione")](images/ch10fg03-large.png#lightbox "la condivisione delle risorse")

Non confondere la `x:Static` estensione di markup e il `StaticResource` estensione di markup.

### <a name="a-tree-of-dictionaries"></a>Un albero di dizionari

Quando il parser XAML rileva un `StaticResource`, inizia a cercare la struttura ad albero visuale per una chiave corrispondente e successivamente analizza il `ResourceDictionary` dell'applicazione `App` classe. In questo modo gli elementi in un dizionario di risorse più approfondito la struttura ad albero visuale per eseguire l'override di un dizionario di risorse superiore nell'albero visuale. Questa funzionalità viene illustrata la [ **ResourceTrees** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees) esempio.

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource per scopi speciali

Il `StaticResource` estensione di markup fa sì che un elemento da recuperare dal dizionario quando un struttura ad albero visuale viene compilato durante la `InitializeComponent` chiamare. Un'alternativa al `StaticResource` viene [ `DynamicResource` ](xref:Xamarin.Forms.Xaml.DynamicResourceExtension), che mantiene un collegamento alla chiave del dizionario e aggiorna la destinazione quando l'elemento a cui fanno riferimento le modifiche principali.

La differenza tra `StaticResource` e `DynamicResource` viene illustrata la [ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) esempio.

Una proprietà impostata dal `DynamicResource` deve essere supportata da una proprietà associabile, come illustrato nella [capitolo 11, l'infrastruttura associabile](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Estensioni di markup meno utilizzata

Usare la [ `x:Null` ](xref:Xamarin.Forms.Xaml.NullExtension) per impostare una proprietà estensione di markup `null`.

Usare la [ `x:Type` ](xref:Xamarin.Forms.Xaml.TypeExtension) estensione di markup per impostare una proprietà a un .NET `Type` oggetto.

Uso [ `x:Array` ](xref:Xamarin.Forms.Xaml.ArrayExtension) per definire una matrice. Specificare il tipo dei membri della matrice impostando il [`Type`] proprietà da un `x:Type` estensione di markup.

## <a name="a-custom-markup-extension"></a>Un'estensione di markup personalizzata

È possibile creare le proprie estensioni di markup XAML scrivendo una classe che implementa il [ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension) interfacciarsi con un [ `ProvideValue` ](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider)) (metodo).

Il [ `HslColorExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) classe soddisfa tali requisiti. Crea un valore di tipo `Color` in base ai valori di proprietà denominate `H`, `S`, `L`, e `A`. Questa classe è il primo elemento in una libreria Xamarin.Forms denominata [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) che è compilato e usato nel corso di questo manuale.

Il [ **CustomExtensionDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) esempio viene illustrato come fare riferimento a questa raccolta e usare l'estensione di markup personalizzata.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 10 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Capitolo 10 campioni](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [Estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
