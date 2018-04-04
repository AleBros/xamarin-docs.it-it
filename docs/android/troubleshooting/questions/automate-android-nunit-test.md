---
title: Come automatizzare un progetto di NUnit Test Android?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/29/2018
ms.openlocfilehash: f63a25f36682038b7fcd85d711d980b9e3ec869d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Come automatizzare un progetto di NUnit Test Android?

> [!NOTE]
> Questa guida illustra come automatizzare un progetto di test NUnit Android, non a un progetto UITest. Sono disponibili le guide UITest [qui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

Quando si crea un **applicazione Unit Test (Android)** progetto in Visual Studio (o **Android Unit Test** progetto in Visual Studio per Mac), questo progetto verranno automaticamente eseguiti i test per impostazione predefinita.
Per eseguire test NUnit su un dispositivo di destinazione, è possibile creare un [Android.App.Instrumentation](https://developer.xamarin.com/api/type/Android.App.Instrumentation/) sottoclasse che viene avviata usando il comando seguente: 

```shell
adb shell am instrument 
```

I passaggi seguenti illustrano questo processo:

1.  Creare un nuovo file denominato **TestInstrumentation.cs**: 

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
    In questo file, [Xamarin.Android.NUnitLite.TestSuiteInstrumentation](https://developer.xamarin.com/api/type/Xamarin.Android.NUnitLite.TestSuiteInstrumentation/) (dal **Xamarin.Android.NUnitLite.dll**) è una sottoclasse per creare `TestInstrumentation`.

2.  Implementare il [TestInstrumentation](https://developer.xamarin.com/api/constructor/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.TestSuiteInstrumentation/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) costruttore e il [AddTests](https://developer.xamarin.com/api/member/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.AddTests%28%29) metodo. Il `AddTests` quali test vengono eseguiti controlli di metodo.

3.  Modificare il `.csproj` file da aggiungere **TestInstrumentation.cs**. Ad esempio:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project DefaultTargets="Build" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
        ...
        <ItemGroup>
            <Compile Include="TestInstrumentation.cs" />
        </ItemGroup>
        <Target Name="RunTests" DependsOnTargets="_ValidateAndroidPackageProperties">
            <Exec Command="&quot;$(_AndroidPlatformToolsDirectory)adb&quot; $(AdbTarget) $(AdbOptions) shell am instrument -w $(_AndroidPackage)/app.tests.TestInstrumentation" />
        </Target>
        ...
    </Project>
    ```

3.  Utilizzare il comando seguente per eseguire gli unit test. Sostituire `PACKAGE_NAME` con nome del pacchetto dell'app (il nome del pacchetto è reperibile dell'app `/manifest/@package` attributo che si trova **Androidmanifest**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

4.  Facoltativamente, è possibile modificare il `.csproj` file per aggiungere il `RunTests` destinazione MSBuild. In questo modo è possibile richiamare gli unit test con un comando simile al seguente:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```
    (Si noti che tramite questa nuova destinazione non sono necessario; le versioni precedenti `adb` comando può essere utilizzato al posto di `msbuild`.)

Per ulteriori informazioni sull'utilizzo di `adb shell am instrument` comando per eseguire unit test, vedere Android Developer [esecuzione di test con ADB](https://developer.android.com/studio/test/command-line.html#RunTestsDevice) argomento.


> [!NOTE]
> Con il [xamarin. Android 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) versione, i nomi dei pacchetti predefinito per Android Callable Wrapper saranno basate su MD5SUM del nome qualificato dall'assembly del tipo che si desidera esportare. In questo modo lo stesso nome completo essere fornito da due assembly diversi e non ottenere un errore del pacchetto. Pertanto, assicurarsi di utilizzare il `Name` proprietà il `Instrumentation` attributo per generare un nome leggibile ACW/class.

_Il nome ACW deve essere utilizzato nel `adb` comando precedente_.
Ridenominazione o il refactoring di classe c# sarà pertanto necessario modificare il `RunTests` comando da utilizzare il nome ACW corretto.

