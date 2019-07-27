---
ms.openlocfilehash: e383bbccd4e76be8a208f5680e5cf21e45a0dbc3
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511936"
---

La riga di comando seguente consente di specificare una build di rilascio della soluzione **SOLUTION_FILE. sln** per iPhone. È possibile impostare la posizione dell'IPA specificando la `IpaPackageDir` proprietà nella riga di comando:

- Sul Mac, usando **xbuild**:

        xbuild /p:Configuration="Release" \ 
           /p:Platform="iPhone" \ 
           /p:IpaPackageDir="$HOME/Builds" \
           /t:Build MyProject.sln

Il comando **xbuild** si trova in genere nella directory **/Library/Frameworks/mono.Framework/Commands**.

- In Windows usando **MSBuild**:

        msbuild /p:Configuration="Release" 
            /p:Platform="iPhone" 
            /p:IpaPackageDir="%USERPROFILE%\Builds" 
            /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
            /t:Build MyProject.sln


**MSBuild** non espanderà `$( )` automaticamente le espressioni passate dalla riga di comando. Per questo motivo è consigliabile usare un percorso completo quando si imposta la dalla `IpaPackageDir` riga di comando.

Vedere le [Note sulla versione per iOS 9,8](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_9/xamarin.ios_9.8.md#new-msbuild-property-ipapackagedir-to-customize-ipa-output-location) per ulteriori informazioni sulla `IpaPackageDir` proprietà.
