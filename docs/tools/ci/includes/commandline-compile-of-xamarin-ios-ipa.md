---
ms.openlocfilehash: ef16224b9ef060ff8643211b8e1f0c767c518bdf
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841580"
---

La riga di comando seguente per specificare una build di rilascio della soluzione **SOLUTION_FILE.sln** per iPhone. Il percorso del pacchetto IPA può essere impostato specificando il `IpaPackageDir` proprietà nella riga di comando:

- Nel Mac, usando **xbuild**:

        xbuild /p:Configuration="Release" \ 
           /p:Platform="iPhone" \ 
           /p:IpaPackageDir="$HOME/Builds" \
           /t:Build MyProject.sln

Il **xbuild** comando si trova in genere nella directory **/Library/Frameworks/Mono.framework/Commands**.

- In Windows, utilizzando **msbuild**:

        msbuild /p:Configuration="Release" 
            /p:Platform="iPhone" 
            /p:IpaPackageDir="%USERPROFILE%\Builds" 
            /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
            /t:Build MyProject.sln


**MSBuild** non si espanderà automaticamente `$( )` espressioni passati dalla riga di comando. Per questo motivo, è consigliabile usare un percorso completo quando si impostano le `IpaPackageDir` nella riga di comando.


Vedere le [note sulla versione per iOS 9.8](https://developer.xamarin.com/releases/ios/xamarin.ios_9/xamarin.ios_9.8/#New_MSBuild_property_IpaPackageDir_to_customize_.ipa_output_location) per altre informazioni sul `IpaPackageDir` proprietà.
