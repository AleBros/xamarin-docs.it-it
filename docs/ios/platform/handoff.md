---
title: Passaggio di consegne in xamarin. IOS
description: Questo articolo lavorando Handoff in un'app xamarin. IOS per il trasferimento include altri dispositivi dell'attività utente tra le App in esecuzione per l'utente.
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 1a5cc9f06fdca5944a9a3201ac15d63ca7f15453
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61385584"
---
# <a name="handoff-in-xamarinios"></a>Passaggio di consegne in xamarin. IOS

_Questo articolo lavorando Handoff in un'app xamarin. IOS per il trasferimento include altri dispositivi dell'attività utente tra le App in esecuzione per l'utente._

Apple ha introdotto Handoff in iOS 8 e OS X Yosemite (10.10) per fornire un meccanismo per l'utente da trasferire le attività di avvio in uno dei propri dispositivi, comune a un altro dispositivo che esegue la stessa app o un'altra app che supporta la stessa attività.

[![](handoff-images/handoff02.png "Un esempio di esecuzione di un'operazione Handoff")](handoff-images/handoff02.png#lightbox)

Questo articolo verrà dare un'occhiata veloce di attivazione di attività di condivisione in un'app xamarin. IOS e illustra il framework di passaggio di consegne in modo dettagliato:

## <a name="about-handoff"></a>Sulle Handoff

Esegui handoff (noto anche come continuità) è stato introdotto da Apple in iOS 8 e OS X Yosemite (10.10) come un modo per l'utente avviare un'attività in uno dei propri dispositivi (iOS o Mac) e continuare a quella stessa attività in un'altra dei propri dispositivi (come identificato dalla iClou dell'utente p Account).

Esegui handoff è stato ampliato in iOS 9 supporti anche nuove, migliorate funzionalità di ricerca. Per altre informazioni, vedere la [miglioramenti della ricerca](~/ios/platform/search/index.md) documentazione.

Ad esempio, l'utente può avviare un messaggio di posta elettronica sul proprio iPhone e continuare senza problemi il messaggio di posta elettronica nei computer Mac, con tutte le stesse informazioni di messaggio compilato e il cursore nella stessa posizione sono stato lasciato in iOS.

Qualsiasi App che condividono lo stesso _ID Team_ sono firmati da uno sviluppatore registrato o idonei per utilizzando Handoff per continuare le attività degli utenti tra le app, purché queste app possono essere recapitati tramite Store l'App iTunes (per Mac, Enterprise o le app Ad Hoc).

Eventuali `NSDocument` o `UIDocument` basato su app hanno automaticamente Handoff supporto incorporato e richiede modifiche minime per il supporto di consegna.

### <a name="continuing-user-activities"></a>Continuare le attività degli utenti

Il `NSUserActivity` classe (con alcune piccole modifiche che `UIKit` e `AppKit`) fornisce il supporto per la definizione di attività di un utente che potenzialmente possono essere riattivati in un altro dei dispositivi dell'utente.

Per un'attività deve essere passato un altro dei dispositivi dell'utente, deve essere incapsulata in un'istanza `NSUserActivity`, contrassegnato come il _attività corrente_, impostare il payload (i dati usati per eseguire la continuazione) e il attività devono quindi trasmessi al dispositivo.

Esegui handoff passa il livello minimo di informazioni per definire l'attività deve essere continuata, con pacchetti di dati di dimensioni maggiori da sincronizzare tramite iCloud.

Sul dispositivo di ricezione, l'utente riceverà una notifica che un'attività è disponibile per la continuazione. Se l'utente sceglie di continuare l'attività nel nuovo dispositivo, viene avviata l'app specificata (se non è già in esecuzione) e il payload dal `NSUserActivity` consente di riavviare l'attività.

