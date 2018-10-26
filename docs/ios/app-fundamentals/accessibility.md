---
title: Accessibilità in iOS
description: Questo documento descrive l'accessibilità in iOS, discusse le varie proprietà e funzionalità che possono essere usate per rendere utilizzabile l'applicazione da tutti gli utenti possibili.
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/18/2016
ms.openlocfilehash: aa3e15797ae1dac621ea8a78345044be1387ebaa
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108899"
---
# <a name="accessibility-on-ios"></a>Accessibilità in iOS

Questa pagina descrive come usare l'API di accessibilità di iOS per compilare le App in base al [elenco di controllo di accesso facilitato](~/cross-platform/app-fundamentals/accessibility.md).
Vedere il [accessibilità Android](~/android/app-fundamentals/accessibility.md) e [accessibilità OS X](~/mac/app-fundamentals/accessibility.md) pagine per gli altri API della piattaforma.

## <a name="describing-ui-elements"></a>Che descrive gli elementi dell'interfaccia utente

iOS fornisce il `AccessibilityLabel` e `AccessibilityHint` lettore per rendere più accessibili i controlli di schermata delle proprietà per gli sviluppatori di aggiungere testo descrittivo che può essere usato da di Voice over. I controlli possono essere contrassegnati anche con uno o più tratti che forniscono ulteriore contesto nelle modalità accessibile.

Alcuni controlli potrebbero non devono essere accessibili (ad esempio, un'etichetta di un input di testo o un'immagine è puramente decorativa) – il `IsAccessibilityElement` consente di disabilitare l'accessibilità in questi casi.

**Finestra di progettazione dell'interfaccia utente**

Il **riquadro delle proprietà** contiene una sezione di accessibilità che consente a queste impostazioni da modificare quando viene selezionato un controllo in iOS Designer dell'interfaccia utente:

![](accessibility-images/ios-designer-sml.png "Impostazioni di accessibilità")

**C#**

Queste proprietà possono anche essere impostate direttamente nel codice:

```csharp
usernameInput.AccessibilityLabel = "Search";
usernameInput.Hint = "Press Enter after typing to search employee list";
someLabel.IsAccessibilityElement = false;
displayOnlyText.AccessibilityTraits = UIAccessibilityTrait.Header | UIAccessibilityTrait.Selected;
```

### <a name="what-is-accessibilityidentifier"></a>Che cos'è AccessibilityIdentifier?

Il `AccessibilityIdentifier` consente di impostare una chiave univoca che può essere utilizzata per fare riferimento a elementi dell'interfaccia utente tramite l'API UIAutomation.

Il valore di `AccessibilityIdentifier` è parlata o mai visualizzato all'utente.

<a name="postnotification" />

## <a name="postnotification"></a>PostNotification

Il `UIAccessibility.PostNotification` metodo consente gli eventi da generare per l'utente di fuori di interazione diretta (ad esempio, quando si interagisce con un controllo specifico).

### <a name="announcement"></a>Annuncio

Un annuncio può essere inviato dal codice per informare l'utente che alcuni Stati sono stato modificato (ad esempio, è stata completata un'operazione in background). Ciò può essere accompagnato da un'indicazione visiva dell'interfaccia utente:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>LayoutChanged

Il `LayoutChanged` annuncio viene usato quando il layout della schermata:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```


## <a name="accessibility-and-localization"></a>Accessibilità e localizzazione

Proprietà di accessibilità, ad esempio l'etichetta e l'hint può essere localizzati semplicemente come altro testo dell'interfaccia utente.

**MainStoryboard.strings**

Se l'interfaccia utente è disposto in uno storyboard, è possibile fornire le traduzioni per le proprietà di accessibilità nello stesso modo delle altre proprietà. Nell'esempio seguente, un `UITextField` ha un **ID di localizzazione** di `Pqa-aa-ury` e due proprietà di accessibilità da impostare in spagnolo:

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

Questo file potrebbe essere inserito nel **es.lproj** directory per il contenuto spagnolo.

**Localizable.Strings**

In alternativa, è possibile aggiungere le traduzioni per il **Localizable.strings** file (ad esempio la directory del contenuto localizzata **es.lproj** per lo spagnolo):

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

Queste conversioni possono essere usate in C# tramite il `LocalizedString` metodo:

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

Fare riferimento al [Guida alla localizzazione iOS](~/ios/app-fundamentals/localization/index.md) per altri dettagli sulla localizzazione del contenuto.

<a name="testing" />

## <a name="testing-accessibility"></a>Verifica dell'accessibilità

Voice over è abilitata nel **le impostazioni** app passando a **generale > Accessibilità > VoiceOver**:

![](accessibility-images/settings-sml.png "Impostare la velocità di pronuncia")

Il **accessibilità** schermata include inoltre impostazioni per zoom, dimensione del testo, le opzioni di & contrasto dei colori, le impostazioni di riconoscimento vocale e altre opzioni di configurazione.

Seguire queste [istruzioni di VoiceOver](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) per testare l'accessibilità nei dispositivi iOS.


## <a name="simulator-testing"></a>Test del simulatore

Durante il test nel simulatore, il **controllo di accesso facilitato** è disponibile per verificare le proprietà di accessibilità e gli eventi siano configurati correttamente. Attivare il controllo nel **le impostazioni** app passando a **generale > Accessibilità > controllo di accesso facilitato**:

![](accessibility-images/settings-inspector-sml.png "Abilitare controllo di accesso facilitato")

Una volta abilitata, la finestra di controllo viene posizionato su schermata iOS in qualsiasi momento.
Ecco un esempio dell'output quando è selezionata una riga della visualizzazione tabella, si noti che il **etichetta** contiene una frase che fornisce il contenuto della riga e che si è "done" (Internet Explorer. il segno di graduazione è visibile):

![](accessibility-images/tableview-a11y-sml.png "Utilizzo di controllo di accesso facilitato")

Anche se il controllo è visibile, usare l'icona "X" in alto a sinistra per temporaneamente mostrare e nascondere il controllo overlay e abilitare o disabilitare le impostazioni di accessibilità.



## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità multipiattaforma](~/cross-platform/app-fundamentals/accessibility.md)
- [iOS accessibilità (Apple)](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [iOS Voice over](http://www.apple.com/accessibility/ios/voiceover/)
