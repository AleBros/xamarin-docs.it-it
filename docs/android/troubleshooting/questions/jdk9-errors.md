---
title: Novell. Android e Java Development Kit 9
description: Questo articolo illustra come risolvere gli errori di Java Development Kit (JDK) 9 o versioni successive in Novell. Android.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 3f1f49a5f63d8159644bd2bb247287a91c27023a
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70225549"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Novell. Android e Java Development Kit 9 o versione successiva

_Questo articolo illustra come risolvere gli errori di Java Development Kit (JDK) 9 o versioni successive in Novell. Android._


## <a name="overview"></a>Panoramica

Novell. Android usa Java Development Kit (JDK) per l'integrazione con i Android SDK per la creazione di app Android e l'esecuzione di Android designer. Le versioni più recenti del Android SDK (API 24 e versioni successive) richiedono JDK 8 (1,8) o Microsoft Mobile OpenJDK Preview. **Poiché gli strumenti di Android SDK disponibili in Google non sono ancora compatibili con JDK 9, Novell. Android non funziona con JDK 9 o versioni successive.**

## <a name="jdk-errors"></a>Errori JDK

Se si prova a compilare un progetto Novell. Android con una versione di JDK successiva a JDK 8, verrà generato un errore esplicito che indica che la versione di JDK non è supportata. Ad esempio:

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors
```

Per risolvere questi errori, è necessario installare JDK 8 (1,8) come illustrato in [ricerca per categorie aggiornare la versione di Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md).
In alternativa, è possibile installare [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md) . Microsoft Mobile OpenJDK sostituirà infine JDK 8 per lo sviluppo di Novell. Android.


## <a name="checking-the-jdk-version"></a>Verifica della versione JDK

È possibile verificare la versione di Java installata immettendo il comando seguente (la directory JDK `bin` deve trovarsi `PATH`nel):

```shell
java -version
```

Se JDK 9 è installato, viene visualizzato un messaggio simile al seguente:

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

Se è installato JDK 9 o versione successiva, è necessario installare Java JDK 8 (1,8) o Microsoft Mobile OpenJDK Preview. Per informazioni su come installare JDK 8, vedere [ricerca per categorie aggiornare la versione di Java Development Kit (JDK)](~/android/troubleshooting/questions/update-jdk.md). Per informazioni sull'installazione di Microsoft Mobile OpenJDK, vedere [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md).

Si noti che non è necessario disinstallare una versione successiva del JDK. Tuttavia, è necessario assicurarsi che Novell usi JDK 8 anziché una versione successiva di JDK. In Visual Studio fare clic su **strumenti > opzioni > novell > Android Settings**. Se il **percorso di Java Development Kit** non è impostato su un percorso JDK 8, ad esempio **\\C:\\Program\\Files Java JDK 1.8.0 _111**, fare clic su **Cambia** e impostarlo sul percorso in cui è installato JDK 8. In Visual Studio per Mac passare a **preferenze > progetti > percorsi SDK > Android > Java SDK (JDK)** e fare clic su **Sfoglia** per aggiornare questo percorso.

## <a name="known-issues-with-jdk-9"></a>Problemi noti relativi a JDK 9

### <a name="apksigner"></a>apksigner

Si è verificato un problema noto con apksigner e JDK 9 in cui `apksigner.bat` il file richiama `apksigner.jar` con `-Djava.ext.dirs` invece di `-classpath` quale JDK 9 prevede. È consigliabile usare JDK 8 (1,8). Per informazioni su come installare JDK 8, vedere [ricerca per categorie aggiornare la versione di Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md)

Se è stato installato JDK 9, verificare che il percorso seguente non sia impostato sulla `PATH` variabile di ambiente, perché ancora punta a JDK 9:. `C:\ProgramData\Oracle\Java\javapath` Dopo la rimozione, `java-version` in una riga di comando dovrebbe essere visualizzato JDK 8.
