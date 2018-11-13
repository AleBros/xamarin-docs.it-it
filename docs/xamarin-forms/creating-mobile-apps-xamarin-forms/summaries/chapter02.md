---
title: Riepilogo del capitolo 2. Anatomia di un'app
description: "Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 2. Anatomia di un'app"
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: fcb832e851d7467679d979841dad60d117240b59
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563876"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Riepilogo del capitolo 2. Anatomia di un'app

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui xamarin. Forms è diversa dal materiale presentato nel libro.

In un'applicazione xamarin. Forms, gli oggetti che occupano spazio sullo schermo vengono definiti *gli elementi visivi*, incapsulati dalle [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe. Gli elementi visivi possono essere suddivisa in tre categorie corrispondenti a queste classi:

- [Page](xref:Xamarin.Forms.Page)
- [Layout](xref:Xamarin.Forms.Layout)
- [Visualizza](xref:Xamarin.Forms.View)

Oggetto `Page` derivative occupa l'intera schermata o quasi l'intero schermo. L'elemento figlio di una pagina è spesso un `Layout` derivato per organizzare gli elementi visivi figlio. Gli elementi figlio del `Layout` può essere loro `Layout` classi o `View` derivati (spesso chiamato *elementi*), che sono oggetti familiari, ad esempio testo, bitmap, dispositivi di scorrimento, i pulsanti, caselle di riepilogo e così via.

Questo capitolo viene illustrato come creare un'applicazione focalizzando l'attenzione sul [ `Label` ](xref:Xamarin.Forms.Label), ovvero il `View` derivato che visualizza il testo.

## <a name="say-hello"></a>Dal benvenuto

Con la piattaforma Xamarin installata, è possibile creare una nuova soluzione xamarin. Forms in Visual Studio o Visual Studio per Mac. Il [ **Hello** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) soluzione Usa una libreria di classi portabile per il codice comune. 

> [!NOTE] 
> Librerie di classi portabili sono state sostituite dalle librerie .NET Standard. Tutto il codice di esempio dal libro è stato convertito per utilizzare le librerie .NET standard.

Questo esempio illustra una soluzione xamarin. Forms creata in Visual Studio senza modifiche. La soluzione è costituita da sei progetti:

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), una libreria di classi portabile (PCL) condiviso da altri progetti
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), un progetto di App per Android
- [**A Hello. IOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), un progetto di App per iOS
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), un progetto di applicazione per la piattaforma Windows Universal (Windows 10 e Windows 10 Mobile)
- [**Hello.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Windows), un progetto di applicazione per Windows 8.1
- [**Hello.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.WinPhone), un progetto di applicazione per Windows Phone 8.1

> [!NOTE] 
> Xamarin. Forms non supporta più Windows 8.1, Windows Phone 8.1 o Windows 10 Mobile, ma le applicazioni xamarin. Forms vengono eseguite sul desktop di Windows 10. 

È possibile apportare qualsiasi di questi progetti di applicazione il progetto di avvio e quindi compilare ed eseguire il programma in un dispositivo o simulatore.

In molti dei propri programmi di xamarin. Forms, non modifica i progetti dell'applicazione. Spesso questi rimangono piccolo stub solo per l'avvio del programma. La maggior parte dell'attenzione sarà la libreria comune a tutte le applicazioni.

## <a name="inside-the-files"></a>All'interno dei file

Gli oggetti visivi visualizzati per il **Hello** programma sono definiti nel costruttore della [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) classe. `App` deriva dalla classe di xamarin. Forms [ `Application` ](xref:Xamarin.Forms.Application).

> [!NOTE] 
> I modelli di soluzione Visual Studio per xamarin. Forms creano una pagina con un file XAML. XAML non è illustrata in questo libro fino alla [capitolo 7](chapter07.md).

Il **riferimenti** sezione il **Hello** progetto PCL include i seguenti assembly xamarin. Forms:

- **Xamarin.Forms.Core**
- **Xamarin.Forms.Xaml**
- **Xamarin.Forms.Platform**

Il **riferimenti** sezioni dei progetti del cinque applicazione includono assembly aggiuntivi che si applicano alle singole piattaforme:

- **Xamarin.Forms.Platform.Android**
- **Xamarin.Forms.Platform.iOS**
- **Xamarin.Forms.Platform.UWP**
- **Xamarin.Forms.Platform.WinRT**
- **Xamarin.Forms.Platform.WinRT.Tablet**
- **Xamarin.Forms.Platform.WinRT.Phone**

> [!NOTE] 
> Il **riferimenti** nelle sezioni di questi progetti non sono elencati gli assembly. Al contrario, il file di progetto contiene un **PackageReference** tag facendo riferimento al pacchetto NuGet di xamarin. Forms. Il **riferimenti** sezione negli elenchi di Visual Studio il **xamarin. Forms** package anziché l'assembly xamarin. Forms. 

