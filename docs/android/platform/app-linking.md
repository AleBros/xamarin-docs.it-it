---
title: Collegamento di app in Android
description: Questa guida illustra in che modo Android 6,0 supporta il collegamento di app, una tecnica che consente alle app per dispositivi mobili di rispondere agli URL nei siti Web. Verrà illustrato il collegamento di app, come implementare il collegamento di app in un'applicazione Android 6,0 e come configurare un sito Web per concedere le autorizzazioni all'app per dispositivi mobili per un dominio.
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d65e8fabff88489571bba9d03379ff605a6ed0fe
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757736"
---
# <a name="app-linking-in-android"></a>Collegamento di app in Android

_Questa guida illustra in che modo Android 6,0 supporta il collegamento di app, una tecnica che consente alle app per dispositivi mobili di rispondere agli URL nei siti Web. Verrà illustrato il collegamento di app, come implementare il collegamento di app in un'applicazione Android 6,0 e come configurare un sito Web per concedere le autorizzazioni all'app per dispositivi mobili per un dominio._

## <a name="app-linking-overview"></a>Panoramica sul collegamento di app

Le applicazioni per dispositivi mobili non sono più &ndash; disponibili in un silo in molti casi sono componenti importanti delle loro attività, insieme al relativo sito Web. È auspicabile che le aziende connettano senza difficoltà la propria presenza Web e le applicazioni per dispositivi mobili, con collegamenti in un sito Web che avvia applicazioni per dispositivi mobili e visualizzando contenuto pertinente nell'app per dispositivi mobili. *Collegamento di app* (noto anche come *deep linking*) è una tecnica che consente a un dispositivo mobile di rispondere a un URI e avviare un'applicazione per dispositivi mobili che corrisponde a tale URI.

Android gestisce il collegamento delle app tramite &ndash; il *sistema Intent* quando l'utente fa clic su un collegamento in un browser per dispositivi mobili, il browser per dispositivi mobili invierà a un'applicazione registrata una finalità che Android delegherà. Se ad esempio si fa clic su un collegamento in un sito Web di cucina, si aprirà un'app per dispositivi mobili associata al sito Web e si visualizzerà una ricetta specifica per l'utente. Se è presente più di un'applicazione registrata per gestire tale finalità, Android genererà una finestra di dialogo di risoluzione dell' *ambiguità* che chiederà a un utente quale applicazione selezionare l'applicazione che deve gestire lo scopo, ad esempio:

