---
title: HealthKit in Novell. iOS
description: Questo documento descrive HealthKit, un Framework introdotto in iOS 8 che fornisce un archivio dati centralizzato, coordinato e sicuro per le informazioni relative allo stato di integrità. Viene illustrato come eseguire il provisioning di un'app HealthKit e come scrivere codice che usa il Framework HealthKit.
ms.prod: xamarin
ms.assetid: E3927A21-507C-43BA-A2AD-957716BA9B52
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 8090cb3c694083be4ef12294799d6aadf26b6038
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84569127"
---
# <a name="healthkit-in-xamarinios"></a>HealthKit in Novell. iOS

Il kit di integrità fornisce un archivio dati protetto per le informazioni relative all'integrità dell'utente. Le app del kit di integrità possono, con l'autorizzazione esplicita dell'utente, leggere e scrivere in questo archivio dati e ricevere notifiche quando vengono aggiunti dati pertinenti. Le app possono presentare i dati oppure l'utente può usare l'app per l'integrità fornita da Apple per visualizzare un dashboard di tutti i relativi dati.

Poiché i dati correlati all'integrità sono così sensibili e cruciali, il kit di integrità è fortemente tipizzato, con unità di misura e un'associazione esplicita al tipo di informazioni registrate (ad esempio, il livello di glucosio del sangue o la frequenza cardiaca). Inoltre, le app del kit di integrità devono usare diritti espliciti, devono richiedere l'accesso a particolari tipi di informazioni e l'utente deve concedere esplicitamente all'app l'accesso a tali tipi di dati.

In questo articolo vengono presentate le seguenti operazioni:

- Requisiti di sicurezza del kit di integrità, inclusi il provisioning delle applicazioni e l'autorizzazione utente per l'accesso al database del kit di integrità;
- Sistema di tipi del kit di integrità, che consente di ridurre al minimo la possibilità di applicare o interpretare erroneamente i dati.
- Scrittura nell'archivio dati del kit di integrità condiviso a livello di sistema.

In questo articolo non vengono trattati argomenti più avanzati, ad esempio l'esecuzione di query sul database, la conversione tra unità di misura o la ricezione di notifiche di nuovi dati.

In questo articolo verrà creata un'applicazione di esempio per registrare la frequenza cardiaca dell'utente:

[![](healthkit-images/image01.png "A sample application to record the users heart rate")](healthkit-images/image01.png#lightbox)

## <a name="requirements"></a>Requisiti

Per completare i passaggi illustrati in questo articolo, sono necessari gli elementi seguenti:

- **Xcode 7 e iOS 8 (o versione successiva)** : le API più recenti di Apple Xcode e iOS devono essere installate e configurate nel computer dello sviluppatore.
- **Visual Studio per Mac o Visual Studio** : la versione più recente di Visual Studio per Mac deve essere installata e configurata nel computer dello sviluppatore.
- **dispositivo iOS 8 (o versione successiva)** : un dispositivo iOS che esegue la versione più recente di iOS 8 o versione successiva per il test.

> [!IMPORTANT]
> Il kit di integrità è stato introdotto in iOS 8. Attualmente, il kit di integrità non è disponibile nel simulatore iOS e il debug richiede la connessione a un dispositivo iOS fisico.

## <a name="creating-and-provisioning-a-health-kit-app"></a>Creazione e provisioning di un'app del kit di integrità
Prima che un'applicazione Novell iOS 8 possa usare l'API HealthKit, deve essere configurata correttamente e sottoposta a provisioning. In questa sezione vengono illustrati i passaggi necessari per configurare correttamente l'applicazione Novell.

Le app del kit di integrità richiedono:

- **ID app**esplicito.
- Un **profilo di provisioning** associato a tale **ID app** esplicito e con autorizzazioni del **Kit integrità** .
- `Entitlements.plist`Con una `com.apple.developer.healthkit` proprietà di tipo `Boolean` impostata su `Yes` .
- Oggetto la `Info.plist` cui `UIRequiredDeviceCapabilities` chiave contiene una voce con il `String` valore `healthkit` .
- Il `Info.plist` deve avere anche le voci di spiegazione della privacy appropriate: una `String` spiegazione della chiave `NSHealthUpdateUsageDescription` se l'app scriverà i dati e una `String` spiegazione della chiave `NSHealthShareUsageDescription` se l'app legge i dati del kit di integrità.

Per altre informazioni sul provisioning di un'app per iOS, l'articolo sul [provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md) nella serie **Introduzione** di Novell descrive la relazione tra certificati per sviluppatori, ID app, profili di provisioning e diritti delle app.

<a name="explicit-appid"></a>

### <a name="explicit-app-id-and-provisioning-profile"></a>ID app esplicito e profilo di provisioning

La creazione di un **ID app** esplicito e di un **profilo di provisioning** appropriato viene eseguita nel centro per [sviluppatori iOS](https://developer.apple.com/devcenter/ios/index.action)di Apple. 

Gli **ID app** correnti sono elencati nella sezione [Certificates, Identifiers & Profiles](https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action) del Dev Center. In questo elenco vengono spesso visualizzati i valori **ID** di `*` , a indicare che il nome dell' **ID app**  -  **Name** può essere utilizzato con un numero qualsiasi di suffissi. Questi *ID app con caratteri jolly* non possono essere usati con Health Kit.

Per creare un **ID app**esplicito, fare clic sul **+** pulsante in alto a destra per passare alla pagina **registra ID app iOS** :

[![](healthkit-images/image02.png "Registering an app on the Apple Developer Portal")](healthkit-images/image02.png#lightbox)

Come illustrato nell'immagine precedente, dopo aver creato una descrizione dell'app, usare la sezione **ID app esplicita** per creare un ID per l'applicazione. Nella sezione **Servizi app** , controllare il **Kit di integrità** nella sezione **abilitare i servizi** .

Al termine, fare clic sul pulsante **continue (continua** ) per registrare l' **ID app** nell'account. Verrà visualizzata di nuovo la pagina **certificati, identificatori e profili** . Fare clic su **profili di provisioning** per passare all'elenco dei profili di provisioning correnti, quindi fare clic sul **+** pulsante nell'angolo in alto a destra per passare alla pagina **Aggiungi profilo di provisioning iOS** . Selezionare l'opzione **sviluppo app iOS** e fare clic su **continua** per passare alla pagina **Seleziona ID app** . Qui, selezionare l' **ID app** esplicito specificato in precedenza:

[![](healthkit-images/image03.png "Select the explicit App ID")](healthkit-images/image03.png#lightbox)

Fare clic su **continua** e utilizzare le schermate rimanenti, in cui verranno specificati i **certificati dello sviluppatore**, i **dispositivi**e un **nome** per il profilo di **provisioning**:

[![](healthkit-images/image04.png "Generating the Provisioning Profile")](healthkit-images/image04.png#lightbox)

Fare clic su **genera** e attendere la creazione del profilo. Scaricare il file e fare doppio clic su di esso per installarlo in Xcode. È possibile confermare l'installazione in **Xcode > preferenze > account > visualizzare i dettagli...** Il profilo di provisioning appena installato verrà visualizzato e dovrebbe avere l'icona per il kit di integrità e qualsiasi altro servizio speciale nella riga **diritti** :

[![](healthkit-images/image05.png "Viewing the profile in Xcode")](healthkit-images/image05.png#lightbox)

<a name="associating-appid"></a>

### <a name="associating-the-app-id-and-provisioning-profile-with-your-xamarinios-app"></a>Associazione dell'ID app e del profilo di provisioning all'app Novell. iOS

Dopo aver creato e installato un profilo di **provisioning** appropriato come descritto, sarebbe in genere opportuno creare una soluzione in Visual Studio per Mac o Visual Studio. L'accesso al kit di integrità è disponibile per qualsiasi progetto C# o F # di iOS.

Invece di esaminare il processo di creazione manuale di un progetto Novell iOS 8, aprire l'app di esempio collegata a questo articolo (che include uno storyboard e un codice predefiniti). Per associare l'app di esempio al **profilo di provisioning**abilitato per il kit di integrità, nel **riquadro della soluzione**, fare clic con il pulsante destro del mouse sul progetto e visualizzare la finestra di dialogo **Opzioni** . Passare al pannello **dell'applicazione iOS** e immettere l' **ID app** esplicito creato in precedenza come identificatore del **bundle**dell'app:

[![](healthkit-images/image06.png "Enter the explicit App ID")](healthkit-images/image06.png#lightbox)

Passare ora al pannello di **firma del bundle iOS** . Il **profilo di provisioning**installato di recente, con la relativa associazione all' **ID app**esplicito, sarà ora disponibile come **profilo di provisioning**:

[![](healthkit-images/image07.png "Select the Provisioning Profile")](healthkit-images/image07.png#lightbox)

Se il **profilo di provisioning** non è disponibile, controllare l' **identificatore del bundle** nel pannello **applicazioni iOS** rispetto a quello specificato in **iOS Dev Center** e che il profilo di **provisioning** sia installato (**Xcode > preferenze > account > visualizzare i dettagli...**).

Quando è selezionato il profilo di **provisioning** abilitato per il kit di integrità, fare clic su **OK** per chiudere la finestra di dialogo Opzioni progetto.

### <a name="entitlementsplist-and-infoplist-values"></a>Valori dei diritti. plist e INFO. plist

L'app di esempio include un `Entitlements.plist` file (necessario per le app abilitate per il kit di integrità) e non incluso in ogni modello di progetto. Se il progetto non include i diritti, fare clic con il pulsante destro del mouse sul progetto, scegliere **file > nuovo file... > iOS > titles. plist** per aggiungerne uno manualmente.

Infine, il `Entitlements.plist` deve avere la coppia chiave/valore seguente:

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

Analogamente, l'oggetto `Info.plist` per l'app deve avere un valore `healthkit` associato alla `UIRequiredDeviceCapabilities` chiave:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
<string>armv7</string>
    <string>healthkit</string>
</array>

```

L'applicazione di esempio fornita con questo articolo include una preconfigurata `Entitlements.plist` che include tutte le chiavi necessarie.

<a name="programming"></a>

## <a name="programming-health-kit"></a>Kit di integrità di programmazione

L'archivio dati del kit di integrità è un archivio dati privato specifico dell'utente condiviso tra le app. Poiché le informazioni sull'integrità sono così sensibili, l'utente deve eseguire passaggi positivi per consentire l'accesso ai dati. Questo tipo di accesso può essere parziale (scrittura, ma non letto, accesso per alcuni tipi di dati ma non altri e così via) e può essere revocato in qualsiasi momento. Le applicazioni del kit di integrità devono essere scritte in modo difensivo, con la consapevolezza che molti utenti saranno riluttanti a archiviare le informazioni relative all'integrità.

I dati del kit di integrità sono limitati ai tipi specificati da Apple. Questi tipi sono rigorosamente definiti: alcuni, ad esempio il tipo di sangue, sono limitati ai valori specifici di un'enumerazione fornita da Apple, mentre altri combinano una grandezza con un'unità di misura (ad esempio grammi, calorie e litri). Anche i dati che condividono un'unità di misura compatibile sono distinti dal rispettivo `HKObjectType` . ad esempio, il sistema di tipi rileverà un tentativo errato di archiviare un `HKQuantityTypeIdentifier.NumberOfTimesFallen` valore in un campo che prevede un `HKQuantityTypeIdentifier.FlightsClimbed` anche se entrambe usano l' `HKUnit.Count` unità di misura.

I tipi archiviabili nell'archivio dati del kit di integrità sono tutte sottoclassi di `HKObjectType` . `HKCharacteristicType`gli oggetti archiviano il sesso biologico, il tipo di sangue e la data di nascita. Tuttavia, sono `HKSampleType` oggetti, che rappresentano i dati campionati in un momento specifico o in un periodo di tempo. 

[![](healthkit-images/image08.png "HKSampleType objects chart")](healthkit-images/image08.png#lightbox)

`HKSampleType`è astratto e ha quattro sottoclassi concrete. Attualmente è disponibile un solo tipo di `HKCategoryType` dati, ovvero l'analisi della sospensione. La maggior parte dei dati nel kit di integrità è di tipo `HKQuantityType` e archivia i dati negli `HKQuantitySample` oggetti, che vengono creati usando il modello di progettazione della Factory noto:

[![](healthkit-images/image09.png "The large majority of data in Health Kit are of type HKQuantityType and store their data in HKQuantitySample objects")](healthkit-images/image09.png#lightbox)

`HKQuantityType`i tipi variano da `HKQuantityTypeIdentifier.ActiveEnergyBurned` a `HKQuantityTypeIdentifier.StepCount` . 

<a name="requesting-permission"></a>

### <a name="requesting-permission-from-the-user"></a>Richiesta dell'autorizzazione all'utente

Gli utenti finali devono eseguire passaggi positivi per consentire a un'app di leggere o scrivere i dati del kit di integrità. Questa operazione viene eseguita tramite l'app per l'integrità che è preinstallata nei dispositivi iOS 8. La prima volta che si esegue un'app del kit di integrità, viene visualizzata una finestra di dialogo di **accesso all'integrità** controllata dal sistema:

[![](healthkit-images/image10.png "The user is presented with a system-controlled Health Access dialog")](healthkit-images/image10.png#lightbox)

Successivamente, l'utente può modificare le autorizzazioni usando la finestra di dialogo **Sources** dell'app Health:

[![](healthkit-images/image11.png "The user can change permissions using Health apps Sources dialog")](healthkit-images/image11.png#lightbox)

Poiché le informazioni sull'integrità sono estremamente riservate, gli sviluppatori di app devono scrivere i propri programmi in modo difensivo, con la previsione che le autorizzazioni vengano rifiutate e modificate mentre l'app è in esecuzione. L'idioma più comune è quello di richiedere le autorizzazioni nel `UIApplicationDelegate.OnActivated` metodo e quindi di modificare l'interfaccia utente in base alle esigenze.

### <a name="permissions-walkthrough"></a>Procedura dettagliata per le autorizzazioni

Nel progetto con provisioning del kit di integrità aprire il `AppDelegate.cs` file. Si noti l'istruzione che usa `HealthKit` ; nella parte superiore del file.

Il codice seguente si riferisce alle autorizzazioni del kit di integrità:

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

Tutto il codice in questi metodi può essere eseguito inline in `OnActivated` , ma l'app di esempio usa metodi distinti per renderlo più chiaro: `ValidateAuthorization()` prevede i passaggi necessari per richiedere l'accesso ai tipi specifici scritti (e leggere, se l'app desiderata) ed `ReactToHealthCarePermissions()` è un callback che viene attivato dopo che l'utente ha interagito con la finestra di dialogo delle autorizzazioni in Health. app.

Il compito di `ValidateAuthorization()` è compilare il set di `HKObjectTypes` che verrà scritto dall'app e richiedere l'autorizzazione per l'aggiornamento dei dati. Nell'app di esempio, `HKObjectType` è per la chiave `KHQuantityTypeIdentifierKey.HeartRate` . Questo tipo viene aggiunto al set `typesToWrite` , mentre il set `typesToRead` viene lasciato vuoto. Questi set e un riferimento al `ReactToHealthCarePermissions()` callback vengono passati a `HKHealthStore.RequestAuthorizationToShare()` .

Il `ReactToHealthCarePermissions()` callback verrà chiamato dopo che l'utente ha interagito con la finestra di dialogo delle autorizzazioni e ha passato due informazioni: un `bool` valore che sarà `true` se l'utente ha interagito con la finestra di dialogo delle autorizzazioni e un `NSError` che, se non null, indica un tipo di errore associato alla presentazione della finestra di dialogo delle autorizzazioni.

> [!IMPORTANT]
> Per chiarire gli argomenti relativi a questa funzione: i parametri _Success_ e _Error_ non indicano se l'utente ha concesso l'autorizzazione per accedere ai dati del kit di integrità. Indicano solo che all'utente è stata data la possibilità di consentire l'accesso ai dati.

Per verificare se l'app può accedere ai dati, `HKHealthStore.GetAuthorizationStatus()` viene usato, passando `HKQuantityTypeIdentifierKey.HeartRate` . In base allo stato restituito, l'app Abilita o Disabilita la possibilità di immettere i dati. Non esiste un'esperienza utente standard per la gestione di un attacco Denial of Access e sono disponibili molte opzioni possibili. Nell'app di esempio, lo stato viene impostato su un `HeartRateModel` oggetto singleton che, a sua volta, genera gli eventi rilevanti.

## <a name="model-view-and-controller"></a>Modello, visualizzazione e controller

Per esaminare l' `HeartRateModel` oggetto singleton, aprire il `HeartRateModel.cs` file:

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

La prima sezione è il codice standard per la creazione di eventi e gestori generici. Anche la parte iniziale della `HeartRateModel` classe è standard per la creazione di un oggetto Singleton thread-safe.

Quindi `HeartRateModel` espone 3 eventi: 

- `EnabledChanged`-Indica che l'archiviazione della frequenza cardiaca è stata abilitata o disabilitata (si noti che l'archiviazione è inizialmente disabilitata). 
- `ErrorMessageChanged`-Per questa applicazione di esempio, è presente un modello di gestione degli errori molto semplice: una stringa con l'ultimo errore. 
- `HeartRateStored`-Generato quando una frequenza cardiaca viene archiviata nel database del kit di integrità.

Si noti che, ogni volta che questi eventi vengono generati, viene eseguita tramite `NSObject.InvokeOnMainThread()` , che consente ai sottoscrittori di aggiornare l'interfaccia utente. In alternativa, gli eventi possono essere documentati come generati su thread in background e la responsabilità di garantire la compatibilità potrebbe essere lasciata ai gestori. Le considerazioni sui thread sono importanti nelle applicazioni del kit di integrità perché molte delle funzioni, ad esempio la richiesta di autorizzazione, sono asincrone ed eseguono i callback nei thread non principali.

Il codice specifico di Heath kit in `HeartRateModel` si trova nelle due funzioni `HeartRateInBeatsPerMinute()` e `StoreHeartRate()` . 

`HeartRateInBeatsPerMinute()`Converte l'argomento in un kit di integrità fortemente tipizzato `HKQuantity` . Il tipo della quantità è quello specificato da `HKQuantityTypeIdentifierKey.HeartRate` e le unità della quantità sono `HKUnit.Count` divise per `HKUnit.Minute` (in altre parole, l'unità è di tipo *ritmi al minuto*). 

La `StoreHeartRate()` funzione accetta un oggetto `HKQuantity` (nell'app di esempio, uno creato da `HeartRateInBeatsPerMinute()` ). Per convalidare i dati, viene usato il `HKQuantity.IsCompatible()` metodo, che restituisce `true` se le unità dell'oggetto possono essere convertite in unità nell'argomento. Se la quantità è stata creata con `HeartRateInBeatsPerMinute()` questa funzione, restituirà ovviamente `true` , ma restituirà anche `true` se la quantità è stata creata come, ad esempio, i *battimenti all'ora*. Più comunemente, `HKQuantity.IsCompatible()` può essere usato per convalidare la massa, la distanza e l'energia che l'utente o un dispositivo può inserire o visualizzare in un sistema di misurazione, ad esempio unità imperiali, ma che potrebbe essere archiviato in un altro sistema (ad esempio unità metriche). 

Una volta convalidata la compatibilità della quantità, il `HKQuantitySample.FromType()` metodo factory viene usato per creare un oggetto fortemente tipizzato `heartRateSample` . `HKSample`gli oggetti hanno una data di inizio e di fine. per le letture istantanee, questi valori devono essere uguali, come nell'esempio. Nell'esempio non vengono inoltre impostati dati chiave-valore nel relativo `HKMetadata` argomento, ma è possibile utilizzare codice come il codice seguente per specificare la posizione del sensore:

```csharp
var hkm = new HKMetadata();
hkm.HeartRateSensorLocation = HKHeartRateSensorLocation.Chest;

```

Una volta `heartRateSample` creato, il codice crea una nuova connessione al database con il blocco using. All'interno del blocco, il `HKHealthStore.SaveObject()` metodo tenta la scrittura asincrona nel database. La chiamata risultante all'espressione lambda attiva eventi rilevanti, `HeartRateStored` ovvero o `ErrorMessageChanged` .

Ora che il modello è stato programmato, è possibile vedere in che modo il controller riflette lo stato del modello. Aprire il `HKWorkViewController.cs` file. Il costruttore collega semplicemente il `HeartRateModel` singleton ai metodi di gestione degli eventi (anche in questo caso, è possibile eseguire questa operazione inline con espressioni lambda, ma i metodi separati rendono più ovvia la finalità):

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

Ovviamente, in un'applicazione con un singolo controller, è possibile evitare la creazione di un oggetto modello separato e l'uso di eventi per il flusso di controllo, ma l'uso di oggetti modello è più appropriato per le app reali.

## <a name="running-the-sample-app"></a>Esecuzione dell'app di esempio

Il simulatore iOS non supporta il kit di integrità. Il debug deve essere eseguito in un dispositivo fisico che esegue iOS 8.

Alleghi al sistema un dispositivo di sviluppo iOS 8 con provisioning corretto. Selezionarlo come destinazione di distribuzione in Visual Studio per Mac e scegliere **esegui > debug**dal menu.

> [!IMPORTANT]
> Gli errori relativi al provisioning sono esposti a questo punto. Per risolvere gli errori, vedere la sezione Creazione e provisioning di un'app del kit di integrità riportata sopra. I componenti sono: 
>
> - **iOS Dev Center** : ID app esplicito & Kit di integrità abilitato per il profilo di provisioning. 
> - **Opzioni progetto** -identificatore Bundle (ID app esplicito) & profilo di provisioning.
> - **Codice sorgente** -diritti. plist & info. plist

Supponendo che le disposizioni siano state impostate correttamente, l'applicazione verrà avviata. Quando raggiunge il `OnActivated` metodo, richiede l'autorizzazione del kit di integrità. La prima volta che viene rilevata dal sistema operativo, l'utente verrà visualizzato con la finestra di dialogo seguente:

[![](healthkit-images/image12.png "The user will be presented with this dialog")](healthkit-images/image12.png#lightbox)

Abilitare l'app per aggiornare i dati della frequenza cardiaca e l'app verrà visualizzata nuovamente. Il `ReactToHealthCarePermissions` callback verrà attivato in modo asincrono. Questa operazione causerà la `HeartRateModel’s` `Enabled` modifica della proprietà, che genererà l' `EnabledChanged` evento, che causerà l' `HKPermissionsViewController.OnEnabledChanged()` esecuzione del gestore dell'evento, che consente di abilitare il `StoreData` pulsante. Il diagramma seguente illustra la sequenza:

[![](healthkit-images/image13.png "This diagram shows the sequence of events")](healthkit-images/image13.png#lightbox)

Premere il pulsante **registra** . In questo modo verrà `StoreData_TouchUpInside()` eseguito il gestore, che tenterà di analizzare il valore del campo di `heartRate` testo, convertire in un oggetto `HKQuantity` tramite la funzione discussa in precedenza `HeartRateModel.HeartRateInBeatsPerMinute()` e passare tale quantità a `HeartRateModel.StoreHeartRate()` . Come descritto in precedenza, in questo modo si tenterà di archiviare i dati e verrà generato un `HeartRateStored` `ErrorMessageChanged` evento o.

Fare doppio clic sul pulsante **Home** nel dispositivo e aprire app per l'integrità. Fare clic sulla scheda **Sources (origini** ). l'app di esempio sarà elencata. Selezionarlo e non consentire l'autorizzazione per aggiornare i dati della frequenza cardiaca. Fare doppio clic sul pulsante **Home** e tornare all'app. Anche in questo caso, `ReactToHealthCarePermissions()` verrà chiamato, ma questa volta, poiché l'accesso viene negato, il pulsante **StoreData** diventerà disabilitato (si noti che ciò si verifica in modo asincrono e la modifica dell'interfaccia utente potrebbe essere visibile all'utente finale).

## <a name="advanced-topics"></a>Argomenti avanzati

La lettura dei dati dal database del kit di integrità è molto simile alla scrittura dei dati: uno specifica i tipi di dati a cui si sta tentando di accedere, richiede l'autorizzazione e, se tale autorizzazione viene concessa, i dati sono disponibili, con conversione automatica in unità di misura compatibili.

Sono disponibili numerose funzioni di query più sofisticate che consentono query basate su predicati e query che eseguono aggiornamenti quando vengono aggiornati i dati rilevanti. 

Gli sviluppatori di applicazioni del kit di integrità dovrebbero esaminare la sezione Health Kit delle [linee guida](https://developer.apple.com/app-store/review/guidelines/#healthkit)per la revisione delle app di Apple.

Una volta compresi i modelli di sicurezza e di sistema dei tipi, l'archiviazione e la lettura dei dati nel database del kit di integrità condiviso sono piuttosto semplici. Molte delle funzioni del kit di integrità funzionano in modo asincrono e gli sviluppatori di applicazioni devono scrivere i propri programmi in modo appropriato.

Al momento della stesura di questo articolo, non è attualmente disponibile un kit di integrità equivalente a Android o Windows Phone.

## <a name="summary"></a>Summary

In questo articolo è stato illustrato il modo in cui Health Kit consente alle applicazioni di archiviare, recuperare e condividere le informazioni relative all'integrità, fornendo anche un'app per l'integrità standard che consente all'utente di accedere e controllare questi dati. 

È stato anche illustrato come l'integrità della privacy, della sicurezza e dei dati stia ignorando i problemi per le informazioni relative all'integrità e le app che usano il kit di integrità devono gestire l'aumento della complessità negli aspetti di gestione delle applicazioni (provisioning), codifica (sistema di tipi del kit di integrità) ed esperienza utente (controllo utente delle autorizzazioni tramite finestre di dialogo di sistema e app per 

Infine, è stata esaminata una semplice implementazione del kit di integrità usando l'app di esempio inclusa che scrive i dati di heartbeat nell'archivio del kit di integrità e ha una progettazione compatibile con la modalità asincrona.

## <a name="related-links"></a>Collegamenti correlati

- [HKWork (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-introtohealthkit)
- [Introduzione a iOS 8](~/ios/platform/introduction-to-ios8.md)
