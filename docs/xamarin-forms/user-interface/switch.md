---
title: Opzione Novell. Forms
description: L'opzione Novell. Forms è un tipo di pulsante che può essere modificato dall'utente per passare da uno stato all'altro. Questo articolo illustra come usare la classe Switch per visualizzare un elemento dell'interfaccia utente di attivazione/disattivazione.
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/18/2019
ms.openlocfilehash: 88655aabdbd32db63aaf3330a18b0ad8105ea26c
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "78291788"
---
# <a name="xamarinforms-switch"></a>Opzione Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

Il controllo [`Switch`](xref:Xamarin.Forms.Switch) Novell. Forms è un interruttore orizzontale che può essere modificato dall'utente per passare da uno stato all'altro, rappresentato da un valore `boolean`. La classe `Switch` eredita da [`View`](xref:Xamarin.Forms.View).

Gli screenshot seguenti mostrano un controllo `Switch` negli **Stati di attivazione** e **disattivazione** in iOS e Android:

![Screenshot dei commutatori in stato on e off, in iOS e Android](switch-images/switch-states-default.png "Switch in iOS e Android")

Il controllo `Switch` definisce le proprietà seguenti:

* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) è un valore `boolean` che indica se il `Switch` è **on**.
* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor) è un `Color` che influiscono sulla modalità di rendering del `Switch` nello stato **attivato o disattivato**.
* `ThumbColor` è l'`Color` del Thumb del cambio.

Queste proprietà sono supportate da un oggetto [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che è possibile applicare uno stile ai `Switch` ed essere la destinazione delle associazioni dati.

Il controllo `Switch` definisce un evento `Toggled` generato quando la proprietà `IsToggled` viene modificata, tramite la manipolazione dell'utente o quando un'applicazione imposta la proprietà `IsToggled`. L'oggetto `ToggledEventArgs` che accompagna l'evento `Toggled` dispone di una singola proprietà denominata `Value`, di tipo `bool`. Quando viene generato l'evento, il valore della proprietà `Value` riflette il nuovo valore della proprietà `IsToggled`.

## <a name="create-a-switch"></a>Creare un'opzione

È possibile creare un'istanza di un `Switch` in XAML. È possibile impostare la relativa proprietà `IsToggled` per abilitare o disabilitare l'`Switch`. Per impostazione predefinita, la proprietà `IsToggled` è `false`. Nell'esempio seguente viene illustrato come creare un'istanza di un `Switch` in XAML con il set di proprietà `IsToggled` facoltativo:

```xaml
<Switch IsToggled="true"/>
```

Nel codice è anche possibile creare un `Switch`:

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>Cambia aspetto

Oltre alle proprietà che [`Switch`](xref:Xamarin.Forms.Switch) eredita dalla classe [`View`](xref:Xamarin.Forms.View) , `Switch` definisce anche le proprietà `OnColor` e `ThumbColor`. È possibile impostare la proprietà `OnColor` per definire il colore `Switch` quando viene attivata o disattivata sullo stato **on** e la proprietà `ThumbColor` può essere impostata in modo da definire il `Color` del Thumb di cambio. Nell'esempio seguente viene illustrato come creare un'istanza di un `Switch` in XAML con queste proprietà impostate:

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

Le proprietà possono essere impostate anche durante la creazione di una `Switch` nel codice:

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

Lo screenshot seguente mostra il `Switch` negli Stati **di attivazione e** **disattivazione** , con le proprietà `OnColor` e `ThumbColor` impostate:

![Screenshot dei commutatori in stato on e off, in iOS e Android](switch-images/switch-states-colors.png "Switch in iOS e Android")

## <a name="respond-to-a-switch-state-change"></a>Risposta a una modifica dello stato del commutatore

Quando la proprietà `IsToggled` viene modificata, tramite la manipolazione dell'utente o quando un'applicazione imposta la proprietà `IsToggled`, viene generato l'evento `Toggled`. Un gestore eventi per questo evento può essere registrato per rispondere alla modifica:

```xaml
<Switch Toggled="OnToggled" />
```

Il file code-behind contiene il gestore per l'evento `Toggled`:

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

L'argomento `sender` nel gestore eventi è il `Switch` responsabile dell'attivazione di questo evento. È possibile utilizzare la proprietà `sender` per accedere all'oggetto `Switch` o per distinguere tra più oggetti `Switch` che condividono lo stesso gestore eventi `Toggled`.

Il gestore dell'evento `Toggled` può anche essere assegnato nel codice:

```csharp
Switch switchControl = new Switch {...};
switchControl.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>Associare i dati a un'opzione

Il gestore dell'evento `Toggled` può essere eliminato utilizzando data binding e trigger per rispondere a un `Switch` modificare gli Stati di attivazione/disattivazione.

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

In questo esempio, il [`Label`](xref:Xamarin.Forms.Label) utilizza un'espressione di associazione in un `DataTrigger` per monitorare la proprietà `IsToggled` della `Switch` denominata `styleSwitch`. Quando questa proprietà diventa `true`, le proprietà `FontAttributes` e `FontSize` del `Label` vengono modificate. Quando la proprietà `IsToggled` restituisce `false`, le proprietà `FontAttributes` e `FontSize` del `Label` vengono reimpostate sullo stato iniziale.

Per informazioni sui trigger, vedere [trigger Novell. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-switch"></a>Disabilitare un'opzione

È possibile che un'applicazione entri in uno stato in cui la `Switch` attivata o disattivata non sia un'operazione valida. In questi casi, è possibile disabilitare il `Switch` impostando la relativa proprietà `IsEnabled` su `false`. In questo modo gli utenti non potranno modificare la `Switch`.

## <a name="related-links"></a>Collegamenti correlati

* [Demo switch](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
* [Trigger di Novell. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
