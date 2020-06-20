---
title: Riepilogo del capitolo 2. Anatomia di un'app
description: "Creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 2. Anatomia di un'app"
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 78da3ed91acea0c056074d712d368de70b251392
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136916"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Riepilogo del capitolo 2. Anatomia di un'app

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> Le note in questa pagina indicano Xamarin.Forms le aree in cui è stato divergente rispetto al materiale presentato nel libro.

In un' Xamarin.Forms applicazione, gli oggetti che occupano spazio sullo schermo sono noti come *elementi visivi*, incapsulati dalla [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe. Gli elementi visivi possono essere suddivisi in tre categorie corrispondenti a queste classi:

- [Page](xref:Xamarin.Forms.Page)
- [Layout](xref:Xamarin.Forms.Layout)
- [Visualizza](xref:Xamarin.Forms.View)

Un `Page` derivato occupa l'intero schermo o quasi l'intera schermata. Spesso, l'elemento figlio di una pagina è un `Layout` derivato per organizzare gli elementi visivi figlio. Gli elementi figlio di `Layout` possono essere altre `Layout` classi o `View` derivati (spesso denominati *elementi*), che sono oggetti noti come testo, bitmap, dispositivi di scorrimento, pulsanti, caselle di riepilogo e così via.

In questo capitolo viene illustrato come creare un'applicazione concentrandosi su [`Label`](xref:Xamarin.Forms.Label) , ovvero sul `View` derivato che Visualizza il testo.

## <a name="say-hello"></a>Pronunciare Hello

Con la piattaforma Novell installata, è possibile creare una nuova Xamarin.Forms soluzione in Visual Studio o Visual Studio per Mac. La soluzione [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) usa una libreria di classi portabile per il codice comune.

> [!NOTE]
> Le librerie di classi portabili sono state sostituite dalle librerie .NET Standard. Tutto il codice di esempio del libro è stato convertito in modo da usare le librerie .NET standard.

In questo esempio viene illustrata una Xamarin.Forms soluzione creata in Visual Studio senza modifiche. La soluzione è costituita da quattro progetti:

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), una libreria di classi portabile (PCL) condivisa dagli altri progetti
- [**Hello. Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), un progetto di applicazione per Android
- [**Hello. iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), un progetto di applicazione per iOS
- [**Hello. UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), un progetto di applicazione per il piattaforma UWP (Universal Windows Platform) (Windows 10 e Windows 10 Mobile)

> [!NOTE]
> Xamarin.Formsnon supporta più Windows 8.1, Windows Phone 8,1 o Windows 10 Mobile, ma le Xamarin.Forms applicazioni vengono eseguite sul desktop di Windows 10.

È possibile fare in modo che uno di questi progetti di applicazione crei il progetto di avvio, quindi compilare ed eseguire il programma in un dispositivo o un simulatore.

In molti Xamarin.Forms programmi, i progetti dell'applicazione non verranno modificati. Spesso rimangono piccoli Stub solo per avviare il programma. La maggior parte dell'obiettivo sarà la libreria comune a tutte le applicazioni.

## <a name="inside-the-files"></a>All'interno dei file

Gli oggetti visivi visualizzati dal programma **Hello** sono definiti nel costruttore della [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) classe. `App`deriva dalla Xamarin.Forms classe [`Application`](xref:Xamarin.Forms.Application) .

> [!NOTE]
> I modelli di soluzione Visual Studio per Xamarin.Forms creano una pagina con un file XAML. XAML non è trattato in questo libro fino al [capitolo 7](chapter07.md).

La sezione **References** del progetto **Hello** PCL include gli Xamarin.Forms assembly seguenti:

- **Xamarin.Forms. Core**
- **Xamarin.Forms. XAML**
- **Xamarin.Forms. Piattaforma**

Le sezioni **riferimenti** dei cinque progetti di applicazione includono assembly aggiuntivi che si applicano alle singole piattaforme:

- **Xamarin.Forms. Platform. Android**
- **Xamarin.Forms. Platform. iOS**
- **Xamarin.Forms. Platform. UWP**
- **Xamarin.Forms. Platform. WinRT**
- **Xamarin.Forms. Platform. WinRT. Tablet**
- **Xamarin.Forms. Platform. WinRT. Phone**

> [!NOTE]
> Le sezioni dei **riferimenti** di questi progetti non elencano più gli assembly. Al contrario, il file di progetto contiene tag **PackageReference** che fanno riferimento al Xamarin.Forms pacchetto NuGet. La sezione **References** in Visual Studio elenca il **Xamarin.Forms** pacchetto anziché gli Xamarin.Forms assembly.

