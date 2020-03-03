---
title: Trigger Xamarin.Forms
description: Questo articolo illustra come usare i trigger Xamarin.Forms per rispondere alle modifiche all'interfaccia utente con XAML. I trigger consentono di esprimere in modo dichiarativo in XAML le azioni che modificano l'aspetto dei controlli in base a eventi o modifiche delle proprietà.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2020
ms.openlocfilehash: bf9c06dae0df7da1cc69a85d8436376494039959
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635704"
---
# <a name="xamarinforms-triggers"></a>Trigger Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)

I trigger consentono di esprimere in modo dichiarativo in XAML le azioni che modificano l'aspetto dei controlli in base a eventi o modifiche delle proprietà. Inoltre, i trigger di stato, un gruppo specializzato di trigger, definiscono quando è necessario applicare [`VisualState`](xref:Xamarin.Forms.VisualState).

È possibile assegnare un trigger direttamente a un controllo o aggiungerlo a un dizionario risorse a livello di pagina o a livello di app perché sia applicato a più controlli.

## <a name="property-triggers"></a>Trigger di proprietà

È possibile esprimere un trigger semplice puramente in XAML aggiungendo un elemento `Trigger` a una raccolta di trigger del controllo.
Questo esempio illustra un trigger che modifica il colore di sfondo di un oggetto `Entry` quando riceve lo stato attivo:

```xaml
<Entry Placeholder="enter name">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="IsFocused" Value="True">
            <Setter Property="BackgroundColor" Value="Yellow" />
            <!-- multiple Setters elements are allowed -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

Le parti importanti della dichiarazione del trigger sono le seguenti:

- **TargetType**: tipo di controllo a cui viene applicato il trigger.

- **Property**: proprietà del controllo sottoposta a monitoraggio.

- **Value**: se si verifica per la proprietà monitorata, valore che determina l'attivazione del trigger.

- **Setter**: è possibile aggiungere una raccolta di elementi `Setter` quando la condizione di trigger è soddisfatta. È necessario specificare gli oggetti `Property` e `Value` da impostare.

- **EnterActions ed ExitActions** (non illustrate): parti scritte nel codice che possono essere usate in aggiunta o al posto degli elementi `Setter`. Sono [descritte di seguito](#enteractions-and-exitactions).

### <a name="applying-a-trigger-using-a-style"></a>Applicazione di un trigger tramite uno stile

È anche possibile aggiungere trigger a una dichiarazione `Style` in un controllo, in una pagina o in un oggetto `ResourceDictionary` dell'applicazione. Questo esempio dichiara uno stile implicito, ovvero uno stile in cui non è impostato alcun elemento `Key`. Ciò significa che viene applicato a tutti i controlli `Entry` della pagina.

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style TargetType="Entry">
                        <Style.Triggers>
                <Trigger TargetType="Entry"
                         Property="IsFocused" Value="True">
                    <Setter Property="BackgroundColor" Value="Yellow" />
                    <!-- multiple Setters elements are allowed -->
                </Trigger>
            </Style.Triggers>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>
```

## <a name="data-triggers"></a>Trigger di dati

I trigger di dati usano il data binding per monitorare un altro controllo affinché esegua la chiamata di elementi `Setter`. Anziché impostare l'attributo `Property` in un trigger di proprietà, impostare l'attributo `Binding` per monitorare il valore specificato.

L'esempio seguente usa la sintassi del data binding `{Binding Source={x:Reference entry}, Path=Text.Length}`
che fa riferimento alle proprietà di un altro controllo. Quando la lunghezza di `entry` è zero, il trigger viene attivato. In questo esempio il trigger disabilita il pulsante quando l'input è vuoto.

```xaml
<!-- the x:Name is referenced below in DataTrigger-->
<!-- tip: make sure to set the Text="" (or some other default) -->
<Entry x:Name="entry"
       Text=""
       Placeholder="required field" />

<Button x:Name="button" Text="Save"
        FontSize="Large"
        HorizontalOptions="Center">
    <Button.Triggers>
        <DataTrigger TargetType="Button"
                     Binding="{Binding Source={x:Reference entry},
                                       Path=Text.Length}"
                     Value="0">
            <Setter Property="IsEnabled" Value="False" />
            <!-- multiple Setters elements are allowed -->
        </DataTrigger>
    </Button.Triggers>
</Button>
```

