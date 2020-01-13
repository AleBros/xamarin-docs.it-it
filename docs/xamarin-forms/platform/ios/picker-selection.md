---
title: Selezione elemento selezione in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla quando la selezione degli elementi si verifica in una selezione.
ms.prod: xamarin
ms.assetid: 26B0604A-BD30-49FD-83A6-F0EDFBB0524B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 57420921100c99db1e2c3a5259ece30cfda719f2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651850"
---
# <a name="picker-item-selection-on-ios"></a>Selezione elemento selezione in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questi controlli specifici della piattaforma iOS quando si verifica la selezione degli [`Picker`](xref:Xamarin.Forms.Picker)elementi in un oggetto, consentendo all'utente di specificare che la selezione degli elementi si verifica quando si esplorano gli elementi nel controllo o solo dopo aver premuto il pulsante **fine** . Vengono utilizzati in XAML, impostando il `Picker.UpdateMode` proprietà associata a un valore del `UpdateMode` enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

Il `Picker.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `Picker.SetUpdateMode` metodo, nel [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene utilizzato per controllare quando si verifica la selezione di elementi, con il `UpdateMode` enumerazione che fornisce due valori possibili:

- `Immediately` -la selezione di elementi si verifica quando l'utente torna a visualizzare gli elementi di [ `Picker` ](xref:Xamarin.Forms.Picker). Questo è il comportamento predefinito in Xamarin.Forms.
- `WhenFinished` – la selezione di elementi ha effetto solo quando l'utente ha premuto il **eseguita** pulsante il [ `Picker` ](xref:Xamarin.Forms.Picker).

Inoltre, il `SetUpdateMode` metodo può essere utilizzato per attivare o disattivare i valori di enumerazione chiamando il `UpdateMode` metodo, che restituisce l'attuale `UpdateMode`:

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

Il risultato è che un oggetto specificato `UpdateMode` viene applicato per il [`Picker`](xref:Xamarin.Forms.Picker), che controlla quando si verifica la selezione di elementi:

[![](picker-selection-images/picker-updatemode.png "Selezione UpdateMode specifiche della piattaforma")](picker-selection-images/picker-updatemode-large.png#lightbox "selezione UpdateMode specifiche della piattaforma")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
