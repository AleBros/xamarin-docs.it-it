---
title: Introduzione alla shell Xamarin.Forms
description: La shell Xamarin.Forms fornisce le principali funzionalità richieste dalla maggior parte delle applicazioni, tra cui un'esperienza utente di navigazione comune, uno schema di navigazione basato su URI e un gestore di ricerca integrato.
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 20d9fb79d03990824dd884b62138a3e29b3ee04f
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65054481"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/Xaminals/)

La shell Xamarin.Forms riduce la complessità dello sviluppo di applicazioni per dispositivi mobili offrendo le principali funzionalità richieste dalla maggior parte delle applicazioni per dispositivi mobili, tra cui:

- Un'unica posizione per descrivere la gerarchia visiva di un'applicazione.
- Un'esperienza utente di navigazione comune.
- Uno schema di navigazione basato su URI che consente di passare a qualsiasi pagina nell'applicazione.
- Un gestore integrato per la ricerca.

Le applicazioni shell consentono inoltre una velocità di rendering maggiore e un consumo di memoria ridotto.

> [!IMPORTANT]
> Le applicazioni Android e iOS esistenti possono adottare Shell usufruendo immediatamente dei miglioramenti in termini di navigazione, prestazioni ed estendibilità.

Shell è attualmente in fase di sperimentazione e può essere usato solo aggiungendo `Forms.SetFlags("Shell_Experimental");` al progetto di piattaforma, prima di richiamare il metodo `Forms.Init`.

# <a name="androidtabandroid"></a>[Android](#tab/android)

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        global::Xamarin.Forms.Forms.SetFlags("Shell_Experimental");

        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(savedInstanceState);

        global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
        LoadApplication(new App());
    }
}
```

# <a name="iostabios"></a>[iOS](#tab/ios)

```csharp
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.SetFlags("Shell_Experimental");

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new App());

        return base.FinishedLaunching(app, options);
    }
}
```

----

## <a name="shell-navigation-experience"></a>Esperienza di navigazione nella shell

La shell offre una solida esperienza di navigazione, basata su riquadri a comparsa e schede. Il livello superiore di navigazione in un'applicazione Shell è un riquadro a comparsa:

[![Screenshot di un riquadro a comparsa della shell, in iOS e Android](introduction-images/flyout.png "Riquadro a comparsa della shell")](introduction-images/flyout-large.png#lightbox "Riquadro a comparsa della shell")

Selezionando un elemento del riquadro a comparsa, la scheda inferiore che rappresenta l'elemento viene selezionata e visualizzata:

[![Screenshot delle schede inferiori della shell, in iOS e Android](introduction-images/monkeys.png "Schede inferiori della shell")](introduction-images/monkeys-large.png#lightbox "Schede inferiori della shell")

> [!NOTE]
> Quando il riquadro a comparsa non è aperto, la barra delle schede inferiore viene considerata il livello di navigazione principale nell'applicazione.

Ogni scheda visualizza un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage). Se tuttavia una scheda inferiore contiene più di una pagina, è possibile spostarsi tra le pagine tramite la barra delle schede superiore:

[![Screenshot delle schede superiori della shell, in iOS e Android](introduction-images/cats.png "Schede superiori della shell")](introduction-images/cats-large.png#lightbox "Schede superiori della shell")

All'interno di ogni scheda, è possibile spostarsi ad altri oggetti [`ContentPage`](xref:Xamarin.Forms.ContentPage):

[![Screenshot della navigazione tra pagine della shell, in iOS e Android](introduction-images/cat-details.png "Navigazione nell'app shell")](introduction-images/cat-details-large.png#lightbox "Navigazione nell'app shell")

## <a name="subclassing-the-shell-class"></a>Creazione di sottoclassi della classe Shell

Un oggetto `Shell` sottoclassato descrive la gerarchia visiva di un'applicazione shell ed è costituito da tre oggetti gerarchici principali:

- `FlyoutItem`, che rappresenta uno o più elementi nel riquadro a comparsa. Ogni oggetto `FlyoutItem` è figlio dell'oggetto `Shell`.
- `Tab`, che rappresenta contenuto raggruppato, in cui è possibile spostarsi tramite le schede inferiori. Ogni oggetto `Tab` è figlio di un oggetto `FlyoutItem`.
- `ShellContent`, che rappresenta gli oggetti `ContentPage` nell'applicazione. Ogni oggetto `ShellContent` è figlio di un oggetto `Tab`. Quando è presente più di un oggetto `ShellContent` in `Tab`, è possibile spostarsi tra gli oggetti tramite le schede superiori.

Nessuno di questi oggetti rappresenta un'interfaccia utente, ma piuttosto l'organizzazione della gerarchia visiva dell'applicazione. La shell accetta questi oggetti e genera l'interfaccia utente di navigazione per il contenuto.

> [!NOTE]
> La classe `FlyoutItem` è un alias per la classe `ShellItem`, mentre la classe `Tab` è un alias per la classe `ShellSection`. Questi alias sono progettati per semplificare l'uso dell'API.

Il codice XAML seguente mostra un esempio semplice di un oggetto `Shell` sottoclassato:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    ...
    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png">
                <views:CatsPage />
            </ShellContent>
            <ShellContent Title="Dogs"
                          Icon="dog.png">
                <views:DogsPage />
            </ShellContent>
        </Tab>
        <ShellContent Title="Monkeys"
                      Icon="monkey.png">
            <views:MonkeysPage />
        </ShellContent>
        <ShellContent Title="Elephants"
                      Icon="elephant.png">  
            <views:ElephantsPage />
        </ShellContent>
        <ShellContent Title="Bears"
                      Icon="bear.png">
            <views:BearsPage />
        </ShellContent>
    </FlyoutItem>
    ...
</Shell>
```

