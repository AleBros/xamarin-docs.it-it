---
title: Stack HttpClient e selettore di implementazione di SSL/TLS per Android
description: I selettori HttpClient Stack e l'implementazione di SSL/TLS determinano l'implementazione HttpClient e SSL/TLS che verrà utilizzato da app xamarin.
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 04/20/2018
ms.openlocfilehash: bedcf0603fffc9886155881f91972203104ba155
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2018
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>Stack HttpClient e selettore di implementazione di SSL/TLS per Android

I selettori HttpClient Stack e l'implementazione di SSL/TLS determinano l'implementazione HttpClient e SSL/TLS che verrà utilizzato da app xamarin.

Devono fare riferimento a progetti di **System.NET. HTTP** assembly.

> [!WARNING]
> **Aprile, 2018** : a causa di una maggiore sicurezza i requisiti, inclusi la conformità PCI, principali provider di servizi cloud e i server web sono previsti per arrestare il supporto di versioni TLS meno recente 1.2.  Xamarin i progetti creati nelle versioni precedenti di predefinito di Visual Studio per utilizzare le versioni precedenti di TLS.
>
> Per verificare che le app continuano a lavorare con tali server e servizi, **è necessario aggiornare i progetti di Xamarin con il `Android HttpClient` e `Native TLS 1.2` impostazioni illustrate di seguito, quindi ricompilare e ridistribuire le app** per il utenti.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

È la configurazione di xamarin. Android HttpClient **opzioni progetto > Opzioni Android**, quindi fare clic sul **opzioni avanzate** pulsante.

Queste sono le impostazioni consigliate per il supporto di TLS 1.2:

[![Opzioni Android di Visual Studio](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

La configurazione di xamarin. Android HttpClient è in **opzioni progetto > Compila > di compilazione Android** le impostazioni e fare clic sui **generale** scheda.

Queste sono le impostazioni consigliate per il supporto di TLS 1.2:

[![Visual Studio per Mac Android opzioni](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>Opzioni di configurazione alternativo

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler è il nuovo gestore che delega a codice nativo di Java/OS anziché implementare tutti gli elementi nel codice gestito.
**Si tratta dell'opzione consigliata.**

#### <a name="pros"></a>Professionisti

- Utilizzare l'API nativa per migliorare le prestazioni e dimensioni eseguibile.
- Il supporto per gli standard più recenti, ad esempio. TLS 1.2.

#### <a name="cons"></a>Svantaggi

- È necessario Android 5.0 o versione successiva.
- Alcune funzionalità di HttpClient/opzioni non sono disponibili.

### <a name="managed-httpclienthandler"></a>Gestito (HttpClientHandler)

Gestore gestito è il gestore HttpClient completamente gestito che è stato spedito con le versioni precedenti di xamarin.

#### <a name="pros"></a>Professionisti

- È più compatibile (funzionalità) con Microsoft .NET e le versioni precedenti di Xamarin.

#### <a name="cons"></a>Svantaggi

- Non è completamente integrato con il sistema operativo (ad es. limitato a TLS 1.0).
- È in genere più lento (ad es. crittografia) rispetto all'API nativa.
- Richiede più il codice gestito, creazione di applicazioni di grandi dimensioni.



### <a name="choosing-a-handler"></a>Scelta di un gestore

La scelta tra `AndroidClientHandler` e `HttpClientHandler` dipende dalle esigenze dell'applicazione. `AndroidClientHandler` è consigliabile, ad esempio per il supporto della sicurezza più aggiornata.

-   È necessario il che supporto di TLS 1.2 +.
-   L'app è destinato a Android 5.0 (API 21) o versione successiva.
-   È necessario TLS 1.2 + supporto per `HttpClient`.
-   Non è necessario supporto per TLS 1.2 + `WebClient`.

`HttpClientHandler` è consigliabile se è necessario TLS 1.2 + supportano ma deve supportare le versioni precedenti ad Android 5.0 di Android. È anche una buona scelta se è necessario TLS 1.2 + supporto per `WebClient`.

A partire da xamarin 8.3, `HttpClientHandler` impostazioni predefinite per SSL barenatura (`btls`) come provider di TLS sottostante. Il provider barenatura SSL TLS offre i vantaggi seguenti:

-   Supporta TLS 1.2.
-   Supporta tutte le versioni di Android.
-   Viene fornito il supporto di TLS 1.2 per entrambi `HttpClient` e `WebClient`.

Lo svantaggio dell'uso di SSL barenatura come provider di TLS sottostante è che è possibile aumentare le dimensioni dell'APK risultante (aggiunge circa 1MB di dimensioni di file APK aggiuntivi per ABI supportati).

A partire da 8.3 xamarin, il provider TLS predefinito è barenatura SSL (`btls`). Se si desidera utilizzare barenatura SSL, è possibile ripristinare l'implementazione di SSL gestita cronologico impostando la `$(AndroidTlsProvider)` proprietà `legacy` (per ulteriori informazioni sull'impostazione delle proprietà di compilazione, vedere [processo di compilazione](~/android/deploy-test/building-apps/build-process.md)).


### <a name="programatically-using-androidclienthandler"></a>A livello di programmazione utilizzando `AndroidClientHandler`

Il `Xamarin.Android.Net.AndroidClientHandler` è un `HttpMessageHandler` implementazione in modo specifico per xamarin.
Le istanze di questa classe utilizzano nativo `java.net.URLConnection` implementazione per tutte le connessioni HTTP. In teoria, questo fornirà un aumento delle prestazioni di HTTP e ridurre le dimensioni dei file APK.

Questo frammento di codice è riportato un esempio di come in modo esplicito per una singola istanza del `HttpClient` classe:

```csharp
// Android 5.0 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> Il dispositivo Android sottostante deve supportare TLS 1.2 (ie. Android 5.0 e versioni successive)


## <a name="ssltls-implementation-build-option"></a>Opzione di compilazione di implementazione di SSL/TLS

Questa opzione di progetto determina quale libreria TLS sottostante verrà utilizzata da tutte le richieste web, entrambi `HttpClient` e `WebRequest`. Per impostazione predefinita, viene selezionato TLS 1.2:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Casella combinata di implementazione di TLS/SSL in Visual Studio](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Casella combinata di implementazione di TLS/SSL in Visual Studio per Mac](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

Ad esempio:

```csharp
var client = new HttpClient();
```

Se l'implementazione di HttpClient è stato impostato su **gestito** e l'implementazione di TLS era impostato su **nativo TLS 1.2 +**, quindi il `client` oggetto automaticamente utilizzerebbe gestito `HttpClientHandler` e TLS 1.2 (fornito dalla libreria BoringSSL) per le richieste HTTP.

Tuttavia, se il **HttpClient implementazione** è impostato su `AndroidHttpClient`, quindi tutti `HttpClient` oggetti utilizzerà la classe Java sottostante `java.net.URLConnection` e non saranno interessate dal **diimplementazionediTLS/SSL** valore. `WebRequest` oggetti utilizzerebbe la libreria BoringSSL.

## <a name="other-ways-to-control-ssltls-configuration"></a>Altri modi per controllare la configurazione di SSL/TLS

Esistono tre modi che un'applicazione di xamarin, controllare le impostazioni di TLS:

1. Selezionare la libreria TLS HttpClient sull'implementazione e l'impostazione predefinita nelle opzioni del progetto.
2. A livello di programmazione utilizzando `Xamarin.Android.Net.AndroidClientHandler`.
3. Dichiarare le variabili di ambiente (facoltativo).

Delle tre opzioni, l'approccio consigliato consiste nell'utilizzare le opzioni di progetto xamarin per dichiarare il valore predefinito `HttpMessageHandler` e TLS per l'intera app. Quindi, se necessario, a livello di codice crea un'istanza `Xamarin.Android.Net.AndroidClientHandler` oggetti. Queste opzioni sono descritte in precedenza.

La terza opzione &ndash; utilizzando variabili di ambiente &ndash; vengono illustrati di seguito.

### <a name="declare-environment-variables"></a>Dichiarare le variabili di ambiente

Esistono due variabili di ambiente sono correlate all'uso di TLS in xamarin:

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; Il valore predefinito di dichiara la variabile di ambiente `HttpMessageHandler` che verrà utilizzato dall'applicazione. Ad esempio:

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; Questa variabile di ambiente dichiarerà libreria in cui TLS da utilizzare, ovvero `btls`, `legacy`, o `default` (ovvero lo stesso come l'omissione di questa variabile):

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

Questa variabile di ambiente viene impostata tramite l'aggiunta di un _file dell'ambiente_ al progetto. Un file di ambiente è un file di testo formattato Unix con un'azione di compilazione di **AndroidEnvironment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Schermata dell'azione di compilazione AndroidEnvironment in Visual Studio.](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Schermata del AndroidEnvironment compila azione in Visual Studio per Mac.](http-stack-images/tls03-xs.png)

-----

Vedere il [ambiente xamarin](~/android/deploy-test/environment.md) Guida per ulteriori informazioni sulle variabili di ambiente e xamarin.


## <a name="related-links"></a>Collegamenti correlati

- [Transport Layer Security (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
