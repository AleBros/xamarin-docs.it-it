---
title: Riepilogo del capitolo 7. XAML e codice
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 7. XAML e codice'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 02e4ea44d87360deed361d161759fa3a2808100f
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995157"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Riepilogo del capitolo 7. XAML e codice

Xamarin. Forms supporta un linguaggio di markup basato su XML denominato l'Extensible Application Markup Language o XAML (si pronuncia "zammel"). XAML offre un'alternativa al linguaggio c# nella definizione del layout dell'interfaccia utente di un'applicazione xamarin. Forms e definire associazioni tra elementi dell'interfaccia utente e i dati sottostanti.

## <a name="properties-and-attributes"></a>Proprietà e gli attributi

Strutture e classi xamarin. Forms diventano elementi XML in XAML e proprietà di queste classi e strutture diventano attributi XML. Per creare un'istanza in XAML, una classe in genere deve avere un costruttore pubblico senza parametri. Qualsiasi proprietà impostata in XAML deve avere pubblico `set` le funzioni di accesso.

Per le proprietà dei tipi di dati di base (`string`, `double`, `bool`e così via), il parser XAML Usa lo standard `TryParse` metodi per convertire questi tipi di impostazioni degli attributi. Il parser XAML può gestire anche facilmente i tipi di enumerazione ed è possibile combinare i membri dell'enumerazione se il tipo di enumerazione è contrassegnato con il `Flags` attributo.

Per semplificare il parser XAML, i tipi più complessi (o le proprietà di tali tipi) possono includere un [ `TypeConverterAttribute` ](xref:Xamarin.Forms.TypeConverterAttribute) che identifica una classe che deriva da [ `TypeConverter` ](xref:Xamarin.Forms.TypeConverter) che supporta la conversione da valori di stringa ai tipi. Ad esempio, il [ `ColorTypeConverter` ](xref:Xamarin.Forms.ColorTypeConverter) converte colori nomi e le stringhe, ad esempio "#rrggbb", in `Color` valori.

## <a name="property-element-syntax"></a>Sintassi proprietà-elemento

In XAML, le classi e gli oggetti creati da tali sono espresse come elementi XML. Si parla *elementi oggetto*. La maggior parte delle proprietà di questi oggetti sono espresse come attributi XML. Questi sono denominati *gli attributi della proprietà*.

Talvolta una proprietà deve essere impostata su un oggetto che non può essere espressi sotto forma di stringa semplice. In tal caso, XAML supporta un tag denominato una *property (elemento)* costituito il nome della classe e il nome di proprietà separati da un punto. Un elemento oggetto può quindi trovarsi all'interno di una coppia di tag di elemento di proprietà.

## <a name="adding-a-xaml-page-to-your-project"></a>Aggiunta di una pagina XAML al progetto

Libreria di classi portabile xamarin. Forms può contenere una pagina XAML quando viene creato o è possibile aggiungere una pagina XAML a un progetto esistente. Nella finestra di dialogo per aggiungere un nuovo elemento, scegliere l'elemento che fa riferimento a una pagina XAML o `ContentPage` e XAML. (Non un `ContentView`.)

Vengono creati due file: un file XAML con l'estensione. nome del file XAML e un file c# con l'estensione. Window1.Xaml.cs. File c# è noto anche come il *code-behind* del file XAML. Il file code-behind è una definizione di classe parziale che deriva da `ContentPage`. In fase di compilazione, il XAML viene analizzato e viene generata un'altra definizione di classe parziale per la stessa classe. La classe generata include un metodo denominato `InitializeComponent` che viene chiamato dal costruttore del file code-behind.

Durante la fase di esecuzione, al termine del `InitializeComponent` chiamare, tutti gli elementi del file XAML sono stati creata un'istanza e inizializzati come se avessero stato creato nel codice c#.

L'elemento radice nel file XAML è `ContentPage`. Il tag radice contiene almeno due dichiarazioni dello spazio dei nomi XML, uno per gli elementi di xamarin. Forms e l'altra definizione di un `x` prefisso per gli elementi e attributi intrinseci per tutte le implementazioni di XAML. Il tag radice contiene anche un `x:Class` attributo che indica dello spazio dei nomi e il nome della classe che deriva da `ContentPage`. Questo corrisponde al nome dello spazio dei nomi e classe nel file code-behind.

