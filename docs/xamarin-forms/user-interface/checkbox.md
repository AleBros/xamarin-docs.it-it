---
title: Xamarin.Forms CheckBox
description: La casella di controllo di xamarin. Forms è un tipo di pulsante che può essere controllato o vuoto. Se una casella di controllo è selezionata, è considerato in. Quando una casella di controllo è vuoto, questo ha considerato per essere disattivato.
ms.prod: xamarin
ms.assetid: B8B9268B-BCB8-42B9-B08C-C0F22C137238
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: 42631b1b67dc1d342e9f8666916604e68ee158d8
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67517920"
---
# <a name="xamarinforms-checkbox"></a>Xamarin.Forms CheckBox

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CheckBoxDemos)

Xamarin. Forms `CheckBox` è un tipo di pulsante che può essere selezionato o vuoto. Se una casella di controllo è selezionata, è considerato in. Quando una casella di controllo è vuoto, questo ha considerato per essere disattivato.

`CheckBox` definisce un `bool` proprietà denominata `IsChecked`, che indica se il `CheckBox` sia selezionata. Questa proprietà è supportata anche da un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetto, il che significa che possono essere personalizzati con stili e può essere la destinazione di associazioni dati.

> [!NOTE]
> Il `IsChecked` proprietà associabili ha una modalità di associazione predefinita [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay).

`CheckBox` definisce un `CheckedChanged` evento generato quando il `IsChecked` le modifiche alle proprietà, tramite la modifica dell'utente o quando un'applicazione imposta il `IsChecked` proprietà. Il `CheckedChangedEventArgs` che accompagna il `CheckedChanged` evento dispone di una singola proprietà denominata `Value`, di tipo `bool`. Quando viene generato l'evento, il valore della `Value` proprietà è impostata sul nuovo valore della `IsChecked` proprietà.

## <a name="create-a-checkbox"></a>Creare una casella di controllo

Nell'esempio seguente viene mostrato come creare un `CheckBox` in XAML:

```xaml
<CheckBox />
```

Questo XAML determina l'aspetto illustrato negli screenshot seguenti:

![Screenshot di una casella di controllo vuoto in iOS e Android](checkbox-images/checkbox-empty.png "vuota la casella di controllo")

Per impostazione predefinita, il `CheckBox` è vuoto. Il `CheckBox` può essere controllato mediante la modifica dell'utente, o impostando il `IsChecked` proprietà `true`:

```xaml
<CheckBox IsChecked="true" />
```

Questo XAML determina l'aspetto illustrato negli screenshot seguenti:

![Screenshot di una casella di controllo selezionata, in iOS e Android](checkbox-images/checkbox-checked.png "selezionata la casella di controllo")

In alternativa, un `CheckBox` possono essere creati nel codice:

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Rispondere a una casella di controllo modifica dello stato

Quando la `IsChecked` le modifiche alle proprietà, tramite la modifica dell'utente o quando un'applicazione imposta il `IsChecked` proprietà, il `CheckedChanged` viene generato l'evento. Un gestore eventi per questo evento può essere registrato per rispondere alla modifica:

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

In alternativa, un gestore eventi per il `CheckedChanged` può essere registrato un evento nel codice:

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Associa dati di una casella di controllo

Il `CheckedChanged` gestore dell'evento possa essere eliminato usando data binding e trigger per rispondere a un `CheckBox` selezionato o vuoto:

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

In questo esempio, il [ `Label` ](xref:Xamarin.Forms.Label) Usa un'espressione di associazione in un trigger di dati per monitorare il `IsChecked` proprietà del `CheckBox`. Quando questa proprietà diventa `true`, il `FontAttributes` e `FontSize` delle proprietà del `Label` modificare. Quando la `IsChecked` proprietà restituisce al `false`, il `FontAttributes` e `FontSize` le proprietà del `Label` vengono reimpostati allo stato iniziale.

Nelle schermate seguenti, la schermata iOS Mostra il [ `Label` ](xref:Xamarin.Forms.Label) formattazione quando il `CheckBox` è vuoto, mentre la schermata Android Mostra il `Label` formattazione quando il `CheckBox` viene controllata:

[![Screenshot di un dato associato casella di controllo, in iOS e Android](checkbox-images/checkbox-databinding.png "casella di controllo con associazione a dati")](checkbox-images/checkbox-databinding-large.png#lightbox "casella di controllo con associazione a dati")

Per altre informazioni sui trigger, vedere [trigger di xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Disabilitare una casella di controllo

In alcuni casi un'applicazione passa allo stato in cui un `CheckBox` sottoposto al controllo non è un'operazione valida. In questi casi, il `CheckBox` possono essere disabilitate impostando relativi `IsEnabled` proprietà `false`.

## <a name="checkbox-appearance"></a>Aspetto di casella di controllo

Oltre alle proprietà che `CheckBox` eredita dal [ `View` ](xref:Xamarin.Forms.View) (classe), `CheckBox` definisce anche un `Color` proprietà che imposta il colore di un [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<CheckBox Color="Red" />
```

Le schermate seguenti illustrano una serie di checked `CheckBox` oggetti, dove ogni oggetto dispone relativo `Color` proprietà è impostata su una diversa [ `Color` ](xref:Xamarin.Forms.Color):

![Schermata di caselle di controllo colorata, su iOS e Android](checkbox-images/checkbox-colors.png "casella di controllo colorata")

## <a name="checkbox-visual-states"></a>Stati di visualizzazione di casella di controllo

`CheckBox` è un' `IsChecked` [ `VisualState` ](xref:Xamarin.Forms.VisualState) che può essere utilizzato per avviare una modifica visiva al `CheckBox` quando è stato selezionato.

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

In questo esempio, il `IsChecked` [ `VisualState` ](xref:Xamarin.Forms.VisualState) specifica che quando il `CheckBox` è selezionata, relativo `Color` verrà impostata su verde. Il `Normal` `VisualState` specifica che quando il `CheckBox` è in uno stato normale, relativo `Color` verrà impostata su rosso. Pertanto, il risultato complessivo è che il `CheckBox` è rosso quando è vuoto e verde quando viene selezionato.

Per altre informazioni sugli stati visual, vedere [xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Collegamenti correlati

- [Demo di casella di controllo (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CheckBoxDemos)
- [Trigger di xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)
