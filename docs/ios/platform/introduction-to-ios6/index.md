---
title: Introduzione a iOS 6
description: Questo documento contiene collegamenti a guide che descrivono le funzionalità introdotte in iOS 6. Sono illustrate tutte le visualizzazioni di raccolta, PassKit, il Framework sociale e le modifiche apportate a StoreKit.
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: dc8f2c3a11dd283db46fd0a2530fcca435da75f9
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752045"
---
# <a name="introduction-to-ios-6"></a>Introduzione a iOS 6

_iOS 6 include un'ampia gamma di nuove tecnologie per lo sviluppo di app, che Novell. iOS C# 6 offre agli sviluppatori._

[![](images/ios6-large.jpg "Logo iOS 6")](images/ios6-large.jpg#lightbox)

Con iOS 6 e Novell. iOS 6, gli sviluppatori ora hanno a disposizione una vasta gamma di funzionalità per la creazione di applicazioni iOS, incluse quelle destinate a iPhone 5.
Questo documento elenca alcune delle nuove funzionalità più interessanti disponibili e i collegamenti agli articoli per ogni argomento. Inoltre, si riferisce a un paio di modifiche che saranno importanti quando gli sviluppatori passano a iOS 6 e la nuova risoluzione di iPhone 5.

## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[Introduzione alle visualizzazioni di raccolta](~/ios/user-interface/controls/uicollectionview.md)

Le visualizzazioni di raccolta consentono di visualizzare il contenuto utilizzando layout arbitrari. Consentono di creare facilmente layout simili a griglia, supportando al tempo stesso anche i layout personalizzati. Per ulteriori informazioni, vedere la Guida [introduttiva alle viste di raccolta](~/ios/user-interface/controls/uicollectionview.md) [](~/ios/user-interface/controls/uicollectionview.md).

## <a name="introduction-to-passkitiosplatformpasskitmd"></a>[Introduction to PassKit](~/ios/platform/passkit.md) (Introduzione a PassKit)

Il Framework PassKit consente alle applicazioni di interagire con i pass digitali gestiti nell'app Passbook. Per ulteriori informazioni, vedere la [Guida introduttiva al passaggio del kit](~/ios/platform/passkit.md).

## <a name="introduction-to-eventkitiosplatformeventkitmd"></a>[Introduzione a EventKit](~/ios/platform/eventkit.md)

EventKit Framework fornisce un modo per accedere ai calendari, agli eventi del calendario e ai promemoria dei dati archiviati nel database Calendar. L'accesso ai calendari e agli eventi di calendario è stato disponibile a partire da iOS 4, ma iOS 6 ora espone l'accesso ai dati dei promemoria. Per ulteriori informazioni, [vedere la guida](~/ios/platform/eventkit.md) [di NTRODUZIONE a EventKit](~/ios/platform/eventkit.md) .

## <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[Introduzione al Framework di social networking](~/ios/platform/social-framework.md)

Il Framework di social networking fornisce un'API unificata per interagire con i social network, inclusi Twitter e Facebook, oltre a SinaWeibo per gli utenti in Cina. Per ulteriori informazioni, vedere l' [Introduzione alla guida di social Framework](~/ios/platform/social-framework.md) .

## <a name="changes-to-storekitchanges-to-storekitmd"></a>[Modifiche di StoreKit](changes-to-storekit.md)

Apple ha introdotto due nuove funzionalità in Store Kit: acquisto e download di contenuto iTunes o App Store dall'interno dell'app e hosting dei file di contenuto per gli acquisti in-app. Per ulteriori informazioni, vedere la Guida alle [modifiche apportate al kit di archiviazione](changes-to-storekit.md) .

## <a name="other-changes"></a>Altre modifiche

### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload e ViewDidUnload deprecati

I `ViewWillUnload` metodi `ViewDidUnload` e di`UIViewController` non vengono più chiamati in iOS 6. Nelle versioni precedenti di iOS, questi metodi potrebbero essere stati usati dalle applicazioni per salvare lo stato prima di scaricare e pulire il codice, rispettivamente.

Ad esempio, Visual Studio per Mac creerebbe un metodo denominato `ReleaseDesignerOutlets`, illustrato di seguito, che verrebbe quindi chiamato da `ViewDidUnload`:

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

Tuttavia, in iOS 6 non è più necessario chiamare `ReleaseDesignerOutlets`.   

Per il codice di pulitura, le applicazioni `DidReceiveMemoryWarning`iOS 6 devono usare. Tuttavia, il codice che `Dispose` chiama deve essere usato solo per gli oggetti con utilizzo intensivo di memoria, come illustrato di seguito:

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

Anche in questo `Dispose` caso, la chiamata al livello precedente dovrebbe essere raramente necessaria. In generale, la maggior parte delle applicazioni deve rimuovere i gestori eventi.

Per il salvataggio dello stato, le applicazioni possono eseguire questa operazione `ViewWillDisappear` in `ViewDidDisappear` e anziché `ViewWillUnload`in.

### <a name="iphone-5-resolution"></a>Risoluzione di iPhone 5

i dispositivi iPhone 5 hanno una risoluzione 640X1136. Le applicazioni destinate alle versioni precedenti di iOS verranno visualizzate in formato letterbox quando vengono eseguite su un iPhone 5, come illustrato di seguito:

 [![](images/01-letterboxed.png "Le applicazioni destinate alle versioni precedenti di iOS verranno visualizzate in formato letterbox quando vengono eseguite su un iPhone 5")](images/01-letterboxed.png#lightbox)

Affinché l'applicazione venga visualizzata a schermo intero in iPhone 5, è sufficiente aggiungere un'immagine denominata `Default-568h@2x.png` con la risoluzione 640X1136. Lo screenshot seguente mostra l'applicazione in esecuzione dopo che è stata inclusa questa immagine:

 [![](images/02-fullscreen.png "Questa schermata mostra l'applicazione in esecuzione dopo che l'immagine è stata inclusa")](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>Sottoclasse UINavigationBar

In iOS 6 `UINavigationBar` può essere sottoclassato. Questo consente un controllo aggiuntivo dell'aspetto di `UINavigationBar`. Ad esempio, le applicazioni possono eseguire una sottoclasse per aggiungere visualizzazioni, animarle e modificare i limiti dell'oggetto `UINavigationBar`.

Il codice seguente mostra un esempio di una sottoclasse `UINavigationBar` che aggiunge un oggetto: `UIImageView`

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

Per `UINavigationBar` aggiungere una sottoclasse a un oggetto `UINavigationController`, usare il `UINavigationController` costruttore che accetta il tipo di `UINavigationBar` e `UIToolbar`, come illustrato di seguito:

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

L'uso `UINavigationBar` di questa sottoclasse comporta la visualizzazione della visualizzazione immagine, come illustrato nello screenshot seguente:

 [![](images/03-navbar.png "Se si usa questa sottoclasse UINavigationBar, la visualizzazione immagine viene visualizzata come illustrato in questa schermata")](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>Orientamento interfaccia

Prima di eseguire l'override `ShouldAutorotateToInterfaceOrientation`delle applicazioni iOS 6, restituire true per tutti gli orientamenti supportati dal controller specifico. Il codice seguente, ad esempio, verrebbe utilizzato per supportare solo il formato verticale:

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

In iOS 6 `ShouldAutorotateToInterfaceOrientation` è deprecato.
Le applicazioni possono invece `GetSupportedInterfaceOrientations` eseguire l'override del controller di visualizzazione radice, come illustrato di seguito:

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

In iPad, il valore predefinito è tutti e quattro gli orientamenti se `GetSupportedInterfaceOrientation` non è implementato. In iPhone e iPod touch, il valore predefinito è tutti gli orientamenti eccetto `PortraitUpsideDown`.
