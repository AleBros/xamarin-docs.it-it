---
title: Localizzazione di Android
description: Questo documento vengono presentate le funzionalità di localizzazione di Android SDK e come accedervi con Xamarin.
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 076cadd16c3953ee4e06193190b59035ad57f2c1
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="android-localization"></a>Localizzazione di Android

_Questo documento vengono presentate le funzionalità di localizzazione di Android SDK e come accedervi con Xamarin._

## <a name="android-platform-features"></a>Funzionalità della piattaforma Android

In questa sezione descrive le funzionalità principali di localizzazione di Android. Ignorare il [nella sezione successiva](#basics) per visualizzare codice specifico ed esempi.

### <a name="locale"></a>Impostazioni locali

Gli utenti di scegliere la lingua in **Impostazioni > lingua & input**. Questa impostazione controlla sia la lingua di visualizzazione e impostazioni internazionali usate (ad es. per la data e formattazione di numero).

Le impostazioni locali correnti è possibile eseguire query tramite il contesto corrente `Resources`:

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

Questo valore è un identificatore delle impostazioni locali che contiene un codice di lingua e un codice impostazioni locali, separati da un carattere di sottolineatura. Per riferimento, di seguito è un [elenco delle impostazioni locali di Java](http://www.oracle.com/technetwork/java/javase/locales-137662.html) e [impostazioni locali supportate Android tramite StackOverflow](http://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android).

Ecco alcuni esempi comuni:

* `en_US` per l'inglese (Regno Statees)
* `es_ES` per lo spagnolo (Spagna)
* `ja_JP` per il giapponese (Giappone)
* `zh_CN` per il cinese (Cina)
* `zh_TW` per il cinese (Taiwan)
* `pt_PT` per il portoghese (Portogallo)
* `pt_BR` per il portoghese (Brasile)

### <a name="localechanged"></a>LOCALE_CHANGED

Genera l'errore Android `android.intent.action.LOCALE_CHANGED` quando l'utente modifica la selezione della lingua.

Le attività è possono scegliere di gestire questa situazione impostando il `android:configChanges` attributo sull'attività, simile al seguente:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
        ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>Nozioni di internazionalizzazione in Android

Strategia di localizzazione di Android presenta i seguenti componenti chiave.

- Cartelle di risorse che contengono le stringhe localizzate, immagini e altre risorse.

- `GetText` metodo, che viene utilizzato per recuperare le stringhe localizzate nel codice

- `@string/id` nei file AXML per posizionare automaticamente le stringhe localizzate nel layout.

### <a name="resource-folders"></a>Cartelle di risorse

Applicazioni Android gestiscono la maggior parte dei contenuti in cartelle di risorse, ad esempio:

* **layout** -contiene i file di layout AXML.
* **drawable** -contiene le immagini e altre risorse drawable.
* **i valori** -contiene le stringhe.
* **Raw** -contiene i file di dati.

La maggior parte degli sviluppatori hanno già familiari con l'utilizzo di **dpi** suffissi sul **drawable** directory per fornire più versioni di un'immagine, consentendo di scegliere la versione corretta per ogni dispositivo Android. Lo stesso meccanismo utilizzato per fornire più traduzioni delle lingue aggiungendo le directory delle risorse con gli identificatori di lingua e le impostazioni cultura.

![Schermata di cartelle di risorse/drawable e risorse di valori per gli identificatori di lingua più](localization-images/resources.png)

> [!NOTE]
> Quando si specifica un linguaggio di livello superiore come `es` solo due caratteri sono obbligatori; tuttavia, quando si specifica una completo delle impostazioni locali, il formato del nome directory richiede un trattino e una minuscola **r** per separare le due parti, ad esempio **pt rBR** o **zh-salti riga**. Confrontando questo funzionamento con il valore restituito nel codice, che è un carattere di sottolineatura (ad es. `pt_BR`). Entrambi sono diverse per il valore .NET `CultureInfo` , utilizzato dalla classe che dispone di un trattino solo (ad es. `pt-BR`). Tenere presente queste differenze quando si utilizzano piattaforme Xamarin.

#### <a name="stringsxml-file-format"></a>Formato di file Strings

Localizzato **valori** directory (ad es. **i valori es** o **valori-pt-rBR**) deve contenere un file denominato **Strings** che conterrà il testo tradotto delle impostazioni locali.

Ogni stringa traducibile sono un elemento XML con la risorsa con ID specificato come il `name` attributo e la stringa convertita come valore:

```xml
<string name="app_name">TaskyL10n</string>
```

È necessario eseguire l'escape in base alle normale regole XML e `name` deve essere un ID di risorsa di Android valido (senza spazi o trattini). Di seguito è riportato un esempio del file di stringhe (in lingua inglese) predefinito per un esempio:

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

La directory spagnola **valori es** contiene un file con lo stesso nome (**Strings**) che contiene le traduzioni:

**i valori-es/Strings**

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

![Schermata di più valori di cartelle, ognuna delle quali contiene un file Strings](localization-images/values.png)

Con la configurazione di file di stringhe, è possono fare riferimento ai valori tradotti nel layout e nel codice.

### <a name="axml-layout-files"></a>File di Layout AXML

Per fare riferimento alle stringhe localizzate in file di layout, utilizzare il `@string/id` sintassi. Questo frammento XML illustrato nell'esempio `text` impostate con localizzata (altri attributi sono stati omessi) ID di risorsa:

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

Per recuperare le stringhe tradotte nel codice, utilizzare il `GetText` (metodo) e passare l'ID risorsa:

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>Quantità stringhe

Risorse stringa Android consentono inoltre di creare *stringhe quantità* che consente ai traduttori di fornire più traduzioni per quantità diverse, ad esempio:

* "Si è a sinistra 1 attività".
* "Sono presenti 2 attività ancora scopo."

(anziché un oggetto generico "There are sinistra attività n").

Nel **Strings**

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

Per eseguire il rendering di utilizzare la stringa completa il `GetQuantityString` , passando l'ID risorsa e il valore da visualizzare (che viene passato due volte). Il secondo parametro è utilizzato da Android per determinare *che* `quantity` stringa per l'utilizzo, il terzo parametro è il valore di sostituzione effettivamente nella stringa (sono necessari entrambi).

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

Essi è descritto in dettaglio nel [documenti Android](http://developer.android.com/guide/topics/resources/string-resource.html#Plurals). Se una determinata lingua non richiede 'special' gestione quelli `quantity` stringhe verranno ignorate (inglese, ad esempio, utilizza solo `one` e `other`; specificando un `zero` stringa avrà alcun effetto, non verrà utilizzato).

### <a name="images"></a>Immagini

Immagini localizzate seguono le stesse regole come file di stringhe: tutte le immagini a cui fa riferimento nell'applicazione devono essere inserite in **drawable** directory, pertanto non c'è un fallback.

Immagini delle impostazioni locali specifiche devono quindi essere inserite nelle cartelle drawable completo, ad esempio **drawable es** o **drawable ja** (identificatori di dpi è inoltre possibile aggiungere).

In questa schermata, quattro immagini vengono salvate nel **drawable** directory, ma solo uno **flag.png**, sia localizzata copie delle altre directory.

![Schermata di più cartelle drawable, ognuna delle quali contiene uno o più file con estensione png localizzata](localization-images/drawable.png)


#### <a name="other-resource-types"></a>Altri tipi di risorse

È anche possibile fornire altri tipi di alternative, risorse specifiche della lingua, inclusi i layout, animazioni e file non elaborati. Ciò significa che è possibile fornire un layout della schermata specifico per uno o più le lingue di destinazione, ad esempio è possibile creare un layout in modo specifico per il tedesco che consente di etichette di testo molto lungo.

Introduzione del supporto per Android 4.2 [lingue destra-sinistra (RTL)](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html) se si imposta l'impostazione dell'applicazione `android:supportsRtl="true"`. Il qualificatore delle risorse `"ldrtl"` può essere incluso in un nome di direcory per contenere i layout personalizzati con cui sono progettati per visualizzare a destra.

Per ulteriori informazioni sulla denominazione di directory di risorse e di fallback, fare riferimento ai documenti per Android [offrire risorse alternative](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources).


### <a name="app-name"></a>Nome dell'App

Il nome dell'applicazione è facile da localizzare utilizzando un `@string/id` in per il `MainLauncher` attività:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>Lingue da destra a sinistra (RTL)

Android 4.2 e successive fornisce supporto completo per il layout da destra a sinistra, descritto in dettaglio nel [blog del supporto da destra a sinistra nativo](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html).

Quando si usa Android 4.2 (livello API 17) e aligment più recente, è possibile specificare i valori con `start` e `end` anziché `left` e `right` (ad esempio `android:paddingStart`). Sono inoltre disponibili nuove API come `LayoutDirection`, `TextDirection`, e `TextAlignment` per compilare schermate che adattano per i reader da destra a sinistra.

La schermata seguente mostra il [localizzata **Tasky** esempio](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) in arabo:

[![Schermata dell'app Tasky in arabo](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

Illustrato nella schermata successiva il [localizzata **Tasky** esempio](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) (versione ebraica):

[![Schermata dell'app Tasky (versione ebraica)](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

Testo da destra a sinistra è localizzato mediante **Strings** file nello stesso modo come testo LTR.

## <a name="testing"></a>Test

Assicurarsi di testare accuratamente le impostazioni locali predefinite. Se non è possibile caricare le risorse predefinite per qualche motivo (ad esempio sono disponibili), verificherà un arresto anomalo dell'applicazione.

### <a name="emulator-testing"></a>Emulatore di test

Fare riferimento a Google [test in un emulatore Android](https://developer.android.com/guide/topics/resources/localization.html#testing) per istruzioni su come impostare un emulatore per impostazioni locali specifiche utilizzando la shell ADB.

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>Test del dispositivo

Per eseguire il test in un dispositivo, modificare la lingua di **impostazioni** app.
**Suggerimento:** prendere nota delle icone e posizione delle voci di menu in modo che è possibile ripristinare la lingua per l'impostazione originale.


## <a name="summary"></a>Riepilogo

Questo articolo vengono illustrate le nozioni di base di localizzazione di applicazioni Android mediante la gestione di risorse predefiniti. Informazioni sulle i18n e L10n per iOS, Android e App per più piattaforme (incluse xamarin. Forms) in [questa guida multipiattaforma](~/cross-platform/app-fundamentals/localization.md).



## <a name="related-links"></a>Collegamenti correlati

- [Tasky (localizzato nel codice) (esempio)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Android localizzazione di risorse](http://developer.android.com/guide/topics/resources/localization.html)
- [Cenni preliminari sulla localizzazione multipiattaforma](~/cross-platform/app-fundamentals/localization.md)
- [Localizzazione di xamarin. Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localizzazione di iOS](~/ios/app-fundamentals/localization/index.md)
