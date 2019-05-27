---
title: Layout della shell Xamarin.Forms
description: Dopo il riquadro a comparsa, il livello di navigazione successivo in un'applicazione shell è la barra delle schede inferiore. Quando una scheda contiene più di una pagina, è possibile spostarsi tra le pagine tramite le schede superiori.
ms.prod: xamarin
ms.assetid: 318D81DB-E456-4E44-B083-36A27DBD9523
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: a8da1e96bbdf51899b1780265933402da791a03e
ms.sourcegitcommit: 0596004d4a0e599c1da1ddd75a6ac928f21191c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66005151"
---
# <a name="xamarinforms-shell-tabs"></a>Schede della shell Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

Dopo il riquadro a comparsa, il livello di navigazione successivo in un'applicazione shell è la barra delle schede inferiore. In alternativa, quando il riquadro a comparsa è chiuso, la barra delle schede inferiore viene considerata il livello di navigazione principale.

Ogni oggetto `FlyoutItem` può contenere uno o più oggetti `Tab`, con ogni oggetto `Tab` che rappresenta una scheda nella barra delle schede inferiore. Ogni oggetto `Tab` può contenere uno o più oggetti `ShellContent` e ogni oggetto `ShellContent` visualizza un singolo oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage). Quando è presente più di un oggetto `ShellContent` in `Tab`, è possibile spostarsi tra gli oggetti `ContentPage` tramite le schede superiori.

All'interno di ogni oggetto `ContentPage` è possibile spostarsi ad altri oggetti `ContentPage`. Per altre informazioni sulla navigazione, vedere [Navigazione nella shell Xamarin.Forms](navigation.md).

## <a name="single-page-application"></a>Applicazione a singola pagina

L'applicazione shell più semplice è un'applicazione a singola pagina, che può essere creata aggiungendo un singolo oggetto `Tab` in un oggetto `FlyoutItem`. All'interno dell'oggetto `Tab`, un oggetto `ShellContent` deve essere impostato su un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell"
       FlyoutBehavior="Disabled">
    <FlyoutItem>
        <Tab>
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
    </FlyoutItem>
</Shell>
```

L'esempio di codice crea l'applicazione a singola pagina seguente:

[![Screenshot di un'app shell a singola pagina, in iOS e Android](tabs-images/single-page-app.png "App shell a singola pagina")](tabs-images/single-page-app-large.png#lightbox "App shell a singola pagina")

In un'applicazione a singola pagina non è necessario un riquadro a comparsa e quindi la proprietà `Shell.FlyoutBehavior` è impostata su `Disabled`.

> [!NOTE]
> La barra di spostamento può essere nascosta, se necessario, impostando la proprietà associata `Shell.NavBarIsVisible` su `false` nell'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage).

La shell include operatori di conversione implicita che consentono di semplificare la gerarchia visiva della shell, senza introdurre visualizzazioni aggiuntive nella struttura ad albero visuale. Ciò è possibile perché un oggetto `Shell` sottoclassato può contenere solo oggetti `FlyoutItem`, che possono contenere solo oggetti `Tab`, che possono contenere solo oggetti `ShellContent`. Questi operatori di conversione implicita possono essere usati per rimuovere gli oggetti `FlyoutItem`, `Tab` e `ShellContent` nell'esempio precedente:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell"
       FlyoutBehavior="Disabled">
    <views:CatsPage />
</Shell>
```

Questa conversione implicita esegue automaticamente il wrapping dell'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) in un oggetto `ShellContent`, di cui viene eseguito il wrapping in un oggetto `Tab`, di cui viene eseguito il wrapping in un oggetto `FlyoutItem`.

> [!IMPORTANT]
> In un'applicazione shell ogni elemento [`ContentPage`](xref:Xamarin.Forms.ContentPage) figlio di un oggetto `ShellContent` viene creato durante l'avvio dell'applicazione. L'aggiunta di ulteriori oggetti `ShellContent` usando questo approccio comporta la creazione di pagine aggiuntive durante l'avvio dell'applicazione e ciò può portare a un'esperienza di avvio insoddisfacente. La shell, tuttavia, è anche in grado di creare pagine su richiesta, in risposta alla navigazione. Per altre informazioni, vedere [Caricamento efficiente delle pagine](tabs.md#efficient-page-loading).

