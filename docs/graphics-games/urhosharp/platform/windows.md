---
title: Supporto di Windows UrhoSharp
description: "Il programma di installazione specifiche di Windows e funzionalità per UrhoSharp."
ms.topic: article
ms.prod: xamarin
ms.assetid: A4F36014-AE4E-4F07-A1AC-F264AAA68ACF
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: 0deefe0e00ec96a21317bfa2e8bf4894d5deae47
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="urhosharp-windows-support"></a>Supporto di Windows UrhoSharp

_Funzionalità e l'installazione di Windows specifico_

Mentre Urho è una libreria di classi portabile e consente la stessa API da utilizzare nella piattaforma diversi per la logica di gioco, è comunque necessario inizializzare Urho nel driver specifico della piattaforma in alcuni casi, è possibile sfruttare le funzionalità specifiche della piattaforma .

Nelle pagine, si supponga che `MyGame` è una sottoclasse di `Application` classe.

**Architetture supportate:** solo 64 bit di Windows.

È possibile visualizzare esempi completi in cui viene illustrato come utilizzare questa funzione è presente il nostro [esempi](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples)

# <a name="standalone-project"></a>Progetto autonomo

### <a name="creating-a-project"></a>Creazione di un progetto

Creare un progetto Console, fare riferimento a Urho NuGet e quindi assicurarsi che sia possibile individuare le risorse (la directory contenente la directory dei dati).

### <a name="configuring-and-launching-urho"></a>La configurazione e avvio Urho

Per avviare l'applicazione, eseguire questa operazione:

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```
### <a name="example"></a>Esempio

[Esempio completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Desktop)

## <a name="integrated-with-wpf"></a>Integrato con WPF

### <a name="creating-a-project"></a>Creazione di un progetto

Creare un progetto WPF, fare riferimento a Urho NuGet e quindi assicurarsi che sia possibile individuare le risorse (la directory contenente la directory dei dati).

### <a name="configuring-and-launching-urho-from-wpf"></a>La configurazione e avvio Urho da WPF

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

Creare un progetto UWP, fare riferimento a Urho NuGet e quindi assicurarsi che sia possibile individuare le risorse (la directory contenente la directory dei dati).

### <a name="configuring-and-launching-urho-from-uwp"></a>La configurazione e avvio Urho da UWP

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

Creare un progetto di Windows. Forms, fare riferimento a Urho NuGet e quindi assicurarsi che sia possibile individuare le risorse (la directory contenente la directory dei dati).

### <a name="configuring-and-launching-urho-from-windowsforms"></a>La configurazione e avvio Urho da Windows. Forms

Avviare Urho dal form, vedere [esempio completo](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/WinForms/SamplesForm.cs)

