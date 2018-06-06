---
title: HttpClient e selettore di implementazione di SSL/TLS per iOS/macOS
description: Lo stack di HttpClient e SSL/TLS selettore implementazione determina l'implementazione di SSL/TLS e HttpClient che verrà usato dall'app iOS, tvOS oppure macOS Xamarin.
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: 9de2c97933bd33111a751be51e06dffe09794f15
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782268"
---
# <a name="httpclient-and-ssltls-implementation-selector-for-iosmacos"></a>HttpClient e selettore di implementazione di SSL/TLS per iOS/macOS

Il **HttpClient implementazione selettore** per xamarin. IOS, Xamarin.tvOS e Xamarin.Mac determina quale `HttpClient` implementazione da usare. È possibile passare a un'implementazione che utilizza i trasporti nativi di iOS, tvOS oppure macOS (`NSUrlSession` o `CFNetwork`, a seconda del sistema operativo). Il vantaggio è file binari TLS 1.2-supporto di dimensioni ridotte e più velocemente Scarica; lo svantaggio è che richiede il ciclo di eventi sia in esecuzione di operazioni asincrone da eseguire.

Devono fare riferimento a progetti di **System.NET. HTTP** assembly.

> [!WARNING]
> **Aprile, 2018** : a causa di una maggiore sicurezza i requisiti, inclusi la conformità PCI, principali provider di servizi cloud e i server web sono previsti per arrestare il supporto di versioni TLS meno recente 1.2.  Xamarin i progetti creati nelle versioni precedenti di predefinito di Visual Studio per utilizzare le versioni precedenti di TLS.
>
> Per verificare che le app continuano a lavorare con i server e servizi **è necessario aggiornare i progetti di Xamarin con il `NSUrlSession` impostazione illustrato di seguito, quindi ricompilare e ridistribuire le app** agli utenti.

<a name="Selecting-a-HttpClient-Stack" />

### <a name="selecting-a-httpclient-stack"></a>Selezione di uno Stack di HttpClient

Per regolare il `HttpClient` viene usato dall'app:

