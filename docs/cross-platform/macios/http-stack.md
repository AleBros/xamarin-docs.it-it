---
title: HttpClient e selettore dell'implementazione di SSL/TLS per iOS o macOS
description: Stack HttpClient e SSL/TLS selettore dell'implementazione determina l'implementazione di HttpClient e SSL/TLS che verrà usato dall'app per Xamarin iOS, tvOS o macOS.
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: fd48c7148aadd8d156544113e2d719295294bf40
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261273"
---
# <a name="httpclient-and-ssltls-implementation-selector-for-iosmacos"></a>Selettore dell'implementazione di HttpClient e SSL/TLS per iOS o macOS

Il **selettore dell'implementazione di HttpClient** per xamarin. IOS, tvos e xamarin. Mac consente di controllare quale `HttpClient` implementazione da usare. È possibile passare a un'implementazione che usa i trasporti nativi di iOS, tvOS o macOS (`NSUrlSession` o `CFNetwork`, a seconda del sistema operativo). Il vantaggio è file binari TLS 1.2-supporto di dimensioni ridotte e scarica più velocemente; lo svantaggio è che richiede il ciclo di eventi sia in esecuzione di operazioni asincrone da eseguire.

Devono fare riferimento a progetti di **System.NET. HTTP** assembly.

> [!WARNING]
> **Aprile 2018** : a causa di una maggiore sicurezza i requisiti, incluse la conformità PCI, la maggior parte delle provider di servizi cloud e i server web devono interrompere il supporto per le versioni TLS precedente alla 1.2.  Progetti Xamarin creati nelle versioni precedenti di predefinito di Visual Studio per usare le versioni precedenti di TLS.
>
> Per assicurarsi che le app continuano a funzionare con questi server e servizi **è necessario aggiornare i progetti Xamarin con il `NSUrlSession` impostazione illustrato di seguito, quindi ricompilare e ridistribuire le tue app** agli utenti.

### <a name="selecting-an-httpclient-stack"></a>Selezione di uno stack di HttpClient

Per regolare la `HttpClient` viene usata dall'app:

