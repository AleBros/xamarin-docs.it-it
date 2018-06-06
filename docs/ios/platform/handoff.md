---
title: Passaggio di consegne in xamarin. IOS
description: In questo articolo viene illustrato l'uso di passaggio di consegne in un'app xamarin per trasferire le attività degli utenti tra le applicazioni in esecuzione per l'utente 's altri dispositivi.
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: ec324e8fb8327b622424311b89567608311a6a19
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787520"
---
# <a name="handoff-in-xamarinios"></a>Passaggio di consegne in xamarin. IOS

_In questo articolo viene illustrato l'uso di passaggio di consegne in un'app xamarin per trasferire le attività degli utenti tra le applicazioni in esecuzione per l'utente 's altri dispositivi._

Apple ha introdotto il passaggio di consegne in iOS 8 e OS X Yosemite (10.10) per fornire un meccanismo per l'utente da trasferire avviata in uno dei propri dispositivi, attività comune in un altro dispositivo che esegue la stessa applicazione o un'altra applicazione che supporta la stessa attività.

[![](handoff-images/handoff02.png "Un esempio di esecuzione di un'operazione di passaggio di consegne")](handoff-images/handoff02.png#lightbox)

In questo articolo verrà un rapido controllo di attivazione di attività in un'app xamarin di condivisione e riguardano il framework Handoff in dettaglio:

## <a name="about-handoff"></a>Su Handoff

Handoff (noto anche come continuità) è stato introdotto da Apple in iOS 8 e OS X Yosemite (10.10) in modo all'utente di avviare un'attività su uno dei propri dispositivi (iOS o Mac) e continuare a quella stessa attività in un altro dei propri dispositivi (come identificato dalla iClou dell'utente p Account).

Handoff espanso in iOS 9 per supportano anche nuove funzionalità di ricerca avanzate. Per ulteriori informazioni, vedere il nostro [miglioramenti della ricerca](~/ios/platform/search/index.md) documentazione.

Ad esempio, l'utente può avviare un messaggio di posta elettronica nel loro iPhone e continuare senza problemi il messaggio di posta elettronica sul loro Mac con tutte le stesse informazioni messaggio compilato e il cursore nella stessa posizione che è stato lasciato in iOS.

Qualsiasi App che condividono lo stesso _ID Team_ sono idonei per utilizzando Handoff di continuare le attività degli utenti tra App, purché queste app possono essere recapitati tramite iTunes App Store o firmato da uno sviluppatore registrato (per Mac, Enterprise o Ad Hoc App).

Qualsiasi `NSDocument` o `UIDocument` App basata su hanno automaticamente Handoff supportano predefiniti e richiede modifiche minime per supportare la consegna.

### <a name="continuing-user-activities"></a>Attività di continuazione dell'utente

Il `NSUserActivity` classe (con alcune piccole modifiche a `UIKit` e `AppKit`) fornisce il supporto per la definizione di attività dell'utente che potenzialmente possono essere riattivati in un altro dispositivi dell'utente.

Per un'attività deve essere passato un altro dispositivi dell'utente, deve essere incapsulata in un'istanza `NSUserActivity`, contrassegnato come il _attività corrente_, impostare il payload (i dati utilizzati per eseguire la continuazione) e attività devono quindi essere trasmesse a tale dispositivo.

Passaggio di consegne passa il livello minimo di informazioni per definire l'attività da completare, con i pacchetti di dati più grandi da sincronizzare tramite iCloud.

Sul dispositivo di ricezione, l'utente riceverà una notifica che un'attività è disponibile per la continuazione. Se l'utente sceglie di continuare l'attività del nuovo dispositivo, viene avviata l'app specificato (se non è già in esecuzione) e il payload dal `NSUserActivity` consente di riavviare l'attività.

[![](handoff-images/handoffinteractions.png "Una panoramica delle attività di continuazione utente")](handoff-images/handoffinteractions.png#lightbox)

Solo le app che condividono lo stesso sviluppatore ID Team e rispondano a un determinato _tipo di attività_ idonei per la continuazione. Un'applicazione definisce i tipi di attività che supporta sotto il `NSUserActivityTypes` la chiave di relativo **Info. plist** file. Pertanto, un dispositivo continuo sceglie l'app per eseguire la continuazione in base all'ID Team, il tipo di attività e, facoltativamente, il _titolo attività_.

L'applicazione ricevente utilizza le informazioni dal `NSUserActivity`del `UserInfo` dizionario utilizzato per configurare la propria interfaccia utente e ripristinare lo stato dell'attività specificata in modo che la transizione viene visualizzato come trasparente all'utente finale.

Se la continuazione richiede ulteriori informazioni che possono essere inviati in modo efficace tramite un `NSUserActivity`, la ripresa di app può inviare una chiamata per l'applicazione di origine e definire uno o più flussi per trasmettere i dati richiesti. Ad esempio, se l'attività è stata modifica di un documento di testo di grandi dimensioni con più immagini, il flusso necessario per trasferire le informazioni necessarie per continuare l'attività sul dispositivo di ricezione. Per ulteriori informazioni, vedere il [supporto per la continuazione flussi](#Supporting-Continuation-Streams) sezione riportata di seguito.

Come descritto in precedenza, `NSDocument` o `UIDocument` applicazioni automaticamente dispongono di un Handoff supporto incorporato. Per ulteriori informazioni, vedere il [supporto Handoff nelle applicazioni basate su documenti](#Supporting-Handoff-in-Document-Based-Apps) sezione riportata di seguito.

### <a name="the-nsuseractivity-class"></a>La classe NSUserActivity

La `NSUserActivity` classe è l'oggetto principale in uno scambio uniforme e viene usato per incapsulare lo stato di un'attività dell'utente che è disponibile per la continuazione. Un'applicazione crea un'istanza di una copia di `NSUserActivity` per qualsiasi attività supporta e si desidera continuare in un altro dispositivo. Ad esempio, editor di documenti creerà un'attività per ogni documento aperto. Tuttavia, solo per il documento in primo piano (visualizzato nella scheda o in primo piano finestra) è il _attività corrente_ e, pertanto, è disponibile per la continuazione.

Un'istanza di `NSUserActivity` è identificato da entrambi relativo `ActivityType` e `Title` proprietà. Il `UserInfo` proprietà dizionario viene utilizzata per fornire informazioni sullo stato dell'attività. Impostare il `NeedsSave` proprietà `true` se si desidera lazy a caricare le informazioni sullo stato tramite il `NSUserActivity`del delegato. Utilizzare il `AddUserInfoEntries` metodo per unire i nuovi dati provenienti da altri client nel `UserInfo` dizionario come richiesto per mantenere lo stato dell'attività.

### <a name="the-nsuseractivitydelegate-class"></a>La classe NSUserActivityDelegate

Il `NSUserActivityDelegate` viene utilizzato per mantenere le informazioni un `NSUserActivity`del `UserInfo` dizionario aggiornato e sincronizzato con lo stato corrente dell'attività. Quando il sistema è necessario che le informazioni dell'attività da aggiornare (ad esempio prima di continuazione in un altro dispositivo), chiama il `UserActivityWillSave` metodo del delegato.

È necessario implementare la `UserActivityWillSave` (metodo) e apportare le modifiche apportate al `NSUserActivity` (ad esempio `UserInfo`, `Title`e così via) per garantire che riflette ancora lo stato dell'attività corrente. Quando il sistema chiama il `UserActivityWillSave` (metodo), il `NeedsSave` flag verrà cancellato. Se si modificano le proprietà dei dati dell'attività, sarà necessario impostare `NeedsSave` per `true` nuovamente.

Anziché utilizzare il `UserActivityWillSave` metodo presentato in precedenza, facoltativamente è possibile configurare `UIKit` o `AppKit` gestire automaticamente l'attività dell'utente. A tale scopo, impostare l'oggetto di risponditore `UserActivity` proprietà e implementare il `UpdateUserActivityState` metodo. Vedere il [supporto Handoff in risponditori](#Supporting-Handoff-in-Responders) sezione riportata di seguito per ulteriori informazioni.

### <a name="app-framework-support"></a>Supporto di Framework applicazione

Entrambi `UIKit` (iOS) e `AppKit` (OS X) fornisce supporto incorporato per la consegna nel `NSDocument`, risponditore (`UIResponder`/`NSResponder`), e `AppDelegate` classi. Mentre ogni sistema operativo implementa Handoff in modo leggermente diverso, il meccanismo di base e le API sono uguali.

#### <a name="user-activities-in-document-based-apps"></a>Attività degli utenti nelle applicazioni basate su documenti

Basate su documenti App iOS e OS X dispone automaticamente supporto Handoff incorporato. Per attivare questo supporto, è necessario aggiungere un `NSUbiquitousDocumentUserActivityType` chiave e il valore per ogni `CFBundleDocumentTypes` voce dell'app **Info. plist** file.

Se questa chiave è presente, entrambi `NSDocument` e `UIDocument` creare automaticamente `NSUserActivity` istanze per documenti basati su iCloud del tipo specificato. È necessario fornire un tipo di attività per ogni tipo di documento che supporta l'app e i tipi di documento più possono utilizzare lo stesso tipo di attività. Entrambi `NSDocument` e `UIDocument` popolare automaticamente la `UserInfo` proprietà del `NSUserActivity` con i relativi `FileURL` valore della proprietà.

In OS X, il `NSUserActivity` gestiti da `AppKit` e associata automaticamente a risponditori diventano l'attività corrente, quando la finestra del documento diventa la finestra principale. In iOS, per `NSUserActivity` gli oggetti gestiti da `UIKit`, è necessario chiamare `BecomeCurrent` metodo in modo esplicito o il documento `UserActivity` proprietà è impostata su un `UIViewController` quando l'app passa in primo piano.

`AppKit` verrà automaticamente ripristinata `UserActivity` proprietà creata in questo modo su OS X. Questo errore si verifica se il `ContinueUserActivity` restituisce `false` o se è non implementata. In questo caso, il documento viene aperto con il `OpenDocument` metodo il `NSDocumentController` e quindi si riceverà un `RestoreUserActivityState` chiamata al metodo.

Vedere il [supporto Handoff nelle applicazioni basate su documenti](#Supporting-Handoff-in-Document-Based-Apps) sezione riportata di seguito per ulteriori informazioni.

#### <a name="user-activities-and-responders"></a>Risponditori e le attività degli utenti

Entrambi `UIKit` e `AppKit` può gestire automaticamente un'attività dell'utente se si imposta come oggetto risponditore `UserActivity` proprietà. Se lo stato è stato modificato, sarà necessario impostare il `NeedsSave` proprietà del risponditore `UserActivity` a `true`. Salva automaticamente il sistema di `UserActivity` quando necessario, dopo aver dato l'ora del risponditore per aggiornare lo stato chiamando il relativo `UpdateUserActivityState` (metodo).

Se più risponditori condividono un unico `NSUserActivity` istanza, ricevono un `UpdateUserActivityState` callback quando il sistema aggiorna l'oggetto attività utente. Il risponditore deve chiamare il `AddUserInfoEntries` per aggiornare il `NSUserActivity`del `UserInfo` dizionario in modo da riflettere lo stato dell'attività corrente a questo punto. Il `UserInfo` dizionario viene cancellato prima di ogni `UpdateUserActivityState` chiamare.

Per annullare l'associazione stessa da un'attività, è possibile impostare un risponditore relativo `UserActivity` proprietà `null`. Quando un framework di applicazione gestito `NSUserActivity` istanza non ha più associati risponditori o documenti, è automaticamente annullata.

Vedere il [supporto Handoff in risponditori](#Supporting-Handoff-in-Responders) sezione riportata di seguito per ulteriori informazioni.

#### <a name="user-activities-and-the-appdelegate"></a>Attività dell'utente e la AppDelegate

L'app `AppDelegate` è il punto di ingresso principale quando si gestiscono una continuazione uniforme. Quando l'utente risponde a una notifica di continuità, viene avviata l'app appropriata (se non è già in esecuzione) e `WillContinueUserActivityWithType` metodo il `AppDelegate` verrà chiamato. A questo punto, l'app deve informare l'utente che viene avviata la continuazione.

Il `NSUserActivity` istanza viene recapitata quando il `AppDelegate`del `ContinueUserActivity` metodo viene chiamato. A questo punto, è necessario configurare l'interfaccia utente dell'applicazione e continuare l'attività specificata.

Vedere il [implementazione Handoff](#Implementing-Handoff) sezione riportata di seguito per ulteriori informazioni.

## <a name="enabling-handoff-in-a-xamarin-app"></a>Abilitazione Handoff in un'App Xamarin

A causa dei requisiti di sicurezza imposti da Handoff, un'app xamarin che utilizza il framework Handoff deve essere configurata correttamente in entrambi il portale per sviluppatori di Apple e nel file di progetto xamarin. IOS.

Seguire questa procedura:

1. Accedere al [portale per sviluppatori Apple](http://developer.apple.com).
2. Fare clic su **certificati, profili & identificatori**.
3. Se non già stato fatto, fare clic su **identificatori** e creare un ID per l'applicazione (ad esempio `com.company.appname`), in caso contrario, modificare l'ID esistente.
4. Verificare che il **iCloud** servizio è stato verificato per l'ID specificato: 

    [![](handoff-images/provision01.png "Abilitare il servizio iCloud per l'ID specificato")](handoff-images/provision01.png#lightbox)
5. Salvare le modifiche.
4. Fare clic su **i profili di Provisioning** > **sviluppo** e creare un nuovo profilo di provisioning per l'utente di sviluppo di app: 

    [![](handoff-images/provision02.png "Creare un nuovo profilo di provisioning per l'app di sviluppo")](handoff-images/provision02.png#lightbox)
5. Scaricare e installare il nuovo profilo di provisioning o utilizzare Xcode per scaricare e installare il profilo.
6. Modificare le opzioni di progetto xamarin e assicurarsi che si sta utilizzando il profilo di provisioning appena creato: 

    [![](handoff-images/provision03.png "Selezionare il profilo di provisioning appena creato")](handoff-images/provision03.png#lightbox)
7. Successivamente, modificare il **Info. plist** e assicurarsi che si sta utilizzando l'ID dell'App che è stato utilizzato per creare il profilo di provisioning: 

    [![](handoff-images/provision04.png "Impostare l'ID App")](handoff-images/provision04.png#lightbox)
8. Scorrere verso il **modalità di Background** sezione e controllare gli elementi seguenti: 

    [![](handoff-images/provision05.png "Abilitare la modalità di background obbligatorio")](handoff-images/provision05.png#lightbox)
9. Salvare le modifiche a tutti i file.

Con queste impostazioni, l'applicazione è ora possibile accedere alle API Handoff Framework. Per informazioni dettagliate sul provisioning, vedere il nostro [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md) e [Provisioning dell'App](~/ios/get-started/installation/device-provisioning/index.md) Guide.

## <a name="implementing-handoff"></a>Implementazione Handoff

È possono continuare le attività degli utenti tra le app siano firmate con lo stesso sviluppatore ID Team e supportano lo stesso tipo di attività. L'implementazione di passaggio di consegne in un'app xamarin richiede di creare un oggetto di attività utente (in `UIKit` o `AppKit`), aggiornare lo stato dell'oggetto per tenere traccia dell'attività e continuare l'attività in un dispositivo di destinazione.

### <a name="identifying-user-activities"></a>Identificare le attività degli utenti

Il primo passaggio nell'implementazione Handoff consiste nell'identificare i tipi di attività dell'utente che supporta l'applicazione e visualizzare quali di queste attività sono buoni candidati per la continuazione in un altro dispositivo. Ad esempio: un'app ToDo potrebbe supportare la modifica di elementi come una _il tipo di attività utente_e supportano l'esplorazione dell'elenco di elementi disponibili di un altro.

Un'app è possibile creare tante attività tipi utente come sono necessari, per qualsiasi funzione che fornisce l'app. Per ogni tipo di attività utente, l'app sarà necessario rilevare quando un'attività del tipo inizia e termina e desidera mantenere informazioni sullo stato aggiornata per continuare l'attività in un altro dispositivo.

È possono continuare le attività degli utenti in qualsiasi app firmato con lo stesso ID Team senza alcun mapping uno a uno tra le app di inviare e ricezione. Ad esempio, un'applicazione specificata è possibile creare quattro tipi diversi di attività, vengono utilizzati da diversi, le singole applicazioni in un altro dispositivo. Si tratta di un problema comune tra una versione Mac dell'app (che potrebbero avere molte funzionalità e funzioni) e le app iOS, dove ogni app è più piccolo e sono specifici per un'attività specifica.

### <a name="creating-activity-type-identifiers"></a>Creazione di identificatori di tipo attività

Il _identificatore del tipo di attività_ è una stringa breve aggiunti il `NSUserActivityTypes` matrice dell'app **Info. plist** file utilizzato per identificare in modo univoco un determinato tipo di attività utente. Sarà presente una voce nella matrice per ogni attività che supporta l'app. Apple suggerisce utilizzando una notazione inversa-DNS-style per l'identificatore di tipo di attività per evitare conflitti. Ad esempio: `com.company-name.appname.activity` per app specifiche attività basate sugli o `com.company-name.activity` per le attività che è possono eseguire tra più app.

L'identificatore di tipo attività viene utilizzato durante la creazione di un `NSUserActivity` istanza per identificare il tipo di attività. Quando un'attività continua in un altro dispositivo, il tipo di attività (con l'ID dell'app Team) determina quale app da avviare per continuare l'attività.

Ad esempio, occorre creare un'app di esempio denominata **MonkeyBrowser** ([scaricare qui](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)). Questa app offre quattro schede, ognuna con un diverso open URL in una vista del browser web. L'utente sarà in grado di continuare a qualsiasi scheda di un dispositivo iOS diverso che esegue l'app.

Per creare gli identificatori di tipo attività necessarie per supportare questo comportamento, modificare il **Info. plist** file e passare il **origine** visualizzazione. Aggiungere un `NSUserActivityTypes` chiave e creare gli identificatori seguenti:

[![](handoff-images/type01.png "Il NSUserActivityTypes chiave e gli identificatori necessari nell'editor plist")](handoff-images/type01.png#lightbox)

Abbiamo creato quattro nuove attività gli identificatori dei tipi, uno per ogni scheda nell'esempio **MonkeyBrowser** app. Quando si crea la propria App, sostituire il contenuto del `NSUserActivityTypes` matrice con gli identificatori di tipo di attività specifiche per le attività, l'app supporta.

### <a name="tracking-user-activity-changes"></a>Rilevamento delle modifiche di attività utente

Quando si crea una nuova istanza di `NSUserActivity` (classe), è necessario specificare un `NSUserActivityDelegate` istanza per rilevare le modifiche allo stato dell'attività. Ad esempio, nel codice seguente consente di tenere traccia delle modifiche di stato:

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

Il `UserActivityReceivedData` metodo viene chiamato quando un flusso di continuazione ha ricevuto i dati da un dispositivo di invio. Per ulteriori informazioni, vedere il [supporto per la continuazione flussi](#Supporting-Continuation-Streams) sezione riportata di seguito.

Il `UserActivityWasContinued` metodo viene chiamato quando un altro dispositivo ha assunto da un'attività dal dispositivo corrente. A seconda del tipo di attività, ad esempio l'aggiunta di un nuovo elemento a un elenco di attività, l'applicazione potrebbe essere necessario interrompere l'attività sul dispositivo di invio.

Il `UserActivityWillSave` metodo viene chiamato prima che le modifiche all'attività vengono salvate e sincronizzate tra i dispositivi disponibili localmente. È possibile utilizzare questo metodo per apportare le modifiche dell'ultimo minuto per il `UserInfo` proprietà del `NSUserActivity` istanza prima che venga inviato.

### <a name="creating-a-nsuseractivity-instance"></a>Creazione di un'istanza di NSUserActivity

Ogni attività che si desidera fornire la possibilità di continuare in un altro dispositivo app deve essere incapsulate in un `NSUserActivity` istanza. L'app è possibile creare le attività in base alle esigenze senza che sia la natura di tali attività dipenda dalle funzionalità e le funzionalità dell'app in questione. Ad esempio, un'app di posta elettronica potrebbe creare un'attività per la creazione di un nuovo messaggio e l'altro per la lettura di un messaggio.

Per l'applicazione di esempio, un nuovo `NSUserActivity` viene creato ogni volta che l'utente immette un nuovo URL in una visualizzazione esplorazione web a schede. Il codice seguente archivia lo stato di una scheda specificata:

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

Viene creato un nuovo `NSUserActivity` utilizzando uno del tipo di attività utente creato in precedenza e fornisce un titolo leggibile per l'attività. Collega a un'istanza del `NSUserActivityDelegate` creato in precedenza per il controllo per lo stato viene modificato e informa iOS che questa attività dell'utente è l'attività corrente.

### <a name="populating-the-userinfo-dictionary"></a>La compilazione del dizionario UserInfo

Come abbiamo visto sopra, il `UserInfo` proprietà del `NSUserActivity` classe è un `NSDictionary` di coppie chiave-valore usata per definire lo stato di una determinata attività. I valori archiviati `UserInfo` deve essere uno dei seguenti tipi: `NSArray`, `NSData`, `NSDate`, `NSDictionary`, `NSNull`, `NSNumber`, `NSSet`, `NSString`, o `NSURL`. `NSURL` i valori dei dati che puntano ai documenti iCloud verranno automaticamente modificati in modo che facciano riferimento agli stessi documenti in un dispositivo di destinazione.

Nell'esempio precedente, viene creato un `NSMutableDictionary` dell'oggetto e viene popolato con una singola chiave fornendo l'URL a cui l'utente è attualmente visualizzato nella scheda specificata. Il `AddUserInfoEntries` metodo dell'attività utente è stato utilizzato per aggiornare l'attività con i dati che verranno utilizzati per ripristinare l'attività sul dispositivo di ricezione:

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple è consigliabile mantenere le informazioni inviate al minimo indispensabile per garantire che l'attività viene inviato in modo tempestivo per il dispositivo di ricezione. Se più grande vengono richiesti, ad esempio possibile modificare un'immagine associata a un documento deve essere inviato il è consigliabile utilizzare i flussi di continuazione. Vedere il [supporto per la continuazione flussi](#Supporting-Continuation-Streams) sezione riportata di seguito per ulteriori dettagli.

### <a name="continuing-an-activity"></a>Un'attività di continuazione

Comunicare informazioni automaticamente handoff locale dispositivi iOS e OS X che si trovano in prossimità fisica per il dispositivo di origine ed effettuato lo stesso account iCloud, la disponibilità di critica attività dell'utente. Se l'utente sceglie di continuare un'attività in un nuovo dispositivo, il sistema verrà avviata l'app appropriata (in base l'ID Team e il tipo di attività) e le informazioni relative `AppDelegate` che continuazione deve essere eseguito.

Prima di tutto, il `WillContinueUserActivityWithType` metodo viene chiamato in modo l'app può informare l'utente che la continuazione verrà avviato. È il codice seguente nel **appdelegate. cs** file dell'app di esempio per gestire un avvio di continuazione:

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

Nell'esempio precedente, ogni Controller vista registra con il `AppDelegate` e ha un pubblico `PreparingToHandoff` metodo che visualizza un indicatore di attività e un messaggio per consentire all'utente di sapere che l'attività sta per essere passato per il dispositivo corrente. Esempio:

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

Il `ContinueUserActivity` del `AppDelegate` verrà chiamata per continuare effettivamente l'attività specificata. Di nuovo dall'applicazione di esempio:

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

Pubblico `PerformHandoff` metodo di ogni Controller di visualizzazione effettivamente esegue il passaggio di consegne e ripristina l'attività del dispositivo corrente. Nel caso dell'esempio, viene visualizzato lo stesso URL in una scheda specifica che l'utente è stato esplorazione in un altro dispositivo. Esempio:

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

Il `ContinueUserActivity` metodo include un `UIApplicationRestorationHandler` che è possibile chiamare per documento o risponditore basato su attività ripresa. È necessario passare un `NSArray` o gli oggetti ripristinabili quando viene chiamato il gestore di ripristino. Ad esempio:

```csharp
completionHandler (new NSObject[]{Tab4});
```

Per ogni oggetto passato, il relativo `RestoreUserActivityState` metodo verrà chiamato. Ogni oggetto può quindi utilizzare i dati nella `UserInfo` dizionario per ripristinare il proprio stato. Ad esempio:

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

Per le app basate su documenti, se non si implementa il `ContinueUserActivity` metodo oppure restituisce `false`, `UIKit` o `AppKit` possibile riprendere automaticamente l'attività. Vedere il [supporto Handoff nelle applicazioni basate su documenti](#Supporting-Handoff-in-Document-Based-Apps) sezione riportata di seguito per ulteriori informazioni.

### <a name="failing-handoff-gracefully"></a>In mancanza di Handoff normalmente

Poiché Handoff si basa sulla trasmissione di informazioni tra un raccolta fortemente connesso dispositivi iOS e OS X, il processo di trasferimento può talvolta non riuscire. È consigliabile progettare all'app di gestire correttamente questi errori e informare l'utente di eventuali situazioni che si verificano.

In caso di errore, il `DidFailToContinueUserActivitiy` metodo il `AppDelegate` verrà chiamato. Ad esempio:

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

È consigliabile utilizzare il parametro fornito `NSError` per fornire informazioni all'utente sull'errore.

## <a name="native-app-to-web-browser-handoff"></a>App nativa a Handoff di Web Browser

Un utente desideri continuare un'attività senza avere un'applicazione nativa appropriata installata sul dispositivo desiderato. In alcuni casi, un'interfaccia basata sul web può fornire le funzionalità richieste e l'attività è possibile comunque continuare. Ad esempio, account di posta elettronica dell'utente potrebbe fornire un'interfaccia utente web di base per la scrittura e lettura dei messaggi.

Se l'app nativa di origine conosce l'URL per l'interfaccia web (e la sintassi necessaria per identificare l'elemento specificato viene continuata), può codificare queste informazioni nel `WebpageURL` proprietà del `NSUserActivity` istanza. Se il dispositivo di ricezione non dispone di un'app nativa appropriata per gestire la continuazione, l'interfaccia web forniti può essere chiamato.

## <a name="web-browser-to-native-app-handoff"></a>Browser Web a Handoff di App Native

Se l'utente utilizza un'interfaccia basata sul web sul dispositivo di origine e parte del dominio di attestazioni di un'applicazione nativa sul dispositivo di ricezione il `WebpageURL` proprietà, quindi il sistema utilizzerà tale app, l'handle della continuazione. Verrà visualizzato il nuovo dispositivo un `NSUserActivity` istanza che contrassegna il tipo di attività come `BrowsingWeb` e `WebpageURL` conterrà l'URL è stato visitando l'utente, il `UserInfo` dizionario sarà vuoto.

Per un'app deve far parte di questo tipo di passaggio di consegne, è necessario attestazioni il dominio in un `com.apple.developer.associated-domains` con il formato `<service>:<fully qualified domain name>` (ad esempio: `activity continuation:company.com`).

Se il dominio specificato corrisponde un `WebpageURL` valore della proprietà, Handoff scaricato un elenco di ID di app approvate dal sito Web di tale dominio. Il sito Web è necessario fornire un elenco di ID approvate in un file JSON firmato denominato **app apple-associazione sito** (ad esempio, `https://company.com/apple-app-site-association`).

Questo file JSON contiene un dizionario che specifica un elenco di app ID nel formato `<team identifier>.<bundle identifier>`. Ad esempio:

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

Per firmare il file JSON (in modo che possa corrette `Content-Type` dei `application/pkcs7-mime`), utilizzare il **Terminal** app e un `openssl` comando con un certificato e chiave emesso da un'autorità di certificazione attendibile per iOS (vedere [ http://support.apple.com/kb/ht5012 ](http://support.apple.com/kb/ht5012) per un elenco). Ad esempio:

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

Il `openssl` comando genera un file JSON firmato che si attribuisce al sito Web all'indirizzo di **app apple-associazione sito** URL. Ad esempio:

```csharp
https://example.com/apple-app-site-association.
```

L'app riceverà tutte le attività il cui `WebpageURL` dominio è relativo `com.apple.developer.associated-domains` diritto. Solo il `http` e `https` i protocolli sono di supporto, tutti gli altri protocolli genererà un'eccezione.

## <a name="supporting-handoff-in-document-based-apps"></a>Supporto Handoff nelle applicazioni basate su documenti

Come descritto in precedenza, in iOS e OS X, le applicazioni basate su documenti automaticamente supporterà Handoff di documenti basati su iCloud se l'app **Info. plist** file contiene un `CFBundleDocumentTypes` la chiave di `NSUbiquitousDocumentUserActivityType`. Ad esempio:

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

In questo esempio la stringa è un indicatore di app inversa DNS con il nome dell'attività aggiunte. Se immesso in questo modo, le voci di tipo di attività non è necessario essere ripetuto nel `NSUserActivityTypes` matrice il **Info. plist** file.

L'oggetto di attività degli utenti creati automaticamente (disponibili tramite il documento `UserActivity` proprietà) può essere fatto riferimento da altri oggetti nell'app e utilizzato per ripristinare lo stato di continuazione. Posizionare, ad esempio, per tenere traccia elemento documento e selezione. È necessario impostare questa attività `NeedsSave` proprietà `true` ogni volta che lo stato viene modificato e aggiornare il `UserInfo` dizionario il `UpdateUserActivityState` metodo.

Il `UserActivity` proprietà può essere utilizzata da qualsiasi thread ed è conforme al protocollo di chiave-valore di osservazione (KVO), pertanto può essere utilizzato per sincronizzare un documento durante lo spostamento e la disconnessione di iCloud. Il `UserActivity` verrà invalidata proprietà quando il documento viene chiuso.

Per ulteriori informazioni, vedere Apple [supporto attività utente nelle applicazioni basate su documenti](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5) documentazione.

## <a name="supporting-handoff-in-responders"></a>Supporto Handoff in risponditori

È possibile associare risponditori (ereditata da una `UIResponder` in iOS o `NSResponder` su OS X) per le attività mediante l'impostazione loro `UserActivity` proprietà. Il sistema salva automaticamente il `UserActivity` proprietà appropriata volte, la chiamata del risponditore `UpdateUserActivityState` metodo per aggiungere i dati correnti per l'oggetto attività dell'utente usando il `AddUserInfoEntriesFromDictionary` metodo.

## <a name="supporting-continuation-streams"></a>Supporto di flussi di continuazione

I casi in cui la quantità di informazioni necessarie per un'attività di continuazione non è possibile trasferire in modo efficiente per il payload Handoff iniziale. In questi casi, l'applicazione ricevente può stabilire una o più flusso tra l'elemento e l'applicazione di origine per il trasferimento dei dati.

L'applicazione di origine verrà impostato il `SupportsContinuationStreams` proprietà del `NSUserActivity` istanza `true`. Ad esempio:

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

L'applicazione ricevente può quindi chiamare il `GetContinuationStreams` metodo il `NSUserActivity` nel relativo `AppDelegate` per stabilire il flusso. Ad esempio:

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

Nel dispositivo di origine, il delegato dell'attività utente riceve i flussi chiamando il relativo `DidReceiveInputStream` metodo per fornire i dati richiesti per continuare l'attività dell'utente sul dispositivo di ripresa.

Si utilizzerà un `NSInputStream` per fornire l'accesso in sola lettura per trasmettere dati e un `NSOutputStream` forniscono l'accesso in sola lettura. I flussi devono essere utilizzati in una modalità di richiesta e risposta, in cui l'applicazione ricevente richiede più dati e fornisce l'applicazione di origine. In modo che i dati scritti nel flusso di output sul dispositivo di origine viene letto dal flusso di input nel dispositivo continuo e viceversa.

Anche in situazioni in cui è richiesta la continuazione flusso, dovrà essere presente un numero minimo dei backup e via la comunicazione tra due applicazioni.

Per ulteriori informazioni, vedere Apple [Using continuazione flussi](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13) documentazione.

## <a name="handoff-best-practices"></a>Procedure consigliate handoff

La corretta implementazione di continuazione di un'attività utente tramite Handoff trasparente richiede un'attenta progettazione a causa di tutti i vari componenti coinvolti. Apple suggerisce adottare le procedure consigliate seguenti per le app Handoff abilitato:

- Progettare le attività dell'utente per richiedere il payload più piccolo possibile per correlare lo stato dell'attività da completare. Payload maggiore, maggiore sarà il tempo impiegato la continuazione per avviare.
- Se è necessario trasferire grandi quantità di dati per la continuazione ha esito positivo, prendere in considerazione, i costi associati a un overhead di rete e configurazione.
- È comune per un'app Mac di grandi dimensioni creare le attività degli utenti che sono gestiti da diversi, più piccoli, specifiche per le app nei dispositivi iOS. Le app diverse versioni del sistema operativo devono essere progettate per funzionare correttamente insieme o continuare normalmente.
- Quando si specificano i tipi di attività, utilizzare la notazione DNS inversa per evitare conflitti. Se un'attività è specifica per un'applicazione specificata, il nome deve essere incluso nella definizione del tipo (ad esempio `com.myCompany.myEditor.editing`). Se l'attività può funziona tra più App, è possibile eliminare il nome dell'applicazione dalla definizione (ad esempio `com.myCompany.editing`).
- Se l'app deve aggiornare lo stato di un'attività dell'utente (`NSUserActivity`) impostare il `NeedsSave` proprietà `true`. Al momento appropriato, Handoff chiamerà il delegato `UserActivityWillSave` metodo sarà quindi possibile aggiornare il `UserInfo` dizionario come richiesto.
- Poiché il processo di passaggio di consegne non può inizializzare immediatamente sul dispositivo di ricezione, è opportuno implementare la `AppDelegate`del `WillContinueUserActivity` e informare l'utente che sta per avviare una continuazione.

## <a name="example-handoff-app"></a>Applicazione di esempio Handoff

Ad esempio dell'utilizzo Handoff in un'app xamarin. IOS, è stato incluso il [ **MonkeyBrowser** ](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/) app di esempio in questa Guida. L'applicazione dispone di quattro schede che l'utente può utilizzare per navigare in rete, ognuna con un tipo di attività specificata: meteo, preferito, pausa e lavoro.

Scheda quando l'utente immette un nuovo URL e le scelte di **passare** pulsante, un nuovo `NSUserActivity` viene creata per tale scheda che contiene l'URL a cui l'utente è attualmente visualizzato:

[![](handoff-images/handoff01.png "Applicazione di esempio Handoff")](handoff-images/handoff01.png#lightbox)

Se un altro dispositivi dell'utente ha il **MonkeyBrowser** app installata, viene eseguito l'accesso a iCloud utilizzando lo stesso account utente, è nella stessa rete e in prossimità del dispositivo precedente, verranno visualizzate le attività il passaggio di consegne in home schermata (nell'angolo inferiore sinistro):

[![](handoff-images/handoff02.png "L'attività Handoff visualizzata nella schermata iniziale nell'angolo inferiore sinistro")](handoff-images/handoff02.png#lightbox)

Se l'utente trascina verso l'alto nell'icona Handoff, verrà avviata l'app e l'attività dell'utente specificato nella `NSUserActivity` sulla nuova periferica verrà estesa:

[![](handoff-images/handoff03.png "L'attività dell'utente ha continuato sulla nuova periferica")](handoff-images/handoff03.png#lightbox)

Quando l'attività dell'utente è stata inviata correttamente a un altro dispositivo Apple, il dispositivo di invio `NSUserActivity` riceverà una chiamata al `UserActivityWasContinued` metodo relativo `NSUserActivityDelegate` per informarlo che l'attività dell'utente è stato trasferito a un altro dispositivo.

## <a name="summary"></a>Riepilogo

In questo articolo è stata fornita un'introduzione a Handoff framework utilizzato per un'attività utente tra più dispositivi di Apple dell'utente di continuare. Successivamente, è stato spiegato come abilitare e implementare Handoff in un'app xamarin. IOS. Infine, è stato illustrato i diversi tipi di continuazioni Handoff disponibili e le procedure consigliate di continuità.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Esempio MonkeyBrowser](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)
- [iOS 9 per gli sviluppatori](https://developer.apple.com/ios/pre-release/)
- [Novità in iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Linee guida dell'interfaccia utente HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Riferimento di Framework HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref)
