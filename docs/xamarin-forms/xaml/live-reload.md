---
title: Ricaricamento in tempo reale
description: Vedere le modifiche al codice XAML applicate in tempo reale, senza richiedere un'altra compilazione e distribuzione.
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
ms.date: 04/23/2018
ms.openlocfilehash: bfb53af420b64fb9af994d3fb19293406d3acd7b
ms.sourcegitcommit: 180a8411d912de40545f9624e2127a66ee89e7b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2018
---
# <a name="xamarin-live-reload"></a>Ricaricamento in tempo reale di Xamarin

![Anteprima](~/media/shared/preview.png)

Ricarica Live Xamarin consente **apportare modifiche al codice XAML e visualizzarli in tempo reale, senza richiedere un'altra compilazione e distribuzione**. Le modifiche apportate al codice XAML verranno ridistribuite salvare e riflesse nella destinazione di distribuzione.

Poiché l'app viene compilato quando si usa Live ricaricamento, funziona con tutte le raccolte e i controlli di terze parti. In tempo reale il funzionamento di ricarica su tutte le piattaforme supporta, tra cui Android, iOS e UWP, xamarin. Forms e funziona in tutte le destinazioni di distribuzione valido, inclusi simulatori, emulatori e dispositivi fisici.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

Ricaricamento in tempo reale è attualmente disponibile solo in Visual Studio 2017.

## <a name="requirements"></a>Requisiti

