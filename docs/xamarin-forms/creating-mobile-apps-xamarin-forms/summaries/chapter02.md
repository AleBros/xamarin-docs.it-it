---
title: Riepilogo del capitolo 2. Anatomia di un'app
description: "Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 2. Anatomia di un'app"
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: f900cb1532ba4415127c95b07e777881e1d74994
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78291529"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Riepilogo del capitolo 2. Anatomia di un'app

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> Le note in questa pagina indicano le aree in cui xamarin. Forms è diversa dal materiale presentato nel libro.

In un'applicazione Novell. Forms, gli oggetti che occupano spazio sullo schermo sono noti come *elementi visivi*, incapsulati dalla classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Gli elementi visivi possono essere suddivisa in tre categorie corrispondenti a queste classi:

- [Page](xref:Xamarin.Forms.Page)
- [Layout](xref:Xamarin.Forms.Layout)
- [Visualizza](xref:Xamarin.Forms.View)

Una `Page` derivata occupa l'intero schermo o quasi l'intera schermata. Spesso, l'elemento figlio di una pagina è un `Layout` derivato per organizzare gli elementi visivi figlio. Gli elementi figlio del `Layout` possono essere altre classi `Layout` o derivati `View` (spesso denominati *elementi*), che sono oggetti noti come testo, bitmap, dispositivi di scorrimento, pulsanti, caselle di riepilogo e così via.

In questo capitolo viene illustrato come creare un'applicazione concentrandosi sulla [`Label`](xref:Xamarin.Forms.Label), ovvero sul `View` derivato che Visualizza il testo.

## <a name="say-hello"></a>Dal benvenuto

Con la piattaforma Xamarin installata, è possibile creare una nuova soluzione xamarin. Forms in Visual Studio o Visual Studio per Mac. La soluzione [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) usa una libreria di classi portabile per il codice comune.

> [!NOTE]
> Librerie di classi portabili sono state sostituite dalle librerie .NET Standard. Tutto il codice di esempio dal libro è stato convertito per utilizzare le librerie .NET standard.

Questo esempio illustra una soluzione xamarin. Forms creata in Visual Studio senza modifiche. La soluzione è costituita da quattro progetti:

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), una libreria di classi portabile (PCL) condivisa dagli altri progetti
- [**Hello. Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), un progetto di applicazione per Android
- [**Hello. iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), un progetto di applicazione per iOS
- [**Hello. UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), un progetto di applicazione per il piattaforma UWP (Universal Windows Platform) (Windows 10 e Windows 10 Mobile)

> [!NOTE]
> Xamarin. Forms non supporta più Windows 8.1, Windows Phone 8.1 o Windows 10 Mobile, ma le applicazioni xamarin. Forms vengono eseguite sul desktop di Windows 10.

È possibile apportare qualsiasi di questi progetti di applicazione il progetto di avvio e quindi compilare ed eseguire il programma in un dispositivo o simulatore.

In molti dei propri programmi di xamarin. Forms, non modifica i progetti dell'applicazione. Spesso questi rimangono piccolo stub solo per l'avvio del programma. La maggior parte dell'attenzione sarà la libreria comune a tutte le applicazioni.

## <a name="inside-the-files"></a>All'interno dei file

Gli oggetti visivi visualizzati dal programma **Hello** sono definiti nel costruttore della classe [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) . `App` deriva dalla classe Novell. Forms [`Application`](xref:Xamarin.Forms.Application).

> [!NOTE]
> I modelli di soluzione Visual Studio per xamarin. Forms creano una pagina con un file XAML. XAML non è trattato in questo libro fino al [capitolo 7](chapter07.md).

La sezione **References** del progetto **Hello** PCL include gli assembly Novell. Forms seguenti:

- **Novell. Forms. Core**
- **Novell. Forms. XAML**
- **Novell. Forms. Platform**

Le sezioni **riferimenti** dei cinque progetti di applicazione includono assembly aggiuntivi che si applicano alle singole piattaforme:

- **Novell. Forms. Platform. Android**
- **Novell. Forms. Platform. iOS**
- **Novell. Forms. Platform. UWP**
- **Novell. Forms. Platform. WinRT**
- **Novell. Forms. Platform. WinRT. Tablet**
- **Novell. Forms. Platform. WinRT. Phone**

> [!NOTE]
> Le sezioni dei **riferimenti** di questi progetti non elencano più gli assembly. Al contrario, il file di progetto contiene tag **PackageReference** che fanno riferimento al pacchetto NuGet Novell. Forms. La sezione **References** in Visual Studio elenca il pacchetto **Novell. Forms** anziché gli assembly Novell. Forms.

