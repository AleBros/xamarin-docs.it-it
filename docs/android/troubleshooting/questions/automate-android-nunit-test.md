---
title: Come si può automatizzare un progetto di test Android NUnit?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: 94a0bddcb3a9a1e7236bed4b4c95fc38e1f9f0dd
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510429"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Come si può automatizzare un progetto di test Android NUnit?

> [!NOTE]
> Questa guida illustra come automatizzare un progetto di test di Android NUnit, non un progetto Novell. UITest. Le guide Novell. UITest sono disponibili [qui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

Quando si crea un progetto di **app per unit test (Android)** in Visual Studio (o in un progetto di **unit test Android** in Visual Studio per Mac), questo progetto non eseguirà automaticamente i test per impostazione predefinita.
Per eseguire test NUnit in un dispositivo di destinazione, è possibile creare una sottoclasse [Android. app. Instrumentation](xref:Android.App.Instrumentation) avviata usando il comando seguente: 

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
    In questo file, `Xamarin.Android.NUnitLite.TestSuiteInstrumentation` (da **Novell. Android. NUnitLite. dll**) viene sottoclassato per la `TestInstrumentation`creazione.

2.  Implementare il `TestInstrumentation` costruttore e il `AddTests` metodo. Il `AddTests` metodo controlla i test effettivamente eseguiti.

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

4.  Usare il comando seguente per eseguire gli unit test. Sostituire `PACKAGE_NAME` con il nome del pacchetto dell'app (il nome del pacchetto si trova nell' `/manifest/@package` attributo dell'app che si trova in **file AndroidManifest. XML**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

5.  Facoltativamente, è possibile modificare il `.csproj` file per aggiungere la `RunTests` destinazione MSBuild. In questo modo è possibile richiamare gli unit test con un comando simile al seguente:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```
    Si noti che l'uso di questa nuova destinazione non è obbligatorio. `adb` è possibile usare il comando precedente `msbuild`invece di.

Per altre informazioni sull'uso del `adb shell am instrument` comando per l'esecuzione di unit test, vedere l'argomento relativo agli sviluppatori Android [che eseguono test con ADB](https://developer.android.com/studio/test/command-line.html#RunTestsDevice) .


> [!NOTE]
> Con la versione [Novell. Android 5,0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) , i nomi di pacchetto predefiniti per Android Callable Wrapper saranno basati sull'md5sum del nome qualificato dall'assembly del tipo esportato. In questo modo è possibile fornire lo stesso nome completo da due assembly diversi e non ottenere un errore di creazione del pacchetto. Assicurarsi quindi di usare la `Name` proprietà `Instrumentation` nell'attributo per generare un nome di classe o ACW leggibile.

_Il nome di ACW deve essere usato nel `adb` comando riportato sopra_.
Per ridenominare/effettuare il C# refactoring della classe, `RunTests` è necessario modificare il comando in modo da usare il nome di ACW corretto.

