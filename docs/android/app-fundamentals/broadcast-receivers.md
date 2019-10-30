---
title: Ricevitori broadcast in Novell. Android
description: In questa sezione viene illustrato come utilizzare un ricevitore di trasmissione.
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: 9f490bec121481d9f3f661913d8aefcef999bb4a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016961"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Ricevitori broadcast in Novell. Android

_In questa sezione viene illustrato come utilizzare un ricevitore di trasmissione._

## <a name="broadcast-receiver-overview"></a>Panoramica sul ricevitore di trasmissione

Un _ricevitore di trasmissione_ è un componente Android che consente a un'applicazione di rispondere ai messaggi (un [`Intent`](xref:Android.Content.Intent)Android) trasmessi dal sistema operativo Android o da un'applicazione. Le trasmissioni seguono un modello di _pubblicazione-sottoscrizione_ &ndash; un evento causa la pubblicazione e la ricezione di una trasmissione da parte di tali componenti interessati all'evento.

Android identifica due tipi di trasmissioni:

- **Trasmissione esplicita** &ndash; questi tipi di broadcast sono destinati a un'applicazione specifica. L'uso più comune di una trasmissione esplicita è l'avvio di un'attività. Un esempio di trasmissione esplicita quando un'app deve comporre un numero di telefono. invierà una finalità destinata all'app per il telefono in Android e passerà il numero di telefono per la connessione. Android instraderà quindi l'intento all'app per telefoni.
- **Trasmissione implicita** &ndash; le trasmissioni vengono inviate a tutte le app nel dispositivo. Un esempio di trasmissione implicita è l'Intent `ACTION_POWER_CONNECTED`. Questa finalità viene pubblicata ogni volta che Android rileva che la batteria sul dispositivo sta per essere caricata. Android instraderà questa finalità a tutte le app che si sono registrate per questo evento.

Il ricevitore di trasmissione è una sottoclasse del tipo di `BroadcastReceiver` e deve eseguire l'override del metodo [`OnReceive`](xref:Android.Content.BroadcastReceiver.OnReceive*) . Android eseguirà `OnReceive` sul thread principale, quindi questo metodo deve essere progettato per l'esecuzione rapida. È necessario prestare attenzione quando si generano thread in `OnReceive` perché Android può terminare il processo quando il metodo termina. Se un ricevitore di trasmissione deve eseguire operazioni a esecuzione prolungata, è consigliabile pianificare un _processo_ usando il `JobScheduler` o il _dispatcher del processo Firebase_. La pianificazione del lavoro con un processo verrà descritta in una guida separata.

Un _filtro preventivo_ viene usato per registrare un ricevitore di trasmissione in modo che Android possa indirizzare correttamente i messaggi. Il filtro preventivo può essere specificato in fase di esecuzione (a volte viene definito _ricevitore registrato dal contesto_ o come _registrazione dinamica_) oppure può essere definito in modo statico nel manifesto Android ( _ricevitore registrato_da un manifesto). Novell. Android fornisce un C# attributo,`IntentFilterAttribute`, che registrerà in modo statico il filtro preventivo (questo argomento verrà discusso più dettagliatamente più avanti in questa guida). A partire da Android 8,0, non è possibile che un'applicazione si registri in modo statico per una trasmissione implicita.

La differenza principale tra il ricevitore registrato dal manifesto e il ricevitore registrato dal contesto è che un ricevitore registrato dal contesto risponderà solo alle trasmissioni mentre un'applicazione è in esecuzione, mentre un ricevitore registrato da un manifesto può rispondere a trasmette anche se l'app potrebbe non essere in esecuzione.  

Sono disponibili due set di API per la gestione di un ricevitore di trasmissione e l'invio di trasmissioni:

1. **`Context`** &ndash; la classe `Android.Content.Context` può essere utilizzata per registrare un ricevitore di trasmissione che risponderà a eventi a livello di sistema. Il `Context` viene utilizzato anche per pubblicare trasmissioni a livello di sistema.
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; si tratta di un'API disponibile tramite il [pacchetto NuGet della libreria di supporto Novell V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Questa classe viene utilizzata per gestire le trasmissioni e i ricevitori broadcast isolati nel contesto dell'applicazione che li utilizza. Questa classe può essere utile per impedire ad altre applicazioni di rispondere alle trasmissioni di sola applicazione o di inviare messaggi a ricevitori privati.

