---
title: Riepilogo del capitolo 2. Anatomia di un'app
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: f1569911eb47f28ea6aee2313d01e07c801746df
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Riepilogo del capitolo 2. Anatomia di un'app

In un'applicazione di xamarin. Forms, gli oggetti che occupano spazio sullo schermo sono note come *elementi visivi*, incapsulato dal [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) classe. Gli elementi visivi possono essere suddivisi in tre categorie corrispondenti a queste classi:

- [pagina](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)
- [Layout](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)
- [Visualizza](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)

Oggetto `Page` derivato occupa l'intero schermo, o quasi l'intero schermo. Elemento figlio di una pagina è spesso un `Layout` derivato per organizzare gli elementi visivi figlio. Gli elementi figlio del `Layout` possono essere altre `Layout` classi o `View` derivati (spesso chiamato *elementi*), che sono oggetti familiarità, ad esempio testo, bitmap, cursori, pulsanti, caselle di riepilogo e così via.

In questo capitolo viene illustrato come creare un'applicazione di concentrarsi sul [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/), ovvero il `View` derivati che visualizza il testo.

## <a name="say-hello"></a>Ecco

Con la piattaforma Xamarin installata, è possibile creare una nuova soluzione xamarin. Forms in Visual Studio o Visual Studio per Mac. Il [ **Hello** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) soluzione utilizza una libreria di classi portabile per il codice comune. Viene illustrato come una soluzione xamarin. Forms creata in Visual Studio senza modifiche. La soluzione è costituita da sei progetti (l'ultima due dei quali non vengono creati con i modelli di soluzioni xamarin. Forms correnti):

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), una libreria di classe portabile (PCL) condiviso da altri progetti
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), un progetto di applicazione per Android
- [**Hello.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), un progetto di applicazione per iOS
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), un progetto di applicazione per la piattaforma Windows universale (Windows 10 e Windows 10 Mobile)
- [**Hello.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Windows), un progetto di applicazione per Windows 8.1
- [**Hello.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.WinPhone), un progetto di applicazione per Windows Phone 8.1

È possibile apportare questi progetti di applicazione, il progetto di avvio e quindi compilare ed eseguire il programma in un dispositivo o simulatore.

In molti dei programmi di xamarin. Forms, non modificare i progetti di applicazione. Spesso, questi rimangono piccolo stub solo per l'avvio del programma. La maggior parte delle concentrati sarà la libreria di classi portabile comuni a tutte le applicazioni.

## <a name="inside-the-files"></a>All'interno dei file

Gli oggetti visivi visualizzati dal **Hello** definiti nel costruttore del programma di [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) classe. `App` deriva dalla classe di xamarin. Forms [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/).

Il **riferimenti** sezione la **Hello** progetto PCL include i seguenti assembly xamarin. Forms:

- **Xamarin.Forms.Core**
- **Xamarin.Forms.Xaml**
- **Xamarin.Forms.Platform**

Il **riferimenti** sezioni i progetti di cinque applicazione includono assembly aggiuntivi che si applicano alle singole piattaforme:

- **Xamarin.Forms.Platform.Android**
- **Xamarin.Forms.Platform.iOS**
- **Xamarin.Forms.Platform.UWP**
- **Xamarin.Forms.Platform.WinRT**
- **Xamarin.Forms.Platform.WinRT.Tablet**
- **Xamarin.Forms.Platform.WinRT.Phone**

Ognuno dei progetti dell'applicazione contiene una chiamata a statico `Forms.Init` metodo il `Xamarin.Forms` dello spazio dei nomi. Consente di inizializzare la libreria di xamarin. Forms. Una versione diversa di `Forms.Init` è definito per ogni piattaforma. Le chiamate a questo metodo sono reperibile nelle seguenti classi:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- Piattaforma UWP: [ `App` (classe), `OnLaunched` (metodo)](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- Windows 8.1: [ `App` (classe), `OnLaunched` (metodo)](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Windows/App.xaml.cs#L65)
- Windows Phone 8.1: [ `App` (classe), `OnLaunched` (metodo)](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.WinPhone/App.xaml.cs#L67)

È inoltre necessario creare un'istanza di ogni piattaforma di `App` classe posizione nella libreria di classi Portabile. Questo errore si verifica in una chiamata a `LoadApplication` nelle seguenti classi:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- PIATTAFORMA UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)
- Windows 8.1: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Windows/MainPage.xaml.cs)
- Windows Phone 8.1: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.WindowsPhone/MainPage.xaml.cs)

In caso contrario, questi progetti di applicazione sono normali programmi "non eseguire alcuna operazione".

## <a name="pcl-or-sap"></a>Libreria di classi Portabile o SAP?