La combinazione di codice e XAML viene dimostrata la [ **CodePlusXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07) esempio.

## <a name="the-xaml-compiler"></a>Il compilatore XAML

Xamarin. Forms ha un compilatore XAML, ma l'utilizzo è facoltativo in base all'utilizzo di un [ `XamlCompilationAttribute` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute). Se non viene compilato il XAML, il XAML viene analizzato in fase di compilazione e il file XAML è incorporato nella libreria di classi Portabile, in cui viene anche analizzato in fase di esecuzione. Se viene compilato il XAML, il processo di compilazione Converte il XAML in un formato binario e l'elaborazione runtime risulta più efficiente.

## <a name="platform-specificity-in-the-xaml-file"></a>Specificità piattaforma nel file XAML

In XAML, il [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) classe può essere utilizzata per selezionare markup dipendente dalla piattaforma. Si tratta di una classe generica e deve essere creata un'istanza con un `x:TypeArguments` attributo che corrisponde al tipo di destinazione. Il [ `OnIdiom` ](xref:Xamarin.Forms.OnIdiom`1) classe è simile, ma usati con minore frequenza.

L'uso di `OnPlatform` poiché è stato pubblicato il libro è stato modificato. È stato originariamente usato in combinazione con le proprietà denominate `iOS`, `Android`, e `WinPhone`. Viene ora usato con l'elemento figlio [ `On` ](xref:Xamarin.Forms.On) oggetti. Impostare il [ `Platform` ](xref:Xamarin.Forms.On.Platform) proprietà in una stringa coerenza con il pubblico `const` campi del [ `Device` ](xref:Xamarin.Forms.Device) classe. Impostare il [ `Value` ](xref:Xamarin.Forms.On.Value) la proprietà su un valore coerenza con il `x:TypeArguments` attributo del `OnPlatform` tag.

`OnPlatform` viene illustrata nel [ **ScaryColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) campione, pertanto chiamato perché contiene blocchi di XAML quasi identica. L'esistenza di questo markup ripetitive suggerisce che le tecniche devono essere disponibile per ridurlo.

## <a name="the-content-property-attributes"></a>Gli attributi della proprietà del contenuto

Alcuni elementi di proprietà si verificano piuttosto frequentemente, come la `<ContentPage.Content>` tag per l'elemento radice di un `ContentPage`, o il `<StackLayout.Children>` tag che racchiude gli elementi figlio di `StackLayout`.

Ogni classe è consentito per identificare una proprietà con un [ `ContentPropertyAttribute` ](xref:Xamarin.Forms.ContentPropertyAttribute) nella classe. Per questa proprietà, i tag di elemento di proprietà non sono necessari. `ContentPage` definisce le proprietà di contenuto come `Content`, e `Layout<T>` (la classe da cui `StackLayout` deriva) definisce la proprietà del contenuto come `Children`. Questi tag di elemento di proprietà non sono necessari.

L'elemento di proprietà del `Label` è `Text`.

## <a name="formatted-text"></a>Testo formattato

Il [ **TextVariations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) esempio contiene alcuni esempi di impostazione la `Text` e `FormattedText` le proprietà di `Label`. In XAML, `Span` gli oggetti vengono visualizzati come elementi figlio del `FormattedString` oggetto.

 Quando una stringa con più righe è impostata sul `Text` proprietà, end-of-line caratteri vengono convertiti in caratteri di spazio, ma i caratteri di fine-of-line vengono mantenuti quando una stringa con più righe viene visualizzata come contenuto del `Label` o `Label.Text` tag:

 [![Schermata triplo delle variazioni di testo di condivisione](images/ch07fg03-small.png "variazioni di testo formattato")](images/ch07fg03-large.png#lightbox "variazioni di testo formattato")



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 7 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Capitolo 7 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Esempio di capitolo 7 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