* [Visual Studio 2017 15.7 Preview 4](https://www.visualstudio.com/vs/preview/) o versione successiva con il **sviluppo per dispositivi Mobile con .NET** carico di lavoro.
* [Xamarin. Forms 3.0.354232-pre3](https://www.nuget.org/packages/Xamarin.Forms/3.0.0.354232-pre3) o versione successiva.

## <a name="getting-started"></a>Attività iniziali
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1. Installare Xamarin Ricarica in tempo reale da Visual Studio Marketplace

Xamarin Live Ricarica viene distribuito tramite Visual Studio Marketplace. Per installare l'estensione, visitare il [Xamarin Live Ricarica pagina in Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload) sito Web e fare clic su **scaricare**.

Aprire l'estensione VSIX che viene scaricato e fare clic su **installare**.

![Programma di installazione Visual Studio conferma Ricarica Live Xamarin](images/LiveReloadVSIXInstall.png)

In alternativa, è possibile cercare nel **Online** nella scheda il **estensioni e aggiornamenti** finestra all'interno di Visual Studio.

### <a name="2-configure-your-app-to-use-live-reload"></a>2. Configurare l'app per l'utilizzo di ricaricamento in tempo reale

Aggiunta in tempo reale Ricarica esistente App per dispositivi mobili può essere eseguita in tre passaggi:

1. Verificare che tutti i progetti siano aggiornati per usare [3.0.354232-pre3 xamarin. Forms](https://www.nuget.org/packages/Xamarin.Forms/3.0.0.354232-pre3) o versione successiva.
2. Installare il **Xamarin.LiveReload** NuGet nella libreria del .NET 2.0 Standard. Ciò non è necessario installare nei progetti di piattaforma. Verificare che il **origine pacchetto** è impostata su **tutte**.

![Aggiungere NuGet di Xamarin Ricarica in tempo reale con Gestione pacchetti NuGet](images/addlivereloadnuget.png)

3. Aggiungere `LiveReload.Init();` al costruttore nel `Application` classe, come illustrato nel frammento di codice seguente:

```csharp
public partial class App : Application
{
    public App ()
    {
        // Initialize Live Reload.
        LiveReload.Init();
    
        InitializeComponent();
        MainPage = new MainPage();
    }
}
```

### <a name="3-start-live-reloading"></a>3. Avviare il ricaricamento in tempo reale

Compilare e distribuire l'applicazione. Dopo aver distribuito l'app, aprire un file XAML, apportare alcune modifiche e salvare il file. Le modifiche sono ridistribuite alla destinazione di distribuzione.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

In tempo reale works Ricarica con le modifiche apportate a qualsiasi file XAML. Modifiche al codice c# o l'aggiunta/rimozione dei pacchetti NuGet richiede una nuova compilazione e distribuzione per rendere effettive.

## <a name="frequently-asked-questions"></a>Domande frequenti 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>È disponibile in Visual Studio Xamarin Live ricarica per Mac? 

La versione di anteprima iniziale di ricaricamento in tempo reale di Xamarin è disponibile solo per Visual Studio 2017. Supporto per Visual Studio per Mac è pianificato per una versione futura.

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>Ciò funziona con tutte le librerie, ad esempio prisma? 

Poiché l'app viene compilato, Live Ricarica funziona con tutte le librerie, ad esempio prisma e librerie di controlli di terze parti, ad esempio Telerik Infragistics, Syncfusion, ArcGIS, GrapeCity e altri fornitori di controllo.

### <a name="what-changes-does-live-reload-redeploy"></a>Le modifiche ridistribuire Live ricarica? 

Ricaricamento in tempo reale si applica solo le modifiche apportate in XAML. Se si apportano modifiche a un file c#, la ricompilazione saranno necessario. Supporto per il ricaricamento c# è pianificato per una versione futura.

### <a name="what-platforms-are-supported"></a>Quali piattaforme sono supportate? 

Funzionamento del ricaricamento in tempo reale su alcuna piattaforma supportata da xamarin. Forms, tra cui Android, iOS e UWP.

### <a name="does-this-work-on-emulators-simulators-and-physical-devices"></a>Ciò funziona su emulatori, simulatori e dispositivi fisici? 

Sì, Live Ricarica funziona con tutte le destinazioni di distribuzione valido, inclusi gli emulatori Android, iOS simulatori e dispositivi fisici. Distribuzione a un dispositivo richiede che il dispositivo e il computer sia nella stessa rete Wi-Fi.

### <a name="does-this-work-with-corporate-networks"></a>Ciò funziona con reti aziendali?

Se si esegue il debug di un emulatore Android o un simulatore iOS, Ricarica Live viene utilizzato localhost per comunicare. Se si desidera distribuire in un dispositivo, è necessario trovarsi nella stessa rete Wi-Fi il dispositivo e il computer. Negli scenari in cui ciò non è possibile, è possibile [configurare il proprio server Live Ricarica](#live-reload-server), che consentirà di si ricarica in tempo reale, indipendentemente dalle impostazioni di connettività di rete.

### <a name="does-it-require-debugging-the-app"></a>È necessario il debug dell'app? 

No. In effetti, è possibile anche avviare tutte supportate destinazione dell'applicazione (Android, iOS e UWP) in un numero qualsiasi di dispositivi o simulatori/emulatori e visualizzarli tutti aggiornare contemporaneamente. 

## <a name="limitations"></a>Limitazioni

* È supportato solo il ricaricamento di XAML.
* Supportato solo in Visual Studio.
* Funziona solo con le librerie .NET Standard.
* Fogli di stile CSS non sono supportati.
* Stato dell'interfaccia utente potrebbe non essere mantenuto tra distribuzioni, a meno che non utilizzando MVVM.
* Ricaricamento di risorse a livello di app (vale a dire **app** o condiviso i dizionari delle risorse), navigazione app viene reimpostato.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="app-doesnt-connect"></a>Non collega l'App

Quando l'applicazione viene compilata, le informazioni dal **strumenti > Opzioni > Xamarin > Live Ricarica** (nome, porte e crittografia chiavi degli host) sono incorporate nell'app, in modo che quando `LiveReload.Init()` viene eseguito, nessuna associazione o configurazione è necessario per la connessione abbia esito positivo.

Diverso da problemi di rete normali (firewall, al dispositivo in una rete diversa), il motivo principale per che l'app potrebbe non connettersi IDE infatti la configurazione è diverso da quello in Visual Studio. Questo può succedere se:

* App è stata compilata in un computer diverso.
* App è stato compilato e distribuito in un'altra sessione di Visual Studio, e **per la generazione automatica delle chiavi di crittografia** è selezionata (impostazione predefinita) in **strumenti > Opzioni > Xamarin > Live Ricarica**.
* Impostazioni di Visual Studio sono state modificate (ad esempio le chiavi di nome host, porta o la crittografia), ma l'app non è stato compilato e distribuito nuovamente.

Tutti questi casi vengono risolte compilando e ripetere la distribuzione dell'app.

### <a name="uninstalling-preview-1"></a>Disinstallazione di anteprima 1

Se si dispone di un'anteprima precedente e si riscontrano disinstallarlo, seguire questi passaggi:

1. Eliminare la cartella **C:\Program Files (x86) \Microsoft Visual Studio\Preview\Enterprise\Common7\IDE\Extensions\Xamarin\LiveReload** (Nota: sostituire l'edizione "Enterprise" e "Preview" con "2017" Se si installato in un stabile Visual Studio)
2. Aprire una **prompt dei comandi sviluppatore** ad che Visual Studio ed eseguire `devenv /updateconfiguration`. 

## <a name="tips--tricks"></a>Suggerimenti e consigli

* Fino a quando non modificano le impostazioni di Live ricarica (incluse le chiavi di crittografia, ad esempio se si disattiva **per la generazione automatica delle chiavi di crittografia**) e si compila nello stesso computer, non è necessario compilare e distribuire l'app dopo l'iniziale distribuire, a meno che non si modifica il codice o dipendenze. È possibile avviare solo nuovamente un'app distribuita in precedenza e non si connetterà all'ultimo host utilizzato.

* Non sussiste alcuna limitazione sul numero di dispositivi è possibile connettersi alla stessa sessione di Visual Studio. È possibile distribuire e avviare l'app in dispositivi/simulatori tante in base alle esigenze per visualizzare il funzionamento di ricaricamento in tempo reale su tutti gli elementi nello stesso momento.

* Ricaricamento in tempo reale ricaricherà solo la parte dell'interfaccia utente dell'app, ma *non* ricreare le pagine, né sostituire il modello di visualizzazione (o il contesto di associazione). Ciò significa che il *intera* lo stato dell'app viene sempre mantenuto tra ricaricamenti, incluse le dipendenze dei propri inserite.

## <a name="live-reload-server"></a>Server di ricaricamento in tempo reale

Negli scenari in cui una connessione da app in esecuzione nel computer in uso (come indicato tramite `localhost` o `127.0.0.1` in **strumenti > Opzioni > Xamarin > Live Ricarica**) non è possibile (ad esempio i firewall, reti diverse), è possibile configurare un server remoto, invece, quale sia l'IDE e l'app verrà connettersi a una a.

Ricaricamento in tempo reale utilizza lo standard [protocollo MQTT](http://mqtt.org/) per lo scambio di messaggi e possono pertanto comunicare [server di terze parti](https://github.com/mqtt/mqtt.github.io/wiki/servers). Esistono anche [server pubblici](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers) (noto anche come *Broker*) disponibili che è possibile utilizzare. Ricaricamento in tempo reale è stato testato con `broker.hivemq.com` e `iot.eclipse.org` i nomi host, nonché i servizi forniti da [www.cloudmqtt.com](https://www.cloudmqtt.com) e [www.cloudamqp.com](https://www.cloudamqp.com). È inoltre possibile distribuire il proprio server MQTT nel cloud, ad esempio [HiveMQ in Azure](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud) oppure [MQ coniglio su AWS](http://www.rabbitmq.com/ec2.html). 

È possibile configurare qualsiasi porta, ma è comune per utilizzare la porta 1883 predefinita per i server remoti. I messaggi di ricaricamento in tempo reale Usa end-to-end AES simmetrica crittografia avanzata, pertanto è consigliabile connettersi a server remoti. Per impostazione predefinita, sia la chiave di crittografia e il vettore di inizializzazione (IV) vengono rigenerate in ogni sessione di Visual Studio.

Probabilmente il modo più semplice consiste nell'installare il [mosquitto](https://mosquitto.org) server in una VM Ubuntu vuoto in Azure:

1. Creare una nuova macchina virtuale Server Ubuntu nel portale di Azure
2. Aggiungere una nuova regola di porta in ingresso per 1883 (la porta predefinita MQTT) della scheda di rete
3. Aprire la [Shell Cloud](https://docs.microsoft.com/azure/cloud-shell/overview) (barra rovesciata modalità)
4. Digitare `ssh [USERNAME]@[PUBLIC_IP]` utilizzando il nome utente si è scelto in 1) e l'indirizzo IP pubblico nella pagina Panoramica VM
5. Eseguire `sudo apt-get install mosquitto`, immettere la password si è scelto in 1)

Ora è possibile utilizzare tale IP per la connessione al server MQTT.
