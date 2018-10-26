---
title: Sicurezza del trasporto di App di xamarin. IOS
description: App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app.
ms.prod: xamarin
ms.assetid: F8C5E444-2D05-4D9B-A2EF-EB052CD6F007
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: 0645b326576a68c97479bc5b59aabaa104f87ae2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114262"
---
# <a name="app-transport-security-in-xamarinios"></a>Sicurezza del trasporto di App di xamarin. IOS

_App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app._

Questo articolo verrà introdotte le modifiche di sicurezza che applica la sicurezza del trasporto di App in un'app per iOS 9 e [conseguenze per i progetti xamarin. IOS](#xamarinsupport), che verrà applicata la [opzioni di configurazione ATS](#config) e che verrà applicata come [rifiutare esplicitamente di ATS](#optout) ATS se necessario. Poiché ATS è abilitato per impostazione predefinita, tutte le connessioni internet non sicuri genererà un'eccezione nelle app di iOS 9 (a meno che non è stata autorizzata in modo esplicito,).


## <a name="about-app-transport-security"></a>Sulla sicurezza del trasporto di App

Come indicato in precedenza, ATS garantisce che tutte le comunicazioni internet in iOS 9 e OS X El Capitan siano conformi per proteggere la connessione le procedure consigliate, evitando la diffusione accidentale di informazioni riservate direttamente tramite l'app o una libreria che è utilizzato.

Per le app esistenti, implementare il `HTTPS` protocollo laddove possibile. Per le nuove app xamarin. IOS, è consigliabile usare `HTTPS` esclusivamente durante la comunicazione con le risorse internet. Inoltre, la comunicazione di alto livello dell'API deve essere crittografata usando TLS versione 1.2 con PFS.

Qualsiasi connessione effettuata con [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/), [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/) oppure [NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/) utilizzeranno ATS per impostazione predefinita nelle App per iOS 9 e OS X 10.11 (El Capitan).

## <a name="default-ats-behavior"></a>Comportamento predefinito ATS

Perché è abilitata per impostazione predefinita nelle App per iOS 9 e OS X 10.11 (El Capitan), tutte le connessioni usando la ATS [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/), [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/) oppure [NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/) sarà soggetto a Requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.

### <a name="ats-connection-requirements"></a>Requisiti di connessione ATS

ATS imporrà i seguenti requisiti per tutte le connessioni internet:

- Tutte le crittografie a connessione deve usare PFS. Visualizzare l'elenco di crittografie accettate riportato di seguito.
- Il protocollo Transport Layer Security (TLS) deve essere della versione 1.2 o successiva.
- Almeno un'impronta digitale SHA256 con un a 2048 bit o maggiore chiave RSA, o un a 256 bit o una chiave maggiore Elliptic Curve (ECC) deve essere usata per tutti i certificati.

Anche in questo caso, poiché ATS è abilitata per impostazione predefinita in iOS 9, qualsiasi tentativo di stabilire una connessione che non soddisfa questi requisiti comporterà un'eccezione generata. 

<a name="ATS-Compatible-Ciphers" />

### <a name="ats-compatible-ciphers"></a>ATS crittografie compatibili

Il seguente tipo di crittografia PFS vengono accettati da ATS protette le comunicazioni internet:

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

Per altre informazioni sull'uso delle classi di comunicazione internet di iOS, vedere di Apple [riferimento alla classe NSURLConnection](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html#//apple_ref/doc/uid/TP40003755), [riferimento CFURL](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/uid/20001206) o [riferimento alla classe di NSURLSession ](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/index.html#//apple_ref/doc/uid/TP40013435).

<a name="xamarinsupport" />

## <a name="supporting-ats-in-xamarinios"></a>Supporto ATS in xamarin. IOS

Poiché ATS è abilitato per impostazione predefinita in iOS 9 e OS X El Capitan, se l'app xamarin. IOS o qualsiasi libreria o il servizio che usa esegue connessione a internet, è necessario eseguire un'azione o le connessioni comporterà un'eccezione generata.