## <a name="bottom-tabs"></a>Schede inferiori

Per gli oggetti `Tab` viene eseguito il rendering come schede inferiori, a condizione che vi siano più oggetti `Tab` in un singolo oggetto `FlyoutItem`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell"
       FlyoutBehavior="Disabled">
    <FlyoutItem>
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
    </FlyoutItem>
</Shell>
```

I titoli e le icone delle schede vengono impostati in ogni oggetto `Tab` e visualizzati nelle schede inferiori:

[![Screenshot di un'app shell di due pagine con schede inferiori, in iOS e Android](tabs-images/two-page-app-bottom-tabs.png "App shell di due pagine con schede inferiori")](tabs-images/two-page-app-bottom-tabs-large.png#lightbox "App shell di due pagine con schede inferiori")

In alternativa, è possibile usare gli operatori di conversione implicita della shell per rimuovere gli oggetti `ShellContent` e `Tab` nell'esempio precedente:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell"
       FlyoutBehavior="Disabled">
    <FlyoutItem>
        <views:CatsPage IconImageSource="cat.png" />
        <views:DogsPage IconImageSource="dog.png" />
    </FlyoutItem>
</Shell>
```

Questa conversione implicita esegue automaticamente il wrapping di ogni oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) in un oggetto `ShellContent`, quindi viene eseguito il wrapping di entrambi in un oggetto `Tab`.

> [!IMPORTANT]
> In un'applicazione shell ogni elemento [`ContentPage`](xref:Xamarin.Forms.ContentPage) figlio di un oggetto `ShellContent` viene creato durante l'avvio dell'applicazione. L'aggiunta di ulteriori oggetti `ShellContent` usando questo approccio comporta la creazione di pagine aggiuntive durante l'avvio dell'applicazione e ciò può portare a un'esperienza di avvio insoddisfacente. La shell, tuttavia, è anche in grado di creare pagine su richiesta, in risposta alla navigazione. Per altre informazioni, vedere [Caricamento efficiente delle pagine](tabs.md#efficient-page-loading).

### <a name="tab-class"></a>Classe Tab

La classe `Tab` include le proprietà seguenti che controllano l'aspetto e il comportamento delle schede:

- `CurrentItem`, di tipo `ShellContent`, ovvero l'elemento selezionato.
- `FlyoutDisplayOptions`, di tipo `FlyoutDisplayOptions`, che definisce come vengono visualizzati l'elemento e i relativi elementi figlio nel riquadro a comparsa. Il valore predefinito è `AsSingleItem`.
- `FlyoutIcon`, di tipo `ImageSource`, che definisce l'icona che verrà visualizzata nel riquadro a comparsa.
- `Icon`, di tipo `ImageSource`, che definisce l'icona da visualizzare nelle parti del riquadro che non costituiscono il riquadro a comparsa.
- `IsChecked`, di tipo `boolean`, che definisce se l'elemento è attualmente evidenziato nel riquadro a comparsa.
- `IsEnabled`, di tipo `boolean`, che definisce se l'elemento è selezionabile nel riquadro.
- `Items`, di tipo `ShellContentCollection`, che definisce tutto il contenuto all'interno di un oggetto `Tab`.
- `Title`, di tipo `string`, ovvero il titolo da visualizzare sulla scheda nell'interfaccia utente.

## <a name="shell-content"></a>Contenuto della shell

L'elemento figlio di ogni oggetto `Tab` è un oggetto `ShellContent`, la cui proprietà `Content` è impostata su un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell"
       FlyoutBehavior="Disabled">
    <FlyoutItem>
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
    </FlyoutItem>
</Shell>
```

All'interno di ogni oggetto `ContentPage` è possibile spostarsi ad altri oggetti `ContentPage`. Per altre informazioni sulla navigazione, vedere [Navigazione nella shell Xamarin.Forms](navigation.md).

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

Tutte queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) e ciò significa che tali proprietà possono essere destinazioni di data binding.

## <a name="bottom-and-top-tabs"></a>Schede inferiori e superiori

Quando è presente più di un oggetto `ShellContent` in un oggetto `Tab`, viene aggiunta una barra delle schede superiore oltre alla barra inferiore, che consente di spostarsi tra gli oggetti `ContentPage`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell"
       FlyoutBehavior="Disabled">
    <FlyoutItem>
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
    </FlyoutItem>
</Shell>
```