1. Fare doppio clic su di **nome progetto** nel **Esplora** per aprire le opzioni del progetto.
2. Passare il **compilare** le impostazioni per il progetto (ad esempio, **compilazione iOS** per un'app xamarin).
3. Dal **implementazione HttpClient** elenco a discesa Seleziona il `HttpClient` digitare come uno dei seguenti: **NSUrlSession** (scelta consigliata), **CFNetwork**, o  **Gestito**.

[![Scegliere HttpClient implementazione gestita, CFNetwork o NSUrlSession](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> Per il supporto di TLS 1.2 il `NSUrlSession` opzione è consigliata.

<a name="NSUrlSession" />

### <a name="nsurlsession"></a>NSUrlSession

Il `NSURLSession`-gestore di base si basa sul nativo `NSURLSession` framework disponibile in iOS 7 e versioni successive. 
**Si tratta dell'impostazione consigliata.**

#### <a name="pros"></a>Professionisti

- Usa le API native per migliorare le prestazioni e dimensioni dei file eseguibili più piccole.
- Supporto per gli standard più recenti, ad esempio TLS 1.2.

#### <a name="cons"></a>Svantaggi

- Richiede iOS 7 o versioni successive.
- Alcuni  `HttpClient` /opzioni di funzionalità non disponibili.

<a name="CFNetwork" />

### <a name="cfnetwork"></a>CFNetwork

Il `CFNetwork`-gestore di base si basa sul nativo `CFNetwork` framework disponibile in iOS 6 e versioni successive.

#### <a name="pros"></a>Professionisti

- Usa le API native per migliorare le prestazioni e dimensioni dei file eseguibili più piccole.
- Supporto per gli standard più recenti, ad esempio TLS 1.2.

#### <a name="cons"></a>Svantaggi

- Richiede iOS 6 o versione successiva.
- Non è disponibile in watchOS.
- Alcune funzionalità di HttpClient/opzioni non sono disponibili.

<a name="Managed" />

### <a name="managed"></a>Gestito

Il gestore gestito è il gestore HttpClient completamente gestito che è stato fornito con la versione precedente di Xamarin.

#### <a name="pros"></a>Professionisti

- Include la funzionalità più compatibile impostata con Microsoft .NET e le versioni precedenti di Xamarin.

#### <a name="cons"></a>Svantaggi

- Non è completamente integrato con i sistemi operativi Apple ed è limitato a TLS 1.0. Potrebbe non essere in grado di connettersi per proteggere i server web o servizi cloud in futuro.
- È in genere molto più lento a elementi quali la crittografia dell'API native.
- Richiede più il codice gestito, creando così un'app di dimensioni maggiori distribuibile.

### <a name="programmatically-setting-the-httpmessagehandler"></a>Impostazione a livello di programmazione HttpMessageHandler

Oltre alla configurazione a livello di progetto illustrata in precedenza, è possibile anche creare un'istanza di un `HttpClient` e inserire l'oggetto desiderato `HttpMessageHandler` mediante il costruttore, come illustrato in questi frammenti di codice:

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

Questo rende possibile l'utilizzo di un altro `HttpMessageHandler` da ciò che viene dichiarato nel **opzioni progetto** finestra di dialogo.

<a name="New-SSL-TLS-implementation-build-option" />
<a name="Selecting-a-SSL-TLS-implementation" />
<a name="Apple-TLS" />

## <a name="ssltls-implementation"></a>Implementazione di SSL/TLS

SSL (Secure Socket Layer) e il corrispondente successore, TLS (Transport Layer Security), fornire il supporto per HTTP e le altre connessioni di rete tramite `System.Net.Security.SslStream`. Xamarin. IOS, Xamarin.tvOS o del Xamarin.Mac `System.Net.Security.SslStream` implementazione verrà chiamata l'implementazione di SSL/TLS native di Apple invece di usare l'implementazione gestita fornita da Mono. Implementazione di Apple nativa supporta TLS 1.2.

<a name="App-Transport-Security" />

## <a name="app-transport-security"></a>Sicurezza del trasporto di App

Apple _la sicurezza del trasporto App_ (AT) applica connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app. AT garantisce che tutte le comunicazioni internet siano conformi per le procedure consigliate, connessione sicura impedendo in tal modo la diffusione accidentale di informazioni riservate direttamente tramite l'app o una libreria che sta utilizzando.

Poiché AT è attivata per impostazione predefinita nelle App per iOS 9, tvOS 9 e OS X 10.11 (montagna di El Capitan) e versioni successive, tutte le connessioni utilizzando `NSUrlConnection`, `CFUrl` o `NSUrlSession` saranno soggetti a requisiti di sicurezza AT. Se le connessioni non soddisfano questi requisiti, non riuscirà con un'eccezione.

In base alle selezioni HttpClient Stack e l'implementazione di SSL/TLS, potrebbe essere necessario apportare modifiche all'App per funzionare correttamente con AT.

Per ulteriori informazioni su AT, vedere il nostro [Guida di sicurezza del trasporto App](~/ios/app-fundamentals/ats.md).

## <a name="known-issues"></a>Problemi noti

In questa sezione illustra i problemi noti con supporto TLS in xamarin. IOS.

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>Progetto non è stato possibile caricare con errore "Valore richiesto non è stato trovato AppleTLS"

Xamarin 9.8 introdotte alcune nuove impostazioni di contenuti di **csproj** file per un'applicazione di xamarin. IOS. Queste modifiche possono causare problemi quando il progetto viene aperto con le versioni precedenti di xamarin. IOS. Nella schermata riportata di seguito è riportato un esempio del messaggio di errore che può essere visualizzato in questo scenario:

![Schermata di errore durante il tentativo di caricare il progetto, è richiesto legacy di valore non trovato](http-stack-images/tlserror-xs.png)

Questo errore è causato dall'introduzione del `MtouchTlsProvider` impostazione al file di progetto in 9.8 xamarin. IOS. Se non è possibile eseguire l'aggiornamento a xamarin. IOS 9.8 (o versione successiva), di risolvere il problema è necessario modificare manualmente il **csproj** file dell'applicazione, rimuovere il `MtouchTlsprovider` elemento e quindi salvare il file di progetto modificato.

Frammento riportato di seguito è riportato un esempio di ciò che il `MtouchTlsProvider` impostazione potrebbe apparire come all'interno di un **csproj** file:

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>Collegamenti correlati

- [Transport Layer Security (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [ATS (App Transport Security)](~/ios/app-fundamentals/ats.md)
