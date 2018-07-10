---
title: Riepilogo del capitolo 2. Anatomia di un'app
description: "Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 2. Anatomia di un'app"
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 94c575bdfdc2325def00de58381f9bc295d953b9
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935120"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Riepilogo del capitolo 2. Anatomia di un'app

In un'applicazione xamarin. Forms, gli oggetti che occupano spazio sullo schermo vengono definiti *gli elementi visivi*, incapsulati dalle [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) classe. Gli elementi visivi possono essere suddivisa in tre categorie corrispondenti a queste classi:

- [Page](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)
- [Layout](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)
- [Visualizza](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)

Oggetto `Page` derivative occupa l'intera schermata o quasi l'intero schermo. L'elemento figlio di una pagina è spesso un `Layout` derivato per organizzare gli elementi visivi figlio. Gli elementi figlio del `Layout` può essere loro `Layout` classi o `View` derivati (spesso chiamato *elementi*), che sono oggetti familiari, ad esempio testo, bitmap, dispositivi di scorrimento, i pulsanti, caselle di riepilogo e così via.

Questo capitolo viene illustrato come creare un'applicazione focalizzando l'attenzione sul [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/), ovvero il `View` derivato che visualizza il testo.

## <a name="say-hello"></a>Dal benvenuto

Con la piattaforma Xamarin installata, è possibile creare una nuova soluzione xamarin. Forms in Visual Studio o Visual Studio per Mac. Il [ **Hello** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) soluzione Usa una libreria di classi portabile per il codice comune. Viene illustrato come una soluzione xamarin. Forms creata in Visual Studio senza modifiche. La soluzione è costituita da sei progetti (l'ultimo due dei quali non vengono creati con i modelli di soluzione xamarin. Forms correnti):

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), una libreria di classi portabile (PCL) condiviso da altri progetti
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), un progetto di App per Android
- [**A Hello. IOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), un progetto di App per iOS
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), un progetto di applicazione per la piattaforma Windows Universal (Windows 10 e Windows 10 Mobile)
- [**Hello.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Windows), un progetto di applicazione per Windows 8.1
- [**Hello.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.WinPhone), un progetto di applicazione per Windows Phone 8.1

È possibile apportare qualsiasi di questi progetti di applicazione il progetto di avvio e quindi compilare ed eseguire il programma in un dispositivo o simulatore.

In molti dei propri programmi di xamarin. Forms, non modifica i progetti dell'applicazione. Spesso questi rimangono piccolo stub solo per l'avvio del programma. La maggior parte dell'attenzione sarà la libreria di classi portabile comuni a tutte le applicazioni.

## <a name="inside-the-files"></a>All'interno dei file

Gli oggetti visivi visualizzati per il **Hello** programma sono definiti nel costruttore della [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) classe. `App` deriva dalla classe di xamarin. Forms [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/).

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

Ognuno dei progetti dell'applicazione contiene una chiamata alla proprietà statica `Forms.Init` metodo nella `Xamarin.Forms` dello spazio dei nomi. Ciò consente di inizializzare la libreria xamarin. Forms. Una versione diversa di `Forms.Init` definita per ogni piattaforma. Le chiamate a questo metodo sono reperibili nelle seguenti classi:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- Piattaforma UWP: [ `App` classe `OnLaunched` (metodo)](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- Windows 8.1: [ `App` classe `OnLaunched` (metodo)](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Windows/App.xaml.cs#L65)
- Windows Phone 8.1: [ `App` classe `OnLaunched` (metodo)](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.WinPhone/App.xaml.cs#L67)

È inoltre necessario creare un'istanza di ogni piattaforma di `App` classe posizione nella libreria di classi Portabile. Ciò si verifica in una chiamata a `LoadApplication` nelle seguenti classi:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- PIATTAFORMA UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)
- Windows 8.1: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Windows/MainPage.xaml.cs)
- Windows Phone 8.1: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.WindowsPhone/MainPage.xaml.cs)

In caso contrario, questi progetti di applicazione sono normali programmi "do nothing".

## <a name="pcl-or-sap"></a>Libreria di classi Portabile o SAP?

È possibile creare una soluzione xamarin. Forms con il codice comune in una libreria di classi portabile (PCL) o in un progetto di Asset condivisi (SAP). Per creare una soluzione SAP, selezionare l'opzione Shared in Visual Studio. Il [ **HelloSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) soluzione illustra il modello SAP senza modifiche.

I bundle di approccio PCL tutti i più comuni di codice in un progetto di libreria a cui fanno riferimento i progetti di applicazione della piattaforma. Con l'approccio SAP, il codice comune in modo efficace è presente in tutti i progetti di applicazione della piattaforma ed è condiviso tra di essi.

