---
title: Dove trovare il file dsym per aggiungere i log di arresto anomalo iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/09/2018
ms.openlocfilehash: 0b8f3aa736cba6e70fbf346766499c23a9bbe270
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114067"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>Dove trovare il file dsym per aggiungere i log di arresto anomalo iOS?

Quando si compila un'app per iOS con Visual Studio per Mac o Visual Studio 2017, il file dsym che ti serve per simbolicizzerà arresti anomali del sistema verrà inserito nella stessa gerarchia di directory del file di progetto dell'app (con estensione csproj). La posizione esatta dipende dalle impostazioni di compilazione del progetto:

- Se è stato abilitato compilazioni specifiche per dispositivo, di dsym è reperibile nella directory seguente:

    **&lt;directory del progetto&gt;/bin/&lt;platform&gt;/&lt;configuration&gt;/device-builds /&lt;dispositivo&gt; - &lt; versione del sistema operativo&gt;/**

    Ad esempio:
  
    **TestApp/bin/iPhone/Release/device-builds/iphone8.4-11.3.1/**

- Se non è stato abilitato compilazioni specifiche per dispositivo, di dsym è reperibile nella directory seguente:

    **&lt;directory del progetto&gt;/bin/&lt;platform&gt;/&lt;configurazione&gt;/**

    Ad esempio:

    **TestApp/bin/iPhone/rilascio /**

> [!NOTE]
> Come parte del processo di compilazione, Visual Studio 2017 consente di copiare il file dsym dall'host di compilazione Mac per Windows. Se non è possibile visualizzare un file dsym su Windows, assicurarsi di aver configurato le impostazioni di compilazione dell'app per [creare un file con estensione IPA](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="see-also"></a>Vedere anche

- [Symbolicating iOS i file di arresto anomalo del sistema (xamarin. IOS)](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Demistificazione dei log di arresto anomalo dell'applicazione iOS](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)

