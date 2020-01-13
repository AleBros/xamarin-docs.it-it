---
title: Stile di presentazione pagina modale in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma iOS imposta lo stile di presentazione di una pagina modale.
ms.prod: xamarin
ms.assetid: C791F7CF-330A-44BA-987A-4CFCCBB9278B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 3b1a88968334bed42be53119c26de43ef9cd1419
ms.sourcegitcommit: eb23b7d745d1090376f9def07e0f11cb089494d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72171053"
---
# <a name="modal-page-presentation-style-on-ios"></a>Stile di presentazione pagina modale in iOS

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS viene utilizzata per impostare lo stile di presentazione di una pagina modale. Vengono utilizzati in XAML, impostando il `Page.ModalPresentationStyle` la proprietà associabile per un `UIModalPresentationStyle` valore di enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="FormSheet">
    ...
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSModalFormSheetPageCS : ContentPage
{
    public iOSModalFormSheetPageCS()
    {
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.FormSheet);
        ...
    }
}
```

Il `Page.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `Page.SetModalPresentationStyle` metodo, nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usato per impostare lo stile di presentazione modale in un [ `Page` ](xref:Xamarin.Forms.Page) specificando uno dei seguenti `UIModalPresentationStyle` enumerazione valori:

- `FullScreen`, che imposta lo stile di presentazione modale per includere l'intero schermo. Per impostazione predefinita, vengono visualizzate pagine modali utilizza questo stile di presentazione.
- `FormSheet`, che imposta lo stile di presentazione modale da essere centrati sul e inferiori rispetto allo schermo.

Inoltre, il `GetModalPresentationStyle` metodo può essere utilizzato per recuperare il valore corrente del `UIModalPresentationStyle` enumerazione che viene applicato al [ `Page` ](xref:Xamarin.Forms.Page).

Il risultato è che lo stile di presentazione modali in un' [ `Page` ](xref:Xamarin.Forms.Page) può essere impostato:

[![](page-presentation-style-images/modal-presentation-style-small.png "Stili di presentazione modale in un iPad")](page-presentation-style-images/modal-presentation-style-large.png#lightbox "Stili di presentazione modale in un iPad")

> [!NOTE]
> Le pagine che utilizzano questo specifico della piattaforma per impostare lo stile di presentazione modale è necessario usare navigazione modale. Per altre informazioni, vedere [Xamarin.Forms pagine modali](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