Un ricevitore di trasmissione potrebbe non visualizzare le finestre di dialogo ed è fortemente sconsigliato avviare un'attività all'interno di un ricevitore di trasmissione. Se un ricevitore di trasmissione deve inviare una notifica all'utente, deve pubblicare una notifica.

Non è possibile eseguire l'associazione o l'avvio di un servizio all'interno di un ricevitore di trasmissione. 

In questa guida verrà illustrato come creare un ricevitore di trasmissione e come registrarlo in modo che possa ricevere trasmissioni.

## <a name="creating-a-broadcast-receiver"></a>Creazione di un ricevitore di trasmissione

Per creare un ricevitore di trasmissione in Novell. Android, un'applicazione deve creare una sottoclasse della classe `BroadcastReceiver`, decorarla con la `BroadcastReceiverAttribute`ed eseguire l'override del metodo `OnReceive`:

```csharp
[BroadcastReceiver(Enabled = true, Exported = false)]
public class SampleReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here.

        String value = intent.GetStringExtra("key");
    }
}
```

Quando Novell. Android compila la classe, verrà aggiornato anche il file AndroidManifest con i metadati necessari per registrare il ricevitore. Per un ricevitore di broadcast registrato in modo statico, il `Enabled` deve essere impostato correttamente `true`. in caso contrario, Android non sarà in grado di creare un'istanza del destinatario.

La proprietà `Exported` controlla se il ricevitore di trasmissione può ricevere messaggi dall'esterno dell'applicazione. Se la proprietà non è impostata in modo esplicito, il valore predefinito della proprietà viene determinato da Android in base a se sono presenti filtri per finalità associate al ricevitore di trasmissione. Se è presente almeno un filtro preventivo per il ricevitore di trasmissione, Android presuppone che la proprietà `Exported` sia `true`. Se al ricevitore di trasmissione non sono associati filtri per finalità, Android presuppone che il valore sia `false`. 

Il metodo `OnReceive` riceve un riferimento al `Intent` che è stato inviato al ricevitore di trasmissione. Questo rende possibile al mittente della finalità di passare i valori al ricevitore di trasmissione.

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>Registrazione statica di un ricevitore di trasmissione con un filtro preventivo

Quando un `BroadcastReceiver` viene decorato con il [`IntentFilterAttribute`](xref:Android.App.IntentFilterAttribute), Novell. Android aggiungerà l'elemento `<intent-filter>` necessario al manifesto Android in fase di compilazione. Il frammento di codice seguente è un esempio di ricevitore di trasmissione che verrà eseguito al termine dell'avvio di un dispositivo, se l'utente ha concesso le autorizzazioni Android appropriate:

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { Android.Content.Intent.ActionBootCompleted })]
public class MyBootReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Work that should be done when the device boots.     
    }
}
```

È anche possibile creare un filtro preventivo che risponderà a Intent personalizzati. Si consideri l'esempio seguente: 

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { "com.xamarin.example.TEST" })]
public class MySampleBroadcastReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here
    }
}
```

