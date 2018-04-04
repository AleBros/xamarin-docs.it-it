---
title: Accessibilità in iOS
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/18/2016
ms.openlocfilehash: af28d0866337c769d1d6102317fc186c49ec259e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="accessibility-on-ios"></a>Accessibilità in iOS

Questa pagina viene descritto come utilizzare le API di accesso facilitato iOS consente di creare App in base al [elenco di controllo di accesso facilitato](~/cross-platform/app-fundamentals/accessibility.md).
Consultare la [accessibilità Android](~/android/app-fundamentals/accessibility.md) e [accessibilità OS X](~/mac/app-fundamentals/accessibility.md) pagine per altri API della piattaforma.

## <a name="describing-ui-elements"></a>Che descrive gli elementi dell'interfaccia utente

iOS fornisce il `AccessibilityLabel` e `AccessibilityHint` proprietà agli sviluppatori di aggiungere un testo descrittivo che può essere usato per il Voice over screen reader per rendere più accessibili i controlli. Controlli possono anche essere contrassegnati con uno o più caratteristiche che forniscono ulteriore contesto nelle modalità accessibile.

Alcuni controlli potrebbero non devono essere accessibili (ad esempio, un'etichetta di un input di testo o un'immagine è puramente decorativa) – il `IsAccessibilityElement` consente di disabilitare l'accessibilità in questi casi.

**Finestra di progettazione dell'interfaccia utente**

Il **proprietà riempimento** contiene una sezione di accessibilità che consente di queste impostazioni essere modificato quando un controllo è selezionato nella finestra di progettazione dell'interfaccia utente iOS:

![](accessibility-images/ios-designer-sml.png "Impostazioni di accessibilità")

**C#**

Queste proprietà possono essere impostate anche direttamente nel codice:

```csharp
usernameInput.AccessibilityLabel = "Search";
usernameInput.Hint = "Press Enter after typing to search employee list";
someLabel.IsAccessibilityElement = false;
displayOnlyText.AccessibilityTraits = UIAccessibilityTrait.Header | UIAccessibilityTrait.Selected;
```

### <a name="what-is-accessibilityidentifier"></a>Che cos'è AccessibilityIdentifier?

Il `AccessibilityIdentifier` utilizzato per impostare una chiave univoca che può essere usata per fare riferimento a elementi dell'interfaccia utente tramite l'API UIAutomation.

Il valore di `AccessibilityIdentifier` mai pronunciata sia visualizzato all'utente.

<a name="postnotification" />

## <a name="postnotification"></a>PostNotification

Il `UIAccessibility.PostNotification` metodo consente gli eventi da generare per l'utente di fuori di interazione diretta (ad esempio, quando si interagisce con un controllo specifico).

### <a name="announcement"></a>Annuncio

Per informare l'utente che ha modificato lo stato di alcuni (ad esempio è stata completata un'operazione in background), un annuncio può essere inviato dal codice. Questo potrebbe essere accompagnato da un'indicazione visiva dell'interfaccia utente:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>LayoutChanged

Il `LayoutChanged` annuncio viene utilizzato quando il layout della schermata:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```


## <a name="accessibility-and-localization"></a>Localizzazione e accessibilità

Proprietà di accessibilità, come l'etichetta e l'hint può essere localizzate solo come testo nell'interfaccia utente.

**MainStoryboard.strings**

Se l'interfaccia utente è disposto in uno storyboard, è possibile specificare traduzioni per le proprietà di accessibilità esattamente come altre proprietà. Nell'esempio seguente, un `UITextField` ha un **ID di localizzazione** di `Pqa-aa-ury` e due proprietà di accessibilità viene impostata in spagnolo:

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

Questo file verrà inserito nella **es.lproj** directory per il contenuto spagnolo.

**Localizable.strings**

In alternativa, è possibile aggiungere le traduzioni per il **Localizable.strings** file (ad es la directory del contenuto localizzato. **es.lproj** per lo spagnolo):

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

Queste conversioni sono utilizzabili in c# tramite la `LocalizedString` metodo:

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

Vedere il [iOS localizzazione Guida](~/ios/app-fundamentals/localization/index.md) per ulteriori informazioni sulla localizzazione contenuto.

<a name="testing" />

## <a name="testing-accessibility"></a>Verifica dell'accessibilità

Voice over è abilitata nel **impostazioni** app passando a **generale > Accessibilità > Voice over**:

![](accessibility-images/settings-sml.png "Impostare la velocità di pronuncia")

Il **accessibilità** schermata fornisce anche le impostazioni per lo zoom, la dimensione del testo, opzioni colore e contrasto, le impostazioni di riconoscimento vocale e altre opzioni di configurazione.

Attenersi alla seguente [istruzioni Voice over](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) per testare l'accessibilità nei dispositivi iOS.


## <a name="simulator-testing"></a>Simulatore test

Durante il test nel simulatore di **controllo di accesso facilitato** è disponibile per verificare le proprietà di accessibilità e gli eventi siano configurati correttamente. Attivare il controllo nel **impostazioni** app passando a **generale > Accessibilità > controllo di accesso facilitato**:

![](accessibility-images/settings-inspector-sml.png "Abilitare il controllo di accesso facilitato")

Una volta abilitato, la finestra di controllo viene spostato su schermo iOS in qualsiasi momento.
Di seguito è riportato un esempio dell'output quando è selezionata una riga di vista della tabella, si noti il **etichetta** contiene una frase che fornisce il contenuto della riga e che si è "done" (ie. i segni di graduazione è visibile):

![](accessibility-images/tableview-a11y-sml.png "Tramite il controllo di accesso facilitato")

Mentre il controllo è visibile, utilizzare l'icona "X" nella parte superiore sinistra temporaneamente mostrare e nascondere la sovrapposizione e abilitare o disabilitare le impostazioni di accessibilità.



## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità multipiattaforma](~/cross-platform/app-fundamentals/accessibility.md)
- [iOS accessibilità (mela)](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [iOS VoiceOver](http://www.apple.com/accessibility/ios/voiceover/)
