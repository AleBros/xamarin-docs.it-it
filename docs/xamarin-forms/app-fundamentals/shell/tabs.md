---
title: Layout della shell Xamarin.Forms
description: Dopo il riquadro a comparsa, il livello di navigazione successivo in un'applicazione shell è la barra delle schede inferiore. In alternativa, il modello di spostamento per un'applicazione può iniziare con le schede nella parte inferiore e non usare un riquadro a comparsa. In entrambi i casi, quando una scheda inferiore contiene più di una pagina, è possibile spostarsi tra le pagine tramite le schede superiori.
ms.prod: xamarin
ms.assetid: 318D81DB-E456-4E44-B083-36A27DBD9523
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/06/2019
ms.openlocfilehash: 0ffcbe99ef9696c5fde501809bea5ddcc7a793a7
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305143"
---
# <a name="xamarinforms-shell-tabs"></a>Schede della shell Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Quando il modello di spostamento per un'applicazione include un riquadro a comparsa, il livello successivo di spostamento nell'applicazione è la barra delle schede inferiore. Inoltre, quando il riquadro a comparsa è chiuso, la barra delle schede inferiore può essere considerato il livello di spostamento principale.

In alternativa, il modello di spostamento per un'applicazione può iniziare con le schede nella parte inferiore e non usare un riquadro a comparsa. In questo scenario, l'elemento figlio dell'oggetto `Shell` deve essere un oggetto `TabBar` che rappresenta la barra delle schede inferiore.

> [!NOTE]
> Il tipo `TabBar` disabilita il riquadro a comparsa.

