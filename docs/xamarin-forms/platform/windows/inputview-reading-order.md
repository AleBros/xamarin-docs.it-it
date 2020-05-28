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
ms.openlocfilehash: f5f0bcdc2d2c8eb1b51ad8dcd1014c649af80c90
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137761"
---
# <a name="inputview-reading-order-on-windows"></a>Ordine di lettura InputView per Windows

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa piattaforma UWP (Universal Windows Platform) specifica della piattaforma consente [`Entry`](xref:Xamarin.Forms.Entry) [`Editor`](xref:Xamarin.Forms.Editor) [`Label`](xref:Xamarin.Forms.Label) di rilevare dinamicamente l'ordine di lettura (da sinistra a destra o da destra a sinistra) del testo bidirezionale nelle istanze di, e. Viene utilizzato in XAML impostando [`InputView.DetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (per le `Entry` istanze e `Editor` ) o la [`Label.DetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) proprietà associata su un `boolean` valore:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

Il `Editor.On<Windows>` metodo specifica che questa specifica della piattaforma verrà eseguita solo sul piattaforma UWP (Universal Windows Platform). [ `InputView.SetDetectReadingOrderFromContent` ] (Xrif: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. InputView. SetDetectReadingOrderFromContent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Il metodo InputView}, System. Boolean)), nello [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) spazio dei nomi, viene usato per controllare se l'ordine di lettura viene rilevato dal contenuto nell'oggetto [`InputView`](xref:Xamarin.Forms.InputView) . Inoltre, il `InputView.SetDetectReadingOrderFromContent` metodo può essere utilizzato per indicare se l'ordine di lettura viene rilevato dal contenuto chiamando [ `InputView.GetDetectReadingOrderFromContent` ] (xrif: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. InputView. GetDetectReadingOrderFromContent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . InputView})) metodo per restituire il valore corrente:

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

Il risultato è che [`Entry`](xref:Xamarin.Forms.Entry) [`Editor`](xref:Xamarin.Forms.Editor) [`Label`](xref:Xamarin.Forms.Label) le istanze di, e possono avere l'ordine di lettura del contenuto rilevato in modo dinamico:

[![InputView rilevamento dell'ordine di lettura dal contenuto specifico della piattaforma](inputview-reading-order-images/editor-readingorder.png "InputView rilevamento dell'ordine di lettura dal contenuto specifico della piattaforma")](inputview-reading-order-images/editor-readingorder-large.png#lightbox "InputView rilevamento dell'ordine di lettura dal contenuto specifico della piattaforma")

> [!NOTE]
> Diversamente dall'impostazione della [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà, la logica per le viste che rilevano l'ordine di lettura dal contenuto di testo non influirà sull'allineamento del testo all'interno della vista. Modifica invece l'ordine in cui vengono disposti i blocchi del testo bidirezionale.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
