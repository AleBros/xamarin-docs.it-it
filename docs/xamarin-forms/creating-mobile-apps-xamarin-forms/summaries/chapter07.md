---
title: Riepilogo del capitolo 7. XAML e codice
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 7. XAML e codice'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: ce4dde3716176daf826678809339afb84c25d84a
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334740"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Riepilogo del capitolo 7. XAML e codice

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> Le note in questa pagina indicano le aree in cui xamarin. Forms è diversa dal materiale presentato nel libro.

Xamarin. Forms supporta un linguaggio di markup basato su XML denominato l'Extensible Application Markup Language o XAML (si pronuncia "zammel"). XAML offre un'alternativa al linguaggio c# nella definizione del layout dell'interfaccia utente di un'applicazione xamarin. Forms e definire associazioni tra elementi dell'interfaccia utente e i dati sottostanti.

## <a name="properties-and-attributes"></a>Proprietà e gli attributi

Strutture e classi xamarin. Forms diventano elementi XML in XAML e proprietà di queste classi e strutture diventano attributi XML. Per creare un'istanza in XAML, una classe in genere deve avere un costruttore pubblico senza parametri. Le proprietà impostate in XAML devono disporre di funzioni di accesso `set` pubbliche.

Per le proprietà dei tipi di dati di base (`string`, `double`, `bool`e così via), il parser XAML usa i metodi `TryParse` standard per convertire le impostazioni degli attributi in questi tipi. Il parser XAML può anche gestire facilmente i tipi di enumerazione e può combinare i membri di enumerazione se il tipo di enumerazione è contrassegnato con l'attributo `Flags`.

Per supportare il parser XAML, i tipi più complessi (o le proprietà di tali tipi) possono includere un [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) che identifica una classe che deriva da [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) che supporta la conversione da valori stringa a tali tipi. Ad esempio, il [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) converte i nomi di colore e le stringhe, ad esempio "#rrggbb", in valori di `Color`.

## <a name="property-element-syntax"></a>Sintassi proprietà-elemento

In XAML, le classi e gli oggetti creati da tali sono espresse come elementi XML. Queste sono note come *elementi oggetto*. La maggior parte delle proprietà di questi oggetti sono espresse come attributi XML. Questi sono denominati *attributi della proprietà*.

Talvolta una proprietà deve essere impostata su un oggetto che non può essere espressi sotto forma di stringa semplice. In tal caso, XAML supporta un tag denominato *elemento Property* costituito dal nome della classe e dal nome della proprietà separati da un punto. Un elemento oggetto può quindi trovarsi all'interno di una coppia di tag di elemento di proprietà.

## <a name="adding-a-xaml-page-to-your-project"></a>Aggiunta di una pagina XAML al progetto

Libreria di classi portabile xamarin. Forms può contenere una pagina XAML quando viene creato o è possibile aggiungere una pagina XAML a un progetto esistente. Nella finestra di dialogo per aggiungere un nuovo elemento, scegliere l'elemento che fa riferimento a una pagina XAML o `ContentPage` e XAML. (Non un `ContentView`).

> [!NOTE]
> Opzioni di Visual Studio sono stati modificati perché è stato scritto in questo capitolo.

Vengono creati due file: un file XAML con l'estensione. nome del file XAML e un file c# con l'estensione. Window1.Xaml.cs. Il C# file viene spesso definito *code-behind* del file XAML. Il file code-behind è una definizione di classe parziale che deriva da `ContentPage`. In fase di compilazione, il XAML viene analizzato e viene generata un'altra definizione di classe parziale per la stessa classe. Questa classe generata include un metodo denominato `InitializeComponent` che viene chiamato dal costruttore del file code-behind.

Durante il runtime, al termine della chiamata di `InitializeComponent`, è stata creata un'istanza di tutti gli elementi del file XAML e ne è stata inizializzata la creazione nel C# codice.

