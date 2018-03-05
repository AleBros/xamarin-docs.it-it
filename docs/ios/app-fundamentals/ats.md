---
title: Sicurezza del trasporto di App
description: Sicurezza del trasporto di App (AT) applica connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app.
ms.topic: article
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/13/2017
ms.openlocfilehash: 60858e05e222725f05eb67bd7aaa4e56d2ff3880
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="app-transport-security"></a>Sicurezza del trasporto di App

_Sicurezza del trasporto di App (AT) applica connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app._

In questo articolo presenterà le modifiche di sicurezza che applica la sicurezza del trasporto App in un'app di iOS 9 e [implicazioni per i progetti di xamarin](#xamarinsupport), che verrà applicata la [opzioni di configurazione at](#config) e che verrà applicata come [rinuncia di at](#optout) AT se necessario. Poiché AT è abilitato per impostazione predefinita, tutte le connessioni internet non sicure genererà un'eccezione nelle app di iOS 9 (a meno che non è stato esplicitamente autorizzato).


## <a name="about-app-transport-security"></a>Sulla sicurezza del trasporto di App

Come indicato in precedenza, AT garantisce che tutte le comunicazioni internet in iOS 9 e montagna di El OS Capitan X siano conformi per le procedure consigliate, connessione sicura impedendo in tal modo la diffusione accidentale di informazioni riservate direttamente tramite l'app o una libreria che è utilizzo.

Per le app esistenti, implementare il `HTTPS` del protocollo quando possibile. Per nuove app xamarin. IOS, è necessario utilizzare `HTTPS` esclusivamente durante la comunicazione con le risorse internet. Inoltre, la comunicazione di alto livello dell'API deve essere crittografata con TLS versione 1.2 PFS.

Qualsiasi connessione stabilita con [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/), [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/) o [NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/) utilizzeranno AT per impostazione predefinita nelle App per iOS 9 e OS X 10.11 (montagna di El Capitan).

## <a name="default-ats-behavior"></a>Comportamento di AT predefinito

Poiché AT è attivata per impostazione predefinita nelle App per iOS 9 e OS X 10.11 (montagna di El Capitan), tutte le connessioni utilizzando [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/), [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/) o [NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/) sarà soggetto a Requisiti di sicurezza AT. Se le connessioni non soddisfano questi requisiti, non riuscirà con un'eccezione.

### <a name="ats-connection-requirements"></a>Requisiti di connessione AT

AT applicherà i seguenti requisiti per tutte le connessioni internet:

- Tutte le crittografie di connessione devono utilizzare PFS. Visualizzare l'elenco di crittografie accettate riportato di seguito.
- Il protocollo Transport Layer Security (TLS) deve essere versione 1.2 o successiva.
- Almeno delle impronte digitali SHA256 con un 2048 bit o maggiore chiave RSA, o un a 256 bit o una chiave maggiore Elliptic Curve (ECC) devono essere utilizzata per tutti i certificati.

Nuovamente, poiché AT è attivata per impostazione predefinita in iOS 9, qualsiasi tentativo di stabilire una connessione che non soddisfa questi requisiti comporterà viene generata un'eccezione. 

<a name="ATS-Compatible-Ciphers" />

### <a name="ats-compatible-ciphers"></a>Crittografie compatibile at

Il seguente tipo di crittografia PFS vengono accettati da AT internet comunicazioni protette:

- `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA`
- `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`

Per ulteriori informazioni sull'utilizzo delle classi di comunicazione internet iOS, vedere Apple [riferimento alla classe NSURLConnection](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html#//apple_ref/doc/uid/TP40003755), [CFURL riferimento](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/uid/20001206) o [NSURLSession classe di riferimento ](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/index.html#//apple_ref/doc/uid/TP40013435).

<a name="xamarinsupport" />

## <a name="supporting-ats-in-xamarinios"></a>Supporto di AT in xamarin. IOS

Poiché AT è abilitato per impostazione predefinita in iOS 9 e montagna di El OS Capitan X, se l'app xamarin qualsiasi libreria o servizio in uso esegue connessione a internet, è necessario eseguire un'azione o le connessioni si verificherà un'eccezione generata.

