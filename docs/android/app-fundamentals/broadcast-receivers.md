---
title: Ricevitori di trasmissioni in xamarin. Android
description: Questa sezione illustra come usare un ricevitore di trasmissione.
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: 51bd3dd4c27dce19344f7660c31a0d4e741e1ad4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121139"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Ricevitori di trasmissioni in xamarin. Android

_Questa sezione illustra come usare un ricevitore di trasmissione._

## <a name="broadcast-receiver-overview"></a>Cenni preliminari sul ricevitore di trasmissione

Oggetto _ricevitore di trasmissioni_ è un componente di Android che consente a un'applicazione rispondere a messaggi (Android [ `Intent` ](https://developer.xamarin.com/api/type/Android.Content.Intent/)) che vengono trasmessi dal sistema operativo Android o da un'applicazione. Trasmissioni seguono una _pubblicazione-sottoscrizione_ modello &ndash; un evento provoca una trasmissione essere pubblicate e ricevuti da tali componenti che sono interessati nell'evento.

Android identifica due tipi di trasmissioni:

* **Trasmissione espliciti** &ndash; questi tipi di trasmissioni come destinazione un'applicazione specifica. L'uso più comune di una trasmissione esplicita consiste nell'iniziare un'attività. Un esempio di una trasmissione esplicita quando un'app richiede per comporre un numero di telefono. verrà inviato un Intent che ha come destinazione l'app per telefoni Android e passata lungo il numero di telefono da comporre. Android quindi instrada la finalità per l'app per telefoni.
* **Trasmissione implicita** &ndash; queste trasmissioni vengono inviate a tutte le app nel dispositivo. Un esempio di una trasmissione implicito è il `ACTION_POWER_CONNECTED` finalità. Questa finalità viene pubblicata ogni volta che Android rileva che carica la batteria sul dispositivo. Android indirizzerà questa finalità a tutte le app che hanno registrato per questo evento.

Ricevitori di trasmissioni sono una sottoclasse del `BroadcastReceiver` deve eseguire l'override di tipo e il [ `OnReceive` ](https://developer.xamarin.com/api/member/Android.Content.BroadcastReceiver.OnReceive/p/Android.Content.Context/Android.Content.Intent/) (metodo). Android eseguirà `OnReceive` sul thread principale, pertanto questo metodo deve essere progettato per eseguire rapidamente. Fare attenzione quando durante la generazione del thread in `OnReceive` perché Android potrebbe terminare il processo quando il metodo termina. Se un ricevitore di trasmissioni deve eseguire operazioni di lunga esecuzione, è consigliabile pianificare un _processo_ usando la `JobScheduler` o il _Firebase processo Dispatcher_. Pianificare il lavoro con un processo verrà descritta in una guida separata.

Un' _filtro intent_ viene usato per registrare un ricevitore di trasmissioni in modo che Android può indirizzare correttamente i messaggi. Il filtro intent può essere specificato in fase di esecuzione (ciò è talvolta detta un _ricevitore contesto registrato_ oppure come _registrazione dinamica_) o può essere definita in modo statico nel manifesto Android (una _registrato al manifesto del ricevitore_). Xamarin. Android fornisce un C# attributo `IntentFilterAttribute`, che verranno registrati in modo statico al filtro intent (questo argomento verrà discusso più dettagliatamente più avanti in questa Guida). A partire da Android 8.0, non è possibile che un'applicazione di registrare in modo statico per una trasmissione implicita.

La differenza principale tra il ricevitore di manifesto registrati e il destinatario del contesto registrato è che un ricevitore di contesto registrato risponderà solo alle trasmissioni durante l'esecuzione di un'applicazione, mentre un ricevitore di manifesto registrati può rispondere a trasmette anche se l'app potrebbe non essere in esecuzione.  

Esistono due set di API per la gestione di un ricevitore di trasmissione e l'invio di trasmissioni:

1. **`Context`** &ndash; Il `Android.Content.Context` classe può essere utilizzata per registrare un ricevitore di trasmissioni che risponde agli eventi a livello di sistema. Il `Context` viene usato anche per pubblicare le trasmissioni a livello di sistema.
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; Si tratta di un'API che è disponibile tramite il [pacchetto NuGet della libreria di supporto Xamarin v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Questa classe viene utilizzata per mantenere le trasmissioni e i ricevitori di trasmissioni isolati nel contesto dell'applicazione che li utilizza. Questa classe può essere utile per impedire che altre applicazioni da rispondere alle trasmissioni solo dell'applicazione o l'invio di messaggi privati ricevitori.

Un ricevitore di trasmissioni non vengano visualizzate le finestre di dialogo, ed è fortemente sconsigliato per avviare un'attività dall'interno di un ricevitore di trasmissione. Se un ricevitore di trasmissioni deve inviare una notifica all'utente, consigliabile pubblicare una notifica.

Non è possibile associare a o avviare un servizio dall'interno di un ricevitore di trasmissione. 

Questa guida illustra come creare un ricevitore di trasmissioni e come registrarlo in modo da poter ricevere i broadcast.

## <a name="creating-a-broadcast-receiver"></a>Creazione di un ricevitore di trasmissione

Per creare un ricevitore di trasmissioni in xamarin. Android, un'applicazione deve essere una sottoclasse di `BroadcastReceiver` di classi, decorare con il `BroadcastReceiverAttribute`ed eseguire l'override di `OnReceive` (metodo):

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

Se xamarin. Android viene compilata la classe, anche aggiorneranno le AndroidManifest con i metadati necessari per registrare il ricevitore. Per un ricevitore di trasmissioni registrato in modo statico, il `Enabled` correttamente, deve essere impostata su `true`, in caso contrario Android non sarà in grado di creare un'istanza del destinatario.
 
Il `Exported` proprietà controlla se il ricevitore di trasmissione può ricevere messaggi dall'esterno dell'applicazione. Se la proprietà non è impostata in modo esplicito, il valore predefinito della proprietà è determinato dal basato su se esistono eventuali filtri di intent associati ricevitori di trasmissioni di Android. Se esiste almeno un filtro intent per il ricevitore di trasmissioni, Android presupporrà che il `Exported` è di proprietà `true`. Se non sono presenti filtri di intent associati ricevitori di trasmissioni e quindi Android presupporrà che il valore è `false`. 

Il `OnReceive` metodo riceve un riferimento al `Intent` che è stato inviato al ricevitore di trasmissione. In questo modo è possibile che il mittente dell'intento di passare i valori per il ricevitore di trasmissione.

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>In modo statico la registrazione di un ricevitore di trasmissione con un filtro Intent

Quando un `BroadcastReceiver` decorata con il [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/), xamarin. Android aggiungerà le necessarie `<intent-filter>` elemento per il Android manifesto in fase di compilazione. Il frammento seguente è riportato un esempio di un ricevitore di trasmissione che verrà eseguito quando un dispositivo ha completato l'avvio (se sono state concesse le autorizzazioni di Android appropriate dall'utente):

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

È anche possibile creare un filtro intent che risponderà a scopi personalizzati. Si consideri l'esempio seguente: 

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

