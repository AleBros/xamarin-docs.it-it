---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e1590d0a4f9716541f18bc4f50a2c480c5e4478a
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84129779"
---
# <a name="keyboard-accessibility-in-xamarinforms"></a>Accessibilità tastiera inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

Gli utenti che usano utilità per la lettura dello schermo o con problemi di mobilità possono avere difficoltà nell'usare le applicazioni che non forniscono un accesso tramite tastiera appropriato. Xamarin.Formsper migliorare l'usabilità e l'accessibilità, è possibile specificare un ordine di tabulazione previsto per le applicazioni. Specificare un ordine di tabulazione per i controlli consente gli spostamenti tramite tastiera, prepara le pagine dell'applicazione per ricevere l'input in un ordine particolare e consente alle utilità per la lettura dello schermo di leggere gli elementi su cui è possibile spostare lo stato attivo per gli utenti.

Per impostazione predefinita, l'ordine di tabulazione dei controlli è lo stesso in cui sono elencati in XAML o vengono aggiunti a livello di codice a una raccolta figlio. Questo ordine corrisponde all'ordine con cui ci si sposterà tra i controlli tramite tastiera e all'ordine di lettura delle utilità per la lettura dello schermo e spesso l'ordine predefinito è l'ordine ottimale. Tuttavia, l'ordine predefinito non sempre coincide con l'ordine previsto, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname" />
</Grid>
```

Lo screenshot seguente mostra l'ordine di tabulazione predefinito per questo esempio di codice:

![](keyboard-images/default-tab-order.png "Default Row-based Tab Order")

In questo caso l'ordine di tabulazione è basato su riga e corrisponde all'ordine in cui sono elencati i controlli in XAML. La pressione del tasto TAB consente pertanto di spostarsi tra le [`Entry`](xref:Xamarin.Forms.Entry) istanze di forename, seguite da istanze di cognome `Entry` . Tuttavia, l'uso di spostamenti tramite TAB basati su colonna offrirebbe un'esperienza più intuitiva, ovvero premendo TAB ci si potrebbe spostare in progressione tra le coppie di nome e cognome. Si può ottenere questo comportamento specificando l'ordine di tabulazione dei controlli di input.

> [!NOTE]
> Nella piattaforma UWP (Universal Windows Platform) è possibile definire tasti di scelta rapida che offrono agli utenti un modo intuitivo per spostarsi e interagire nell'interfaccia utente visibile dell'applicazione tramite tastiera anziché tramite tocco o mouse. Per altre informazioni, vedere [Impostazione delle chiavi di accesso VisualElement](~/xamarin-forms/platform/windows/visualelement-access-keys.md).

## <a name="setting-the-tab-order"></a>Impostazione dell'ordine di tabulazione

La `VisualElement.TabIndex` proprietà viene utilizzata per indicare l'ordine in cui [`VisualElement`](xref:Xamarin.Forms.VisualElement) le istanze di ricevono lo stato attivo quando l'utente passa attraverso i controlli premendo il tasto TAB. Il valore predefinito della proprietà è 0, e può essere impostata su qualsiasi valore `int`.

Le regole seguenti si applicano per l'uso dell'ordine di tabulazione predefinito o per l'impostazione della proprietà `TabIndex`:

- [`VisualElement`](xref:Xamarin.Forms.VisualElement)le istanze con `TabIndex` uguale a 0 vengono aggiunte all'ordine di tabulazione in base al relativo ordine di dichiarazione in raccolte XAML o figlio.
- [`VisualElement`](xref:Xamarin.Forms.VisualElement)le istanze con una `TabIndex` maggiore di 0 vengono aggiunte all'ordine di tabulazione in base al relativo `TabIndex` valore.
- [`VisualElement`](xref:Xamarin.Forms.VisualElement)le istanze con un `TabIndex` valore minore di 0 vengono aggiunte all'ordine di tabulazione e vengono visualizzate prima di qualsiasi valore zero.
- I conflitti per `TabIndex` vengono risolti in base all'ordine di dichiarazione.

Dopo aver definito un ordine di tabulazione, premendo TAB lo stato attivo viene spostato in sequenza tra i controlli in ordine crescente di `TabIndex`, ritornando all'inizio dopo aver raggiunto il controllo finale.

L'esempio XAML seguente mostra la proprietà `TabIndex` impostata per i controlli di input per consentire gli spostamenti tramite TAB basati su colonna:

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="1" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="3" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="2" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="4" />
</Grid>
```

Lo screenshot seguente mostra l'ordine di tabulazione per questo esempio di codice:

![](keyboard-images/correct-tab-order.png "Column-based Tab Order")

In questo caso l'ordine di tabulazione è basato su colonna. La pressione del tasto TAB consente pertanto di spostarsi tra le coppie forename-cognome [`Entry`](xref:Xamarin.Forms.Entry) .

> [!IMPORTANT]
> Le utilità per la lettura dello schermo in iOS e Android rispetteranno il `TabIndex` di un [`VisualElement`](xref:Xamarin.Forms.VisualElement) durante la lettura degli elementi accessibili sullo schermo.

## <a name="excluding-controls-from-the-tab-order"></a>Esclusione di controlli dall'ordine di tabulazione

Oltre a impostare l'ordine di tabulazione dei controlli, potrebbe essere necessario escludere i controlli dall'ordine di tabulazione. Un modo per ottenere questo problema consiste nell'impostare la [`IsEnabled`](xref:Xamarin.Forms.VisualElement) proprietà dei controlli su `false` , perché i controlli disabilitati sono esclusi dall'ordine di tabulazione.

Tuttavia, potrebbe essere necessario escludere i controlli dall'ordine di tabulazione anche quando non son disabilitati. Questa operazione può essere eseguita con la `VisualElement.IsTabStop` proprietà, che indica se un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) è incluso nella navigazione tramite tabulazione. Il valore predefinito è `true` e quando questo valore è `false` il controllo viene ignorato dall'infrastruttura di spostamento tramite TAB, indipendentemente dall'impostazione di `TabIndex`.

## <a name="supported-controls"></a>Controlli supportati

Le proprietà `TabIndex` e `IsTabStop` sono supportate per i controlli seguenti, che accettano input da tastiera in una o più piattaforme:

- [`Button`](xref:Xamarin.Forms.Button)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`Switch`](xref:Xamarin.Forms.Switch)
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

> [!NOTE]
> Ognuno di questi controlli non è attivabile tramite TAB in ogni piattaforma.

## <a name="related-links"></a>Collegamenti correlati

- [Accessibility (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility) (Esempio di accessibilità)
