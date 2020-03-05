---
title: Selezione elemento TimePicker in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla quando la selezione degli elementi si verifica in un TimePicker.
ms.prod: xamarin
ms.assetid: 554AC877-8698-4B8C-A676-80DD64305A06
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: 818f368da8ebb375fbacd97d3d48185ba60470d4
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291658"
---
# <a name="timepicker-item-selection-on-ios"></a>Selezione elemento TimePicker in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questi controlli specifici della piattaforma iOS quando la selezione degli elementi si verifica in un [`TimePicker`](xref:Xamarin.Forms.TimePicker), consentendo all'utente di specificare che la selezione degli elementi si verifica quando si esplorano gli elementi nel controllo o solo dopo aver premuto il pulsante **fine** . Viene utilizzato in XAML impostando la proprietà associata `TimePicker.UpdateMode` su un valore dell'enumerazione `UpdateMode`:

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

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

Il metodo `TimePicker.On<iOS>` specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il metodo `TimePicker.SetUpdateMode`, nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , viene usato per controllare quando si verifica la selezione degli elementi, con l'enumerazione `UpdateMode` che fornisce due valori possibili:

- `Immediately`: la selezione degli elementi si verifica quando l'utente Sfoglia gli elementi nel [`TimePicker`](xref:Xamarin.Forms.TimePicker). Questo è il comportamento predefinito in xamarin. Forms.
- `WhenFinished`: la selezione degli elementi si verifica solo dopo che l'utente ha premuto il pulsante **done** nell' [`TimePicker`](xref:Xamarin.Forms.TimePicker).

Inoltre, è possibile usare il metodo `SetUpdateMode` per impostare o disabilitare i valori di enumerazione chiamando il metodo `UpdateMode`, che restituisce il `UpdateMode`corrente:

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

Il risultato è che un `UpdateMode` specificato viene applicato al [`TimePicker`](xref:Xamarin.Forms.TimePicker), che controlla quando si verifica la selezione dell'elemento:

[![Screenshot delle modalità di aggiornamento TimePicker](timepicker-selection-images/timepicker-updatemode.png "Specifiche della piattaforma TimePicker UpdateMode")](timepicker-selection-images/timepicker-updatemode-large.png#lightbox "Specifiche della piattaforma TimePicker UpdateMode")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
