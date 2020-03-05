---
title: Primo risponditore di visualElement in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma iOS che consente a un oggetto visivo di diventare il primo risponditore per il tocco degli eventi.
ms.prod: xamarin
ms.assetid: 3A77BA02-B87A-44EC-AC51-9D3130EF314C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: be6c233b63d172d2fcacb1cea7f5e9aeeb7faed1
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291937"
---
# <a name="visualelement-first-responder-on-ios"></a>Primo risponditore di visualElement in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS consente a un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) di diventare il primo risponditore per gli eventi di tocco, anziché la pagina contenente l'elemento. Viene utilizzato in XAML impostando la proprietà associabile `VisualElement.CanBecomeFirstResponder` su `true`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry Placeholder="Enter text" />
        <Button ios:VisualElement.CanBecomeFirstResponder="True"
                Text="OK" />
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Entry entry = new Entry { Placeholder = "Enter text" };
Button button = new Button { Text = "OK" };
button.On<iOS>().SetCanBecomeFirstResponder(true);
```

Il metodo `VisualElement.On<iOS>` specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il metodo `VisualElement.SetCanBecomeFirstResponder`, nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , viene usato per impostare il `VisualElement` come primo risponditore per gli eventi di tocco. Inoltre, è possibile utilizzare il metodo `VisualElement.CanBecomeFirstResponder` per restituire se il `VisualElement` è il primo risponditore per il tocco degli eventi.

Il risultato è che un [`VisualElement`](xref:Xamarin.Forms.VisualElement) può diventare il primo risponditore per gli eventi di tocco, invece della pagina che contiene l'elemento. In questo modo, scenari come le applicazioni di chat non rilasciano una tastiera quando viene toccato un [`Button`](xref:Xamarin.Forms.Button) .

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
