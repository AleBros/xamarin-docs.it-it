---
title: Ambiente Xamarin.Android
ms.prod: xamarin
ms.assetid: 67BFD4E1-276C-4B9F-9BD8-A5218D2BD529
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 14be0fec50138aed7b2b3e8d48d49cad86abbb2b
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119422"
---
# <a name="xamarinandroid-environment"></a>Ambiente Xamarin.Android

## <a name="execution-environment"></a>Ambiente di esecuzione

L'*ambiente di esecuzione* è il set di variabili di ambiente e di proprietà di sistema di Android che influenzano l'esecuzione del programma. Le proprietà di sistema di Android possono essere impostate con il comando `adb shell setprop`, le variabili di ambiente, invece, impostando la proprietà di sistema `debug.mono.env`:

```shell
## Enable GREF logging
adb shell setprop debug.mono.log gref

## Set the MONO_LOG_LEVEL and MONO_LOG_MASK environment variables
## so that additional Mono messages will be written to `adb logcat`.
adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"
```

Le proprietà di sistema di Android vengono impostate per tutti i processi nel dispositivo di destinazione.

A partire da Xamarin.Android 4.6, sia le proprietà di sistema che le variabili di ambiente possono essere impostate o sostituite in base alle singole app aggiungendo un *file di ambiente* al progetto. Un file di ambiente è un file di testo normale in formato Unix con l'[**azione di compilazione** `AndroidEnvironment`](~/android/deploy-test/building-apps/build-process.md).
Il file dell'ambiente contiene righe nel formato *chiave=valore*.
I commenti sono righe che iniziano con `#`. Le righe vuote vengono ignorate.

Se la *chiave* inizia con una lettera maiuscola, la *chiave* viene considerata una variabile di ambiente e viene usato **setenv**(3) per impostare la variabile di ambiente sul *valore* specificato durante l'avvio del processo.

Se la *chiave* inizia con una lettera minuscola, la *chiave* viene considerata come proprietà di sistema di Android e il *valore* è il *valore predefinito*: prima vengono cercate nell'archivio delle proprietà di sistema di Android le proprietà di sistema di Android che controllano il comportamento di esecuzione di Xamarin.Android e, se non sono presenti valori, viene usato il valore specificato nel file dell'ambiente. Lo scopo è quello di consentire l'uso di `adb shell setprop` per eseguire l'override dei valori ricavati dal file dell'ambiente a fini diagnostici.

## <a name="xamarinandroid-environment-variables"></a>Variabili di ambiente Xamarin.Android

Xamarin.Android supporta la variabile `XA_HTTP_CLIENT_HANDLER_TYPE`, che può essere impostata tramite `adb shell setprop debug.mono.env` o tramite l'azione di compilazione `$(AndroidEnvironment)`.


### `XA_HTTP_CLIENT_HANDLER_TYPE`

Tipo qualificato dall'assembly che deve ereditare da [HttpMessageHandler](https://docs.microsoft.com/dotnet/api/system.net.http.httpmessagehandler?view=xamarinandroid-7.1) e viene creato dal [costruttore predefinito `HttpClient()`](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient.-ctor?view=xamarinandroid-7.1#System_Net_Http_HttpClient__ctor).

