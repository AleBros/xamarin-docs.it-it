---
title: Transport Layer Security (TLS)
description: L'abilitazione di TLS 1.2 per i progetti di Xamarin in Android, iOS e Mac
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/10/2017
ms.openlocfilehash: 8b2d0288248f2468e6976ad4f7c46255690116c0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

_L'abilitazione di TLS 1.2 per i progetti di Xamarin in Android, iOS e Mac_

Utilizzando la versione più recente di [ _Transport Layer Security_ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) è importante per garantire la sicurezza delle comunicazioni di rete dell'applicazione.

> [!NOTE]
> Xamarin rilascia dal [2017 febbraio](https://releases.xamarin.com/stable-release-cycle-9/) usano TLS 1.2 nei nuovi progetti per impostazione predefinita.

Supporto di TLS 1.2 è ora disponibile in:

* 4.8 mono (include [supporto di TLS 1.2](http://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support))
* Xamarin.iOS
* Xamarin.Mac
* Xamarin (è necessario Android 5.0 o versioni successive)

Devono fare riferimento a progetti di **System.NET. HTTP** assembly. 

## <a name="updating-to-tls-12"></a>L'aggiornamento di TLS 1.2

Questa sezione illustra alcune delle opzioni di configurazione per la rete in progetti di Xamarin, così da poter aggiornare il _esistente_ App per sfruttare il protocollo più sicuro.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Queste impostazioni sono disponibili **opzioni progetto > Opzioni Android** e quindi fare clic di **avanzate** pulsante: 

[![Configurare HttpClient e TLS in Visual Studio](transport-layer-security-images/properties-vs-sml.png)](transport-layer-security-images/properties-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)
Queste impostazioni sono disponibili **le proprietà del progetto > le opzioni di compilazione > Avanzate** scheda:

[![Configurare HttpClient e TLS in Visual Studio e Xamarin Studio per Mac](transport-layer-security-images/properties-xs-sml.png)](transport-layer-security-images/properties-xs.png#lightbox)

-----


### <a name="httpclient-implementation"></a>Implementazione di HttpClient

Gli sviluppatori di Xamarin sono sempre stato in grado di utilizzare le classi di rete native nel codice, tuttavia è anche un'opzione che determina quali stack di rete viene utilizzata il `HttpClient` classi. Fornisce un'API .NET familiarità con i vantaggi di velocità e la sicurezza della piattaforma nativa.

È possibile scegliere:

- **Dello stack gestito** : la funzionalità di rete fornita Mono, o
- **Stack nativo** : vari servizi di rete le API fornite da piattaforme sottostanti (Android, iOS o Mac OS).

Lo stack gestito fornisce il massimo livello di compatibilità con il codice esistente .NET, tuttavia, può risultare più lenta e provocare l'aumento delle dimensioni del file eseguibile.

Le opzioni native possono essere più veloce e una migliore protezione (inclusi TLS 1.2), ma potrebbe non fornire tutte le funzionalità e opzioni del `HttpClient` classe.


### <a name="ssltls-implementation"></a>Implementazione di SSL/TLS

Le opzioni del progetto consentono anche di scegliere quale implementazione di SSL/TLS per il supporto:

- **Mono/gestito** – TLS 1.1 in Android, TLS 1.0 in iOS e Mac OS.
- **Native** – TLS 1.2 per Android, iOS e Mac OS.

Nuovi progetti di Xamarin per impostazione predefinita l'implementazione nativa che supporti TLS 1.2 (scelta consigliata per tutti i progetti), è tuttavia possibile passare al codice gestito se necessario per motivi di compatibilità.

> [!IMPORTANT]
> Il **Mono/gestito** verrà rimossa una [futuro](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/).
>
> È consigliabile l'opzione nativo.

## <a name="platform-specific-details"></a>Dettagli specifici della piattaforma

Il riepilogo sopra vengono illustrate le impostazioni a livello di progetto per l'implementazione HttpClient e SSL/TLS in progetti di Xamarin. L'implementazione di HttpClient può anche essere impostata in modo dinamico nel codice e nelle versioni iOS sono disponibili due opzioni native da selezionare.

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS e Mac**](~/cross-platform/macios/http-stack.md)


## <a name="summary"></a>Riepilogo

Le applicazioni devono utilizzare Transport Layer Security (TLS) 1.2, laddove possibile.
Nuove app ora per impostazione predefinita questa configurazione, tuttavia potrebbe essere necessario aggiornare le impostazioni di applicazioni esistenti in base alle istruzioni in questo articolo.

## <a name="related-links"></a>Collegamenti correlati

- [ATS (App Transport Security)](~/ios/app-fundamentals/ats.md)
- [Ambiente xamarin](~/android/deploy-test/environment.md)
- [Ciclo di Xamarin 9 (febbraio 2017)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipedia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Note sulla versione 4.8 mono - supporto di TLS 1.2](http://www.mono-project.com/docs/about-monohttps://developer.xamarin.com/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient HttpClientHandler e WebRequestHandler descritto](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/en-us/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/en-us/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/en-us/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/en-us/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/en-us/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/en-us/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](http://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](https://developer.xamarin.com/api/type/CoreFoundation.CFNetwork/)
- [Foundation.NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)
- [System.Net.WebRequest](https://msdn.microsoft.com/en-us/library/system.net.webrequest(v=vs.110).aspx)
- [HTTP Client (esempio)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
