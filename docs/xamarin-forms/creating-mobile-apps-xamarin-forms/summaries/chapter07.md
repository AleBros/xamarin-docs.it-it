---
title: Riepilogo del capitolo 7. XAML e codice
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo capitolo 7 o versioni successive. XAML e codice'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: efa3a22c12983ef742bb46f91ab6096294cdc533
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241448"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Riepilogo del capitolo 7. XAML e codice

Xamarin. Forms supporta un linguaggio di markup basato su XML denominato Extensible Application Markup Language o XAML (si pronuncia "zammel"). XAML fornisce un'alternativa a c# nella definizione del layout dell'interfaccia utente di un'applicazione di xamarin. Forms e nella definizione di associazioni tra gli elementi dell'interfaccia utente e i dati sottostanti.

## <a name="properties-and-attributes"></a>Proprietà e gli attributi

Strutture e classi di xamarin. Forms diventano elementi XML in XAML, e proprietà delle classi e strutture di attributi XML. Per creare un'istanza in XAML, una classe deve avere in genere un costruttore pubblico senza parametri. Qualsiasi proprietà impostato in XAML devono essere pubblici `set` funzioni di accesso.

Per le proprietà dei tipi di dati di base (`string`, `double`, `bool`e così via), il parser XAML utilizza lo standard `TryParse` metodi per convertire le impostazioni di attributi a questi tipi. Il parser XAML può gestire facilmente i tipi di enumerazione e può essere composto da membri di enumerazione se il tipo di enumerazione è contrassegnato con il `Flags` attributo.

Per semplificare il parser XAML, tipi complessi (o la proprietà di tali tipi) può includere un [ `TypeConverterAttribute` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverterAttribute/) che identifica una classe che deriva da [ `TypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverter/) che supporta la conversione da valori stringa ai tipi. Ad esempio, il [ `ColorTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColorTypeConverter/) converte i nomi e le stringhe, ad esempio "#rrggbb", colore in `Color` valori.

## <a name="property-element-syntax"></a>Sintassi dell'elemento di proprietà

In XAML, classi e gli oggetti creati da essi sono espresse come elementi XML. Questi sono conosciuti come *elementi oggetto*. La maggior parte delle proprietà di questi oggetti sono espressi come attributi XML. Questi sono denominati *attributi della proprietà*.

In alcuni casi è necessario impostare una proprietà a un oggetto che non può essere espresso come una stringa semplice. In tal caso, XAML supporta un tag denominato un *elemento proprietà* che include il nome della classe e il nome di proprietà separati da un punto. Un elemento oggetto quindi può trovarsi all'interno di una coppia di tag di elemento di proprietà.

## <a name="adding-a-xaml-page-to-your-project"></a>Aggiunta di una pagina XAML al progetto

Una libreria di classi portabile di xamarin. Forms può contenere una pagina XAML quando viene creato o è possibile aggiungere una pagina XAML a un progetto esistente. Nella finestra di dialogo per aggiungere un nuovo elemento, scegliere l'elemento che fa riferimento a una pagina XAML o `ContentPage` e XAML. (Non un `ContentView`.)

Vengono creati due file: un file XAML con XAML di estensione di nome file e un file c# con l'estensione. xaml.cs. Il file c# è noto anche come il *codice* del file XAML. Il file code-behind è una definizione di classe parziale che deriva da `ContentPage`. In fase di compilazione, il codice XAML viene analizzato e un'altra definizione di classe parziale viene generata per la stessa classe. Questa classe generata include un metodo denominato `InitializeComponent` che viene chiamato dal costruttore del file code-behind.

Durante la fase di esecuzione, al termine del `InitializeComponent` chiama, tutti gli elementi del file XAML sono stati creata un'istanza e inizializzati come se è state create in codice c#.

