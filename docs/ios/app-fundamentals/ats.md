---
title: Sicurezza del trasporto delle app in Novell. iOS
description: Sicurezza del trasporto app impone connessioni sicure tra le risorse Internet, ad esempio il server back-end dell'app, e l'app.
ms.prod: xamarin
ms.assetid: F8C5E444-2D05-4D9B-A2EF-EB052CD6F007
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: 67fbd3fb7fb9c7bf1e326404d0d63bc42a3fd8ed
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227674"
---
# <a name="app-transport-security-in-xamarinios"></a>Sicurezza del trasporto delle app in Novell. iOS

_Sicurezza del trasporto app impone connessioni sicure tra le risorse Internet, ad esempio il server back-end dell'app, e l'app._

In questo articolo vengono introdotte le modifiche di sicurezza applicate dalla sicurezza del trasporto app in un'app iOS 9 e il [significato per i progetti Novell. iOS](#xamarinsupport), che includerà le [Opzioni di configurazione di ATS](#config) e verrà illustrato come [rifiutare esplicitamente ATS](#optout) Se necessario, ATS. Poiché ATS è abilitato per impostazione predefinita, eventuali connessioni Internet non sicure genereranno un'eccezione nelle app iOS 9 (a meno che non sia stata consentita in modo esplicito).


## <a name="about-app-transport-security"></a>Informazioni sulla sicurezza del trasporto app

Come indicato in precedenza, ATS garantisce che tutte le comunicazioni Internet in iOS 9 e OS X El Capitan siano conformi alle procedure consigliate per la connessione sicura, impedendo in tal modo la divulgazione accidentale di informazioni riservate direttamente tramite l'app o una libreria utilizzo.

Per le app esistenti, implementare `HTTPS` il protocollo quando possibile. Per le nuove app Novell. iOS, è consigliabile `HTTPS` usare esclusivamente per la comunicazione con le risorse Internet. Inoltre, la comunicazione API di alto livello deve essere crittografata usando TLS versione 1,2 con segretezza diretta.

Tutte le connessioni effettuate con [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) o [NSUrlSession](xref:Foundation.NSUrlSession) utilizzeranno ATS per impostazione predefinita nelle app compilate per iOS 9 e OS X 10,11 (El Capitan).

## <a name="default-ats-behavior"></a>Comportamento ATS predefinito

Poiché ATS è abilitato per impostazione predefinita nelle app compilate per iOS 9 e OS X 10,11 (El Capitan), tutte le connessioni che usano [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) o [NSUrlSession](xref:Foundation.NSUrlSession) saranno soggette ai requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, avranno esito negativo con un'eccezione.

### <a name="ats-connection-requirements"></a>Requisiti di connessione ATS

ATS applicherà i requisiti seguenti per tutte le connessioni Internet:

- Tutte le crittografie della connessione devono utilizzare la segretezza diretta. Vedere l'elenco di crittografie accettate di seguito.
- Il protocollo Transport Layer Security (TLS) deve essere 1,2 o versione successiva.
- Per tutti i certificati è necessario usare almeno un'impronta digitale SHA256 con una chiave RSA di 2048 bit o superiore oppure una chiave di tipo 256 bit o una curva ellittica (ECC).

Anche in questo caso, poiché ATS è abilitato per impostazione predefinita in iOS 9, qualsiasi tentativo di creare una connessione che non soddisfi questi requisiti comporterà la generazione di un'eccezione.

<a name="ATS-Compatible-Ciphers" />

### <a name="ats-compatible-ciphers"></a>Crittografie compatibili con ATS

Il seguente tipo di crittografia in base alla segretezza viene accettato dalle comunicazioni Internet protette da ATS:

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

Per ulteriori informazioni sull'utilizzo delle classi di comunicazione Internet iOS, vedere la pagina relativa al riferimento alla [classe NSURLConnection](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html#//apple_ref/doc/uid/TP40003755)di Apple, al [riferimento CFURL](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/uid/20001206) o al [riferimento alla classe NSURLSession](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/index.html#//apple_ref/doc/uid/TP40013435).

