---
title: RadioButton Novell. Forms
description: Il RadioButton Novell. Forms è un tipo di pulsante che consente agli utenti di selezionare un'opzione da un set. Ogni opzione è rappresentata da un solo pulsante di opzione ed è possibile selezionare un solo pulsante di opzione in un gruppo.
ms.prod: xamarin
ms.assetid: E2AA40E0-69A5-41DF-BFC4-C151CA657451
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/13/2020
ms.openlocfilehash: 128ab4f6f00adaf86afc08eba37bcb81d3a04a90
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532814"
---
# <a name="xamarinforms-radiobutton"></a>RadioButton Novell. Forms

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)

Novell. Forms `RadioButton` è un tipo di pulsante che consente agli utenti di selezionare un'opzione da un set. Ogni opzione è rappresentata da un solo pulsante di opzione ed è possibile selezionare un solo pulsante di opzione in un gruppo. La `RadioButton` classe eredita dalla [`Button`](xref:Xamarin.Forms.Button) classe.

Gli screenshot seguenti mostrano `RadioButton` gli oggetti negli stati cancellati e selezionati, in iOS e Android:

![Screenshot dei radiopulsanti negli stati selezionati e deselezionati in iOS e Android](radiobutton-images/radiobutton-states.png "RadioButtons in iOS e Android")

> [!IMPORTANT]
> `RadioButton`è attualmente sperimentale e può essere usato solo impostando `RadioButton_Experimental` il flag. Per ulteriori informazioni, vedere [flag sperimentali](~/xamarin-forms/internals/experimental-flags.md).

Il `RadioButton` controllo definisce le proprietà seguenti:

- `IsChecked`, di tipo `bool`, che definisce se l' `RadioButton` oggetto è selezionato. Questa proprietà usa un' `TwoWay` associazione e il valore predefinito è `false`.
- `GroupName`, di tipo `string`, che definisce il nome che specifica quali `RadioButton` controlli si escludono a vicenda. Questa proprietà ha un valore predefinito di `null`.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Il `RadioButton` controllo definisce un `CheckedChanged` evento generato quando la proprietà viene `IsChecked` modificata tramite l'utente o la manipolazione a livello di codice. L' `CheckedChangedEventArgs` oggetto che accompagna l' `CheckedChanged` evento ha una singola proprietà denominata `Value`, di tipo. `bool` Quando viene generato l'evento, il valore della `Value` proprietà viene impostato sul nuovo valore della `IsChecked` proprietà.

Inoltre, la `RadioButton` classe eredita le seguenti proprietà utilizzate in genere dalla [`Button`](xref:Xamarin.Forms.Button) classe:

- [`Command`](xref:Xamarin.Forms.Button.Command), di tipo `ICommand`, che viene eseguito quando l' `RadioButton` oggetto è selezionato.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter), di tipo `object`, che è il parametro passato a `Command`.
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes), di tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes), che determina lo stile del testo.
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily), di tipo `string`, che definisce la famiglia di caratteri.
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize), di tipo `double`, che definisce le dimensioni del carattere.
- [`Text`](xref:Xamarin.Forms.Button.Text), di tipo `string`, che definisce il testo da visualizzare.
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor), di tipo [`Color`](xref:Xamarin.Forms.Color), che definisce il colore del testo visualizzato.

Per ulteriori informazioni sul controllo [`Button`](xref:Xamarin.Forms.Button) , vedere il [pulsante Novell. Forms](~/xamarin-forms/user-interface/button.md).

## <a name="create-radiobuttons"></a>Crea radiopulsanti

Nell'esempio seguente viene illustrato come creare un' `RadioButton` istanza di oggetti in XAML:

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton Text="Cat" />
    <RadioButton Text="Dog" />
    <RadioButton Text="Elephant" />
    <RadioButton Text="Monkey"
                 IsChecked="true" />
</StackLayout>
```

In questo esempio `RadioButton` gli oggetti vengono raggruppati in modo implicito all'interno dello stesso contenitore padre. Questo codice XAML genera l'aspetto illustrato nelle schermate seguenti:

![Screenshot dei radiopulsanti raggruppati in modo implicito in iOS e Android](radiobutton-images/radiobuttons.png "I radiopulsanti sono raggruppati in modo implicito in iOS e Android")

In alternativa, `RadioButton` è possibile creare oggetti nel codice:

```csharp
StackLayout stackLayout = new StackLayout
{
    Children =
    {
        new Label { Text = "What's your favorite animal?" },
        new RadioButton { Text = "Cat" },
        new RadioButton { Text = "Dog" },
        new RadioButton { Text = "Elephant" },
        new RadioButton { Text = "Monkey", IsChecked = true }
    }
};
```

## <a name="group-radiobuttons"></a>Gruppi di radiopulsanti

I pulsanti di opzione funzionano in gruppi e sono disponibili due approcci per raggruppare i pulsanti di opzione:

- Inserendoli all'interno dello stesso contenitore padre. Questa operazione è nota come raggruppamento implicito.
- Impostando la `GroupName` proprietà per ogni pulsante di opzione sullo stesso valore. Questa operazione è nota come raggruppamento esplicito.

Nell'esempio di codice XAML seguente viene illustrato come `RadioButton` raggruppare in modo `GroupName` esplicito gli oggetti impostando le relative proprietà:

```xaml
<Label Text="What's your favorite color?" />
<RadioButton Text="Red"
             TextColor="Red"
             GroupName="colors" />