È possibile creare una soluzione xamarin. Forms con il codice comune in una libreria di classe portabile (PCL) o un progetto Asset condiviso (SAP). Per creare una soluzione SAP, selezionare l'opzione Shared in Visual Studio. Il [ **HelloSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) soluzione viene illustrato il modello di SAP senza modifiche.

I bundle PCL approccio comune tutte le code in un progetto di libreria a cui fa riferimento i progetti di applicazione della piattaforma. Con l'approccio SAP, il codice comune in modo efficace è presente in tutti i progetti di applicazione di piattaforma e viene condiviso tra di essi.

La maggior parte degli sviluppatori di xamarin. Forms preferiscono l'approccio della libreria di classi Portabile. In questo manuale, la maggior parte delle soluzioni è PCL. Quelli che utilizzano SAP includono un **Sap** suffisso nel nome del progetto.

Per supportare tutte le piattaforme di xamarin. Forms, la versione di .NET utilizzata dalla libreria di classi Portabile deve supportare le piattaforme seguenti:

- .NET Framework 4.5
- Windows 8
- Windows Phone 8.1
- Xamarin.Android
- Xamarin.iOS
- Xamarin. IOS (Classic)

Questo è noto come PC profilo 111.

Con l'approccio SAP il codice nel progetto condiviso può eseguire codice diverso per varie piattaforme con le direttive del preprocessore c# (`#if`, #`elif`, e `#endif`) con questi identificatori di predefinite:

- iOS: `__IOS__`
- Android: `__ANDROID__`
- PIATTAFORMA UWP: `WINDOWS_UWP`
- Windows 8.1: `WINDOWS_APP`
- Windows Phone 8.1: `WINDOWS_PHONE_APP`

In una PCL è possibile determinare quale piattaforma su cui viene eseguito in fase di esecuzione, come verrà illustrato più avanti in questo capitolo.

## <a name="labels-for-text"></a>Etichette di testo

Il [ **Greetings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) soluzione viene illustrato come aggiungere un nuovo file c# per il **Greetings** progetto. Questo file definisce una classe denominata `GreetingsPage` che deriva da `ContentPage`. In questo manuale, la maggior parte dei progetti contengono un singolo `ContentPage` derivato il cui nome è il nome del progetto con il suffisso `Page` aggiunto.

Il `GreetingsPage` costruttore crea un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) visualizzazione, ovvero la visualizzazione di xamarin. Forms che visualizza il testo. Il [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) proprietà è impostata per il testo visualizzato per il `Label`. Imposta questo programma il `Label` per il `Content` proprietà `ContentPage`. Il costruttore del `App` quindi un'istanza `GreetingsPage` e lo imposta su relativo `MainPage` proprietà.

Il testo viene visualizzato nell'angolo superiore sinistro della pagina. In iOS, ciò significa che si sovrappone a barra di stato della pagina. Esistono diverse soluzioni a questo problema:

### <a name="solution-1-include-padding-on-the-page"></a>Soluzione 1. Includere la spaziatura interna della pagina

Impostare un [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Padding/) proprietà nella pagina. `Padding` è di tipo [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/), una struttura con quattro proprietà:

- [`Left`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Left/)
- [`Top`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Top/)
- [`Right`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Right/)
- [`Bottom`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Bottom/)

`Padding` Definisce un'area all'interno di una pagina in cui contenuto è escluso. In questo modo il `Label` per evitare di sovrascrivere la barra di stato di iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Soluzione 2. Includere la spaziatura interna solo per iOS (SAP solo)

Impostare una proprietà 'Spaziatura interna' solo per iOS usando un SAP con una direttiva del preprocessore c#. Questa funzionalità viene illustrata la [ **GreetingsSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) soluzione.

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Soluzione 3. Includere la spaziatura interna solo per iOS (libreria di classi Portabile o SAP)

Nella versione di xamarin. Forms utilizzato per il tipo di libro, un `Padding` proprietà specifica di iOS in una libreria di classi Portabile o SAP possono essere selezionati utilizzando il [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) o [ `Device.OnPlatform<T>` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform%7BT%7D/p/T/T/T/) metodo statico. Questi metodi sono ora deprecati

Il `Device.OnPlatform` metodi vengono utilizzati per eseguire codice specifico della piattaforma o per selezionare i valori specifici della piattaforma. Internamente, rendono usano il [ `Device.OS` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.OS/) statico-proprietà di sola lettura, che restituisce un membro del [ `TargetPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetPlatform/) enumerazione:

- [`iOS`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.iOS/)
- [`Android`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Android/)
- [`Windows`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Windows/) per Windows 8.1, Windows Phone 8.1 e tutti i dispositivi UWP.
- [`WinPhone`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.WinPhone/), precedentemente utilizzato per identificare Windows Phone 8.0, ma è ora inutilizzati
- [`Other`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Other/) viene utilizzato

Il `Device.OnPlatform` metodi, il `Device.OS` , proprietà e `TargetPlatform` enumerazione sono ora deprecate. Utilizzare invece il [ `Device.RuntimePlatform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.RuntimePlatform/) proprietà e confrontare il `string` restituito con i campi statici seguenti:

