---
title: Utilizzo di watchOS localizzazione in Xamarin
description: Questo documento descrive come localizzare le app watchOS compilate con Xamarin. Vengono illustrati le app watch, le estensioni di espressioni di controllo, le stringhe nel codice, creare uno storyboard testo, test e altro ancora.
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 008858f987a5a3f83b518b98a0647ac5a68b0672
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768732"
---
# <a name="working-with-watchos-localization-in-xamarin"></a>Utilizzo di watchOS localizzazione in Xamarin

_Adattamento delle tue App watchOS per più lingue_

![](localization-images/both-languages-sml.png "Visualizzare il contenuto localizzato di Apple Watch")

App watchOS sono localizzate usando i metodi standard di iOS:

- Usando **ID di localizzazione** su elementi dello storyboard,
- **.Strings** file associati con lo storyboard, e
- **Localizable.Strings** file per il testo usato nel codice.

Gli storyboard predefinite e le risorse si trovano in un **Base** directory e le conversioni specifiche della lingua e altre risorse vengono archiviate in **.lproj** le directory.
iOS e OS Watch userà automaticamente selezione della lingua dell'utente per caricare le stringhe corrette e le risorse.

Perché un'app per Apple Watch è costituita da due parti - App e guardare estensione - stringa localizzata sono necessarie risorse in due posizioni, a seconda di come vengono usati.

Il testo localizzato e le risorse saranno *diversi* nell'app watch e l'estensione di espressioni di controllo.

## <a name="watch-app"></a>App Watch

L'app watch contiene lo storyboard che descrive l'interfaccia utente dell'app. Tutti i controlli (ad esempio `Label` e `Image`) che dispone di supportare la localizzazione un **ID di localizzazione**.

Ogni specifico della lingua **.lproj** directory deve contenere **.strings** i file con le traduzioni per ogni elemento (utilizzando il **ID di localizzazione**), nonché immagini fa riferimento lo storyboard.

## <a name="watch-extension"></a>Estensione di espressioni di controllo

L'estensione di espressioni di controllo è in cui viene eseguito il codice dell'app. Qualsiasi testo che viene visualizzato all'utente dal codice deve essere localizzato dell'estensione e non nell'app watch.

L'estensione deve inoltre contenere specifiche del linguaggio **.lproj** directory, ma la **.strings** file richiedono solo le traduzioni per il testo che viene usato nel codice.

## <a name="globalizing-the-watch-solution"></a>La soluzione di espressioni di controllo di globalizzazione

Globalizzazione è il processo di creazione di un'applicazione localizzabile.
Per le app watch ciò significa che la progettazione di storyboard con testo-lunghezze diverse presente, verificare ogni layout della schermata viene modificata in modo appropriato in base alla quale il testo viene visualizzato. È necessario anche assicurarsi che tutte le stringhe di cui viene fatto riferimento nel codice dell'estensione delle espressioni di controllo possono essere convertite utilizzando il `LocalizedString` (metodo).

### <a name="watch-app"></a>App Watch

Per impostazione predefinita l'app watch non è configurato per la localizzazione. È necessario spostare il file di storyboard predefinite e creare alcune altre directory per le traduzioni:

1. Creare **Base.lproj** directory e spostare le **Interface.storyboard** al suo interno.

2. Creare le directory  **Language>.lprojperognilinguachesivuolesupportare\<** .

3. Il **.lproj** directory devono contenere un' **Interface.strings** file di testo (il nome del file deve corrispondere a quello del storboard). È facoltativamente possibile inserire tutte le immagini che necessitano di localizzazione in tali directory.

Il progetto dell'app watch aspetto simile al seguente dopo aver apportate queste modifiche (solo inglese e spagnola lingua sono stati aggiunti file):

  ![](localization-images/watchapp-solution.png "Il progetto dell'app watch con i file di lingua inglese e spagnolo")

#### <a name="storyboard-text"></a>Testo di uno storyboard

