---
title: Xamarin.Forms Switch
description: L'opzione di xamarin. Forms è un tipo di pulsante che può essere modificato dall'utente per attivare/disattivare tra e disattivare gli stati. Questo articolo illustra come usare la classe di opzione per visualizzare un elemento dell'interfaccia utente attivata e disattivato.
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/03/2019
ms.openlocfilehash: 22a17f9a916d94a3a0f44a451512de43c943e95a
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675038"
---
# <a name="xamarinforms-switch"></a>Xamarin.Forms Switch

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SwitchDemos)

Xamarin. Forms [ `Switch` ](xref:Xamarin.Forms.Switch) è un pulsante di attivazione/disattivazione orizzontale che può essere modificato dall'utente per attivare/disattivare tra e disattivare gli stati, che sono rappresentate da un `boolean` valore. Il `Switch` classe eredita da [ `View` ](xref:Xamarin.Forms.View).

La schermata seguente mostra una `Switch` controllare in relativi **sul** e **off** attivare/disattivare gli stati in iOS e Android:

![Screenshot di commutatori di attivare e disattivare gli stati, in iOS e Android](switch-images/switch-states-default.png "passa in iOS e Android")

Il `Switch` controllo definisce due proprietà:

* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor) è un `Color` che influisce su come il `Switch` viene eseguito il rendering in attivato/disattivato, o **su**, stato.
* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) è un `boolean` valore che indica se il `Switch` viene **su**.

Queste proprietà sono supportate da un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetto, ovvero il `Switch` possono essere personalizzati con stili e può essere la destinazione di associazioni dati.

Il `Switch` controllo definisce un `Toggled` evento generato quando il `IsToggled` le modifiche alle proprietà, tramite la modifica dell'utente o quando un'applicazione imposta il `IsToggled` proprietà. Il `ToggledEventArgs` che accompagna il `Toggled` evento dispone di una singola proprietà denominata `Value`, di tipo `bool`. Quando viene generato l'evento, il valore della `Value` proprietà riflette il nuovo valore della `IsToggled` proprietà.

## <a name="create-a-switch"></a>Creare un commutatore

Oggetto `Switch` può essere inizializzata in XAML. Relativi `IsToggled` proprietà può essere impostata per attivare o disattivare il `Switch`. Per impostazione predefinita, il `IsToggled` è di proprietà `false`. Nell'esempio seguente viene illustrato come creare un'istanza di un `Switch` in XAML con l'opzione facoltativa `IsToggled` set di proprietà:

```xaml
<Switch IsToggled="true"/>
```

Oggetto `Switch` possono anche essere creati nel codice:

```csharp
Switch switch = new Switch { IsToggled = true };
```

### <a name="switch-style-properties"></a>Passare le proprietà di stile

Il `OnColor` proprietà può essere impostata per definire le `Switch` colore quando viene passato a relativo **su** dello stato. Nell'esempio seguente viene illustrato come creare un'istanza di un `Switch` in XAML con il `OnColor` set di proprietà:

```xaml
<Switch OnColor="Orange" />
```

Il `OnColor` proprietà può essere impostata anche durante la creazione di un `Switch` nel codice:

```csharp
Switch switch = new Switch { OnColor = Color.Orange };
```

La schermata seguente mostra il `Switch` nel relativo **sul** e **off** attivare/disattivare gli stati, con il `OnColor` proprietà impostata su `Color.Orange` in iOS e Android:

![Screenshot di commutatori di attivare e disattivare gli stati, in iOS e Android](switch-images/switch-states-oncolor.png "passa in iOS e Android")

## <a name="respond-to-a-switch-state-change"></a>Rispondere a una modifica dello stato di commutatore

Quando la `IsToggled` le modifiche alle proprietà, tramite la modifica dell'utente o quando un'applicazione imposta il `IsToggled` proprietà, il `Toggled` viene generato l'evento. Un gestore eventi per questo evento può essere registrato per rispondere alla modifica:

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

Il `sender` argomento nel gestore eventi è il `Switch` responsabile della generazione di questo evento. È possibile usare la `sender` proprietà a cui accedere il `Switch` oggetto, o per distinguere tra più `Switch` gli oggetti che condividono lo stesso `Toggled` gestore dell'evento.

Il `Toggled` gestore eventi può anche essere assegnato nel codice:

```csharp
Switch switch = new Switch {...};
switch.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>Associa dati di un commutatore

Il `Toggled` gestore dell'evento possa essere eliminato usando data binding e trigger per rispondere a un `Switch` modifica degli stati di attivazione/disattivazione.

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

In questo esempio, il [ `Label` ](xref:Xamarin.Forms.Label) viene utilizzata un'espressione di associazione in un `DataTrigger` per monitorare il `IsToggled` proprietà del `Switch` denominato `styleSwitch`. Quando questa proprietà diventa `true`, il `FontAttributes` e `FontSize` delle proprietà del `Label` vengono modificati. Quando la `IsToggled` proprietà restituisce al `false`, il `FontAttributes` e `FontSize` le proprietà del `Label` vengono reimpostati allo stato iniziale.

Per informazioni sui trigger, vedere [trigger di xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-switch"></a>Disabilitare un commutatore

Un'applicazione potrebbe entrare in uno stato in cui il `Switch` viene attivato/disattivato, non è un'operazione valida. In questi casi, il `Switch` possono essere disabilitate impostando relativi `IsEnabled` proprietà `false`. Ciò impedirà agli utenti la possibilità di modificare il `Switch`.

## <a name="related-links"></a>Collegamenti correlati

* [Demo di commutatore](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SwitchDemos)
* [Trigger di xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