Le app destinate a Android 8,0 (livello API 26) o versione successiva potrebbero non essere registrate in modo statico per una trasmissione implicita. Le app possono comunque essere registrate in modo statico per una trasmissione esplicita. È disponibile un piccolo elenco di trasmissioni implicite esentate da questa restrizione. Queste eccezioni sono descritte nella Guida alle [eccezioni broadcast implicite](https://developer.android.com/guide/components/broadcast-exceptions.html) nella documentazione di Android. Le app interessate dalle trasmissioni implicite devono eseguire questa operazione in modo dinamico usando il metodo `RegisterReceiver`. Questa operazione è descritta di seguito.

### <a name="context-registering-a-broadcast-receiver"></a>Contesto-registrazione di un ricevitore di trasmissione

La registrazione del contesto, nota anche come registrazione dinamica, di un ricevitore viene eseguita richiamando il metodo `RegisterReceiver` e il ricevitore di trasmissione deve essere annullato con una chiamata al metodo `UnregisterReceiver`. Per evitare la perdita di risorse, è importante annullare la registrazione del ricevitore quando non è più pertinente per il contesto (attività o servizio). Ad esempio, un servizio può trasmettere una finalità per informare un'attività che gli aggiornamenti sono disponibili per essere visualizzati all'utente. Quando l'attività viene avviata, si registra per gli Intent. Quando l'attività viene spostata in background e non è più visibile all'utente, deve annullare la registrazione del ricevitore perché l'interfaccia utente per la visualizzazione degli aggiornamenti non è più visibile. Il frammento di codice seguente è un esempio di come registrare e annullare la registrazione di un ricevitore di trasmissione nel contesto di un'attività:

```csharp
[Activity(Label = "MainActivity", MainLauncher = true, Icon = "@mipmap/icon")]
public class MainActivity: Activity 
{
    MySampleBroadcastReceiver receiver;

    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        receiver = new MySampleBroadcastReceiver()

        // Code omitted for clarity
    }

    protected override OnResume() 
    {
        base.OnResume();
        RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
        // Code omitted for clarity
    }

    protected override OnPause() 
    {
        UnregisterReceiver(receiver);
        // Code omitted for clarity
        base.OnPause();
    }
}
```

Nell'esempio precedente, quando l'attività viene visualizzata in primo piano, registrerà un ricevitore di trasmissione che resterà in ascolto di una finalità personalizzata usando il metodo del ciclo di vita `OnResume`. Quando l'attività viene spostata in background, il metodo `OnPause()` Annulla la registrazione del destinatario.

## <a name="publishing-a-broadcast"></a>Pubblicazione di una trasmissione

Una trasmissione può essere pubblicata in tutte le app installate nel dispositivo creando un oggetto preventivo e inviando l'oggetto con il `SendBroadcast` o il metodo di `SendOrderedBroadcast`.  

1. I **Metodi Context. SendBroadcast** &ndash; sono disponibili diverse implementazioni di questo metodo.
   Questi metodi trasmetteranno l'intento all'intero sistema. Ricevitori di trasmissioni che riceveranno lo scopo in un ordine indeterminato. Ciò offre una grande flessibilità, ma significa che altre applicazioni possono registrarsi e ricevere lo scopo. Questo può costituire un potenziale rischio per la sicurezza. Per evitare accessi non autorizzati, è possibile che le applicazioni debbano implementare una protezione aggiuntiva. Una possibile soluzione consiste nell'usare la `LocalBroadcastManager` che invierà messaggi solo nello spazio privato dell'app. Questo frammento di codice è un esempio di come inviare uno scopo usando uno dei metodi di `SendBroadcast`:

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   message.PutExtra("key", "value");
   SendBroadcast(message);
   ```

    Questo frammento di codice è un altro esempio di invio di una trasmissione tramite il metodo `Intent.SetAction` per identificare l'azione:

    ```csharp
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **Context. SendOrderedBroadcast** &ndash; questo metodo è molto simile a `Context.SendBroadcast`, con la differenza che lo scopo verrà pubblicato uno alla volta per i destinatari, nell'ordine in cui i ricevitori sono stati registrati.

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

La [libreria di supporto Novell V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) fornisce una classe helper denominata [`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html). Il `LocalBroadcastManager` è destinato alle app che non vogliono inviare o ricevere trasmissioni da altre app nel dispositivo. Il `LocalBroadcastManager` pubblicherà solo i messaggi nel contesto dell'applicazione e solo i ricevitori di trasmissione registrati con l'`LocalBroadcastManager`. Questo frammento di codice è un esempio di registrazione di un ricevitore di trasmissione con `LocalBroadcastManager`:

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

Le altre app del dispositivo non possono ricevere i messaggi pubblicati con il `LocalBroadcastManager`. Questo frammento di codice Mostra come inviare un preventivo usando il `LocalBroadcastManager`:

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
message.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>Collegamenti correlati

- [API BroadcastReceiver](xref:Android.Content.BroadcastReceiver)
- [API context. RegisterReceiver](xref:Android.Content.Context.RegisterReceiver*)
- [API context. SendBroadcast](xref:Android.Content.Context.SendBroadcast*)
- [API context. UnregisterReceiver](xref:Android.Content.Context.UnregisterReceiver*)
- [API finalità](xref:Android.Content.Intent)
- [API IntentFilter](xref:Android.App.IntentFilterAttribute)
- [LocalBroadcastManager (documentazione di Android)](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- Guida [alle notifiche locali in Android](~/android/app-fundamentals/notifications/local-notifications.md)
- [Libreria di supporto Android V4 (NuGet)](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
