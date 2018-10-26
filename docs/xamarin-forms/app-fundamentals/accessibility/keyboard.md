---
title: Navigazione tramite tastiera
description: Anziché utilizzare la sequenza di tabulazione predefinito, è talvolta necessario ottimizzare l'interfaccia utente specificando la sequenza di scheda con una combinazione delle proprietà TabIndex e IsTapStop.
ms.prod: xamarin
ms.assetid: 8be8f498-558a-4894-a01f-91a0d3ef927e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2018
ms.openlocfilehash: f703dff56d2947c35a9bc76e0eb909bfe9023bac
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131555"
---
# <a name="keyboard-navigation-in-xamarinforms"></a>Navigazione tramite tastiera in xamarin. Forms

Alcuni utenti possono avere difficoltà nell'utilizzare le applicazioni che non forniscono accesso tramite tastiera appropriato. Specificare un ordine di tabulazione dei controlli consente la navigazione da tastiera e lo prepara le pagine dell'applicazione per ricevere l'input in un ordine particolare.

Per impostazione predefinita, l'ordine di tabulazione dei controlli è stesso ordine in cui essi sono elencati in XAML o a livello di codice aggiunto a una raccolta figlio. Questo ordine corrisponde all'ordine in cui verranno reindirizzati i controlli tramite con una tastiera e spesso l'ordine predefinito è l'ordine ottimale. Tuttavia, l'ordine predefinito non è sempre lo stesso l'ordine previsto, come illustrato nell'esempio di codice XAML seguente:

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

![](keyboard-images/default-tab-order.png "Ordine di tabulazione basata su riga predefinita")

In questo caso l'ordine di tabulazione è basato su riga e corrisponde all'ordine in cui che sono elencati i controlli nella finestra di XAML. Di conseguenza, premendo il tasto Tab consente di passare attraverso facsimile [ `Entry` ](xref:Xamarin.Forms.Entry) istanze, seguite dal cognome `Entry` istanze. Tuttavia, un'esperienza più intuitiva sarebbe utilizzare una navigazione tramite tabulazione colonna-first, in modo che premendo il tasto Tab consente di passare attraverso le coppie nome-Cognome. Ciò può essere ottenuto specificando l'ordine di tabulazione dei controlli di input.

> [!NOTE]
> Nella piattaforma Windows universale, è possibile definire tasti di scelta rapida che forniscono un modo intuitivo per gli utenti di passare rapidamente e interagire con l'interfaccia utente visibile dell'applicazione tramite la tastiera anziché tramite tocco o mouse. Per altre informazioni, vedere [chiavi di accesso di impostazione VisualElement](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#visualelement-accesskeys).

## <a name="setting-the-tab-order"></a>Impostazione dell'ordine di tabulazione

Il `VisualElement.TabIndex` proprietà viene utilizzata per indicare l'ordine in cui [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) istanze ricevano lo stato attivo quando l'utente si sposta attraverso controlli premendo il tasto Tab. Il valore predefinito della proprietà è 0, e può essere impostata su uno `int` valore.

Le regole seguenti si applicano quando usando l'ordine di tabulazione predefinito, o impostando la `TabIndex` proprietà:

 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) istanze con un `TabIndex` uguale a 0 vengono aggiunti per l'ordine di tabulazione in base all'ordine di dichiarazione nelle raccolte XAML o figlio.
 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) istanze con un `TabIndex` maggiore di 0 vengono aggiunti per l'ordine di tabulazione in base i `TabIndex` valore.
 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) istanze con un `TabIndex` minori di 0 vengono aggiunti per l'ordine di tabulazione e vengono visualizzati prima di un valore pari a zero.
 - È in conflitto in un `TabIndex` vengono risolti in base all'ordine di dichiarazione.

Dopo aver definito un ordine di tabulazione, premendo il tasto Tab viene ripresa lo stato attivo tramite i controlli in ordine crescente `TabIndex` ordine, il wrapping intorno all'inizio una volta raggiunto il controllo finale.

L'esempio XAML seguente viene illustrato il `TabIndex` proprietà è impostata su controlli di input per consentire una navigazione scheda colonna-first:

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

![](keyboard-images/correct-tab-order.png "Ordine di tabulazione in colonne")

In questo caso l'ordine di tabulazione è basata su colonne. Di conseguenza, premendo il tasto Tab consente di passare attraverso il cognome di cognome [ `Entry` ](xref:Xamarin.Forms.Entry) coppie.

## <a name="excluding-controls-from-the-tab-order"></a>Esclusi i controlli dall'ordine di tabulazione

Oltre a impostare l'ordine di tabulazione dei controlli, è necessario escludere i controlli dall'ordine di tabulazione. Testmanagementclient ciò consiste nell'impostare il [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement) proprietà dei controlli da `false`, in quanto i controlli disattivati vengono esclusi dall'ordine di tabulazione.

Tuttavia, potrebbe essere necessario escludere i controlli dall'ordine di tabulazione, anche quando essi non vengono disabilitati. Ciò può essere ottenuto con il `VisualElement.IsTapStop` proprietà, che indica se un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) è incluso nella navigazione tramite tabulazione. Il valore predefinito è `true`, e quando il relativo valore è `false` il controllo viene ignorato dall'infrastruttura di spostamento tramite tabulazione, anche nel caso se un `TabIndex` è impostata.

## <a name="supported-controls"></a>Controlli supportati

Il `TabIndex` e `IsTapStop` sono supportate le proprietà nei comandi seguenti, che accettano input da tastiera in uno o più piattaforme:

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
> Ognuno di questi controlli non è attivabile in ogni piattaforma di scheda.

## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
