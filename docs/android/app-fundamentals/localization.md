---
title: Localizzazione di Android
description: Questo documento presenta le funzionalità di localizzazione di Android SDK e come accedervi con Xamarin.
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 634131025b322b64e89ece3b4c9d092e6b17a373
ms.sourcegitcommit: d09391c315336d36496880ef465a72b8974f2ac7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51579817"
---
# <a name="android-localization"></a>Localizzazione di Android

_Questo documento presenta le funzionalità di localizzazione di Android SDK e come accedervi con Xamarin._

## <a name="android-platform-features"></a>Funzionalità della piattaforma Android

In questa sezione vengono descritte le funzionalità di localizzazione principale di Android. Ignorare per il [nella sezione successiva](#basics) per visualizzare codice specifico ed esempi.

### <a name="locale"></a>Impostazioni locali

Gli utenti di scegliere la lingua nel **Impostazioni > Language & input**. Questa selezione determina la lingua visualizzata e impostazioni internazionali usate (ad es. per la data e formattazione dei numeri).

Le impostazioni locali correnti è possibile eseguire query tramite il contesto corrente `Resources`:

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

Questo valore sarà un identificatore delle impostazioni locali che contiene un codice di lingua e un codice impostazioni locali, separati da un carattere di sottolineatura. Per riferimento, ecco una [elenco delle impostazioni locali di Java](http://www.oracle.com/technetwork/java/javase/locales-137662.html) e [impostazioni locali supportato da Android tramite StackOverflow](http://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android).

Ecco alcuni esempi comuni:

* `en_US` per l'inglese (Stati Uniti)
* `es_ES` per lo spagnolo (Spagna)
* `ja_JP` per il giapponese (Giappone)
* `zh_CN` per il cinese (Cina)
* `zh_TW` per il cinese (Taiwan)
* `pt_PT` per il portoghese (Portogallo)
* `pt_BR` per il portoghese (Brasile)

### <a name="localechanged"></a>LOCALE_CHANGED

Android genera `android.intent.action.LOCALE_CHANGED` quando l'utente modifica la selezione della lingua.

Le attività possono scegliere di gestire questa situazione impostando il `android:configChanges` attributo sull'attività, simile al seguente:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
        ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>Nozioni sull'internazionalizzazione in Android

Strategia di localizzazione di Android include le parti principali seguenti:

- Cartelle di risorse per contenere le stringhe localizzate, immagini e altre risorse.

- `GetText` metodo, che viene usato per recuperare le stringhe localizzate nel codice

- `@string/id` nei file AXML, per inserire automaticamente le stringhe localizzate nei layout.

### <a name="resource-folders"></a>Cartelle di risorse

Le applicazioni Android gestiscono la maggior parte dei contenuti in cartelle di risorse, ad esempio:

* **layout** -contiene i file di layout AXML.
* **drawable** -contiene le immagini e altre risorse drawable.
* **i valori** -contiene le stringhe.
* **Raw** -contiene i file di dati.

La maggior parte degli sviluppatori hanno familiari con l'uso di **dpi** suffissi sulle **drawable** directory per fornire più versioni di un'immagine, consentendo di Android scegliere la versione corretta per ogni dispositivo. Lo stesso meccanismo viene usato per fornire più traduzioni di linguaggio aggiungendo le directory delle risorse con gli identificatori di lingua e impostazioni cultura.

![Screenshot delle cartelle drawable/le risorse e le risorse e i valori per più identificatori relative alla lingua](localization-images/resources.png)

> [!NOTE]
> Quando si specifica un linguaggio di livello superiore, ad esempio `es` solo due caratteri sono richiesti; tuttavia, quando si specifica un'impostazione internazionale completa, il formato del nome directory richiede un trattino e una minuscola **r** per separare le due parti, ad esempio **pt rBR** oppure **zh-salti riga**. Confrontare questo con il valore restituito nel codice, che è un carattere di sottolineatura (ad es. `pt_BR`). Entrambi gli elementi sono diversi da quello .NET `CultureInfo` , utilizzato dalla classe che dispone di un trattino solo (ad es. `pt-BR`). Quando si lavora in piattaforme Xamarin, tenere presente queste differenze.

#### <a name="stringsxml-file-format"></a>Formato di file Strings. Xml

Localizzato **valori** directory (ad es. **i valori-es** oppure **valori: pt-rBR**) deve contenere un file denominato **Strings. XML** che conterrà il testo tradotto per tali impostazioni locali.

Ogni stringa traducibile è un elemento XML con la risorsa con ID specificato come il `name` attributo e la stringa tradotta come valore:

```xml
<string name="app_name">TaskyL10n</string>
```

È necessario eseguire l'escape in base alle normali regole XML e il `name` deve essere un ID di risorsa di Android valido (senza spazi o trattini). Di seguito è riportato un esempio del file di stringhe (in inglese) predefinito per un esempio:

**values/Strings.xml**

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

La directory spagnolo **valori-es** contiene un file con lo stesso nome (**Strings. XML**) che contiene le traduzioni:

**i valori-es/Strings. Xml**

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

![Schermata di più valori cartelle, ognuno dei quali contiene un file Strings. Xml](localization-images/values.png)

Con la configurazione di file di stringhe, i valori tradotti sono reperibile in entrambi i layout e codice.

### <a name="axml-layout-files"></a>File di Layout AXML

Per fare riferimento a stringhe localizzate nei file di layout, usare il `@string/id` sintassi. Questo frammento di codice XML nell'esempio viene illustrato `text` proprietà impostate con localizzate (altri attributi sono stati omessi) ID risorsa:

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

### <a name="gettext-method"></a>GetText (metodo)

Per recuperare le stringhe tradotte nel codice, usare il `GetText` (metodo) e passare l'ID risorsa:

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>Stringhe di quantità

Risorse stringa Android consentono anche di creare *stringhe di quantità* che permette ai traduttori fornire traduzioni diverse per quantità diverse, ad esempio:

* "Si è a sinistra di 1 attività".
* "Esistono 2 attività ancora da eseguire."

(anziché un oggetto generico "There are a sinistra all'attività n").

Nel **Strings. Xml**

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

Per eseguire il rendering di usare la stringa completa il `GetQuantityString` , passando l'ID di risorsa e il valore da visualizzare (che viene passato due volte). Il secondo parametro è utilizzato da Android per determinare *quale* `quantity` stringa da usare, il terzo parametro è il valore di sostituzione effettivamente nella stringa (entrambe sono necessarie).

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

Valido `quantity` le opzioni sono:

* zero
* one
* two
* alcuni
* many
* altro

Descritto più dettagliatamente la [documentazione Android](http://developer.android.com/guide/topics/resources/string-resource.html#Plurals). Se una determinata lingua non richiede gestione 'speciale', quelli `quantity` stringhe verranno ignorate (in inglese, ad esempio, utilizza solo `one` e `other`; specificando un `zero` stringa non ha alcun effetto, non verrà utilizzato).

### <a name="images"></a>Immagini

Le immagini localizzate seguono le stesse regole come file di stringhe: tutte le immagini di cui viene fatto riferimento nell'applicazione devono essere inserite in **drawable** directory pertanto non c'è un fallback.

Immagini specifiche delle impostazioni locali devono quindi trovarsi nel cartelle drawable completo, ad esempio **drawable-es** oppure **drawable Giappone** (identificatori di dpi possono anche essere aggiunto).

In questo screenshot, quattro immagini vengono salvate nel **drawable** directory, ma solo uno **flag.png**, sia localizzata copie in altre directory.

![Schermata di più cartelle drawable, ognuno dei quali contiene uno o più file con estensione png localizzato](localization-images/drawable.png)


#### <a name="other-resource-types"></a>Altri tipi di risorse

È anche possibile fornire altri tipi di alternative, risorse specifiche della lingua, inclusi i layout, animazioni e i file non elaborati. Questo significa che è possibile fornire un layout di schermata specifica per uno o più delle lingue di destinazione, ad esempio è possibile creare un layout in modo specifico per il tedesco che consente di etichette di testo molto lunghi.

Ha introdotto il supporto per Android 4.2 [lingue destra-sinistra (RTL)](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html) se si imposta l'impostazione dell'applicazione `android:supportsRtl="true"`. Il qualificatore delle risorse `"ldrtl"` possono essere inclusi in un nome di directory per contenere i layout personalizzati che sono progettati per la visualizzazione destra-sinistra.

Per ulteriori informazioni sulla denominazione directory delle risorse e del fallback, vedere la documentazione di Android [offrire risorse alternative](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources).


### <a name="app-name"></a>Nome dell'App

Il nome dell'applicazione è semplice da localizzare utilizzando un `@string/id` in per il `MainLauncher` attività:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>Lingue destra-sinistra (RTL)

Android 4.2 e versioni successive fornisce supporto completo per i layout da destra a sinistra, descritti dettagliatamente nel [blog di supporto da destra a sinistra Native](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html).

Quando si usa Android 4.2 (livello API 17) e l'allineamento più recente, i valori possono essere specificati con `start` e `end` invece di `left` e `right` (ad esempio `android:paddingStart`). Sono disponibili anche nuove API, ad esempio `LayoutDirection`, `TextDirection`, e `TextAlignment` per la creazione di schermate adattabili per i lettori da destra a sinistra.

La schermata seguente mostra le [localizzata **Tasky** esempio](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) in arabo:

[![Screenshot dell'app Tasky in arabo](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

L'esempio di screenshot seguente mostra le [localizzata **Tasky** esempio](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) in ebraico:

[![Screenshot dell'app Tasky in ebraico](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

Testo da destra a sinistra è localizzato usando **Strings. XML** file nello stesso modo come testo di conservazione a lungo termine.

## <a name="testing"></a>Test

Assicurarsi di testare completamente le impostazioni locali predefinite. L'applicazione viene interrotta se non è possibile caricare le risorse predefinite per qualche motivo (ad esempio sono mancanti).

### <a name="emulator-testing"></a>Emulatore di test

Fare riferimento a Google [test in un emulatore Android](https://developer.android.com/guide/topics/resources/localization.html#testing) per istruzioni su come impostare un emulatore per impostazioni locali specifiche utilizzando la shell ADB.

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>Test del dispositivo

Per eseguire il test in un dispositivo, modificare la lingua nel **impostazioni** app.
**Suggerimento:** prendere nota delle icone e del percorso delle voci di menu in modo che è possibile ripristinare la lingua per l'impostazione originale.


## <a name="summary"></a>Riepilogo

Questo articolo tratta i fondamenti della localizzazione di applicazioni Android tramite la gestione delle risorse predefiniti. Altre informazioni su i18n e L10n per iOS, Android e App multipiattaforma (tra cui xamarin. Forms) nelle [questa guida per lo sviluppo multipiattaforma](~/cross-platform/app-fundamentals/localization.md).



## <a name="related-links"></a>Collegamenti correlati

- [Tasky (localizzato in codice) (esempio)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Android con le risorse di localizzazione](http://developer.android.com/guide/topics/resources/localization.html)
- [Panoramica sulla localizzazione multipiattaforma](~/cross-platform/app-fundamentals/localization.md)
- [Localizzazione di xamarin. Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localizzazione di iOS](~/ios/app-fundamentals/localization/index.md)
