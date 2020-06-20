---
title: Riepilogo del capitolo 10. Estensioni di markup XAML
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 10. Estensioni di markup XAML'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8f23034df684e778677e4f2e480e1c41807536fb
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136812"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Riepilogo del capitolo 10. Estensioni di markup XAML

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

In genere, il parser XAML converte qualsiasi stringa impostata come valore di attributo nel tipo della proprietà in base alle conversioni standard per i tipi di dati .NET di base o a un [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) derivato collegato alla proprietà o al relativo tipo con un oggetto [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) .

Tuttavia, a volte è utile impostare un attributo da un'origine diversa, ad esempio un elemento in un dizionario, oppure il valore di una proprietà o di un campo statico o da un calcolo di un determinato tipo.

Si tratta del processo di un' *estensione di markup XAML*. Nonostante il nome, le estensioni di markup XAML *non* sono un'estensione di XML. XAML è sempre un XML valido.

## <a name="the-code-infrastructure"></a>Infrastruttura del codice

Un'estensione di markup XAML è una classe che implementa l' [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) interfaccia. Una classe di questo tipo ha spesso la parola `Extension` alla fine del nome, ma in genere viene visualizzata in XAML senza tale suffisso.

Le estensioni di markup XAML seguenti sono supportate da tutte le implementazioni di XAML:

- `x:Static`supportato da[`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference`supportato da[`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type`supportato da[`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null`supportato da[`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array`supportato da[`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

Queste quattro estensioni di markup XAML sono supportate da molte implementazioni di XAML, tra cui Xamarin.Forms :

- `StaticResource`supportato da[`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource`supportato da[`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding`supportato da [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) &mdash; discusso nel [capitolo 16. Data Binding](chapter16.md)
- `TemplateBinding`supportato da [`TemplateBindingExtension`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) &mdash; non trattato nel libro

Un'estensione di markup XAML aggiuntiva è inclusa in Xamarin.Forms in connessione a [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) :

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;non trattato nel libro

## <a name="accessing-static-members"></a>Accesso a membri statici

Usare l' [`x:Static`](xref:Xamarin.Forms.Xaml.StaticExtension) elemento per impostare un attributo sul valore di una proprietà statica pubblica, di un campo o di un membro di enumerazione. Impostare la [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) proprietà sul membro statico. È in genere più facile specificare `x:Static` e il nome del membro tra parentesi graffe. `Member`Non è necessario includere il nome della proprietà, bensì solo il membro stesso. Questa sintassi comune è illustrata nell'esempio [**SharedStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) . I campi statici vengono definiti nella [`AppConstants`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) classe. Questa tecnica consente di stabilire costanti usate in un programma.

Con una dichiarazione di spazio dei nomi XML aggiuntiva, è possibile fare riferimento a proprietà statiche pubbliche, campi o membri di enumerazione definiti in .NET Framework, come illustrato nell'esempio [**SystemStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics) .

## <a name="resource-dictionaries"></a>Dizionari di risorse

La `VisualElement` classe definisce una proprietà denominata [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) che è possibile impostare su un oggetto di tipo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . In XAML è possibile archiviare gli elementi in questo dizionario e identificarli con l' `x:Key` attributo. Gli elementi archiviati nel dizionario risorse sono condivisi tra tutti i riferimenti all'elemento.

### <a name="staticresource-for-most-purposes"></a>StaticResource per la maggior parte degli scopi

Nella maggior parte dei casi, si userà l' [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) estensione di markup per fare riferimento a un elemento del dizionario risorse, come illustrato nell'esempio [**ResourceSharing**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) . È possibile usare un `StaticResourceExtension` elemento o `StaticResource` racchiuso tra parentesi graffe:

[![Schermata tripla della condivisione delle risorse](images/ch10fg03-small.png "Condivisione di risorse")](images/ch10fg03-large.png#lightbox "Condivisione di risorse")

Non confondere l' `x:Static` estensione di markup e l' `StaticResource` estensione di markup.

### <a name="a-tree-of-dictionaries"></a>Un albero di dizionari

Quando il parser XAML rileva un oggetto `StaticResource` , inizia a cercare una chiave corrispondente nella struttura ad albero visuale e quindi Cerca nell'oggetto della `ResourceDictionary` classe dell'applicazione `App` . In questo modo gli elementi in un dizionario risorse sono più approfonditi nella struttura ad albero visuale per eseguire l'override di un dizionario risorse più in alto nella struttura ad albero Questa operazione è illustrata nell'esempio [**ResourceTrees**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees) .

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource per scopi speciali

L' `StaticResource` estensione di markup fa sì che un elemento venga recuperato dal dizionario quando viene compilata una struttura ad albero visuale durante la `InitializeComponent` chiamata. Un'alternativa a `StaticResource` è [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) , che mantiene un collegamento alla chiave del dizionario e aggiorna la destinazione quando l'elemento a cui fa riferimento la chiave viene modificato.

La differenza tra `StaticResource` e `DynamicResource` è illustrata nell'esempio [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) .

Una proprietà impostata da `DynamicResource` deve essere supportata da una proprietà associabile, come illustrato nel [capitolo 11, l'infrastruttura associabile](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Estensioni di markup meno utilizzate

Utilizzare l' [`x:Null`](xref:Xamarin.Forms.Xaml.NullExtension) estensione di markup per impostare una proprietà su `null` .

Utilizzare l' [`x:Type`](xref:Xamarin.Forms.Xaml.TypeExtension) estensione di markup per impostare una proprietà su un `Type` oggetto .NET.

Usare [`x:Array`](xref:Xamarin.Forms.Xaml.ArrayExtension) per definire una matrice. Specificare il tipo di membri della matrice impostando la `Type` Proprietà [] su un' `x:Type` estensione di markup.

## <a name="a-custom-markup-extension"></a>Estensione di markup personalizzata

È possibile creare estensioni di markup XAML scrivendo una classe che implementa l' [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) interfaccia con un [`ProvideValue`](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider)) metodo.

La [`HslColorExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) classe soddisfa tali requisiti. Viene creato un valore di tipo `Color` in base ai valori delle proprietà denominate `H` ,, `S` `L` e `A` . Questa classe è il primo elemento di una Xamarin.Forms libreria denominata [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) , che è stato compilato e utilizzato nel corso di questo libro.

Nell'esempio [**CustomExtensionDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) viene illustrato come fare riferimento a questa libreria e utilizzare l'estensione di markup personalizzata.

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 10 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Esempi del capitolo 10](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [Estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
