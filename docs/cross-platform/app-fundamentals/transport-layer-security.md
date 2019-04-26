---
title: Transport Layer Security (TLS) 1.2
description: Questo documento descrive come abilitare TLS 1.2 per i progetti xamarin. IOS, xamarin. Android e xamarin. Mac. Viene illustrato come eseguire questa operazione in Visual Studio 2019 e Visual Studio per Mac.
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: 26870ae0e84a84a7b78f7766a8e134ecfc7b223e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304778"
---
# <a name="transport-layer-security-tls-12"></a>Transport Layer Security (TLS) 1.2

Usare la versione più recente di [ _Transport Layer Security_ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) è importante per garantire la sicure delle comunicazioni di rete dell'applicazione.

> [!WARNING]
> **Aprile 2018** : a causa di una maggiore sicurezza i requisiti, incluse la conformità PCI, la maggior parte delle provider di servizi cloud e i server web devono interrompere il supporto per le versioni TLS precedente alla 1.2.  Progetti Xamarin creati nelle versioni precedenti di predefinito di Visual Studio per usare le versioni precedenti di TLS.
>
> Per verificare che le app continuano a funzionare con questi server e servizi **è necessario aggiornare i progetti Xamarin per usare le impostazioni seguenti, quindi ricompilare e ridistribuire le tue app** agli utenti.

Devono fare riferimento a progetti di **System.NET. HTTP** assembly e configurarlo come illustrato di seguito.

## <a name="update-xamarinandroid-to-tls-12"></a>Aggiornamento di xamarin. Android a TLS 1.2

Aggiorna il **implementazione di HttpClient** e **implementazione di SSL/TLS** opzioni per abilitare la sicurezza TLS 1.2.

> [!NOTE]
> È necessario Android 5.0 o versione successiva.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Queste impostazioni sono disponibili **proprietà progetto > Opzioni Android** e fare clic sui **avanzate** pulsante:

[![Configurare TLS e HttpClient in Visual Studio](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Queste impostazioni sono disponibili nel **opzioni progetto > compilazione > compilazione Android** scheda:

[![Configurare TLS e HttpClient in Visual Studio per Mac](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-xamarinios-to-tls-12"></a>Aggiornamento di xamarin. IOS a TLS 1.2

Aggiorna il **implementazione di HttpClient** opzione per abilitare la sicurezza TSL 1.2.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Questa impostazione è disponibile nel **proprietà progetto > compilazione iOS**:

[![Configurare TLS e HttpClient in Visual Studio](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Questa impostazione è disponibile nel **opzioni progetto > compilazione > compilazione iOS** scheda:

[![Configurare HttpClient in Visual Studio per Mac](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-xamarinmac-to-tls-12"></a>Aggiornamento di xamarin. Mac a TLS 1.2

In Visual Studio per Mac, per abilitare TLS 1.2 in un'app xamarin. Mac, aggiornare il **implementazione di HttpClient** opzione **opzioni progetto > compilazione > compilazione Mac**:

[![Configurare HttpClient in Visual Studio per Mac](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

> [!WARNING]
> La prossima versione di Xamarin.Mac 4.8 supporterà solo macOS 10.9 o versione successiva.
> Le versioni precedenti di Xamarin.Mac supportavano macOS 10.7 o versione successiva, ma queste versioni precedenti di macOS non dispongono di un'infrastruttura TLS sufficiente per il supporto di TLS 1.2. Per scegliere come destinazione macOS 10.7 o macOS 10.8, usare Xamarin.Mac 4.6 o versioni precedenti.

## <a name="alternative-configuration-options"></a>Opzioni di configurazione alternativo

In questa sezione illustra le alternative per le configurazioni supportate di TLS 1.2 illustrate in precedenza.
Gli sviluppatori di applicazioni è opportuno considerare solo queste alternative se comprendono i rischi dell'uso di diversi livelli di supporto TLS.

### <a name="httpclient-implementation"></a>Implementazione di HttpClient

Gli sviluppatori di Xamarin sono sempre state in grado di usare le classi di rete native nel proprio codice, tuttavia non vi è un'opzione che determina quali stack di rete viene usata anche per il `HttpClient` classi. Questo fornisce un'API .NET familiare che offre i vantaggi di velocità e sicurezza della piattaforma nativa.

È possibile scegliere:

- **Stack gestito** : la funzionalità di rete fornita da Mono, o
- **Stack nativo** : varie le API fornite da piattaforme sottostanti (Android, iOS o macOS) di rete.

Lo stack gestito fornisce il massimo livello di compatibilità con il codice .NET esistente, ma può essere più lento e comportare l'aumento delle dimensioni del file eseguibile.

Le opzioni native possono essere più veloce e avere una migliore protezione (tra cui TLS 1.2), ma potrebbe non fornire tutte le funzionalità e le opzioni del `HttpClient` classe.

### <a name="ssltls-implementation-android"></a>Implementazione di SSL/TLS (Android)

Le opzioni del progetto Android consentono anche di scegliere quale implementazione di SSL/TLS per supportare:

- **Mono/gestiti** – TLS 1.1 in Android
- **Nativo** – TLS 1.2 in Android.

I nuovi progetti Xamarin per impostazione predefinita l'implementazione nativa che supporti TLS 1.2 (scelta consigliata per tutti i progetti), tuttavia è possibile passare al codice gestito se necessario per motivi di compatibilità.

> [!IMPORTANT]
> Il **gestita/Mono** opzione è stata [rimosse da iOS e Mac](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/) opzioni progetto.
>
> L'opzione nativa viene sempre utilizzato su piattaforme iOS e Mac.

## <a name="platform-specific-details"></a>Dettagli specifici della piattaforma

Il riepilogo precedente illustra le impostazioni a livello di progetto per l'implementazione di HttpClient e SSL/TLS in progetti Xamarin. L'implementazione di HttpClient può anche essere impostata dinamicamente nel codice. Vedere le guide seguenti specifiche della piattaforma per altre informazioni:

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS e Mac**](~/cross-platform/macios/http-stack.md)

## <a name="summary"></a>Riepilogo

Le applicazioni devono utilizzare Transport Layer Security (TLS) 1.2, laddove possibile.
Deve aggiornare le impostazioni nelle applicazioni esistenti seguendo le istruzioni in questo articolo, quindi ricompilare e distribuire di nuovo ai tuoi clienti.

## <a name="related-links"></a>Collegamenti correlati

- [ATS (App Transport Security)](~/ios/app-fundamentals/ats.md)
- [Xamarin.Android Environment](~/android/deploy-test/environment.md)
- [Xamarin ciclo 9 (febbraio 2017)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipedia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Note sulla versione di mono 4.8 - supporto di TLS 1.2](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient e HttpClientHandler WebRequestHandler illustrati](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](https://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](xref:CoreFoundation.CFNetwork)
- [Foundation.NSUrlConnection](xref:Foundation.NSUrlConnection)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [HTTP Client (esempio)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
