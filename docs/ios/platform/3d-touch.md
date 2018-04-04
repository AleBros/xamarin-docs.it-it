---
title: Introduzione a 3D Touch
description: In questo articolo viene illustrato l'utilizzo di nuovo i movimenti iPhone 6s e iPhone 6s Plus 3D Touch nell'app.
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a0d89315b82f4931538cdabe64aade7986b2a42e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-3d-touch"></a>Introduzione a 3D Touch

_In questo articolo viene illustrato l'utilizzo di nuovo i movimenti iPhone 6s e iPhone 6s Plus 3D Touch nell'app._

[![](3d-touch-images/info01.png "Esempi di 3D Touch abilitato App")](3d-touch-images/info01.png#lightbox)

Questo articolo vengono forniti e Introduzione a utilizzando le nuove API 3D Touch per aggiungere i movimenti sensibili pressione App xamarin in esecuzione nel nuovo iPhone 6s e iPhone 6s Plus dispositivi.

Con 3D Touch è ora in grado non solo indicare che l'utente tocca dello schermo del dispositivo, ma è in grado di rilevare la quantità pressione è esercitando l'utente e rispondere ai livelli diversi di pressione un'app iPhone.

3D Touch offre le seguenti funzionalità all'App:

- [Utilizzo di sensibilità](#Pressure-Sensitivity) - App a questo punto è possono misurare la difficoltà o chiaro l'utente tocca il vantaggio dello schermo e intraprendere delle informazioni.
  Ad esempio, un'applicazione di disegno può rendere una linea spessa o sottili in base a come disco rigido l'utente tocca lo schermo.
- [Visualizza e Pop](#Peek-and-Pop) -l'app può ora consentire all'utente di interagire con i relativi dati senza dover passare fuori il contesto corrente. Premendo rigido nella schermata di, si può leggere l'elemento a che cui sono interessati (ad esempio la visualizzazione in anteprima un messaggio). Premendo più difficile, è possibile prelevare l'articolo.
- [Azioni rapide](#Quick-Actions) -pensare di azioni rapide come menu di scelta rapida che può essere estratto verticale quando l'utente fa clic su un elemento in un'applicazione desktop.
  Con azioni rapide, è possibile aggiungere collegamenti alle funzioni nell'app direttamente dall'icona dell'app dalla schermata iniziale.
- [Test 3D Touch nel simulatore](#Testing-3D-Touch-in-the-Simulator) -con l'hardware Mac corretto, è possibile testare 3D Touch abilitato app nel simulatore iOS.

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>Pressione

Come descritto in precedenza, con nuove proprietà del [UITouch](https://developer.xamarin.com/api/type/UIKit.UITouch/) classe è possibile misurare la quantità di richieste utente applica allo schermo del dispositivo iOS e usare queste informazioni nell'interfaccia utente. Ad esempio, rendendo un tratto di pennello più opaco o trasparente in base alla quantità di utilizzo elevato di.

[![](3d-touch-images/pressure01.png "In base alla quantità di utilizzo elevato di un tratto di pennello sottoposto a rendering come più opaco o trasparente")](3d-touch-images/pressure01.png#lightbox)

In seguito al tocco 3D, se l'esecuzione dell'app in iOS 9 (o versione successiva) e il dispositivo iOS è in grado di supporto 3D Touch, le modifiche in congestione causeranno la `TouchesMoved` dell'evento.

Ad esempio, durante il monitoraggio di `TouchesMoved` evento di un [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/), è possibile utilizzare il codice seguente per ottenere la pressione corrente che l'utente applica allo schermo:

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

Il `MaximumPossibleForce` proprietà restituisce il valore massimo possibile per il `Force` proprietà del [UITouch](https://developer.xamarin.com/api/type/UIKit.UITouch/) in base al dispositivo iOS che è in esecuzione l'app.

> [!IMPORTANT]
> Le modifiche in pressione causeranno la `TouchesMoved` evento da generare, anche se X / coordinate Y non sono stati modificati. A causa di questa modifica nel comportamento, le app iOS devono essere preparate per il `TouchesMoved` evento da richiamare più spesso e per X / Y coordinate deve per corrispondere all'ultimo `TouchesMoved` chiamare.




Per ulteriori informazioni, vedere Apple [TouchCanvas: utilizzando UITouch efficiente ed efficace](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/) app di esempio e [riferimento alla classe UITouch](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/).

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>Peek e Pop

3D Touch offre nuovi modi per un utente di interagire con le informazioni all'interno dell'app più rapidamente che mai, senza dover passare dalla posizione corrente.

Ad esempio, se l'app viene visualizzata una tabella dei messaggi, l'utente può premere sul disco rigido su un elemento per visualizzare in anteprima il contenuto in una vista di sovrapposizione (che si intende Apple come un *Visualizza*).

[![](3d-touch-images/peekandpop01.png "Un esempio di visualizzazione contenuto")](3d-touch-images/peekandpop01.png#lightbox)

Se l'utente preme il tasto più difficile, dovrà immettere la visualizzazione del messaggio normale (definito come *Pop*-ping nella visualizzazione).

### <a name="checking-for-3d-touch-availability"></a>Verifica della disponibilità 3D Touch

Quando si lavora con un [UIViewController]() è possibile utilizzare il codice seguente per vedere se il dispositivo iOS è in esecuzione l'app supporta 3D Touch:

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

Questo metodo può essere chiamato prima *o dopo* `ViewDidLoad()`. 

### <a name="handling-peek-and-pop"></a>Anteprima di gestione e POP

In un dispositivo iOS in grado di gestire 3D Touch, è possibile utilizzare un'istanza di `UIViewControllerPreviewingDelegate` classe per gestire la visualizzazione di **Visualizza** e **Pop** elemento dettagli. Ad esempio, se si è verificato un Controller di visualizzazione tabella chiamato `MasterViewController ` è stato possibile utilizzare il codice seguente per supportare **Visualizza** e **Pop**:

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

Il `GetViewControllerForPreview` metodo viene utilizzato per eseguire il **Visualizza** operazione. Accede ai dati di backup e di cella della tabella e quindi carica il `DetailViewController` dallo Storyboard corrente. Impostando il `PreferredContentSize` a (0,0) viene richiesto per il valore predefinito **Visualizza** dimensione di visualizzazione. Infine, abbiamo sfocatura tutto tranne la cella viene visualizzato con `previewingContext.SourceRect = cell.Frame` e restituiamo la nuova vista per la visualizzazione.

Il `CommitViewController` riutilizza la vista creata nel **Visualizza** per il **Pop** visualizzare quando l'utente preme più difficile.

### <a name="registering-for-peek-and-pop"></a>La registrazione per la visualizzazione e Pop

Dal Controller di visualizzazione che si vuole consentire all'utente di **Visualizza** e **Pop** gli elementi da, è necessario effettuare la registrazione per questo servizio. Nell'esempio precedente di un Controller di vista della tabella (`MasterViewController`), è necessario utilizzare il codice seguente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Check to see if 3D Touch is available
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        // Regiser for Peek and Pop
        RegisterForPreviewingWithDelegate(new PreviewingDelegate(this), View);
    }
    ...

}
```

Qui di seguito viene chiamato il `RegisterForPreviewingWithDelegate` (metodo) con un'istanza del `PreviewingDelegate` creato in precedenza. Nei dispositivi iOS che supportano 3D Touch, l'utente può premere disco rigido su un elemento per leggere la. Se si utilizza anche più complessa, l'elemento verrà visualizzato in esso standard visualizzazione.

Per ulteriori informazioni, vedere il nostro [iOS 9 esempio ApplicationShortcuts](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) e Apple [ViewControllerPreviews: utilizzando il UIViewController anteprima API](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) app di esempio, [ Riferimento alla classe UIPreviewAction](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/), [riferimento alla classe UIPreviewActionGroup](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/) e [UIPreviewActionItem Protocol Reference](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/).

<a name="Quick-Actions" />

## <a name="quick-actions"></a>Azioni rapide

Tramite tocco e azioni rapide 3D, è possibile aggiungere più comuni, semplice e rapido ai collegamenti di accesso alle funzioni nell'app dall'icona schermata Home nel dispositivo iOS.

Come descritto in precedenza, è possibile considerare azioni rapide come menu di scelta rapida che può essere estratto verticale quando l'utente fa clic su un elemento in un'applicazione desktop. Utilizzare azioni rapide per fornire collegamenti per le funzioni o le funzionalità dell'app i più comuni.

[![](3d-touch-images/quickactions01.png "Un esempio di un menu di azioni rapide")](3d-touch-images/quickactions01.png#lightbox)


### <a name="defining-static-quick-actions"></a>Definizione di azioni rapide statiche

Se uno o più delle azioni rapide richiesto dall'app sono statici e non si desidera modificare, è possibile definirli nell'app `Info.plist` file. Modificare questo file in un editor esterno e aggiungere le chiavi seguenti:

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

Di seguito viene definita la due elementi azione rapida statici con le chiavi seguenti:

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
* `UIApplicationShortcutItemTitle` -Definisce il titolo per l'elemento.
* `UIApplicationShortcutItemType` -È un valore stringa che verrà utilizzato per identificare l'elemento nella nostra app. Per altre informazioni, vedere la sezione successiva.

> [!IMPORTANT]
> Rapido scelta rapida azioni che vengono impostate nel `Info.plist` non è possibile accedere al file con il `Application.ShortcutItems` proprietà. Vengono passati solo per il `HandleShortcutItem` gestore dell'evento. 





### <a name="identifying-quick-action-items"></a>Identificazione degli elementi di azione rapida

Come illustrato in precedenza, quando è definito gli elementi delle azioni rapide nella propria applicazione `Info.plist`, è assegnato un valore stringa per il `UIApplicationShortcutItemType` chiave per identificare in modo sicuro.

Per rendere più facile da utilizzare nel codice di questi identificatori, aggiungere una classe denominata `ShortcutIdentifier` all'app di progetto e renderlo simile al seguente:

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

Successivamente, è necessario modificare l'applicazione `AppDelegate.cs` file per gestire l'utente selezionando un elemento azione rapida dall'icona dell'app dalla schermata iniziale.

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

In primo luogo, definiamo un pubblico `LaunchedShortcutItem` proprietà da rilevare l'ultimo elemento azione rapida selezionato dall'utente. Quindi, viene eseguito l'override di `FinishedLaunching` metodo e vedere se `launchOptions` è stato passato e contiene un elemento azione rapida. In questo caso, vengono archiviati l'azione in rapido il `LaunchedShortcutItem` proprietà.

Successivamente, viene eseguito l'override di `OnActivated` (metodo) e passare selezionare qualsiasi elemento di avvio veloce per il `HandleShortcutItem` metodo per agire su. Attualmente ci stiamo solo la scrittura di un messaggio di **Console**. In un'applicazione reale, è necessario gestire più azione è stata necessaria. Dopo che è stata eseguita l'azione, il `LaunchedShortcutItem` proprietà è deselezionata.

Infine, se l'app era già in esecuzione, il `PerformActionForShortcutItem` (metodo) deve essere chiamato per gestire l'elemento azione rapida, quindi è necessario eseguirne l'override e chiamare il nostro `HandleShortcutItem` metodo anche qui.


### <a name="creating-dynamic-quick-action-items"></a>La creazione di azioni rapide dinamica

Inoltre, per definire l'azione rapida statico elementi all'interno dell'applicazione `Info.plist` file, è possibile creare dinamica su immediatamente azioni rapide. Per definire due azioni rapide dinamica nuovo, modificare il `AppDelegate.cs` file nuovamente e modificare il `FinishedLaunching` metodo per la ricerca è simile alla seguente:

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

Ora ci stiamo verificando se il `application` contiene già un set di creati dinamicamente `ShortcutItems`, se non verranno creati due nuovi `UIMutableApplicationShortcutItem` oggetti per definire i nuovi elementi e aggiungerli al `ShortcutItems` matrice.

Il codice è già aggiunto il [la gestione di un'azione rapida](#Handling-a-Quick-Action) sezione precedente consente di gestire queste azioni rapide dinamica come quelli statici.

Si noti che è possibile creare una combinazione di elementi di azione rapida statiche e dinamiche (come obiettivo qui), non si è limitati a uno o l'altro.

Per ulteriori informazioni, il nostro [iOS 9 esempio ViewControllerPreview](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) e vedere Apple [ApplicationShortcuts: UIApplicationShortcutItem utilizzando](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/) app di esempio, [ Riferimento alla classe UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/), [riferimento alla classe UIMutableApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/) e [riferimento alla classe UIApplicationShortcutIcon](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/).

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>Test 3D Touch nel simulatore

Quando utilizza la versione più recente di Xcode e il simulatore iOS su un Mac compatibile con un tocco Force Abilita trackpad, è possibile testare la funzionalità di tocco 3D nel simulatore.

Per abilitare questa funzionalità, eseguire qualsiasi app iPhone simulato hardware che supporta 3D Touch (iPhone 6s e versioni successive). Selezionare quindi il **Hardware** menu in iOS simulatore e abilitare il **Trackpad Force utilizzare per il tocco 3D** voce di menu:

[![](3d-touch-images/simulator01.png "Selezionare il menu di Hardware nel simulatore iOS e abilitare la forza di Trackpad utilizzare per la voce di menu 3D touch")](3d-touch-images/simulator01.png#lightbox)

Con questa funzionalità è attiva, è possibile premere più difficile trackpad del Mac per abilitare 3D Touch esattamente come avviene con hardware reale iPhone.

## <a name="summary"></a>Riepilogo

In questo articolo è state introdotte nuove 3D Touch API reso disponibile in iOS 9 per iPhone 6s le iPhone 6s Plus. Che trattato pressione aggiunta a un'app; utilizzo di Peek e Pop per visualizzare rapidamente le informazioni all'interno dell'applicazione nel contesto corrente senza la navigazione. e con azioni rapide per fornire collegamenti alle app più comunemente utilizzato funzionalità.



## <a name="related-links"></a>Collegamenti correlati

- [iOS 9 esempio ViewControllerPreview](https://developer.xamarin.com/samples/monotouch/ios9/ViewControllerPreview/)
- [iOS 9 esempio ApplicationShortcuts](https://developer.xamarin.com/samples/monotouch/ios9/ApplicationShortcuts/)
- [iOS 9 per gli sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Preparare le app iPhone per 3D Touch](https://developer.apple.com/ios/3d-touch/)
