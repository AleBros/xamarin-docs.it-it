---
title: Trigger Xamarin.Forms
description: Questo articolo illustra come usare i trigger Xamarin.Forms per rispondere alle modifiche all'interfaccia utente con XAML. I trigger consentono di esprimere in modo dichiarativo in XAML le azioni che modificano l'aspetto dei controlli in base a eventi o modifiche delle proprietà.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: e9ec9288e2b8ea991ef8d41f9b601d0897631b9d
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675211"
---
# <a name="xamarinforms-triggers"></a>Trigger Xamarin.Forms

I trigger consentono di esprimere in modo dichiarativo in XAML le azioni che modificano l'aspetto dei controlli in base a eventi o modifiche delle proprietà.

È possibile assegnare un trigger direttamente a un controllo o aggiungerlo a un dizionario risorse a livello di pagina o a livello di app perché sia applicato a più controlli.

Esistono quattro tipi di trigger:

* [Trigger di proprietà](#property): si verifica quando una proprietà su un controllo è impostata su un particolare valore.

* [Trigger di dati](#data) : usa il data binding per eseguire il trigger sulla base delle proprietà di un altro controllo.

* [Trigger di evento](#event): si verifica quando un evento avviene su un controllo.

* [MultiTrigger](#multi): consente di impostare più condizioni di trigger prima che si verifichi un'azione.

<a name="property" />

## <a name="property-triggers"></a>Trigger di proprietà

È possibile esprimere un trigger semplice puramente in XAML aggiungendo un elemento `Trigger` a una raccolta di trigger del controllo.
Questo esempio illustra un trigger che modifica il colore di sfondo di un oggetto `Entry` quando riceve lo stato attivo:

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

Le parti importanti della dichiarazione del trigger sono le seguenti:

* **TargetType**: tipo di controllo a cui viene applicato il trigger.

* **Property**: proprietà del controllo sottoposta a monitoraggio.

* **Value**: se si verifica per la proprietà monitorata, valore che determina l'attivazione del trigger.

* **Setter**: è possibile aggiungere una raccolta di elementi `Setter` quando la condizione di trigger è soddisfatta. È necessario specificare gli oggetti `Property` e `Value` da impostare.

* **EnterActions ed ExitActions** (non illustrate): parti scritte nel codice che possono essere usate in aggiunta o al posto degli elementi `Setter`. Sono [descritte di seguito](#enterexit).

### <a name="applying-a-trigger-using-a-style"></a>Applicazione di un trigger usando lo stile

È anche possibile aggiungere trigger a una dichiarazione `Style` in un controllo, in una pagina o in un oggetto `ResourceDictionary` dell'applicazione. In questo esempio viene dichiarato uno stile implicito, vale a dire `Key` non è impostato. In questo caso sarà applicato a tutti i controlli `Entry` della pagina.

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
        </DataTrigger>
    </Button.Triggers>
</Button>
```

Suggerimento: quando viene valutato `Path=Text.Length`, specificare sempre un valore predefinito per la proprietà di destinazione, ad esempio `Text=""`). In caso contrario, il valore sarà `null` e il trigger non funzionerà come previsto.

Oltre a specificare elementi `Setter`, è anche possibile scrivere le parti [`EnterActions` e `ExitActions`](#enterexit).

<a name="event" />

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

* Implementare la classe `TriggerAction<T>` generica con il parametro generico corrispondente al tipo di controllo a cui verrà applicato il trigger. È possibile usare superclassi, ad esempio `VisualElement` per scrivere azioni trigger che funzionano con una serie di controlli, oppure specificare un controllo, ad esempio `Entry`.

* Eseguire l'override del metodo `Invoke`. Viene chiamato ogni volta che vengono soddisfatti i criteri di trigger.

* In alternativa, esporre le proprietà che possono essere impostata in XAML quando viene dichiarato il trigger. Nell'esempio sono `Anchor`, `Scale` e `Length`.

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

Le proprietà esposte dall'azione trigger possono essere impostate nella dichiarazione XAML nel modo seguente:

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Considerare che quando i trigger vengono condivisi in un oggetto `ResourceDictionary`, un'istanza viene condivisa tra i controlli, in modo che gli stati configurati una volta vengano applicati a tutti.

Si noti che i trigger di evento non supportano le raccolte `EnterActions` e `ExitActions`    [descritte di seguito](#enterexit).

<a name="multi" />

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

* Il pulsante ha `IsEnabled="false"` impostato per impostazione predefinita.
* Le condizioni MultiTrigger usano il convertitore per trasformare il valore `Text.Length` in un valore `boolean`.
* Quando tutte le condizioni sono `true`, l'elemento Setter imposta la proprietà `IsEnabled` del pulsante su `true`.

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

![](triggers-images/multi-requireall.png "Esempi di MultiTrigger")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions ed ExitActions

Un altro modo per implementare le modifiche quando si verifica un trigger consiste nell'aggiungere le raccolte `EnterActions` e `ExitActions` e specificare le implementazioni `TriggerAction<T>`.

In un trigger è possibile specificare *sia* `EnterActions` che `ExitActions` ma anche gli elementi `Setter`. Si consideri tuttavia che gli elementi `Setter` vengono chiamati immediatamente, non attendono che `EnterAction` o `ExitAction` siano completate. In alternativa è possibile eseguire tutti gli oggetti nel codice e non usare affatto gli elementi `Setter`.

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

Nota: `EnterActions` e `ExitActions` vengono ignorate nei  **trigger di evento**.



## <a name="related-links"></a>Collegamenti correlati

- [Esempio di trigger](https://developer.xamarin.com/samples/WorkingWithTriggers)
- [Documentazione per le API di Xamarin.Forms](xref:Xamarin.Forms.TriggerAction`1)