Per impostazione predefinita, in Xamarin.Android 6.1 questa variabile di ambiente non è impostata e verrà usata la classe [HttpClientHandler](https://docs.microsoft.com/dotnet/api/system.net.http.httpclienthandler?view=xamarinandroid-7.1).

In alternativa, è possibile specificare il valore `Xamarin.Android.Net.AndroidClientHandler` per utilizzare [`java.net.URLConnection`](xref:Java.Net.URLConnection)
per l'accesso di rete, che *può* consentire l'uso di TLS 1.2 se supportato da Android.

Aggiunto in Xamarin.Android 6.1.

## <a name="xamarinandroid-system-properties"></a>Proprietà di sistema di Xamarin.Android

Xamarin.Android supporta le proprietà di sistema seguenti, che possono essere impostate tramite `adb shell setprop` o tramite l'azione di compilazione `$(AndroidEnvironment)`.

- `debug.mono.debug`
- `debug.mono.env`
- `debug.mono.gc`
- `debug.mono.log`
- `debug.mono.max_grefc`
- `debug.mono.profile`
- `debug.mono.runtime_args`
- `debug.mono.trace`
- `debug.mono.wref`
- `XA_HTTP_CLIENT_HANDLER_TYPE`

### `debug.mono.debug`

Il valore della proprietà di sistema `debug.mono.debug` è un intero. Se `1`, si comporta "come se" il processo fosse stato avviato con `mono --debug`.
In genere visualizza le informazioni su file e righe in analisi dello stack e così via, senza che l'app debba essere avviata da un debugger.

### `debug.mono.env`

Contiene un elenco delle variabili di ambiente delimitato da `|`.

### `debug.mono.gc`

Il valore della proprietà di sistema `debug.mono.debug` è un intero.
Se `1`, verranno registrate le informazioni relative a GC.

Equivale ad avere la proprietà di sistema `debug.mono.log` contenente `gc`.

### `debug.mono.log`

Controlla quali informazioni aggiuntive verranno registrate da Xamarin.Android in `adb logcat`.
Si tratta di una stringa delimitata da virgole (`,`), contenente uno dei valori seguenti:

- `all`: visualizza *tutti* i messaggi. Non è in genere una scelta valida, perché include i messaggi `lref`.
- `assembly`: visualizza il file `.apk` e i messaggi di analisi dell'assembly.
- `gc`: visualizza i messaggi relativi a GC.
- `gref`: visualizza i messaggi di riferimento globale JNI.
- `lref`: visualizza i messaggi di riferimento locale JNI.  
    *Nota*: si tratta *effettivamente* di `adb logcat` di posta indesiderata.  
    In Xamarin.Android 5.1 verrà anche creato un file `.__override__/lrefs.txt`, che può assumere dimensioni *enormi*.  
    Da evitare.
- `timing`: visualizza alcune informazioni sugli intervalli dei metodi. Verranno anche creati i file `.__override__/methods.txt` e `.__override__/counters.txt`.


### `debug.mono.max_grefc`

Il valore della proprietà di sistema `debug.mono.max_grefc` è un intero.
Il valore *esegue l'override* del conteggio di GREF massimo rilevato predefinito per il dispositivo di destinazione.

*Nota:* questa proprietà può essere usata solo con `adb shell setprop
debug.mono.max_grefc` perché il valore non sarà disponibile in tempo con un file **environment.txt**.

### `debug.mono.profile`

La proprietà di sistema `debug.mono.profile` abilita il profiler.
Equivale all'opzione `mono --profile` e usa gli stessi valori. Per altre informazioni, vedere la pagina di manuale [**mono**(1)](http://docs.go-mono.com/?link=man%3amono(1)).

### `debug.mono.runtime_args`

La proprietà di sistema `debug.mono.runtime_args` contiene opzioni aggiuntive che devono essere analizzate da **mono**.

### `debug.mono.trace`

La proprietà di sistema `debug.mono.trace` abilita l'analisi.
Equivale all'opzione `mono --trace` e usa gli stessi valori. Per altre informazioni, vedere la pagina di manuale [**mono**(1)](http://docs.go-mono.com/?link=man%3amono(1)).

In generale, *non usarla*. L'uso dell'analisi visualizzerà un output `adb logcat` indesiderato e rallenterà considerevolmente e modificherà il comportamento del programma (fino ad aggiungere ulteriori condizioni di errore).

Tuttavia *a volte* consente di eseguire ulteriori indagini.

### `debug.mono.wref`

La proprietà di sistema `debug.mono.wref` consente l'override del meccanismo di riferimento debole JNI rilevato predefinito. I valori supportati sono due:

- `jni`: usa i riferimenti deboli JNI, creati da `JNIEnv::NewWeakGlobalRef()` ed eliminati definitivamente da `JNIEnv::DeleteWeakGlobalREf()`.
- `java`: usa i riferimenti globali JNI che fanno riferimento alle istanze di `java.lang.WeakReference`.

Per impostazione predefinita, `java` viene usato fino all'API 7 e nell'API 19 (Kit Kat) con ART abilitato. Nell'API 8 sono stati aggiunti i riferimenti `jni` e in ART sono stati *interrotti* i riferimenti `jni`.

Questa proprietà di sistema è utile per i test e per alcune forme di analisi.
*In generale* non deve essere modificata.

### <a name="xa_http_client_handler_type"></a>XA\_HTTP\_CLIENT\_HANDLER\_TYPE

Questa variabile di ambiente, introdotta in Xamarin.Android 6.1, dichiara l'implementazione `HttpMessageHandler` predefinita che verrà usata da `HttpClient`. Per impostazione predefinita, questa variabile non è impostata e Xamarin.Android userà `HttpClientHandler`.

```shell
XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
```

> [!NOTE]
> Il dispositivo Android sottostante deve supportare TLS 1.2.
Android 5.0 e versioni successive supportano TLS 1.2


## <a name="example"></a>Esempio

```shell
## Comments are lines which start with '#'
## Blank lines are ignored.


## Enable GREF messages to `adb logcat`
debug.mono.log=gref

## Clear out a Mono environment variable to decrease logging
MONO_LOG_LEVEL=
```



## <a name="related-links"></a>Collegamenti correlati

- [Transport Layer Security](~/cross-platform/app-fundamentals/transport-layer-security.md)
