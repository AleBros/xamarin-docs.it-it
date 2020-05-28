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
ms.openlocfilehash: 2abc255964df35fbdfeb4191911c57df9be99fd9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128011"
---
# <a name="modal-page-presentation-style-on-ios"></a>Stile di presentazione pagina modale in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS viene utilizzata per impostare lo stile di presentazione di una pagina modale ed è inoltre possibile utilizzare per visualizzare le pagine modali con sfondi trasparenti. Viene utilizzato in XAML impostando la `Page.ModalPresentationStyle` proprietà associabile su un `UIModalPresentationStyle` valore di enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="OverFullScreen">
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
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.OverFullScreen);
        ...
    }
}
```

Il `Page.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `Page.SetModalPresentationStyle` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene usato per impostare lo stile di presentazione modale su un oggetto [`Page`](xref:Xamarin.Forms.Page) specificando uno dei seguenti `UIModalPresentationStyle` valori di enumerazione:

- `FullScreen`, che imposta lo stile di presentazione modale per includere l'intero schermo. Per impostazione predefinita, le pagine modali vengono visualizzate utilizzando questo stile di presentazione.
- `FormSheet`, che imposta lo stile di presentazione modale in modo che sia centrato su una dimensione inferiore a quella dello schermo.
- `Automatic`, che imposta lo stile di presentazione modale sul valore predefinito scelto dal sistema. Per la maggior parte dei controller di visualizzazione, ne `UIKit` esegue il mapping a `UIModalPresentationStyle.PageSheet` , ma alcuni controller di visualizzazione di sistema possono eseguirne il mapping a uno stile diverso.
- `OverFullScreen`, che imposta lo stile di presentazione modale per coprire lo schermo.
- `PageSheet`, che imposta lo stile di presentazione modale per coprire il contenuto sottostante.

Inoltre, il `GetModalPresentationStyle` metodo può essere utilizzato per recuperare il valore corrente dell' `UIModalPresentationStyle` enumerazione applicata a [`Page`](xref:Xamarin.Forms.Page) .

Il risultato è che lo stile di presentazione modale su un oggetto [`Page`](xref:Xamarin.Forms.Page) può essere impostato:

[![](page-presentation-style-images/modal-presentation-style-small.png "Modal Presentation Styles")](page-presentation-style-images/modal-presentation-style-large.png#lightbox "Modal Presentation Styles")

> [!NOTE]
> Le pagine che usano questo specifico della piattaforma per impostare lo stile di presentazione modale devono usare la navigazione modale. Per ulteriori informazioni, vedere le [ Xamarin.Forms pagine modali](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