L'elemento radice nel file XAML è `ContentPage`. Il tag radice contiene almeno due dichiarazioni dello spazio dei nomi XML, uno per gli elementi di xamarin. Forms e l'altra definizione di un `x` prefisso per gli elementi e attributi intrinseci per tutte le implementazioni di XAML. Il tag radice contiene inoltre un `x:Class` attributo che indica dello spazio dei nomi e il nome della classe che deriva da `ContentPage`. Questo corrisponde al nome di classe e spazio dei nomi nel file code-behind.

La combinazione di XAML e il codice viene dimostrata il [ **CodePlusXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07) esempio.

## <a name="the-xaml-compiler"></a>Il compilatore XAML

Xamarin. Forms è un compilatore XAML, ma il relativo utilizzo è facoltativo in base all'utilizzo di un [ `XamlCompilationAttribute` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/). Se il codice XAML non è compilato, il codice XAML viene analizzato in fase di compilazione e il file XAML è incorporato nella libreria di classi Portabile, in cui viene anche analizzato in fase di esecuzione. Se viene compilato il codice XAML, il processo di compilazione converte il codice XAML in un formato binario e l'elaborazione della fase di esecuzione è più efficiente.

## <a name="platform-specificity-in-the-xaml-file"></a>Specificità piattaforma nel file XAML

In XAML, il [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) classe può essere utilizzata per selezionare il codice dipendente dalla piattaforma. Si tratta di una classe generica e deve essere creata un'istanza con un `x:TypeArguments` attributo che corrisponde al tipo di destinazione. Il [ `OnIdiom` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnIdiom%3CT%3E/) classe è simile ma utilizzati più raramente.

L'utilizzo di `OnPlatform` è stato modificato dopo il libro è stato pubblicato. È stata originariamente utilizzata in combinazione con le proprietà denominate `iOS`, `Android`, e `WinPhone`. Attualmente viene utilizzato con figlio [ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) oggetti. Impostare il [ `Platform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Platform/) proprietà in una stringa coerenza con il pubblico `const` campi del [ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) classe. Impostare il [ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Value/) su un valore coerenza con il `x:TypeArguments` attributo del `OnPlatform` tag.

`OnPlatform` viene illustrata la [ **ScaryColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) esempio, così definito perché contiene blocchi di codice XAML quasi identico. L'esistenza di questo markup ripetitive suggerisce che le tecniche devono essere disponibile per ridurlo.

## <a name="the-content-property-attributes"></a>Gli attributi della proprietà di contenuto

Alcuni elementi di proprietà si verificano molto frequentemente, ad esempio il `<ContentPage.Content>` tag per l'elemento radice di un `ContentPage`, o `<StackLayout.Children>` tag che include gli elementi figlio di `StackLayout`.

Ogni classe è consentito per identificare una proprietà con un [ `ContentPropertyAttribute` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPropertyAttribute/) nella classe. Per questa proprietà, i tag di elemento di proprietà non sono necessari. `ContentPage` definisce la proprietà del contenuto come `Content`, e `Layout<T>` (la classe da cui `StackLayout` deriva) definisce la proprietà del contenuto come `Children`. Questi tag di elemento di proprietà non sono necessari.

L'elemento della proprietà `Label` è `Text`.

## <a name="formatted-text"></a>Testo formattato

Il [ **TextVariations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) esempio sono contenuti numerosi esempi di impostazione di `Text` e `FormattedText` le proprietà di `Label`. In XAML, `Span` gli oggetti vengono visualizzati come elementi figlio del `FormattedString` oggetto.

 Quando una stringa su più righe è impostata sul `Text` proprietà, i caratteri di fine della riga vengono convertiti in caratteri di spazio, ma i caratteri di fine della riga vengono mantenuti quando una stringa su più righe viene visualizzata come contenuto del `Label` o `Label.Text` tag:

 [![Schermata triplo delle variazioni di testo condivisione](images/ch07fg03-small.png "variazioni di testo formattato")](images/ch07fg03-large.png#lightbox "variazioni di testo formattato")



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 7 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Esempi di capitolo 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Esempio 7 capitolo F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
