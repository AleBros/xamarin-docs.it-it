---
title: Dove trovare il file .dSYM symbolicate i registri di arresto anomalo iOS
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/09/2018
ms.openlocfilehash: 60d897be8739ff5b78a322bc4ea3f43011785bb5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>Dove trovare il file .dSYM symbolicate i registri di arresto anomalo iOS

Quando si compila un'app iOS con Visual Studio per Mac o Visual Studio 2017, il file .dSYM che è necessario per symbolicate segnalazioni di arresti anomali verrà inserito nella stessa gerarchia di directory del file di progetto dell'app (con estensione csproj). La posizione esatta dipende dalle impostazioni di compilazione del progetto:

- Se è stata abilitata compilazioni specifiche del dispositivo, il il .dSYM è reperibile nella directory seguente:

    **&lt;directory del progetto&gt;/bin/&lt;piattaforma&gt;/&lt;configurazione&gt;/device-builds /&lt;dispositivo&gt; - &lt; versione del sistema operativo&gt;/**

    Ad esempio:
  
    **TestApp/bin/iPhone/Release/device-builds/iphone8.4-11.3.1/**

- Se non è stato abilitato compilazioni specifiche del dispositivo, il .dSYM è reperibile nella directory seguente:

    **&lt;directory del progetto&gt;/bin/&lt;piattaforma&gt;/&lt;configurazione&gt;/**

    Ad esempio:

    **TestApp/bin/iPhone/rilascio /**

> [!NOTE]
> Come parte del processo di compilazione, Visual Studio 2017 copia il file .dSYM dall'host di compilazione Mac e Windows. Se non è presente un file .dSYM in Windows, assicurarsi che sono state configurate impostazioni di compilazione dell'app per [creare un file con estensione IPA](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="see-also"></a>Vedere anche

- [Symbolicating iOS di arresto anomalo del file (xamarin)](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Demistificazione iOS i registri di arresto anomalo applicazioni](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)

