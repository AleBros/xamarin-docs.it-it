---
title: Utilizzo di localizzazione
description: Adattamento App watchOS per più lingue
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: c765005491f55a1bdcadb1bc5aea97f693dc4570
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-localization"></a>Utilizzo di localizzazione

_Adattamento App watchOS per più lingue_

![](localization-images/both-languages-sml.png "Apple Watch visualizzare il contenuto localizzato")

App watchOS sono localizzate mediante i metodi standard iOS:

- Utilizzando **ID di localizzazione** per gli elementi di uno storyboard,
- **.Strings** i file associati, lo storyboard e
- **Localizable.Strings** file per il testo utilizzato nel codice.

Gli storyboard predefinito e le risorse si trovano un **Base** directory e traduzioni specifiche della lingua e altre risorse vengono archiviate in **.lproj** directory.
iOS e OS Watch utilizzeranno automaticamente selezione della lingua dell'utente per caricare le stringhe corrette e le risorse.

Poiché un'app di Apple Watch è costituita da due parti - App di espressioni di controllo e l'estensione di espressioni di controllo - stringa localizzata, sono necessarie risorse in due posizioni, a seconda della modalità di utilizzo.

Il testo localizzato e di risorse *diversi* l'applicazione di espressioni di controllo e l'estensione di espressioni di controllo.

## <a name="watch-app"></a>Applicazione delle espressioni di controllo

L'applicazione di espressioni di controllo contiene lo storyboard che descrive l'interfaccia utente dell'applicazione. Tutti i controlli (ad esempio `Label` e `Image`) che dispone di supportare la localizzazione un **ID di localizzazione**.

Ogni specifico della lingua **.lproj** directory deve contenere **.strings** i file con le traduzioni per ogni elemento (usando il **ID di localizzazione**), nonché le immagini riferimento dello storyboard.

## <a name="watch-extension"></a>Estensione delle espressioni di controllo

L'estensione di espressioni di controllo è in cui viene eseguito il codice dell'app. Qualsiasi testo che viene visualizzato all'utente dal codice deve essere localizzato dell'estensione e non nell'applicazione di espressioni di controllo.

L'estensione deve inoltre contenere specifiche della lingua **.lproj** directory, ma la **.strings** file richiedono solo le traduzioni per il testo che viene utilizzato nel codice.

## <a name="globalizing-the-watch-solution"></a>La soluzione di espressioni di controllo di globalizzazione

La globalizzazione è il processo di creazione di un'applicazione localizzabile.
Per le app di espressioni di controllo ciò significa che lo storyboard con lunghezze di testo diversi in considerazione di progettazione, ogni layout della schermata di garantire regola in modo appropriato a seconda di quale testo viene visualizzato. È inoltre necessario verificare tutte le stringhe a cui fa riferimento nel codice dell'estensione delle espressioni di controllo possono essere convertite tramite il `LocalizedString` metodo.

### <a name="watch-app"></a>Applicazione delle espressioni di controllo

Per impostazione predefinita l'applicazione di espressioni di controllo non è configurato per la localizzazione. È necessario spostare il file di storyboard predefinito e creare alcune altre directory per le traduzioni:

1. Creare **Base.lproj** directory e spostamento di **Interface.storyboard** al suo interno.

2. Creare  **<language>.lproj** directory per ogni lingua che si desidera supportare.

3. Il **.lproj** directory devono contenere un **Interface.strings** file di testo (il nome del file deve corrispondere a quello del storboard). Facoltativamente, è possibile inserire tutte le immagini che richiedono la localizzazione in tali directory.

Una volta apportate queste modifiche (solo inglese e spagnolo lingua sono stati aggiunti file), il progetto di app di espressioni di controllo è simile al seguente:

  ![](localization-images/watchapp-solution.png "Il progetto di app di espressioni di controllo con i file di lingua inglese e spagnolo")

#### <a name="storyboard-text"></a>Testo di storyboard

