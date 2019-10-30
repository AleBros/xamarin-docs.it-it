---
title: Transport Layer Security (TLS) 1,2
description: Questo documento descrive come abilitare TLS 1,2 per i progetti Novell. iOS, Novell. Android e Novell. Mac. Viene illustrato come eseguire questa operazione sia in Visual Studio 2019 che in Visual Studio per Mac.
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: 8999efc3e954f4917e6c91a1e325d16e41c48b19
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016677"
---
# <a name="transport-layer-security-tls-12"></a>Transport Layer Security (TLS) 1,2

L'uso della versione più recente di [ _Transport Layer Security_ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) è importante per garantire la protezione delle comunicazioni di rete dell'applicazione.

> [!WARNING]
> **Aprile 2018** : a causa di un aumento dei requisiti di sicurezza, tra cui la conformità PCI, i principali provider di servizi cloud e i server Web dovrebbero smettere di supportare le versioni TLS precedenti a 1,2. Per impostazione predefinita, i progetti Novell creati nelle versioni precedenti di Visual Studio usano le versioni precedenti di TLS.
>
> Per garantire che le app continuino a funzionare con questi server e servizi, **è necessario aggiornare i progetti Novell in modo da usare le impostazioni seguenti, quindi ricompilare e ridistribuire le app** agli utenti.

È necessario che i progetti facciano riferimento all'assembly **System .NET. http** e siano configurati come illustrato di seguito.

## <a name="update-xamarinandroid-to-tls-12"></a>Aggiornare Novell. Android a TLS 1,2

Aggiornare le opzioni di implementazione di **HttpClient** e **SSL/TLS** per abilitare la sicurezza di TLS 1,2.

> [!NOTE]
> Richiede Android 5,0 o versione successiva.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Queste impostazioni sono disponibili nelle **proprietà del progetto > opzioni Android** e quindi facendo clic sul pulsante **Avanzate** :

[![configurare HttpClient e TLS in Visual Studio](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Queste impostazioni sono disponibili in **Opzioni progetto > compila > scheda compilazione Android** :

[![configurare HttpClient e TLS in Visual Studio per Mac](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-xamarinios-to-tls-12"></a>Aggiornare Novell. iOS a TLS 1,2

Aggiornare l'opzione di **implementazione di HttpClient** per abilitare la sicurezza di TSL 1,2.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Questa impostazione si trova nelle **proprietà del progetto > Build iOS**:

[![configurare HttpClient e TLS in Visual Studio](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Questa impostazione è disponibile in **Opzioni progetto > compila > scheda compilazione iOS** :

[![configurare HttpClient in Visual Studio per Mac](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-xamarinmac-to-tls-12"></a>Aggiornare Novell. Mac a TLS 1,2

In Visual Studio per Mac, per abilitare TLS 1,2 in un'app Novell. Mac, aggiornare l'opzione di **implementazione di HttpClient** in **Opzioni progetto > Compila > compilazione Mac**:

[![configurare HttpClient in Visual Studio per Mac](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

> [!WARNING]
> La prossima versione di Xamarin.Mac 4.8 supporterà solo macOS 10.9 o versione successiva.
> Le versioni precedenti di Xamarin.Mac supportavano macOS 10.7 o versione successiva, ma queste versioni precedenti di macOS non dispongono di un'infrastruttura TLS sufficiente per il supporto di TLS 1.2. Per scegliere come destinazione macOS 10.7 o macOS 10.8, usare Xamarin.Mac 4.6 o versioni precedenti.

## <a name="alternative-configuration-options"></a>Opzioni di configurazione alternative

Questa sezione illustra le alternative alle configurazioni supportate da TLS 1,2 illustrate in precedenza.
Gli sviluppatori di applicazioni devono prendere in considerazione queste alternative solo se comprendono i rischi derivanti dall'uso di diversi livelli di supporto TLS.

### <a name="httpclient-implementation"></a>Implementazione di HttpClient

Gli sviluppatori di Novell hanno sempre potuto usare le classi di rete native nel codice, ma esiste anche un'opzione che determina quale stack di rete viene usato dalle classi `HttpClient`. Questo fornisce un'API .NET familiare con i vantaggi della velocità e della sicurezza della piattaforma nativa.

È possibile scegliere:

- **Stack gestito** : funzionalità di rete fornita da mono o
- **Native stack** : varie API di rete fornite dalle piattaforme sottostanti (Android, iOS o MacOS).

Lo stack gestito fornisce il livello più elevato di compatibilità con il codice .NET esistente, tuttavia può essere più lento e produrre dimensioni eseguibili più grandi.

Le opzioni native possono essere più veloci e avere una sicurezza migliore (incluso TLS 1,2), ma potrebbero non fornire tutte le funzionalità e le opzioni della classe `HttpClient`.

### <a name="ssltls-implementation-android"></a>Implementazione di SSL/TLS (Android)

Le opzioni del progetto Android consentono inoltre di scegliere quale implementazione SSL/TLS supportare:

- **Mono/gestiti** -TLS 1,1 in Android
- **Nativo** : TLS 1,2 in Android.

Per impostazione predefinita, i nuovi progetti Novell sono l'implementazione nativa che supporta TLS 1,2 (scelta consigliata per tutti i progetti). Tuttavia, se necessario, è possibile tornare al codice gestito per motivi di compatibilità.

> [!IMPORTANT]
> L'opzione **mono/Managed** è stata [rimossa dalle opzioni del progetto iOS e Mac](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_10/xamarin.ios_10.8.md) .
>
> L'opzione native viene sempre usata nelle piattaforme iOS e Mac.

## <a name="platform-specific-details"></a>Dettagli specifici della piattaforma

Il riepilogo precedente illustra le impostazioni a livello di progetto per l'implementazione di HttpClient e SSL/TLS nei progetti Novell. L'implementazione di HttpClient può essere impostata anche in modo dinamico nel codice. Per ulteriori informazioni, fare riferimento a queste guide specifiche della piattaforma:

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS e Mac**](~/cross-platform/macios/http-stack.md)

## <a name="summary"></a>Riepilogo

Le applicazioni devono usare Transport Layer Security (TLS) 1,2, laddove possibile.
È necessario aggiornare le impostazioni nelle applicazioni esistenti seguendo le istruzioni riportate in questo articolo, quindi ricompilare e ridistribuire i clienti.

## <a name="related-links"></a>Collegamenti correlati

- [ATS (App Transport Security)](~/ios/app-fundamentals/ats.md)
- [Ambiente Novell. Android](~/android/deploy-test/environment.md)
- [Ciclo Novell 9 (febbraio 2017)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipedia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Note sulla versione di mono 4,8-supporto di TLS 1,2](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [Spiegazione di HttpClient, HttpClientHandler e WebRequestHandler](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System .NET. HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)
- [System .NET. HttpClientHandler](https://msdn.microsoft.com/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System .NET. HttpMessageHandler](https://msdn.microsoft.com/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System .NET. HttpWebRequest](https://msdn.microsoft.com/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System .NET. WebClient](https://msdn.microsoft.com/library/system.net.webclient(v=vs.110).aspx)
- [System .NET. WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [Java. NET. URLConnection](https://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation. CFNetwork](xref:CoreFoundation.CFNetwork)
- [Foundation. NSUrlConnection](xref:Foundation.NSUrlConnection)
- [System .NET. WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [Client HTTP (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/httpclient/)