Il risultato è il layout illustrato negli screenshot seguenti:

[![Screenshot di un'app shell di due pagine con schede superiori e inferiori, in iOS e Android](tabs-images/two-page-app-top-tabs.png "App shell di due pagine con schede superiori e inferiori")](tabs-images/two-page-app-top-tabs-large.png#lightbox "App shell di due pagine con schede superiori e inferiori")

In alternativa, è possibile usare gli operatori di conversione implicita della shell per rimuovere gli oggetti `ShellContent` e il secondo oggetto `Tab` nell'esempio precedente:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell"
       FlyoutBehavior="Disabled">
    <FlyoutItem>
        <Tab Title="Domestic"
             Icon="domestic.png">
            <views:CatsPage />
            <views:DogsPage />
        </Tab>
        <views:MonkeysPage IconImageSource="monkey.png" />
    </FlyoutItem>
</Shell>
```

Questa conversione implicita esegue automaticamente il wrapping di `MonkeysPage` in un oggetto `ShellContent`, di cui quindi viene eseguito il wrapping in un oggetto `Tab`. Viene inoltre eseguito il wrapping in modo implicito di `CatsPage` e `DogsPage` in oggetti `ShellContent`.

## <a name="efficient-page-loading"></a>Caricamento efficiente delle pagine

In un'applicazione shell ogni oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) in un oggetto `ShellContent` viene creato durante l'avvio dell'applicazione e ciò può portare a un'esperienza di avvio insoddisfacente. La shell, tuttavia, consente anche la creazione di pagine su richiesta, in risposta alla navigazione. A tale scopo, è possibile usare l'estensione di markup `DataTemplate` per convertire ogni oggetto `ContentPage` in un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) e quindi impostare il risultato come valore della proprietà `ShellContent.ContentTemplate`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">    
    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
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
    </FlyoutItem>    
</Shell>
```

Questo codice XAML crea e visualizza `CatsPage`, perché è il primo elemento del contenuto dichiarato nell'oggetto `Shell` sottoclassato. È possibile spostarsi tra `CatsPage` e `MonkeysPage` tramite le schede inferiori e queste pagine vengono create solo quando l'utente si sposta su di esse. Il vantaggio di questo approccio è che si evita di creare un'esperienza di avvio insoddisfacente, in quanto le pagine vengono create su richiesta in risposta alla navigazione, invece che all'avvio dell'applicazione.

## <a name="tab-appearance"></a>Aspetto delle schede

La classe `Shell` definisce le proprietà seguenti che controllano l'aspetto delle schede:

- `TabBarBackgroundColor`, di tipo `Color`, una proprietà associata che definisce il colore di sfondo per la barra delle schede. Se la proprietà non è impostata, viene usato il valore della proprietà `BackgroundColor`.
- `TabBarDisabledColor`, di tipo `Color`, una proprietà associata che definisce il colore degli elementi disabilitati per la barra delle schede. Se la proprietà non è impostata, viene usato il valore della proprietà `DisabledColor`.
- `TabBarForegroundColor`, di tipo `Color`, una proprietà associata che definisce il colore primo piano per la barra delle schede. Se la proprietà non è impostata, viene usato il valore della proprietà `ForegroundColor`.
- `TabBarTitleColor`, di tipo `Color`, una proprietà associata che definisce il colore del titolo per la barra delle schede. Se la proprietà non è impostata, viene usato il valore della proprietà `TitleColor`.
- `TabBarUnselectedColor`, di tipo `Color`, una proprietà associata che definisce il colore degli elementi non selezionati per la barra delle schede. Se la proprietà non è impostata, viene usato il valore della proprietà `UnselectedColor`.

Tutte queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) e ciò significa che tali proprietà possono essere destinazioni di data binding.

Lo stile delle schede può essere quindi definito usando stili XAML. L'esempio seguente illustra uno stile XAML che imposta proprietà diverse per i colori delle schede:

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

- [Xaminals (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
- [Navigazione nella shell Xamarin.Forms](navigation.md)
- [Xamarin.Forms Shell specific properties](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties) (Proprietà specifiche della shell Xamarin.Forms)