La maggior parte degli sviluppatori di xamarin. Forms preferiscono l'approccio di libreria di classi Portabile. In questo libro, la maggior parte delle soluzioni è libreria di classi Portabile. Quelle che usano SAP include un' **Sap** suffisso nel nome del progetto.

Per supportare tutte le piattaforme di xamarin. Forms, la versione di .NET utilizzata dalla libreria di classi Portabile deve supportare le piattaforme seguenti:

- .NET Framework 4.5
- Windows 8
- Windows Phone 8.1
- Xamarin.Android
- Xamarin.iOS
- Xamarin. IOS (versione classica)

Questo è noto come PC profilo 111.

L'approccio SAP il codice nel progetto condiviso permette di eseguire codice diverso per varie piattaforme utilizzando direttive del preprocessore c# (`#if`, &`elif`, e `#endif`) con questi identificatori predefiniti:

- iOS: `__IOS__`
- Android: `__ANDROID__`
- PIATTAFORMA UWP: `WINDOWS_UWP`
- Windows 8.1: `WINDOWS_APP`
- Windows Phone 8.1: `WINDOWS_PHONE_APP`

In una libreria di classi Portabile è possibile determinare quale piattaforma su cui viene eseguito in fase di esecuzione, come si vedrà più avanti in questo capitolo.

## <a name="labels-for-text"></a>Etichette di testo

Il [ **Greetings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) soluzione illustra come aggiungere un nuovo file c# per il **Greetings** progetto. Questo file definisce una classe denominata `GreetingsPage` che deriva da `ContentPage`. In questo libro, la maggior parte dei progetti contengono un unico `ContentPage` derivato il cui nome è il nome del progetto con il suffisso `Page` aggiunto.

Il `GreetingsPage` costruttore crea un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) visualizzazione, che è la visualizzazione di xamarin. Forms che visualizza il testo. Il [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) viene impostata per il testo visualizzato dal `Label`. Imposta questo programma la `Label` per il `Content` proprietà di `ContentPage`. Il costruttore del `App` classe crea quindi un'istanza `GreetingsPage` e lo imposta su relativo `MainPage` proprietà.

Il testo viene visualizzato nell'angolo superiore sinistro della pagina. In iOS, ciò significa che si sovrappone a barra di stato della pagina. Esistono diverse soluzioni a questo problema:

### <a name="solution-1-include-padding-on-the-page"></a>Soluzione 1. Includere la spaziatura interna nella pagina

Impostare una [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Padding/) proprietà nella pagina. `Padding` JE typu [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/), una struttura con quattro proprietà:

- [`Left`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Left/)
- [`Top`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Top/)
- [`Right`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Right/)
- [`Bottom`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Bottom/)

`Padding` Definisce un'area all'interno di una pagina in cui è escluso il contenuto. In questo modo il `Label` per evitare di sovrascrivere la barra di stato iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Soluzione 2. Includere la spaziatura interna solo per iOS (solo SAP)

Impostare una proprietà 'Spaziatura interna' solo in iOS usando un SAP con una direttiva del preprocessore c#. Questa funzionalità viene illustrata la [ **GreetingsSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) soluzione.

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Soluzione 3. Includere la spaziatura interna solo per iOS (libreria di classi Portabile o SAP)

Nella versione di xamarin. Forms utilizzato per il libro, una `Padding` proprietà specifici ai dispositivi iOS in una libreria di classi Portabile o SAP possono essere selezionati utilizzando il [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) oppure [ `Device.OnPlatform<T>` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform%7BT%7D/p/T/T/T/) metodo statico. Questi metodi sono stati deprecati

Il `Device.OnPlatform` metodi vengono utilizzati per eseguire codice specifico della piattaforma o per selezionare i valori specifici della piattaforma. Internamente, rendono Usa la [ `Device.OS` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.OS/) sola lettura proprietà statica, che restituisce un membro del [ `TargetPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetPlatform/) enumerazione:

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) per Windows 8.1, Windows Phone 8.1 e tutti i dispositivi di piattaforma UWP.
- [`WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone), usato per identificare Windows Phone 8.0 in precedenza ma è ora inutilizzata
- [`Other`](xref:Xamarin.Forms.TargetPlatform.Other) non viene utilizzato

