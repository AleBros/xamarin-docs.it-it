---
title: Riepilogo del capitolo 7. XAML o codice
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 7. XAML o codice'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0b92988e1e838072fca0d8a284455a62db05e757
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136859"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Riepilogo del capitolo 7. XAML o codice

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> Le note in questa pagina indicano Xamarin.Forms le aree in cui è stato divergente rispetto al materiale presentato nel libro.

Xamarin.Formssupporta un linguaggio di markup basato su XML denominato Extensible Application Markup Language o XAML (pronunciato "acronimo"). XAML fornisce un'alternativa a C# per la definizione del layout dell'interfaccia utente di un' Xamarin.Forms applicazione e per la definizione di associazioni tra gli elementi dell'interfaccia utente e i dati sottostanti.

## <a name="properties-and-attributes"></a>Proprietà e attributi

Xamarin.Formsclassi e strutture diventano elementi XML in XAML e le proprietà di queste classi e strutture diventano attributi XML. Per creare un'istanza in XAML, una classe deve in genere avere un costruttore pubblico senza parametri. Le proprietà impostate in XAML devono disporre di `set` funzioni di accesso pubbliche.

Per le proprietà dei tipi di dati di base ( `string` , `double` , `bool` e così via), il parser XAML usa i `TryParse` metodi standard per convertire le impostazioni degli attributi in questi tipi. Il parser XAML può anche gestire facilmente i tipi di enumerazione e può combinare i membri di enumerazione se il tipo di enumerazione è contrassegnato con l' `Flags` attributo.

Per supportare il parser XAML, i tipi più complessi (o le proprietà di tali tipi) possono includere un oggetto [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) che identifica una classe che deriva da [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) che supporta la conversione da valori stringa a tali tipi. Ad esempio, [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) converte i nomi di colore e le stringhe, ad esempio "#rrggbb", in `Color` valori.

## <a name="property-element-syntax"></a>Sintassi elemento proprietà

In XAML, le classi e gli oggetti creati da essi sono espressi come elementi XML. Queste sono note come *elementi oggetto*. La maggior parte delle proprietà di questi oggetti è espressa come attributi XML. Questi sono denominati *attributi della proprietà*.

In alcuni casi è necessario impostare una proprietà su un oggetto che non può essere espresso come stringa semplice. In tal caso, XAML supporta un tag denominato *elemento Property* costituito dal nome della classe e dal nome della proprietà separati da un punto. Un elemento oggetto può quindi essere visualizzato all'interno di una coppia di tag elemento proprietà.

## <a name="adding-a-xaml-page-to-your-project"></a>Aggiunta di una pagina XAML al progetto

Una Xamarin.Forms libreria di classi portabile può contenere una pagina XAML quando viene creata per la prima volta oppure è possibile aggiungere una pagina XAML a un progetto esistente. Nella finestra di dialogo per aggiungere un nuovo elemento, scegliere l'elemento che fa riferimento a una pagina XAML o `ContentPage` e a XAML. (Non `ContentView` .)

> [!NOTE]
> Le opzioni di Visual Studio sono state modificate dopo la scrittura di questo capitolo.

Vengono creati due file: un file XAML con estensione XAML e un file C# con estensione xaml.cs. Il file C# viene spesso definito *code-behind* del file XAML. Il file code-behind è una definizione di classe parziale che deriva da `ContentPage` . In fase di compilazione, il codice XAML viene analizzato e viene generata un'altra definizione di classe parziale per la stessa classe. Questa classe generata include un metodo denominato `InitializeComponent` che viene chiamato dal costruttore del file code-behind.

Durante il runtime, al termine della `InitializeComponent` chiamata, è stata creata un'istanza di tutti gli elementi del file XAML e ne è stata inizializzata la creazione nel codice C#.

L'elemento radice nel file XAML è `ContentPage` . Il tag radice contiene almeno due dichiarazioni dello spazio dei nomi XML, una per gli Xamarin.Forms elementi e l'altra che definisce un `x` prefisso per gli elementi e gli attributi intrinseci a tutte le implementazioni XAML. Il tag radice contiene anche un `x:Class` attributo che indica lo spazio dei nomi e il nome della classe che deriva da `ContentPage` . Corrisponde allo spazio dei nomi e al nome della classe nel file code-behind.

