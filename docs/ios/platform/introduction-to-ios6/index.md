---
title: Introduzione a iOS 6
description: iOS 6 include un'ampia gamma di nuove tecnologie per lo sviluppo di App, che rende disponibile per gli sviluppatori c# 6 xamarin. IOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 0d08b2ff5131996984dbe41862ad1d6aac33b3d0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-ios-6"></a>Introduzione a iOS 6

_iOS 6 include un'ampia gamma di nuove tecnologie per lo sviluppo di App, che rende disponibile per gli sviluppatori c# 6 xamarin. IOS._

[ ![](images/ios6-large.jpg "Il logo iOS 6")](images/ios6-large.jpg)

IOS 6 e xamarin. IOS 6, gli sviluppatori devono ora una vasta gamma di funzionalità a loro disposizione per creare applicazioni iOS, inclusi quelli che iPhone 5 di destinazione.
Questo documento vengono elencate alcune delle più nuove funzionalità che sono disponibili collegamenti ad articoli per ogni argomento. Inoltre interessa due modifiche che sono importante in quanto gli sviluppatori spostano iOS 6 e la risoluzione di nuovo di iPhone 5.


## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[Introduzione alle visualizzazioni di raccolta](~/ios/user-interface/controls/uicollectionview.md)

Le viste di raccolta consentono di contenuto che deve essere visualizzato utilizzando i layout arbitrario. Consentono di creare con facilità il layout di griglia predefinita, supportando nonché i layout personalizzati. Per ulteriori informazioni, vedere la [Introduzione alle visualizzazioni di raccolta](~/ios/user-interface/controls/uicollectionview.md) [ ](~/ios/user-interface/controls/uicollectionview.md)Guida.


## <a name="introduction-to-pass-kitiosplatformpasskitmd"></a>[Introduzione a passare Kit](~/ios/platform/passkit.md)

Il framework di Kit passare consente alle applicazioni di interagire con passate digitale che sono gestite in all'app Passbook. Per ulteriori informazioni, vedere la [Introduzione alla Guida Kit passare](~/ios/platform/passkit.md).


##  <a name="introduction-to-event-kitiosplatformeventkitmd"></a>[Introduzione al Kit di evento](~/ios/platform/eventkit.md)

Il framework EventKit fornisce un modo per accedere ai dati promemoria che archivia il Database di calendario, calendari e gli eventi del calendario. Accesso ai calendari e calendario eventi è disponibile da iOS 4, ma iOS 6 espone ora l'accesso ai dati di promemoria. Per ulteriori informazioni, vedere il [si](~/ios/platform/eventkit.md) [ntroduction a EventKit](~/ios/platform/eventkit.md) Guida.


##  <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[Introduzione a Framework social networking](~/ios/platform/social-framework.md)

Il Framework di social networking fornisce un'API unificata per l'interazione con i social network inclusi Facebook e Twitter, nonché SinaWeibo per gli utenti in Cina. Per ulteriori informazioni, vedere la [Introduzione a Framework Social](~/ios/platform/social-framework.md) Guida.


##  <a name="changes-to-store-kitchanges-to-storekitmd"></a>[Modifiche per l'archiviazione Kit](changes-to-storekit.md)

Apple è state introdotte due nuove funzionalità di archivio Kit: acquisto e download iTunes o contenuto App Store dall'interno dell'app e l'hosting dei file di contenuto per acquisti in-app. Per ulteriori informazioni, vedere la [diventa Store Kit](changes-to-storekit.md) Guida.


## <a name="other-changes"></a>Altre modifiche


### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload e ViewDidUnload deprecato

Il `ViewWillUnload` e `ViewDidUnload` metodi di `UIViewController` non vengono chiamati in iOS 6. Nelle versioni precedenti di iOS, questi metodi possono essere utilizzati dalle applicazioni di salvare lo stato prima di scaricamento di una vista e il codice di pulitura rispettivamente.

Ad esempio, Visual Studio per Mac creerebbe un metodo denominato `ReleaseDesignerOutlets`, come illustrato di seguito, che viene quindi chiamato dal `ViewDidUnload`:

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