> [!TIP]
> Quando viene valutato `Path=Text.Length`, specificare sempre un valore predefinito per la proprietà di destinazione, ad esempio `Text=""`). In caso contrario, il valore sarà `null` e il trigger non funzionerà come previsto.

Oltre a specificare elementi `Setter`, è anche possibile scrivere le parti [`EnterActions` e `ExitActions`](#enteractions-and-exitactions).

## <a name="event-triggers"></a>Trigger di evento

L'elemento `EventTrigger` richiede solo una proprietà `Event`. Nell'esempio riportato di seguito è `"Clicked"`.

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Si noti che non esistono elementi `Setter`, ma piuttosto un riferimento a una classe definita da `local:NumericValidationTriggerAction` che richiede `xmlns:local` per essere dichiarata nel linguaggio XAML della pagina:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

Questa classe implementa `TriggerAction` che dovrà specificare un override per il metodo `Invoke` chiamato ogni volta che si verifica un evento di trigger.

L'implementazione di un'azione trigger deve eseguire le operazioni seguenti:

- Implementare la classe `TriggerAction<T>` generica con il parametro generico corrispondente al tipo di controllo a cui verrà applicato il trigger. È possibile usare superclassi, ad esempio `VisualElement` per scrivere azioni trigger che funzionano con una serie di controlli, oppure specificare un controllo, ad esempio `Entry`.

- Eseguire l'override del metodo `Invoke`. Viene chiamato ogni volta che vengono soddisfatti i criteri di trigger.

- Esporre facoltativamente le proprietà che possono essere impostate in XAML quando viene dichiarato il trigger. Per un esempio, vedere la classe `VisualElementPopTriggerAction` nell'applicazione di esempio associata.

```csharp
public class NumericValidationTriggerAction : TriggerAction<Entry>
{
    protected override void Invoke (Entry entry)
    {
        double result;
        bool isValid = Double.TryParse (entry.Text, out result);
        entry.TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

Il trigger di evento può quindi essere utilizzato da XAML:

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Considerare che quando i trigger vengono condivisi in un oggetto `ResourceDictionary`, un'istanza viene condivisa tra i controlli, in modo che gli stati configurati una volta vengano applicati a tutti.

Si noti che i trigger di evento non supportano le raccolte `EnterActions` e `ExitActions` [descritte di seguito](#enteractions-and-exitactions).

## <a name="multi-triggers"></a>MultiTrigger

L'oggetto `MultiTrigger` è simile a `Trigger` o `DataTrigger`, ma possono esistere più condizioni. È necessario che tutte le condizioni siano soddisfatte, perché gli elementi `Setter` siano attivati.

Di seguito viene riportato un esempio di un trigger per un pulsante che associa due input diversi (`email` e `phone`):

```xaml
<MultiTrigger TargetType="Button">
    <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference email},
                                   Path=Text.Length}"
                               Value="0" />
        <BindingCondition Binding="{Binding Source={x:Reference phone},
                                   Path=Text.Length}"
                               Value="0" />
    </MultiTrigger.Conditions>
    <Setter Property="IsEnabled" Value="False" />
    <!-- multiple Setter elements are allowed -->
</MultiTrigger>
```

La raccolta `Conditions` può anche contenere elementi `PropertyCondition` come nell'esempio seguente:

```xaml
<PropertyCondition Property="Text" Value="OK" />
```

### <a name="building-a-require-all-multi-trigger"></a>Associazione di un MultiTrigger con richiesta di "tutto"

L'elemento MultiTrigger aggiorna soltanto il relativo controllo quando tutte le condizioni vengono soddisfatte. Il test di "tutte le lunghezze dei campi sono zero", ad esempio in un pagina di accesso in cui tutti gli input devono essere completi, è un'operazione complessa, poiché si richiedere una condizione "dove Text.Length > 0" che non può tuttavia essere espressa in XAML.

È possibile invece usare un oggetto `IValueConverter`. Il codice del convertitore riportato di seguito trasforma il binding `Text.Length` in `bool`, il quale indica se un campo è vuoto o meno:

```csharp
public class MultiTriggerConverter : IValueConverter
{
    public object Convert(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        if ((int)value > 0) // length > 0 ?
            return true;            // some data has been entered
        else
            return false;            // input is empty
    }

    public object ConvertBack(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        throw new NotSupportedException ();
    }
}
```

Per usare il convertitore in un MultiTrigger, aggiungere prima l'elemento al dizionario risorse della pagina (insieme a una definizione dello spazio dei nomi `xmlns:local` personalizzato):

```xaml
<ResourceDictionary>
   <local:MultiTriggerConverter x:Key="dataHasBeenEntered" />
