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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305129"
---
# <a name="xamarinforms-triggers"></a>Trigger Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)

I trigger consentono di esprimere in modo dichiarativo in XAML le azioni che modificano l'aspetto dei controlli in base a eventi o modifiche delle proprietà. Inoltre, i trigger di stato, che sono un gruppo [`VisualState`](xref:Xamarin.Forms.VisualState) specializzato di trigger, definiscono quando un oggetto deve essere applicato.

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

### <a name="applying-a-trigger-using-a-style"></a>Applicazione di un trigger utilizzando uno stile

È anche possibile aggiungere trigger a una dichiarazione `Style` in un controllo, in una pagina o in un oggetto `ResourceDictionary` dell'applicazione. In questo esempio viene dichiarato uno stile `Key` implicito (ovvero no è `Entry` impostato) il che significa che verrà applicato a tutti i controlli nella pagina.

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

Oltre a specificare `Setter`s è anche possibile fornire [ `EnterActions` e `ExitActions` ](#enteractions-and-exitactions).

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

Si noti che i trigger di evento non supportano le raccolte `EnterActions` e `ExitActions`    [descritte di seguito](#enteractions-and-exitactions).

## <a name="multi-triggers"></a>Più trigger

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

La [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) raccolta viene utilizzata `IList` [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) per definire un oggetto che verrà richiamato quando viene soddisfatta la condizione del trigger. La [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) raccolta viene utilizzata `IList` `TriggerAction` per definire un oggetto che verrà richiamato dopo che la condizione di trigger non viene più soddisfatta.

> [!NOTE]
> Gli [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) oggetti definiti `EnterActions` `ExitActions` negli insiemi e vengono ignorati dalla [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) classe .    

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

I trigger di stato sono stati introdotti in Xamarin.Forms 4.5 e sono [`VisualState`](xref:Xamarin.Forms.VisualState) un gruppo specializzato di trigger che definiscono le condizioni in cui a deve essere applicato. Tuttavia, sono attualmente sperimentali e possono essere utilizzati solo aggiungendo la seguente riga di codice al file *di App.xaml.cs:*

```csharp
Device.SetFlags(new string[]{ "StateTriggers_Experimental" });
```

I trigger di stato [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) vengono [`VisualState`](xref:Xamarin.Forms.VisualState)aggiunti all'insieme di un oggetto . Questa raccolta può contenere un singolo trigger di stato o più trigger di stato. A [`VisualState`](xref:Xamarin.Forms.VisualState) verrà applicato quando sono attivi i trigger di stato nella raccolta.

Quando si utilizzano trigger di stato per controllare gli stati di visualizzazione, Xamarin.Forms utilizza le seguenti regole di precedenza per determinare quale trigger (e corrispondente) [`VisualState`](xref:Xamarin.Forms.VisualState)sarà attivo:

1. Qualsiasi trigger derivato [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)da .
1. Un [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) attivato a [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth) causa della condizione soddisfatta.
1. Un [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) attivato a [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) causa della condizione soddisfatta.

Se più trigger sono attivi contemporaneamente (ad esempio, due trigger personalizzati), il primo trigger dichiarato nel markup ha la precedenza.

> [!NOTE]
> I trigger di stato [`Style`](xref:Xamarin.Forms.Style)possono essere impostati in un oggetto o direttamente sugli elementi.

Per ulteriori informazioni sugli stati di visualizzazione, vedere [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

### <a name="state-trigger"></a>Trigger di stato

La [`StateTrigger`](xref:Xamarin.Forms.StateTrigger) classe , che [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) deriva dalla [`IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive) classe , dispone di una proprietà associabile. Un `StateTrigger` trigger [`VisualState`](xref:Xamarin.Forms.VisualState) a una `IsActive` modifica quando la proprietà cambia valore.

La [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe , che è la classe base [`IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive) per tutti [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) i trigger di stato, dispone di una proprietà e di un evento. Questo evento viene [`VisualState`](xref:Xamarin.Forms.VisualState) generato ogni volta che si verifica una modifica.

> [!IMPORTANT]
> La [`StateTrigger.IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive) proprietà associabile nasconde [`StateTriggerBase.IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive) la proprietà ereditata.

L'esempio XAML [`Style`](xref:Xamarin.Forms.Style) seguente [`StateTrigger`](xref:Xamarin.Forms.StateTrigger) mostra un che include oggetti:The following XAML example shows a that includes objects:

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

In questo esempio, [`Style`](xref:Xamarin.Forms.Style) [`Grid`](xref:Xamarin.Forms.Grid) gli oggetti di destinazione impliciti. Quando `IsToggled` la proprietà dell'oggetto associato è `true` `Grid` , il colore di sfondo dell'oggetto è impostato su nero. Quando `IsToggled` la proprietà dell'oggetto associato diventa `false`, viene attivata una [`VisualState`](xref:Xamarin.Forms.VisualState) modifica e il colore di sfondo dell'oggetto `Grid` diventa bianco.

Inoltre, ogni volta [`VisualState`](xref:Xamarin.Forms.VisualState) che si [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) verifica `VisualState` una modifica, viene generato l'evento per l'oggetto . Ogni `VisualState` ogni registra un gestore eventi per questo evento:Each registers an event handler for this event:

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

In questo esempio, quando [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) viene generato un gestore per [`VisualState`](xref:Xamarin.Forms.VisualState) l'evento, il gestore restituisce se l'oggetto è attivo o meno. Ad esempio, i seguenti messaggi vengono inviati alla `Checked` finestra della `Unchecked` console quando si passa dallo stato di visualizzazione allo stato di visualizzazione:For example, the following messages are output to the console window when changing from the visual state to the visual state:

```
Checked state active: False
Unchecked state active: True
```

> [!NOTE]
> I trigger di stato personalizzati possono [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) essere creati derivando dalla classe .

### <a name="adaptive-trigger"></a>Trigger adattivo

Un [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) trigger [`VisualState`](xref:Xamarin.Forms.VisualState) una modifica quando la finestra è un'altezza o larghezza specificata. Questo trigger ha due proprietà associabili:This trigger has two bindable properties:

- [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight), di `double`tipo , che indica l'altezza minima della finestra alla quale deve essere applicato l'oggetto [`VisualState`](xref:Xamarin.Forms.VisualState) .
- [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight), di `double`tipo , che indica la [`VisualState`](xref:Xamarin.Forms.VisualState) larghezza minima della finestra alla quale deve essere applicato l'oggetto .

> [!NOTE]
> Deriva [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) dalla [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe e può pertanto associare [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) un gestore eventi all'evento.

L'esempio XAML [`Style`](xref:Xamarin.Forms.Style) seguente [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) mostra un che include oggetti:The following XAML example shows a that includes objects:

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

In questo esempio, [`Style`](xref:Xamarin.Forms.Style) [`StackLayout`](xref:Xamarin.Forms.StackLayout) gli oggetti di destinazione impliciti. Quando la larghezza della finestra è compresa tra `StackLayout` 0 e `Style` 800 unità indipendenti dal dispositivo, gli oggetti a cui viene applicato avranno un orientamento verticale. Quando la larghezza della finestra è >di [`VisualState`](xref:Xamarin.Forms.VisualState) unità indipendenti dal dispositivo, la modifica viene attivata e l'orientamento `StackLayout` diventa orizzontale:

![Verticale StackLayout VisualState](triggers-images/adaptivetrigger-vertical.png "Esempio AdaptiveTrigger")
![Horizontal StackLayout VisualState](triggers-images/adaptivetrigger-horizontal.png "Esempio AdaptiveTrigger")

Le [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) proprietà e possono essere utilizzate in modo indipendente o in combinazione tra loro. Il codice XAML seguente mostra un esempio di impostazione di entrambe le proprietà:The following XAML shows an example of setting both properties:

```xaml
<AdaptiveTrigger MinWindowWidth="800"
                 MinWindowHeight="1200"/>
```

In questo esempio, [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) l'oggetto [`VisualState`](xref:Xamarin.Forms.VisualState) indica che il corrispondente verrà applicato quando la larghezza della finestra corrente è >unità indipendenti dal dispositivo e l'altezza della finestra corrente è >di unità indipendenti dal dispositivo.

### <a name="compare-state-trigger"></a>Attiva dello stato Confronta

L'oggetto [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) [`VisualState`](xref:Xamarin.Forms.VisualState) attiva una modifica quando una proprietà è uguale a un valore specifico. Questo trigger ha due proprietà associabili:This trigger has two bindable properties:

- [`Property`](xref:Xamarin.Forms.CompareStateTrigger.Property), di `object`tipo , che indica la proprietà confrontata dal trigger.
- [`Value`](xref:Xamarin.Forms.CompareStateTrigger.Value), di `object`tipo , che indica [`VisualState`](xref:Xamarin.Forms.VisualState) il valore in corrispondenza del quale deve essere applicato l'oggetto .

> [!NOTE]
> Deriva [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) dalla [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe e può pertanto associare [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) un gestore eventi all'evento.

L'esempio XAML [`Style`](xref:Xamarin.Forms.Style) seguente [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) mostra un che include oggetti:The following XAML example shows a that includes objects:

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

In questo esempio, [`Style`](xref:Xamarin.Forms.Style) [`Grid`](xref:Xamarin.Forms.Grid) gli oggetti di destinazione impliciti. Quando [`IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) la proprietà [`CheckBox`](xref:Xamarin.Forms.CheckBox) `false`di è , `Grid` il colore di sfondo dell'oggetto è impostato su bianco. Quando `CheckBox.IsChecked` la `true`proprietà [`VisualState`](xref:Xamarin.Forms.VisualState) diventa , viene attivata una `Grid` modifica e il colore di sfondo del diventa nero:

[![Screenshot di una modifica dello stato di visualizzazione attivata, in iOS e Android](triggers-images/comparestatetrigger-unchecked.png "Esempio Di compareStateTrigger")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "Esempio Di compareStateTrigger")
Screenshot di una modifica dello[![stato di visualizzazione attivata, su iOS e Android](triggers-images/comparestatetrigger-checked.png "Esempio Di compareStateTrigger")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "Esempio Di compareStateTrigger")

### <a name="device-state-trigger"></a>Trigger dello stato del dispositivo

L'operazione [`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger) [`VisualState`](xref:Xamarin.Forms.VisualState) attiva una modifica in base alla piattaforma del dispositivo su cui è in esecuzione l'app. Questo trigger ha una singola proprietà associabile:This trigger has a single bindable property:

- [`Device`](xref:Xamarin.Forms.DeviceStateTrigger.Device), di `string`tipo , che indica la [`VisualState`](xref:Xamarin.Forms.VisualState) piattaforma del dispositivo su cui deve essere applicato l'oggetto .

> [!NOTE]
> Deriva [`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger) dalla [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe e può pertanto associare [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) un gestore eventi all'evento.

L'esempio XAML [`Style`](xref:Xamarin.Forms.Style) seguente `DeviceStateTrigger` mostra un che include oggetti:The following XAML example shows a that includes objects:

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

In questo esempio, [`Style`](xref:Xamarin.Forms.Style) [`ContentPage`](xref:Xamarin.Forms.ContentPage) gli oggetti targets espliciti. `ContentPage`gli oggetti che consumano lo stile impostano il colore di sfondo su argento su iOS, al blu pallido su Android e all'acquamarina su UWP. The following screenshots show the resulting pages on iOS and Android:

[![Screenshot di una modifica dello stato di visualizzazione attivata, in iOS e Android](triggers-images/devicestatetrigger.png "Esempio DeviceStateTrigger")](triggers-images/devicestatetrigger-large.png#lightbox "Esempio DeviceStateTrigger")

### <a name="orientation-state-trigger"></a>Trigger dello stato di orientamento

L'oggetto [`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger) [`VisualState`](xref:Xamarin.Forms.VisualState) attiva una modifica quando cambia l'orientamento del dispositivo. Questo trigger ha una singola proprietà associabile:This trigger has a single bindable property:

- [`Orientation`](xref:Xamarin.Forms.OrientationStateTrigger.Orientation), di [`DeviceOrientation`](xref:Xamarin.Forms.Internals.DeviceOrientation)tipo , che indica [`VisualState`](xref:Xamarin.Forms.VisualState) l'orientamento a cui deve essere applicato l'oggetto .

> [!NOTE]
> Deriva [`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger) dalla [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe e può pertanto associare [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) un gestore eventi all'evento.

L'esempio XAML [`Style`](xref:Xamarin.Forms.Style) seguente `OrientationStateTrigger` mostra un che include oggetti:The following XAML example shows a that includes objects:

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

In questo esempio, [`Style`](xref:Xamarin.Forms.Style) [`ContentPage`](xref:Xamarin.Forms.ContentPage) gli oggetti targets espliciti. `ContentPage`gli oggetti che utilizzano lo stile impostano il colore di sfondo su argento quando l'orientamento è verticale e impostano il colore di sfondo su bianco quando l'orientamento è orizzontale.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di trigger](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)
- [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)
- [Xamarin.Forms Trigger API](xref:Xamarin.Forms.TriggerAction`1)
