---
title: Introduzione a iOS 6
description: Questo documento include collegamenti alle guide che descrivono le funzionalità introdotte in iOS 6. Le visualizzazioni di raccolta, PassKit, il Framework basati su social network, e modifiche di StoreKit sono illustrate.
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 25926d82e060b91b007da9c2295b328cb049e8df
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103868"
---
# <a name="introduction-to-ios-6"></a>Introduzione a iOS 6

_iOS 6 include una vasta gamma di nuove tecnologie per lo sviluppo di App, che consente di xamarin. IOS 6 alla C# gli sviluppatori._

[ ![](images/ios6-large.jpg "Il logo iOS 6")](images/ios6-large.jpg#lightbox)

IOS 6 e xamarin. IOS 6, gli sviluppatori ora hanno un'ampia gamma di funzionalità a disposizione per creare applicazioni iOS, inclusi quelli tale destinazione iPhone 5.
Questo documento vengono elencate alcune delle nuove funzionalità più interessanti disponibili e i collegamenti agli articoli per ogni argomento. Inoltre tocca le due modifiche che saranno importanti durante gli sviluppatori spostano a iOS 6 e la risoluzione di nuovo di iPhone 5.


## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[Introduzione alle visualizzazioni di raccolta](~/ios/user-interface/controls/uicollectionview.md)

Le visualizzazioni di raccolta consentono il contenuto deve essere visualizzato utilizzando layout arbitrario. Consentono di creare con facilità i layout di griglia per impostazione predefinita, supportando anche i layout personalizzati. Per altre informazioni vedere, il [Introduzione alle visualizzazioni di raccolta](~/ios/user-interface/controls/uicollectionview.md) [ ](~/ios/user-interface/controls/uicollectionview.md)Guida.


## <a name="introduction-to-passkitiosplatformpasskitmd"></a>[Introduction to PassKit](~/ios/platform/passkit.md) (Introduzione a PassKit)

Il framework di PassKit consente alle applicazioni di interagire con passate digitale gestite in all'app Passbook. Per altre informazioni vedere, il [Introduzione alla Guida del Kit passare](~/ios/platform/passkit.md).


##  <a name="introduction-to-eventkitiosplatformeventkitmd"></a>[Introduzione a EventKit](~/ios/platform/eventkit.md)

Il framework EventKit fornisce un modo per accedere i calendari, gli eventi del calendario e i dati di promemoria che archivia il Database di calendario. Accesso ai calendari e calendario degli eventi è stato reso disponibile poiché iOS 4, ma iOS 6 espone ora l'accesso ai dati di promemoria. Per altre informazioni, vedere la [ho](~/ios/platform/eventkit.md) [Introduzione ai EventKit](~/ios/platform/eventkit.md) Guida.


##  <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[Introduzione al Framework basati su social network](~/ios/platform/social-framework.md)

Il Framework basati su social network fornisce un'API unificata per l'interazione con i social network, tra cui Facebook e Twitter, nonché SinaWeibo per gli utenti in Cina. Per altre informazioni vedere, il [Introduzione al Framework basati su social network](~/ios/platform/social-framework.md) Guida.


##  <a name="changes-to-storekitchanges-to-storekitmd"></a>[Modifiche di StoreKit](changes-to-storekit.md)

Apple ha introdotto due nuove funzionalità nel Kit Store: acquisto e il download di iTunes o App Store contenuti all'interno dell'app e hosting dei file di contenuto per gli acquisti in-app!. Per altre informazioni vedere, il [cambia in Store Kit](changes-to-storekit.md) Guida.


## <a name="other-changes"></a>Altre modifiche


### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload e ViewDidUnload deprecato

Il `ViewWillUnload` e `ViewDidUnload` metodi `UIViewController` non vengono chiamati in iOS 6. Nelle versioni precedenti di iOS, questi metodi possono avere stati utilizzati da applicazioni per il salvataggio dello stato prima di una vista viene scaricato e il codice di pulitura rispettivamente.

Ad esempio, Visual Studio per Mac creerà un metodo denominato `ReleaseDesignerOutlets`, come illustrato di seguito, che viene quindi chiamato dal `ViewDidUnload`:

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
   
   
   
Per il codice di pulizia, è necessario utilizzare le applicazioni iOS 6 `DidReceiveMemoryWarning`. Tuttavia, codice che chiama `Dispose` deve essere utilizzata con cautela e solo per la memoria con utilizzo intensivo oggetti come mostrato di seguito:

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

Anche in questo caso, la chiamata `Dispose` come indicato in precedenza dovrebbe raramente necessaria. In genere che necessario eseguire la maggior parte delle applicazioni consiste nel rimuovere i gestori eventi.

Nel caso di salvataggio dello stato, le applicazioni possono eseguire questa operazione in `ViewWillDisappear` e `ViewDidDisappear` invece di `ViewWillUnload`.


### <a name="iphone-5-resolution"></a>iPhone 5 risoluzione

i dispositivi iPhone 5 hanno una risoluzione di 640 x 1136. Le applicazioni destinate a versioni precedenti di iOS verranno visualizzato letterboxed quando eseguito su un iPhone 5, come illustrato di seguito:

 [![](images/01-letterboxed.png "Le applicazioni destinate a versioni precedenti di iOS verranno visualizzato letterboxed quando eseguita in un iPhone 5")](images/01-letterboxed.png#lightbox)

Affinché l'applicazione viene visualizzata a schermo intero in iPhone 5, è sufficiente aggiungere un'immagine denominata `Default-568h@2x.png` con una risoluzione di 640 x 1136. Lo screenshot seguente mostra l'applicazione in esecuzione dopo questa immagine è stato inclusa:

 [![](images/02-fullscreen.png "Questa schermata mostra l'applicazione in esecuzione dopo che l'immagine è stata inclusa")](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>Creazione di una sottoclasse UINavigationBar

In iOS 6 `UINavigationBar` possono essere sottoclassati. Ciò consente un maggiore controllo dell'aspetto del `UINavigationBar`. Ad esempio, le applicazioni possono creare una sottoclasse per aggiungere le visualizzazioni secondarie, aggiungere un'animazione a tali viste e modificare i limiti del `UINavigationBar`.

Il codice seguente illustra un esempio di una sottoclasse `UINavigationBar` che aggiunge un `UIImageView`:

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

Per aggiungere una sottoclasse `UINavigationBar` a un `UINavigationController`, utilizzare il `UINavigationController` costruttore che accetta il tipo del `UINavigationBar` e `UIToolbar`, come illustrato di seguito:

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

Usando questo `UINavigationBar` sottoclasse comporta la visualizzazione di immagini viene visualizzata, come illustrato nello screenshot seguente:

 [![](images/03-navbar.png "Tramite questo avrà come risultato sottoclasse UINavigationBar nella visualizzazione di immagini viene visualizzata, come illustrato in questo screenshot")](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>Orientamento di interfaccia

Prima di iOS è possibile eseguire l'override di 6 applicazioni `ShouldAutorotateToInterfaceOrientation`, restituendo true per tutti gli orientamenti supportati controller specifico. Ad esempio, il codice seguente sarebbe essere utilizzato per supportare solo verticale:

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

In iOS 6 `ShouldAutorotateToInterfaceOrientation` è deprecata.
Al contrario delle applicazioni possono eseguire l'override `GetSupportedInterfaceOrientations` sul controller visualizzazione radice come illustrato di seguito:

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

Su iPad, il valore predefinito è tutti e quattro gli orientamenti se `GetSupportedInterfaceOrientation` non implementato. Su iPhone e iPod Touch, il valore predefinito è tutti gli orientamenti, ad eccezione `PortraitUpsideDown`.
