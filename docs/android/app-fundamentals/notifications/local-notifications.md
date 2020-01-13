---
title: Notifiche locali in Android
description: Questa sezione illustra come implementare le notifiche locali in Xamarin.Android. Vengono illustrati i vari elementi dell'interfaccia utente di una notifica Android e viene illustrata la creazione e la visualizzazione di una notifica da parte dell'API.
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/16/2018
ms.openlocfilehash: 617c04e2f40af535fb381362a389524d693fad0b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025645"
---
# <a name="local-notifications-on-android"></a>Notifiche locali in Android

_Questa sezione illustra come implementare le notifiche locali in Xamarin.Android. Vengono illustrati i vari elementi dell'interfaccia utente di una notifica Android e viene illustrata la creazione e la visualizzazione di una notifica da parte dell'API._

## <a name="local-notifications-overview"></a>Panoramica delle notifiche locali

Android offre due aree controllate dal sistema per la visualizzazione delle icone di notifica e delle informazioni di notifica all'utente. Quando una notifica viene pubblicata per la prima volta, la relativa icona viene visualizzata nell' *area di notifica*, come illustrato nello screenshot seguente:

![Area di notifica di esempio in un dispositivo](local-notifications-images/01-notification-shade.png)

Per ottenere informazioni dettagliate sulla notifica, l'utente può aprire il cassetto delle notifiche (che espande ogni icona di notifica per rivelare il contenuto della notifica) ed eseguire le azioni associate alle notifiche. Lo screenshot seguente mostra un *cassetto delle notifiche* che corrisponde all'area di notifica visualizzata in precedenza:

[![cassetto di notifica di esempio che visualizza tre notifiche](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Le notifiche Android usano due tipi di layout:

- Il ***layout di Base*** &ndash; un formato di presentazione compatto e fisso.

- ***Layout espanso*** &ndash; un formato di presentazione che può espandersi a una dimensione maggiore per visualizzare altre informazioni.

Ognuno di questi tipi di layout (e come crearli) viene descritto nelle sezioni seguenti.

> [!NOTE]
> Questa guida è incentrata sulle [API NotificationCompat](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) della [libreria del supporto Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Queste API garantiscono la massima compatibilità con le versioni precedenti di Android 4,0 (livello API 14).

### <a name="base-layout"></a>Layout di base

Tutte le notifiche Android sono basate sul formato di layout di base, che include almeno gli elementi seguenti:

1. *Icona di notifica*, che rappresenta l'app di origine o il tipo di notifica se l'app supporta tipi diversi di notifiche.

2. Il *titolo*della notifica o il nome del mittente se la notifica è un messaggio personale.

3. Messaggio di notifica.

4. *Timestamp*.

Questi elementi vengono visualizzati come illustrato nel diagramma seguente:

[![posizione degli elementi di notifica](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

I layout di base sono limitati a 64 pixel indipendenti dalla densità (DP) in altezza. Per impostazione predefinita, Android crea questo stile di notifica di base.

Facoltativamente, le notifiche possono visualizzare un'icona grande che rappresenta l'applicazione o la foto del mittente. Quando si usa un'icona di grandi dimensioni in una notifica in Android 5,0 e versioni successive, l'icona di notifica piccola viene visualizzata come un badge sull'icona grande:

![Foto della notifica semplice](local-notifications-images/04-simple-notification-photo.png)

A partire da Android 5,0, le notifiche possono anche essere visualizzate nella schermata di blocco:

[notifica della schermata di blocco di esempio![](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

L'utente può fare doppio toccare la notifica della schermata di blocco per sbloccare il dispositivo e passare all'app che ha originato tale notifica oppure scorrere per ignorare la notifica. Le app possono impostare il livello di visibilità di una notifica per controllare ciò che viene visualizzato nella schermata di blocco e gli utenti possono scegliere se consentire la visualizzazione di contenuto sensibile nelle notifiche della schermata di blocco.

Android 5,0 ha introdotto un formato di presentazione delle notifiche con priorità alta chiamato *Heads-up*. Le notifiche Heads-up scorrono verso il basso dalla parte superiore della schermata per alcuni secondi e quindi ritirate fino all'area di notifica:

[![la notifica Heads-up di esempio](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

Le notifiche Head-up consentono all'interfaccia utente del sistema di inserire informazioni importanti davanti all'utente senza compromettere lo stato dell'attività attualmente in esecuzione.

Android include il supporto per i metadati delle notifiche, in modo che le notifiche possano essere ordinate e visualizzate in modo intelligente. I metadati delle notifiche controllano anche il modo in cui vengono presentate le notifiche nella schermata di blocco e in formato Heads-up. Le applicazioni possono impostare i seguenti tipi di metadati di notifica:

- **Priorità** &ndash; il livello di priorità determina come e quando vengono presentate le notifiche. Ad esempio, in Android 5,0, le notifiche ad alta priorità vengono visualizzate come notifiche Heads-up.

- **Visibility** &ndash; specifica la quantità di contenuto di notifica da visualizzare quando la notifica viene visualizzata nella schermata di blocco.

- **Category** &ndash; informa il sistema come gestire la notifica in diverse circostanze, ad esempio quando il dispositivo è in modalità *non disturbare* .

> [!NOTE]
> La **visibilità** e la **categoria** sono state introdotte in Android 5,0 e non sono disponibili nelle versioni precedenti di Android. A partire da Android 8,0, i [canali di notifica](#notif-chan) vengono usati per controllare il modo in cui le notifiche vengono presentate all'utente.

### <a name="expanded-layouts"></a>Layout espansi

A partire da Android 4,1, le notifiche possono essere configurate con stili di layout espansi che consentono all'utente di espandere l'altezza della notifica per visualizzare altri contenuti. Ad esempio, nell'esempio seguente viene illustrata una notifica di layout espansa in modalità contratta:

![Notifica contratta](local-notifications-images/07-contracted-notification.png)

Quando questa notifica viene espansa, viene rivelato l'intero messaggio:

![Notifiche espanse](local-notifications-images/08-expanded-notification.png)

Android supporta tre stili di layout espansi per le notifiche a evento singolo:

- ***Big Text*** &ndash; in modalità a contratto, Visualizza un estratto della prima riga del messaggio seguito da due punti. In modalità espansa, Visualizza l'intero messaggio (come illustrato nell'esempio precedente).

- &ndash; della ***posta in arrivo*** in modalità a contratto, Visualizza il numero di nuovi messaggi. In modalità espansa Visualizza il primo messaggio di posta elettronica o un elenco dei messaggi presenti nella posta in arrivo.

- ***Image*** &ndash; in modalità contratta, Visualizza solo il testo del messaggio. In modalità espansa Visualizza il testo e un'immagine.

[Oltre alla notifica di base](#beyond-the-basic-notification) , più avanti in questo articolo, spiega come creare notifiche di testo, *posta in arrivo*e *Immagini* di *grandi dimensioni*.

<a name="notif-chan"></a>
<a name="notification-channels"></a>
## <a name="notification-channels"></a>Canali di notifica

A partire da Android 8,0 (Oreo), è possibile usare la funzionalità *canali di notifica* per creare un canale personalizzabile dall'utente per ogni tipo di notifica che si vuole visualizzare. I canali di notifica consentono di raggruppare le notifiche in modo che tutte le notifiche inviate a un canale mostrino lo stesso comportamento. Ad esempio, si potrebbe disporre di un canale di notifica destinato a notifiche che richiedono attenzione immediata e un canale "più tranquillo" separato usato per i messaggi informativi.

L'app **YouTube** installata con Android Oreo elenca due categorie di notifiche: **notifiche di download** e **notifiche generali**:

[schermate di notifica![per YouTube in Android Oreo](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

Ognuna di queste categorie corrisponde a un canale di notifica. L'app YouTube implementa un canale di **notifiche di download** e un canale di **notifiche generale** . L'utente può toccare **Scarica notifiche**, che visualizza la schermata impostazioni per il canale delle notifiche di download dell'app:

[schermata di download delle notifiche di![per l'app YouTube](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

In questa schermata, l'utente può modificare il comportamento del canale notifiche di **download** eseguendo le operazioni seguenti:

- Impostare il livello di importanza su **urgente**, **High**, **medium**o **low**, che configura il livello di interrupt audio e visivo.

- Attivare o disattivare il punto di notifica.

- Attivare o disattivare la luce lampeggiante.

- Mostrare o nascondere le notifiche nella schermata di blocco.

- Eseguire l'override dell'impostazione non **disturbare** .

Il canale **notifiche generale** presenta impostazioni simili:

[schermata![notifiche generali per l'app YouTube](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

Si noti che non si ha il controllo assoluto sulla modalità di interazione dei canali di notifica con l'utente &ndash; l'utente può modificare le impostazioni per qualsiasi canale di notifica sul dispositivo come illustrato nelle schermate precedenti. Tuttavia, è possibile configurare i valori predefiniti (come verrà descritto di seguito). Come illustrato in questi esempi, la nuova funzionalità canali di notifica consente di fornire agli utenti un controllo con granularità fine su diversi tipi di notifiche.

## <a name="notification-creation"></a>Creazione di notifiche

Per creare una notifica in Android, usare la classe [NotificationCompat. Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder) dal pacchetto NuGet [Xamarin.Android. support. v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) . Questa classe rende possibile la creazione e la pubblicazione di notifiche nelle versioni precedenti di Android.
viene inoltre illustrata la `NotificationCompat.Builder`.

`NotificationCompat.Builder` fornisce metodi per l'impostazione delle varie opzioni in una notifica, ad esempio:

- Il contenuto, inclusi il titolo, il testo del messaggio e l'icona di notifica.

- Stile della notifica, ad esempio *Big text*, *Inbox*o *Image* Style.

- Priorità della notifica: Minimum, low, default, High o Maximum. In Android 8,0 e versioni successive, la priorità viene impostata tramite un [_canale di notifica_](#notification-channels).

- Visibilità della notifica sulla schermata di blocco: Public, private o Secret.

- Metadati di categoria che consentono a Android di classificare e filtrare la notifica.

- Finalità facoltativa che indica un'attività da avviare quando viene toccata la notifica.

- ID del canale di notifica in cui verrà pubblicata la notifica (Android 8,0 e versioni successive).

Dopo aver impostato queste opzioni nel generatore, viene generato un oggetto notifica contenente le impostazioni. Per pubblicare la notifica, passare questo oggetto notifica a *Notification Manager*. Android fornisce la classe [NotificationManager](xref:Android.App.NotificationManager) , che è responsabile della pubblicazione delle notifiche e della relativa visualizzazione all'utente. Un riferimento a questa classe può essere ottenuto da qualsiasi contesto, ad esempio un'attività o un servizio.

### <a name="creating-a-notification-channel"></a>Creazione di un canale di notifica

Le app in esecuzione in Android 8,0 devono creare un canale di notifica per le notifiche. Un canale di notifica richiede le seguenti tre informazioni:

- Stringa ID univoca per il pacchetto che identificherà il canale.
- Nome del canale che verrà visualizzato all'utente.  Il nome deve avere una lunghezza compresa tra 1 e 40 caratteri.
- Importanza del canale.

Le app dovranno verificare la versione di Android in esecuzione.
I dispositivi che eseguono versioni precedenti a Android 8,0 non devono creare un canale di notifica. Il metodo seguente è un esempio di come creare un canale di notifica in un'attività:

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

Il canale di notifica deve essere creato ogni volta che viene creata l'attività. Per il metodo `CreateNotificationChannel`, deve essere chiamato nel metodo `OnCreate` di un'attività.

### <a name="creating-and-publishing-a-notification"></a>Creazione e pubblicazione di una notifica

Per generare una notifica in Android, seguire questa procedura:

1. Creare un'istanza di un oggetto `NotificationCompat.Builder`.

2. Chiamare diversi metodi sull'oggetto `NotificationCompat.Builder` per impostare le opzioni di notifica.

3. Chiamare il metodo di [compilazione](xref:Android.App.Notification.Builder.Build) dell'oggetto `NotificationCompat.Builder` per creare un'istanza di un oggetto notifica.

4. Chiamare il metodo [Notify](xref:Android.App.NotificationManager.Notify*) di Notification Manager per pubblicare la notifica.

È necessario specificare almeno le informazioni seguenti per ogni notifica:

- Icona di piccole dimensioni (24x24 DP)

- Titolo breve

- Testo della notifica

Nell'esempio di codice seguente viene illustrato come utilizzare `NotificationCompat.Builder` per generare una notifica di base. Si noti che `NotificationCompat.Builder` metodi supportano il [concatenamento](https://en.wikipedia.org/wiki/Method_chaining)di metodi; ovvero, ogni metodo restituisce l'oggetto generatore per poter utilizzare il risultato dell'ultima chiamata al metodo per richiamare la chiamata al metodo successiva:

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

In questo esempio viene creata un'istanza di un nuovo oggetto `NotificationCompat.Builder` denominato `builder`, insieme all'ID del canale di notifica da usare. Il titolo e il testo della notifica vengono impostati e l'icona di notifica viene caricata da **Resources/ic_notification. png**. La chiamata al metodo `Build` del generatore di notifiche crea un oggetto notifica con queste impostazioni. Il passaggio successivo consiste nel chiamare il metodo `Notify` di Notification Manager. Per individuare gestione notifiche, chiamare `GetSystemService`, come illustrato in precedenza.

Il metodo `Notify` accetta due parametri: l'identificatore della notifica e l'oggetto notifica. L'identificatore di notifica è un intero univoco che identifica la notifica per l'applicazione. In questo esempio l'identificatore di notifica è impostato su zero (0); Tuttavia, in un'applicazione di produzione, è consigliabile assegnare a ogni notifica un identificatore univoco. Il riutilizzo del valore dell'identificatore precedente in una chiamata a `Notify` comporta la sovrascrittura dell'ultima notifica.

Quando questo codice viene eseguito in un dispositivo Android 5,0, viene generata una notifica simile all'esempio seguente:

![Risultato della notifica per il codice di esempio](local-notifications-images/09-hello-world.png)

L'icona di notifica viene visualizzata sul lato sinistro della notifica &ndash; l'immagine di un cerchio &ldquo;i&rdquo; ha un canale alfa, in modo che Android possa creare uno sfondo circolare grigio dietro di esso. È anche possibile specificare un'icona senza un canale alfa. Per visualizzare un'immagine fotografica come icona, vedere il [formato di icone grandi](#large-icon-format) più avanti in questo argomento.

Il timestamp viene impostato automaticamente, ma è possibile eseguire l'override di questa impostazione chiamando il metodo [sewhen](xref:Android.App.Notification.Builder.SetWhen*) del generatore di notifiche. Ad esempio, l'esempio di codice seguente imposta il timestamp sull'ora corrente:

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>Abilitazione di suoni e vibrazioni

Se si vuole che la notifica riproduca anche un suono, è possibile chiamare il metodo [Sedefaults](xref:Android.App.Notification.Builder.SetDefaults*) del generatore di notifiche e passare il flag di `NotificationDefaults.Sound`:

```csharp
// Instantiate the notification builder and enable sound:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

Questa chiamata a `SetDefaults` provocherà la riproduzione di un suono da parte del dispositivo quando viene pubblicata la notifica. Se si vuole che il dispositivo vibra anziché riprodurre un suono, è possibile passare `NotificationDefaults.Vibrate` a `SetDefaults.` se si vuole che il dispositivo riproduca un suono e vibrazione il dispositivo, è possibile passare entrambi i flag al `SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

Se si Abilita il suono senza specificare un suono, Android usa il suono di notifica di sistema predefinito. Tuttavia, è possibile modificare il suono che verrà riprodotto chiamando il metodo [sesound](xref:Android.App.Notification.Builder.SetSound*) del generatore di notifiche. Ad esempio, per riprodurre il suono di allarme con la notifica, anziché il suono di notifica predefinito, è possibile ottenere l'URI per il segnale acustico da [RingtoneManager](xref:Android.Media.RingtoneManager) e passarlo a `SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

In alternativa, è possibile usare il suono predefinito del sistema ringtone per la notifica:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

Dopo aver creato un oggetto notifica, è possibile impostare le proprietà delle notifiche nell'oggetto notifica, anziché configurarle in anticipo tramite metodi di `NotificationCompat.Builder`. Ad esempio, invece di chiamare il metodo `SetDefaults` per abilitare la vibrazione di una notifica, è possibile modificare direttamente il flag di bit della proprietà [valori predefiniti](xref:Android.App.Notification.Defaults) della notifica:

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

Questo esempio causa la vibrazione del dispositivo quando la notifica viene pubblicata.

### <a name="updating-a-notification"></a>Aggiornamento di una notifica

Se si desidera aggiornare il contenuto di una notifica dopo che è stata pubblicata, è possibile riutilizzare l'oggetto `NotificationCompat.Builder` esistente per creare un nuovo oggetto notifica e pubblicare la notifica con l'identificatore dell'ultima notifica. Esempio:

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

In questo esempio, l'oggetto `NotificationCompat.Builder` esistente viene usato per creare un nuovo oggetto notifica con un titolo e un messaggio diversi.
Il nuovo oggetto notifica viene pubblicato usando l'identificatore della notifica precedente e aggiorna il contenuto della notifica pubblicata in precedenza:

![Notifica aggiornata](local-notifications-images/12-updated-notification.png)

Il corpo della notifica precedente viene riutilizzato &ndash; solo il titolo e il testo della notifica cambiano mentre la notifica viene visualizzata nel cassetto delle notifiche. Il testo del titolo passa da "notifica di esempio" a "notifica aggiornata" e il testo del messaggio cambia da "Hello World! Questa è la mia prima notifica! " a "modificato in questo messaggio".

Una notifica rimane visibile fino a quando non si verifica una delle tre situazioni seguenti:

- L'utente omette la notifica o tocca *Cancella tutto*.

- L'applicazione effettua una chiamata a `NotificationManager.Cancel`, passando l'ID di notifica univoco assegnato al momento della pubblicazione della notifica.

- L'applicazione chiama `NotificationManager.CancelAll`.

Per altre informazioni sull'aggiornamento delle notifiche Android, vedere [modificare una notifica](https://developer.android.com/training/notify-user/managing.html#Updating).

### <a name="starting-an-activity-from-a-notification"></a>Avvio di un'attività da una notifica

In Android è normale che una notifica venga associata a un' *azione* &ndash; un'attività che viene avviata quando l'utente tocca la notifica. Questa attività può trovarsi in un'altra applicazione o anche in un'altra attività. Per aggiungere un'azione a una notifica, creare un oggetto [PendingIntent](xref:Android.App.PendingIntent) e associare il `PendingIntent` alla notifica. Un `PendingIntent` è un tipo speciale di finalità che consente all'applicazione del destinatario di eseguire una parte di codice predefinita con le autorizzazioni dell'applicazione mittente. Quando l'utente tocca la notifica, Android avvia l'attività specificata dal `PendingIntent`.

Il frammento di codice seguente illustra come creare una notifica con un `PendingIntent` che avvierà l'attività dell'app di origine, `MainActivity`:

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

Questo codice è molto simile al codice di notifica nella sezione precedente, ad eccezione del fatto che un `PendingIntent` viene aggiunto all'oggetto notifica. In questo esempio, il `PendingIntent` è associato all'attività dell'app di origine prima che venga passato al metodo [SetContentIntent](xref:Android.App.Notification.Builder.SetContentIntent*) del generatore di notifiche. Il flag `PendingIntentFlags.OneShot` viene passato al metodo `PendingIntent.GetActivity` in modo che il `PendingIntent` venga usato una sola volta. Quando viene eseguito questo codice, viene visualizzata la seguente notifica:

![Prima notifica azione](local-notifications-images/10-first-action-notification.png)

Se si tocca questa notifica, l'utente torna all'attività di origine.

In un'app di produzione l'app deve gestire lo *stack indietro* quando l'utente preme il pulsante **indietro** nell'attività di notifica. se non si ha familiarità con le attività di Android e lo stack indietro, vedere [attività e stack back](https://developer.android.com/guide/components/tasks-and-back-stack.html).
Nella maggior parte dei casi, la navigazione all'indietro dall'attività di notifica deve restituire l'utente dall'app e tornare alla schermata iniziale. Per gestire lo stack indietro, l'app usa la classe [TaskStackBuilder](xref:Android.App.TaskStackBuilder) per creare un `PendingIntent` con uno stack indietro.

Un'altra considerazione reale è che l'attività di origine potrebbe dover inviare dati all'attività di notifica. Ad esempio, la notifica può indicare che è arrivato un messaggio di testo e l'attività di notifica (una schermata di visualizzazione dei messaggi) richiede l'ID del messaggio per visualizzare il messaggio all'utente. L'attività che crea la `PendingIntent` può utilizzare il metodo [Intent. PutExtra](xref:Android.Content.Intent.PutExtra*) per aggiungere dati, ad esempio una stringa, allo scopo, in modo che questi dati vengano passati all'attività di notifica.

Nell'esempio di codice seguente viene illustrato come utilizzare `TaskStackBuilder` per gestire lo stack indietro e viene incluso un esempio di invio di una singola stringa di messaggio a un'attività di notifica denominata `SecondActivity`:

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

In questo esempio di codice l'app è costituita da due attività: `MainActivity` (che contiene il codice di notifica precedente) e `SecondActivity`, la schermata visualizzata dall'utente dopo aver toccato la notifica. Quando viene eseguito questo codice, viene presentata una semplice notifica (simile all'esempio precedente). Se si tocca la notifica, l'utente viene visualizzato nella schermata `SecondActivity`:

![Schermata seconda attività](local-notifications-images/11-second-activity.png)

Il messaggio stringa (passato nel metodo di `PutExtra` della finalità) viene recuperato in `SecondActivity` tramite questa riga di codice:

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

Questo messaggio recuperato, "Greetings from MainActivity!", viene visualizzato nella schermata `SecondActivity`, come illustrato nello screenshot precedente. Quando l'utente preme il pulsante **indietro** mentre è `SecondActivity`, la navigazione esce dall'app e torna alla schermata precedente all'avvio dell'app.

Per ulteriori informazioni sulla creazione di Intent in sospeso, vedere [PendingIntent](xref:Android.App.PendingIntent).

<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>Oltre la notifica di base

Per impostazione predefinita, le notifiche sono un semplice formato di layout di base in Android, ma è possibile migliorare questo formato di base effettuando ulteriori chiamate al metodo `NotificationCompat.Builder`. In questa sezione verrà illustrato come aggiungere un'icona foto di grandi dimensioni alla notifica. verranno visualizzati esempi di come creare notifiche di layout espanse.

<a name="large-icon-format" />

### <a name="large-icon-format"></a>Formato icone grandi

Nelle notifiche Android viene in genere visualizzata l'icona dell'app di origine (sul lato sinistro della notifica). Tuttavia, le notifiche possono visualizzare un'immagine o una foto, ovvero un' *icona di grandi dimensioni*, anziché l'icona standard Small. Ad esempio, un'app di messaggistica potrebbe visualizzare una foto del mittente anziché l'icona dell'app.

Di seguito è riportato un esempio di una notifica di base di Android 5,0 &ndash; viene visualizzata solo l'icona dell'app piccola:

![Notifica normale di esempio](local-notifications-images/13-sample-notification.png)

Di seguito è riportata una schermata della notifica dopo averla modificata per visualizzare un'icona di grandi dimensioni &ndash; usa un'icona creata da un'immagine di una scimmia di codice Novell:

![Esempio di notifica icone grandi](local-notifications-images/14-large-icon-sample.png)

Si noti che quando una notifica viene presentata in un formato di icona grande, l'icona dell'app piccola viene visualizzata come un badge nell'angolo inferiore destro dell'icona grande.

Per usare un'immagine come icona di grandi dimensioni in una notifica, chiamare il metodo [SetLargeIcon](xref:Android.App.Notification.Builder.SetLargeIcon*) del generatore di notifiche e passare una bitmap dell'immagine. A differenza di `SetSmallIcon`, `SetLargeIcon` accetta solo una bitmap. Per convertire un file di immagine in una bitmap, usare la classe [BitmapFactory](xref:Android.Graphics.BitmapFactory) . Esempio:

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

Questo codice di esempio apre il file di immagine in **Resources/monkey_icon. png**, lo converte in una bitmap e passa la bitmap risultante a `NotificationCompat.Builder`. In genere, la risoluzione dell'immagine di origine è più grande dell'icona piccola &ndash; ma non molto più grande. Un'immagine troppo grande potrebbe causare operazioni di ridimensionamento non necessarie che potrebbero ritardare l'invio della notifica.

### <a name="big-text-style"></a>Stile testo grande

Lo stile di *testo di grandi dimensioni* è un modello di layout espanso utilizzato per la visualizzazione di messaggi lunghi nelle notifiche. Analogamente a tutte le notifiche di layout espanse, la notifica del testo di grandi dimensioni viene inizialmente visualizzata in un formato di presentazione compatto:

![Notifica di grande testo di esempio](local-notifications-images/15-big-text-notification.png)

In questo formato, viene visualizzato solo un estratto del messaggio, terminato da due punti. Quando l'utente trascina la notifica, si espande per visualizzare l'intero messaggio di notifica:

![Notifica espansa per testo grande](local-notifications-images/16-big-text-expanded.png)

Questo formato di layout espanso include anche il testo di riepilogo nella parte inferiore della notifica. L'altezza massima della notifica di *testo grande* è 256 DP.

Per creare una notifica di *testo di grandi dimensioni* , creare un'istanza di un oggetto `NotificationCompat.Builder`, come in precedenza, quindi creare un'istanza e aggiungere un oggetto [BigTextStyle](xref:Android.App.Notification.BigTextStyle) all'oggetto `NotificationCompat.Builder`. Ecco un esempio:

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

In questo esempio, il testo del messaggio e il testo di riepilogo vengono archiviati nell'oggetto `BigTextStyle` (`textStyle`) prima di essere passati a `NotificationCompat.Builder.`

### <a name="image-style"></a>Stile immagine

Lo stile dell' *immagine* (detto anche stile *immagine grande* ) è un formato di notifica espanso che è possibile usare per visualizzare un'immagine nel corpo di una notifica. Ad esempio, un'app screenshot o un'app Photo può usare lo stile di notifica *immagine* per fornire all'utente una notifica dell'ultima immagine acquisita. Si noti che l'altezza massima della notifica dell' *immagine* è 256 DP &ndash; Android ridimensiona l'immagine per adattarla a questa restrizione di altezza massima, entro i limiti della memoria disponibile.

Analogamente a tutte le notifiche di layout espanse, le notifiche di *Immagini* vengono visualizzate per prime in un formato compatto che visualizza un estratto del testo del messaggio di accompagnamento:

![La notifica delle immagini compatte non mostra alcuna immagine](local-notifications-images/17-image-compact.png)

Quando l'utente trascina la notifica dell' *immagine* , si espande per rivelare un'immagine. Ad esempio, di seguito è illustrata la versione espansa della notifica precedente:

![La notifica immagine espansa rivela un'immagine](local-notifications-images/18-image-expanded.png)

Si noti che quando la notifica viene visualizzata in formato Compact, viene visualizzato il testo della notifica (il testo passato al metodo `SetContentText` del generatore di notifiche, come illustrato in precedenza). Tuttavia, quando la notifica viene espansa per rivelare l'immagine, viene visualizzato il testo di riepilogo sopra l'immagine.

Per creare una notifica *immagine* , creare un'istanza di un oggetto `NotificationCompat.Builder` come in precedenza, quindi creare e inserire un oggetto [BigPictureStyle](xref:Android.App.Notification.BigPictureStyle) nell'oggetto `NotificationCompat.Builder`. Esempio:

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

Analogamente al metodo `SetLargeIcon` di `NotificationCompat.Builder`, il metodo [BigPicture](xref:Android.App.Notification.BigPictureStyle.BigPicture*) di `BigPictureStyle` richiede una bitmap dell'immagine che si desidera visualizzare nel corpo della notifica. In questo esempio, il metodo [DecodeResource](xref:Android.Graphics.BitmapFactory.DecodeResource*) di `BitmapFactory` legge il file di immagine che si trova in **Resources/x_bldg. png** e lo converte in una bitmap.

È anche possibile visualizzare immagini che non sono in pacchetto come risorsa. Ad esempio, il codice di esempio seguente carica un'immagine dalla scheda SD locale e la Visualizza in una notifica *immagine* :

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

In questo esempio, il file di immagine che si trova in **/sdcard/Pictures/My-tshirt.jpg** viene caricato, ridimensionato alla metà della dimensione originale e quindi convertito in una bitmap da usare nella notifica:

![Esempio di immagine della maglietta nella notifica](local-notifications-images/19-tshirt-notification.png)

Se non si conoscono in anticipo le dimensioni del file di immagine, è consigliabile eseguire il wrapping della chiamata a [BitmapFactory. DecodeFile ha](xref:Android.Graphics.BitmapFactory.DecodeFile*) in un gestore di eccezioni &ndash; potrebbe essere generata un'eccezione `OutOfMemoryError` se l'immagine è troppo grande per il ridimensionamento di Android.

Per altre informazioni sul caricamento e la decodifica di immagini bitmap di grandi dimensioni, vedere [caricare in modo efficiente le bitmap di grandi dimensioni](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently).

### <a name="inbox-style"></a>Stile posta in arrivo

Il formato della *posta in arrivo* è un modello di layout espanso progettato per la visualizzazione di righe di testo separate (ad esempio, un riepilogo della posta in arrivo della posta elettronica) nel corpo della notifica. La notifica del formato della *posta in arrivo* viene visualizzata per la prima volta in un formato compatto:

![Esempio di notifica della posta in arrivo compatta](local-notifications-images/20-inbox-compact.png)

Quando l'utente trascina la notifica, si espande per rivelare un riepilogo della posta elettronica, come illustrato nella schermata seguente:

![Notifica della posta in arrivo di esempio espansa](local-notifications-images/21-inbox-expanded.png)

Per creare una notifica della *posta in arrivo* , creare un'istanza di un oggetto `NotificationCompat.Builder`, come in precedenza, e aggiungere un oggetto [InboxStyle](xref:Android.App.Notification.InboxStyle) al `NotificationCompat.Builder`. Ecco un esempio:

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

Per aggiungere nuove righe di testo al corpo della notifica, chiamare il metodo [AddLine](xref:Android.App.Notification.InboxStyle.AddLine*) dell'oggetto `InboxStyle` (l'altezza massima della notifica della *posta in arrivo* è 256 DP). Si noti che, a differenza dello stile di *testo di grandi dimensioni* , lo stile della *posta in arrivo* supporta le singole righe di testo nel corpo della notifica.

È anche possibile usare lo stile della *posta in arrivo* per tutte le notifiche che devono visualizzare singole righe di testo in un formato espanso. Ad esempio, è possibile usare lo stile di notifica della *posta in arrivo* per combinare più notifiche in sospeso in una notifica di riepilogo &ndash; è possibile aggiornare una singola notifica dello stile della *posta in arrivo* con nuove righe di contenuto della notifica (vedere aggiornamento di [un Notifica](#updating-a-notification) precedente), anziché generare un flusso continuo di notifiche nuove, per lo più simili.

## <a name="configuring-metadata"></a>Configurazione dei metadati

`NotificationCompat.Builder` include metodi che è possibile chiamare per impostare i metadati relativi alla notifica, ad esempio priorità, visibilità e categoria. Android usa queste informazioni &mdash; insieme alle impostazioni delle preferenze utente &mdash; per determinare come e quando visualizzare le notifiche.

### <a name="priority-settings"></a>Impostazioni priorità

Le app in esecuzione in Android 7,1 e versioni precedenti devono impostare la priorità direttamente nella notifica. L'impostazione di priorità di una notifica determina due risultati quando viene pubblicata la notifica:

- Posizione in cui viene visualizzata la notifica rispetto ad altre notifiche.
    Ad esempio, le notifiche con priorità alta vengono presentate sopra le notifiche con priorità più bassa nel cassetto delle notifiche, indipendentemente dal momento in cui ogni notifica è stata pubblicata.

- Indica se la notifica viene visualizzata nel formato di notifica Heads-up (Android 5,0 e versioni successive). Solo le notifiche di priorità *alta* e *massima* vengono visualizzate come notifiche Heads-up.

Xamarin.Android definisce le enumerazioni seguenti per impostare la priorità di notifica:

- `NotificationPriority.Max` &ndash; avvisa l'utente di una condizione urgente o critica (ad esempio, una chiamata in ingresso, direzioni di turn-by-turn o un avviso di emergenza). Nei dispositivi Android 5,0 e versioni successive, le notifiche con priorità massima vengono visualizzate in formato Heads-up.

- `NotificationPriority.High` &ndash; informa l'utente di eventi importanti, ad esempio messaggi di posta elettronica importanti o l'arrivo di messaggi di chat in tempo reale. Nei dispositivi Android 5,0 e versioni successive, le notifiche con priorità alta vengono visualizzate in formato Heads-up.

- `NotificationPriority.Default` &ndash; notifica all'utente le condizioni che hanno un livello di priorità medio.

- `NotificationPriority.Low` &ndash; per informazioni non urgenti a cui l'utente deve essere informato, ad esempio promemoria degli aggiornamenti software o aggiornamenti di social network.

- `NotificationPriority.Min` &ndash; per informazioni di base visualizzate dall'utente solo quando si visualizzano le notifiche (ad esempio, posizione o informazioni meteo).

Per impostare la priorità di una notifica, chiamare il metodo [sepriority](xref:Android.App.Notification.Builder.SetPriority*) dell'oggetto `NotificationCompat.Builder`, passando il livello di priorità. Esempio:

```csharp
builder.SetPriority (NotificationPriority.High);
```

Nell'esempio seguente, la notifica con priorità alta, "un messaggio importante!" viene visualizzato nella parte superiore del cassetto delle notifiche:

![Esempio di notifica ad alta priorità](local-notifications-images/22-hi-priority-drawer.png)

Dal momento che si tratta di una notifica con priorità alta, viene visualizzata anche come notifica Heads-up sopra la schermata dell'attività corrente dell'utente in Android 5,0:

![Esempio di notifica Heads-up](local-notifications-images/23-heads-up-example.png)

Nell'esempio seguente, la notifica "pensiero per il giorno" con priorità bassa viene visualizzata con una notifica del livello di batteria con priorità più elevata:

![Esempio di notifica con priorità bassa](local-notifications-images/24-lo-priority-drawer.png)

Poiché il "pensiero per la giornata" notifica è una notifica con priorità bassa, Android non lo visualizzerà nel formato Heads-up.

> [!NOTE]
> In Android 8,0 e versioni successive, la priorità del canale di notifica e delle impostazioni utente determinerà la priorità della notifica.

### <a name="visibility-settings"></a>Impostazioni di visibilità

A partire da Android 5,0, è disponibile l'impostazione di *visibilità* che consente di controllare la quantità di contenuto delle notifiche visualizzata nella schermata di blocco protetto.
Xamarin.Android definisce le enumerazioni seguenti per l'impostazione della visibilità delle notifiche:

- `NotificationVisibility.Public` &ndash; il contenuto completo della notifica viene visualizzato nella schermata di blocco protetto.

- `NotificationVisibility.Private` &ndash; solo le informazioni essenziali vengono visualizzate nella schermata di blocco sicura, ad esempio l'icona di notifica e il nome dell'app che l'ha inviata, ma il resto dei dettagli della notifica è nascosto. Per impostazione predefinita, tutte le notifiche `NotificationVisibility.Private`.

- `NotificationVisibility.Secret` &ndash; non viene visualizzato nulla nella schermata di blocco sicura, neanche nell'icona di notifica. Il contenuto della notifica è disponibile solo dopo che l'utente ha sbloccato il dispositivo.

Per impostare la visibilità di una notifica, le app chiamano il metodo di `SetVisibility` dell'oggetto `NotificationCompat.Builder`, passando l'impostazione di visibilità. Ad esempio, questa chiamata a `SetVisibility` rende `Private`la notifica:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

Quando viene inviata una notifica di `Private`, nella schermata di blocco sicura vengono visualizzati solo il nome e l'icona dell'app. Anziché il messaggio di notifica, l'utente Visualizza "sbloccare il dispositivo per visualizzare la notifica":

![Sbloccare il messaggio di notifica del dispositivo](local-notifications-images/25-lockscreen-private.png)

In questo esempio, **NotificationsLab** è il nome dell'app di origine. Questa versione di notifica viene visualizzata solo quando la schermata di blocco è protetta (ad esempio, protetta tramite PIN, criterio o password) &ndash; se la schermata di blocco non è protetta, il contenuto completo della notifica è disponibile nella schermata di blocco.

### <a name="category-settings"></a>Impostazioni categoria

A partire da Android 5,0, le categorie predefinite sono disponibili per le notifiche di classificazione e filtro. Xamarin.Android fornisce le enumerazioni seguenti per queste categorie:

- `Notification.CategoryCall` &ndash; telefonata in arrivo.

- `Notification.CategoryMessage` &ndash; messaggio di testo in arrivo.

- `Notification.CategoryAlarm` &ndash; una condizione di allarme o una scadenza del timer.

- `Notification.CategoryEmail` &ndash; messaggio di posta elettronica in arrivo.

- `Notification.CategoryEvent` &ndash; un evento del calendario.

- `Notification.CategoryPromo` &ndash; un messaggio promozionale o un annuncio pubblicitario.

- `Notification.CategoryProgress` &ndash; lo stato di avanzamento di un'operazione in background.

- `Notification.CategorySocial` &ndash; aggiornamento di social networking.

- `Notification.CategoryError` &ndash; esito negativo di un'operazione in background o di un processo di autenticazione.

- `Notification.CategoryTransport` &ndash; aggiornamento della riproduzione multimediale.

- `Notification.CategorySystem` &ndash; riservata per l'utilizzo del sistema (stato del sistema o del dispositivo).

- `Notification.CategoryService` &ndash; indica che è in esecuzione un servizio in background.

- `Notification.CategoryRecommendation` &ndash; un messaggio di raccomandazione correlato all'app attualmente in esecuzione.

- `Notification.CategoryStatus` &ndash; informazioni sul dispositivo.

Quando le notifiche sono ordinate, la priorità della notifica ha la precedenza sull'impostazione della categoria. Ad esempio, verrà visualizzata una notifica con priorità alta, anche se appartiene alla categoria `Promo`. Per impostare la categoria di una notifica, chiamare il metodo `SetCategory` dell'oggetto `NotificationCompat.Builder`, passando l'impostazione Category. Esempio:

```csharp
builder.SetCategory (Notification.CategoryCall);
```

La funzionalità non *disturbare* (novità di Android 5,0) filtra le notifiche in base alle categorie. Ad esempio, la schermata *non disturbare* in **Impostazioni** consente all'utente di esentare le notifiche per le chiamate telefoniche e i messaggi:

![Non disturbare le opzioni dello schermo](local-notifications-images/26-do-not-disturb.png)

Quando l'utente configura non *disturbare* il blocco di tutti gli interrupt ad eccezione delle chiamate telefoniche (come illustrato nello screenshot precedente), Android consente di inviare notifiche con un'impostazione di categoria di `Notification.CategoryCall` da presentare mentre il dispositivo si trova in non *disturbare* modalità. Si noti che le notifiche `Notification.CategoryAlarm` non vengono mai bloccate in modalità *non di disturbo* .

Nell'esempio [LocalNotifications](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications) viene illustrato come utilizzare `NotificationCompat.Builder` per avviare una seconda attività da una notifica. Questo codice di esempio è illustrato nella procedura dettagliata [uso delle notifiche locali in Xamarin.Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md) .

### <a name="notification-styles"></a>Stili di notifica

Per creare notifiche di tipo *Big text*, *Image*o *Inbox* con `NotificationCompat.Builder`, l'app deve usare le versioni di compatibilità di questi stili. Ad esempio, per usare lo stile di *testo di grandi dimensioni* , creare un'istanza di `NotificationCompat.BigTextstyle`:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

Analogamente, l'app può usare `NotificationCompat.InboxStyle` e `NotificationCompat.BigPictureStyle` rispettivamente per la *posta in arrivo* e gli stili di *immagine* .

### <a name="notification-priority-and-category"></a>Priorità e categoria delle notifiche

`NotificationCompat.Builder` supporta il metodo `SetPriority` (disponibile a partire da Android 4,1). Tuttavia, il metodo `SetCategory` *non* è supportato da `NotificationCompat.Builder` perché le categorie fanno parte del nuovo sistema di metadati delle notifiche introdotto in Android 5,0.

Per supportare le versioni precedenti di Android, in cui `SetCategory` non è disponibile, il codice può controllare il livello API in fase di esecuzione per chiamare in modo condizionale `SetCategory` quando il livello API è uguale o maggiore di Android 5,0 (livello API 21):

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

In questo esempio, il Framework di **destinazione** dell'app è impostato su Android 5,0 e la **versione minima di Android** è impostata su **Android 4,1 (livello API 16)** . Poiché `SetCategory` è disponibile nel livello API 21 e versioni successive, questo codice di esempio chiamerà `SetCategory` solo se disponibile &ndash; non chiamerà `SetCategory` quando il livello API è inferiore a 21.

### <a name="lock-screen-visibility"></a>Visibilità della schermata di blocco

Poiché Android non supporta le notifiche della schermata di blocco prima di Android 5,0 (API level 21), `NotificationCompat.Builder` non supporta il metodo `SetVisibility`. Come spiegato in precedenza per `SetCategory`, il codice può controllare il livello API in fase di esecuzione e chiamare `SetVisiblity` solo quando è disponibile:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetVisibility (Notification.Public);
}
```

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare notifiche locali in Android. Descrive l'anatomia di una notifica, spiega come usare `NotificationCompat.Builder` per creare notifiche, come applicare uno stile alle notifiche in formati di icone grandi, *testo*, *Immagini* e *posta in arrivo* , come impostare le impostazioni dei metadati delle notifiche, ad esempio priorità, visibilità e categoria e come avviare un'attività da una notifica. In questo articolo è stata descritta anche la modalità di funzionamento di queste impostazioni di notifica con le nuove intestazioni, schermata di blocco e *non disturbare* le funzionalità introdotte in Android 5,0. Infine, si è appreso come usare `NotificationCompat.Builder` per mantenere la compatibilità delle notifiche con le versioni precedenti di Android.

Per le linee guida sulla progettazione di notifiche per Android, vedere [notifiche](https://developer.android.com/guide/topics/ui/notifiers/notifications.html).

## <a name="related-links"></a>Collegamenti correlati

- [NotificationsLab (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-notificationslab)
- [LocalNotifications (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [Procedure dettagliate per le notifiche locali in Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [Notifica all'utente](https://developer.android.com/training/notify-user/index.html)
- [Notifica](xref:Android.App.Notification)
- [NotificationManager](xref:Android.App.NotificationManager)
- [NotificationCompat. Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](xref:Android.App.PendingIntent)
