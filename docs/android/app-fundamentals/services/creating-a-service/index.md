---
title: Creazione di un servizio
ms.topic: article
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/01/2018
ms.openlocfilehash: 3d692ffbffd85f8cc2b51fd72c13527031058daf
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="creating-a-service"></a>Creazione di un servizio

Servizi di xamarin devono rispettare due regole inviolabili dei servizi Android:

* È necessario estendere il [ `Android.App.Service` ](https://developer.xamarin.com/api/type/Android.App.Service/).
* Deve essere provvisto di [ `Android.App.ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/).

Un altro requisito dei servizi Android è che devono essere registrati nel **AndroidManifest.xml** e assegnare un nome univoco. Xamarin verrà registrato automaticamente il servizio nel manifesto in fase di compilazione con l'attributo XML necessario.

Questo frammento di codice è semplice esempio di creazione di un servizio in xamarin che soddisfa questi due requisiti:  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

In fase di compilazione xamarin verrà registrato il servizio inserendo il seguente elemento XML in **AndroidManifest.xml** (si noti che xamarin sia generato un nome casuale per il servizio):

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

È possibile condividere un servizio con altre applicazioni Android da _esportazione_ è. Questa operazione viene eseguita impostando il `Exported` proprietà il `ServiceAttribute`. Durante l'esportazione di un servizio, il `ServiceAttribute.Name` proprietà deve essere impostata anche per fornire un nome significativo pubblico per il servizio. Questo frammento di codice viene illustrato come esportare e nome di un servizio:

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

Il **AndroidManifest.xml** elemento per questo servizio sarà quindi simile:

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

Servizi hanno i propri ciclo di vita con metodi di callback da richiamare quando viene creato il servizio. Esattamente quali metodi vengono richiamati dipende dal tipo di servizio. Un servizio avviato deve implementare i metodi del ciclo di vita diverso rispetto a un servizio associato, mentre un servizio ibrido deve implementare i metodi di callback per un servizio avviato e un servizio associato. Questi metodi sono tutti i membri del `Service` classe; la modalità di avvio del servizio determina quali metodi del ciclo di vita verranno richiamati. Questi metodi del ciclo di vita verranno descritta più dettagliatamente in un secondo momento.

Per impostazione predefinita, un servizio verrà avviato nello stesso processo come un'applicazione Android. È possibile avviare un servizio nel proprio processo impostando il `ServiceAttribute.IsolatedProcess` proprietà su true:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

Il passaggio successivo è possibile analizzare come avviare un servizio e quindi continuare a esaminare come implementare i tre diversi tipi di servizi.

> [!NOTE]
> Un servizio viene eseguito sul thread dell'interfaccia utente, se tutte le operazioni da eseguire che blocca l'interfaccia utente, il servizio è necessario utilizzare i thread per eseguire il lavoro.

## <a name="starting-a-service"></a>Avvio di un servizio

È il modo più semplice per avviare un servizio in Android per inviare un `Intent` che contiene i metadati per identificare il servizio deve essere avviato. Esistono due diversi stili di tipi che possono essere utilizzati per avviare un servizio:

-   **Finalità esplicita** &ndash; un _intento esplicito_ identificherà esattamente il tipo di servizio deve essere utilizzato per completare una determinata azione. Un intento esplicito può essere considerato come una lettera di un indirizzo specifico. Android indirizzerà lo scopo per il servizio identificato in modo esplicito. Questo frammento è un esempio dell'uso dell'intento esplicito per avviare un servizio denominato `DownloadService`:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

-   **Finalità implicita** &ndash; questo tipo di finalità regime di controllo libero identifica dell'azione che deve essere eseguita, ma il servizio esatto per eseguire questa operazione è sconosciuto. Un intento implicito può essere considerato come una lettera indirizzati a "To Whom It maggio problema …".
    Android esaminerà il contenuto della finalità e determinare se è presente un servizio esistente a cui corrisponde la finalità.

    Un _filtro preventivo_ viene utilizzato per ottenere una corrispondenza con l'intento implicito con un servizio registrato. Un filtro preventivo è un elemento XML che viene aggiunto a **AndroidManifest.xml** che contiene i metadati necessari per ottenere una corrispondenza con un servizio con stato intento implicito.

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

Se Android dispone di più di una corrispondenza possibile per una finalità implicita, potrebbe chiedere all'utente di selezionare il componente per gestire l'azione:

![Schermata di una finestra di dialogo di risoluzione dell'ambiguità](images/creating-a-service-01.png "schermata di una finestra di dialogo di risoluzione dell'ambiguità")

> [!IMPORTANT]
> A partire da Android 5.0 (livello PA 21) intento implicito non è utilizzato per avviare un servizio.

Dove possibile, le applicazioni devono utilizzare tipi espliciti per avviare un servizio. Non richiede un intento implicito per un servizio specifico avviare &ndash; è una richiesta per un servizio installato nel dispositivo per gestire la richiesta. Questa richiesta ambigua può comportare il servizio non corretto che gestisce la richiesta o un'altra app inutilmente avvio (che aumenta la pressione per le risorse nel dispositivo).

La modalità in cui viene inviato l'intento dipende dal tipo di servizio e verrà descritta più dettagliatamente più avanti nelle guide specifiche per ogni tipo di servizio.


### <a name="creating-an-intent-filter-for-implicit-intents"></a>Creazione di un filtro preventivo per scopi implicite

Per associare una finalità implicita di un servizio, un'app Android necessario fornire alcuni metadati per identificare le funzionalità del servizio. Questi metadati viene fornito da _filtri preventivi_. Filtri preventivi contengono alcune informazioni, ad esempio un'azione o un tipo di dati, che devono essere presenti in un tentativo di avviare un servizio. In xamarin, il filtro preventivo viene registrato in **AndroidManifest.xml** dichiarando un servizio con il [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/). Ad esempio, il codice seguente aggiunge un filtro preventivo associata a un'azione di `com.xamarin.DemoService`:

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

Il risultato è una voce che verrà inclusa nel **AndroidManifest.xml** file &ndash; una voce che viene creato un pacchetto con l'applicazione in modo analogo all'esempio seguente:

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

Con i concetti di base di un servizio in modo di xamarin, si esamineranno i diversi sottotipi dei servizi in modo più dettagliato.


## <a name="related-links"></a>Collegamenti correlati

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)
- [Android.App.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