Per un'app esistente, Apple suggerito è supportare la `HTTPS` protocollo appena possibile. Se non è una possibile perché ci si connette a un 3rd parte del servizio web che non supporta `HTTPS` o se si supporta `HTTPS` poco pratica, è possibile rifiutare esplicitamente AT. Vedere il [Opting-Out di at](#Opting-Out-of-ATS) sezione riportata di seguito per ulteriori dettagli.

Per una nuova app xamarin. IOS, è necessario utilizzare `HTTPS` esclusivamente durante la comunicazione con le risorse internet. Nuovamente, potrebbero esserci situazioni (come l'uso di un servizio web di terze parti 3rd) in cui questo non è possibile e occorre rifiutare esplicitamente AT.

AT applica inoltre, la comunicazione di alto livello dell'API da crittografare con TLS versione 1.2 PFS. Vedere il [requisiti di connessione AT](#ATS-Connection-Requirements) e [at compatibile crittografie](#ATS-Compatible-Ciphers) sezioni precedenti per ulteriori dettagli.

Anche se potrebbe non essere familiarità con TLS ([Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)) è il successore di SSL ([Secure Socket Layer](https://en.wikipedia.org/wiki/Transport_Layer_Security)) e fornisce un insieme di protocolli di crittografia per garantire la protezione su connessioni di rete.

Il livello di TLS è controllato dal servizio web che si utilizza, pertanto è di fuori di controllo dell'applicazione. Sia il `HttpClient` e `ModernHttpClient` automaticamente deve usare il massimo livello di crittografia TLS è supportata dal server.

In base al server che si parla (specialmente se si tratta di un servizio di terze parti 3rd), potrebbe essere necessario disabilitare PFS o selezionare un livello inferiore di TLS. Vedere il [configurazione delle opzioni di at](#Configuring-ATS-Options) sezione riportata di seguito per ulteriori dettagli.

> [!IMPORTANT]
> **Nota:** App la sicurezza del trasporto non è valida per le app Xamarin mediante **implementazioni gestite HTTPClient**. Si applica alle connessioni tramite CFNetwork **implementazioni HTTPClient** o **NSURLSession HTTPClient implementazioni** solo.

### <a name="setting-the-httpclient-implementation"></a>Impostazione dell'implementazione di HTTPClient

Per impostare l'implementazione di HTTPClient utilizzata da un'app iOS, fare doppio clic su di **progetto** nel **Esplora** per aprire la **opzioni progetto**. Passare a **compilazione iOS** e selezionare il tipo di client desiderato nel **HttpClient implementazione** elenco a discesa:

![](ats-images/client01.png "Impostare le opzioni di compilazione iOS")


#### <a name="managed-handler"></a>Gestore gestito

Il gestore gestito è il gestore HttpClient completamente gestito che è stato spedito con le versioni precedenti di xamarin. IOS e il gestore predefinito.

Vantaggi:

- È più compatibile con Microsoft .NET e la versione precedente di Xamarin.

Svantaggi:

- Non è completamente integrato con iOS (ad esempio, è limitata a TLS 1.0).
- È in genere molto più lento rispetto alle API native.
- Richiede più il codice gestito e crea le app di dimensioni maggiori.

#### <a name="cfnetwork-handler"></a>Gestore CFNetwork

Il gestore CFNetwork basata è basato su nativo `CFNetwork` framework.

Vantaggi:

- Usa l'API nativa per migliorare le prestazioni e ridurre le dimensioni dei file eseguibile.
- Aggiunge il supporto per gli standard più recenti, ad esempio TLS 1.2.

Svantaggi:

- Richiede iOS 6 o versione successiva.
- Non è disponibile di watchOS.
- Alcune funzionalità HttpClient e le opzioni non sono disponibili.

#### <a name="nsurlsession-handler"></a>Gestore NSUrlSession

Il gestore NSUrlSession basata è basato su nativo `NSUrlSession` API.

Vantaggi:

- Usa l'API nativa per migliorare le prestazioni e ridurre le dimensioni dei file eseguibile.
- Aggiunge il supporto per gli standard più recenti, ad esempio TLS 1.2.

Svantaggi:

- Richiede iOS 7 o versioni successive.
- Alcune funzionalità HttpClient e le opzioni non sono disponibili. 

## <a name="diagnosing-ats-issues"></a>La diagnosi dei problemi di AT

Quando si tenta di connettersi a internet, direttamente o da una visualizzazione web a iOS 9, si potrebbe ricevere un errore nel formato:

> Sicurezza del trasporto App ha bloccato un carico di risorsa (http://www.-the-blocked-domain.com) HTTP come testo non crittografato perché è non protetto. Eccezioni temporanee possono essere configurate tramite file Info. plist dell'app.

Sicurezza trasporto App (AT) iOS9, garantisce connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app. AT richiede inoltre la comunicazione con il `HTTPS` protocollo e la comunicazione di alto livello dell'API da crittografare con TLS versione 1.2 PFS.

Poiché AT è attivata per impostazione predefinita nelle App per iOS 9 e OS X 10.11 (montagna di El Capitan), tutte le connessioni utilizzando `NSURLConnection`, `CFURL` o `NSURLSession` saranno soggetti a requisiti di sicurezza AT. Se le connessioni non soddisfano questi requisiti, non riuscirà con un'eccezione.

Apple fornisce inoltre il [App di esempio TLSTool](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2) che può essere compilato (o facoltativamente transcodificato Xamarin e c#) e utilizzato per diagnosticare i problemi di AT/TLS. Vedere il [Opting-Out di at](#Opting-Out_of_ATS) sezione riportata di seguito per informazioni su come risolvere il problema.


<a name="config" />

## <a name="configuring-ats-options"></a>Configurazione delle opzioni di AT

È possibile configurare diverse funzionalità di AT impostando i valori delle chiavi specifiche dell'app **Info. plist** file. Le chiavi seguenti sono disponibili per il controllo at (_rientrati per mostrare come sono nidificati_):

```csharp
NSAppTransportSecurity
    NSAllowsArbitraryLoads
    NSAllowsArbitraryLoadsInWebContent
    NSExceptionDomains
    <domain-name-for-exception-as-string>
        NSExceptionMinimumTLSVersion
        NSExceptionRequiresForwardSecrecy
        NSExceptionAllowsInsecureHTTPLoads
        NSRequiresCertificateTransparency
        NSIncludesSubdomains
        NSThirdPartyExceptionMinimumTLSVersion
        NSThirdPartyExceptionRequiresForwardSecrecy
        NSThirdPartyExceptionAllowsInsecureHTTPLoads
```

Ogni chiave è il tipo e il significato seguente:

- **NSAppTransportSecurity** (`Dictionary`) - contiene tutte le chiavi di impostazione e i valori di AT.
- **NSAllowsArbitraryLoads** (`Boolean`) - se `YES` AT verrà disabilitato per qualsiasi dominio **non** elencati in `NSExceptionDomains`. Per i domini elencati, verranno utilizzate le impostazioni di sicurezza specificate.
- **NSAllowsArbitraryLoadsInWebContent** (`Boolean`) - se `YES` consentirà alle pagine web di caricare correttamente durante la protezione di sicurezza di trasporto di Apple (AT) è ancora abilitata per il resto dell'applicazione.
- **NSExceptionDomains** (`Dictionary`)-una raccolta di domini che e le impostazioni di sicurezza che at devono usare per un determinato dominio.
- **< Domain-name-for-exception-as-string >** (`Dictionary`)-una raccolta di eccezioni per un determinato dominio (ad es. `www.xamarin.com`).
- **NSExceptionMinimumTLSVersion** (`String`)-la versione minima di TLS come `TLSv1.0`, `TLSv1.1` o `TLSv1.2` (ovvero il valore predefinito).
- **NSExceptionRequiresForwardSecrecy** (`Boolean`) - se `NO` il dominio non è necessario utilizzare una crittografia a sicurezza diretta. Il valore predefinito è `YES`.
- **NSExceptionAllowsInsecureHTTPLoads** (`Boolean`) - se `NO` (impostazione predefinita) tutte le comunicazioni con il dominio devono essere il `HTTPS` protocollo.
- **NSRequiresCertificateTransparency** (`Boolean`) - se `YES` SSL del dominio Secure Sockets Layer () devono includere dati di trasparenza valido. Il valore predefinito è `NO`.
- **NSIncludesSubdomains** (`Boolean`) - se `YES` queste impostazioni sostituiscono tutti i sottodomini del dominio. Il valore predefinito è `NO`.
- **NSThirdPartyExceptionMinimumTLSVersion** (`String`)-versione di TLS utilizzata quando il dominio è un servizio di terze parti 3rd all'esterno di controllo dello sviluppatore.
- **NSThirdPartyExceptionRequiresForwardSecrecy** (`Boolean`) - se `YES` un dominio di terze parti 3rd richiede PFS.
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** (`Boolean`) - se `YES` l'at consente la comunicazione non sicure con i domini di parti 3rd.

<a name="optout" />

### <a name="opting-out-of-ats"></a>Acconsentito esplicitamente at

Mentre Apple elevata suggerisce l'utilizzo di `HTTPS` base delle protocollo e comunicazione protetta a internet, potrebbe essere volte che questo non è sempre possibile. Ad esempio, se si comunica con un servizio web di terze parti 3rd o si usa internet recapitati annunci nell'app.

Se l'app xamarin. IOS è necessario effettuare una richiesta a un dominio non protetto, le modifiche seguenti all'app **Info. plist** file disabilita le impostazioni predefinite di sicurezza che applica AT per un dominio specificato:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>www.the-domain-name.com</key>
        <dict>
            <key>NSExceptionMinimumTLSVersion</key>
            <string>TLSv1.0</string>
            <key>NSExceptionRequiresForwardSecrecy</key>
            <false/>
            <key>NSExceptionAllowsInsecureHTTPLoads</key>
            <true/>
            <key>NSIncludesSubdomains</key>
            <true/>
        </dict>
    </dict>
</dict>
```

All'interno di Visual Studio per Mac, fare doppio clic sul `Info.plist` file nel **Esplora soluzioni**, passare il **origine** consente di visualizzare e aggiungere le chiavi precedenti:

[ ![](ats-images/ats01.png "La vista di origine del file Info. plist")](ats-images/ats01.png)


Se l'applicazione deve caricare e visualizzare il contenuto web di siti non sicure, è possibile aggiungere le seguenti all'app **Info. plist** file per consentire alle pagine web di caricare correttamente durante la protezione di sicurezza di trasporto di Apple (AT) è ancora abilitata per il resto dell'app:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

Facoltativamente, è possibile apportare le modifiche seguenti all'app **Info. plist** file consente di disabilitare completamente AT per tutti i domini e comunicazione internet:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

All'interno di Visual Studio per Mac, fare doppio clic sul `Info.plist` file nel **Esplora soluzioni**, passare il **origine** consente di visualizzare e aggiungere le chiavi precedenti:

[ ![](ats-images/ats02.png "La vista di origine del file Info. plist")](ats-images/ats02.png)

> [!IMPORTANT]
> **Nota:** se l'applicazione richiede una connessione a un sito Web di non protetta, è necessario **sempre** immettere il dominio come un'eccezione utilizzando `NSExceptionDomains` anziché disattivare completamente l'utilizzo di at `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` deve essere utilizzato solo in situazioni di emergenza estremi.




Nuovamente, la disabilitazione di AT deve _solo_ essere utilizzato come ultima risorsa, se il passaggio a connessioni protette è poco o non disponibile.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo ha introdotto sicurezza trasporto App (AT) e descritto il modo in cui applica a proteggere le comunicazioni con internet. Trattati in primo luogo, le modifiche at richiede per un'app xamarin in esecuzione in iOS 9. Quindi trattati controllo at funzionalità e opzioni. Infine, trattati rifiuto AT in app xamarin. IOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 per gli sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
