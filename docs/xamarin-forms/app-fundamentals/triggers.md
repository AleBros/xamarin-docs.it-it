---
title: Trigger di xamarin. Forms
description: Questo articolo illustra come usare i trigger di xamarin. Forms per rispondere alle modifiche all'interfaccia con XAML. I trigger consentono di esprimere le azioni in modo dichiarativo in XAML che modificano l'aspetto dei controlli basato su eventi o modifiche alle proprietà.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: e9ec9288e2b8ea991ef8d41f9b601d0897631b9d
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675211"
---
# <a name="xamarinforms-triggers"></a>Trigger di xamarin. Forms

I trigger consentono di esprimere le azioni in modo dichiarativo in XAML che modificano l'aspetto dei controlli basato su eventi o modifiche alle proprietà.

È possibile assegnare un trigger direttamente a un controllo o aggiungerlo a un dizionario risorse a livello di pagina o a livello di app da applicare a più controlli.

Esistono quattro tipi di trigger:

* [Trigger di proprietà](#property) -si verifica quando una proprietà su un controllo è impostata su un particolare valore.

* [Trigger data](#data) : Usa i dati di associazione di trigger basato sulle proprietà di un altro controllo.

* [Trigger di evento](#event) -si verifica quando si verifica un evento del controllo.

* [Più Trigger](#multi) -consente a più condizioni di trigger da impostare prima che venga eseguita un'azione.

<a name="property" />

## <a name="property-triggers"></a>Trigger di proprietà

Un semplice trigger può essere espressa esclusivamente in XAML, aggiungere un `Trigger` elemento a un controllo Attiva collection.
Questo esempio viene illustrato un trigger che modifica un `Entry` quando riceve lo stato attivo al colore di sfondo:

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

* **TargetType** -tipo di controllo che il trigger viene applicato a.

* **Proprietà** -la proprietà del controllo che viene monitorato.

* **Valore** -il valore, quando si verifica per la proprietà monitorata, che attiva il trigger da attivare.

* **Setter** -una raccolta di `Setter` è possibile aggiungere elementi e quando viene soddisfatta la condizione del trigger. È necessario specificare il `Property` e `Value` da impostare.

* **EnterActions ed ExitActions** (non illustrato): vengono scritti nel codice e può essere usato oltre a (o anziché) `Setter` elementi. Si trovano [descritto di seguito](#enterexit).

### <a name="applying-a-trigger-using-a-style"></a>Applicazione di un Trigger utilizzando uno stile

È inoltre possibile aggiungere trigger per un `Style` dichiarazione in un controllo, in una pagina o un'applicazione `ResourceDictionary`. In questo esempio dichiara uno stile implicito (ie. Nessun `Key` è impostato) vale a dire che verranno applicate a tutti `Entry` controlli della pagina.

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

Trigger di dati utilizzano l'associazione dati a un altro controllo per fare in modo di monitorare il `Setter`s richiamato. Anziché il `Property` in un trigger di proprietà dell'attributo, impostare il `Binding` attributo per il monitoraggio per il valore specificato.

L'esempio seguente usa la sintassi di associazione dati `{Binding Source={x:Reference entry}, Path=Text.Length}`
come avviene in modo in cui si fa riferimento alle proprietà di un altro controllo. Quando la lunghezza del `entry` è uguale a zero, il trigger viene attivato. In questo esempio il trigger disabilita il pulsante quando l'input è vuota.

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

Suggerimento: quando si valuta `Path=Text.Length` sempre fornire un valore predefinito per la proprietà di destinazione (ad es. `Text=""`) perché in caso contrario sarà `null` e il trigger non funzionerà come previsto.

Oltre a specificare `Setter`s è anche possibile fornire [ `EnterActions` e `ExitActions` ](#enterexit).

<a name="event" />

## <a name="event-triggers"></a>Trigger di evento

Il `EventTrigger` elemento richiede solo un' `Event` proprietà, ad esempio `"Clicked"` nell'esempio seguente.

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Si noti che esistono nessun `Setter` elementi, ma piuttosto un riferimento a una classe definita dal `local:NumericValidationTriggerAction` che richiede il `xmlns:local` per essere dichiarata nella pagina del XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

Implementa la classe stessa `TriggerAction` vale a dire che deve fornire una sostituzione per il `Invoke` metodo che viene chiamato ogni volta che si verifica l'evento di attivazione.

Un'implementazione di azione di trigger deve:

* Implementare il modello generico `TriggerAction<T>` (classe), con il parametro generico corrispondente con il tipo di controllo verrà applicato per il trigger. È possibile usare come superclasse `VisualElement` azioni del trigger che funziona con un'ampia gamma di controlli o specificare un tipo di controllo, ad esempio scrivere `Entry`.

* Eseguire l'override di `Invoke` - questo viene chiamato ogni volta che vengono soddisfatti i criteri di trigger.

* Se lo si desidera esporre le proprietà che possono essere impostate nella finestra di XAML quando viene dichiarato il trigger (ad esempio `Anchor`, `Scale`, e `Length` in questo esempio).

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

Possono impostare le proprietà esposte dall'azione del trigger nella dichiarazione di XAML come segue:

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Prestare attenzione quando si condividono i trigger in un `ResourceDictionary`, un'istanza viene condivisa tra i controlli in modo che qualsiasi stato una volta configurato verrà applicata a tutti.

Si noti che i trigger di evento non supportano `EnterActions` e `ExitActions` [descritto di seguito](#enterexit).

<a name="multi" />

## <a name="multi-triggers"></a>Più trigger

Oggetto `MultiTrigger` ha un aspetto simile a un `Trigger` o `DataTrigger` ma possono essere presenti più di una condizione. Tutte le condizioni devono essere vere prima il `Setter`s vengono attivati.

Di seguito è riportato un esempio di un trigger per un pulsante che viene associata a due diversi input (`email` e `phone`):

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

Il `Conditions` può contenere anche dati raccolta `PropertyCondition` elementi simile al seguente:

```xaml
<PropertyCondition Property="Text" Value="OK" />
```

### <a name="building-a-require-all-multi-trigger"></a>Creazione di un trigger con più "richiedere tutto"

Il trigger di più Aggiorna relativo controllo solo quando tutte le condizioni sono vere. Test per "tutte le lunghezze di campo sono zero" (ad esempio una pagina di accesso in cui tutti gli input devono essere completati) è difficile perché si desidera che una condizione "dove Text.Length > 0", ma ciò non può essere espressi in XAML.

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
            return false;            // input is empty
    }

    public object ConvertBack(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        throw new NotSupportedException ();
    }
}
```

Per usare il convertitore in un trigger di più, aggiungerlo innanzitutto al dizionario risorse della pagina (insieme a un oggetto personalizzato `xmlns:local` definizione dello spazio dei nomi):

```xaml
<ResourceDictionary>
   <local:MultiTriggerConverter x:Key="dataHasBeenEntered" />
</ResourceDictionary>
```

il XAML è illustrato di seguito. Tenere presente le seguenti differenze del primo esempio di trigger più:

* Il pulsante presenta `IsEnabled="false"` impostato per impostazione predefinita.
* Le condizioni di attivazione con più usano il convertitore per attivare i `Text.Length` valore in un `boolean`.
* Quando tutte le condizioni vengono `true`, il setter rende il pulsante `IsEnabled` proprietà `true`.

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
Nella parte inferiore delle schermate, i **account di accesso** pulsante rimane inattivo fino a quando entrambi i campi contengono dati.

![](triggers-images/multi-requireall.png "MultiTrigger esempi")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions ed ExitActions

Un altro modo per implementare le modifiche quando si verifica un trigger è aggiungendo `EnterActions` e `ExitActions` raccolte e specificando `TriggerAction<T>` implementazioni.

È possibile fornire *entrambe* `EnterActions` e `ExitActions` nonché `Setter`s in un trigger, ma tenere presente che il `Setter`s viene chiamato immediatamente (non attendono il `EnterAction` o `ExitAction` a completamento). In alternativa è possibile eseguire tutti gli elementi nel codice e non usare `Setter`s affatto.

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

Come sempre, quando una classe viene fatto riferimento in XAML è necessario dichiarare uno spazio dei nomi, ad esempio `xmlns:local` come illustrato di seguito:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

Il `FadeTriggerAction` codice è illustrato di seguito:

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

Nota: `EnterActions` e `ExitActions` vengono ignorate **i trigger di evento**.



## <a name="related-links"></a>Collegamenti correlati

- [Esempio di trigger](https://developer.xamarin.com/samples/WorkingWithTriggers)
- [Documentazione di xamarin. Forms API](xref:Xamarin.Forms.TriggerAction`1)