Il `Device.OnPlatform` metodi, le `Device.OS` proprietà e il `TargetPlatform` enumerazione sono tutti ora deprecata. Usare invece il [ `Device.RuntimePlatform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.RuntimePlatform/) proprietà e il confronto di `string` restituito con i campi statici seguenti:

- [`iOS`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.iOS/), la stringa "iOS"
- [`Android`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Android/), la stringa "Android"
- [`UWP`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.UWP/), la stringa "UWP", che fa riferimento alla piattaforma di Windows Runtime
- [`Windows`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Windows/), la stringa "Windows" per il Runtime di Windows (Windows 8.1 e Windows Phone 8.1)
- [`WinPhone`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinPhone/), la stringa "WinPhone" per Windows Phone 8.0

Il [ `Device.Idiom` ](xref:Xamarin.Forms.Device.Idiom) è correlata la proprietà statica di sola lettura. Restituisce un membro del [ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom), che dispone di questi membri:

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported) non viene utilizzato

Per iOS e Android, tra il taglio `Tablet` e `Phone` una larghezza verticale delle 600 unità. Per la piattaforma Windows, `Desktop` indica un'applicazione UWP in esecuzione in Windows 10 `Tablet` è un programma, Windows 8.1 e `Phone` indica un'applicazione UWP in esecuzione con un'applicazione Windows Phone 8.1 o Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>Soluzione 3a. Imposta margine sull'etichetta

Il [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) proprietà è stata introdotta troppo tardi per essere inclusi nel libro, ma anche è di tipo `Thickness` ed è possibile impostarla sul `Label` per definire un'area all'esterno della vista che viene incluso nel calcolo del layout della visualizzazione.

Il `Padding` proprietà è disponibile solo nei [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) e [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) derivati. Il `Margin` proprietà è disponibile in tutti i [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) derivati.

## <a name="solution-4-center-the-label-within-the-page"></a>Soluzione 4. Allineare al centro l'etichetta all'interno della pagina

È possibile centrare il `Label` all'interno di `Page` (o inserirlo in uno degli otto altre posizioni) impostando il [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) le proprietà del `Label` per un valore di tipo [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/). Il `LayoutOptions` struttura definisce due proprietà:

- Un' [ `Alignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Alignment/) vlastnosti typu [ `LayoutAlignment` ](xref:Xamarin.Forms.LayoutAlignment), un'enumerazione con quattro membri: [ `Start` ](xref:Xamarin.Forms.LayoutAlignment.Start), ovvero sinistro o superiore a seconda di orientamento [ `Center` ](xref:Xamarin.Forms.LayoutAlignment.Center), [ `End` ](xref:Xamarin.Forms.LayoutAlignment.End), il che significa destro o inferiore a seconda dell'orientamento, e [ `Fill` ](xref:Xamarin.Forms.LayoutAlignment.Fill).

- Un' [ `Expands` ](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Expands/) vlastnosti typu `bool`.

In genere queste proprietà non vengono usate direttamente. Al contrario, le combinazioni di queste due proprietà fornite da otto statica proprietà di sola lettura di tipo `LayoutOptions`:

- [`LayoutOptions.Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`LayoutOptions.Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`LayoutOptions.End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`LayoutOptions.StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`LayoutOptions.CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`LayoutOptions.EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`LayoutOptions.FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

`HorizontalOptions` e `VerticalOptions` sono le proprietà più importanti nel layout di xamarin. Forms e sono descritti più dettagliatamente in [ **capitolo 4. Scorrimento dello Stack**](chapter04.md).

Ecco il risultato con il `HorizontalOptions` e `VerticalOptions` delle proprietà di `Label` entrambi impostati su `LayoutOptions.Center`:

[![Tripla screenshot del programma di messaggi di saluto](images/ch02fg05-small.png "orizzontalmente e verticalmente al centro etichettare")](images/ch02fg05-large.png#lightbox "orizzontalmente e verticalmente al centro un'etichetta")

## <a name="solution-5-center-the-text-within-the-label"></a>Soluzione 5. Allineare al centro il testo all'interno dell'etichetta

È anche possibile centrare il testo (o inserirlo in otto altre posizioni nella pagina) impostando il [ `HorizontalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.HorizontalTextAlignment/) e [ `VerticalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.VerticalTextAlignment/) le proprietà di `Label` a un membro del [ `TextAlignment` ](xref:Xamarin.Forms.TextAlignment) enumerazione:

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), significato sinistro o superiore (a seconda di orientamento)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), vale a dire destro o inferiore (a seconda di orientamento)

Queste due proprietà sono definite solo da `Label`, mentre la `HorizontalAlignment` e `VerticalAlignment` proprietà vengono definite dalle `View` ed ereditato da tutti `View` derivati. I risultati visual potrebbero sembrare simili, ma sono molto diversi, come illustrato nel capitolo successivo.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 2 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Esempi di capitolo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Esempi di capitolo 2 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Introduzione a xamarin. Forms](~/xamarin-forms/get-started/index.md)
