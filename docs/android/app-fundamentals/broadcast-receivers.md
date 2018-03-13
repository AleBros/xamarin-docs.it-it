---
title: Ricevitori di trasmissione in xamarin
description: In questa sezione viene illustrato come utilizzare un ricevitore di trasmissione.
ms.topic: article
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/09/2018
ms.openlocfilehash: b2da136ddfa6aab4121ba21d0e6f83b2390ba10b
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Ricevitori di trasmissione in xamarin

_In questa sezione viene illustrato come utilizzare un ricevitore di trasmissione._


## <a name="broadcast-receiver-overview"></a>Panoramica di broadcast destinatario

Oggetto _broadcast destinatario_ è un componente di Android che consente a un'applicazione rispondere a messaggi (un Android [ `Intent` ](https://developer.xamarin.com/api/type/Android.Content.Intent/)) che vengono trasmessi dal sistema operativo Android o da un'applicazione. Trasmissioni seguono un _pubblicazione-sottoscrizione_ modello &ndash; un evento comporta una trasmissione da pubblicare e ricevuti da tali componenti che sono interessati nell'evento. 

Android identifica due categorie di trasmissioni:

* **Trasmissione normale** &ndash; una trasmissione normale sarà indirizzata a tutti i ricevitori broadcast registrati in un ordine indeterminato. Ogni ricevitore riceverà le finalità di un ordine non definito. 
* **Trasmissione ordinata** &ndash; una trasmissione ordinata viene recapitata una alla volta ricevitori registrati. Quando viene ricevuta la finalità, il ricevitore broadcast è possibile modificare lo scopo o possa terminare la trasmissione.

Il ricevitore broadcast è una sottoclasse del `BroadcastReceiver` classe e deve eseguire l'override di [ `OnReceive` ](https://developer.xamarin.com/api/member/Android.Content.BroadcastReceiver.OnReceive/p/Android.Content.Context/Android.Content.Intent/) metodo. Android eseguirà `OnReceive` nel thread principale, pertanto, questo metodo deve essere progettato per eseguire rapidamente. Prestare attenzione quando durante la generazione del thread nel `OnReceive` perché Android può terminare il processo quando il metodo termina. Se un ricevitore di trasmissione deve eseguire il lavoro di lunga esecuzione, è consigliabile pianificare un _processo_ utilizzando il `JobScheduler` o _Firebase processo Dispatcher_. Verrà descritta la pianificazione di lavoro con un processo in una guida separata.

Un _filtro preventivo_ viene utilizzato per registrare un ricevitore di trasmissione in modo che Android possono indirizzare correttamente i messaggi. È possibile specificare il filtro preventivo in fase di esecuzione (ciò è talvolta detta un _contesto registrato ricevitore_ o come _registrazione dinamica_) o può essere definito in modo statico in (un dimanifestoAndroid_registrato manifesto ricevitore_). Xamarin fornisce un attributo di c#, `IntentFilterAttribute`, che verrà registrato in modo statico il filtro preventivo (ciò verrà descritta in dettaglio più avanti in questa Guida). 

La differenza principale tra il destinatario registrato manifesto e il destinatario del contesto registrato è che un destinatario registrato contesto risponderà solo alle trasmissioni durante l'esecuzione di un'applicazione, mentre un ricevitore registrato manifesto può rispondere a trasmette anche se l'app potrebbe non essere in esecuzione.  

Esistono due set di API per la gestione di un ricevitore di trasmissione e l'invio di trasmissioni:

1. **`Context`** &ndash; La `Android.Content.Context` classe può essere utilizzata per registrare un ricevitore di trasmissione che verrà rispondere agli eventi a livello di sistema. Il `Context` viene inoltre utilizzato per pubblicare le trasmissioni a livello di sistema.
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; Si tratta di un'API che è disponibile tramite il [pacchetto NuGet di libreria di supporto Xamarin v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Questa classe viene utilizzata per mantenere le trasmissioni e broadcast ricevitori isolati nel contesto dell'applicazione che li utilizza. Questa classe può essere utile per impedire che risponde alle trasmissioni solo applicazione o l'invio di messaggi per i ricevitori privati di altre applicazioni.

Un ricevitore di broadcast non vengano visualizzate le finestre di dialogo, ed è fortemente sconsigliata per avviare un'attività dall'interno di un ricevitore di trasmissione. Se un ricevitore di trasmissione deve notificare l'utente, è necessario pubblicare una notifica.

Non è possibile associare o avviare un servizio dall'interno di un ricevitore di trasmissione. 

Questa guida illustra come creare un ricevitore di broadcast e registrarlo in modo che è possibile ricevere trasmissioni.

## <a name="creating-a-broadcast-receiver"></a>Creazione di un ricevitore di Broadcast

Per creare un ricevitore di trasmissione in xamarin, un'applicazione deve essere una sottoclasse di `BroadcastReceiver` classe, per decorare con il `BroadcastReceiverAttribute`ed eseguire l'override di `OnReceive` (metodo):

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

