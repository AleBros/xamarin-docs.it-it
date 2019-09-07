---
title: Dove si può trovare il file dSYM per aggiungere simboli di debug ai log di arresto anomalo iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/09/2018
ms.openlocfilehash: cc681e94187530c5442ad4fb12557017b92b2bb4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769225"
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