<a name="xamarinsupport" />

## <a name="supporting-ats-in-xamarinios"></a>Supporto di ATS in Novell. iOS

Poiché ATS è abilitato per impostazione predefinita in iOS 9 e OS X El Capitan, se l'app Novell. iOS o una raccolta o un servizio che usa effettua la connessione a Internet, è necessario eseguire un'azione o le connessioni comporteranno la generazione di un'eccezione.

Per un'app esistente, Apple suggerisce di supportare il `HTTPS` protocollo il prima possibile. Se non si è in grado di connettersi a un servizio Web di terze parti che `HTTPS` non supporta o `HTTPS` se il supporto non è pratico, è possibile rifiutare esplicitamente ATS. Per altri dettagli, vedere la sezione relativa alla disattivazione [di ATS di](#optout) seguito.

Per una nuova app Novell. iOS, è consigliabile usare `HTTPS` esclusivamente per la comunicazione con le risorse Internet. Anche in questo caso, è possibile che si verifichino situazioni, ad esempio l'uso di un servizio Web di terze parti, e che sia necessario rifiutare esplicitamente ATS.

Inoltre, ATS impone la crittografia delle comunicazioni API di alto livello usando TLS versione 1,2 con segretezza diretta. Per altri dettagli, vedere le sezioni [relative ai requisiti di connessione ATS](#ats-connection-requirements) e alle [crittografie compatibili con ATS](#ats-compatible-ciphers) .

Sebbene l'utente non abbia familiarità con TLS ([Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)), è il successore di SSL ([Secure Socket Layer](https://en.wikipedia.org/wiki/Transport_Layer_Security)) e fornisce una raccolta di protocolli crittografici per applicare la sicurezza sulle connessioni di rete.

Il livello TLS è controllato dal servizio Web che si sta utilizzando ed è quindi esterno al controllo dell'app. `HttpClient` Sia`ModernHttpClient` che devono usare automaticamente il livello più alto della crittografia TLS supportata dal server.

A seconda del server a cui si sta parlando, soprattutto se si tratta di un servizio di terze parti, potrebbe essere necessario disabilitare la riservatezza in avanti o selezionare un livello TLS più basso. Per altri dettagli, vedere la sezione relativa alla [configurazione delle opzioni ATS](#configuring-ats-options) di seguito.

> [!IMPORTANT]
> La sicurezza del trasporto delle app non si applica alle app Novell che usano **implementazioni HTTPClient gestite**. Si applica alle connessioni usando solo **implementazioni CFNetwork HTTPClient** o solo **implementazioni NSURLSession HTTPClient** .

### <a name="setting-the-httpclient-implementation"></a>Impostazione dell'implementazione di HTTPClient

Per impostare l'implementazione di HTTPClient usata da un'app iOS, fare doppio clic sul **progetto** nella **Esplora soluzioni** per aprire le **Opzioni del progetto**. Passare a **compilazione iOS** e selezionare il tipo di client desiderato nell'elenco a discesa **implementazione di HttpClient** :

![](ats-images/client01.png "Impostazione delle opzioni di compilazione iOS")


#### <a name="managed-handler"></a>Gestore gestito

Il gestore gestito è il gestore HttpClient completamente gestito che è stato fornito con le versioni precedenti di Novell. iOS ed è il gestore predefinito.

I professionisti

- È il più compatibile con Microsoft .NET e la versione precedente di Novell.

Svantaggi

- Non è completamente integrato con iOS (ad esempio, è limitato a TLS 1,0).
- È in genere molto più lento rispetto alle API native.
- Richiede codice più gestito e crea app di dimensioni maggiori.

#### <a name="cfnetwork-handler"></a>Gestore CFNetwork

Il gestore basato su CFNetwork è basato sul Framework `CFNetwork` nativo.

I professionisti

- Usa l'API nativa per ottenere prestazioni migliori e dimensioni eseguibili più piccole.
- Aggiunge il supporto per gli standard più recenti, ad esempio TLS 1,2.

Svantaggi

- Richiede iOS 6 o versione successiva.
- Non disponibile per watchos.
- Alcune funzionalità e opzioni di HttpClient non sono disponibili.

#### <a name="nsurlsession-handler"></a>Gestore NSUrlSession

Il gestore basato su NSUrlSession è basato sull'API `NSUrlSession` nativa.

I professionisti

- Usa l'API nativa per ottenere prestazioni migliori e dimensioni eseguibili più piccole.
- Aggiunge il supporto per gli standard più recenti, ad esempio TLS 1,2.

Svantaggi

- Richiede iOS 7 o versione successiva.
- Alcune funzionalità e opzioni di HttpClient non sono disponibili.

## <a name="diagnosing-ats-issues"></a>Diagnosi dei problemi ATS

Quando si tenta di connettersi a Internet, direttamente o da una visualizzazione Web in iOS 9, è possibile che venga visualizzato un errore nel formato seguente:

> La sicurezza del trasporto app ha bloccato un http http://www.-the-blocked-domain.com) non crittografato (caricamento delle risorse perché non è protetto. Le eccezioni temporanee possono essere configurate tramite il file INFO. plist dell'app.

In iOS9, la sicurezza del trasporto app (ATS) applica connessioni sicure tra le risorse Internet, ad esempio il server back-end dell'app, e l'app. Inoltre, ATS richiede la comunicazione con `HTTPS` il protocollo e la comunicazione API di alto livello per la crittografia usando TLS versione 1,2 con la riservatezza in futuro.

Poiché ATS è abilitato per impostazione predefinita nelle app compilate per iOS 9 e OS X 10,11 (El Capitan), `NSURLConnection`tutte `CFURL` le `NSURLSession` connessioni che usano o saranno soggette ai requisiti di sicurezza di ATS. Se le connessioni non soddisfano questi requisiti, avranno esito negativo con un'eccezione.

Apple fornisce anche l' [app di esempio TLSTool](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2) che può essere compilata (o eventualmente transcodificata in C#Novell e) e usata per diagnosticare i problemi di ATS/TLS. Per informazioni su come risolvere il problema, vedere la sezione relativa alla disattivazione [di ATS riportata di](#optout) seguito.


<a name="config" />

## <a name="configuring-ats-options"></a>Configurazione delle opzioni ATS

È possibile configurare diverse funzionalità di ATS impostando i valori per chiavi specifiche nel file **info. plist** dell'app. Sono disponibili le chiavi seguenti per il controllo di ATS (rientrato_per mostrare come sono annidate_):

```
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

Ogni chiave presenta il tipo e il significato seguenti:

- **NSAppTransportSecurity** (`Dictionary`): Contiene tutte le chiavi e i valori di impostazione per ATS.
- **NSAllowsArbitraryLoads** (`Boolean`)-Se `YES` ATS verrà disabilitato per tutti i domini **non** elencati `NSExceptionDomains`in. Per i domini elencati, verranno usate le impostazioni di sicurezza specificate.
- **NSAllowsArbitraryLoadsInWebContent** (`Boolean`)-Se `YES` consentirà il caricamento corretto delle pagine Web mentre la protezione di Apple Transport Security (ATS) è ancora abilitata per il resto dell'app.
- **NSExceptionDomains** (`Dictionary`): Raccolta di domini che e le impostazioni di sicurezza che devono essere utilizzate da ATS per un determinato dominio.
- `Dictionary`  **Domain-Name-for-exception-As-String>()-unaraccoltadieccezioniperundeterminato\<** dominio (ad esempio, `www.xamarin.com`).
- **NSExceptionMinimumTLSVersion** (`String`): Versione minima `TLSv1.0`di TLS come `TLSv1.1` o `TLSv1.2` (impostazione predefinita).
- **NSExceptionRequiresForwardSecrecy** (`Boolean`)-Se `NO` per il dominio non è necessario utilizzare una crittografia con sicurezza in diretta. Il valore predefinito è `YES`.
- **NSExceptionAllowsInsecureHTTPLoads** (`Boolean`)-Se `NO` (impostazione predefinita) tutte le comunicazioni con il `HTTPS` dominio devono essere presenti nel protocollo.
- **NSRequiresCertificateTransparency** (`Boolean`): Se `YES` il Secure Sockets Layer del dominio (SSL) deve includere dati di trasparenza validi. Il valore predefinito è `NO`.
- **NSIncludesSubdomains** (`Boolean`)-Se `YES` queste impostazioni eseguono l'override di tutti i sottodomini del dominio. Il valore predefinito è `NO`.
- **NSThirdPartyExceptionMinimumTLSVersion** (`String`): Versione di TLS usata quando il dominio è un servizio di terze parti all'esterno del controllo dello sviluppatore.
- **NSThirdPartyExceptionRequiresForwardSecrecy** (`Boolean`)-Se `YES` un dominio di terze parti richiede la riservatezza in futuro.
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** (`Boolean`)-Se `YES` ATS consente la comunicazione non protetta con domini di terze parti.

<a name="optout" />

### <a name="opting-out-of-ats"></a>Esclusione di ATS

Anche se Apple consiglia di usare `HTTPS` il protocollo e di proteggere le comunicazioni con le informazioni basate su Internet, è possibile che non sia sempre possibile. Ad esempio, se si sta comunicando con un servizio Web di terze parti o usando annunci distribuiti tramite Internet nell'app.

Se l'app Novell. iOS deve effettuare una richiesta a un dominio non sicuro, le seguenti modifiche al file **info. plist** dell'app disabilitano le impostazioni predefinite di sicurezza applicate da ATS per un determinato dominio:

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

All'interno Visual Studio per Mac fare doppio clic sul `Info.plist` file nella **Esplora soluzioni**, passare alla visualizzazione **origine** e aggiungere le chiavi indicate sopra:

[![](ats-images/ats01.png "Visualizzazione origine del file INFO. plist")](ats-images/ats01.png#lightbox)


Se l'app deve caricare e visualizzare contenuto Web da siti non protetti, aggiungere il codice seguente al file **info. plist** dell'app per consentire il caricamento corretto delle pagine Web mentre la protezione di Apple Transport Security (ATS) è ancora abilitata per il resto dell'app:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

Facoltativamente, è possibile apportare le seguenti modifiche al file **info. plist** dell'app per disabilitare completamente ATS per tutti i domini e la comunicazione Internet:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

All'interno Visual Studio per Mac fare doppio clic sul `Info.plist` file nella **Esplora soluzioni**, passare alla visualizzazione **origine** e aggiungere le chiavi indicate sopra:

[![](ats-images/ats02.png "Visualizzazione origine del file INFO. plist")](ats-images/ats02.png#lightbox)

> [!IMPORTANT]
> Se l'applicazione richiede una connessione a un sito Web non sicuro, è **sempre** necessario immettere il dominio come eccezione usando `NSExceptionDomains` invece di disattivare completamente ATS usando. `NSAllowsArbitraryLoads` `NSAllowsArbitraryLoads` deve essere utilizzato solo in situazioni di emergenza estreme.




Anche in questo caso, la disabilitazione di ATS dovrebbe essere utilizzata _solo_ come ultima risorsa, se il trasferimento a connessioni protette non è disponibile o non è pratico.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stata introdotta la sicurezza del trasporto app (ATS) e viene descritta la modalità di applicazione delle comunicazioni sicure con Internet. Per prima cosa, abbiamo trattato le modifiche che ATS richiede per un'app Novell. iOS in esecuzione in iOS 9. Il controllo delle funzionalità e delle opzioni di ATS è stato analizzato. Infine, abbiamo trattato di rifiutare esplicitamente ATS nell'app Novell. iOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 per sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