- [`iOS`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.iOS/), la stringa "iOS" 
- [`Android`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Android/), la stringa "Android"
- [`UWP`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.UWP/), la stringa "UWP", che fa riferimento alla piattaforma di Windows Runtime
- [`Windows`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Windows/), la stringa "Windows" per Windows Runtime (Windows 8.1 e Windows Phone 8.1)
- [`WinPhone`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinPhone/), la stringa "WinPhone" per Windows Phone 8.0 

Il [ `Device.Idiom` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.Idiom/) è correlata la proprietà statica di sola lettura. Restituisce un membro del [ `TargetIdiom` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetIdiom/), che dispone di questi membri:

- [`Desktop`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Desktop/)
- [`Tablet`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Tablet/)
- [`Phone`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Phone/)
- [`Unsupported`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Unsupported/) viene utilizzato

Per iOS e Android, il taglio tra `Tablet` e `Phone` una larghezza verticale di 600 unità. Per la piattaforma Windows, `Desktop` indica un'applicazione UWP in esecuzione in Windows 10, `Tablet` è un programma, Windows 8.1 e `Phone` indica un'applicazione UWP in esecuzione in un'applicazione Windows Phone 8.1 o Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>Soluzione 3a. Imposta margine sull'etichetta

Il [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) proprietà è stata introdotta troppo tardi per essere incluse nel libro, ma anche è di tipo `Thickness` e viene impostata per il `Label` per definire un'area all'esterno della vista che è incluso nel calcolo del layout della visualizzazione.

Il `Padding` proprietà è disponibile solo in [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) e [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) derivati. Il `Margin` proprietà è disponibile in tutti [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) derivati.

## <a name="solution-4-center-the-label-within-the-page"></a>Soluzione 4. Centrare l'etichetta all'interno della pagina

Centrare il `Label` all'interno di `Page` (o inserirlo in una delle otto altre posizioni) impostando il [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) le proprietà del `Label` per un valore di tipo [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/). Il `LayoutOptions` struttura definisce due proprietà:

- Un [ `Alignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Alignment/) proprietà di tipo [ `LayoutAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutAlignment/), un'enumerazione con quattro membri: [ `Start` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Start/), vale a dire che sinistro o superiore a seconda di orientamento, [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Center/), [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.End/), ovvero destro o inferiore a seconda dell'orientamento, e [ `Fill` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Fill/).

- Un [ `Expands` ](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Expands/) proprietà di tipo `bool`.

In genere queste proprietà non vengono utilizzate direttamente. Al contrario, le combinazioni di queste due proprietà fornite da otto statica proprietà di sola lettura di tipo `LayoutOptions`:

- [`LayoutOptions.Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`LayoutOptions.Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`LayoutOptions.End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`LayoutOptions.StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`LayoutOptions.CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`LayoutOptions.EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`LayoutOptions.FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

`HorizontalOptions` e `VerticalOptions` sono le proprietà più importanti nel layout di xamarin. Forms e sono descritti in dettaglio in [ **capitolo 4. Scorrere lo Stack**](chapter04.md).

Ecco il risultato con il `HorizontalOptions` e `VerticalOptions` le proprietà di `Label` entrambe impostate su `LayoutOptions.Center`:

[![Schermata triplo del programma greetings](images/ch02fg05-small.png "orizzontalmente e verticalmente centrato etichetta")](images/ch02fg05-large.png#lightbox "orizzontalmente e verticalmente centrato etichetta")

## <a name="solution-5-center-the-text-within-the-label"></a>Soluzione 5. Allineare al centro il testo all'interno dell'etichetta

È anche possibile allineare al centro il testo (o inserirlo in otto altre posizioni nella pagina) impostando il [ `HorizontalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.HorizontalTextAlignment/) e [ `VerticalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.VerticalTextAlignment/) le proprietà di `Label` a un membro del [ `TextAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextAlignment/) enumerazione:

- [`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Start/), significato sinistro o superiore (a seconda di orientamento)
- [`Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Center/)
- [`End`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.End/), vale a dire destro o inferiore (a seconda di orientamento)

Queste due proprietà sono definite solo da `Label`, mentre il `HorizontalAlignment` e `VerticalAlignment` proprietà vengono definite da `View` ed è ereditata da tutte `View` derivati. I risultati visual potrebbero sembrare simili, ma sono molto diversi, come illustrato nel capitolo successivo.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 2 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Esempi di capitolo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Esempi di capitolo 2 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Introduzione a xamarin. Forms](~/xamarin-forms/get-started/index.md)
