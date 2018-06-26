---
title: Ricevitori di trasmissione in xamarin. Android
description: In questa sezione viene illustrato come utilizzare un ricevitore di trasmissione.
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 04/20/2018
ms.openlocfilehash: 6b2e316eaf67e51801be4fcd670e80ec81c8ff08
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935399"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Ricevitori di trasmissione in xamarin. Android

_In questa sezione viene illustrato come utilizzare un ricevitore di trasmissione._

## <a name="broadcast-receiver-overview"></a>Panoramica di ricevitore broadcast

Un _ricevitore broadcast_ è un componente di Android che consente a un'applicazione rispondere ai messaggi (un Android [ `Intent` ](https://developer.xamarin.com/api/type/Android.Content.Intent/)) che vengono trasmessi dal sistema operativo Android o da un'applicazione. Trasmissioni seguono una _pubblicazione-sottoscrizione_ modello &ndash; un evento comporta una trasmissione da pubblicare e ricevuti da tali componenti sono interessati nell'evento. 

Android identifica due tipi di trasmissioni:

* **Trasmissione esplicita** &ndash; questi tipi di trasmissioni destinazione un'applicazione specifica. L'utilizzo più comune di una trasmissione esplicita consiste nell'iniziare un'attività. Un esempio di una trasmissione esplicita quando un'app richiede per comporre un numero di telefono; verrà inviato un preventivo che ha come destinazione l'app in Android e passata lungo il numero di telefono da comporre Phone. Android indirizzerà quindi la finalità all'app telefono.
* **Trasmissione implicita** &ndash; queste trasmissioni vengano inviate a tutte le app nel dispositivo. Un esempio di una trasmissione implicito è il `ACTION_POWER_CONNECTED` finalità. Questa finalità è pubblicata in ogni momento che Android rileva che carica la batteria del dispositivo. Android indirizzerà questa finalità a tutte le app che sono registrati per questo evento.

Il ricevitore broadcast è una sottoclasse del `BroadcastReceiver` deve eseguire l'override di tipo e il [ `OnReceive` ](https://developer.xamarin.com/api/member/Android.Content.BroadcastReceiver.OnReceive/p/Android.Content.Context/Android.Content.Intent/) (metodo). Android eseguirà `OnReceive` nel thread principale, pertanto, questo metodo deve essere progettato per eseguire rapidamente. Prestare attenzione quando durante la generazione del thread nel `OnReceive` perché Android potrebbe terminare il processo quando il metodo termina. Se un ricevitore di trasmissione deve eseguire un lavoro esecuzione prolungata, è consigliabile pianificare un _processo_ usando la `JobScheduler` o il _Firebase processo Dispatcher_. Programmazione con un processo di lavoro verranno descritte in una guida separata.

Un' _filtro preventivo_ viene utilizzato per registrare un ricevitore di trasmissione in modo che Android possono indirizzare correttamente i messaggi. Il filtro preventivo può essere specificato in fase di esecuzione (ciò è talvolta detta una _contesto registrato ricevitore_ oppure come _registrazione dinamica_) o può essere definito in modo statico in (un dimanifestoAndroid_registrato manifesto del ricevitore_). Xamarin. Android fornisce un attributo di c#, `IntentFilterAttribute`, che verrà registrato in modo statico il filtro preventivo (questo punto verrà descritto più dettagliatamente più avanti in questa Guida). A partire da Android 8.0, non è possibile che un'applicazione di registrare in modo statico per una trasmissione implicita.

La differenza principale tra il destinatario registrato manifesto e il destinatario del contesto registrato è che un destinatario registrato contesto risponderà solo alle trasmissioni durante l'esecuzione di un'applicazione, mentre può rispondere a un destinatario registrato manifesto trasmette anche se l'app potrebbe non essere in esecuzione.  

Esistono due set di API per la gestione di un ricevitore di trasmissione e l'invio di trasmissioni:

1. **`Context`** &ndash; Il `Android.Content.Context` classe può essere utilizzata per registrare un ricevitore di trasmissione che verrà rispondere agli eventi a livello di sistema. Il `Context` viene inoltre utilizzato per pubblicare le trasmissioni a livello di sistema.
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; Si tratta di un'API che è disponibile tramite il [pacchetto NuGet di libreria di supporto Xamarin v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Questa classe viene utilizzata per mantenere le trasmissioni e broadcast ricevitori isolati nel contesto dell'applicazione che li utilizza. Questa classe può essere utile per impedire che altre applicazioni da risponde alle trasmissioni solo dell'applicazione o l'invio di messaggi per i ricevitori privati.

Un ricevitore di broadcast non vengano visualizzate le finestre di dialogo, ed è fortemente sconsigliata per avviare un'attività dall'interno un ricevitore di trasmissione. Se un ricevitore di trasmissione deve notificare l'utente, consigliabile pubblicare una notifica.

Non è possibile associare a o avviare un servizio dall'interno di un ricevitore di trasmissione. 

Questa guida illustra come creare un ricevitore di trasmissione e come per la registrazione in modo che è possibile ricevere trasmissioni.

## <a name="creating-a-broadcast-receiver"></a>Creazione di un ricevitore Broadcast

Per creare un ricevitore di trasmissione in xamarin. Android, un'applicazione deve essere una sottoclasse il `BroadcastReceiver` classe, per decorare con il `BroadcastReceiverAttribute`ed eseguire l'override di `OnReceive` metodo:

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

Quando xamarin consente di compilare la classe, verrà aggiornato anche il AndroidManifest con i metadati necessari per registrare il ricevitore. Per i ricevitori broadcast registrato in modo statico, il `Enabled` correttamente, deve essere impostato su `true`, in caso contrario Android non sarà in grado di creare un'istanza del ricevitore.
 
Il `Exported` proprietà controlla se il destinatario broadcast può ricevere messaggi provenienti dall'esterno dell'applicazione. Se la proprietà non è impostata in modo esplicito, il valore predefinito della proprietà è determinato da Android basata se sono presenti eventuali filtri finalità associati il ricevitore broadcast. Se si verifica almeno un filtro finalità per il ricevitore broadcast gli Android si presuppone che il `Exported` proprietà `true`. Se non sono presenti filtri finalità associati il ricevitore broadcast, allora Android presupporrà che il valore è `false`. 

Il `OnReceive` metodo riceve un riferimento al `Intent` che è stata inviata al ricevitore di trasmissione. In questo modo è possibile che il mittente dell'intento di passare i valori al ricevitore di trasmissione.

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>In modo statico la registrazione di un ricevitore di trasmissione con un filtro finalità

Quando un `BroadcastReceiver` è decorata con il [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/), xamarin. Android aggiungerà necessarie `<intent-filter>` elemento per il Android manifesto in fase di compilazione. Nel frammento seguente è riportato un esempio di un ricevitore di trasmissione che verrà eseguito quando un dispositivo ha completato l'avvio (se sono state concesse le autorizzazioni appropriate Android dall'utente):

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

È anche possibile creare un filtro preventivo che risponderà a scopi personalizzati. Si consideri l'esempio seguente: 

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

