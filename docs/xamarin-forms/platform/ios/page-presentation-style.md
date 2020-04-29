---
title: Stile di presentazione pagina modale in iOS
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS imposta lo stile di presentazione di una pagina modale.
ms.prod: xamarin
ms.assetid: C791F7CF-330A-44BA-987A-4CFCCBB9278B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
ms.openlocfilehash: 5078b280499929e0e2e3691539cf1927b4c79fe7
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517530"
---
# <a name="modal-page-presentation-style-on-ios"></a>Stile di presentazione pagina modale in iOS

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS viene utilizzata per impostare lo stile di presentazione di una pagina modale. Viene utilizzato in XAML impostando la `Page.ModalPresentationStyle` proprietà associabile su un `UIModalPresentationStyle` valore di enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="FormSheet">
    ...
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

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

Il `Page.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `Page.SetModalPresentationStyle` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene usato per impostare lo stile di presentazione modale [`Page`](xref:Xamarin.Forms.Page) su un oggetto specificando uno `UIModalPresentationStyle` dei seguenti valori di enumerazione:

- `FullScreen`, che imposta lo stile di presentazione modale per includere l'intero schermo. Per impostazione predefinita, le pagine modali vengono visualizzate utilizzando questo stile di presentazione.
- `FormSheet`, che imposta lo stile di presentazione modale in modo che sia centrato su una dimensione inferiore a quella dello schermo.
- `Automatic`, che imposta lo stile di presentazione modale sul valore predefinito scelto dal sistema. Per la maggior parte dei `UIKit` controller di visualizzazione `UIModalPresentationStyle.PageSheet`, ne esegue il mapping a, ma alcuni controller di visualizzazione di sistema possono eseguirne il mapping a uno stile diverso.
- `OverFullScreen`, che imposta lo stile di presentazione modale per coprire lo schermo.
- `PageSheet`, che imposta lo stile di presentazione modale per coprire il contenuto sottostante.

Inoltre, il `GetModalPresentationStyle` metodo può essere utilizzato per recuperare il valore corrente dell' `UIModalPresentationStyle` enumerazione applicata a. [`Page`](xref:Xamarin.Forms.Page)

Il risultato è che lo stile di presentazione modale su [`Page`](xref:Xamarin.Forms.Page) un oggetto può essere impostato:

[![](page-presentation-style-images/modal-presentation-style-small.png "Modal Presentation Styles")](page-presentation-style-images/modal-presentation-style-large.png#lightbox "Modal Presentation Styles")

> [!NOTE]
> Le pagine che usano questo specifico della piattaforma per impostare lo stile di presentazione modale devono usare la navigazione modale. Per altre informazioni, vedere [Novell. Forms Modal Pages](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Link correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
