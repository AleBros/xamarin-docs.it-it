---
title: Riepilogo del capitolo 8. Codice e XAML nel armonia
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 452a7835bcb54501edffe7a2467544c6677616ba
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Riepilogo del capitolo 8. Codice e XAML nel armonia

In questo capitolo viene esaminata XAML in modo più specifico, e in particolare come codice e XAML interagire.

## <a name="passing-arguments"></a>Passaggio di argomenti

Nel caso generale, una classe creata un'istanza in XAML deve avere un costruttore senza parametri pubblico. l'oggetto risultante viene inizializzato tramite le impostazioni delle proprietà. Tuttavia, esistono altri due metodi che è possono creare un'istanza e inizializzare gli oggetti.

Anche se si tratta di tecniche di uso generale, vengono utilizzate principalmente per MVVM, visualizzazione di modelli.

### <a name="constructors-with-arguments"></a>Costruttori con argomenti

Il [ **ParameteredConstructorDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) esempio viene illustrato come utilizzare il `x:Arguments` tag per specificare gli argomenti del costruttore. Questi argomenti devono essere racchiusi tra tag di elemento che indica il tipo dell'argomento. Per i tipi di dati .NET di base, sono disponibili i seguenti tag:

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

Il [ **FactoryMethodDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) esempio viene illustrato come utilizzare il `x:FactoryMethod` elemento per specificare un metodo factory che viene richiamato per creare un oggetto. Tale metodo factory deve essere pubblico e statico e deve creare un oggetto del tipo in cui è definito. (Ad esempio il [ `Color.FromRgb` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Double/System.Double/System.Double/)) metodo qualifica perché è pubblico e statico e restituisce un valore di tipo `Color`.) Gli argomenti al metodo factory vengono specificati all'interno di `x:Arguments` tag.

## <a name="the-xname-attribute"></a>L'attributo X:Name

Il `x:Name` attributo consente a un oggetto di cui creare un'istanza in XAML devono avere un nome. Le regole per tali nomi sono gli stessi nomi di variabili c#. Dopo la restituzione del `InitializeComponent` chiamare nel costruttore, il file code-behind può fare riferimento a questi nomi per accedere all'elemento corrispondente di XAML. I nomi vengono effettivamente convertiti dal parser XAML in campi privati nella classe parziale generata.

Il [ **XamlClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) esempio viene illustrato l'utilizzo di `x:Name` per consentire il file code-behind mantenere due `Label` elementi definiti in XAML aggiornato con la data e ora correnti.

Lo stesso nome non può essere utilizzato per più elementi nella stessa pagina. Si tratta di un particolare problema se si utilizza `OnPlatform` per creare oggetti denominati per ogni piattaforma. Il [ **PlatformSpecificLabele** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) esempio viene illustrato un modo migliore per eseguire un'operazione simile.

## <a name="custom-xaml-based-views"></a>Visualizzazioni personalizzate basate su XAML

Esistono diversi modi per evitare la ripetizione del markup in XAML. Una tecnica comune consiste nel creare una nuova classe basata su XAML che deriva da [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/). Questa tecnica è dimostrata nel [ **ColorViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) esempio. Il `ColorView` deriva dalla classe `ContentView` per visualizzare un determinato colore e il relativo nome, mentre il `ColorViewListPage` deriva dalla classe `ContentPage` come di consueto e in modo esplicito crea 17 istanze di `ColorView`.

L'accesso di `ColorView` classe in XAML richiede un'altra dichiarazione dello spazio dei nomi XML, comunemente denominata `local` per le classi nello stesso assembly.

## <a name="events-and-handlers"></a>Eventi e gestori

È possibile assegnare ai gestori di eventi in XAML, ma il gestore dell'evento stesso deve essere implementato nel file code-behind. Il [ **XamlKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) viene illustrato come compilare un'interfaccia utente tastierino in XAML e come implementare il `Clicked` gestori nel file code-behind.

## <a name="tap-gestures"></a>Movimenti tocco

Qualsiasi `View` oggetto può ottenere l'input tocco e generare gli eventi da quell'input. Il `View` classe definisce un [ `GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/) proprietà della raccolta che può contenere uno o più istanze di classi che derivano da [ `GestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/).

Il [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) genera [ `Tapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.TapGestureRecognizer.Tapped/) eventi. Il [ **MonkeyTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) programma illustra come collegare `TapGestureRecognizer` quattro oggetti `BoxView` elementi per creare un gioco le:

[![Schermata di triplo della scelta monkey](images/ch08fg07-small.png "gioco imitazione")](images/ch08fg07-large.png#lightbox "imitazione gioco")

Ma la **MonkeyTap** suono effettivamente necessari al programma. (Vedere [capitolo successivo](chapter09.md).)



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 8 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Esempi di capitolo 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Esempio capitolo 8 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