Le app destinate a Android 8.0 (livello API 26) o versione successiva possono non staticamente registrarsi per una trasmissione implicita. Le app possono registrare comunque in modo statico per una trasmissione esplicita. È un elenco di piccole dimensioni di trasmissioni implicite che verranno esentati da questa limitazione. Queste eccezioni sono descritte nel [eccezioni implicite Broadcast](https://developer.android.com/guide/components/broadcast-exceptions.html) Guida nella documentazione di Android. Le app che sono interessate a trasmissioni implicite devono ricorrere dinamicamente il `RegisterReceiver` metodo. Ciò è descritto di seguito.

### <a name="context-registering-a-broadcast-receiver"></a>Registrazione di un ricevitore Broadcast contesto

Contesto della registrazione (detta anche la registrazione dinamica) di un ricevitore viene eseguita invocando la `RegisterReceiver` metodo e il destinatario broadcast deve essere registrato con una chiamata al `UnregisterReceiver` metodo. Per evitare perdite di risorse, è importante annullare la registrazione il ricevitore quando non è più pertinente per il contesto (l'attività o il servizio). Ad esempio, un servizio può trasmettere una finalità per informare un'attività che gli aggiornamenti disponibili da visualizzare all'utente. Quando si avvia l'attività, è registrato per tali tipi. Quando l'attività viene spostata in background e non sarà più visibile all'utente, non deve annullare la registrazione il destinatario perché l'interfaccia utente per visualizzare gli aggiornamenti non è più visibile. Frammento di codice seguente è riportato un esempio di come registrare e annullare la registrazione di un ricevitore di trasmissione nel contesto di un'attività:

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

Nell'esempio precedente, quando l'attività torna in primo piano, verrà registrato un ricevitore di trasmissione che rimarrà in attesa una finalità personalizzato utilizzando la `OnResume` metodo del ciclo di vita. Durante lo spostamento dell'attività in background, il `OnPause()` metodo la registrazione il ricevitore.

## <a name="publishing-a-broadcast"></a>Pubblicazione di una trasmissione

Una trasmissione può essere pubblicata di tutte le app installate nel dispositivo creando un oggetto preventivo e distribuirlo con il `SendBroadcast` o `SendOrderedBroadcast` metodo.  

1. **Metodi Context.SendBroadcast** &ndash; sono disponibili varie implementazioni di questo metodo.
   Questi metodi verranno trasmesso all'intento dell'intero sistema. Ricevitori broadcast thatwill lo scopo di ricezione in un ordine indeterminato. Ciò consente una notevole flessibilità ma significa che è possibile che altre applicazioni registrare e lo scopo di ricezione. Ciò può causare un rischio di sicurezza potenziale. Le applicazioni potrebbero essere necessario implementare la sicurezza di addizione per evitare accessi non autorizzati. Una possibile soluzione consiste nell'utilizzare il `LocalBroadcastManager` solo che invierà i messaggi nello spazio privato dell'app. Questo frammento di codice è un esempio di come inviare un obiettivi utilizzando uno del `SendBroadcast` metodi:

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   intent.PutExtra("key", "value");
   SendBroadcast(intent);
   ```

    Questo frammento è un altro esempio di invio di una trasmissione utilizzando il `Intent.SetAction` metodo per identificare l'azione:

    ```csharp 
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **Context.SendOrderedBroadcast** &ndash; si tratta di metodo è molto simile a `Context.SendBroadcast`, con la differenza è che l'intento sarà pubblicato uno in fase di ricevitori, nell'ordine che il recievers siano stati registrati.

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

Il [v4 di libreria di supporto Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) fornisce una classe helper denominata [ `LocalBroadcastManager` ](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html). Il `LocalBroadcastManager` previsto per le app che non si desidera inviare o ricevere le trasmissioni da altre app nel dispositivo. Il `LocalBroadcastManager` pubblicheranno messaggi all'interno del contesto dell'applicazione e solo per i ricevitori broadcast che sono registrati con solo il `LocalBroadcastManager`. Questo frammento di codice è riportato un esempio di registrazione di un ricevitore di broadcast con `LocalBroadcastManager`:

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

Le altre app nel dispositivo non può ricevere i messaggi che vengono pubblicati con la `LocalBroadcastManager`. Questo frammento di codice viene illustrato come inviare un preventivo utilizzando il `LocalBroadcastManager`:

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
intent.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>Collegamenti correlati

- [BroadcastReceiver](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Context.RegisterReceiver](https://developer.xamarin.com/api/member/Android.Content.Context.RegisterReceiver/p/Android.Content.BroadcastReceiver/Android.Content.IntentFilter/System.String/Android.OS.Handler/)
- [Context.SendBroadcast](https://developer.xamarin.com/api/member/Android.Content.Context.SendBroadcast/p/Android.Content.Intent/)
- [Context.UnregisterReceiver](https://developer.xamarin.com/api/member/Android.Content.Context.UnregisterReceiver/p/Android.Content.BroadcastReceiver/)
- [Finalità](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [IntentFilter](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
- [LocalBroadcastManager](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [Notifiche locale in Android](~/android/app-fundamentals/notifications/local-notifications.md)
- [Android supporta libreria v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
