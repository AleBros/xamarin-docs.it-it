---
title: "creare un' Xamarin.Forms applicazione shell" Descrizione: "il processo per la creazione di un' Xamarin.Forms applicazione shell consiste nel creare un file XAML che sottoclassa la classe shell, impostare la proprietà MainPage della classe App dell'applicazione sull'oggetto Shell sottoclassato e quindi descrivere la gerarchia visiva dell'applicazione nella classe della shell sottoclassata".
ms. prod: Novell MS. AssetID: 2A51D78F-6CD5-4BC4-A62E-11CEFA799987 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 05/24/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="create-a-xamarinforms-shell-application"></a>Creare un' Xamarin.Forms applicazione shell

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Il processo per la creazione di un' Xamarin.Forms applicazione shell è il seguente:

1. Creare una nuova Xamarin.Forms applicazione o caricare un'applicazione esistente che si desidera convertire in un'applicazione shell.
1. Aggiungere un file XAML al progetto di codice condiviso, che rappresenta una sottoclasse della classe `Shell`. Per altre informazioni, vedere [Creare una sottoclasse della classe Shell](#subclass-the-shell-class).
1. Impostare la [`MainPage`](xref:Xamarin.Forms.Application.MainPage) proprietà della classe dell'applicazione sull' `App` oggetto sottoclassato `Shell` . Per altre informazioni, vedere [Bootstrap dell'applicazione shell](#bootstrap-the-shell-application).
1. Descrivere la gerarchia visiva dell'applicazione nella classe `Shell` sottoclassata. Per altre informazioni, vedere [Descrivere la gerarchia visiva dell'applicazione](#describe-the-visual-hierarchy-of-the-application).

## <a name="subclass-the-shell-class"></a>Creare una sottoclasse della classe Shell

Il primo passaggio nella creazione di un' Xamarin.Forms applicazione shell consiste nell'aggiungere un file XAML al progetto di codice condiviso che sottoclassa la `Shell` classe. È possibile assegnare qualsiasi nome a questo file, ma si consiglia di denominarlo **AppShell**. L'esempio di codice seguente mostra un nuovo file **AppShell.xaml**:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       x:Class="Xaminals.AppShell">

</Shell>
```

L'esempio seguente mostra il file code-behind **AppShell.xaml.cs**:

```csharp
using Xamarin.Forms;

namespace Xaminals
{
    public partial class AppShell : Shell
    {
        public AppShell()
        {
            InitializeComponent();
        }
    }
}
```

## <a name="bootstrap-the-shell-application"></a>Bootstrap dell'applicazione shell

Dopo aver creato il file XAML che sottoclassi l' `Shell` oggetto, la [`MainPage`](xref:Xamarin.Forms.Application.MainPage) proprietà della `App` classe deve essere impostata sull'oggetto sottoclassato `Shell` :

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

In questo esempio, la classe `AppShell` è il file XAML che deriva dalla classe `Shell`.

> [!NOTE]
> Anche se un'applicazione shell vuota può essere compilata, l'esecuzione causerà la generazione di `InvalidOperationException`.

## <a name="describe-the-visual-hierarchy-of-the-application"></a>Descrivere la gerarchia visiva dell'applicazione

Il passaggio finale della creazione di un' Xamarin.Forms applicazione shell consiste nel descrivere la gerarchia visiva dell'applicazione nella classe sottoclasse `Shell` . Una classe `Shell` sottoclassata è costituita da tre oggetti gerarchici principali:

- `FlyoutItem` o `TabBar`. L'oggetto `FlyoutItem` rappresenta uno o più elementi nel riquadro a comparsa e deve essere usato quando il modello di spostamento per l'applicazione include un riquadro a comparsa. Un oggetto `TabBar` rappresenta la barra di schede inferiore e deve essere usato quando il modello di spostamento per l'applicazione inizia con le schede nella parte inferiore. Ogni oggetto `FlyoutItem` o `TabBar` è figlio dell'oggetto `Shell`.
- `Tab`, che rappresenta contenuto raggruppato, in cui è possibile spostarsi tramite le schede inferiori. Ogni oggetto `Tab` è figlio di un oggetto `FlyoutItem` o `TabBar`.
- `ShellContent`, che rappresenta gli oggetti `ContentPage` nell'applicazione. Ogni oggetto `ShellContent` è figlio di un oggetto `Tab`. Quando è presente più di un oggetto `ShellContent` in `Tab`, è possibile spostarsi tra gli oggetti tramite le schede superiori.

Nessuno di questi oggetti rappresenta un'interfaccia utente, ma piuttosto l'organizzazione della gerarchia visiva dell'applicazione. La shell accetta questi oggetti e genera l'interfaccia utente di navigazione per il contenuto.

Il codice XAML seguente mostra un esempio di classe `Shell` sottoclassata:

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

Quando eseguito, questo codice XAML visualizza `CatsPage`, perché è il primo elemento del contenuto dichiarato nella classe `Shell` sottoclassata:

[![Screenshot di un'app shell, in iOS e Android](create-images/cats.png "App shell")](create-images/cats-large.png#lightbox "App shell")

Premendo l'icona hamburger oppure scorrendo rapidamente da sinistra viene visualizzato il riquadro a comparsa:

[![Screenshot di un riquadro a comparsa della shell, in iOS e Android](create-images/flyout-reduced.png "Riquadro a comparsa della shell")](create-images/flyout-reduced-large.png#lightbox "Riquadro a comparsa della shell")

> [!IMPORTANT]
> In un'applicazione shell ogni [`ContentPage`](xref:Xamarin.Forms.ContentPage) elemento figlio di un `ShellContent` oggetto viene creato durante l'avvio dell'applicazione. L'aggiunta di ulteriori oggetti `ShellContent` usando questo approccio comporta la creazione di pagine aggiuntive durante l'avvio dell'applicazione e ciò può portare a un'esperienza di avvio insoddisfacente. La shell, tuttavia, è anche in grado di creare pagine su richiesta, in risposta alla navigazione. Per ulteriori informazioni, vedere [caricamento di pagine efficienti](tabs.md#efficient-page-loading) nella Guida alle [ Xamarin.Forms schede della shell](tabs.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
