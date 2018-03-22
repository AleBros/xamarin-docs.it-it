---
title: HomeKit
description: "HomeKit è il framework di Apple per controllare i dispositivi di automazione home. In questo articolo presenta HomeKit e copre accessori la configurazione di test nel simulatore HomeKit accessori e la scrittura di una semplice app xamarin di interagire con questi accessori."
ms.topic: article
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 02116e8e11cb6ff050e2c885338777e1fd25c4cb
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2018
---
# <a name="homekit"></a>HomeKit

_HomeKit è il framework di Apple per controllare i dispositivi di automazione home. In questo articolo presenta HomeKit e copre accessori la configurazione di test nel simulatore HomeKit accessori e la scrittura di una semplice app xamarin di interagire con questi accessori._

[![](homekit-images/accessory01.png "App abilitata per un esempio HomeKit")](homekit-images/accessory01.png#lightbox)

Apple ha introdotto HomeKit in iOS 8 come un modo per l'integrazione di più dispositivi di automazione iniziale da fornitori di diversi in un'unità singola e coerente. La promozione di un protocollo comune per l'individuazione, configurazione e controllo dei dispositivi di automazione home, HomeKit consente ai dispositivi da fornitori non correlati all'interazione senza la necessità di coordinare le attività singoli fornitori.

Con HomeKit, è possibile creare un'app xamarin che controlla tutti i dispositivi HomeKit abilitato senza utilizzando fornitore specificati API o App. Utilizza HomeKit, è possibile eseguire le operazioni seguenti:

- Rilevare i nuovi dispositivi di automazione home HomeKit abilitato e li aggiunge a un database che verrà mantenuti in tutti i dispositivi di iOS dell'utente.
- Programma di installazione, configurare, visualizzare e controllare tutti i dispositivi nel HomeKit _Home i Database di configurazione_.
- Comunicare con qualsiasi dispositivo HomeKit preconfigurata e comando per eseguire singole azioni o di lavoro insieme, ad esempio l'attivazione su tutte le luci cucina.

Oltre a servire i dispositivi nel Database di configurazione Home per App HomeKit abilitata, HomeKit fornisce l'accesso ai comandi vocali Siri. Dato un programma di installazione HomeKit configurato in modo appropriato, l'utente può inviare comandi vocali, ad esempio "Siri, attivare le luci nella chat room living."

<a name="Home-Configuration-Database" />

## <a name="the-home-configuration-database"></a>Il Database di configurazione iniziale

HomeKit organizza tutti i dispositivi di automazione in una posizione specificata in una raccolta Home. Questa raccolta fornisce un modo per l'utente raggruppare i dispositivi di automazione home in unità logicamente disposta con etichette significative e leggibile.

La raccolta Home viene archiviata in una pagina iniziale Database di configurazione che verrà automaticamente eseguito il backup e sincronizzato in tutti i dispositivi di iOS dell'utente. HomeKit fornisce le classi seguenti per l'utilizzo con il Database di configurazione Home:

- `HMHome` -Questo è il contenitore di livello superiore che contiene tutte le informazioni e le configurazioni per tutti i dispositivi di automazione iniziale in un'unica ubicazione fisica (ad es. una singola famiglia residenza). L'utente potrebbe avere più di residenza, ad esempio a casa principale e una casa ferie. O potrebbe essere diverso "ospita" sulla stessa proprietà, ad esempio la casa principale e una casa guest su garage. In entrambi i casi, almeno uno `HMHome` oggetto _deve_ da programma di installazione e archiviare prima di qualsiasi altra informazione HomeKit può essere immesso.
- `HMRoom` -Facoltativo, mentre un `HMRoom` consente all'utente di definire locali specifici all'interno di una posizione iniziale (`HMHome`), ad esempio: cucina, bagni, Garage o uso domestico. L'utente è possibile raggruppare tutti i dispositivi di automazione iniziale in una posizione specifica le parti in un `HMRoom` e agire come unità. Ad esempio, in cui viene chiesto Siri per disattivare le luci garage.
- `HMAccessory` -Ciò rappresenta una singola, HomeKit fisico abilitato dispositivo di automazione che è stata installata in residenza dell'utente (ad esempio un termostato intelligente). Ogni `HMAccessory` viene assegnato a un `HMRoom`. Se l'utente non ha configurato le stanze, accessori HomeKit assegna a una chat room predefinita speciali.
- `HMService` -Rappresenta un servizio fornito da un determinato `HMAccessory`, ad esempio lo stato di attivazione/disattivazione di una luce o colore (se la modifica di colore è supportata). Ogni `HMAccessory` può avere più di un servizio, ad esempio di strumento di apertura di porte garage che include anche una luce. Inoltre, un determinato `HMAccessory` potrebbe essere servizi, ad esempio di aggiornamento del firmware, che sono di fuori controllo utente.
- `HMZone` -Consente di raggruppare un insieme di `HMRoom` gli oggetti in aree logiche, ad esempio installare al piano o seminterrato. Sebbene sia facoltativo, in questo modo per le interazioni come porre Siri per attivare tutti della luce disattivato al piano.

<a name="Provisioning-a-HomeKit-App" />

## <a name="provisioning-a-homekit-app"></a>Provisioning di un'App HomeKit

A causa dei requisiti di sicurezza imposti da HomeKit, un'app xamarin che utilizza il framework HomeKit deve essere configurata correttamente in entrambi il portale per sviluppatori di Apple e nel file di progetto xamarin. IOS.

Seguire questa procedura:

1. Accedere al [portale per sviluppatori Apple](http://developer.apple.com).
2. Fare clic su **certificati, profili & identificatori**.
3. Se non già stato fatto, fare clic su **identificatori** e creare un ID per l'applicazione (ad esempio `com.company.appname`), in caso contrario, modificare l'ID esistente.
4. Verificare che il **HomeKit** servizio è stato verificato per l'ID specificato: 

    [![](homekit-images/provision01.png "Abilitare il servizio HomeKit per l'ID specificato")](homekit-images/provision01.png#lightbox)
5. Salvare le modifiche.
4. Fare clic su **i profili di Provisioning** > **sviluppo** e creare un nuovo profilo di provisioning per l'app di sviluppo: 

    [![](homekit-images/provision02.png "Creare un nuovo profilo di provisioning per l'app di sviluppo")](homekit-images/provision02.png#lightbox)
5. Scaricare e installare il nuovo profilo di provisioning o utilizzare Xcode per scaricare e installare il profilo.
6. Modificare le opzioni di progetto xamarin e assicurarsi che si sta utilizzando il profilo di provisioning appena creato: 

    [![](homekit-images/provision03.png "Selezionare profilo di provisioning appena creato")](homekit-images/provision03.png#lightbox)
7. Successivamente, modificare il **Info. plist** e assicurarsi che si sta utilizzando l'ID dell'App che è stato utilizzato per creare il profilo di provisioning: 

    [![](homekit-images/provision04.png "Impostare l'ID App ")](homekit-images/provision04.png#lightbox)
8. Infine, modificare il **Entitlements.plist** e assicurarsi che il **HomeKit** diritto è stato selezionato: 

    [![](homekit-images/provision05.png "Abilitare i diritti di HomeKit")](homekit-images/provision05.png#lightbox)
9. Salvare le modifiche a tutti i file.

Con queste impostazioni, l'applicazione è ora possibile accedere alle API HomeKit Framework. Per informazioni dettagliate sul provisioning, vedere il nostro [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md) e [Provisioning dell'App](~/ios/get-started/installation/device-provisioning/index.md) Guide.

> [!IMPORTANT]
> Test di un'app abilitata HomeKit richiede un dispositivo iOS reali che sia stato preparato correttamente per lo sviluppo. Non è possibile testare HomeKit dal simulatore iOS.

## <a name="the-homekit-accessory-simulator"></a>Il simulatore di accessori HomeKit

Per fornire un modo per testare tutti i dispositivi possibili automazione home e i servizi, senza che sia necessario un dispositivo fisico, Apple creato il _HomeKit accessori simulatore_. Con il simulatore è possibile installare e configurare dispositivi HomeKit virtuali.

### <a name="installing-the-simulator"></a>L'installazione del simulatore

Apple fornisce il simulatore di accessori HomeKit come download separato da Xcode, pertanto sarà necessario installarlo prima di continuare.

Seguire questa procedura:

1. In un web browser, visitare [download per gli sviluppatori di Apple](https://developer.apple.com/download/more/?name=for%20Xcode)
2. Scaricare il **strumenti aggiuntivi per Xcode xxx** (dove xxx è la versione di Xcode installato): 

    [![](homekit-images/simulator01.png "Scaricare gli strumenti aggiuntivi per Xcode")](homekit-images/simulator01.png#lightbox)
3. Aprire l'immagine del disco e installare gli strumenti del **applicazioni** directory.

Con il simulatore di accessori HomeKit installato, è possono creare accessori virtuali per il test.

### <a name="creating-virtual-accessories"></a>Creazione di accessori virtuali

Per avviare il simulatore di accessori HomeKit e creare alcuni accessori virtuali, eseguire le operazioni seguenti:

1. Dalla cartella applicazioni, avviare il simulatore di accessori HomeKit: 

    [![](homekit-images/simulator02.png "Il simulatore di accessori HomeKit")](homekit-images/simulator02.png#lightbox)
2. Fare clic su di  **+**  e selezionare **nuovo accessorio...** : 

    [![](homekit-images/simulator03.png "Aggiungere un nuovo accessorio")](homekit-images/simulator03.png#lightbox)
3. Inserire le informazioni sulla nuova funzione di accesso e fare clic su di **fine** pulsante: 

    [![](homekit-images/simulator04.png "Inserire le informazioni sulla nuova funzione di accesso")](homekit-images/simulator04.png#lightbox)
4. Fare clic su di **aggiungere il servizio...** pulsante e selezionare un tipo di servizio nell'elenco a discesa: 

    [![](homekit-images/simulator05.png "Selezionare un tipo di servizio nell'elenco a discesa")](homekit-images/simulator05.png#lightbox)
5. Fornire un **nome** per il servizio e fare clic su di **fine** pulsante: 

    [![](homekit-images/simulator06.png "Immettere un nome per il servizio")](homekit-images/simulator06.png#lightbox)
6. È possibile fornire le caratteristiche facoltative per un servizio, fare clic su di **aggiungere caratteristica** pulsante e la configurazione delle impostazioni necessarie: 

    [![](homekit-images/simulator07.png "Configurare le impostazioni necessarie")](homekit-images/simulator07.png#lightbox)
7. Ripetere i passaggi precedenti per creare una per ogni tipo di dispositivo virtuale automazione home HomeKit supporta.

Con alcuni esempio virtuale HomeKit accessori creato e configurato, è possibile utilizzare e controllare questi dispositivi dall'app xamarin. IOS.

## <a name="configuring-the-infoplist-file"></a>Configurazione del File Info. plist

Novità per iOS 10 (e successive), lo sviluppatore dovrà aggiungere la `NSHomeKitUsageDescription` chiave per l'app `Info.plist` file e fornire una stringa di dichiarazione perché l'applicazione desidera accedere a database HomeKit dell'utente. Questa stringa viene presentata il tempo utente la prima che esecuzione dell'app:

[![](homekit-images/info01.png "La finestra di dialogo autorizzazioni HomeKit")](homekit-images/info01.png#lightbox)

Per impostare questa chiave, eseguire le operazioni seguenti:

1. Fare doppio clic su di `Info.plist` file nel **Esplora** per aprirlo e modificarlo.
2. Nella parte inferiore della schermata, passare il **origine** visualizzazione.
3. Aggiungere un nuovo **voce** all'elenco.
4. Nell'elenco a discesa, selezionare **Privacy - descrizione dell'utilizzo di HomeKit**: 

    [![](homekit-images/info02.png "Selezionare Privacy - descrizione dell'utilizzo di HomeKit")](homekit-images/info02.png#lightbox)
5. Immettere una descrizione per il motivo per cui l'app desidera accedere a database HomeKit dell'utente: 

    [![](homekit-images/info03.png "Immettere una descrizione")](homekit-images/info03.png#lightbox)
6. Salvare le modifiche apportate al file.

> [!IMPORTANT]
> Impossibile impostare il `NSHomeKitUsageDescription` chiave nel `Info.plist` file comporterà l'app _invisibile all'utente hanno generato errori_ (viene chiuso dal sistema in fase di esecuzione) senza errori quando si esegue in iOS 10 (o versione successiva).

## <a name="connecting-to-homekit"></a>Connessione a HomeKit

Per comunicare con HomeKit, è necessario prima creare un'istanza di app xamarin di `HMHomeManager` classe. Il gestore Home è il punto di ingresso centrale in HomeKit ed è responsabile di fornire un elenco di case disponibili, l'aggiornamento e la gestione di tale elenco e la restituzione dell'utente _primario Home_.

Il `HMHome` oggetto contiene tutte le informazioni relative a una casa offrono compresa la chat, gruppi o aree che possono contenere, insieme a accessori automazione principale che sono stati installati. Prima di possono eseguire qualsiasi operazione in HomeKit, almeno uno `HMHome` deve essere creato e assegnato come la home page primario.

L'app è responsabile della verifica dell'esistenza di una casa primario e la creazione e l'assegnazione di un in caso contrario.

### <a name="adding-a-home-manager"></a>Aggiunta di una gestione Home

Per aggiungere HomeKit riconoscimento a un'app xamarin. IOS, modificare il **appdelegate. cs** file per modificarlo e renderlo simile al seguente:

```csharp
using HomeKit;
...

public HMHomeManager HomeManager { get; set; }
...

public override void FinishedLaunching (UIApplication application)
{
    // Attach to the Home Manager
    HomeManager = new HMHomeManager ();
    Console.WriteLine ("{0} Home(s) defined in the Home Manager", HomeManager.Homes.Count());

    // Wire-up Home Manager Events
    HomeManager.DidAddHome += (sender, e) => {
        Console.WriteLine("Manager Added Home: {0}",e.Home);
    };

    HomeManager.DidRemoveHome += (sender, e) => {
        Console.WriteLine("Manager Removed Home: {0}",e.Home);
    };
    HomeManager.DidUpdateHomes += (sender, e) => {
        Console.WriteLine("Manager Updated Homes");
    };
    HomeManager.DidUpdatePrimaryHome += (sender, e) => {
        Console.WriteLine("Manager Updated Primary Home");
    };
}
```

Alla prima esecuzione dell'applicazione, viene chiesto all'utente se desidera consentono di accedere alle informazioni HomeKit:

[![](homekit-images/home01.png "L'utente verrà richiesto se si desidera consentire di accedere alle informazioni HomeKit")](homekit-images/home01.png#lightbox)

Se l'utente risponde **OK**, quindi l'applicazione sarà in grado di lavorare con i relativi accessori HomeKit è e tutte le chiamate a HomeKit avrà esito negativo con un errore.

Con il gestore Home sul posto, quindi l'applicazione sarà necessario vedere se è stata configurata una casa primario e in caso contrario, fornire un modo per l'utente creare e assegnare a uno.

### <a name="accessing-the-primary-home"></a>L'accesso alla home page primario

Come descritto in precedenza, è necessario creare e configurare HomeKit è disponibile ed è responsabilità dell'applicazione per fornire un modo per l'utente creare e assegnare una Home primario se non esiste già una casa primario.

Quando l'app si avvia o restituisce dallo sfondo, è necessario monitorare il `DidUpdateHomes` evento del `HMHomeManager` classe per controllare l'esistenza di una casa primario. Se non esiste, deve fornire un'interfaccia per l'utente per crearne uno.

Aggiungere il codice seguente a un controller di visualizzazione per cercare la home page primario:

```csharp
using HomeKit;
...

public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
...

// Wireup events
ThisApp.HomeManager.DidUpdateHomes += (sender, e) => {

    // Was a primary home found?
    if (ThisApp.HomeManager.PrimaryHome == null) {
        // Ask user to add a home
        PerformSegue("AddHomeSegue",this);
    }
};
```

Quando il gestore Home stabilisce una connessione al HomeKit, il `DidUpdateHomes` evento verrà generato, qualsiasi case esistenti verranno caricate nell'insieme del gestore di case e la home page primario verrà caricata, se disponibile.

### <a name="adding-a-primary-home"></a>Aggiunta di una casa primario

Se il `PrimaryHome` proprietà del `HMHomeManager` è `null` dopo un `DidUpdateHomes` evento, è necessario fornire un modo per l'utente creare e assegnare una Home primario prima di continuare.

In genere l'app presenterà un form per l'utente per assegnare un nome di una nuova pagina iniziale che quindi viene passata al gestore principale per l'installazione come la home page primario. Per il **HomeKitIntro** creato nella finestra di progettazione IOS e chiamato da app di esempio, una vista modale la `AddHomeSegue` definire dall'interfaccia principale dell'app.

Fornisce un campo di testo per l'utente di immettere un nome per la nuova pagina iniziale e un pulsante per aggiungere la home page. Quando l'utente tocca il **Aggiungi Home** pulsante, il codice seguente chiama il responsabile principale per aggiungere la home page:

```csharp
// Add new home to HomeKit
ThisApp.HomeManager.AddHome(HomeName.Text,(home,error) =>{
    // Did an error occur
    if (error!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Add Home Error",string.Format("Error adding {0}: {1}",HomeName.Text,error.LocalizedDescription),this);
        return;
    }

    // Make the primary house
    ThisApp.HomeManager.UpdatePrimaryHome(home,(err) => {
        // Error?
        if (err!=null) {
            // Inform user of error
            AlertView.PresentOKAlert("Add Home Error",string.Format("Unable to make this the primary home: {0}",err.LocalizedDescription),this);
            return ;
        }
    });

    // Close the window when the home is created
    DismissViewController(true,null);
});
```

Il `AddHome` metodo tenterà di creare una nuova pagina iniziale e ripristinare la routine di callback specificato. Se il `error` proprietà non è `null`, si è verificato un errore e deve essere presentata all'utente. Gli errori più comuni sono causati da un nome principale non è univoco o la gestione principale non è in grado di comunicare con HomeKit.

Se la home page è stata creata correttamente, è necessario chiamare il `UpdatePrimaryHome` per impostare la nuova pagina iniziale come la home page primario. Anche se il `error` proprietà non è `null`, si è verificato un errore e deve essere presentata all'utente.

È inoltre consigliabile monitorare Home del gestore di `DidAddHome` e `DidRemoveHome` eventi e l'aggiornamento dell'interfaccia utente dell'applicazione in base alle esigenze.

> [!IMPORTANT]
> Il `AlertView.PresentOKAlert` metodo usato nell'esempio di codice precedente è una classe helper nell'applicazione HomeKitIntro che rende l'utilizzo di iOS avvisi più semplice.


## <a name="finding-new-accessories"></a>Ricerca di nuovi accessori

Una volta una casa primario è stata definita o caricata da Gestione Home, è possibile chiamare l'app xamarin di `HMAccessoryBrowser` per trovare eventuali nuovi accessori di automazione home e li aggiunge a una posizione iniziale.

Chiamare il `StartSearchingForNewAccessories` metodo per iniziare a cercare nuovi accessori e `StopSearchingForNewAccessories` metodo al termine.

> [!IMPORTANT]
> `StartSearchingForNewAccessories` non lasciare in esecuzione per lunghi periodi di tempo perché l'operazione influirebbe negativamente la durata della batteria sia sulle prestazioni del dispositivo iOS. Apple suggerisce chiamata `StopSearchingForNewAccessories` dopo un minuto o la ricerca solo quando l'interfaccia utente di trovare accessori viene presentato all'utente.

Il `DidFindNewAccessory` evento verrà chiamato quando vengono individuati nuovi accessori e verranno aggiunti per il `DiscoveredAccessories` elenco nel Browser accessori.

Il `DiscoveredAccessories` elenco conterrà un insieme di `HMAccessory` gli oggetti che definiscono un offrono HomeKit abilitati dispositivo automazione home e i servizi disponibili, ad esempio luci o controllo sportello garage.

Dopo aver individuato la nuova funzione di accesso, deve essere presentata all'utente e pertanto possono selezionarlo e aggiungerlo a una posizione iniziale. Esempio:

[![](homekit-images/accessory01.png "Ricerca di un nuovo accessorio")](homekit-images/accessory01.png#lightbox)

Chiamare il `AddAccessory` metodo per aggiungere la funzione di accesso selezionato insieme della home page. Ad esempio:

```csharp
// Add the requested accessory to the home
ThisApp.HomeManager.PrimaryHome.AddAccessory (_controller.AccessoryBrowser.DiscoveredAccessories [indexPath.Row], (err) => {
    // Did an error occur
    if (err !=null) {
        // Inform user of error
        AlertView.PresentOKAlert("Add Accessory Error",err.LocalizedDescription,_controller);
    }
});
```

Se il `err` proprietà non è `null`, si è verificato un errore e deve essere presentata all'utente. In caso contrario, l'utente viene chiesto di immettere il codice di programma di installazione per il dispositivo da aggiungere:

[![](homekit-images/accessory02.png "Immettere il codice di programma di installazione per il dispositivo da aggiungere")](homekit-images/accessory02.png#lightbox)

Nel simulatore accessori HomeKit questo numero è reperibile nella **codice di installazione** campo:

[![](homekit-images/accessory03.png "Il campo del codice di programma di installazione nel simulatore accessori HomeKit")](homekit-images/accessory03.png#lightbox)

Per accessories HomeKit reale, ovvero il codice di installazione verrà stampato su un'etichetta sul dispositivo, nella finestra di prodotto o nel manuale dell'utente della funzione di accesso.

È necessario monitorare il Browser Accessori `DidRemoveNewAccessory` evento e l'utente di aggiornamento dell'interfaccia per rimuovere un accessorio dall'elenco disponibile quando l'utente ha aggiunto alla raccolta loro Home.

## <a name="working-with-accessories"></a>Utilizzo di accessori

Una volta una casa primario e stabilita e accessori sono stati aggiunti a esso, è possibile presentare un elenco di accessori (e facoltativamente chat) per l'utente da utilizzare.

Il `HMRoom` oggetto contiene tutte le informazioni relative a una sola chat room e accessori appartenenti a esso. Chat, facoltativamente, possono essere organizzate in una o più zone. Oggetto `HMZone` contiene tutte le informazioni relative a una zona specificata e tutte le chat appartenenti a esso.

Ai fini di questo esempio, si saranno mantenendo operazioni semplici e possono essere utilizzati con accessori un domestico direttamente, anziché organizzandoli nella chat room o alle zone.

Il `HMHome` oggetto contiene un elenco di accessori assegnato che possono essere presentato all'utente nel relativo `Accessories` proprietà. Ad esempio:

[![](homekit-images/accessory04.png "Un accessorio di esempio")](homekit-images/accessory04.png#lightbox)

Modulo in questo caso, l'utente può selezionare un accessorio specificato e lavorare con i servizi che offre.

## <a name="working-with-services"></a>Utilizzo dei servizi

Quando l'utente interagisce con un determinato dispositivo abilitato automazione home HomeKit, è in genere tramite i servizi che offre. Il `Services` proprietà del `HMAccessory` classe contiene una raccolta di `HMService` offre un dispositivo di oggetti che definiscono i servizi.

I servizi sono elementi quali le luci, thermostats, ciò porta garage, commutatori o blocchi. Alcuni dispositivi (ad esempio, un strumento di apertura sportello garage) fornirà più di un servizio, ad esempio una luce e la possibilità di aprire o chiudere lo sportello di una.

Oltre a servizi specifici che fornisce una funzione di accesso specificato, ogni funzione di accesso contiene un `Information Service` che definisce le proprietà, ad esempio nome, produttore, modello e il numero di serie.

### <a name="accessory-service-types"></a>Tipi di servizio degli accessori

I seguenti tipi di servizio sono disponibili tramite il `HMServiceType` enum:

- **AccessoryInformation** -fornisce informazioni sul dispositivo specificato automazione home (accessori).
- **AirQualitySensor** -definisce un sensore di qualità aria.
- **Batteria** -definisce lo stato della batteria dell'accessorio.
- **CarbonDioxideSensor** -definisce un sensore biossido di carbonio.
- **CarbonMonoxideSensor** -definisce un sensore carbonio.
- **ContactSensor** -definisce un sensore contatto (ad esempio una finestra viene aperta o chiusa).
- **Lo sportello** -definisce un sensore stato porta (ad esempio aperto o chiuso).
- **Ventola** -definisce una ventola controllata remota.
- **GarageDoorOpener** -definisce un strumento di apertura sportello garage.
- **HumiditySensor** -definisce un sensore umidità.
- **LeakSensor** -definisce un sensore perdita (come per un riscaldamento calda o lavatrice).
- **Lampadina** -definisce una luce autonomo o una che fa parte di un'altra funzione di accesso (ad esempio, un strumento di apertura sportello garage).
- **LightSensor** -definisce un sensore di luce.
- **LockManagement** -definisce un servizio che gestisce un blocco dello sportello automatico.
- **LockMechanism** -definisce un blocco di controllo remoto (ad esempio il blocco di una porta).
- **MotionSensor** -definisce un sensore di movimento.
- **OccupancySensor** -definisce un sensore occupazione.
- **Presa** -definisce una presa a muro controllata remoto.
- **SecuritySystem** -definisce un sistema di sicurezza iniziale.
- **StatefulProgrammableSwitch** -definisce un'opzione programmabile che rimane in uno stato offrono una volta attivato (ad esempio un commutatore capovolto).
- **StatelessProgrammableSwitch** -definisce un'opzione programmabile che restituisce lo stato iniziale dopo l'attivazione (ad esempio un pulsante di comando).
- **SmokeSensor** -definisce un sensore smoke.
- **Passare** -definisce un'opzione di attivazione/disattivazione come un interruttore standard.
- **Sensore di temperatura** -definisce un sensore di temperatura.
- **Termostato** -definisce un termostato smart consentono di controllare il sistema di un impianto.
- **Finestra** -definisce una finestra automatizzata che canna in modalità remota è aperta o chiusa.
- **WindowCovering** -definisce una finestra controllata in modalità remota di copertura, ad esempio entrata che può essere aperta o chiusa.

### <a name="displaying-service-information"></a>Visualizzazione di informazioni sul servizio

Dopo aver caricato un `HMAccessory` è possibile eseguire query singoli `HNService` oggetti fornisce e visualizzare tali informazioni all'utente:

[![](homekit-images/accessory05.png "Visualizzazione di informazioni sul servizio")](homekit-images/accessory05.png#lightbox)

È sempre consigliabile deve controllare il `Reachable` proprietà di un `HMAccessory` prima di tentare di utilizzarlo. Impossibile raggiungere l'utente non è all'interno dell'intervallo del dispositivo o se è stato scollegato, può essere un accessorio.

Dopo aver selezionato un servizio, l'utente può visualizzare o modificare una o più caratteristiche di tale servizio, monitorare o controllare un dispositivo di automazione iniziale specificato.

<a name="Working-with-Characteristics" />

## <a name="working-with-characteristics"></a>Utilizzo di caratteristiche

Ogni `HMService` oggetto può contenere una raccolta di `HMCharacteristic` gli oggetti che possono fornire informazioni sullo stato del servizio (ad esempio, una porta viene aperto o chiuso) o consentire all'utente di modificare uno stato (ad esempio, l'impostazione del colore di luce).

`HMCharacteristic` non solo fornisce informazioni su una caratteristica e il relativo stato, ma fornisce anche metodi per l'utilizzo con lo stato tramite _metadati caratteristica_ (`HMCharacteristisMetadata`). Questi metadati possono fornire le proprietà (ad esempio, gli intervalli di valori minimo e massimo) che sono utili quando si visualizzano informazioni per l'utente o consentono di modificare gli stati.

Il `HMCharacteristicType` enum fornisce un set di valori dei metadati di caratteristiche che può essere definita o modificata come segue:

 - AdminOnlyAccess
 - AirParticulateDensity
 - AirParticulateSize
 - AirQuality
 - AudioFeedback
 - BatteryLevel
 - Luminosità
 - CarbonDioxideDetected
 - CarbonDioxideLevel
 - CarbonDioxidePeakLevel
 - CarbonMonoxideDetected
 - CarbonMonoxideLevel
 - CarbonMonoxidePeakLevel
 - ChargingState
 - ContactType
 - CoolingThreshold
 - CurrentDoorState
 - CurrentHeatingCooling
 - CurrentHorizontalTilt
 - CurrentLightLevel
 - CurrentLockMechanismState
 - CurrentPosition
 - CurrentRelativeHumidity
 - CurrentSecuritySystemState
 - CurrentTemperature
 - CurrentVerticalTilt
 - FirmwareVersion
 - HardwareVersion
 - HeatingCoolingStatus
 - HeatingThreshold
 - HoldPosition
 - Tonalità
 - Identify
 - InputEvent
 - LeakDetected
 - LockManagementAutoSecureTimeout
 - LockManagementControlPoint
 - LockMechanismLastKnownAction
 - Log
 - Produttore
 - Modello
 - MotionDetected
 - nome
 - ObstructionDetected
 - OccupancyDetected
 - OutletInUse
 - OutputState
 - PositionState
 - Alimentazione
 - RotationDirection
 - RotationSpeed
 - Saturazione
 - numero di serie
 - SmokeDetected
 - SoftwareVersion
 - StatusActive
 - StatusFault
 - StatusJammed
 - StatusLowBattery
 - StatusTampered
 - TargetDoorState
 - TargetHeatingCooling
 - TargetHorizontalTilt
 - TargetLockMechanismState
 - TargetPosition
 - TargetRelativeHumidity
 - TargetSecuritySystemState
 - TargetTemperature
 - TargetVerticalTilt
 - TemperatureUnits
 - Versione

### <a name="working-with-a-characteristics-value"></a>Utilizzo di valore di una caratteristica

Per garantire l'app ha lo stato più recente di una determinata caratteristica, chiamare il `ReadValue` metodo la `HMCharacteristic` classe. Se il `err` proprietà non è `null`, si è verificato un errore e può o non può essere presentato all'utente.

La caratteristica `Value` proprietà contiene lo stato corrente della caratteristica specificato come un `NSObject`e di conseguenza non possono essere gestiti direttamente nel linguaggio c#.

Per leggere il valore, la seguente classe helper è stato aggiunto per il **HomeKitIntro** applicazione di esempio:

```csharp
using System;
using Foundation;
using System.Globalization;
using CoreGraphics;

namespace HomeKitIntro
{
    /// <summary>
    /// NS object converter is a helper class that helps to convert NSObjects into
    /// C# objects
    /// </summary>
    public static class NSObjectConverter
    {
        #region Static Methods
        /// <summary>
        /// Converts to an object.
        /// </summary>
        /// <returns>The object.</returns>
        /// <param name="nsO">Ns o.</param>
        /// <param name="targetType">Target type.</param>
        public static Object ToObject (NSObject nsO, Type targetType)
        {
            if (nsO is NSString) {
                return nsO.ToString ();
            }

            if (nsO is NSDate) {
                var nsDate = (NSDate)nsO;
                return DateTime.SpecifyKind ((DateTime)nsDate, DateTimeKind.Unspecified);
            }

            if (nsO is NSDecimalNumber) {
                return decimal.Parse (nsO.ToString (), CultureInfo.InvariantCulture);
            }

            if (nsO is NSNumber) {
                var x = (NSNumber)nsO;

                switch (Type.GetTypeCode (targetType)) {
                case TypeCode.Boolean:
                    return x.BoolValue;
                case TypeCode.Char:
                    return Convert.ToChar (x.ByteValue);
                case TypeCode.SByte:
                    return x.SByteValue;
                case TypeCode.Byte:
                    return x.ByteValue;
                case TypeCode.Int16:
                    return x.Int16Value;
                case TypeCode.UInt16:
                    return x.UInt16Value;
                case TypeCode.Int32:
                    return x.Int32Value;
                case TypeCode.UInt32:
                    return x.UInt32Value;
                case TypeCode.Int64:
                    return x.Int64Value;
                case TypeCode.UInt64:
                    return x.UInt64Value;
                case TypeCode.Single:
                    return x.FloatValue;
                case TypeCode.Double:
                    return x.DoubleValue;
                }
            }

            if (nsO is NSValue) {
                var v = (NSValue)nsO;

                if (targetType == typeof(IntPtr)) {
                    return v.PointerValue;
                }

                if (targetType == typeof(CGSize)) {
                    return v.SizeFValue;
                }

                if (targetType == typeof(CGRect)) {
                    return v.RectangleFValue;
                }

                if (targetType == typeof(CGPoint)) {
                    return v.PointFValue;
                }
            }

            return nsO;
        }

        /// <summary>
        /// Convert to string
        /// </summary>
        /// <returns>The string.</returns>
        /// <param name="nsO">Ns o.</param>
        public static string ToString(NSObject nsO) {
            return (string)ToObject (nsO, typeof(string));
        }

        /// <summary>
        /// Convert to date time
        /// </summary>
        /// <returns>The date time.</returns>
        /// <param name="nsO">Ns o.</param>
        public static DateTime ToDateTime(NSObject nsO){
            return (DateTime)ToObject (nsO, typeof(DateTime));
        }

        /// <summary>
        /// Convert to decimal number
        /// </summary>
        /// <returns>The decimal.</returns>
        /// <param name="nsO">Ns o.</param>
        public static decimal ToDecimal(NSObject nsO){
            return (decimal)ToObject (nsO, typeof(decimal));
        }

        /// <summary>
        /// Convert to boolean
        /// </summary>
        /// <returns><c>true</c>, if bool was toed, <c>false</c> otherwise.</returns>
        /// <param name="nsO">Ns o.</param>
        public static bool ToBool(NSObject nsO){
            return (bool)ToObject (nsO, typeof(bool));
        }

        /// <summary>
        /// Convert to character
        /// </summary>
        /// <returns>The char.</returns>
        /// <param name="nsO">Ns o.</param>
        public static char ToChar(NSObject nsO){
            return (char)ToObject (nsO, typeof(char));
        }

        /// <summary>
        /// Convert to integer
        /// </summary>
        /// <returns>The int.</returns>
        /// <param name="nsO">Ns o.</param>
        public static int ToInt(NSObject nsO){
            return (int)ToObject (nsO, typeof(int));
        }

        /// <summary>
        /// Convert to float
        /// </summary>
        /// <returns>The float.</returns>
        /// <param name="nsO">Ns o.</param>
        public static float ToFloat(NSObject nsO){
            return (float)ToObject (nsO, typeof(float));
        }

        /// <summary>
        /// Converts to double
        /// </summary>
        /// <returns>The double.</returns>
        /// <param name="nsO">Ns o.</param>
        public static double ToDouble(NSObject nsO){
            return (double)ToObject (nsO, typeof(double));
        }
        #endregion
    }
}
```

Il `NSObjectConverter` viene utilizzato ogni volta che l'applicazione deve leggere lo stato corrente di una caratteristica. Di seguito è riportato un esempio:

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

Nella riga precedente converte il valore in un `float` che può quindi essere usato nel codice c# di Xamarin.

Per modificare un `HMCharacteristic`, chiamare il relativo `WriteValue` (metodo) e andare a capo il nuovo valore in un `NSObject.FromObject` chiamare. Di seguito è riportato un esempio:

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

Se il `err` proprietà non è `null`, si è verificato e deve essere presentato all'utente di un errore.

### <a name="testing-characteristic-value-changes"></a>Le modifiche dei valori di caratteristiche di test

Quando si lavora con `HMCharacteristics` e accessori simulati, le modifiche per il `Value` proprietà può essere monitorata all'interno di accessori HomeKit simulatore.

Con il **HomeKitIntro** app in esecuzione nel dispositivo Hardware, le modifiche al valore di una caratteristica di iOS reale deve essere visibile quasi istantaneamente nel simulatore HomeKit accessori. Ad esempio, la modifica dello stato di una luce nell'app iOS:

[![](homekit-images/test01.png "Modifica dello stato di una luce in un'app iOS")](homekit-images/test01.png#lightbox)

Dovrebbero modificare lo stato della luce nel simulatore HomeKit accessori. Se il valore non viene modificato, controllare lo stato del messaggio di errore durante la scrittura di nuovi valori di caratteristiche e verificare che il dispositivo è ancora raggiungibile.

## <a name="advanced-homekit-features"></a>Funzionalità avanzate HomeKit

In questo articolo è illustrati le funzionalità di base necessarie per l'utilizzo di accessori HomeKit in un'app xamarin. IOS. Tuttavia, esistono numerose funzionalità avanzate di HomeKit che non sono trattate in questa introduzione:

- **Chat** -accessori HomeKit abilitata possono facoltativamente organizzati in chat dall'utente finale. In questo modo HomeKit accessori presente in un modo semplice per l'utente a comprendere e utilizzare. Per ulteriori informazioni sulla creazione e la gestione di chat, vedere Apple [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom) documentazione.
- **Le zone** -locali possono essere organizzati in aree facoltativamente dall'utente finale. Una zona fa riferimento a una raccolta di chat che l'utente può gestire come singola unità. Ad esempio: installare, al piano o seminterrato. Nuovamente, in questo modo HomeKit consentire di lavorare con accessori in modo significativo per l'utente finale. Per ulteriori informazioni sulla creazione e la gestione di zone, vedere Apple [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone) documentazione.
- **Azioni e l'azione imposta** -azioni modificare le caratteristiche di servizio degli accessori e possono essere raggruppate in set. Set di azioni fungono da script per controllare un gruppo di accessori e coordinare le azioni. Ad esempio, uno script "TV espressioni di controllo" potrebbe chiudere l'entrata abbassare le luci e attivare il televisore e del relativo sistema audio. Per ulteriori informazioni sulla creazione e gestione di azioni e set di azioni, vedere Apple [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction) e [HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet) documentazione.
- **I trigger** : un trigger può attivare uno o più azione impostare quando un determinato insieme di condizioni sono stati soddisfatti. Ad esempio, attivare la luce portch e bloccare tutte le porte esterne quando raggiunge scuro all'esterno. Per ulteriori informazioni sulla creazione e gestione di trigger, vedere Apple [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger) documentazione.

Poiché queste funzioni utilizzano le stesse tecniche presentate nell'esempio precedente, dovrebbero essere facile da implementare da Apple seguente [HomeKitDeveloper Guida](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html), [linee guida dell'interfaccia utente HomeKit](https://developer.apple.com/homekit/ui-guidelines/) e [ Riferimento di Framework HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref).

## <a name="homekit-app-review-guidelines"></a>Linee guida di revisione di App HomeKit

Prima di inviare un HomeKit app xamarin abilitata a iTunes Connect per il rilascio nell'iTunes App Store, assicurarsi di seguire le linee guida di Apple per le app HomeKit abilitato:

 - Lo scopo principale dell'app _deve_ essere automazione home, se si usa il framework HomeKit.
 - Testo di marketing dell'app deve informare gli utenti che viene utilizzato HomeKit e devono specificare un criterio di privacy.
 - La raccolta di informazioni utente o tramite HomeKit per la pubblicità è rigorosamente vietata.

Per la versione completa, rivedere le linee guida, vedere Apple [linee guida per analizzare App Store](https://developer.apple.com/app-store/review/guidelines/).

## <a name="whats-new-in-ios-9"></a>Novità in iOS 9

Apple ha le seguenti modifiche e aggiunte a HomeKit per iOS 9:

- **Gestione di oggetti esistenti** : se viene modificato un accessorio esistente, il gestore Home (`HMHomeManager`) verrà che informa dell'elemento specifico che è stato modificato.
- **Identificatori persistenti** -tutte le relative classi HomeKit includono ora un `UniqueIdentifier` proprietà per identificare un determinato elemento tra HomeKit abilitata App (o le istanze della stessa app).
- **Gestione utenti** -aggiunta di un controller di visualizzazione predefinite per fornire una gestione utenti gli utenti che dispongono dell'accesso ai dispositivi HomeKit nella home page dell'utente primario.
- **Funzionalità di utente** : gli utenti HomeKit hanno un set di privilegi che controllano quali funzioni sono in grado di utilizzare in HomeKit e HomeKit abilitato accessori. L'app deve visualizzare solo funzionalità correlate all'utente corrente. Ad esempio, solo un admins deve essere in grado di gestire gli altri utenti.
- **Predefiniti scene** -scene predefinite sono state create per quattro eventi comuni che si verificano per l'utente HomeKit medio: backup, lasciare, restituire, Vai a letto. Impossibile eliminare queste scene predefinite da una posizione iniziale.
- **Scene e Siri** -Siri ha un migliore supporto per le scene in iOS 9 e riconoscono il nome di qualsiasi scena definito in HomeKit. Un utente può eseguire una scena semplicemente parlando il nome su Siri.
- **Categorie degli accessori** -un set di categorie predefinite è stato aggiunto a tutti Accessori e consente di identificare il tipo di accessori da aggiungere a una posizione iniziale o di elaborazione all'interno dell'app. Tali categorie sono disponibili durante l'installazione di accessori.
- **Supporto di Apple Watch** - HomeKit è ora disponibile per watchOS e l'Apple Watch sarà in grado di controllo HomeKit abilitato dispositivi senza un iPhone da vicino le espressioni di controllo. HomeKit per watchOS supporta le seguenti funzionalità: case di visualizzazione, controllare accessori e automaticamente l'esecuzione.
- **Nuovo tipo di Trigger di evento** - oltre i trigger di tipo timer è supportati in iOS 8, iOS 9 ora supporta i trigger di evento in base a stato accessori (ad esempio, i dati del sensore) o geolocation. Utilizzano i trigger di evento `NSPredicates` per impostare le condizioni per l'esecuzione.
- **Accesso remoto** -con accesso remoto, l'utente è ora in grado di controllo relativi HomeKit abilitato Home automazione accessori quando sono fuori della casa in una posizione remota. In iOS 8 questo era supportato solo se l'utente dispone di una generazione 3rd Apple TV della casa. In iOS 9, questa limitazione è elevata e accesso remoto è supportata tramite iCloud e il protocollo di accessori HomeKit (HAP).
- **Nuova capacità di energia bassa Bluetooth (disattiva)** -HomeKit ora supporta più tipi di accessori in grado di comunicare tramite il protocollo di energia bassa Bluetooth (disattiva). Utilizza Secure Tunneling HAP, un accessorio HomeKit può esporre un altro accessorio Bluetooth tramite Wi-Fi (se è compreso nell'intervallo Bluetooth). In iOS 9, accessori BILITA dispone del supporto completo per le notifiche e metadati.
- **Le nuove categorie Accessori** -Apple aggiunte le seguenti nuove categorie di accessori in iOS 9: tende, motorizzate porte e Windows, i sistemi di allarme, sensori e commutatori programmabili.

Per ulteriori informazioni sulle nuove funzionalità di HomeKit in iOS 9, vedere Apple [HomeKit indice](https://developer.apple.com/homekit/) e [novità di HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210) video.

## <a name="summary"></a>Riepilogo

In questo articolo è stato introdotto framework di automazione home HomeKit di Apple. È stato illustrato come impostare e configurare i dispositivi di test con il simulatore di accessori HomeKit e come creare una semplice app xamarin per individuare, comunicare e controllare i dispositivi di automazione domestica utilizzando HomeKit.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 per gli sviluppatori](https://developer.apple.com/ios/pre-release/)
- [Novità in iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Linee guida dell'interfaccia utente HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Riferimento di Framework HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref)
