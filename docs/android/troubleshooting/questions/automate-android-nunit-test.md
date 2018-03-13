---
title: Come automatizzare un progetto di NUnit Test Android?
ms.topic: article
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 11b693193b36a80b55a61308d98b76f4f6984e8a
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Come automatizzare un progetto di NUnit Test Android?

> [!NOTE]
> Questa guida illustra i passaggi per configurare un progetto di test NUnit Android, non a un progetto UITest. Sono disponibili le guide UITest [qui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

Quando si crea un progetto Android Unit Test [Visual Studio per Mac] o l'applicazione Unit Test (Android) [Visual Studio], per impostazione predefinita non eseguirà automaticamente i test.
Per automatizzare android Unit Test: per eseguire test NUnit in un dispositivo di destinazione, viene usato un `Android.App.Instrumentation` sottoclasse, che può essere creata e può essere eseguita tramite il `adb shell am instrument` comando.

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

*Nota importante*: con il [xamarin 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) versione, i nomi dei pacchetti predefinito per Android Callable Wrapper saranno basate su MD5SUM del nome completo dell'assembly del tipo da esportare. In questo modo lo stesso nome completo essere fornito da due assembly diversi e non ottenere un errore del pacchetto. Assicurarsi di utilizzare il \`nome\` proprietà il \`strumentazione\` un attributo per generare un nome di classe ACW leggibile.

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

