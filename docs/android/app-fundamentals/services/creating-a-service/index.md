---
title: Creazione di un servizio
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/03/2018
ms.openlocfilehash: 24d86827ab93dcf7dfc4da39c4a03a0a2805f332
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107430"
---
# <a name="creating-a-service"></a>Creazione di un servizio

Servizi di xamarin. Android devono rispettare due regole inviolabili dei servizi Android:

* È necessario estendere la [ `Android.App.Service` ](https://developer.xamarin.com/api/type/Android.App.Service/).
* Deve essere decorate con il [ `Android.App.ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/).

Un altro requisito dei servizi Android è che deve essere registrati nel **androidmanifest. XML** viene assegnato un nome univoco. Xamarin. Android registrerà automaticamente il servizio nel manifesto in fase di compilazione con l'attributo XML necessario.

Questo frammento di codice è un esempio più semplice di creazione di un servizio in xamarin. Android che soddisfi questi due requisiti:  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

In fase di compilazione xamarin. Android verrà registrato il servizio inserendo l'elemento XML seguente nel **androidmanifest. XML** (si noti che xamarin. Android generato un nome casuale per il servizio):

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

È possibile condividere un servizio con altre applicazioni per Android _esportazione_ è. Questa operazione viene eseguita impostando il `Exported` proprietà di `ServiceAttribute`. Quando si esporta un servizio, il `ServiceAttribute.Name` proprietà deve essere impostata anche per fornire un nome significativo pubblico per il servizio. Questo frammento di codice viene illustrato come esportare e assegnare un nome servizio:

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

Il **androidmanifest. XML** (elemento) per questo servizio sarà quindi simile a:

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

I servizi è proprio ciclo di vita con i metodi di callback richiamati quando viene creato il servizio. Esattamente quali metodi vengono richiamati dipende dal tipo di servizio. Un servizio avviato deve implementare i metodi del ciclo di vita diverso rispetto a un servizio associato, mentre un servizio ibrido deve implementare i metodi di callback per un servizio avviato e un servizio associato. Questi metodi sono tutti i membri del `Service` classe; la modalità in cui viene avviato il servizio determina quali metodi del ciclo di vita verranno richiamati. Questi metodi del ciclo di vita verranno descritte in dettaglio in un secondo momento.

Per impostazione predefinita, un servizio verrà avviato nello stesso processo come un'applicazione Android. È possibile avviare un servizio in un proprio processo impostando il `ServiceAttribute.IsolatedProcess` proprietà su true:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

Il passaggio successivo è esaminare come avviare un servizio e quindi passare a esaminare come implementare i tre diversi tipi di servizi.

> [!NOTE]
> Un servizio viene eseguito sul thread dell'interfaccia utente, in modo che se tutte le operazioni da eseguire che bloccano l'interfaccia utente, il servizio deve usare i thread per eseguire il lavoro.

## <a name="starting-a-service"></a>Avvio di un servizio

È il modo più semplice per avviare un servizio in Android per inviare un `Intent` che contiene i metadati per consentire di identificare il servizio che deve essere avviato. Esistono due diversi stili di Intent che può essere usato per avviare un servizio:

-   **Intent esplicito** &ndash; un' _intento esplicito_ identificherà esattamente quali servizio deve essere usato per completare una determinata azione. Un Intent esplicito può essere considerato una lettera che ha un indirizzo specifico. Android indirizzerà l'intento al servizio in cui viene identificato in modo esplicito. Questo frammento di codice è un esempio dell'uso di un Intent esplicito per avviare un servizio denominato `DownloadService`:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

-   **Intent implicito** &ndash; identifica regime di controllo libero delle finalità di questo tipo i di azione che l'utente desidera eseguire, ma il servizio esatto per completare l'operazione è sconosciuto. Un Intent implicito può essere considerato come una lettera che viene risolto "To Whom It May interesse in corso".
    Android verrà esaminarne il contenuto delle finalità e determinare se c'è un servizio esistente che corrisponde alla finalità.

    Un' _filtro intent_ consente di far corrispondere l'intent implicito con un servizio registrato. Un filtro intent è un elemento XML che viene aggiunto alla **androidmanifest. XML** che contiene i metadati necessari per ottenere una corrispondenza con un servizio con un intent implicito.

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

Se Android ha più di una corrispondenza possibile per un intent implicito, potrebbe chiedere all'utente di selezionare il componente per gestire l'azione:

![Screenshot di una finestra di dialogo di disambiguazione](images/creating-a-service-01.png "Screenshot di una finestra di dialogo di disambiguazione")

> [!IMPORTANT]
> A partire da Android 5.0 (livello AP 21) un intent implicito non può essere utilizzato per avviare un servizio.

Dove possibile, le applicazioni devono utilizzare Intent esplicito per avviare un servizio. Un Intent implicito non richiede un servizio specifico per l'avvio &ndash; è una richiesta per alcuni servizi installata nel dispositivo per gestire la richiesta. Questa richiesta ambigua può comportare servizio errato che gestisce la richiesta o un'altra app inutilmente avvio (che aumenta la pressione per le risorse nel dispositivo).

Modo in cui viene inviato l'intento dipende dal tipo di servizio e verrà descritte in dettaglio in un secondo momento nelle guide specifiche per ogni tipo di servizio.


### <a name="creating-an-intent-filter-for-implicit-intents"></a>Creazione di un filtro Intent per Intent implicito

Per associare un servizio con un Intent implicito, un'app per Android è necessario fornire alcuni metadati per identificare le funzionalità del servizio. Questi metadati viene fornito da _filtri di intent_. Filtri di Intent contengono alcune informazioni, ad esempio un'azione o un tipo di dati, che devono essere presenti in un Intent per avviare un servizio. In xamarin. Android, il filtro intent viene registrato nel **androidmanifest. XML** tramite la decorazione di un servizio con il [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/). Ad esempio, il codice seguente aggiunge un filtro intent associata a un'azione di `com.xamarin.DemoService`:

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

Il risultato è una voce che verrà inclusa nel **androidmanifest. XML** file &ndash; una voce in un pacchetto con l'applicazione in modo analogo all'esempio seguente:

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

Con le nozioni di base di un servizio di xamarin. Android dall'area di lavoro, esaminiamo i diversi sottotipi dei servizi in modo più dettagliato.


## <a name="related-links"></a>Collegamenti correlati

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)
- [Android.App.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
