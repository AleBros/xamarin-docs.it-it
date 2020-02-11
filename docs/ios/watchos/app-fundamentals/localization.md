---
title: Uso della localizzazione watchos in Xamarin
description: Questo documento descrive come localizzare le app watchos compilate con Xamarin. Illustra le app Watch, le estensioni Watch, le stringhe nel codice, il testo dello storyboard, i test e altro ancora.
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 82b739697705ac4c90390a36405d755a5f523159
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028409"
---
# <a name="working-with-watchos-localization-in-xamarin"></a>Uso della localizzazione watchos in Xamarin

_Adattamento delle app watchos per più lingue_

![](localization-images/both-languages-sml.png "Apple Watch displaying localized content")

le app watchos vengono localizzate usando i metodi standard di iOS:

- Uso dell' **ID localizzazione** negli elementi storyboard
- file con **estensione Strings** associati allo storyboard e
- File **. Strings localizzabili** per il testo utilizzato nel codice.

Gli storyboard e le risorse predefiniti si trovano in una directory di **base** , mentre le traduzioni specifiche della lingua e altre risorse vengono archiviate in directory **. lproj** .
iOS e il sistema operativo Watch utilizzeranno automaticamente la selezione della lingua dell'utente per caricare le stringhe e le risorse corrette.

Dal momento che un'app Apple Watch dispone di due parti-Watch app e Watch Extension-le risorse di tipo stringa localizzate sono obbligatorie in due posizioni, a seconda del modo in cui vengono usate.

Il testo localizzato e le risorse saranno *diverse* nell'app Watch e nell'estensione Watch.

## <a name="watch-app"></a>Guarda l'app

L'app Watch contiene lo storyboard che descrive l'interfaccia utente dell'app. Tutti i controlli, ad esempio `Label` e `Image`, che supportano la localizzazione hanno un **ID localizzazione**.

