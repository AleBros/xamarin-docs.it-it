---
title: App e il collegamento in Android
description: Questa guida illustra come Android 6.0 supporta app-il collegamento, una tecnica che consente alle App per dispositivi mobili rispondere agli URL nei siti Web. Illustrerà quali app-il collegamento è, come implementare l'app e il collegamento in un'applicazione Android 6.0 e come configurare un sito Web per concedere le autorizzazioni per l'app per dispositivi mobili per un dominio.
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: dd4ba236df8e5993c7f7ed86393eb66ce01db595
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60956151"
---
# <a name="app-linking-in-android"></a>App e il collegamento in Android

_Questa guida illustra come Android 6.0 supporta app-il collegamento, una tecnica che consente alle App per dispositivi mobili rispondere agli URL nei siti Web. Illustrerà quali app-il collegamento è, come implementare l'app e il collegamento in un'applicazione Android 6.0 e come configurare un sito Web per concedere le autorizzazioni per l'app per dispositivi mobili per un dominio._

## <a name="app-linking-overview"></a>Panoramica di collegamento dell'App

Applicazioni per dispositivi mobili non sono più racchiusi in un silo &ndash; in molti casi si tratta di un'importante componenti delle rispettive attività, insieme ai propri siti Web. È consigliabile per le aziende di connettere facilmente la presenza di web e applicazioni per dispositivi mobili, con collegamenti in un sito Web l'avvio di applicazioni per dispositivi mobili e visualizzato contenuto rilevante nell'app per dispositivi mobili. *App-collegamento* (detta anche *collegamento avanzato*) è una tecnica che consente a un dispositivo mobile per rispondere a un URI e avviare un'applicazione per dispositivi mobili che corrisponde a quell'URI.

Android gestisce il collegamento app tramite il *sistema basato su intent* &ndash; quando l'utente fa clic su un collegamento in un browser per dispositivi mobili, il browser per dispositivi mobili invierà un intent che Android delegano a un'applicazione registrata. Ad esempio, facendo clic su un collegamento in un sito Web di cucina, verrebbe aprire un'app per dispositivi mobili che è associata a tale sito Web e visualizzare una ricetta specifica per l'utente. Se è presente più di un'applicazione registrato per gestire tale intento, quindi Android genera è nota come un *finestra di dialogo di disambiguazione* che richiederà un utente dell'applicazione per selezionare l'applicazione che deve gestire la finalità per esempio:

![Screenshot di esempio di una finestra di dialogo di disambiguazione](app-linking-images/01-disambiguation-dialog.png)

Android 6.0 perfezionato questo usando la gestione di collegamento automatico. È possibile per Android registrare automaticamente un'applicazione come gestore predefinito per un URI &ndash; dell'app vengono lanciati automaticamente e passare direttamente all'attività pertinenti. Come Android 6.0 decide di fare clic su un URI di gestire dipende dai criteri seguenti:

1. **Un'app esistente è già associata con l'URI** &ndash; l'utente potrebbe avere già associato un'app esistente un URI. In tal caso, Android continuerà a usare tale applicazione.
2. **Non è un'app esistente è associata all'URI, ma è installata un'app di supporta** &ndash; In questo scenario, l'utente non ha specificato un'app esistente, in modo Android userà l'applicazione installata di supporto per gestire la richiesta.
3. **Non è un'app esistente è associata all'URI, ma molte applicazioni di supporta vengono installate** &ndash; poiché sono presenti più applicazioni che supportano l'URI, verrà visualizzata la finestra di dialogo di disambiguazione e l'utente deve selezionare quale app verrà gestire l'URI.

Se l'utente non dispone di alcuna App installate che supportano l'URI e successivamente ne è installata, quindi Android imposterà tale applicazione come gestore predefinito per l'URI dopo aver verificato l'associazione con il sito Web che viene associato all'URI.

Questa guida illustra come configurare un'applicazione Android 6.0 e su come creare e pubblicare il file di Digital Asset collegamenti per supportare l'app e il collegamento in Android 6.0.

## <a name="requirements"></a>Requisiti

Questa guida richiede xamarin. Android 6.1 e un'applicazione destinata a Android 6.0 (livello API 23) o versione successiva.

