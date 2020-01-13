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
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026995"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>Come si può automatizzare un progetto di test Android NUnit?

> [!NOTE]
> Questa guida illustra come automatizzare un progetto di test di Android NUnit, non un progetto Xamarin.UITest. Le guide Xamarin.UITest sono disponibili [qui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest).

Quando si crea un progetto di **app per unit test (Android)** in Visual Studio (o in un progetto di **unit test Android** in Visual Studio per Mac), questo progetto non eseguirà automaticamente i test per impostazione predefinita.
Per eseguire test NUnit in un dispositivo di destinazione, è possibile creare una sottoclasse [Android. app. Instrumentation](xref:Android.App.Instrumentation) avviata usando il comando seguente: 

```shell
adb shell am instrument 
```

I passaggi seguenti illustrano questo processo:

1. Creare un nuovo file denominato **TestInstrumentation.cs**: 

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

    In questo file `Xamarin.Android.NUnitLite.TestSuiteInstrumentation` (da **Xamarin.Android. NUnitLite. dll**) viene sottoclassato per creare `TestInstrumentation`.

2. Implementare il costruttore `TestInstrumentation` e il metodo `AddTests`. Il metodo `AddTests` controlla i test effettivamente eseguiti.

3. Modificare il file di `.csproj` per aggiungere **TestInstrumentation.cs**. Esempio:

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

4. Usare il comando seguente per eseguire gli unit test. Sostituire `PACKAGE_NAME` con il nome del pacchetto dell'app (il nome del pacchetto è disponibile nell'attributo `/manifest/@package` dell'app che si trova in **file AndroidManifest. XML**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

5. Facoltativamente, è possibile modificare il file di `.csproj` per aggiungere la destinazione `RunTests` MSBuild. In questo modo è possibile richiamare gli unit test con un comando simile al seguente:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```

    Si noti che l'uso di questa nuova destinazione non è obbligatorio. è possibile usare il comando `adb` precedente invece di `msbuild`.

Per altre informazioni sull'uso del comando `adb shell am instrument` per l'esecuzione di unit test, vedere l'argomento relativo agli sviluppatori Android [che eseguono test con ADB](https://developer.android.com/studio/test/command-line.html#RunTestsDevice) .

> [!NOTE]
> Con la versione [Xamarin.Android 5,0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) , i nomi di pacchetto predefiniti per Android Callable Wrapper saranno basati sull'md5sum del nome qualificato dall'assembly del tipo esportato. In questo modo è possibile fornire lo stesso nome completo da due assembly diversi e non ottenere un errore di creazione del pacchetto. Assicurarsi quindi di usare la proprietà `Name` sull'attributo `Instrumentation` per generare un nome di classe ACW/classe leggibile.

_Il nome di ACW deve essere usato nel comando `adb` precedente_.
La ridenominazione/refactoring C# della classe richiede pertanto la modifica del comando`RunTests`per usare il nome di ACW corretto.