Ogni progetto di applicazione contiene una chiamata al `Forms.Init` metodo statico nello `Xamarin.Forms` spazio dei nomi. Questa operazione consente di inizializzare la Xamarin.Forms libreria. Viene definita una versione diversa di `Forms.Init` per ogni piattaforma. Le chiamate a questo metodo si trovano nelle classi seguenti:

- iOS[`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android[`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [ `App` classe, `OnLaunched` Metodo](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

Ogni piattaforma deve inoltre creare un'istanza del `App` percorso della classe nella libreria condivisa. Questo errore si verifica in una chiamata a `LoadApplication` nelle classi seguenti:

- iOS[`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android[`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP[`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

In caso contrario, questi progetti di applicazioni sono normali programmi "Do Nothing".

## <a name="pcl-or-sap"></a>PCL o SAP?

È possibile creare una Xamarin.Forms soluzione con il codice comune in una libreria di classi portabile (PCL) o in un progetto asset condiviso (SAP). Per creare una soluzione SAP, selezionare l'opzione Shared in Visual Studio. La soluzione [**HelloSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) illustra il modello SAP senza modifiche.

> [!NOTE]
> Le librerie di classi portabili sono state sostituite dalle librerie .NET Standard. Tutto il codice di esempio del libro è stato convertito in modo da usare le librerie .NET standard. In caso contrario, le librerie PCL e .NET Standard sono concettualmente molto simili.

L'approccio della libreria raggruppa tutto il codice comune in un progetto di libreria a cui fanno riferimento i progetti dell'applicazione della piattaforma. Con l'approccio SAP, il codice comune è effettivamente presente in tutti i progetti di applicazioni della piattaforma ed è condiviso tra di essi.

La maggior parte Xamarin.Forms degli sviluppatori preferisce l'approccio alla libreria. In questo libro la maggior parte delle soluzioni usa una libreria. Quelli che usano SAP includono un suffisso **SAP** nel nome del progetto.

Con l'approccio SAP, il codice nel progetto condiviso può eseguire codice diverso per le varie piattaforme usando le direttive per il preprocessore C# ( `#if` , # `elif` e `#endif` ) con questi identificatori predefiniti:

- iOS`__IOS__`
- Android: `__ANDROID__`
- UWP`WINDOWS_UWP`

In una libreria condivisa è possibile determinare la piattaforma in esecuzione in fase di esecuzione, come si vedrà più avanti in questo capitolo.

## <a name="labels-for-text"></a>Etichette per il testo

La soluzione [**Greetings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) illustra come aggiungere un nuovo file C# al progetto **Greetings** . Questo file definisce una classe denominata `GreetingsPage` che deriva da `ContentPage` . In questo libro la maggior parte dei progetti contiene un singolo `ContentPage` derivato il cui nome è il nome del progetto con il suffisso `Page` aggiunto.

Il `GreetingsPage` costruttore crea un'istanza di una [`Label`](xref:Xamarin.Forms.Label) vista, ovvero la Xamarin.Forms vista che Visualizza il testo. La [`Text`](xref:Xamarin.Forms.Label.Text) proprietà viene impostata sul testo visualizzato da `Label` . Questo programma imposta sulla `Label` `Content` proprietà di `ContentPage` . Il costruttore della `App` classe quindi crea un'istanza `GreetingsPage` e la imposta sulla `MainPage` Proprietà.

Il testo viene visualizzato nell'angolo superiore sinistro della pagina. In iOS questo significa che si sovrappone alla barra di stato della pagina. Esistono diverse soluzioni per questo problema:

### <a name="solution-1-include-padding-on-the-page"></a>Soluzione 1. Includi spaziatura interna nella pagina

Impostare una [`Padding`](xref:Xamarin.Forms.Page.Padding) proprietà nella pagina. `Padding`è di tipo [`Thickness`](xref:Xamarin.Forms.Thickness) , una struttura con quattro proprietà:

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding`definisce un'area all'interno di una pagina in cui è escluso il contenuto. `Label`In questo modo, è possibile evitare di sovrascrivere la barra di stato di iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Soluzione 2. Includi spaziatura interna solo per iOS (solo SAP)

Impostare una proprietà "Padding" solo in iOS usando un SAP con una direttiva per il preprocessore C#. Questa operazione viene illustrata nella soluzione [**GreetingsSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) .

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Soluzione 3. Includi spaziatura interna solo per iOS (PCL o SAP)

Nella versione di Xamarin.Forms usata per il libro `Padding` è possibile selezionare una proprietà specifica per iOS in una libreria di classi portabile o SAP usando [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) il [`Device.OnPlatform<T>`](xref:Xamarin.Forms.Device.OnPlatform*) metodo statico o. Questi metodi sono ora deprecati

I `Device.OnPlatform` metodi vengono usati per eseguire codice specifico della piattaforma o per selezionare valori specifici della piattaforma. Internamente, usano la [`Device.OS`](xref:Xamarin.Forms.Device.OS) proprietà statica di sola lettura, che restituisce un membro dell' [`TargetPlatform`](xref:Xamarin.Forms.TargetPlatform) enumerazione:

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows)per i dispositivi UWP.

I `Device.OnPlatform` metodi, la `Device.OS` proprietà e l' `TargetPlatform` enumerazione sono ora deprecati. Usare invece la [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) proprietà e confrontare il `string` valore restituito con i campi statici seguenti:

- [`iOS`](xref:Xamarin.Forms.Device.iOS), stringa "iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android), la stringa "Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP), la stringa "UWP", che fa riferimento al piattaforma UWP (Universal Windows Platform)

La [`Device.Idiom`](xref:Xamarin.Forms.Device.Idiom) proprietà di sola lettura statica è correlata. Viene restituito un membro dell'oggetto [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom) , che dispone di questi membri:

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported)non usato

Per iOS e Android, il cutoff tra `Tablet` e `Phone` è una larghezza verticale di 600 unità. Per la piattaforma Windows, `Desktop` indica un'applicazione UWP in esecuzione in Windows 10 e `Phone` indica un'applicazione UWP in esecuzione nell'applicazione Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>Soluzione 3A. Imposta margine sull'etichetta

La [`Margin`](xref:Xamarin.Forms.View.Margin) proprietà è stata introdotta troppo tardi per essere inclusa nel libro, ma è anche di tipo `Thickness` ed è possibile impostarla su `Label` per definire un'area esterna alla visualizzazione inclusa nel calcolo del layout della visualizzazione.

La `Padding` proprietà è disponibile solo in [`Layout`](xref:Xamarin.Forms.Layout) e [`Page`](xref:Xamarin.Forms.Page) derivati. La `Margin` proprietà è disponibile in tutti i [`View`](xref:Xamarin.Forms.View) derivati.

## <a name="solution-4-center-the-label-within-the-page"></a>Soluzione 4. Centrare l'etichetta all'interno della pagina

È possibile centrare l'oggetto `Label` all'interno `Page` di (o inserirlo in una delle altre otto posizioni) impostando le [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà e di `Label` su un valore di tipo [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) . La `LayoutOptions` struttura definisce due proprietà:

- Una [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) proprietà di tipo [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment) , un'enumerazione con quattro membri: [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start) , che indica Left o top a seconda dell'orientamento,, [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center) [`End`](xref:Xamarin.Forms.LayoutAlignment.End) , che indica Right o Bottom, a seconda dell'orientamento, e [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill) .

- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)Proprietà di tipo `bool` .

In genere queste proprietà non vengono usate direttamente. Al contrario, le combinazioni di queste due proprietà vengono fornite da otto proprietà statiche di sola lettura di tipo `LayoutOptions` :

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions`e `VerticalOptions` sono le proprietà più importanti nel Xamarin.Forms layout e sono descritte più dettagliatamente nel [**capitolo 4. Scorrimento dello stack**](chapter04.md).

Ecco il risultato con le `HorizontalOptions` proprietà e `VerticalOptions` di `Label` entrambi impostati su `LayoutOptions.Center` :

[![Triplice screenshot del programma Greetings](images/ch02fg05-small.png "Etichetta centrata orizzontalmente e verticalmente")](images/ch02fg05-large.png#lightbox "Etichetta centrata orizzontalmente e verticalmente")

## <a name="solution-5-center-the-text-within-the-label"></a>Soluzione 5. Centra il testo all'interno dell'etichetta

È anche possibile centrare il testo (o posizionarlo in otto altre posizioni nella pagina) impostando le [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) proprietà e di `Label` su un membro dell' [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) enumerazione:

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), ovvero Left o Top (a seconda dell'orientamento)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), ovvero Right o Bottom (a seconda dell'orientamento)

Queste due proprietà sono definite solo da `Label` , mentre le `HorizontalAlignment` `VerticalAlignment` proprietà e sono definite da `View` e ereditate da tutti i `View` derivati. I risultati visivi potrebbero sembrare simili, ma sono molto diversi come illustrato nel prossimo capitolo.

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 2 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Esempi del capitolo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Capitolo 2 esempi di F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Introduzione conXamarin.Forms](~/get-started/index.yml)
