---
title: Localizzazione Android
description: In questo documento vengono presentate le funzionalità di localizzazione del Android SDK e viene illustrato come accedervi con Novell.
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: ae97297b81d33c4b9f814d4b3639984b05ce3d72
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021660"
---
# <a name="android-localization"></a>Localizzazione Android

_In questo documento vengono presentate le funzionalità di localizzazione del Android SDK e viene illustrato come accedervi con Novell._

## <a name="android-platform-features"></a>Funzionalità della piattaforma Android

Questa sezione descrive le principali funzionalità di localizzazione di Android. Passare alla [sezione successiva](#basics) per visualizzare codice ed esempi specifici.

### <a name="locale"></a>Impostazioni locali

Gli utenti scelgono la lingua in **impostazioni > lingua & input**. Questa selezione controlla sia la lingua visualizzata sia le impostazioni internazionali usate (ad esempio, per la formattazione di data e numero).

Le impostazioni locali correnti possono essere sottoposte a query tramite il `Resources`del contesto corrente:

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

Questo valore sarà un identificatore delle impostazioni locali che contiene un codice di lingua e un codice delle impostazioni locali, separati da un carattere di sottolineatura. Per riferimento, di seguito è riportato un [elenco delle impostazioni locali Java e delle](https://www.oracle.com/technetwork/java/javase/locales-137662.html) [impostazioni locali supportate da Android tramite StackOverflow](https://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android).

Ecco alcuni esempi comuni:

- `en_US` per la lingua inglese (Stati Uniti)
- `es_ES` per spagnolo (Spagna)
- `ja_JP` per il giapponese (Giappone)
- `zh_CN` per il cinese (Cina)
- `zh_TW` per il cinese (Taiwan)
- `pt_PT` per portoghese (Portogallo)
- `pt_BR` per portoghese (Brasile)

### <a name="locale_changed"></a>LOCALE_CHANGED

Android genera `android.intent.action.LOCALE_CHANGED` quando l'utente modifica la selezione della lingua.

Le attività possono scegliere di gestire questa impostazione impostando l'attributo `android:configChanges` sull'attività, come indicato di seguito:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>Nozioni fondamentali sull'internazionalizzazione in Android

La strategia di localizzazione di Android presenta le parti principali seguenti:

- Cartelle delle risorse che contengono stringhe localizzate, immagini e altre risorse.

- `GetText` metodo, usato per recuperare le stringhe localizzate nel codice

- `@string/id` nei file AXML, per inserire automaticamente le stringhe localizzate nei layout.

### <a name="resource-folders"></a>Cartelle delle risorse

Le applicazioni Android gestiscono la maggior parte del contenuto nelle cartelle delle risorse, ad esempio:

- **layout** : contiene i file di layout di AXML.
- **disegnatore** : contiene immagini e altre risorse ricavabili.
- **values** : contiene le stringhe.
- **RAW** : contiene file di dati.

La maggior parte degli sviluppatori ha già familiarità con l'uso dei suffissi **dpi** nella directory che è possibile **creare** per fornire più versioni di un'immagine, consentendo a Android di scegliere la versione corretta per ogni dispositivo. Lo stesso meccanismo viene usato per fornire traduzioni a più lingue tramite il suffisso delle directory di risorse con identificatori di lingua e impostazioni cultura.

![Screenshot delle cartelle risorse/riportabili e di risorse/valori per più identificatori culturali](localization-images/resources.png)

> [!NOTE]
> Quando si specifica una lingua di primo livello, ad esempio `es` sono necessari solo due caratteri; Tuttavia, quando si specificano le impostazioni locali complete, il formato del nome di directory richiede un trattino e un **r** minuscolo per separare le due parti, ad esempio **PT-rBR** o **ZH-rCN**. Confrontare questo valore con il valore restituito nel codice, che presenta un carattere di sottolineatura, ad esempio `pt_BR`). Entrambi sono diversi dal valore di .NET `CultureInfo` Class USA, che ha solo un trattino (ad esempio, `pt-BR`). Tenere presenti queste differenze quando si lavora su piattaforme Novell.

#### <a name="stringsxml-file-format"></a>Formato file Strings. XML

Una directory di **valori** localizzati, ad esempio **values-es** o **values-PT-rBR**) deve contenere un file denominato **Strings. XML** che conterrà il testo tradotto per le impostazioni locali.

