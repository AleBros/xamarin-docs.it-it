---
title: Introduzione a 3D touch in Novell. iOS
description: Questo articolo descrive come usare i movimenti di tocco 3D introdotti con iPhone 6s e iPhone 6s Plus. Questi movimenti abilitano la sensibilità alla pressione, la visualizzazione e il pop e le azioni rapide.
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: b69328b00778b0c3c03cd4d954b2e0bef33e4784
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574600"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Introduzione a 3D touch in Novell. iOS

_Questo articolo illustra l'uso dei nuovi movimenti iPhone 6s e iPhone 6s Plus 3D touch nell'app._

[![](3d-touch-images/info01.png "Examples of 3D Touch enabled apps")](3d-touch-images/info01.png#lightbox)

Questo articolo fornisce e introduce l'uso delle nuove API Touch 3D per aggiungere movimenti sensibili alla pressione alle app Novell. iOS in esecuzione nei nuovi dispositivi iPhone 6s e iPhone 6s Plus.

Con il tocco 3D, un'app iPhone è ora in grado di non solo indicare che l'utente sta toccando lo schermo del dispositivo, ma è in grado di rilevare la quantità di pressione che l'utente impiega e rispondere ai diversi livelli di pressione.

3D touch offre all'app le funzionalità seguenti:

- [Sensibilità alla pressione](#Pressure-Sensitivity) : le app possono ora misurare il livello di rigidezza o di luminosità dell'utente che tocca lo schermo e sfruttare tali informazioni.
  Ad esempio, un'app di disegno può rendere una linea più spessa o più sottile a seconda della difficoltà con cui l'utente tocca lo schermo.
- [Peek e pop](#Peek-and-Pop) : l'app può ora consentire all'utente di interagire con i dati senza dover uscire dal contesto corrente. Premendo lo schermo sullo schermo, è possibile visualizzare l'elemento a cui si è interessati, ad esempio in anteprima un messaggio. Premendo più difficile, è possibile visualizzare l'elemento.
- [Azioni rapide](#Quick-Actions) : si pensi a azioni rapide come i menu contestuali che possono essere visualizzati quando un utente fa clic con il pulsante destro del mouse su un elemento in un'app desktop.
  Con azioni rapide è possibile aggiungere collegamenti alle funzioni nell'app direttamente dall'icona dell'app nella schermata iniziale.
- [Test 3D touch nel simulatore](#Testing-3D-Touch-in-the-Simulator) : con l'hardware Mac corretto, è possibile testare le app abilitate per il tocco 3D nel simulatore iOS.

<a name="Pressure-Sensitivity"></a>

## <a name="pressure-sensitivity"></a>Sensibilità alla pressione

Come indicato in precedenza, usando le nuove proprietà della classe [UITouch](xref:UIKit.UITouch) è possibile misurare la quantità di pressione applicata dall'utente alla schermata del dispositivo iOS e usare queste informazioni nell'interfaccia utente. Ad esempio, rendendo un tratto di pennello più trasparente o opaco in base alla quantità di pressione.

[![](3d-touch-images/pressure01.png "A brush stroke rendered as more translucent or opaque based on the amount of pressure")](3d-touch-images/pressure01.png#lightbox)

In seguito a 3D touch, se l'app è in esecuzione in iOS 9 (o versione successiva) e il dispositivo iOS è in grado di supportare il tocco 3D, le variazioni di pressione comporteranno la `TouchesMoved` generazione dell'evento.

Ad esempio, quando si monitora l' `TouchesMoved` evento di un [UIView](xref:UIKit.UIView), è possibile usare il codice seguente per ottenere la pressione corrente che l'utente sta applicando allo schermo:

```csharp
public override void TouchesMoved (NSSet touches, UIEvent evt)
{
    base.TouchesMoved (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        // Get the pressure
        var force = touch.Force;
        var maxForce = touch.MaximumPossibleForce;

        // Do something with the touch and the pressure
        ...
    }
}
```

La `MaximumPossibleForce` proprietà restituisce il valore massimo possibile per la `Force` proprietà di [UITouch](xref:UIKit.UITouch) in base al dispositivo iOS su cui è in esecuzione l'app.

> [!IMPORTANT]
> Le modifiche di pressione provocheranno la `TouchesMoved` generazione dell'evento, anche se le coordinate X/Y non sono state modificate. A causa di questa modifica del comportamento, le app iOS devono essere preparate affinché l' `TouchesMoved` evento venga richiamato più spesso e che le coordinate X/Y siano uguali a quelle dell'ultima `TouchesMoved` chiamata.

Per altre informazioni, vedere TouchCanvas di Apple [: uso di UITouch in modo efficiente ed efficace](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/) esempio di app e [riferimento alla classe UITouch](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/).

<a name="Peek-and-Pop"></a>

## <a name="peek-and-pop"></a>Visualizzazione e pop

3D touch offre nuovi modi per consentire a un utente di interagire con le informazioni all'interno dell'app più rapidamente che mai, senza dover passare dalla posizione corrente.

Ad esempio, se l'app visualizza una tabella di messaggi, l'utente può premere su un elemento per visualizzare un'anteprima del contenuto in una visualizzazione sovrapposta (Apple si riferisce a una *sbirciatina*).

[![](3d-touch-images/peekandpop01.png "An example of Peeking at content")](3d-touch-images/peekandpop01.png#lightbox)

Se l'utente preme più difficile, entrerà nella visualizzazione normale del messaggio, che viene definita *pop*-ping nella visualizzazione.

### <a name="checking-for-3d-touch-availability"></a>Verifica della disponibilità del tocco 3D

Quando si lavora con un `UIViewController` , è possibile usare il codice seguente per verificare se il dispositivo iOS in cui è in esecuzione l'app supporta il tocco 3D:

```csharp
public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
{
    //Important: call the base function
    base.TraitCollectionDidChange(previousTraitCollection);

    //See if the new TraitCollection value includes force touch
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        //Do something with 3D touch, for instance...
        RegisterForPreviewingWithDelegate (this, View);
        ...
```

Questo metodo può essere chiamato prima *o dopo* `ViewDidLoad()` .

### <a name="handling-peek-and-pop"></a>Gestione di Peek e pop

In un dispositivo iOS in grado di gestire il tocco 3D, è possibile usare un'istanza della `UIViewControllerPreviewingDelegate` classe per gestire la visualizzazione **Peek** dei dettagli dell'elemento di visualizzazione e **pop** . Se, ad esempio, si dispone di un controller di visualizzazione tabella denominato, `MasterViewController` è possibile utilizzare il codice seguente per supportare la **visualizzazione** e il **pop**:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace DTouch
{
    public class PreviewingDelegate : UIViewControllerPreviewingDelegate
    {
        #region Computed Properties
        public MasterViewController MasterController { get; set; }
        #endregion

        #region Constructors
        public PreviewingDelegate (MasterViewController masterController)
        {
            // Initialize
            this.MasterController = masterController;
        }

        public PreviewingDelegate (NSObjectFlag t) : base(t)
        {
        }

        public PreviewingDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        /// Present the view controller for the "Pop" action.
        public override void CommitViewController (IUIViewControllerPreviewing previewingContext, UIViewController viewControllerToCommit)
        {
            // Reuse Peek view controller for details presentation
            MasterController.ShowViewController(viewControllerToCommit,this);
        }

        /// Create a previewing view controller to be shown at "Peek".
        public override UIViewController GetViewControllerForPreview (IUIViewControllerPreviewing previewingContext, CGPoint location)
        {
            // Grab the item to preview
            var indexPath = MasterController.TableView.IndexPathForRowAtPoint (location);
            var cell = MasterController.TableView.CellAt (indexPath);
            var item = MasterController.dataSource.Objects [indexPath.Row];

            // Grab a controller and set it to the default sizes
            var detailViewController = MasterController.Storyboard.InstantiateViewController ("DetailViewController") as DetailViewController;
            detailViewController.PreferredContentSize = new CGSize (0, 0);

            // Set the data for the display
            detailViewController.SetDetailItem (item);
            detailViewController.NavigationItem.LeftBarButtonItem = MasterController.SplitViewController.DisplayModeButtonItem;
            detailViewController.NavigationItem.LeftItemsSupplementBackButton = true;

            // Set the source rect to the cell frame, so everything else is blurred.
            previewingContext.SourceRect = cell.Frame;

            return detailViewController;
        }
        #endregion
    }
}
```

Il `GetViewControllerForPreview` metodo viene utilizzato per eseguire l'operazione di **visualizzazione** . Ottiene l'accesso alla cella della tabella e al backup dei dati, quindi carica l'oggetto `DetailViewController` dallo storyboard corrente. Impostando `PreferredContentSize` su (0, 0) viene richiesta la dimensione della visualizzazione di **Peek** visualizzazione predefinita. Infine, viene offuscato tutto tranne la cella visualizzata con `previewingContext.SourceRect = cell.Frame` e viene restituita la nuova visualizzazione per la visualizzazione.

Il `CommitViewController` Riutilizza la visualizzazione creata nella visualizzazione per **Peek** la visualizzazione **pop** quando l'utente preme più difficile.

### <a name="registering-for-peek-and-pop"></a>Registrazione per PEEK e pop

Dal controller di visualizzazione per cui si vuole consentire all'utente di **leggere** e **estrarre** elementi, è necessario registrarsi per questo servizio. Nell'esempio riportato sopra di un controller di visualizzazione tabella ( `MasterViewController` ), si utilizzerà il codice seguente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Check to see if 3D Touch is available
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        // Register for Peek and Pop
        RegisterForPreviewingWithDelegate(new PreviewingDelegate(this), View);
    }
    ...

}
```

Qui viene chiamato il `RegisterForPreviewingWithDelegate` metodo con un'istanza del creato in `PreviewingDelegate` precedenza. Nei dispositivi iOS che supportano il tocco 3D, l'utente può premere su un elemento per visualizzarlo. Se la pressione è ancora più complessa, l'elemento verrà visualizzato nella visualizzazione standard.

Per altre informazioni, vedere l' [esempio ApplicationShortcuts di iOS 9](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview) e [l'ViewControllerPreviews di Apple: uso dell'](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) app di esempio delle API di anteprima di UIViewController, [riferimento alla classe UIPreviewAction](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/), riferimento alla [classe UIPreviewActionGroup](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/) e informazioni di [riferimento sul protocollo UIPreviewActionItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/).

<a name="Quick-Actions"></a>

## <a name="quick-actions"></a>Azioni rapide

Usando il tocco 3D e le azioni rapide, è possibile aggiungere collegamenti comuni, rapidi e facili da accedere alle funzioni nell'app dall'icona della schermata iniziale sul dispositivo iOS.

Come indicato in precedenza, è possibile pensare a azioni rapide come i menu contestuali che possono essere visualizzati quando un utente fa clic con il pulsante destro del mouse su un elemento in un'app desktop. È consigliabile usare azioni rapide per fornire collegamenti alle funzioni o alle funzionalità più comuni dell'app.

[![](3d-touch-images/quickactions01.png "An example of a Quick Actions menu")](3d-touch-images/quickactions01.png#lightbox)

### <a name="defining-static-quick-actions"></a>Definizione di azioni rapide statiche

Se una o più azioni rapide richieste dall'app sono statiche e non è necessario modificarle, è possibile definirle nel file dell'app `Info.plist` . Modificare questo file in un editor esterno e aggiungere le chiavi seguenti:

```xml
<key>UIApplicationShortcutItems</key>
<array>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeSearch</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will search for an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Search</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.000</string>
    </dict>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeShare</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will share an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Share</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.001</string>
    </dict>
</array>
```

Qui vengono definiti due elementi di azione rapida statici con le chiavi seguenti:

- `UIApplicationShortcutItemIconType`: Definisce l'icona che verrà visualizzata dall'elemento di azione rapida come uno dei valori seguenti:
  - `UIApplicationShortcutIconTypeAdd`
  - `UIApplicationShortcutIconTypeAlarm`
  - `UIApplicationShortcutIconTypeAudio`
  - `UIApplicationShortcutIconTypeBookmark`
  - `UIApplicationShortcutIconTypeCapturePhoto`
  - `UIApplicationShortcutIconTypeCaptureVideo`
  - `UIApplicationShortcutIconTypeCloud`
  - `UIApplicationShortcutIconTypeCompose`
  - `UIApplicationShortcutIconTypeConfirmation`
  - `UIApplicationShortcutIconTypeContact`
  - `UIApplicationShortcutIconTypeDate`
  - `UIApplicationShortcutIconTypeFavorite`
  - `UIApplicationShortcutIconTypeHome`
  - `UIApplicationShortcutIconTypeInvitation`
  - `UIApplicationShortcutIconTypeLocation`
  - `UIApplicationShortcutIconTypeLove`
  - `UIApplicationShortcutIconTypeMail`
  - `UIApplicationShortcutIconTypeMarkLocation`
  - `UIApplicationShortcutIconTypeMessage`
  - `UIApplicationShortcutIconTypePause`
  - `UIApplicationShortcutIconTypePlay`
  - `UIApplicationShortcutIconTypeProhibit`
  - `UIApplicationShortcutIconTypeSearch`
  - `UIApplicationShortcutIconTypeShare`
  - `UIApplicationShortcutIconTypeShuffle`
  - `UIApplicationShortcutIconTypeTask`
  - `UIApplicationShortcutIconTypeTaskCompleted`
  - `UIApplicationShortcutIconTypeTime`
  - `UIApplicationShortcutIconTypeUpdate`

  ![](3d-touch-images/uiapplicationshortcuticontype.png "UIApplicationShortcutIconType imagery")

- `UIApplicationShortcutItemSubtitle`: Definisce il sottotitolo dell'elemento.
- `UIApplicationShortcutItemTitle`: Definisce il titolo dell'elemento.
- `UIApplicationShortcutItemType`-Valore stringa che verrà usato per identificare l'elemento nell'app. Per altre informazioni, vedere la sezione successiva.

> [!IMPORTANT]
> Non è possibile accedere agli elementi rapidi dell'azione rapida impostati nel `Info.plist` file con la `Application.ShortcutItems` Proprietà. Vengono passati solo al `HandleShortcutItem` gestore eventi.

### <a name="identifying-quick-action-items"></a>Identificazione degli elementi di azione rapida

Come illustrato in precedenza, quando sono stati definiti gli elementi di azione rapida nell'app `Info.plist` , è stato assegnato un valore stringa alla `UIApplicationShortcutItemType` chiave per identificarli.

Per semplificare l'uso degli identificatori nel codice, aggiungere una classe denominata `ShortcutIdentifier` al progetto dell'app e renderla simile alla seguente:

```csharp
using System;

namespace AppSearch
{
    public static class ShortcutIdentifier
    {
        public const string First = "com.company.appname.000";
        public const string Second = "com.company.appname.001";
        public const string Third = "com.company.appname.002";
        public const string Fourth = "com.company.appname.003";
    }
}
```

<a name="Handling-a-Quick-Action"></a>

### <a name="handling-a-quick-action"></a>Gestione di un'azione rapida

Successivamente, è necessario modificare il file dell'app `AppDelegate.cs` per gestire l'utente selezionando una voce di azione rapida dall'icona dell'app nella schermata iniziale.

Apportare le modifiche seguenti:

```csharp
using System;
...

public UIApplicationShortcutItem LaunchedShortcutItem { get; set; }

public bool HandleShortcutItem(UIApplicationShortcutItem shortcutItem) {
    var handled = false;

    // Anything to process?
    if (shortcutItem == null) return false;

    // Take action based on the shortcut type
    switch (shortcutItem.Type) {
    case ShortcutIdentifier.First:
        Console.WriteLine ("First shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Second:
        Console.WriteLine ("Second shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Third:
        Console.WriteLine ("Third shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Fourth:
        Console.WriteLine ("Forth shortcut selected");
        handled = true;
        break;
    }

    // Return results
    return handled;
}

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    return shouldPerformAdditionalDelegateHandling;
}

public override void OnActivated (UIApplication application)
{
    // Handle any shortcut item being selected
    HandleShortcutItem(LaunchedShortcutItem);

    // Clear shortcut after it's been handled
    LaunchedShortcutItem = null;
}

public override void PerformActionForShortcutItem (UIApplication application, UIApplicationShortcutItem shortcutItem, UIOperationHandler completionHandler)
{
    // Perform action
    completionHandler(HandleShortcutItem(shortcutItem));
}
```

In primo luogo, si definisce una `LaunchedShortcutItem` proprietà pubblica per tenere traccia dell'ultimo elemento di azione rapida selezionato da parte dell'utente. Quindi, si esegue l'override del `FinishedLaunching` metodo e si verifica se `launchOptions` sono stati passati e se contengono un elemento di azione rapida. In caso affermativo, l'azione rapida viene archiviata nella `LaunchedShortcutItem` Proprietà.

Viene quindi eseguito l'override del `OnActivated` metodo e viene passato qualsiasi elemento di avvio veloce selezionato al `HandleShortcutItem` metodo su cui agire. Attualmente viene scritto solo un messaggio nella **console**. In un'app reale si gestirebbe l'azione necessaria. Una volta eseguita l'azione, la `LaunchedShortcutItem` proprietà viene cancellata.

Infine, se l'app è già in esecuzione, viene `PerformActionForShortcutItem` chiamato il metodo per gestire l'elemento di azione rapida, quindi è necessario eseguirne l'override e chiamare `HandleShortcutItem` anche il metodo.

### <a name="creating-dynamic-quick-action-items"></a>Creazione di elementi di azione rapida dinamici

Oltre a definire gli elementi di azione rapida statici nel file dell'app `Info.plist` , è possibile creare azioni rapide dinamiche in tempo reale. Per definire due nuove azioni rapide dinamiche, modificare `AppDelegate.cs` di nuovo il file e modificare il `FinishedLaunching` metodo in modo che abbia un aspetto simile al seguente:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    // Add dynamic shortcut items
    if (application.ShortcutItems.Length == 0) {
        var shortcut3 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Third, "Play") {
            LocalizedSubtitle = "Will play an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Play)
        };

        var shortcut4 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Fourth, "Pause") {
            LocalizedSubtitle = "Will pause an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Pause)
        };

        // Update the application providing the initial 'dynamic' shortcut items.
        application.ShortcutItems = new UIApplicationShortcutItem[]{shortcut3, shortcut4};
    }

    return shouldPerformAdditionalDelegateHandling;
}
```

A questo punto viene verificato se l'oggetto `application` contiene già un set di oggetti creati in modo dinamico `ShortcutItems` , in caso contrario verranno creati due nuovi `UIMutableApplicationShortcutItem` oggetti per definire i nuovi elementi e aggiungerli alla `ShortcutItems` matrice.

Il codice già aggiunto nella sezione [gestione di un'azione rapida](#Handling-a-Quick-Action) precedente gestirà queste azioni rapide dinamiche esattamente come quelle statiche.

Si noti che è possibile creare una combinazione di elementi di azione rapida statica e dinamica (come in questo caso), non si è limitati a uno o l'altro.

Per altre informazioni, vedere l' [esempio di ViewControllerPreview per iOS 9](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview) e vedere [ApplicationShortcuts di Apple: uso](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/) dell'app di esempio UIApplicationShortcutItem, [riferimento alla classe UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/), riferimento alla classe [UIMutableApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/) e [riferimento alla classe UIApplicationShortcutIcon](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/).

<a name="Testing-3D-Touch-in-the-Simulator"></a>

## <a name="testing-3d-touch-in-the-simulator"></a>Test 3D touch nel simulatore

Quando si usa la versione più recente di Xcode e il simulatore iOS in un Mac compatibile con un Force Touch abilitare trackpad, è possibile testare la funzionalità di tocco 3D in simulatore.

Per abilitare questa funzionalità, eseguire qualsiasi app in hardware iPhone simulato che supporta 3D touch (iPhone 6s e versioni successive). Successivamente, selezionare il menu **hardware** nel simulatore iOS e abilitare la voce **di menu use trackpad Force for 3D Touch** :

[![](3d-touch-images/simulator01.png "Select the Hardware menu in the iOS Simulator and enable the Use Trackpad Force for 3D touch menu item")](3d-touch-images/simulator01.png#lightbox)

Grazie a questa funzionalità, è possibile premere più difficile sul trackpad del Mac per abilitare il tocco 3D esattamente come si farebbe con l'hardware di iPhone reale.

## <a name="summary"></a>Summary

Questo articolo ha introdotto le nuove API 3D Touch rese disponibili in iOS 9 per iPhone 6s e iPhone 6s Plus. Si è trattato di aggiungere la sensibilità di pressione a un'app; uso di Peek e pop per visualizzare rapidamente le informazioni in-app dal contesto corrente senza navigazione; e con azioni rapide per fornire collegamenti alle funzionalità di uso più comune dell'app.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di ViewControllerPreview per iOS 9](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview)
- [Esempio di ApplicationShortcuts per iOS 9](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-applicationshortcuts)
- [iOS 9 per sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Prepara le tue app iPhone per il tocco 3D](https://developer.apple.com/ios/3d-touch/)
