---
title: Casella di controllo Novell. Forms
description: La casella di controllo Novell. Forms è un tipo di pulsante che può essere selezionato o vuoto. Quando una casella di controllo è selezionata, viene considerata attivata. Quando una casella di controllo è vuota, viene considerata disattivata.
ms.prod: xamarin
ms.assetid: B8B9268B-BCB8-42B9-B08C-C0F22C137238
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: f78ca9d2cf7a9e57b81c5d923c64b36a7982c4b0
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739151"
---
# <a name="xamarinforms-checkbox"></a>Casella di controllo Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

Novell. Forms `CheckBox` è un tipo di pulsante che può essere selezionato o vuoto. Quando una casella di controllo è selezionata, viene considerata attivata. Quando una casella di controllo è vuota, viene considerata disattivata.

`CheckBox`definisce una `bool` proprietà denominata `IsChecked`, che indica se l' `CheckBox` oggetto è selezionato. Questa proprietà è supportata anche da un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetto, il che significa che è possibile applicare uno stile e essere la destinazione delle associazioni dati.

> [!NOTE]
> La `IsChecked` proprietà associabile ha una modalità di [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)associazione predefinita.

`CheckBox`definisce un `CheckedChanged` evento generato quando la `IsChecked` proprietà viene modificata, tramite la manipolazione dell'utente o quando un'applicazione imposta la `IsChecked` proprietà. L' `CheckedChangedEventArgs` oggetto che accompagna l' `CheckedChanged` evento ha una singola proprietà denominata `Value`, di tipo `bool`. Quando viene generato l'evento, il valore della `Value` proprietà viene impostato sul nuovo valore `IsChecked` della proprietà.

## <a name="create-a-checkbox"></a>Crea una casella di controllo

Nell'esempio seguente viene mostrato come creare un `CheckBox` in XAML:

```xaml
<CheckBox />
```

Questo codice XAML genera l'aspetto illustrato nelle schermate seguenti:

![Screenshot di una casella di controllo vuota, in iOS e Android](checkbox-images/checkbox-empty.png "Casella") di controllo vuota

Per impostazione predefinita, `CheckBox` è vuoto. L' `CheckBox` oggetto può essere controllato dalla manipolazione dell'utente oppure impostando `IsChecked` la proprietà `true`su:

```xaml
<CheckBox IsChecked="true" />
```

Questo codice XAML genera l'aspetto illustrato nelle schermate seguenti:

![Screenshot di una casella di controllo selezionata, in iOS e Android](checkbox-images/checkbox-checked.png "Casella di controllo selezionata")

In alternativa, è `CheckBox` possibile creare un oggetto nel codice:

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Rispondi a una casella di controllo modifica stato

Quando la `IsChecked` proprietà viene modificata, tramite la manipolazione dell'utente o quando un'applicazione `IsChecked` imposta la proprietà `CheckedChanged` , viene generato l'evento. Un gestore eventi per questo evento può essere registrato per rispondere alla modifica:

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

Il file code-behind contiene il gestore per il `CheckedChanged` evento:

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

Il `sender` argomento è il `CheckBox` responsabile di questo evento. Ciò consente di accedere il `CheckBox` oggetto, o per distinguere tra più `CheckBox` gli oggetti che condividono lo stesso `CheckedChanged` evento.

In alternativa, un gestore eventi per l' `CheckedChanged` evento può essere registrato nel codice:

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Casella di controllo Associa dati

Il `CheckedChanged` gestore eventi può essere eliminato utilizzando Data Binding e trigger per rispondere a un `CheckBox` oggetto che viene verificato o vuoto:

```xaml
<CheckBox x:Name="checkBox" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

In questo esempio, [`Label`](xref:Xamarin.Forms.Label) usa un'espressione di associazione in un trigger di dati per monitorare la `IsChecked` proprietà dell' `CheckBox`oggetto. Quando questa proprietà diventa `true`, le `FontAttributes` proprietà `FontSize` e della `Label` modifica. Quando la `IsChecked` `false`proprietà restituisce, le `FontAttributes` proprietà e `FontSize` dell'oggetto `Label` vengono reimpostate sullo stato iniziale.

Negli screenshot seguenti lo screenshot iOS Mostra [`Label`](xref:Xamarin.Forms.Label) la formattazione `CheckBox` quando è vuoto, mentre lo screenshot di Android Mostra la `Label` formattazione quando è selezionata l' `CheckBox` opzione:

[ ![Screenshot della casella di controllo Data Binding, nella casella di]controllo(checkbox-images/checkbox-databinding.png "Data Binding") iOS e Android] (checkbox-images/checkbox-databinding-large.png#lightbox "Casella di controllo Data Binding")

Per ulteriori informazioni sui trigger, vedere [trigger Novell. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Disabilitare una casella di controllo

A volte un'applicazione entra in uno stato `CheckBox` in cui un controllo non è un'operazione valida. In questi casi, `CheckBox` può essere disabilitato impostando la relativa `IsEnabled` proprietà `false`su.

## <a name="checkbox-appearance"></a>Aspetto casella di controllo

Oltre alle proprietà che `CheckBox` ereditano [`View`](xref:Xamarin.Forms.View) dalla classe, `CheckBox` definisce anche una `Color` proprietà che ne imposta il colore su un oggetto [`Color`](xref:Xamarin.Forms.Color):

```xaml
<CheckBox Color="Red" />
```

Gli screenshot seguenti mostrano una serie di oggetti controllati `CheckBox` , in cui ogni oggetto ha la `Color` proprietà impostata su un valore [`Color`](xref:Xamarin.Forms.Color)diverso:

![Screenshot delle caselle di controllo colorate, in iOS e Android](checkbox-images/checkbox-colors.png "Casella") di controllo colorato

## <a name="checkbox-visual-states"></a>Stati di visualizzazione casella di controllo

`CheckBox`dispone di `IsChecked` un oggetto [`VisualState`](xref:Xamarin.Forms.VisualState) che può essere utilizzato per avviare `CheckBox` una modifica visiva a quando viene controllata.

Nell'esempio XAML seguente viene illustrato come definire uno stato di visualizzazione per il `IsChecked` stato:

```xaml
<CheckBox ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Red" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="IsChecked">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Green" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</CheckBox>
```

`IsChecked` In questo esempio, [`VisualState`](xref:Xamarin.Forms.VisualState) specifica che quando `CheckBox` è selezionato, `Color` la proprietà verrà impostata su verde. Specifica che quando`CheckBox` è in uno stato normale, la relativa `Color` proprietà sarà impostata su rosso. `Normal` `VisualState` Pertanto, l'effetto complessivo è che il `CheckBox` è rosso quando è vuoto e verde quando è selezionato.

Per ulteriori informazioni sugli stati visivi, vedere [Novell. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Collegamenti correlati

- [Demo CheckBox (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Trigger di Novell. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Gestore dello stato di visualizzazione di Novell. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
