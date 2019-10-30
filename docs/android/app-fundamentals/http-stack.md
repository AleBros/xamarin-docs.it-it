---
title: Stack HttpClient e selettore di implementazione SSL/TLS per Android
description: Lo stack HttpClient e i selettori di implementazione SSL/TLS determinano l'implementazione di HttpClient e SSL/TLS che verrà usata dalle app Novell. Android.
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: f9f9b112a083615f9cf1d74d7cf81f5ca4f7901f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019239"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>Stack HttpClient e selettore di implementazione SSL/TLS per Android

Lo stack HttpClient e i selettori di implementazione SSL/TLS determinano l'implementazione di HttpClient e SSL/TLS che verrà usata dalle app Novell. Android.

I progetti devono fare riferimento all'assembly **System .NET. http** .

> [!WARNING]
> **Aprile 2018** : a causa di un aumento dei requisiti di sicurezza, tra cui la conformità PCI, i principali provider di servizi cloud e i server Web dovrebbero smettere di supportare le versioni TLS precedenti a 1,2. Per impostazione predefinita, i progetti Novell creati nelle versioni precedenti di Visual Studio usano le versioni precedenti di TLS.
>
> Per garantire che le app continuino a funzionare con questi server e servizi, **è necessario aggiornare i progetti Novell con le impostazioni `Android HttpClient` e `Native TLS 1.2` illustrate di seguito, quindi ricompilare e ridistribuire le app** agli utenti.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

La configurazione di Novell. Android HttpClient è in **Opzioni progetto > opzioni Android**, quindi fare clic sul pulsante **Opzioni avanzate** .

Queste sono le impostazioni consigliate per il supporto di TLS 1,2:

[![opzioni di Visual Studio per Android](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

La configurazione di Novell. Android HttpClient è in **Opzioni progetto > compila > impostazioni di compilazione Android** e fare clic sulla scheda **generale** .

Queste sono le impostazioni consigliate per il supporto di TLS 1,2:

[Opzioni di![Visual Studio per Mac Android](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>Opzioni di configurazione alternative

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler è il nuovo gestore che delega il codice Java/OS nativo anziché implementare tutti gli elementi nel codice gestito.
**Questa è l'opzione consigliata.**

#### <a name="pros"></a>I professionisti

- Usa l'API nativa per ottenere prestazioni migliori e dimensioni eseguibili più piccole.
- Supporto per gli standard più recenti, ad esempio. TLS 1,2.

#### <a name="cons"></a>Svantaggi

- Richiede Android 4,1 o versione successiva.
- Alcune funzionalità/opzioni di HttpClient non sono disponibili.

### <a name="managed-httpclienthandler"></a>Gestito (HttpClientHandler)

Managed handler è il gestore HttpClient completamente gestito che è stato fornito con le versioni precedenti di Novell. Android.

#### <a name="pros"></a>I professionisti

- Sono le più compatibili (funzionalità) con MS .NET e le versioni precedenti di Novell.

#### <a name="cons"></a>Svantaggi

- Non è completamente integrato con il sistema operativo (ad esempio, limitato a TLS 1,0).
- È in genere molto più lento, ad esempio crittografia) rispetto all'API nativa.
- Richiede codice più gestito, creazione di applicazioni di dimensioni maggiori.

### <a name="choosing-a-handler"></a>Scelta di un gestore

La scelta tra `AndroidClientHandler` e `HttpClientHandler` dipende dalle esigenze dell'applicazione. `AndroidClientHandler` è consigliabile per il supporto della sicurezza più aggiornato, ad esempio.

- È necessario il supporto di TLS 1.2 +.
- L'app è destinata a Android 4,1 (API 16) o versione successiva.
- Per `HttpClient`è necessario il supporto di TLS 1.2 +.
- Non è necessario il supporto di TLS 1.2 + per `WebClient`.

`HttpClientHandler` è una scelta ottimale se è necessario il supporto di TLS 1.2 e versioni successive, ma deve supportare versioni di Android precedenti a Android 4,1. È anche una scelta ottimale se è necessario il supporto di TLS 1.2 + per `WebClient`.

A partire da Novell. Android 8,3, `HttpClientHandler` il valore predefinito è noioso SSL (`btls`) come provider TLS sottostante. Il provider TLS SSL noioso offre i vantaggi seguenti:

- Supporta TLS 1.2 +.
- Supporta tutte le versioni di Android.
- Fornisce supporto TLS 1.2 + per `HttpClient` e `WebClient`.

Lo svantaggio dell'uso di SSL noioso come provider TLS sottostante è la possibilità di aumentare le dimensioni dell'APK risultante (aggiunge circa 1 MB di dimensioni APK aggiuntive per ogni ABI supportato).

A partire da Novell. Android 8,3, il provider TLS predefinito è noioso SSL (`btls`). Se non si vuole usare la crittografia SSL noiosa, è possibile ripristinare l'implementazione SSL gestita cronologicamente impostando la proprietà `$(AndroidTlsProvider)` su `legacy` (per ulteriori informazioni sull'impostazione delle proprietà di compilazione, vedere [processo di compilazione](~/android/deploy-test/building-apps/build-process.md)).

### <a name="programatically-using-androidclienthandler"></a>A livello con `AndroidClientHandler`

Il `Xamarin.Android.Net.AndroidClientHandler` è un'implementazione di `HttpMessageHandler` in modo specifico per Novell. Android.
Le istanze di questa classe utilizzeranno l'implementazione di `java.net.URLConnection` nativa per tutte le connessioni HTTP. In teoria, verrà fornito un aumento delle prestazioni HTTP e delle dimensioni APK più piccole.

Questo frammento di codice è un esempio di come specificare in modo esplicito una singola istanza della classe `HttpClient`:

```csharp
// Android 4.1 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> Il dispositivo Android sottostante deve supportare TLS 1,2 (IE. Android 4,1 e versioni successive. Si noti che il supporto ufficiale per TLS 1,2 è in Android 5.0 +. Tuttavia alcuni dispositivi supportano TLS 1,2 in Android 4.1 +.

## <a name="ssltls-implementation-build-option"></a>Opzione di compilazione implementazione SSL/TLS

Questa opzione del progetto controlla la libreria TLS sottostante che verrà usata da tutte le richieste Web, sia `HttpClient` che `WebRequest`. Per impostazione predefinita, è selezionato TLS 1,2:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![casella combinata implementazione TLS/SSL in Visual Studio](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![casella combinata di implementazione di TLS/SSL in Visual Studio per Mac](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

Esempio:

```csharp
var client = new HttpClient();
```

Se l'implementazione di HttpClient è stata impostata su **Managed** e l'implementazione di TLS è stata impostata sulla modalità **nativa TLS 1.2 +** , l'oggetto `client` utilizzerà automaticamente il `HttpClientHandler` gestito e TLS 1,2 (fornito dalla libreria BoringSSL) per il relativo http richieste.

Tuttavia, se l' **implementazione di HttpClient** è impostata su `AndroidHttpClient`, tutti gli oggetti `HttpClient` utilizzeranno la classe Java sottostante `java.net.URLConnection` e non saranno interessati dal valore di **implementazione di TLS/SSL** . `WebRequest` oggetti utilizzeranno la libreria BoringSSL.

## <a name="other-ways-to-control-ssltls-configuration"></a>Altri modi per controllare la configurazione di SSL/TLS

Esistono tre modi in cui un'applicazione Novell. Android è in grado di controllare le impostazioni TLS:

1. Selezionare l'implementazione di HttpClient e la libreria TLS predefinita nelle opzioni del progetto.
2. A livello con `Xamarin.Android.Net.AndroidClientHandler`.
3. Dichiarare le variabili di ambiente (facoltativo).

Tra le tre scelte, l'approccio consigliato consiste nell'usare le opzioni del progetto Novell. Android per dichiarare i `HttpMessageHandler` predefiniti e TLS per l'intera app. Quindi, se necessario, creare un'istanza di `Xamarin.Android.Net.AndroidClientHandler` oggetti a livello di codice. Queste opzioni sono descritte in precedenza.

La terza opzione &ndash; uso delle variabili di ambiente &ndash; è illustrata di seguito.

### <a name="declare-environment-variables"></a>Dichiarare le variabili di ambiente

Sono disponibili due variabili di ambiente correlate all'uso di TLS in Novell. Android:

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; questa variabile di ambiente dichiara il `HttpMessageHandler` predefinito che verrà usato dall'applicazione. Esempio:

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; questa variabile di ambiente dichiara quale libreria TLS verrà utilizzata, `btls`, `legacy`o `default` (che equivale a omettere questa variabile):

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

Questa variabile di ambiente viene impostata aggiungendo un _file di ambiente_ al progetto. Un file di ambiente è un file di testo normale in formato UNIX con un'azione di compilazione **AndroidEnvironment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Screenshot dell'azione di compilazione AndroidEnvironment in Visual Studio.](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Screenshot dell'azione di compilazione AndroidEnvironment in Visual Studio per Mac.](http-stack-images/tls03-xs.png)

-----

Per altre informazioni sulle variabili di ambiente e su Novell. Android, vedere la guida dell' [ambiente Novell. Android](~/android/deploy-test/environment.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Transport Layer Security (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