Quando xamarin consente di compilare la classe, verrà aggiornato anche il AndroidManifest con i metadati necessari per registrare il ricevitore. Per i ricevitori di broadcast registrato in modo statico, il `Enabled` correttamente, deve essere impostato su `true`, in caso contrario Android non sarà in grado di creare un'istanza del ricevitore.
 
Il `Exported` proprietà controlla se il broadcast destinatario può ricevere messaggi provenienti dall'esterno dell'applicazione. Se la proprietà non è impostata in modo esplicito, il valore predefinito della proprietà è determinato da Android in base se sono presenti eventuali finalità-filtri associati a broadcast destinatario. Se è presente almeno un filtro con finalità di broadcast destinatario quindi Android presupporrà che la `Exported` proprietà `true`. Se non sono associati a broadcast destinatario finalità-filtri, quindi Android presupporrà che il valore è `false`. 

Il `OnReceive` metodo riceve un riferimento di `Intent` che è stata inviata al ricevitore di trasmissione. In questo modo è possibile che il mittente dell'intento di passare i valori al ricevitore di trasmissione.

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>In modo statico la registrazione di un ricevitore di trasmissione con un filtro preventivo

Quando un `BroadcastReceiver` è decorata con il [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/), xamarin aggiungerà necessari `<intent-filter>` elemento per il Android manifesto in fase di compilazione. Nel frammento seguente è riportato un esempio di un ricevitore di trasmissione che verrà eseguito quando un dispositivo ha completato l'avvio (se le autorizzazioni appropriate di Android sono state concesse dall'utente):

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

È anche possibile creare un filtro preventivo che risponda a scopi personalizzati. Si consideri l'esempio seguente: 

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

### <a name="context-registering-a-broadcast-receiver"></a>Registrazione di un ricevitore Broadcast contesto 

Registrazione di un ricevitore di contesto viene eseguita invocando la `RegisterReceiver` metodo e il destinatario di trasmissione deve essere registrato con una chiamata al `UnregisterReceiver` metodo. Per evitare perdite di risorse, è importante annullare la registrazione il ricevitore quando non è più rilevante per il contesto. Ad esempio, un servizio può trasmettere un intento per informare un'attività che gli aggiornamenti disponibili da visualizzare all'utente. Quando si avvia l'attività, registrato per tali scopi. Quando l'attività viene spostata in background e non sono più visibili all'utente, non deve annullare la registrazione il destinatario perché l'interfaccia utente per visualizzare gli aggiornamenti non è più visibile. Frammento di codice seguente è riportato un esempio di come registrare e annullare la registrazione di un ricevitore di trasmissione nel contesto di un'attività:

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

Nell'esempio precedente, quando l'attività in primo piano, verrà registrato un ricevitore di trasmissione che sarà in ascolto per una finalità personalizzato utilizzando la `OnResume` metodo del ciclo di vita. Come si sposta l'attività in background, il `OnPause()` metodo annullare il ricevitore.

## <a name="publishing-a-broadcast"></a>Pubblicazione di una trasmissione

La trasmissione di una pubblicazione incapsulando un _azione_ intento e distribuirlo con uno dei due API: 

1. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; Che vengono pubblicati con la `LocalBroadcastManager` verranno ricevuti solo dall'applicazione; non verranno indirizzati ad altre applicazioni. Deve essere preferito, fornisce un ulteriore livello di sicurezza, mantenendo i tipi all'interno dell'applicazione corrente, e poiché tutto ciò che è in-process non è previsto alcun overhead associato all'esecuzione di chiamate tra processi. Questo frammento di codice viene illustrato come un'attività potrebbe inviare un preventivo utilizzando il `LocalBroadcastManager`:

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   intent.PutExtra("key", "value");
   Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
   ```

2. **[`Context.SendBroadcast`](https://developer.xamarin.com/api/member/Android.Content.Context.SendBroadcast/p/Android.Content.Intent/) metodi** &ndash; sono disponibili diverse implementazioni di questo metodo.
   Questi metodi verranno trasmesso all'intento dell'intero sistema. Fornisce una notevole flessibilità, ma significa che le altre applicazioni possono registrarsi per ricevere l'applicazione. Ciò può rappresentare un potenziale rischio di sicurezza. Applicazioni potrebbero essere necessario implementare la sicurezza aggiunta per garantire l'accesso non autorizzato allo scopo. Questo frammento di codice è un esempio di come inviare un intento utilizzando uno del `SendBroadcast` metodi:

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   intent.PutExtra("key", "value");
   SendBroadcast(intent);
   ```
        
> [!NOTE]
> È disponibile tramite il LocalBroadcastManager il [libreria di supporto Xamarin v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) pacchetto NuGet. 

Questo frammento è un altro esempio di invio di una trasmissione utilizzando il `Intent.SetAction` metodo per identificare l'azione:

```csharp 
Intent intent = new Intent();
intent.SetAction("com.xamarin.example.TEST");
intent.PutExtra("key", "value");
SendBroadcast(intent);
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
