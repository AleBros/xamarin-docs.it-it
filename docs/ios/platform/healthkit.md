---
title: HealthKit
description: "HealthKit è un framework, introdotto in iOS 8 che fornisce un archivio dati centralizzato, coordinato e sicuro per le informazioni correlate di integrità. Il sistema operativo garantisce la privacy e protezione delle informazioni di integrità e, con l'app di integrità, un dashboard per l'utente. Con l'autorizzazione dell'utente, le applicazioni possono leggere e scrivere una vasta gamma di informazioni di integrità."
ms.topic: article
ms.prod: xamarin
ms.assetid: E3927A21-507C-43BA-A2AD-957716BA9B52
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 3e21794c9f1d8f010ec323774bc93987f4b89e1d
ms.sourcegitcommit: 028936cd2fe547963c1cf82343c3ee16f658089a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="healthkit"></a>HealthKit

_HealthKit è un framework, introdotto in iOS 8 che fornisce un archivio dati centralizzato, coordinato e sicuro per le informazioni correlate di integrità. Il sistema operativo garantisce la privacy e protezione delle informazioni di integrità e, con l'app di integrità, un dashboard per l'utente. Con l'autorizzazione dell'utente, le applicazioni possono leggere e scrivere una vasta gamma di informazioni di integrità._

Kit di integrità fornisce un archivio dati di protezione per le informazioni dell'utente all'integrità. Integrità Kit App può, con l'autorizzazione esplicita dell'utente, leggere e scrivere l'archivio dati e ricevere notifiche quando vengono aggiunti dati pertinenti. Le applicazioni possono presentare i dati o dell'utente è possibile utilizzare app integrità fornito di Apple per visualizzare un dashboard di tutti i dati.

Poiché i dati relativi a integrità sono così sensibili e fondamentale, Kit di integrità è fortemente tipizzata, con unità di misura e un'associazione esplicita con il tipo di informazioni registrate (ad esempio, il livello di glucosio sangue o cuore frequenza). Inoltre, le app di integrità Kit devono usare i diritti espliciti, deve richiedere l'accesso a determinati tipi di informazioni e l'utente deve concedere esplicitamente all'applicazione l'accesso a tali tipi di dati.

Verrà presentate in questo articolo:

- Requisiti di sicurezza del Kit di integrità, tra cui provisioning delle applicazioni e che richiede l'autorizzazione utente per accedere al database di integrità Kit;
- Sistema di tipi del Kit di integrità, riducendo al minimo la possibilità di non applicare o interpretare i dati.
- La scrittura nell'archivio dati integrità Kit condivisa, a livello di sistema.

In questo articolo non copre argomenti più avanzati, ad esempio una query sul database, la conversione tra le unità di misura o la ricezione delle notifiche di nuovi dati.

In questo articolo, che verrà creata un'applicazione di esempio per registrare il tasso di base dell'utente:

[![](healthkit-images/image01.png "Un'applicazione di esempio per registrare il tasso di base di utenti")](healthkit-images/image01.png#lightbox)

## <a name="requirements"></a>Requisiti

Per completare la procedura illustrata in questo articolo è necessario quanto segue:

- **Xcode 7 e iOS 8 (o versione successiva)** – Xcode e API iOS più recente di Apple devono essere installati e configurati nel computer dello sviluppatore.
- **Visual Studio per Mac o Visual Studio** : la versione più recente di Visual Studio per Mac deve essere installata e configurata nel computer dello sviluppatore.
- **iOS 8 (o versione successiva) dispositivo** : un dispositivo iOS in esecuzione la versione più recente di iOS 8 o versione successiva per il test.

> [!IMPORTANT]
> **Nota:** Kit di integrità è stato introdotto in iOS 8. Attualmente, Kit di integrità non è disponibile nel simulatore iOS e il debug richiede una connessione a un dispositivo dei / o fisici.




## <a name="creating-and-provisioning-a-health-kit-app"></a>Creazione e il Provisioning di un'App di Kit di integrità
Prima di un'applicazione iOS 8 Xamarin è possibile utilizzare l'API HealthKit, deve essere correttamente configurato e il provisioning. In questa sezione illustra i passaggi necessari per configurare correttamente l'applicazione di Xamarin.

Richiedi integrità Kit App:

- Esplicita **ID App**.
- A **profilo di Provisioning** associato che esplicita **ID App** e con **Kit integrità** autorizzazioni.
- Un `Entitlements.plist` con un `com.apple.developer.healthkit` proprietà di tipo `Boolean` impostato su `Yes`.
- Un `Info.plist` cui `UIRequiredDeviceCapabilities` chiave contiene una voce con la `String` valore `healthkit`.
- Il `Info.plist` deve avere anche le voci appropriate sulla privacy-spiegazione: un `String` spiegazione della chiave `NSHealthUpdateUsageDescription` se l'app dovrà scrivere i dati e un `String` spiegazione della chiave `NSHealthShareUsageDescription` se l'app di leggere il Kit di integrità dati.

Per ulteriori informazioni sul provisioning di un'app per iOS, il [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md) articolo di Xamarin **Introduzione** serie descrive la relazione tra i certificati per sviluppatori, ID di App, I profili di provisioning e dei diritti delle App.

<a name="explicit-appid" />

### <a name="explicit-app-id-and-provisioning-profile"></a>ID dell'App esplicita e profilo di Provisioning

La creazione di un oggetto esplicito **ID App** e un appropriato **profilo di Provisioning** viene effettuata all'interno di Apple [iOS Dev Center](https://developer.apple.com/devcenter/ios/index.action). 

Corrente **ID App** elencati all'interno di [certificati, profili & identificatori](https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action) sezione del centro per sviluppatori di. Spesso, questo elenco verranno visualizzati **ID** valori di `*`, a indicare che il **ID App** - **nome** può essere utilizzato con un numero qualsiasi di suffissi. Tali *Wildcard App IDs* non può essere utilizzato con il Kit di integrità.
 
Per creare un oggetto esplicito **ID App**, fare clic su di  **+**  pulsante in alto a destra per accedere al **registrare l'ID di App iOS** pagina:


[![](healthkit-images/image02.png "Registrazione di un'app nel portale per sviluppatori di Apple")](healthkit-images/image02.png#lightbox)

Come illustrato nell'immagine precedente, dopo la creazione di una descrizione dell'app, utilizzare il **ID App esplicito** sezione per creare un ID per l'applicazione. Nel **servizi App** sezione controllo **Kit integrità** nel **Abilita servizi** sezione.

Al termine, premere il **continua** pulsante per registrare il **ID App** nell'account. Si passa nuovamente il **certificati, gli identificatori e i profili** pagina. Fare clic su **i profili di Provisioning** consentono all'elenco dei profili di provisioning correnti e fare clic su di  **+**  pulsante nell'angolo superiore destro per accedere al **aggiungere iOS Profilo di provisioning** pagina. Selezionare il **lo sviluppo di App iOS** opzione e fare clic su **continua** per ottenere il **selezionare ID App** pagina. In questo caso, selezionare la proprietà esplicita **ID App** specificate in precedenza:


[![](healthkit-images/image03.png "Selezionare l'ID App esplicita")](healthkit-images/image03.png#lightbox)

Fare clic su **continua** e di lavoro tramite le schermate rimanenti, in cui si specificherà il **Developer certificati**, **dispositivi**e un **nome** per questo **profilo di Provisioning**:

[![](healthkit-images/image04.png "Generazione del profilo di Provisioning")](healthkit-images/image04.png#lightbox)

Fare clic su **genera** e attende la creazione del profilo. Scaricare il file e fare doppio clic per installare in Xcode. È possibile verificare l'installazione di in **Xcode > Preferenze > account > Visualizza dettagli...** Verrà visualizzato il profilo di provisioning appena installato, e deve essere l'icona per integrità Kit e qualsiasi altro servizio speciale nel relativo **diritti** riga:

[![](healthkit-images/image05.png "Visualizzazione del profilo in Xcode")](healthkit-images/image05.png#lightbox)

<a name="associating-appid" />

### <a name="associating-the-app-id-and-provisioning-profile-with-your-xamarinios-app"></a>Associare l'ID dell'App e il Provisioning del profilo con l'App xamarin

Dopo avere creato e installato un appropriato **profilo di Provisioning** come descritto, normalmente sarebbe necessario creare una soluzione in Visual Studio per Mac o Visual Studio. Integrità Kit accesso è disponibile a qualsiasi iOS in c# o di un progetto F #.

Piuttosto che scorrere il processo di creazione di un progetto iOS 8 Xamarin manualmente, aprire l'app di esempio associato a questo articolo (che include uno Storyboard e un codice predefiniti). Per associare l'app di esempio con il Kit di integrità abilitato **profilo di Provisioning**, nel **soluzione riempimento**, pulsante destro del mouse sul progetto e visualizzare il relativo **opzioni** finestra di dialogo. Passare il **applicazione iOS** pannello e immettere esplicita **ID App** creato in precedenza come l'app **identificatore Bundle**:

[![](healthkit-images/image06.png "Immettere l'ID App esplicita")](healthkit-images/image06.png#lightbox)

Passare subito al **firma Bundle iOS** pannello. Il recente installato **profilo di Provisioning**, con l'associazione a esplicita **ID App**, saranno ora disponibili come il **profilo di Provisioning**:

[![](healthkit-images/image07.png "Selezionare il profilo di Provisioning")](healthkit-images/image07.png#lightbox)

Se il **profilo di Provisioning** non è disponibile, è necessario verificare attentamente il **identificatore Bundle** nel **applicazione iOS** e specificati nel pannello del **iOS Centro per sviluppatori** e che il **profilo di Provisioning** è installato (**Xcode > Preferenze > account > Visualizza dettagli** ).

Quando il Kit di integrità abilitato **profilo di Provisioning** è selezionata, fare clic su **OK** per chiudere la finestra di dialogo Project Options.

### <a name="entitlementsplist-and-infoplist-values"></a>Entitlements.plist e i valori di Info. plist

L'app di esempio include un `Entitlements.plist` file (che è necessario per l'abilitazione di integrità Kit App) e non è incluso in ogni modello di progetto. Se il progetto non include i diritti, fare clic sul progetto, scegliere **File > Nuovo File... > iOS > Entitlements.plist** per aggiungere uno manualmente.

Infine, il `Entitlements.plist` deve avere la seguente coppia chiave / valore:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.HealthKit</key>
    <true/>
</dict>
</plist>

```

Analogamente, il `Info.plist` per l'app deve avere un valore di `healthkit` associato il `UIRequiredDeviceCapabilities` chiave:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
<string>armv7</string>
    <string>healthkit</string>
</array>

```

L'applicazione di esempio fornito in questo articolo include un preconfigurato `Entitlements.plist` che include tutte le chiavi necessarie.

<a name="programming" />

## <a name="programming-health-kit"></a>Kit di integrità di programmazione

L'archivio dati integrità Kit è un archivio dati privato, specifici dell'utente che viene condiviso tra le app. Poiché le informazioni di integrità sono così sensibili, l'utente deve eseguire passaggi positivo per consentire l'accesso ai dati. Questo tipo di accesso possono essere parziale (scrittura ma non accesso in lettura e per alcuni tipi di dati, ma non altri, e così via) e potrebbero essere revocate in qualsiasi momento. Applicazioni Kit integrità devono essere scritto difendendosi, a condizione che molti utenti saranno propense sull'archiviazione le relative informazioni di salute.

Dati di integrità Kit sono limitati a Apple tipi specificati. Questi tipi sono definiti rigidamente: alcuni, ad esempio il tipo di sangue, sono limitati a particolari valori di un'enumerazione di Apple fornite, mentre altri combinare una grandezza con un'unità di misura (ad esempio grammi calorie e litri). Anche i dati che condividono un'unità di misura compatibile si distinguono in base loro `HKObjectType`; ad esempio, il sistema di tipi verrà rilevato un tentativo di errata per archiviare un `HKQuantityTypeIdentifier.NumberOfTimesFallen` valore per un campo previsto un `HKQuantityTypeIdentifier.FlightsClimbed` anche se utilizzano entrambi la` HKUnit.Count` unità di misura.

I tipi memorizzati nell'archivio dati integrità Kit sono tutte le sottoclassi di `HKObjectType`. `HKCharacteristicType` oggetti archiviano biologici sesso, il tipo di sangue e data di nascita. Anche i più comuni sono `HKSampleType` oggetti che rappresentano i dati campionati in un momento specifico o in un periodo di tempo. 

[![](healthkit-images/image08.png "Grafico di oggetti HKSampleType")](healthkit-images/image08.png#lightbox)

`HKSampleType` è astratta e ha quattro sottoclassi concrete. È attualmente un solo tipo di `HKCategoryType` dati, ovvero l'analisi della sospensione. Maggioranza dei dati nel Kit di integrità sono di tipo `HKQuantityType` e archiviano i dati in `HKQuantitySample` gli oggetti che vengono creati utilizzando il familiare modello di progettazione di Factory:

[![](healthkit-images/image09.png "Maggioranza dei dati nel Kit di integrità sono di tipo HKQuantityType e archiviare i dati negli oggetti HKQuantitySample")](healthkit-images/image09.png#lightbox)

`HKQuantityType` tipi di intervallo da `HKQuantityTypeIdentifier.ActiveEnergyBurned` a `HKQuantityTypeIdentifier.StepCount`. 

<a name="requesting-permission" />

### <a name="requesting-permission-from-the-user"></a>Richiedere l'autorizzazione da parte dell'utente

Gli utenti finali devono eseguire passaggi positivo per consentire un'app di leggere o scrivere dati di integrità Kit. Questa operazione viene eseguita tramite l'app integrità pre-installata nei dispositivi iOS 8. La prima volta un Kit di integrità di app viene eseguita, viene visualizzato l'utente con un sistema controllato **accesso integrità** finestra di dialogo:

[![](healthkit-images/image10.png "Viene visualizzata una finestra di dialogo accesso integrità di sistema incluso")](healthkit-images/image10.png#lightbox)

In un secondo momento, l'utente può modificare le autorizzazioni tramite l'integrità dell'app **origini** finestra di dialogo:

[![](healthkit-images/image11.png "L'utente può modificare le autorizzazioni mediante finestra di dialogo di integrità App origini")](healthkit-images/image11.png#lightbox)

Poiché le informazioni di integrità sono estremamente sensibile, sviluppatori dell'applicazione devono scrivere i propri programmi a priori, nella previsione che le autorizzazioni verranno rifiutate e modificate durante l'esecuzione dell'app. Il linguaggio più comune consiste nel richiedere le autorizzazioni nel `UIApplicationDelegate.OnActivated` (metodo) e quindi modificare l'interfaccia utente in modo appropriato.

### <a name="permissions-walkthrough"></a>Procedura dettagliata di autorizzazioni

Nel progetto provisioning integrità Kit, aprire il `AppDelegate.cs` file. Si noti l'istruzione usando `HealthKit`; nella parte superiore del file.


Il codice seguente è relativo alle autorizzazioni di Kit di integrità:

```csharp
private HKHealthStore healthKitStore = new HKHealthStore ();

public override void OnActivated (UIApplication application)
{
        ValidateAuthorization ();
}

private void ValidateAuthorization ()
{
        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
        var heartRateType = HKObjectType.GetQuantityType (heartRateId);
        var typesToWrite = new NSSet (new [] { heartRateType });
        var typesToRead = new NSSet ();
        healthKitStore.RequestAuthorizationToShare (
                typesToWrite, 
                typesToRead, 
                ReactToHealthCarePermissions);
}

void ReactToHealthCarePermissions (bool success, NSError error)
{
        var access = healthKitStore.GetAuthorizationStatus (HKObjectType.GetQuantityType (HKQuantityTypeIdentifierKey.HeartRate));
        if (access.HasFlag (HKAuthorizationStatus.SharingAuthorized)) {
                HeartRateModel.Instance.Enabled = true;
        } else {
                HeartRateModel.Instance.Enabled = false;
        }
}

```

Tutto il codice in questi metodi poteva essere eseguita inline in `OnActivated`, ma l'app di esempio vengono utilizzati metodi separati per rendere il loro scopo più chiara: `ValidateAuthorization()` include i passaggi necessari per richiedere l'accesso a determinati tipi scritti (e lettura, se lo si desidera l'app) e `ReactToHealthCarePermissions()` è un callback che viene attivato dopo che l'utente ha interagito con la finestra di dialogo autorizzazioni nel Health.app.

Il processo di `ValidateAuthorization()` consiste nel compilare il set di `HKObjectTypes` che scriverà l'app e richiedere l'autorizzazione per aggiornare i dati. Nell'applicazione di esempio, il `HKObjectType` è per la chiave `KHQuantityTypeIdentifierKey.HeartRate`. Questo tipo viene aggiunto al set di `typesToWrite`, mentre il set di `typesToRead` viene lasciato vuoto. Questi set e un riferimento di `ReactToHealthCarePermissions()` callback, viene passato a `HKHealthStore.RequestAuthorizationToShare()`.

Il `ReactToHealthCarePermissions()` verrà chiamato il callback dopo che l'utente ha interagito con la finestra di dialogo autorizzazioni e viene passato a due tipi di informazioni: un `bool` valore che verrà `true` se l'utente ha interagito con la finestra di dialogo autorizzazioni e un `NSError`che, se diverso da null, indica un tipo di errore associato alla presentazione di finestra di dialogo autorizzazioni.

> [!IMPORTANT]
> **Nota:** chiarire gli argomenti di questa funzione: il _successo_ e _errore_ parametri non indicano se l'utente dispone delle autorizzazioni necessarie per accedere ai dati di integrità Kit! Indicano solo che l'utente ha avuto la possibilità per consentire l'accesso ai dati.

Per verificare se l'applicazione abbia accesso ai dati, il `HKHealthStore.GetAuthorizationStatus()` è possibile utilizzare `HKQuantityTypeIdentifierKey.HeartRate`. L'app in base allo stato restituito, Abilita o disabilita la possibilità di immettere i dati. Non vi è alcuna esperienza utente standard per la gestione di un negato l'accesso e sono disponibili molte opzioni possibili. Nell'applicazione di esempio, lo stato è impostato su un `HeartRateModel` oggetto singleton che, a sua volta, genera eventi rilevanti.

## <a name="model-view-and-controller"></a>Modello, visualizzazione e Controller

Per esaminare il `HeartRateModel` oggetto singleton, aprire il `HeartRateModel.cs` file:

```csharp
using System;
using HealthKit;
using Foundation;

namespace HKWork
{
        public class GenericEventArgs<T> : EventArgs
        {
                public T Value { get; protected set; }
                public DateTime Time { get; protected set; }

                public GenericEventArgs (T value)
                {
                        this.Value = value;
                        Time = DateTime.Now;
                }
        }

        public delegate void GenericEventHandler<T> (object sender,GenericEventArgs<T> args);

        public sealed class HeartRateModel : NSObject
        {
                private static volatile HeartRateModel singleton;
                private static object syncRoot = new Object ();

                private HeartRateModel ()
                {
                }

                public static HeartRateModel Instance {
                        get {
                                //Double-check lazy initialization
                                if (singleton == null) {
                                        lock (syncRoot) {
                                                if (singleton == null) {
                                                        singleton = new HeartRateModel ();
                                                }
                                        }
                                }

                                return singleton;
                        }
                }

                private bool enabled = false;

                public event GenericEventHandler<bool> EnabledChanged;
                public event GenericEventHandler<String> ErrorMessageChanged;
                public event GenericEventHandler<Double> HeartRateStored;

                public bool Enabled { 
                        get { return enabled; }
                        set {
                                if (enabled != value) {
                                        enabled = value;
                                        InvokeOnMainThread(() => EnabledChanged (this, new GenericEventArgs<bool>(value)));
                                }
                        }
                }

                public void PermissionsError(string msg)
                {
                        Enabled = false;
                        InvokeOnMainThread(() => ErrorMessageChanged (this, new GenericEventArgs<string>(msg)));
                }

                //Converts its argument into a strongly-typed quantity representing the value in beats-per-minute
                public HKQuantity HeartRateInBeatsPerMinute(ushort beatsPerMinute)
                {
                        var heartRateUnitType = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        var quantity = HKQuantity.FromQuantity (heartRateUnitType, beatsPerMinute);

                        return quantity;
                }
                        
                public void StoreHeartRate(HKQuantity quantity)
                {
                        var bpm = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        //Confirm that the value passed in is of a valid type (can be converted to beats-per-minute)
                        if (! quantity.IsCompatible(bpm))
                        {
                                InvokeOnMainThread(() => ErrorMessageChanged(this, new GenericEventArgs<string> ("Units must be compatible with BPM")));
                        }

                        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
                        var heartRateQuantityType = HKQuantityType.GetQuantityType (heartRateId);
                        var heartRateSample = HKQuantitySample.FromType (heartRateQuantityType, quantity, new NSDate (), new NSDate (), new HKMetadata());

                        using (var healthKitStore = new HKHealthStore ()) {
                                healthKitStore.SaveObject (heartRateSample, (success, error) => {
                                        InvokeOnMainThread (() => {
                                                if (success) {
                                                        HeartRateStored(this, new GenericEventArgs<Double>(quantity.GetDoubleValue(bpm)));
                                                } else {
                                                        ErrorMessageChanged(this, new GenericEventArgs<string>("Save failed"));
                                                }
                                                if (error != null) {
                                                        //If there's some kind of error, disable 
                                                        Enabled = false;
                                                        ErrorMessageChanged (this, new GenericEventArgs<string>(error.ToString()));
                                                }
                                        });
                                });
                        }
                }
        }
}

```

La prima sezione è codice boilerplate per la creazione di gestori eventi generici. La parte iniziale della `HeartRateModel` classe risulta inoltre boilerplate per la creazione di un oggetto singleton thread-safe.

Quindi, `HeartRateModel` espone gli eventi di 3: 

- `EnabledChanged` -Indica che archiviazione cuore velocità è stato abilitato o disabilitato (si noti che l'archiviazione verrà inizialmente disabilitata). 
- `ErrorMessageChanged` -Per questa app di esempio, si dispone di un modello di gestione degli errori molto semplice: una stringa con l'ultimo errore. 
- `HeartRateStored` -Generato quando una frequenza di cuore è archiviata nel database del Kit di integrità.

Si noti che l'ogni volta che questi eventi vengono generati, viene eseguita tramite `NSObject.InvokeOnMainThread()`, che consente ai sottoscrittori di aggiornare l'interfaccia utente. In alternativa, gli eventi potrebbero essere documentati come generati su thread in background e la responsabilità di garantire la compatibilità può essere lasciata ai relativi gestori. Poiché molte delle funzioni, ad esempio la richiesta di autorizzazione, sono asincroni ed eseguire i callback su thread non principale, thread considerazioni sono importanti nelle applicazioni di integrità Kit.

Il codice specifico del Kit di integrità in `HeartRateModel` in due funzioni `HeartRateInBeatsPerMinute()` e `StoreHeartRate()`. 

`HeartRateInBeatsPerMinute()` Converte il relativo argomento in un Kit di integrità fortemente tipizzato `HKQuantity`. Il tipo della quantità è quello specificato da di `HKQuantityTypeIdentifierKey.HeartRate` e le unità della quantità sono `HKUnit.Count` diviso `HKUnit.Minute` (in altre parole, l'unità è *è superiore al minuto*). 

Il `StoreHeartRate()` funzione accetta un `HKQuantity` (nell'applicazione di esempio, crea uno `HeartRateInBeatsPerMinute()` ). Per convalidare i dati, viene utilizzato il `HKQuantity.IsCompatible()` metodo, che restituisce `true` se l'unità dell'oggetto può essere convertita in unità nell'argomento. Se la quantità è stata creata con `HeartRateInBeatsPerMinute()` ovviamente restituirà `true`, ma anche restituirebbe `true` se la quantità sono stata creata come, ad esempio, *è superiore all'ora*. Più comunemente, `HKQuantity.IsCompatible()` può essere usato per la convalida di massa, distanza e energia che l'utente o un dispositivo potrebbe essere di input o visualizzare in un sistema di misurazione (ad esempio, in unità imperiali), ma che potrebbe essere archiviato in un altro sistema (ad esempio unità metriche). 

Una volta convalidata, la compatibilità della quantità di `HKQuantitySample.FromType()` metodo factory viene utilizzata per creare l'oggetto fortemente tipizzato `heartRateSample` oggetto. `HKSample` gli oggetti hanno una data di inizio e di fine. per le letture istantanee, questi valori devono essere lo stesso, come se fossero nell'esempio. L'esempio inoltre non imposta i dati di chiave-valore relativa `HKMetadata` argomento, ma una possibile usare codice, ad esempio il codice seguente per specificare il percorso del sensore:

```csharp
var hkm = new HKMetadata();
hkm.HeartRateSensorLocation = HKHeartRateSensorLocation.Chest;

```

Una volta il `heartRateSample` è stato creato, il codice crea una nuova connessione al database con il blocco. All'interno del blocco, il `HKHealthStore.SaveObject()` metodo nel tentativo di scrittura asincrona al database. La chiamata all'espressione lambda risulta attiva gli eventi rilevanti, ovvero `HeartRateStored` o `ErrorMessageChanged`.

Ora che il modello è stato programmato, è necessario vedere come il controller riflette lo stato del modello. Aprire il `HKWorkViewController.cs` file. Il costruttore collega semplicemente il `HeartRateModel` singleton ai metodi di gestione degli eventi (nuovamente, questa operazione in linea con le espressioni lambda, ma metodi separati rendono un po' più evidente lo scopo):

```csharp
public HKWorkViewController (IntPtr handle) : base (handle)
{
     HeartRateModel.Instance.EnabledChanged += OnEnabledChanged;
     HeartRateModel.Instance.ErrorMessageChanged += OnErrorMessageChanged;
     HeartRateModel.Instance.HeartRateStored += OnHeartBeatStored;
}

```

Ecco i gestori pertinenti:

```csharp
void OnEnabledChanged (object sender, GenericEventArgs<bool> args)
{
        StoreData.Enabled = args.Value;
        PermissionsLabel.Text = args.Value ? "Ready to record" : "Not authorized to store data.";
        PermissionsLabel.SizeToFit ();
}

void OnErrorMessageChanged (object sender, GenericEventArgs<string> args)
{
        PermissionsLabel.Text = args.Value;
}

void OnHeartBeatStored (object sender, GenericEventArgs<double> args)
{
        PermissionsLabel.Text = String.Format ("Stored {0} BPM", args.Value);
}

```

Ovviamente, in un'applicazione con un singolo controller, sarà possibile evitare la creazione di un oggetto modello separato e l'utilizzo di eventi per il flusso di controllo, ma l'utilizzo di oggetti del modello è più adatta per le applicazioni reali.

## <a name="running-the-sample-app"></a>Esecuzione dell'applicazione di esempio

Simulatore iOS non supporta il Kit di integrità. Il debug deve essere eseguito in un dispositivo fisico che eseguono iOS 8.

Collegare un dispositivo di sviluppo correttamente il provisioning di iOS 8 al sistema. Selezionare come destinazione di distribuzione in Visual Studio per Mac e scegliere dal menu **eseguire > Debug**.

> [!IMPORTANT]
> **Nota:** errori relativi al provisioning esporrà a questo punto. Per risolvere gli errori, esaminare la creazione e il Provisioning di una sezione di integrità Kit App precedente. I componenti sono: 
>
> - **Centro per sviluppatori iOS** -ID App esplicito & Kit integrità abilitazione del profilo di Provisioning. 
> - **Opzioni del progetto** -Bundle identificatore (ID App esplicita) & profilo di Provisioning.
> - **Codice sorgente** -Entitlements.plist & Info. plist

Supponendo che esegue il provisioning è stato impostato correttamente, verrà avviata l'applicazione. Quando raggiunge il relativo `OnActivated` (metodo), richiederà l'autorizzazione di integrità Kit. La prima volta, che questo viene rilevato dal sistema operativo, l'utente visualizzerà la finestra di dialogo seguenti:


[![](healthkit-images/image12.png "Questa finestra di dialogo verrà visualizzato l'utente")](healthkit-images/image12.png#lightbox)

Abilitare l'app per aggiornare i dati di cuore frequenza e l'app verrà visualizzato nuovamente. Il `ReactToHealthCarePermissions` callback verrà attivato in modo asincrono. In questo modo il `HeartRateModel’s` `Enabled` proprietà da modificare, che verrà generato il `EnabledChanged` evento, che provoca il `HKPermissionsViewController.OnEnabledChanged()` il gestore eventi per l'esecuzione, che consente il `StoreData` pulsante. Nel diagramma seguente viene illustrata la sequenza:


[![](healthkit-images/image13.png "Questo diagramma mostra la sequenza di eventi")](healthkit-images/image13.png#lightbox)

Premere il **Record** pulsante. In questo modo il `StoreData_TouchUpInside()` gestore per l'esecuzione, che verrà eseguito un tentativo di analizzare il valore della `heartRate` campo di testo, convertire in un `HKQuantity` tramite descritta in precedenza `HeartRateModel.HeartRateInBeatsPerMinute()` funzione e passare tale quantità `HeartRateModel.StoreHeartRate()`. Come descritto in precedenza, questo verrà eseguito un tentativo di archiviare i dati e verrà generato uno un `HeartRateStored` o `ErrorMessageChanged` evento.

Fare doppio clic su di **Home** pulsante sul dispositivo e aprire l'app di integrità. Fare clic su di **origini** scheda per visualizzare l'app di esempio elencato. Selezionare tale collegamento e impedire l'autorizzazione per l'aggiornamento dati cuore velocità. Fare doppio clic su di **Home** pulsante e tornare all'app. In questo caso, `ReactToHealthCarePermissions()` viene chiamato, ma questa volta, perché viene negato l'accesso, il **DatiMemorizzati** pulsante verrà disattivato (si noti che questo errore si verifica in modo asincrono e la modifica nell'interfaccia utente può essere visibile all'utente finale).

## <a name="advanced-topics"></a>Argomenti avanzati

Lettura dei dati del Kit di integrità database è molto simile alla scrittura di dati: uno specifica i tipi di dati di un tentativo di accesso, le richieste di autorizzazione, e se tale autorizzazione viene concessa, i dati sono disponibili, con la conversione automatica per le unità compatibile di unità di misura.

Sono disponibili numerose funzioni di query più sofisticate che consentono le query basate sul predicato e le query che eseguono aggiornamenti quando vengono aggiornati i dati rilevanti. 

Gli sviluppatori di applicazioni di integrità Kit devono consultare la sezione di integrità Kit di Apple [App rivedere le linee guida](https://developer.apple.com/app-store/review/guidelines/#healthkit).

Una volta la sicurezza e i modelli di sistema di tipi noti, l'archiviazione e lettura dei dati nel database condiviso integrità Kit è piuttosto semplice. Molte delle funzioni nel Kit di integrità operano in modo asincrono e gli sviluppatori di applicazioni devono scrivere i propri programmi in modo appropriato.

Al momento della stesura di questo articolo, non è attualmente disponibile equivalente al Kit di integrità in Android o Windows Phone.

## <a name="summary"></a>Riepilogo

In questo articolo che abbiamo visto come Kit integrità consente alle applicazioni di archiviare, recuperare e stato della condivisione informazioni, mantenendo al contempo un'app di integrità standard che consente l'accesso utente e il controllo su questi dati. 

Abbiamo visto anche come l'integrità dei dati, sicurezza e privacy sta eseguendo l'override di problemi per le informazioni relative a integrità e l'App utilizzando il Kit di integrità deve gestire l'aumento della complessità in aspetti della gestione dell'applicazione (provisioning) (tipo di integrità del Kit di codifica sistema) e (il controllo utente delle autorizzazioni tramite le finestre di dialogo di sistema e app di integrità) di esperienza utente. 

Infine, abbiamo un'occhiata a un'implementazione semplice del Kit di integrità tramite l'app di esempio incluso che scrive dati heartbeat nell'archivio integrità Kit e che dispone di una progettazione asincrona in grado di riconoscere.

## <a name="related-links"></a>Collegamenti correlati

- [HKWork (esempio)](https://developer.xamarin.com/samples/monotouch/ios8/IntroToHealthKit/)
- [Introduzione a iOS 8](~/ios/platform/introduction-to-ios8.md)