</ResourceDictionary>
```

Il linguaggio XAML è illustrato di seguito. Si notino le differenze rispetto al primo esempio MultiTrigger:

- Il pulsante ha `IsEnabled="false"` impostato per impostazione predefinita.
- Le condizioni MultiTrigger usano il convertitore per trasformare il valore `Text.Length` in un valore `boolean`.
- Quando tutte le condizioni sono `true`, l'elemento Setter imposta la proprietà `IsEnabled` del pulsante su `true`.

```xaml
<Entry x:Name="user" Text="" Placeholder="user name" />

<Entry x:Name="pwd" Text="" Placeholder="password" />

<Button x:Name="loginButton" Text="Login"
        FontSize="Large"
        HorizontalOptions="Center"
        IsEnabled="false">
  <Button.Triggers>
    <MultiTrigger TargetType="Button">
      <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference user},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
        <BindingCondition Binding="{Binding Source={x:Reference pwd},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
      </MultiTrigger.Conditions>
      <Setter Property="IsEnabled" Value="True" />
    </MultiTrigger>
  </Button.Triggers>
</Button>
```

Questi screenshot illustrano la differenza tra i due esempi di MultiTrigger illustrati sopra. Nella parte superiore delle schermate l'input di testo in un oggetto `Entry` è sufficiente per abilitare il pulsante **Save** (Salva).
Nella parte inferiore delle schermate il pulsante **Login** (Accesso) rimane inattivo finché nei due campi sono contenuti dati.

![](triggers-images/multi-requireall.png "MultiTrigger Examples")

## <a name="enteractions-and-exitactions"></a>EnterActions ed ExitActions

Un altro modo per implementare le modifiche quando si verifica un trigger consiste nell'aggiungere le raccolte `EnterActions` e `ExitActions` e specificare le implementazioni `TriggerAction<T>`.

La raccolta [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) viene usata per definire un `IList` di oggetti [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) che verranno richiamati quando viene soddisfatta la condizione del trigger. La raccolta [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) viene usata per definire un `IList` di oggetti `TriggerAction` che verranno richiamati quando la condizione del trigger non è più soddisfatta.

> [!NOTE]
> Gli oggetti [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) definiti nelle raccolte `EnterActions` e `ExitActions` vengono ignorati dalla classe [`EventTrigger`](xref:Xamarin.Forms.EventTrigger).    

In un trigger è possibile specificare *sia* `EnterActions` che `ExitActions`, ma anche elementi `Setter`. Si consideri tuttavia che gli elementi `Setter` vengono chiamati immediatamente e non attendono il completamento di `EnterAction` o `ExitAction`. In alternativa è possibile eseguire tutti gli oggetti nel codice e non usare affatto gli elementi `Setter`.

```xaml
<Entry Placeholder="enter job title">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="Entry.IsFocused" Value="True">
            <Trigger.EnterActions>
                <local:FadeTriggerAction StartsFrom="0"" />
            </Trigger.EnterActions>

            <Trigger.ExitActions>
                <local:FadeTriggerAction StartsFrom="1" />
            </Trigger.ExitActions>
            <!-- You can use both Enter/Exit and Setter together if required -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

Come sempre, quando si fa riferimento a una classe in XAML, è necessario dichiarare uno spazio dei nomi, ad esempio `xmlns:local`, come illustrato di seguito:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

Il codice `FadeTriggerAction` è illustrato di seguito:

```csharp
public class FadeTriggerAction : TriggerAction<VisualElement>
{
    public int StartsFrom { set; get; }

    protected override void Invoke(VisualElement sender)
    {
        sender.Animate("FadeTriggerAction", new Animation((d) =>
        {
            var val = StartsFrom == 1 ? d : 1 - d;
            // so i was aiming for a different color, but then i liked the pink :)
            sender.BackgroundColor = Color.FromRgb(1, val, 1);
        }),
        length: 1000, // milliseconds
        easing: Easing.Linear);
    }
}
```

## <a name="state-triggers"></a>Trigger di stato

I trigger di stato, introdotti in Xamarin.Forms 4.5, sono un gruppo specializzato di trigger che definiscono le condizioni in base alle quali è necessari applicare [`VisualState`](xref:Xamarin.Forms.VisualState). Attualmente, tuttavia, i trigger di stato sono sperimentali e possono essere usati solo aggiungendo al file *App.xaml.cs* la riga di codice seguente:

```csharp
Device.SetFlags(new string[]{ "StateTriggers_Experimental" });
```

I trigger di stato vengono aggiunti alla raccolta [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) di [`VisualState`](xref:Xamarin.Forms.VisualState). Questa raccolta può contenere un solo o più trigger di stato. [`VisualState`](xref:Xamarin.Forms.VisualState) viene applicato quando nella raccolta è attivo un qualsiasi trigger di stato.

Quando si usano trigger di stato per controllare lo stato degli oggetti visivi, per determinare quale trigger (e quale oggetto [`VisualState`](xref:Xamarin.Forms.VisualState) corrispondente) è attivo, Xamarin.Forms usa le regole di precedenza seguenti:

1. Qualsiasi trigger che derivi da [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase).
1. [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) attivato perché la condizione [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth) è soddisfatta.
1. [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) attivato perché la condizione [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) è soddisfatta.

Se sono attivi più trigger, ad esempio due trigger personalizzati, contemporaneamente, il primo trigger dichiarato nel markup ha la precedenza.

> [!NOTE]
> I trigger di stato possono essere impostati in [`Style`](xref:Xamarin.Forms.Style) o direttamente negli elementi.

Per altre informazioni sugli stati degli oggetti visivi, vedere [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

### <a name="state-trigger"></a>Trigger di stato

La classe [`StateTrigger`](xref:Xamarin.Forms.StateTrigger), che deriva dalla classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase), ha una proprietà [`IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive) associabile. `StateTrigger` attiva una modifica di [`VisualState`](xref:Xamarin.Forms.VisualState) quando la proprietà `IsActive` cambia valore.

La classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase), che rappresenta la classe di base per tutti i trigger di stato, ha una proprietà [`IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive) e un evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged). Questo evento viene generato ogni volta che si verifica una modifica di [`VisualState`](xref:Xamarin.Forms.VisualState).

> [!IMPORTANT]
> La proprietà associabile [`StateTrigger.IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive) nasconde la proprietà [`StateTriggerBase.IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive) ereditata.

L'esempio di codice XAML seguente illustra un elemento [`Style`](xref:Xamarin.Forms.Style) che include oggetti [`StateTrigger`](xref:Xamarin.Forms.StateTrigger):

```xaml
<Style TargetType="Grid">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Checked">
                    <VisualState.StateTriggers>
                        <StateTrigger IsActive="{Binding IsToggled}"
                                      IsActiveChanged="OnCheckedStateIsActiveChanged" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Black" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Unchecked">
                    <VisualState.StateTriggers>
                        <StateTrigger IsActive="{Binding IsToggled, Converter={StaticResource inverseBooleanConverter}}"
                                      IsActiveChanged="OnUncheckedStateIsActiveChanged" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

In questo esempio, l'elemento [`Style`](xref:Xamarin.Forms.Style) implicito ha come destinazioni oggetti [`Grid`](xref:Xamarin.Forms.Grid). Quando la proprietà `IsToggled` dell'oggetto associato corrisponde a `true`, il colore di sfondo di `Grid` viene impostato sul nero. Quando la proprietà `IsToggled` dell'oggetto associato diventa `false`, viene attivata una modifica di [`VisualState`](xref:Xamarin.Forms.VisualState) e il colore di sfondo di `Grid` diventa bianco.

Inoltre, ogni volta che si verifica una modifica di [`VisualState`](xref:Xamarin.Forms.VisualState), viene generato l'evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) per `VisualState`. Ogni `VisualState` registra un gestore per questo evento:

```csharp
void OnCheckedStateIsActiveChanged(object sender, EventArgs e)
{
    StateTriggerBase stateTrigger = sender as StateTriggerBase;
    Console.WriteLine($"Checked state active: {stateTrigger.IsActive}");
}

void OnUncheckedStateIsActiveChanged(object sender, EventArgs e)
{
    StateTriggerBase stateTrigger = sender as StateTriggerBase;
    Console.WriteLine($"Unchecked state active: {stateTrigger.IsActive}");
}
```

In questo esempio, quando viene attivato un gestore per l'evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged), il gestore genera come output se [`VisualState`](xref:Xamarin.Forms.VisualState) è attivo o meno. Quando si passa dallo stato di visualizzazione `Checked` allo stato di visualizzazione `Unchecked`, ad esempio, nella finestra della console vengono visualizzati i messaggi seguenti:

```
Checked state active: False
Unchecked state active: True
```

> [!NOTE]
> È possibile creare trigger di stato personalizzati derivandoli dalla classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase).

### <a name="adaptive-trigger"></a>Trigger adattivi

[`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) attiva una modifica di [`VisualState`](xref:Xamarin.Forms.VisualState) quando la finestra assume l'altezza o la larghezza specificata. Questo trigger ha due proprietà associabili:

- [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight), di tipo `double`, che indica l'altezza minima della finestra in corrispondenza della quale è necessario applicare [`VisualState`](xref:Xamarin.Forms.VisualState).
- [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight), di tipo `double`, che indica la larghezza minima della finestra in corrispondenza della quale è necessario applicare [`VisualState`](xref:Xamarin.Forms.VisualState).

> [!NOTE]
> [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) deriva dalla classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) e può quindi allegare un gestore all'evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged).

L'esempio di codice XAML seguente illustra un elemento [`Style`](xref:Xamarin.Forms.Style) che include oggetti [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger):

```xaml
<Style TargetType="StackLayout">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Vertical">
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="0" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="Orientation"
                                Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Horizontal">
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="800" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="Orientation"
                                Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

In questo esempio, l'elemento [`Style`](xref:Xamarin.Forms.Style) implicito ha come destinazioni oggetti [`StackLayout`](xref:Xamarin.Forms.StackLayout). Quando la larghezza della finestra è compresa tra 0 e 800 unità indipendenti dal dispositivo, gli oggetti `StackLayout` a cui è applicato `Style` hanno un orientamento verticale. Quando la larghezza della finestra è >= 800 unità indipendenti dal dispositivo, viene attivata la modifica di [`VisualState`](xref:Xamarin.Forms.VisualState) e l'orientamento di `StackLayout` passa a orizzontale:

![VisualState verticale in StackLayout](triggers-images/adaptivetrigger-vertical.png "Esempio di AdaptiveTrigger")
![VisualState orizzontale in StackLayout](triggers-images/adaptivetrigger-horizontal.png "Esempio di AdaptiveTrigger")

Le proprietà [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) e [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) possono essere usate in modo indipendente o in combinazione. Il codice XAML seguente illustra un esempio di impostazione di entrambe le proprietà:

```xaml
<AdaptiveTrigger MinWindowWidth="800"
                 MinWindowHeight="1200"/>
```

In questo esempio, [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) indica che l'elemento [`VisualState`](xref:Xamarin.Forms.VisualState) corrispondente viene applicato quando la larghezza della finestra corrente è >= 800 unità indipendenti dal dispositivo e l'altezza della finestra corrente è >= 1200 unità indipendenti dal dispositivo.

### <a name="compare-state-trigger"></a>Trigger di stato di confronto

[`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) attiva una modifica di [`VisualState`](xref:Xamarin.Forms.VisualState) quando una proprietà è uguale a un valore specifico. Questo trigger ha due proprietà associabili:

- [`Property`](xref:Xamarin.Forms.CompareStateTrigger.Property), di tipo `object`, che indica la proprietà sottoposta a confronto dal trigger.
- [`Value`](xref:Xamarin.Forms.CompareStateTrigger.Value), di tipo `object`, che indica il valore in corrispondenza del quale è necessario applicare [`VisualState`](xref:Xamarin.Forms.VisualState).

> [!NOTE]
> [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) deriva dalla classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) e può quindi allegare un gestore all'evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged).

L'esempio di codice XAML seguente illustra un elemento [`Style`](xref:Xamarin.Forms.Style) che include oggetti [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger):

```xaml
<Style TargetType="Grid">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Checked">
                    <VisualState.StateTriggers>
                        <CompareStateTrigger Property="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                                             Value="True" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Black" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Unchecked">
                    <VisualState.StateTriggers>
                        <CompareStateTrigger Property="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                                             Value="False" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
...
<Grid>
    <Frame BackgroundColor="White"
           CornerRadius="12"
           Margin="24"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <StackLayout Orientation="Horizontal">
            <CheckBox x:Name="checkBox"
                      VerticalOptions="Center" />
            <Label Text="Check the CheckBox to modify the Grid background color."
                   VerticalOptions="Center" />
        </StackLayout>
    </Frame>
</Grid>
```