Ogni oggetto `FlyoutItem` o `TabBar` può contenere uno o più oggetti `Tab`, con ogni oggetto `Tab` che rappresenta una scheda nella barra delle schede inferiore. Ogni `Tab` oggetto può contenere uno o più `ShellContent` oggetti e ogni `ShellContent` oggetto visualizzerà un singolo [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto. Quando è presente più di un oggetto `ShellContent` in `Tab`, è possibile spostarsi tra gli oggetti `ContentPage` tramite le schede superiori.

All'interno di ogni [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto, è possibile navigare a oggetti aggiuntivi. `ContentPage` Per altre informazioni sulla navigazione, vedere [Navigazione nella shell Xamarin.Forms](navigation.md).

## <a name="single-page-application"></a>Applicazione a singola pagina

L'applicazione shell più semplice è un'applicazione a singola pagina, che può essere creata aggiungendo un singolo oggetto `Tab` in un oggetto `TabBar`. All'interno dell'oggetto, `Tab` un `ShellContent` [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto deve essere impostato su un oggetto:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab>
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

L'esempio di codice crea l'applicazione a singola pagina seguente:

[![Screenshot di un'app Shell a pagina singola, su iOS e Android](tabs-images/single-page-app.png "App Shell a pagina singola")](tabs-images/single-page-app-large.png#lightbox "App Shell a pagina singola")

> [!NOTE]
> La barra di spostamento può essere nascosta, se necessario, impostando la `Shell.NavBarIsVisible` proprietà associata su `false` sull'oggetto. [`ContentPage`](xref:Xamarin.Forms.ContentPage)

La shell include operatori di conversione implicita che consentono di semplificare la gerarchia visiva della shell, senza introdurre visualizzazioni aggiuntive nella struttura ad albero visuale. Ciò è possibile perché un oggetto `Shell` sottoclassato può contenere solo oggetti `FlyoutItem` o un oggetto `TabBar`, che possono contenere solo oggetti `Tab`, che possono contenere solo oggetti `ShellContent`. Questi operatori di conversione implicita possono essere usati per rimuovere gli oggetti `TabBar`, `Tab` e `ShellContent` nell'esempio precedente:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell"
       FlyoutBehavior="Disabled">
    <views:CatsPage />
</Shell>
```

Questa conversione implicita [`ContentPage`](xref:Xamarin.Forms.ContentPage) esegue `ShellContent` automaticamente il wrapping dell'oggetto in un oggetto, di cui viene eseguito il wrapping in un `Tab` oggetto, di cui viene eseguito il wrapping in un `FlyoutItem` oggetto. In un'applicazione a singola pagina non è necessario un riquadro a comparsa e quindi la proprietà `Shell.FlyoutBehavior` è impostata su `Disabled`.

> [!IMPORTANT]
> In un'applicazione [`ContentPage`](xref:Xamarin.Forms.ContentPage) Shell, ogni elemento `ShellContent` figlio di un oggetto viene creato durante l'avvio dell'applicazione. L'aggiunta di ulteriori oggetti `ShellContent` usando questo approccio comporta la creazione di pagine aggiuntive durante l'avvio dell'applicazione e ciò può portare a un'esperienza di avvio insoddisfacente. La shell, tuttavia, è anche in grado di creare pagine su richiesta, in risposta alla navigazione. Per altre informazioni, vedere [Caricamento efficiente delle pagine](tabs.md#efficient-page-loading).

## <a name="bottom-tabs"></a>Schede inferiori

Per gli oggetti `Tab` viene eseguito il rendering come schede inferiori, a condizione che vi siano più oggetti `Tab` in un singolo oggetto `TabBar`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Cats"
             Icon="cat.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Dogs"
             Icon="dog.png">
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

I titoli e le icone delle schede vengono impostati in ogni oggetto `Tab` e visualizzati nelle schede inferiori:

[![Screenshot di un'app Shell a due pagine con schede inferiore, su iOS e Android](tabs-images/two-page-app-bottom-tabs.png "App Shell a due pagine con schede inferiori")](tabs-images/two-page-app-bottom-tabs-large.png#lightbox "App Shell a due pagine con schede inferiori")

Quando sono presenti più di cinque schede, viene visualizzata una scheda **Altro,** che può essere utilizzata per accedere alle schede aggiuntive:

[![Screenshot di un'app Shell con una scheda Altro, su iOS e Android](tabs-images/more-tabs.png "App Shell con scheda Altro")](tabs-images/more-tabs-large.png#lightbox "Shellapp con altre schede")

In alternativa, è possibile usare gli operatori di conversione implicita della shell per rimuovere gli oggetti `ShellContent` e `Tab` nell'esempio precedente:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <views:CatsPage IconImageSource="cat.png" />
        <views:DogsPage IconImageSource="dog.png" />
    </TabBar>
</Shell>
```

Questa conversione implicita [`ContentPage`](xref:Xamarin.Forms.ContentPage) esegue `ShellContent` automaticamente il wrapping di ogni `Tab` oggetto in un oggetto, che vengono quindi entrambi racchiusi in un oggetto.

> [!IMPORTANT]
> In un'applicazione [`ContentPage`](xref:Xamarin.Forms.ContentPage) Shell, ogni elemento `ShellContent` figlio di un oggetto viene creato durante l'avvio dell'applicazione. L'aggiunta di ulteriori oggetti `ShellContent` usando questo approccio comporta la creazione di pagine aggiuntive durante l'avvio dell'applicazione e ciò può portare a un'esperienza di avvio insoddisfacente. La shell, tuttavia, è anche in grado di creare pagine su richiesta, in risposta alla navigazione. Per altre informazioni, vedere [Caricamento efficiente delle pagine](tabs.md#efficient-page-loading).

### <a name="tab-class"></a>Classe Tab

La classe `Tab` include le proprietà seguenti che controllano l'aspetto e il comportamento delle schede:

- `CurrentItem`, di tipo `ShellContent`, ovvero l'elemento selezionato.
- `FlyoutDisplayOptions`, di tipo `FlyoutDisplayOptions`, che definisce come vengono visualizzati l'elemento e i relativi elementi figlio nel riquadro a comparsa. Il valore predefinito è `AsSingleItem`.
- `FlyoutIcon`, di tipo `ImageSource`, che definisce l'icona che verrà visualizzata nel riquadro a comparsa.
- `Icon`, di tipo `ImageSource`, che definisce l'icona da visualizzare nelle parti del riquadro che non costituiscono il riquadro a comparsa.
- `IsChecked`, di tipo `boolean`, che definisce se l'elemento è attualmente evidenziato nel riquadro a comparsa.
- `IsEnabled`, di tipo `boolean`, che definisce se l'elemento è selezionabile nel riquadro.
- `IsTabStop`, di tipo `bool`, che indica se un oggetto `Tab` è incluso nella navigazione tramite tabulazione. Il valore predefinito è `true` e quando questo valore è `false` l'oggetto `Tab` viene ignorato dall'infrastruttura di navigazione tramite tabulazione, indipendentemente dall'impostazione di `TabIndex`.
- `Items`, di tipo `IList<ShellContent>`, che definisce tutto il contenuto all'interno di un oggetto `Tab`.
- `TabIndex`, di tipo `int`, che indica l'ordine in cui gli oggetti `Tab` ricevono lo stato attivo quando l'utente si sposta tra gli elementi premendo TAB. Il valore predefinito della proprietà è 0.
- `Title`, di tipo `string`, ovvero il titolo da visualizzare sulla scheda nell'interfaccia utente.

## <a name="shell-content"></a>Contenuto della shell

L'elemento `Tab` figlio di `ShellContent` ogni `Content` oggetto è un [`ContentPage`](xref:Xamarin.Forms.ContentPage)oggetto, la cui proprietà è impostata su :

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Cats"
             Icon="cat.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Dogs"
             Icon="dog.png">
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

All'interno di ogni [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto, è possibile navigare a oggetti aggiuntivi. `ContentPage` Per altre informazioni sulla navigazione, vedere [Navigazione nella shell Xamarin.Forms](navigation.md).

> [!NOTE]
> L'oggetto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) viene ereditato dall'oggetto padre. `Tab` `ShellContent`

### <a name="shellcontent-class"></a>Classe ShellContent

La classe `ShellContent` include le proprietà seguenti che controllano l'aspetto e il comportamento del contenuto delle schede:

- `Content`, di tipo `object`, ovvero il contenuto di `ShellContent`.
- `ContentTemplate`, di tipo `DataTemplate`, ovvero il modello usato per l'inserimento dinamico del contenuto di `ShellContent`.
- `FlyoutIcon`, di tipo `ImageSource`, che definisce l'icona che verrà visualizzata nel riquadro a comparsa.
- `Icon`, di tipo `ImageSource`, che definisce l'icona da visualizzare nelle parti del riquadro che non costituiscono il riquadro a comparsa.
- `IsChecked`, di tipo `boolean`, che definisce se l'elemento è attualmente evidenziato nel riquadro a comparsa.
- `IsEnabled`, di tipo `boolean`, che definisce se l'elemento è selezionabile nel riquadro.
- `MenuItems`, di tipo `MenuItemCollection`, ovvero le voci di menu da visualizzare nel riquadro a comparsa quando questo oggetto `ShellContent` è la pagina visualizzata.
- `Title`, di tipo `string`, ovvero il titolo da visualizzare nell'interfaccia utente.

Tutte queste proprietà sono [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) supportate da oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

## <a name="bottom-and-top-tabs"></a>Schede inferiori e superiori

Quando in `ShellContent` un `Tab` oggetto sono presenti più oggetti, viene aggiunta una barra [`ContentPage`](xref:Xamarin.Forms.ContentPage) delle schede superiore alla scheda inferiore, tramite la quale gli oggetti sono navigabili:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Domestic"
             Icon="domestic.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Monkeys"
             Icon="monkey.png">
            <ShellContent>
                <views:MonkeysPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

Il risultato è il layout illustrato negli screenshot seguenti:

[![Screenshot di un'app Shell a due pagine con schede superiore e inferiore, su iOS e Android](tabs-images/two-page-app-top-tabs.png "App Shell a due pagine con schede superiore e inferiore")](tabs-images/two-page-app-top-tabs-large.png#lightbox "App Shell a due pagine con schede superiore e inferiore")

In alternativa, è possibile usare gli operatori di conversione implicita della shell per rimuovere gli oggetti `ShellContent` e il secondo oggetto `Tab` nell'esempio precedente:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Domestic"
             Icon="domestic.png">
            <views:CatsPage />
            <views:DogsPage />
        </Tab>
        <views:MonkeysPage IconImageSource="monkey.png" />
    </TabBar>
</Shell>
```

Questa conversione implicita esegue automaticamente il wrapping di `MonkeysPage` in un oggetto `ShellContent`, di cui quindi viene eseguito il wrapping in un oggetto `Tab`. Viene inoltre eseguito il wrapping in modo implicito di `CatsPage` e `DogsPage` in oggetti `ShellContent`.

## <a name="efficient-page-loading"></a>Caricamento efficiente delle pagine

In un'applicazione [`ContentPage`](xref:Xamarin.Forms.ContentPage) Shell, `ShellContent` ogni oggetto in un oggetto viene creato durante l'avvio dell'applicazione, con conseguente esperienza di avvio non corretta. La shell, tuttavia, consente anche la creazione di pagine su richiesta, in risposta alla navigazione. Questa operazione può essere `DataTemplate` eseguita utilizzando `ContentPage` l'estensione di markup per convertire ciascuno in un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)oggetto , quindi impostando il risultato come valore della `ShellContent.ContentTemplate` proprietà:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">    
    <TabBar>
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png"
                          ContentTemplate="{DataTemplate views:CatsPage}" />
            <ShellContent Title="Dogs"
                          Icon="dog.png"
                          ContentTemplate="{DataTemplate views:DogsPage}" />
        </Tab>
        <ShellContent Title="Monkeys"
                      Icon="monkey.png"
                      ContentTemplate="{DataTemplate views:MonkeysPage}" />
    </TabBar>    
</Shell>
```

Questo codice XAML crea e visualizza `CatsPage`, perché è il primo elemento del contenuto dichiarato nell'oggetto `Shell` sottoclassato. È possibile spostarsi tra `CatsPage` e `MonkeysPage` tramite le schede inferiori e queste pagine vengono create solo quando l'utente si sposta su di esse. Il vantaggio di questo approccio è che si evita di creare un'esperienza di avvio insoddisfacente, in quanto le pagine vengono create su richiesta in risposta alla navigazione, invece che all'avvio dell'applicazione.

## <a name="tab-appearance"></a>Aspetto delle schede

La classe `Shell` definisce le proprietà associate seguenti che controllano l'aspetto delle schede:

- `TabBarBackgroundColor`, di tipo `Color`, che definisce il colore di sfondo per la barra delle schede. Se la proprietà non è impostata, viene usato il valore della proprietà `BackgroundColor`.
- `TabBarDisabledColor`, di tipo `Color`, che definisce il colore disabilitato per la barra delle schede. Se la proprietà non è impostata, viene usato il valore della proprietà `DisabledColor`.
- `TabBarForegroundColor`, di tipo `Color`, che definisce il colore di primo piano per la barra delle schede. Se la proprietà non è impostata, viene usato il valore della proprietà `ForegroundColor`.
- `TabBarTitleColor`, di tipo `Color`, che definisce il colore del titolo per la barra delle schede. Se la proprietà non è impostata, viene usato il valore della proprietà `TitleColor`.
- `TabBarUnselectedColor`, di tipo `Color`, che definisce il colore non selezionato per la barra delle schede. Se la proprietà non è impostata, viene usato il valore della proprietà `UnselectedColor`.

Tutte queste proprietà sono [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) supportate da oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati e stili.

L'esempio seguente illustra uno stile XAML che imposta proprietà diverse per i colori delle schede:

```xaml
<Style x:Key="BaseStyle"
       TargetType="Element">
    <Setter Property="Shell.TabBarBackgroundColor"
            Value="#3498DB" />
    <Setter Property="Shell.TabBarTitleColor"
            Value="White" />
    <Setter Property="Shell.TabBarUnselectedColor"
            Value="#B4FFFFFF" />
</Style>
```

È anche possibile definire lo stile delle schede usando fogli di stile CSS (Cascading Style Sheet). Per altre informazioni, vedere [Xamarin.Forms Shell specific properties](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties) (Proprietà specifiche della shell Xamarin.Forms).

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Navigazione nella shell Xamarin.Forms](navigation.md)
- [Xamarin.Forms CSS Shell specific properties](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties) (Proprietà specifiche della shell CSS Xamarin.Forms)
