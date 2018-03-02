---
title: "Accessibilità in macOS"
description: "Questa guida descrive le funzionalità per la creazione di un'applicazione Xamarin.Mac accessibile."
ms.topic: article
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 0117364f02302add1f8788de1a79e4c4210fd07b
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="accessibility-on-macos"></a>Accessibilità in macOS

Questa pagina viene descritto come utilizzare l'API di accesso facilitato macOS per compilare le applicazioni in base al [elenco di controllo di accesso facilitato](~/cross-platform/app-fundamentals/accessibility.md).
Vedere il [accessibilità Android](~/android/app-fundamentals/accessibility.md) e [iOS accessibilità](~/ios/app-fundamentals/accessibility.md) pagine per altri API della piattaforma.

Per comprendere il funzionamento dell'API di accessibilità in macOS (noto in OS X), esaminare prima i [il modello di accessibilità di OS X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html).

## <a name="describing-ui-elements"></a>Che descrive gli elementi dell'interfaccia utente

AppKit utilizza il `NSAccessibility` protocollo per l'esposizione di API che consentono di rendere accessibile l'interfaccia utente. Ciò include un comportamento predefinito che tenta di impostare valori significativi per le proprietà di accessibilità, ad esempio l'impostazione di un pulsante `AccessibilityLabel`. L'etichetta è in genere una singola parola o frase breve che descrive il controllo o una vista.

### <a name="storyboard-files"></a>File di storyboard

Xamarin.Mac utilizza il generatore di interfaccia Xcode per modificare i file di storyboard.
Informazioni sull'accessibilità possono essere modificate nella **controllo di identità** quando un controllo è selezionato nella finestra di progettazione (come illustrato nella schermata seguente):

[![Aggiunta di accessibilità in interfaccia generatore del Xcode](accessibility-images/xcode.png "aggiunta di accessibilità in Generatore del Xcode di interfaccia")](accessibility-images/xcode-large.png)

### <a name="code"></a>Codice

Xamarin.Mac non attualmente espone come `AccessibilityLabel` setter.  Aggiungere il seguente metodo helper per impostare l'etichetta di accessibilità:

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

Questo metodo può quindi essere utilizzato nel codice, come illustrato:

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

Il `AccessibilityHelp` è di proprietà per una spiegazione della visualizzazione controlli o e devono essere aggiunti solo quando l'etichetta potrebbe non fornire informazioni sufficienti. Il testo della Guida ancora mantenere più corte possibili, per esempio "Elimina il documento".

Alcuni elementi dell'interfaccia utente non sono rilevanti per l'accesso accessibile (ad esempio un'etichetta accanto a un input con etichetta di accessibilità e della Guida in linea).
In questi casi, impostare `AccessibilityElement = false` in modo che questi controlli o le viste verranno ignorate da lettura dello schermo o altri strumenti di accessibilità.

Apple fornisce [facilitato](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html) che illustra le procedure consigliate per il testo di etichette e la Guida di accessibilità.

## <a name="custom-controls"></a>Controlli personalizzati

Fare riferimento a Apple [linee guida per i controlli personalizzati accessibile](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html) per informazioni dettagliate sui passaggi aggiuntivi necessari.

## <a name="testing-accessibility"></a>Verifica dell'accessibilità

macOS fornisce un **controllo di accesso facilitato** che consente di testare la funzionalità di accessibilità. Il controllo è incluso in Xcode.

La prima volta che viene avviata, il **controllo di accesso facilitato** richiederanno l'autorizzazione per controllare il computer tramite accesso facilitato:

![Controllo accessibilità richiede l'autorizzazione a eseguire](accessibility-images/accessibility-inspector-1.png "che richiede l'autorizzazione per eseguire controllo di accesso facilitato")

Sbloccare la schermata Impostazioni (se richiesto, in basso a sinistra) e i segni di graduazione di **controllo di accesso facilitato**:

![Schermata di impostazioni per abilitare il controllo di accesso facilitato](accessibility-images/accessibility-inspector-2.png "schermata delle impostazioni per abilitare il controllo di accesso facilitato")

Una volta abilitato, il controllo viene visualizzato come una finestra mobile che può essere spostata sullo schermo. La schermata riportata di seguito viene illustrato il controllo in esecuzione accanto a un'app di esempio Mac. Quando il cursore viene spostato sopra la finestra, il controllo Mostra tutte le proprietà di ogni controllo accessibile:

[![Esempio di esecuzione di controllo di accesso facilitato](accessibility-images/accessibility-example.png "esecuzione di esempio di controllo di accesso facilitato")](accessibility-images/accessibility-example-large.png)

Per ulteriori informazioni, leggere la [testare l'accessibilità della Guida di OS X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html).



## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità multipiattaforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Accessibilità Mac](https://www.apple.com/accessibility/mac/)
