---
title: Come automatizzare un progetto di NUnit Test Android?
ms.topic: article
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: acb213e8c73013bc9b2482afb45296c4e1f61ab5
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Come automatizzare un progetto di NUnit Test Android?

> [!NOTE]
> Questa guida illustra i passaggi per configurare un progetto di test NUnit Android, non a un progetto UITest. Sono disponibili le guide UITest [qui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

Quando si crea un progetto di Unit Test Android in Visual Studio per Mac o un'App Unit Test (Android) in Visual Studio, per impostazione predefinita non eseguirà automaticamente i test.
Per eseguire test NUnit su un dispositivo di destinazione, viene usata un' `Android.App.Instrumentation` sottoclasse, che può essere creata ed eseguita usando la `adb shell am instrument` comando.

È necessario innanzitutto creare il **TestInstrumentation.cs** file, che crea una sottoclasse di `Xamarin.Android.NUnitLite.TestSuiteInstrumentation` (dichiarato in `Xamarin.Android.NUnitLite.dll`). Il `TestInstrumentation(IntPtr, JniHandleOwnership)` costruttore _deve_ essere fornito e virtuale `AddTests()` deve eseguire l'override di metodo.
`AddTests()` controlli vengono eseguiti i test. Questo file è in gran parte standard.

Successivamente, il `.csproj` deve essere modificato per aggiungere `TestInstrumentation.cs`.

Facoltativamente, il `.csproj` può essere modificato per aggiungere il `RunTests` destinazione MSBuild, consentendo ad richiamando gli unit test come:

```shell
msbuild /t:RunTests Project.csproj
```

L'utilizzo di una nuova destinazione non è obbligatorio; il comando adb corrispondente potrebbe invece essere utilizzato:

```shell
adb shell am instrument -w @PACKAGE_NAME@/app.tests.TestInstrumentation
```

Sostituire `@PACKAGE\_NAME@` come appropriato, ma è anche il valore presente nel **AndroidManifest.xml** `/manifest/@package` attributo.


> [!NOTE]
> *Importante*: con il [xamarin. Android 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) versione, i nomi dei pacchetti predefinito per Android Callable Wrapper saranno basate su MD5SUM del nome qualificato dall'assembly del tipo che si desidera esportare. In questo modo lo stesso nome completo essere fornito da due assembly diversi e non ottenere un errore del pacchetto. Assicurarsi di utilizzare il \`nome\` proprietà il \`strumentazione\` un attributo per generare un nome di classe ACW leggibile.

_Il nome ACW deve essere utilizzato nel `adb` comando_. Ridenominazione o il refactoring di classe c# sarà pertanto necessario modificare il `RunTests` comando da utilizzare il nome ACW corretto.

Aggiunte al file con estensione csproj:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Project DefaultTargets="Build" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <Compile Include="TestInstrumentation.cs" />
    </ItemGroup>
    <Target Name="RunTests" DependsOnTargets="_ValidateAndroidPackageProperties">
        <Exec Command="&quot;$(_AndroidPlatformToolsDirectory)adb&quot; $(AdbTarget) $(AdbOptions) shell am instrument -w $(_AndroidPackage)/app.tests.TestInstrumentation" />
    </Target>
</Project>
```

**TestInstruments.cs**:

```cs 
using System;
using System.Reflection;
 
using Android.App;
using Android.Content;
using Android.Runtime;
 
using Xamarin.Android.NUnitLite;
 
namespace App.Tests {
 
    [Instrumentation(Name="app.tests.TestInstrumentation")]
    public class TestInstrumentation : TestSuiteInstrumentation {
 
        public TestInstrumentation (IntPtr handle, JniHandleOwnership transfer) : base (handle, transfer)
        {
        }
 
        protected override void AddTests ()
        {
            AddTest (Assembly.GetExecutingAssembly ());
        }
    }
}
```

