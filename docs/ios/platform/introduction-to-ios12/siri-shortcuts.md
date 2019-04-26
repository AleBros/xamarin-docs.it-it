---
title: Tasti di scelta rapida Siri in xamarin. IOS
description: Questo documento descrive come usare i tasti di scelta rapida Siri in iOS 12. Vengono illustrati NSUserActivities, scopi personalizzati, assegnazione di tasti di scelta rapida vocali, i collegamenti rilevanti e altro ancora.
ms.prod: xamarin
ms.assetid: 86424F79-3A7D-436E-927D-9A3267DA333B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: e37fd88f0d5fcf02ece0ae2f5e3164a507067e29
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034785"
---
# <a name="siri-shortcuts-in-xamarinios"></a>Tasti di scelta rapida Siri in xamarin. IOS

Nelle [iOS 10](~/ios/platform/sirikit/index.md), Apple ha introdotto SiriKit, permettendo così di messaggistica di compilazione, chiamate VoIP, pagamenti, allenamenti, recarsi prenotazioni e la foto di App di ricerca che interagiscono con Siri.

Nelle [iOS 11](~/ios/platform/introduction-to-ios11/sirikit.md), SiriKit ottenuto il supporto per più tipi di App e una maggiore flessibilità per la personalizzazione dell'interfaccia utente.

iOS 12 aggiunge collegamenti Siri, consentendo tutti i tipi di App per esporre le proprie funzionalità ai Siri. Siri apprende quando determinate attività basato su app sono più rilevanti per l'utente e queste informazioni vengono utilizzate per suggerire potenziali azioni tramite _tasti di scelta rapida_. Toccare un collegamento o chiamata con un comando vocale verrà aprire un'app o eseguire un'attività in background.

Tasti di scelta rapida consente di accelerare la capacità di un utente per completare un'attività comune, in molti casi senza aprire l'app in questione.

## <a name="sample-app-soup-chef"></a>App di esempio: Considerazioni su Chef