Ogni stringa traducibile è un elemento XML con l'ID risorsa specificato come attributo `name` e la stringa tradotta come valore:

```xml
<string name="app_name">TaskyL10n</string>
```

È necessario eseguire l'escape in base alle normali regole XML e il `name` deve essere un ID di risorsa Android valido (senza spazi o trattini). Di seguito è riportato un esempio del file di stringhe predefinito (Inglese) per l'esempio:

**values/Strings. XML**

```xml
<resources>
    <string name="app_name">TaskyL10n</string>
    <string name="taskadd">Add Task</string>
    <string name="taskname">Name</string>
    <string name="tasknotes">Notes</string>
    <string name="taskdone">Done</string>
    <string name="taskcancel">Cancel</string>
</resources>
```

La directory spagnola **values-es** contiene un file con lo stesso nome (**Strings. XML**) che contiene le traduzioni:

**values-es/Strings. XML**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">TaskyLeon</string>
    <string name="taskadd">agregar tarea</string>
    <string name="taskname">Nombre</string>
    <string name="tasknotes">Notas</string>
    <string name="taskdone">Completo</string>
    <string name="taskcancel">Cancelar</string>
</resources>
```

![Screenshot di più cartelle di valori, ognuno dei quali contiene un file Strings. XML](localization-images/values.png)

Con i file di stringhe impostati, è possibile fare riferimento ai valori tradotti sia nel layout che nel codice.

### <a name="axml-layout-files"></a>File di layout di AXML

Per fare riferimento alle stringhe localizzate nei file di layout, usare la sintassi `@string/id`. Questo frammento di codice XML dell'esempio Mostra `text` proprietà impostate con ID di risorsa localizzati (altri attributi sono stati omessi):

```xml
<TextView
    android:id="@+id/NameLabel"
    android:text="@string/taskname"
    ... />
<CheckBox
    android:id="@+id/chkDone"
    android:text="@string/taskdone"
    ... />
```

### <a name="gettext-method"></a>Metodo GetText

Per recuperare le stringhe tradotte nel codice, usare il metodo `GetText` e passare l'ID risorsa:

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>Quantità stringhe

Le risorse di tipo stringa Android consentono inoltre di creare *stringhe di quantità* che consentono ai traduttori di fornire traduzioni diverse per quantità diverse, ad esempio:

- "C'è 1 attività a sinistra".
- "Ci sono 2 attività ancora da fare".

(invece di un generico "ci sono n attività rimaste").

In **Strings. XML**

```xml
<plurals name="numberOfTasks">
   <!--
      As a developer, you should always supply "one" and "other"
      strings. Your translators will know which strings are actually
      needed for their language.
    -->
   <item quantity="one">There is %d task left.</item>
   <item quantity="other">There are %d tasks still to do.</item>
 </plurals>