1. Fare doppio clic il **nome progetto** nel **Esplora soluzioni** per aprire le opzioni di progetto.
2. Passare al **compilare** le impostazioni per il progetto (ad esempio, **compilazione iOS** per un'app xamarin. IOS).
3. Dal **implementazione di HttpClient** elenco a discesa, seleziona il `HttpClient` digitare come uno dei seguenti: **NSUrlSession** (scelta consigliata), **CFNetwork**, o **gestito**.

[![Implementazione di HttpClient scegliere gestito, CFNetwork o NSUrlSession](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> Per supportare TLS 1.2 il `NSUrlSession` opzione è consigliata.

### <a name="nsurlsession"></a>NSUrlSession

Il `NSURLSession`-basato su gestore si basa sul nativo `NSURLSession` framework disponibile in iOS 7 e versioni successive. 
**Si tratta dell'impostazione consigliata.**

#### <a name="pros"></a>Professionisti

- Usa API native per migliorare le prestazioni e dimensioni dei file eseguibili più piccole.
- Supporto per gli standard più recenti, ad esempio TLS 1.2.

#### <a name="cons"></a>Svantaggi

- Richiede iOS 7 o versione successiva.
- Alcuni `HttpClient` non sono disponibili le funzionalità/Opzioni.

### <a name="cfnetwork"></a>CFNetwork

Il `CFNetwork`-basato su gestore si basa sul nativo `CFNetwork` framework disponibile in iOS 6 e versioni successive.

#### <a name="pros"></a>Professionisti

- Usa API native per migliorare le prestazioni e dimensioni dei file eseguibili più piccole.
- Supporto per gli standard più recenti, ad esempio TLS 1.2.

#### <a name="cons"></a>Svantaggi

- Richiede iOS 6 e versioni successive.
- Non è disponibile in watchOS.
- Alcune funzionalità HttpClient/opzioni non sono disponibili.

### <a name="managed"></a>Gestito

Il gestore gestito è il gestore di HttpClient completamente gestito che è stato spedito con versione precedente di Xamarin.

#### <a name="pros"></a>Professionisti

- Include la funzionalità più compatibile impostato con le versioni precedenti di Xamarin e Microsoft .NET.

#### <a name="cons"></a>Svantaggi

- Non è completamente integrato con i sistemi operativi Apple ed è limitato a TLS 1.0. Potrebbe non essere in grado di connettersi per proteggere i server web o servizi cloud in futuro.
- Si tratta in genere molto inferiore di elementi come crittografia API native.
- Richiede più il codice gestito, creando pertanto un'applicazione più grande distribuibile.

### <a name="programmatically-setting-the-httpmessagehandler"></a>Impostazione a livello di programmazione HttpMessageHandler

Oltre alla configurazione a livello di progetto illustrata in precedenza, è possibile anche creare un'istanza di un `HttpClient` e inserire il valore desiderato `HttpMessageHandler` mediante il costruttore, come illustrato in questi frammenti di codice:

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

Questo rende possibile usare un diverso `HttpMessageHandler` da quanto dichiarato nel **opzioni progetto** finestra di dialogo.

## <a name="ssltls-implementation"></a>Implementazione di SSL/TLS

SSL (Secure Socket Layer) e il relativo successore, TLS (Transport Layer Security), forniscono il supporto per HTTP e altre connessioni di rete tramite `System.Net.Security.SslStream`. Xamarin. IOS, tvos o di xamarin. Mac `System.Net.Security.SslStream` implementazione chiamerà l'implementazione di SSL/TLS native di Apple invece di usare l'implementazione gestita fornita da Mono. Implementazione di Apple nativa supporta TLS 1.2.

> [!WARNING]
> La prossima versione di Xamarin.Mac 4.8 supporterà solo macOS 10.9 o versione successiva.
> Le versioni precedenti di Xamarin.Mac supportavano macOS 10.7 o versione successiva, ma queste versioni precedenti di macOS non dispongono di un'infrastruttura TLS sufficiente per il supporto di TLS 1.2. Per scegliere come destinazione macOS 10.7 o macOS 10.8, usare Xamarin.Mac 4.6 o versioni precedenti.

## <a name="app-transport-security"></a>ATS (App Transport Security)

Apple _App Transport Security_ (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app. ATS garantisce che tutte le comunicazioni internet siano conformi per proteggere la connessione le procedure consigliate, evitando la diffusione accidentale di informazioni riservate direttamente tramite l'app o una libreria che sta utilizzando.

Poiché ATS è abilitato per impostazione predefinita nelle App per iOS 9, tvOS 9 e OS X 10.11 (El Capitan) e versioni più recenti, tutte le connessioni utilizzando `NSUrlConnection`, `CFUrl` o `NSUrlSession` saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.

Base alle selezioni HttpClient Stack e implementazione di SSL/TLS, potrebbe essere necessario apportare modifiche all'App per funzionare correttamente con ATS.

Per altre informazioni sulle ATS, vedere la [Guida alla sicurezza del trasporto App](~/ios/app-fundamentals/ats.md).

## <a name="known-issues"></a>Problemi noti

Questa sezione illustra i problemi noti con supporto TLS in xamarin. IOS.

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>Progetto non è stato possibile caricare i dati con errore "Valore richiesto non è stato trovato AppleTLS"

Xamarin. IOS 9.8 introdotte alcune nuove impostazioni contenuti il **file con estensione csproj** file per un'applicazione xamarin. IOS. Queste modifiche possono provocare problemi quando si apre il progetto con le versioni precedenti di xamarin. IOS. Lo screenshot seguente è riportato un esempio del messaggio di errore che può essere visualizzato in questo scenario:

![Schermata di errore durante il tentativo di caricare il progetto, richiesto legacy di valore non trovato](http-stack-images/tlserror-xs.png)

Questo errore è causato dall'introduzione del `MtouchTlsProvider` impostazione al file di progetto in 9.8 di xamarin. IOS. Se non è possibile eseguire l'aggiornamento a xamarin. IOS 9.8 (o versione successiva), il lavoro è circa modificare manualmente il **file con estensione csproj** applicazione di file, rimuovere il `MtouchTlsprovider` elemento e quindi salvare il file di progetto è stato modificato.

Il frammento seguente è riportato un esempio di ciò che il `MtouchTlsProvider` impostazione potrebbe essere, ad esempio all'interno di un **csproj** file:

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>Collegamenti correlati

- [Transport Layer Security (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [ATS (App Transport Security)](~/ios/app-fundamentals/ats.md)
