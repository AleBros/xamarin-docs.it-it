---
title: Opzione Novell. Forms
description: L'opzione Novell. Forms è un tipo di pulsante che può essere modificato dall'utente per passare da uno stato all'altro. Questo articolo illustra come usare la classe Switch per visualizzare un elemento dell'interfaccia utente di attivazione/disattivazione.
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/18/2019
ms.openlocfilehash: 1f2ef838287e32df5df42f73e4b43816d618552d
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887872"
---
# <a name="xamarinforms-switch"></a>Opzione Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

Il controllo Novell. [`Switch`](xref:Xamarin.Forms.Switch) Forms è un interruttore orizzontale che può essere modificato dall'utente per passare da uno stato all'altro e viceversa, rappresentato da un `boolean` valore. La `Switch` classe eredita da [`View`](xref:Xamarin.Forms.View).

Gli screenshot seguenti mostrano un `Switch` controllo negli Stati di attivazione e disattivazione in iOS e Android:

![Screenshot dei commutatori in stato on e off, in iOS e Android](switch-images/switch-states-default.png "Switch in iOS e Android")

Il `Switch` controllo definisce due proprietà:

* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)valore che indica se l'oggetto `Switch` è **on**. `boolean`
* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor)è un `Color` oggetto che influiscono `Switch` sulla modalità di rendering dell'oggetto nello statoattivato o disattivato.
* `ThumbColor`è l' `Color` oggetto del cursore del cambio.

Queste proprietà sono supportate da un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetto, il `Switch` che significa che è possibile applicare uno stile a ed essere la destinazione delle associazioni dati.

Il `Switch` controllo definisce un `Toggled` evento generato quando la `IsToggled` proprietà viene modificata, tramite la manipolazione dell'utente o quando un'applicazione imposta la `IsToggled` proprietà. L' `ToggledEventArgs` oggetto che accompagna l' `Toggled` evento ha una singola proprietà denominata `Value`, di tipo `bool`. Quando viene generato l'evento, il valore della `Value` proprietà riflette il nuovo valore `IsToggled` della proprietà.

## <a name="create-a-switch"></a>Creare un'opzione

È `Switch` possibile creare un'istanza di un oggetto in XAML. La proprietà può essere impostata in modo da abilitare `Switch`o disabilitare. `IsToggled` Per impostazione predefinita, `IsToggled` la proprietà `false`è. Nell'esempio seguente viene illustrato come creare un'istanza `Switch` di in XAML con il `IsToggled` set di proprietà facoltativo:

```xaml
<Switch IsToggled="true"/>
```

Un `Switch` oggetto può essere creato anche nel codice:

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>Cambia aspetto

Oltre alle proprietà che [`Switch`](xref:Xamarin.Forms.Switch) ereditano [`View`](xref:Xamarin.Forms.View) dalla classe, `Switch` definisce `OnColor` anche le proprietà e `ThumbColor` . La `OnColor` proprietà può essere impostata in modo da `Switch` definire il colore quando viene attivata o disattivata allo stato **on** e la `ThumbColor` proprietà può essere impostata in `Color` modo da definire l'oggetto del cursore del cambio. Nell'esempio seguente viene illustrato come creare un'istanza `Switch` di in XAML con queste proprietà impostate:

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

Le proprietà possono essere impostate anche durante la creazione `Switch` di un nel codice:

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

Lo screenshot seguente Mostra gli `Switch` stati di attivazione e disattivazione degli interruttori, `OnColor` con `ThumbColor` le proprietà e impostate:

![Screenshot dei commutatori in stato on e off, in iOS e Android](switch-images/switch-states-colors.png "Switch in iOS e Android")

## <a name="respond-to-a-switch-state-change"></a>Risposta a una modifica dello stato del commutatore

Quando la `IsToggled` proprietà viene modificata, tramite la manipolazione dell'utente o quando un'applicazione `IsToggled` imposta la proprietà `Toggled` , viene generato l'evento. Un gestore eventi per questo evento può essere registrato per rispondere alla modifica:

```xaml
<Switch Toggled="OnToggled" />
```

Il file code-behind contiene il gestore per il `Toggled` evento:

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

In questo esempio [`Label`](xref:Xamarin.Forms.Label) , usa un'espressione di associazione in un oggetto `DataTrigger` per monitorare `IsToggled` la proprietà della `Switch` classe `styleSwitch`denominata. Quando questa proprietà diventa `true`, le `FontAttributes` proprietà `FontSize` e dell'oggetto `Label` vengono modificate. Quando la `IsToggled` `false`proprietà restituisce, le `FontAttributes` proprietà e `FontSize` dell'oggetto `Label` vengono reimpostate sullo stato iniziale.

Per informazioni sui trigger, vedere [trigger Novell. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-switch"></a>Disabilitare un'opzione

Un'applicazione può entrare in uno stato in `Switch` cui l'elemento attivato o disattivato non è un'operazione valida. In questi casi, `Switch` può essere disabilitato impostando la relativa `IsEnabled` proprietà `false`su. In questo modo gli utenti non potranno modificare il `Switch`.

## <a name="related-links"></a>Collegamenti correlati

* [Demo switch](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
* [Trigger di Novell. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
