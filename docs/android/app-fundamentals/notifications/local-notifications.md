---
title: Notifiche locali
description: Questa sezione illustra come implementare le notifiche locali in xamarin. Android. Illustra i vari elementi dell'interfaccia utente di una notifica di Android e l'API di coinvolti con la creazione e visualizzazione di una notifica.
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: c36b31e28011bea287903ee0681a316209abd22d
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829990"
---
<a name="compatibility"></a>

# <a name="local-notifications"></a>Notifiche locali

_Questa sezione illustra come implementare le notifiche locali in xamarin. Android. Illustra i vari elementi dell'interfaccia utente di una notifica di Android e l'API di coinvolti con la creazione e visualizzazione di una notifica._

## <a name="local-notifications-overview"></a>Panoramica delle notifiche locali

Android offre due aree e controllato dal sistema per la visualizzazione delle icone di notifica e le informazioni di notifica all'utente. Quando una notifica verrà pubblicata per prime, la relativa icona viene visualizzata nel *area di notifica*, come illustrato nello screenshot seguente:

![Area di notifica di esempio in un dispositivo](local-notifications-images/01-notification-shade.png)

Per ottenere dettagli sulla notifica, l'utente può aprire il pannello di notifiche (che si espande ogni icona di notifica per visualizzare il contenuto di notifica) ed eseguire eventuali azioni associate le notifiche. La schermata seguente mostra una *Pannello di notifiche* che corrisponde all'area di notifica visualizzato sopra:

