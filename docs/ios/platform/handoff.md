---
title: Continuità in Novell. iOS
description: Questo articolo illustra come usare la consegna in un'app Novell. iOS per trasferire le attività degli utenti tra le app in esecuzione negli altri dispositivi dell'utente.
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 28c5086833ceb1dc8550e513b120f7355aa9bebe
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656571"
---
# <a name="handoff-in-xamarinios"></a>Continuità in Novell. iOS

_Questo articolo illustra come usare la consegna in un'app Novell. iOS per trasferire le attività degli utenti tra le app in esecuzione negli altri dispositivi dell'utente._

Apple ha introdotto la consegna in iOS 8 e OS X Yosemite (10,10) per fornire un meccanismo comune per l'utente per trasferire le attività avviate in uno dei loro dispositivi, a un altro dispositivo che esegue la stessa app o un'altra app che supporta la stessa attività.

[![](handoff-images/handoff02.png "Esempio di esecuzione di un'operazione di continuità")](handoff-images/handoff02.png#lightbox)

Questo articolo illustra in modo rapido come abilitare la condivisione delle attività in un'app Novell. iOS e come illustrare in dettaglio il Framework uniforme:

## <a name="about-handoff"></a>Informazioni

La consegna, nota anche come continuità, è stata introdotta da Apple in iOS 8 e OS X Yosemite (10,10) come metodo per l'avvio di un'attività su uno dei dispositivi (iOS o Mac) e continuare la stessa attività in un altro dispositivo (come identificato dal iClou dell'utente Account d).

La consegna è stata espansa in iOS 9 per supportare anche nuove funzionalità di ricerca migliorate. Per ulteriori informazioni, vedere la documentazione relativa ai miglioramenti per la [ricerca](~/ios/platform/search/index.md) .

Ad esempio, l'utente può avviare un messaggio di posta elettronica sull'iPhone e continuare senza interruzioni nel Mac, con tutte le stesse informazioni di messaggio compilate e il cursore nella stessa posizione in cui sono stati lasciati in iOS.

Tutte le app che condividono lo stesso _ID team_ sono idonee per l'uso della distribuzione per continuare le attività degli utenti nelle app, purché queste app vengano distribuite tramite iTunes App Store o firmate da uno sviluppatore registrato (per app Mac, Enterprise o ad hoc).

Tutte `NSDocument` le `UIDocument` app o basate su hanno automaticamente il supporto integrato e richiedono modifiche minime per supportare la consegna.

### <a name="continuing-user-activities"></a>Continuazione delle attività degli utenti

La `NSUserActivity` classe (insieme ad alcune piccole modifiche a `UIKit` e `AppKit`) fornisce il supporto per la definizione dell'attività di un utente che può essere potenzialmente continuata in un altro dispositivo dell'utente.

Per passare un'attività a un altro dispositivo dell'utente, è necessario incapsularla in un'istanza `NSUserActivity`di, contrassegnata come _attività corrente_, impostare il payload (i dati utilizzati per eseguire la continuazione) e l'attività deve quindi essere trasmessi al dispositivo.

La consegna passa il minimo di informazioni per definire l'attività da continuare, con la sincronizzazione di pacchetti di dati di grandi dimensioni tramite iCloud.

Sul dispositivo ricevente, l'utente riceverà una notifica che indica che un'attività è disponibile per la continuazione. Se l'utente sceglie di continuare l'attività nel nuovo dispositivo, l'app specificata viene avviata (se non è già in esecuzione) e il payload da `NSUserActivity` viene usato per riavviare l'attività.

[![](handoff-images/handoffinteractions.png "Panoramica delle attività utente continue")](handoff-images/handoffinteractions.png#lightbox)

Solo le app che condividono lo stesso ID team dello sviluppatore e rispondono a un determinato _tipo di attività_ sono idonee per la continuazione. Un'app definisce i tipi di attività che supporta sotto la `NSUserActivityTypes` chiave del file **info. plist** . Dato questo, un dispositivo continuo sceglie l'app per eseguire la continuazione in base all'ID team, al tipo di attività e, facoltativamente, al _titolo dell'attività_.

L'app ricevente usa le informazioni `NSUserActivity` `UserInfo` del dizionario di per configurare la relativa interfaccia utente e ripristinare lo stato dell'attività specificata in modo che la transizione risulti trasparente per l'utente finale.

Se per la continuazione sono necessarie altre informazioni rispetto a quelle che `NSUserActivity`possono essere inviate in modo efficiente tramite un, l'app di ripresa può inviare una chiamata all'app di origine e stabilire uno o più flussi per trasmettere i dati necessari. Se, ad esempio, l'attività stava modificando un documento di testo di grandi dimensioni con più immagini, sarebbe necessario eseguire lo streaming per trasferire le informazioni necessarie per continuare l'attività sul dispositivo di destinazione. Per ulteriori informazioni, vedere la sezione [supporto dei flussi](#supporting-continuation-streams) di continuazione di seguito.

Come indicato in precedenza `NSDocument` , `UIDocument` o le app basate hanno automaticamente il supporto integrato. Per ulteriori informazioni, vedere la sezione [supporto della continuità in app basate su documenti](#supporting-handoff-in-document-based-apps) .

### <a name="the-nsuseractivity-class"></a>Classe NSUserActivity

La `NSUserActivity` classe è l'oggetto primario in uno scambio con continuità e viene usato per incapsulare lo stato di un'attività utente disponibile per la continuazione. Un'app creerà un'istanza di una copia `NSUserActivity` di per qualsiasi attività supportata e desideri continuare su un altro dispositivo. Ad esempio, l'editor di documenti creerà un'attività per ogni documento attualmente aperto. Tuttavia, solo il documento più anteriore (visualizzato nella finestra o nella scheda più anteriore) è l' _attività corrente_ e ne sono disponibili per la continuazione.

Un'istanza di `NSUserActivity` viene identificata da entrambe `ActivityType` le `Title` proprietà e. La `UserInfo` proprietà Dictionary viene utilizzata per includere informazioni sullo stato dell'attività. Impostare la `NeedsSave` proprietà su `true` se si desidera che il metodo Lazy carichi le informazioni sullo `NSUserActivity`stato tramite il delegato di. Usare il `AddUserInfoEntries` metodo per unire i nuovi dati di altri client `UserInfo` nel dizionario, come richiesto per mantenere lo stato dell'attività.

### <a name="the-nsuseractivitydelegate-class"></a>Classe NSUserActivityDelegate

L' `NSUserActivityDelegate` oggetto viene usato per rendere aggiornate le `NSUserActivity`informazioni `UserInfo` nel dizionario di un e sincronizzate con lo stato corrente dell'attività. Quando il sistema richiede che le informazioni nell'attività vengano aggiornate, ad esempio prima della continuazione in un altro dispositivo, viene chiamato `UserActivityWillSave` il metodo del delegato.

È `UserActivityWillSave` necessario implementare il metodo e apportare le modifiche `NSUserActivity` a (ad esempio `UserInfo`, `Title`, e così via) per assicurarsi che rifletta comunque lo stato dell'attività corrente. Quando il sistema chiama il `UserActivityWillSave` metodo, il `NeedsSave` flag verrà cancellato. Se si modifica una qualsiasi delle proprietà dei dati dell'attività, sarà necessario impostare `NeedsSave` di nuovo su. `true`

Anziché utilizzare il `UserActivityWillSave` metodo illustrato in precedenza, è possibile facoltativamente disporre `UIKit` o `AppKit` gestire automaticamente l'attività utente. A tale scopo, impostare la `UserActivity` proprietà dell'oggetto risponditore e implementare il `UpdateUserActivityState` metodo. Per ulteriori informazioni, vedere la sezione supporto per la consegna dei risponditori riportata [di](#supporting-handoff-in-responders) seguito.

### <a name="app-framework-support"></a>Supporto per App Framework

Sia `UIKit` (iOS) che `AppKit` (OS X) forniscono il supporto incorporato per la `NSDocument`consegna nelle classi, risponditore (`UIResponder`/`NSResponder`) e `AppDelegate` . Mentre ogni sistema operativo implementa la consegna in modo leggermente diverso, il meccanismo di base e le API sono gli stessi.

#### <a name="user-activities-in-document-based-apps"></a>Attività degli utenti nelle app basate su documenti

Le app iOS e OS X basate su documenti hanno automaticamente il supporto integrato. Per attivare questo supporto, è necessario aggiungere una chiave e `NSUbiquitousDocumentUserActivityType` un valore per ogni `CFBundleDocumentTypes` voce nel file **info. plist** dell'app.

Se questa chiave è presente, `NSDocument` e `UIDocument` creano `NSUserActivity` automaticamente istanze per i documenti basati su iCloud del tipo specificato. È necessario fornire un tipo di attività per ogni tipo di documento supportato dall'app e più tipi di documenti possono usare lo stesso tipo di attività. Sia `NSDocument` `UserInfo` che `UIDocument` popolano`FileURL` automaticamente la proprietà di conilvaloredellaproprietà.`NSUserActivity`

In OS X, il `NSUserActivity` gestito da `AppKit` e associato ai risponditori diventano automaticamente l'attività corrente quando la finestra del documento diventa la finestra principale. In iOS, per `NSUserActivity` gli oggetti gestiti `UIKit`da, è necessario chiamare `BecomeCurrent` il `UserActivity` metodo in modo esplicito o impostare la proprietà del documento `UIViewController` su un oggetto quando l'app viene visualizzata in primo piano.

`AppKit`ripristinerà automaticamente qualsiasi `UserActivity` proprietà creata in questo modo in OS X. Questo errore si verifica `ContinueUserActivity` se il `false` metodo restituisce o se non è implementato. In questa situazione, il documento viene aperto con il `OpenDocument` metodo `NSDocumentController` di e riceve una `RestoreUserActivityState` chiamata al metodo.

Per ulteriori informazioni, vedere la sezione supporto per le [app basate su documenti](#supporting-handoff-in-document-based-apps) .

#### <a name="user-activities-and-responders"></a>Attività utente e risponditori

Sia `UIKit` che `UserActivity` possono gestire automaticamente un'attività utente se la si imposta come proprietà di un oggetto risponditore. `AppKit` Se lo stato è stato modificato, è necessario impostare la `NeedsSave` proprietà del risponditore `UserActivity` su `true`. Il sistema salverà automaticamente l' `UserActivity` oggetto quando necessario, dopo aver conseguito il tempo di risposta per aggiornare lo stato `UpdateUserActivityState` chiamando il relativo metodo.

Se più risponditori condividono una sola `NSUserActivity` istanza, ricevono un `UpdateUserActivityState` callback quando il sistema aggiorna l'oggetto attività utente. Il risponditore deve chiamare il `AddUserInfoEntries` metodo per aggiornare `UserInfo` il `NSUserActivity`dizionario di per riflettere lo stato dell'attività corrente a questo punto. Il `UserInfo` dizionario viene cancellato prima di `UpdateUserActivityState` ogni chiamata.

Per disassociarsi da un'attività, un risponditore può impostare la `UserActivity` relativa proprietà `null`su. Quando un'istanza gestita `NSUserActivity` di App Framework non dispone di più risponditori o documenti associati, viene invalidata automaticamente.

Per ulteriori informazioni, vedere la sezione supporto per la consegna dei risponditori riportata [di](#supporting-handoff-in-responders) seguito.

#### <a name="user-activities-and-the-appdelegate"></a>Attività utente e AppDelegate

L'app è `AppDelegate` il punto di ingresso principale durante la gestione di una continuazione di continuità. Quando l'utente risponde a una notifica di continuità, viene avviata l'app appropriata (se non è già in esecuzione `WillContinueUserActivityWithType` ) e viene `AppDelegate` chiamato il metodo di. A questo punto, l'app deve informare l'utente che è in corso l'avvio della continuazione.

L' `NSUserActivity` istanza viene recapitata `AppDelegate`quando `ContinueUserActivity` viene chiamato il metodo di. A questo punto, è necessario configurare l'interfaccia utente dell'app e continuare l'attività specificata.

Per ulteriori informazioni, vedere la sezione relativa all' [implementazione della distribuzione](#implementing-handoff) .

## <a name="enabling-handoff-in-a-xamarin-app"></a>Abilitazione della consegna in un'app Novell

A causa dei requisiti di sicurezza imposti dalla consegna, un'app Novell. iOS che usa il Framework di consegna deve essere configurata correttamente nel portale Apple Developer e nel file di progetto Novell. iOS.

Seguire questa procedura:

1. Accedere al [portale Apple Developer](https://developer.apple.com).
2. Fare clic su **certificati, identificatori & profili**.
3. Se non è già stato fatto, fare clic su identificatori e creare un ID per l'app (ad `com.company.appname`esempio), altrimenti modificare l'ID esistente.
4. Verificare che il servizio **iCloud** sia stato controllato per l'ID specificato:

    [![](handoff-images/provision01.png "Abilita il servizio iCloud per l'ID specificato")](handoff-images/provision01.png#lightbox)
5. Salvare le modifiche.
6. Fare clic su provisioning dei **profili** > **sviluppo** e creare un nuovo profilo di provisioning di sviluppo per l'app:

    [![](handoff-images/provision02.png "Creare un nuovo profilo di provisioning di sviluppo per l'app")](handoff-images/provision02.png#lightbox)
7. Scaricare e installare il nuovo profilo di provisioning o usare Xcode per scaricare e installare il profilo.
8. Modificare le opzioni del progetto Novell. iOS e assicurarsi di usare il profilo di provisioning appena creato:

    [![](handoff-images/provision03.png "Selezionare il profilo di provisioning appena creato")](handoff-images/provision03.png#lightbox)
9. Modificare quindi il file **info. plist** e assicurarsi di usare l'ID app usato per creare il profilo di provisioning:

    [![](handoff-images/provision04.png "Imposta ID app")](handoff-images/provision04.png#lightbox)
10. Scorrere fino alla sezione **modalità in background** e controllare gli elementi seguenti:

    [![](handoff-images/provision05.png "Abilitare le modalità di sfondo richieste")](handoff-images/provision05.png#lightbox)
11. Salvare le modifiche apportate a tutti i file.

Con queste impostazioni, l'applicazione è ora pronta per accedere alle API del Framework uniforme. Per informazioni dettagliate sul provisioning, vedere le guide all'app per il provisioning e [il](~/ios/get-started/installation/device-provisioning/index.md) provisioning dei [dispositivi](~/ios/get-started/installation/device-provisioning/index.md) .

## <a name="implementing-handoff"></a>Implementazione della distribuzione

Le attività utente possono essere proseguite tra le app firmate con lo stesso ID team di sviluppo e supportano lo stesso tipo di attività. Per implementare la consegna in un'app Novell. iOS, è necessario creare un oggetto attività utente ( `UIKit` in `AppKit`o), aggiornare lo stato dell'oggetto per tenere traccia dell'attività e continuare l'attività in un dispositivo di destinazione.

### <a name="identifying-user-activities"></a>Identificazione delle attività degli utenti

Il primo passaggio per implementare la distribuzione consiste nell'identificare i tipi di attività degli utenti supportati dall'app e vedere quali di queste attività sono candidati idonei per la continuazione in un altro dispositivo. Ad esempio: un'app ToDo potrebbe supportare la modifica di elementi come un _tipo di attività utente_e supportare l'esplorazione dell'elenco di elementi disponibili come un altro.

Un'app può creare tutti i tipi di attività degli utenti necessari, uno per qualsiasi funzione fornita dall'app. Per ogni tipo di attività utente, l'app deve tenere traccia quando un'attività del tipo inizia e termina ed è necessario mantenere aggiornate le informazioni sullo stato per continuare l'attività in un altro dispositivo.

Le attività dell'utente possono essere continuate in qualsiasi app firmata con lo stesso ID team senza un mapping uno-a-uno tra le app di invio e ricezione. Ad esempio, una determinata app può creare quattro tipi diversi di attività, che vengono utilizzate da diverse app singole in un altro dispositivo. Si tratta di un'occorrenza comune tra una versione Mac dell'app (che potrebbe avere molte funzioni e funzioni) e app iOS, in cui ogni app è più piccola e incentrata su un'attività specifica.

### <a name="creating-activity-type-identifiers"></a>Creazione di identificatori del tipo di attività

L' _identificatore del tipo di attività_ è una stringa breve aggiunta `NSUserActivityTypes` alla matrice del file **info. plist** dell'app usato per identificare in modo univoco un tipo di attività utente specifico. Nell'array sarà presente una voce per ogni attività supportata dall'app. Apple suggerisce di usare una notazione in stile DNS inverso per l'identificatore del tipo di attività per evitare conflitti. Ad esempio, `com.company-name.appname.activity` per attività specifiche basate su app `com.company-name.activity` o per attività che possono essere eseguite su più app.

L'identificatore del tipo di attività viene utilizzato durante `NSUserActivity` la creazione di un'istanza di per identificare il tipo di attività. Quando un'attività viene continuata in un altro dispositivo, il tipo di attività (insieme all'ID team dell'app) determina quale app avviare per continuare l'attività.

Ad esempio, verrà creata un'app di esempio denominata **MonkeyBrowser** ([Scarica qui](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser)). Questa app presenta quattro schede, ognuna con un URL diverso aperto in una visualizzazione del Web browser. L'utente sarà in grado di continuare qualsiasi scheda in un altro dispositivo iOS che esegue l'app.

Per creare gli identificatori del tipo di attività necessari per supportare questo comportamento, modificare il file **info. plist** e passare alla visualizzazione di **origine** . Aggiungere una `NSUserActivityTypes` chiave e creare gli identificatori seguenti:

[![](handoff-images/type01.png "La chiave NSUserActivityTypes e gli identificatori obbligatori nell'editor plist")](handoff-images/type01.png#lightbox)

Sono stati creati quattro nuovi identificatori del tipo di attività, uno per ciascuna delle schede nell'app **MonkeyBrowser** di esempio. Quando si creano app personalizzate, sostituire il contenuto della `NSUserActivityTypes` matrice con gli identificatori del tipo di attività specifici per le attività supportate dall'app.

### <a name="tracking-user-activity-changes"></a>Rilevamento delle modifiche alle attività dell'utente

Quando si crea una nuova istanza della `NSUserActivity` classe, si specificherà un' `NSUserActivityDelegate` istanza di per tenere traccia delle modifiche apportate allo stato dell'attività. Ad esempio, il codice seguente può essere usato per tenere traccia delle modifiche dello stato:

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

Il `UserActivityReceivedData` metodo viene chiamato quando un flusso di continuazione riceve i dati da un dispositivo di invio. Per ulteriori informazioni, vedere la sezione [supporto dei flussi](#supporting-continuation-streams) di continuazione di seguito.

Il `UserActivityWasContinued` metodo viene chiamato quando un altro dispositivo ha rilevato un'attività dal dispositivo corrente. A seconda del tipo di attività, ad esempio l'aggiunta di un nuovo elemento a un elenco attività, l'app potrebbe dover interrompere l'attività sul dispositivo di invio.

Il `UserActivityWillSave` metodo viene chiamato prima che qualsiasi modifica all'attività venga salvata e sincronizzata tra dispositivi disponibili localmente. È possibile utilizzare questo metodo per apportare le modifiche `UserInfo` `NSUserActivity` dell'ultimo minuto alla proprietà dell'istanza prima che venga inviata.

### <a name="creating-a-nsuseractivity-instance"></a>Creazione di un'istanza di NSUserActivity

Ogni attività che l'app desidera fornire la possibilità di continuare su un altro dispositivo deve essere incapsulata in `NSUserActivity` un'istanza. L'app può creare tutte le attività necessarie e la natura di tali attività dipende dalla funzionalità e dalle funzionalità dell'app in questione. Ad esempio, un'app di posta elettronica potrebbe creare un'attività per la creazione di un nuovo messaggio e un'altra per la lettura di un messaggio.

Per l'app di esempio, viene `NSUserActivity` creato un nuovo ogni volta che l'utente immette un nuovo URL in una delle visualizzazioni del browser Web a schede. Il codice seguente archivia lo stato di una scheda specificata:

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

Crea un nuovo `NSUserActivity` oggetto usando uno dei tipi di attività utente creati in precedenza e fornisce un titolo leggibile per l'attività. Si connette a un'istanza del `NSUserActivityDelegate` creato sopra per controllare le modifiche di stato e informa iOS che questa attività utente è l'attività corrente.

### <a name="populating-the-userinfo-dictionary"></a>Popolamento del dizionario UserInfo

Come è stato illustrato in precedenza, `UserInfo` la proprietà `NSUserActivity` della classe è un `NSDictionary` oggetto di coppie chiave-valore usate per definire lo stato di un'attività specificata. I valori archiviati in `UserInfo` devono essere di uno dei seguenti tipi: `NSArray`, `NSData` `NSDate`,, `NSDictionary`, `NSNull`, `NSNumber`, `NSSet`, `NSString`o `NSURL`. `NSURL`i valori dei dati che puntano ai documenti iCloud verranno regolati automaticamente in modo che puntino agli stessi documenti in un dispositivo di destinazione.

Nell'esempio precedente è stato creato un `NSMutableDictionary` oggetto che è stato popolato con una singola chiave che fornisce un URL che l'utente stava visualizzando nella scheda specificata. Il `AddUserInfoEntries` metodo dell'attività utente è stato utilizzato per aggiornare l'attività con i dati che verranno utilizzati per ripristinare l'attività sul dispositivo di destinazione:

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple consiglia di mantenere le informazioni inviate al minimo per garantire che l'attività venga inviata in modo tempestivo al dispositivo ricevente. Se sono necessarie informazioni più grandi, ad esempio l'invio di un'immagine collegata a un documento deve essere inviato, è necessario usare i flussi di continuazione. Per altri dettagli, vedere la sezione [supporto dei flussi](#supporting-continuation-streams) di continuazione di seguito.

### <a name="continuing-an-activity"></a>Continuazione di un'attività

La consegna comunicherà automaticamente i dispositivi iOS e OS X locali che si trovano in prossimità fisica al dispositivo di origine e che hanno effettuato l'accesso allo stesso account iCloud, della disponibilità di attività utente continue. Se l'utente sceglie di continuare un'attività in un nuovo dispositivo, il sistema avvierà l'app appropriata (in base all'ID e al tipo di attività del team) e le `AppDelegate` informazioni relative al fatto che la continuazione deve essere eseguita.

Prima di tutto `WillContinueUserActivityWithType` , viene chiamato il metodo in modo che l'app possa informare l'utente che la continuazione sta per iniziare. Usare il codice seguente nel file **AppDelegate.cs** dell'app di esempio per gestire una continuazione che inizia con:

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

Nell'esempio precedente, ogni controller di visualizzazione viene registrato con `AppDelegate` e ha un metodo `PreparingToHandoff` pubblico che visualizza un indicatore di attività e un messaggio che informa l'utente che l'attività sta per essere passata al dispositivo corrente. Esempio:

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

`ContinueUserActivity` Il`AppDelegate` di verrà chiamato per continuare effettivamente l'attività specificata. Anche in questo caso, dall'app di esempio:

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

Il metodo `PerformHandoff` pubblico di ogni controller di visualizzazione in realtà consente di predisporre la consegna e di ripristinare l'attività sul dispositivo corrente. Nel caso dell'esempio, viene visualizzato lo stesso URL in una scheda specificata che l'utente stava esplorando in un dispositivo diverso. Esempio:

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

Il `ContinueUserActivity` metodo include un `UIApplicationRestorationHandler` oggetto che è possibile chiamare per la ripresa dell'attività basata su documento o risponditore. È necessario passare un `NSArray` oggetto o oggetti ripristinabili al gestore di ripristino quando viene chiamato. Ad esempio:

```csharp
completionHandler (new NSObject[]{Tab4});
```

Per ogni oggetto passato, viene `RestoreUserActivityState` chiamato il relativo metodo. Ogni oggetto può quindi utilizzare i dati nel `UserInfo` dizionario per ripristinare il proprio stato. Ad esempio:

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

Per le app basate su documenti, se non si implementa il `ContinueUserActivity` metodo o `UIKit` restituisce `false` `AppKit` oppure è possibile riprendere automaticamente l'attività. Per ulteriori informazioni, vedere la sezione supporto per le [app basate su documenti](#supporting-handoff-in-document-based-apps) .

### <a name="failing-handoff-gracefully"></a>Mancata consegna normale

Poiché la consegna si basa sulla trasmissione di informazioni tra una raccolta di dispositivi iOS e OS X con connessione debole, il processo di trasferimento può talvolta avere esito negativo. È consigliabile progettare l'applicazione in modo che gestisca correttamente questi errori e informare l'utente di eventuali situazioni che si verificano.

In caso di errore, `DidFailToContinueUserActivitiy` `AppDelegate` verrà chiamato il metodo di. Ad esempio:

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

È necessario utilizzare l'oggetto `NSError` fornito per fornire all'utente informazioni sull'errore.

## <a name="native-app-to-web-browser-handoff"></a>Applicazione nativa al Web browser

È possibile che un utente desideri continuare un'attività senza che sia installata un'app nativa appropriata nel dispositivo desiderato. In alcune situazioni, un'interfaccia basata sul Web può fornire la funzionalità richiesta e l'attività può continuare. L'account di posta elettronica dell'utente, ad esempio, può fornire un'interfaccia utente basata sul Web per la composizione e la lettura dei messaggi.

Se l'app nativa di origine conosce l'URL per l'interfaccia Web (e la sintassi richiesta per l'identificazione dell'elemento specificato viene continuata), può codificare queste informazioni nella `WebpageURL` proprietà `NSUserActivity` dell'istanza. Se per il dispositivo di destinazione non è installata un'app nativa appropriata per gestire la continuazione, è possibile chiamare l'interfaccia Web specificata.

## <a name="web-browser-to-native-app-handoff"></a>Da Web browser a app nativa

Se l'utente usa un'interfaccia basata sul Web sul dispositivo di origine e un'app nativa sul dispositivo ricevente dichiara la parte di dominio della `WebpageURL` proprietà, il sistema userà tale app per gestire la continuazione. Il nuovo dispositivo riceverà un' `NSUserActivity` istanza che contrassegna il tipo di `BrowsingWeb` attività e `WebpageURL` conterrà l'URL visitato dall'utente, il `UserInfo` dizionario sarà vuoto.

Affinché un'app partecipi a questo tipo di consegna, deve rivendicare il dominio in un `com.apple.developer.associated-domains` diritto con il formato `<service>:<fully qualified domain name>` (ad esempio: `activity continuation:company.com`).

Se il dominio specificato corrisponde al `WebpageURL` valore di una proprietà, la consegna viene scaricata da un elenco di ID app approvati dal sito Web in tale dominio. Il sito Web deve fornire un elenco di ID approvati in un file JSON firmato denominato **Apple-App-site-Association** ( `https://company.com/apple-app-site-association`ad esempio,).

Questo file JSON contiene un dizionario che specifica un elenco di ID app nel form `<team identifier>.<bundle identifier>`. Ad esempio:

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

Per firmare il file JSON (in modo che sia corretto `Content-Type` `application/pkcs7-mime`), usare l'app **Terminal** e un `openssl` comando con un certificato e una chiave rilasciati da un'autorità di certificazione considerata attendibile da iOS (vedere [https://support.apple.com/kb/ht5012](https://support.apple.com/kb/ht5012) per un elenco). Ad esempio:

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

Il `openssl` comando restituisce un file JSON firmato che viene inserito nel sito Web nell'URL **Apple-App-site-Association** . Ad esempio:

```csharp
https://example.com/apple-app-site-association.
```

L'app riceverà tutte le attività `WebpageURL` il cui dominio è `com.apple.developer.associated-domains` a suo diritto. Solo i `http` protocolli `https` e sono supportati, qualsiasi altro protocollo genererà un'eccezione.

## <a name="supporting-handoff-in-document-based-apps"></a>Supporto della continuità nelle app basate su documenti

Come indicato in precedenza, in iOS e OS X le app basate su documenti supporteranno automaticamente la consegna dei documenti basati su iCloud se il file **info. plist** dell'app `CFBundleDocumentTypes` contiene una `NSUbiquitousDocumentUserActivityType`chiave di. Ad esempio:

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

In questo esempio la stringa è un designatore di app DNS inverso con il nome dell'attività accodata. Se è stato immesso in questo modo, le voci del tipo di attività non devono `NSUserActivityTypes` essere ripetute nella matrice del file **info. plist** .

È possibile fare riferimento all'oggetto attività utente creato automaticamente (disponibile tramite `UserActivity` la proprietà del documento) da altri oggetti nell'app e usarlo per ripristinare lo stato durante la continuazione. Ad esempio, per tenere traccia della selezione degli elementi e della posizione del documento. È necessario impostare questa proprietà attività `NeedsSave` su `true` ogni volta che lo stato viene modificato e `UserInfo` `UpdateUserActivityState` aggiornare il dizionario nel metodo.

La `UserActivity` proprietà può essere usata da qualsiasi thread e conforme al protocollo KVO (Key-Value osservating), in modo che possa essere usata per tenere sincronizzato un documento mentre si sposta all'interno e all'esterno di iCloud. La `UserActivity` proprietà verrà invalidata quando il documento viene chiuso.

Per ulteriori informazioni, vedere la documentazione relativa [all'attività utente di Apple nella documentazione relativa alle app basate su documenti](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5) .

## <a name="supporting-handoff-in-responders"></a>Supporto della continuità nei risponditori

È possibile associare i risponditori (ereditati `UIResponder` da su iOS `NSResponder` o in OS X) alle attività impostando `UserActivity` le relative proprietà. Il sistema salva automaticamente la `UserActivity` proprietà nei momenti appropriati, chiamando il `UpdateUserActivityState` metodo del risponditore per aggiungere i dati correnti all'oggetto attività utente usando il `AddUserInfoEntriesFromDictionary` metodo.

## <a name="supporting-continuation-streams"></a>Supporto di flussi di continuazione

Potrebbe essere una situazione in cui la quantità di informazioni necessarie per continuare un'attività non può essere trasferita in modo efficiente dal payload di continuità iniziale. In queste situazioni, l'app ricevente può stabilire uno o più flussi tra di loro e l'app di origine per trasferire i dati.

L'app di origine imposterà `SupportsContinuationStreams` la proprietà `NSUserActivity` dell'istanza su `true`. Ad esempio:

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

L'app ricevente può quindi chiamare `GetContinuationStreams` il metodo di `NSUserActivity` nell' `AppDelegate` oggetto per stabilire il flusso. Ad esempio:

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

Sul dispositivo di `DidReceiveInputStream` origine, il delegato dell'attività utente riceve i flussi chiamando il metodo per fornire i dati richiesti per continuare l'attività dell'utente sul dispositivo di ripresa.

Si userà un `NSInputStream` per fornire l'accesso in sola lettura ai dati di flusso e un `NSOutputStream` per fornire l'accesso in sola scrittura. I flussi devono essere usati in modalità di richiesta e risposta, in cui l'app ricevente richiede più dati e l'app di origine lo fornisce. In questo modo, i dati scritti nel flusso di output sul dispositivo di origine vengono letti dal flusso di input sul dispositivo continuo e viceversa.

Anche nelle situazioni in cui è richiesto il flusso di continuità, dovrebbe essere presente una comunicazione minima tra le due app.

Per ulteriori informazioni, vedere la documentazione relativa all' [utilizzo dei flussi](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13) di continuazione di Apple.

## <a name="handoff-best-practices"></a>Procedure consigliate per la consegna

L'implementazione corretta della continuazione di un'attività utente tramite la consegna richiede un'attenta progettazione a causa di tutti i vari componenti. Apple suggerisce di adottare le procedure consigliate seguenti per le app abilitate per la continuità:

- Progettare le attività dell'utente in modo da richiedere il minor carico di lavoro possibile per correlare lo stato dell'attività per proseguire. Maggiore è il payload, maggiore è il tempo necessario per avviare la continuazione.
- Se è necessario trasferire grandi quantità di dati per una continuazione corretta, prendere in considerazione i costi associati alla configurazione e all'overhead di rete.
- È comune che un'app Mac di grandi dimensioni crei attività utente gestite da diverse app più piccole e specifiche per le attività nei dispositivi iOS. Le diverse versioni dell'app e del sistema operativo devono essere progettate per funzionare correttamente insieme o con esito negativo.
- Quando si specificano i tipi di attività, usare la notazione DNS inversa per evitare conflitti. Se un'attività è specifica di una determinata app, il nome deve essere incluso nella definizione del tipo (ad esempio `com.myCompany.myEditor.editing`). Se l'attività può funzionare in più app, eliminare il nome dell'app dalla definizione (ad esempio `com.myCompany.editing`).
- Se l'app deve aggiornare lo stato di un'attività utente (`NSUserActivity`), impostare la `NeedsSave` proprietà su. `true` In momenti appropriati, la consegna chiamerà il `UserActivityWillSave` metodo del delegato in modo che sia possibile aggiornare il `UserInfo` dizionario come richiesto.
- Poiché il processo `AppDelegate`di `WillContinueUserActivity` consegna potrebbe non essere inizializzato immediatamente sul dispositivo ricevente, è necessario implementare e informare l'utente che la continuazione sta per essere avviata.

## <a name="example-handoff-app"></a>App uniforme di esempio

Come esempio di utilizzo della consegna in un'app Novell. iOS, è stata inclusa l'app di esempio [**MonkeyBrowser**](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser) in questa guida. L'app dispone di quattro schede che l'utente può usare per esplorare il Web, ognuna con un determinato tipo di attività: Meteo, preferito, pausa caffè e lavoro.

In qualsiasi scheda, quando l'utente immette un nuovo URL e tocca il pulsante **Vai** , viene creato `NSUserActivity` un nuovo oggetto per la scheda che contiene l'URL attualmente esplorato dall'utente:

[![](handoff-images/handoff01.png "App uniforme di esempio")](handoff-images/handoff01.png#lightbox)

Se l'app **MonkeyBrowser** è installata in un altro dispositivo dell'utente, è stato eseguito l'accesso a iCloud con lo stesso account utente, si trova nella stessa rete e in prossimità del dispositivo precedente, l'attività di consegna verrà visualizzata nella schermata iniziale (in basso angolo sinistro):

[![](handoff-images/handoff02.png "L'attività di continuità visualizzata nella schermata iniziale nell'angolo in basso a sinistra")](handoff-images/handoff02.png#lightbox)

Se l'utente trascina verso l'alto sull'icona di consegna, l'app verrà avviata e l'attività utente specificata in `NSUserActivity` verrà continuata sul nuovo dispositivo:

[![](handoff-images/handoff03.png "L'attività dell'utente continua sul nuovo dispositivo")](handoff-images/handoff03.png#lightbox)

Quando l'attività dell'utente è stata inviata correttamente a un altro dispositivo Apple, il dispositivo `NSUserActivity` mittente riceve una chiamata `UserActivityWasContinued` al metodo sul relativo `NSUserActivityDelegate` per indicare che l'attività dell'utente è stata trasferita correttamente a un'altra. dispositivo.

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
