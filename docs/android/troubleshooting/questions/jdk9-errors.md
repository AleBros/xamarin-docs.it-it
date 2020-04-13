---
title: Kit di sviluppo Xamarin.Android e Java 9
description: In questo articolo viene illustrato come risolvere gli errori Java Development Kit (JDK) 9 o versioni successive in Xamarin.Android.This article explains how to resolve Java Development Kit (JDK) 9 or later errors in Xamarin.Android.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 2ea7c9b9f900bc339d183c2f5b317792ebec5232
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026838"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Xamarin.Android e Java Development Kit 9 o versioni successive

_In questo articolo viene illustrato come risolvere gli errori Java Development Kit (JDK) 9 o versioni successive in Xamarin.Android.This article explains how to resolve Java Development Kit (JDK) 9 or later errors in Xamarin.Android._

## <a name="overview"></a>Panoramica

Xamarin.Android utilizza Java Development Kit (JDK) per l'integrazione con Android SDK per la creazione di applicazioni Android e l'esecuzione della finestra di progettazione Android. Le versioni più recenti di Android SDK (API 24 e versioni successive) richiedono JDK 8 (1.8) o Microsoft Mobile OpenJDK Preview. **Poiché gli strumenti di Android SDK disponibili da Google non sono ancora compatibili con JDK 9, Xamarin.Android non funziona con JDK 9 o versioni successive.**

## <a name="jdk-errors"></a>Errori JDK

Se si tenta di compilare un progetto Xamarin.Android con una versione di JDK successiva a JDK 8, si otterrà un errore esplicito che indica che questa versione di JDK non è supportata. Ad esempio:

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors
```

Per risolvere questi errori, è necessario installare JDK 8 (1.8) come spiegato in [Come si aggiorna la versione Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md).
In alternativa, è possibile installare [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md) Microsoft Mobile OpenJDK alla fine sostituirà JDK 8 per lo sviluppo di Xamarin.Android.

## <a name="checking-the-jdk-version"></a>Verifica della versione di JDK

È possibile verificare quale versione di Java è stata installata `bin` immettendo il `PATH`seguente comando (la directory JDK deve essere nella propria ):

```shell
java -version
```

Se jDK 9 è installato, verrà visualizzato un messaggio simile al seguente:

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

Se è installato JDK 9 o versione successiva, è necessario installare Java JDK 8 (1.8) o Microsoft Mobile OpenJDK Preview. Per informazioni su come installare JDK 8, vedere [Come si aggiorna la versione Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md). Per informazioni su come installare Microsoft Mobile OpenJDK, vedere [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md).

Si noti che non è necessario disinstallare una versione successiva di JDK; tuttavia, è necessario assicurarsi che Xamarin utilizzi JDK 8 anziché una versione JDK successiva. In Visual Studio fare clic su **Strumenti > Opzioni > Impostazioni Xamarin > Android**. Se **Java Development Kit Location** non è impostato su un percorso JDK 8 (ad esempio **C:\\\\Programmi Java\\jdk1.8.0_111**), fare clic su **Cambia** e impostarlo sul percorso in cui è installato JDK 8. In Visual Studio per Mac passare a **Preferenze > Progetti > SDK percorsi > Android > Java SDK (JDK)** e fare clic su **Sfoglia** per aggiornare questo percorso.

## <a name="known-issues-with-jdk-9"></a>Problemi noti con JDK 9

### <a name="apksigner"></a>apksigner

Esiste un problema noto con apksigner e `apksigner.bat` JDK `apksigner.jar` 9 `-Djava.ext.dirs` in `-classpath` cui il file richiama il with anziché il previsto da JDK 9. Si consiglia di utilizzare JDK 8 (1.8). Per informazioni su come installare JDK 8, vedere [Come si aggiorna la versione Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md)

Se è stato installato JDK 9, assicurarsi che `PATH` il percorso seguente non sia impostato `C:\ProgramData\Oracle\Java\javapath`sulla variabile di ambiente in quanto punterà comunque a JDK 9: . Dopo averlo `java-version` rimosso, alla riga di comando dovrebbe mostrare JDK 8.