Per comprendere meglio i tasti di scelta rapida Siri, esaminiamo il [Chef MINESTRONE](https://developer.xamarin.com/samples/monotouch/ios12/SoupChef/) app di esempio. MINESTRONE Chef consente agli utenti di effettuare ordini da un ristorante MINESTRONE immaginaria, visualizzare la cronologia degli ordini e definire le frasi da utilizzare quando si ordina MINESTRONE interagendo con Siri.

> [!TIP]
> Prima di testare MINESTRONE Chef in un dispositivo o simulatore iOS 12, abilitare le due impostazioni seguenti, che sono utili durante il debug di tasti di scelta rapida:
>
> - Nel **le impostazioni** app, abilitare **Developer > tasti di scelta rapida recente visualizzato**.
> - Nel **le impostazioni** app, abilitare **Developer > donazioni visualizzato nella schermata di blocco**.
>
> Questi debug verificare le impostazioni è più facile trovare-creati di recente (invece di stimati) tasti di scelta rapida in iOS schermata di blocco e la schermata di ricerca.

Per usare l'app di esempio:

- Installare ed eseguire il Chef MINESTRONE app di esempio in un simulatore iOS 12 o [dispositivo](#testing-on-device).
- Scegliere il **+** pulsante in alto a destra per creare un nuovo ordine.
- Selezionare un tipo di MINESTRONE, specificare una quantità e le opzioni e toccare **effettuare l'ordine**.
- Nel **cronologia degli ordini** toccare l'ordine appena creata per visualizzarne i dettagli.
- Nella parte inferiore della schermata dei dettagli dell'ordine, toccare **Aggiungi a Siri**.
- Registrare una frase vocali per associare l'ordine e toccare **Fine**.
- Ridurre al minimo MINESTRONE Chef, richiamare Siri e inserire nuovamente l'ordine usando la frase vocale che appena registrata.
- Al termine dell'ordine di Siri, aprire nuovamente MINESTRONE Chef e notare che il nuovo ordine viene elencato nella **cronologia degli ordini** dello schermo.

L'app di esempio viene illustrato come:

- [Utilizzare un collegamento NSUserActivity per aprire un'app](#using-an-nsuseractivity-shortcut-to-open-an-app).
- [Utilizzare un collegamento preventivo personalizzato per eseguire un'attività](#using-a-custom-intent-shortcut-to-perform-a-task).
- [Fornire un'interfaccia utente per una finalità personalizzato](#providing-a-user-interface-for-a-custom-intent).
- [Creare un collegamento vocale](#creating-a-voice-shortcut).

## <a name="infoplist-and-entitlementsplist"></a>File Info. plist ed entitlements. plist

Prima di passare un livello più profondo per il codice MINESTRONE Chef, dare un'occhiata relativi **Info. plist** e **entitlements. plist** file.

### <a name="infoplist"></a>Info. plist

Il **Info. plist** del file nei **SoupChef** progetto definisce il **identificatore del Bundle** come `com.xamarin.SoupChef`. Questo identificatore del bundle verrà usato come prefisso per gli identificatori di bundle dei Intent e interfaccia utente Intent estensioni illustrate più avanti in questo documento.

Il **Info. plist** file contiene inoltre quanto segue:

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>OrderSoupIntent</string>
    <string>com.xamarin.SoupChef.viewMenu</string>
</array>
```

Ciò `NSUserActivityTypes` coppia chiave/valore indica che MINESTRONE Chef sa come gestire un' `OrderSoupIntent`e un [ `NSUserActivity` ](xref:Foundation.NSUserActivity) aventi un [ `ActivityType` ](xref:Foundation.NSUserActivity.ActivityType) di "com.xamarin.SoupChef.viewMenu".

Le attività e scopi personalizzati passati all'app stessa, in contrapposizione alle relative estensioni, vengono gestiti nel `AppDelegate` (una [ `UIApplicationDelegate` ](xref:UIKit.UIApplicationDelegate) per il [ `ContinueUserActivity` ](xref:UIKit.UIApplicationDelegate.ContinueUserActivity*) (metodo).

### <a name="entitlementsplist"></a>Entitlements.plist

Il **entitlements. plist** del file nei **SoupChef** progetto contiene quanto segue:

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
<key>com.apple.developer.siri</key>
<true/>
```

Questa configurazione indica che l'app Usa il gruppo di app "group.com.xamarin.SoupChef". Il **SoupChefIntents** estensione dell'app Usa questo stesso gruppo di app, che consente i due progetti da condividere [`NSUserDefaults`](xref:Foundation.NSUserDefaults)
dati.

Il `com.apple.developer.siri` chiave indica che l'app interagisce con Siri.

> [!NOTE]
> Il **SoupChef** set di configurazione di compilazione del progetto **Entitlement personalizzati** al **entitlements. plist**.

## <a name="using-an-nsuseractivity-shortcut-to-open-an-app"></a>Uso di un collegamento NSUserActivity per aprire un'app

Per creare un collegamento che apre un'app per visualizzare il contenuto specifico, creare un `NSUserActivity` e collegarlo al controller di visualizzazione per la schermata si desidera aprire il collegamento.

### <a name="setting-up-an-nsuseractivity"></a>Configurazione di un NSUserActivity

Nella schermata menu `SoupMenuViewController` crea un' `NSUserActivity` e la assegna al controller di visualizzazione [ `UserActivity` ](xref:UIKit.UIResponder.UserActivity) proprietà:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    UserActivity = NSUserActivityHelper.ViewMenuActivity;
}
```

Impostando il `UserActivity` proprietà _donates_ l'attività a Siri. Da questo donazione, Siri acquisisce informazioni su dove e quando questa attività è rilevante per l'utente e impara a meglio suggerire in futuro.

`NSUserActivityHelper` incluso una classe di utilità di **SoupChef** soluzione, nella **SoupKit** libreria di classi. Crea un `NSUserActivity` e imposta le varie proprietà relative a Siri e ricerca:

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

Notare in particolare quanto segue:

- L'impostazione `EligibleForPrediction` a `true` indica che Siri può prevedere questa attività e della superficie di attacco, come collegamento.
- Il [ `ContentAttributeSet` ](xref:Foundation.NSUserActivity.ContentAttributeSet) matrice è uno standard [ `CSSearchableItemAttributeSet` ](xref:CoreSpotlight.CSSearchableItemAttributeSet) utilizzato per includere un `NSUserActivity` nei risultati della ricerca di iOS.
- [`SuggestedInvocationPhrase`](xref:Foundation.NSUserActivity.SuggestedInvocationPhrase) è una frase che Siri suggerirà all'utente come una potenziale scelta quando si assegna una frase a un scelta rapida.

### <a name="handling-an-nsuseractivity-shortcut"></a>La gestione di un collegamento NSUserActivity

Per gestire un `NSUserActivity` scelta rapida richiamato da un utente, un'applicazione iOS deve eseguire l'override di `ContinueUserActivity` metodo del `AppDelegate` (classe), rispondendo in base il `ActivityType` campo del passato `NSUserActivity` oggetto:

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

Questo metodo chiama `HandleUserActivity`, che consente di trovare l'elemento segue la schermata di menu che viene quindi richiamata:

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

### <a name="assigning-a-phrase-to-an-nsuseractivity"></a>Assegnazione di una frase a una NSUserActivity

Per assegnare una frase a un' `NSUserActivity`, aprire il file iOS **impostazioni** app e scegliere **Siri e ricerche > collegamenti**. Quindi, selezionare il collegamento (in questo caso, "Ordine di pranzo") e registrare una frase.

Richiamo di Siri e usando la frase verrà aperto MINESTRONE Chef schermata del menu.

## <a name="using-a-custom-intent-shortcut-to-perform-a-task"></a>Usando un collegamento preventivo personalizzato per eseguire un'attività

### <a name="defining-a-custom-intent"></a>La definizione di un programma personalizzato

Per fornire un collegamento che consente agli utenti di completare rapidamente una specifica attività correlata all'app, creare un programma personalizzato. Un programma personalizzato rappresenta un'attività di che un utente desideri completare, parametri relativi a tale attività e le potenziali risposte risultante dall'esecuzione dell'attività. A seconda del modo in cui è definito un programma personalizzato, richiamarlo può aprire l'app oppure eseguire un'attività in background.

Usare Xcode 10 per creare tipi personalizzati. Nel [repository SoupChef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), l'intento personalizzato è definito nel **OrderSoupIntentCodeGen**, un progetto di Objective-C. Aprire questo progetto e selezionare il **Intents.intentdefinition** del file nei **Project Navigator** per visualizzare il **OrderSoup** finalità.

Tenere presente quanto segue:

- Lo scopo è un **categoria** dei **ordine**. Esistono diverse categorie predefinite che possono essere usate per scopi personalizzati; Selezionare quella che rispecchia meglio l'attività che verrà abilitata la finalità personalizzata. Poiché si tratta di un MINESTRONE app, di ordinamento **OrderSoupIntent** Usa **ordine**.
- Il **conferma** casella di controllo indica se Siri deve richiedere conferma prima dell'esecuzione dell'attività. Per il **MINESTRONE ordine** preventivo in Chef MINESTRONE, questa opzione è abilitata poiché l'utente effettua un acquisto.
- Il **parametri** sezione del file .intentdefinition definisce i parametri relativi a un collegamento. Per inserire un ordine MINESTRONE, Chef MINESTRONE necessario conoscere il tipo di MINESTRONE, la relativa quantità e le opzioni associate.
Ogni parametro ha un tipo. parametro che non può essere rappresentato da un tipo predefinito sono impostate come **Custom**.
- Il **tipi di collegamento** interfaccia descrive le varie combinazioni di parametri Siri è possibile usare quando si suggerisce di scelta rapida. L'oggetto associato **Title** e **sottotitolo** sezioni consentono di definire i messaggi Siri userà per presentare un collegamento consigliato all'utente.
- Il **supporta l'esecuzione in Background** casella di controllo deve essere selezionata per qualsiasi collegamento che può essere eseguito senza dover aprire l'app per un'ulteriore interazione dell'utente.

### <a name="defining-custom-intent-responses"></a>La definizione delle risposte preventivi personalizzate

Il **risposta** elemento annidato sotto il **OrderSoup** finalità rappresenta le risposte potenziali che derivano da un ordine basata su acronimi.

Nel **OrderSoup** la definizione di risposta dell'intento, tenere presente quanto segue:

- Una risposta **proprietà** può essere utilizzato per personalizzare il messaggio comunicato all'utente. Il **OrderSoup** intent risposta ha **soup** e **waitTime** proprietà.
- Il **i modelli di risposta** specificare i vari messaggi di esito positivo e negativo che possono essere utilizzati per indicare lo stato dopo il completamento dell'attività di un intent.
- Il **Success** casella di controllo deve essere selezionata per le risposte che indicano l'esito positivo.
 - Il **OrderSoupIntent** risposta di esito positivo viene utilizzato il **soup** e **waitTime** proprietà per fornire un messaggio descrittivo e utile che descrive quando l'ordine MINESTRONE potranno essere.

### <a name="generating-code-for-the-custom-intent"></a>Generazione di codice per lo scopo personalizzato

Compilazione del progetto Xcode che contiene questa definizione preventivo personalizzata fa sì che Xcode generare codice che può essere usato per interagire a livello di codice con l'intento personalizzato e le relative risposte.

Per visualizzare il codice generato:

- Aprire **appdelegate. M**.
- Aggiungere un'importazione al file di intestazione dell'intento personalizzato: `#import "OrderSoupIntent.h"`
- In qualsiasi metodo nella classe, aggiungere un riferimento a `OrderSoupIntent`.
- Fare clic su `OrderSoupIntent` e scegliere **passare alla definizione**.
- Fare doppio clic nel file appena aperta **OrderSoupIntent.h**e selezionare **Mostra nel Finder**.
- Verrà aperta una **Finder** finestra che contiene un file con estensione h e m contenente il codice generato.

Questo codice generato include:

- `OrderSoupIntent` : Una classe che rappresenta l'intento personalizzato.
- `OrderSoupIntentHandling` : Un protocollo che definisce i metodi che verranno usati per verificare che lo scopo deve essere eseguito e il metodo che viene effettivamente eseguito.
- `OrderSoupIntentResponseCode` : Un'enumerazione che definisce gli stati di risposta diversi.
- `OrderSoupIntentResponse` : una classe che rappresenta la risposta per l'esecuzione di un intent.

### <a name="creating-a-binding-to-the-custom-intent"></a>Creazione di un'associazione all'Intent personalizzato

Per usare il codice generato da Xcode in un'app xamarin. IOS, creare un C# per tale associazione.

#### <a name="creating-a-static-library-and-c-binding-definitions"></a>Creazione di una libreria statica e C# le definizioni di associazione

Nel [repository SoupChef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), dare un'occhiata **OrderSoupIntentStaticLib** cartella e aprire il **OrderSoupIntentStaticLib.xcodeproj** progetto Xcode.

Ciò **Cocoa Touch libreria statica** progetto contiene il **OrderSoupIntent.h** e **OrderSoupIntent.m** file generati da Xcode.

#### <a name="configuring-the-static-library-project-build-settings"></a>Configurare le impostazioni di compilazione progetto libreria statica

In Xcode **Project Navigator**, selezionare il progetto di primo livello **OrderSoupIntentStaticLib**e passare a **Build Phases > compilare origini**.
Si noti che **OrderSoupIntent.m** (quali importazioni **OrderSoupIntent.h**) è incluso nell'elenco. Nelle **Link Binary With Libraries**, si noti che **Intents.framework** e **Foundation.framework** sono inclusi.
Con queste impostazioni posto, il framework verrà compilato correttamente.

#### <a name="building-the-static-library-and-generating-c-bindings-definitions"></a>Compilazione della libreria statica e la generazione di C# definizioni di associazioni

Per compilare la libreria statica e generare C# definizioni di associazioni, seguire questa procedura:

- [Installare Sharpie obiettivo](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started?context=xamarin/mac#installing-objective-sharpie), lo strumento utilizzato per generare definizioni di associazioni dai file con estensione h e m creati da Xcode.

- Configurare il sistema per l'uso di strumenti da riga di comando Xcode 10:

    > [!WARNING]
    > L'aggiornamento degli strumenti della riga di comando selezionato influisce su tutte le versioni installate di Xcode nel sistema. Al termine usando il Chef MINESTRONE app di esempio, assicurarsi di annullare questa impostazione di configurazione originale.

    - In Xcode, scegliere **Xcode > Preferenze > percorsi** e impostare **strumenti da riga di comando** all'installazione di Xcode 10 più recente disponibile nel sistema.

- Nel terminale `cd` per il **OrderSoupIntentStaticLib** directory.

- Tipo `make`, quali compilazioni:

    - La libreria statica, **libOrderSoupIntentStaticLib.a**
    - Nel **bo** directory di output, C# le definizioni di binding:
        - **ApiDefinitions.cs**
        - **StructsAndEnums.cs**

Il **OrderSoupIntentBindings** progetto, che si basa su questa libreria statica e le relative definizioni binding associati, questi elementi viene compilato automaticamente.
Tuttavia, esecuzione manuale tramite il processo precedente garantisce che la compilazione come previsto.

Per altre informazioni sulla creazione di una libreria statica e l'uso Sharpie descrive come creare C# definizioni di associazioni, esaminiamo il [associazione di una libreria Objective-C iOS](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#creating-a-static-library) procedura dettagliata.

#### <a name="creating-a-bindings-library"></a>Creazione di una libreria di binding

Con la libreria statica e il C# creare definizioni di associazioni, la parte rimanente necessari per utilizzare generato da Xcode correlati con finalità di codice in un progetto xamarin. IOS è una libreria di binding.

Nel [repository Chef MINESTRONE](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), aprire il **SoupChef.sln** file. Tra le altre cose, questa soluzione contiene **OrderSoupIntentBinding**, una libreria di binding per la libreria statica generata in precedenza.

Si noti in particolare che questo progetto include:

- **ApiDefinitions.cs** : un file generato in precedenza da Sharpie obiettivo e aggiunti al progetto. Questo file **Build Action** è impostata su **ObjcBindingApiDefinition**.
- **StructsAndEnums.cs** : un altro file generati in precedenza da Sharpie obiettivo e aggiunti al progetto. Questo file **Build Action** è impostata su **ObjcBindingCoreSource**.
- Oggetto **riferimento nativo** al **libOrderSoupIntentStaticLib.a**, la libreria statica creata in precedenza.

> [!NOTE]
> Entrambe **ApiDefinitions.cs** e **StructsAndEnums.cs** contengono attributi, ad esempio `[Watch (5,0), iOS (12,0)]`. Questi attributi, generati da Sharpie obiettivo, sono stati commentati poiché essi non sono necessarie per questo progetto.

Per altre informazioni sulla creazione di un C# libreria di binding, consultare i [associazione di una libreria Objective-C iOS](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#create-a-xamarinios-binding-project) procedura dettagliata.

Si noti che il **SoupChef** progetto contiene un riferimento a **OrderSoupIntentBinding**, il che significa che ora possibile accedere, in C#, le classi, interfacce ed enumerazioni contiene:

- `OrderSoupIntent`
- `OrderSoupIntentHandling`
- `OrderSoupIntentResponse`
- `OrderSoupIntenseResponseCode`

### <a name="adding-the-intentdefinition-file-to-your-solution"></a>Aggiunta del file .intentdefinition alla soluzione

Nel C# **SoupChef** soluzione, il **SoupKit** progetto contiene codice condiviso tra l'app e alle relative estensioni. Il **Intents.intentdefinition** file è stato inserito nel **Base.lproj** directory dei **SoupKit**, e ha un **azione di compilazione** di **Contenuto**. Il processo di compilazione copia il file nel bundle dell'app basata su acronimi Chef, in cui è richiesto per l'app funzionare correttamente.

### <a name="donating-an-intent"></a>Donazione un intent

Affinché Siri suggerire un scelta rapida, innanzitutto necessario comprendere quando sono rilevanti lo scelta rapida.

Per consentire a Siri di questa comprensione, Chef MINESTRONE _donates_ intenzione di Siri ogni volta che l'utente posiziona un ordine basata su acronimi. Basato su questo donation: quando è stato donato, in cui è stato donato, i parametri che contiene: Siri apprende quando per i suggerimenti per lo scelta rapida in futuro.

**SoupChef** utilizza il `SoupOrderDataManager` classe per posizionare le donazioni.
Quando viene chiamato per inserire un ordine MINESTRONE per un utente, il `PlaceOrder` metodo chiama a sua volta [ `DonateInteraction` ](xref:Intents.INInteraction.DonateInteraction*):

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

Dopo il recupero di un intent, ne viene eseguito il wrapping un' [ `INInteraction` ](xref:Intents.INInteraction).
Il `INInteraction` viene assegnato un [`Identifier`](xref:Intents.INInteraction.Identifier*)
che corrisponde all'ID univoco dell'ordine (ciò sarà utile in un secondo momento durante l'eliminazione di donazioni preventivi che non sono più valide). Quindi, l'interazione viene donata in Siri.

La chiamata al `order.Intent` getter recuperi un `OrderSoupIntent` che rappresenta l'ordine impostando relativo `Quantity`, `Soup`, `Options`e l'immagine e una frase di chiamata da utilizzare come un suggerimento quando l'utente registra una frase di Siri associare con lo scopo di:

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

È importante rimuovere donazioni che non sono più valide in modo che Siri non rende i suggerimenti di scelta rapida inutili e confusi.

In Chef MINESTRONE, il **dal Menu configura** schermata può essere utilizzata per contrassegnare elementi come non disponibile un menu di scelta. Siri non deve non è più consigliabile un collegamento per ordinare una voce di menu non disponibile, in modo che il `RemoveDonation` metodo di `SoupMenuManager` Elimina donazioni per voci di menu che non sono più disponibili. A tale scopo:

- Ricerca degli ordini associati con la voce di menu ora disponibile.
- Cattura i relativi identificatori.
- L'eliminazione di interazioni che dispongono di tale identificatore stesso.

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

Il codice che viene eseguito quando Siri richiama un intent viene inserito in un'estensione per Intent, che può essere aggiunto come un nuovo progetto alla soluzione stessa come un'app xamarin. IOS esistente, ad esempio Chef basata su acronimi. Nel **SoupChef** soluzione, l'estensione viene chiamato **SoupChefIntents**.

#### <a name="soupchefintents-infoplist-and-entitlementsplist"></a>SoupChefIntents-Info. plist ed entitlements. plist

##### <a name="soupchefintents-infoplist"></a>SoupChefIntents-Info. plist

Il **Info. plist** nel **SoupChefIntents** progetto definisce il **identificatore del Bundle** come `com.xamarin.SoupChef.SoupChefIntents`.

Il **Info. plist** file contiene inoltre quanto segue:

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

Nell'esempio precedente **Info. plist**:

- `IntentsRestrictedWhileLocked` Elenca gli Intent che devono essere gestiti solo quando il dispositivo è sbloccato.
- `IntentsSupported` Elenca i tipi gestiti da questa estensione.
- `NSExtensionPointIdentifier` Specifica il tipo dell'estensione dell'app (vedere [documentazione di Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) per altre informazioni).
- `NSExtensionPrincipalClass` Specifica la classe che deve essere usata per gestire gli Intent supportati da questa estensione.

##### <a name="soupchefintents-entitlementsplist"></a>SoupChefIntents – entitlements. plist

Il **entitlements. plist** nel **SoupChefIntents** project ha il **i gruppi di App** funzionalità. Questa funzionalità è configurata per usare lo stesso gruppo di app come il **SoupChef** progetto:

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
```

MINESTRONE Chef rende persistenti i dati con `NSUserDefaults`. Per condividere dati tra l'app e l'estensione dell'app, fanno riferimento a stesso gruppo di app nel loro **entitlements. plist** file.

> [!NOTE]
> Il **SoupChefIntents** set di configurazione di compilazione del progetto **Entitlement personalizzati** al **entitlements. plist**.

#### <a name="handling-an-ordersoupintent-background-task"></a>La gestione di un'attività in background OrderSoupIntent

Un'estensione per Intent esegue le attività in background necessarie per creare un collegamento basato su una finalità personalizzato.

Siri chiama il [ `GetHandler` ](xref:Intents.INExtension.GetHandler*) metodo del `IntentHandler` classe (definito nel **Info. plist** come il `NSExtensionPrincipalClass`) per ottenere un'istanza di una classe che estende `OrderSoupIntentHandling`, che può essere usato per gestire un `OrderSoupIntent`:

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

`OrderSoupIntentHandler`, definito nel **SoupKit** progetto codice condiviso, implementa due metodi importanti:

- `ConfirmOrderSoup` – Conferma se l'attività associata con l'intento effettivamente deve essere eseguita
- `HandleOrderSoup` – Invia l'ordine MINESTRONE e risponde all'utente chiamando il gestore di completamento passato

#### <a name="handling-an-ordersoupintent-that-opens-the-app"></a>Gestisce un OrderSoupIntent che apre l'app

Un'app deve gestire correttamente gli Intent che non vengono eseguite in background.
Vengono gestiti nello stesso modo `NSUserActivity` tasti di scelta rapida, nella `ContinueUserActivity` metodo `AppDelegate`:

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

## <a name="providing-a-user-interface-for-a-custom-intent"></a>Fornisce un'interfaccia utente per una finalità personalizzato

Un'estensione dell'interfaccia utente Intent fornisce un'interfaccia utente personalizzata per un'estensione per Intent. Nel **SoupChef** soluzione **SoupChefIntentsUI** è un'estensione dell'interfaccia utente Intent che fornisce un'interfaccia per **SoupChefIntents**.

### <a name="soupchefintentsui--infoplist-and-entitlementsplist"></a>SoupChefIntentsUI-Info. plist ed entitlements. plist

#### <a name="soupchefintentsui-infoplist"></a>SoupChefIntentsUI-Info. plist

Il **Info. plist** nel **SoupChefIntentsUI** progetto definisce il **identificatore del Bundle** come `com.xamarin.SoupChef.SoupChefIntentsui`.

Il **Info. plist** file contiene inoltre quanto segue:

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

Nell'esempio precedente **Info. plist**:

- `IntentsSupported` indica che il `OrderSoupIntent` viene gestita da questa estensione per interfaccia utente Intent.
- `NSExtensionPointIdentifier` Specifica il tipo dell'estensione dell'app (vedere [documentazione di Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) per altre informazioni).
- `NSExtensionMainStoryboard` Specifica lo storyboard che definisce l'interfaccia principale di questa estensione

#### <a name="soupchefintentsui-entitlementsplist"></a>SoupChefIntentsUI – entitlements. plist

Il **SoupChefIntentsUI** progetto non è necessario un **entitlements. plist** file.

### <a name="creating-the-user-interface"></a>Creazione dell'interfaccia utente

Poiché il **Info. plist** per **SoupChefIntentsUI** imposta la `NSExtensionMainStoryboard` chiave `MainInterface`, il **MainInterace.storyboard** file definisce l'interfaccia per l'estensione dell'interfaccia utente Intent.

In questo storyboard, è disponibile un controller di visualizzazione singolo, di tipo **IntentViewController**. Fa riferimento a due visualizzazioni:

- **invoiceView**, di tipo `InvoiceView`
- **confirmationView**, di tipo `ConfirmOrderView`

> [!NOTE]
> Per le interfacce **invoiceView** e **confirmationView** sono definite nel **Main. Storyboard** come visualizzazioni secondarie. IOS Designer in Visual Studio per Mac e Visual Studio 2017 non fornisce il supporto per la visualizzazione o modifica delle visualizzazioni secondarie; a tale scopo, aprire **Main. Storyboard** in Interface Builder di Xcode.

`IntentViewController` implementa il [`IINUIHostedViewControlling`](xref:IntentsUI.IINUIHostedViewControlling)
interfaccia utilizzata per fornire un'interfaccia personalizzata quando si lavora con finalità di Siri. Il [`ConfigureView`](xref:IntentsUI.INUIHostedViewControlling_Extensions.ConfigureView*)
metodo viene chiamato per personalizzare l'interfaccia, la visualizzazione di conferma o la fattura, a seconda del fatto che è in corso confermata l'interazione ([`INIntentHandlingStatus.Ready`](xref:Intents.INIntentHandlingStatus)) o è stata eseguita correttamente ([ `INIntentHandlingStatus.Success`](xref:Intents.INIntentHandlingStatus)):

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
> Per altre informazioni sul `ConfigureView` metodo, presentazione WWDC 2017 di Apple, guardare [What ' s New in SiriKit](https://developer.apple.com/videos/play/wwdc2017/214/).

## <a name="creating-a-voice-shortcut"></a>Creazione di un collegamento vocale

MINESTRONE Chef fornisce un'interfaccia per assegnare un collegamento di voce per ogni ordine, permettendo così di MINESTRONE ordine con Siri. In effetti, l'interfaccia utilizzata per registrare e assegnare tasti di scelta rapida voice viene fornito da iOS e richiede poco codice personalizzato.

Nelle `OrderDetailViewController`, quando un utente tocca la tabella **aggiungere a Siri** riga, il [ `RowSelected` ](xref:UIKit.UITableViewSource.RowSelected*) metodo visualizza una schermata per aggiungere o modificare un collegamento vocale:

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

O meno è presente un collegamento a voce esistente per l'ordine attualmente visualizzata, in base `RowSelected` presenta un controller di visualizzazione di tipo [ `INUIEditVoiceShortcutViewController` ](xref:IntentsUI.INUIEditVoiceShortcutViewController) oppure [ `INUIAddVoiceShortcutViewController` ](xref:IntentsUI.INUIAddVoiceShortcutViewController).
In ogni caso `OrderDetailViewController` si configura come il controller di visualizzazione `Delegate`, motivo per cui implementa inoltre [`IINUIAddVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIAddVoiceShortcutViewControllerDelegate)
e [ `IINUIEditVoiceShortcutViewControllerDelegate` ](xref:IntentsUI.IINUIEditVoiceShortcutViewControllerDelegate).

## <a name="testing-on-device"></a>Test su dispositivo

Per eseguire MINESTRONE Chef in un dispositivo, seguire le istruzioni seguenti. Leggere anche il [nota sul provisioning automatico](#automatic-provisioning).

### <a name="app-group-app-ids-provisioning-profiles"></a>Gruppo di App, gli ID delle App, profili di Provisioning

Nel **certificati, ID e profili** sezione il [portale Apple Developer](https://developer.apple.com/), eseguire le operazioni seguenti:

- Creare un gruppo di app per la condivisione dei dati tra l'app basata su acronimi Chef e alle relative estensioni. Ad esempio: **group.com.yourcompanyname.SoupChef**

- Creare tre ID App: uno per l'app stessa, uno per l'estensione per Intent e uno per l'estensione dell'interfaccia utente Intent. Ad esempio:

    - App: **com.yourcompanyname.SoupChef**
        - A questo ID App, assegnare il SiriKit e **gruppi di App** funzionalità.

    - Estensione per Intent: **com.yourcompanyname.SoupChef.Intents**
        - A questo ID App, assegnare il **gruppi di App** funzionalità.

    - Estensione per interfaccia utente Intent: **com.yourcompanyname.SoupChef.Intentsui**
        - Questo ID App non deve alcuna funzionalità speciali.

- Dopo aver creato l'ID dell'App precedente, modificare il **gruppi di App** capacità assegnata per l'app e l'estensione per Intent, che specifica il gruppo specifico dell'app creata in precedenza.

- Creare tre nuove sviluppo i profili di provisioning, uno per ogni nuovo ID App.

- Scaricare questi profili di provisioning e fare doppio clic su ognuna di esse per installarlo. Se Visual Studio per Mac o Visual Studio 2017 è già in esecuzione, riavviarlo per assicurarsi che registra i nuovi profili di provisioning.

### <a name="editing-infoplist-entitlementsplist-and-source-code"></a>Modifiche al file Info. plist, entitlements. plist e codice sorgente

In Visual Studio per Mac o Visual Studio 2017, eseguire le operazioni seguenti:

- Aggiornare i vari **Info. plist** file nella soluzione. Impostare l'app, l'estensione per Intent ed estensione dell'interfaccia utente Intent **identificatore del Bundle** per gli ID dell'App definito in precedenza:

    - App: **com.yourcompanyname.SoupChef**
    - Estensione per Intent: **com.yourcompanyname.SoupChef.Intents**
    - Estensione per interfaccia utente Intent: **com.yourcompanyname.SoupChef.Intentsui**

- Aggiorna il **entitlements. plist** del file per il **SoupChef** progetto:
    - Per il **gruppi di App** funzionalità, impostare il gruppo al nuovo gruppo di app creato in precedenza (nell'esempio precedente, era **group.com.yourcompanyname.SoupChef**).
    - Verificare che l'opzione **SiriKit** è abilitata.

- Aggiorna il **entitlements. plist** del file per il **SoupChefIntents** progetto:
    - Per il **gruppi di App** funzionalità, impostare il gruppo al nuovo gruppo di app creato in precedenza (nell'esempio precedente, era **group.com.yourcompanyname.SoupChef**).

- Aprire infine **NSUserDefaultsHelper.cs**. Impostare il `AppGroup` variabile sul valore del nuovo gruppo di app (ad esempio, impostarlo su `group.com.yourcompanyname.SoupChef`).

### <a name="configuring-the-build-settings"></a>Configurazione delle impostazioni di compilazione

In Visual Studio per Mac o Visual Studio 2017:

- Aprire le opzioni/proprietà per il **SoupChef** progetto. Nel **firma del Bundle iOS** scheda, impostare **identità di firma** su automatico e **profilo di Provisioning** per il nuovo provisioning specifico dell'app del profilo creato in precedenza.

- Aprire le opzioni/proprietà per il **SoupChefIntents** progetto. Nel **firma del Bundle iOS** scheda, impostare **identità di firma** su automatico e **profilo di Provisioning** per gli scopi nuovo profilo di provisioning specifiche dell'estensione è stato creato in precedenza.

- Aprire le opzioni/proprietà per il **SoupChefIntentsUI** progetto. Nel **firma del Bundle iOS** scheda, impostare **identità di firma** su automatico e **profilo di Provisioning** per la nuova interfaccia utente Intent specifiche dell'estensione il profilo di provisioning creato in precedenza.

Con queste modifiche nella posizione, l'app verrà eseguita in un dispositivo iOS.

### <a name="automatic-provisioning"></a>Il provisioning automatico

Si noti che è possibile usare [provisioning automatico](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/automatic-provisioning) per eseguire molte di queste attività direttamente nell'IDE di provisioning.
Tuttavia, il provisioning automatico non configurare i gruppi di app. È necessario configurare manualmente il **entitlements. plist** file con il nome del gruppo di app da usare, visitare il portale per sviluppatori di Apple per creare il gruppo di app, assegnare a tale gruppo di app per ogni ID App creato da automatico il provisioning, rigenerare i profili di provisioning (app, estensione per Intent, estensione dell'interfaccia utente Intent) per includere il gruppo di app appena creata e scaricarli e installarli.

## <a name="related-links"></a>Collegamenti correlati

- [MINESTRONE Chef (Xamarin)](https://developer.xamarin.com/samples/monotouch/ios12/SoupChef/)
- [MINESTRONE Chef (Swift)](https://developer.apple.com/documentation/sirikit/accelerating_app_interactions_with_shortcuts?language=objc)
- [SiriKit (Apple)](https://developer.apple.com/sirikit/)
- [Introduzione a Siri di scelta rapida: WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/211/)
- [Compilazione per la voce con tasti di scelta rapida Siri – WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/214/)
- [Tasti di scelta rapida Siri nel quadrante dell'orologio Siri – WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/217/)
- [Che cosa sono le novità di SiriKit – WWDC 2017](https://developer.apple.com/videos/play/wwdc2017/214/)
- [Creare un'estensione per Intent App (Apple)](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension?language=objc)