In questo esempio, l'elemento [`Style`](xref:Xamarin.Forms.Style) implicito ha come destinazioni oggetti [`Grid`](xref:Xamarin.Forms.Grid). Quando la proprietà [`IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) di [`CheckBox`](xref:Xamarin.Forms.CheckBox) è `false`, il colore di sfondo di `Grid` viene impostato sul bianco. Quando la proprietà `CheckBox.IsChecked` diventa `true`, viene attivata una modifica di [`VisualState`](xref:Xamarin.Forms.VisualState) e il colore di sfondo di `Grid` diventa nero:

[![Screenshot di un cambiamento attivato dello stato dell'oggetto visivo, in iOS e Android](triggers-images/comparestatetrigger-unchecked.png "Esempio di CompareStateTrigger")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "Esempio di CompareStateTrigger")
[![Screenshot di un cambiamento attivato dello stato dell'oggetto visivo, in iOS e Android](triggers-images/comparestatetrigger-checked.png "Esempio di CompareStateTrigger")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "Esempio di CompareStateTrigger")

### <a name="device-state-trigger"></a>Trigger di stato del dispositivo

[`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger) attiva una modifica di [`VisualState`](xref:Xamarin.Forms.VisualState) in base alla piattaforma del dispositivo in cui è in esecuzione l'app. Questo trigger ha un'unica proprietà associabile:

- [`Device`](xref:Xamarin.Forms.DeviceStateTrigger.Device), di tipo `string`, che indica la piattaforma del dispositivo in cui è necessario applicare [`VisualState`](xref:Xamarin.Forms.VisualState).

> [!NOTE]
> [`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger) deriva dalla classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) e può quindi allegare un gestore all'evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged).

L'esempio di codice XAML seguente illustra un elemento [`Style`](xref:Xamarin.Forms.Style) che include oggetti `DeviceStateTrigger`:

```xaml
<Style x:Key="DeviceStateTriggerPageStyle"
       TargetType="ContentPage">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="iOS">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="iOS" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Silver" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Android">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="Android" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="#2196F3" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="UWP">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="UWP" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Aquamarine" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

In questo esempio, l'elemento [`Style`](xref:Xamarin.Forms.Style) esplicito ha come destinazioni oggetti [`ContentPage`](xref:Xamarin.Forms.ContentPage). Oggetti `ContentPage` che utilizzano lo stile impostano il proprio colore di sfondo su argento in iOS, su azzurro chiaro in Android e su acquamarina nella piattaforma UWP. Gli screenshot seguenti visualizzano le pagine finali in iOS e Android:

[![Screenshot dello stato della modifica attivata di un oggetto visivo in iOS e Android](triggers-images/devicestatetrigger.png "Esempio di DeviceStateTrigger")](triggers-images/devicestatetrigger-large.png#lightbox "Esempio di DeviceStateTrigger")

### <a name="orientation-state-trigger"></a>Trigger di stato dell'orientamento

[`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger) attiva una modifica di [`VisualState`](xref:Xamarin.Forms.VisualState) quando l'orientamento del dispositivo cambia. Questo trigger ha un'unica proprietà associabile:

- [`Orientation`](xref:Xamarin.Forms.OrientationStateTrigger.Orientation), di tipo [`DeviceOrientation`](xref:Xamarin.Forms.Internals.DeviceOrientation), che indica l'orientamento a cui è necessario applicare [`VisualState`](xref:Xamarin.Forms.VisualState).

> [!NOTE]
> [`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger) deriva dalla classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) e può quindi allegare un gestore all'evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged).

L'esempio di codice XAML seguente illustra un elemento [`Style`](xref:Xamarin.Forms.Style) che include oggetti `OrientationStateTrigger`:

```xaml
<Style x:Key="OrientationStateTriggerPageStyle"
       TargetType="ContentPage">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Portrait">
                    <VisualState.StateTriggers>
                        <OrientationStateTrigger Orientation="Portrait" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Silver" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Landscape">
                    <VisualState.StateTriggers>
                        <OrientationStateTrigger Orientation="Landscape" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

In questo esempio, l'elemento [`Style`](xref:Xamarin.Forms.Style) esplicito ha come destinazioni oggetti [`ContentPage`](xref:Xamarin.Forms.ContentPage). Gli oggetti `ContentPage` che utilizzano lo stile impostano il proprio colore di sfondo su argento quando l'orientamento è verticale e impostano il colore di sfondo su bianco quando l'orientamento è orizzontale.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di trigger](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)
- [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)
- [API trigger di Xamarin.Forms](xref:Xamarin.Forms.TriggerAction`1)
