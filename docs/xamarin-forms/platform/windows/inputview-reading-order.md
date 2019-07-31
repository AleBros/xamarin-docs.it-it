---
title: Ordine di lettura InputView per Windows
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare l'oggetto specifico della piattaforma Windows che consente di rilevare in modo dinamico l'ordine di lettura del testo bidirezionale.
ms.prod: xamarin
ms.assetid: E61BAEE0-C8B7-4F33-8DDC-FA1B9CA8E81D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: c184424a982aa82712685dbc33ad57422f2f8338
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651432"
---
# <a name="inputview-reading-order-on-windows"></a>Ordine di lettura InputView per Windows

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa piattaforma UWP (Universal Windows Platform) specifica della piattaforma consente di rilevare dinamicamente l'ordine di lettura (da sinistra a destra o da destra a sinistra) del testo bidirezionale nelle [`Entry`](xref:Xamarin.Forms.Entry)istanze [`Label`](xref:Xamarin.Forms.Label) di, [`Editor`](xref:Xamarin.Forms.Editor)e. Vengono utilizzati in XAML, impostando il [`InputView.DetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (per `Entry` e `Editor` istanze) o [`Label.DetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) proprietà associata una `boolean` valore:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

Il `Editor.On<Windows>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo nella piattaforma Windows universale. Il [`InputView.SetDetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.SetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView},System.Boolean)) (metodo), nella [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene usata per controllare se l'ordine di lettura viene rilevato dal contenuto il [`InputView`](xref:Xamarin.Forms.InputView). Inoltre, il `InputView.SetDetectReadingOrderFromContent` metodo può essere utilizzato per attivare / disattivare l'ordine di lettura viene rilevato dal contenuto chiamando il [`InputView.GetDetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.GetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView})) per restituire il valore corrente:

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

Il risultato è che [`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), e [`Label`](xref:Xamarin.Forms.Label) istanze possono avere l'ordine di lettura dei propri contenuti rilevati in modo dinamico:

[![InputView rilevamento di ordine di lettura dal contenuto specifico della piattaforma](inputview-reading-order-images/editor-readingorder.png "InputView rilevamento di ordine di lettura dal contenuto specifico della piattaforma")](inputview-reading-order-images/editor-readingorder-large.png#lightbox "InputView rilevamento di ordine di lettura da contenuto specifico della piattaforma")

> [!NOTE]
> A differenza di impostazione la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà, la logica per le visualizzazioni che rilevano l'ordine di lettura dal contenuto di testo non influiranno l'allineamento del testo all'interno della visualizzazione. Al contrario, modifica l'ordine in cui sono disposti i blocchi di testo bidirezionale.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
