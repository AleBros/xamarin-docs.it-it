---
title: Transport Layer Security (TLS) 1.2
description: L'abilitazione di TLS 1.2 per i progetti di Xamarin in Android, iOS e Mac
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: 8e27801a9feb8cf7ba1534f88479dbf7259c3e85
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="transport-layer-security-tls-12"></a>Transport Layer Security (TLS) 1.2

Utilizzando la versione più recente di [ _Transport Layer Security_ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) è importante per garantire la sicurezza delle comunicazioni di rete dell'applicazione.

> [!WARNING]
> **Aprile, 2018** : a causa di una maggiore sicurezza i requisiti, inclusi la conformità PCI, principali provider di servizi cloud e i server web sono previsti per arrestare il supporto di versioni TLS meno recente 1.2.  Xamarin i progetti creati nelle versioni precedenti di predefinito di Visual Studio per utilizzare le versioni precedenti di TLS.
>
> Per verificare che le app continuano a lavorare con i server e servizi **è necessario aggiornare i progetti di Xamarin per utilizzare le impostazioni seguenti, quindi ricompilare e ridistribuire le app** agli utenti.

Devono fare riferimento a progetti di **System.NET. HTTP** assembly e configurarle come illustrato di seguito.

## <a name="update-android-to-tls-12"></a>Aggiornare Android a TLS 1.2

Aggiornamento di **implementazione HttpClient** e **implementazione di SSL/TLS** opzioni per abilitare la sicurezza TLS 1.2.

> [!NOTE]
> È necessario Android 5.0 o versione successiva.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Queste impostazioni possono essere disponibili **le proprietà del progetto > Opzioni Android** e quindi fare clic sul **avanzate** pulsante:

[![Configurare HttpClient e TLS in Visual Studio](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Queste impostazioni possono essere disponibili **opzioni progetto > Compila > di compilazione Android** scheda:

[![Configurare TLS e HttpClient in Visual Studio per Mac](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-ios-to-tls-12"></a>Aggiornare iOS TLS 1.2

Aggiornamento di **implementazione HttpClient** opzione per abilitare la sicurezza TSL 1.2.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Questa impostazione sono reperibili **le proprietà del progetto > iOS compilazione**:

[![Configurare HttpClient e TLS in Visual Studio](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Questa impostazione sono reperibili **opzioni progetto > Compila > compilazione iOS** scheda:

[![Configurare HttpClient in Visual Studio per Mac](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-macos-to-tls-12-in-visual-studio-for-mac"></a>Aggiornare macOS TLS 1.2 in Visual Studio per Mac

Aggiornamento di **implementazione HttpClient** opzione **opzioni progetto > compilare > Mac compilare** scheda per abilitare la protezione TSL 1.2:

[![Configurare HttpClient in Visual Studio per Mac](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

## <a name="alternative-configuration-options"></a>Opzioni di configurazione alternativo

In questa sezione vengono illustrate le alternative per le configurazioni supportate di TLS 1.2 illustrate in precedenza.
Gli sviluppatori di applicazioni devono considerare solo queste alternative se comprendano i rischi dell'uso di TLS supportano livelli diversi.

### <a name="httpclient-implementation"></a>Implementazione di HttpClient

Gli sviluppatori di Xamarin sono sempre stato in grado di utilizzare le classi di rete native nel codice, tuttavia è anche un'opzione che determina quali stack di rete viene utilizzata il `HttpClient` classi. Fornisce un'API .NET familiarità con i vantaggi di velocità e la sicurezza della piattaforma nativa.

È possibile scegliere:

- **Dello stack gestito** : la funzionalità di rete fornita Mono, o
- **Stack nativo** : vari servizi di rete le API fornite da piattaforme sottostanti (Android, iOS o Mac OS).

Lo stack gestito fornisce il massimo livello di compatibilità con il codice esistente .NET, tuttavia, può risultare più lenta e provocare l'aumento delle dimensioni del file eseguibile.

Le opzioni native possono essere più veloce e una migliore protezione (inclusi TLS 1.2), ma potrebbe non fornire tutte le funzionalità e opzioni del `HttpClient` classe.

### <a name="ssltls-implementation-android"></a>Implementazione di SSL/TLS (Android)

Opzioni progetto Android consentono inoltre di scegliere quale implementazione di SSL/TLS per il supporto:

- **Mono/gestito** – TLS 1.1 in Android
- **Native** – TLS 1.2 in Android.

Nuovi progetti di Xamarin per impostazione predefinita l'implementazione nativa che supporti TLS 1.2 (scelta consigliata per tutti i progetti), è tuttavia possibile passare al codice gestito se necessario per motivi di compatibilità.

> [!IMPORTANT]
> Il **Mono/Managed** opzione è stata [rimosso da iOS e Mac](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/) opzioni del progetto.
>
> L'opzione Native verrà sempre eseguita in piattaforme iOS e Mac.

## <a name="platform-specific-details"></a>Dettagli specifici della piattaforma

Il riepilogo sopra vengono illustrate le impostazioni a livello di progetto per l'implementazione HttpClient e SSL/TLS in progetti di Xamarin. L'implementazione di HttpClient può anche essere impostata in modo dinamico nel codice. Fare riferimento a queste guide specifiche della piattaforma per ulteriori informazioni:

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS e Mac**](~/cross-platform/macios/http-stack.md)


## <a name="summary"></a>Riepilogo

Le applicazioni devono utilizzare Transport Layer Security (TLS) 1.2, laddove possibile.
È necessario aggiornare le impostazioni nelle applicazioni esistenti in base alle istruzioni in questo articolo, quindi ricompilare e distribuire di nuovo ai clienti.

## <a name="related-links"></a>Collegamenti correlati

- [ATS (App Transport Security)](~/ios/app-fundamentals/ats.md)
- [Ambiente xamarin](~/android/deploy-test/environment.md)
- [Ciclo di Xamarin 9 (febbraio 2017)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipedia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Note sulla versione 4.8 mono - supporto di TLS 1.2](http://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient HttpClientHandler e WebRequestHandler descritto](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](http://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](https://developer.xamarin.com/api/type/CoreFoundation.CFNetwork/)
- [Foundation.NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [HTTP Client (esempio)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
