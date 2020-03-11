---
title: Riepilogo del capitolo 8. Codice e XAML in armonia
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 8. Codice e XAML in armonia'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 75f153499edb6d979f9a0269a1439eaf8ca53878
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334246"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Riepilogo del capitolo 8. Codice e XAML in armonia

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)

In questo capitolo esamina un livello più profondo XAML e in particolare come codice e XAML interagiscono.

## <a name="passing-arguments"></a>Passaggio di argomenti

In generale, una classe creata un'istanza in XAML deve avere un costruttore pubblico senza parametri; l'oggetto risultante viene inizializzato tramite le impostazioni delle proprietà. Tuttavia, esistono altri due metodi che gli oggetti possono essere creata un'istanza e inizializzati.

Anche se queste sono le tecniche per utilizzo generico, vengono utilizzate principalmente in relazione a MVVM, visualizzazione di modelli.

### <a name="constructors-with-arguments"></a>Costruttori con argomenti

L'esempio [**ParameteredConstructorDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) illustra come usare il tag `x:Arguments` per specificare gli argomenti del costruttore. Questi argomenti devono essere racchiuso tra tag di elemento che indica il tipo dell'argomento. Per i tipi di dati .NET di base, sono disponibili i seguenti tag:

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

Nell'esempio [**FactoryMethodDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) viene illustrato come utilizzare l'elemento `x:FactoryMethod` per specificare un metodo factory richiamato per creare un oggetto. Tale metodo factory deve essere pubblico e statico e deve creare un oggetto del tipo in cui è definito. Ad esempio, il metodo [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) si qualifica perché è Public e static e restituisce un valore di tipo `Color`. Gli argomenti per il metodo factory sono specificati all'interno di `x:Arguments` tag.

## <a name="the-xname-attribute"></a>L'attributo X:Name

L'attributo `x:Name` consente di assegnare un nome a un oggetto di cui è stata creata un'istanza in XAML. Le regole per tali nomi sono gli stessi nomi di variabili c#. In seguito alla restituzione della chiamata `InitializeComponent` nel costruttore, il file code-behind può fare riferimento a questi nomi per accedere all'elemento XAML corrispondente. I nomi vengono effettivamente convertiti dal parser XAML in campi privati nella classe parziale generata.

Nell'esempio [**XamlClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) viene illustrato l'utilizzo di `x:Name` per consentire al file code-behind di mantenere due elementi `Label` definiti in XAML aggiornati con la data e l'ora correnti.

Lo stesso nome non può essere usato per più elementi nella stessa pagina. Si tratta di un problema particolare se si usa `OnPlatform` per creare oggetti denominati paralleli per ogni piattaforma. Nell'esempio [**PlatformSpecificLabele**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) viene illustrato un modo migliore per eseguire un'operazione simile.

## <a name="custom-xaml-based-views"></a>Visualizzazioni personalizzate basate su XAML

Esistono diversi modi per evitare la ripetizione di markup in XAML. Una tecnica comune consiste nel creare una nuova classe basata su XAML che deriva da [`ContentView`](xref:Xamarin.Forms.ContentView). Questa tecnica è illustrata nell'esempio [**ColorViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) . La classe `ColorView` deriva da `ContentView` per visualizzare un colore particolare e il relativo nome, mentre la classe `ColorViewListPage` deriva da `ContentPage` come di consueto e crea in modo esplicito 17 istanze di `ColorView`.

Per accedere alla classe `ColorView` in XAML è necessaria un'altra dichiarazione dello spazio dei nomi XML, comunemente denominata `local` per le classi nello stesso assembly.

## <a name="events-and-handlers"></a>Gli eventi e gestori

Gli eventi possono essere assegnati a gestori eventi in XAML, ma il gestore dell'evento stesso deve essere implementato nel file code-behind. [**XamlKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) illustra come creare un'interfaccia utente per la tastiera in XAML e come implementare i gestori `Clicked` nel file code-behind.

## <a name="tap-gestures"></a>Movimenti di tocco

Qualsiasi `View` oggetto può ottenere input tocco e generare eventi da tale input. La classe `View` definisce una proprietà di raccolta [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) che può contenere una o più istanze di classi che derivano da [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer).

Il [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) genera eventi di [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) . Il programma [**MonkeyTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) illustra come aggiungere oggetti `TapGestureRecognizer` a quattro elementi `BoxView` per creare un gioco di imitazione:

[![Schermata tripla del tocco scimmia](images/ch08fg07-small.png "Gioco di imitazione")](images/ch08fg07-large.png#lightbox "Gioco di imitazione")

Ma il programma **MonkeyTap** richiede effettivamente un suono. Vedere [il capitolo successivo](chapter09.md).

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 8 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Esempi capitolo 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Esempio del F# capitolo 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [Passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md)
