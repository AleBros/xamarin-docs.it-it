---
title: Dove si può trovare il file dSYM per aggiungere simboli di debug ai log di arresto anomalo iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/09/2018
ms.openlocfilehash: edd5a2c1ed2efdffc9bd28bb06ac19348615eefc
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292106"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>Dove si può trovare il file dSYM per aggiungere simboli di debug ai log di arresto anomalo iOS?

Quando si compila un'app iOS con Visual Studio per Mac o Visual Studio 2017, il file. dSYM necessario per la simbolizzazione dei report di arresto anomalo del sistema verrà inserito nella stessa gerarchia di directory del file di progetto dell'app (con estensione csproj). Il percorso esatto dipende dalle impostazioni di compilazione del progetto:

- Se sono state abilitate compilazioni specifiche del dispositivo, il. dSYM si trova nella directory seguente:

    **&lt;&gt;dispositivo/Device-Builds/per&lt;la&gt;configurazione&lt;della&gt;piattaforma//bin/&lt;della directory di progetto&gt; - &lt; versione sistema operativo&gt;/**

    Ad esempio:
  
    **TestApp/bin/iPhone/Release/device-builds/iphone8.4-11.3.1/**

- Se non sono state abilitate compilazioni specifiche del dispositivo, il. dSYM si trova nella directory seguente:

    **&lt;configurazione della&gt;piattaforma&lt;/bin/della&gt;directorydiprogetto/&lt;&gt;/**

    Ad esempio:

    **TestApp/bin/iPhone/Release/**

> [!NOTE]
> Come parte del processo di compilazione, Visual Studio 2017 copia il file con estensione dSYM dall'host di compilazione Mac a Windows. Se non viene visualizzato un file con estensione dSYM in Windows, assicurarsi di aver configurato le impostazioni di compilazione dell'app per [creare un file con estensione IPA](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="see-also"></a>Vedere anche

- [File di arresto anomalo Symbolicating iOS (Novell. iOS)](https://www.jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Demistificazione dei log di arresto anomalo dell'applicazione iOS](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)

