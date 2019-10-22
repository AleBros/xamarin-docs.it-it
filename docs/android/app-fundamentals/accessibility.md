---
title: Accessibilità in Android
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 982d5b81a22d6e69227081420a5947aed4d3aab1
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70755675"
---
# <a name="accessibility-on-android"></a>Accessibilità in Android

Questa pagina descrive come usare le API di accessibilità Android per creare app in base all' [elenco di controllo di accessibilità](~/cross-platform/app-fundamentals/accessibility.md).
Vedere le pagine di accessibilità [iOS](~/ios/app-fundamentals/accessibility.md) e [OS X](~/mac/app-fundamentals/accessibility.md) per altre API della piattaforma.

## <a name="describing-ui-elements"></a>Descrizione degli elementi dell'interfaccia utente

Android fornisce una proprietà `ContentDescription` utilizzata dalle API per la lettura dello schermo per fornire una descrizione accessibile dello scopo del controllo.

La descrizione del contenuto può essere impostata in C# o nel file di layout AXML.

**C#**

La descrizione può essere impostata nel codice per qualsiasi stringa (o una risorsa di stringa):

```csharp
saveButton.ContentDescription = "Save data";
```

**Layout di AXML**

Nei layout XML utilizzare l'attributo `android:contentDescription`:

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>USA hint per TextView

Per i controlli `EditText` e `TextView` per l'input dei dati, usare la proprietà `Hint` per fornire una descrizione dell'input previsto (anziché `ContentDescription`).
Quando è stato immesso un testo, il testo stesso sarà "Read" anziché l'hint.

**C#**

Impostare la proprietà `Hint` nel codice:

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**Layout di AXML**

Nei file di layout XML utilizzare l'attributo `android:hint`:

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```

### <a name="labelfor-links-input-fields-with-labels"></a>LabelFor collega campi di input con etichette

Per associare un'etichetta a un controllo di input dei dati, utilizzare la proprietà `LabelFor` per

**C#**

In C#impostare la proprietà `LabelFor` sull'ID risorsa del controllo descritto da questo contenuto, in genere questa proprietà è impostata su un'etichetta e fa riferimento a un altro controllo di input:

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**Layout di AXML**

In XML di layout usare la proprietà `android:labelFor` per fare riferimento all'identificatore di un altro controllo:

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>Annunciare per l'accessibilità

Usare il metodo `AnnounceForAccessibility` su qualsiasi controllo di visualizzazione per comunicare un evento o una modifica dello stato agli utenti quando è abilitata l'accessibilità. Questo metodo non è obbligatorio per la maggior parte delle operazioni in cui la funzionalità di narrazione incorporata fornisce un feedback sufficiente, ma deve essere utilizzata laddove informazioni aggiuntive sarebbero utili per l'utente.

Il codice seguente illustra un semplice esempio di chiamata a `AnnounceForAccessibility`:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>Modifica delle impostazioni di attivazione

La navigazione accessibile si basa sui controlli che hanno lo stato attivo per aiutare l'utente a comprendere quali operazioni sono disponibili. Android fornisce una proprietà di `Focusable` che consente di contrassegnare i controlli in modo specifico in grado di ricevere lo stato attivo durante la navigazione.

**C#**

Per impedire che un controllo ottenga lo stato C#attivo con, impostare la proprietà `Focusable` su `false`:

```csharp
label.Focusable = false;
```

**Layout di AXML**

In file XML di layout impostare l'attributo `android:focusable`:

```xml
<android:focusable="false" />
```

È anche possibile controllare l'ordine di attivazione con gli attributi `nextFocusDown`, `nextFocusLeft`, `nextFocusRight` `nextFocusUp`, in genere impostati nel layout AXML. Usare questi attributi per assicurarsi che l'utente possa spostarsi facilmente attraverso i controlli sullo schermo.

## <a name="accessibility-and-localization"></a>Accessibilità e localizzazione

Negli esempi precedenti l'hint e la descrizione del contenuto sono impostati direttamente sul valore di visualizzazione. È preferibile usare i valori in un file **Strings. XML** , ad esempio:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

L'uso del testo da un file di stringhe è C# riportato di seguito nei file di layout e AXML:

**C#**

Anziché usare valori letterali stringa nel codice, cercare i valori tradotti dai file di stringhe con `Resources.GetText`:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

Negli attributi di accessibilità XML del layout come `hint` e `contentDescription` può essere impostato su un identificatore di stringa:

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

Il vantaggio di archiviare il testo in un file separato è costituito da più traduzioni della lingua del file che possono essere fornite nell'app. Vedere la [Guida alla localizzazione di Android](~/android/app-fundamentals/localization.md) per informazioni su come aggiungere file di stringhe localizzate a un progetto di applicazione.

## <a name="testing-accessibility"></a>Test dell'accessibilità

Seguire [questa procedura](https://developer.android.com/training/accessibility/testing.html#how-to) per abilitare la discussione ed esplorare tramite tocco per testare l'accessibilità nei dispositivi Android.

Potrebbe essere [necessario installare la](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) discussione da Google Play se non viene visualizzata nelle **Impostazioni > Accessibilità**.

## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità multipiattaforma](~/cross-platform/app-fundamentals/accessibility.md)
- [API di accessibilità Android](https://developer.android.com/guide/topics/ui/accessibility/index.html)
