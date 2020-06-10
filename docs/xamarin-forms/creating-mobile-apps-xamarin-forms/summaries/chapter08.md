---
title: "Riepilogo del capitolo 8. Codice e XAML in Harmony "Descrizione:" creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 8. Codice e XAML in Harmony "ms. prod: Novell MS. Technology: Novell-Forms ms. AssetID: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED Author: davidbritch ms. Author: dabritch ms. Date: 07/19/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Riepilogo del capitolo 8. Armonia tra codice e XAML

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)

In questo capitolo viene esplorato più approfonditamente XAML e in particolare il modo in cui il codice e XAML interagiscono.

## <a name="passing-arguments"></a>Passaggio di argomenti

Nel caso generale, una classe di cui è stata creata un'istanza in XAML deve avere un costruttore pubblico senza parametri; l'oggetto risultante viene inizializzato tramite le impostazioni della proprietà. Esistono tuttavia altri due modi in cui è possibile creare un'istanza e inizializzare gli oggetti.

Sebbene si tratta di tecniche generiche, vengono utilizzate principalmente per la connessione ai modelli di visualizzazione MVVM.

### <a name="constructors-with-arguments"></a>Costruttori con argomenti

L'esempio [**ParameteredConstructorDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) illustra come usare il `x:Arguments` tag per specificare gli argomenti del costruttore. Questi argomenti devono essere delimitati da tag di elemento che indicano il tipo dell'argomento. Per i tipi di dati .NET di base, sono disponibili i seguenti tag:

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

### <a name="can-i-call-methods-from-xaml"></a>È possibile chiamare I metodi da XAML?

Nell'esempio [**FactoryMethodDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) viene illustrato come utilizzare l' `x:FactoryMethod` elemento per specificare un metodo factory richiamato per creare un oggetto. Questo metodo factory deve essere pubblico e statico ed è necessario creare un oggetto del tipo in cui è definito. Il metodo, ad esempio, [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) si qualifica perché è Public e static e restituisce un valore di tipo `Color` . Gli argomenti per il metodo factory sono specificati all'interno dei `x:Arguments` tag.

## <a name="the-xname-attribute"></a>Attributo x:Name

L' `x:Name` attributo consente di assegnare un nome a un oggetto di cui è stata creata un'istanza in XAML. Le regole per questi nomi sono le stesse per i nomi delle variabili C#. In seguito alla restituzione della `InitializeComponent` chiamata nel costruttore, il file code-behind può fare riferimento a questi nomi per accedere all'elemento XAML corrispondente. I nomi vengono effettivamente convertiti dal parser XAML in campi privati nella classe parziale generata.

Nell'esempio [**XamlClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) viene illustrato l'utilizzo di `x:Name` per consentire al file code-behind di tenere `Label` aggiornati due elementi definiti in XAML con la data e l'ora correnti.

Non è possibile usare lo stesso nome per più elementi nella stessa pagina. Si tratta di un problema particolare se si usa `OnPlatform` per creare oggetti denominati paralleli per ogni piattaforma. Nell'esempio [**PlatformSpecificLabele**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) viene illustrato un modo migliore per eseguire un'operazione simile.

## <a name="custom-xaml-based-views"></a>Visualizzazioni personalizzate basate su XAML

Esistono diversi modi per evitare la ripetizione del markup in XAML. Una tecnica comune consiste nel creare una nuova classe basata su XAML che deriva da [`ContentView`](xref:Xamarin.Forms.ContentView) . Questa tecnica è illustrata nell'esempio [**ColorViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) . La `ColorView` classe deriva da `ContentView` per visualizzare un colore particolare e il relativo nome, mentre la `ColorViewListPage` classe deriva da `ContentPage` come di consueto e crea in modo esplicito 17 istanze di `ColorView` .

Per accedere alla `ColorView` classe in XAML è necessaria un'altra dichiarazione dello spazio dei nomi XML, comunemente denominata `local` per le classi nello stesso assembly.

## <a name="events-and-handlers"></a>Eventi e gestori

Gli eventi possono essere assegnati a gestori eventi in XAML, ma il gestore eventi deve essere implementato nel file code-behind. [**XamlKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) illustra come creare un'interfaccia utente per la tastiera in XAML e come implementare i `Clicked` gestori nel file code-behind.

## <a name="tap-gestures"></a>Toccare movimenti

Qualsiasi `View` oggetto può ottenere input tocco e generare eventi da tale input. La `View` classe definisce una [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) proprietà di raccolta che può contenere una o più istanze di classi che derivano da [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) .

[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)Genera [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) gli eventi. Il programma [**MonkeyTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) illustra come aggiungere `TapGestureRecognizer` oggetti a quattro `BoxView` elementi per creare un gioco di imitazione:

[![Schermata tripla del tocco scimmia](images/ch08fg07-small.png "Gioco di imitazione")](images/ch08fg07-large.png#lightbox "Gioco di imitazione")

Ma il programma **MonkeyTap** richiede effettivamente un suono. Vedere [il capitolo successivo](chapter09.md).

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 8 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Esempi capitolo 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Capitolo 8 F # esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [Passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md)