Collegamento di App è possibile nelle versioni precedenti di Android usando il [mobileengagement Chiodini](https://www.nuget.org/packages/Rivets/) dall'archivio componenti Xamarin. I pacchetto rivetti non è compatibile con il collegamento app in Android 6.0; non supporta il collegamento di app Android 6.0.

## <a name="configuring-app-linking-in-android-60"></a>Configurazione di App-collegamento in Android 6.0

Configurazione di collegamenti di app in Android 6.0 prevede due passaggi principali:

1. **Aggiunta di uno o più finalità-filtri per del sito Web URI** &ndash; i filtri di intent guidano Android in modalità di gestione fare clic su un URL in un browser per dispositivi mobili.
2. **Pubblicazione di un *JSON i collegamenti di Asset digitali* file nel sito Web** &ndash; si tratta di un file viene caricato in un sito Web e viene usato da Android per verificare la relazione tra l'app per dispositivi mobili e il dominio del sito Web. In caso contrario, Android non è possibile installare l'app per la gestione predefinita per l'URI; l'utente è necessario farlo manualmente.

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>Configurazione del filtro Intent

È necessario configurare un filtro intent che esegue il mapping di un URI (o possibile un set di URI) da un sito Web a un'attività in un'applicazione Android. In xamarin. Android, questa relazione viene stabilita tramite la decorazione di un'attività con il [IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/). Il filtro intent deve dichiarare le informazioni seguenti:

* **`Intent.ActionView`** &ndash; Consente di registrare il filtro intent per rispondere alle richieste per visualizzare le informazioni
* **`Categories`** &ndash;  Dovrebbe eseguire la registrazione sia il filtro intent **[Intent.CategoryBrowsable](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryBrowsable/)** e **[Intent.CategoryDefault](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryDefault/)** sia in grado di correttamente gestire l'URI di web.
* **`DataScheme`** &ndash; Il filtro intent deve dichiarare `http` e/o `https`. Questi sono gli unici due schemi validi.
* **`DataHost`** &ndash; Si tratta del dominio che hanno origine gli URI da.
* **`DataPathPrefix`** &ndash; Si tratta di un percorso facoltativo per le risorse nel sito Web.
* **`AutoVerify`** &ndash; Il `autoVerify` attributo chiede ad Android per verificare la relazione tra l'applicazione e il sito Web. Questo argomento verrà discusso più sotto.

Nell'esempio seguente viene illustrato come utilizzare il [IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) per gestire i collegamenti dal `https://www.recipe-app.com/recipes` e da `http://www.recipe-app.com/recipes`:

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

Android verificherà ogni host in cui viene identificato dai filtri preventivi in base al File di asset digitali nel sito Web prima di registrare l'applicazione come gestore predefinito per un URI. Tutti i filtri di intent devono superare la verifica prima Android è possibile stabilire l'app come gestore predefinito.

### <a name="creating-the-digital-assets-link-file"></a>Creazione del File di collegamento di risorse digitali

Collegamento di app Android 6.0 richiede che Android verificare l'associazione tra l'applicazione e il sito Web prima di impostare l'applicazione come gestore predefinito per l'URI. Quando l'applicazione viene installata prima di tutto, si verificherà la verifica. Il *Digital Asset collegamenti* file è un file JSON che è ospitato dal webdomain(s) pertinenti.

> [!NOTE]
> Il `android:autoVerify` attributo deve essere impostato per il filtro intent &ndash; in caso contrario, Android non eseguirà la verifica.

Il file viene inserito per i webmaster del dominio in corrispondenza della posizione **https://domain/.well-known/assetlinks.json**.

Il File di Asset digitali contiene i metadati necessari per Android per verificare l'associazione. Un' **assetlinks.json** file contiene le coppie chiave-valore seguente:

* `namespace` &ndash; lo spazio dei nomi dell'applicazione Android.
* `package_name` &ndash; il nome del pacchetto dell'applicazione Android (dichiarato nel manifesto dell'applicazione).
* `sha256_cert_fingerprints` &ndash; gli ID digitali SHA256 dell'applicazione con segno. Vedere la Guida [individuazione firma SHA1 o MD5 dell'archivio chiavi](~/android/deploy-test/signing/keystore-signature.md) per altre informazioni su come ottenere l'ID digitale SHA1 di un'applicazione.

Il frammento seguente è riportato un esempio di **assetlinks.json** con una singola applicazione elencate:

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

È possibile registrare più di un ID digitale SHA256 per supportare le diverse versioni o build dell'applicazione. Successiva **assetlinks.json** file è un esempio di registrazione di più applicazioni:

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

Il [sito Web Google Digital Asset collegamenti](https://developers.google.com/digital-asset-links/tools/generator) è uno strumento online che può essere utile alla creazione e il file di risorse digitali di test.

### <a name="testing-app-links"></a>Test di App-collegamenti

Dopo l'implementazione di app-collegamenti, i vari elementi devono essere testati per assicurarsi che funzionino come previsto.

È possibile verificare che il file di risorse digitali sia correttamente formattato e ospitato usando API di collegamenti Asset digitali di Google, come illustrato in questo esempio:

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

Esistono due test che possono essere eseguite per garantire che i filtri di intent sono stati configurati correttamente e che l'app è impostato come gestore predefinito per un URI:

1.  File di Asset digitali è ospitato correttamente come descritto in precedenza. Il primo test verrà inviato un intent che Android deve reindirizzare l'applicazione per dispositivi mobili. L'applicazione Android deve avviare e visualizzare l'attività registrata per l'URL. Al prompt dei comandi digitare:

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2.  Visualizzare il collegamento esistente gestisce i criteri per le applicazioni installate in un determinato dispositivo. Il comando seguente viene eseguito il dump di un elenco di criteri di collegamento per ogni utente nel dispositivo con le informazioni seguenti. Al prompt dei comandi digitare il seguente comando:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    * **`Package`** &ndash; Il nome del pacchetto dell'applicazione.
    * **`Domain`** &ndash; I domini (separati da spazi) con i collegamenti web verranno gestiti dall'applicazione
    * **`Status`** &ndash; Questo è lo stato corrente di gestione dei collegamenti per l'app. Un valore pari **sempre** significa che l'applicazione ha `android:autoVerify=true` dichiarato e ha superato la verifica di sistema. È seguito da un numero esadecimale che rappresenta il record del sistema Android delle preferenze.

    Ad esempio:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>Riepilogo

Questa guida ha illustrato come il collegamento app funziona in Android 6.0. Quindi illustrato come configurare un'applicazione Android 6.0 per supportare e rispondere ai collegamenti di app. È stato inoltre illustrato come testare l'app e il collegamento in un'applicazione Android.


## <a name="related-links"></a>Collegamenti correlati

- [Ricerca di firma SHA1 o MD5 dell'archivio chiavi](~/android/deploy-test/signing/keystore-signature.md)
- [Le attività e gli Intent](https://university.xamarin.com/classes#4)
- [AppLinks](http://applinks.org/)
- [Google Digital Asset collegamenti](https://developers.google.com/digital-asset-links/)
- [Generatore elenco di istruzione e tester](https://developers.google.com/digital-asset-links/tools/generator)
