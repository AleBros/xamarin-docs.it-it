---
title: Accessibilità in Android
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: d004b753c89f3995e8dc511877bd115a894396fc
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671624"
---
# <a name="accessibility-on-android"></a>Accessibilità in Android

Questa pagina descrive come usare le API di accessibilità di Android per compilare le App in base al [elenco di controllo di accesso facilitato](~/cross-platform/app-fundamentals/accessibility.md).
Fare riferimento al [iOS accessibilità](~/ios/app-fundamentals/accessibility.md) e [accessibilità OS X](~/mac/app-fundamentals/accessibility.md) pagine per gli altri API della piattaforma.


## <a name="describing-ui-elements"></a>Che descrive gli elementi dell'interfaccia utente

Android fornisce un `ContentDescription` proprietà utilizzato dalla schermata leggere le API per fornire una descrizione dello scopo del controllo accessibile.

La descrizione del contenuto può essere impostata nel C# o nel file di layout AXML.

**C#**

La descrizione può essere impostata nel codice per qualsiasi stringa (o una risorsa di tipo stringa):

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

Per la `EditText` e `TextView` controlli per input di dati, utilizzare il `Hint` proprietà per fornire una descrizione di è previsto l'input (invece di `ContentDescription`).
Se non si immette un testo, il testo stesso verrà "letto" anziché il suggerimento.

**C#**

Impostare il `Hint` proprietà nel codice:

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**Layout AXML**

Nel codice XML utilizzano i file di layout di `android:hint` attributo:

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```


### <a name="labelfor-links-input-fields-with-labels"></a>I collegamenti LabelFor campi con le etichette di input

Per associare un'etichetta a un controllo input di dati, usare il `LabelFor` proprietà

**C#**

In C#, impostare la `LabelFor` proprietà per l'ID risorsa del controllo che descrive il contenuto (in genere questa proprietà è impostata su un'etichetta e fa riferimento a un altro controllo di input):

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**Layout AXML**

Nel layout di utilizzo dei valori XML di `android:labelFor` proprietà cui fare riferimento a un altro identificatore di controllo:

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

Usare il `AnnounceForAccessibility` metodo su qualsiasi controllo per comunicare un evento o stato di modifica agli utenti quando è abilitato l'accesso facilitato di visualizzazione. Questo metodo non è necessario per la maggior parte delle operazioni in cui il commento predefinito offre un feedback sufficiente, ma usare informazioni aggiuntive in cui potrebbe essere utile per l'utente.

Il codice seguente illustra una chiamata di esempio semplice `AnnounceForAccessibility`:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>Modifica delle impostazioni di messa a fuoco

È possibile la navigazione si basa su controlli che presentano lo stato attivo per l'utente di aiuto nel comprendere quali operazioni sono disponibili. Android fornisce un `Focusable` proprietà che è possibile contrassegnare i controlli in modo specifico in grado di ricevere lo stato attivo durante la navigazione.

**C#**

Per evitare di ottenere lo stato attivo con un controllo C#, impostare la `Focusable` alla proprietà `false`:

```csharp
label.Focusable = false;
```

**Layout AXML**

Nel layout di set di file XML di `android:focusable` attributo:

```xml
<android:focusable="false" />
```

È inoltre possibile controllare l'ordine di focus con il `nextFocusDown`, `nextFocusLeft`, `nextFocusRight`, `nextFocusUp` attributi, in genere impostati nel layout AXML. Usare questi attributi per verificare che l'utente possa passare facilmente tra i controlli sullo schermo.


## <a name="accessibility-and-localization"></a>Accessibilità e localizzazione

Negli esempi precedenti il contenuto e hint per la descrizione viene impostata direttamente al valore da visualizzare. È preferibile usare i valori in una **Strings. XML** file, simile al seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

Utilizzando il testo da un file di stringhe è illustrato di seguito in C# e i file di layout AXML:

**C#**

Invece di usare valori letterali stringa nel codice, cercare valori tradotti da file di stringhe con `Resources.GetText`:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

Nel layout XML, ad esempio gli attributi di accessibilità `hint` e `contentDescription` può essere impostato su un identificatore di stringa:

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

Il vantaggio dell'archiviazione di testo in un file separato è che più traduzioni di lingua del file possono essere fornite nell'app. Vedere le [Guida di Android localizzazione](~/android/app-fundamentals/localization.md) per informazioni su come aggiungere file di stringhe localizzate in un progetto di applicazione.


## <a name="testing-accessibility"></a>Test dell'accessibilità

Seguire [questi passaggi](https://developer.android.com/training/accessibility/testing.html#how-to) abilitare TalkBack ed Esplora direttamente da Touch testare l'accessibilità nei dispositivi Android.

Potrebbe essere necessario installare [TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) da Google Play se non viene visualizzato nelle **Impostazioni > accesso facilitato**.


## <a name="related-links"></a>Collegamenti correlati

- [Accessibilità multipiattaforma](~/cross-platform/app-fundamentals/accessibility.md)
- [API di Android accessibilità](https://developer.android.com/guide/topics/ui/accessibility/index.html)
