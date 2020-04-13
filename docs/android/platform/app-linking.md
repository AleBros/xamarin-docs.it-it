---
title: Collegamento di app in Android
description: Questa guida illustra in che modo Android 6.0 supporta il collegamento di app, una tecnica che consente alle app per dispositivi mobili di rispondere agli URL nei siti Web. Verrà illustrato il collegamento di app, come implementare il collegamento di app in un'applicazione Android 6.0 e come configurare un sito Web per concedere autorizzazioni all'app per dispositivi mobili per un dominio.
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: af90c286d2bb960a9f78547dd15c3d98a69529ae
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487828"
---
# <a name="app-linking-in-android"></a>Collegamento di app in Android

_Questa guida illustra in che modo Android 6.0 supporta il collegamento di app, una tecnica che consente alle app per dispositivi mobili di rispondere agli URL nei siti Web. Verrà illustrato il collegamento di app, come implementare il collegamento di app in un'applicazione Android 6.0 e come configurare un sito Web per concedere autorizzazioni all'app per dispositivi mobili per un dominio._

## <a name="app-linking-overview"></a>Panoramica sul collegamento di app

Le applicazioni mobili non vivono più in un silo &ndash; in molti casi sono una componente importante delle loro attività, insieme al loro sito web. È auspicabile che le aziende colleghino senza problemi la loro presenza web e le loro applicazioni mobili, con link su un sito web che lanciano applicazioni per dispositivi mobili e visualizzano contenuti pertinenti nell'app per dispositivi mobili. *Il collegamento* di app (noto anche come *collegamento diretto*) è una tecnica che consente a un dispositivo mobile di rispondere a un URI e avviare un'applicazione per dispositivi mobili che corrisponde a tale URI.

Android gestisce il collegamento di app tramite il sistema &ndash; di *finalità* quando l'utente fa clic su un collegamento in un browser per dispositivi mobili, il browser per dispositivi mobili invierà l'intento che Android delega a un'applicazione registrata. Ad esempio, facendo clic su un collegamento su un sito Web di cucina si aprirà un'app per dispositivi mobili associata a tale sito Web e verrà visualizzata una ricetta specifica per l'utente. Se è presente più di un'applicazione registrata per gestire tale finalità, quindi Android genererà ciò che è noto come una finestra di dialogo di disambiguazione che chiederà a un utente quale applicazione per selezionare l'applicazione che deve gestire l'intento, ad esempio:If there is more than one application registered to handle that intent, then Android will raise what is known as a *disambiguation dialog* that will ask a user what application to select the application that should handle the intent, for example:

![Schermata di esempio di una finestra di dialogo di disambiguazione](app-linking-images/01-disambiguation-dialog.png)

Android 6.0 migliora su questo utilizzando la gestione automatica dei collegamenti. È possibile che Android registri automaticamente un'applicazione come &ndash; gestore predefinito per un URI, l'app avvierà automaticamente e passerà direttamente all'attività pertinente. Come Android 6.0 decide di gestire un clic URI dipende dai seguenti criteri:

1. **Un'app esistente è già associata all'URI** &ndash; L'utente potrebbe aver già associato un'app esistente a un URI. In tal caso, Android continuerà a utilizzare tale applicazione.
2. **Nessuna app esistente è associata all'URI, ma viene installata** &ndash; un'app di supporto In questo scenario, l'utente non ha specificato un'app esistente, pertanto Android utilizzerà l'applicazione di supporto installata per gestire la richiesta.
3. **Nessuna app esistente è associata all'URI, ma molte app** &ndash; di supporto vengono installate Poiché sono presenti più applicazioni che supportano l'URI, verrà visualizzata la finestra di dialogo di disambiguazione e l'utente dovrà selezionare l'app che gestirà l'URI.

Se l'utente non dispone di app che supportano l'URI e una viene successivamente installata, Android imposterà tale applicazione come gestore predefinito per l'URI dopo aver verificato l'associazione con il sito Web associato all'URI.

Questa guida illustra come configurare un'applicazione Android 6.0 e come creare e pubblicare il file Digital Asset Links per supportare il collegamento di app in Android 6.0.

