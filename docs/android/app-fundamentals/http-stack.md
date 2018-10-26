---
title: Stack HttpClient e selettore dell'implementazione di SSL/TLS per Android
description: I selettori di HttpClient Stack e implementazione di SSL/TLS determinano l'implementazione di HttpClient e SSL/TLS che verrà usato dalle App xamarin. Android.
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: 47c9ddf3f1a61b0ec7e2a8ed993ad665267993fd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114275"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>Stack HttpClient e selettore dell'implementazione di SSL/TLS per Android

I selettori di HttpClient Stack e implementazione di SSL/TLS determinano l'implementazione di HttpClient e SSL/TLS che verrà usato dalle App xamarin. Android.

Devono fare riferimento a progetti di **System.NET. HTTP** assembly.

> [!WARNING]
> **Aprile 2018** : a causa di una maggiore sicurezza i requisiti, incluse la conformità PCI, la maggior parte delle provider di servizi cloud e i server web devono interrompere il supporto per le versioni TLS precedente alla 1.2.  Progetti Xamarin creati nelle versioni precedenti di predefinito di Visual Studio per usare le versioni precedenti di TLS.
>
> Per verificare che le app continuano a funzionare con questi server e servizi di **è necessario aggiornare i progetti Xamarin con il `Android HttpClient` e `Native TLS 1.2` impostazioni mostrate di seguito, quindi ricompilare e ridistribuire le tue app** per il utenti.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

È la configurazione di xamarin. Android HttpClient **opzioni progetto > Opzioni Android**, quindi fare clic sui **opzioni avanzate** pulsante.

Queste sono le impostazioni consigliate per il supporto di TLS 1.2:

[![Opzioni Android di Visual Studio](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

La configurazione di xamarin. Android HttpClient è in **opzioni progetto > compilazione > compilazione Android** le impostazioni e fare clic sui **generali** scheda.

Queste sono le impostazioni consigliate per il supporto di TLS 1.2:

[![Visual Studio per Mac opzioni Android](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>Opzioni di configurazione alternativo

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler è il nuovo gestore che delega a codice nativo di Java/sistema operativo anziché implementare tutti gli elementi nel codice gestito.
**Si tratta dell'opzione consigliata.**

#### <a name="pros"></a>Professionisti

- Usare un'API nativa per migliorare le prestazioni e dimensioni dei file eseguibili più piccole.
- Il supporto per gli standard più recenti, ad es. TLS 1.2.

#### <a name="cons"></a>Svantaggi

- È necessario Android 5.0 o versioni successive.
- Alcune funzionalità HttpClient/opzioni non sono disponibili.

### <a name="managed-httpclienthandler"></a>Gestito (HttpClientHandler)

Gestito è il gestore di HttpClient completamente gestito che è stato spedito con le versioni precedenti di xamarin. Android.

#### <a name="pros"></a>Professionisti

- È più compatibile (caratteristiche) con Microsoft .NET e le versioni precedenti di Xamarin.

#### <a name="cons"></a>Svantaggi

- Non è completamente integrato con il sistema operativo (ad es. limitato a TLS 1.0).
- È in genere molto più lenta (ad es. crittografia dei dati) rispetto all'API nativa.
- Richiede più il codice gestito, la creazione di applicazioni di grandi dimensioni.



### <a name="choosing-a-handler"></a>Scelta di un gestore

La scelta tra `AndroidClientHandler` e `HttpClientHandler` varia a seconda delle esigenze dell'applicazione. `AndroidClientHandler` è consigliabile, ad esempio per il supporto della sicurezza più aggiornata.

-   È necessario che supportare TLS 1.2 +.
-   È destinata l'app Android 5.0 (API 21) o versione successiva.
-   È necessario TLS 1.2 + supporto per `HttpClient`.
-   Non è necessario supporto per TLS 1.2 + `WebClient`.

`HttpClientHandler` è un'ottima scelta se è necessario TLS 1.2 + supporta ma deve supportare le versioni di Android precedente alla versione 5.0 di Android. È anche un'ottima scelta se è necessario TLS 1.2 + supporto per `WebClient`.

A partire da xamarin. Android 8.3 `HttpClientHandler` il valore predefinito è Boring SSL (`btls`) come il provider TLS sottostante. Il provider Boring SSL TLS offre i vantaggi seguenti:

-   Supporta TLS 1.2 +.
-   Supporta tutte le versioni di Android.
-   Fornisce supporto sia per TLS 1.2 + `HttpClient` e `WebClient`.

Lo svantaggio dell'uso Boring SSL come il provider TLS sottostante è che è possibile aumentare le dimensioni dell'APK risultante (aggiunge circa 1MB delle dimensioni di file APK aggiuntive per ogni ABI supportato).

A partire da xamarin. Android 8.3, il provider TLS predefinito è Boring SSL (`btls`). Se non vuoi usare Boring SSL, è possibile ripristinare l'implementazione SSL cronologica gestita impostando il `$(AndroidTlsProvider)` proprietà `legacy` (per altre informazioni sull'impostazione delle proprietà di compilazione, vedere [processo di compilazione](~/android/deploy-test/building-apps/build-process.md)).


### <a name="programatically-using-androidclienthandler"></a>A livello di codice usando `AndroidClientHandler`

Il `Xamarin.Android.Net.AndroidClientHandler` è un `HttpMessageHandler` implementazione in modo specifico per xamarin. Android.
Istanze di questa classe verranno usato l'oggetto nativo `java.net.URLConnection` implementazione per tutte le connessioni HTTP. In teoria, ciò fornirà un aumento delle prestazioni di HTTP e di dimensioni inferiori APK.

Questo frammento di codice è un esempio di come in modo esplicito per una singola istanza del `HttpClient` classe:

```csharp
// Android 5.0 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> Il dispositivo Android sottostante deve supportare TLS 1.2 (ad esempio, Android 5.0 e versioni successive)


## <a name="ssltls-implementation-build-option"></a>Opzione di compilazione di implementazione di SSL/TLS

Questa opzione di progetto determina quale libreria TLS sottostante verrà usata da tutte le richieste web, entrambe `HttpClient` e `WebRequest`. Per impostazione predefinita, viene selezionata TLS 1.2:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Casella combinata di implementazione di TLS/SSL in Visual Studio](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Casella combinata di implementazione di TLS/SSL in Visual Studio per Mac](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

Ad esempio:

```csharp
var client = new HttpClient();
```

Se è stato impostato l'implementazione di HttpClient **Managed** e l'implementazione di TLS è stata impostata su **TLS 1.2 + nativo**, il `client` oggetto userà automaticamente gestita `HttpClientHandler` e TLS 1.2 (forniti dalla libreria BoringSSL) per le richieste HTTP.

Tuttavia, se il **implementazione di HttpClient** è impostata su `AndroidHttpClient`, quindi tutti `HttpClient` oggetti utilizzerà la classe Java sottostante `java.net.URLConnection` e non saranno interessate dal **implementazione di TLS/SSL** valore. `WebRequest` gli oggetti userà la libreria BoringSSL.

## <a name="other-ways-to-control-ssltls-configuration"></a>Altri modi per controllare la configurazione di SSL/TLS

Esistono tre modi che un'applicazione xamarin. Android possa controllare le impostazioni di TLS:

1. Selezionare la libreria TLS predefiniti e sull'implementazione di HttpClient nelle opzioni del progetto.
2. A livello di codice usando `Xamarin.Android.Net.AndroidClientHandler`.
3. Dichiarare le variabili di ambiente (facoltativo).

Delle tre opzioni, l'approccio consigliato consiste nell'usare le opzioni di progetto xamarin. Android per dichiarare il valore predefinito `HttpMessageHandler` e TLS per l'intera app. Quindi, se necessario, a livello di codice crea un'istanza di `Xamarin.Android.Net.AndroidClientHandler` oggetti. Queste opzioni sono descritte in precedenza.

La terza opzione &ndash; usando le variabili di ambiente &ndash; viene illustrato di seguito.

### <a name="declare-environment-variables"></a>Dichiarare le variabili di ambiente

Esistono due variabili di ambiente che riguardano l'uso di TLS in xamarin. Android:

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; Questa variabile di ambiente consente di dichiarare il valore predefinito `HttpMessageHandler` che verrà utilizzata dall'applicazione. Ad esempio:

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; Questa variabile di ambiente verrà dichiarato che TLS libreria verrà usata, ovvero `btls`, `legacy`, o `default` (ovvero lo stesso come l'omissione di questa variabile):

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

Questa variabile di ambiente viene impostata tramite l'aggiunta di un _file di ambiente_ al progetto. Un file dell'ambiente è un file di testo normale formato Unix con un'azione di compilazione **AndroidEnvironment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Screenshot dell'azione di compilazione AndroidEnvironment in Visual Studio.](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Screenshot della finestra di AndroidEnvironment compilare azione in Visual Studio per Mac.](http-stack-images/tls03-xs.png)

-----

Vedere le [ambiente xamarin. Android](~/android/deploy-test/environment.md) Guida per altre informazioni sulle variabili di ambiente e xamarin. Android.


## <a name="related-links"></a>Collegamenti correlati

- [Transport Layer Security (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
