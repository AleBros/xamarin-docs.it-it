---
title: Come si può automatizzare un progetto di test Android NUnit?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/29/2018
ms.openlocfilehash: 1246eeac63a0ae232396d4c2fd69d8bf516f5e3e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026995"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Come si può automatizzare un progetto di test Android NUnit?

> [!NOTE]
> Questa guida spiega come automatizzare un progetto di test NUnit Android, non un progetto Xamarin.UITest.This guide explains how to automate an Android NUnit test project, not a Xamarin.UITest project. Le guide Xamarin.UITest sono disponibili [qui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest).

Quando si crea un progetto **di app di unit test (Android)** in Visual Studio (o progetto di unit test **Android** in Visual Studio per Mac), questo progetto non eseguirà automaticamente i test per impostazione predefinita.
Per eseguire i test NUnit in un dispositivo di destinazione, è possibile creare una sottoclasse Android.App.Instrumentation avviata utilizzando il comando seguente:To run NUnit tests on a target device, you can create an [Android.App.Instrumentation](xref:Android.App.Instrumentation) subclass that is started by using the following command: 

```shell
adb shell am instrument 
```

I passaggi seguenti illustrano questo processo:The following steps explain this process:

1. Creare un nuovo file denominato **TestInstrumentation.cs:** 

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

    In questo `Xamarin.Android.NUnitLite.TestSuiteInstrumentation` file ( da **Xamarin.Android.NUnitLite.dll**) `TestInstrumentation`è sottoclassato per creare .

2. Implementare `TestInstrumentation` il `AddTests` costruttore e il metodo. Il `AddTests` metodo controlla quali test vengono effettivamente eseguiti.

3. Modificare `.csproj` il file per aggiungere **TestInstrumentation.cs**file . Ad esempio:

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

4. Utilizzare il comando seguente per eseguire gli unit test. Sostituire `PACKAGE_NAME` con il nome del pacchetto dell'app (il nome `/manifest/@package` del pacchetto è disponibile nell'attributo dell'app che si trova in **AndroidManifest.xml**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

5. Facoltativamente, è possibile `.csproj` modificare il `RunTests` file per aggiungere la destinazione MSBuild.Optionally, you can modify the file to add the MSBuild target. In questo modo è possibile richiamare gli unit test con un comando simile al seguente:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```

    (Si noti che l'utilizzo di `adb` questa nuova destinazione `msbuild`non è necessario; il comando precedente può essere utilizzato al posto di .)

Per altre informazioni `adb shell am instrument` sull'uso del comando per eseguire unit test, vedere l'argomento Android Developer Running Tests with ADB (Esegui [test con ADB).](https://developer.android.com/studio/test/command-line.html#RunTestsDevice)

> [!NOTE]
> Con la versione [Xamarin.Android 5.0,](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) i nomi di pacchetto predefiniti per Android Callable Wrapper saranno basati sull'MD5SUM del nome qualificato dall'assembly del tipo esportato. In questo modo lo stesso nome completo può essere fornito da due assembly diversi e non viene visualizzato un errore di creazione di pacchetti. Assicurarsi quindi di `Name` utilizzare la `Instrumentation` proprietà sull'attributo per generare un nome ACW/classe leggibile.

_Il nome ACW deve `adb` essere utilizzato nel comando precedente._
La ridenominazione/refactoring della classe C, pertanto, richiederà la modifica del `RunTests` comando per l'utilizzo del nome ACW corretto.