## <a name="requirements"></a>Requisiti

Questa guida richiede Xamarin.Android 6.1 e un'applicazione destinata ad Android 6.0 (livello API 23) o superiore.

Il collegamento di app è possibile nelle versioni precedenti di Android usando il [pacchetto Rivets NuGet](https://www.nuget.org/packages/Rivets/) dell'archivio dei componenti Xamarin. Il pacchetto Rivets non è compatibile con il collegamento di app in Android 6.0; non supporta il collegamento di app Android 6.0.

## <a name="configuring-app-linking-in-android-60"></a>Configurazione del collegamento di app in Android 6.0Configuring App-Linking in Android 6.0

La configurazione dei collegamenti alle app in Android 6.0 prevede due passaggi principali:Setting up app-links in Android 6.0 involves two major steps:

1. &ndash; **L'aggiunta di uno o più filtri finalità per l'URI del sito Web** guida Android su come gestire un clic su URL in un browser per dispositivi mobili.
2. **Pubblicazione di un file *JSON di collegamenti di risorse digitali* sul sito Web,** &ndash; ovvero un file che viene caricato in un sito Web e utilizzato da Android per verificare la relazione tra l'app per dispositivi mobili e il dominio del sito Web. Senza questo, Android non può installare l'app come handle predefinito per URI; l'utente deve farlo manualmente.

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>Configurazione del filtro intento

È necessario configurare un filtro intento che esegue il mapping di un URI (o possibile un set di URI) da un sito Web a un'attività in un'applicazione Android.It is necessary to configure an intent filter that maps a URI (or possible a set of URIs) from a website to an Activity in an Android application. In Xamarin.Android, questa relazione viene stabilita eseguendo l'ornamento di un'attività con [il IntentFilterAttribute](xref:Android.App.IntentFilterAttribute). Il filtro finalità deve dichiarare le informazioni seguenti:The intent filter must declare the following information:

- **`Intent.ActionView`**&ndash; In questo modo verrà registrato il filtro intento per rispondere alle richieste di visualizzazione delle informazioni
- **`Categories`**&ndash; Il filtro intento deve registrare sia **[Intent.CategoryBrowsable](xref:Android.Content.Intent.CategoryBrowsable)** **[che Intent.CategoryDefault](xref:Android.Content.Intent.CategoryDefault)** per poter gestire correttamente l'URI Web.
- **`DataScheme`**&ndash; Il filtro finalità deve dichiarare `http` e/o `https`. Questi sono gli unici due schemi validi.
- **`DataHost`**&ndash; Questo è il dominio da cui avranno origine gli URI.
- **`DataPathPrefix`**&ndash; Si tratta di un percorso facoltativo per le risorse del sito Web.
- **`AutoVerify`**&ndash; L'attributo `autoVerify` indica ad Android di verificare la relazione tra l'applicazione e il sito Web. Questo sarà discusso più avanti.

Nell'esempio seguente viene illustrato come utilizzare [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) per gestire i collegamenti da `https://www.recipe-app.com/recipes` e verso `http://www.recipe-app.com/recipes`:

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

Android verificherà ogni host identificato dai filtri di finalità rispetto al file di risorse digitali nel sito Web prima di registrare l'applicazione come gestore predefinito per un URI. Tutti i filtri finalità devono superare la verifica prima che Android possa stabilire l'app come gestore predefinito.

### <a name="creating-the-digital-assets-link-file"></a>Creazione del file di collegamento delle risorse digitali

Il collegamento dell'app Android 6.0 richiede che Android verifichi l'associazione tra l'applicazione e il sito Web prima di impostare l'applicazione come gestore predefinito per l'URI. Questa verifica si verifica quando l'applicazione viene installata per la prima volta. Il file *Digital Assets Links* è un file JSON ospitato dai domini Web pertinenti.

> [!NOTE]
> L'attributo `android:autoVerify` deve essere &ndash; impostato dal filtro finalità altrimenti Android non eseguirà la verifica.

Il file viene inserito dal webmaster **https://domain/.well-known/assetlinks.json**del dominio nella posizione .

Il file di risorse digitali contiene i metadati necessari ad Android per verificare l'associazione. Un file **assetlinks.json** ha le seguenti coppie chiave-valore:

- `namespace`&ndash; lo spazio dei nomi dell'applicazione Android.
- `package_name`&ndash; il nome del pacchetto dell'applicazione Android (dichiarato nel manifesto dell'applicazione).
- `sha256_cert_fingerprints`&ndash; le impronte digitali SHA256 dell'applicazione firmata. Per ulteriori informazioni su come ottenere l'impronta digitale SHA1 di un'applicazione, vedere la guida [Ricerca della firma MD5 o SHA1 del Keystore.](~/android/deploy-test/signing/keystore-signature.md)

