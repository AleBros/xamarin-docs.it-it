---
title: Riassunto del capitolo 7. XAML o codice
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 7. XAML o codice'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: ce4dde3716176daf826678809339afb84c25d84a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334740"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Riassunto del capitolo 7. XAML o codice

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> Le note in questa pagina indicano le aree in cui Xamarin.Forms si è discostata dal materiale presentato nel libro.

Xamarin.Forms supporta un linguaggio di markup basato su XML denominato Extensible Application Markup Language o XAML (pronunciato "zammel"). XAML offre un'alternativa al linguaggio C, nella definizione del layout dell'interfaccia utente di un'applicazione Xamarin.Forms e nella definizione di associazioni tra elementi dell'interfaccia utente e dati sottostanti.

## <a name="properties-and-attributes"></a>Proprietà e attributi

Le classi e le strutture Xamarin.Forms diventano elementi XML in XAML e le proprietà di queste classi e strutture diventano attributi XML. Per creare un'istanza in XAML, una classe deve in genere avere un costruttore pubblico senza parametri. Tutte le proprietà impostate `set` in XAML devono avere funzioni di accesso pubbliche.

Per le proprietà dei`string`tipi `double` `bool`di dati di base ( , `TryParse` , e così via), il parser XAML utilizza i metodi standard per convertire le impostazioni degli attributi in questi tipi. Il parser XAML può anche gestire facilmente i tipi di enumerazione e può `Flags` combinare i membri di enumerazione se il tipo di enumerazione è contrassegnato con l'attributo.

Per supportare il parser XAML, i tipi più complessi [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) (o le proprietà [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) di tali tipi) possono includere un che identifica una classe che deriva da cui supporta la conversione da valori stringa a tali tipi. Ad esempio, [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) converte i nomi dei colori e `Color` le stringhe, ad esempio "#rrggbb", in valori.

## <a name="property-element-syntax"></a>Sintassi degli elementi proprietà

In XAML, le classi e gli oggetti creati da esse vengono espressi come elementi XML. Questi sono noti come *elementi oggetto*. La maggior parte delle proprietà di questi oggetti sono espresse come attributi XML. Questi sono chiamati attributi di *proprietà*.

A volte una proprietà deve essere impostata su un oggetto che non può essere espresso come stringa semplice. In tal caso, XAML supporta un tag denominato *elemento proprietà* costituito dal nome della classe e dal nome della proprietà separati da un punto. Un elemento oggetto può quindi essere visualizzato all'interno di una coppia di tag proprietà-elemento.

## <a name="adding-a-xaml-page-to-your-project"></a>Aggiunta di una pagina XAML al progetto

Una libreria di classi portabile Xamarin.Forms può contenere una pagina XAML quando viene creata per la prima volta oppure è possibile aggiungere una pagina XAML a un progetto esistente. Nella finestra di dialogo per aggiungere un nuovo elemento scegliere `ContentPage` l'elemento che fa riferimento a una pagina XAML o xaml. (Non `ContentView`un .)

> [!NOTE]
> Le opzioni di Visual Studio sono state modificate dopo la scrittura di questo capitolo.

Vengono creati due file: un file XAML con estensione xaml e un file di C' con estensione xaml.cs. Il file di C , spesso indicato come il *code-behind* del file XAML. Il file code-behind è una definizione `ContentPage`di classe parziale che deriva da . In fase di compilazione, il codice XAML viene analizzato e viene generata un'altra definizione di classe parziale per la stessa classe. Questa classe generata `InitializeComponent` include un metodo denominato che viene chiamato dal costruttore del file code-behind.

Durante la fase di `InitializeComponent` esecuzione, al termine della chiamata, tutti gli elementi del file XAML sono stati istanziati e inizializzati come se fossero stati creati nel codice c'è.

L'elemento radice nel `ContentPage`file XAML è . Il tag radice contiene almeno due dichiarazioni dello spazio dei nomi XML, una `x` per gli elementi Xamarin.Forms e l'altra che definisce un prefisso per gli elementi e gli attributi intrinseci a tutte le implementazioni XAML. Il tag radice `x:Class` contiene anche un attributo che indica lo `ContentPage`spazio dei nomi e il nome della classe che deriva da . Corrisponde allo spazio dei nomi e al nome della classe nel file code-behind.