![Schermata di esempio di una finestra di dialogo di risoluzione dell'ambiguità](app-linking-images/01-disambiguation-dialog.png)

Android 6,0 migliora questa operazione usando la gestione automatica dei collegamenti. È possibile che Android registri automaticamente un'applicazione come gestore predefinito per un URI &ndash; . l'app verrà avviata automaticamente e passa direttamente all'attività pertinente. Il modo in cui Android 6,0 decide di gestire un URI fare clic dipende dai criteri seguenti:

1. **Un'app esistente è già associata all'URI** &ndash; L'utente potrebbe avere già associato un'app esistente a un URI. In tal caso, Android continuerà a usare l'applicazione.
2. **Nessuna app esistente è associata all'URI, ma è installata un'app di supporto** &ndash; In questo scenario l'utente non ha specificato un'app esistente, quindi Android userà l'applicazione di supporto installata per gestire la richiesta.
3. **Nessuna app esistente è associata all'URI, ma sono installate molte app di supporto** &ndash; Poiché sono presenti più applicazioni che supportano l'URI, viene visualizzata la finestra di dialogo di risoluzione dell'ambiguità e l'utente deve selezionare l'app che gestirà l'URI.

Se per l'utente non sono installate app che supportano l'URI e ne viene successivamente installata una, l'applicazione verrà impostata da Android come gestore predefinito per l'URI dopo la verifica dell'associazione con il sito Web associato all'URI.

Questa guida illustra come configurare un'applicazione Android 6,0 e come creare e pubblicare il file di collegamenti di asset digitali per supportare il collegamento di app in Android 6,0.

## <a name="requirements"></a>Requisiti

Questa guida richiede Novell. Android 6,1 e un'applicazione destinata a Android 6,0 (livello API 23) o superiore.

Il collegamento di app è possibile nelle versioni precedenti di Android usando il [pacchetto NuGet dei rivetti](https://www.nuget.org/packages/Rivets/) dall'archivio componenti di Novell. Il pacchetto dei rivetti non è compatibile con il collegamento di app in Android 6,0; non supporta il collegamento di app Android 6,0.

## <a name="configuring-app-linking-in-android-60"></a>Configurazione del collegamento di app in Android 6,0

La configurazione di collegamenti app in Android 6,0 prevede due passaggi principali:

1. **Aggiunta di uno o più filtri per finalità per l'URI del sito Web** &ndash; la guida per i filtri per le finalità di Android in come gestire un URL fare clic in un browser per dispositivi mobili.
2. **La pubblicazione di un file *JSON dei collegamenti asset digitali* nel sito Web** &ndash; è un file che viene caricato in un sito Web e viene usato da Android per verificare la relazione tra l'app per dispositivi mobili e il dominio del sito Web. Senza questo, Android non può installare l'app come handle predefinito per l'URI; l'utente deve eseguire questa operazione manualmente.

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>Configurazione del filtro preventivo

È necessario configurare un filtro preventivo che esegue il mapping di un URI (o un set di URI) da un sito Web a un'attività in un'applicazione Android. In Novell. Android questa relazione viene stabilita decorando un'attività con il [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute). Il filtro preventivo deve dichiarare le seguenti informazioni:

- **`Intent.ActionView`** &ndash; Questa operazione registrerà il filtro preventivo per rispondere alle richieste di visualizzazione delle informazioni
- **`Categories`** Il filtro preventivo deve registrare sia **[Intent. CategoryBrowsable](xref:Android.Content.Intent.CategoryBrowsable)** che **[Intent. CategoryDefault](xref:Android.Content.Intent.CategoryDefault)** per poter gestire correttamente l'URI Web. &ndash;
- **`DataScheme`** Il filtro preventivo deve dichiarare `http` e/o `https`. &ndash; Questi sono gli unici due schemi validi.
- **`DataHost`** &ndash; Si tratta del dominio da cui derivano gli URI.
- **`DataPathPrefix`** &ndash; Si tratta di un percorso facoltativo per le risorse nel sito Web.
- **`AutoVerify`** &ndash; L'`autoVerify` attributo indica a Android di verificare la relazione tra l'applicazione e il sito Web. Questo argomento verrà illustrato di seguito.

Nell'esempio seguente viene illustrato come utilizzare [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) per gestire i collegamenti da `https://www.recipe-app.com/recipes` e da `http://www.recipe-app.com/recipes`:

```csharp
[IntentFilter(new [] { Intent.ActionView },
              Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
              DataScheme = "http",
              DataHost = "recipe-app.com",
              DataPathPrefix = "/recipe",
              AutoVerify=true)]
public class RecipeActivity : Activity
{
    // Code for the activity omitted
}
```

Android verificherà ogni host identificato da Intent filters nel file di asset digitali nel sito Web prima di registrare l'applicazione come gestore predefinito per un URI. Tutti i filtri per finalità devono superare la verifica prima che Android possa stabilire l'app come gestore predefinito.

### <a name="creating-the-digital-assets-link-file"></a>Creazione del file di collegamento asset digitali

Il collegamento app Android 6,0 richiede che Android verifichi l'associazione tra l'applicazione e il sito Web prima di impostare l'applicazione come gestore predefinito per l'URI. Questa verifica si verifica quando l'applicazione viene installata per la prima volta. Il file di *collegamenti di asset digitali* è un file JSON ospitato dai domini webdomain pertinenti.

> [!NOTE]
> L' `android:autoVerify` attributo deve essere impostato dal filtro &ndash; preventivo altrimenti Android non eseguirà la verifica.

Il file viene inserito dal webmaster del dominio nel percorso **https://domain/.well-known/assetlinks.json** .

Il file di asset digitale contiene i metadati necessari a Android per verificare l'associazione. Un file **assetlinks. JSON** presenta le coppie chiave-valore seguenti:

- `namespace`&ndash; spazio dei nomi dell'applicazione Android.
- `package_name`&ndash; nome del pacchetto dell'applicazione Android (dichiarato nel manifesto dell'applicazione).
- `sha256_cert_fingerprints`&ndash; impronte digitali SHA256 dell'applicazione firmata. Per altre informazioni su come ottenere l'impronta digitale SHA1 di un'applicazione, vedere la Guida [trovare la firma MD5 o SHA1 dell'archivio](~/android/deploy-test/signing/keystore-signature.md) chiavi.

