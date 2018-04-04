---
title: Xamarin e JDK 9
description: In questo articolo viene illustrato come risolvere gli errori di Java Development Kit (JDK) 9 in xamarin.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 8857823884447f22b7bc5535f43369671d3285bc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinandroid-and-jdk-9"></a>Xamarin e JDK 9

_In questo articolo viene illustrato come risolvere gli errori di Java Development Kit (JDK) 9 in xamarin._


## <a name="overview"></a>Panoramica

Xamarin utilizza Java Development Kit (JDK) per l'integrazione con Android SDK per la compilazione di App per Android e in esecuzione la finestra di progettazione Android. Le versioni più recenti di Android SDK (API 24 e versioni successive) richiedono JDK 8 (1,8). **Poiché gli strumenti di Android SDK disponibili da Google non sono ancora compatibili con JDK 9, xamarin non funziona con JDK 9.**

## <a name="jdk-9-errors"></a>JDK 9 errori

Se si tenta di compilare un progetto xamarin con JDK 9 installato, si otterrà un errore esplicito che indica che JDK 9 non è supportato.

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors  
```

Per risolvere questi errori, è necessario installare JDK 8 (1.8), come illustrato in [come aggiornare la versione di Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md).


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

Se è installato JDK 9, è necessario installare Java JDK 8 (1,8). Per informazioni sull'installazione di JDK 8, vedere [come aggiornare la versione di Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md)

## <a name="known-issues-with-jdk-9"></a>Problemi noti con JDK 9

### <a name="apksigner"></a>apksigner

Si verifica un problema noto con apksigner e 9 JDK in cui il `apksigner.bat` file richiama il `apksigner.jar` con `-Djava.ext.dirs` anziché `-classpath` che prevede di JDK 9. È consigliabile utilizzare JDK 8 (1,8). Per informazioni sull'installazione di JDK 8, vedere [come aggiornare la versione di Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md)

Dopo la disinstallazione di JDK 9, assicurarsi che il percorso seguente non è impostato sul `PATH` variabile di ambiente come punteranno ancora di JDK 9: `C:\ProgramData\Oracle\Java\javapath`. Dopo la rimozione, `java -version` alla riga di comando deve visualizzare JDK 8.