La combinazione di XAML e codice è dimostrata dall'esempio [**CodePlusXaml.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

## <a name="the-xaml-compiler"></a>Il compilatore XAML

Xamarin.Forms dispone di un compilatore XAML, ma il [`XamlCompilationAttribute`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)relativo utilizzo è facoltativo in base all'utilizzo di un oggetto . Se il codice XAML non viene compilato, viene analizzato in fase di compilazione e il file XAML è incorporato nella libreria di classi Porta colori system, dove viene analizzato anche in fase di esecuzione. Se il codice XAML viene compilato, il processo di compilazione converte il codice XAML in un formato binario e l'elaborazione di runtime è più efficiente.

## <a name="platform-specificity-in-the-xaml-file"></a>Specificità della piattaforma nel file XAML

In XAML, [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) la classe può essere utilizzata per selezionare il markup dipendente dalla piattaforma. Si tratta di una classe generica `x:TypeArguments` e deve essere creata un'istanza con un attributo che corrisponde al tipo di destinazione. La [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) classe è simile ma usata molto meno spesso.

L'uso `OnPlatform` di è cambiato da quando il libro è stato pubblicato. È stato originariamente utilizzato `iOS`in `Android`combinazione `WinPhone`con proprietà denominate , , e . Viene ora utilizzato [`On`](xref:Xamarin.Forms.On) con gli oggetti figlio. Impostare [`Platform`](xref:Xamarin.Forms.On.Platform) la proprietà su una `const` stringa [`Device`](xref:Xamarin.Forms.Device) coerente con i campi pubblici della classe. Impostare [`Value`](xref:Xamarin.Forms.On.Value) la proprietà su `x:TypeArguments` un valore `OnPlatform` coerente con l'attributo del tag.

`OnPlatform`è illustrato nel [**ScaryColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) esempio, così chiamato perché contiene blocchi di XAML quasi identico. L'esistenza di questo markup ripetitivo suggerisce che le tecniche dovrebbero essere disponibili per ridurlo.

## <a name="the-content-property-attributes"></a>Attributi delle proprietà di contenuto

Alcuni elementi proprietà si verificano `<ContentPage.Content>` abbastanza frequentemente, `ContentPage`ad esempio `<StackLayout.Children>` il tag nell'elemento radice di un oggetto , o il tag che racchiude gli elementi figlio di `StackLayout`.

Ogni classe può identificare una [`ContentPropertyAttribute`](xref:Xamarin.Forms.ContentPropertyAttribute) proprietà con un on the class. Per questa proprietà, i tag dell'elemento proprietà non sono obbligatori. `ContentPage`definisce la proprietà `Content`di `Layout<T>` contenuto come `StackLayout` e (la classe da `Children`cui deriva) la proprietà di contenuto come . Questi tag degli elementi proprietà non sono obbligatori.

L'elemento `Label` proprietà `Text`di is .

## <a name="formatted-text"></a>Testo formattato

L'esempio [**TextVariations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) contiene diversi `Text` `FormattedText` esempi `Label`di impostazione delle proprietà e di . In XAML, `Span` gli oggetti `FormattedString` vengono visualizzati come elementi figlio dell'oggetto.

 Quando una stringa su più `Text` righe è impostata sulla proprietà , i caratteri di fine riga vengono convertiti in caratteri di `Label` `Label.Text` spazio, ma i caratteri di fine riga vengono mantenuti quando una stringa su più righe viene visualizzata come contenuto dei tag o :

 [![Tripla schermata della condivisione delle varianti di testo](images/ch07fg03-small.png "Variazioni di testo formattato")](images/ch07fg03-large.png#lightbox "Variazioni di testo formattato")

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 7 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Esempi del capitolo 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Capitolo 7 Esempio di F](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [Nozioni di base su XAML](~/xamarin-forms/xaml/xaml-basics/index.md)
