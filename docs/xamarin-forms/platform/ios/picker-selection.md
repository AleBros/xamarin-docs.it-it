---
title: Selezione di elementi di selezione in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare iOS specifiche della piattaforma che controlla quando si verifica la selezione di elementi in un controllo di selezione.
ms.prod: xamarin
ms.assetid: 26B0604A-BD30-49FD-83A6-F0EDFBB0524B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 21c4c289a3fd30db890be6811875412ce4913cf5
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208716"
---
# <a name="picker-item-selection-on-ios"></a>Selezione di elementi di selezione in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Questo specifico della piattaforma iOS consente di controllare quando si verifica la selezione di elementi in un [ `Picker` ](xref:Xamarin.Forms.Picker), che consente all'utente di specificare che la selezione di elementi si verifica quando si esplorano gli elementi del controllo o solo una volta il **eseguita** pulsante è premuto. Vengono utilizzati in XAML, impostando il `Picker.UpdateMode` proprietà associata a un valore del `UpdateMode` enumerazione:

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

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

Il `Picker.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `Picker.SetUpdateMode` metodo, nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene utilizzato per controllare quando si verifica la selezione di elementi, con il `UpdateMode` enumerazione che fornisce due valori possibili:

- `Immediately` -la selezione di elementi si verifica quando l'utente torna a visualizzare gli elementi di [ `Picker` ](xref:Xamarin.Forms.Picker). Questo è il comportamento predefinito in xamarin. Forms.
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

Il risultato è che un oggetto specificato `UpdateMode` viene applicato per il [ `Picker` ](xref:Xamarin.Forms.Picker), che controlla quando si verifica la selezione di elementi:

[![](picker-selection-images/picker-updatemode.png "Selezione UpdateMode specifiche della piattaforma")](picker-selection-images/picker-updatemode-large.png#lightbox "selezione UpdateMode specifiche della piattaforma")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