Quando si modifica lo storyboard, selezionare ogni elemento e si noti che il **ID di localizzazione** che viene visualizzato nella **proprietà** riempimento:

  [![](localization-images/storyboard-sml.png "L'ID di localizzazione che viene visualizzato nel riquadro delle proprietà")](localization-images/storyboard.png#lightbox)

Nel **Base.lproj** cartella, creare coppie chiave-valore, come illustrato di seguito, in cui la chiave viene formata dalle **ID di localizzazione** e il nome di una proprietà sul controllo, aggiunti da un punto (`.`).

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

Si noti che in questo esempio che un **ID di localizzazione** può essere una semplice stringa numerica (ad es. "0", "1", e così via) o una stringa più complessa (ad esempio "AgC eL Hgc"). `Label` i controlli dispongono di un `Text` proprietà e `Button`hanno un `Title` proprietà, che si riflette in modo i valori localizzati sono impostati - assicurarsi di usare il nome della proprietà lettere minuscole, come illustrato nell'esempio precedente.

Quando lo storyboard deve essere visualizzato nelle espressioni di controllo, i valori corretti automaticamente estratte e visualizzati in base alla lingua selezionata dall'utente.

#### <a name="storyboard-images"></a>Immagini di storyboard

La soluzione di esempio include anche un **gradient@2x.png** immagine in ogni cartella della lingua. Questa immagine può essere diversa per ogni lingua (ad es. potrebbero avere già implementato il testo che richiede la conversione, o usare localizzato visualizzato).

È sufficiente impostare l'immagine **immagine** proprietà storyboard e l'immagine corretta verrà visualizzata nella finestra Espressioni di controllo in base alla lingua selezionata dall'utente.

![](localization-images/storyboard-image.png "Impostare le immagini di proprietà dell'immagine dello storyboard")

Nota: poiché tutte le espressioni di controllo di Apple hanno display Retina, solo il **@2x** versione dell'immagine è necessaria. Non è necessario specificare **@2x** nello storyboard.

### <a name="watch-extension"></a>Estensione di espressioni di controllo

L'estensione di espressioni di controllo richiede una struttura di directory simile a supportare la localizzazione, tuttavia, non è Nessuno storyboard. Le stringhe localizzate nell'estensione sono solo quelli a cui fanno riferimento C# codice.

![](localization-images/watchextension-solution.png "La struttura di directory di estensione di espressioni di controllo per supportare la localizzazione")

#### <a name="strings-in-code"></a>Stringhe nel codice

Il **Localizable.strings** file ha una struttura leggermente diversa rispetto a se è stato associato a uno storyboard. In questo caso è possibile scegliere qualsiasi stringa di "chiave"; Apple consiglia di usare una chiave che riflette il testo effettivo che verrà visualizzato nella lingua predefinita:

```csharp
"Breakfast time" = "Breakfast time!"; // morning
"Lunch time" = "Lunch time!"; // midday
"Dinner time" = "Dinner time!"; // evening
"Bed time" = "Bed time!"; // night
```

Il `NSBundle.MainBundle.LocalizedString` metodo viene utilizzato per risolvere le stringhe nelle rispettive controparti tradotte, come illustrato nel codice seguente.

```csharp
var display = "Breakfast time";
var localizedDisplay =
  NSBundle.MainBundle.LocalizedString (display, comment:"greeting");
displayText.SetText (localizedDisplay);
```

#### <a name="images-in-code"></a>Immagini nel codice

Le immagini che vengono popolate dal codice possono essere impostate in due modi.

1. È possibile modificare un `Image` controllo impostando il relativo valore sul nome di stringa di un'immagine già esistente nell'app Watch, ad esempio

    ```csharp
    displayImage.SetImage("gradient"); // image in Watch App (as shown above)
    ```

2. È possibile spostare un'immagine dall'estensione per l'espressione di controllo usando `FromBundle` e l'app verrà scelto automaticamente l'immagine corretta per la selezione della lingua dell'utente. Nella soluzione di esempio è disponibile un'immagine **language@2x.png** in ogni lingua cartella che viene visualizzato in `DetailController` usando il codice seguente:

    ```csharp
    using (var image = UIImage.FromBundle ("language")) {
        displayImage.SetImage (image);
    }
    ```

    Si noti che non è necessario specificare **@2x** quando si fa riferimento al nome file dell'immagine.

Il secondo metodo è applicabile se si scarica un'immagine da un server remoto per eseguire il rendering in watch; anche Tuttavia in questo caso è necessario assicurarsi che l'immagine che è scaricare è localizzato correttamente in base alle preferenze dell'utente.

## <a name="localization"></a>Localizzazione

Dopo aver configurato la soluzione, funzioni di conversione sarà necessario elaborare le **.strings** file e immagini per ogni lingua si desidera supportare.

È possibile creare tanti **.lproj** le directory, è necessitano (uno per ogni lingua supportata). Essi vengono denominati usando codici di lingua, ad esempio **en**, **es**, **Germania**, **Giappone**, **pt-BR**, e così via (per la lingua inglese Spagnolo, tedesco, giapponese e portoghese (Brasile) rispettivamente).

L'esempio allegato Usa le traduzioni (generati dal computer) per illustrare come localizzare un'app watchOS.

### <a name="watch-app"></a>App Watch

Questi valori vengono usati per convertire l'interfaccia utente definita nello storyboard dell'app per le espressioni di controllo. Il *key* valore è una combinazione di ogni controllo di storyboard **ID di localizzazione** e la proprietà tradotta.

È consigliabile aggiungere commenti che contiene il testo originale del file in modo che i traduttori sapere quale deve essere la conversione.

#### <a name="eslprojinterfacestrings"></a>es.lproj/Interface.strings

Le stringhe lingua spagnola (traduzione) per lo storyboard sono illustrate di seguito. È utile aggiungere commenti a ogni riga, perché è difficile sapere cosa il **ID di localizzazione** fa riferimento in caso contrario:

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>Estensione di espressioni di controllo

Questi valori vengono usati nel codice per convertire le informazioni prima di essere visualizzato all'utente. Il *chiave* venga selezionata dallo sviluppatore mentre si scrive codice e in genere contiene la stringa effettiva da tradurre.

#### <a name="eslprojlocalizablestrings-file"></a>file es.lproj/Localizable.strings

Le stringhe di linguaggio Spansish (traduzione):

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>Test

Il metodo per modificare le preferenze di lingua è diverso tra il simulatore e dispositivi fisici.

### <a name="simulator"></a>Simulatore

Nel simulatore, selezionare la lingua da testare con iOS **impostazioni** app (l'icona grigia gears nella schermata principale del simulatore).

  ![](localization-images/sim-settings-sml.png "Impostazioni localizzazione dell'app impostazioni iOS")

### <a name="watch-device"></a>Dispositivo di espressioni di controllo

Durante il test con un'espressione di controllo, cambiare lingua dell'orologio nel **Apple Watch** app su iPhone associato.

  ![](localization-images/phone-settings-sml.png "Modificare la lingua dell'orologio nell'app Apple Watch su iPhone associato")

## <a name="related-links"></a>Collegamenti correlati

- [WatchLocalization (esempio)](https://developer.xamarin.com//samples/monotouch/watchOS/WatchLocalization/)