Quando eseguito, questo codice XAML visualizza `CatsPage`, perché è il primo elemento del contenuto dichiarato nell'oggetto `Shell` sottoclassato:

[![Screenshot di un'app shell, in iOS e Android](introduction-images/cats.png "App shell")](introduction-images/cats-large.png#lightbox "App shell")

Premendo l'icona hamburger oppure scorrendo rapidamente da sinistra viene visualizzato il riquadro a comparsa:

[![Screenshot di un riquadro a comparsa della shell, in iOS e Android](introduction-images/flyout-reduced.png "Riquadro a comparsa della shell")](introduction-images/flyout-reduced-large.png#lightbox "Riquadro a comparsa della shell")

> [!IMPORTANT]
> In un'applicazione shell ogni elemento [`ContentPage`](xref:Xamarin.Forms.ContentPage) figlio di un oggetto `ShellContent` viene creato durante l'avvio dell'applicazione. L'aggiunta di ulteriori oggetti `ShellContent` usando questo approccio comporta la creazione di pagine aggiuntive durante l'avvio dell'applicazione e ciò può portare a un'esperienza di avvio insoddisfacente. La shell, tuttavia, è anche in grado di creare pagine su richiesta, in risposta alla navigazione. Per altre informazioni, vedere [Caricamento efficiente delle pagine](tabs.md#efficient-page-loading) nella guida [Schede della shell Xamarin.Forms](tabs.md).

## <a name="bootstrapping-a-shell-application"></a>Bootstrap di un'applicazione Shell

Il bootstrap di un'applicazione shell avviene impostando la proprietà [`MainPage`](xref:Xamarin.Forms.Application.MainPage) della classe `App` su un oggetto `Shell` sottoclassato:

```csharp
namespace Xaminals
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new AppShell();
        }
        ...
    }
}
```

In questo esempio la classe `AppShell` è un file XAML che deriva dalla classe `Shell`, che descrive la gerarchia visiva dell'applicazione.

## <a name="shell-appearance"></a>Aspetto della shell

La classe `Shell` definisce le proprietà seguenti che controllano l'aspetto di un'applicazione shell:

- `ShellBackgroundColor`, di tipo `Color`, una proprietà associata che definisce il colore di sfondo del riquadro della shell. Il colore non verrà inserito dietro il contenuto della shell.
- `ShellDisabledColor`, di tipo `Color`, una proprietà associata che definisce il colore per l'ombreggiatura di icone e testo disabilitati.
- `ShellForegroundColor`, di tipo `Color`, una proprietà associata che definisce il colore per l'ombreggiatura di icone e testo.
- `ShellTitleColor`, di tipo `Color`, una proprietà associata che definisce il colore usato per il titolo della pagina corrente.
- `ShellUnselectedColor`, di tipo `Color`, una proprietà associata che definisce il colore per le icone e il testo non selezionati nel riquadro della shell.

Tutte queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) e ciò significa che tali proprietà possono essere destinazioni di data binding.

Queste proprietà possono essere impostate anche usando fogli di stile CSS (Cascading Style Sheet). Per altre informazioni, vedere [Xamarin.Forms Shell specific properties](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties) (Proprietà specifiche della shell Xamarin.Forms).

## <a name="shell-content-layout"></a>Layout del contenuto della shell

La classe `Shell` definisce le proprietà seguenti che influiscono sul layout del contenuto di un'applicazione shell:

- `NavBarIsVisible`, di tipo `boolean`, una proprietà associata che definisce se la barra di spostamento deve essere visibile quando viene visualizzata una pagina. Questa proprietà deve essere impostata su una pagina e il suo valore predefinito è `true`.
- `SetPaddingInsets`, di tipo `bool`, una proprietà associata che controlla se il contenuto della pagina scorre sotto qualsiasi riquadro della shell. Questa proprietà deve essere impostata su una pagina e il suo valore predefinito è `false`.
- `TabBarIsVisible`, di tipo `bool`, una proprietà associata che definisce se la barra delle schede deve essere visibile quando viene visualizzata una pagina. Questa proprietà deve essere impostata su una pagina e il suo valore predefinito è `true`.
- `TitleView`, di tipo `View`, una proprietà associata che definisce l'oggetto `TitleView` per una pagina. Questa proprietà deve essere impostata su una pagina.

Tutte queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) e ciò significa che tali proprietà possono essere destinazioni di data binding.

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/Xaminals/)
- [Xamarin.Forms Shell specific properties](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties) (Proprietà specifiche della shell Xamarin.Forms)