<RadioButton Text="Green"
             TextColor="Green"
             GroupName="colors" />
<RadioButton Text="Blue"
             TextColor="Blue"
             GroupName="colors" />
<RadioButton Text="Other"
             GroupName="colors" />
```

In questo esempio ognuno si `RadioButton` escludono a vicenda perché condivide lo stesso `GroupName` valore. Questo codice XAML genera l'aspetto illustrato nelle schermate seguenti:

![Screenshot dei radiopulsanti raggruppati in modo esplicito in iOS e Android](radiobutton-images/grouped-radiobuttons.png "Raggruppare in modo esplicito i radiopulsanti in iOS e Android")

## <a name="respond-to-a-radiobutton-state-change"></a>Risposta a una modifica dello stato di RadioButton

Un pulsante di opzione ha due stati: selezionato o deselezionato. Quando viene selezionato un pulsante di opzione, `IsChecked` la relativa `true`proprietà è. Quando viene cancellato un pulsante di opzione, `IsChecked` la relativa `false`proprietà è. Un pulsante di opzione può essere deselezionato facendo clic su un altro pulsante di opzione nello stesso gruppo, ma non può essere deselezionato facendo di nuovo clic su di esso. Tuttavia, è possibile cancellare un pulsante di opzione a livello di codice `IsChecked` impostando `false`la relativa proprietà su.

Quando la `IsChecked` proprietà viene modificata, tramite la manipolazione utente o a livello `CheckedChanged` di codice, viene generato l'evento. Un gestore eventi per questo evento può essere registrato per rispondere alla modifica:

```xaml
<RadioButton Text="Red"
             TextColor="Red"
             GroupName="colors"
             CheckedChanged="OnColorsRadioButtonCheckedChanged" />
```

Il code-behind contiene il gestore per l' `CheckedChanged` evento:

```csharp
void OnColorsRadioButtonCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation
}
```

L' `sender` argomento è `RadioButton` responsabile di questo evento. È possibile usarlo per accedere all' `RadioButton` oggetto o per distinguere tra più `RadioButton` oggetti che condividono lo stesso `CheckedChanged` gestore eventi.

In alternativa, un gestore eventi per l' `CheckedChanged` evento può essere registrato nel codice:

```csharp
RadioButton radioButton = new RadioButton { ... };
radioButton.CheckedChanged += (sender, e) =>
{
    // Perform required operation
};
```

> [!NOTE]
> Un approccio alternativo per rispondere a una `RadioButton` modifica di stato consiste nel definire `ICommand` un oggetto e assegnarlo `RadioButton.Command` alla proprietà. Per altre informazioni, vedere [Button: using the Command Interface](~/xamarin-forms/user-interface/button.md#using-the-command-interface).

## <a name="radiobutton-visual-states"></a>Stati di visualizzazione RadioButton

`RadioButton`dispone di `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) un oggetto che può essere utilizzato per avviare una modifica visiva quando `RadioButton` è selezionato un oggetto.

Nell'esempio di codice XAML riportato di seguito viene illustrato come definire uno `IsChecked` stato di visualizzazione per lo stato:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="RadioButton">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Red" />
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="IsChecked">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Green" />
                                <Setter Property="Opacity"
                                        Value="1" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout>
        <Label Text="What's your favorite mode of transport?" />
        <RadioButton Text="Car"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Bike"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Train"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Walking"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
    </StackLayout>
</ContentPage>
```

In questo esempio gli oggetti di [`Style`](xref:Xamarin.Forms.Style) destinazione `RadioButton` impliciti. `IsChecked` `RadioButton` `TextColor` `Opacity` Specifica che quando è selezionato un oggetto, la relativa proprietà verrà impostata su verde con un valore pari a 1. [`VisualState`](xref:Xamarin.Forms.VisualState) `Normal` `RadioButton` `TextColor` `Opacity` Specifica che quando un oggetto è in uno stato cancellato, la relativa proprietà sarà impostata su rosso con un valore di 0,5. `VisualState` Pertanto, l'effetto complessivo è che quando un `RadioButton` oggetto viene cancellato è rosso e parzialmente trasparente ed è verde senza trasparenza quando è selezionato:

![Screenshot dell'aspetto di RadioButton impostato in base allo stato di visualizzazione, in iOS e Android](radiobutton-images/ischecked-visualstate.png "Stati di visualizzazione RadioButton in iOS e Android")

Per ulteriori informazioni sugli stati visivi, vedere [Novell. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-a-radiobutton"></a>Disabilitare un RadioButton

A volte un'applicazione entra in uno stato `RadioButton` in cui un controllo non è un'operazione valida. In questi casi, `RadioButton` può essere disabilitato impostando la `IsEnabled` relativa proprietà `false`su.

## <a name="related-links"></a>Link correlati

- [Demo RadioButton (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)
- [Pulsante Novell. Forms](~/xamarin-forms/user-interface/button.md)
- [Gestore dello stato di visualizzazione di Novell. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
