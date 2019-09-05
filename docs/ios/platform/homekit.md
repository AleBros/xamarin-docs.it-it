---
title: HomeKit in Novell. iOS
description: HomeKit è il Framework di Apple per il controllo dei dispositivi di automazione domestica. Questo articolo presenta HomeKit e illustra la configurazione di accessori di test nel simulatore di accessori HomeKit e la scrittura di una semplice app Novell. iOS per interagire con questi accessori.
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: f98cd3110719827d8cfeceef4dc9e73776c79f3f
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292719"
---
# <a name="homekit-in-xamarinios"></a>HomeKit in Novell. iOS

_HomeKit è il Framework di Apple per il controllo dei dispositivi di automazione domestica. Questo articolo presenta HomeKit e illustra la configurazione di accessori di test nel simulatore di accessori HomeKit e la scrittura di una semplice app Novell. iOS per interagire con questi accessori._

[![](homekit-images/accessory01.png "App abilitata per HomeKit di esempio")](homekit-images/accessory01.png#lightbox)

Apple ha introdotto HomeKit in iOS 8 come metodo per integrare facilmente più dispositivi di automazione della casa da diversi fornitori in un'unica unità coerente. Con la promozione di un protocollo comune per l'individuazione, la configurazione e il controllo dei dispositivi di automazione domestica, HomeKit consente ai dispositivi di fornitori non correlati di collaborare, il tutto senza che i singoli fornitori debbano coordinare le attività.

Con HomeKit è possibile creare un'app Novell. iOS che controlla qualsiasi dispositivo abilitato per HomeKit senza usare le API o le app fornite dal fornitore. Utilizzando HomeKit, è possibile eseguire le operazioni seguenti:

- Individuare i nuovi dispositivi di Home Automation abilitati per HomeKit e aggiungerli a un database che sarà mantenuto in tutti i dispositivi iOS dell'utente.
- Configurare, configurare, visualizzare e controllare qualsiasi dispositivo nel database di _configurazione_di HomeKit Home.
- Comunicare con qualsiasi dispositivo HomeKit preconfigurato e ordinarlo per eseguire singole azioni o lavorare in concerto, ad esempio accendendo tutte le luci della cucina.

Oltre a servire i dispositivi nel database di configurazione Home per le app abilitate per HomeKit, HomeKit fornisce l'accesso ai comandi vocali Siri. Considerata una configurazione di HomeKit opportunamente configurata, l'utente può emettere comandi vocali, ad esempio "Siri, accendere le luci nella sala da vivere".

<a name="Home-Configuration-Database" />

## <a name="the-home-configuration-database"></a>Database di configurazione Home

HomeKit organizza tutti i dispositivi di automazione in una determinata posizione in una raccolta Home. Questa raccolta fornisce agli utenti un modo per raggruppare i dispositivi di automazione principale in unità disposte logicamente con etichette significative leggibili.

La raccolta Home è archiviata in un database di configurazione Home che verrà automaticamente sottoposto a backup e sincronizzato in tutti i dispositivi iOS dell'utente. HomeKit fornisce le classi seguenti per l'utilizzo del database di configurazione Home:

- `HMHome`-Questo è il contenitore di livello superiore che include tutte le informazioni e le configurazioni per tutti i dispositivi di automazione domestica in un'unica posizione fisica, ad esempio una singola residenza familiare). L'utente potrebbe avere più di una residenza, ad esempio la casa principale e una casa di ferie. Oppure possono avere "case" diverse sulla stessa proprietà, ad esempio la casa principale e una casa del garage. In entrambi i casi, è `HMHome` _necessario_ che almeno un oggetto venga configurato e archiviato prima che sia possibile immettere altre informazioni HomeKit.
- `HMRoom`-Mentre facoltativo, un `HMRoom` consente all'utente di definire camere specifiche all'interno di un Home`HMHome`(), ad esempio: Kitchen, bathroom, garage o living room. L'utente può raggruppare tutti i dispositivi di automazione della casa in una posizione specifica all'interno di `HMRoom` un e agire su di essi come unità. Ad esempio, chiedere a Siri di spegnere le luci del garage.
- `HMAccessory`-Si tratta di un dispositivo di automazione singolo abilitato per HomeKit fisico che è stato installato nella residenza dell'utente (ad esempio un termostato intelligente). Ogni `HMAccessory` viene assegnato a un `HMRoom`oggetto. Se l'utente non ha configurato le chat, HomeKit assegna gli accessori a una stanza predefinita speciale.
- `HMService`: Rappresenta un servizio fornito da un oggetto `HMAccessory`specificato, ad esempio lo stato on/off di una luce o il colore (se la modifica del colore è supportata). Ogni `HMAccessory` può avere più di un servizio, ad esempio un apritore di sportello del garage che include anche una luce. Inoltre, un dato `HMAccessory` potrebbe avere servizi, ad esempio un aggiornamento del firmware, che non rientrano nel controllo utente.
- `HMZone`-Consente all'utente di raggruppare una raccolta `HMRoom` di oggetti in zone logiche, ad esempio un piano superiore, al di sotto o al seminterrato. Sebbene sia facoltativo, questo consente interazioni come chiedere a Siri di disattivare la luce al di fuori.

<a name="Provisioning-a-HomeKit-App" />

## <a name="provisioning-a-homekit-app"></a>Provisioning di un'app HomeKit

A causa dei requisiti di sicurezza imposti da HomeKit, un'app Novell. iOS che usa il Framework HomeKit deve essere configurata correttamente nel portale Apple Developer e nel file di progetto Novell. iOS.

Seguire questa procedura:

1. Accedere al [portale Apple Developer](https://developer.apple.com).
2. Fare clic su **certificati, identificatori & profili**.
3. Se non è già stato fatto, fare clic su **identificatori** e creare un ID per l'app (ad `com.company.appname`esempio), altrimenti modificare l'ID esistente.
4. Verificare che il servizio **HomeKit** sia stato controllato per l'ID specificato: 

    [![](homekit-images/provision01.png "Abilita il servizio HomeKit per l'ID specificato")](homekit-images/provision01.png#lightbox)
5. Salvare le modifiche.
6. Fare clic su **provisioning dei profili** > **sviluppo** e creare un nuovo profilo di provisioning di sviluppo per l'app: 

    [![](homekit-images/provision02.png "Creare un nuovo profilo di provisioning di sviluppo per l'app")](homekit-images/provision02.png#lightbox)
7. Scaricare e installare il nuovo profilo di provisioning o usare Xcode per scaricare e installare il profilo.
8. Modificare le opzioni del progetto Novell. iOS e assicurarsi di usare il profilo di provisioning appena creato: 

    [![](homekit-images/provision03.png "Selezionare il profilo di provisioning appena creato")](homekit-images/provision03.png#lightbox)
9. Modificare quindi il file **info. plist** e assicurarsi di usare l'ID app usato per creare il profilo di provisioning: 

    [![](homekit-images/provision04.png "Impostare l'ID app")](homekit-images/provision04.png#lightbox)
10. Infine, modificare il file con **estensione plist dei diritti** e verificare che sia stato selezionato il diritto **HomeKit** : 

    [![](homekit-images/provision05.png "Abilita diritti HomeKit")](homekit-images/provision05.png#lightbox)
11. Salvare le modifiche apportate a tutti i file.

Con queste impostazioni, l'applicazione è ora pronta per accedere alle API del Framework HomeKit. Per informazioni dettagliate sul provisioning, vedere le guide all'app per il provisioning e [il](~/ios/get-started/installation/device-provisioning/index.md) provisioning dei [dispositivi](~/ios/get-started/installation/device-provisioning/index.md) .

> [!IMPORTANT]
> Il test di un'app abilitata per HomeKit richiede un dispositivo iOS reale di cui è stato eseguito correttamente il provisioning per lo sviluppo. Non è possibile testare HomeKit dal simulatore iOS.

## <a name="the-homekit-accessory-simulator"></a>Simulatore di accessori HomeKit

Per fornire un modo per testare tutti i servizi e i dispositivi di automazione di casa possibili, senza dover avere un dispositivo fisico, Apple ha creato il _simulatore di accessori HomeKit_. Con questo simulatore è possibile configurare e configurare i dispositivi HomeKit virtuali.

### <a name="installing-the-simulator"></a>Installazione del simulatore

Apple fornisce il simulatore di accessori HomeKit come download separato da Xcode, quindi sarà necessario installarlo prima di continuare.

Seguire questa procedura:

1. In un Web browser visitare [download per sviluppatori Apple](https://developer.apple.com/download/more/?name=for%20Xcode)
2. Scaricare gli **strumenti aggiuntivi per Xcode xxx** (dove xxx è la versione di Xcode installata): 

    [![](homekit-images/simulator01.png "Scaricare gli strumenti aggiuntivi per Xcode")](homekit-images/simulator01.png#lightbox)
3. Aprire l'immagine del disco e installare gli strumenti nella directory delle **applicazioni** .

Con il simulatore di accessori HomeKit installato, è possibile creare accessori virtuali per il test.

### <a name="creating-virtual-accessories"></a>Creazione di accessori virtuali

Per avviare il simulatore di accessori HomeKit e creare alcuni accessori virtuali, seguire questa procedura:

1. Dalla cartella applicazioni, avviare il simulatore di accessori HomeKit: 

    [![](homekit-images/simulator02.png "Simulatore di accessori HomeKit")](homekit-images/simulator02.png#lightbox)
2. Fare clic **+** sul pulsante e selezionare **nuovo accessorio...** : 

    [![](homekit-images/simulator03.png "Aggiungere un nuovo accessorio")](homekit-images/simulator03.png#lightbox)
3. Inserire le informazioni sul nuovo accessorio e fare clic sul pulsante **fine** : 

    [![](homekit-images/simulator04.png "Compila le informazioni sul nuovo accessorio")](homekit-images/simulator04.png#lightbox)
4. Fare clic su **Aggiungi servizio.** e selezionare un tipo di servizio nell'elenco a discesa: 

    [![](homekit-images/simulator05.png "Selezionare un tipo di servizio nell'elenco a discesa")](homekit-images/simulator05.png#lightbox)
5. Specificare un **nome** per il servizio e fare clic sul pulsante **fine** : 

    [![](homekit-images/simulator06.png "Immettere un nome per il servizio")](homekit-images/simulator06.png#lightbox)
6. È possibile fornire caratteristiche facoltative per un servizio facendo clic sul pulsante **Aggiungi caratteristica** e configurando le impostazioni necessarie: 

    [![](homekit-images/simulator07.png "Configurazione delle impostazioni necessarie")](homekit-images/simulator07.png#lightbox)
7. Ripetere i passaggi precedenti per creare uno di ogni tipo di dispositivo di automazione domestica virtuale supportato da HomeKit.

Con alcuni accessori HomeKit virtuali di esempio creati e configurati, è ora possibile utilizzare e controllare questi dispositivi dall'app Novell. iOS.

## <a name="configuring-the-infoplist-file"></a>Configurazione del file INFO. plist

Novità per iOS 10 (e versioni successive), lo sviluppatore dovrà aggiungere la `NSHomeKitUsageDescription` chiave al `Info.plist` file dell'app e fornire una stringa che dichiara il motivo per cui l'app vuole accedere al database HomeKit dell'utente. Questa stringa verrà presentata all'utente la prima volta che esegue l'app:

[![](homekit-images/info01.png "Finestra di dialogo di autorizzazione HomeKit")](homekit-images/info01.png#lightbox)

Per impostare questa chiave, eseguire le operazioni seguenti:

1. Fare doppio clic sul `Info.plist` file nella **Esplora soluzioni** per aprirlo per la modifica.
2. Nella parte inferiore della schermata passare alla visualizzazione **origine** .
3. Consente di aggiungere una nuova **voce** all'elenco.
4. Nell'elenco a discesa selezionare **privacy-Descrizione utilizzo HomeKit**: 

    [![](homekit-images/info02.png "Selezionare Privacy-Descrizione utilizzo HomeKit")](homekit-images/info02.png#lightbox)
5. Immettere una descrizione per il motivo per cui l'app vuole accedere al database HomeKit dell'utente: 

    [![](homekit-images/info03.png "Immettere una descrizione")](homekit-images/info03.png#lightbox)
6. Salvare le modifiche apportate al file.

> [!IMPORTANT]
> Se non si imposta `NSHomeKitUsageDescription` la chiave `Info.plist` nel file, l'app non riuscirà in modo _invisibile all'utente_ (chiusa dal sistema in fase di esecuzione) senza errori quando viene eseguita in iOS 10 (o versione successiva).

## <a name="connecting-to-homekit"></a>Connessione a HomeKit

Per comunicare con HomeKit, l'app Novell. iOS deve prima creare un'istanza della `HMHomeManager` classe. Il responsabile principale è il punto di ingresso centrale in HomeKit ed è responsabile della fornitura di un elenco di Home page disponibili, dell'aggiornamento e della gestione dell'elenco e della restituzione della _Home Page principale_dell'utente.

L' `HMHome` oggetto contiene tutte le informazioni relative a una Home page, incluse eventuali chat, gruppi o zone che può contenere, insieme a tutti gli accessori di automazione domestica installati. Prima di poter eseguire qualsiasi operazione in HomeKit, è necessario crearne almeno uno `HMHome` e assegnarlo come Home Page principale.

L'app è responsabile di verificare se esiste una Home Page principale e di crearla e assegnarne una in caso contrario.

### <a name="adding-a-home-manager"></a>Aggiunta di un gestore Home

Per aggiungere HomeKit Awareness a un'app Novell. iOS, modificare il file **AppDelegate.cs** per modificarlo e renderlo simile al seguente:

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

Quando l'applicazione viene eseguita per la prima volta, viene chiesto all'utente se desidera consentire l'accesso alle informazioni di HomeKit:

[![](homekit-images/home01.png "All'utente verrà chiesto se desidera consentire l'accesso alle informazioni di HomeKit")](homekit-images/home01.png#lightbox)

Se l'utente risponde a **OK**, l'applicazione sarà in grado di usare i loro accessori HomeKit in caso contrario, non sarà possibile e le chiamate a HomeKit avranno esito negativo con un errore.

Con il responsabile della Home page, successivamente l'applicazione dovrà verificare se è stata configurata una Home Page principale e, in caso contrario, fornire un modo all'utente per crearla e assegnarne una.

### <a name="accessing-the-primary-home"></a>Accesso alla Home Page principale

Come indicato in precedenza, è necessario creare e configurare una Home page primaria prima che HomeKit sia disponibile ed è responsabilità dell'app fornire un modo per consentire all'utente di creare e assegnare una casa primaria se non ne esiste già una.

Quando l'app viene avviata o restituita per la prima volta dallo sfondo, `DidUpdateHomes` deve monitorare l' `HMHomeManager` evento della classe per verificare l'esistenza di una Home Page principale. Se non esiste, deve fornire un'interfaccia per consentire all'utente di crearne uno.

Il codice seguente può essere aggiunto a un controller di visualizzazione per verificare la Home Page principale:

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

Quando il gestore Home esegue una connessione a HomeKit, l' `DidUpdateHomes` evento viene generato, le eventuali case esistenti verranno caricate nella raccolta di case del Manager e la Home Page principale sarà caricata, se disponibile.

### <a name="adding-a-primary-home"></a>Aggiunta di un Home Page principale

Se la `PrimaryHome` `HMHomeManager` proprietà di è `null` successiva a un `DidUpdateHomes` evento, è necessario fornire un modo per consentire all'utente di creare e assegnare una Home page primaria prima di continuare.

In genere l'app presenta un modulo che consente all'utente di assegnare un nome a una nuova Home page che viene quindi passata al gestore Home per la configurazione come Home Page principale. Per l'app di esempio **HomeKitIntro** , una visualizzazione modale è stata creata in iOS designer e chiamata da `AddHomeSegue` segue dall'interfaccia principale dell'app.

Fornisce un campo di testo in cui l'utente deve immettere un nome per la nuova Home page e un pulsante per aggiungere la Home page. Quando l'utente tocca il pulsante **Aggiungi Home** , il codice seguente chiama il gestore Home per aggiungere la Home page:

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

Il `AddHome` metodo tenterà di creare una nuova Home page e di restituirla alla routine di callback specificata. Se la `error` proprietà non `null`è, si è verificato un errore che deve essere presentato all'utente. Gli errori più comuni sono causati da un nome Home non univoco o dal gestore Home che non è in grado di comunicare con HomeKit.

Se la Home page è stata creata correttamente, è necessario chiamare `UpdatePrimaryHome` il metodo per impostare la nuova Home page come Home Page principale. Anche in questo caso `error` , se la `null`proprietà non è presente, si è verificato un errore che dovrebbe essere presentato all'utente.

È inoltre consigliabile monitorare gli eventi `DidAddHome` e `DidRemoveHome` del gestore Home e aggiornare l'interfaccia utente dell'app in modo obbligatorio.

> [!IMPORTANT]
> Il `AlertView.PresentOKAlert` metodo usato nel codice di esempio precedente è una classe helper nell'applicazione HomeKitIntro che semplifica l'uso degli avvisi iOS.


## <a name="finding-new-accessories"></a>Ricerca di nuovi accessori

Una volta che una Home Page principale è stata definita o caricata da Home Manager, l'app Novell. iOS `HMAccessoryBrowser` può chiamare il per trovare eventuali nuovi accessori di automazione della casa e aggiungerli a una Home page.

Chiamare il `StartSearchingForNewAccessories` metodo per iniziare a cercare nuovi accessori e il `StopSearchingForNewAccessories` metodo al termine dell'operazione.

> [!IMPORTANT]
> `StartSearchingForNewAccessories`non deve essere lasciato in esecuzione per lunghi periodi di tempo, perché influirà negativamente sulla durata della batteria e sulle prestazioni del dispositivo iOS. Apple suggerisce la `StopSearchingForNewAccessories` chiamata dopo un minuto o la ricerca solo quando l'interfaccia utente trova accessorio viene presentata all'utente.

L' `DidFindNewAccessory` evento verrà chiamato quando vengono individuati nuovi accessori e verranno aggiunti `DiscoveredAccessories` all'elenco nel browser degli accessori.

L' `DiscoveredAccessories` elenco conterrà una raccolta di `HMAccessory` oggetti che definiscono un dispositivo di automazione domestica abilitato per HomeKit e i relativi servizi disponibili, ad esempio luci o controllo sportello del garage.

Una volta trovato, il nuovo accessorio dovrebbe essere visualizzato all'utente e quindi può selezionarlo e aggiungerlo a una Home page. Esempio:

[![](homekit-images/accessory01.png "Ricerca di un nuovo accessorio")](homekit-images/accessory01.png#lightbox)

Chiamare il `AddAccessory` metodo per aggiungere l'accessorio selezionato alla raccolta della Home. Ad esempio:

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

Se la `err` proprietà non `null`è, si è verificato un errore che deve essere presentato all'utente. In caso contrario, all'utente verrà richiesto di immettere il codice di installazione del dispositivo da aggiungere:

[![](homekit-images/accessory02.png "Immettere il codice di installazione del dispositivo da aggiungere")](homekit-images/accessory02.png#lightbox)

In HomeKit accessorio simulatore questo numero è disponibile nel campo del **codice di installazione** :

[![](homekit-images/accessory03.png "Il campo del codice di installazione nel simulatore degli accessori HomeKit")](homekit-images/accessory03.png#lightbox)

Per gli accessori HomeKit reali, il codice di installazione verrà stampato su un'etichetta nel dispositivo stesso, nella casella del prodotto o nel manuale dell'utente dell'accessorio.

È necessario monitorare l' `DidRemoveNewAccessory` evento del browser degli accessori e aggiornare l'interfaccia utente per rimuovere un accessorio dall'elenco disponibile una volta che l'utente lo ha aggiunto alla propria raccolta Home.

## <a name="working-with-accessories"></a>Uso degli accessori

Una volta che è stata stabilita una casa primaria e sono stati aggiunti accessori, è possibile presentare un elenco di accessori (e facoltativamente camere) per l'utilizzo da parte dell'utente.

L' `HMRoom` oggetto contiene tutte le informazioni relative a una determinata stanza e a tutti gli accessori che vi appartengono. Facoltativamente, le chat possono essere organizzate in una o più zone. Un `HMZone` oggetto contiene tutte le informazioni relative a una determinata zona e a tutte le chat che vi appartengono.

Ai fini di questo esempio, si procederà con semplicità e si funzionerà direttamente con gli accessori di casa, anziché organizzarli in chat room o zone.

L' `HMHome` oggetto contiene un elenco di accessori assegnati che possono essere presentati all'utente nella relativa `Accessories` proprietà. Ad esempio:

[![](homekit-images/accessory04.png "Accessorio di esempio")](homekit-images/accessory04.png#lightbox)

Qui, l'utente può selezionare un determinato accessorio e usare i servizi che fornisce.

## <a name="working-with-services"></a>Uso dei servizi

Quando l'utente interagisce con un dispositivo di automazione domestica abilitato per HomeKit, in genere è attraverso i servizi che fornisce. La `Services` proprietà `HMService` della classe contiene una raccolta di oggetti che definiscono i servizi offerti da un dispositivo. `HMAccessory`

I servizi sono elementi quali luci, termostati, Open Door del garage, commutatori o blocchi. Alcuni dispositivi (ad esempio, uno sportello del garage) forniranno più di un servizio, ad esempio una luce e la possibilità di aprire o chiudere uno sportello.

Oltre ai servizi specifici forniti da un determinato accessorio, ciascun accessorio contiene un oggetto `Information Service` che definisce proprietà quali il nome, il produttore, il modello e il numero di serie.

### <a name="accessory-service-types"></a>Tipi di servizio accessori

I tipi di servizio seguenti sono disponibili tramite `HMServiceType` enum:

- **AccessoryInformation** : fornisce informazioni sul dispositivo di automazione domestica specificato (accessorio).
- **AirQualitySensor** : definisce un sensore di qualità aria.
- **Batteria** : definisce lo stato della batteria di un accessorio.
- **CarbonDioxideSensor** : definisce un sensore di anidride carbonica.
- **CarbonMonoxideSensor** : definisce un sensore di monossido di carbonio.
- **ContactSensor** : definisce un sensore di contatto, ad esempio l'apertura o la chiusura di una finestra.
- **Sportello** : definisce un sensore di stato della porta, ad esempio aperto o chiuso.
- **Ventola** : definisce una ventola controllata in remoto.
- **GarageDoorOpener** : definisce uno sportello del garage.
- **HumiditySensor** : definisce un sensore di umidità.
- **LeakSensor** : definisce un sensore di perdita (ad esempio per un riscaldamento o un computer di lavaggio).
- **Lampadina** : definisce una luce autonoma o una luce che fa parte di un altro accessorio (ad esempio un apritore di sportello del garage).
- **LightSensor** : definisce un sensore di luminosità.
- **LockManagement** : definisce un servizio che gestisce un blocco sportello automatico.
- **LockMechanism** : definisce un blocco controllato in remoto, ad esempio un blocco sportello.
- **MotionSensor** : definisce un sensore di movimento.
- **OccupancySensor** : definisce un sensore di occupazione.
- **Outlet** : definisce una presa a muro controllata dal computer remoto.
- **Securiza** : definisce un sistema di sicurezza della Home page.
- **StatefulProgrammableSwitch** : definisce un comportatore programmabile che rimane in uno stato di give una volta attivato (ad esempio, un Commuter).
- **StatelessProgrammableSwitch** : definisce un'opzione programmabile che torna allo stato iniziale dopo l'attivazione, ad esempio un pulsante di comando.
- **SmokeSensor** : definisce un sensore di fumo.
- **Switch** : definisce un'opzione di attivazione/disattivazione, ad esempio un comcambio di parete standard.
- **Sensore** : definisce un sensore di temperatura.
- **Termostato** : definisce un termostato intelligente usato per controllare un sistema HVAC.
- **Finestra** : definisce una finestra automatica che deve essere aperta o chiusa in modalità remota.
- **WindowCovering** : definisce una copertura della finestra controllata in remoto, ad esempio i bui che possono essere aperti o chiusi.

### <a name="displaying-service-information"></a>Visualizzazione delle informazioni sul servizio

Una volta caricato, `HMAccessory` è possibile eseguire una query `HNService` sui singoli oggetti forniti e visualizzare le informazioni all'utente:

[![](homekit-images/accessory05.png "Visualizzazione delle informazioni sul servizio")](homekit-images/accessory05.png#lightbox)

È sempre necessario controllare la `Reachable` proprietà di un oggetto `HMAccessory` prima di provare a utilizzarlo. Un accessorio può essere irraggiungibile perché l'utente non è compreso nell'intervallo del dispositivo o se è stato scollegato.

Una volta selezionato un servizio, l'utente può visualizzare o modificare una o più caratteristiche del servizio per monitorare o controllare un determinato dispositivo di automazione domestica.

<a name="Working-with-Characteristics" />

## <a name="working-with-characteristics"></a>Utilizzo delle caratteristiche

Ogni `HMService` oggetto può contenere una raccolta di `HMCharacteristic` oggetti che possono fornire informazioni sullo stato del servizio, ad esempio una porta aperta o chiusa, oppure consentire all'utente di modificare uno stato, ad esempio impostando il colore di una luce.

`HMCharacteristic`non solo fornisce informazioni su una caratteristica e il relativo stato, ma fornisce anche metodi per l'utilizzo dello stato tramite _metadati caratteristici_ (`HMCharacteristisMetadata`). Questi metadati possono fornire proprietà, ad esempio intervalli di valori minimo e massimo, utili per la visualizzazione di informazioni all'utente o per consentire la modifica degli Stati.

L' `HMCharacteristicType` enumerazione fornisce un set di valori di metadati caratteristici che possono essere definiti o modificati nel modo seguente:

- AdminOnlyAccess
- AirParticulateDensity
- AirParticulateSize
- Qualità dell'it
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
- SerialNumber
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

### <a name="working-with-a-characteristics-value"></a>Uso del valore di una caratteristica

Per assicurarsi che l'app abbia lo stato più recente di una determinata caratteristica, chiamare `ReadValue` il metodo `HMCharacteristic` della classe. Se la `err` proprietà non `null`è, si è verificato un errore che può essere visualizzato o meno all'utente.

La `Value` proprietà della caratteristica contiene lo stato corrente della caratteristica specificata `NSObject`come e, di conseguenza, non può essere usato direttamente in. C#

Per leggere il valore, è stata aggiunta la seguente classe helper all'applicazione di esempio **HomeKitIntro** :

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

`NSObjectConverter` Viene usato ogni volta che l'applicazione deve leggere lo stato corrente di una caratteristica. Di seguito è riportato un esempio:

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

La riga precedente converte il valore in un `float` oggetto che può essere usato nel codice Novell C# .

Per modificare un `HMCharacteristic`oggetto, chiamare `WriteValue` il relativo metodo ed eseguire il wrapping del `NSObject.FromObject` nuovo valore in una chiamata. Di seguito è riportato un esempio:

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

Se la `err` proprietà non `null`è, si è verificato un errore che deve essere presentato all'utente.

### <a name="testing-characteristic-value-changes"></a>Test delle modifiche del valore caratteristico

Quando si lavora `HMCharacteristics` con gli accessori simulati, è possibile `Value` monitorare le modifiche apportate alla proprietà all'interno del simulatore di accessori HomeKit.

Con l'app **HomeKitIntro** in esecuzione su hardware del dispositivo iOS reale, le modifiche apportate al valore di una caratteristica dovrebbero essere visualizzate quasi immediatamente nel simulatore di accessori HomeKit. Ad esempio, modificando lo stato di una luce nell'app iOS:

[![](homekit-images/test01.png "Modifica dello stato di una luce in un'app iOS")](homekit-images/test01.png#lightbox)

Deve modificare lo stato della luce nel simulatore di accessori HomeKit. Se il valore non viene modificato, controllare lo stato del messaggio di errore quando si scrivono nuovi valori caratteristici e assicurarsi che l'accessorio sia ancora raggiungibile.

## <a name="advanced-homekit-features"></a>Funzionalità avanzate di HomeKit

Questo articolo ha trattato le funzionalità di base necessarie per l'uso di accessori HomeKit in un'app Novell. iOS. Esistono tuttavia diverse funzionalità avanzate di HomeKit che non sono descritte in questa introduzione:

- **Rooms** : gli accessori abilitati per HomeKit possono essere organizzati facoltativamente in chat room dall'utente finale. Questo consente a HomeKit di presentare gli accessori in modo da semplificare la comprensione e l'utilizzo da parte dell'utente. Per altre informazioni sulla creazione e sulla gestione delle chat, vedere la documentazione di Apple [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom) .
- **Zones** : le camere possono essere organizzate facoltativamente in zone dall'utente finale. Una zona fa riferimento a una raccolta di chat che l'utente può considerare come una singola unità. Ad esempio:  Al piano superiore, in basso o in seminterrato. Anche in questo caso, questo consente a HomeKit di presentare e usare gli accessori in modo significativo per l'utente finale. Per altre informazioni sulla creazione e la gestione delle zone, vedere la documentazione di Apple [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone) .
- **Azioni e set** di azioni: le azioni modificano le caratteristiche del servizio accessorio e possono essere raggruppate in set. I set di azioni fungono da script per controllare un gruppo di accessori e coordinare le azioni. Uno script "Watch TV", ad esempio, potrebbe chiudere i bui, abbassare le luci e accendere la televisione e il suo sistema audio. Per altre informazioni sulla creazione e sulla gestione di azioni e set di azioni, vedere la documentazione di Apple [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction) e [HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet) .
- **Trigger** : un trigger consente di attivare uno o più set di azioni quando un determinato set di condizioni è stato soddisfatto. Ad esempio, accendere la luce portch e bloccare tutte le porte esterne quando si trova all'esterno. Per altre informazioni sulla creazione e sulla gestione dei trigger, vedere la documentazione di Apple [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger) .

Poiché queste funzionalità usano le stesse tecniche illustrate in precedenza, dovrebbero essere facili da implementare seguendo la [Guida HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)di Apple, le [linee guida dell'interfaccia utente di HomeKit](https://developer.apple.com/homekit/ui-guidelines/) e il [riferimento a HomeKit Framework](https://developer.apple.com/library/ios/home_kit_framework_ref).

## <a name="homekit-app-review-guidelines"></a>Linee guida per la revisione dell'app HomeKit

Prima di inviare un'app Novell. iOS abilitata per HomeKit a iTunes Connect per la versione in iTunes App Store, assicurarsi di seguire le linee guida di Apple per le app abilitate per HomeKit:

- Lo scopo principale dell'app _deve_ essere l'automazione domestica se si usa il Framework HomeKit.
- Il testo di marketing dell'app deve notificare agli utenti che HomeKit è in uso e che devono fornire un'informativa sulla privacy.
- La raccolta di informazioni sugli utenti o l'utilizzo di HomeKit per la pubblicità è strettamente proibita.

Per le linee guida di revisione completa, vedere le [linee guida per la revisione dell'app Store](https://developer.apple.com/app-store/review/guidelines/)di Apple.

## <a name="whats-new-in-ios-9"></a>Novità di iOS 9

Apple ha apportato le modifiche e le aggiunte seguenti a HomeKit per iOS 9:

- Gestione **degli oggetti esistenti** : se viene modificato un accessorio esistente, il gestore principale`HMHomeManager`() informa l'utente dell'elemento specifico che è stato modificato.
- **Identificatori permanenti** : tutte le classi HomeKit pertinenti ora `UniqueIdentifier` includono una proprietà per identificare in modo univoco un determinato elemento tra le app abilitate per HomeKit (o le istanze della stessa app).
- **Gestione utenti** : è stato aggiunto un controller di visualizzazione incorporato per fornire la gestione degli utenti sugli utenti che hanno accesso ai dispositivi HomeKit nella Home page dell'utente primario.
- **Funzionalità utente** : gli utenti di HomeKit dispongono ora di un set di privilegi che controllano le funzioni che è possibile usare negli accessori abilitati per HomeKit e HomeKit. L'app deve visualizzare solo le funzionalità rilevanti per l'utente corrente. Ad esempio, solo gli amministratori devono essere in grado di gestire altri utenti.
- **Scene predefinite** : sono state create scene predefinite per quattro eventi comuni che si verificano per l'utente HomeKit medio: Get up, Leave, return, go to Bed. Queste scene predefinite non possono essere eliminate da un Home.
- **Scenes e Siri** -Siri hanno un supporto più approfondito per le scene in iOS 9 e possono riconoscere il nome di qualsiasi scena definita in HomeKit. Un utente può eseguire una scena semplicemente pronunciando il proprio nome in Siri.
- **Categorie** di accessori: un set di categorie predefinite è stato aggiunto a tutti gli accessori e consente di identificare il tipo di accessorio aggiunto a una casa o a cui si è lavorato dall'interno dell'app. Queste nuove categorie sono disponibili durante la configurazione dell'accessorio.
- **Apple Watch support** -HomeKit è ora disponibile per watchos e il Apple Watch sarà in grado di controllare i dispositivi abilitati per HomeKit senza un iPhone vicino al Watch. HomeKit per watchos supporta le funzionalità seguenti: Visualizzazione delle abitazioni, controllo degli accessori e esecuzione di scene.
- **Nuovo tipo di trigger di evento** : oltre ai trigger del tipo di timer supportati in iOS 8, iOS 9 supporta ora i trigger di evento in base allo stato dell'accessorio (ad esempio, i dati del sensore) o la georilevazione. I trigger di `NSPredicates` evento usano per impostare le condizioni per la loro esecuzione.
- **Accesso remoto** : con accesso remoto, l'utente è ora in grado di controllare i propri accessori di HomeKit abilitati per l'automazione domestica quando sono lontani dalla casa in una posizione remota. In iOS 8 Questa era supportata solo se l'utente avesse un Apple TV di terze generazione nella Home page. In iOS 9 questa limitazione è stata revocata e l'accesso remoto è supportato tramite iCloud e il protocollo HomeKit accessorio (HAP).
- **Nuove capacità Bluetooth Low Energy (BLE)** -HomeKit supporta ora più tipi di accessori che possono comunicare tramite il protocollo BLE (Bluetooth Low Energy). Con il tunneling sicuro di HAP, un accessorio HomeKit può esporre un altro accessorio Bluetooth su Wi-Fi (se non è compreso nell'intervallo di Bluetooth). In iOS 9 gli accessori BLE hanno un supporto completo per le notifiche e i metadati.
- **Nuove categorie di accessori** -Apple ha aggiunto le nuove categorie di accessori seguenti in iOS 9: Rivestimenti di finestre, porte e finestre motorizzate, sistemi di allarme, sensori e commutatori programmabili.

Per altre informazioni sulle nuove funzionalità di HomeKit in iOS 9, vedere l' [Indice HomeKit](https://developer.apple.com/homekit/) di Apple e novità del video di [HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210) .

## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto il Framework di automazione HomeKit Home di Apple. È stato illustrato come configurare e configurare i dispositivi di test usando il simulatore di accessori HomeKit e come creare una semplice app Novell. iOS per individuare, comunicare e controllare i dispositivi di automazione domestica usando HomeKit.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 per sviluppatori](https://developer.apple.com/ios/pre-release/)
- [Novità di iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida a HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Linee guida dell'interfaccia utente di HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Guida di riferimento a HomeKit Framework](https://developer.apple.com/library/ios/home_kit_framework_ref)
