---
title: iPad modale stile di visualizzazione pagina presentazione
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare i set di specifiche della piattaforma iOS lo stile di presentazione di una pagina modale in un iPad.
ms.prod: xamarin
ms.assetid: C791F7CF-330A-44BA-987A-4CFCCBB9278B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: b99898301ed6469b6e0d62ae0077b96aa9c4f3eb
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209086"
---
# <a name="ipad-modal-page-presentation-style"></a>iPad modale stile di visualizzazione pagina presentazione

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Questo specifico della piattaforma iOS consente di impostare lo stile di presentazione di una pagina modale in un iPad. Vengono utilizzati in XAML, impostando il `Page.ModalPresentationStyle` la proprietà associabile per un `UIModalPresentationStyle` valore di enumerazione:

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

[![](page-presentation-style-images/modal-presentation-style-small.png "Modale gli stili di presentazione in un iPad")](page-presentation-style-images/modal-presentation-style-large.png#lightbox "modale gli stili di presentazione in un iPad")

> [!NOTE]
> Le pagine che utilizzano questo specifico della piattaforma per impostare lo stile di presentazione modale è necessario usare navigazione modale. Per altre informazioni, vedere [xamarin. Forms pagine modali](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
