---
title: MasterDetailPage Shadow in iOS
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla se alla pagina dei dettagli di un MasterDetailPage è applicata un'ombreggiatura, quando viene rivelata la pagina master.
ms.prod: xamarin
ms.assetid: FB907EA2-C00A-43A5-84B1-A43584C867A5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
ms.openlocfilehash: eaa82b5accae73e4c6e8eb8d3fbcc873fc111be9
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532709"
---
# <a name="masterdetailpage-shadow-on-ios"></a>MasterDetailPage Shadow in iOS

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma controlla se alla pagina dei dettagli di [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) un oggetto è applicata un'ombreggiatura, quando viene rivelata la pagina master. Viene utilizzato in XAML impostando la `MasterDetailPage.ApplyShadow` proprietà associabile su `true`:

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                  ios:MasterDetailPage.ApplyShadow="true">
    ...
</MasterDetailPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSMasterDetailPageCS : MasterDetailPage
{
    public iOSMasterDetailPageCS(ICommand restore)
    {
        On<iOS>().SetApplyShadow(true);
        // ...
    }
}
```

Il `MasterDetailPage.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `MasterDetailPage.SetApplyShadow` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene utilizzato per controllare se alla pagina dei dettagli [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) di un oggetto è applicata un'ombreggiatura, quando viene rivelata la pagina master. Inoltre, il `GetApplyShadow` metodo può essere utilizzato per determinare se l'ombreggiatura viene applicata alla pagina di dettaglio di `MasterDetailPage`un oggetto.

Il risultato è che alla pagina dei dettagli di [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) un oggetto può essere applicata un'ombreggiatura, quando viene rivelata la pagina master:

[![Screenshot di un MasterDetailPage con e senza ombreggiatura](masterdetailpage-shadow-images/shadow.png "MasterDetailPage con e senza ombreggiatura")](masterdetailpage-shadow-images/shadow-large.png#lightbox "MasterDetailPage con e senza ombreggiatura")

## <a name="related-links"></a>Link correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