La combinazione di XAML e codice è illustrata nell'esempio [**CodePlusXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07) .

## <a name="the-xaml-compiler"></a>Compilatore XAML

Xamarin.Formsdispone di un compilatore XAML, ma il suo utilizzo è facoltativo in base all'utilizzo di un oggetto [`XamlCompilationAttribute`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) . Se il codice XAML non è compilato, il codice XAML viene analizzato in fase di compilazione e il file XAML viene incorporato nella libreria di classi portabile, dove viene anche analizzato in fase di esecuzione. Se il codice XAML viene compilato, il processo di compilazione converte il codice XAML in un formato binario e l'elaborazione del runtime è più efficiente.

## <a name="platform-specificity-in-the-xaml-file"></a>Specificità della piattaforma nel file XAML

In XAML la [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classe può essere usata per selezionare il markup dipendente dalla piattaforma. Si tratta di una classe generica ed è necessario crearne un'istanza con un `x:TypeArguments` attributo che corrisponda al tipo di destinazione. La [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) classe è simile, ma viene usata molto meno spesso.

L'uso di `OnPlatform` è cambiato dopo la pubblicazione del libro. È stata originariamente utilizzata insieme alle proprietà denominate `iOS` , `Android` e `WinPhone` . Viene ora usato con oggetti figlio [`On`](xref:Xamarin.Forms.On) . Impostare la [`Platform`](xref:Xamarin.Forms.On.Platform) proprietà su una stringa coerente con i `const` campi pubblici della [`Device`](xref:Xamarin.Forms.Device) classe. Impostare la [`Value`](xref:Xamarin.Forms.On.Value) proprietà su un valore coerente con l' `x:TypeArguments` attributo del `OnPlatform` tag.

`OnPlatform`viene illustrato nell'esempio [**ScaryColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) , quindi chiamato perché contiene blocchi di codice XAML quasi identico. L'esistenza di questo markup ripetitivo suggerisce che le tecniche devono essere disponibili per ridurla.

## <a name="the-content-property-attributes"></a>Attributi della proprietà di contenuto

Alcuni elementi della proprietà si verificano molto spesso, ad esempio il `<ContentPage.Content>` tag nell'elemento radice di un oggetto `ContentPage` o il `<StackLayout.Children>` tag che racchiude gli elementi figlio di `StackLayout` .

Ogni classe può identificare una proprietà con un oggetto [`ContentPropertyAttribute`](xref:Xamarin.Forms.ContentPropertyAttribute) nella classe. Per questa proprietà, i tag elemento proprietà non sono obbligatori. `ContentPage`definisce la proprietà di contenuto come `Content` e `Layout<T>` (la classe da cui `StackLayout` deriva) definisce la relativa proprietà di contenuto come `Children` . Questi tag dell'elemento proprietà non sono obbligatori.

L'elemento Property di `Label` è `Text` .

## <a name="formatted-text"></a>Testo formattato

L'esempio [**TextVariations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) contiene diversi esempi di impostazione delle `Text` `FormattedText` proprietà e di `Label` . In XAML `Span` gli oggetti vengono visualizzati come elementi figlio dell' `FormattedString` oggetto.

 Quando una stringa su più righe è impostata sulla `Text` proprietà, i caratteri di fine riga vengono convertiti in caratteri di spazio, ma i caratteri di fine riga vengono conservati quando una stringa su più righe viene visualizzata come contenuto dei `Label` `Label.Text` tag o:

 [![Schermata tripla della condivisione delle variazioni del testo](images/ch07fg03-small.png "Variazioni di testo formattato")](images/ch07fg03-large.png#lightbox "Variazioni di testo formattato")

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 7 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Esempi del capitolo 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Esempio del capitolo 7 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [Nozioni di base su XAML](~/xamarin-forms/xaml/xaml-basics/index.md)
