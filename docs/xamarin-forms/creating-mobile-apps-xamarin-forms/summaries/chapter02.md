---
title: Riassunto del capitolo 2. Anatomia di un'app
description: "Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 2. Anatomia di un'app"
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: f900cb1532ba4415127c95b07e777881e1d74994
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291529"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Riassunto del capitolo 2. Anatomia di un'app

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> Le note in questa pagina indicano le aree in cui Xamarin.Forms si è discostata dal materiale presentato nel libro.

In un'applicazione Xamarin.Forms, gli oggetti che occupano spazio sullo [`VisualElement`](xref:Xamarin.Forms.VisualElement) schermo sono noti come *elementi visivi*, incapsulati dalla classe . Gli elementi visivi possono essere suddivisi in tre categorie corrispondenti a queste classi:Visual Elements can be split into three categories corresponding to these classes:

- [Pagina](xref:Xamarin.Forms.Page)
- [Layout](xref:Xamarin.Forms.Layout)
- [Visualizza](xref:Xamarin.Forms.View)

Una `Page` derivata occupa l'intero schermo o quasi l'intero schermo. Spesso, l'elemento figlio `Layout` di una pagina è un derivato per organizzare gli elementi visivi figlio. Gli elementi `Layout` figlio di `Layout` possono `View` essere altre classi o derivati (spesso chiamati *elementi*), che sono oggetti familiari come testo, bitmap, cursori, pulsanti, caselle di riepilogo e così via.

In questo capitolo viene illustrato come [`Label`](xref:Xamarin.Forms.Label)creare un'applicazione concentrandosi sull'oggetto , ovvero la derivata che visualizza il `View` testo.

## <a name="say-hello"></a>Salutare

Con la piattaforma Xamarin installata, è possibile creare una nuova soluzione Xamarin.Forms in Visual Studio o Visual Studio per Mac. La soluzione [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) usa una libreria di classi portabile per il codice comune.

> [!NOTE]
> Librerie di classi portabili sono state sostituite da librerie .NET Standard.Portable Class Libraries have been replaced by .NET Standard libraries. Tutto il codice di esempio del libro è stato convertito per utilizzare librerie standard .NET.

In questo esempio viene illustrata una soluzione Xamarin.Forms creata in Visual Studio senza modifiche. La soluzione è costituita da quattro progetti:

- [**Ciao**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), una libreria di classi portabile (PCL) condivisa dagli altri progetti
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), un progetto applicativo per Android
- [**Hello.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), un progetto di applicazione per iOS
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), un progetto di applicazione per la piattaforma Windows universale (Windows 10 e Windows 10 Mobile)

> [!NOTE]
> Xamarin.Forms non supporta più Windows 8.1, Windows Phone 8.1 o Windows 10 Mobile, ma le applicazioni Xamarin.Forms vengono eseguite sul desktop di Windows 10.

È possibile impostare uno qualsiasi di questi progetti di applicazione come progetto di avvio e quindi compilare ed eseguire il programma in un dispositivo o un simulatore.

In molti dei programmi Xamarin.Forms non verranno modificati i progetti applicativi. Questi spesso rimangono piccoli stub solo per avviare il programma. La maggior parte del vostro obiettivo sarà la libreria comune a tutte le applicazioni.

## <a name="inside-the-files"></a>All'interno dei file

Gli oggetti visivi visualizzati dal programma **Hello** [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) sono definiti nel costruttore della classe. `App`deriva dalla classe [`Application`](xref:Xamarin.Forms.Application)Xamarin.Forms .

> [!NOTE]
> I modelli di soluzione di Visual Studio per Xamarin.Forms creano una pagina con un file XAML. XAML non viene trattato in questo libro fino al [capitolo 7.](chapter07.md)

La sezione **Riferimenti** del progetto **Hello** PCL include i seguenti assembly Xamarin.Forms:

- **Xamarin.Forms.Core**
- **Xamarin.Forms.Xaml**
- **Xamarin.Forms.Piattaforma**

Le sezioni **Riferimenti** dei cinque progetti di applicazione includono assembly aggiuntivi che si applicano alle singole piattaforme:

- **Xamarin.Forms.Platform.Android**
- **Xamarin.Forms.Platform.iOS**
- **Xamarin.Forms.Platform.UWP**
- **Xamarin.Forms.Piattaforma.WinRT**
- **Xamarin.Forms.Piattaforma.WinRT.Tablet**
- **Xamarin.Forms.Piattaforma.WinRT.Telefono**

> [!NOTE]
> Le sezioni **Riferimenti** di questi progetti non elencano più gli assembly. Al contrario, il file di progetto contiene un **PackageReference** tag che fanno riferimento al pacchetto Xamarin.Forms NuGet. Nella sezione **Riferimenti** di Visual Studio sono elencati il pacchetto **Xamarin.Forms** anziché gli assembly Xamarin.Forms.

