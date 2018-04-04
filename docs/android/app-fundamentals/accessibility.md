---
title: Accessibilità in Android
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/28/2018
ms.openlocfilehash: 2a49d15651b8c6ab7417a69d934af5d20bfc13d0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="accessibility-on-android"></a>Accessibilità in Android

Questa pagina viene descritto come utilizzare le API Android di accessibilità per compilare le applicazioni in base al [elenco di controllo di accesso facilitato](~/cross-platform/app-fundamentals/accessibility.md).
Consultare il [iOS accessibilità](~/ios/app-fundamentals/accessibility.md) e [accessibilità OS X](~/mac/app-fundamentals/accessibility.md) pagine per altri API della piattaforma.


## <a name="describing-ui-elements"></a>Che descrive gli elementi dell'interfaccia utente

Android fornisce un `ContentDescription` proprietà utilizzata per la lettura di API a schermo per fornire una descrizione dello scopo del controllo accessibile.

In c# o nel file di layout AXML, è possibile impostare la descrizione del contenuto.

**C#**

La descrizione può essere impostata nel codice a qualsiasi stringa (o una risorsa stringa):

```csharp
saveButton.ContentDescription = "Save data";
```

**Layout AXML**

Nel codice XML layout di utilizzare il `android:contentDescription` attributo:

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>Utilizzare l'Hint per TextView

Per `EditText` e `TextView` controlli per dati di input, utilizzare il `Hint` proprietà per fornire una descrizione di è previsto il tipo di input (anziché `ContentDescription`).
Quando è stato immesso del testo, il testo verrà "letto" anziché l'hint.

**C#**

Impostare il `Hint` proprietà nel codice:

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**Layout AXML**

In XML usano i file di layout di `android:hint` attributo:

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```


### <a name="labelfor-links-input-fields-with-labels"></a>Collegamenti LabelFor campi con le etichette di input

Per associare un'etichetta a un controllo input di dati, usare il `LabelFor` proprietà

**C#**

In c#, impostare il `LabelFor` descrive proprietà per l'ID di risorsa del controllo questo è il contenuto (in genere questa proprietà è impostata su un'etichetta e fa riferimento a un altro controllo di input):

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**Layout AXML**

Nel layout di utilizzo in XML la `android:labelFor` proprietà per fare riferimento all'identificatore del controllo:

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>Avvisi per l'accessibilità

Utilizzare il `AnnounceForAccessibility` su qualsiasi metodo di controllo per comunicare una modifica di stato o l'evento agli utenti quando è abilitata l'accessibilità della visualizzazione. Questo metodo non è necessario per la maggior parte delle operazioni in cui il commento predefinito fornisce un feedback sufficiente, ma deve essere utilizzato informazioni aggiuntive in cui potrebbe essere utile per l'utente.

Il codice seguente viene illustrata la chiamata di un semplice esempio `AnnounceForAccessibility`:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>Modifica delle impostazioni dello stato attivo

È possibile la navigazione si basa su controlli che presentano lo stato attivo per aiutare l'utente comprendere quali operazioni sono disponibili. Android fornisce un `Focusable` proprietà che è possibile contrassegnare i controlli in grado di ricevere lo stato attivo durante la navigazione in modo specifico.

**C#**

Per evitare che un controllo di ottenere lo stato attivo con c#, impostare il `Focusable` proprietà `false`:

```csharp
label.Focusable = false;
```

**Layout AXML**

Nel layout del set di file XML di `android:focusable` attributo:

```xml
<android:focusable="false" />
```

È inoltre possibile controllare l'ordine lo stato attivo con il `nextFocusDown`, `nextFocusLeft`, `nextFocusRight`, `nextFocusUp` gli attributi, in genere impostati nel layout AXML. Questi attributi possono essere usati per verificare che l'utente possa passare facilmente tra i controlli sullo schermo.


## <a name="accessibility-and-localization"></a>Localizzazione e accessibilità

Negli esempi precedenti la descrizione hint e il contenuto si impostare direttamente il valore di visualizzazione. È preferibile utilizzare i valori in un **Strings** file, simile al seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

Testo da un file di stringhe di seguito è riportato in c# e il file di layout AXML:

**C#**

Anziché utilizzare valori letterali stringa nel codice, cercare valori tradotti da file di stringhe con `Resources.GetText`:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

Nel layout XML come attributi di accessibilità `hint` e `contentDescription` può essere impostato su un identificatore di stringa:

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

Il vantaggio di archiviazione di testo in un file separato è che più traduzioni delle lingue del file possono essere fornite in app. Vedere il [Guida Android localizzazione](~/android/app-fundamentals/localization.md) per informazioni su come aggiungere file di stringhe localizzate per un progetto di applicazione.


## <a name="testing-accessibility"></a>Verifica dell'accessibilità

Seguire [procedura](http://developer.android.com/training/accessibility/testing.html#how-to) per abilitare TalkBack ed Esplora da tocco testare l'accessibilità nei dispositivi Android.

Potrebbe essere necessario installare [TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) da Google Play se non viene visualizzato **Impostazioni > accessibilità**.


## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità multipiattaforma](~/cross-platform/app-fundamentals/accessibility.md)
- [API di Android accessibilità](http://developer.android.com/guide/topics/ui/accessibility/index.html)
