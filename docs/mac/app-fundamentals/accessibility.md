---
title: Accessibilità in macOS
description: Questo documento descrive come usare le funzionalità di accessibilità macOS in un'app Xamarin.Mac. Viene descritta la descrizione degli elementi dell'interfaccia utente negli storyboard e nel codice, nei controlli personalizzati e nell'accessibilità del test.
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 2162fba1275b66167965e90aeade721e08ea9130
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489323"
---
# <a name="accessibility-on-macos"></a>Accessibilità in macOS

Questa pagina descrive come usare le API di accessibilità macOS per creare app in base all' [elenco di controllo di accessibilità](~/cross-platform/app-fundamentals/accessibility.md).
Vedere le pagine relative all'accessibilità di [Android](~/android/app-fundamentals/accessibility.md) e all' [accessibilità iOS](~/ios/app-fundamentals/accessibility.md) per altre API della piattaforma.

Per comprendere il funzionamento delle API di accessibilità in macOS (in precedenza noto come OS X), esaminare prima di tutto il [modello di accessibilità di OS x](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html).

## <a name="describing-ui-elements"></a>Descrizione degli elementi dell'interfaccia utente

AppKit usa il protocollo `NSAccessibility` per esporre le API che consentono di rendere accessibile l'interfaccia utente. Questo include un comportamento predefinito che tenta di impostare valori significativi per le proprietà di accessibilità, ad esempio l'impostazione del `AccessibilityLabel`di un pulsante. L'etichetta è in genere una singola parola o breve frase che descrive il controllo o la visualizzazione.

### <a name="storyboard-files"></a>File storyboard

Xamarin.Mac usa il Interface Builder Xcode per modificare i file dello storyboard.
Le informazioni di accessibilità possono essere modificate in **Identity Inspector** quando si seleziona un controllo nell'area di progettazione, come illustrato nella schermata seguente:

[![Aggiunta dell'accessibilità in Interface Builder di Xcode](accessibility-images/xcode.png "Aggiunta dell'accessibilità in Interface Builder di Xcode")](accessibility-images/xcode-large.png#lightbox)

### <a name="code"></a>Codice

Xamarin.Mac non è attualmente esposto come Setter `AccessibilityLabel`.  Aggiungere il seguente metodo helper per impostare l'etichetta di accessibilità:

```csharp
public static class AccessibilityHelper
{
    [System.Runtime.InteropServices.DllImport (ObjCRuntime.Constants.ObjectiveCLibrary)]
    extern static void objc_msgSend (IntPtr handle, IntPtr selector, IntPtr label);

    static public void SetAccessibilityLabel (this NSView view, string value)
    {
        objc_msgSend (view.Handle, new ObjCRuntime.Selector ("setAccessibilityLabel:").Handle, new NSString (value).Handle);
    }
}
```

Questo metodo può quindi essere utilizzato nel codice come illustrato di seguito:

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

La proprietà `AccessibilityHelp` è per una spiegazione del funzionamento del controllo o della vista e deve essere aggiunta solo quando l'etichetta non fornisce informazioni sufficienti. Il testo della guida deve comunque essere mantenuto il più breve possibile, ad esempio "Elimina il documento".

Alcuni elementi dell'interfaccia utente non sono rilevanti per l'accesso accessibile, ad esempio un'etichetta accanto a un input con una propria etichetta di accessibilità e la relativa guida.
In questi casi, impostare `AccessibilityElement = false` in modo che questi controlli o viste vengano ignorati dalle utilità per la lettura dello schermo o da altri strumenti di accessibilità.

Apple fornisce [linee guida per l'accessibilità](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html) che illustrano le procedure consigliate per le etichette e il testo della guida.

## <a name="custom-controls"></a>Controlli personalizzati

Per informazioni dettagliate sui passaggi aggiuntivi necessari, vedere le [linee guida di Apple per i controlli personalizzati accessibili](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html) .

## <a name="testing-accessibility"></a>Test dell'accessibilità

macOS fornisce un **controllo di accessibilità** che consente di testare la funzionalità di accessibilità. Il controllo è incluso in Xcode.

Alla prima avvio, il controllo di **accessibilità** richiederà l'autorizzazione per controllare il computer tramite accessibilità:

![Controllo di accessibilità che richiede l'autorizzazione per l'esecuzione](accessibility-images/accessibility-inspector-1.png "Controllo di accessibilità che richiede l'autorizzazione per l'esecuzione")

Sbloccare la schermata delle impostazioni (se necessario, in basso a sinistra) e selezionare **controllo di accessibilità**:

![Schermata delle impostazioni per abilitare il controllo dell'accessibilità](accessibility-images/accessibility-inspector-2.png "Schermata delle impostazioni per abilitare il controllo dell'accessibilità")

Una volta abilitato, il controllo viene visualizzato come una finestra mobile che può essere spostata intorno allo schermo. La schermata seguente mostra il controllo in esecuzione accanto a un'app Mac di esempio. Quando il cursore viene spostato sulla finestra, il controllo Visualizza tutte le proprietà accessibili di ogni controllo:

[![Esempio di controllo di accessibilità in esecuzione](accessibility-images/accessibility-example.png "Esempio di controllo di accessibilità in esecuzione")](accessibility-images/accessibility-example-large.png#lightbox)

Per altre informazioni, vedere la [Guida test di accessibilità per OS X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html).

## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità tra piattaforme](~/cross-platform/app-fundamentals/accessibility.md)
- [Accessibilità Mac](https://www.apple.com/accessibility/mac/)
