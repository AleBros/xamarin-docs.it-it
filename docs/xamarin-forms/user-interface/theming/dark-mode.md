---
title: Rilevare la modalità scura nelle applicazioni Novell. Forms
description: La modalità scura può essere supportata in qualsiasi applicazione Novell. Forms tramite una combinazione di ResourceDictionaries, DynamicResources e Knowledge Base Platform.
ms.prod: xamarin
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/19/2020
ms.openlocfilehash: 7136e3240a39321b2d67ca29c16a0758cf5c4cfb
ms.sourcegitcommit: 524fc148bad17272bda83c50775771daa45bfd7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "78292520"
---
# <a name="detect-dark-mode-in-xamarinforms-applications"></a>Rilevare la modalità scura nelle applicazioni Novell. Forms

Le applicazioni Novell. Forms possono rispondere alle modifiche del tema del sistema operativo usando la stessa strategia che consente [di supportare i temi.](theming.md) La differenza principale consiste nel modo in cui la modifica del tema viene attivata. La modalità scura si riferisce a un set più ampio di preferenze di aspetto che è possibile impostare a livello di sistema operativo e alle applicazioni che si prevede di rispettare e rispondere immediatamente.

I requisiti minimi per l'uso della modalità scura nelle applicazioni Novell. Forms sono:

- iOS 13 o versione successiva.
- Android 9 o versione successiva (Android 9 supporta le modalità di visualizzazione su cui è possibile eseguire query).
- Android 10 o versione successiva (Android 10 notifica alle app le modifiche alla modalità).
- UWP v 10.0.10240.0 o versione successiva.
- Novell. Forms (qualsiasi versione).

Il processo di supporto delle modalità di aspetto, tra cui chiaro e scuro, è il seguente:

1. Definire le risorse condivise dall'intera applicazione in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary).
2. Definire un tema del dizionario risorse per ogni modalità di aspetto che fornisce le sostituzioni specifiche per ogni stile che si vuole modificare.
3. Impostare il tema della modalità di aspetto predefinito nel file **app. XAML** dell'applicazione.
4. Applicare uno stile all'applicazione usando l'estensione di markup `DynamicResource` in cui si vuole che gli stili reagiscono quando cambiano le modalità di visualizzazione.
5. Aggiungere il codice per ascoltare le modifiche del tema del sistema operativo e caricare il tema corrispondente dell'applicazione.

Gli screenshot seguenti mostrano le pagine con tema, per la modalità chiaro e scuro:

[![Screenshot della pagina principale di un'app con tema, in iOS e android](theming-images/main-page-both-themes.png "Pagina principale dell'app con tema")](theming-images/main-page-both-themes-large.png#lightbox "Pagina principale dell'app con tema")
[ ![screenshot della pagina dei dettagli di un'app con tema, in iOS e Android](theming-images/detail-page-both-themes.png "Pagina dei dettagli dell'app con tema")](theming-images/detail-page-both-themes-large.png#lightbox "Pagina dei dettagli dell'app con tema")

## <a name="define-themes"></a>Definire i temi

Per informazioni dettagliate sulla creazione di temi scuri e leggeri, seguire la [Guida di tema](theming.md) .

## <a name="react-to-appearance-mode-changes"></a>Reagire alle modifiche della modalità di aspetto

La modalità di visualizzazione in un dispositivo può variare per diversi motivi, a seconda del modo in cui l'utente ha configurato le proprie preferenze, tra cui la scelta esplicita di una modalità, un'ora del giorno o fattori ambientali, ad esempio la scarsa luminosità. È necessario aggiungere il codice di piattaforma per garantire che l'applicazione possa rispondere a queste modifiche e le sezioni seguenti ne discutono più dettagliatamente.

### <a name="android"></a>Android

Nel file **MainActivity.cs** dell'applicazione aggiungere il campo `ConfigChanges.UiMode` alla proprietà `ConfigurationChanges` nell'attributo `Activity`, in modo che l'app riceverà una notifica delle modifiche della modalità interfaccia utente:

```csharp
[Activity(
    // other settings
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation | ConfigChanges.UiMode)]
```

Nello stesso file **MainActivity.cs** , eseguire l'override del metodo `OnConfigurationChanged`:

```csharp
public override void OnConfigurationChanged(Configuration newConfig)
{
    base.OnConfigurationChanged(newConfig);

    if ((newConfig.UiMode & UiMode.NightNo) != 0)
    {
        // change to light theme
        // e.g. App.Current.Resources = new YourLightTheme();
    }
    else
    {
        // change to dark theme
        // e.g. App.Current.Resources = new YourDarkTheme();
    }
}
```

### <a name="ios"></a>iOS

In iOS le modifiche alla modalità di aspetto vengono notificate a UIViewController, che in Novell. Forms è la `ContentPage`. Per eseguire l'override del metodo, creare un renderer personalizzato nel progetto iOS denominato `PageRenderer.cs`:

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
                SetAppTheme();
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine($"\t\t\tERROR: {ex.Message}");
            }
        }

        public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
        {
            base.TraitCollectionDidChange(previousTraitCollection);

            if(this.TraitCollection.UserInterfaceStyle != previousTraitCollection.UserInterfaceStyle)
            {
                SetAppTheme();
            }
        }

        void SetAppTheme()
        {
            if (this.TraitCollection.UserInterfaceStyle == UIUserInterfaceStyle.Dark)
            {
                // change to dark theme
                // e.g. App.Current.Resources = new YourDarkTheme();
            }
            else
            {
                // change to light theme
                // e.g. App.Current.Resources = new YourLightTheme();
            }
        }
    }
}
```

L'override del metodo `TraitCollectionDidChange` consente di agire su una modifica di `UserInterfaceStyle`.

### <a name="uwp"></a>UWP

In UWP aggiungere il codice seguente al file **MainPage.XAML.cs** dell'applicazione:

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
        var backgroundColor = sender.GetColorValue(UIColorType.Background);
        var isDarkMode = backgroundColor == Colors.Black;
        if(isDarkMode)
        {
            Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
            {
                // change to dark theme
                // e.g. App.Current.Resources = new YourDarkTheme();
            });
        }
        else
        {
            Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
            {
                // change to light theme
                // e.g. App.Current.Resources = new YourLightTheme();
            });
        }
    }
}
```

## <a name="set-dark-and-light-themes"></a>Imposta temi scuri e chiari

Dopo aver seguito [la guida al tema,](theming.md) è possibile impostare facilmente un nuovo tema per l'applicazione nel percorso appropriato del codice della piattaforma sopra. Per caricare un nuovo tema, è sufficiente sostituire il dizionario risorse corrente dell'applicazione con un dizionario risorse con tema scelto:

```csharp
App.Current.Resources = new YourDarkTheme();
```

## <a name="related-links"></a>Collegamenti correlati

- [Temi (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Stili dinamici in Novell. Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Applicazione di stili alle app Xamarin.Forms con gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