[![Pannello di notifiche di esempio la visualizzazione di tre notifiche](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Notifiche di Android usano due tipi di layout:

-   ***Layout di base*** &ndash; un formato di presentazione compatto, predefinito.

-   ***Layout espanso*** &ndash; un formato di presentazione che può espandersi in una dimensione maggiore per visualizzare ulteriori informazioni.

Ognuno di questi tipi di layout (e come crearli) sono illustrati nelle sezioni seguenti.

> [!NOTE]
> Questa guida è incentrata sul [NotificationCompat APIs](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) dal [libreria di supporto Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Queste API verranno garantire massima la compatibilità per Android 4.0 (livello API 14).


### <a name="base-layout"></a>Layout di base

Tutte le notifiche di Android sono basate sul formato di layout di base, che, come minimo, include gli elementi seguenti:

1.  Oggetto *icona di notifica*, che rappresenta l'app di origine o il tipo di notifica se l'app supporta diversi tipi di notifiche.

2.  La notifica *titolo*, o il nome del mittente se la notifica è un messaggio personale.

3.  Il messaggio di notifica.

4.  Oggetto *timestamp*.

Questi elementi vengono visualizzati come illustrato nel diagramma seguente:

[![Posizione degli elementi di notifica](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

Layout di base sono limitate a 64 pixel indipendenti dalla densità (dp) in altezza. Android crea questo stile di notifica di base per impostazione predefinita.

Facoltativamente, le notifiche possono visualizzare un'icona di grandi dimensioni che rappresenta l'applicazione o una foto del mittente. Quando viene usata un'icona di grandi dimensioni in una notifica in Android 5.0 e versioni successive, l'icona di notifica di piccole dimensioni viene visualizzato come notifica badge sull'icona di grandi dimensioni:

![Foto di notifica semplice](local-notifications-images/04-simple-notification-photo.png)

A partire da Android 5.0, le notifiche possono essere visualizzati anche nella schermata di blocco:

[![Esempio di notifica di blocco dello schermo](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

L'utente può toccare due volte la notifica di schermata di blocco per sbloccare il dispositivo e passare all'app che ha avuto origine la notifica, oppure scorrere rapidamente per ignorarla. Le app possono impostare il livello di visibilità di una notifica a quanto illustrato nella schermata di blocco di controllo e gli utenti possono scegliere se consentire contenuti sensibili da visualizzare nelle notifiche schermata di blocco.

Android 5.0 è stato introdotto un formato di presentazione di notifica ad alta priorità chiamato *Heads-up*. Le notifiche Heads-up scorrere verso il basso dalla parte superiore della schermata per pochi secondi e quindi riesame eseguire il backup in area di notifica:

[![Esempio di notifica heads-up](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

Le notifiche Heads-up rendono possibili per il sistema dell'interfaccia utente di inserire informazioni importanti davanti all'utente senza interrompere lo stato dell'attività attualmente in esecuzione.

Android include il supporto per i metadati di notifica in modo che le notifiche possono essere ordinate e visualizzate in modo intelligente. I metadati di notifica controlla anche la modalità di presentazione delle notifiche nella schermata di blocco e nel formato Heads-up. Le applicazioni possono impostare i seguenti tipi di metadati di notifica:

-   **Priorità** &ndash; il livello di priorità determina come e quando le notifiche vengono visualizzate. Ad esempio, In Android 5.0, le notifiche ad alta priorità vengono visualizzate come notifiche Heads-up.

-   **Visibilità** &ndash; specifica la quantità di contenuto notifica deve essere visualizzato quando la notifica viene visualizzata nella schermata di blocco.

-   **Categoria** &ndash; informa il sistema come gestire la notifica in diverse circostanze, ad esempio quando il dispositivo viene *non disturbare* modalità.

> [!NOTE]
> **Visibilità** e **categoria** introdotti in Android 5.0 e non sono disponibili nelle versioni precedenti di Android. A partire da Android 8.0 [canali di notifica](#notif-chan) consentono di controllare come le notifiche vengono presentate all'utente.


### <a name="expanded-layouts"></a>Layout espanso

A partire da Android 4.1, le notifiche sono configurabili con gli stili di layout espansa che consentono all'utente di espandere l'altezza della notifica per visualizzare altri contenuti. Ad esempio, nell'esempio seguente viene illustrata una notifica di layout espanso in modalità di ambito di un contratto:

![Ambito di un contratto di notifica](local-notifications-images/07-contracted-notification.png)

Quando questa notifica viene espanso, rivela l'intero messaggio:

![Notifica espansa](local-notifications-images/08-expanded-notification.png)

Android supporta tre stili di layout espanso per le notifiche degli eventi-singolo:

-   ***Testo grande*** &ndash; In modalità di ambito di un contratto, viene visualizzato un estratto della prima riga del messaggio seguito da due punti. In modalità espansa, Visualizza l'intero messaggio (come illustrato nell'esempio precedente).

-   ***Posta in arrivo*** &ndash; In modalità di ambito di un contratto, Visualizza il numero di nuovi messaggi. In modalità espansa, Visualizza il primo messaggio di posta elettronica o un elenco dei messaggi in arrivo.

-   ***Immagine*** &ndash; In modalità di ambito di un contratto, viene visualizzato solo il testo del messaggio. In modalità espansa, Visualizza il testo e un'immagine.

[Oltre la notifica di base](#beyond-the-basic-notification) (più avanti in questo articolo) spiega come creare *testo grande*, *posta in arrivo*, e *immagine* notifiche.

<a name="notif-chan"></a>
<a name="notification-channels"></a>
## <a name="notification-channels"></a>Canali di notifica

A partire da Android 8.0 (Oreo), è possibile usare la *canali di notifica* funzionalità per creare un canale personalizzabili dall'utente per ogni tipo di notifica che si desidera visualizzare. I canali di notifica rendono possibili per l'utente per le notifiche di gruppo in modo che tutte le notifiche registrato in un documento di canale in modo analogo. Ad esempio, potrebbe essere un canale di notifica che è previsto per le notifiche che richiedono attenzione immediata e un canale separato "risulteranno meno intensi" che viene usato per i messaggi informativi.

Il **YouTube** app che viene installato con Android Oreo sono elencati due categorie di notifica: **Scaricare le notifiche** e **notifiche generali**:

[![Schermate di notifica per YouTube in Android Oreo](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

Ognuna di queste categorie corrisponde a un canale di notifica. YouTube app implementa un **notifiche scaricare** canale e una **notifiche generali** canale. L'utente può toccare **scaricare notifiche**, che consente di visualizzare la schermata Impostazioni per il canale di notifiche di download dell'app:

[![Scaricare schermata notifiche dell'App YouTube](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

In questa schermata, l'utente può modificare il comportamento dei **scaricare** le notifiche di channel eseguendo le operazioni seguenti:

-   Impostare il livello di importanza **urgente**, **elevata**, **Medium**, o **bassa**, che configura il livello di interruzioni audio e visual.

-   Il punto di notifica di attivare o disattivare.

-   Attivare la luce intermittente o disattivare.

-   Mostra o Nascondi le notifiche nella schermata di blocco.

-   Eseguire l'override di **non disturbare** impostazione.

Il **notifiche generali** canale con impostazioni simili:

[![Schermata di notifiche generali dell'App YouTube](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

Si noti che non è assoluto controllo sul modo in cui i canali di notifica interagiscono con l'utente &ndash; l'utente può modificare le impostazioni per qualsiasi canale di notifica sul dispositivo come illustrato nelle schermate precedenti. Tuttavia, è possibile configurare i valori predefiniti (come verrà illustrato di seguito). Come illustrano in questi esempi, la nuova funzionalità di canali di notifica rende possibile per l'utente concedere agli utenti un controllo accurato diversi tipi di notifiche.


## <a name="notification-creation"></a>Creazione di notifica

Per creare una notifica in Android, si utilizza il [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder) classe la [Xamarin.Android.Support.v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) pacchetto NuGet. Questa classe rende possibile creare e pubblicare le notifiche nelle versioni precedenti di Android. Per altre informazioni sull'uso `NotificationCompat.Builder`, vedere [compatibilità](#compatibility) più avanti in questo argomento.

`NotificationCompat.Builder` fornisce metodi per impostare le varie opzioni in una notifica, quali:

-   Il contenuto, incluso il titolo, il testo del messaggio e l'icona di notifica.

-   Lo stile della notifica, ad esempio *testo grande*, *posta in arrivo*, o *immagine* stile.

-   La priorità della notifica: come minimo, bassa, predefinita elevata o massima. In Android 8.0 e versioni successive, la priorità viene impostata tramite un [ _canale di notifica_](#notification-channels).

-   La visibilità della notifica nella schermata di blocco: pubblico, privato o segreto.

-   Metadati di categoria che consente di Android classificano e filtrare la notifica.

-   Un intent facoltativo che indica un'attività da avviare quando si tocca la notifica.

-   L'ID del canale di notifica che la notifica verrà pubblicata in (Android 8.0 e versioni successive).

Dopo aver impostato le opzioni del generatore di, si genera un oggetto di notifica che contiene le impostazioni. Per pubblicare la notifica, si passa l'oggetto di notifica per il *Notification Manager*. Android offre il [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/) (classe), che è responsabile per le notifiche di pubblicazione e la visualizzazione all'utente. Un riferimento a questa classe può essere ottenuto da qualsiasi contesto, ad esempio un'attività o un servizio.


### <a name="creating-a-notification-channel"></a>Creazione di un canale di notifica

Le app che eseguono Android 8.0 è necessario creare un canale di notifica per le notifiche. Un canale di notifica richiede i seguenti tre tipi di informazioni:

* Una stringa ID univoca per il pacchetto che identificherà il canale.
* Il nome del canale che verrà visualizzato all'utente.  Il nome deve essere compreso tra 1 e 40 caratteri.
* L'importanza del canale.

Le app dovranno verificare la versione di Android che sono in esecuzione.
I dispositivi che eseguono le versioni precedenti a Android 8.0 non creare un canale di notifica. Il metodo seguente è un esempio di come creare un canale di notifica in un'attività:

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var channelName = Resources.GetString(Resource.String.channel_name);
    var channelDescription = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
                  {
                      Description = channelDescription
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Il canale di notifica deve essere creato ogni volta che viene creata l'attività. Per il `CreateNotificationChannel` metodo, deve essere chiamato `OnCreate` metodo di un'attività.

### <a name="creating-and-publishing-a-notification"></a>Creazione e pubblicazione di una notifica

Per generare una notifica in Android, seguire questa procedura:

1.  Creare un'istanza di un `NotificationCompat.Builder` oggetto.

2.  Chiamare metodi diversi sul `NotificationCompat.Builder` oggetto per impostare le opzioni di notifica.

3.  Chiamare il [compilare](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.Build/) metodo il `NotificationCompat.Builder` oggetto per creare un'istanza di un oggetto delle notifiche.

4.  Chiamare il [Notify](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/(System.Int32%2cAndroid.App.Notification)) metodo di gestione notifiche per la notifica di pubblicazione.

È necessario fornire almeno le informazioni seguenti per ogni notifica:

-   Un'icona piccola (24 x 24 dp dimensioni)

-   Un titolo breve

-   Il testo della notifica

Esempio di codice seguente viene illustrato come utilizzare `NotificationCompat.Builder` per generare una notifica di base. Si noti che `NotificationCompat.Builder` metodi supportano [concatenamento del metodo](https://en.wikipedia.org/wiki/Method_chaining); vale a dire, ogni metodo restituisce l'oggetto compilatore in modo che è possibile usare il risultato dell'ultima chiamata al metodo per richiamare la successiva chiamata al metodo:

```csharp
// Instantiate the builder and set notification elements:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

In questo esempio, una nuova `NotificationCompat.Builder` oggetto chiamato `builder` viene creata un'istanza, insieme all'ID del canale di notifica da usare. Il titolo e testo della notifica siano impostate e l'icona di notifica viene caricato dal **Resources/drawable/ic_notification.png**. La chiamata per il generatore di notifica `Build` metodo crea un oggetto delle notifiche con queste impostazioni. Il passaggio successivo consiste nel chiamare il `Notify` metodo di notification manager. Per individuare la gestione delle notifiche, chiamare `GetSystemService`, come illustrato in precedenza.

Il `Notify` metodo accetta due parametri: l'identificatore di notifica e l'oggetto notifica. Identificatore di notifica è un integer univoco che identifica la notifica all'applicazione. In questo esempio, l'identificatore di notifica è impostata su zero (0). Tuttavia, in un'applicazione di produzione, si dovranno assegnare un identificatore univoco di ogni notifica. Riutilizza il precedente valore dell'identificatore in una chiamata a `Notify` fa sì che l'ultima notifica devono essere sovrascritti.

Quando questo codice viene eseguito in un dispositivo Android 5.0, questo genera una notifica che è simile al seguente:

![Risultato della notifica per il codice di esempio](local-notifications-images/09-hello-world.png)

L'icona di notifica viene visualizzata sul lato sinistro della notifica &ndash; questa immagine di un cerchio &ldquo;ho&rdquo; dispone di un canale alfa in modo che Android può disegnare uno sfondo grigio circolare dietro di essa. È anche possibile fornire un'icona senza canale alfa. Per visualizzare un'immagine fotografica ridotta a icona, vedere [formato delle icone grandi](#large-icon-format) più avanti in questo argomento.

Il timestamp viene impostato automaticamente, ma è possibile eseguire l'override di questa impostazione tramite la chiamata di [SetWhen](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetWhen/) metodo del generatore di notifica. Ad esempio, il codice seguente imposta il timestamp per l'ora corrente:

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>Abilitazione di vibrazione e suono

Se si vuole che la notifica anche riprodurre un suono, è possibile chiamare il generatore di notifica [SetDefaults](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetDefaults/) metodo e passare il `NotificationDefaults.Sound` flag:

```csharp
// Instantiate the notification builder and enable sound:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

Questa chiamata a `SetDefaults` causerà il dispositivo per riprodurre un suono quando viene pubblicata la notifica. Se si vuole che il dispositivo di vibrazione anziché riprodurre un suono, è possibile passare `NotificationDefaults.Vibrate` al `SetDefaults.` se si vuole che il dispositivo per riprodurre un suono e vibrare il dispositivo, è possibile passare a entrambi i flag `SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

Se si abilita l'audio senza specificare un suono da riprodurre, Android Usa il suono di notifica di sistema predefinito. Tuttavia, è possibile modificare il file audio viene riprodotto chiamando il generatore di notifica [SetSound](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetSound/p/Android.Net.Uri/) (metodo). Ad esempio, per riprodurre l'allarme suono con la notifica (anziché l'audio di notifica predefinito), è possibile ottenere l'URI per l'allarme audio dal [RingtoneManager](https://developer.xamarin.com/api/type/Android.Media.RingtoneManager/) e passarlo a `SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

In alternativa, è possibile usare la suoneria predefinita di sistema audio per la notifica:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

Dopo aver creato un oggetto delle notifiche, è possibile impostare le proprietà di notifica per l'oggetto notifica (piuttosto che configurarli in anticipo tramite `NotificationCompat.Builder` metodi). Ad esempio, invece di chiamare il `SetDefaults` metodo per consentire la vibrazione su una notifica, è possibile modificare direttamente il flag di bit della notifica [per impostazione predefinita](https://developer.xamarin.com/api/property/Android.App.Notification.Defaults/) proprietà:

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

In questo esempio consente al dispositivo di vibrazione quando viene pubblicata la notifica.

<a name="updating-a-notification" />

### <a name="updating-a-notification"></a>L'aggiornamento di una notifica

Se si desidera aggiornare il contenuto di una notifica dopo che è stata pubblicata, è possibile usare di nuovo `NotificationCompat.Builder` oggetto per creare un nuovo oggetto di notifica e la notifica con l'identificatore dell'ultima notifica di pubblicazione. Ad esempio:

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

In questo esempio, l'oggetto esistente `NotificationCompat.Builder` oggetto viene usato per creare un nuovo oggetto di notifica con un titolo diverso e un messaggio.
Il nuovo oggetto di notifica è stato pubblicato tramite l'identificatore della precedente notifica e si aggiorna il contenuto della notifica pubblicata in precedenza:

![Notifica aggiornata](local-notifications-images/12-updated-notification.png)

Il corpo della notifica precedente è stato riusato &ndash; solo il titolo e il testo della notifica varia anche se la notifica viene visualizzata nel menu informazioni di notifica. Il testo del titolo cambia da "Notifica di esempio" a "Notifica di aggiornamento" e il testo del messaggio viene modificato da "Hello World! Questa è la prima notifica!" per "Changed a questo messaggio".

Una notifica rimane visibile fino a quando non si verifica una delle tre operazioni:

-   L'utente chiude la notifica (o tocca *Cancella tutto*).

-   L'applicazione effettua una chiamata a `NotificationManager.Cancel`, passando l'ID di notifica univoco assegnato al momento della notifica è stata pubblicata.

-   L'applicazione chiama `NotificationManager.CancelAll`.

Per altre informazioni sull'aggiornamento delle notifiche di Android, vedere [modificare una notifica](https://developer.android.com/training/notify-user/managing.html#Updating).


### <a name="starting-an-activity-from-a-notification"></a>Avvio di un'attività da una notifica

In Android, è comune per una notifica da associare a un *azione* &ndash; un'attività che viene avviata quando l'utente tocca la notifica. Questa attività può risiedere in un'altra applicazione o anche in un'altra attività. Per aggiungere un'azione a una notifica, crei una [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/) dell'oggetto e associare il `PendingIntent` con la notifica. Oggetto `PendingIntent` è un tipo speciale di intent che consente all'applicazione destinataria eseguire una parte predefinita di codice con le autorizzazioni dell'applicazione mittente. Quando l'utente tocca la notifica, l'attività specificata da avvio Android il `PendingIntent`.

Il frammento di codice seguente viene illustrato come creare una notifica con un `PendingIntent` che avvia l'attività dell'app, origine `MainActivity`:

```csharp
// Set up an intent so that tapping the notifications returns to this app:
Intent intent = new Intent (this, typeof(MainActivity));

// Create a PendingIntent; we're only using one PendingIntent (ID = 0):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    PendingIntent.GetActivity (this, pendingIntentId, intent, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

Questo codice è molto simile al codice di notifica nella sezione precedente, tranne il fatto che un `PendingIntent` viene aggiunto all'oggetto di notifica. In questo esempio, il `PendingIntent` viene associata all'attività dell'app origine prima di essere passato per il generatore di notifica [SetContentIntent](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetContentIntent/) (metodo). Il `PendingIntentFlags.OneShot` flag viene passato al `PendingIntent.GetActivity` metodo in modo che il `PendingIntent` viene utilizzata una sola volta. Quando si esegue questo codice, viene visualizzata la notifica seguente:

![Notifica prima dell'azione](local-notifications-images/10-first-action-notification.png)

Se si tocca la notifica indirizza l'utente torna all'attività di origine.

In un'app di produzione, l'app deve gestire il *stack Indietro* quando l'utente preme il **nuovamente** pulsante all'interno dell'attività di notifica (se non ha familiarità con lo stack indietro e attività Android, vedere [ Attività e Stack Indietro](https://developer.android.com/guide/components/tasks-and-back-stack.html)).
Nella maggior parte dei casi, lo spostamento all'indietro verso l'attività di notifica deve restituire all'utente all'esterno dell'app e tornare alla schermata Home. Per gestire lo stack indietro, l'app Usa la [TaskStackBuilder](https://developer.xamarin.com/api/type/Android.App.TaskStackBuilder/) classe per creare un `PendingIntent` con uno stack indietro.

Un'altra considerazione del mondo reale è che l'attività di origine potrebbe essere necessario inviare dati all'attività di notifica. Ad esempio, la notifica può indicare che è arrivato un messaggio di testo e l'attività di notifica (un messaggio visualizzato schermo), richiede l'ID del messaggio per visualizzare il messaggio all'utente. L'attività che crea il `PendingIntent` possibile usare il [Intent.PutExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.PutExtra/p/System.String/System.String/) metodo per aggiungere dati (ad esempio, una stringa) per lo scopo in modo che questi dati vengono passati all'attività di notifica.

Esempio di codice seguente viene illustrato come utilizzare `TaskStackBuilder` per gestire lo stack indietro che include un esempio di come inviare una stringa singolo messaggio a un'attività di notifica denominata `SecondActivity`:

```csharp
// Setup an intent for SecondActivity:
Intent secondIntent = new Intent (this, typeof(SecondActivity));

// Pass some information to SecondActivity:
secondIntent.PutExtra ("message", "Greetings from MainActivity!");

// Create a task stack builder to manage the back stack:
TaskStackBuilder stackBuilder = TaskStackBuilder.Create(this);

// Add all parents of SecondActivity to the stack:
stackBuilder.AddParentStack (Java.Lang.Class.FromType (typeof (SecondActivity)));

// Push the intent that starts SecondActivity onto the stack:
stackBuilder.AddNextIntent (secondIntent);

// Obtain the PendingIntent for launching the task constructed by
// stackbuilder. The pending intent can be used only once (one shot):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    stackBuilder.GetPendingIntent (pendingIntentId, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including
// the pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my second action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

In questo esempio di codice, l'app è costituito da due attività: `MainActivity` (che contiene il codice di notifica precedente), e `SecondActivity`, la schermata viene visualizzata dopo aver toccato la notifica. Quando viene eseguito questo codice, viene visualizzata una notifica semplice (simile all'esempio precedente). Se si tocca la notifica richiede all'utente di `SecondActivity` dello schermo:

![Seconda schermata di attività](local-notifications-images/11-second-activity.png)

La stringa di messaggio (passato l'intento `PutExtra` metodo) viene recuperato nel `SecondActivity` tramite la riga di codice:

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

Viene visualizzato questo messaggio recuperato, "Messaggi di saluto da MainActivity!," nel `SecondActivity` schermata, come illustrato nello screenshot precedente. Quando l'utente preme il **nuovamente** pulsante mentre `SecondActivity`, navigazione conduce all'esterno dell'app e tornare alla schermata precedente l'avvio dell'app.

Per altre informazioni sulla creazione in sospeso Intent, vedere [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/).


<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>Oltre la notifica di base

Per impostazione predefinita le notifiche in un formato semplice layout di base in Android, ma è possibile migliorare questo formato di base, rendendo aggiuntive `NotificationCompat.Builder` chiamate al metodo. In questa sezione si apprenderà come aggiungere un'icona di foto di grandi dimensioni per la notifica e sono riportati esempi di come creare le notifiche di layout espanso.

<a name="large-icon-format" />

### <a name="large-icon-format"></a>Formato delle icone grandi

Notifiche di Android in genere visualizzata l'icona dell'app per l'origine (sul lato sinistro della notifica). Tuttavia, possono visualizzare le notifiche di un'immagine o una foto (un *icone grandi*) anziché la piccola icona standard. Ad esempio, un'app di messaggistica è stato possibile visualizzare una foto del mittente piuttosto che l'icona dell'app.

Di seguito è riportato un esempio di una notifica di base Android 5.0 &ndash; viene visualizzata solo l'icona di app di piccole dimensioni:

![Esempio di notifica normale](local-notifications-images/13-sample-notification.png)

Ed ecco uno screenshot della notifica dopo averlo modificato per visualizzare un'icona grande &ndash; Usa un'icona creata da un'immagine di un meccanico codice Xamarin:

![Esempio di notifica di icone grandi](local-notifications-images/14-large-icon-sample.png)

Si noti che quando una notifica viene presentata nel formato delle icone di grandi dimensioni, l'icona di app di piccole dimensioni viene visualizzata come una notifica nell'angolo inferiore destro dell'icona di grandi dimensioni.

Per usare un'immagine come un'icona di grandi dimensioni in una notifica, si chiama il generatore di notifica [SetLargeIcon](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetLargeIcon/) (metodo) e passare in una bitmap dell'immagine. A differenza `SetSmallIcon`, `SetLargeIcon` accetta solo una bitmap. Per convertire un file di immagine in una bitmap, usare il [BitmapFactory](https://developer.xamarin.com/api/type/Android.Graphics.BitmapFactory/) classe. Ad esempio:

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

Questo codice di esempio apre il file di immagine alla **Resources/drawable/monkey_icon.png**, lo converte in una bitmap e passa la bitmap risulta da `NotificationCompat.Builder`. In genere, la risoluzione dell'immagine di origine è maggiore dell'icona piccola &ndash; ma non molto più grandi. Un'immagine troppo grande può causare inutili operazioni di ridimensionamento che è stato possibile ritardare la registrazione della notifica.


### <a name="big-text-style"></a>Stile del testo di big Data

Il *testo grande* stile è un modello di layout espanso utilizzabili per la visualizzazione di messaggi lunghi nelle notifiche. Come tutte le notifiche di layout espansa, la notifica di testo grande deve essere visualizzata inizialmente in un formato di presentazione compact:

![Esempio di notifica di testo grande](local-notifications-images/15-big-text-notification.png)

In questo formato, viene visualizzato solo un frammento del messaggio, terminate da due punti. Quando l'utente trascina verso il basso sulla notifica, questa si espande per visualizzare il messaggio di notifica intero:

![Notifica di testo grande espansa](local-notifications-images/16-big-text-expanded.png)

Questo formato espanso layout include inoltre testo di riepilogo nella parte inferiore della notifica. L'altezza massima del *testo grande* notifica è 256 dp.

Per creare un *testo grande* notifica, si crea un'istanza una `NotificationCompat.Builder` dell'oggetto, come in precedenza e quindi creare un'istanza e aggiungere un [BigTextStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigTextStyle/) dell'oggetto per il `NotificationCompat.Builder` oggetto. Di seguito è fornito un esempio:

```csharp
// Instantiate the Big Text style:
Notification.BigTextStyle textStyle = new Notification.BigTextStyle();

// Fill it with text:
string longTextMessage = "I went up one pair of stairs.";
longTextMessage += " / Just like me. ";
//...
textStyle.BigText (longTextMessage);

// Set the summary text:
textStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (textStyle);

// Create the notification and publish it ...
```

In questo esempio, il testo del messaggio e il testo di riepilogo vengono archiviati nel `BigTextStyle` oggetto (`textStyle`) prima di essere passato a `NotificationCompat.Builder.`


### <a name="image-style"></a>Stile di immagine

Il *immagine* stile (chiamato anche il *quadro d'insieme* stile) è un formato espanso notifica che è possibile usare per visualizzare un'immagine nel corpo di una notifica. Ad esempio, possibile usare un'app schermata o un'app per le foto il *immagine* notifica stile da fornire all'utente una notifica dell'ultima immagine che è stata acquisita. Si noti che l'altezza massima del *immagine* notifica è 256 dp &ndash; Android verrà ridimensionato l'immagine in modo da adattarsi a questa limitazione di altezza massima, entro i limiti di memoria disponibile.

Come tutte le notifiche di layout, espanso *immagine* le notifiche vengono visualizzate prima di tutto in un formato compatto che consente di visualizzare un estratto del testo del messaggio di accompagnamento:

![Immagine Compact notifica non indica nessuna immagine](local-notifications-images/17-image-compact.png)

Quando l'utente trascina verso il basso *immagine* notifica, si espande per mostrare un'immagine. Ad esempio, ecco la versione espansa della precedente notifica:

![Immagine ampliata notifica rivela immagine](local-notifications-images/18-image-expanded.png)

Si noti che quando la notifica viene visualizzata nel formato compatto, viene visualizzato testo della notifica (il testo che viene passato per il generatore di notifica `SetContentText` metodo, come illustrato in precedenza). Tuttavia, quando la notifica viene espanso per visualizzare l'immagine, Visualizza riepilogo testo sopra l'immagine.

Per creare un *immagine* notifica, si crea un'istanza una `NotificationCompat.Builder` oggetto come in precedenza e quindi crea e inserisce un [BigPictureStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigPictureStyle/) all'oggetto di `NotificationCompat.Builder` oggetto. Ad esempio:

```csharp
// Instantiate the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Convert the image to a bitmap before passing it into the style:
picStyle.BigPicture (BitmapFactory.DecodeResource (Resources, Resource.Drawable.x_bldg));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create the notification and publish it ...
```

Ad esempio la `SetLargeIcon` metodo di `NotificationCompat.Builder`, il [BigPicture](https://developer.xamarin.com/api/member/Android.App.Notification+BigPictureStyle.BigPicture/) metodo `BigPictureStyle` richiede una bitmap dell'immagine che si desidera visualizzare nel corpo della notifica. In questo esempio, il [DecodeResource](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeResource/(Android.Content.Res.Resources%2cSystem.Int32)) metodo `BitmapFactory` legge il file di immagine che si trova in **Resources/drawable/x_bldg.png** e lo converte in una bitmap.

È inoltre possibile visualizzare le immagini che non sono state inserite come una risorsa. Ad esempio, esempio di codice seguente carica un'immagine dalla scheda SD locale e lo visualizza in un *immagine* notifica:

```csharp
// Using the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Read an image from the SD card, subsample to half size:
BitmapFactory.Options options = new BitmapFactory.Options();
options.InSampleSize = 2;
string imagePath = "/sdcard/Pictures/my-tshirt.jpg";
picStyle.BigPicture (BitmapFactory.DecodeFile (imagePath, options));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("Check out my new T-Shirt!");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create notification and publish it ...
```

In questo esempio, il file di immagine che si trova in **/sdcard/Pictures/my-tshirt.jpg** viene caricato, ridimensionata a metà della dimensione originale e quindi convertito in una bitmap per l'utilizzo della notifica:

![Immagine di esempio t-shirt nella notifica](local-notifications-images/19-tshirt-notification.png)

Se si non conoscono in anticipo le dimensioni del file di immagine, è consigliabile eseguire il wrapping della chiamata a [BitmapFactory.DecodeFile](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeFile/p/System.String/Android.Graphics.BitmapFactory+Options/) in un gestore di eccezioni &ndash; un `OutOfMemoryError` generata eccezione se l'immagine è troppo grande per Android da ridimensionare.

Per altre informazioni sul caricamento e la decodifica delle immagini bitmap di grandi dimensioni, vedere [carico elevato le bitmap in modo efficiente](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently).


### <a name="inbox-style"></a>Stile della posta in arrivo

Il *posta in arrivo* formato è un modello di layout espanso destinato per la visualizzazione di righe distinte di testo (ad esempio un messaggio di posta elettronica nella posta in arrivo riepilogo) nel corpo della notifica. Il *posta in arrivo* formato notifica la prima volta che in un formato compatto:

![Esempio di notifica di posta in arrivo compact](local-notifications-images/20-inbox-compact.png)

Quando l'utente trascina verso il basso sulla notifica, questa si espande per visualizzare un riepilogo di posta elettronica come illustrato nello screenshot seguente:

![Esempio di notifica di posta in arrivo espanso](local-notifications-images/21-inbox-expanded.png)

Per creare un *posta in arrivo* notifica, si crea un'istanza una `NotificationCompat.Builder` dell'oggetto, come in precedenza e aggiungere un [InboxStyle](https://developer.xamarin.com/api/type/Android.App.Notification+InboxStyle/) dell'oggetto per il `NotificationCompat.Builder`. Di seguito è fornito un esempio:

```csharp
// Instantiate the Inbox style:
Notification.InboxStyle inboxStyle = new Notification.InboxStyle();

// Set the title and text of the notification:
builder.SetContentTitle ("5 new messages");
builder.SetContentText ("chimchim@xamarin.com");

// Generate a message summary for the body of the notification:
inboxStyle.AddLine ("Cheeta: Bananas on sale");
inboxStyle.AddLine ("George: Curious about your blog post");
inboxStyle.AddLine ("Nikko: Need a ride to Evolve?");
inboxStyle.SetSummaryText ("+2 more");

// Plug this style into the builder:
builder.SetStyle (inboxStyle);
```

Per aggiungere nuove righe di testo al corpo della notifica, chiamare il [Addline](https://developer.xamarin.com/api/member/Android.App.Notification+InboxStyle.AddLine/p/System.String/) metodo per il `InboxStyle` oggetto (l'altezza massima del *posta in arrivo* notifica è 256 dp). Si noti che, a differenza *testo grande* stile di visualizzazione, il *posta in arrivo* stile supporta singole righe di testo nel corpo della notifica.

È anche possibile usare la *posta in arrivo* stile per le notifiche che è necessario visualizzare singole righe di testo in formato espanso. Ad esempio, il *posta in arrivo* stile notifica può essere utilizzato per combinare più notifiche in sospeso in una notifica di riepilogo &ndash; è possibile aggiornare un singolo *posta in arrivo* applicare uno stile di notifica con new righe di contenuto della notifica (vedere [l'aggiornamento di una notifica](#updating-a-notification) sopra), anziché a generare un flusso continuo di nuovo, principalmente come le notifiche.


## <a name="configuring-metadata"></a>Configurazione di metadati

`NotificationCompat.Builder` include metodi che è possibile chiamare per impostare i metadati relativi alla notifica, ad esempio la priorità, visibilità e la categoria. Android Usa queste informazioni &mdash; insieme alle impostazioni delle preferenze utente &mdash; per determinare i casi in cui visualizzare le notifiche.


### <a name="priority-settings"></a>Impostazioni di priorità

Le App in esecuzione su Android 7.1 e versioni precedenti è necessario impostare la priorità direttamente nella notifica. L'impostazione di priorità di una notifica determina due risultati quando viene pubblicata la notifica:

-   La notifica in cui viene visualizzato in relazione ad altre notifiche.
    Ad esempio, le notifiche con priorità alta vengono presentate sopra le notifiche di priorità più basso nel pannello notifiche indipendentemente da quando è stata pubblicata ogni notifica.

-   Indica se la notifica viene visualizzata nel formato Heads-up notifica (Android 5.0 e versioni successive). Solo *elevata* e *massimo* le notifiche di priorità vengono visualizzate come notifiche Heads-up.

Xamarin. Android definisce le enumerazioni seguenti per l'impostazione di priorità di notifica:

-   `NotificationPriority.Max` &ndash; Avvisa l'utente a una condizione ad alta priorità o critica (ad esempio, una chiamata in ingresso, le direzioni dal attiva-attiva o un avviso di emergenza). In Android 5.0 e versioni successive, le notifiche di priorità massima vengono visualizzate in formato Heads-up.

-   `NotificationPriority.High` &ndash; Informa l'utente di eventi importanti (ad esempio, messaggi contrassegnati come importanti o l'arrivo dei messaggi di chat in tempo reale). In Android 5.0 e versioni successive, le notifiche con priorità alta vengono visualizzate in formato Heads-up.

-   `NotificationPriority.Default` &ndash; Notifica all'utente di condizioni che hanno un livello medio di importanza.

-   `NotificationPriority.Low` &ndash; Per priorità bassa informazioni che l'utente deve essere informato di (ad esempio, i promemoria di aggiornamento software o aggiornamenti di rete basati su social network).

-   `NotificationPriority.Min` &ndash; Per informazioni complementari che l'utente rileva solo quando la visualizzazione delle notifiche (ad esempio, le informazioni di percorso o meteo).

Per impostare la priorità di una notifica, chiamare il [SetPriority](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetPriority/) metodo il `NotificationCompat.Builder` oggetto, passando il livello di priorità. Ad esempio:

```csharp
builder.SetPriority (NotificationPriority.High);
```

Nell'esempio seguente, la notifica di priorità alta, "Messaggio importante!" viene visualizzata nella parte superiore del cassetto della notifica:

![Esempio di notifica ad alta priorità](local-notifications-images/22-hi-priority-drawer.png)

Poiché si tratta di una notifica ad alta priorità, viene inoltre visualizzato come notifica Heads-up sopra schermata attività dell'utente corrente in Android 5.0:

![Esempio di notifica di Heads-up](local-notifications-images/23-heads-up-example.png)

Nell'esempio seguente, la notifica "Pensato per il giorno" con priorità bassa viene visualizzata in una notifica a livello di batteria con priorità più alta:

![Esempio di notifica con priorità bassa](local-notifications-images/24-lo-priority-drawer.png)

Poiché la notifica "Pensiero del giorno" è una notifica con priorità bassa, verranno visualizzati non sarà in formato Heads-up di Android.

> [!NOTE]
> In Android 8.0 e versioni successive, la priorità delle impostazioni utente e del canale di notifica determina la priorità della notifica.

### <a name="visibility-settings"></a>Impostazioni di visibilità

A partire da Android 5.0, il *visibilità* impostazione è disponibile per controllare la quantità di contenuto la notifica viene visualizzata nella schermata di blocco protetta.
Xamarin. Android definisce le enumerazioni seguenti per l'impostazione di visibilità di notifica:

-   `NotificationVisibility.Public` &ndash; L'intero contenuto della notifica viene visualizzato nella schermata di blocco protetta.

-   `NotificationVisibility.Private` &ndash; Solo le informazioni essenziali viene visualizzate nella schermata di blocco protetto (ad esempio l'icona di notifica e il nome dell'app che si è registrato), ma il resto dei dettagli della notifica sono nascosti. Per impostazione predefinita tutte le notifiche `NotificationVisibility.Private`.

-   `NotificationVisibility.Secret` &ndash; Nella schermata di blocco protetta, non lo è nemmeno l'icona di notifica viene visualizzato nulla. Il contenuto della notifica è disponibile solo dopo che l'utente Sblocca il dispositivo.

Per impostare la visibilità di una notifica, chiamata di App di `SetVisibility` metodo del `NotificationCompat.Builder` oggetto, passando l'impostazione di visibilità. Ad esempio, questa chiamata a `SetVisibility` rende la notifica `Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

Quando un `Private` viene inviata notifica, viene visualizzato solo il nome e l'icona dell'app nella schermata di blocco protetta. Anziché il messaggio di notifica, l'utente vede "Sblocco del dispositivo per visualizzare la notifica":

![Sblocca il messaggio di notifica di dispositivo](local-notifications-images/25-lockscreen-private.png)

In questo esempio **NotificationsLab** è il nome dell'app per l'origine. Questa versione con alcune modifiche della notifica viene visualizzata solo quando la schermata di blocco è sicura (ad esempio, protetto tramite PIN, modello o password) &ndash; se la schermata di blocco non è protetta, l'intero contenuto della notifica è disponibile nella schermata di blocco.


### <a name="category-settings"></a>Impostazioni di categoria

A partire da Android 5.0, le categorie predefinite sono disponibili per la classificazione e filtrare le notifiche. Xamarin. Android fornisce le enumerazioni seguenti per queste categorie:

-   `Notification.CategoryCall` &ndash; Telefonata in arrivo.

-   `Notification.CategoryMessage` &ndash; Testo del messaggio in ingresso.

-   `Notification.CategoryAlarm` &ndash; Un allarme condizione o un timer di scadenza.

-   `Notification.CategoryEmail` &ndash; Messaggio di posta elettronica in arrivo.

-   `Notification.CategoryEvent` &ndash; Un evento del calendario.

-   `Notification.CategoryPromo` &ndash; Un messaggio promozionale o un annuncio.

-   `Notification.CategoryProgress` &ndash; Lo stato di avanzamento di un'operazione in background.

-   `Notification.CategorySocial` &ndash; Aggiornamento di rete basati su social network.

-   `Notification.CategoryError` &ndash; Errore di un processo di autenticazione o di operazione in background.

-   `Notification.CategoryTransport` &ndash; Aggiornamento di riproduzione multimediale.

-   `Notification.CategorySystem` &ndash; Riservato per utilizzi di sistema (stato sistema o dispositivo).

-   `Notification.CategoryService` &ndash; Indica che un servizio in background è in esecuzione.

-   `Notification.CategoryRecommendation` &ndash; Un messaggio correlato all'app attualmente in esecuzione.

-   `Notification.CategoryStatus` &ndash; Informazioni sul dispositivo.

Quando le notifiche vengono ordinate, la priorità della notifica ha la priorità l'impostazione di categoria. Ad esempio, verrà visualizzata una notifica ad alta priorità come l'Heads-up anche se appartiene al `Promo` categoria. Per impostare la categoria di una notifica, si chiama il `SetCategory` metodo di `NotificationCompat.Builder` oggetto, passando l'impostazione di categoria. Ad esempio:

```csharp
builder.SetCategory (Notification.CategoryCall);
```

Il *non disturbare* funzionalità (novità in Android 5.0) filtrare le notifiche in base alle categorie. Ad esempio, il *non disturbare* nella schermata **impostazioni** consente all'utente esente notifiche per le chiamate telefoniche e messaggi:

![Non disturbare commutatori dello schermo](local-notifications-images/26-do-not-disturb.png)

Quando l'utente configura *non disturbare* per bloccare tutti gli interrupt, ad eccezione di telefonate (come illustrato nello screenshot precedente), Android consente le notifiche con un'impostazione della categoria `Notification.CategoryCall` presentati mentre il dispositivo le novità *non disturbare* modalità. Si noti che `Notification.CategoryAlarm` mai bloccate in notifiche *non disturbare* modalità.

Il [LocalNotifications](https://developer.xamarin.com/samples/monodroid/LocalNotifications) esempio di seguito viene illustrato come utilizzare `NotificationCompat.Builder` per avviare una seconda attività da una notifica. Questo codice di esempio viene illustrato nel [tramite le notifiche locali in xamarin. Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md) procedura dettagliata.


### <a name="notification-styles"></a>Stili di notifica

Per creare *testo grande*, *immagine*, o *posta in arrivo* stile notifiche con `NotificationCompat.Builder`, l'app deve usare le versioni di compatibilità di questi stili. Ad esempio, per usare la *testo grande* applicare uno stile, creare un'istanza `NotificationCompat.BigTextstyle`:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

Analogamente, l'app può usare `NotificationCompat.InboxStyle` e `NotificationCompat.BigPictureStyle` per *posta in arrivo* e *immagine* stili, rispettivamente.


### <a name="notification-priority-and-category"></a>Categoria e la priorità di notifica

`NotificationCompat.Builder` supporta il `SetPriority` metodo (disponibile a partire Android 4.1). Tuttavia, il `SetCategory` metodo è *non* supportati da `NotificationCompat.Builder` poiché categorie fanno parte del nuovo sistema di metadati notifica che è stato introdotto in Android 5.0.

Per supportare le versioni precedenti di Android, dove `SetCategory` è non disponibile, il codice può controllare il livello di API in fase di esecuzione da chiamare in modo condizionale `SetCategory` quando il livello API è uguale o maggiore di Android 5.0 (livello API 21):

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

In di questo esempio, l'app **Framework di destinazione** è impostata su Android 5.0 e il **versione minima di Android** è impostata su **Android 4.1 (livello API 16)** . Poiché `SetCategory` è disponibile nel livello API 21 e versioni successiva, questo codice di esempio chiama `SetCategory` solo quando è disponibile &ndash; non lo chiamerà `SetCategory` quando il livello di API è inferiore a 21.


### <a name="lock-screen-visibility"></a>Visibilità del blocco dello schermo

Poiché Android non supporta le notifiche schermata di blocco prima di Android 5.0 (livello API 21), `NotificationCompat.Builder` non supporta il `SetVisibility` (metodo). Come spiegato in precedenza per `SetCategory`, il codice può verificare il livello di API di runtime e chiamare `SetVisiblity` solo quando è disponibile:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetVisibility (Notification.Public);
}
```


## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare le notifiche locali in Android. Descritto l'anatomia di una notifica, spiegato come usare `NotificationCompat.Builder` per creare le notifiche, le notifiche di stile nell'icona di grandi dimensioni, come *testo grande*, *immagine* e *della posta in arrivo*  formati, come impostare le impostazioni dei metadati, ad esempio la priorità, visibilità e la categoria di notifica e come avviare un'attività da una notifica. Questo articolo anche descritto il funzionano di queste impostazioni di notifica con il nuovo Heads-up, schermata di blocco, e *non disturbare* caratteristiche introdotte in Android 5.0. Infine, si è appreso come usare `NotificationCompat.Builder` per mantenere la compatibilità di notifica con le versioni precedenti di Android.

Per linee guida sulle notifiche di progettazione per Android, vedere [notifiche](https://developer.android.com/guide/topics/ui/notifiers/notifications.html).


## <a name="related-links"></a>Collegamenti correlati

- [NotificationsLab (sample)](https://developer.xamarin.com/samples/monodroid/android5.0/NotificationsLab/)
- [LocalNotifications (esempio)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Notifiche locali nella procedura dettagliata di Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [Avvisare l'utente](https://developer.android.com/training/notify-user/index.html)
- [Notifica](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
