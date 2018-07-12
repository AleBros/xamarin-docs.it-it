---
title: Ricaricamento in tempo reale
description: Verificare che le modifiche per il XAML riportato in tempo reale, senza richiedere un'altra compilazione e distribuzione.
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
ms.date: 05/11/2018
ms.openlocfilehash: 12b677c8cc4a709a865d2eaee3ea44a6babf1b05
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38860667"
---
# <a name="xamarin-live-reload"></a>Xamarin Live Reload

![Anteprima](~/media/shared/preview.png)

Xamarin Live Reload consente **apportare modifiche a di XAML e visualizzarli in tempo reale, senza richiedere un'altra compilazione e distribuzione**. Eventuali modifiche apportate al XAML verranno ridistribuiti nella Salva e riportate nella destinazione di distribuzione.

Dal momento che l'app viene compilato quando si usa Live Reload, funziona con tutte le librerie e i controlli di terze parti. Live works Ricarica su tutte le piattaforme xamarin. Forms supporta, tra cui Android, iOS e UWP e funziona su tutte le destinazioni di distribuzione valido, inclusi simulatori, emulatori e dispositivi fisici.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

Livereload è attualmente disponibile solo in Visual Studio 2017.

## <a name="requirements"></a>Requisiti

* [Visual Studio 2017 versione 15.7 o successiva](https://visualstudio.microsoft.com/vs/) o versione successiva con il **sviluppo per dispositivi mobili con .NET** carico di lavoro.
* [Xamarin. Forms 3.0.0 o successiva](https://www.nuget.org/packages/Xamarin.Forms/) o versione successiva.

## <a name="getting-started"></a>Introduzione
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1. Installare Xamarin Live Reload da Visual Studio Marketplace

Xamarin Live Reload viene distribuito tramite Visual Studio Marketplace. Per installare l'estensione, visitare il [Xamarin Live Ricarica pagina in Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload) sito Web e fare clic su **scaricare**.

Aprire l'estensione VSIX che viene scaricato e fare clic su **installare**.

![Conferma di Xamarin Live Reload installazione di Visual Studio](images/LiveReloadVSIXInstall.png)

In alternativa, è possibile cercarla nel **Online** scheda le **estensioni e aggiornamenti** dialogo all'interno di Visual Studio.

### <a name="2-configure-your-app-to-use-live-reload"></a>2. Configurare l'app per usare Live Reload

Aggiunta di ricaricamento in tempo reale per le App per dispositivi mobili esistente può essere eseguita in tre passaggi:

1. Verificare che tutti i progetti vengono aggiornati per usare [xamarin. Forms 3.0.0 o successiva](https://www.nuget.org/packages/Xamarin.Forms/) o versione successiva.

2. Aggiungere il **Xamarin.LiveReload** pacchetto NuGet:

    a. **.NET standard** – installare il **Xamarin.LiveReload** NuGet nella libreria .NET Standard 2.0. Non si dovrà essere installato nei progetti di piattaforma. Verificare che il **origine pacchetto** è impostata su **tutte**.
    
    b. **Progetti condivisi** – installare il **Xamarin.LiveReload** NuGet tutti i progetti di piattaforma (ad esempio Android, iOS, piattaforma UWP, ecc.). Verificare che il **origine pacchetto** è impostata su **tutte**.

    [![Aggiungi NuGet di Xamarin Live Reload con Gestione pacchetti NuGet](images/addlivereloadnuget.w157-sml.png)](images/addlivereloadnuget.w157.png#lightbox)

3. Aggiungere `LiveReload.Init();` al costruttore nel `Application` classe, come illustrato nel frammento di codice seguente:

```csharp
public partial class App : Application
{
    public App ()
    {
        // Initialize Live Reload.
        #if DEBUG
        LiveReload.Init();
        #endif
        
        InitializeComponent();
        MainPage = new MainPage();
    }
}
```

### <a name="3-start-live-reloading"></a>3. Avviare il ricaricamento in tempo reale

Compilare e distribuire l'applicazione. Dopo aver distribuito l'app, aprire un file XAML, apportare alcune modifiche e salvare il file. Le modifiche sono ridistribuite alla destinazione di distribuzione.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

Live works Ricarica con le modifiche apportate a qualsiasi file XAML. Le modifiche al codice c# o aggiungendo o rimuovendo i pacchetti NuGet richiede una nuova compilazione e distribuzione per rendere effettive.

## <a name="frequently-asked-questions"></a>Domande frequenti 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>È disponibile in Visual Studio per Mac Xamarin Live Reload? 

La versione di anteprima iniziale di Xamarin Live Reload è disponibile solo per Visual Studio 2017. Supporto per Visual Studio per Mac è previsto per una versione futura.

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>Ciò funziona con tutte le librerie, ad esempio Prism? 

Poiché è stata compilata, Live Reload funziona con tutte le librerie, ad esempio Prism e librerie di controlli di terze parti, ad esempio Telerik, Infragistics, Syncfusion, ArcGIS, GrapeCity e altri fornitori di controlli.

### <a name="what-changes-does-live-reload-redeploy"></a>Le modifiche ridistribuisce ricaricamento in tempo reale? 

Livereload si applica solo le modifiche apportate a XAML o CSS. Se si apportano modifiche a un file c#, sarà necessaria una ricompilazione. Supporto per il ricaricamento in c# è previsto per una versione futura.

### <a name="what-platforms-are-supported"></a>Quali piattaforme sono supportate? 

Livereload funziona su qualsiasi piattaforma supportata da xamarin. Forms, tra cui Android, iOS e UWP.

### <a name="does-this-work-on-emulators-simulators-and-physical-devices"></a>Ciò funziona su emulatori, simulatori e dispositivi fisici? 

Sì, Live Reload funziona con tutte le destinazioni di distribuzione valido, inclusi gli emulatori Android, simulatori iOS e dispositivi fisici. Distribuzione in un dispositivo richiede che il dispositivo e il computer siano nella stessa rete Wi-Fi.

### <a name="does-this-work-with-corporate-networks"></a>Ciò funziona con reti aziendali?

Se si esegue il debug di un emulatore Android o un simulatore di iOS, Live Reload Usa localhost per comunicare. Se si desidera distribuire in un dispositivo, il dispositivo e il computer dovrà essere nella stessa rete Wi-Fi. Negli scenari in cui questo non è possibile, è possibile [configurare il server di Live Reload](#live-reload-server), consentendo al ricaricamento in tempo reale, indipendentemente dalle impostazioni di connettività di rete.

### <a name="does-it-require-debugging-the-app"></a>È necessario debug dell'app? 

No. In effetti, è possibile anche avviare tutte le destinazioni di applicazioni supportate (Android, iOS e UWP) su un numero qualsiasi di dispositivi o emulatori o simulatori e visualizzarli tutti aggiornare contemporaneamente. 

## <a name="limitations"></a>Limitazioni

* È supportato solo il ricaricamento di XAML.
* Stato dell'interfaccia utente potrebbe non essere mantenuto tra distribuzioni, a meno che non utilizzando MVVM.

## <a name="known-issues"></a>Problemi noti

* Supportato solo in Visual Studio.
* Il collegamento deve essere impostato su **non collegare** o **solo collegamento Framework SDK** 
* Ricaricamento delle risorse a livello di app (vale a dire **app** o i dizionari risorse condivisi), spostamento di app viene reimpostato. Questo problema verrà risolto nella prossima versione di anteprima.
* Modificando XAML durante il debug di UWP può causare un arresto anomalo del sistema di runtime. Soluzione alternativa: Utilizzare **Avvia senza eseguire debug (CTRL+F5)** invece di **Avvia debug (F5)**.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="error-codes"></a>Codici di errore

* **XLR001**: *il progetto corrente fa riferimento a versione del pacchetto NuGet 'Xamarin.LiveReload' '[versione]', ma l'estensione Xamarin Live Reload richiede la versione '[versione]'.*

  Per consentire un'iterazione rapida e l'evoluzione della funzionalità di Live Reload, il pacchetto nuget e l'estensione di Visual Studio deve corrispondere esattamente. Aggiornare il pacchetto nuget per la stessa versione dell'estensione installata.

* **XLR002**: *ricaricamento in tempo reale richiede almeno la proprietà 'MqttHostname' durante la compilazione dalla riga di comando. In alternativa, impostare 'EnableLiveReload' su 'false' per disabilitare la funzionalità.*

  Le proprietà obbligatorie per Live Reload non sono disponibili se la compilazione dalla riga di comando (o in integrazione continua) e pertanto deve essere fornito in modo esplicito. 

* **XLR003**: *pacchetto nuget di ricaricamento in tempo reale è necessario installare l'estensione Xamarin Live Reload Visual Studio.*

  Tentativo di creare un progetto che fa riferimento il pacchetto nuget di ricaricamento in tempo reale, ma non è installata l'estensione Visual.  

* *Eccezione durante il caricamento degli assembly: System.IO.FileNotFoundException: Impossibile caricare l'assembly ' Xamarin.Live.Reload, versione = 0.3.27.0, Culture = neutral, PublicKeyToken ='.*

  Il progetto host deve usare `PackageReference` invece di `packages.config`

### <a name="app-doesnt-connect"></a>App non può connettersi

Quando viene compilata l'applicazione, le informazioni dal **strumenti > Opzioni > Xamarin > Live Reload** (host nome, porta e la crittografia delle chiavi) sono incorporate nell'app, in modo che quando `LiveReload.Init()` viene eseguito, nessuna associazione o la configurazione è necessario per la connessione abbia esito positivo.

Diverso da problemi di rete normali (firewall, al dispositivo in una rete diversa), il motivo principale per che l'app non può connettersi correttamente IDE è perché la relativa configurazione è diverso da quello in Visual Studio. Questa situazione può verificarsi se:

* App è stata compilata in un computer diverso.
* App è stata compilata e distribuita in un'altra sessione di Visual Studio, e **generare automaticamente le chiavi di crittografia** è selezionata (impostazione predefinita) **strumenti > Opzioni > Xamarin > Live Reload**.
* Impostazioni di Visual Studio sono state modificate (ad esempio le chiavi di nome host, porta o la crittografia), ma l'app non è stata creata e distribuita nuovamente.

Tutti questi casi vengono valutati tramite la creazione e la distribuzione dell'app nuovamente.

### <a name="uninstalling-preview-1"></a>Disinstallazione Preview 1

Se si dispone di un'anteprima precedente e si riscontrano disinstallarlo, seguire questa procedura:

1. Eliminare la cartella **C:\Program Files (x86) \Microsoft Visual Studio\Preview\Enterprise\Common7\IDE\Extensions\Xamarin\LiveReload** (Nota: sostituire "Enterprise" con l'edizione installata e "Anteprima" con "2017" Se si installato in un Visual Studio stabile)
2. Aprire una **prompt dei comandi sviluppatore** per Visual Studio e l'esecuzione `devenv /updateconfiguration`. 

## <a name="tips--tricks"></a>Suggerimenti e consigli

* Fino a quando non modificare le impostazioni di Live Reload (tra cui le chiavi di crittografia, ad esempio se si sceglie di disattivare **generare automaticamente le chiavi di crittografia**) e si compila dallo stesso computer, non è necessario compilare e distribuire l'app dopo l'iniziale distribuire, a meno che non si modifica il codice o dipendenze. È possibile avviare nuovamente un'app distribuita in precedenza e si connetta all'ultimo host utilizzato.

* Non sussiste alcuna limitazione sul numero di dispositivi è possibile connettersi alla stessa sessione di Visual Studio. È possibile distribuire e avviare l'app in tutti i dispositivi/simulatori in base alle esigenze per visualizzare il funzionamento di ricaricamento in tempo reale su tutti gli elementi nello stesso momento.

* Livereload ricaricherà solo la parte dell'interfaccia utente dell'app, ma provoca grossi *non* ricreare le pagine, nessuno dei due sostituire il modello di visualizzazione (o contesto di associazione). Ciò significa che il *intera* lo stato dell'app viene sempre mantenuto tra ricaricamenti, incluse le dipendenze inserite.

## <a name="live-reload-server"></a>Livereload Server

Negli scenari in cui una connessione da app in esecuzione nel computer (come indicato utilizzando `localhost` o `127.0.0.1` nelle **strumenti > Opzioni > Xamarin > Live Reload**) non è possibile (ad esempio i firewall, reti diverse), è possibile configurare un server remoto, invece che l'IDE e l'app verrà /Impossibile connettersi alla.

Livereload Usa lo standard [protocollo MQTT](http://mqtt.org/) per lo scambio di messaggi e possono pertanto comunicare [i server di terze parti](https://github.com/mqtt/mqtt.github.io/wiki/servers). Esistono anche [server pubblici](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers) (noto anche come *Broker*) disponibili che è possibile usare. Ricaricamento in tempo reale è stato testato con `broker.hivemq.com` e `iot.eclipse.org` nomi host, nonché i servizi forniti dal [www.cloudmqtt.com](https://www.cloudmqtt.com) e [www.cloudamqp.com](https://www.cloudamqp.com). È anche possibile distribuire il proprio server MQTT nel cloud, ad esempio [HiveMQ in Azure](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud).

È possibile configurare qualsiasi porta, ma accade spesso di usare la porta 1883 predefinita per i server remoti. I messaggi di ricaricamento in tempo reale usano end-to-end AES simmetrica crittografia avanzata, pertanto è opportuno per connettersi ai server remoti. Per impostazione predefinita, la chiave di crittografia e il vettore di inizializzazione (IV) vengono rigenerate in ogni sessione di Visual Studio.

Probabilmente il modo più semplice consiste nell'installare il [mosquitto](https://mosquitto.org) server in una VM Ubuntu vuoto in Azure:

1. Creare una nuova macchina virtuale di Ubuntu Server nel portale di Azure
2. Aggiungere una nuova regola porta in ingresso per 1883 (la porta predefinita MQTT) nella scheda di rete
3. Aprire il [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) (modalità bash)
4. Digitare `ssh [USERNAME]@[PUBLIC_IP]` utilizzando il nome utente scelto in 1) e l'indirizzo IP pubblico visualizzato nella pagina Panoramica della macchina virtuale
5. Eseguire `sudo apt-get install mosquitto`, immettere la password scelta nel 1)

A questo punto è possibile utilizzare tale IP per connettersi al proprio server MQTT.
