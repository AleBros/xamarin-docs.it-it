---
title: HealthKit in xamarin. IOS
description: Questo documento descrive HealthKit, un framework introdotto in iOS 8 che fornisce un archivio dati centralizzato, coordinato e sicuro per le informazioni correlate all'integrità. Viene descritto come effettuare il provisioning di un'app di HealthKit e spiega come scrivere codice che usa il framework di HealthKit.
ms.prod: xamarin
ms.assetid: E3927A21-507C-43BA-A2AD-957716BA9B52
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: df0b0e8dd57129917f2d8dab07115551ca675acf
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109224"
---
# <a name="healthkit-in-xamarinios"></a>HealthKit in xamarin. IOS

Kit di integrità fornisce un archivio dati sicuro per informazioni correlate all'integrità dell'utente. Integrità Kit App può, con l'autorizzazione esplicita dell'utente, leggere e scrivere in questo archivio dati e ricevere notifiche quando vengono aggiunti dati pertinenti. Le app possono presentare i dati o dell'utente è possibile usare app integrità specificato di Apple per visualizzare un dashboard di tutti i dati.

Poiché correlate all'integrità dei dati sono così sensibili e cruciali, Kit di integrità è fortemente tipizzata, con le unità di misura e un'associazione esplicita con il tipo di informazioni registrate (ad esempio, a livello di glucosio sangue o frequenza cuore). Inoltre, le app Kit di integrità devono usare i diritti espliciti, necessario richiedere l'accesso a determinati tipi di informazioni e l'utente deve concedere esplicitamente all'app l'accesso a questi tipi di dati.

Questo articolo descrive:

- Requisiti di sicurezza del Kit di integrità, tra cui il provisioning dell'applicazione e che richiede l'autorizzazione utente per accedere al database di integrità Kit;
- Sistema di tipi del Kit di integrità, riducendo al minimo la possibilità di applicare correttamente oppure, è preferibile formattarli dati;
- La scrittura nell'archivio dati di integrità Kit condiviso e a livello di sistema.

Questo articolo non illustra argomenti più avanzati, ad esempio una query sul database, la conversione tra unità di misura o la ricezione delle notifiche di nuovi dati.

In questo articolo, verranno creati un'applicazione di esempio per registrare il tasso di cuore dell'utente:

