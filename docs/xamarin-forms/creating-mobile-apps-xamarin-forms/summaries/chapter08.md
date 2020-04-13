---
title: Riassunto del capitolo 8. Armonia tra codice e XAML
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 8. Armonia tra codice e XAML'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 75f153499edb6d979f9a0269a1439eaf8ca53878
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334246"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Riassunto del capitolo 8. Armonia tra codice e XAML

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)

Questo capitolo esplora XAML in modo più approfondito e in particolare il modo in cui il codice e XAML interagiscono.

## <a name="passing-arguments"></a>Passaggio di argomenti

In generale, una classe di cui è stata creata un'istanza in XAML deve avere un costruttore pubblico senza parametri. l'oggetto risultante viene inizializzato tramite le impostazioni delle proprietà. Tuttavia, esistono altri due modi in cui è possibile creare un'istanza e inizializzare gli oggetti.

Anche se si tratta di tecniche generiche, vengono utilizzate principalmente in relazione ai modelli di visualizzazione MVVM.

### <a name="constructors-with-arguments"></a>Costruttori con argomentiConstructors with arguments

Nell'esempio [**ParameteredConstructorDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) viene `x:Arguments` illustrato come utilizzare il tag per specificare gli argomenti del costruttore. Questi argomenti devono essere delimitati da tag di elemento che indicano il tipo dell'argomento. Per i tipi di dati .NET di base, sono disponibili i tag seguenti:

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

### <a name="can-i-call-methods-from-xaml"></a>È possibile chiamare metodi da XAML?

[**Nell'esempio FactoryMethodDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) viene `x:FactoryMethod` illustrato come utilizzare l'elemento per specificare un metodo factory richiamato per creare un oggetto. Tale metodo factory deve essere pubblico e statico e deve creare un oggetto del tipo in cui è definito. Ad esempio, [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) il metodo si qualifica perché è public e `Color`static e restituisce un valore di tipo . Gli argomenti del metodo factory `x:Arguments` vengono specificati all'interno dei tag.

## <a name="the-xname-attribute"></a>L'attributo x:Name

L'attributo `x:Name` consente a un oggetto di cui è stata creata un'istanza in XAML un nome. Le regole per questi nomi sono le stesse dei nomi delle variabili di C. Dopo la restituzione della `InitializeComponent` chiamata nel costruttore, il file code-behind può fare riferimento a questi nomi per accedere all'elemento XAML corrispondente. I nomi vengono effettivamente convertiti dal parser XAML in campi privati nella classe parziale generata.

Nell'esempio [**XamlClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) viene `x:Name` illustrato l'utilizzo di per `Label` consentire al file code-behind di mantenere due elementi definiti in XAML aggiornati con la data e l'ora correnti.

Lo stesso nome non può essere utilizzato per più elementi nella stessa pagina. Si tratta di un `OnPlatform` problema specifico se si utilizza per creare oggetti denominati paralleli per ogni piattaforma. Il [**PlatformSpecificLabele**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) esempio viene illustrato un modo migliore per eseguire un'operazione simile.

## <a name="custom-xaml-based-views"></a>Visualizzazioni personalizzate basate su XAML

Esistono diversi modi per evitare la ripetizione del markup in XAML. Una tecnica comune consiste nel creare una nuova [`ContentView`](xref:Xamarin.Forms.ContentView)classe basata su XAML che deriva da . Questa tecnica è illustrata nell'esempio [**ColorViewList.This**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) technique is demonstrated in the ColorViewList sample. La `ColorView` classe deriva `ContentView` da per visualizzare un colore `ColorViewListPage` particolare e `ContentPage` il relativo nome, mentre `ColorView`la classe deriva da come di consueto e crea in modo esplicito 17 istanze di .

L'accesso `ColorView` alla classe in XAML richiede `local` un'altra dichiarazione dello spazio dei nomi XML, comunemente denominata per le classi nello stesso assembly.

## <a name="events-and-handlers"></a>Eventi e gestori

Gli eventi possono essere assegnati ai gestori eventi in XAML, ma il gestore eventi stesso deve essere implementato nel file code-behind. Il [**XamlKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) viene illustrato come compilare un'interfaccia utente `Clicked` della tastiera in XAML e come implementare i gestori nel file code-behind.

## <a name="tap-gestures"></a>Movimenti tocco

Qualsiasi `View` oggetto può ottenere l'input tocco e generare eventi da tale input. La `View` classe definisce una [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) proprietà di raccolta che può [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer)contenere una o più istanze di classi che derivano da .

Gli [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) eventi generates. Il programma [**MonkeyTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) mostra `TapGestureRecognizer` come `BoxView` associare oggetti a quattro elementi per creare un gioco di imitazione:

[![Triplo screenshot del rubinetto scimmia](images/ch08fg07-small.png "Gioco di imitazione")](images/ch08fg07-large.png#lightbox "Gioco di imitazione")

Ma il programma **MonkeyTap** ha davvero bisogno di suono. (Vedere [il capitolo successivo](chapter09.md).)

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 8 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Esempi del capitolo 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Capitolo 8 Esempio di F](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [Passaggio di argomenti in XAMLPassing Arguments in XAML](~/xamarin-forms/xaml/passing-arguments.md)
