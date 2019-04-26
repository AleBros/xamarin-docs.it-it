---
title: Accessibilità in macOS
description: Questo documento descrive come usare le funzionalità di accessibilità di macOS in un'app xamarin. Mac. Vengono illustrati elementi dell'interfaccia utente che descrive gli storyboard e codice, i controlli personalizzati e accessibilità test.
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: fdda52309ffdb0d32cc42a4dff052cd9050b1e4f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61378478"
---
# <a name="accessibility-on-macos"></a>Accessibilità in macOS

Questa pagina descrive come usare l'API di accessibilità di macOS per creare App in base al [elenco di controllo di accesso facilitato](~/cross-platform/app-fundamentals/accessibility.md).
Vedere il [accessibilità Android](~/android/app-fundamentals/accessibility.md) e [accessibilità iOS](~/ios/app-fundamentals/accessibility.md) pagine per gli altri API della piattaforma.

Per comprendere il funzionamento dell'API di accessibilità in macOS (precedentemente denominato OS X), esaminare prima i [modello di accessibilità di OS X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html).

## <a name="describing-ui-elements"></a>Che descrive gli elementi dell'interfaccia utente

Usa AppKit il `NSAccessibility` protocollo per esporre le API che consentono di rendere accessibile l'interfaccia utente. Si tratta di un comportamento predefinito che tenta di impostare valori significativi per le proprietà di accessibilità, ad esempio l'impostazione di un pulsante `AccessibilityLabel`. L'etichetta è in genere una parola o frase breve che descrive il controllo o una vista.

### <a name="storyboard-files"></a>File storyboard

Xamarin. Mac Usa di Interface Builder di Xcode per modificare i file storyboard.
Le informazioni sull'accessibilità possono essere modificate nella **Identity inspector** quando viene selezionato un controllo nell'area di progettazione (come illustrato nello screenshot seguente):

[![Aggiunta di accessibilità in Interface Builder di Xcode](accessibility-images/xcode.png "aggiunta di accessibilità in Interface Builder di Xcode")](accessibility-images/xcode-large.png#lightbox)

### <a name="code"></a>Codice

Xamarin. Mac non attualmente espone come `AccessibilityLabel` setter.  Aggiungere il metodo helper seguente per impostare l'etichetta di accessibilità:

```csharp
public static class AccessibilityHelper
{
    [System.Runtime.InteropServices.DllImport (ObjCRuntime.Constants.ObjectiveCLibrary)]
    extern static void objc_msgSend (IntPtr handle, IntPtr selector, IntPtr label);

    static public void SetAccessibilityLabel (this NSView view, string value)
    {
        objc_msgSend (view.Handle, new ObjCRuntime.Selector ("setAccessibilityLabel:").Handle, new NSString (value).Handle);
    }
}
```

Questo metodo può quindi essere utilizzato nel codice come illustrato:

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

Il `AccessibilityHelp` proprietà viene utilizzata per una spiegazione della visualizzazione controlli o e devono essere aggiunti solo quando l'etichetta potrebbe non fornire informazioni sufficienti. Il testo della Guida ancora deve essere mantenuto più corte possibili, per esempio "Elimina il documento".

Alcuni elementi dell'interfaccia utente non sono rilevanti per l'accesso accessibile (ad esempio un'etichetta accanto a un input che ha un proprio etichetta di accessibilità e Guida).
In questi casi, impostare `AccessibilityElement = false` in modo che questi controlli o le viste verranno ignorate da lettori dello schermo o altri strumenti di accessibilità.

Apple offre [linee guida sull'accessibilità](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html) che spiega le procedure consigliate per il testo delle etichette e la Guida di accessibilità.

## <a name="custom-controls"></a>Controlli personalizzati

Fare riferimento a Apple [linee guida per i controlli personalizzati accessibile](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html) per informazioni dettagliate sui passaggi aggiuntivi necessari.

## <a name="testing-accessibility"></a>Verifica dell'accessibilità

macOS fornisce un' **controllo di accesso facilitato** che consente di testare la funzionalità di accessibilità. Il controllo è incluso in Xcode.

La prima volta viene avviata, il **controllo di accesso facilitato** richiederà l'autorizzazione per controllare il computer tramite accesso facilitato:

![Controllo di accesso facilitato che richiede l'autorizzazione per eseguire](accessibility-images/accessibility-inspector-1.png "controllo di accesso facilitato che richiede l'autorizzazione per l'esecuzione")

Sblocco dello schermo di impostazioni (se richiesto, in basso a sinistra) e segno di graduazione il **controllo di accesso facilitato**:

![Schermata delle impostazioni per abilitare il controllo di accesso facilitato](accessibility-images/accessibility-inspector-2.png "schermata delle impostazioni per abilitare il controllo di accesso facilitato")

Una volta abilitato, il controllo viene visualizzato come una finestra mobile che può essere spostata attorno alla schermata. La schermata seguente mostra il controllo in esecuzione accanto a un'app di esempio Mac. Quando il cursore viene spostato nell'intervallo, il controllo consente di visualizzare tutte le proprietà accessibili di ogni controllo:

[![Esempio di esecuzione di controllo di accesso facilitato](accessibility-images/accessibility-example.png "esecuzione di esempio di controllo di accesso facilitato")](accessibility-images/accessibility-example-large.png#lightbox)

Per altre informazioni, vedere la [test di accessibilità per la Guida per OS X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html).



## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità multipiattaforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Accessibilità di Mac](https://www.apple.com/accessibility/mac/)
