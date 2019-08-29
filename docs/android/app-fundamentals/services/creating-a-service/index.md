---
title: Creazione di un servizio
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/03/2018
ms.openlocfilehash: 63f815cc974315735220a99fd4cce2af408a8c2f
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119052"
---
# <a name="creating-a-service"></a>Creazione di un servizio

I servizi Novell. Android devono rispettare due regole inviolabili dei servizi Android:

- Devono estendere [`Android.App.Service`](xref:Android.App.Service).
- Devono essere decorati con [`Android.App.ServiceAttribute`](xref:Android.App.ServiceAttribute).

Un altro requisito dei servizi Android è che devono essere registrati in **file AndroidManifest. XML** e dato un nome univoco. Novell. Android registrerà automaticamente il servizio nel manifesto in fase di compilazione con l'attributo XML necessario.

Questo frammento di codice è il più semplice esempio di creazione di un servizio in Novell. Android che soddisfi questi due requisiti:  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

In fase di compilazione, Novell. Android registrerà il servizio inserendo l'elemento XML seguente in **file AndroidManifest. XML** (si noti che Novell. Android ha generato un nome casuale per il servizio):

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

È possibile condividere un servizio con altre applicazioni Android esportandolo . Questa operazione viene eseguita impostando `Exported` la proprietà nell' `ServiceAttribute`oggetto. Quando si esporta un servizio, `ServiceAttribute.Name` la proprietà deve essere impostata anche per fornire un nome pubblico significativo per il servizio. Questo frammento di codice illustra come esportare e denominare un servizio:

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

L'elemento **file AndroidManifest. XML** per questo servizio sarà simile al seguente:

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

I servizi hanno il proprio ciclo di vita con metodi di callback che vengono richiamati durante la creazione del servizio. I metodi richiamati variano in base al tipo di servizio. Un servizio avviato deve implementare metodi del ciclo di vita diversi rispetto a un servizio associato, mentre un servizio ibrido deve implementare i metodi di callback sia per un servizio avviato che per un servizio associato. Questi metodi sono tutti membri della `Service` classe. il modo in cui il servizio viene avviato determinerà i metodi del ciclo di vita che verranno richiamati. Questi metodi del ciclo di vita verranno descritti in dettaglio più avanti.

Per impostazione predefinita, un servizio viene avviato nello stesso processo di un'applicazione Android. È possibile avviare un servizio in un processo autonomo impostando la `ServiceAttribute.IsolatedProcess` proprietà su true:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

Il passaggio successivo consiste nell'esaminare come avviare un servizio e quindi passare a esaminare come implementare i tre diversi tipi di servizi.

> [!NOTE]
> Un servizio viene eseguito nel thread dell'interfaccia utente, pertanto se è necessario eseguire un lavoro che blocca l'interfaccia utente, è necessario che il servizio utilizzi thread per eseguire il lavoro.

## <a name="starting-a-service"></a>Avvio di un servizio

Il modo più semplice per avviare un servizio in Android è inviare un oggetto `Intent` che contiene i metadati per identificare il servizio da avviare. Per avviare un servizio, è possibile usare due stili diversi di Intent:

- **Finalità esplicita** Una finalità esplicita identificherà esattamente il servizio da usare per completare una determinata azione. &ndash; Una finalità esplicita può essere considerata come una lettera con un indirizzo specifico. Android instraderà l'intento al servizio identificato in modo esplicito. Questo frammento di codice è un esempio dell'uso di un'intenzione esplicita `DownloadService`per avviare un servizio denominato:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

- **Finalità implicita** &ndash; Questo tipo di finalità identifica in modo generico l'azione che l'utente desidera eseguire, ma il servizio esatto per completare l'azione è sconosciuto. Un intento implicito può essere considerato come una lettera "a chi potrebbe preoccuparsi...".
    Android analizzerà il contenuto della finalità e determinerà se esiste già un servizio che corrisponde allo scopo.

    Viene utilizzato un _filtro preventivo_ per trovare una corrispondenza tra finalità implicita e un servizio registrato. Un filtro preventivo è un elemento XML aggiunto a **file AndroidManifest. XML** che contiene i metadati necessari per consentire la corrispondenza di un servizio con finalità implicite.

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

Se Android ha più di una corrispondenza possibile per uno scopo implicito, potrebbe richiedere all'utente di selezionare il componente per gestire l'azione:

![Screenshot della finestra di dialogo di risoluzione dell'ambiguità](images/creating-a-service-01.png "Screenshot della finestra di dialogo di risoluzione dell'ambiguità")

> [!IMPORTANT]
> A partire da Android 5,0 (AP Level 21) non è possibile usare un preventivo implicito per avviare un servizio.

Laddove possibile, le applicazioni devono usare gli Intent espliciti per avviare un servizio. Un intento implicito non richiede che un servizio specifico venga &ndash; avviato. si tratta di una richiesta per il servizio installato sul dispositivo per gestire la richiesta. Questa richiesta ambigua può causare un servizio errato che gestisce la richiesta o un'altra app avviata in modo inutilmente (che aumenta la pressione delle risorse sul dispositivo).

Il modo in cui viene inviato lo scopo dipende dal tipo di servizio e verrà illustrato in dettaglio più avanti nelle guide specifiche per ogni tipo di servizio.


### <a name="creating-an-intent-filter-for-implicit-intents"></a>Creazione di un filtro preventivo per gli Intent impliciti

Per associare un servizio a uno scopo implicito, un'app Android deve fornire alcuni metadati per identificare le funzionalità del servizio. Questi metadati sono forniti dai filtri per _finalità_. I filtri per finalità contengono alcune informazioni, ad esempio un'azione o un tipo di dati, che devono essere presenti nello scopo di avviare un servizio. In Novell. Android il filtro preventivo è registrato in **file AndroidManifest. XML** , decorando un servizio con [`IntentFilterAttribute`](xref:Android.App.IntentFilterAttribute). Il codice seguente, ad esempio, aggiunge un filtro preventivo con un'azione associata `com.xamarin.DemoService`di:

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

In questo modo viene inclusa una voce nel &ndash; file **file AndroidManifest. XML** , una voce che viene inclusa in un pacchetto con l'applicazione in modo analogo all'esempio seguente:

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

Con le nozioni di base di un servizio Novell. Android, esaminiamo i diversi sottotipi di servizi in modo più dettagliato.


## <a name="related-links"></a>Collegamenti correlati

- [Android.App.Service](xref:Android.App.Service)
- [Android.App.ServiceAttribute](xref:Android.App.ServiceAttribute)
- [Android.App.Intent](xref:Android.Content.Intent)
- [Android.App.IntentFilterAttribute](xref:Android.App.IntentFilterAttribute)
