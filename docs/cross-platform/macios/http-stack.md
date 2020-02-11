---
title: Selettore di implementazione di HttpClient e SSL/TLS per iOS/macOS
description: Lo stack HttpClient e il selettore di implementazione SSL/TLS determinano l'implementazione di HttpClient e SSL/TLS che verrà usata dall'app Xamarin iOS, tvOS o macOS.
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: 3b87eb6ae9e97be4ee34b092efce72c0d97c88b3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015846"
---
# <a name="httpclient-and-ssltls-implementation-selector-for-iosmacos"></a>Selettore di implementazione di HttpClient e SSL/TLS per iOS/macOS

Il **selettore di implementazione di HttpClient** per Xamarin.iOS, Xamarin.TvOS e Xamarin.Mac controlla che `HttpClient` implementazione da usare. È possibile passare a un'implementazione che usa i trasporti nativi iOS, tvOS o macOS (`NSUrlSession` o `CFNetwork`, a seconda del sistema operativo). Il lato positivo è TLS 1,2: supporto, file binari più piccoli e download più veloci. lo svantaggio è che è necessario che il ciclo di eventi sia in esecuzione per l'esecuzione di operazioni asincrone.

I progetti devono fare riferimento all'assembly **System .NET. http** .

> [!WARNING]
> **Aprile 2018** : a causa di un aumento dei requisiti di sicurezza, tra cui la conformità PCI, i principali provider di servizi cloud e i server Web dovrebbero smettere di supportare le versioni TLS precedenti a 1,2. Per impostazione predefinita, i progetti Xamarin creati nelle versioni precedenti di Visual Studio usano le versioni precedenti di TLS.
>
> Per garantire che le app continuino a funzionare con questi server e servizi, **è necessario aggiornare i progetti Xamarin con l'impostazione `NSUrlSession` illustrata di seguito, quindi ricompilare e ridistribuire le app** agli utenti.

## <a name="selecting-an-httpclient-stack"></a>Selezione di uno stack HttpClient

Per modificare il `HttpClient` usato dall'app:

1. Fare doppio clic sul **nome del progetto** nella **Esplora soluzioni** per aprire le opzioni del progetto.
2. Passare alle impostazioni di **compilazione** per il progetto (ad esempio, **compilazione iOS** per un'app Xamarin.iOS).
3. Dall'elenco a discesa **implementazione HttpClient** selezionare il tipo di `HttpClient` come uno dei seguenti: **NSUrlSession** (scelta consigliata), **CFNetwork**o **gestito**.

[![scegliere l'implementazione di HttpClient da Managed, CFNetwork o NSUrlSession](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> Per il supporto di TLS 1,2 è consigliabile usare l'opzione `NSUrlSession`.

### <a name="nsurlsession"></a>NSUrlSession

Il gestore basato su `NSURLSession`è basato sul Framework di `NSURLSession` nativo disponibile in iOS 7 e versioni successive. 
**Si tratta dell'impostazione consigliata.**

#### <a name="pros"></a>I professionisti

- USA API native per ottenere prestazioni migliori e dimensioni eseguibili più piccole.
- Supporto per gli standard più recenti, ad esempio TLS 1,2.

#### <a name="cons"></a>Svantaggi

- Richiede iOS 7 o versione successiva.
- Alcune funzionalità o opzioni di `HttpClient` non sono disponibili.

### <a name="cfnetwork"></a>CFNetwork

Il gestore basato su `CFNetwork`è basato sul Framework di `CFNetwork` nativo disponibile in iOS 6 e versioni successive.

#### <a name="pros"></a>I professionisti

- USA API native per ottenere prestazioni migliori e dimensioni eseguibili più piccole.
- Supporto per gli standard più recenti, ad esempio TLS 1,2.

#### <a name="cons"></a>Svantaggi

- Richiede iOS 6 o versione successiva.
- Non disponibile in watchos.
- Alcune funzionalità/opzioni di HttpClient non sono disponibili.

### <a name="managed"></a>Gestito

Il gestore gestito è il gestore HttpClient completamente gestito che è stato fornito con la versione precedente di Novell.

#### <a name="pros"></a>I professionisti

- Dispone del set di funzionalità più compatibile con Microsoft .NET e versioni precedenti di Novell.

#### <a name="cons"></a>Svantaggi

- Non è completamente integrato con i sistemi operativi Apple ed è limitato a TLS 1,0. In futuro, potrebbe non essere in grado di connettersi ai server Web o ai servizi cloud protetti.
- Si tratta in genere di un'attività molto più lenta, ad esempio la crittografia rispetto alle API native.
- Richiede codice più gestito, creando in tal modo una più grande app distribuibile.

### <a name="programmatically-setting-the-httpmessagehandler"></a>Impostazione di HttpMessageHandler a livello di codice

Oltre alla configurazione a livello di progetto illustrata in precedenza, è anche possibile creare un'istanza di un `HttpClient` e inserire la `HttpMessageHandler` desiderata tramite il costruttore, come illustrato nei frammenti di codice seguenti:

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

In questo modo è possibile utilizzare una `HttpMessageHandler` diversa da quella dichiarata nella finestra di dialogo **Opzioni progetto** .

## <a name="ssltls-implementation"></a>Implementazione di SSL/TLS

SSL (Secure Socket Layer) e il suo successore, TLS (Transport Layer Security), forniscono il supporto per HTTP e altre connessioni di rete tramite `System.Net.Security.SslStream`. Xamarin.iOS, Xamarin.tvOS o Xamarin.Mac l'implementazione di `System.Net.Security.SslStream` chiamerà l'implementazione SSL/TLS nativa di Apple invece di usare l'implementazione gestita fornita da mono. L'implementazione nativa di Apple supporta TLS 1,2.

> [!WARNING]
> La prossima versione di Xamarin.Mac 4.8 supporterà solo macOS 10.9 o versione successiva.
> Le versioni precedenti di Xamarin.Mac supportavano macOS 10.7 o versione successiva, ma queste versioni precedenti di macOS non dispongono di un'infrastruttura TLS sufficiente per il supporto di TLS 1.2. Per scegliere come destinazione macOS 10.7 o macOS 10.8, usare Xamarin.Mac 4.6 o versioni precedenti.

## <a name="app-transport-security"></a>ATS (App Transport Security)

La _sicurezza del trasporto app_ di Apple (ATS) impone connessioni sicure tra le risorse Internet, ad esempio il server back-end dell'app, e l'app. ATS garantisce che tutte le comunicazioni Internet siano conformi alle procedure consigliate per la connessione sicura, impedendo in tal modo la divulgazione accidentale di informazioni riservate direttamente tramite l'app o una libreria che sta utilizzando.

Poiché ATS è abilitato per impostazione predefinita nelle app compilate per iOS 9, tvOS 9 e OS X 10,11 (El Capitan) e versioni successive, tutte le connessioni che usano `NSUrlConnection`, `CFUrl` o `NSUrlSession` saranno soggette ai requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, avranno esito negativo con un'eccezione.

In base alle selezioni dello stack di HttpClient e dell'implementazione di SSL/TLS, potrebbe essere necessario apportare modifiche all'app per funzionare correttamente con ATS.

Per ulteriori informazioni su ATS, vedere la guida alla [sicurezza del trasporto delle app](~/ios/app-fundamentals/ats.md).

## <a name="known-issues"></a>Problemi noti

In questa sezione vengono descritti i problemi noti relativi al supporto TLS in Xamarin.iOS.

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>Non è stato possibile caricare il progetto con errore "il valore richiesto AppleTLS non è stato trovato"

Xamarin.iOS 9,8 ha introdotto alcune nuove impostazioni che contenevano il file con **estensione csproj** per un'applicazione Xamarin.iOS. Queste modifiche possono causare problemi quando il progetto viene aperto con le versioni precedenti di Xamarin.iOS. Lo screenshot seguente è un esempio del messaggio di errore che può essere visualizzato in questo scenario:

![Screenshot dell'errore durante il tentativo di caricare il progetto. il valore richiesto legacy non è stato trovato](http-stack-images/tlserror-xs.png)

Questo errore è causato dall'introduzione dell'impostazione `MtouchTlsProvider` al file di progetto in Xamarin.iOS 9,8. Se non è possibile eseguire l'aggiornamento a Xamarin.iOS 9,8 (o versione successiva), la soluzione alternativa consiste nel modificare manualmente l'applicazione del file con **estensione csproj** , rimuovere l'elemento `MtouchTlsprovider`, quindi salvare il file di progetto modificato.

Il frammento di codice seguente è un esempio di ciò che l'impostazione del `MtouchTlsProvider` potrebbe essere simile all'interno di un file con estensione **csproj** :

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>Collegamenti correlati

- [Transport Layer Security (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [ATS (App Transport Security)](~/ios/app-fundamentals/ats.md)