Il frammento di codice seguente è un esempio di assetlinks.json con una singola applicazione elencata:The following snippet is an example of **assetlinks.json** with a single application listed:

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

È possibile registrare più di un'impronta digitale SHA256 per supportare versioni o build diverse dell'applicazione. Il prossimo file assetlinks.json è un esempio di registrazione di più applicazioni:This next **assetlinks.json** file is an example of registering multiple applications:

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

Il sito web di [Google Digital Asset Links](https://developers.google.com/digital-asset-links/tools/generator) dispone di uno strumento online che può aiutare a creare e testare il file delle risorse digitali.

### <a name="testing-app-links"></a>Test dei collegamenti alle app

Dopo aver implementato i collegamenti alle app, le varie parti devono essere testate per assicurarsi che funzionino come previsto.

È possibile verificare che il file Digital Assets sia formattato correttamente e ospitato utilizzando l'API Digital Asset Links di Google, come illustrato in questo esempio:

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

Esistono due test che possono essere eseguiti per garantire che i filtri finalità siano stati configurati correttamente e che l'app sia impostata come gestore predefinito per un URI:There are two tests that can be performed to ensure that the intent filters have been properly configured and that the app is set as the default handler for a URI:

1. Il file di risorse digitali è ospitato correttamente come descritto in precedenza. Il primo test invierà un intento che Android deve reindirizzare all'applicazione per dispositivi mobili. L'applicazione Android deve avviare e visualizzare l'attività registrata per l'URL. Al prompt dei comandi digitare:

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2. Visualizzare i criteri di gestione dei collegamenti esistenti per le applicazioni installate in un determinato dispositivo. Il comando seguente eseguirà il dump di un elenco di criteri di collegamento per ogni utente nel dispositivo con le informazioni seguenti. Al prompt dei comandi digitare il comando seguente:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    - **`Package`**&ndash; Nome del pacchetto dell'applicazione.
    - **`Domain`**&ndash; I domini (separati da spazi) i cui collegamenti Web saranno gestiti dall'applicazione
    - **`Status`**&ndash; Questo è lo stato corrente di gestione dei collegamenti per l'app. Il valore di **indica** sempre `android:autoVerify=true` che l'applicazione ha dichiarato e ha superato la verifica del sistema. È seguito da un numero esadecimale che rappresenta il record del sistema Android della preferenza.

    Ad esempio:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>Riepilogo

Questa guida illustra come funziona il collegamento di app in Android 6.0.This guide discussed how app-linking works in Android 6.0. È stato quindi illustrato come configurare un'applicazione Android 6.0 per supportare e rispondere ai collegamenti dell'app. È stato inoltre illustrato come testare il collegamento di app in un'applicazione Android.It also discussed how to test app-linking in an Android application.

## <a name="related-links"></a>Collegamenti correlati

- [Ricerca della firma MD5 o SHA1 dell'archivio chiavi](~/android/deploy-test/signing/keystore-signature.md)
- [AppLinks (AppLinks)](https://developers.facebook.com/docs/applinks)
- [Collegamenti alle risorse digitali di Google](https://developers.google.com/digital-asset-links/)
- [Generatore e tester dell'elenco di istruzioni](https://developers.google.com/digital-asset-links/tools/generator)