Ogni directory **. lproj** specifica della lingua deve contenere file con **estensione Strings** con le traduzioni per ogni elemento (usando l' **ID di localizzazione**), nonché le immagini a cui fa riferimento lo storyboard.

## <a name="watch-extension"></a>Estensione Watch

L'estensione Watch è la posizione in cui viene eseguito il codice dell'app. Qualsiasi testo visualizzato all'utente dal codice deve essere localizzato nell'estensione e non nell'app Watch.

L'estensione deve inoltre contenere directory **. lproj** specifiche della lingua, ma i file con estensione **String** richiedono solo le traduzioni per il testo utilizzato nel codice.

## <a name="globalizing-the-watch-solution"></a>Globalizzazione della soluzione Watch

La globalizzazione è il processo di creazione di un'applicazione localizzabile.
Per le app Watch questo significa progettare lo storyboard con diverse lunghezze di testo, garantendo che ogni layout della schermata venga regolato in modo appropriato a seconda del testo visualizzato. È anche necessario assicurarsi che tutte le stringhe a cui viene fatto riferimento nel codice dell'estensione dell'espressione di controllo possano essere convertite usando il metodo `LocalizedString`.

### <a name="watch-app"></a>Guarda l'app

Per impostazione predefinita, l'app Watch non è configurata per la localizzazione. È necessario spostare il file dello storyboard predefinito e creare altre directory per le traduzioni:

1. Creare la directory **base. lproj** e spostarvi l' **interfaccia. Storyboard** .

2. Creare directory **\<language >. lproj** per ogni lingua che si vuole supportare.

3. Le directory **. lproj** devono contenere un file di testo **Interface. Strings** (il nome del file deve corrispondere al nome di storboard). Facoltativamente, è possibile inserire le immagini che richiedono la localizzazione in queste directory.

Dopo aver apportato queste modifiche, il progetto Watch app è simile al seguente (sono stati aggiunti solo i file in lingua inglese e spagnola):

  ![](localization-images/watchapp-solution.png "The watch app project with English and Spanish language files")

#### <a name="storyboard-text"></a>Testo storyboard

Quando si modifica lo storyboard, selezionare ogni elemento e notare l' **ID localizzazione** visualizzato nel riquadro delle **Proprietà** :

  [![](localization-images/storyboard-sml.png "The Localization ID that appears in the Properties pad")](localization-images/storyboard.png#lightbox)

Nella cartella **base. lproj** creare coppie chiave-valore come illustrato di seguito, in cui la chiave è costituita dall' **ID di localizzazione** e da un nome di proprietà nel controllo, unite da un punto (`.`).

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

Si noti che in questo esempio un **ID di localizzazione** può essere una stringa di numeri semplici (ad esempio, "0", "1" e così via) o una stringa più complessa (ad esempio "AgC-eL-HGC"). i controlli `Label` hanno una proprietà `Text` e `Button`s hanno una proprietà `Title`, che viene riflessa nel modo in cui i valori localizzati sono impostati. Assicurarsi di usare il nome di proprietà minuscole, come illustrato nell'esempio precedente.

Quando viene eseguito il rendering dello storyboard sull'espressione di controllo, i valori corretti verranno estratti e visualizzati automaticamente in base alla lingua selezionata dall'utente.

#### <a name="storyboard-images"></a>Immagini storyboard

La soluzione di esempio include anche un'immagine **gradient@2x.png** in ogni cartella della lingua. Questa immagine può essere diversa per ogni lingua, ad esempio potrebbe avere testo incorporato che deve tradurre o usare l'iconografia localizzata.

È sufficiente impostare la proprietà **Image** dell'immagine nello storyboard e verrà eseguito il rendering dell'immagine corretta nell'espressione di controllo in base alla lingua selezionata dall'utente.

![](localization-images/storyboard-image.png "Set the images Image property in the storyboard")

Nota: poiché tutti gli Apple Watch hanno schermi Retina, è necessario solo il **@2x** versione dell'immagine. Non è necessario specificare **@2x** nello storyboard.

### <a name="watch-extension"></a>Estensione Watch

L'estensione Watch richiede una struttura di directory simile per supportare la localizzazione, tuttavia non è presente alcuno storyboard. Le stringhe localizzate nell'estensione sono solo quelle a cui fa C# riferimento il codice.

![](localization-images/watchextension-solution.png "The watch extension directory structure to support localization")

#### <a name="strings-in-code"></a>Stringhe nel codice

Il file con **estensione Strings localizzabile** presenta una struttura leggermente diversa rispetto a quella associata a uno storyboard. In questo caso è possibile scegliere qualsiasi stringa "Key"; Apple consiglia di usare una chiave che rifletta il testo effettivo che verrebbe visualizzato nella lingua predefinita:

```csharp
"Breakfast time" = "Breakfast time!"; // morning
"Lunch time" = "Lunch time!"; // midday
"Dinner time" = "Dinner time!"; // evening
"Bed time" = "Bed time!"; // night
```

Il metodo `NSBundle.MainBundle.LocalizedString` viene usato per risolvere le stringhe nelle rispettive controparti tradotte, come illustrato nel codice riportato di seguito.

```csharp
var display = "Breakfast time";
var localizedDisplay =
  NSBundle.MainBundle.LocalizedString (display, comment:"greeting");
displayText.SetText (localizedDisplay);
```

#### <a name="images-in-code"></a>Immagini nel codice

Le immagini popolate dal codice possono essere impostate in due modi.

1. È possibile modificare un controllo `Image` impostando il relativo valore sul nome di stringa di un'immagine già esistente nell'app Watch, ad esempio

    ```csharp
    displayImage.SetImage("gradient"); // image in Watch App (as shown above)
    ```

2. È possibile spostare un'immagine dall'estensione all'espressione di controllo usando `FromBundle` e l'app sceglierà automaticamente l'immagine corretta per la selezione della lingua dell'utente. Nella soluzione di esempio è presente un'immagine **language@2x.png** in ogni cartella della lingua e viene visualizzata in `DetailController` usando il codice seguente:

    ```csharp
    using (var image = UIImage.FromBundle ("language")) {
        displayImage.SetImage (image);
    }
    ```

    Si noti che non è necessario specificare il **@2x** quando si fa riferimento al nome file dell'immagine.

Il secondo metodo è applicabile anche se si scarica un'immagine da un server remoto per eseguire il rendering nell'espressione di controllo; in questo caso, tuttavia, è necessario assicurarsi che l'immagine scaricata sia localizzata correttamente in base alle preferenze dell'utente.

## <a name="localization"></a>Localizzazione

Una volta configurata la soluzione, i traduttori dovranno elaborare i file **. Strings** e le immagini per ogni lingua che si desidera supportare.

È possibile creare tutte le directory con **estensione LPROJ** necessarie, una per ogni lingua supportata. Vengono denominati usando i codici di lingua, ad esempio **en**, **es**, **de**, **ja**, **PT-BR**e così via, rispettivamente per inglese, spagnolo, tedesco, giapponese e portoghese (brasiliano).

L'esempio associato usa le traduzioni (generate dal computer) per dimostrare come localizzare un'app watchos.

### <a name="watch-app"></a>Guarda l'app

Questi valori vengono usati per tradurre l'interfaccia utente definita nello storyboard dell'app Watch. Il valore della *chiave* è una combinazione dell' **ID di localizzazione** di ogni controllo Storyboard e della proprietà da convertire.

Si consiglia di aggiungere commenti contenenti il testo originale al file in modo che i traduttori sappiano quale dovrebbe essere la traduzione.

#### <a name="eslprojinterfacestrings"></a>es. lproj/Interface. Strings

Di seguito sono illustrate le stringhe di lingua spagnola (Machine translated) per lo storyboard. È utile aggiungere commenti a ogni riga, perché è difficile conoscere il riferimento all' **ID di localizzazione** in caso contrario:

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>Estensione Watch

Questi valori vengono usati nel codice per tradurre le informazioni prima di essere visualizzate all'utente. La *chiave* viene selezionata dallo sviluppatore mentre sta scrivendo il codice e in genere contiene la stringa effettiva da tradurre.

#### <a name="eslprojlocalizablestrings-file"></a>es. lproj/file. Strings localizzabile

Stringhe di lingua Spansish (Machine translated):

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>Test

Il metodo per modificare le preferenze della lingua differisce tra il simulatore e i dispositivi fisici.

### <a name="simulator"></a>Simulatore

Nel simulatore selezionare la lingua da testare usando l'app **Impostazioni** iOS (l'icona a forma di ingranaggio grigio nella schermata iniziale del simulatore).

  ![](localization-images/sim-settings-sml.png "The iOS Settings app Localization settings")

### <a name="watch-device"></a>Dispositivo di controllo

Quando si esegue il test con un'espressione di controllo, modificare la lingua dell'orologio nell'app **Apple Watch** sull'iPhone associato.

  ![](localization-images/phone-settings-sml.png "Change the watch's language in the Apple Watch app on the paired iPhone")

## <a name="related-links"></a>Collegamenti correlati

- [WatchLocalization (esempio)](https://developer.xamarin.com//samples/monotouch/watchOS/WatchLocalization/)
