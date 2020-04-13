---
title: Rilevare la modalità scura nelle applicazioni Xamarin.FormsDetect dark mode in Xamarin.Forms applications
description: La modalità scura può essere supportata in qualsiasi applicazione Xamarin.Forms utilizzando una combinazione di ResourceDictionaries, DynamicResources e informazioni sulla piattaforma.
ms.prod: xamarin
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 03/13/2020
ms.openlocfilehash: 7fe1a98e6a497a5791f26df2fc96d41781b71ef6
ms.sourcegitcommit: b93754b220fca3d6e3d131341e3cfbe233d10f84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628307"
---
# <a name="detect-dark-mode-in-xamarinforms-applications"></a>Rilevare la modalità scura nelle applicazioni Xamarin.FormsDetect dark mode in Xamarin.Forms applications

Le applicazioni Xamarin.Forms possono rispondere alle modifiche dei temi del sistema operativo utilizzando la stessa strategia che consente di supportare [i temi.](theming.md) La differenza principale è nel modo in cui viene attivato il cambiamento di tema. La modalità scura si riferisce a un insieme più ampio di preferenze di aspetto che possono essere impostate a livello di sistema operativo e a quali applicazioni si prevede rispettino e rispondano immediatamente.

I requisiti minimi per l'utilizzo della modalità scura nelle applicazioni Xamarin.Forms sono:

- iOS 13 o versione successiva.
- Android 9 o versione successiva (Android 9 supporta le modalità di aspetto su cui è possibile eseguire query).
- Android 10 o versione successiva (Android 10 notifica alle app le modifiche di modalità).
- UWP v10.0.10240.0 o versione successiva.
- Xamarin.Forms (qualsiasi versione).

Il processo di supporto delle modalità di visualizzazione, tra cui chiaro e scuro, è il seguente:

1. Definire le risorse condivise dall'intera applicazione in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)oggetto .
2. Definire un tema del dizionario risorse per ogni modalità di aspetto che fornisce sostituzioni specifiche per ogni stile che si desidera modificare.
3. Impostare il tema della modalità di aspetto predefinito nel file **App.xaml** dell'applicazione.
4. Applicare uno stile `DynamicResource` all'applicazione utilizzando l'estensione di markup in cui si desidera che gli stili reagiscano quando cambiano le modalità di aspetto.
5. Aggiungere il codice per l'ascolto delle modifiche del tema del sistema operativo e caricare il tema corrispondente dell'applicazione.

Le seguenti schermate mostrano le pagine a tema, per la modalità chiaro e scuro:

[![Screenshot della pagina principale di un'app a tema, su iOS e Android](theming-images/main-page-both-themes.png "Pagina principale dell'app a tema")](theming-images/main-page-both-themes-large.png#lightbox "Pagina principale dell'app a tema")
[![Screenshot della pagina dei dettagli di un'app a tema, su iOS e Android](theming-images/detail-page-both-themes.png "Pagina dei dettagli dell'app a tema")](theming-images/detail-page-both-themes-large.png#lightbox "Pagina dei dettagli dell'app a tema")

## <a name="define-themes"></a>Definire i temi

Segui la [guida ai temi](theming.md) per i dettagli passo dopo passo sulla creazione di temi scuri e chiari.

È possibile impostare facilmente un nuovo tema per l'applicazione nella posizione appropriata del codice della piattaforma. Per caricare un nuovo tema, è sufficiente sostituire il dizionario risorse corrente dell'applicazione con un dizionario risorse a tema di propria scelta:To load a new theme, simply replace the application's current resource dictionary with a themed resource dictionary of your choice:

```csharp
App.Current.Resources = new YourDarkTheme();
```

## <a name="detect-and-apply-theme"></a>Rilevare e applicare il tema

Il rilevamento del tema attualmente in [`RequestedTheme`](~/essentials/app-theme.md) esecuzione può essere ottenuto utilizzando la funzionalità di [Xamarin.Essentials](~/essentials/index.md) (versione 1.4.0 o successiva). È quindi possibile creare un metodo helper `App` in una nuova classe o nella classe per configurare il tema:You can then create a helper method in a new class or in the class to configure the me:

```csharp
public partial class App : Application
{
    public static void ApplyTheme()
    {
        if (AppInfo.RequestedTheme == AppTheme.Dark)
        {
            // Change to dark theme
            // e.g. App.Current.Resources = new YourDarkTheme();
        }
        else
        {
            // Change to light theme
            // e.g. App.Current.Resources = new YourLightTheme();
        }
    }
}
```

## <a name="react-to-appearance-mode-changes"></a>Reagisci alle modifiche della modalità di aspetto

La modalità di aspetto su un dispositivo può cambiare per una serie di motivi a seconda di come l'utente ha configurato le proprie preferenze, tra cui la scelta esplicita di una modalità, l'ora del giorno o fattori ambientali come la luce bassa. È necessario aggiungere codice della piattaforma per assicurarsi che l'applicazione possa reagire a queste modifiche e nelle sezioni seguenti questo argomento viene illustrato in modo più dettagliato.

### <a name="android"></a>Android

Per supportare la modalità scura nell'app, devi aggiornare `Resources/values/styles.xml`il tema `DayNight` dell'app, che può essere trovato in , per ereditare da un tema:

```xml
<style name="MainTheme.Base" parent="Theme.AppCompat.DayNight">
```

Se è stato eseguito l'aggiornamento a [componenti](https://www.nuget.org/packages/Xamarin.Google.Android.Material/) materiali di AndroidX (1.1.0-rc2) o più recenti è possibile utilizzare:

```xml
<style name="MainTheme.Base" parent="Theme.MaterialComponents.DayNight">
```

Nel file **MainActivity.cs** dell'applicazione `ConfigChanges.UiMode` aggiungi il `ConfigurationChanges` campo `Activity` alla proprietà nell'attributo, in modo che l'app riceva una notifica delle modifiche apportate alla modalità dell'interfaccia utente:

```csharp
[Activity(
    // other settings
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation | ConfigChanges.UiMode)]
```

Nello stesso file di `OnConfigurationChanged` **MainActivity.cs,** eseguire l'override del metodo:

```csharp
public override void OnConfigurationChanged(Configuration newConfig)
{
    base.OnConfigurationChanged(newConfig);
    App.ApplyTheme();
}
```

### <a name="ios"></a>iOS

In iOS, le modifiche alla modalità di aspetto vengono notificate nel `ContentPage`UIViewController, che in Xamarin.Forms è il metodo . Per eseguire l'override di tale metodo, creare un `PageRenderer.cs`renderer personalizzato nel progetto iOS denominato :

```csharp
using System;
using UIKit;
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(ContentPage), typeof(YourApp.iOS.Renderers.PageRenderer))]
namespace YourApp.iOS.Renderers
{
    public class PageRenderer : Xamarin.Forms.Platform.iOS.PageRenderer
    {
        protected override void OnElementChanged(VisualElementChangedEventArgs e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                App.ApplyTheme();
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine($"\t\t\tERROR: {ex.Message}");
            }
        }

        public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
        {
            base.TraitCollectionDidChange(previousTraitCollection);

            App.ApplyTheme();
        }
    }
}
```

L'override `TraitCollectionDidChange` del metodo consente `UserInterfaceStyle` di agire in base a una modifica.

### <a name="uwp"></a>UWP

Nella piattaforma UWP aggiungere il codice seguente al file **di MainPage.xaml.cs** dell'applicazione:

```csharp
public sealed partial class MainPage
{
    UISettings uiSettings;

    public MainPage()
    {
        this.InitializeComponent();

        LoadApplication(new YourApp.App());

        uiSettings = new UISettings();
        uiSettings.ColorValuesChanged += ColorValuesChanged;
    }

    private void ColorValuesChanged(UISettings sender, object args)
    {
        Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
        {
            App.ApplyTheme();
        });
    }
}
```

## <a name="related-links"></a>Collegamenti correlati

- [Tema (esempio)Theming (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Stili dinamici in Xamarin.Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Applicazione di stili alle app Xamarin.Forms con gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
