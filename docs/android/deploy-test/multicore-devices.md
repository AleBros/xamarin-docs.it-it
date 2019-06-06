---
title: Dispositivi multi-core e Xamarin.Android
description: Android può essere eseguito su diverse architetture di computer. In questo documento vengono illustrate le diverse architetture della CPU che possono essere impiegate per un'applicazione Xamarin.Android. Verrà inoltre descritto come creare pacchetti di applicazioni Android per supportare diverse architetture della CPU. Verrà presentata l'interfaccia ABI (Application Binary Interface) e saranno fornite indicazioni sulle ABI da usare in un'applicazione Xamarin.Android.
ms.prod: xamarin
ms.assetid: D812883C-A14A-E74B-0F72-E50071E96328
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2019
ms.openlocfilehash: bb1b615bc922b19c50435218dfee51f9e19d1259
ms.sourcegitcommit: dd73477b1bccbd7ca45c1fb4e794da6b36ca163d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394718"
---
# <a name="multi-core-devices--xamarinandroid"></a>Dispositivi multi-core e Xamarin.Android

_Android può essere eseguito su diverse architetture di computer. In questo documento vengono illustrate le diverse architetture della CPU che possono essere impiegate per un'applicazione Xamarin.Android. Verrà inoltre descritto come creare pacchetti di applicazioni Android per supportare diverse architetture della CPU. Verrà presentata l'interfaccia ABI (Application Binary Interface) e saranno fornite indicazioni sulle ABI da usare in un'applicazione Xamarin.Android._

## <a name="overview"></a>Panoramica

Android consente la creazione di "file binari FAT", singoli file `.apk` che contengono codice macchina in grado di supportare più architetture della CPU. Questa operazione viene eseguita associando ogni frammento di codice macchina a un'interfaccia *ABI (Application Binary Interface)* . L'interfaccia ABI viene usata per definire quale codice macchina deve essere eseguito in un determinato dispositivo hardware. Ad esempio, per consentire l'esecuzione di un'applicazione Android su un dispositivo x86, è necessario includere il supporto dell'ABI x86 durante la compilazione dell'applicazione.

In particolare, ogni applicazione Android supporterà almeno un'interfaccia *EABI (Embedded-Application Binary Interface)* . Le interfacce EABI sono convenzioni specifiche dei programmi software incorporati. Una tipica interfaccia EABI descrive elementi quali:

- Il set di istruzioni per la CPU.

- L'ordine dei byte delle archiviazioni in memoria e dei caricamenti in fase di esecuzione.

- Il formato binario di file oggetto e librerie di programma, nonché il tipo di contenuto consentito o supportato in tali file e librerie.

- Le varie convenzioni usate per il passaggio dei dati tra il codice dell'applicazione e il sistema (ad esempio, modalità d'uso dei registri e/o dello stack durante le chiamate di funzioni, vincoli di allineamento e così via).

- Vincoli di allineamento e dimensioni per tipi enum, strutture, campi e matrici.

- L'elenco dei simboli di funzione disponibili per il codice macchina in fase di esecuzione, in genere da un set molto specifico di librerie selezionate.

### <a name="armeabi-and-thread-safety"></a>armeabi e thread safety

L'interfaccia Application Binary Interface verrà descritta in dettaglio di seguito, ma è importante ricordare che il runtime `armeabi` usato da Xamarin.Android *non è thread-safe*. Se un'applicazione che supporta `armeabi` viene distribuita in un dispositivo `armeabi-v7a`, verranno generate numerose eccezioni insolite e inspiegabile.

A causa di un bug di Android 4.0.0, 4.0.1, 4.0.2 e 4.0.3, verranno prelevate le librerie native dalla directory `armeabi`, anche se è presente una directory `armeabi-v7a` e il dispositivo è un dispositivo `armeabi-v7a`.

> [!NOTE]
> Xamarin.Android verificherà che i file `.so` vengano aggiunti al file APK nell'ordine corretto. Questo bug non dovrebbe rappresentare un problema per gli utenti di Xamarin.Android.

### <a name="abi-descriptions"></a>Descrizioni delle interfacce ABI

Ogni interfaccia ABI supportata da Android è identificata da un nome univoco.

#### <a name="armeabi"></a>armeabi

Questo è il nome di un'interfaccia EABI per le CPU basate su ARM che supportano almeno il set di istruzioni ARMv5TE. Android segue l'interfaccia ABI GNU/Linux ARM little-endian. Questa interfaccia ABI non supporta i calcoli a virgola mobile assistiti mediante hardware. Tutte le operazioni a virgola mobile vengono eseguite dalle funzioni helper del software che provengono dalla libreria statica `libgcc.a` del compilatore. I dispositivi SMP non sono supportati da `armeabi`.