Quando si modifica lo storyboard, selezionare ogni elemento e notare il **ID di localizzazione** che compare nella **proprietà** riempimento:

  [![](localization-images/storyboard-sml.png "L'ID di localizzazione che viene visualizzato nel riquadro proprietà")](localization-images/storyboard.png#lightbox)

Nel **Base.lproj** cartella, creare le coppie chiave-valore, come illustrato di seguito, dove la chiave è costituita dal **ID di localizzazione** e aggiunti a un nome di una proprietà sul controllo, da un punto (`.`).

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

Si noti che in questo esempio che un **ID di localizzazione** può essere una semplice stringa numerica (ad es. "0", "1", e così via) o una stringa più complessa (ad esempio "AgC eL Hgc"). `Label` i controlli hanno un `Text` proprietà e `Button`hanno un `Title` proprietà, che viene riflesso nel modo in cui vengono impostati i relativi valori localizzate - assicurarsi di usare il nome di proprietà lettere minuscole, come illustrato nell'esempio precedente.

Quando viene eseguito il rendering dello storyboard nelle espressioni di controllo, i valori corretti verranno automaticamente estratto e visualizzati in base alla lingua selezionata dall'utente.

#### <a name="storyboard-images"></a>Immagini di storyboard

La soluzione di esempio include anche un **gradient@2x.png** immagine in ogni cartella della lingua. Questa immagine può essere diversa per ogni lingua (ad es. può avere incorporato testo che richiede la conversione o utilizzare localizzato visualizzato).

L'immagine è sufficiente impostare **immagine** verrà visualizzata come proprietà nello storyboard e l'immagine corretta nell'orologio in base alla lingua selezionata dall'utente.

![](localization-images/storyboard-image.png "Imposta le immagini di proprietà dell'immagine dello storyboard")

Nota: poiché dispongono di tutte le espressioni di controllo di Apple Retina viene visualizzata solo la **@2x** è necessaria una versione dell'immagine. Non è necessario specificare **@2x** dello storyboard.

### <a name="watch-extension"></a>Estensione delle espressioni di controllo

L'estensione di espressioni di controllo richiede una simile struttura di directory per supportare la localizzazione, tuttavia, non è Nessuno storyboard. Le stringhe localizzate nell'estensione sono solo quelli a cui fa riferimento il codice c#.

![](localization-images/watchextension-solution.png "La struttura di directory estensione espressioni di controllo per supportare la localizzazione")

#### <a name="strings-in-code"></a>Stringhe nel codice

Il **Localizable.strings** file ha una struttura leggermente diversa rispetto a se è associata a uno storyboard. In questo caso è possibile scegliere qualsiasi stringa "chiave"; Apple consiglia di utilizzare una chiave che riflette il testo effettivo che verrà visualizzato nella lingua predefinita:

```csharp
"Breakfast time" = "Breakfast time!"; // morning
"Lunch time" = "Lunch time!"; // midday
"Dinner time" = "Dinner time!"; // evening
"Bed time" = "Bed time!"; // night
```

Il `NSBundle.MainBundle.LocalizedString` metodo viene utilizzato per risolvere le stringhe nelle relative controparti tradotte, come illustrato nel codice seguente.

```csharp
var display = "Breakfast time";
var localizedDisplay =
  NSBundle.MainBundle.LocalizedString (display, comment:"greeting");
displayText.SetText (localizedDisplay);
```

#### <a name="images-in-code"></a>Immagini nel codice

Le immagini sono popolati da codice possono essere impostate in due modi.

1. È possibile modificare un `Image` controllo impostandone il valore per il nome della stringa di un'immagine che già esiste nell'applicazione di espressioni di controllo, ad esempio

  ```csharp
  displayImage.SetImage("gradient"); // image in Watch App (as shown above)
  ```

2. È possibile spostare un'immagine dall'estensione per l'utilizzo di espressioni di controllo `FromBundle` e l'app verrà scelto automaticamente l'immagine corretta per la selezione della lingua dell'utente. Nella soluzione di esempio è un'immagine **language@2x.png** in ciascuna lingua viene visualizzata nella cartella e `DetailController` utilizzando il codice seguente:

  ```csharp
  using (var image = UIImage.FromBundle ("language")) {
    displayImage.SetImage (image);
  }
  ```

  Si noti che non è necessario specificare il **@2x** quando si fa riferimento al nome file dell'immagine.

Il secondo metodo è applicabile se si scarica un'immagine da un server remoto per eseguire il rendering di guardia; anche Tuttavia in questo caso è necessario assicurarsi che l'immagine scaricato correttamente è localizzata in base alle preferenze dell'utente.

## <a name="localization"></a>Localizzazione

Dopo aver configurato la soluzione, funzioni di conversione sarà necessario elaborare il **.strings** file e immagini per ogni lingua che si desidera supportare.

È possibile creare tante **.lproj** directory, se è necessario (uno per ogni lingua supportata). I file sono denominati con i codici di lingua, ad esempio **en**, **es**, **Germania**, **ja**, **pt-BR**, e così via (per la lingua inglese Spagnolo, italiano, giapponese e portoghese (Brasile) rispettivamente).

L'esempio allegato Usa traduzioni (generati dal computer) per illustrare come localizzare un'app watchOS.

### <a name="watch-app"></a>Applicazione delle espressioni di controllo

Questi valori vengono utilizzati per convertire l'interfaccia utente definito in uno storyboard delle espressioni di controllo dell'applicazione. Il *chiave* valore è una combinazione di ogni controllo di storyboard **ID di localizzazione** e la proprietà tradotta.

È consigliabile aggiungere commenti contenente il testo originale del file in modo che i traduttori sapere quale deve essere la traduzione.

#### <a name="eslprojinterfacestrings"></a>es.lproj/Interface.strings

Le stringhe di lingua spagnola (tradotto) per lo storyboard è illustrate di seguito. È utile aggiungere commenti a ogni riga, in quanto è difficile sapere cosa il **ID di localizzazione** fa riferimento a:

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>Estensione delle espressioni di controllo

Questi valori vengono utilizzati nel codice per convertire le informazioni prima di essere visualizzato all'utente. Il *chiave* è selezionata per lo sviluppatore mentre si scrive codice e in genere contiene la stringa effettiva da convertire.

#### <a name="eslprojlocalizablestrings-file"></a>es.lproj/Localizable.strings file

Le stringhe di linguaggio Spansish (tradotto):

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>Test

Il metodo per modificare le preferenze della lingua differisce tra il simulatore e dispositivi fisici.

### <a name="simulator"></a>Simulatore

Nel simulatore, selezionare la lingua da testare con iOS **impostazioni** app (l'icona di colore grigio marcia nella schermata principale del simulatore).

  ![](localization-images/sim-settings-sml.png "Le impostazioni di localizzazione dell'app impostazioni iOS")

### <a name="watch-device"></a>Dispositivo di espressioni di controllo

Quando si verifica con un'espressione di controllo, modificare la lingua dell'orologio nel **Apple Watch** app su iPhone associati.

  ![](localization-images/phone-settings-sml.png "Modificare la lingua dell'orologio nell'app di Apple Watch su iPhone associati")



## <a name="related-links"></a>Collegamenti correlati

- [WatchLocalization (esempio)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchLocalization/)
