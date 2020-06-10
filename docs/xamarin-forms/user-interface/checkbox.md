---
title: " Xamarin.Forms CheckBox" Description: "la Xamarin.Forms casella di controllo è un tipo di pulsante che può essere selezionato o vuoto. Quando una casella di controllo è selezionata, viene considerata attivata. Quando una casella di controllo è vuota, viene considerata disattivata ".
ms. prod: Novell MS. AssetID: B8B9268B-BCB8-42B9-B08C-C0F22C137238 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 06/11/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-checkbox"></a>Xamarin.FormsCasella

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

Xamarin.Forms `CheckBox` È un tipo di pulsante che può essere selezionato o vuoto. Quando una casella di controllo è selezionata, viene considerata attivata. Quando una casella di controllo è vuota, viene considerata disattivata.

`CheckBox`definisce una `bool` proprietà denominata `IsChecked` , che indica se l'oggetto `CheckBox` è selezionato. Questa proprietà è supportata anche da un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetto, il che significa che è possibile applicare uno stile e essere la destinazione delle associazioni dati.

> [!NOTE]
> La `IsChecked` proprietà associabile ha una modalità di associazione predefinita [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) .

`CheckBox`definisce un `CheckedChanged` evento generato quando la `IsChecked` proprietà viene modificata, tramite la manipolazione dell'utente o quando un'applicazione imposta la `IsChecked` Proprietà. L' `CheckedChangedEventArgs` oggetto che accompagna l' `CheckedChanged` evento ha una singola proprietà denominata `Value` , di tipo `bool` . Quando viene generato l'evento, il valore della `Value` proprietà viene impostato sul nuovo valore della `IsChecked` Proprietà.

## <a name="create-a-checkbox"></a>Crea una casella di controllo

Nell'esempio seguente viene illustrato come creare un'istanza di `CheckBox` in XAML:

```xaml
<CheckBox />
```

Questo codice XAML genera l'aspetto illustrato nelle schermate seguenti:

![Screenshot di una casella di controllo vuota, in iOS e Android](checkbox-images/checkbox-empty.png "Casella di controllo vuota")

Per impostazione predefinita, `CheckBox` è vuoto. L'oggetto `CheckBox` può essere controllato dalla manipolazione dell'utente oppure impostando la `IsChecked` proprietà su `true` :

```xaml
<CheckBox IsChecked="true" />
```

Questo codice XAML genera l'aspetto illustrato nelle schermate seguenti:

![Screenshot di una casella di controllo selezionata, in iOS e Android](checkbox-images/checkbox-checked.png "Casella di controllo selezionata")

In alternativa, `CheckBox` è possibile creare un oggetto nel codice:

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Rispondi a una casella di controllo modifica stato

Quando la `IsChecked` proprietà viene modificata, tramite la manipolazione dell'utente o quando un'applicazione imposta la `IsChecked` proprietà, viene `CheckedChanged` generato l'evento. Un gestore eventi per questo evento può essere registrato per rispondere alla modifica:

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

Il file code-behind contiene il gestore per l' `CheckedChanged` evento:

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

L' `sender` argomento è `CheckBox` responsabile di questo evento. È possibile usarlo per accedere all' `CheckBox` oggetto o per distinguere tra più `CheckBox` oggetti che condividono lo stesso `CheckedChanged` gestore eventi.

In alternativa, un gestore eventi per l' `CheckedChanged` evento può essere registrato nel codice:

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Casella di controllo Associa dati

Il `CheckedChanged` gestore eventi può essere eliminato utilizzando Data Binding e trigger per rispondere a un oggetto `CheckBox` che viene verificato o vuoto:

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

In questo esempio, [`Label`](xref:Xamarin.Forms.Label) Usa un'espressione di associazione in un trigger di dati per monitorare la `IsChecked` proprietà dell'oggetto `CheckBox` . Quando questa proprietà diventa `true` , le `FontAttributes` `FontSize` proprietà e della `Label` modifica. Quando la `IsChecked` proprietà restituisce `false` , le `FontAttributes` proprietà e `FontSize` dell'oggetto `Label` vengono reimpostate sullo stato iniziale.

Negli screenshot seguenti lo screenshot iOS Mostra la [`Label`](xref:Xamarin.Forms.Label) formattazione quando `CheckBox` è vuoto, mentre lo screenshot di Android Mostra la `Label` formattazione quando è selezionata l'opzione `CheckBox` :

[![Screenshot della casella di controllo Data Binding in iOS e Android](checkbox-images/checkbox-databinding.png "Casella di controllo Data Binding")](checkbox-images/checkbox-databinding-large.png#lightbox "Casella di controllo Data Binding")

Per ulteriori informazioni sui trigger, vedere [ Xamarin.Forms trigger](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Disabilitare una casella di controllo

A volte un'applicazione entra in uno stato in cui un `CheckBox` controllo non è un'operazione valida. In questi casi, `CheckBox` può essere disabilitato impostando la relativa `IsEnabled` proprietà su `false` .

## <a name="checkbox-appearance"></a>Aspetto CheckBox

Oltre alle proprietà che `CheckBox` ereditano dalla [`View`](xref:Xamarin.Forms.View) classe, `CheckBox` definisce anche una `Color` proprietà che ne imposta il colore su un oggetto [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<CheckBox Color="Red" />
```

Gli screenshot seguenti mostrano una serie di oggetti controllati `CheckBox` , in cui ogni oggetto ha la `Color` proprietà impostata su un valore diverso [`Color`](xref:Xamarin.Forms.Color) :

![Screenshot delle caselle di controllo colorate, in iOS e Android](checkbox-images/checkbox-colors.png "Casella di controllo colorato")

## <a name="checkbox-visual-states"></a>Stati di visualizzazione casella di controllo

`CheckBox`dispone di un oggetto `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) che può essere utilizzato per avviare una modifica visiva a `CheckBox` quando viene controllata.

Nell'esempio di codice XAML riportato di seguito viene illustrato come definire uno stato di visualizzazione per lo `IsChecked` stato:

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

In questo esempio, `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) specifica che quando `CheckBox` è selezionato, la `Color` proprietà verrà impostata su verde. `Normal` `VisualState` Specifica che quando `CheckBox` è in uno stato normale, la relativa `Color` proprietà sarà impostata su rosso. Pertanto, l'effetto complessivo è che il `CheckBox` è rosso quando è vuoto e verde quando è selezionato.

Per ulteriori informazioni sugli stati visivi, vedere [ Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Collegamenti correlati

- [Demo CheckBox (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Xamarin.FormsTrigger](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.FormsGestione stato di visualizzazione](~/xamarin-forms/user-interface/visual-state-manager.md)