```

Per eseguire il rendering della stringa completa, usare il metodo `GetQuantityString`, passando l'ID risorsa e il valore da visualizzare (che viene passato due volte). Il secondo parametro viene usato da Android per determinare *quale* stringa di `quantity` usare, il terzo parametro è il valore effettivamente sostituito nella stringa (sono necessari entrambi).

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

Le opzioni di `quantity` valide sono:

- zero
- one
- due
- pochi
- many
- altro

Sono descritte più dettagliatamente nella documentazione di [Android](https://developer.android.com/guide/topics/resources/string-resource.html#Plurals). Se una determinata lingua non richiede la gestione "speciale", le stringhe `quantity` verranno ignorate. ad esempio, in inglese vengono utilizzati solo `one` e `other`. Se si specifica una stringa di `zero`, non verrà utilizzato alcun effetto.

### <a name="images"></a>Immagini

Le immagini localizzate seguono le stesse regole dei file di stringhe: tutte le immagini a cui viene fatto riferimento nell'applicazione devono essere inserite in directory di cui è possibile **creare** un fallback.

Le immagini specifiche delle impostazioni locali devono quindi essere posizionate in cartelle ricavabili qualificate, ad esempio **disegnatore-es** o **Disegnatore-ja** (gli identificatori DPI possono essere aggiunti).

In questa schermata, le quattro immagini vengono salvate nella directory di cui è stata creata la **traccia** , ma solo una, **flag. png**, contiene copie localizzate in altre directory.

![Screenshot di più cartelle che contengono uno o più file con estensione png localizzati](localization-images/drawable.png)

#### <a name="other-resource-types"></a>Altri tipi di risorse

È anche possibile fornire altri tipi di risorse alternative specifiche del linguaggio, tra cui layout, animazioni e file non elaborati. Ciò significa che è possibile fornire un layout di schermata specifico per una o più lingue di destinazione, ad esempio è possibile creare un layout specifico per il tedesco che consente etichette di testo molto lunghe.

Android 4,2 ha introdotto il supporto per le [lingue da destra a sinistra (RTL)](https://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html) se si imposta l'impostazione dell'applicazione `android:supportsRtl="true"`. Il qualificatore di risorsa `"ldrtl"` può essere incluso in un nome di directory per contenere layout personalizzati progettati per la visualizzazione RTL.

Per altre informazioni sulla denominazione e sul fallback delle directory delle risorse, vedere la documentazione di Android per [fornire risorse alternative](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources).

### <a name="app-name"></a>Nome dell'app

Il nome dell'applicazione è facile da localizzare usando un `@string/id` in per l'attività `MainLauncher`:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>Lingue da destra a sinistra (RTL)

Android 4,2 e versioni successive offrono il supporto completo per i layout RTL, descritti in dettaglio nel [Blog nativo del supporto di RTL](https://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html).

Quando si usa Android 4,2 (livello API 17) e versioni successive, è possibile specificare i valori di allineamento con `start` e `end` invece di `left` e `right` (ad esempio `android:paddingStart`). Sono inoltre disponibili nuove API, ad esempio `LayoutDirection`, `TextDirection`e `TextAlignment` per facilitare la compilazione di schermate adatte per i lettori RTL.

Lo screenshot seguente illustra l' [esempio di **attività** localizzate](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) in arabo:

[![screenshot dell'app Tasky in arabo](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

Lo screenshot seguente mostra l' [esempio di **attività** localizzato](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) in ebraico:

[![screenshot dell'app Tasky in ebraico](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

Il testo RTL viene localizzato utilizzando i file **Strings. XML** in modo analogo al testo ltr.

## <a name="testing"></a>Test

Assicurarsi di testare accuratamente le impostazioni locali predefinite. L'applicazione si arresterà in modo anomalo se non è possibile caricare le risorse predefinite per qualche motivo, ovvero mancanti.

### <a name="emulator-testing"></a>Test dell'emulatore

Per istruzioni su come impostare un emulatore per le impostazioni locali specifiche usando la shell ADB, vedere la sezione test di Google [in una emulatore Android](https://developer.android.com/guide/topics/resources/localization.html#testing) .

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>Test del dispositivo

Per eseguire il test in un dispositivo, modificare la lingua nell'app **Impostazioni** .

> [!TIP]
> Prendere nota delle icone e della posizione delle voci di menu in modo da poter ripristinare l'impostazione originale della lingua.

## <a name="summary"></a>Riepilogo

Questo articolo illustra le nozioni di base della localizzazione di applicazioni Android usando la gestione delle risorse incorporata. Sono disponibili altre informazioni su i18n e l10n per le app iOS, Android e multipiattaforma (incluse Xamarin.Forms) in [questa guida multipiattaforma](~/cross-platform/app-fundamentals/localization.md).

## <a name="related-links"></a>Collegamenti correlati

- [Tasky (localizzato nel codice) (esempio)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Localizzazione di Android con risorse](https://developer.android.com/guide/topics/resources/localization.html)
- [Panoramica della localizzazione multipiattaforma](~/cross-platform/app-fundamentals/localization.md)
- [Localizzazione di Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localizzazione in iOS](~/ios/app-fundamentals/localization/index.md)
