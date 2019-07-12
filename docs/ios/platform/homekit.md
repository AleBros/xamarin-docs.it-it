---
title: HomeKit in xamarin. IOS
description: HomeKit è il framework Apple per controllare i dispositivi di automazione domestica. Questo articolo introduce HomeKit e illustra la configurazione accessori di test nel simulatore accessori HomeKit e la scrittura di una semplice app xamarin. IOS per interagire con questi accessori.
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: a5402bb3b10387644282301075f63644149fcdc6
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831537"
---
# <a name="homekit-in-xamarinios"></a>HomeKit in xamarin. IOS

_HomeKit è il framework Apple per controllare i dispositivi di automazione domestica. Questo articolo introduce HomeKit e illustra la configurazione accessori di test nel simulatore accessori HomeKit e la scrittura di una semplice app xamarin. IOS per interagire con questi accessori._

[![](homekit-images/accessory01.png "App abilitata per un esempio HomeKit")](homekit-images/accessory01.png#lightbox)

Apple ha introdotto HomeKit in iOS 8 che consente di integrare facilmente più dispositivi di domotica fornitori una varietà di un'unità singola e coerente. Promuovendo un protocollo comune per l'individuazione, configurazione e controllo dei dispositivi di domotica, HomeKit consente ai dispositivi da fornitori non correlate all'interazione senza singoli fornitori che sia necessario coordinare le attività.

Con HomeKit, è possibile creare un'app xamarin. IOS che consente di controllare tutti i dispositivi HomeKit abilitato senza utilizzando fornitore specificati le API o le app. Uso di HomeKit, è possibile eseguire le operazioni seguenti:

- Rilevare i nuovi dispositivi di domotica HomeKit abilitata e aggiungerli a un database che verrà mantenuta tra tutti i dispositivi iOS dell'utente.
- Impostare, configurare, visualizzare e controllare tutti i dispositivi nel HomeKit _il Database di configurazione di Home_.
- Comunicare con qualsiasi dispositivo HomeKit preconfigurati e loro di eseguire singole azioni o lavorare insieme, ad esempio l'accensione di tutte le luci in cucina dei comandi.

Oltre a servire i dispositivi nel Database di configurazione Home per le app HomeKit abilitata, HomeKit fornisce accesso a comandi vocali Siri. Dato un programma di installazione di HomeKit configurato in modo appropriato, l'utente può inviare i comandi vocali, ad esempio "Siri, spegnere le luci in salotto."

<a name="Home-Configuration-Database" />

## <a name="the-home-configuration-database"></a>Il Database di configurazione iniziale

HomeKit consente di organizzare tutti i dispositivi di automazione in una posizione specificata in una raccolta Home. Questa raccolta offre un modo per l'utente raggruppare i dispositivi di domotica in unità disposte in modo logico con etichette significative e leggibile.

La raccolta Home viene archiviata in una pagina iniziale Database di configurazione che verrà automaticamente eseguito il backup e sincronizzato in tutti i dispositivi iOS dell'utente. HomeKit fornisce le classi seguenti per l'uso di Database di configurazione Home:

- `HMHome` -Si tratta del contenitore di primo livello che contiene tutte le informazioni e le configurazioni per tutti i dispositivi di domotica in un'unica posizione fisica (ad es. una singola famiglia residenza). L'utente può avere più residenza, ad esempio casa principale e una casa di ferie. Oppure possono contenere diversi "ospita" per la stessa proprietà, ad esempio casa principale e una casa guest tramite l'officina. In entrambi i casi, almeno uno `HMHome` oggetti _necessario_ da programma di installazione e archiviare prima di qualsiasi altra informazione HomeKit può essere immessi.
- `HMRoom` -While facoltativo, un `HMRoom` consente all'utente di definire locali specifici all'interno di una home page (`HMHome`), ad esempio: Cucina, bagno, Garage o salotto. L'utente può raggruppare tutti i dispositivi di domotica in una posizione specifica nelle loro casa in un `HMRoom` e agire su di essi come un'unità. Ad esempio, che chiede di Siri per spegnere le luci garage.
- `HMAccessory` -Ciò rappresenta una singola, HomeKit fisico abilitato dispositivo automazione in cui è stata installata residenza dell'utente (ad esempio un termostato intelligente). Ciascuna `HMAccessory` viene assegnato a un `HMRoom`. Se l'utente non ha configurato le stanze, accessori HomeKit assegna a una chat room predefinita speciali.
- `HMService` : Rappresenta un servizio fornito da un determinato `HMAccessory`, ad esempio lo stato di attivazione/disattivazione di una luce o il relativo colore (se è supportata la modifica di colore). Ogni `HMAccessory` può avere più di un servizio, ad esempio un opener sportello garage che include anche una luce. Inoltre, un determinato `HMAccessory` possono esserci servizi, ad esempio aggiornamento del firmware, non controllati dall'utente.
- `HMZone` -Consente all'utente di raggruppare un insieme di `HMRoom` gli oggetti in zone logiche, ad esempio installare, al piano o di magazzino. Anche se facoltativo, in questo modo per le interazioni, ad esempio che richiede di Siri per attivare tutti la luce disattivata al piano.

<a name="Provisioning-a-HomeKit-App" />

## <a name="provisioning-a-homekit-app"></a>Il provisioning di un'App HomeKit

A causa dei requisiti di sicurezza imposti da HomeKit, un'app xamarin. IOS che usa il framework HomeKit deve essere configurata correttamente in entrambi al portale Apple Developer e nel file di progetto xamarin. IOS.

Seguire questa procedura:

1. Accedere al [portale Apple Developer](https://developer.apple.com).
2. Fare clic su **certificati, identificatori e profili**.
3. Se non già stato fatto, fare clic su **identificatori** e creare un ID per l'app (ad esempio `com.company.appname`), altrimenti modificare l'ID esistente.
4. Verificare che il **HomeKit** service è stata selezionata per l'ID specificato: 

    [![](homekit-images/provision01.png "Abilitare il servizio HomeKit per l'ID specificato")](homekit-images/provision01.png#lightbox)
5. Salvare le modifiche.
4. Fare clic su **profili di Provisioning** > **sviluppo** e creare un nuovo profilo di provisioning per l'app di sviluppo: 

    [![](homekit-images/provision02.png "Creare un nuovo profilo di provisioning per l'app di sviluppo")](homekit-images/provision02.png#lightbox)
5. Scaricare e installare il nuovo profilo di provisioning o usare Xcode per scaricare e installare il profilo.
6. Modificare le opzioni di progetto xamarin. IOS e assicurarsi di usare il profilo di provisioning appena creato: 

    [![](homekit-images/provision03.png "Seleziona profilo di provisioning appena creato")](homekit-images/provision03.png#lightbox)
7. Successivamente, modificare il **Info. plist** file e assicurarsi che si sta utilizzando l'ID dell'App che è stato utilizzato per creare il profilo di provisioning: 

    [![](homekit-images/provision04.png "Impostare l'ID App ")](homekit-images/provision04.png#lightbox)
8. Infine, modificare il **entitlements. plist** del file e verificare che il **HomeKit** entitlement è stato selezionato: 

    [![](homekit-images/provision05.png "Abilitare i diritti di HomeKit")](homekit-images/provision05.png#lightbox)
9. Salvare le modifiche a tutti i file.

Con queste impostazioni posto, l'applicazione è ora pronta per accedere alle API di Framework HomeKit. Per informazioni dettagliate sul provisioning, vedere la [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) e [Provisioning dell'App](~/ios/get-started/installation/device-provisioning/index.md) Guide.

> [!IMPORTANT]
> Test di un'applicazione HomeKit abilitata richiede un dispositivo iOS reali che sia stato preparato correttamente per lo sviluppo. Non è possibile testare HomeKit dal simulatore iOS.

## <a name="the-homekit-accessory-simulator"></a>Il simulatore di accessori HomeKit

Per fornire un modo per testare tutti i dispositivi di domotica possibili e i servizi, senza che sia necessario un dispositivo fisico, Apple creato il _simulatore accessori HomeKit_. Usa il simulatore, è possibile impostare e configurare i dispositivi virtuali HomeKit.

### <a name="installing-the-simulator"></a>Installazione del simulatore

Apple offre il simulatore di accessori HomeKit come download separato da Xcode, pertanto sarà necessario installarlo prima di continuare.

Seguire questa procedura:

1. In un web browser, visitare [download per sviluppatori Apple](https://developer.apple.com/download/more/?name=for%20Xcode)
2. Scaricare il **strumenti di Xcode xxx aggiuntivi** (dove xxx è la versione di Xcode installati): 

    [![](homekit-images/simulator01.png "Scaricare gli strumenti aggiuntivi di Xcode")](homekit-images/simulator01.png#lightbox)
3. Aprire l'immagine del disco e installare gli strumenti di **applicazioni** directory.

Con il simulatore di accessori HomeKit installato, è possono creare accessori virtuali per il test.

### <a name="creating-virtual-accessories"></a>Creazione di accessori virtuali

Avviare il simulatore di accessori HomeKit e creare alcune accessori virtuali, eseguire le operazioni seguenti:

1. Dalla cartella di applicazioni, avviare il simulatore di accessori HomeKit: 

    [![](homekit-images/simulator02.png "Il simulatore di accessori HomeKit")](homekit-images/simulator02.png#lightbox)
2. Scegliere il **+** e selezionare **accessorio nuovo...** : 

    [![](homekit-images/simulator03.png "Aggiungere un nuovo accessorio")](homekit-images/simulator03.png#lightbox)
3. Inserire le informazioni sulla nuova funzione di accesso e scegliere il **fine** pulsante: 

    [![](homekit-images/simulator04.png "Inserire le informazioni sulla nuova funzione di accesso")](homekit-images/simulator04.png#lightbox)
4. Fare clic su di **aggiungere il servizio...** e selezionare un tipo di servizio dall'elenco a discesa: 

    [![](homekit-images/simulator05.png "Selezionare un tipo di servizio dall'elenco a discesa")](homekit-images/simulator05.png#lightbox)
5. Fornire una **Name** per il servizio e fare clic sui **fine** pulsante: 

    [![](homekit-images/simulator06.png "Immettere un nome per il servizio")](homekit-images/simulator06.png#lightbox)
6. È possibile specificare le caratteristiche facoltative per un servizio, fare clic il **caratteristica aggiungere** pulsante e configurare le impostazioni richieste: 

    [![](homekit-images/simulator07.png "Configurare le impostazioni obbligatorie")](homekit-images/simulator07.png#lightbox)
7. Ripetere i passaggi precedenti per creare uno di ogni tipo di dispositivo virtuali automazione domestica che supporta HomeKit.

Con alcuni esempio virtuali accessori HomeKit creata e configurata, è possibile utilizzare e controllare i dispositivi dall'app xamarin. IOS.

## <a name="configuring-the-infoplist-file"></a>Configurazione del File Info. plist

Novità per iOS 10 (e versioni successive), lo sviluppatore dovrà aggiungere il `NSHomeKitUsageDescription` chiave per l'app `Info.plist` file e fornire una dichiarazione di stringa perché l'app desidera accedere ai database HomeKit dell'utente. Questa stringa verrà visualizzata il tempo utente la prima che esecuzione dell'app:

[![](homekit-images/info01.png "La finestra di dialogo di autorizzazione HomeKit")](homekit-images/info01.png#lightbox)

Per impostare questa chiave, eseguire le operazioni seguenti:

1. Fare doppio clic il `Info.plist` del file nei **Esplora soluzioni** per aprirlo e modificarlo.
2. Nella parte inferiore della schermata, passare al **origine** visualizzazione.
3. Aggiungere un nuovo **voce** all'elenco.
4. Nell'elenco a discesa, selezionare **Privacy - descrizione utilizzo HomeKit**: 

    [![](homekit-images/info02.png "Selezionare Privacy - descrizione utilizzo HomeKit")](homekit-images/info02.png#lightbox)
5. Immettere una descrizione per il motivo per cui vuole che l'app per l'accesso HomeKit database dell'utente: 

    [![](homekit-images/info03.png "Immettere una descrizione")](homekit-images/info03.png#lightbox)
6. Salvare le modifiche apportate al file.

> [!IMPORTANT]
> Impossibile impostare il `NSHomeKitUsageDescription` chiavi nel `Info.plist` file comporterà l'app _invisibile all'utente non riusciti_ (viene chiuso dal sistema in fase di esecuzione) senza errori quando si esegue in iOS 10 (o versioni successive).

## <a name="connecting-to-homekit"></a>La connessione a HomeKit

Per comunicare con HomeKit, deve prima creare un'istanza di un'istanza di app xamarin. IOS di `HMHomeManager` classe. Il gestore Home è il punto di ingresso centrale in HomeKit ed è responsabile di fornire un elenco delle posizioni disponibili, l'aggiornamento e gestione di tale elenco e la restituzione dell'utente _primario Home_.

Il `HMHome` oggetto contiene tutte le informazioni su un indirizzo di base offrono inclusi eventuali chat, gruppi o aree che potrebbero contenere, insieme a eventuali accessori di automazione domestica che sono stati installati. Per poter eseguire qualsiasi operazione in HomeKit, almeno uno `HMHome` devono essere creati e assegnati come la home page database primario.

L'app è responsabile della verifica dell'esistenza di un database primario a casa e la creazione e assegnazione di uno se non esiste.

### <a name="adding-a-home-manager"></a>Aggiunta di una gestione Home

Per aggiungere riconoscimento HomeKit a un'app xamarin. IOS, modificare il **AppDelegate.cs** file per modificarlo e renderlo simile al seguente:

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

Quando l'applicazione viene eseguita prima di tutto, l'utente verrà richiesto se desiderano in modo che possa accedere alle informazioni HomeKit:

[![](homekit-images/home01.png "L'utente verrà richiesto se desiderano in modo che possa accedere alle informazioni HomeKit")](homekit-images/home01.png#lightbox)

Se l'utente risponde **OK**, quindi l'applicazione sarà in grado di lavorare con loro accessori HomeKit in caso contrario non verrà e tutte le chiamate a HomeKit avrà esito negativo con un errore.

Con il gestore Home posto, quindi l'applicazione sarà necessario vedere se è stato configurato un database primario a casa e in caso contrario, offrono un modo per l'utente creare e assegnare uno.

### <a name="accessing-the-primary-home"></a>L'accesso a casa primario

Come indicato in precedenza, home page di un database primario deve essere creato e configurato prima HomeKit è disponibile ed è responsabilità dell'app per fornire un modo per l'utente creare e assegnare un indirizzo di base in presenza di una primaria non esiste già.

Quando l'app viene avviata prima di tutto o restituisce dallo sfondo, è necessario monitorare il `DidUpdateHomes` eventi del `HMHomeManager` classe per controllare l'esistenza di un database primario a casa. Se non ne esiste, deve fornire un'interfaccia per l'utente per crearne uno.

Il codice seguente può essere aggiunti a un controller di visualizzazione per verificare la presenza della casa primario:

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

Quando il gestore Home stabilisce una connessione a HomeKit, il `DidUpdateHomes` evento verrà generato, qualsiasi case esistenti verranno caricate per l'insieme del gestore del case e la home page database primario verrà caricata, se disponibile.

### <a name="adding-a-primary-home"></a>Aggiunta di un database primario a casa

Se il `PrimaryHome` proprietà del `HMHomeManager` viene `null` dopo che un `DidUpdateHomes` evento, è necessario fornire un modo per l'utente creare e assegnare una home page di database primario prima di continuare.

In genere l'app presenterà un form per l'utente per assegnare un nome di una nuova pagina iniziale che viene quindi passata alla gestione iniziale per il programma di installazione come la home page database primario. Per il **HomeKitIntro** creato in IOS Designer e chiamato dall'app di esempio, una vista modale la `AddHomeSegue` segue dall'interfaccia principale dell'app.

Fornisce un campo di testo per l'utente di immettere un nome per la nuova home page e un pulsante per aggiungere la home page. Quando l'utente tocca il **Add Home** pulsante, il codice seguente chiama il responsabile principale per aggiungere la home page:

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

Il `AddHome` metodo tenterà di creare nuova home page e restituirlo alla routine di callback specificato. Se il `error` proprietà non `null`, si è verificato un errore e deve essere presentato all'utente. Gli errori più comuni sono causati da un nome non univoco home o la gestione principale non riesca a comunicare con HomeKit.

Se la home page è stata creata correttamente, è necessario chiamare il `UpdatePrimaryHome` metodo per impostare la nuova home page come la home page database primario. Anche in questo caso, se il `error` proprietà non `null`, si è verificato un errore e deve essere presentato all'utente.

È anche necessario monitorare Home del responsabile `DidAddHome` e `DidRemoveHome` eventi e aggiornamento dell'interfaccia utente dell'app in base alle esigenze.

> [!IMPORTANT]
> Il `AlertView.PresentOKAlert` metodo usato nell'esempio di codice precedente è una classe helper nell'applicazione HomeKitIntro che rende l'utilizzo di iOS gli avvisi più semplice.


## <a name="finding-new-accessories"></a>Ricerca di nuovi accessori

Una volta home page di un database primario è stato definito o caricato da Gestione principale, l'app xamarin. IOS è possibile chiamare il `HMAccessoryBrowser` per individuare eventuali nuovi accessori di automazione domestica e aggiungerle a una posizione iniziale.

Chiamare il `StartSearchingForNewAccessories` metodo per avviare la ricerca di nuovi accessori e `StopSearchingForNewAccessories` metodo al termine.

> [!IMPORTANT]
> `StartSearchingForNewAccessories` non lasciare in esecuzione per lunghi periodi di tempo perché l'operazione influirebbe negativamente sia la durata della batteria e le prestazioni di un dispositivo iOS. Chiamare il metodo suggerite da Apple `StopSearchingForNewAccessories` dopo un minuto o la ricerca solo quando l'interfaccia utente trova accessorio viene presentata all'utente.

Il `DidFindNewAccessory` eventi verranno chiamato quando vengono rilevati nuovi accessori e verranno aggiunti per il `DiscoveredAccessories` elenco nel Browser accessori.

Il `DiscoveredAccessories` elenco conterrà una raccolta di `HMAccessory` oggetti che definiscono un offrono HomeKit abilitato dispositivo automazione domestica e i relativi servizi disponibili, ad esempio luci o garage sportello controllo.

Una volta che viene rilevata nuova funzione di accesso, dovrebbe essere visualizzata all'utente e quindi, è possibile selezionarla e aggiungerlo a una posizione iniziale. Esempio:

[![](homekit-images/accessory01.png "Ricerca di un nuovo accessorio")](homekit-images/accessory01.png#lightbox)

Chiamare il `AddAccessory` metodo per aggiungere l'accessorio selezionato alla raccolta della home page. Ad esempio:

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

Se il `err` proprietà non `null`, si è verificato un errore e deve essere presentato all'utente. In caso contrario, l'utente verrà richiesto di immettere il codice di programma di installazione per il dispositivo da aggiungere:

[![](homekit-images/accessory02.png "Immettere il codice di programma di installazione per il dispositivo da aggiungere")](homekit-images/accessory02.png#lightbox)

Nel simulatore accessori HomeKit questo numero può trovarsi sotto il **codice di installazione** campo:

[![](homekit-images/accessory03.png "Il campo del codice di programma di installazione nel simulatore accessori HomeKit")](homekit-images/accessory03.png#lightbox)

Per gli accessori HomeKit reali, ovvero il codice di installazione verrà stampato su un'etichetta nel dispositivo stesso, nella confezione del prodotto o nel manuale dell'utente della funzione di accesso.

È necessario monitorare il Browser Accessori `DidRemoveNewAccessory` evento e l'utente di aggiornamento dell'interfaccia per rimuovere un accessorio dall'elenco disponibile dopo che l'utente ha aggiunto per la propria raccolta Home.

## <a name="working-with-accessories"></a>Utilizzo di accessori

Una volta una home page di database primario e stato definito e accessories sono stati aggiunti a esso, è possibile presentare un elenco di accessori (e facoltativamente le chat) per l'utente lavorare con.

Il `HMRoom` oggetto contiene tutte le informazioni su una sola chat room e qualsiasi accessori appartenenti a esso. Le chat, facoltativamente, possono essere organizzate in una o più zone. Oggetto `HMZone` contiene tutte le informazioni su una determinata zona e tutte le sale appartenenti a esso.

Ai fini di questo esempio, ti terremo aggiornato operazioni semplici e funzionano con accessori di un indirizzo di base direttamente, anziché organizzandoli in chat o aree.

Il `HMHome` oggetto contiene un elenco di assegnato accessorio che può essere presentato all'utente nel relativo `Accessories` proprietà. Ad esempio:

[![](homekit-images/accessory04.png "Un accessorio di esempio")](homekit-images/accessory04.png#lightbox)

Formare in questo caso, l'utente può selezionare un accessorio specificato e utilizzare i servizi che offre.

## <a name="working-with-services"></a>Uso dei servizi

Quando l'utente interagisce con un determinato dispositivo abilitato automazione domestica HomeKit, è in genere tramite i servizi che offre. Il `Services` proprietà del `HMAccessory` classe contiene una raccolta di `HMService` offre un dispositivo di oggetti che definiscono i servizi.

I servizi sono elementi quali le luci, termostati connessi, ciò porta garage, commutatori o i blocchi. Alcuni dispositivi (ad esempio, un comportamento sportello garage) fornirà più di un servizio, ad esempio una luce e la possibilità di aprire o chiudere lo sportello di una.

Oltre a servizi specifici che fornisce un accessorio specificato, ogni accessorio contiene un `Information Service` che definisce le proprietà, ad esempio nome, produttore, modello e numero di serie.

### <a name="accessory-service-types"></a>Tipi di servizio degli accessori

I seguenti tipi di servizio sono disponibili tramite il `HMServiceType` enum:

- **AccessoryInformation** -fornisce informazioni sul dispositivo specificato automazione domestica (accessori).
- **AirQualitySensor** -definisce un sensore di qualità dell'aria.
- **Batteria** -definisce lo stato della batteria di un accessorio.
- **CarbonDioxideSensor** -definisce un emissioni di anidride carbonica sensore.
- **CarbonMonoxideSensor** -definisce un sensore di carbonio.
- **ContactSensor** -definisce un sensore contatto (ad esempio, una finestra viene aperto o chiuso).
- **Lo sportello** -definisce un sensore stato sportello (ad esempio aperto o chiuso).
- **Ventola** -definisce una ventola controllata remota.
- **GarageDoorOpener** -definisce un opener sportello garage.
- **HumiditySensor** -definisce un sensore umidità.
- **LeakSensor** -definisce un sensore perdita (come per una frequente water heater o lavatrice).
- **Indicatore lampadina** -definisce una luce autonomo o una luce che fa parte di un'altra accessorio (ad esempio, un comportamento sportello garage).
- **LightSensor** -definisce un sensore di luminosità.
- **LockManagement** -definisce un servizio che gestisce una serratura automatizzati.
- **LockMechanism** -definisce un blocco remoto controllato (ad esempio, una serratura).
- **MotionSensor** -definisce un sensore di movimento.
- **OccupancySensor** -definisce un sensore di occupazione.
- **Outlet** -definisce una presa a muro controllata remoto.
- **SecuritySystem** -definisce un sistema di sicurezza a casa.
- **StatefulProgrammableSwitch** -definisce un'opzione programmabile che rimane in uno stato di assegnare una volta attivato (ad esempio un interruttore flip).
- **StatelessProgrammableSwitch** -definisce un'opzione programmabile che restituisce lo stato iniziale dopo l'attivazione (ad esempio un pulsante di comando).
- **SmokeSensor** -definisce un sensore di fumo.
- **Passare** -definisce un'opzione di attivazione/disattivazione, ad esempio un interruttore standard.
- **Sensore di temperatura** -definisce un sensore di temperatura.
- **Termostato** -definisce un termostato intelligente consentono di controllare un sistema HVAC.
- **Finestra** -definisce una finestra automatizzata che zucchero essere in modalità remota aperta o chiusa.
- **WindowCovering** -definisce una finestra controllata da postazione remota che copre, ad esempio a entrata che può essere aperta o chiusa.

### <a name="displaying-service-information"></a>Visualizzazione di informazioni sul servizio

Dopo il caricamento di un `HMAccessory` è possibile eseguire una query singoli `HNService` oggetti fornisce e visualizzare tali informazioni all'utente:

[![](homekit-images/accessory05.png "Visualizzazione di informazioni sul servizio")](homekit-images/accessory05.png#lightbox)

È sempre consigliabile deve controllare la `Reachable` proprietà di un `HMAccessory` prima di provare a usarlo. Un accessorio può essere Impossibile raggiungere l'utente non è nell'intervallo del dispositivo o se è stato scollegato.

Dopo aver selezionato un servizio, l'utente può visualizzare o modificare una o più caratteristiche del servizio per monitorare o controllare un dispositivo specifico automazione domestica.

<a name="Working-with-Characteristics" />

## <a name="working-with-characteristics"></a>Utilizzo di caratteristiche

Ciascuna `HMService` oggetto può contenere una raccolta di `HMCharacteristic` gli oggetti che possono fornire informazioni sullo stato del servizio (ad esempio, una porta viene aperta o chiusa) o consentire all'utente di modificare uno stato (ad esempio, l'impostazione del colore di una luce).

`HMCharacteristic` non solo fornisce informazioni su una caratteristica e il relativo stato, ma fornisce anche metodi per l'utilizzo con lo stato tramite _metadati caratteristica_ (`HMCharacteristisMetadata`). Questi metadati possono fornire proprietà (ad esempio, gli intervalli di valori minimo e massimo) che sono utili quando si visualizzano informazioni per l'utente o consentendo loro di modificare gli stati.

Il `HMCharacteristicType` enum fornisce un set di valori di metadati caratteristica può essere definita o modificata come segue:

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
- currentPosition
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
- Name
- ObstructionDetected
- OccupancyDetected
- OutletInUse
- OutputState
- PositionState
- PowerState
- RotationDirection
- RotationSpeed
- Saturazione
- Numero di serie
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
- Version

### <a name="working-with-a-characteristics-value"></a>Utilizza valore di una caratteristica

Per essere certi che è l'app ha lo stato più recente di una caratteristica specificata, chiamare il `ReadValue` metodo di `HMCharacteristic` classe. Se il `err` proprietà non `null`, si è verificato un errore e può o non può essere presentato all'utente.

La caratteristica `Value` proprietà contiene lo stato corrente della caratteristica specificata come un `NSObject`e di conseguenza non può essere modificata con direttamente in C#.

Per leggere il valore, è stata aggiunta la seguente classe helper per la **HomeKitIntro** applicazione di esempio:

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

Il `NSObjectConverter` viene usato ogni volta che l'applicazione deve leggere lo stato corrente di una caratteristica. Di seguito è riportato un esempio:

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

La riga precedente converte il valore in una `float` che può quindi essere utilizzato in di Xamarin C# codice.

Per modificare un `HMCharacteristic`, chiamare relativi `WriteValue` (metodo) e andare a capo il nuovo valore in un `NSObject.FromObject` chiamare. Di seguito è riportato un esempio:

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

Se il `err` proprietà non `null`, un errore si è verificato e deve essere presentato all'utente.

### <a name="testing-characteristic-value-changes"></a>Le modifiche dei valori caratteristici di test

Quando si lavora `HMCharacteristics` e accessori simulati, modifiche al `Value` proprietà può essere monitorata all'interno del simulatore di accessori HomeKit.

Con il **HomeKitIntro** app in esecuzione in iOS reali Hardware del dispositivo, le modifiche a valore di una caratteristica deve essere quasi immediatamente visibile nel simulatore accessori HomeKit. Ad esempio, la modifica dello stato di una luce nell'app per iOS:

[![](homekit-images/test01.png "La modifica dello stato di una luce in un'app per iOS")](homekit-images/test01.png#lightbox)

Devono modificare lo stato della luce nel simulatore accessori HomeKit. Se il valore non cambia, controllare lo stato del messaggio di errore durante la scrittura di nuovi valori caratteristici e assicurarsi che il dispositivo è ancora raggiungibile.

## <a name="advanced-homekit-features"></a>Funzionalità avanzate HomeKit

Questo articolo ha illustrato le funzionalità di base necessarie per l'utilizzo con accessori HomeKit in un'app xamarin. IOS. Esistono tuttavia alcune funzionalità avanzate di HomeKit che non sono illustrate in questa introduzione:

- **Le chat** -accessori HomeKit abilitata possono facoltativamente organizzati in chat dall'utente finale. In questo modo HomeKit accessori presente in un modo semplice per l'utente a comprendere e utilizzare. Per altre informazioni sulla creazione e gestione di chat, vedere di Apple [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom) documentazione.
- **Le zone** -chat possono facoltativamente essere organizzate in zone dall'utente finale. Una zona si intende una raccolta di stanze di cui l'utente può gestire come singola unità. Ad esempio:  Installare, magazzino o al piano. Anche in questo caso, in questo modo HomeKit presentare e lavorare con accessori in modo significativo per l'utente finale. Per altre informazioni sulla creazione e gestione di zone, vedere di Apple [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone) documentazione.
- **Set di azioni e azioni** -azioni modificare caratteristiche del servizio degli accessori e possono essere raggruppate in set. Set di azioni fungono da script per controllare un gruppo di accessori e coordinare le azioni. Ad esempio, uno script "Watch TV" potrebbe chiudere l'entrata abbassare le luci e attivare la televisione e del relativo sistema audio. Per altre informazioni sulla creazione e gestione di set di azioni e azioni, vedere di Apple [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction) e [HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet) documentazione.
- **I trigger** : un trigger può attivare uno o più azione impostata quando un determinato set di condizioni siano stati soddisfatti. Ad esempio, attivare la luce portch e bloccare tutte le porte esterne quando rileva scuro all'esterno. Per altre informazioni sulla creazione e gestione di trigger, vedere di Apple [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger) documentazione.

Poiché queste funzioni usano le stesse tecniche presentate nell'esempio precedente, devono essere facili da implementare da seguenti di Apple [Guida HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html), [linee guida dell'interfaccia utente HomeKit](https://developer.apple.com/homekit/ui-guidelines/) e [ Riferimento a Framework HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref).

## <a name="homekit-app-review-guidelines"></a>Linee guida di revisione di App HomeKit

Prima di inviare un HomeKit abilitata app xamarin. IOS in iTunes Connect per il rilascio in iTunes App Store, assicurarsi di seguire le linee guida di Apple per le app HomeKit abilitata:

- Lo scopo principale dell'app _necessario_ da automazione domestica se si usa il framework HomeKit.
- Testo marketing SQL dell'app deve informare gli utenti che viene usato HomeKit e devono fornire un'informativa sulla privacy.
- Raccolta delle informazioni utente o l'uso di HomeKit per scopi di pubblicità rigorosamente è vietata.

Rivedere le linee guida per la versione completa, vedere di Apple [linee guida revisione di App Store](https://developer.apple.com/app-store/review/guidelines/).

## <a name="whats-new-in-ios-9"></a>What ' s New in iOS 9

Apple ha tentato di modifiche e le aggiunte seguenti HomeKit per iOS 9:

- **Manutenzione di oggetti esistenti** : se viene modificato un accessorio esistente, la gestione Home (`HMHomeManager`) indicherà dell'elemento specifico che è stato modificato.
- **Identificatori persistenti** -tutte le relative classi HomeKit includono ora un `UniqueIdentifier` proprietà per identificare in modo univoco un elemento specificato tra HomeKit abilitata App (o le istanze della stessa app).
- **Gestione degli utenti** -aggiunta di un controller di visualizzazione predefinite per fornire la gestione utente gli utenti che dispongono dell'accesso ai dispositivi HomeKit nella home page dell'utente primario.
- **Le funzionalità utente** : HomeKit gli utenti hanno un set di privilegi che controllano quali funzioni sono in grado di utilizzare in HomeKit e HomeKit abilitato accessori. L'app deve essere visualizzato solo funzionalità pertinenti per l'utente corrente. Ad esempio, solo un amministratori devono essere in grado di mantenere gli altri utenti.
- **Predefined scene** -scene predefinite sono state create per quattro eventi più comuni che si verificano per l'utente HomeKit medio: Inizia subito, lasciare, restituire, andare a letto. Non è possibile eliminare queste scene predefinite da una posizione iniziale.
- **Le scene e Siri** -Siri hanno un supporto più approfondito per in background in iOS 9 ed è possibile riconoscere il nome di qualsiasi scena definito in HomeKit. Un utente può eseguire una scena semplicemente parlando impostarne il nome su Siri.
- **Le categorie degli accessori** -un set di categorie predefinite è stato aggiunto a tutti i accessori e consente di identificare il tipo di accessori viene aggiunto a un indirizzo di base o lavorato all'interno dell'app. Queste nuove categorie sono disponibili durante l'installazione di accessori.
- **Supporto di Apple Watch** : HomeKit è ora disponibile per watchOS e l'Apple Watch saranno in grado di controllo HomeKit abilitato i dispositivi senza un iPhone da vicino l'espressione di controllo. HomeKit per watchOS supporta le funzionalità seguenti: Visualizzazione delle abitazioni, controllo accessori e l'esecuzione in background.
- **Nuovo tipo di Trigger di evento** : oltre ai trigger di tipo timer è supportati in iOS 8 e iOS 9 ora supporta i trigger di evento in base accessorio stato (ad esempio i dati del sensore) o la georilevazione. Usano i trigger di evento `NSPredicates` impostare le condizioni per l'esecuzione.
- **Accesso remoto** -con accesso remoto, l'utente sarà in grado di controllo relativi HomeKit abilitato Home accessori di automazione da fuori casa in una posizione remota. In iOS 8 questo era supportato solo se l'utente ha una generazione 3rd Apple TV nell'ambiente domestico. In iOS 9, questa limitazione è stata aumentata e accesso remoto è supportato tramite il protocollo di accessori HomeKit (HAP) e iCloud.
- **Nuove funzionalità Bluetooth Low Energy (BLE)** -HomeKit ora supporta più tipi di accessori in grado di comunicare tramite il protocollo Bluetooth Low Energy (BLE). Usa Secure Tunneling HAP, un accessorio HomeKit può esporre un altro accessorio Bluetooth tramite Wi-Fi (se è compreso nell'intervallo di Bluetooth). In iOS 9, BLE accessori dispone del supporto completo per le notifiche e metadati.
- **Le nuove categorie accessorio** -Apple aggiunte le seguenti nuove categorie accessorio in iOS 9: Tende, porte concetto e Windows, sistemi di allarme, sensori e commutatori programmabili.

Per altre informazioni sulle nuove funzionalità di HomeKit in iOS 9, vedere di Apple [indice HomeKit](https://developer.apple.com/homekit/) e [What ' s New in HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210) video.

## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto i framework di automazione domestica HomeKit Apple. È stato illustrato come impostare e configurare i dispositivi di test usando il simulatore di accessori HomeKit e su come creare una semplice app xamarin. IOS per individuare, comunicare e controllare i dispositivi di domotica utilizzo HomeKit.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [per gli sviluppatori iOS 9](https://developer.apple.com/ios/pre-release/)
- [What ' s New in iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Linee guida dell'interfaccia utente di HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Riferimento a Framework HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref)
