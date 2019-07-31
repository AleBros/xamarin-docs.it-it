---
title: Collegamenti Siri in Novell. iOS
description: Questo documento descrive come usare i collegamenti Siri in iOS 12. Viene illustrato NSUserActivities, gli Intent personalizzati, l'assegnazione di scelte rapide vocali, i collegamenti pertinenti e altro ancora.
ms.prod: xamarin
ms.assetid: 86424F79-3A7D-436E-927D-9A3267DA333B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: 4fa15e73575e20541df7ee8f606b01ec6e3d875a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656725"
---
# <a name="siri-shortcuts-in-xamarinios"></a>Collegamenti Siri in Novell. iOS

In [iOS 10](~/ios/platform/sirikit/index.md)Apple ha introdotto SiriKit, che consente di creare messaggi di posta elettronica, chiamate VoIP, pagamenti, allenamenti, prenotazioni di corse e app per la ricerca di foto che interagiscono con Siri.

In [iOS 11](~/ios/platform/introduction-to-ios11/sirikit.md), SiriKit ha ottenuto il supporto per più tipi di app e una maggiore flessibilità per la personalizzazione dell'interfaccia utente.

iOS 12 aggiunge i collegamenti Siri, consentendo a tutti i tipi di app di esporre le funzionalità a Siri. Siri apprende quando determinate attività basate su app sono più rilevanti per l'utente e usano queste informazioni per suggerire azioni potenziali tramite _collegamenti_. Toccando un collegamento o richiamandolo con un comando Voice si aprirà un'app o si eseguirà un'attività in background.

I tasti di scelta rapida devono essere usati per accelerare la capacità di un utente di eseguire un'attività comune, in molti casi senza aprire l'app in questione.

## <a name="sample-app-soup-chef"></a>App di esempio: Chef zuppa

Per comprendere meglio i collegamenti Siri, vedere l'app di esempio [Soup chef](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-soupchef) . Soup chef consente agli utenti di inserire ordini da un ristorante immaginario Soup, visualizzare la cronologia degli ordini e definire frasi da usare per l'ordinamento della zuppa interagendo con Siri.

> [!TIP]
> Prima di testare Soup chef in un simulatore o dispositivo iOS 12, abilitare le due impostazioni seguenti, utili per il debug dei collegamenti:
>
> - Nell'app **Impostazioni** abilitare **Developer > visualizzare i collegamenti recenti**.
> - Nell'app **Impostazioni** abilitare **Developer > visualizzare**le donazioni nella schermata di blocco.
>
> Queste impostazioni di debug semplificano la ricerca di collegamenti creati di recente (anziché stimati) nella schermata di blocco e nella schermata di ricerca di iOS.

Per usare l'app di esempio:

- Installare ed eseguire l'app di esempio Soup chef in un simulatore o [dispositivo](#testing-on-device)IOS 12.
- Fare clic **+** sul pulsante in alto a destra per creare un nuovo ordine.
- Selezionare un tipo di zuppa, specificare una quantità e le opzioni e toccare **Inserisci ordine**.
- Nella schermata **Order History** toccare l'ordine appena creato per visualizzarne i dettagli.
- Nella parte inferiore della schermata Order Details toccare **Aggiungi a Siri**.
- Registrare una frase vocali per associare l'ordine e toccare **Fine**.
- Ridurre a icona Soup chef, richiamare Siri e riposizionare l'ordine usando la frase vocale appena registrata.
- Dopo che Siri ha completato l'ordine, riaprire Soup chef e notare che il nuovo ordine è elencato nella schermata **Order History** .

L'app di esempio illustra come eseguire le operazioni seguenti:

- [Usare un collegamento NSUserActivity per aprire un'app](#using-an-nsuseractivity-shortcut-to-open-an-app).
- [Usare un collegamento per finalità personalizzate per eseguire un'attività](#using-a-custom-intent-shortcut-to-perform-a-task).
- [Fornire un'interfaccia utente per finalità personalizzate](#providing-a-user-interface-for-a-custom-intent).
- [Creare un collegamento vocale](#creating-a-voice-shortcut).

## <a name="infoplist-and-entitlementsplist"></a>INFO. plist e diritti. plist

Prima di approfondire ulteriormente il codice del chef, vedere il file **info. plist** e i file con **estensione plist** .

### <a name="infoplist"></a>Info. plist

Il file **info. plist** nel progetto **SoupChef** definisce l' **identificatore del bundle** come `com.xamarin.SoupChef`. Questo identificatore del bundle verrà usato come prefisso per gli identificatori del bundle delle estensioni dell'interfaccia utente per Intent e Intent, descritti più avanti in questo documento.

Il file **info. plist** contiene anche quanto segue:

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>OrderSoupIntent</string>
    <string>com.xamarin.SoupChef.viewMenu</string>
</array>
```

Questa `NSUserActivityTypes` coppia chiave/valore indica che Soup chef sa come gestire un oggetto `OrderSoupIntent`e [`NSUserActivity`](xref:Foundation.NSUserActivity) che contiene un [`ActivityType`](xref:Foundation.NSUserActivity.ActivityType) valore di "com. Novell. SoupChef. viewMenu".

Le attività e gli Intent personalizzati passati all'app stessa, anziché alle relative estensioni, vengono gestiti in `AppDelegate` (a [`UIApplicationDelegate`](xref:UIKit.UIApplicationDelegate) dal [`ContinueUserActivity`](xref:UIKit.UIApplicationDelegate.ContinueUserActivity*) metodo.

### <a name="entitlementsplist"></a>Entitlements.plist

Il file **titles. plist** nel progetto **SoupChef** contiene gli elementi seguenti:

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
<key>com.apple.developer.siri</key>
<true/>
```

Questa configurazione indica che l'app usa il gruppo di app "Group. com. Novell. SoupChef". L'estensione dell'app **SoupChefIntents** usa lo stesso gruppo di app, che consente la condivisione dei due progetti[`NSUserDefaults`](xref:Foundation.NSUserDefaults)
dati.

La `com.apple.developer.siri` chiave indica che l'app interagisce con Siri.

> [!NOTE]
> La configurazione della build del progetto **SoupChef** imposta **diritti personalizzati** su titles **. plist**.

## <a name="using-an-nsuseractivity-shortcut-to-open-an-app"></a>Uso di un collegamento NSUserActivity per aprire un'app

Per creare un collegamento che apre un'app per visualizzare contenuto specifico, creare un `NSUserActivity` oggetto e collegarlo al controller di visualizzazione per la schermata che si vuole aprire con il collegamento.

### <a name="setting-up-an-nsuseractivity"></a>Configurazione di un NSUserActivity

Nella schermata `SoupMenuViewController` di menu Crea un oggetto `NSUserActivity` e lo assegna [`UserActivity`](xref:UIKit.UIResponder.UserActivity) alla proprietà del controller di visualizzazione:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    UserActivity = NSUserActivityHelper.ViewMenuActivity;
}
```

L'impostazione `UserActivity` della proprietà _dona_ l'attività a Siri. Da questa donazione, Siri ottiene informazioni su quando e dove questa attività è rilevante per l'utente e apprende di consigliarla in futuro.

`NSUserActivityHelper`è una classe di utilità inclusa nella soluzione **SoupChef** , nella libreria di classi **SoupKit** . Viene creato un `NSUserActivity` oggetto e vengono impostate diverse proprietà correlate a Siri e alla ricerca:

```csharp
public static string ViewMenuActivityType = "com.xamarin.SoupChef.viewMenu";

public static NSUserActivity ViewMenuActivity {
    get
    {
        var userActivity = new NSUserActivity(ViewMenuActivityType)
        {
            Title = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            EligibleForSearch = true,
            EligibleForPrediction = true
        };

        var attributes = new CSSearchableItemAttributeSet(NSUserActivityHelper.SearchableItemContentType)
        {
            ThumbnailData = UIImage.FromBundle("tomato").AsPNG(),
            Keywords = ViewMenuSearchableKeywords,
            DisplayName = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            ContentDescription = NSBundleHelper.SoupKitBundle.GetLocalizedString("VIEW_MENU_CONTENT_DESCRIPTION", "View menu content description")
        };
        userActivity.ContentAttributeSet = attributes;

        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_SUGGESTED_PHRASE", "Voice shortcut suggested phrase");
        userActivity.SuggestedInvocationPhrase = phrase;
        return userActivity;
    }
}
```

Si noti quanto segue in particolare:

- L' `EligibleForPrediction` impostazione `true` di su indica che Siri può prevedere questa attività e visualizzarla come collegamento.
- La [`ContentAttributeSet`](xref:Foundation.NSUserActivity.ContentAttributeSet) matrice è uno standard [`CSSearchableItemAttributeSet`](xref:CoreSpotlight.CSSearchableItemAttributeSet) usato per includere `NSUserActivity` nei risultati della ricerca iOS.
- [`SuggestedInvocationPhrase`](xref:Foundation.NSUserActivity.SuggestedInvocationPhrase)è una frase che Siri suggerisce all'utente come scelta potenziale quando si assegna una frase a un collegamento.

### <a name="handling-an-nsuseractivity-shortcut"></a>Gestione di un collegamento NSUserActivity

Per gestire un `NSUserActivity` collegamento richiamato da un utente, un'applicazione iOS deve eseguire `ContinueUserActivity` l'override del `AppDelegate` metodo della classe, rispondendo `ActivityType` `NSUserActivity` in base al campo dell'oggetto passato:

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    // ...
    else if (userActivity.ActivityType == NSUserActivityHelper.ViewMenuActivityType)
    {
        HandleUserActivity();
        return true;
    }
    // ...
}
```

Questo metodo chiama `HandleUserActivity`, che trova il segue della schermata di menu e lo richiama:

```csharp
void HandleUserActivity()
{
    var rootViewController = Window?.RootViewController as UINavigationController;
    var orderHistoryViewController = rootViewController?.ViewControllers?.FirstOrDefault() as OrderHistoryTableViewController;
    if (orderHistoryViewController is null)
    {
        Console.WriteLine("Failed to access OrderHistoryTableViewController.");
        return;
    }
    var segue = OrderHistoryTableViewController.SegueIdentifiers.SoupMenu;
    orderHistoryViewController.PerformSegue(segue, null);
}
```

### <a name="assigning-a-phrase-to-an-nsuseractivity"></a>Assegnazione di una frase a un NSUserActivity

Per assegnare una frase a un `NSUserActivity`, aprire l'app **Impostazioni** iOS e scegliere **Siri & Cerca > i collegamenti**. Selezionare quindi il collegamento, in questo caso "Order lunch", e registrare una frase.

Se si richiama Siri e si usa questa frase, Soup chef verrà aperto nella schermata di menu.

## <a name="using-a-custom-intent-shortcut-to-perform-a-task"></a>Uso di un collegamento preventivo personalizzato per eseguire un'attività

### <a name="defining-a-custom-intent"></a>Definizione di una finalità personalizzata

Per fornire un collegamento che consenta a un utente di completare rapidamente un'attività specifica relativa all'app, creare una finalità personalizzata. Una finalità personalizzata rappresenta un'attività che un utente può desiderare di completare, i parametri rilevanti per tale attività e le potenziali risposte risultanti dall'esecuzione dell'attività. A seconda di come viene definito uno scopo personalizzato, richiamarlo può aprire l'app o eseguire un'attività in background.

Usare Xcode 10 per creare Intent personalizzati. Nel [repository SoupChef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), lo scopo personalizzato è definito in **OrderSoupIntentCodeGen**, un progetto Objective-C. Aprire il progetto e selezionare il file Intents **. intentdefinition** in **Project Navigator** per visualizzare lo scopo di **OrderSoup** .

Tenere presente quanto segue:

- Lo scopo ha una **categoria** di **ordine**. Sono disponibili diverse categorie predefinite che è possibile usare per gli Intent personalizzati; selezionare quello che corrisponde maggiormente all'attività che si intende abilitare per lo scopo personalizzato. Poiché si tratta di un'app per la mensa degli ordini, **OrderSoupIntent** USA **Order**.
- La casella di controllo **conferma** indica se Siri deve richiedere la conferma prima di eseguire l'attività. Per lo scopo della **zuppa di ordini** in Soup chef, questa opzione è abilitata perché l'utente sta effettuando un acquisto.
- La sezione **Parameters** del file con estensione intentdefinition definisce i parametri relativi a un collegamento. Per effettuare un ordine di minestra, Soup chef deve conoscerne il tipo, la quantità e le opzioni associate.
Ogni parametro ha un tipo; il parametro che non può essere rappresentato da un tipo predefinito viene impostato come **personalizzato**.
- L'interfaccia **tipi di collegamento** descrive le varie combinazioni di parametri che Siri può usare quando si suggerisce il collegamento. Le sezioni **titolo** e **sottotitolo** associate consentono di definire i messaggi che Siri userà per presentare un collegamento suggerito all'utente.
- È necessario selezionare la casella di controllo **supporta l'esecuzione in background** per qualsiasi collegamento che può essere eseguito senza aprire l'app per un'ulteriore interazione con l'utente.

### <a name="defining-custom-intent-responses"></a>Definizione di risposte per finalità personalizzate

L'elemento di **risposta** nidificato al di sotto della finalità **OrderSoup** rappresenta le potenziali risposte risultanti da un ordine di zuppa.

Nella definizione della risposta della finalità **OrderSoup** , tenere presente quanto segue:

- È possibile utilizzare le **Proprietà** di una risposta per personalizzare il messaggio comunicato all'utente. La risposta **OrderSoup** Intent ha proprietà **Soup** e **tempo attesa** .
- I **modelli di risposta** specificano i vari messaggi di esito positivo e negativo che possono essere usati per indicare lo stato dopo il completamento di un'attività di Intent.
- Per le risposte che indicano l'esito positivo, selezionare la casella di controllo **operazione riuscita** .
- La risposta **OrderSoupIntent** Success usa le proprietà **Soup** e **tempo attesa** per fornire un messaggio intuitivo e utile che descriva quando l'ordine di zuppa sarà pronto.

### <a name="generating-code-for-the-custom-intent"></a>Generazione di codice per finalità personalizzate

Se si compila il progetto Xcode contenente questa definizione di finalità personalizzata, Xcode genera codice che può essere usato per interagire a livello di programmazione con l'intento personalizzato e le relative risposte.

Per visualizzare il codice generato:

- Aprire **AppDelegate. m**.
- Aggiungere un'importazione al file di intestazione della finalità personalizzata:`#import "OrderSoupIntent.h"`
- In qualsiasi metodo della classe, aggiungere un riferimento a `OrderSoupIntent`.
- Fare clic con il `OrderSoupIntent` pulsante destro del mouse su e scegliere **Vai a definizione**.
- Fare clic con il pulsante destro del mouse sul file appena aperto, **OrderSoupIntent. h**, e scegliere **Mostra in Finder**.
- Verrà aperta una finestra di **ricerca** che contiene un file con estensione h e m contenente il codice generato.

Il codice generato include:

- `OrderSoupIntent`: Classe che rappresenta la finalità personalizzata.
- `OrderSoupIntentHandling`: Protocollo che definisce i metodi che verranno usati per confermare che lo scopo deve essere eseguito e il metodo che lo esegue effettivamente.
- `OrderSoupIntentResponseCode`: Enumerazione che definisce vari Stati di risposta.
- `OrderSoupIntentResponse`: classe che rappresenta la risposta all'esecuzione di una finalità.

### <a name="creating-a-binding-to-the-custom-intent"></a>Creazione di un'associazione alla finalità personalizzata

Per usare il codice generato da Xcode in un'app Novell. iOS, creare un' C# associazione.

#### <a name="creating-a-static-library-and-c-binding-definitions"></a>Creazione di una libreria statica C# e delle definizioni di associazione

Nel [repository SoupChef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)esaminare la cartella **OrderSoupIntentStaticLib** e aprire il progetto Xcode **OrderSoupIntentStaticLib. xcodeproj** .

Questo progetto di **libreria statica Cocoa Touch** contiene i file **OrderSoupIntent. h** e **OrderSoupIntent. m** generati da Xcode.

#### <a name="configuring-the-static-library-project-build-settings"></a>Configurazione delle impostazioni di compilazione del progetto di libreria statica

In strumento di **navigazione del progetto**Xcode selezionare il progetto di primo livello, **OrderSoupIntentStaticLib**, e passare a **compilazione fasi > compila origini**.
Si noti che **OrderSoupIntent. m** (che importa **OrderSoupIntent. h**) è elencato qui. In **link Binary with Libraries**, si noti che sono inclusi gli **Intent. Framework** e **Foundation. Framework** .
Con queste impostazioni, il Framework viene compilato correttamente.

#### <a name="building-the-static-library-and-generating-c-bindings-definitions"></a>Compilazione della libreria statica e generazione C# delle definizioni dei binding

Per compilare la libreria statica e generare C# le definizioni dei binding, attenersi alla procedura seguente:

- [Installare objectivey Objective](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started?context=xamarin/mac#installing-objective-sharpie), lo strumento usato per generare le definizioni dei binding dai file con estensione h e m creati da Xcode.

- Configurare il sistema per l'uso degli strumenti da riga di comando di Xcode 10:

    > [!WARNING]
    > L'aggiornamento degli strumenti da riga di comando selezionati ha effetto su tutte le versioni di Xcode installate nel sistema. Dopo aver terminato di usare l'app di esempio Soup chef, assicurarsi di ripristinare la configurazione originale di questa impostazione.

    - In Xcode scegliere **xcode > preferenze > percorsi** e impostare **gli strumenti da riga di comando** per l'installazione di Xcode 10 più recente disponibile nel sistema.

- Nel terminale, `cd` alla directory **OrderSoupIntentStaticLib**

- Digitare `make`, che compila:

    - Libreria statica, **libOrderSoupIntentStaticLib. a**
    - Nella directory di output **Bo** le C# definizioni dei binding:
        - **ApiDefinitions.cs**
        - **StructsAndEnums.cs**

Il progetto **OrderSoupIntentBindings** , che si basa su questa libreria statica e le relative definizioni di binding associate, compila automaticamente questi elementi.
Tuttavia, l'esecuzione manuale del processo precedente garantisce che venga compilato come previsto.

Per altre informazioni sulla creazione di una libreria statica e sull'uso di Objective Sharpie per la creazione di definizioni di C# binding, vedere l'argomento relativo all'associazione di [una libreria Objective-C di iOS](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#creating-a-static-library) .

#### <a name="creating-a-bindings-library"></a>Creazione di una libreria bindings

Con la libreria statica e le C# definizioni di binding create, il componente rimanente necessario per utilizzare il codice correlato all'intento generato da Xcode in un progetto Novell. iOS è una libreria di binding.

Nel [repository di Soup chef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)aprire il file **SoupChef. sln** . Tra le altre cose, questa soluzione contiene **OrderSoupIntentBinding**, una libreria di binding per la libreria statica generata in precedenza.

Si noti in particolare che questo progetto include:

- **ApiDefinitions.cs** : file generato in precedenza da Objective Sharpe e aggiunto al progetto. L'azione di **compilazione** di questo file è impostata su **ObjcBindingApiDefinition**.
- **StructsAndEnums.cs** : un altro file generato in precedenza da Objective Sharpe e aggiunto al progetto. L'azione di **compilazione** di questo file è impostata su **ObjcBindingCoreSource**.
- **Riferimento nativo** a **libOrderSoupIntentStaticLib. a**, la libreria statica compilata in precedenza.

> [!NOTE]
> Sia **ApiDefinitions.cs** che **StructsAndEnums.cs** contengono `[Watch (5,0), iOS (12,0)]`attributi quali. Questi attributi, generati da Objective Sharpie, sono stati impostati come commento perché non sono necessari per questo progetto.

Per ulteriori informazioni sulla creazione di C# una libreria di binding, vedere l'argomento relativo all' [associazione di una libreria Objective-C di iOS](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#create-a-xamarinios-binding-project) .

Si noti che il progetto **SoupChef** contiene un riferimento a **OrderSoupIntentBinding**, il che significa che ora può accedere a C#, in, le classi, le interfacce e le enumerazioni che contiene:

- `OrderSoupIntent`
- `OrderSoupIntentHandling`
- `OrderSoupIntentResponse`
- `OrderSoupIntenseResponseCode`

### <a name="adding-the-intentdefinition-file-to-your-solution"></a>Aggiunta del file con estensione intentdefinition alla soluzione

Nella soluzione SoupChef il progetto **SoupKit** contiene codice condiviso tra l'app e le relative estensioni. C# Il **file Intent. intentdefinition** è stato inserito nella directory **base. lproj** di **SoupKit**e ha un' **azione di compilazione** del **contenuto**. Il processo di compilazione copia il file nel bundle dell'app Soup chef, dove è necessario per il corretto funzionamento dell'app.

### <a name="donating-an-intent"></a>Donazione di un preventivo

Per consentire a Siri di suggerire un collegamento, è necessario innanzitutto comprendere quando il collegamento è pertinente.

Per fornire a Siri questa comprensione, Soup chef _dona_ una finalità a Siri ogni volta che l'utente inserisce un ordine di zuppa. In base a questa donazione, quando è stata donata, dove è stata donata, i parametri in esso contenuti – Siri apprende quando suggerire il collegamento in futuro.

**SoupChef** usa la `SoupOrderDataManager` classe per inserire le donazioni.
Quando viene chiamato per inserire un ordine di zuppa per un utente `PlaceOrder` , il metodo a [`DonateInteraction`](xref:Intents.INInteraction.DonateInteraction*)sua volta chiama:

```csharp
void DonateInteraction(Order order)
{
    var interaction = new INInteraction(order.Intent, null);
    interaction.Identifier = order.Identifier.ToString();
    interaction.DonateInteraction((error) =>
    {
        // ...
    });
}
```

Dopo il recupero di un preventivo, viene eseguito il wrapper [`INInteraction`](xref:Intents.INInteraction)in un oggetto.
Al `INInteraction` viene assegnato un[`Identifier`](xref:Intents.INInteraction.Identifier*)
corrisponde all'ID univoco dell'ordine (questa operazione sarà utile in un secondo momento quando si eliminano le donazioni intenzionali che non sono più valide). L'interazione viene quindi donata a Siri.

La chiamata `order.Intent` al metodo Get recupera un oggetto `Quantity` `OrderSoupIntent` che rappresenta l'ordine impostando la relativa immagine `Soup`, `Options`, e e una frase di chiamata da utilizzare come suggerimento quando l'utente registra una frase per l'associazione di Siri con lo scopo:

```csharp
public OrderSoupIntent Intent
{
    get
    {
        var orderSoupIntent = new OrderSoupIntent();
        orderSoupIntent.Quantity = new NSNumber(Quantity);
        orderSoupIntent.Soup = new INObject(MenuItem.ItemNameKey, MenuItem.LocalizedString);

        var image = UIImage.FromBundle(MenuItem.IconImageName);
        if (!(image is null))
        {
            var data = image.AsPNG();
            orderSoupIntent.SetImage(INImage.FromData(data), "soup");
        }

        orderSoupIntent.Options = MenuItemOptions
            .ToArray<MenuItemOption>()
            .Select<MenuItemOption, INObject>(arg => new INObject(arg.Value, arg.LocalizedString))
            .ToArray<INObject>();

        var comment = "Suggested phrase for ordering a specific soup";
        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_SOUP_SUGGESTED_PHRASE", comment);
        orderSoupIntent.SuggestedInvocationPhrase = String.Format(phrase, MenuItem.LocalizedString);

        return orderSoupIntent;
    }
}
```

#### <a name="removing-invalid-donations"></a>Rimozione di donazioni non valide

È importante rimuovere le donazioni che non sono più valide, in modo che Siri non renda disponibili suggerimenti rapidi o confusi.

In Soup chef è possibile usare la schermata **Configura menu** per contrassegnare una voce di menu come non disponibile. Siri non dovrebbe più suggerire un collegamento per ordinare una voce di menu non disponibile, `RemoveDonation` quindi il `SoupMenuManager` metodo di Elimina le donazioni per le voci di menu che non sono più disponibili. A tale scopo, è necessario:

- Ricerca degli ordini associati alla voce di menu ora non disponibile.
- Acquisizione degli identificatori.
- Eliminazione di interazioni con lo stesso identificatore.

```csharp
void RemoveDonation(MenuItem menuItem)
{
    if (!menuItem.IsAvailable)
    {
        Order[] orderHistory = OrderManager?.OrderHistory.ToArray<Order>();
        if (orderHistory is null)
        {
            return;
        }

        string[] orderIdentifiersToRemove = orderHistory
            .Where<Order>((order) => order.MenuItem.ItemNameKey == menuItem.ItemNameKey)
            .Select<Order, string>((order) => order.Identifier.ToString())
            .ToArray<string>();

        INInteraction.DeleteInteractions(orderIdentifiersToRemove, (error) =>
        {
            if (!(error is null))
            {
                Console.WriteLine($"Failed to delete interactions with error {error.ToString()}");
            }
            else
            {
                Console.WriteLine("Successfully deleted interactions");
            }
        });
    }
}
```

### <a name="creating-an-intents-extension"></a>Creazione di un'estensione per Intent

Il codice che viene eseguito quando Siri richiama un preventivo viene inserito in un'estensione per Intent, che può essere aggiunto come nuovo progetto alla stessa soluzione di un'app Novell. iOS esistente, ad esempio Soup chef. Nella soluzione **SoupChef** l'estensione è denominata **SoupChefIntents**.

#### <a name="soupchefintents-infoplist-and-entitlementsplist"></a>SoupChefIntents-info. plist e diritti. plist

##### <a name="soupchefintents-infoplist"></a>SoupChefIntents-info. plist

Il file **info. plist** nel progetto **SoupChefIntents** definisce l' **identificatore del bundle** come. `com.xamarin.SoupChef.SoupChefIntents`

Il file **info. plist** contiene anche quanto segue:

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsRestrictedWhileLocked</key>
        <array/>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <key>IntentsRestrictedWhileProtectedDataUnavailable</key>
        <array/>
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-service</string>
    <key>NSExtensionPrincipalClass</key>
    <string>IntentHandler</string>
</dict>
```

Nel file **info. plist**precedente:

- `IntentsRestrictedWhileLocked`elenca gli Intent che devono essere gestiti solo quando il dispositivo è sbloccato.
- `IntentsSupported`elenca gli Intent gestiti da questa estensione.
- `NSExtensionPointIdentifier`Specifica il tipo di estensione dell'app. per altre informazioni, vedere la [documentazione di Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) .
- `NSExtensionPrincipalClass`Specifica la classe da usare per gestire gli Intent supportati da questa estensione.

##### <a name="soupchefintents-entitlementsplist"></a>SoupChefIntents-titles. plist

Il file **titles. plist** nel progetto **SoupChefIntents** include la funzionalità **gruppi di app** . Questa funzionalità è configurata in modo da usare lo stesso gruppo di app del progetto **SoupChef** :

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
```

Soup chef rende permanente i dati `NSUserDefaults`con. Per condividere i dati tra l'app e l'estensione dell'app, fanno riferimento allo stesso gruppo di app nei file con estensione plist dei rispettivi **diritti** .

> [!NOTE]
> La configurazione della build del progetto **SoupChefIntents** imposta **diritti personalizzati** su titles **. plist**.

#### <a name="handling-an-ordersoupintent-background-task"></a>Gestione di un'attività in background OrderSoupIntent

Un'estensione per Intent esegue le attività in background necessarie per un collegamento basato su una finalità personalizzata.

Siri chiama il [`GetHandler`](xref:Intents.INExtension.GetHandler*) metodo `IntentHandler` della classe (definito in **info. plist** come `NSExtensionPrincipalClass`) per ottenere un'istanza di una classe che estende `OrderSoupIntentHandling`, che può essere usata per gestire un oggetto `OrderSoupIntent`:

```csharp
[Register("IntentHandler")]
public class IntentHandler : INExtension
{
    public override NSObject GetHandler(INIntent intent)
    {
        if (intent is OrderSoupIntent)
        {
            return new OrderSoupIntentHandler();
        }
        throw new Exception("Unhandled intent type: ${intent}");
    }

    protected IntentHandler(IntPtr handle) : base(handle) { }
}
```

`OrderSoupIntentHandler`, definito nel progetto **SoupKit** del codice condiviso, implementa due metodi importanti:

- `ConfirmOrderSoup`: Conferma se l'attività associata allo scopo deve essere effettivamente eseguita
- `HandleOrderSoup`: Inserisce l'ordine di zuppa e risponde all'utente chiamando il gestore di completamento passato

#### <a name="handling-an-ordersoupintent-that-opens-the-app"></a>Gestione di un OrderSoupIntent che apre l'app

Un'app deve gestire correttamente gli Intent che non vengono eseguiti in background.
Questi vengono gestiti nello stesso modo `NSUserActivity` dei collegamenti, `ContinueUserActivity` nel metodo di `AppDelegate`:

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var intent = userActivity.GetInteraction()?.Intent as OrderSoupIntent;
    if (!(intent is null))
    {
        HandleIntent(intent);
        return true;
    }
    // ...
}  
```

## <a name="providing-a-user-interface-for-a-custom-intent"></a>Fornire un'interfaccia utente per finalità personalizzate

Un'estensione per interfaccia utente Intent fornisce un'interfaccia utente personalizzata per un'estensione per Intent. Nella soluzione **SoupChef** , **SoupChefIntentsUI** è un'estensione per interfaccia utente Intent che fornisce un'interfaccia per **SoupChefIntents**.

### <a name="soupchefintentsui--infoplist-and-entitlementsplist"></a>SoupChefIntentsUI-info. plist e diritti. plist

#### <a name="soupchefintentsui-infoplist"></a>SoupChefIntentsUI-info. plist

Il file **info. plist** nel progetto **SoupChefIntentsUI** definisce l' **identificatore del bundle** come. `com.xamarin.SoupChef.SoupChefIntentsui`

Il file **info. plist** contiene anche quanto segue:

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <!-- ... -->
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-ui-service</string>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
</dict>
```

Nel file **info. plist**precedente:

- `IntentsSupported`indica che l' `OrderSoupIntent` oggetto è gestito da questa estensione dell'interfaccia utente di Intent.
- `NSExtensionPointIdentifier`Specifica il tipo di estensione dell'app. per altre informazioni, vedere la [documentazione di Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) .
- `NSExtensionMainStoryboard`Specifica lo storyboard che definisce l'interfaccia principale di questa estensione

#### <a name="soupchefintentsui-entitlementsplist"></a>SoupChefIntentsUI-titles. plist

Il progetto **SoupChefIntentsUI** non necessita di un file con **estensione plist** .

### <a name="creating-the-user-interface"></a>Creazione dell'interfaccia utente

Poiché **info. plist** per **SoupChefIntentsUI** imposta la `NSExtensionMainStoryboard` chiave su `MainInterface`, il file **MainInterace. Storyboard** definisce l'interfaccia per l'estensione dell'interfaccia utente Intent.

In questo storyboard è presente un solo controller di visualizzazione di tipo **IntentViewController**. Fa riferimento a due visualizzazioni:

- **invoiceView**, di tipo`InvoiceView`
- **confirmationView**, di tipo`ConfirmOrderView`

> [!NOTE]
> Le interfacce per **invoiceView** e **confirmationView** sono definite in **Main. Storyboard** come visualizzazioni secondarie. IOS designer in Visual Studio per Mac e Visual Studio 2017 non fornisce supporto per la visualizzazione o la modifica delle visualizzazioni secondarie; a tale scopo, aprire **Main. Storyboard** nel Interface Builder di Xcode.

`IntentViewController`implementa l'oggetto[`IINUIHostedViewControlling`](xref:IntentsUI.IINUIHostedViewControlling)
interfaccia utilizzata per fornire un'interfaccia personalizzata quando si utilizzano Siri. Il[`ConfigureView`](xref:IntentsUI.INUIHostedViewControlling_Extensions.ConfigureView*)
viene chiamato il metodo per personalizzare l'interfaccia, visualizzando la conferma o la fattura, a seconda che l'interazione venga confermata ([`INIntentHandlingStatus.Ready`](xref:Intents.INIntentHandlingStatus)) o che sia stata eseguita correttamente[`INIntentHandlingStatus.Success`](xref:Intents.INIntentHandlingStatus)():

```csharp
[Export("configureViewForParameters:ofInteraction:interactiveBehavior:context:completion:")]
public void ConfigureView(
    NSSet<INParameter> parameters,
    INInteraction interaction,
    INUIInteractiveBehavior interactiveBehavior,
    INUIHostedViewContext context,
    INUIHostedViewControllingConfigureViewHandler completion)
{
    // ...
    if (interaction.IntentHandlingStatus == INIntentHandlingStatus.Ready)
    {
        desiredSize = DisplayInvoice(order, intent);
    }
    else if(interaction.IntentHandlingStatus == INIntentHandlingStatus.Success)
    {
        var response = interaction.IntentResponse as OrderSoupIntentResponse;
        if (!(response is null))
        {
            desiredSize = DisplayOrderConfirmation(order, intent, response);
        }
    }
    completion(true, parameters, desiredSize);
}
```

> [!TIP]
> Per ulteriori informazioni sul metodo `ConfigureView` , guardare la presentazione di Apple WWDC 2017, [novità di SiriKit](https://developer.apple.com/videos/play/wwdc2017/214/).

## <a name="creating-a-voice-shortcut"></a>Creazione di un collegamento vocale

Soup chef fornisce un'interfaccia per l'assegnazione di un collegamento vocale a ogni ordine, rendendo possibile l'ordinamento della zuppa con Siri. Infatti, l'interfaccia usata per registrare e assegnare i tasti di scelta rapida viene fornita da iOS e richiede un codice personalizzato ridotto.

In `OrderDetailViewController`, quando un utente tocca la riga **Aggiungi a Siri** della tabella, il [`RowSelected`](xref:UIKit.UITableViewSource.RowSelected*) metodo Visualizza una schermata per aggiungere o modificare un tasto di scelta rapida:

```csharp
public override void RowSelected(UITableView tableView, NSIndexPath indexPath)
{
    // ...
    else if (TableConfiguration.Sections[indexPath.Section].Type == OrderDetailTableConfiguration.SectionType.VoiceShortcut)
    {
        INVoiceShortcut existingShortcut = VoiceShortcutDataManager?.VoiceShortcutForOrder(Order);
        if (!(existingShortcut is null))
        {
            var editVoiceShortcutViewController = new INUIEditVoiceShortcutViewController(existingShortcut);
            editVoiceShortcutViewController.Delegate = this;
            PresentViewController(editVoiceShortcutViewController, true, null);
        }
        else
        {
            // Since the app isn't yet managing a voice shortcut for
            // this order, present the add view controller
            INShortcut newShortcut = new INShortcut(Order.Intent);
            if (!(newShortcut is null))
            {
                var addVoiceShortcutVC = new INUIAddVoiceShortcutViewController(newShortcut);
                addVoiceShortcutVC.Delegate = this;
                PresentViewController(addVoiceShortcutVC, true, null);
            }
        }
    }
}
```

A seconda che esista un collegamento vocale esistente per l'ordine attualmente visualizzato, `RowSelected` presenta un controller di visualizzazione di tipo [`INUIEditVoiceShortcutViewController`](xref:IntentsUI.INUIEditVoiceShortcutViewController) o [`INUIAddVoiceShortcutViewController`](xref:IntentsUI.INUIAddVoiceShortcutViewController).
In ogni caso, `OrderDetailViewController` imposta se stesso come controller di `Delegate`visualizzazione, motivo per cui implementa anche[`IINUIAddVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIAddVoiceShortcutViewControllerDelegate)
e [`IINUIEditVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIEditVoiceShortcutViewControllerDelegate).

## <a name="testing-on-device"></a>Test nel dispositivo

Per eseguire Soup chef in un dispositivo, seguire le istruzioni riportate di seguito. Leggere anche la [Nota sul provisioning automatico](#automatic-provisioning).

### <a name="app-group-app-ids-provisioning-profiles"></a>Gruppo di app, ID app, profili di provisioning

Nella sezione **Certificates, IDs &** profiles del [portale Apple Developer](https://developer.apple.com/)eseguire le operazioni seguenti:

- Creare un gruppo di app per condividere i dati tra l'app Soup chef e le relative estensioni. Ad esempio: **Group. com. NomeSocietà. SoupChef**

- Creare tre ID app: uno per l'app, uno per l'estensione per Intent e uno per l'estensione dell'interfaccia utente Intent. Ad esempio:

    - App: **com.yourcompanyname.SoupChef**
        - Per questo ID app, assegnare le funzionalità SiriKit e **gruppi di app** .

    - Estensione per Intent: **com. NomeSocietà. SoupChef. Intent**
        - Per questo ID app, assegnare la funzionalità **gruppi di app** .

    - Estensione per interfaccia utente Intent: **com. NomeSocietà. SoupChef. Intentsui**
        - Questo ID app non richiede funzionalità speciali.

- Dopo aver creato gli ID app precedenti, modificare la funzionalità **gruppi di app** assegnata all'app e l'estensione per Intent, specificando il gruppo di app specifico creato in precedenza.

- Creare tre nuovi profili di provisioning di sviluppo, uno per ognuno dei nuovi ID app.

- Scaricare i profili di provisioning e fare doppio clic su ognuno di essi per installarlo. Se Visual Studio per Mac o Visual Studio 2017 è già in esecuzione, riavviarlo per assicurarsi che registri i nuovi profili di provisioning.

### <a name="editing-infoplist-entitlementsplist-and-source-code"></a>Modifica di info. plist, diritti. plist e codice sorgente

In Visual Studio per Mac o Visual Studio 2017 eseguire le operazioni seguenti:

- Aggiornare i vari file **info. plist** nella soluzione. Impostare l'identificatore dell'app, l'estensione per Intent e l'identificatore del **bundle** di estensione dell'interfaccia utente per gli ID app definiti in precedenza:

    - App: **com.yourcompanyname.SoupChef**
    - Estensione per Intent: **com. NomeSocietà. SoupChef. Intent**
    - Estensione per interfaccia utente Intent: **com. NomeSocietà. SoupChef. Intentsui**

- Aggiornare il file con **estensione plist dei diritti** per il progetto **SoupChef** :
    - Per la funzionalità **gruppi di app** , impostare il gruppo sul nuovo gruppo di app creato in precedenza (nell'esempio precedente, era **Group. com. NomeSocietà. SoupChef**).
    - Verificare che **SiriKit** sia abilitato.

- Aggiornare il file con **estensione plist dei diritti** per il progetto **SoupChefIntents** :
    - Per la funzionalità **gruppi di app** , impostare il gruppo sul nuovo gruppo di app creato in precedenza (nell'esempio precedente, era **Group. com. NomeSocietà. SoupChef**).

- Infine, aprire **NSUserDefaultsHelper.cs**. Impostare la `AppGroup` variabile sul valore del nuovo gruppo di app (ad esempio, impostarlo su `group.com.yourcompanyname.SoupChef`).

### <a name="configuring-the-build-settings"></a>Configurazione delle impostazioni di compilazione

In Visual Studio per Mac o Visual Studio 2017:

- Aprire le opzioni/proprietà per il progetto **SoupChef** . Nella scheda **firma bundle iOS** impostare Identity di **firma** su automatico e **profilo** di provisioning sul nuovo profilo di provisioning specifico dell'app creato in precedenza.

- Aprire le opzioni/proprietà per il progetto **SoupChefIntents** . Nella scheda **firma bundle iOS** impostare Identity di **firma** su automatico e **profilo** di provisioning sul nuovo profilo di provisioning specifico dell'estensione Intents creato in precedenza.

- Aprire le opzioni/proprietà per il progetto **SoupChefIntentsUI** . Nella scheda **firma bundle iOS** impostare Identity di **firma** su automatico e **profilo** di provisioning sul nuovo profilo di provisioning specifico dell'estensione dell'interfaccia utente creato in precedenza.

Con queste modifiche, l'app viene eseguita in un dispositivo iOS.

### <a name="automatic-provisioning"></a>Provisioning automatico

Si noti che è possibile usare il provisioning [automatico](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/automatic-provisioning) per eseguire molte di queste attività di provisioning direttamente nell'IDE.
Tuttavia, il provisioning automatico non configura gruppi di app. È necessario configurare manualmente i file **. plist dei diritti** con il nome del gruppo di app che si vuole usare, visitare il portale Apple Developer per creare il gruppo di app, assegnare il gruppo di app a ogni ID app creato dal provisioning automatico, rigenerare i profili di provisioning (app, estensione per Intent, estensione per interfaccia utente Intent) per includere il gruppo di app appena creato e scaricarli e installarli.

## <a name="related-links"></a>Collegamenti correlati

- [Chef zuppa (Novell)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-soupchef)
- [Chef della zuppa (SWIFT)](https://developer.apple.com/documentation/sirikit/accelerating_app_interactions_with_shortcuts?language=objc)
- [SiriKit (Apple)](https://developer.apple.com/sirikit/)
- [Introduzione ai collegamenti Siri-WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/211/)
- [Compilazione per la voce con i collegamenti Siri-WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/214/)
- [Collegamenti Siri sulla faccia Watch Siri – WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/217/)
- [Novità di SiriKit – WWDC 2017](https://developer.apple.com/videos/play/wwdc2017/214/)
- [Creare un'estensione per app Intent (Apple)](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension?language=objc)