[![](handoff-images/handoffinteractions.png "Una panoramica di continuare le attività degli utenti")](handoff-images/handoffinteractions.png#lightbox)

Solo le app che condividono lo stesso sviluppatore ID Team e rispondano a una determinata _tipo di attività_ idonei per la continuazione. Un'app definisce i tipi di attività che supporta sotto il `NSUserActivityTypes` chiave di relativo **Info. plist** file. Detto questo, un dispositivo continuo sceglie l'app per eseguire la continuazione in base all'ID del Team, tipo di attività e, facoltativamente, il _titolo dell'attività_.

L'app ricevente Usa le informazioni dal `NSUserActivity`del `UserInfo` dizionario utilizzato per configurare l'interfaccia utente e ripristinare lo stato dell'attività specificata in modo che la transizione viene visualizzato senza problemi all'utente finale.

Se la continuazione richiede più informazioni possono essere inviati in modo efficiente tramite una `NSUserActivity`, la ripresa dell'app può inviare una chiamata all'app di origine e definire uno o più flussi per trasmettere i dati necessari. Ad esempio, se l'attività è stata la modifica di un documento di testo di grandi dimensioni con più immagini, streaming potrebbe essere necessario eseguire per trasferire le informazioni necessarie per continuare l'attività sul dispositivo di ricezione. Per altre informazioni, vedere la [supporto per la continuazione flussi](#supporting-continuation-streams) sezione riportata di seguito.

Come indicato in precedenza, `NSDocument` o `UIDocument` basato su app hanno automaticamente Handoff supporto incorporato. Per altre informazioni, vedere la [Handoff supporto nelle App basate su documenti](#supporting-handoff-in-document-based-apps) sezione riportata di seguito.

### <a name="the-nsuseractivity-class"></a>La classe NSUserActivity

Il `NSUserActivity` classe è l'oggetto principale in uno scambio di consegna e viene usato per incapsulare lo stato di un'attività di utente che è disponibile per la continuazione. Un'app verrà creata un'istanza di una copia di `NSUserActivity` per qualsiasi attività supporta e si vuole continuare in un altro dispositivo. Ad esempio, editor di documenti creerà un'attività per ogni documento attualmente aperto. Tuttavia, solo per il documento in primo piano (mostrato in primo piano finestra o scheda) è il _attività corrente_ e, pertanto, disponibili per la continuazione.

Un'istanza di `NSUserActivity` identificato dal relativo `ActivityType` e `Title` proprietà. Il `UserInfo` proprietà dizionario viene utilizzata per fornire informazioni sullo stato dell'attività. Impostare il `NeedsSave` proprietà `true` se si desidera che su lazy caricare le informazioni sullo stato tramite il `NSUserActivity`del delegato. Usare la `AddUserInfoEntries` metodo per unire i nuovi dati provenienti da altri client nella `UserInfo` dizionario come richiesto per mantenere lo stato dell'attività.

### <a name="the-nsuseractivitydelegate-class"></a>La classe NSUserActivityDelegate

Il `NSUserActivityDelegate` viene usato per mantenere le informazioni un `NSUserActivity`del `UserInfo` dizionario aggiornato e sincronizzato con lo stato corrente dell'attività. Quando il sistema ha bisogno delle informazioni nell'attività di aggiornamento (ad esempio prima di continuazione in un altro dispositivo), chiama il `UserActivityWillSave` metodo del delegato.

È necessario implementare il `UserActivityWillSave` (metodo) e apportare le modifiche al `NSUserActivity` (come `UserInfo`, `Title`e così via) per assicurare che rifletta ancora lo stato dell'attività corrente. Quando il sistema chiama il `UserActivityWillSave` metodo, il `NeedsSave` flag verrà cancellato. Se si modificano le proprietà dei dati dell'attività, è necessario impostare `NeedsSave` a `true` nuovamente.

Invece di usare la `UserActivityWillSave` metodo presentata nell'esempio precedente, è possibile facoltativamente avere `UIKit` o `AppKit` gestire automaticamente l'attività dell'utente. A tale scopo, impostare l'oggetto di risponditore `UserActivity` proprietà e implementare il `UpdateUserActivityState` (metodo). Vedere le [Handoff che supportano in risponditori](#supporting-handoff-in-responders) sezione di seguito per altre informazioni.

### <a name="app-framework-support"></a>Supporto per Framework di App

Entrambe `UIKit` (iOS) e `AppKit` (OS X) fornisce supporto incorporato per passarlo nel `NSDocument`, risponditore (`UIResponder`/`NSResponder`), e `AppDelegate` classi. Mentre ogni sistema operativo implementa Handoff in modo leggermente diverso, il meccanismo di base e le API sono gli stessi.

#### <a name="user-activities-in-document-based-apps"></a>Attività degli utenti nelle App basate su documenti

Documento basato su App per iOS e OS X dispongono automaticamente di supporto Handoff incorporato. Per attivare questo supporto, è necessario aggiungere un' `NSUbiquitousDocumentUserActivityType` chiave e il valore per ogni `CFBundleDocumentTypes` voce dell'app **Info. plist** file.

Se questa chiave è presente, entrambe `NSDocument` e `UIDocument` crea automaticamente `NSUserActivity` istanze per documenti basati su iCloud del tipo specificato. Si dovrà fornire un tipo di attività per ogni tipo di documento che l'app supporta e tipi di documenti multipli possono usare lo stesso tipo di attività. Entrambe `NSDocument` e `UIDocument` popolare automaticamente la `UserInfo` proprietà del `NSUserActivity` con loro `FileURL` valore della proprietà.

In OS X, il `NSUserActivity` gestiti da `AppKit` e associata automaticamente risponditori diventano l'attività corrente quando la finestra del documento diventa la finestra principale. In iOS, per `NSUserActivity` gli oggetti gestiti da `UIKit`, è necessario chiamare `BecomeCurrent` metodo in modo esplicito o dispone del documento `UserActivity` proprietà è impostata su un `UIViewController` quando l'app passa in primo piano.

`AppKit` ripristino automatico qualsiasi `UserActivity` proprietà creati in questo modo su OS X. Ciò si verifica se il `ContinueUserActivity` restituzione del metodo `false` o se è non implementata. In questo caso, il documento viene aperto con il `OpenDocument` metodo per il `NSDocumentController` e riceverà un `RestoreUserActivityState` chiamata al metodo.

Vedere le [Handoff supporto nelle App basate su documenti](#supporting-handoff-in-document-based-apps) sezione di seguito per altre informazioni.

#### <a name="user-activities-and-responders"></a>Le attività degli utenti e ai risponditori

Entrambe `UIKit` e `AppKit` può gestire automaticamente un'attività dell'utente se si imposta come oggetto risponditore `UserActivity` proprietà. Se lo stato è stato modificato, è necessario impostare il `NeedsSave` proprietà del risponditore `UserActivity` a `true`. Il sistema salvano automaticamente le `UserActivity` quando richiesto, dopo aver dato il tempo del risponditore per aggiornare lo stato chiamando relativo `UpdateUserActivityState` (metodo).

Se più risponditori condividono un unico `NSUserActivity` istanza, ricevono un `UpdateUserActivityState` callback quando il sistema aggiorna l'oggetto attività utente. Il risponditore deve chiamare il `AddUserInfoEntries` metodo per aggiornare il `NSUserActivity`del `UserInfo` dizionario in modo da riflettere lo stato dell'attività corrente a questo punto. Il `UserInfo` dizionario viene cancellato prima della ognuno `UpdateUserActivityState` chiamare.

Per annullare l'associazione di se stesso da un'attività, è possibile impostare un risponditore relativi `UserActivity` proprietà `null`. Quando un framework di app gestite `NSUserActivity` istanza non dispone di più risponditori associati o documenti, è automaticamente annullata.

Vedere le [Handoff che supportano in risponditori](#supporting-handoff-in-responders) sezione di seguito per altre informazioni.

#### <a name="user-activities-and-the-appdelegate"></a>Le attività degli utenti e AppDelegate

L'app `AppDelegate` è il punto di ingresso principale quando si gestiscono una continuazione di consegna. Quando l'utente risponde a una notifica di consegna, viene avviata l'app appropriata (se non è già in esecuzione) e il `WillContinueUserActivityWithType` metodo di `AppDelegate` verrà chiamato. A questo punto, l'app deve informare l'utente che viene avviata la continuazione.

Il `NSUserActivity` istanza viene recapitata quando il `AppDelegate`del `ContinueUserActivity` viene chiamato il metodo. A questo punto, è necessario configurare l'interfaccia utente dell'app e continuare l'attività specificata.

Vedere le [Handoff implementazione](#implementing-handoff) sezione di seguito per altre informazioni.

## <a name="enabling-handoff-in-a-xamarin-app"></a>Abilitazione di passaggio di consegne in un'App Xamarin

A causa dei requisiti di sicurezza imposti dal passaggio di consegne, un'app xamarin. IOS che usa il framework Handoff deve essere configurata correttamente in entrambi al portale Apple Developer e nel file di progetto xamarin. IOS.

Seguire questa procedura:

1. Accedere al [portale Apple Developer](https://developer.apple.com).
2. Fare clic su **certificati, identificatori e profili**.
3. Se non già stato fatto, fare clic su **identificatori** e creare un ID per l'app (ad esempio `com.company.appname`), altrimenti modificare l'ID esistente.
4. Verificare che il **iCloud** service è stata selezionata per l'ID specificato:

    [![](handoff-images/provision01.png "Abilitare il servizio di iCloud per l'ID specificato")](handoff-images/provision01.png#lightbox)
5. Salvare le modifiche.
4. Fare clic su **profili di Provisioning** > **sviluppo** e creare un nuovo profilo di provisioning per l'utente di sviluppo di app:

    [![](handoff-images/provision02.png "Creare un nuovo profilo di provisioning per l'app di sviluppo")](handoff-images/provision02.png#lightbox)
5. Scaricare e installare il nuovo profilo di provisioning o usare Xcode per scaricare e installare il profilo.
6. Modificare le opzioni di progetto xamarin. IOS e assicurarsi di usare il profilo di provisioning appena creato:

    [![](handoff-images/provision03.png "Selezionare il profilo di provisioning appena creato")](handoff-images/provision03.png#lightbox)
7. Successivamente, modificare il **Info. plist** file e assicurarsi che si sta utilizzando l'ID dell'App che è stato utilizzato per creare il profilo di provisioning:

    [![](handoff-images/provision04.png "Impostare l'ID App")](handoff-images/provision04.png#lightbox)
8. Scorrere fino al **modalità di Background** sezione e controllare gli elementi seguenti:

    [![](handoff-images/provision05.png "Abilitare la modalità in background richieste")](handoff-images/provision05.png#lightbox)
9. Salvare le modifiche a tutti i file.

Con queste impostazioni posto, l'applicazione è ora pronta per accedere alle API di Framework di consegna. Per informazioni dettagliate sul provisioning, vedere la [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) e [Provisioning dell'App](~/ios/get-started/installation/device-provisioning/index.md) Guide.

## <a name="implementing-handoff"></a>Implementazione Handoff

È possono continuare le attività degli utenti tra le app siano firmate con lo stesso sviluppatore ID Team e supportano lo stesso tipo di attività. Implementazione Handoff in un'app xamarin. IOS richiede di creare un oggetto attività utente (in `UIKit` o `AppKit`), aggiornare lo stato dell'oggetto per tenere traccia dell'attività e continuare l'attività in un dispositivo di destinazione.

### <a name="identifying-user-activities"></a>Che identifica le attività degli utenti

Il primo passaggio dell'implementazione Handoff consiste nell'identificare i tipi di attività utente supportate dall'app e visualizzare quali di queste attività sono buoni candidati per la continuazione in un altro dispositivo. Ad esempio: un'app ToDo potrebbe supportare la modifica di elementi come una _tipo di attività utente_e supporta l'esplorazione dell'elenco di elementi disponibile un'altra.

Un'app può creare tante utente tipi di attività sono obbligatori, uno per qualsiasi funzione che fornisce l'app. Per ogni tipo di attività utente, l'app dovrà rilevare quando un'attività del tipo inizia e termina e desidera mantenere le informazioni aggiornate sullo stato per continuare l'attività in un altro dispositivo.

Per le app firmate con lo stesso ID Team senza alcun mapping uno a uno tra le app di inviare e ricezione, è possono continuare le attività degli utenti. Una determinata app, ad esempio, possibile creare quattro tipi diversi di attività, che vengono usati da diverse, le singole App in un altro dispositivo. Si tratta di una situazione comune tra una versione Mac dell'app (che potrebbe avere molte funzioni e funzionalità) e le app iOS, in cui ogni app è più piccolo e di focalizzare l'attenzione su un'attività specifica.

### <a name="creating-activity-type-identifiers"></a>Creazione di identificatori di tipo di attività

Il _identificatore di tipo attività_ viene aggiunta una stringa breve per il `NSUserActivityTypes` matrice dell'app **Info. plist** file utilizzato per identificare in modo univoco un determinato tipo di attività utente. Vi sarà una voce nella matrice per ogni attività che supporta l'app. Apple consiglia di usare una notazione di stile di DNS inverso per l'identificatore del tipo di attività per evitare conflitti. Ad esempio: `com.company-name.appname.activity` per app specifiche attività basate sugli o `com.company-name.activity` per le attività che possono essere eseguiti in più app.

L'identificatore di tipo attività viene utilizzato durante la creazione di un `NSUserActivity` istanza per identificare il tipo di attività. Quando un'attività viene continuata in un altro dispositivo, il tipo di attività (con l'ID dell'app Team) determina quali app devono essere avviati per continuare l'attività.

Ad esempio, si intende creare un'app di esempio chiamata **MonkeyBrowser** ([scaricare qui](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)). Questa app verrà visualizzate quattro schede, ognuna con un diverso open URL in una visualizzazione del web browser. L'utente sarà in grado di continuare a qualsiasi scheda in un dispositivo iOS diverso che esegue l'app.

Per creare gli identificatori di tipo attività necessarie per supportare questo comportamento, modificare il **Info. plist** del file e passare al **origine** visualizzazione. Aggiungere un `NSUserActivityTypes` della chiave e creare gli identificatori seguenti:

[![](handoff-images/type01.png "La chiave di NSUserActivityTypes e gli identificatori necessari nell'editor plist")](handoff-images/type01.png#lightbox)

Abbiamo creato quattro nuove attività di tipo identificatori, uno per ogni scheda nell'esempio **MonkeyBrowser** app. Durante la creazione di App personalizzate, sostituire il contenuto del `NSUserActivityTypes` matrice con gli identificatori di tipo attività specifiche per le attività, l'app supporta.

### <a name="tracking-user-activity-changes"></a>Rilevamento delle modifiche di attività utente

Quando si crea una nuova istanza della `NSUserActivity` (classe), si specificherà un `NSUserActivityDelegate` istanza tenga traccia delle modifiche allo stato dell'attività. Ad esempio, il codice seguente è utilizzabile per tenere traccia delle modifiche di stato:

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

Il `UserActivityReceivedData` metodo viene chiamato quando una continuazione Stream ha ricevuto i dati da un dispositivo di invio. Per altre informazioni, vedere la [supporto per la continuazione flussi](#supporting-continuation-streams) sezione riportata di seguito.

Il `UserActivityWasContinued` metodo viene chiamato quando un altro dispositivo ha preso il sopravvento su un'attività dal dispositivo corrente. A seconda del tipo di attività, ad esempio l'aggiunta di un nuovo elemento a un elenco ToDo, l'app potrebbe essere necessario interrompere l'attività sul dispositivo di invio.

Il `UserActivityWillSave` metodo viene chiamato prima che eventuali modifiche apportate all'attività vengono salvate e sincronizzate tra i dispositivi disponibili in locale. È possibile utilizzare questo metodo per apportare modifiche ultimo minuto per il `UserInfo` proprietà del `NSUserActivity` istanza prima che venga inviato.

### <a name="creating-a-nsuseractivity-instance"></a>Creazione di un'istanza NSUserActivity

Ogni attività che l'app desidera offrire la possibilità di continuare in un altro dispositivo devono essere incapsulate in un `NSUserActivity` istanza. L'app può creare tutte le attività in base alle esigenze e la natura di tali attività dipende dalla caratteristiche e delle funzionalità dell'app in questione. Ad esempio, un'app di posta elettronica può creare un'attività per la creazione di un nuovo messaggio e l'altro per la lettura di un messaggio.

Per l'app di esempio, un nuovo `NSUserActivity` viene creato ogni volta che l'utente immette un nuovo URL in una visualizzazione a schede web browser. Il codice seguente archivia lo stato di una scheda specificata:

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

Crea un nuovo `NSUserActivity` utilizzando uno del tipo di attività utente creato in precedenza e fornisce un titolo leggibile dall'utente per l'attività. È necessario collegarlo a un'istanza del `NSUserActivityDelegate` creato sopra da controllare per lo stato cambia e Mostra iOS che questa attività dell'utente è l'attività corrente.

### <a name="populating-the-userinfo-dictionary"></a>Popolare il dizionario di informazioni utente

Come abbiamo visto in precedenza, il `UserInfo` proprietà del `NSUserActivity` classe è un `NSDictionary` di coppie chiave-valore usate per definire lo stato di una determinata attività. I valori archiviati nelle `UserInfo` deve essere uno dei seguenti tipi: `NSArray`, `NSData`, `NSDate`, `NSDictionary`, `NSNull`, `NSNumber`, `NSSet`, `NSString`, o `NSURL`. `NSURL` i valori di dati che puntano a documenti iCloud verranno automaticamente modificati in modo che facciano riferimento ai documenti in un dispositivo di destinazione stesso.

Nell'esempio precedente è stato creato un `NSMutableDictionary` dell'oggetto e averlo popolato con una singola chiave fornendo l'URL a cui l'utente è stato attualmente visualizzando la scheda specificata. Il `AddUserInfoEntries` metodo l'attività degli utenti è stato usato per aggiornare l'attività con i dati che verranno usati per ripristinare l'attività sul dispositivo di ricezione:

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple consiglia di mantenere le informazioni inviate al minimo indispensabile per garantire che l'attività venga inviato in modo tempestivo per il dispositivo di ricezione. Se sono necessarie, maggiori informazioni, ad esempio possibile modificare un'immagine associata a un documento deve essere inviato, il è consigliabile usare i flussi di continuazione. Vedere le [supporto per la continuazione flussi](#supporting-continuation-streams) sezione di seguito per altri dettagli.

### <a name="continuing-an-activity"></a>Un'attività di continuazione

Esegui handoff automaticamente informerà locale dispositivi iOS e OS X in prossimità fisica per dispositivo di origine e effettuato lo stesso account iCloud, della disponibilità delle attività utente consentita. Se l'utente sceglie di continuare a un'attività in un nuovo dispositivo, il sistema verrà avviata l'app appropriata (in base l'ID Team e il tipo di attività) e informazioni relativi `AppDelegate` che continuazione deve essere eseguito.

Prima di tutto la `WillContinueUserActivityWithType` metodo viene chiamato in modo che l'app può informare l'utente che la continuazione sta per iniziare. Utilizziamo il codice seguente nel **AppDelegate.cs** file l'app di esempio per gestire l'avvio di una continuazione:

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

Nell'esempio precedente, ogni Controller di visualizzazione registra con il `AppDelegate` e dispone di un pubblico `PreparingToHandoff` metodo che visualizza un indicatore dell'attività e un messaggio che comunica all'utente che l'attività sta per essere inviato al dispositivo corrente. Esempio:

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

Il `ContinueUserActivity` del `AppDelegate` verrà chiamato per effettivamente continuare l'attività specificata. Anche in questo caso dalla nostra app di esempio:

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

Pubblico `PerformHandoff` metodo per ogni Controller di visualizzazione effettivamente avviato il passaggio di consegne e ripristina l'attività nel dispositivo corrente. Nel caso dell'esempio, viene visualizzato lo stesso URL in una scheda specificata che l'utente Naviga in un altro dispositivo. Esempio:

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

Il `ContinueUserActivity` metodo include un `UIApplicationRestorationHandler` che è possibile chiamare per documento o un risponditore basato su attività ripresa. È necessario passare un `NSArray` o gli oggetti che è possibile ripristinare il gestore di ripristino quando viene chiamato. Ad esempio:

```csharp
completionHandler (new NSObject[]{Tab4});
```

Per ogni oggetto passato, relativo `RestoreUserActivityState` metodo verrà chiamato. Ogni oggetto può quindi usare i dati di `UserInfo` dizionario utilizzato per ripristinare il proprio stato. Ad esempio:

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

Per le app basate su documenti, se non si implementa il `ContinueUserActivity` metodo oppure restituisce `false`, `UIKit` o `AppKit` può riprendere automaticamente l'attività. Vedere le [Handoff supporto nelle App basate su documenti](#supporting-handoff-in-document-based-apps) sezione di seguito per altre informazioni.

### <a name="failing-handoff-gracefully"></a>In mancanza normalmente Handoff

Poiché Handoff si basa sulla trasmissione di informazioni tra un raccolta connesso in modo approssimativo dispositivi iOS e OS X, il processo di trasferimento può talvolta non riuscire. È consigliabile progettare l'app per gestire correttamente questi errori e informare l'utente di eventuali situazioni che si verificano.

In caso di errore, il `DidFailToContinueUserActivitiy` metodo di `AppDelegate` verrà chiamato. Ad esempio:

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

È consigliabile usare il parametro fornito `NSError` per fornire informazioni sull'errore all'utente.

## <a name="native-app-to-web-browser-handoff"></a>App nativa a Handoff di Web Browser

Un utente desideri continuare un'attività senza la necessità di un'app nativa appropriata installata sul dispositivo desiderato. In alcune situazioni, un'interfaccia basata sul web può fornire le funzionalità richieste e può continuare comunque l'attività. Account di posta elettronica dell'utente, ad esempio, può fornire un'interfaccia utente web di base per la composizione e la lettura dei messaggi.

Se l'app nativa di origine conosce l'URL per l'interfaccia web e la sintassi necessaria per identificare l'elemento specificato in corso continua, può codificare queste informazioni nel `WebpageURL` proprietà del `NSUserActivity` istanza. Se il dispositivo di ricezione non dispone di un'app nativa appropriata installata per gestire la continuazione, l'interfaccia web specificato può essere chiamato.

## <a name="web-browser-to-native-app-handoff"></a>Web Browser a Handoff di App Native

Se l'utente sta utilizzando un'interfaccia basata sul web sul dispositivo di origine e la parte del dominio di attestazioni di un'app nativa sul dispositivo di ricezione il `WebpageURL` proprietà, quindi il sistema utilizzerà l'handle della continuazione l'app. Il nuovo dispositivo riceverà un `NSUserActivity` istanza che contrassegna il tipo di attività come `BrowsingWeb` e il `WebpageURL` conterrà l'URL è stato visitato l'utente, il `UserInfo` dizionario sarà vuoto.

Per un'app a far parte di questo tipo di passaggio di consegne, è necessario attestazione il dominio in una `com.apple.developer.associated-domains` entitlement con il formato `<service>:<fully qualified domain name>` (ad esempio: `activity continuation:company.com`).

Se il dominio specificato corrisponde a un `WebpageURL` valore della proprietà, Handoff scarica un elenco di app approvate ID dal sito Web di tale dominio. Il sito Web deve fornire un elenco di ID approvati in un file JSON firmato denominato **apple-app-sito-association** (ad esempio, `https://company.com/apple-app-site-association`).

Questo file JSON contiene un dizionario che specifica un elenco di app ID nel formato `<team identifier>.<bundle identifier>`. Ad esempio:

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

Per firmare il file JSON (in modo che includa i valori corretti `Content-Type` dei `application/pkcs7-mime`), utilizzare il **Terminal** app e un `openssl` comando con un certificato e chiave emesso da un'autorità di certificazione attendibile per iOS (vedere [ https://support.apple.com/kb/ht5012 ](https://support.apple.com/kb/ht5012) per un elenco). Ad esempio:

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

Il `openssl` comando genera un file JSON firmato che si inserisce nel tuo sito Web nel **associazione-apple-app-sito** URL. Ad esempio:

```csharp
https://example.com/apple-app-site-association.
```

L'app verrà visualizzato qualsiasi attività il cui `WebpageURL` dominio si trova in relativo `com.apple.developer.associated-domains` entitlement. Solo le `http` e `https` protocolli sono supportati, tutti gli altri protocolli genererà un'eccezione.

## <a name="supporting-handoff-in-document-based-apps"></a>Nelle App basate su documenti di supporto Handoff

Come indicato in precedenza, nei sistemi iOS e OS X, le app basate su documenti automaticamente supporterà consegna di documenti basati su iCloud se l'app **Info. plist** file contiene una `CFBundleDocumentTypes` chiave di `NSUbiquitousDocumentUserActivityType`. Ad esempio:

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

In questo esempio la stringa è un indicatore di DNS inverso app con il nome dell'attività aggiunta. Se inserito in questo modo, le voci di tipo di attività non sono necessario essere ripetuto nel `NSUserActivityTypes` matrice del **Info. plist** file.

L'oggetto di attività dell'utente creato automaticamente (disponibile tramite il documento `UserActivity` proprietà) può essere fatto riferimento da altri oggetti nell'app e consente di ripristinare lo stato di continuazione. Posizionare, ad esempio, per tenere traccia di elementi selezione e dei documenti. È necessario impostare questa attività `NeedsSave` proprietà `true` ogni volta che lo stato viene modificato e aggiornare le `UserInfo` dizionario nel `UpdateUserActivityState` (metodo).

Il `UserActivity` proprietà può essere usata da qualsiasi thread ed è conforme al protocollo di chiave-valore di osservazione (KVO), in modo che può essere utilizzato per sincronizzare un documento quando questo viene spostato in e out di iCloud. Il `UserActivity` chiave verrà invalidata proprietà quando quest'ultimo viene chiuso.

Per altre informazioni, vedere di Apple [supporto di attività utente nelle App basate su documenti](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5) documentazione.

## <a name="supporting-handoff-in-responders"></a>Supporto Handoff in risponditori

È possibile associare i risponditori (ereditato dal `UIResponder` in iOS o `NSResponder` in OS X) per le attività, impostando i `UserActivity` proprietà. Il sistema consente di salvare automaticamente il `UserActivity` proprietà a volte, la chiamata del risponditore appropriato `UpdateUserActivityState` per aggiungere i dati correnti per l'oggetto attività degli utenti usando il `AddUserInfoEntriesFromDictionary` (metodo).

## <a name="supporting-continuation-streams"></a>Supporto di flussi di continuazione

Il possono verificarsi situazioni in cui la quantità di informazioni necessarie per un'attività di continuazione non è possibile trasferire in modo efficiente per il payload Handoff iniziale. In queste situazioni, l'app ricevente può stabilire una o più flusso tra se stesso e l'app di trasferire i dati di origine.

L'app di origine verrà impostato il `SupportsContinuationStreams` proprietà del `NSUserActivity` dell'istanza per `true`. Ad esempio:

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

L'app ricevente può quindi chiamare il `GetContinuationStreams` metodo del `NSUserActivity` in relativo `AppDelegate` per stabilire il flusso. Ad esempio:

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

Sul dispositivo di origine, il delegato di attività utente riceve i flussi chiamando relativo `DidReceiveInputStream` metodo per fornire i dati richiesti per continuare l'attività dell'utente nel dispositivo ripresa.

Si userà una `NSInputStream` per fornire l'accesso di sola lettura ai dati di flusso e un `NSOutputStream` forniscono l'accesso in sola lettura. Usare i flussi in uno schema di richiesta e risposta, in cui l'app ricevente richiede più dati e la fornisce all'app di origine. In modo che i dati scritti nel flusso di output nel dispositivo di origine viene letto dal flusso di input nel dispositivo continuo e viceversa.

Anche in situazioni in cui sono necessarie continuazione Stream, non vi sarà un minimo di back e via la comunicazione tra le due app.

Per altre informazioni, vedere di Apple [Using continuazione flussi](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13) documentazione.

## <a name="handoff-best-practices"></a>Procedure consigliate handoff

L'implementazione corretta della continuazione senza problemi di un'attività utente tramite Handoff richiede un'attenta progettazione a causa di tutti i vari componenti coinvolti. Adozione di seguire le procedure consigliate per le app abilitate Handoff suggerite da Apple:

- Progettare attività utente di richiedere il payload più piccolo possibile correlare lo stato dell'attività da completare. Maggiore il payload di, maggiore sarà il tempo richiede la continuazione da avviare.
- Se è necessario trasferire grandi quantità di dati per la continuazione ha esito positivo, prendere in considerazione i costi associati a un overhead di rete e configurazione.
- È comune per un'app Mac grande creare le attività degli utenti che sono gestite da diversi, più piccolo, specifiche per le app nei dispositivi iOS. L'app diversi e versioni del sistema operativo devono essere progettate per funzionare correttamente insieme o ha esito negativo correttamente.
- Quando si specificano i tipi di attività, utilizzare la notazione DNS inverso per evitare conflitti. Se un'attività è specifica per una determinata app, il relativo nome deve essere inclusa nella definizione del tipo (ad esempio `com.myCompany.myEditor.editing`). Se l'attività possa funzionare tra più App, eliminare il nome dell'app dalla definizione (ad esempio `com.myCompany.editing`).
- Se l'app deve aggiornare lo stato di un'attività utente (`NSUserActivity`) impostare il `NeedsSave` proprietà `true`. Nel momento appropriato, Handoff chiamerà il delegato `UserActivityWillSave` metodo in modo che è possibile aggiornare il `UserInfo` dizionario come richiesto.
- Poiché il processo di consegna non può inizializzare immediatamente sul dispositivo di ricezione, è necessario implementare il `AppDelegate`del `WillContinueUserActivity` e informare l'utente che una continuazione sta per essere avviato.

## <a name="example-handoff-app"></a>App di esempio Handoff

Ad esempio di utilizzo consegna in un'app xamarin. IOS, è stato incluso il [ **MonkeyBrowser** ](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/) app di esempio in questa Guida. L'app ha quattro schede che l'utente può usare per esplorare il web, ognuno con un tipo di attività specificata: Meteo, Preferiti, pausa e lavoro.

In qualsiasi scheda, quando l'utente immette un nuovo URL e tocca il **andare** pulsante, un nuovo `NSUserActivity` viene creato per tale scheda che contiene l'URL a cui l'utente attualmente durante l'esplorazione:

[![](handoff-images/handoff01.png "App di esempio Handoff")](handoff-images/handoff01.png#lightbox)

Se un altro dei dispositivi dell'utente ha il **MonkeyBrowser** app installata, viene effettuato l'accesso a iCloud usando lo stesso account utente, è nella stessa rete e in stretta vicinanza al dispositivo precedente, verrà visualizzata l'attività di passaggio di consegne in home schermata di (in basso a sinistra):

[![](handoff-images/handoff02.png "L'attività di passaggio di consegne visualizzato nella schermata iniziale in basso a sinistra")](handoff-images/handoff02.png#lightbox)

Se l'utente trascina verso l'alto sull'icona di passaggio di consegne, verrà avviata l'app e l'attività dell'utente specificato nella `NSUserActivity` proseguiranno nel nuovo dispositivo:

[![](handoff-images/handoff03.png "L'attività dell'utente ha continuato nel nuovo dispositivo")](handoff-images/handoff03.png#lightbox)

Quando l'attività dell'utente è stato correttamente inviato a un altro dispositivo Apple, il dispositivo di invio `NSUserActivity` riceverà una chiamata ai `UserActivityWasContinued` metodo sul relativo `NSUserActivityDelegate` necessario indicare che l'attività dell'utente è stato trasferito a un altro dispositivo.

## <a name="summary"></a>Riepilogo

Questo articolo è stata fornita un'introduzione al framework Handoff consente di continuare a un'attività utente tra più dispositivi di Apple dell'utente. Successivamente, è stato illustrato come abilitare e implementare Handoff in un'app xamarin. IOS. Infine, è stato illustrato i diversi tipi di continuazioni Handoff disponibili e le procedure ottimali di consegna.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Esempio MonkeyBrowser](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)
- [per gli sviluppatori iOS 9](https://developer.apple.com/ios/pre-release/)
- [What ' s New in iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Linee guida dell'interfaccia utente di HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Riferimento a Framework HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref)