L'elemento radice nel file XAML è `ContentPage`. Il tag radice contiene almeno due dichiarazioni dello spazio dei nomi XML, una per gli elementi Novell. Forms e l'altra che definisce un prefisso `x` per gli elementi e gli attributi intrinseci a tutte le implementazioni XAML. Il tag radice contiene anche un attributo `x:Class` che indica lo spazio dei nomi e il nome della classe che deriva da `ContentPage`. Questo corrisponde al nome dello spazio dei nomi e classe nel file code-behind.

La combinazione di XAML e codice è illustrata nell'esempio [**CodePlusXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07) .

## <a name="the-xaml-compiler"></a>Il compilatore XAML

Novell. Forms ha un compilatore XAML, ma il suo utilizzo è facoltativo in base all'utilizzo di un [`XamlCompilationAttribute`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute). Se non viene compilato il XAML, il XAML viene analizzato in fase di compilazione e il file XAML è incorporato nella libreria di classi Portabile, in cui viene anche analizzato in fase di esecuzione. Se viene compilato il XAML, il processo di compilazione Converte il XAML in un formato binario e l'elaborazione runtime risulta più efficiente.

## <a name="platform-specificity-in-the-xaml-file"></a>Specificità piattaforma nel file XAML

In XAML, è possibile usare la classe [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) per selezionare il markup dipendente dalla piattaforma. Si tratta di una classe generica ed è necessario crearne un'istanza con un attributo `x:TypeArguments` corrispondente al tipo di destinazione. La classe [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) è simile, ma viene usata molto meno spesso.

L'uso di `OnPlatform` è cambiato dopo la pubblicazione del libro. È stata originariamente utilizzata insieme alle proprietà denominate `iOS`, `Android`e `WinPhone`. Viene ora usato con gli oggetti [`On`](xref:Xamarin.Forms.On) figlio. Impostare la proprietà [`Platform`](xref:Xamarin.Forms.On.Platform) su una stringa coerente con i campi `const` pubblici della classe [`Device`](xref:Xamarin.Forms.Device) . Impostare la proprietà [`Value`](xref:Xamarin.Forms.On.Value) su un valore coerente con l'attributo `x:TypeArguments` del tag `OnPlatform`.

`OnPlatform` è illustrato nell'esempio [**ScaryColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) , quindi chiamato perché contiene blocchi di codice XAML quasi identico. L'esistenza di questo markup ripetitive suggerisce che le tecniche devono essere disponibile per ridurlo.

## <a name="the-content-property-attributes"></a>Gli attributi della proprietà del contenuto

Alcuni elementi della proprietà si verificano molto spesso, ad esempio il tag `<ContentPage.Content>` sull'elemento radice di un `ContentPage`o il tag `<StackLayout.Children>` che racchiude gli elementi figlio di `StackLayout`.

Ogni classe può identificare una proprietà con una [`ContentPropertyAttribute`](xref:Xamarin.Forms.ContentPropertyAttribute) sulla classe. Per questa proprietà, i tag di elemento di proprietà non sono necessari. `ContentPage` definisce la proprietà di contenuto come `Content`e `Layout<T>` (la classe da cui deriva `StackLayout`) definisce la relativa proprietà di contenuto come `Children`. Questi tag di elemento di proprietà non sono necessari.

L'elemento Property di `Label` è `Text`.

## <a name="formatted-text"></a>Testo formattato

L'esempio [**TextVariations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) contiene diversi esempi di impostazione delle proprietà `Text` e `FormattedText` di `Label`. In XAML gli oggetti `Span` vengono visualizzati come elementi figlio dell'oggetto `FormattedString`.

 Quando una stringa su più righe è impostata sulla proprietà `Text`, i caratteri di fine riga vengono convertiti in caratteri di spazio, ma i caratteri di fine riga vengono conservati quando una stringa su più righe viene visualizzata come contenuto dei tag `Label` o `Label.Text`:

 [![Schermata tripla della condivisione delle variazioni del testo](images/ch07fg03-small.png "Variazioni di testo formattato")](images/ch07fg03-large.png#lightbox "Variazioni di testo formattato")

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 7 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Esempi del capitolo 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Esempio del F# capitolo 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [Nozioni di base su XAML](~/xamarin-forms/xaml/xaml-basics/index.md)
