---
title: GDB
ms.prod: xamarin
ms.assetid: CD0BE462-FA38-4881-B481-82AD05B3B8FE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: bc2c1c26bcb3dcf99a5598b6dccb6aad02513a28
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70197566"
---
# <a name="gdb"></a>GDB

## <a name="overview"></a>Panoramica

In Xamarin.Android 4.10 è stato introdotto il supporto parziale per l'uso di `gdb` con la destinazione MSBuild `_Gdb`. 

> [!NOTE]
> Per il supporto di `gdb`, è necessario che sia installato Android NDK.

È possibile usare `gdb` in tre modi:

1. [Build di debug con Fast Deployment abilitato](#Debug_Builds_with_Fast_Deployment).
1. [Build di debug con Fast Deployment disabilitato](#Debug_Builds_without_Fast_Deployment).
1. [Build di rilascio](#Release_Builds).


Se si verificano problemi, vedere la sezione [Risoluzione dei problemi](#Troubleshooting).

<a name="Debug_Builds_with_Fast_Deployment" />

### <a name="debug-builds-with-fast-deployment"></a>Build di debug con Fast Deployment

Quando si compila e si distribuisce una build di debug con Fast Deployment abilitato, `gdb` può essere associato usando la destinazione MSBuild `_Gdb`.

Installare prima l'app. Questa operazione può essere eseguita tramite l'ambiente di sviluppo integrato o la riga di comando:

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:Install *.csproj
```

Eseguire quindi la destinazione `_Gdb`. Al termine dell'esecuzione, verrà visualizzata una riga di comando `gdb`:

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
...
```

La destinazione `_Gdb` avvierà un'attività dell'utilità di avvio arbitraria dichiarata nel file `AndroidManifest.xml`. Per specificare in modo esplicito l'attività da eseguire, usare la proprietà MSBuild `RunActivity`. L'avvio di servizi e altri costrutti Android non è attualmente supportato.

La destinazione `_Gdb` creerà una directory `gdb-symbols` in cui copierà i contenuti delle directory `/system/lib` e `$APPDIR/lib` della destinazione.


> [!NOTE]
> I contenuti della directory `gdb-symbols` sono collegati alla destinazione Android in cui si esegue la distribuzione e non verranno sostituiti automaticamente qualora si cambiasse la destinazione. Si tratta di un bug. Se si cambiano i dispositivi di destinazione Android, sarà necessario eliminare manualmente questa directory.

Copiare infine il comando `gdb` generato ed eseguirlo nella shell:

```bash
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) bt
#0  0x40082e84 in nanosleep () from /Users/jon/Development/Projects/Scratch.HelloXamarin20/gdb-symbols/libc.so
#1  0x4008ffe6 in sleep () from /Users/jon/Development/Projects/Scratch.HelloXamarin20/gdb-symbols/libc.so
#2  0x74e46240 in ?? ()
#3  0x74e46240 in ?? ()
(gdb) c
```

<a name="Debug_Builds_without_Fast_Deployment" />

## <a name="debug-builds-without-fast-deployment"></a>Build di debug senza Fast Deployment

Le build di debug *con* Fast Deployment copiano il programma `gdbserver` di Android NDK nella directory `.__override__` di Fast Deployment. Quando Fast Deployment è disabilitato, questa directory potrebbe non esistere.

Esistono due soluzioni alternative:

- Impostare la proprietà di sistema `debug.mono.log` in modo che venga creata la directory `.__override__`.
- Includere `gdbserver` nel file `.apk`.

### <a name="setting-the-debugmonolog-system-property"></a>Impostazione della proprietà di sistema `debug.mono.log`

Per impostare la proprietà di sistema `debug.mono.log`, usare il comando `adb`:

```bash
$ adb shell setprop debug.mono.log gc
```

Dopo che la proprietà di sistema è stata impostata, eseguire la destinazione `_Gdb` e il comando `gdb` visualizzato, come nella configurazione Build di debug con Fast Deployment:

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
  ...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
  ...
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) c
```


### <a name="including-gdbserver-in-your-app"></a>Inclusione di `gdbserver` nell'app

Per includere `gdbserver` nell'app:

1. Trovare `gdbserver` in Android NDK (dovrebbe essere in **$ANDROID\_NDK\_PATH/prebuilt/android-arm/gdbserver/gdbserver**) e copiarlo nella directory del progetto.

2. Rinominare `gdbserver` in **libs/armeabi-v7a/libgdbserver.so**.

3. Aggiungere **libs/armeabi-v7a/libgdbserver.so** al progetto con l'**azione di compilazione** `AndroidNativeLibrary`.

4. Ricompilare e reinstallare l'applicazione.

Dopo che l'app è stata reinstallata, eseguire la destinazione `_Gdb` e il comando `gdb` visualizzato, come nella configurazione Build di debug con Fast Deployment:

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
  ...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
  ...
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) c
```

<a name="Release_Builds" />

## <a name="release-builds"></a>Build di rilascio

Il supporto di `gdb` presenta tre requisiti:

1. Autorizzazione `INTERNET`.
2. Debug dell'app abilitato.
3. `gdbserver` accessibile.

L'autorizzazione `INTERNET` è abilitata per impostazione predefinita nelle app di debug. Se non è già presente nell'applicazione, è possibile aggiungerla modificando **Properties/AndroidManifest.xml** o modificando le [proprietà del progetto](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest).

Il debug dell'app può essere abilitato impostando la proprietà dell'attributo personalizzato [ApplicationAttribute.Debugging](xref:Android.App.ApplicationAttribute.Debuggable) su `true` o modificando **Properties/AndroidManifest.xml** e impostando l'attributo `//application/@android:debuggable` su `true`:

```xml
<application android:label="Example.Name.Here" android:debuggable="true">
```

Per fornire un `gdbserver` accessibile, è possibile seguire le indicazioni della sezione [Build di debug senza Fast Deployment](#Debug_Builds_without_Fast_Deployment).

Tenere tuttavia presente che la destinazione MSBuild `_Gdb` terminerà le istanze dell'app già in esecuzione. Questo problema non si verificherà nelle destinazioni precedenti ad Android v4.0.

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="mono_pmip-doesnt-work"></a>`mono_pmip` non funziona

La funzione `mono_pmip` (utile per [ottenere stack frame gestiti](https://www.mono-project.com/docs/debug+profile/debug/#debugging-with-gdb))viene esportata da `libmonosgen-2.0.so`, attualmente non scaricato dalla destinazione `_Gdb`. Questo problema verrà corretto in una versione futura.

Per abilitare la chiamata alle funzioni presenti in `libmonosgen-2.0.so`, copiarle dal dispositivo di destinazione alla directory `gdb-symbols`:

```bash
$ adb pull /data/data/Mono.Android.DebugRuntime/lib/libmonosgen-2.0.so Project/gdb-symbols
```

Riavviare quindi la sessione di debug.

### <a name="bus-error-10-when-running-the-gdb-command"></a>Errore del bus: 10 quando si esegue il comando `gdb`

Quando il comando `gdb` genera l'errore `"Bus error: 10"`, riavviare il dispositivo Android.

```bash
$ "/path/to/arm-linux-androideabi-gdb" -x "Project/gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
Copyright (C) 2011 Free Software Foundation, Inc.
...
Bus error: 10
$
```

### <a name="no-stack-trace-after-attach"></a>Nessuna analisi dello stack dopo l'associazione

```bash
$ "/path/to/arm-linux-androideabi-gdb" -x "Project/gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
Copyright (C) 2011 Free Software Foundation, Inc.
...
(gdb) bt
No stack.
```

Questo problema indica in genere che i contenuti della directory `gdb-symbols` non sono sincronizzati con la destinazione Android. La destinazione Android potrebbe essere stata cambiata.

Eliminare la directory `gdb-symbols` e riprovare.
