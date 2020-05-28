---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 009482c8f1e90aaa2f592ea04d8fd4f0f31324e8
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137020"
---
# <a name="timepicker-item-selection-on-ios"></a>Selezione elemento TimePicker in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questi controlli specifici della piattaforma iOS quando si verifica la selezione degli elementi in un oggetto [`TimePicker`](xref:Xamarin.Forms.TimePicker) , consentendo all'utente di specificare che la selezione degli elementi si verifica quando si esplorano gli elementi nel controllo o solo dopo aver premuto il pulsante **fine** . Viene utilizzato in XAML impostando la `TimePicker.UpdateMode` proprietà associata su un valore dell' `UpdateMode` enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
       <TimePicker Time="14:00:00"
                   ios:TimePicker.UpdateMode="WhenFinished" />
       ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

Il `TimePicker.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `TimePicker.SetUpdateMode` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene utilizzato per controllare quando si verifica la selezione dell'elemento, con l' `UpdateMode` enumerazione che fornisce due valori possibili:

- `Immediately`-la selezione degli elementi si verifica quando l'utente Sfoglia gli elementi in [`TimePicker`](xref:Xamarin.Forms.TimePicker) . Si tratta del comportamento predefinito in Xamarin.Forms .
- `WhenFinished`-la selezione degli elementi si verifica solo dopo che l'utente ha premuto il pulsante **done** nell' [`TimePicker`](xref:Xamarin.Forms.TimePicker) .

Inoltre, il `SetUpdateMode` metodo può essere utilizzato per abilitare o disabilitare i valori di enumerazione chiamando il `UpdateMode` metodo, che restituisce l'oggetto corrente `UpdateMode` :

```csharp
switch (timePicker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        timePicker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

Il risultato è che un oggetto specificato `UpdateMode` viene applicato a [`TimePicker`](xref:Xamarin.Forms.TimePicker) , che controlla quando si verifica la selezione dell'elemento:

[![Screenshot delle modalità di aggiornamento TimePicker](timepicker-selection-images/timepicker-updatemode.png "Specifiche della piattaforma TimePicker UpdateMode")](timepicker-selection-images/timepicker-updatemode-large.png#lightbox "Specifiche della piattaforma TimePicker UpdateMode")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
