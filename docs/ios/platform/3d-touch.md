---
title: Introduzione alla funzionalità Touch 3D in xamarin. IOS
description: Questo articolo descrive come usare i movimenti di tocco 3D introdotti con l'iPhone 6s e iPhone 6s Plus. Questi gesti abilitare il rilevamento della pressione, visualizzazione e pop e azioni rapide.
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 08edd3166df8392a5f3e7485a6572f0c94a38f62
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563656"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Introduzione alla funzionalità Touch 3D in xamarin. IOS

_Questo articolo illustra l'uso di nuovo iPhone 6s e iPhone 6s Plus 3D Touch movimenti nell'app._

[![](3d-touch-images/info01.png "Esempi di funzionalità Touch 3D abilitate le app")](3d-touch-images/info01.png#lightbox)

Questo articolo fornisce e Introduzione all'uso di nuove API Touch 3D per aggiungere i movimenti di pressione sensibili per le app xamarin. IOS in esecuzione nel nuovo iPhone 6s e iPhone 6s Plus dispositivi.

Con funzionalità Touch 3D, un'app per iPhone è ora in grado non solo indicano che l'utente tocca lo schermo del dispositivo, ma è in grado di valutare la pressione è ovvero stanno inviando all'utente di rilevare e rispondere ai livelli di pressione diversi.

3D Touch offre le funzionalità seguenti per l'app:

- [Pressione sensibilità](#Pressure-Sensitivity) : le app ora è possono misurare la difficoltà o chiaro l'utente tocca la schermata e Ottieni i vantaggi di tali informazioni.
  Ad esempio, un'app di disegno può effettuare una linea spessa o sottili base difficile l'utente tocca la schermata.
- [Visualizza e Pop](#Peek-and-Pop) -l'app può ora consentire all'utente di interagire con i relativi dati senza la necessità di uscire dal relativo contesto corrente. Premendo rigido sullo schermo lo schermo, si può leggere l'elemento di che cui sono interessati (ad esempio, l'anteprima di un messaggio). Premendo più difficile, è possibile prelevare nell'elemento.
- [Azioni rapide](#Quick-Actions) -pensare di azioni rapide, ad esempio il menu di scelta rapida che può essere estratto verticale quando l'utente fa clic su un elemento in un'app desktop.
  Con azioni rapide, è possibile aggiungere collegamenti alle funzioni nell'app direttamente dall'icona dell'app nella schermata iniziale.
- [Test di funzionalità Touch 3D nel simulatore](#Testing-3D-Touch-in-the-Simulator) -con l'hardware Mac corretto, è possibile testare 3D Touch abilitato app nel simulatore iOS.

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>Rilevamento della pressione

Come indicato in precedenza, con nuove proprietà del [UITouch](https://developer.xamarin.com/api/type/UIKit.UITouch/) classe è possibile misurare la quantità di pressione che l'utente applica allo schermo del dispositivo iOS e usare queste informazioni nell'interfaccia utente. Ad esempio, effettua una Pennellata più opaco o trasparente in base alla quantità di pressione.

[![](3d-touch-images/pressure01.png "Un pennello sottoposto a rendering come più opaco o trasparente in base alla quantità di pressione")](3d-touch-images/pressure01.png#lightbox)

In seguito a 3D Touch, se l'esecuzione dell'app in iOS 9 (o versione successiva) e il dispositivo iOS è in grado di supporto 3D Touch, le modifiche in un utilizzo elevato causa il `TouchesMoved` generazione dell'evento.

Ad esempio, quando viene monitorata il `TouchesMoved` eventi di un [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/), è possibile usare il codice seguente per ottenere la pressione corrente che l'utente applica allo schermo:

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

Il `MaximumPossibleForce` proprietà restituisce il valore massimo consentito per il `Force` proprietà del [UITouch](https://developer.xamarin.com/api/type/UIKit.UITouch/) in base al dispositivo iOS che viene eseguita l'app.

> [!IMPORTANT]
> Causano modifiche in un utilizzo elevato di `TouchesMoved` dell'evento, anche se X / Y coordinate non sono stati modificati. Grazie a questa modifica nel comportamento, le app iOS devono essere preparate per il `TouchesMoved` evento da richiamare più spesso e per X / valore delle coordinate Y sia uguale all'ultimo `TouchesMoved` chiamare.




Per altre informazioni, vedere di Apple [TouchCanvas: utilizzando UITouch efficacemente](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/) app di esempio e [riferimento alla classe UITouch](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/).

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>Anteprima e Pop

3D Touch offre nuove modalità per un utente di interagire con le informazioni all'interno dell'app più veloce che mai, senza la necessità di passare dalla posizione corrente.

Ad esempio, se l'app viene visualizzata una tabella dei messaggi, l'utente può premere disco rigido su un elemento per visualizzare in anteprima il contenuto in una visualizzazione di sovrimpressione (che Apple fa riferimento a come un *Peek*).

[![](3d-touch-images/peekandpop01.png "Un esempio di visualizzazione contenuto")](3d-touch-images/peekandpop01.png#lightbox)

Se l'utente preme più difficile, dovrà immettere la vista messaggio normale (che è detta *Pop*-ping nella visualizzazione).

### <a name="checking-for-3d-touch-availability"></a>Controllo per la disponibilità di funzionalità Touch 3D

Quando si lavora con un [UIViewController]() è possibile usare il codice seguente per verificare se il dispositivo iOS viene eseguita l'app supporta funzionalità Touch 3D:

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

Questo metodo può essere chiamato prima *o successiva* `ViewDidLoad()`. 

### <a name="handling-peek-and-pop"></a>Visualizza la gestione e POP

In un dispositivo iOS in grado di gestire funzionalità Touch 3D, è possibile usare un'istanza del `UIViewControllerPreviewingDelegate` classe per gestire la visualizzazione dei **Peek** e **Pop** dettagli elemento. Ad esempio, se avessimo un Controller visualizzazione tabella denominata `MasterViewController ` è possibile usare il codice seguente per supportare **Peek** e **Pop**:

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

Il `GetViewControllerForPreview` metodo viene usato per eseguire il **Peek** operazione. Riesce ad accedere ai dati di backup e una cella della tabella e quindi carica il `DetailViewController` dallo Storyboard corrente. Impostando il `PreferredContentSize` da (0,0), chiediamo per impostazione predefinita **Peek** dimensioni vista. Infine, abbiamo blur tutti gli elementi tranne la cella viene visualizzato con `previewingContext.SourceRect = cell.Frame` e restituiamo la nuova visualizzazione per la visualizzazione.

Il `CommitViewController` riutilizza la vista è stato creato nel **Peek** per il **Pop** visualizzare quando l'utente preme più difficile.

### <a name="registering-for-peek-and-pop"></a>La registrazione per la visualizzazione e Pop

Dal Controller di visualizzazione che si vuole consentire all'utente **Peek** e **Pop** elementi da, è necessario effettuare la registrazione per questo servizio. Nell'esempio precedente di un Controller visualizzazione tabella (`MasterViewController`), utilizzeremmo il codice seguente:

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

Qui di seguito viene chiamato il `RegisterForPreviewingWithDelegate` metodo con un'istanza del `PreviewingDelegate` creato in precedenza. Nei dispositivi iOS che supportano 3D Touch, l'utente può premere disco rigido su un elemento visualizzazione rapida analizzarlo. Se gli utenti premono ancora più difficile, l'elemento viene visualizzata in esso standard visualizzazione.

Per altre informazioni, vedere nostri [iOS 9 esempio ApplicationShortcuts](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) e di Apple [ViewControllerPreviews: usando il UIViewController anteprima API](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) app di esempio, [ Riferimento alla classe UIPreviewAction](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/), [riferimento alla classe UIPreviewActionGroup](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/) e [riferimento al protocollo UIPreviewActionItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/).

<a name="Quick-Actions" />

## <a name="quick-actions"></a>Azioni rapide

Usa 3D Touch e azioni rapide, è possibile aggiungere più comuni, rapido e semplice ai collegamenti di accesso alle funzioni nell'app dall'icona della schermata Home nel dispositivo iOS.

Come indicato in precedenza, è possibile considerare una delle azioni rapide, ad esempio il menu di scelta rapida che può essere estratto verticale quando l'utente fa clic su un elemento in un'app desktop. È necessario usare azioni rapide per fornire i tasti di scelta rapida per le funzioni o le funzionalità dell'app più comuni.

[![](3d-touch-images/quickactions01.png "Un esempio di un menu Azioni rapide")](3d-touch-images/quickactions01.png#lightbox)


### <a name="defining-static-quick-actions"></a>Definizione delle azioni rapide statiche

Se uno o più delle azioni rapide necessarie per l'app sono statici e non è necessario cambiare, è possibile definirli dell'app `Info.plist` file. Modificare il file in un editor esterno, aggiungere le chiavi seguenti:

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

Di seguito viene definita due elementi di azione rapida statici con le chiavi seguenti:

- `UIApplicationShortcutItemIconType` -Definisce l'icona che verrà visualizzato dall'elemento azione rapida come uno dei valori seguenti:
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

* `UIApplicationShortcutItemSubtitle` -Definisce il sottotitolo per l'elemento.
* `UIApplicationShortcutItemTitle` -Definisce il titolo dell'elemento.
* `UIApplicationShortcutItemType` -È un valore stringa che verrà usato per identificare l'elemento nell'app. Per altre informazioni, vedere la sezione successiva.

> [!IMPORTANT]
> Rapido azione di scelta rapida che viene impostate nel `Info.plist` non è possibile accedere al file con il `Application.ShortcutItems` proprietà. Vengono passati solo al `HandleShortcutItem` gestore dell'evento. 





### <a name="identifying-quick-action-items"></a>Identificare le azioni rapide

Come osservato in precedenza, quando è definito gli elementi di azione rapida dell'app `Info.plist`, è assegnato un valore stringa per il `UIApplicationShortcutItemType` chiave per identificarli.

Per rendere più facile da usare nel codice di tali identificatori, aggiungere una classe denominata `ShortcutIdentifier` all'app di progetto e renderlo simile al seguente:

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

<a name="Handling-a-Quick-Action" />

### <a name="handling-a-quick-action"></a>La gestione di un'azione rapida

Successivamente, è necessario modificare l'app `AppDelegate.cs` file per gestire l'utente la selezione di un elemento azione rapida dall'icona dell'app nella schermata iniziale.

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

In primo luogo, definiamo un pubblico `LaunchedShortcutItem` proprietà da rilevare l'ultimo elemento azione rapida selezionato dall'utente. Quindi, viene eseguito l'override di `FinishedLaunching` metodo e vedere se `launchOptions` sono stati passati e, se si include una voce di azione rapida. In questo caso, archiviamo l'azione rapida nel `LaunchedShortcutItem` proprietà.

Successivamente, viene eseguito l'override di `OnActivated` ereditato e selezionare qualsiasi elemento di avvio veloce per passare il `HandleShortcutItem` metodo agire su. Attualmente, viene scritta solo un messaggio per il **Console**. In una vera app, è necessario gestire più azione era necessario. Dopo che l'azione è stata portata, la `LaunchedShortcutItem` proprietà è deselezionata.

Infine, se l'app era già in esecuzione, il `PerformActionForShortcutItem` metodo viene chiamato per gestire l'elemento azione rapida, quindi è necessario eseguirne l'override e chiamare il `HandleShortcutItem` metodo anche qui.


### <a name="creating-dynamic-quick-action-items"></a>La creazione di azioni rapide dinamica

Oltre a definire l'azione rapida statica di elementi dell'app `Info.plist` file, è possibile creare dinamica su immediatamente azioni rapide. Per definire due azioni rapide dinamici nuovi, modificare il `AppDelegate.cs` nuovamente file e modificare il `FinishedLaunching` metodo aspetto analogo al seguente:

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

A questo punto è in corso un controllo per verificare se il `application` contiene già un set di creati dinamicamente `ShortcutItems`, in caso contrario verranno creati due nuovi `UIMutableApplicationShortcutItem` oggetti per definire i nuovi elementi e aggiungerli al `ShortcutItems` matrice.

Il codice è già stato aggiunto nel [la gestione di un'azione rapida](#Handling-a-Quick-Action) precedente sezione gestirà queste azioni rapide dinamica come quelli statici.

Si noti che è possibile creare una combinazione di elementi di azione rapida statiche e dinamiche (come che stiamo facendo qui), non si è limitati a uno o l'altro.

Per altre informazioni, vedere la [iOS 9 esempio ViewControllerPreview](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) e vedere Apple [ApplicationShortcuts: UIApplicationShortcutItem usando](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/) app di esempio, [ Riferimento alla classe UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/), [riferimento alla classe UIMutableApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/) e [riferimento alla classe UIApplicationShortcutIcon](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/).

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>Test di funzionalità Touch 3D nel simulatore

Quando tramite la versione più recente di Xcode e al simulatore iOS su un Mac compatibile con una forza Touch Abilita trackpad, è possibile testare le funzionalità Touch 3D nel simulatore.

Per abilitare questa funzionalità, eseguire qualsiasi app iPhone simulato su hardware che supporta funzionalità Touch 3D (iPhone 6s e versioni successive). Selezionare quindi il **Hardware** dal menu in iOS Simulator e abilitare il **forza Trackpad usare 3D touch** voce di menu:

[![](3d-touch-images/simulator01.png "Selezionare il menu di scelta dell'Hardware nel simulatore iOS e abilitare la forza di Trackpad usare 3D touch voce di menu")](3d-touch-images/simulator01.png#lightbox)

Con questa funzionalità è attiva, è possibile premere più difficile in trackpad del Mac per abilitare funzionalità Touch 3D come si farebbe in hardware reale iPhone.

## <a name="summary"></a>Riepilogo

Questo articolo è state introdotte nuove 3D Touch API reso disponibile in iOS 9 per iPhone 6s e iPhone 6s Plus. Sia nascosto pressione aggiunta a un'app. utilizzo di Peek e Pop per visualizzare rapidamente informazioni in-app nel contesto corrente senza la navigazione. e con azioni rapide per fornire i tasti di scelta rapida per l'app del più comunemente utilizzato le funzionalità.



## <a name="related-links"></a>Collegamenti correlati

- [iOS 9 ViewControllerPreview esempio](https://developer.xamarin.com/samples/monotouch/ios9/ViewControllerPreview/)
- [iOS 9 ApplicationShortcuts esempio](https://developer.xamarin.com/samples/monotouch/ios9/ApplicationShortcuts/)
- [per gli sviluppatori iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Preparare le app iPhone per funzionalità Touch 3D](https://developer.apple.com/ios/3d-touch/)