[![](healthkit-images/image01.png "Un'applicazione di esempio per registrare il tasso di cuore utenti")](healthkit-images/image01.png#lightbox)

## <a name="requirements"></a>Requisiti

Per completare la procedura descritta in questo articolo sono necessari i seguenti:

- **Xcode 7 e iOS 8 (o versione successiva)** – Xcode e API iOS più recenti di Apple devono essere installati e configurati nel computer dello sviluppatore.
- **Visual Studio per Mac o Visual Studio** : la versione più recente di Visual Studio per Mac deve essere installata e configurata sul computer dello sviluppatore.
- **iOS 8 (o versione successiva) dispositivo** : un dispositivo iOS in esecuzione la versione più recente di iOS 8 o versione successiva per il test.

> [!IMPORTANT]
> Kit di integrità è stato introdotto in iOS 8. Attualmente, Kit di integrità non è disponibile nel simulatore iOS e il debug richiede la connessione a un dispositivo iOS fisico.




## <a name="creating-and-provisioning-a-health-kit-app"></a>Creazione e Provisioning di un'App di Kit di integrità
Prima di un'applicazione per Xamarin iOS 8 è possibile usare l'API di HealthKit, deve essere correttamente configurato e il provisioning. Questa sezione illustra i passaggi necessari per configurare correttamente un'applicazione Xamarin.

Integrità Kit App richiedono:

- L'impostazione esplicita **App ID**.
- Oggetto **profilo di Provisioning** associati che esplicita **ID App** e con **integrità Kit** autorizzazioni.
- Un' `Entitlements.plist` con un `com.apple.developer.healthkit` vlastnosti typu `Boolean` impostato su `Yes`.
- Un' `Info.plist` la cui `UIRequiredDeviceCapabilities` chiave contiene una voce con la `String` valore `healthkit`.
- Il `Info.plist` deve avere anche le voci appropriate sulla privacy-descrizione: una `String` spiegazione per la chiave `NSHealthUpdateUsageDescription` se l'app sta per scrivere i dati e un `String` spiegazione della chiave `NSHealthShareUsageDescription` se l'app sta per leggere il Kit di integrità dati.

Per altre informazioni sul provisioning di un'app per iOS, il [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) articolo di Xamarin **introduttiva** serie descrive la relazione tra i certificati per gli sviluppatori, gli ID delle App, I profili di provisioning e dei diritti delle App.

<a name="explicit-appid" />

### <a name="explicit-app-id-and-provisioning-profile"></a>ID App esplicito e il profilo di Provisioning

La creazione di un'esplicita **App ID** e un oggetto appropriato **profilo di Provisioning** viene eseguita all'interno di Apple [iOS Dev Center](https://developer.apple.com/devcenter/ios/index.action). 

Correnti **App IDs** sono elencati all'interno di [certificati, identificatori e profili](https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action) sezione del centro per sviluppatori di. Spesso, questo elenco verranno visualizzati **ID** i valori di `*`, a indicare che il **App ID** - **nome** può essere utilizzato con un numero qualsiasi di suffissi. Tali *Wildcard App IDs* non può essere usato con integrità Kit.
 
Creazione esplicita **App ID**, fare clic sul **+** pulsante in alto a destra per accedere al **registrare l'ID dell'App iOS** pagina:


[![](healthkit-images/image02.png "Registrazione di un'app nel portale Apple Developer")](healthkit-images/image02.png#lightbox)

Come illustrato nell'immagine precedente, dopo la creazione di una descrizione dell'app, usare il **Explicit App ID** sezione per creare un ID per l'applicazione. Nel **servizi App** sezione controllo **integrità Kit** nel **Abilita servizi** sezione.

Al termine, premere il **continuazione** pulsante per registrare il **ID App** nell'account. Verrà visualizzata nuovamente la **certificati, identificatori e profili** pagina. Fare clic su **profili di Provisioning** consentono di accedere all'elenco dei profili di provisioning correnti e scegliere il **+** pulsante nell'angolo superiore destro per accedere al **Add iOS Il profilo di provisioning** pagina. Selezionare il **iOS App Development** opzione e fare clic su **continua** per ottenere il **ID dell'App selezionare** pagina. In questo caso, selezionare l'impostazione esplicita **App ID** specificata in precedenza:


[![](healthkit-images/image03.png "Selezionare l'ID App esplicito")](healthkit-images/image03.png#lightbox)

Fare clic su **continuazione** e di lavoro tramite le schermate rimanenti, in cui si specificherà il **uno o più certificati per gli sviluppatori**, **dispositivi**e un **nome** per questo **profilo di Provisioning**:

[![](healthkit-images/image04.png "Generare il profilo di Provisioning")](healthkit-images/image04.png#lightbox)

Fare clic su **genera** e attendere la creazione del profilo. Scaricare il file e fare doppio clic per installarlo in Xcode. È possibile confermare dello installazione in **Xcode > Preferenze > account > Visualizza dettagli...** Verrà visualizzato il profilo di provisioning appena installato, e deve avere l'icona per il Kit di integrità e tutti gli altri servizi speciali relative **Entitlement** riga:

[![](healthkit-images/image05.png "Visualizzazione del profilo in Xcode")](healthkit-images/image05.png#lightbox)

<a name="associating-appid" />

### <a name="associating-the-app-id-and-provisioning-profile-with-your-xamarinios-app"></a>Associando l'ID dell'App e il Provisioning del profilo con l'App xamarin. IOS

Dopo aver creato e installato appropriata **profilo di Provisioning** come descritto, normalmente sarebbe possibile creare una soluzione in Visual Studio per Mac o Visual Studio. Integrità Kit accesso è disponibile per qualsiasi iOS C# o F# project.

Piuttosto in dettaglio il processo di creazione manuale di un progetto Xamarin iOS 8, aprire l'app di esempio associata a questo articolo (che include uno Storyboard e un codice predefiniti). Per associare l'app di esempio con il Kit di integrità abilitata **profilo di Provisioning**, nella **riquadro della soluzione**, pulsante destro del mouse sul progetto e visualizzare relativo **opzioni** finestra di dialogo. Passare al **applicazione iOS** pannello e immettere l'impostazione esplicita **App ID** dell'App creata in precedenza **identificatore del Bundle**:

[![](healthkit-images/image06.png "Immettere l'ID App esplicito")](healthkit-images/image06.png#lightbox)

Passare ora al **firma del Bundle iOS** pannello. Il recente installata **profilo di Provisioning**, con l'associazione per l'impostazione esplicita **ID App**, sarà ora disponibile come il **profilo di Provisioning**:

[![](healthkit-images/image07.png "Selezionare il profilo di Provisioning")](healthkit-images/image07.png#lightbox)

Se il **profilo di Provisioning** non è disponibile, verificare il **identificatore del Bundle** nel **applicazione iOS** pannello rispetto a cui specificato nella **iOS DEV Center** e che il **profilo di Provisioning** sia installato (**Xcode > Preferenze > account > Visualizza dettagli...** ).

Quando il Kit di integrità abilitati **profilo di Provisioning** è selezionata, fare clic su **OK** per chiudere la finestra di dialogo Opzioni progetto.

### <a name="entitlementsplist-and-infoplist-values"></a>Entitlements. plist e i valori di Info. plist

L'app di esempio include un `Entitlements.plist` file (che è necessario per le app è abilitato integrità Kit) e non incluso in ogni modello di progetto. Se il progetto non include i diritti, pulsante destro del mouse sul progetto, scegliere **File > Nuovo File... > iOS > entitlements. plist** per aggiungerne uno manualmente.

In definitiva, la `Entitlements.plist` deve avere la coppia chiave / valore seguente:

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

Analogamente, il `Info.plist` per l'app deve essere presente un valore di `healthkit` associato il `UIRequiredDeviceCapabilities` chiave:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
<string>armv7</string>
    <string>healthkit</string>
</array>

```

L'applicazione di esempio fornito in questo articolo include una preconfigurata `Entitlements.plist` che include tutte le chiavi necessarie.

<a name="programming" />

## <a name="programming-health-kit"></a>Kit di integrità di programmazione

L'archivio dati di integrità Kit è un archivio dati privato, specifici dell'utente che è condiviso tra le app. Poiché le informazioni di integrità sono così sensibili, l'utente deve eseguire passaggi positivi per consentire l'accesso ai dati. Questo tipo di accesso possono essere parziale (scrittura ma non in lettura, l'accesso per alcuni tipi di dati, ma non altri, e così via) e potrebbero essere revocate in qualsiasi momento. Le applicazioni Kit di integrità devono essere scritto un approccio difensivo, con la consapevolezza che molti utenti saranno convinto circa l'archiviazione delle loro informazioni correlate all'integrità.

I dati di integrità Kit sono limitati a tipi specificati di Apple. Questi tipi sono definiti rigidamente: alcuni, ad esempio tipo di sangue, sono limitati a particolari valori di enumerazione Apple fornito, mentre altri utenti combinare una grandezza con un'unità di misura (ad esempio grammi calorie e litri). Persino i dati che condividono un'unità di misura compatibile sono distinti da loro `HKObjectType`; ad esempio, il sistema di tipi verrà rilevato un tentativo di errata per archiviare un `HKQuantityTypeIdentifier.NumberOfTimesFallen` valore da un campo previsto un `HKQuantityTypeIdentifier.FlightsClimbed` anche se usano entrambi il` HKUnit.Count` unità di misura.

I tipi possa essere archiviati nell'archivio dati integrità Kit sono tutte le sottoclassi di `HKObjectType`. `HKCharacteristicType` oggetti archiviano biologici sesso, il tipo di sangue e data di nascita. Tuttavia, sono più comuni `HKSampleType` oggetti che rappresentano i dati campionati in un momento specifico o in un periodo di tempo. 

[![](healthkit-images/image08.png "Grafico di oggetti HKSampleType")](healthkit-images/image08.png#lightbox)

`HKSampleType` è astratta e ha quattro sottoclassi concrete. È attualmente un solo tipo di `HKCategoryType` dati, ovvero sospensione Analysis. La maggior parte dei dati nel Kit di integrità sono di tipo `HKQuantityType` e archiviano i dati in `HKQuantitySample` gli oggetti che vengono creati usando il familiare modello di progettazione di Factory:

[![](healthkit-images/image09.png "La maggior parte dei dati nel Kit di integrità sono di tipo HKQuantityType e archiviano i dati negli oggetti HKQuantitySample")](healthkit-images/image09.png#lightbox)

`HKQuantityType` tipi compreso tra `HKQuantityTypeIdentifier.ActiveEnergyBurned` a `HKQuantityTypeIdentifier.StepCount`. 

<a name="requesting-permission" />

### <a name="requesting-permission-from-the-user"></a>Richiesta dell'autorizzazione da parte dell'utente

Gli utenti finali devono eseguire passaggi positivi per consentire a un'app di leggere o scrivere i dati del Kit di integrità. Questa operazione viene eseguita tramite l'app di integrità che è preinstallato nei dispositivi iOS 8. La prima volta un'app Kit di integrità viene eseguita, viene visualizzato con un sistema controllato **integrità accesso** finestra di dialogo:

[![](healthkit-images/image10.png "Viene visualizzata una finestra di dialogo di accesso di integrità e controllato dal sistema")](healthkit-images/image10.png#lightbox)

In un secondo momento, l'utente può modificare le autorizzazioni usando l'integrità dell'app **origini** finestra di dialogo:

[![](healthkit-images/image11.png "L'utente può modificare le autorizzazioni nella finestra Origini di integrità delle App")](healthkit-images/image11.png#lightbox)

Poiché le informazioni di integrità sono estremamente sensibili, gli sviluppatori di app devono scrivere i propri programmi modo sicuro, nella previsione che le autorizzazioni verranno rifiutate e modificate durante l'esecuzione dell'app. Il linguaggio più comune consiste nel richiedere le autorizzazioni nel `UIApplicationDelegate.OnActivated` (metodo) e quindi modificare l'interfaccia utente come appropriato.

### <a name="permissions-walkthrough"></a>Procedura dettagliata di autorizzazioni

Nel progetto con provisioning integrità Kit, aprire il `AppDelegate.cs` file. Si noti che l'istruzione che usa `HealthKit`; nella parte superiore del file.


Il codice seguente è relativo alle autorizzazioni integrità Kit:

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

Tutto il codice in questi metodi poteva essere eseguita inline `OnActivated`, ma l'app di esempio vengono utilizzati metodi separati per rendere più chiaro il loro scopo: `ValidateAuthorization()` include i passaggi necessari per richiedere l'accesso ai specifici e tipi di scrittura (lettura, se lo si desidera l'app) e `ReactToHealthCarePermissions()` corrisponde a un callback che viene attivato dopo che l'utente ha interagito con la finestra di dialogo autorizzazioni nel Health.app.

Il processo di `ValidateAuthorization()` consiste nel compilare il set di `HKObjectTypes` che l'app scriverà e richiedere l'autorizzazione per aggiornare i dati. Nell'app di esempio, il `HKObjectType` è per la chiave `KHQuantityTypeIdentifierKey.HeartRate`. Questo tipo viene aggiunto al set `typesToWrite`, mentre il set `typesToRead` viene lasciato vuoto. Questi set e un riferimento per la `ReactToHealthCarePermissions()` callback, viene passato a `HKHealthStore.RequestAuthorizationToShare()`.

Il `ReactToHealthCarePermissions()` callback verrà chiamato dopo che l'utente ha interagito con la finestra di dialogo delle autorizzazioni e viene passato a due tipi di informazioni: un `bool` valore che sarà `true` se l'utente ha interagito con la finestra di dialogo delle autorizzazioni e un' `NSError`che, se diverso da null, indica un tipo di errore associato a presentare la finestra di dialogo autorizzazioni.

> [!IMPORTANT]
> Per stabilire chiaramente gli argomenti a questa funzione: la _success_ e _errore_ parametri non indicano se l'utente ha concesso l'autorizzazione per accedere ai dati di integrità Kit! Indicano solo che l'utente ha ottenuto la possibilità di consentire l'accesso ai dati.

Per verificare se l'app abbia accesso ai dati, il `HKHealthStore.GetAuthorizationStatus()` viene usato, passando `HKQuantityTypeIdentifierKey.HeartRate`. L'app in base allo stato restituito, Abilita o disabilita la possibilità di immettere i dati. Non vi è alcuna esperienza utente standard per la gestione di un attacco denial of access e sono disponibili molte opzioni possibili. Nell'app di esempio, lo stato è impostato su un `HeartRateModel` oggetto singleton, che a sua volta, genera gli eventi pertinenti.

## <a name="model-view-and-controller"></a>Modello di visualizzazione e Controller

Per rivedere le `HeartRateModel` oggetto singleton, aprirlo il `HeartRateModel.cs` file:

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

La prima sezione è codice boilerplate per la creazione di gestori e gli eventi generici. La parte iniziale del `HeartRateModel` classe è anche standard per la creazione di un oggetto singleton thread-safe.

Quindi, `HeartRateModel` espone 3 eventi: 

- `EnabledChanged` -Indica che archiviazione cuore velocità è stato abilitato o disabilitato (si noti che archiviazione verrà inizialmente disabilitata). 
- `ErrorMessageChanged` -Per questa app di esempio, si dispone di un modello di gestione degli errori molto semplice: una stringa con l'ultimo errore. 
- `HeartRateStored` -Generato quando una tariffa di base vengono archiviata nel database del Kit di integrità.

Si noti che l'ogni volta che questi eventi vengono attivati, bensì tramite `NSObject.InvokeOnMainThread()`, che consente ai sottoscrittori di aggiornare l'interfaccia utente. In alternativa, gli eventi potrebbero essere documentati come viene generato sul thread in background e la responsabilità di garantire la compatibilità può essere lasciata ai gestori. Thread considerazioni sono importanti in applicazioni Kit integrità perché molte delle funzioni, ad esempio la richiesta di autorizzazione, sono asincroni e vengono eseguite le richiamate in thread non principale.

Il codice specifico Heath Kit nel `HeartRateModel` le due funzioni le novità `HeartRateInBeatsPerMinute()` e `StoreHeartRate()`. 

`HeartRateInBeatsPerMinute()` Converte il relativo argomento in un Kit di integrità fortemente tipizzata `HKQuantity`. Il tipo della quantità è quello specificato dal `HKQuantityTypeIdentifierKey.HeartRate` e sono le unità della quantità `HKUnit.Count` diviso `HKUnit.Minute` (in altre parole, l'unità è *battute al minuto*). 

Il `StoreHeartRate()` funzione accetta un `HKQuantity` (nell'app di esempio, crea uno `HeartRateInBeatsPerMinute()` ). Per convalidare i dati, Usa la `HKQuantity.IsCompatible()` metodo, che restituisce `true` se le unità dell'oggetto possono essere convertite in unità nell'argomento. Se la quantità è stata creata con `HeartRateInBeatsPerMinute()` ovviamente restituirà `true`, ma anche restituirebbe `true` se la quantità sono stata creata come, ad esempio, *battute Per ora*. Più comunemente, `HKQuantity.IsCompatible()` può essere usato per convalidare massa, distanza ed energia che l'utente o un dispositivo potrebbe essere di input o visualizzare in un sistema di misura (ad esempio unità imperiale), ma che possono essere archiviati in un altro sistema (ad esempio unità metriche). 

Dopo aver convalidata la compatibilità delle quantità, il `HKQuantitySample.FromType()` metodo factory viene usato per creare l'oggetto fortemente tipizzato `heartRateSample` oggetto. `HKSample` gli oggetti hanno una data di inizio e fine. per le letture istantanee, questi valori devono essere uguali, usate nell'esempio. L'esempio inoltre non imposta tutti i dati chiave-valore relativo `HKMetadata` argomento, ma uno è stato possibile usare codice, ad esempio il codice seguente per specificare il percorso del sensore:

```csharp
var hkm = new HKMetadata();
hkm.HeartRateSensorLocation = HKHeartRateSensorLocation.Chest;

```

Una volta il `heartRateSample` è stato creato, il codice crea una nuova connessione al database con il blocco. All'interno del blocco, il `HKHealthStore.SaveObject()` metodo tenterà la scrittura asincrona nel database. La chiamata all'espressione lambda risulta attiva gli eventi rilevanti, ovvero `HeartRateStored` o `ErrorMessageChanged`.

Ora che è stato programmato il modello, è possibile visualizzare il modo in cui il controller riflette lo stato del modello. Aprire il `HKWorkViewController.cs` file. Il costruttore collega semplicemente il `HeartRateModel` singleton ai metodi di gestione degli eventi (anche in questo caso, questa operazione può essere eseguita inline con espressioni lambda, ma metodi separati rendono l'obiettivo è un po' più ovvio):

```csharp
public HKWorkViewController (IntPtr handle) : base (handle)
{
     HeartRateModel.Instance.EnabledChanged += OnEnabledChanged;
     HeartRateModel.Instance.ErrorMessageChanged += OnErrorMessageChanged;
     HeartRateModel.Instance.HeartRateStored += OnHeartBeatStored;
}

```

Ecco i gestori rilevanti:

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

In un'applicazione con un singolo controller, ovviamente, anche se è possibile evitare la creazione di un oggetto model separata e l'utilizzo di eventi per il flusso di controllo, ma l'uso di oggetti del modello è più appropriato per le app reali.

## <a name="running-the-sample-app"></a>L'esecuzione dell'App di esempio

Il simulatore iOS non supporta il Kit di integrità. Il debug deve essere eseguito in un dispositivo fisico che eseguono iOS 8.

Collegare un dispositivo di sviluppo eseguito correttamente il provisioning iOS 8 nel sistema. Selezionarla come destinazione di distribuzione in Visual Studio per Mac e scegliere dal menu **Esegui > Debug**.

> [!IMPORTANT]
> Gli errori relativi al provisioning esporrà a questo punto. Per risolvere gli errori, esaminare la creazione e Provisioning di una sezione di integrità Kit App precedente. I componenti sono: 
>
> - **Centro per sviluppatori iOS** -ID App esplicito & Kit integrità abilitato il profilo di Provisioning. 
> - **Opzioni progetto** -identificatore del Bundle (ID App esplicito) & profilo di Provisioning.
> - **Codice sorgente** -entitlements. plist & Info. plist

Supponendo che effettua il provisioning è stato impostato correttamente, verrà avviata l'applicazione. Quando raggiunge il `OnActivated` (metodo), richiederà l'autorizzazione di integrità Kit. La prima volta che si è rilevato dal sistema operativo, l'utente visualizzerà la finestra di dialogo seguente:


[![](healthkit-images/image12.png "L'utente verrà visualizzata questa finestra di dialogo")](healthkit-images/image12.png#lightbox)

Abilitare l'app per aggiornare i dati di velocità cuore e l'app verrà visualizzato nuovamente. Il `ReactToHealthCarePermissions` callback verrà attivato in modo asincrono. Ciò causerà il `HeartRateModel’s` `Enabled` proprietà da modificare, che genererà le `EnabledChanged` evento, che provoca l'il `HKPermissionsViewController.OnEnabledChanged()` gestore eventi per l'esecuzione, che consente il `StoreData` pulsante. Il diagramma seguente illustra la sequenza:


[![](healthkit-images/image13.png "Questo diagramma mostra la sequenza di eventi")](healthkit-images/image13.png#lightbox)

Premere il **Record** pulsante. Ciò causerà il `StoreData_TouchUpInside()` gestore per l'esecuzione, che verrà eseguito un tentativo di analizzare il valore del `heartRate` campo di testo, convertire in un `HKQuantity` tramite descritte in precedenza `HeartRateModel.HeartRateInBeatsPerMinute()` funzione e passare tale quantità a `HeartRateModel.StoreHeartRate()`. Come illustrato in precedenza, questo verrà eseguito un tentativo di archiviare i dati e genera una un' `HeartRateStored` o `ErrorMessageChanged` evento.

Fare doppio clic il **Home** pulsante sul dispositivo e aprire l'app di integrità. Scegliere il **origini** scheda e si visualizzerà l'app di esempio indicato. Selezionare tale collegamento e non consentire l'autorizzazione per aggiornare i dati di velocità di cuore. Fare doppio clic il **Home** pulsante e tornare all'app. Ancora una volta `ReactToHealthCarePermissions()` verrà chiamato, ma questa volta, perché l'accesso è negato, il **DatiMemorizzati** pulsante verrà disabilitato (si noti che questo errore si verifica in modo asincrono e la modifica nell'interfaccia utente potrebbe essere visibile all'utente finale).

## <a name="advanced-topics"></a>Argomenti avanzati

La lettura dei dati del Kit di integrità di database è molto simile a la scrittura dei dati: uno specifica i tipi di dati uno sta provando ad accedere, l'autorizzazione delle richieste, e se tale autorizzazione viene concessa, i dati sono disponibili, la conversione automatica a unità compatibile di unità di misura.

Esistono una serie di funzioni di query più sofisticate che consentono le query basate sul predicato e le query che eseguono gli aggiornamenti quando vengono aggiornati i dati rilevanti. 

Gli sviluppatori di applicazioni di integrità Kit dovrebbero vedere la sezione integrità Kit di Apple [linee guida di revisione di App](https://developer.apple.com/app-store/review/guidelines/#healthkit).

Una volta che la sicurezza e i modelli di sistema di tipi vengono riconosciuti, l'archiviazione e la lettura dei dati nel database integrità Kit condiviso è piuttosto semplice. Molte delle funzioni all'interno di integrità Kit operano in modo asincrono e agli sviluppatori di applicazioni devono scrivere i propri programmi in modo appropriato.

Al momento della stesura di questo articolo, non è attualmente disponibile equivalente al Kit di integrità in Android o Windows Phone.

## <a name="summary"></a>Riepilogo

In questo articolo che abbiamo visto come integrità Kit consente alle applicazioni di archiviare, recuperare e l'integrità di condivisione di informazioni correlate, mantenendo al contempo un'app di integrità standard che consente l'accesso degli utenti e controllare i dati. 

Abbiamo visto anche come l'integrità dei dati, sicurezza e privacy sta eseguendo l'override di problemi per informazioni correlate all'integrità e le app con il Kit di integrità devono gestire l'aumento in termini di complessità in aspetti della gestione dell'applicazione (provisioning), la codifica (tipo di integrità del Kit sistema) e (il controllo utente delle autorizzazioni tramite le finestre di dialogo di sistema e app di integrità) di esperienza utente. 

Infine, abbiamo un'occhiata a un'implementazione semplice del Kit di integrità tramite l'app di esempio incluso che scrive i dati di heartbeat per l'archivio integrità Kit e dispone di una progettazione asincrona riconoscere.

## <a name="related-links"></a>Collegamenti correlati

- [HKWork (esempio)](https://developer.xamarin.com/samples/monotouch/ios8/IntroToHealthKit/)
- [Introduzione a iOS 8](~/ios/platform/introduction-to-ios8.md)
