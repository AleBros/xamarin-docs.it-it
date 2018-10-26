---
title: Supporto di Windows di UrhoSharp
description: Questo documento viene descritto il supporto di Windows per UrhoSharp. Viene descritto come creare un progetto, configurare e avviare Urho, si integrano con WPF e integrare con UWP.
ms.prod: xamarin
ms.assetid: A4F36014-AE4E-4F07-A1AC-F264AAA68ACF
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 8aca028ec1015616a9884cd09b7ffa5e04f2e43d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119605"
---
# <a name="urhosharp-windows-support"></a>Supporto di Windows di UrhoSharp

Sebbene Urho è una libreria di classi portabile e consente la stessa API da usare nell'intera piattaforma diverse per la logica del gioco, devi comunque inizializzare Urho nel driver specifici della piattaforma e in alcuni casi, è possibile sfruttare le funzionalità specifiche della piattaforma .

Nelle pagine seguenti, si supponga che `MyGame` è una sottoclasse del `Application` classe.

**Architetture supportate:** solo 64 bit di Windows.

È possibile visualizzare esempi completi che illustrano come utilizzare questa opzione nel nostro [esempi](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples)

## <a name="standalone-project"></a>Progetto autonomo

### <a name="creating-a-project"></a>Creazione di un progetto

Creare un progetto Console, fare riferimento a Urho NuGet e quindi assicurarsi che sia possibile individuare gli asset (la directory che contiene la directory dati).

### <a name="configuring-and-launching-urho"></a>Configurando e avviando Urho

Per avviare l'applicazione, eseguire questa operazione:

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

### <a name="example"></a>Esempio

[Esempio completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Desktop)

## <a name="integrated-with-wpf"></a>Integrato con WPF

### <a name="creating-a-project"></a>Creazione di un progetto

Creare un progetto WPF, fare riferimento a Urho NuGet e quindi assicurarsi che sia possibile individuare gli asset (la directory che contiene la directory dati).

### <a name="configuring-and-launching-urho-from-wpf"></a>Configurando e avviando Urho da WPF

Creare una sottoclasse di `Window` e configurare gli asset simile al seguente:

```csharp
    public partial class MainWindow : Window
    {
        Application currentApplication;

        public MainWindow()
        {
            InitializeComponent();
            DesktopUrhoInitializer.AssetsDirectory = @"../../Assets";
            Loaded += (s,e) => RunGame (new MyGame ());
        }

        async void RunGame(MyGame game)
        {
            var urhoSurface = new Panel { Dock = DockStyle.Fill };
            WindowsFormsHost.Child = urhoSurface;
            WindowsFormsHost.Focus();
            urhoSurface.Focus();
            await Task.Yield();
            var appOptions = new ApplicationOptions(assetsFolder: "Data")
                {
                    ExternalWindow = RunInSdlWindow.IsChecked.Value ? IntPtr.Zero : urhoSurface.Handle,
                    LimitFps = false, //true means "limit to 200fps"
                };
            currentApplication = Urho.Application.CreateInstance(value.Type, appOptions);
            currentApplication.Run();
        }
    }
```

### <a name="example"></a>Esempio

[Esempio completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/WPF)

## <a name="integrated-with-uwp"></a>Integrato con UWP

### <a name="creating-a-project"></a>Creazione di un progetto

Creare un progetto UWP, fare riferimento a Urho NuGet e quindi assicurarsi che sia possibile individuare gli asset (la directory che contiene la directory dati).

### <a name="configuring-and-launching-urho-from-uwp"></a>Configurando e avviando Urho dalla piattaforma UWP

Creare una sottoclasse di `Window` e configurare gli asset simile al seguente:

```csharp
{
            InitializeComponent();
            GameTypes = typeof(Sample).GetTypeInfo().Assembly.GetTypes()
                .Where(t => t.GetTypeInfo().IsSubclassOf(typeof(Application)) && t != typeof(Sample))
                .Select((t, i) => new TypeInfo(t, $"{i + 1}. {t.Name}", ""))
                .ToArray();
            DataContext = this;
            Loaded += (s, e) => RunGame (new MyGame ());
        }

        public void RunGame(TypeInfo value)
        {
            //at this moment, UWP supports assets only in pak files (see PackageTool)
            currentApplication = UrhoSurface.Run(value.Type, "Data.pak");
        }
    }
```

### <a name="example"></a>Esempio

[Esempio completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/UWP)

## <a name="integrated-with-windowsforms"></a>Integrato con Windows. Forms

### <a name="creating-a-project"></a>Creazione di un progetto

Creare un progetto di Windows. Forms, fanno riferimento a Urho NuGet e quindi assicurarsi che sia possibile individuare gli asset (la directory che contiene la directory dati).

### <a name="configuring-and-launching-urho-from-windowsforms"></a>Configurando e avviando Urho da Windows. Forms

Avviare Urho dal form, vedere [esempio completo](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/WinForms/SamplesForm.cs)
