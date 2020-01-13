---
title: Ricaricamento in tempo reale di Novell (anteprima)
description: Vedere le modifiche apportate al codice XAML riflesse in tempo reale, senza richiedere un'altra compilazione e distribuzione.
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
robots: noindex
ms.date: 10/26/2018
ms.openlocfilehash: a5a5a9acf47603601461660df689a7a5fa6aee00
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728343"
---
# <a name="xamarin-live-reload-preview"></a>Ricaricamento in tempo reale di Novell (anteprima)

> [!NOTE]
> L'anteprima del ricaricamento in tempo reale di Novell è terminata e vogliamo ringraziare tutti i commenti e i commenti. Per altre informazioni sulle nuove funzionalità di produttività che stiamo lavorando per Xamarin.Forms, leggi la nostra [Roadmap](https://docs.microsoft.com/visualstudio/productinfo/vs-roadmap) per Visual Studio 2019. Questa estensione rimarrà disponibile per Visual Studio 2017, ma non riceverà aggiornamenti futuri.

Il ricaricamento in tempo reale di Novell consente di **apportare modifiche al codice XAML e visualizzarne il riflesso in tempo reale, senza richiedere un'altra compilazione e distribuzione**. Tutte le modifiche apportate al codice XAML verranno ridistribuite al salvataggio e riflesse nella destinazione di distribuzione.

## <a name="requirements"></a>Requisiti di