Le app destinate a Android 8.0 (livello API 26) o versione successiva non potrà staticamente registrarsi per una trasmissione implicita. Le app possono registrare comunque in modo statico per una trasmissione esplicita. È presente un elenco ridotto di trasmissioni implicite che non sono interessati da questa limitazione. Queste eccezioni sono descritte nel [implicita eccezioni Broadcast](https://developer.android.com/guide/components/broadcast-exceptions.html) Guida nella documentazione di Android. Le app che sono interessate a trasmissioni implicite devono farlo dinamicamente usando la `RegisterReceiver` (metodo). Questo è descritto di seguito.

### <a name="context-registering-a-broadcast-receiver"></a>Contesto-registrazione di un ricevitore di trasmissione

Contesto di registrazione (detta anche la registrazione dinamica) di un ricevitore viene eseguita invocando la `RegisterReceiver` metodo e il ricevitore di trasmissioni deve essere annullata con una chiamata al `UnregisterReceiver` (metodo). Per evitare perdite di risorse, è importante annullare la registrazione ricevitore quando non è più rilevante per il contesto (l'attività o il servizio). Ad esempio, un servizio può trasmettere un intent per segnalare un'attività che gli aggiornamenti disponibili da visualizzare all'utente. Quando l'attività di avvio, verrà registrato per tali finalità. Quando l'attività viene spostata in background e non saranno più visibili all'utente, consigliabile annullare la registrazione il ricevitore perché l'interfaccia utente per visualizzare gli aggiornamenti non è più visibile. Il frammento di codice seguente è un esempio di come registrare e annullare la registrazione di un ricevitore di trasmissione nel contesto di un'attività:

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

Nell'esempio precedente, quando l'attività entra in primo piano, verrà registrato un ricevitore di trasmissioni che rimarrà in ascolto per un programma personalizzato usando il `OnResume` metodo del ciclo di vita. Mentre si sposta l'attività in background, il `OnPause()` metodo verrà annullata la registrazione ricevitore.

## <a name="publishing-a-broadcast"></a>Pubblicazione di una trasmissione

È possibile pubblicare una trasmissione in tutte le app installate nel dispositivo la creazione di un oggetto Intent e distribuirlo con il `SendBroadcast` o il `SendOrderedBroadcast` (metodo).  

1. **I metodi Context.SendBroadcast** &ndash; esistono diverse implementazioni di questo metodo.
   Questi metodi verranno trasmesso l'intento all'intero sistema. Ricevitori di trasmissioni che riceveranno lo scopo in un ordine indeterminato. Ciò garantisce una notevole flessibilità ma significa che è possibile che altre applicazioni per registrarsi e ricevere la finalità. Ciò può causare un rischio di sicurezza potenziale. Le applicazioni potrebbe essere necessario implementare la sicurezza di addizione per impedire accessi non autorizzati. Una possibile soluzione consiste nell'usare il `LocalBroadcastManager` solo che invierà i messaggi nello spazio privato dell'app. Questo frammento di codice è un esempio di come inviare un intent usando uno del `SendBroadcast` metodi:

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   message.PutExtra("key", "value");
   SendBroadcast(message);
   ```

    Questo frammento di codice è un altro esempio di invio di una trasmissione utilizzando la `Intent.SetAction` metodo per identificare l'azione:

    ```csharp
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **Context.SendOrderedBroadcast** &ndash; si tratta di metodo è molto simile a `Context.SendBroadcast`, con la differenza è che l'intento sarà pubblicato uno alla volta ai ricevitori, nell'ordine che sono stati registrati i ricevitori.

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

Il [libreria di supporto di Xamarin v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) fornisce una classe helper denominata [ `LocalBroadcastManager` ](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html). Il `LocalBroadcastManager` è previsto per le app che non si desidera inviare o ricevere le trasmissioni da altre App sul dispositivo. Il `LocalBroadcastManager` pubblicheranno messaggi all'interno del contesto dell'applicazione e solo per i ricevitori di trasmissioni che sono registrati con solo il `LocalBroadcastManager`. Questo frammento di codice è un esempio di registrazione di un ricevitore di trasmissioni con `LocalBroadcastManager`:

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

Altre App sul dispositivo non può ricevere i messaggi che vengono pubblicati con il `LocalBroadcastManager`. Questo frammento di codice illustra come inviare un preventivo con il `LocalBroadcastManager`:

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
message.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>Collegamenti correlati

- [BroadcastReceiver API](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Context.RegisterReceiver API](https://developer.xamarin.com/api/member/Android.Content.Context.RegisterReceiver/p/Android.Content.BroadcastReceiver/Android.Content.IntentFilter/System.String/Android.OS.Handler/)
- [Context.SendBroadcast API](https://developer.xamarin.com/api/member/Android.Content.Context.SendBroadcast/p/Android.Content.Intent/)
- [Context.UnregisterReceiver API](https://developer.xamarin.com/api/member/Android.Content.Context.UnregisterReceiver/p/Android.Content.BroadcastReceiver/)
- [API preventivo](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [IntentFilter API](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
- [LocalBroadcastManager (documentazione Android)](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [Le notifiche locali in Android](~/android/app-fundamentals/notifications/local-notifications.md) Guida
- [Android. Support v4 di libreria (NuGet)](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
