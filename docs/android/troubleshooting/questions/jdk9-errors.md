---
title: Xamarin.Android and Java Development Kit 9
description: Questo articolo illustra come risolvere Java Development Kit (JDK) 9 o versioni successive errori in xamarin. Android.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: d8c64ff79367d93e282edd9534ffb98f5bb90c93
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153330"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Xamarin. Android e Java Development Kit 9 o versione successiva

_Questo articolo illustra come risolvere Java Development Kit (JDK) 9 o versioni successive errori in xamarin. Android._


## <a name="overview"></a>Panoramica

Xamarin. Android Usa Java Development Kit (JDK) per l'integrazione con Android SDK per compilare le app Android ed eseguire Android designer. Le versioni più recenti di Android SDK (API 24 e versioni successive) richiedono JDK 8 (1.8) o l'anteprima di OpenJDK per dispositivi mobili di Microsoft. **Poiché gli strumenti di Android SDK disponibili da Google non sono ancora compatibili con JDK 9, xamarin. Android non funziona con JDK 9 o versione successiva.**

## <a name="jdk-errors"></a>Errori JDK

Se si prova a compilare un progetto xamarin. Android con una versione di JDK successiva rispetto a JDK 8, si otterrà un errore esplicito che indica che questa versione di JDK non è supportata. Ad esempio:

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors  
```

Per risolvere questi errori, è necessario installare JDK 8 (1.8) come illustrato in [come aggiornare la versione di Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md).
In alternativa, è possibile installare il [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md) The Microsoft Mobile OpenJDK sostituirà JDK 8 per lo sviluppo di xamarin. Android.


## <a name="checking-the-jdk-version"></a>Controllo della versione JDK

È possibile verificare quale versione di Java installata immettendo il comando seguente (il pacchetto JDK `bin` directory deve essere il `PATH`):

```shell
java -version
```

Se è installato JDK 9, si verrà visualizzato un messaggio simile al seguente:

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

Se è installato JDK 9 o versioni successive, è necessario installare Java JDK 8 (1.8) o l'anteprima di OpenJDK per dispositivi mobili di Microsoft. Per informazioni sull'installazione di JDK 8, vedere [come aggiornare la versione di Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md). Per informazioni su come installare OpenJDK per dispositivi mobili Microsoft, vedere [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md).

Si noti che non è necessario disinstallare una versione successiva del pacchetto JDK; Tuttavia, è necessario assicurarsi che Xamarin Usa JDK 8 invece di una versione successiva di JDK. In Visual Studio, fare clic su **strumenti > Opzioni > Xamarin > Impostazioni Android**. Se **percorso di Java Development Kit** non è impostata su un percorso JDK 8 (ad esempio **c:\\programmi\\Java\\jdk1.8.0_111**), fare clic su **modifica**  e impostarla per la posizione in cui è installato JDK 8. In Visual Studio per Mac, passare a **Preferenze > progetti > percorsi SDK > Android > Java SDK (JDK)** e fare clic su **Sfoglia** aggiornare questo percorso.

## <a name="known-issues-with-jdk-9"></a>Problemi noti relativi a JDK 9

### <a name="apksigner"></a>apksigner

Si verifica un problema noto con apksigner e JDK 9 in cui il `apksigner.bat` file richiama il `apksigner.jar` con `-Djava.ext.dirs` invece di `-classpath` che ne prevede JDK 9. È consigliabile usare JDK 8 (1.8). Per informazioni sull'installazione di JDK 8, vedere [come aggiornare la versione di Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md)

Se è installato JDK 9, assicurarsi che il percorso seguente non è impostato sulle `PATH` variabile di ambiente perché punteranno ancora alla JDK 9: `C:\ProgramData\Oracle\Java\javapath`. Dopo la rimozione, `java-version` alla riga di comando deve visualizzare JDK 8.
