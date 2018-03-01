---
title: Trigger
description: Rispondere alle modifiche dell'interfaccia utente con XAML
ms.topic: article
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: abfea1dae2699f7d40f2e27285cf8dab3db9400c
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="triggers"></a>Trigger

Trigger consentono di esprimere azioni in modo dichiarativo in XAML che modificano l'aspetto dei controlli in base a eventi o modifiche alle proprietà.

È possibile assegnare un trigger direttamente a un controllo o aggiungerlo a un dizionario di risorse a livello di pagina o a livello di applicazione da applicare a più controlli.

Sono disponibili quattro tipi di trigger:

* [Trigger di proprietà](#property) -si verifica quando una proprietà in un controllo è impostata su un valore specifico.

* [Trigger data](#data) - viene utilizzata un'associazione dati a trigger in base alle proprietà di un altro controllo.

* [Trigger di evento](#event) -si verifica quando si verifica un evento del controllo.

* [Più Trigger](#multi) -consente di più condizioni di trigger da impostare prima che venga eseguita un'azione.

<a name="property" />

## <a name="property-triggers"></a>Trigger di proprietà

Un trigger semplice può essere espressa esclusivamente in XAML, aggiunta di un `Trigger` elemento a un controllo Attiva collection.
In questo esempio viene illustrato un trigger che modifica un `Entry` colore di sfondo quando riceve lo stato attivo:

```xaml
<Entry Placeholder="enter name">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
             Property="IsFocused" Value="True">
            <Setter Property="BackgroundColor" Value="Yellow" />
        </Trigger>
    </Entry.Triggers>
</Entry>
```

Le parti importanti della dichiarazione del trigger sono:

* **TargetType** -il tipo di controllo a cui si applica il trigger.

* **Proprietà** -la proprietà del controllo che viene monitorato.

* **Valore** -il valore, quando si verifica per la proprietà monitorata, che provoca l'attivazione del trigger.

* **Setter** -una raccolta di `Setter` è possibile aggiungere elementi e quando viene soddisfatta la condizione del trigger. È necessario specificare il `Property` e `Value` da impostare.

* **EnterActions ed ExitActions** (non illustrato) - sono scritti in codice e può essere utilizzato in oltre a (o anziché) `Setter` elementi. Sono [descritto di seguito](#enterexit).

### <a name="applying-a-trigger-using-a-style"></a>L'applicazione di un Trigger utilizzando uno stile

È inoltre possibile aggiungere trigger per un `Style` dichiarazione in un controllo, in una pagina o un'applicazione `ResourceDictionary`. In questo esempio dichiara uno stile implicito (ie. Nessun `Key` è impostato) il che significa che verrà applicate a tutti `Entry` controlli della pagina.

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style TargetType="Entry">
                        <Style.Triggers>
                <Trigger TargetType="Entry"
                         Property="IsFocused" Value="True">
                    <Setter Property="BackgroundColor" Value="Yellow" />
                </Trigger>
            </Style.Triggers>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>
```

<a name="data" />

## <a name="data-triggers"></a>Trigger Data

I trigger Data utilizzano l'associazione dati per il monitoraggio di un altro controllo affinché il `Setter`s ottenere chiamato. Anziché il `Property` attributo in un trigger di proprietà, impostare il `Binding` attributo per il monitoraggio per il valore specificato.

Nell'esempio seguente viene utilizzata la sintassi di associazione di dati `{Binding Source={x:Reference entry}, Path=Text.Length}` ovvero si riferisce alle proprietà del controllo. Quando la lunghezza del `entry` è zero, il trigger viene attivato. In questo esempio il trigger disabilita il pulsante quando l'input è vuota.

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
        </DataTrigger>
    </Button.Triggers>
</Button>
```

Suggerimento: durante la valutazione `Path=Text.Length` fornire sempre un valore predefinito per la proprietà di destinazione (ad es. `Text=""`) perché in caso contrario sarà `null` e il trigger non funzionerà come previsto.

Oltre a specificare `Setter`s è anche possibile fornire [ `EnterActions` e `ExitActions` ](#enterexit).

<a name="event" />

## <a name="event-triggers"></a>Trigger di evento

Il `EventTrigger` elemento richiede solo un `Event` proprietà, ad esempio `"Clicked"` nell'esempio seguente.

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Si noti che esistono non `Setter` gli elementi, ma piuttosto un riferimento a una classe definita dal `local:NumericValidationTriggerAction` che richiede il `xmlns:local` per essere dichiarata nella pagina del XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

Implementa la classe stessa `TriggerAction` vale a dire che è necessario fornire un override per il `Invoke` metodo che viene chiamato ogni volta che si verifica l'evento.

Un'implementazione di azione di trigger deve:

* Implementare il modello generico `TriggerAction<T>` (classe), con il parametro generico corrispondente con il tipo di controllo verrà applicato per il trigger. È possibile utilizzare, ad esempio superclassi `VisualElement` per scrivere le azioni dei trigger che supportano una varietà di controlli o specificare un tipo di controllo `Entry`.

* Eseguire l'override di `Invoke` - questo viene chiamato ogni volta che vengono soddisfatti i criteri di trigger.

* Se lo si desidera esporre le proprietà che è possibile impostare nel codice XAML quando il trigger viene dichiarato (ad esempio `Anchor`, `Scale`, e `Length` in questo esempio).

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

Possano impostare le proprietà esposte dall'azione del trigger nella dichiarazione XAML come segue:

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Prestare attenzione quando si condividono i trigger in un `ResourceDictionary`, un'istanza verrà condiviso tra i controlli in modo da qualsiasi stato una volta configurato verrà applicate a tutti.

Si noti che non supportano i trigger di evento `EnterActions` e `ExitActions` [descritto di seguito](#enterexit).   

<a name="multi" />

## <a name="multi-triggers"></a>Più trigger

Oggetto `MultiTrigger` è simile a un `Trigger` o `DataTrigger` ad eccezione del fatto può essere presente più di una condizione. Tutte le condizioni devono essere true prima di `Setter`s vengono attivati.

Di seguito è riportato un esempio di un trigger per un pulsante che associa due diversi input (`email` e `phone`):

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

Il `Conditions` raccolta può contenere anche `PropertyCondition` elementi come segue:

```xaml
<PropertyCondition Property="Text" Value="OK" />
```

### <a name="building-a-require-all-multi-trigger"></a>La creazione di un trigger di più "obbligatoria"

Il trigger di più Aggiorna solo il controllo quando tutte le condizioni sono true. Test per "tutte le lunghezze di campo sono zero" (ad esempio una pagina di accesso in cui tutti gli input devono essere completati) è un'operazione complesso, dal momento che una condizione "in cui Text.Length > 0", ma questo non può essere espressi in XAML.

Questa operazione può essere eseguita con un `IValueConverter`. Il codice del convertitore sotto le trasformazioni di `Text.Length` binding in un `bool` che indica se un campo è vuoto o non:


```csharp
public class MultiTriggerConverter : IValueConverter
{
    public object Convert(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        if ((int)value > 0) // length > 0 ?
            return true;            // some data has been entered
        else
            return false;           // input is empty
    }

    public object ConvertBack(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        throw new NotSupportedException ();
    }
}
```

Per usare il convertitore in un trigger di più, aggiungerlo innanzitutto al dizionario delle risorse della pagina (insieme a un oggetto personalizzato `xmlns:local` definizione dello spazio dei nomi):

```xaml
<ResourceDictionary>
   <local:MultiTriggerConverter x:Key="dataHasBeenEntered" />
</ResourceDictionary>
```

Il codice XAML viene mostrato di seguito. Tenere presente le differenze seguenti rispetto nel primo esempio di più trigger:

* Il pulsante ha `IsEnabled="false"` l'impostazione predefinita.
* Le condizioni di attivazione più usare il convertitore per attivare il `Text.Length` valore in un valore booleano.
* Quando tutte le condizioni sono `true`, il metodo di impostazione rende il pulsante `IsEnabled` proprietà `true`.

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

Questi screenshot mostrano la differenza tra i due esempi di trigger più. Nella parte superiore delle schermate, testo di input in uno solo `Entry` è sufficiente per abilitare il **salvare** pulsante.
Nella parte inferiore delle schermate, la **accesso** pulsante rimane inattivo fino a quando entrambi i campi contengono dati.


![](triggers-images/multi-requireall.png "Esempi multiTrigger")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions ed ExitActions

È possibile implementare le modifiche quando si verifica un trigger aggiungendo `EnterActions` e `ExitActions` raccolte e specificando `TriggerAction<T>` implementazioni.

È possibile fornire *entrambi* `EnterActions` e `ExitActions` nonché `Setter`s in un trigger, ma tenere presente che il `Setter`s vengono chiamati immediatamente (non attendere la `EnterAction` o `ExitAction` per completare). In alternativa è possibile eseguire tutti gli elementi nel codice e non utilizzare `Setter`s affatto.

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

Come sempre, quando una classe viene fatto riferimento in XAML, è necessario dichiarare uno spazio dei nomi, ad esempio `xmlns:local` come illustrato di seguito:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

Il `FadeTriggerAction` codice riportato di seguito:

```csharp
public class FadeTriggerAction : TriggerAction<VisualElement>
{
    public FadeTriggerAction() {}

    public int StartsFrom { set; get; }

    protected override void Invoke (VisualElement visual)
    {
            visual.Animate("", new Animation( (d)=>{
                var val = StartsFrom==1 ? d : 1-d;
                visual.BackgroundColor = Color.FromRgb(1, val, 1);

            }),
            length:1000, // milliseconds
            easing: Easing.Linear);
    }
}
```

Nota: `EnterActions` e `ExitActions` vengono ignorate **trigger di evento**.



## <a name="related-links"></a>Collegamenti correlati

- [Esempio di trigger](https://developer.xamarin.com/samples/WorkingWithTriggers)
- [Documentazione di xamarin. Forms API](https://developer.xamarin.com/api/type/Xamarin.Forms.TriggerAction%3CT%3E/)
