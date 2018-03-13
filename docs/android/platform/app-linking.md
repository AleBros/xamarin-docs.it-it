---
title: Collegamento App in Android
description: "Questa guida illustra come Android 6.0 supporta l'app e il collegamento, una tecnica che consente di App per dispositivi mobili a rispondere a URL nei siti Web. Parlerà quali app per il collegamento viene illustrato come implementare il collegamento app in un'applicazione Android 6.0 e come configurare un sito Web per concedere le autorizzazioni per l'app mobile per un dominio."
ms.topic: article
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 78fef780728ba1c2a3b9978504058f7a386b0e7d
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="app-linking-in-android"></a>Collegamento App in Android

_Questa guida illustra come Android 6.0 supporta l'app e il collegamento, una tecnica che consente di App per dispositivi mobili a rispondere a URL nei siti Web. Parlerà quali app per il collegamento viene illustrato come implementare il collegamento app in un'applicazione Android 6.0 e come configurare un sito Web per concedere le autorizzazioni per l'app mobile per un dominio._

## <a name="app-linking-overview"></a>Panoramica del collegamento App

Applicazioni per dispositivi mobili non è più attivo in un silo &ndash; in molti casi si tratta di un importante componenti dell'azienda, con il sito Web. È consigliabile per aziende di connettersi facilmente loro presenza web e applicazioni per dispositivi mobili, con collegamenti in un sito Web, avvio di applicazioni per dispositivi mobili e la visualizzazione contenuto rilevante nell'app mobile. *Collegamento app* (detta anche *il collegamento diretto*) è una tecnica che consente a un dispositivo mobile per rispondere a un URI e avviare un'applicazione per dispositivi mobili che corrisponde a quell'URI.

Android gestisce il collegamento app tramite il *sistema preventivo* &ndash; quando l'utente fa clic su un collegamento in un browser per dispositivi mobili, il browser per dispositivi mobili verrà inviato un intento di Android delegherà a un'applicazione registrata. Ad esempio, facendo clic su un collegamento in un sito Web di cucina, sarebbe aprire un'app per dispositivi mobili che è associata a tale sito Web e visualizzare una recipe specifico per l'utente. Se è presente più di un'applicazione è registrata per gestire tale intento, quindi Android genererà ciò che viene definito un *finestra di dialogo di risoluzione dell'ambiguità* che chiederà all'utente per selezionare l'applicazione che deve gestire la finalità, per individuare l'applicazione esempio:

![Schermata di esempio di una finestra di dialogo di risoluzione dell'ambiguità](app-linking-images/01-disambiguation-dialog.png)

Android 6.0 migliora questo mediante Gestione collegamento automatico. È possibile per Android registrare automaticamente un'applicazione come gestore predefinito per un URI &ndash; l'app verrà lanciati automaticamente e passare direttamente all'attività rilevanti. Modalità Android 6.0 decide di gestire un clic URI dipende dai criteri seguenti:

1. **Un'app esistente è già associata all'URI** &ndash; l'utente ha già associato un'app esistente di un URI. In tal caso, Android continueranno a utilizzare tale applicazione.
2. **Nessuna app esistente è associata all'URI, ma è installata una supporto app** &ndash; In questo scenario, l'utente non è specificato un'app esistente, in modo da Android utilizzerà l'applicazione di supporto installato per gestire la richiesta.
3. **Nessuna app esistente è associata all'URI, ma molte applicazioni di supporto sono installate** &ndash; perché sono presenti più applicazioni che supportano l'URI, verrà visualizzata la finestra di dialogo di risoluzione dell'ambiguità e l'utente deve selezionare quale app verrà gestire l'URI.

Se l'utente non dispone di alcuna App installate che supportano l'URI e successivamente ne è installata, quindi Android imposterà l'applicazione come gestore predefinito per l'URI dopo aver verificato l'associazione con il sito Web che viene associato all'URI.

Questa guida illustra come configurare un'applicazione Android 6.0 e come creare e pubblicare il file digitale Asset collegamenti per supportare il collegamento app in Android 6.0.

## <a name="requirements"></a>Requisiti

Questa guida richiede 6.1 xamarin e un'applicazione destinata Android 6.0 (livello API 23) o versione successiva.

Collegamento App è possibile nelle versioni precedenti di Android utilizzando il [pacchetto NuGet Chiodini](https://www.nuget.org/packages/Rivets/) dall'archivio dei componenti di Xamarin. Il pacchetto rivetti non è compatibile con il collegamento app in Android 6.0; non supporta il collegamento di app Android 6.0.

## <a name="configuring-app-linking-in-android-60"></a>Configurazione collegamento App in Android 6.0

Configurazione di collegamenti di app in Android 6.0 prevede due passaggi:

1. **Aggiunta di uno o più finalità-filtri per del sito Web URI** &ndash; i filtri finalità della Guida Android in modalità di gestione di un clic URL in un browser per dispositivi mobili.
2. **Pubblicazione di un *digitale Asset collegamenti JSON* file nel sito Web** &ndash; si tratta di un file che viene caricato in un sito Web e viene utilizzato da Android per verificare la relazione tra l'app per dispositivi mobili e il dominio del sito Web. In caso contrario, Android non è possibile installare l'app per la gestione predefinita per l'URI; l'utente è necessario procedere manualmente.

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>Configurazione del filtro preventivo

È necessario configurare un filtro preventivo che esegue il mapping di un URI (o un set di URI possibili) da un sito Web a un'attività in un'applicazione Android. In xamarin, questa relazione viene stabilita tramite la decorazione di un'attività con il [IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/). Il filtro preventivo deve dichiarare le informazioni seguenti:

* **`Intent.ActionView`** &ndash; Consente di registrare il filtro preventivo per rispondere alle richieste per visualizzare le informazioni
* **`Categories`** &ndash;  Il filtro preventivo deve registrare entrambi  **[Intent.CategoryBrowsable](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryBrowsable/)**  e  **[Intent.CategoryDefault](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryDefault/)**  per poter correttamente gestire l'URI di web.
* **`DataScheme`** &ndash; Il filtro preventivo deve dichiarare `http` e/o `https`. Questi sono gli schemi di validi solo due.
* **`DataHost`** &ndash; Si tratta del dominio originate da URI.
* **`DataPathPrefix`** &ndash; Si tratta di un percorso facoltativo di risorse nel sito Web.
* **`AutoVerify`** &ndash; Il `autoVerify` attributo indica Android per verificare la relazione tra l'applicazione e il sito Web. Questo verrà descritta più sotto.

Nell'esempio seguente viene illustrato come utilizzare il [IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) per gestire i collegamenti da `https://www.recipe-app.com/recipes` e da `http://www.recipe-app.com/recipes`:

```csharp
[IntentFilter(new [] { Intent.ActionView },
              Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
              DataScheme = "http",
              DataHost = "recipe-app.com",
              DataPathPrefix = "/recipe"),
              AutoVerify=true]
public class RecipeActivity : Activity
{
    // Code for the activity omitted
}
```

Android verificherà ogni host che è identificato dai filtri preventivi nel file di risorse digitali nel sito Web prima di registrare l'applicazione come gestore predefinito per un URI. Tutti i filtri preventivi devono superare la verifica Android di stabilire l'app come gestore predefinito.

### <a name="creating-the-digital-assets-link-file"></a>Creazione del File di collegamento di risorse digitali

Android 6.0 collegamento app richiede che Android verificare l'associazione tra l'applicazione e il sito Web prima di impostare l'applicazione come gestore predefinito per l'URI. Questa verifica verrà eseguita durante l'installazione iniziale dell'applicazione. Il *collegamenti a risorse digitali* file è un file JSON che è ospitato dal webdomain(s) pertinente.

> [!NOTE]
> Il `android:autoVerify` attributo deve essere impostato dal filtro preventivo &ndash; in caso contrario Android non eseguirà la verifica.

Il file si trova da webmaster del dominio in corrispondenza della posizione **https://domain/.well-known/assetlinks.json**.

Il File di Asset digitale contiene i metadati necessari per Android verificare l'associazione. Un **assetlinks.json** file contiene le coppie chiave-valore seguente:

* `namespace` &ndash; lo spazio dei nomi dell'applicazione Android.
* `package_name` &ndash; il nome del pacchetto dell'applicazione Android (dichiarato nel manifesto dell'applicazione).
* `sha256_cert_fingerprints` &ndash; impronte digitali SHA256 di applicazione firmato. Vedere la Guida [ricerca MD5 o SHA1 firma il Keystore](~/android/deploy-test/signing/keystore-signature.md) per ulteriori informazioni su come ottenere l'impronta digitale SHA1 di un'applicazione.

Nel frammento seguente è riportato un esempio di **assetlinks.json** con una singola applicazione elencati:

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

È possibile registrare più di una delle impronte digitali SHA256 per il supporto di versioni diverse o le compilazioni dell'applicazione. Successiva attività **assetlinks.json** file è riportato un esempio di registrazione di più applicazioni:

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

Il [sito Web Google digitale Asset collegamenti](https://developers.google.com/digital-asset-links/tools/generator) è uno strumento online utili per la creazione e verifica il file di risorse digitali.

### <a name="testing-app-links"></a>Verifica dei collegamenti di App

Dopo avere implementato i collegamenti di app, le varie parti devono essere testate per assicurarsi che funzionino come previsto.

È possibile confermare che il file di risorse digitali sia formattato correttamente e che sia ospitato tramite API di collegamenti Asset digitali di Google, come illustrato in questo esempio:

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

Esistono due test che possono essere eseguite per garantire che i filtri preventivi siano stati configurati correttamente e che l'app è impostato come gestore predefinito per un URI:

1.  Il File di Asset digitale correttamente è ospitato come descritto in precedenza. Il primo test verrà inviato un intento di Android deve reindirizzare l'applicazione per dispositivi mobili. L'applicazione di Android debba avviare e visualizzare l'attività registrata per l'URL. Al prompt dei comandi digitare:

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2.  Visualizzare il collegamento esistente, la gestione di criteri per le applicazioni installate in un determinato dispositivo. Il seguente comando viene eseguito il dump di un elenco di criteri di collegamento per ogni utente nel dispositivo con le informazioni seguenti. Al prompt dei comandi digitare il seguente comando:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    * **`Package`** &ndash; Il nome del pacchetto dell'applicazione.
    * **`Domain`** &ndash; I domini (separati da spazi) con i collegamenti web verranno gestiti dall'applicazione
    * **`Status`** &ndash; Questo è lo stato corrente di gestione di collegamento per l'app. Il valore **sempre** indica che l'applicazione ha `android:autoVerify=true` dichiarato e ha superato la verifica di sistema. È seguito da un numero esadecimale che rappresenta il record del sistema Android della preferenza.

    Ad esempio:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>Riepilogo

Questa guida illustrate funziona come collegamento app in Android 6.0. Quindi descritto come configurare un'applicazione Android 6.0 per supportare e rispondere ai collegamenti di app. È stato inoltre illustrato come testare app e il collegamento in un'applicazione Android.


## <a name="related-links"></a>Collegamenti correlati

- [Ricerca di MD5 o SHA1 firma l'archivio di chiavi](~/android/deploy-test/signing/keystore-signature.md)
- [Le attività e tipi](https://university.xamarin.com/classes#4)
- [AppLinks](http://applinks.org/)
- [Firma digitale di Google asset collegamenti](https://developers.google.com/digital-asset-links/)
- [Tester e il generatore di elenco di istruzione](https://developers.google.com/digital-asset-links/tools/generator)