Ognuno dei progetti di applicazione `Forms.Init` contiene una `Xamarin.Forms` chiamata al metodo statico nello spazio dei nomi. In questo modo viene inizializzata la libreria Xamarin.Forms. Per ogni `Forms.Init` piattaforma è definita una versione diversa di. Le chiamate a questo metodo sono disponibili nelle classi seguenti:The calls to this method can be found in the following classes:

- Ios:[`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android:[`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [ `App` classe, `OnLaunched` metodo](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

Inoltre, ogni piattaforma deve `App` creare un'istanza del percorso della classe nella libreria condivisa. Ciò si verifica `LoadApplication` in una chiamata a nelle seguenti classi:

- Ios:[`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android:[`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- Uwp:[`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

In caso contrario, questi progetti di applicazione sono normali programmi "non fare nulla".

## <a name="pcl-or-sap"></a>PCL o SAP?

È possibile creare una soluzione Xamarin.Forms con il codice comune in una libreria di classi portabile (PCL) o un progetto di risorse condivise (SAP). Per creare una soluzione SAP, selezionare l'opzione Condivisa in Visual Studio.To create an SAP solution, select the Shared option in Visual Studio. La soluzione [**HelloSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) illustra il modello SAP senza modifiche.

> [!NOTE]
> Librerie di classi portabili è stato sostituito da librerie .NET Standard.Portable Class Libraries has been replaced by .NET Standard libraries. Tutto il codice di esempio del libro è stato convertito per utilizzare librerie standard .NET. In caso contrario, le librerie PCL e .NET Standard sono concettualmente molto simili.

L'approccio libreria raggruppa tutto il codice comune in un progetto di libreria a cui fanno riferimento i progetti di applicazione della piattaforma. Con l'approccio SAP, il codice comune esiste in modo efficace in tutti i progetti di applicazione della piattaforma ed è condiviso tra di essi.

La maggior parte degli sviluppatori Xamarin.Forms preferisce l'approccio della libreria. In questo libro, la maggior parte delle soluzioni utilizza una libreria. Quelli che utilizzano SAP includono un suffisso **Sap** nel nome del progetto.

Con l'approccio SAP il codice nel progetto condiviso può eseguire codice diverso`#if`per`elif`le `#endif`varie piattaforme utilizzando le direttive per il preprocessore di C, , e ) con questi identificatori predefiniti:

- Ios:`__IOS__`
- Android: `__ANDROID__`
- Uwp:`WINDOWS_UWP`

In una libreria condivisa, è possibile determinare su quale piattaforma si sta eseguendo in fase di esecuzione, come si vedrà più avanti in questo capitolo.

## <a name="labels-for-text"></a>Etichette per il testo

Il [**Greetings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) soluzione viene illustrato come aggiungere un nuovo file di C , per il **Greetings** progetto. Questo file definisce `GreetingsPage` una classe `ContentPage`denominata che deriva da . In questo libro, la `ContentPage` maggior parte dei progetti contiene un `Page` singolo derivato il cui nome è il nome del progetto con il suffisso aggiunto.

Il `GreetingsPage` costruttore [`Label`](xref:Xamarin.Forms.Label) crea un'istanza di una visualizzazione, ovvero la visualizzazione Xamarin.Forms che visualizza il testo. La [`Text`](xref:Xamarin.Forms.Label.Text) proprietà viene impostata sul `Label`testo visualizzato dal file . Questo programma `Label` imposta `Content` la `ContentPage`proprietà di . Il costruttore `App` della classe `GreetingsPage` crea quindi `MainPage` un'istanza e lo imposta sulla relativa proprietà.

Il testo viene visualizzato nell'angolo superiore sinistro della pagina. In iOS, ciò significa che si sovrappone alla barra di stato della pagina. Esistono diverse soluzioni a questo problema:

### <a name="solution-1-include-padding-on-the-page"></a>Soluzione 1. Includi spaziatura interna nella pagina

Impostare [`Padding`](xref:Xamarin.Forms.Page.Padding) una proprietà nella pagina. `Padding`è di [`Thickness`](xref:Xamarin.Forms.Thickness)tipo , una struttura con quattro proprietà:

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding`definisce un'area all'interno di una pagina in cui il contenuto è escluso. In questo `Label` modo è possibile evitare di sovrascrivere la barra di stato di iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Soluzione 2. Includi imbottitura solo per iOS (solo SAP)

Impostare una proprietà 'Padding' solo in iOS usando un SAP con una direttiva per il preprocessore di C. Ciò è dimostrato nella soluzione [**GreetingsSap.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap)

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Soluzione 3. Includi imbottitura solo per iOS (PCL o SAP)

Nella versione di Xamarin.Forms utilizzata per `Padding` il libro, una proprietà specifica di iOS [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) in [`Device.OnPlatform<T>`](xref:Xamarin.Forms.Device.OnPlatform*) una libreria di classi Portavana o SAP può essere selezionata utilizzando il metodo o static. Questi metodi sono ora deprecati

I `Device.OnPlatform` metodi vengono utilizzati per eseguire codice specifico della piattaforma o per selezionare valori specifici della piattaforma. Internamente, utilizzano la [`Device.OS`](xref:Xamarin.Forms.Device.OS) proprietà statica di sola lettura, [`TargetPlatform`](xref:Xamarin.Forms.TargetPlatform) che restituisce un membro dell'enumerazione:

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows)per i dispositivi UWP.

I `Device.OnPlatform` metodi, `Device.OS` la proprietà `TargetPlatform` e l'enumerazione sono tutti deprecati. Utilizzare invece [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) la proprietà `string` e confrontare il valore restituito con i seguenti campi statici:

- [`iOS`](xref:Xamarin.Forms.Device.iOS), la stringa "iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android), la stringa "Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP), la stringa "UWP", che fa riferimento alla piattaforma Windows universale

La [`Device.Idiom`](xref:Xamarin.Forms.Device.Idiom) proprietà statica di sola lettura è correlata. Restituisce un membro [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom)di , che dispone di questi membri:

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported)è inutilizzato

Per iOS e Android, `Tablet` il `Phone` cutoff tra e è una larghezza verticale di 600 unità. Per la piattaforma `Desktop` Windows, indica un'applicazione UWP `Phone` in esecuzione in Windows 10 e indica un'applicazione UWP in esecuzione nell'applicazione Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>Soluzione 3a. Impostare il margine sull'etichetta

La [`Margin`](xref:Xamarin.Forms.View.Margin) proprietà è stata introdotta troppo tardi per essere inclusa nel libro, ma è anche di tipo `Thickness` ed è possibile impostarla `Label` su per definire un'area esterna alla vista inclusa nel calcolo del layout della vista.

La `Padding` proprietà è [`Layout`](xref:Xamarin.Forms.Layout) disponibile [`Page`](xref:Xamarin.Forms.Page) solo su e derivati. La `Margin` struttura è [`View`](xref:Xamarin.Forms.View) disponibile su tutti i derivati.

## <a name="solution-4-center-the-label-within-the-page"></a>Soluzione 4. Centrare l'etichetta all'interno della pagina

È possibile `Label` centrare `Page` l'interno (o metterlo in uno [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) degli [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) altri `Label` otto punti) [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions)impostando le proprietà e di e su un valore di tipo . La `LayoutOptions` struttura definisce due proprietà:

- Proprietà [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) di [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment)tipo , enumerazione [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start)con quattro membri: , ovvero [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center)a [`End`](xref:Xamarin.Forms.LayoutAlignment.End)sinistra o in alto a seconda [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)dell'orientamento, , , che a seconda dell'orientamento è impostata su destra o meno a seconda dell'orientamento e .

- Proprietà [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) di `bool`tipo .

In genere queste proprietà non vengono utilizzate direttamente. Al contrario, le combinazioni di queste due proprietà `LayoutOptions`sono fornite da otto proprietà statiche di sola lettura di tipo :

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions`e `VerticalOptions` sono le proprietà più importanti nel layout Xamarin.Forms e sono descritte in dettaglio nel [**capitolo 4. Scorrimento dello stack**](chapter04.md).

Ecco il risultato `HorizontalOptions` con `VerticalOptions` le `Label` proprietà `LayoutOptions.Center`e di entrambi impostati su :

[![Tripla schermata del programma di saluti](images/ch02fg05-small.png "Etichetta centrata orizzontalmente e verticalmente")](images/ch02fg05-large.png#lightbox "Etichetta centrata orizzontalmente e verticalmente")

## <a name="solution-5-center-the-text-within-the-label"></a>Soluzione 5. Centrare il testo all'interno dell'etichetta

È inoltre possibile centrare il testo (o inserirlo [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) in [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) altre `Label` otto posizioni [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) nella pagina) impostando le proprietà e su un membro dell'enumerazione:

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), ovvero a sinistra o in alto (a seconda dell'orientamento)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), ovvero a destra o in basso (a seconda dell'orientamento)

Queste due proprietà sono `Label`definite `HorizontalAlignment` solo `VerticalAlignment` da , `View` mentre le `View` proprietà e sono definite da ed ereditate da tutte le derivate. I risultati visivi potrebbero sembrare simili, ma sono molto diversi come dimostra il capitolo successivo.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 2 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Esempi del capitolo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Capitolo 2 Esempi di F](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Introduzione a Xamarin.Forms](~/get-started/index.yml)