Il frammento di codice seguente è un esempio di **assetlinks. JSON** con una singola applicazione elencata:

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"com.example",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

È possibile registrare più di un'impronta digitale SHA256 per supportare versioni o Build diverse dell'applicazione. Il file **assetlinks. JSON** successivo è un esempio di registrazione di più applicazioni:

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.puppies.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   },
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.monkeys.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

Il [sito Web di Google Digital Asset Links](https://developers.google.com/digital-asset-links/tools/generator) include uno strumento online che può risultare utile per la creazione e il test del file di asset digitali.

### <a name="testing-app-links"></a>Test di collegamenti app

Dopo aver implementato i collegamenti all'app, è necessario testare le varie parti per assicurarsi che funzionino come previsto.

È possibile verificare che il file di asset digitali sia formattato correttamente e che sia ospitato usando l'API collegamenti di asset digitali di Google, come illustrato nell'esempio seguente:

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

È possibile eseguire due test per assicurarsi che i filtri per finalità siano stati configurati correttamente e che l'applicazione sia impostata come gestore predefinito per un URI:

1. Il file di asset digitale è ospitato correttamente come descritto in precedenza. Il primo test invierà una finalità che Android deve reindirizzare all'applicazione per dispositivi mobili. L'applicazione Android dovrebbe avviare e visualizzare l'attività registrata per l'URL. Al prompt dei comandi digitare:

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2. Visualizza i criteri di gestione dei collegamenti esistenti per le applicazioni installate in un determinato dispositivo. Con il comando seguente viene eseguito il dump di un elenco di criteri di collegamento per ogni utente del dispositivo con le informazioni seguenti. Al prompt dei comandi digitare il seguente comando:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    - **`Package`** &ndash; Nome del pacchetto dell'applicazione.
    - **`Domain`** &ndash; Domini (separati da spazi) i cui collegamenti Web verranno gestiti dall'applicazione
    - **`Status`** &ndash; Si tratta dello stato corrente di gestione dei collegamenti per l'app. Il valore indica **sempre** che l'applicazione ha `android:autoVerify=true` dichiarato ed è stata superata la verifica del sistema. È seguito da un numero esadecimale che rappresenta il record del sistema Android della preferenza.

    Ad esempio:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>Riepilogo

Questa guida ha illustrato come funziona il collegamento di app in Android 6,0. È stato quindi illustrato come configurare un'applicazione Android 6,0 per supportare e rispondere ai collegamenti all'app. È stato anche illustrato come testare il collegamento di app in un'applicazione Android.

## <a name="related-links"></a>Collegamenti correlati

- [Ricerca della firma MD5 o SHA1 dell'archivio chiavi](~/android/deploy-test/signing/keystore-signature.md)
- [Attività e Intent](https://university.xamarin.com/classes#4)
- [AppLinks](http://applinks.org/)
- [Collegamenti a Google Digital Assets](https://developers.google.com/digital-asset-links/)
- [Generatore elenco di istruzioni e tester](https://developers.google.com/digital-asset-links/tools/generator)
