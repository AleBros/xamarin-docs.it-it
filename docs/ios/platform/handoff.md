---
title: Continuità in Novell. iOS
description: Questo articolo illustra come usare la consegna in un'app Novell. iOS per trasferire le attività degli utenti tra le app in esecuzione negli altri dispositivi dell'utente.
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 97a4a90b66e2c205ef8e9081e6989bf0f3650b16
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032401"
---
# <a name="handoff-in-xamarinios"></a>Continuità in Novell. iOS

_Questo articolo illustra come usare la consegna in un'app Novell. iOS per trasferire le attività degli utenti tra le app in esecuzione negli altri dispositivi dell'utente._

Apple ha introdotto la consegna in iOS 8 e OS X Yosemite (10,10) per fornire un meccanismo comune per l'utente per trasferire le attività avviate in uno dei loro dispositivi, a un altro dispositivo che esegue la stessa app o un'altra app che supporta la stessa attività.

[![](handoff-images/handoff02.png "An example of performing a Handoff operation")](handoff-images/handoff02.png#lightbox)

Questo articolo illustra in modo rapido come abilitare la condivisione delle attività in un'app Novell. iOS e come illustrare in dettaglio il Framework uniforme:

## <a name="about-handoff"></a>Informazioni

La consegna, nota anche come continuità, è stata introdotta da Apple in iOS 8 e OS X Yosemite (10,10) come metodo per l'avvio di un'attività su uno dei dispositivi (iOS o Mac) e continuare la stessa attività in un altro dispositivo (come identificato dal iClou dell'utente Account d).

La consegna è stata espansa in iOS 9 per supportare anche nuove funzionalità di ricerca migliorate. Per ulteriori informazioni, vedere la documentazione relativa ai miglioramenti per la [ricerca](~/ios/platform/search/index.md) .

Ad esempio, l'utente può avviare un messaggio di posta elettronica sull'iPhone e continuare senza interruzioni nel Mac, con tutte le stesse informazioni di messaggio compilate e il cursore nella stessa posizione in cui sono stati lasciati in iOS.

Tutte le app che condividono lo stesso _ID team_ sono idonee per l'uso della distribuzione per continuare le attività degli utenti nelle app, purché queste app vengano distribuite tramite iTunes App Store o firmate da uno sviluppatore registrato (per app Mac, Enterprise o ad hoc).

Tutte le app `NSDocument` o basate su `UIDocument` hanno automaticamente il supporto integrato e richiedono modifiche minime per supportare la consegna.

### <a name="continuing-user-activities"></a>Continuazione delle attività degli utenti

La classe `NSUserActivity` (insieme ad alcune piccole modifiche apportate a `UIKit` e `AppKit`) fornisce il supporto per la definizione dell'attività di un utente che può essere potenzialmente continuata in un altro dispositivo dell'utente.

Affinché un'attività venga passata a un altro dispositivo dell'utente, deve essere incapsulata in un'istanza `NSUserActivity`, contrassegnata come _attività corrente_, avere un set di payload (i dati utilizzati per eseguire la continuazione) e l'attività deve quindi essere trasmessa a il dispositivo.

La consegna passa il minimo di informazioni per definire l'attività da continuare, con la sincronizzazione di pacchetti di dati di grandi dimensioni tramite iCloud.

Sul dispositivo ricevente, l'utente riceverà una notifica che indica che un'attività è disponibile per la continuazione. Se l'utente sceglie di continuare l'attività nel nuovo dispositivo, l'app specificata viene avviata (se non è già in esecuzione) e il payload dal `NSUserActivity` viene usato per riavviare l'attività.

[![](handoff-images/handoffinteractions.png "An overview of Continuing User Activities")](handoff-images/handoffinteractions.png#lightbox)

Solo le app che condividono lo stesso ID team dello sviluppatore e rispondono a un determinato _tipo di attività_ sono idonee per la continuazione. Un'app definisce i tipi di attività supportati nella chiave `NSUserActivityTypes` del file **info. plist** . Dato questo, un dispositivo continuo sceglie l'app per eseguire la continuazione in base all'ID team, al tipo di attività e, facoltativamente, al _titolo dell'attività_.

L'app ricevente usa le informazioni del dizionario `UserInfo` di `NSUserActivity`per configurare la relativa interfaccia utente e ripristinare lo stato dell'attività specificata in modo che la transizione appaia senza interruzioni per l'utente finale.

Se per la continuazione sono necessarie altre informazioni rispetto a quelle che possono essere inviate in modo efficiente tramite una `NSUserActivity`, l'app di ripresa può inviare una chiamata all'app di origine e stabilire uno o più flussi per trasmettere i dati necessari. Se, ad esempio, l'attività stava modificando un documento di testo di grandi dimensioni con più immagini, sarebbe necessario eseguire lo streaming per trasferire le informazioni necessarie per continuare l'attività sul dispositivo di destinazione. Per ulteriori informazioni, vedere la sezione [supporto dei flussi di continuazione](#supporting-continuation-streams) di seguito.

Come indicato in precedenza, le app basate su `NSDocument` o `UIDocument` hanno automaticamente il supporto incorporato. Per ulteriori informazioni, vedere la sezione [supporto della continuità in app basate su documenti](#supporting-handoff-in-document-based-apps) .

### <a name="the-nsuseractivity-class"></a>Classe NSUserActivity

La classe `NSUserActivity` è l'oggetto primario in uno scambio con continuità e viene utilizzata per incapsulare lo stato di un'attività utente disponibile per la continuazione. Un'app creerà un'istanza di una copia di `NSUserActivity` per qualsiasi attività supportata e desideri continuare su un altro dispositivo. Ad esempio, l'editor di documenti creerà un'attività per ogni documento attualmente aperto. Tuttavia, solo il documento più anteriore (visualizzato nella finestra o nella scheda più anteriore) è l' _attività corrente_ e ne sono disponibili per la continuazione.

Un'istanza di `NSUserActivity` viene identificata dalle proprietà `ActivityType` e `Title`. La proprietà `UserInfo` Dictionary viene utilizzata per includere informazioni sullo stato dell'attività. Impostare la proprietà `NeedsSave` su `true` se si desidera caricare le informazioni sullo stato tramite il delegato `NSUserActivity`. Usare il metodo `AddUserInfoEntries` per unire i nuovi dati di altri client nel dizionario `UserInfo`, in base alle esigenze, per mantenere lo stato dell'attività.

### <a name="the-nsuseractivitydelegate-class"></a>Classe NSUserActivityDelegate

Il `NSUserActivityDelegate` viene usato per rendere aggiornate le informazioni nel dizionario `UserInfo` di un `NSUserActivity`e sincronizzate con lo stato corrente dell'attività. Quando il sistema richiede che le informazioni nell'attività vengano aggiornate (ad esempio prima della continuazione in un altro dispositivo), chiama il metodo `UserActivityWillSave` del delegato.

È necessario implementare il metodo `UserActivityWillSave` e apportare eventuali modifiche al `NSUserActivity`, ad esempio `UserInfo`, `Title`e così via, per assicurarsi che rifletta ancora lo stato dell'attività corrente. Quando il sistema chiama il metodo `UserActivityWillSave`, il flag `NeedsSave` verrà cancellato. Se si modificano le proprietà dei dati dell'attività, sarà necessario impostare di nuovo `NeedsSave` su `true`.

Anziché utilizzare il metodo `UserActivityWillSave` presentato in precedenza, è possibile disporre facoltativamente di `UIKit` o `AppKit` gestire automaticamente l'attività utente. A tale scopo, impostare la proprietà `UserActivity` dell'oggetto risponditore e implementare il metodo `UpdateUserActivityState`. Per ulteriori informazioni, vedere la sezione supporto per la consegna dei [risponditori riportata di](#supporting-handoff-in-responders) seguito.

### <a name="app-framework-support"></a>Supporto per App Framework

Sia `UIKit` (iOS) che `AppKit` (OS X) forniscono il supporto incorporato per la consegna in `NSDocument`, risponditore (`UIResponder`/`NSResponder`) e classi `AppDelegate`. Mentre ogni sistema operativo implementa la consegna in modo leggermente diverso, il meccanismo di base e le API sono gli stessi.

#### <a name="user-activities-in-document-based-apps"></a>Attività degli utenti nelle app basate su documenti

Le app iOS e OS X basate su documenti hanno automaticamente il supporto integrato. Per attivare questo supporto, è necessario aggiungere una chiave di `NSUbiquitousDocumentUserActivityType` e un valore per ogni voce `CFBundleDocumentTypes` nel file **info. plist** dell'app.

Se questa chiave è presente, sia `NSDocument` che `UIDocument` creano automaticamente `NSUserActivity` istanze per i documenti basati su iCloud del tipo specificato. È necessario fornire un tipo di attività per ogni tipo di documento supportato dall'app e più tipi di documenti possono usare lo stesso tipo di attività. Sia `NSDocument` che `UIDocument` popolano automaticamente la proprietà `UserInfo` della `NSUserActivity` con il relativo valore della proprietà `FileURL`.

In OS X, le `NSUserActivity` gestite da `AppKit` e associate ai risponditori diventano automaticamente l'attività corrente quando la finestra del documento diventa la finestra principale. In iOS, per `NSUserActivity` oggetti gestiti da `UIKit`, è necessario chiamare il metodo `BecomeCurrent` in modo esplicito o impostare la proprietà `UserActivity` del documento su un `UIViewController` quando l'app viene visualizzata in primo piano.

`AppKit` ripristinerà automaticamente qualsiasi proprietà `UserActivity` creata in questo modo in OS X. Questo errore si verifica se il metodo `ContinueUserActivity` restituisce `false` o se non è implementato. In questa situazione, il documento viene aperto con il metodo `OpenDocument` della `NSDocumentController` e riceverà quindi una chiamata al metodo `RestoreUserActivityState`.

Per ulteriori informazioni, vedere la sezione supporto per le [app basate su documenti](#supporting-handoff-in-document-based-apps) .

#### <a name="user-activities-and-responders"></a>Attività utente e risponditori

Sia `UIKit` che `AppKit` possono gestire automaticamente un'attività utente impostando la proprietà `UserActivity` dell'oggetto risponditore. Se lo stato è stato modificato, è necessario impostare la proprietà `NeedsSave` della `UserActivity` del risponditore su `true`. Il sistema salverà automaticamente il `UserActivity` quando necessario, dopo aver imposto al risponditore il tempo necessario per aggiornare lo stato chiamando il relativo metodo `UpdateUserActivityState`.

Se più risponditori condividono una sola istanza di `NSUserActivity`, ricevono un callback `UpdateUserActivityState` quando il sistema aggiorna l'oggetto attività utente. Il risponditore deve chiamare il metodo `AddUserInfoEntries` per aggiornare il dizionario `UserInfo` di `NSUserActivity`in modo da riflettere lo stato corrente dell'attività in questo punto. Il dizionario `UserInfo` viene cancellato prima di ogni chiamata di `UpdateUserActivityState`.

Per disassociarsi da un'attività, un risponditore può impostare la relativa proprietà `UserActivity` su `null`. Quando un'istanza gestita di App Framework `NSUserActivity` non ha più risponditori o documenti associati, viene invalidata automaticamente.

Per ulteriori informazioni, vedere la sezione supporto per la consegna dei [risponditori riportata di](#supporting-handoff-in-responders) seguito.

#### <a name="user-activities-and-the-appdelegate"></a>Attività utente e AppDelegate

Il `AppDelegate` dell'app è il punto di ingresso principale quando si gestisce una continuazione di continuità. Quando l'utente risponde a una notifica di consegna, viene avviata l'app appropriata (se non è già in esecuzione) e viene chiamato il metodo `WillContinueUserActivityWithType` della `AppDelegate`. A questo punto, l'app deve informare l'utente che è in corso l'avvio della continuazione.

L'istanza di `NSUserActivity` viene recapitata quando viene chiamato il metodo di `ContinueUserActivity` del `AppDelegate`. A questo punto, è necessario configurare l'interfaccia utente dell'app e continuare l'attività specificata.

Per ulteriori informazioni, vedere la sezione relativa all' [implementazione della distribuzione](#implementing-handoff) .

## <a name="enabling-handoff-in-a-xamarin-app"></a>Abilitazione della consegna in un'app Novell

A causa dei requisiti di sicurezza imposti dalla consegna, un'app Novell. iOS che usa il Framework di consegna deve essere configurata correttamente nel portale Apple Developer e nel file di progetto Novell. iOS.

Procedere come descritto di seguito:

1. Accedere al [portale Apple Developer](https://developer.apple.com).
2. Fare clic su **certificati, identificatori & profili**.
3. Se non è già stato fatto, fare clic su **identificatori** e creare un ID per l'app (ad esempio `com.company.appname`). in caso contrario, modificare l'ID esistente.
4. Verificare che il servizio **iCloud** sia stato controllato per l'ID specificato:

    [![](handoff-images/provision01.png "Enable the iCloud service for the given ID")](handoff-images/provision01.png#lightbox)
5. Salvare le modifiche.
6. Fare clic su **profili di provisioning** > **sviluppo** e creare un nuovo profilo di provisioning di sviluppo per l'app:

    [![](handoff-images/provision02.png "Create a new development provisioning profile for the app")](handoff-images/provision02.png#lightbox)
7. Scaricare e installare il nuovo profilo di provisioning o usare Xcode per scaricare e installare il profilo.
8. Modificare le opzioni del progetto Novell. iOS e assicurarsi di usare il profilo di provisioning appena creato:

    [![](handoff-images/provision03.png "Select the provisioning profile just created")](handoff-images/provision03.png#lightbox)
9. Modificare quindi il file **info. plist** e assicurarsi di usare l'ID app usato per creare il profilo di provisioning:

    [![](handoff-images/provision04.png "Set App ID")](handoff-images/provision04.png#lightbox)
10. Scorrere fino alla sezione **modalità in background** e controllare gli elementi seguenti:

    [![](handoff-images/provision05.png "Enable the required background modes")](handoff-images/provision05.png#lightbox)
11. Salvare le modifiche apportate a tutti i file.

Con queste impostazioni, l'applicazione è ora pronta per accedere alle API del Framework uniforme. Per informazioni dettagliate sul provisioning, vedere le guide all'app per il provisioning e [il](~/ios/get-started/installation/device-provisioning/index.md) provisioning dei [dispositivi](~/ios/get-started/installation/device-provisioning/index.md) .

## <a name="implementing-handoff"></a>Implementazione della distribuzione

Le attività utente possono essere proseguite tra le app firmate con lo stesso ID team di sviluppo e supportano lo stesso tipo di attività. Per implementare la distribuzione in un'app Novell. iOS, è necessario creare un oggetto attività utente (in `UIKit` o `AppKit`), aggiornare lo stato dell'oggetto per tenere traccia dell'attività e continuare l'attività in un dispositivo di destinazione.

### <a name="identifying-user-activities"></a>Identificazione delle attività degli utenti

Il primo passaggio per implementare la distribuzione consiste nell'identificare i tipi di attività degli utenti supportati dall'app e vedere quali di queste attività sono candidati idonei per la continuazione in un altro dispositivo. Ad esempio: un'app ToDo potrebbe supportare la modifica di elementi come un _tipo di attività utente_e supportare l'esplorazione dell'elenco di elementi disponibili come un altro.

Un'app può creare tutti i tipi di attività degli utenti necessari, uno per qualsiasi funzione fornita dall'app. Per ogni tipo di attività utente, l'app deve tenere traccia quando un'attività del tipo inizia e termina ed è necessario mantenere aggiornate le informazioni sullo stato per continuare l'attività in un altro dispositivo.

Le attività dell'utente possono essere continuate in qualsiasi app firmata con lo stesso ID team senza un mapping uno-a-uno tra le app di invio e ricezione. Ad esempio, una determinata app può creare quattro tipi diversi di attività, che vengono utilizzate da diverse app singole in un altro dispositivo. Si tratta di un'occorrenza comune tra una versione Mac dell'app (che potrebbe avere molte funzioni e funzioni) e app iOS, in cui ogni app è più piccola e incentrata su un'attività specifica.

### <a name="creating-activity-type-identifiers"></a>Creazione di identificatori del tipo di attività

L' _identificatore del tipo di attività_ è una stringa breve aggiunta alla matrice `NSUserActivityTypes` del file **info. plist** dell'app usato per identificare in modo univoco un tipo di attività utente specificato. Nell'array sarà presente una voce per ogni attività supportata dall'app. Apple suggerisce di usare una notazione in stile DNS inverso per l'identificatore del tipo di attività per evitare conflitti. Ad esempio: `com.company-name.appname.activity` per attività specifiche basate su app o `com.company-name.activity` per le attività che possono essere eseguite su più app.

L'identificatore del tipo di attività viene utilizzato quando si crea un'istanza di `NSUserActivity` per identificare il tipo di attività. Quando un'attività viene continuata in un altro dispositivo, il tipo di attività (insieme all'ID team dell'app) determina quale app avviare per continuare l'attività.

Ad esempio, verrà creata un'app di esempio denominata **MonkeyBrowser** ([Scarica qui](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser)). Questa app presenta quattro schede, ognuna con un URL diverso aperto in una visualizzazione del Web browser. L'utente sarà in grado di continuare qualsiasi scheda in un altro dispositivo iOS che esegue l'app.

Per creare gli identificatori del tipo di attività necessari per supportare questo comportamento, modificare il file **info. plist** e passare alla visualizzazione di **origine** . Aggiungere una chiave di `NSUserActivityTypes` e creare gli identificatori seguenti:

[![](handoff-images/type01.png "The NSUserActivityTypes key and required identifiers in the plist editor")](handoff-images/type01.png#lightbox)

Sono stati creati quattro nuovi identificatori del tipo di attività, uno per ciascuna delle schede nell'app **MonkeyBrowser** di esempio. Quando si creano app personalizzate, sostituire il contenuto della matrice di `NSUserActivityTypes` con gli identificatori del tipo di attività specifici per le attività supportate dall'app.

### <a name="tracking-user-activity-changes"></a>Rilevamento delle modifiche alle attività dell'utente

Quando si crea una nuova istanza della classe `NSUserActivity`, si specificherà un'istanza di `NSUserActivityDelegate` per tenere traccia delle modifiche apportate allo stato dell'attività. Ad esempio, il codice seguente può essere usato per tenere traccia delle modifiche dello stato:

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;

namespace MonkeyBrowse
{
    public class UserActivityDelegate : NSUserActivityDelegate
    {
        #region Constructors
        public UserActivityDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void UserActivityReceivedData (NSUserActivity userActivity, NSInputStream inputStream, NSOutputStream outputStream)
        {
            // Log
            Console.WriteLine ("User Activity Received Data: {0}", userActivity.Title);
        }

        public override void UserActivityWasContinued (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity Was Continued: {0}", userActivity.Title);
        }

        public override void UserActivityWillSave (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity will be Saved: {0}", userActivity.Title);
        }
        #endregion
    }
}
```

Il metodo `UserActivityReceivedData` viene chiamato quando un flusso di continuazione riceve dati da un dispositivo di invio. Per ulteriori informazioni, vedere la sezione [supporto dei flussi di continuazione](#supporting-continuation-streams) di seguito.

Il metodo `UserActivityWasContinued` viene chiamato quando un altro dispositivo ha rilevato un'attività dal dispositivo corrente. A seconda del tipo di attività, ad esempio l'aggiunta di un nuovo elemento a un elenco attività, l'app potrebbe dover interrompere l'attività sul dispositivo di invio.

Il metodo `UserActivityWillSave` viene chiamato prima del salvataggio e della sincronizzazione di tutte le modifiche apportate all'attività tra i dispositivi disponibili localmente. È possibile utilizzare questo metodo per apportare le modifiche dell'ultimo minuto alla proprietà `UserInfo` dell'istanza di `NSUserActivity` prima che venga inviata.

### <a name="creating-a-nsuseractivity-instance"></a>Creazione di un'istanza di NSUserActivity

Ogni attività che l'app desidera fornire la possibilità di continuare in un altro dispositivo deve essere incapsulata in un'istanza di `NSUserActivity`. L'app può creare tutte le attività necessarie e la natura di tali attività dipende dalla funzionalità e dalle funzionalità dell'app in questione. Ad esempio, un'app di posta elettronica potrebbe creare un'attività per la creazione di un nuovo messaggio e un'altra per la lettura di un messaggio.

Per l'app di esempio, viene creato un nuovo `NSUserActivity` ogni volta che l'utente immette un nuovo URL in una delle visualizzazioni del browser Web a schede. Il codice seguente archivia lo stato di una scheda specificata:

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSUserActivity UserActivity { get; set; }
...

UserActivity = new NSUserActivity (UserActivityTab1);
UserActivity.Title = "Weather Tab";
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

Crea una nuova `NSUserActivity` usando uno dei tipi di attività utente creati in precedenza e fornisce un titolo leggibile per l'attività. Si connette a un'istanza del `NSUserActivityDelegate` creato sopra per controllare le modifiche di stato e informa iOS che questa attività utente è l'attività corrente.

### <a name="populating-the-userinfo-dictionary"></a>Popolamento del dizionario UserInfo

Come è stato illustrato in precedenza, la proprietà `UserInfo` della classe `NSUserActivity` è un `NSDictionary` di coppie chiave-valore usate per definire lo stato di un'attività specificata. I valori archiviati in `UserInfo` devono essere di uno dei seguenti tipi: `NSArray`, `NSData`, `NSDate`, `NSDictionary`, `NSNull`, `NSNumber`, `NSSet`, `NSString`o `NSURL`. `NSURL` i valori dei dati che puntano ai documenti iCloud verranno automaticamente modificati in modo che puntino agli stessi documenti in un dispositivo di destinazione.

Nell'esempio precedente è stato creato un oggetto `NSMutableDictionary` e questo è stato popolato con una singola chiave che fornisce un URL che l'utente stava visualizzando nella scheda specificata. Il metodo `AddUserInfoEntries` dell'attività utente è stato utilizzato per aggiornare l'attività con i dati che verranno utilizzati per ripristinare l'attività nel dispositivo di destinazione:

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple consiglia di mantenere le informazioni inviate al minimo per garantire che l'attività venga inviata in modo tempestivo al dispositivo ricevente. Se sono necessarie informazioni più grandi, ad esempio l'invio di un'immagine collegata a un documento deve essere inviato, è necessario usare i flussi di continuazione. Per altri dettagli, vedere la sezione [supporto dei flussi di continuazione](#supporting-continuation-streams) di seguito.

### <a name="continuing-an-activity"></a>Continuazione di un'attività

La consegna comunicherà automaticamente i dispositivi iOS e OS X locali che si trovano in prossimità fisica al dispositivo di origine e che hanno effettuato l'accesso allo stesso account iCloud, della disponibilità di attività utente continue. Se l'utente sceglie di continuare un'attività in un nuovo dispositivo, il sistema avvierà l'app appropriata (in base all'ID e al tipo di attività del team) e le informazioni relative `AppDelegate` che deve essere eseguita la continuazione.

In primo luogo, viene chiamato il metodo `WillContinueUserActivityWithType` in modo che l'app possa informare l'utente che la continuazione sta per iniziare. Usare il codice seguente nel file **AppDelegate.cs** dell'app di esempio per gestire una continuazione che inizia con:

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSString UserActivityTab2 = new NSString ("com.xamarin.monkeybrowser.tab2");
public NSString UserActivityTab3 = new NSString ("com.xamarin.monkeybrowser.tab3");
public NSString UserActivityTab4 = new NSString ("com.xamarin.monkeybrowser.tab4");
...

public FirstViewController Tab1 { get; set; }
public SecondViewController Tab2 { get; set;}
public ThirdViewController Tab3 { get; set; }
public FourthViewController Tab4 { get; set; }
...

public override bool WillContinueUserActivity (UIApplication application, string userActivityType)
{
    // Report Activity
    Console.WriteLine ("Will Continue Activity: {0}", userActivityType);

    // Take action based on the user activity type
    switch (userActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Inform view that it's going to be modified
        Tab1.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Inform view that it's going to be modified
        Tab2.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Inform view that it's going to be modified
        Tab3.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Inform view that it's going to be modified
        Tab4.PreparingToHandoff ();
        break;
    }

    // Inform system we handled this
    return true;
}
```

Nell'esempio precedente, ogni controller di visualizzazione viene registrato con il `AppDelegate` e dispone di un metodo di `PreparingToHandoff` pubblico che visualizza un indicatore di attività e un messaggio che informa l'utente che l'attività sta per essere passata al dispositivo corrente. Esempio:

```csharp
private void ShowBusy(string reason) {

    // Display reason
    BusyText.Text = reason;

    //Define Animation
    UIView.BeginAnimations("Show");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0.5f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PreparingToHandoff() {
    // Inform caller
    ShowBusy ("Continuing Activity...");
}
```

Il `ContinueUserActivity` della `AppDelegate` verrà chiamato per continuare effettivamente l'attività specificata. Anche in questo caso, dall'app di esempio:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

Il metodo public `PerformHandoff` di ogni controller di visualizzazione in realtà consente di predisporre la consegna e di ripristinare l'attività sul dispositivo corrente. Nel caso dell'esempio, viene visualizzato lo stesso URL in una scheda specificata che l'utente stava esplorando in un dispositivo diverso. Esempio:

```csharp
private void HideBusy() {

    //Define Animation
    UIView.BeginAnimations("Hide");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PerformHandoff(NSUserActivity activity) {

    // Hide busy indicator
    HideBusy ();

    // Extract URL from dictionary
    var url = activity.UserInfo ["Url"].ToString ();

    // Display value
    URL.Text = url;

    // Display the give webpage
    WebView.LoadRequest(new NSUrlRequest(NSUrl.FromString(url)));

    // Save activity
    UserActivity = activity;
    UserActivity.BecomeCurrent ();

}
```

Il metodo `ContinueUserActivity` include una `UIApplicationRestorationHandler` che è possibile chiamare per la ripresa dell'attività basata su documento o risponditore. È necessario passare un `NSArray` o oggetti ripristinabili al gestore di ripristino quando viene chiamato. Esempio:

```csharp
completionHandler (new NSObject[]{Tab4});
```

Per ogni oggetto passato, viene chiamato il relativo metodo `RestoreUserActivityState`. Ogni oggetto può quindi utilizzare i dati nel dizionario `UserInfo` per ripristinare il proprio stato. Esempio:

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

Per le app basate su documenti, se non si implementa il metodo `ContinueUserActivity` o restituisce `false`, `UIKit` o `AppKit` possibile riprendere automaticamente l'attività. Per ulteriori informazioni, vedere la sezione supporto per le [app basate su documenti](#supporting-handoff-in-document-based-apps) .

### <a name="failing-handoff-gracefully"></a>Mancata consegna normale

Poiché la consegna si basa sulla trasmissione di informazioni tra una raccolta di dispositivi iOS e OS X con connessione debole, il processo di trasferimento può talvolta avere esito negativo. È consigliabile progettare l'applicazione in modo che gestisca correttamente questi errori e informare l'utente di eventuali situazioni che si verificano.

In caso di errore, verrà chiamato il metodo `DidFailToContinueUserActivitiy` del `AppDelegate`. Esempio:

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

È necessario utilizzare il `NSError` fornito per fornire all'utente informazioni sull'errore.

## <a name="native-app-to-web-browser-handoff"></a>Applicazione nativa al Web browser

È possibile che un utente desideri continuare un'attività senza che sia installata un'app nativa appropriata nel dispositivo desiderato. In alcune situazioni, un'interfaccia basata sul Web può fornire la funzionalità richiesta e l'attività può continuare. L'account di posta elettronica dell'utente, ad esempio, può fornire un'interfaccia utente basata sul Web per la composizione e la lettura dei messaggi.

Se l'app nativa di origine conosce l'URL per l'interfaccia Web (e la sintassi richiesta per l'identificazione dell'elemento specificato continua), può codificare queste informazioni nella proprietà `WebpageURL` dell'istanza di `NSUserActivity`. Se per il dispositivo di destinazione non è installata un'app nativa appropriata per gestire la continuazione, è possibile chiamare l'interfaccia Web specificata.

## <a name="web-browser-to-native-app-handoff"></a>Da Web browser a app nativa

Se l'utente usa un'interfaccia basata sul Web sul dispositivo di origine e un'app nativa sul dispositivo ricevente dichiara la parte del dominio della proprietà `WebpageURL`, il sistema userà tale app per gestire la continuazione. Il nuovo dispositivo riceverà un'istanza `NSUserActivity` che contrassegna il tipo di attività come `BrowsingWeb` e il `WebpageURL` conterrà l'URL visitato dall'utente, il dizionario `UserInfo` sarà vuoto.

Affinché un'app partecipi a questo tipo di consegna, deve richiedere il dominio in un `com.apple.developer.associated-domains` diritto con il formato `<service>:<fully qualified domain name>` (ad esempio: `activity continuation:company.com`).

Se il dominio specificato corrisponde al valore di una proprietà di `WebpageURL`, viene scaricato un elenco di ID app approvati dal sito Web in tale dominio. Il sito Web deve fornire un elenco di ID approvati in un file JSON firmato denominato **Apple-App-site-Association** , ad esempio `https://company.com/apple-app-site-association`.

Questo file JSON contiene un dizionario che specifica un elenco di ID app nel formato `<team identifier>.<bundle identifier>`. Esempio:

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

Per firmare il file JSON (in modo che abbia il `Content-Type` corretto di `application/pkcs7-mime`), usare l'app **Terminal** e un comando `openssl` con un certificato e una chiave rilasciati da un'autorità di certificazione considerata attendibile da iOS (vedere [https://support.apple.com/kb/ht5012](https://support.apple.com/kb/ht5012) per un elenco). Esempio:

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

Il comando `openssl` restituisce un file JSON firmato che viene inserito nel sito Web nell'URL **Apple-App-site-Association** . Esempio:

```csharp
https://example.com/apple-app-site-association.
```

L'app riceverà tutte le attività il cui dominio `WebpageURL` è `com.apple.developer.associated-domains` diritto. Solo i protocolli `http` e `https` sono supportati, qualsiasi altro protocollo genererà un'eccezione.

## <a name="supporting-handoff-in-document-based-apps"></a>Supporto della continuità nelle app basate su documenti

Come indicato in precedenza, in iOS e OS X le app basate su documenti supporteranno automaticamente la consegna dei documenti basati su iCloud se il file **info. plist** dell'app contiene una chiave di `CFBundleDocumentTypes` `NSUbiquitousDocumentUserActivityType`. Esempio:

```xml
<key>CFBundleDocumentTypes</key>
<array>
    <dict>
        <key>CFBundleTypeName</key>
        <string>NSRTFDPboardType</string>
        . . .
        <key>LSItemContentTypes</key>
        <array>
        <string>com.myCompany.rtfd</string>
        </array>
        . . .
        <key>NSUbiquitousDocumentUserActivityType</key>
        <string>com.myCompany.myEditor.editing</string>
    </dict>
</array>
```

In questo esempio la stringa è un designatore di app DNS inverso con il nome dell'attività accodata. Se viene immesso in questo modo, le voci del tipo di attività non devono essere ripetute nella `NSUserActivityTypes` matrice del file **info. plist** .

È possibile fare riferimento all'oggetto attività utente creato automaticamente (disponibile tramite la proprietà `UserActivity` del documento) da altri oggetti nell'app e usarlo per ripristinare lo stato nella continuazione. Ad esempio, per tenere traccia della selezione degli elementi e della posizione del documento. È necessario impostare questa attività `NeedsSave` proprietà su `true` ogni volta che lo stato cambia e aggiornare il dizionario `UserInfo` nel metodo `UpdateUserActivityState`.

La proprietà `UserActivity` può essere usata da qualsiasi thread e conforme al protocollo KVO (Key-Value osservating), in modo che possa essere usata per tenere sincronizzato un documento mentre si sposta all'interno e all'esterno di iCloud. La proprietà `UserActivity` verrà invalidata quando il documento viene chiuso.

Per ulteriori informazioni, vedere la documentazione relativa [all'attività utente di Apple nella documentazione relativa alle app basate su documenti](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5) .

## <a name="supporting-handoff-in-responders"></a>Supporto della continuità nei risponditori

È possibile associare i risponditori (ereditati da `UIResponder` su iOS o `NSResponder` su OS X) alle attività impostando le relative proprietà `UserActivity`. Il sistema salva automaticamente la proprietà `UserActivity` nei momenti appropriati, chiamando il metodo di `UpdateUserActivityState` del risponditore per aggiungere i dati correnti all'oggetto attività utente usando il metodo `AddUserInfoEntriesFromDictionary`.

## <a name="supporting-continuation-streams"></a>Supporto di flussi di continuazione

Potrebbe essere una situazione in cui la quantità di informazioni necessarie per continuare un'attività non può essere trasferita in modo efficiente dal payload di continuità iniziale. In queste situazioni, l'app ricevente può stabilire uno o più flussi tra di loro e l'app di origine per trasferire i dati.

L'app di origine imposterà la proprietà `SupportsContinuationStreams` dell'istanza di `NSUserActivity` su `true`. Esempio:

```csharp
// Create a new user Activity to support this tab
UserActivity = new NSUserActivity (ThisApp.UserActivityTab1){
    Title = "Weather Tab",
    SupportsContinuationStreams = true
};
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

L'app ricevente può quindi chiamare il metodo `GetContinuationStreams` del `NSUserActivity` nel `AppDelegate` per stabilire il flusso. Esempio:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

Sul dispositivo di origine, il delegato dell'attività utente riceve i flussi chiamando il relativo metodo di `DidReceiveInputStream` per fornire i dati richiesti per continuare l'attività dell'utente sul dispositivo di ripresa.

Si utilizzerà un `NSInputStream` per fornire l'accesso in sola lettura ai dati di flusso e un `NSOutputStream` fornire l'accesso in sola scrittura. I flussi devono essere usati in modalità di richiesta e risposta, in cui l'app ricevente richiede più dati e l'app di origine lo fornisce. In questo modo, i dati scritti nel flusso di output sul dispositivo di origine vengono letti dal flusso di input sul dispositivo continuo e viceversa.

Anche nelle situazioni in cui è richiesto il flusso di continuità, dovrebbe essere presente una comunicazione minima tra le due app.

Per ulteriori informazioni, vedere la documentazione relativa all' [utilizzo dei flussi di continuazione](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13) di Apple.

## <a name="handoff-best-practices"></a>Procedure consigliate per la consegna

L'implementazione corretta della continuazione di un'attività utente tramite la consegna richiede un'attenta progettazione a causa di tutti i vari componenti. Apple suggerisce di adottare le procedure consigliate seguenti per le app abilitate per la continuità:

- Progettare le attività dell'utente in modo da richiedere il minor carico di lavoro possibile per correlare lo stato dell'attività per proseguire. Maggiore è il payload, maggiore è il tempo necessario per avviare la continuazione.
- Se è necessario trasferire grandi quantità di dati per una continuazione corretta, prendere in considerazione i costi associati alla configurazione e all'overhead di rete.
- È comune che un'app Mac di grandi dimensioni crei attività utente gestite da diverse app più piccole e specifiche per le attività nei dispositivi iOS. Le diverse versioni dell'app e del sistema operativo devono essere progettate per funzionare correttamente insieme o con esito negativo.
- Quando si specificano i tipi di attività, usare la notazione DNS inversa per evitare conflitti. Se un'attività è specifica di una determinata app, il nome deve essere incluso nella definizione del tipo, ad esempio `com.myCompany.myEditor.editing`. Se l'attività può funzionare in più app, eliminare il nome dell'app dalla definizione, ad esempio `com.myCompany.editing`.
- Se l'app deve aggiornare lo stato di un'attività utente (`NSUserActivity`), impostare la proprietà `NeedsSave` su `true`. In momenti appropriati, la consegna chiamerà il metodo `UserActivityWillSave` del delegato, in modo che sia possibile aggiornare il dizionario `UserInfo` in base alle esigenze.
- Poiché il processo di uniformità potrebbe non essere inizializzato immediatamente sul dispositivo ricevente, è necessario implementare la `WillContinueUserActivity` del `AppDelegate`e informare l'utente che è in corso l'avvio di una continuazione.

## <a name="example-handoff-app"></a>App uniforme di esempio

Come esempio di utilizzo della consegna in un'app Novell. iOS, è stata inclusa l'app di esempio [**MonkeyBrowser**](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser) in questa guida. L'app dispone di quattro schede che l'utente può usare per esplorare il Web, ognuna con un determinato tipo di attività: Meteo, preferito, pausa caffè e lavoro.

In qualsiasi scheda, quando l'utente immette un nuovo URL e tocca il pulsante **Vai** , viene creato un nuovo `NSUserActivity` per la scheda che contiene l'URL attualmente esplorato dall'utente:

[![](handoff-images/handoff01.png "Example Handoff App")](handoff-images/handoff01.png#lightbox)

Se l'app **MonkeyBrowser** è installata in un altro dispositivo dell'utente, è stato eseguito l'accesso a iCloud con lo stesso account utente, si trova nella stessa rete e in prossimità del dispositivo precedente, l'attività di consegna verrà visualizzata nella schermata iniziale (in basso angolo sinistro):

[![](handoff-images/handoff02.png "The Handoff Activity displayed on the home screen in the lower left hand corner")](handoff-images/handoff02.png#lightbox)

Se l'utente trascina verso l'alto sull'icona di consegna, l'app verrà avviata e l'attività utente specificata nel `NSUserActivity` verrà continuata sul nuovo dispositivo:

[![](handoff-images/handoff03.png "The User Activity continued on the new device")](handoff-images/handoff03.png#lightbox)

Quando l'attività dell'utente è stata inviata correttamente a un altro dispositivo Apple, il `NSUserActivity` del dispositivo mittente riceverà una chiamata al metodo `UserActivityWasContinued` sul `NSUserActivityDelegate` per informare che l'attività dell'utente è stata trasferita correttamente a un altro dispositivo.

## <a name="summary"></a>Riepilogo

Questo articolo ha fornito un'introduzione al Framework di continuità usato per continuare l'attività di un utente tra più dispositivi Apple dell'utente. Successivamente, è stato illustrato come abilitare e implementare la consegna in un'app Novell. iOS. Infine, sono stati illustrati i diversi tipi di continuazioni di continuità disponibili e le procedure consigliate per la continuità.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Esempio MonkeyBrowser](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser)
- [iOS 9 per sviluppatori](https://developer.apple.com/ios/pre-release/)
- [Novità di iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida a HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Linee guida dell'interfaccia utente di HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Guida di riferimento a HomeKit Framework](https://developer.apple.com/library/ios/home_kit_framework_ref)
