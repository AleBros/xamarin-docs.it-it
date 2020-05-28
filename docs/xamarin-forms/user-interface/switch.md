---
title: Xamarin.FormsCommutatore
description: L' Xamarin.Forms opzione è un tipo di pulsante che può essere modificato dall'utente per passare da uno stato all'altro e viceversa. Questo articolo illustra come usare la classe Switch per visualizzare un elemento dell'interfaccia utente di attivazione/disattivazione.
ms.prod: ''
ms.assetId: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a5c2583b7632acdfa7d8439dc96b3964fa3cfcab
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136240"
---
# <a name="xamarinforms-switch"></a>Xamarin.FormsCommutatore

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

Il Xamarin.Forms [`Switch`](xref:Xamarin.Forms.Switch) controllo è un interruttore orizzontale che può essere modificato dall'utente per passare da uno stato all'altro e viceversa, rappresentato da un `boolean` valore. La `Switch` classe eredita da [`View`](xref:Xamarin.Forms.View) .

Gli screenshot seguenti mostrano un `Switch` controllo negli Stati di **on** attivazione e **disattivazione** in iOS e Android:

![Screenshot dei commutatori in stato on e off, in iOS e Android](switch-images/switch-states-default.png "Switch in iOS e Android")

Il `Switch` controllo definisce le proprietà seguenti:

* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)`boolean`valore che indica se l'oggetto `Switch` è **on**.
* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor)è un oggetto `Color` che influiscono sulla modalità `Switch` di rendering dell'oggetto nello stato **on**attivato o disattivato.
* `ThumbColor`è l'oggetto `Color` del cursore del cambio.

Queste proprietà sono supportate da un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetto, il che significa che è `Switch` possibile applicare uno stile a ed essere la destinazione delle associazioni dati.

Il `Switch` controllo definisce un `Toggled` evento generato quando la proprietà viene `IsToggled` modificata, tramite la manipolazione dell'utente o quando un'applicazione imposta la `IsToggled` Proprietà. L' `ToggledEventArgs` oggetto che accompagna l' `Toggled` evento ha una singola proprietà denominata `Value` , di tipo `bool` . Quando viene generato l'evento, il valore della `Value` proprietà riflette il nuovo valore della `IsToggled` Proprietà.

## <a name="create-a-switch"></a>Creare un'opzione

`Switch`È possibile creare un'istanza di un oggetto in XAML. La `IsToggled` proprietà può essere impostata in modo da abilitare o disabilitare `Switch` . Per impostazione predefinita, la `IsToggled` proprietà è `false` . Nell'esempio seguente viene illustrato come creare un'istanza di `Switch` in XAML con il `IsToggled` set di proprietà facoltativo:

```xaml
<Switch IsToggled="true"/>
```

Un oggetto `Switch` può essere creato anche nel codice:

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>Cambia aspetto

Oltre alle proprietà che [`Switch`](xref:Xamarin.Forms.Switch) ereditano dalla [`View`](xref:Xamarin.Forms.View) classe, `Switch` definisce anche le `OnColor` proprietà e `ThumbColor` . La `OnColor` proprietà può essere impostata in modo da definire il `Switch` colore quando viene attivata o disattivata allo stato **on** e la `ThumbColor` proprietà può essere impostata in modo da definire l'oggetto `Color` del cursore del cambio. Nell'esempio seguente viene illustrato come creare un'istanza di `Switch` in XAML con queste proprietà impostate:

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

Le proprietà possono essere impostate anche durante la creazione `Switch` di un nel codice:

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

Lo screenshot seguente Mostra gli `Switch` Stati di **on** attivazione e **disattivazione** degli interruttori, con le `OnColor` `ThumbColor` proprietà e impostate:

![Screenshot dei commutatori in stato on e off, in iOS e Android](switch-images/switch-states-colors.png "Switch in iOS e Android")

## <a name="respond-to-a-switch-state-change"></a>Risposta a una modifica dello stato del commutatore

Quando la `IsToggled` proprietà viene modificata, tramite la manipolazione dell'utente o quando un'applicazione imposta la `IsToggled` proprietà, viene `Toggled` generato l'evento. Un gestore eventi per questo evento può essere registrato per rispondere alla modifica:

```xaml
<Switch Toggled="OnToggled" />
```

Il file code-behind contiene il gestore per l' `Toggled` evento:

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

L' `sender` argomento nel gestore eventi è responsabile dell' `Switch` attivazione di questo evento. È possibile usare la `sender` proprietà per accedere all' `Switch` oggetto o per distinguere tra più `Switch` oggetti che condividono lo stesso `Toggled` gestore eventi.

Il `Toggled` gestore eventi può anche essere assegnato nel codice:

```csharp
Switch switchControl = new Switch {...};
switchControl.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>Associare i dati a un'opzione

Il `Toggled` gestore eventi può essere eliminato utilizzando Data Binding e trigger per rispondere a uno `Switch` stato di attivazione o modifica.

```xaml
<Switch x:Name="styleSwitch" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference styleSwitch}, Path=IsToggled}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

In questo esempio, [`Label`](xref:Xamarin.Forms.Label) Usa un'espressione di associazione in un oggetto `DataTrigger` per monitorare la `IsToggled` proprietà della classe `Switch` denominata `styleSwitch` . Quando questa proprietà diventa `true` , le `FontAttributes` `FontSize` proprietà e dell'oggetto `Label` vengono modificate. Quando la `IsToggled` proprietà restituisce `false` , le `FontAttributes` proprietà e `FontSize` dell'oggetto `Label` vengono reimpostate sullo stato iniziale.

Per informazioni sui trigger, vedere [ Xamarin.Forms trigger](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-switch"></a>Disabilitare un'opzione

Un'applicazione può entrare in uno stato in cui l' `Switch` elemento attivato o disattivato non è un'operazione valida. In questi casi, `Switch` può essere disabilitato impostando la relativa `IsEnabled` proprietà su `false` . In questo modo gli utenti non potranno modificare il `Switch` .

## <a name="related-links"></a>Collegamenti correlati

* [Demo switch](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
* [Xamarin.FormsTrigger](~/xamarin-forms/app-fundamentals/triggers.md)
