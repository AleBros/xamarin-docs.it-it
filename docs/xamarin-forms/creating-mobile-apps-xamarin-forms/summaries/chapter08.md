---
title: Riepilogo del capitolo 8. Codice e XAML in armonia
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: Riepilogo del capitolo 8. Codice e XAML in armonia'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 75f153499edb6d979f9a0269a1439eaf8ca53878
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334246"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Riepilogo del capitolo 8. Codice e XAML in armonia

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)

In questo capitolo esamina un livello più profondo XAML e in particolare come codice e XAML interagiscono.

## <a name="passing-arguments"></a>Passaggio di argomenti

In generale, una classe creata un'istanza in XAML deve avere un costruttore pubblico senza parametri; l'oggetto risultante viene inizializzato tramite le impostazioni delle proprietà. Tuttavia, esistono altri due metodi che gli oggetti possono essere creata un'istanza e inizializzati.

Anche se queste sono le tecniche per utilizzo generico, vengono utilizzate principalmente in relazione a MVVM, visualizzazione di modelli.

### <a name="constructors-with-arguments"></a>Costruttori con argomenti

Il [ **ParameteredConstructorDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) esempio viene illustrato come usare il `x:Arguments` tag per specificare gli argomenti del costruttore. Questi argomenti devono essere racchiuso tra tag di elemento che indica il tipo dell'argomento. Per i tipi di dati .NET di base, sono disponibili i seguenti tag:

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

### <a name="can-i-call-methods-from-xaml"></a>È possibile chiamare i metodi da XAML?

Il [ **FactoryMethodDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) esempio viene illustrato come usare il `x:FactoryMethod` elemento per specificare un metodo factory che viene richiamato per creare un oggetto. Tale metodo factory deve essere pubblico e statico e deve creare un oggetto del tipo in cui è definito. (Ad esempio la [ `Color.FromRgb` ](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) metodo qualifica perché è statico e pubblico e restituisce un valore di tipo `Color`.) Gli argomenti al metodo factory vengono specificati all'interno di `x:Arguments` tag.

## <a name="the-xname-attribute"></a>L'attributo X:Name

Il `x:Name` attributo consente a un oggetto creata un'istanza in XAML per essere assegnato un nome. Le regole per tali nomi sono gli stessi nomi di variabili c#. Dopo la restituzione del `InitializeComponent` chiamare nel costruttore, il file code-behind può fare riferimento a questi nomi per accedere all'elemento XAML corrispondente. I nomi vengono effettivamente convertiti dal parser XAML in campi privati nella classe parziale generata.

Il [ **XamlClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) esempio dimostra l'uso di `x:Name` per consentire il file code-behind mantenere due `Label` elementi definiti in XAML aggiornato con la data e ora correnti.

Lo stesso nome non può essere usato per più elementi nella stessa pagina. Si tratta di un particolare problema se si usa `OnPlatform` creare parallele oggetti denominati per ogni piattaforma. Il [ **PlatformSpecificLabele** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) esempio viene illustrato un modo migliore per eseguire un'operazione simile.

## <a name="custom-xaml-based-views"></a>Visualizzazioni personalizzate basate su XAML

Esistono diversi modi per evitare la ripetizione di markup in XAML. Una tecnica comune consiste nel creare una nuova classe basata su XAML che deriva da [ `ContentView` ](xref:Xamarin.Forms.ContentView). Questa tecnica è dimostrata nel [ **ColorViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) esempio. Il `ColorView` deriva dalla classe `ContentView` per visualizzare un determinato colore e il relativo nome, mentre le `ColorViewListPage` deriva dalla classe `ContentPage` come di consueto e in modo esplicito crea 17 istanze di `ColorView`.

L'accesso di `ColorView` classe in XAML richiede un'altra dichiarazione dello spazio dei nomi XML, comunemente denominata `local` per le classi nello stesso assembly.

## <a name="events-and-handlers"></a>Gli eventi e gestori

Gli eventi possono essere assegnati a gestori eventi in XAML, ma il gestore dell'evento stesso deve essere implementato nel file code-behind. Il [ **XamlKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) viene illustrato come compilare un'interfaccia utente tastierino in XAML e come implementare il `Clicked` gestori nel file code-behind.

## <a name="tap-gestures"></a>Movimenti di tocco

Qualsiasi `View` oggetto possibile ottenere l'input tocco e generare gli eventi da quell'input. Il `View` classe definisce un [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) proprietà di raccolta che può contenere uno o più istanze di classi che derivano da [ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer).

Il [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) genera [ `Tapped` ](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) gli eventi. Il [ **MonkeyTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) programma illustra come collegare `TapGestureRecognizer` gli oggetti a quattro `BoxView` elementi per creare un gioco imitazione:

[![Tripla screenshot della scelta monkey](images/ch08fg07-small.png "imitazione Game")](images/ch08fg07-large.png#lightbox "imitazione gioco")

Ma il **MonkeyTap** suono realmente necessario programma. (Vedere [capitolo successivo](chapter09.md).)

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 8 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Esempi di capitolo 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Capitolo 8 F# esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [Passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md)