**Nota**: il codice `armeabi` di Xamarin.Android non è thread-safe e non deve essere usato su dispositivi `armeabi-v7a` con più CPU (descritti di seguito). L'uso del codice `armeabi` su un dispositivo `armeabi-v7a` a singolo core è sicuro.

#### <a name="armeabi-v7a"></a>armeabi-v7a

Questo è un altro set di istruzioni CPU basato su ARM che estende l'interfaccia EABI `armeabi` descritta in precedenza. L'interfaccia EABI `armeabi-v7a` fornisce il supporto per le operazioni a virgola mobile hardware e i dispositivi con più CPU (SMP). Un'applicazione che usa l'interfaccia EABI `armeabi-v7a` può ottenere miglioramenti sostanziali delle prestazioni rispetto a un'applicazione che usa `armeabi`.

**Nota:** il codice macchina `armeabi-v7a` non verrà eseguito sui dispositivi ARMv5.

#### <a name="arm64-v8a"></a>arm64-v8a

Questo è un set di istruzioni a 64 bit basato sull'architettura della CPU ARMv8. Questa architettura viene usata in *Nexus 9*.
ln Xamarin.Android 5.1 è stato introdotto il supporto per questa architettura. Per altre informazioni, vedere [64-bit runtime support](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#64-bit-runtime-support) (Supporto del runtime a 64 bit).

#### <a name="x86"></a>x86

Questo è il nome di un'interfaccia ABI per le CPU che supportano il set di istruzioni denominato *x86* o *IA-32*. L'ABI corrisponde alle istruzioni per il set di istruzioni Pentium Pro, inclusi i set di istruzioni MMX, SSE, SSE2 e SSE3. Non include altre estensioni di set di istruzioni IA-32 facoltative, quali:

- L'istruzione MOVBE.
- L'estensione Supplemental SSE3 (SSSE3).
- Qualsiasi variante di SSE4.

**Nota:** la piattaforma Google TV, nonostante sia in esecuzione su x86, non è supportata da Android NDK.

#### <a name="x8664"></a>x86_64

Questo è il nome di un'interfaccia ABI per le CPU che supportano il set di istruzioni x86 a 64 bit (anche denominato *x64* o *AMD64*). ln Xamarin.Android 5.1 è stato introdotto il supporto per questa architettura. Per altre informazioni, vedere [64-bit runtime support](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#64-bit-runtime-support) (Supporto del runtime a 64 bit).

#### <a name="apk-file-format"></a>Formato di file APK

Il pacchetto dell'applicazione Android è il formato di file che contiene tutto il codice, gli asset, le risorse e i certificati necessari per un'applicazione Android. Si tratta di un file `.zip`, ma usa l'estensione di file `.apk`. Se viene espanso, il contenuto di un file `.apk` creato da Xamarin.Android può essere visualizzato nella schermata seguente:

[![Contenuto del file con estensione apk](multicore-devices-images/00.png)](multicore-devices-images/00.png#lightbox)

Una breve descrizione del contenuto del file `.apk`:

- **AndroidManifest.xml** &ndash; Il file `AndroidManifest.xml`, in formato XML binario.

- **classes.dex** &ndash; Contiene il codice dell'applicazione, compilato nel formato di file `dex`, usato dalla macchina virtuale del runtime Android.

- **resources.arsc** &ndash; Questo file contiene tutte le risorse precompilate per l'applicazione.

- **lib**: questa directory contiene il codice compilato per ogni interfaccia ABI. Contiene una sottocartella per ogni interfaccia ABI descritta nella sezione precedente. Nella schermata precedente, il file `.apk` in questione dispone di librerie native sia per `armeabi-v7a` che per `x86`.

- **META-INF** &ndash; Questa directory (se presente) viene usata per archiviare le informazioni sulla firma, il pacchetto e i dati di configurazione dell'estensione.

- **res** &ndash; Questa directory contiene le risorse che non sono state compilate in `resources.arsc`.

> [!NOTE]
> Il file `libmonodroid.so` è la libreria nativa richiesta da tutte le applicazioni Xamarin.Android.

#### <a name="android-device-abi-support"></a>Supporto per le interfacce ABI dei dispositivi Android

Ogni dispositivo Android supporta l'esecuzione di codice nativo in due interfacce ABI:

- **L'interfaccia ABI "primaria"** &ndash; Corrisponde al codice macchina usato nell'immagine di sistema.

- **Un'interfaccia ABI "secondaria"** &ndash; Si tratta di un'interfaccia ABI facoltativa, anch'essa supportata dall'immagine del sistema.

Ad esempio, un tipico dispositivo ARMv5TE disporrà solo di un'interfaccia ABI primaria `armeabi`, mentre un dispositivo ARMv7 specificherà un'interfaccia ABI primaria `armeabi-v7a` e una secondaria `armeabi`. Un tipico dispositivo x86 specificherà solo un'interfaccia ABI primaria `x86`.

### <a name="android-native-library-installation"></a>Installazione delle librerie native di Android

Al momento dell'installazione del pacchetto, le librerie native all'interno del file `.apk` vengono estratte nella directory delle librerie native dell'app, in genere `/data/data/<package-name>/lib`, e sono pertanto denominate `$APP/lib`.

Il comportamento di installazione delle librerie native di Android varia notevolmente tra le diverse versioni di Android.

#### <a name="installing-native-libraries-pre-android-40"></a>Installazione delle librerie native: versioni precedenti ad Android 4.0

Per le versioni di Android precedenti alla 4.0 Ice Cream Sandwich, verranno estratte solo le librerie native da una *singola ABI* all'interno del file `.apk`. Le app Android di questo tipo tenteranno innanzitutto di estrarre tutte le librerie native per l'interfaccia ABI primaria. Se tali librerie non esistono, Android estrarrà tutte le librerie native per l'interfaccia ABI secondaria. Non viene eseguita alcuna "unione".

Si consideri, ad esempio, una situazione in cui un'applicazione è installata in un dispositivo `armeabi-v7a`. Il file `.apk,` che supporta sia `armeabi` che `armeabi-v7a`, contiene i file e le directory `lib` seguenti dell'interfaccia ABI:

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

Dopo l'installazione, la directory delle librerie native conterrà:

```shell
$APP/lib/libtwo.so # from the armeabi-v7a directory in the apk
```

In altre parole, non è installato alcun `libone.so`. Questo comporta problemi, perché `libone.so` non è presente per il caricamento da parte dell'applicazione in fase di esecuzione. Questo comportamento, sebbene sia imprevisto, è stato registrato come un bug e riclassificato come "[funzionamento previsto](http://code.google.com/p/android/issues/detail?id=9089)".

Di conseguenza, quando la destinazione è costituita da versioni di Android precedenti alla 4.0, è necessario fornire *tutte* le librerie native per *ogni* interfaccia ABI supportata dall'applicazione. In altre parole, il file `.apk` deve contenere:

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libone.so
lib/armeabi-v7a/libtwo.so
```

#### <a name="installing-native-libraries-android-40-ndash-android-403"></a>Installazione delle librerie native: Android 4.0 - Android 4.0.3

In Android 4.0 Ice Cream Sandwich è stata cambiata la logica di estrazione. Vengono enumerate tutte le librerie native, viene verificato se il nome base del file è già stato estratto e, se sono soddisfatte entrambe le condizioni seguenti, viene estratta la libreria:

- Non è già stato estratto.

- L'interfaccia ABI della libreria nativa corrisponde all'interfaccia ABI primaria o secondaria della destinazione.

Il soddisfacimento di queste condizioni consente il comportamento di "unione". In altre parole, se si dispone di un file `.apk` con il seguente contenuto:

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

Dopo l'installazione, la directory delle librerie native conterrà:

```shell
$APP/lib/libone.so
$APP/lib/libtwo.so
```

Sfortunatamente, questo comportamento è dipendente dall'ordine, come descritto nel documento [Issue 24321: Galaxy Nexus 4.0.2 uses armeabi native code when both armeabi and armeabi-v7a is included in apk](http://code.google.com/p/android/issues/detail?id=25321) (Problema 24321: Galaxy Nexus 4.0.2 usa il codice nativo armeabi quando sia armeabi che armeabi-v7a sono inclusi nel file apk).

Le librerie native vengono elaborate "in ordine" (come sono elencate, ad esempio, da unzip) e viene estratta la *prima corrispondenza*. Poiché il file `.apk` contiene le versioni `armeabi` e `armeabi-v7a` di `libtwo.so` e `armeabi` è elencato per primo, viene estratta la versione `armeabi`, *non* la versione `armeabi-v7a`:

```shell
$APP/lib/libone.so # armeabi
$APP/lib/libtwo.so # armeabi, NOT armeabi-v7a!
```

Inoltre, anche se vengono specificate entrambe le interfacce ABI `armeabi` e `armeabi-v7a` (come descritto di seguito nella sezione *Dichiarazione delle interfacce ABI supportate*), Xamarin.Android creerà il seguente elemento in
`csproj`:

```xml
<AndroidSupportedAbis>armeabi,armeabi-v7a</AndroidSupportedAbis>
```

Di conseguenza, verrà innanzitutto trovato il `libmonodroid.so` `armeabi` all'interno del file `.apk` e il `libmonodroid.so` `armeabi` sarà quello che viene estratto, anche se il `libmonodroid.so` `armeabi-v7a` è presente e ottimizzato per la destinazione. Questo può inoltre determinare la generazione errori di runtime, che indicano ad esempio che `armeabi` non è sicuro per SMP.


##### <a name="installing-native-libraries-android-404-and-later"></a>Installazione delle librerie native: Android 4.0.4 e versioni successive

In Android 4.0.4 è stata cambiata la logica di estrazione: vengono enumerate tutte le librerie native, viene letto il nome base del file, quindi viene estratta la versione ABI principale (se presente) o quella secondaria (se presente). Questo consente il comportamento di "unione". In altre parole, se si dispone di un file `.apk` con il seguente contenuto:

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

Dopo l'installazione, la directory delle librerie native conterrà:

```shell
$APP/lib/libone.so # from armeabi
$APP/lib/libtwo.so # from armeabi-v7a
```

### <a name="xamarinandroid-and-abis"></a>Xamarin.Android e interfacce ABI

Xamarin.Android supporta le architetture _a 64 bit_seguenti:

- `arm64-v8a`
- `x86_64`

> [!NOTE]
> Da agosto 2018 le nuove app dovranno usare il livello API 26 come destinazione e da agosto 2019 le app [dovranno fornire versioni a 64 bit](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html) oltre alla versione a 32 bit.

Xamarin.Android supporta queste architetture a 32 bit:

- `armeabi` ^
- `armeabi-v7a`
- `x86`

> [!NOTE]
> **^** A partire da [Xamarin.Android 9.2](https://docs.microsoft.com/xamarin/android/release-notes/9/9.2#removal-of-support-for-armeabi-cpu-architecture), `armeabi` non è più supportato.

Xamarin.Android attualmente non fornisce supporto per `mips`.

### <a name="declaring-supported-abis"></a>Dichiarazione delle interfacce ABI supportate

Per impostazione predefinita, Xamarin.Android userà `armeabi-v7a` per le compilazioni **Versione** e `armeabi-v7a` e `x86` per le compilazioni **Debug**. Il supporto per le diverse interfacce ABI può essere impostato tramite le opzioni del progetto per un progetto Xamarin.Android. In Visual Studio, questa opzione può essere impostata nella pagina **Opzioni Android** delle **proprietà** del progetto, nella scheda **Avanzate**, come illustrato nello screenshot seguente:

![Proprietà avanzate delle opzioni Android](multicore-devices-images/vs-abi-selections.png)

In Visual Studio per Mac, è possibile selezionare le architetture supportate nella pagina **Compilazione Android** di **Opzioni progetto**, nella scheda **Avanzate**, come illustrato nello screenshot seguente:

[![Interfacce ABI supportate della compilazione Android](multicore-devices-images/xs-abi-selections-sml.png)](multicore-devices-images/xs-abi-selections.png#lightbox)

Esistono alcune situazioni in cui potrebbe essere necessario dichiarare il supporto per altre interfacce ABI, ad esempio nei seguenti casi:

- Distribuzione dell'applicazione in un dispositivo `x86`.

- Distribuzione dell'applicazione in un dispositivo `armeabi-v7a` per garantire la thread safety.

## <a name="summary"></a>Riepilogo

In questo documento sono state illustrate le diverse architetture della CPU in cui è possibile eseguire un'applicazione Android. È stata presentata l'interfaccia Application Binary Interface ed è stato descritto come viene usata da Android per supportare varie architetture della CPU.
È stato quindi illustrato come specificare il supporto per le interfacce ABI in un'applicazione Xamarin.Android e sono stati evidenziati i problemi che si verificano quando si usano applicazioni Xamarin.Android destinate unicamente a `armeabi` in un dispositivo `armeabi-v7a`.

## <a name="related-links"></a>Collegamenti correlati

- [Interfaccia ABI per l'architettura ARM (PDF)](http://infocenter.arm.com/help/topic/com.arm.doc.ihi0036b/IHI0036B_bsabi.pdf)
- [Android NDK](https://developer.android.com/tools/sdk/ndk/index.html)
- [Problema 9089:Nexus One non carica alcuna libreria nativa da armeabi se è presente almeno una libreria in armeabi v7a](http://code.google.com/p/android/issues/detail?id=9089)
- [Problema 24321: Galaxy Nexus 4.0.2 usa il codice nativo armeabi quando sia armeabi che armeabi-v7a sono inclusi nel file apk](http://code.google.com/p/android/issues/detail?id=25321)