Tuttavia, in iOS 6, non è più necessario chiamare `ReleaseDesignerOutlets`.   
   
   
   
Per il codice di pulizia, è consigliabile utilizzare le applicazioni iOS 6 `DidReceiveMemoryWarning`. Tuttavia, il codice che chiama `Dispose` deve essere utilizzato con cautela e solo per la memoria con utilizzo intensivo oggetti come mostrato di seguito:

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

Chiamata di nuovo, `Dispose` come sopra raramente è necessaria. In generale, che più le applicazioni devono eseguire consiste nel rimuovere i gestori di eventi.

Nel caso di salvataggio dello stato, le applicazioni eseguibili in `ViewWillDisappear` e `ViewDidDisappear` anziché `ViewWillUnload`.


### <a name="iphone-5-resolution"></a>iPhone 5 risoluzione

dispositivi iPhone 5 hanno una risoluzione 640 x 1136. Applicazioni destinato a versioni precedenti di iOS apparirà letterboxed se eseguita in un iPhone 5, come illustrato di seguito:

 [ ![](images/01-letterboxed.png "Applicazioni destinato a versioni precedenti di iOS saranno visualizzate letterboxed quando viene eseguito un iPhone 5")](images/01-letterboxed.png)

Affinché l'applicazione venga visualizzato a schermo intero in iPhone 5, è sufficiente aggiungere un'immagine denominata `Default-568h@2x.png` con una risoluzione di 640 x 1136. La schermata seguente mostra l'applicazione in esecuzione dopo questa immagine è stato inclusa:

 [ ![](images/02-fullscreen.png "Questa schermata mostra l'applicazione in esecuzione dopo questa immagine è stata inclusa")](images/02-fullscreen.png)

### <a name="subclassing-uinavigationbar"></a>Creazione di sottoclassi UINavigationBar

In iOS 6 `UINavigationBar` possono essere sottoclassati. Questo consente un maggiore controllo dell'aspetto del `UINavigationBar`. Ad esempio, le applicazioni possono sottoclasse per aggiungere sottoviste, aggiungere un'animazione a tali viste e modificare i limiti del `UINavigationBar`.

Il codice riportato di seguito viene illustrato un esempio di una sottoclasse `UINavigationBar` che aggiunge un `UIImageView`:

```csharp
public class CustomNavBar : UINavigationBar
{
    UIImageView iv;
    public CustomNavBar (IntPtr h) : base(h)
    {
        iv = new UIImageView (UIImage.FromFile ("monkey.png"));
        iv.Frame = new CGRect (75, 0, 30, 39);
    }
    public override void Draw (RectangleF rect)
    {
        base.Draw (rect);
        TintColor = UIColor.Purple;
        AddSubview (iv);
    }
}
```

Per aggiungere una sottoclasse `UINavigationBar` per un `UINavigationController`, utilizzare il `UINavigationController` costruttore che accetta il tipo del `UINavigationBar` e `UIToolbar`, come illustrato di seguito:

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

Usando questa `UINavigationBar` sottoclasse comporta la visualizzazione di immagini, viene visualizzata, come illustrato nella schermata seguente:

 [ ![](images/03-navbar.png "Utilizzo di questo UINavigationBar sottoclasse la visualizzazione di immagini, viene visualizzata, come illustrato in questa schermata")](images/03-navbar.png)

### <a name="interface-orientation"></a>Orientamento di interfaccia

Prima di iOS è possibile eseguire l'override di 6 applicazioni `ShouldAutorotateToInterfaceOrientation`, restituendo true per tutti gli orientamenti supportati controller specifico. Ad esempio, il codice seguente potrebbe essere utilizzato per supportare solo verticale:

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

In iOS 6 `ShouldAutorotateToInterfaceOrientation` è deprecata.
Invece le applicazioni possono modificare `GetSupportedInterfaceOrientations` nel controller di visualizzazione principale, come illustrato di seguito:

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

Su iPad, l'impostazione predefinita tutti e quattro gli orientamenti se `GetSupportedInterfaceOrientation` non implementato. IPhone e iPod Touch, il valore predefinito è tutti gli orientamenti, ad eccezione `PortraitUpsideDown`.