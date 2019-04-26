---
title: Come si può automatizzare un progetto di test Android NUnit?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: b785ef171d2cb00d4f8f5a17f37d49de17fd3da9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153295"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Come si può automatizzare un progetto di test Android NUnit?

> [!NOTE]
> Questa guida illustra come automatizzare un progetto di test Android NUnit non a un progetto xamarin. UITest. Sono disponibili le guide di xamarin. UITest [qui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

Quando si crea una **App Unit Test (Android)** progetto in Visual Studio (oppure **Unit Test Android** progetto in Visual Studio per Mac), questo progetto viene automaticamente eseguire i test per impostazione predefinita.
Per eseguire test NUnit in un dispositivo di destinazione, è possibile creare un [Android.App.Instrumentation](https://developer.xamarin.com/api/type/Android.App.Instrumentation/) sottoclasse che viene avviata usando il comando seguente: 

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
    In questo file [Xamarin.Android.NUnitLite.TestSuiteInstrumentation](https://developer.xamarin.com/api/type/Xamarin.Android.NUnitLite.TestSuiteInstrumentation/) (dal **Xamarin.Android.NUnitLite.dll**) è una sottoclasse per creare `TestInstrumentation`.

2.  Implementare il [TestInstrumentation](https://developer.xamarin.com/api/constructor/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.TestSuiteInstrumentation/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) costruttore e il [AddTests](https://developer.xamarin.com/api/member/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.AddTests%28%29) (metodo). Il `AddTests` quali test vengono eseguiti i controlli di metodo.

3.  Modificare il `.csproj` file per aggiungere **TestInstrumentation.cs**. Ad esempio:

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

3.  Usare il comando seguente per eseguire gli unit test. Sostituire `PACKAGE_NAME` con il nome del pacchetto dell'app (sono disponibili il nome del pacchetto dell'app `/manifest/@package` attributo che si trova **androidmanifest. XML**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

4.  Facoltativamente, è possibile modificare il `.csproj` file per aggiungere il `RunTests` destinazione MSBuild. Questo rende possibile richiamare gli unit test con un comando simile al seguente:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```
    (Si noti che con questa nuova destinazione non sono necessario; le versioni precedenti `adb` comando può essere usato al posto di `msbuild`.)

Per altre informazioni sull'uso di `adb shell am instrument` comando per eseguire unit test, vedere lo sviluppatore Android [esecuzione di test con ADB](https://developer.android.com/studio/test/command-line.html#RunTestsDevice) argomento.


> [!NOTE]
> Con il [xamarin. Android 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) release, i nomi dei pacchetti predefinita per Android Callable Wrapper si baserà sul MD5SUM del nome qualificato dall'assembly del tipo in fase di esportazione. In questo modo lo stesso nome completo per essere forniti dalle due assembly diversi e non visualizzato un errore di creazione dei pacchetti. Quindi verificare che sia in uso il `Name` proprietà di `Instrumentation` attributo per generare un nome leggibile ACW/classe.

_Il nome ACW deve essere utilizzato nel `adb` comando precedente_.
Refactoring/ridenominazione di C# classe verrà pertanto è necessario modificare il `RunTests` comando per usare il nome corretto ACW.

