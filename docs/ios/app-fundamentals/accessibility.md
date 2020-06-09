---
title: Accessibilità in iOS
description: Questo documento descrive l'accessibilità in iOS, illustrando le varie proprietà e funzionalità che è possibile usare per rendere l'applicazione utilizzabile per il maggior numero possibile di utenti.
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/18/2016
ms.openlocfilehash: 2259566fc6342a40a8c0a94bacd1c146b6509d52
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574158"
---
# <a name="accessibility-on-ios"></a>Accessibilità in iOS

Questa pagina descrive come usare le API di accessibilità iOS per creare app in base all' [elenco di controllo di accessibilità](~/cross-platform/app-fundamentals/accessibility.md).
Vedere le pagine di accessibilità [Android](~/android/app-fundamentals/accessibility.md) e [OS X](~/mac/app-fundamentals/accessibility.md) per altre API della piattaforma.

## <a name="describing-ui-elements"></a>Descrizione degli elementi dell'interfaccia utente

iOS fornisce le `AccessibilityLabel` `AccessibilityHint` proprietà e per gli sviluppatori per aggiungere testo descrittivo che può essere usato dall'Reader per la lettura dello schermo di VoiceOver per rendere i controlli più accessibili. I controlli possono anche essere contrassegnati con uno o più tratti che forniscono un contesto aggiuntivo in modalità accessibili.

È possibile che alcuni controlli non debbano essere accessibili, ad esempio un'etichetta su un input di testo o un'immagine puramente decorativa, `IsAccessibilityElement` per disabilitare l'accessibilità in tali casi.

**Finestra di progettazione dell'interfaccia utente**

Il **riquadro delle proprietà** contiene una sezione di accessibilità che consente di modificare queste impostazioni quando si seleziona un controllo nella finestra di progettazione dell'interfaccia utente di iOS:

![](accessibility-images/ios-designer-sml.png "Accessibility Settings")

**C#**

Queste proprietà possono anche essere impostate direttamente nel codice:

```csharp
usernameInput.AccessibilityLabel = "Search";
usernameInput.Hint = "Press Enter after typing to search employee list";
someLabel.IsAccessibilityElement = false;
displayOnlyText.AccessibilityTraits = UIAccessibilityTrait.Header | UIAccessibilityTrait.Selected;
```

### <a name="what-is-accessibilityidentifier"></a>Che cos'è AccessibilityIdentifier?

`AccessibilityIdentifier`Viene usato per impostare una chiave univoca che può essere usata per fare riferimento agli elementi dell'interfaccia utente tramite l'API UIAutomation.

Il valore di `AccessibilityIdentifier` non viene mai pronunciato o visualizzato all'utente.

<a name="postnotification"></a>

## <a name="postnotification"></a>Postnotifica

Il `UIAccessibility.PostNotification` metodo consente la generazione di eventi all'esterno dell'interazione diretta, ad esempio quando interagiscono con un controllo specifico.

### <a name="announcement"></a>Annuncio

È possibile inviare un annuncio dal codice per informare l'utente che è stato modificato uno stato, ad esempio un'operazione in background completata. Questo può essere accompagnato da un'indicazione visiva nell'interfaccia utente:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>LayoutChanged

L' `LayoutChanged` annuncio viene usato quando il layout della schermata:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```

## <a name="accessibility-and-localization"></a>Accessibilità e localizzazione

Le proprietà di accessibilità come l'etichetta e l'hint possono essere localizzate proprio come altro testo nell'interfaccia utente.

**File mainstoryboard. Strings**

Se l'interfaccia utente è disposta in uno storyboard, è possibile fornire le traduzioni per le proprietà di accessibilità in modo analogo alle altre proprietà. Nell'esempio seguente un oggetto `UITextField` ha un **ID di localizzazione** `Pqa-aa-ury` e due proprietà di accessibilità impostate in spagnolo:

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

Questo file verrà inserito nella directory **es. lproj** per il contenuto spagnolo.

**Localizzabile. stringhe**

In alternativa, è possibile aggiungere le traduzioni al file con **estensione Strings localizzabile** nella directory del contenuto localizzato, ad esempio **es. lproj** per spagnolo):

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

Queste traduzioni possono essere usate in C# tramite il `LocalizedString` Metodo:

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

Per ulteriori informazioni sulla localizzazione del contenuto, vedere la [Guida alla localizzazione di iOS](~/ios/app-fundamentals/localization/index.md) .

<a name="testing"></a>

## <a name="testing-accessibility"></a>Test dell'accessibilità

VoiceOver è abilitato nell'app **Impostazioni** passando a **generale > accessibilità > VoiceOver**:

![](accessibility-images/settings-sml.png "Setting the speaking rate")

Nella schermata di **accessibilità** sono inoltre disponibili impostazioni per lo zoom, le dimensioni del testo, il colore & opzioni di contrasto, le impostazioni vocali e altre opzioni di configurazione.

Seguire queste [istruzioni VoiceOver](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) per testare l'accessibilità nei dispositivi iOS.

## <a name="simulator-testing"></a>Test del simulatore

Quando si esegue il test nel simulatore, il **controllo di accessibilità** è disponibile per facilitare la verifica delle proprietà di accessibilità ed eventi configurati correttamente. Accendere il controllo nell'app **Impostazioni** passando a **generale > accessibilità > controllo di accessibilità**:

![](accessibility-images/settings-inspector-sml.png "Enable Accessibility Inspector")

Una volta abilitata, la finestra di controllo passa il mouse sulla schermata iOS sempre.
Di seguito è riportato un esempio di output quando viene selezionata una riga della vista tabella. si noti che l'etichetta contiene una frase che fornisce il contenuto della riga e anche che è "Done" (ad esempio, il segno di **indicizzazione** è visibile):

![](accessibility-images/tableview-a11y-sml.png "Using Accessibility Inspector")

Quando il controllo è visibile, usare l'icona "X" nella parte superiore sinistra per visualizzare e nascondere temporaneamente la sovrimpressione e abilitare/disabilitare le impostazioni di accessibilità.

## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità multipiattaforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Accessibilità iOS (Apple)](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [VoiceOver iOS](https://www.apple.com/accessibility/ios/voiceover/)