Ognuno dei progetti dell'applicazione contiene una chiamata alla proprietà statica `Forms.Init` metodo nella `Xamarin.Forms` dello spazio dei nomi. Ciò consente di inizializzare la libreria xamarin. Forms. Una versione diversa di `Forms.Init` definita per ogni piattaforma. Le chiamate a questo metodo sono reperibili nelle seguenti classi:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- Piattaforma UWP: [ `App` classe `OnLaunched` (metodo)](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

È inoltre necessario creare un'istanza di ogni piattaforma di `App` classe percorso nella libreria condivisa. Ciò si verifica in una chiamata a `LoadApplication` nelle seguenti classi:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- PIATTAFORMA UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

In caso contrario, questi progetti di applicazione sono normali programmi "do nothing".

## <a name="pcl-or-sap"></a>Libreria di classi Portabile o SAP?

È possibile creare una soluzione xamarin. Forms con il codice comune in una libreria di classi portabile (PCL) o in un progetto di Asset condivisi (SAP). Per creare una soluzione SAP, selezionare l'opzione Shared in Visual Studio. Il [ **HelloSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) soluzione illustra il modello SAP senza modifiche.

> [!NOTE] 
> Librerie di classi portabile è stata sostituita dalle librerie .NET Standard. Tutto il codice di esempio dal libro è stato convertito per utilizzare le librerie .NET standard. In caso contrario, le librerie PCL e .NET Standard sono concettualmente molto simile.

I bundle di approccio libreria tutti i più comuni di codice in un progetto di libreria a cui fanno riferimento i progetti di applicazione della piattaforma. Con l'approccio SAP, il codice comune in modo efficace è presente in tutti i progetti di applicazione della piattaforma ed è condiviso tra di essi.

La maggior parte degli sviluppatori di xamarin. Forms preferiscono l'approccio di libreria. In questo libro, la maggior parte delle soluzioni di usare una libreria. Quelle che usano SAP include un' **Sap** suffisso nel nome del progetto.

L'approccio SAP il codice nel progetto condiviso permette di eseguire codice diverso per varie piattaforme utilizzando direttive del preprocessore c# (`#if`, &`elif`, e `#endif`) con questi identificatori predefiniti:

- iOS: `__IOS__`
- Android: `__ANDROID__`
- PIATTAFORMA UWP: `WINDOWS_UWP`

In una libreria condivisa, è possibile determinare quale piattaforma su cui viene eseguito in fase di esecuzione, come si vedrà più avanti in questo capitolo.

## <a name="labels-for-text"></a>Etichette di testo

Il [ **Greetings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) soluzione illustra come aggiungere un nuovo file c# per il **Greetings** progetto. Questo file definisce una classe denominata `GreetingsPage` che deriva da `ContentPage`. In questo libro, la maggior parte dei progetti contengono un unico `ContentPage` derivato il cui nome è il nome del progetto con il suffisso `Page` aggiunto.

Il `GreetingsPage` costruttore crea un [ `Label` ](xref:Xamarin.Forms.Label) visualizzazione, che è la visualizzazione di xamarin. Forms che visualizza il testo. Il [ `Text` ](xref:Xamarin.Forms.Label.Text) viene impostata per il testo visualizzato dal `Label`. Imposta questo programma la `Label` per il `Content` proprietà di `ContentPage`. Il costruttore del `App` classe crea quindi un'istanza `GreetingsPage` e lo imposta su relativo `MainPage` proprietà.

Il testo viene visualizzato nell'angolo superiore sinistro della pagina. In iOS, ciò significa che si sovrappone a barra di stato della pagina. Esistono diverse soluzioni a questo problema:

### <a name="solution-1-include-padding-on-the-page"></a>Soluzione 1. Includere la spaziatura interna nella pagina

