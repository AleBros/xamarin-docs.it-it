---
title: Casella di controllo Xamarin.Forms
description: La casella di controllo Xamarin.Forms è un tipo di pulsante che può essere selezionato o vuoto. Quando una casella di controllo è selezionata, viene considerata attivata. Quando una casella di controllo è vuota, viene considerata disattivata.
ms.prod: xamarin
ms.assetid: B8B9268B-BCB8-42B9-B08C-C0F22C137238
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: f78ca9d2cf7a9e57b81c5d923c64b36a7982c4b0
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "68739151"
---
# <a name="xamarinforms-checkbox"></a>Casella di controllo Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

Il `CheckBox` Xamarin.Forms è un tipo di pulsante che può essere selezionato o vuoto. Quando una casella di controllo è selezionata, viene considerata attivata. Quando una casella di controllo è vuota, viene considerata disattivata.

`CheckBox` definisce una proprietà di `bool` denominata `IsChecked`, che indica se il `CheckBox` è selezionato. Questa proprietà è supportata anche da un oggetto [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che è possibile applicare uno stile e essere la destinazione delle associazioni dati.

> [!NOTE]
> Il `IsChecked` proprietà associabile ha una modalità di associazione predefinita di [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay).

`CheckBox` definisce un evento `CheckedChanged` generato quando la proprietà `IsChecked` viene modificata, tramite la manipolazione dell'utente o quando un'applicazione imposta la proprietà `IsChecked`. L'oggetto `CheckedChangedEventArgs` che accompagna l'evento `CheckedChanged` dispone di una singola proprietà denominata `Value`, di tipo `bool`. Quando viene generato l'evento, il valore della proprietà `Value` viene impostato sul nuovo valore della proprietà `IsChecked`.

## <a name="create-a-checkbox"></a>Crea una casella di controllo

Nell'esempio seguente viene illustrato come creare un'istanza di un `CheckBox` in XAML:

```xaml
<CheckBox />
```

Questo codice XAML genera l'aspetto illustrato nelle schermate seguenti:

![Screenshot di una casella di controllo vuota, in iOS e Android](checkbox-images/checkbox-empty.png "Casella di controllo vuota")

Per impostazione predefinita, il `CheckBox` è vuoto. Il `CheckBox` può essere controllato dalla manipolazione dell'utente oppure impostando la proprietà `IsChecked` su `true`:

```xaml
<CheckBox IsChecked="true" />
```

Questo codice XAML genera l'aspetto illustrato nelle schermate seguenti:

![Screenshot di una casella di controllo selezionata, in iOS e Android](checkbox-images/checkbox-checked.png "Casella di controllo selezionata")

In alternativa, è possibile creare un `CheckBox` nel codice:

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Rispondi a una casella di controllo modifica stato

Quando la proprietà `IsChecked` viene modificata, tramite la manipolazione dell'utente o quando un'applicazione imposta la proprietà `IsChecked`, viene generato l'evento `CheckedChanged`. Un gestore eventi per questo evento può essere registrato per rispondere alla modifica:

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

Il file code-behind contiene il gestore per l'evento `CheckedChanged`:

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

L'argomento `sender` è il `CheckBox` responsabile di questo evento. È possibile usarlo per accedere all'oggetto `CheckBox` o per distinguere tra più oggetti `CheckBox` che condividono lo stesso evento di `CheckedChanged`.

In alternativa, è possibile registrare un gestore eventi per l'evento `CheckedChanged` nel codice:

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Casella di controllo Associa dati

Il gestore dell'evento `CheckedChanged` può essere eliminato utilizzando data binding e trigger per rispondere a una `CheckBox` controllata o vuota:

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

In questo esempio, il [`Label`](xref:Xamarin.Forms.Label) utilizza un'espressione di associazione in un trigger di dati per monitorare la proprietà `IsChecked` dell'`CheckBox`. Quando questa proprietà diventa `true`, le proprietà `FontAttributes` e `FontSize` della `Label` cambiano. Quando la proprietà `IsChecked` restituisce `false`, le proprietà `FontAttributes` e `FontSize` del `Label` vengono reimpostate sullo stato iniziale.

Negli screenshot seguenti lo screenshot iOS Mostra la formattazione [`Label`](xref:Xamarin.Forms.Label) quando il `CheckBox` è vuoto, mentre lo screenshot di Android Mostra la formattazione `Label` quando viene controllata la `CheckBox`:

[![Screenshot della casella di controllo Data Binding in iOS e Android](checkbox-images/checkbox-databinding.png "Casella di controllo Data Binding")](checkbox-images/checkbox-databinding-large.png#lightbox "Casella di controllo Data Binding")

Per ulteriori informazioni sui trigger, vedere [trigger Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Disabilitare una casella di controllo

A volte un'applicazione entra in uno stato in cui un `CheckBox` sottoposto a verifica non è un'operazione valida. In questi casi, è possibile disabilitare il `CheckBox` impostando la relativa proprietà `IsEnabled` su `false`.

## <a name="checkbox-appearance"></a>Aspetto casella di controllo

Oltre alle proprietà che `CheckBox` eredita dalla classe [`View`](xref:Xamarin.Forms.View) , `CheckBox` definisce anche una proprietà `Color` che ne imposta il colore su un [`Color`](xref:Xamarin.Forms.Color):

```xaml
<CheckBox Color="Red" />
```

Gli screenshot seguenti mostrano una serie di oggetti `CheckBox` controllati, in cui ogni oggetto ha la relativa proprietà `Color` impostata su un [`Color`](xref:Xamarin.Forms.Color)diverso:

![Screenshot delle caselle di controllo colorate, in iOS e Android](checkbox-images/checkbox-colors.png "Casella di controllo colorato")

## <a name="checkbox-visual-states"></a>Stati di visualizzazione casella di controllo

`CheckBox` dispone di un [`VisualState`](xref:Xamarin.Forms.VisualState) `IsChecked` che può essere utilizzato per avviare una modifica visiva del `CheckBox` quando viene controllata.

Nell'esempio di codice XAML riportato di seguito viene illustrato come definire uno stato di visualizzazione per lo stato `IsChecked`:

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

In questo esempio, il `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) specifica che, quando il `CheckBox` è selezionato, la relativa proprietà `Color` verrà impostata su verde. Il `Normal` `VisualState` specifica che quando il `CheckBox` è in uno stato normale, la relativa proprietà `Color` verrà impostata su rosso. Pertanto, l'effetto complessivo è che la `CheckBox` è rossa quando è vuota e verde quando viene controllata.

Per ulteriori informazioni sugli stati visivi, vedere [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Collegamenti correlati

- [Demo CheckBox (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Trigger di Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Gestore dello stato di visualizzazione di Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
