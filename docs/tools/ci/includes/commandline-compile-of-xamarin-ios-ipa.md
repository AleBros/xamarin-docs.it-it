---
ms.openlocfilehash: 31c8d1b781648f2d9c69062c52e71478fc7a496b
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "69529107"
---

La riga di comando seguente consente di specificare una build di rilascio della soluzione **SOLUTION_FILE. sln** per iPhone. È possibile impostare la posizione dell'IPA specificando la proprietà `IpaPackageDir` nella riga di comando:

- Sul Mac, usando **xbuild**:

  ```
  xbuild /p:Configuration="Release" \ 
         /p:Platform="iPhone" \ 
         /p:IpaPackageDir="$HOME/Builds" \
         /t:Build MyProject.sln
  ```

Il comando **xbuild** si trova in genere nella directory **/Library/Frameworks/mono.Framework/Commands**.

- In Windows usando **MSBuild**:

  ```
  msbuild /p:Configuration="Release" 
          /p:Platform="iPhone" 
          /p:IpaPackageDir="%USERPROFILE%\Builds" 
          /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
          /t:Build MyProject.sln
  ```

**MSBuild** non espanderà automaticamente `$( )` espressioni passate dalla riga di comando. Per questo motivo è consigliabile usare un percorso completo quando si imposta il `IpaPackageDir` dalla riga di comando.

Vedere le [Note sulla versione per iOS 9,8](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_9/xamarin.ios_9.8.md#new-msbuild-property-ipapackagedir-to-customize-ipa-output-location) per altri dettagli sulla proprietà `IpaPackageDir`.