Per un'app esistente, suggerite da Apple supportare il `HTTPS` protocollo appena possibile. Se non è una possibile in quanto ci si connette a un 3rd un'entità servizio web che non supporta `HTTPS` o se si supporta `HTTPS` potrebbe risultare poco pratico, è possibile rifiutare esplicitamente di ATS. Vedere le [ATS Opting esplicitamente](#optout) sezione di seguito per altri dettagli.

Per una nuova app xamarin. IOS, è consigliabile usare `HTTPS` esclusivamente durante la comunicazione con le risorse internet. Anche in questo caso, potrebbero esserci situazioni (ad esempio, tramite un servizio web di terze parti 3rd) in cui questo non è possibile e occorre rifiutare esplicitamente di ATS.

Inoltre, ATS impone la comunicazione dell'API ad alto livello viene crittografata utilizzando TLS versione 1.2 con PFS. Vedere le [requisiti di connessione ATS](#ATS-Connection-Requirements) e [crittografie compatibili ATS](#ATS-Compatible-Ciphers) sezioni precedenti per altri dettagli.

Anche se non è familiare con TLS ([Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)) è il successore di SSL ([Secure Socket Layer](https://en.wikipedia.org/wiki/Transport_Layer_Security)) e fornisce una raccolta di protocolli di crittografia per applicare la protezione tramite connessioni di rete.

Il livello di TLS è controllato dal servizio web che utilizzano e quindi di fuori di controllo dell'app. Sia la `HttpClient` e il `ModernHttpClient` automaticamente deve usare il massimo livello di crittografia TLS supportata dal server.

A seconda del server che si sta parlando (specialmente se si tratta di un servizio di terze parti 3rd), potrebbe essere necessario disabilitare PFS o selezionare un livello inferiore di TLS. Vedere le [configurazione delle opzioni ATS](#Configuring-ATS-Options) sezione di seguito per altri dettagli.

> [!IMPORTANT]
> Sicurezza del trasporto di App non è valida per le app Xamarin usando **HTTPClient gestiti implementazioni**. Si applica alle connessioni tramite CFNetwork **implementazioni HTTPClient** oppure **implementazioni NSURLSession HTTPClient** solo.

### <a name="setting-the-httpclient-implementation"></a>Impostando l'implementazione di HTTPClient

Per impostare l'implementazione di HTTPClient usato da un'app per iOS, fare doppio clic il **progetto** nel **Esplora soluzioni** per aprire la **opzioni progetto**. Passare a **compilazione iOS** e selezionare il tipo di client desiderato sotto il **implementazione di HttpClient** elenco a discesa:

![](ats-images/client01.png "Impostazione opzioni di compilazione iOS")


#### <a name="managed-handler"></a>Gestore gestito

Il gestore gestito è il gestore di HttpClient completamente gestito che è stato spedito con le versioni precedenti di xamarin. IOS e il gestore predefinito.

Vantaggi:

- È più compatibile con Microsoft .NET e versione precedente di Xamarin.

Svantaggi:

- Non è completamente integrato con iOS (ad esempio, è limitata a TLS 1.0).
- È in genere molto più lento rispetto alle API native.
- Richiede più il codice gestito e crea app di grandi dimensioni.

#### <a name="cfnetwork-handler"></a>Gestore CFNetwork

Il gestore CFNetwork basato su base nativo `CFNetwork` framework.

Vantaggi:

- Usa API native per migliorare le prestazioni e l'eseguibile di dimensioni inferiori.
- Aggiunge il supporto per gli standard più recenti, ad esempio TLS 1.2.

Svantaggi:

- Richiede iOS 6 e versioni successive.
- Non è disponibile di watchOS.
- Non sono disponibili alcune opzioni e funzionalità HttpClient.

#### <a name="nsurlsession-handler"></a>Gestore di NSUrlSession

Il gestore di NSUrlSession basato su base nativo `NSUrlSession` API.

Vantaggi:

- Usa API native per migliorare le prestazioni e l'eseguibile di dimensioni inferiori.
- Aggiunge il supporto per gli standard più recenti, ad esempio TLS 1.2.

Svantaggi:

- Richiede iOS 7 o versione successiva.
- Non sono disponibili alcune opzioni e funzionalità HttpClient. 

## <a name="diagnosing-ats-issues"></a>Diagnosi dei problemi di ATS

Quando si prova a connettersi a internet, direttamente o da una visualizzazione web in iOS 9, è possibile ottenere un errore nel formato:

> Sicurezza del trasporto App ha bloccato un HTTP come testo non crittografato (http://www.-the-blocked-domain.com) carico delle risorse perché è non sicuro. Eccezioni temporanee possono essere configurate tramite file Info. plist dell'app.

In iOS9, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app. ATS richiede inoltre la comunicazione con il `HTTPS` protocollo e la comunicazione di API ad alto livello viene crittografata utilizzando TLS versione 1.2 con PFS.

Perché è abilitata per impostazione predefinita nelle App per iOS 9 e OS X 10.11 (El Capitan), tutte le connessioni usando la ATS `NSURLConnection`, `CFURL` o `NSURLSession` saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.

Apple offre anche il [App di esempio TLSTool](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2) che può essere compilato (o facoltativamente transcodificati a Xamarin e C#) e usato per diagnosticare i problemi di ATS/TLS. Vedere le [ATS Opting esplicitamente](#optout) sezione per informazioni su come risolvere questo problema.


<a name="config" />

## <a name="configuring-ats-options"></a>Configurazione delle opzioni di ATS

È possibile configurare le diverse funzionalità di ATS impostando i valori delle chiavi specifiche dell'app **Info. plist** file. Le chiavi seguenti sono disponibili per il controllo ATS (_rientrati per mostrare come annidamento_):

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

- **NSAppTransportSecurity** (`Dictionary`): contiene tutte le chiavi di impostazione e i valori per ATS.
- **NSAllowsArbitraryLoads** (`Boolean`): se l'opzione `YES` ATS verrà disabilitato per qualsiasi dominio **non** racchiusi `NSExceptionDomains`. Per i domini elencati, le impostazioni di sicurezza specificate verranno utilizzate.
- **NSAllowsArbitraryLoadsInWebContent** (`Boolean`): se `YES` consentirà alle pagine web di caricare correttamente mentre protezione Apple Transport Security (ATS) è ancora abilitata per il resto dell'app.
- **NSExceptionDomains** (`Dictionary`)-una raccolta di domini che e le impostazioni di sicurezza che devono usare ATS per un determinato dominio.
- **< Domain-name-for-exception-as-string >** (`Dictionary`)-un insieme di eccezioni per un determinato dominio (ad es. `www.xamarin.com`).
- **NSExceptionMinimumTLSVersion** (`String`)-la versione minima TLS come `TLSv1.0`, `TLSv1.1` o `TLSv1.2` (ovvero l'impostazione predefinita).
- **NSExceptionRequiresForwardSecrecy** (`Boolean`): se `NO` il dominio non è necessario usare una crittografia a sicurezza diretta. Il valore predefinito è `YES`.
- **NSExceptionAllowsInsecureHTTPLoads** (`Boolean`): se l'opzione `NO` (impostazione predefinita) tutte le comunicazioni con questo dominio devono essere il `HTTPS` protocollo.
- **NSRequiresCertificateTransparency** (`Boolean`): se `YES` Secure Sockets Layer (SSL del dominio) devono includere dati trasparenza valido. Il valore predefinito è `NO`.
- **NSIncludesSubdomains** (`Boolean`): se `YES` queste impostazioni sostituiscono tutti i sottodomini di questo dominio. Il valore predefinito è `NO`.
- **NSThirdPartyExceptionMinimumTLSVersion** (`String`)-versione TLS usata quando il dominio è un servizio di terze parti 3rd di fuori il controllo dello sviluppatore.
- **NSThirdPartyExceptionRequiresForwardSecrecy** (`Boolean`): se `YES` un dominio di terze parti 3rd richiede PFS.
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** (`Boolean`): se `YES` il ATS consentirà la comunicazione non protetta con 3 domini di terze parti.

<a name="optout" />

### <a name="opting-out-of-ats"></a>Rifiutare esplicitamente di ATS

Mentre altamente suggerite da Apple con il `HTTPS` protocollo e la comunicazione sicura alla rete internet basati su informazioni, potrebbero esserci volte in cui ciò non è sempre possibile. Ad esempio, se si comunica con un servizio web di terze parti 3rd o si usa internet recapitati annunci nell'app.

Se l'app xamarin. IOS è necessario effettuare una richiesta a un dominio non protetto, le modifiche seguenti all'app **Info. plist** file disabiliterà le impostazioni di sicurezza predefinite da ATS per un determinato dominio:

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

All'interno di Visual Studio per Mac, fare doppio clic sul `Info.plist` del file nei **Esplora soluzioni**, passare al **origine** consente di visualizzare e aggiungere le chiavi precedenti:

[![](ats-images/ats01.png "La vista di origine del file Info. plist")](ats-images/ats01.png#lightbox)


Se l'app deve caricare e visualizzare il contenuto web di siti non sicuri, aggiungere quanto segue per l'app **Info. plist** file per consentire le pagine web caricare correttamente mentre protezione Apple Transport Security (ATS) è ancora abilitata per il resto dell'app:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

Facoltativamente, è possibile apportare le modifiche seguenti all'app **Info. plist** file completamente disabilitare ATS per tutti i domini e comunicazione internet:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

All'interno di Visual Studio per Mac, fare doppio clic sul `Info.plist` del file nei **Esplora soluzioni**, passare al **origine** consente di visualizzare e aggiungere le chiavi precedenti:

[![](ats-images/ats02.png "La vista di origine del file Info. plist")](ats-images/ats02.png#lightbox)

> [!IMPORTANT]
> Se l'applicazione richiede una connessione a un sito Web non sicura, occorre **sempre** immettere il dominio come un'eccezione utilizzando `NSExceptionDomains` anziché la disattivazione ATS interamente con `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` deve essere utilizzato solo in situazioni di emergenza estreme.




Anche in questo caso, disabilitare ATS dovrebbe _solo_ utilizzabile come ultima risorsa, se il passaggio per stabilire connessioni sicure è non disponibile o poco pratico.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto App Transport Security (ATS) e descritto il modo in cui viene utilizzato per applicare proteggere le comunicazioni con internet. In primo luogo, abbiamo illustrato le modifiche ATS richiede per un'app xamarin. IOS che eseguono iOS 9. Quindi abbiamo trattato opzioni e funzionalità del controllo ATS. Infine, abbiamo illustrato rifiuto esplicito ATS nell'app xamarin. IOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [per gli sviluppatori iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