* [Visual Studio 2017 versione 15,7 o successiva](https://visualstudio.microsoft.com/vs/) con il carico di lavoro **sviluppo di applicazioni per dispositivi mobili con .NET** .
* [Xamarin.Forms 3.0.0 o versione successiva](https://www.nuget.org/packages/Xamarin.Forms/).

## <a name="getting-started"></a>Guida introduttiva
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1. installare il ricaricamento in tempo reale di Novell dalla Visual Studio Marketplace

Il ricaricamento in tempo reale di Novell viene distribuito tramite il Visual Studio Marketplace. Per installare l'estensione, visitare la [pagina relativa al ricaricamento in tempo reale di Novell nel](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload) sito Web Visual Studio Marketplace e fare clic su **download**.

Aprire il. vsix scaricato e fare clic su **Installa**.

![Programma di installazione di Visual Studio Novell Live reload conferma](images/LiveReloadVSIXInstall.png)

In alternativa, è possibile cercarlo nella scheda **online** della finestra di dialogo **estensioni e aggiornamenti** all'interno di Visual Studio.

### <a name="2-configure-your-app-to-use-live-reload"></a>2. configurare l'app per l'uso del ricaricamento in tempo reale

L'aggiunta del ricaricamento in tempo reale alle app per dispositivi mobili esistenti può essere eseguita in tre passaggi:

1. Assicurarsi che tutti i progetti siano aggiornati per l'uso di [Xamarin.Forms 3.0.0 o superiore](https://www.nuget.org/packages/Xamarin.Forms/) o superiore.

2. Aggiungere il pacchetto NuGet **Xamarin.LiveReload** :

    a. **.NET standard** : installare NuGet **Xamarin.LiveReload** nella libreria .NET standard 2,0. Questa operazione non deve essere installata nei progetti della piattaforma. Verificare che l' **origine del pacchetto** sia impostata su **tutti**.
    
    b. **Progetti condivisi** : installare **Xamarin.LiveReload** NuGet in tutti i progetti di piattaforma, ad esempio Android, iOS, UWP e così via. Verificare che l' **origine del pacchetto** sia impostata su **tutti**.

    [![aggiungere Novell Live ricarica NuGet con gestione pacchetti NuGet](images/addlivereloadnuget.w157-sml.png)](images/addlivereloadnuget.w157.png#lightbox)

3. Aggiungere `LiveReload.Init();` al costruttore nella classe `Application`, come illustrato nel frammento di codice seguente:

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

### <a name="3-start-live-reloading"></a>3. avviare il ricaricamento in tempo reale

Compilare e distribuire l'applicazione. Una volta distribuita l'app, aprire un file XAML, apportare alcune modifiche e salvare il file. Le modifiche vengono ridistribuite nella destinazione di distribuzione.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

Il ricaricamento attivo funziona con le modifiche apportate a qualsiasi file XAML. Per rendere C# effettive le modifiche o l'aggiunta o la rimozione di pacchetti NuGet, è necessario eseguire una nuova compilazione e distribuzione.

## <a name="frequently-asked-questions"></a>Domande frequenti 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>Il ricaricamento in tempo reale di Novell è disponibile in Visual Studio per Mac? 

No, la versione di anteprima di Novell Live reload è disponibile solo per Visual Studio 2017.

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>Funziona con tutte le librerie, ad esempio Prism? 

Poiché l'app viene compilata, il ricaricamento in tempo reale funziona con tutte le librerie, ad esempio Prism e librerie di controlli di terze parti, ad esempio Telerik, Infragistics, Syncfusion, ArcGIS, GrapeCity e altri fornitori di controlli.

### <a name="what-changes-does-live-reload-redeploy"></a>Quali modifiche vengono ridistribuite in tempo reale? 

Il ricaricamento in tempo reale applica solo le modifiche apportate a XAML o CSS. Se si apportano modifiche C# a un file, sarà necessario eseguire una ricompilazione. 

### <a name="what-platforms-are-supported"></a>Quali piattaforme sono supportate? 

Il ricaricamento in tempo reale funziona su qualsiasi piattaforma supportata da Xamarin.Forms, inclusi Android, iOS e UWP.

### <a name="does-this-work-on-emulators-simulators-and-physical-devices"></a>Funziona su emulatori, simulatori e dispositivi fisici? 

Sì, il ricaricamento in tempo reale funziona con tutte le destinazioni di distribuzione valide, inclusi emulatori Android, simulatori iOS e dispositivi fisici. Per la distribuzione in un dispositivo è necessario che il dispositivo e il computer si trovino nella stessa rete Wi-Fi.

### <a name="does-this-work-with-corporate-networks"></a>Funziona con le reti aziendali?

Se si esegue il debug in un emulatore Android o un simulatore iOS, il ricaricamento in tempo reale USA localhost per la comunicazione. Se si vuole eseguire la distribuzione in un dispositivo, il dispositivo e il computer devono trovarsi nella stessa rete Wi-Fi. Negli scenari in cui questa operazione non è possibile, è possibile [configurare il proprio server di ricaricamento in tempo reale](#live-reload-server), in modo da consentire il ricaricamento in tempo reale, indipendentemente dalle impostazioni di connettività di rete.

### <a name="does-it-require-debugging-the-app"></a>È necessario eseguire il debug dell'app? 

No. Infatti, è anche possibile avviare tutte le destinazioni di applicazioni supportate (Android, iOS e UWP) su un numero qualsiasi di dispositivi o simulatori/emulatori e visualizzarli tutti gli aggiornamenti contemporaneamente. 

## <a name="limitations"></a>Limitazioni

* È supportato solo il ricaricamento di XAML.
* Lo stato dell'interfaccia utente potrebbe non essere mantenuto tra le ridistribuzioni, a meno che non si usi MVVM.

## <a name="known-issues"></a>Problemi noti

* Supportato solo in Visual Studio.
* Il collegamento deve essere impostato su **non collegare** o **collegare solo SDK del Framework** 
* Ricaricando le risorse a livello di app (ad esempio, **app. XAML** o i dizionari risorse condivise), la navigazione delle app viene reimpostata. 
* Il ricaricamento di ContentView richiede attualmente il ricaricamento della pagina che lo contiene.
* Gli elementi contenenti AutomationId possono causare un errore di ricaricamento.
* La modifica di XAML durante il debug di UWP può causare un arresto anomalo del runtime. Soluzione alternativa: usare **Avvia senza eseguire debug (CTRL + F5)** anziché **avviare il debug (F5)** .

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="error-codes"></a>Codici di errore

* **XLR001**: *il progetto corrente fa riferimento alla versione ' [versione]' del pacchetto NuGet ' Xamarin.LiveReload ', ma l'estensione per il ricaricamento in tempo reale di Novell richiede la versione ' [Version]'.*

  Per consentire l'iterazione e l'evoluzione rapide della funzionalità di ricaricamento in tempo reale, il pacchetto NuGet e l'estensione di Visual Studio devono corrispondere esattamente. Aggiornare il pacchetto NuGet con la stessa versione dell'estensione installata.

* **XLR002**: *il ricaricamento in tempo reale richiede almeno la proprietà' MqttHostname ' durante la compilazione dalla riga di comando. In alternativa, impostare ' EnableLiveReload ' su' false ' per disabilitare la funzionalità.*

  Le proprietà necessarie per il ricaricamento in tempo reale non sono disponibili quando si compila dalla riga di comando (o nell'integrazione continua) e devono pertanto essere fornite in modo esplicito. 

* **XLR003**: *il pacchetto NuGet di ricarica in tempo reale richiede l'installazione dell'estensione di Visual Studio per il ricaricamento di Novell Live.*

  Si è provato a compilare un progetto che fa riferimento al pacchetto NuGet di ricaricamento in tempo reale, ma l'estensione visiva non è installata.  

* *Eccezione durante il caricamento degli assembly: System. IO. FileNotFoundException: non è stato possibile caricare l'assembly ' Xamarin.Live. reload, Version = 0.3.27.0, Culture = neutral, PublicKeyToken ='.*

  Il progetto host deve usare `PackageReference` anziché `packages.config`

### <a name="app-doesnt-connect"></a>L'app non si connette

Quando l'applicazione viene compilata, le informazioni degli **strumenti > opzioni > novell > il ricaricamento in tempo reale** (nome host, porta e chiavi di crittografia) sono incorporati nell'app, in modo che quando viene eseguito `LiveReload.Init()`, non è necessaria l'associazione o la configurazione affinché la connessione abbia esito positivo.

A parte i normali problemi di rete (firewall, dispositivo in una rete diversa), il motivo principale per cui l'app non riesce a connettere l'IDE è perché la relativa configurazione è diversa da quella in Visual Studio. Questo problema può verificarsi se:

* L'app è stata compilata in un computer diverso.
* L'app è stata compilata e distribuita in un'altra sessione di Visual Studio e la **generazione automatica delle chiavi di crittografia** è selezionata (impostazione predefinita) in **strumenti > opzioni > Novell > ricarica in tempo reale**.
* Le impostazioni di Visual Studio sono state modificate, ad esempio nome host, porta o chiavi di crittografia, ma l'app non è stata compilata e distribuita nuovamente.

Questi casi sono tutti risolti compilando e distribuendo di nuovo l'app.

### <a name="uninstalling-preview-1"></a>Disinstallazione dell'anteprima 1

Se si dispone di un'anteprima precedente e si verificano problemi di disinstallazione, attenersi alla seguente procedura:

1. Eliminare la cartella **c:\Programmi (x86) \Microsoft Visual Studio\Preview\Enterprise\Common7\IDE\Extensions\Xamarin\LiveReload** (Nota: sostituire "Enterprise" con l'edizione installata e "Preview" con "2017" se è stato installato in uno stabile rispetto a)
2. Aprire un **prompt dei comandi** per gli sviluppatori per Visual Studio ed eseguire `devenv /updateconfiguration`. 

## <a name="tips--tricks"></a>Suggerimenti

* Finché le impostazioni di ricaricamento in tempo reale non cambiano (incluse le chiavi di crittografia, ad esempio se si disattiva la **generazione automatica delle chiavi di crittografia**) e si compila dallo stesso computer, non è necessario compilare e distribuire l'app dopo la distribuzione iniziale, a meno che non si modifichi il codice o le dipendenze. È possibile avviare di nuovo un'app distribuita in precedenza e si connetterà all'ultimo host utilizzato.

* Non esiste alcuna limitazione al numero di dispositivi che è possibile connettere alla stessa sessione di Visual Studio. È possibile distribuire e avviare l'app nel numero di dispositivi/simulatori necessari per visualizzare il ricaricamento in tempo reale su tutti i dispositivi contemporaneamente.

* Il ricaricamento in tempo reale ricaricherà solo la parte dell'interfaccia utente dell'app, *ma non* ricreerà le pagine, né sostituirà il modello di visualizzazione o il contesto di associazione. Ciò significa che l' *intero* stato dell'app viene sempre mantenuto tra i ricaricamenti, incluse le dipendenze inserite.

## <a name="live-reload-server"></a>Ricarica in tempo reale del server

Negli scenari in cui una connessione dall'app in esecuzione al computer (come indicato con `localhost` o `127.0.0.1` in **strumenti > opzioni > novell > ricaricamento in tempo reale**) non è possibile (ad esempio, firewall, reti diverse), è possibile configurare un server remoto, a cui si conect l'IDE e l'app.

Il ricaricamento in tempo reale usa il [protocollo MQTT](https://mqtt.org/) standard per scambiare messaggi e pertanto può comunicare con [server di terze parti](https://github.com/mqtt/mqtt.github.io/wiki/servers). Sono disponibili anche [server pubblici](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers) (noti anche come *broker*) disponibili che è possibile usare. Il ricaricamento in tempo reale è stato testato con `broker.hivemq.com` e `iot.eclipse.org` nomi host, oltre ai servizi forniti da [www.cloudmqtt.com](https://www.cloudmqtt.com) e [www.cloudamqp.com](https://www.cloudamqp.com). È anche possibile distribuire il proprio server MQTT nel cloud, ad esempio [HiveMQ in Azure](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud).

È possibile configurare qualsiasi porta, ma è comune usare la porta 1883 predefinita per i server remoti. I messaggi di ricaricamento in tempo reale utilizzano la crittografia simmetrica end-to-end avanzata, quindi è sicuro connettersi ai server remoti. Per impostazione predefinita, la chiave di crittografia e il vettore di inizializzazione (IV) vengono rigenerati in ogni sessione di Visual Studio.

Probabilmente il modo più semplice consiste nell'installare il server [mosquitto](https://mosquitto.org) in una VM Ubuntu vuota in Azure:

1. Creare una nuova macchina virtuale Ubuntu Server nel portale di Azure
2. Aggiungere una nuova regola di porta in ingresso per 1883 (porta MQTT predefinita) nella scheda rete
3. Aprire il [cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) (modalità bash)
4. Digitare `ssh [USERNAME]@[PUBLIC_IP]` usando il nome utente scelto in 1) e l'indirizzo IP pubblico visualizzato nella pagina di panoramica della macchina virtuale
5. Eseguire `sudo apt-get install mosquitto`, immettendo la password scelta in 1)

A questo punto è possibile usare tale indirizzo IP per connettersi al server MQTT.
