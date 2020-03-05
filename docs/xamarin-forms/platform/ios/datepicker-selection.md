---
title: Selezione elemento DatePicker in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla quando la selezione degli elementi si verifica in un controllo DatePicker.
ms.prod: xamarin
ms.assetid: 847E69D1-6AE0-4E82-B9C8-919E009C2014
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: df84cf01909cec564edc9c6c8bb55382a2b9dfe3
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291794"
---
# <a name="datepicker-item-selection-on-ios"></a>Selezione elemento DatePicker in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questi controlli specifici della piattaforma iOS quando la selezione degli elementi si verifica in un [`DatePicker`](xref:Xamarin.Forms.DatePicker), consentendo all'utente di specificare che la selezione degli elementi si verifica quando si esplorano gli elementi nel controllo o solo dopo aver premuto il pulsante **fine** . Viene utilizzato in XAML impostando la proprietà associata `DatePicker.UpdateMode` su un valore dell'enumerazione `UpdateMode`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
       <DatePicker MinimumDate="01/01/2020"
                   MaximumDate="12/31/2020"
                   ios:DatePicker.UpdateMode="WhenFinished" />
       ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

Il metodo `DatePicker.On<iOS>` specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il metodo `DatePicker.SetUpdateMode`, nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , viene usato per controllare quando si verifica la selezione degli elementi, con l'enumerazione `UpdateMode` che fornisce due valori possibili:

- `Immediately`: la selezione degli elementi si verifica quando l'utente Sfoglia gli elementi nel [`DatePicker`](xref:Xamarin.Forms.DatePicker). Questo è il comportamento predefinito in xamarin. Forms.
- `WhenFinished`: la selezione degli elementi si verifica solo dopo che l'utente ha premuto il pulsante **done** nell' [`DatePicker`](xref:Xamarin.Forms.DatePicker).

Inoltre, è possibile usare il metodo `SetUpdateMode` per impostare o disabilitare i valori di enumerazione chiamando il metodo `UpdateMode`, che restituisce il `UpdateMode`corrente:

```csharp
switch (datePicker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        datePicker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

Il risultato è che un `UpdateMode` specificato viene applicato al [`DatePicker`](xref:Xamarin.Forms.DatePicker), che controlla quando si verifica la selezione dell'elemento:

[![Screenshot delle modalità di aggiornamento DatePicker](datepicker-selection-images/datepicker-updatemode.png "Specifica della piattaforma UpdateMode di DatePicker")](datepicker-selection-images/datepicker-updatemode-large.png#lightbox "Specifica della piattaforma UpdateMode di DatePicker")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