Impostare una [ `Padding` ](xref:Xamarin.Forms.Page.Padding) proprietà nella pagina. `Padding` JE typu [ `Thickness` ](xref:Xamarin.Forms.Thickness), una struttura con quattro proprietà:

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding` Definisce un'area all'interno di una pagina in cui è escluso il contenuto. In questo modo il `Label` per evitare di sovrascrivere la barra di stato iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Soluzione 2. Includere la spaziatura interna solo per iOS (solo SAP)

Impostare una proprietà 'Spaziatura interna' solo in iOS usando un SAP con una direttiva del preprocessore c#. Questa funzionalità viene illustrata la [ **GreetingsSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) soluzione.

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Soluzione 3. Includere la spaziatura interna solo per iOS (libreria di classi Portabile o SAP)

Nella versione di xamarin. Forms utilizzato per il libro, una `Padding` proprietà specifici ai dispositivi iOS in una libreria di classi Portabile o SAP possono essere selezionati utilizzando il [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) oppure [ `Device.OnPlatform<T>` ](xref:Xamarin.Forms.Device.OnPlatform*) metodo statico. Questi metodi sono stati deprecati

Il `Device.OnPlatform` metodi vengono utilizzati per eseguire codice specifico della piattaforma o per selezionare i valori specifici della piattaforma. Internamente, rendono Usa la [ `Device.OS` ](xref:Xamarin.Forms.Device.OS) sola lettura proprietà statica, che restituisce un membro del [ `TargetPlatform` ](xref:Xamarin.Forms.TargetPlatform) enumerazione:

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) per i dispositivi di piattaforma UWP.

Il `Device.OnPlatform` metodi, le `Device.OS` proprietà e il `TargetPlatform` enumerazione sono tutti ora deprecata. Usare invece il [ `Device.RuntimePlatform` ](xref:Xamarin.Forms.Device.RuntimePlatform) proprietà e il confronto di `string` restituito con i campi statici seguenti:

- [`iOS`](xref:Xamarin.Forms.Device.iOS), la stringa "iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android), la stringa "Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP), la stringa "UWP", che fa riferimento alla piattaforma Windows universale

Il [ `Device.Idiom` ](xref:Xamarin.Forms.Device.Idiom) è correlata la proprietà statica di sola lettura. Restituisce un membro del [ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom), che dispone di questi membri:

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported) non viene utilizzato

Per iOS e Android, tra il taglio `Tablet` e `Phone` una larghezza verticale delle 600 unità. Per la piattaforma Windows, `Desktop` indica un'applicazione UWP in esecuzione in Windows 10, e `Phone` indica un'applicazione UWP in esecuzione con applicazioni di Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>Soluzione 3a. Imposta margine sull'etichetta

Il [ `Margin` ](xref:Xamarin.Forms.View.Margin) proprietà è stata introdotta troppo tardi per essere inclusi nel libro, ma anche è di tipo `Thickness` ed è possibile impostarla sul `Label` per definire un'area all'esterno della vista che viene incluso nel calcolo del layout della visualizzazione.

Il `Padding` proprietà è disponibile solo nei [ `Layout` ](xref:Xamarin.Forms.Layout) e [ `Page` ](xref:Xamarin.Forms.Page) derivati. Il `Margin` proprietà è disponibile in tutti i [ `View` ](xref:Xamarin.Forms.View) derivati.

## <a name="solution-4-center-the-label-within-the-page"></a>Soluzione 4. Allineare al centro l'etichetta all'interno della pagina

È possibile centrare il `Label` all'interno di `Page` (o inserirlo in uno degli otto altre posizioni) impostando il [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) e [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) le proprietà del `Label` per un valore di tipo [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions). Il `LayoutOptions` struttura definisce due proprietà:

- Un' [ `Alignment` ](xref:Xamarin.Forms.LayoutOptions.Alignment) vlastnosti typu [ `LayoutAlignment` ](xref:Xamarin.Forms.LayoutAlignment), un'enumerazione con quattro membri: [ `Start` ](xref:Xamarin.Forms.LayoutAlignment.Start), ovvero sinistro o superiore a seconda di orientamento [ `Center` ](xref:Xamarin.Forms.LayoutAlignment.Center), [ `End` ](xref:Xamarin.Forms.LayoutAlignment.End), il che significa destro o inferiore a seconda dell'orientamento, e [ `Fill` ](xref:Xamarin.Forms.LayoutAlignment.Fill).

- Un' [ `Expands` ](xref:Xamarin.Forms.LayoutOptions.Expands) vlastnosti typu `bool`.

In genere queste proprietà non vengono usate direttamente. Al contrario, le combinazioni di queste due proprietà fornite da otto statica proprietà di sola lettura di tipo `LayoutOptions`:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions` e `VerticalOptions` sono le proprietà più importanti nel layout di xamarin. Forms e sono descritti più dettagliatamente in [ **capitolo 4. Scorrimento dello Stack**](chapter04.md).

Ecco il risultato con il `HorizontalOptions` e `VerticalOptions` delle proprietà di `Label` entrambi impostati su `LayoutOptions.Center`:

[![Tripla screenshot del programma di messaggi di saluto](images/ch02fg05-small.png "orizzontalmente e verticalmente al centro etichettare")](images/ch02fg05-large.png#lightbox "orizzontalmente e verticalmente al centro un'etichetta")

## <a name="solution-5-center-the-text-within-the-label"></a>Soluzione 5. Allineare al centro il testo all'interno dell'etichetta

È anche possibile centrare il testo (o inserirlo in otto altre posizioni nella pagina) impostando il [ `HorizontalTextAlignment` ](xref:Xamarin.Forms.Label.HorizontalTextAlignment) e [ `VerticalTextAlignment` ](xref:Xamarin.Forms.Label.VerticalTextAlignment) le proprietà di `Label` a un membro del [ `TextAlignment` ](xref:Xamarin.Forms.TextAlignment) enumerazione:

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), significato sinistro o superiore (a seconda di orientamento)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), vale a dire destro o inferiore (a seconda di orientamento)

Queste due proprietà sono definite solo da `Label`, mentre la `HorizontalAlignment` e `VerticalAlignment` proprietà vengono definite dalle `View` ed ereditato da tutti `View` derivati. I risultati visual potrebbero sembrare simili, ma sono molto diversi, come illustrato nel capitolo successivo.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 2 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Esempi di capitolo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Capitolo 2 F# esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Introduzione a xamarin. Forms](~/xamarin-forms/get-started/index.md)