Ogni progetto di applicazione contiene una chiamata al metodo `Forms.Init` statico nello spazio dei nomi `Xamarin.Forms`. Ciò consente di inizializzare la libreria xamarin. Forms. È stata definita una versione diversa di `Forms.Init` per ogni piattaforma. Le chiamate a questo metodo sono reperibili nelle seguenti classi:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [classe`App`, metodo `OnLaunched`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

Ogni piattaforma deve inoltre creare un'istanza del `App` percorso della classe nella libreria condivisa. Questo errore si verifica in una chiamata a `LoadApplication` nelle classi seguenti:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

In caso contrario, questi progetti di applicazione sono normali programmi "do nothing".

## <a name="pcl-or-sap"></a>Libreria di classi Portabile o SAP?

È possibile creare una soluzione xamarin. Forms con il codice comune in una libreria di classi portabile (PCL) o in un progetto di Asset condivisi (SAP). Per creare una soluzione SAP, selezionare l'opzione Shared in Visual Studio. La soluzione [**HelloSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) illustra il modello SAP senza modifiche.

> [!NOTE]
> Librerie di classi portabile è stata sostituita dalle librerie .NET Standard. Tutto il codice di esempio dal libro è stato convertito per utilizzare le librerie .NET standard. In caso contrario, le librerie PCL e .NET Standard sono concettualmente molto simile.

I bundle di approccio libreria tutti i più comuni di codice in un progetto di libreria a cui fanno riferimento i progetti di applicazione della piattaforma. Con l'approccio SAP, il codice comune in modo efficace è presente in tutti i progetti di applicazione della piattaforma ed è condiviso tra di essi.

La maggior parte degli sviluppatori di xamarin. Forms preferiscono l'approccio di libreria. In questo libro, la maggior parte delle soluzioni di usare una libreria. Quelli che usano SAP includono un suffisso **SAP** nel nome del progetto.

Con l'approccio SAP, il codice nel progetto condiviso può eseguire codice diverso per le varie piattaforme usando C# le direttive per il preprocessore (`#if`, #`elif`e `#endif`) con questi identificatori predefiniti:

- iOS: `__IOS__`
- Android: `__ANDROID__`
- UWP: `WINDOWS_UWP`

In una libreria condivisa, è possibile determinare quale piattaforma su cui viene eseguito in fase di esecuzione, come si vedrà più avanti in questo capitolo.

## <a name="labels-for-text"></a>Etichette di testo

La soluzione [**Greetings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) illustra come aggiungere un nuovo C# file al progetto **Greetings** . Questo file definisce una classe denominata `GreetingsPage` che deriva da `ContentPage`. In questo libro la maggior parte dei progetti contiene una singola `ContentPage` derivata il cui nome è il nome del progetto con il suffisso `Page` accodato.

Il costruttore `GreetingsPage` crea un'istanza di una visualizzazione [`Label`](xref:Xamarin.Forms.Label) , ovvero la visualizzazione Novell. Forms che Visualizza il testo. La proprietà [`Text`](xref:Xamarin.Forms.Label.Text) è impostata sul testo visualizzato dall'`Label`. Questo programma imposta la `Label` sulla proprietà `Content` di `ContentPage`. Il costruttore della classe `App` crea quindi un'istanza di `GreetingsPage` e la imposta sulla relativa proprietà `MainPage`.

Il testo viene visualizzato nell'angolo superiore sinistro della pagina. In iOS, ciò significa che si sovrappone a barra di stato della pagina. Esistono diverse soluzioni a questo problema:

### <a name="solution-1-include-padding-on-the-page"></a>Soluzione 1. Includere la spaziatura interna nella pagina

Impostare una proprietà [`Padding`](xref:Xamarin.Forms.Page.Padding) nella pagina. `Padding` è di tipo [`Thickness`](xref:Xamarin.Forms.Thickness), una struttura con quattro proprietà:

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding` definisce un'area all'interno di una pagina in cui è escluso il contenuto. Ciò consente al `Label` di evitare di sovrascrivere la barra di stato di iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Soluzione 2. Includere la spaziatura interna solo per iOS (solo SAP)

Impostare una proprietà 'Spaziatura interna' solo in iOS usando un SAP con una direttiva del preprocessore c#. Questa operazione viene illustrata nella soluzione [**GreetingsSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) .

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Soluzione 3. Includere la spaziatura interna solo per iOS (libreria di classi Portabile o SAP)

Nella versione di Novell. Forms usata per il libro è possibile selezionare una proprietà di `Padding` specifica di iOS in una libreria di classi portabile o SAP usando il metodo statico [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) o [`Device.OnPlatform<T>`](xref:Xamarin.Forms.Device.OnPlatform*) . Questi metodi sono stati deprecati

I metodi di `Device.OnPlatform` vengono usati per eseguire codice specifico della piattaforma o per selezionare valori specifici della piattaforma. Internamente, usano la proprietà [`Device.OS`](xref:Xamarin.Forms.Device.OS) di sola lettura statica che restituisce un membro dell'enumerazione [`TargetPlatform`](xref:Xamarin.Forms.TargetPlatform) :

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) per i dispositivi UWP.

I metodi `Device.OnPlatform`, la proprietà `Device.OS` e l'enumerazione `TargetPlatform` sono ora deprecati. Usare invece la proprietà [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) e confrontare il valore restituito `string` con i campi statici seguenti:

- [`iOS`](xref:Xamarin.Forms.Device.iOS), stringa "iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android), la stringa "Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP), la stringa "UWP", che fa riferimento al piattaforma UWP (Universal Windows Platform)

Il [`Device.Idiom`](xref:Xamarin.Forms.Device.Idiom) proprietà di sola lettura statica è correlato. Viene restituito un membro dell' [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom), che dispone di questi membri:

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported) non è usato

Per iOS e Android, il cutoff tra `Tablet` e `Phone` è una larghezza verticale di 600 unità. Per la piattaforma Windows, `Desktop` indica un'applicazione UWP in esecuzione in Windows 10 e `Phone` indica un'applicazione UWP in esecuzione nell'applicazione Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>Soluzione 3a. Imposta margine sull'etichetta

Il [`Margin`](xref:Xamarin.Forms.View.Margin) proprietà è stato introdotto troppo tardi per essere incluso nel libro, ma è anche di tipo `Thickness` ed è possibile impostarlo sul `Label` per definire un'area esterna alla visualizzazione inclusa nel calcolo del layout della visualizzazione.

La proprietà `Padding` è disponibile solo in [`Layout`](xref:Xamarin.Forms.Layout) e [`Page`](xref:Xamarin.Forms.Page) derivati. La proprietà `Margin` è disponibile in tutti i [`View`](xref:Xamarin.Forms.View) derivati.

## <a name="solution-4-center-the-label-within-the-page"></a>Soluzione 4. Allineare al centro l'etichetta all'interno della pagina

È possibile centrare la `Label` all'interno del `Page` (o inserirla in una delle altre otto posizioni) impostando le proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) del `Label` su un valore di tipo [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions). La struttura `LayoutOptions` definisce due proprietà:

- Una proprietà [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) di tipo [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment), un'enumerazione con quattro membri: [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start), che indica Left o top a seconda dell'orientamento, [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center) [`End`](xref:Xamarin.Forms.LayoutAlignment.End), che indica Right o Bottom, a seconda dell'orientamento e [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill).

- Proprietà [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) di tipo `bool`.

In genere queste proprietà non vengono usate direttamente. Al contrario, le combinazioni di queste due proprietà vengono fornite da otto proprietà statiche di sola lettura di tipo `LayoutOptions`:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions` e `VerticalOptions` sono le proprietà più importanti nel layout di Novell. Forms e sono descritte in modo più dettagliato nel [**capitolo 4. Scorrimento dello stack**](chapter04.md).

Ecco il risultato con le proprietà `HorizontalOptions` e `VerticalOptions` di `Label` entrambe impostate su `LayoutOptions.Center`:

[![Triplice screenshot del programma Greetings](images/ch02fg05-small.png "Etichetta centrata orizzontalmente e verticalmente")](images/ch02fg05-large.png#lightbox "Etichetta centrata orizzontalmente e verticalmente")

## <a name="solution-5-center-the-text-within-the-label"></a>Soluzione 5. Allineare al centro il testo all'interno dell'etichetta

È anche possibile centrare il testo (o posizionarlo in otto altre posizioni nella pagina) impostando le proprietà [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) e [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) di `Label` su un membro dell'enumerazione [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) :

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), ovvero Left o Top (a seconda dell'orientamento)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), ovvero Right o Bottom (a seconda dell'orientamento)

Queste due proprietà sono definite solo da `Label`, mentre le proprietà `HorizontalAlignment` e `VerticalAlignment` sono definite da `View` e ereditate da tutti i `View` derivati. I risultati visual potrebbero sembrare simili, ma sono molto diversi, come illustrato nel capitolo successivo.

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 2 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Esempi del capitolo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Esempi del F# capitolo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Introduzione con Novell. Forms](~/get-started/index.yml)
