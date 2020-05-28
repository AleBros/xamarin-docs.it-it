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
ms.openlocfilehash: d8bd539c2bb0e8963afae3392b6f8e99d79af9af
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136969"
---
# <a name="visualelement-first-responder-on-ios"></a>Primo risponditore di visualElement in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS consente a un [`VisualElement`](xref:Xamarin.Forms.VisualElement) oggetto di diventare il primo risponditore per gli eventi di tocco, anziché la pagina contenente l'elemento. Viene utilizzato in XAML impostando la `VisualElement.CanBecomeFirstResponder` proprietà associabile su `true` :

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

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Entry entry = new Entry { Placeholder = "Enter text" };
Button button = new Button { Text = "OK" };
button.On<iOS>().SetCanBecomeFirstResponder(true);
```

Il `VisualElement.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `VisualElement.SetCanBecomeFirstResponder` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene usato per impostare l'oggetto in `VisualElement` modo che diventi il primo risponditore per gli eventi di tocco. Inoltre, il `VisualElement.CanBecomeFirstResponder` metodo può essere utilizzato per restituire se `VisualElement` è il primo risponditore per il tocco degli eventi.

Il risultato è che un [`VisualElement`](xref:Xamarin.Forms.VisualElement) può diventare il primo risponditore per gli eventi di tocco, invece della pagina che contiene l'elemento. Questo consente a scenari come le applicazioni di chat di non disperdere una tastiera quando [`Button`](xref:Xamarin.Forms.Button) viene toccato un.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
