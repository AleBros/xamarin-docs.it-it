---
title: Notifiche locale
description: In questa sezione viene illustrato come implementare le notifiche locale in xamarin. Illustra i vari elementi dell'interfaccia utente di una notifica di Android e l'API del coinvolto nella creazione e visualizzazione di una notifica.
ms.topic: article
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 7566ebac0f487ef321c512c988c79f34e50777ac
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="local-notifications"></a>Notifiche locale

_In questa sezione viene illustrato come implementare le notifiche locale in xamarin. Illustra i vari elementi dell'interfaccia utente di una notifica di Android e l'API del coinvolto nella creazione e visualizzazione di una notifica._

## <a name="local-notifications-overview"></a>Panoramica delle notifiche locale

In questo argomento viene illustrato come implementare le notifiche locale in un'applicazione di xamarin. Vengono illustrate le varie parti di una notifica di Android, vengono illustrati gli stili di notifica diversi che sono disponibili per gli sviluppatori di app e introduce alcune delle API che consentono di creare e pubblicare le notifiche.

Android fornisce due aree e controllato dal sistema per visualizzare le icone di notifica e informazioni di notifica all'utente. Quando una notifica prima pubblicazione, la relativa icona viene visualizzata nel *area di notifica*, come illustrato nella schermata seguente:

![Area di notifica di esempio in un dispositivo](local-notifications-images/01-notification-shade.png)

Per ottenere informazioni dettagliate sulla notifica, l'utente può aprire il pannello di notifiche (che si espande ogni icona di notifica per visualizzare il contenuto di notifica) ed eseguire le azioni associate le notifiche. L'immagine seguente mostra un *Pannello di notifiche* che corrisponde all'area di notifica visualizzato di sopra:

[![Pannello di notifiche di esempio la visualizzazione di tre notifiche](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png)

Notifiche Android utilizzano due tipi di layout:

-   ***Layout di base*** &ndash; un formato di presentazione compact, predefinito.

-   ***Layout espanso*** &ndash; un formato di presentazione che può espandersi in una dimensione maggiore per visualizzare ulteriori informazioni.

Ognuno di questi tipi di layout (e come crearli) sono illustrati nelle sezioni seguenti.

<a name="base-layout" />

### <a name="base-layout"></a>Layout di base

Tutte le notifiche di Android sono basate sul formato di layout di base, che, come minimo, include gli elementi seguenti:

1.  Oggetto *icona di notifica*, che rappresenta l'applicazione di origine o il tipo di notifica se l'app supporta diversi tipi di notifiche.

2.  La notifica *titolo*, o il nome del mittente se la notifica è un messaggio personale.

3.  Il messaggio di notifica.

4.  Oggetto *timestamp*.

Questi elementi vengono visualizzati come illustrato nel diagramma seguente:

[![Posizione degli elementi di notifica](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png)

Layout di base sono limitate a 64 indipendente dalla densità di pixel (dp) in altezza. Per impostazione predefinita, Android crea questo stile di notifica di base.

Facoltativamente, le notifiche possono visualizzare un'icona di grandi dimensioni che rappresenta l'applicazione o una foto del mittente. Quando viene utilizzata un'icona di grandi dimensioni in una notifica in Android 5.0 e versioni successive, l'icona di notifica di piccole dimensioni viene visualizzata come un badge sull'icona di grandi dimensioni:

![Foto di notifica semplice](local-notifications-images/04-simple-notification-photo.png)

A partire da Android 5.0, le notifiche possono essere inclusi anche nel lockscreen:

[![Notifica lockscreen di esempio](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png)

L'utente può toccare due volte la notifica lockscreen sbloccare il dispositivo e passare all'app che ha avuto origine la notifica, oppure scorrere rapidamente per chiudere la notifica. Le applicazioni possono impostare il livello di visibilità di una notifica per controllare cosa viene visualizzata sul lockscreen e gli utenti possono scegliere se consentire contenuto riservato da visualizzare nelle notifiche lockscreen.

Android 5.0 è stato introdotto un formato di presentazione di notifica ad alta priorità denominato *Heads-up*. Le notifiche Heads-up scorrere verso il basso dalla parte superiore dello schermo per pochi secondi e quindi riesame backup area di notifica:

[![Notifica heads-up di esempio](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png)

Le notifiche Heads-up rendono possibile per il sistema dell'interfaccia utente di inserire informazioni importanti davanti all'utente senza interrompere lo stato dell'attività attualmente in esecuzione.

Android include il supporto per i metadati di notifica in modo che le notifiche possono essere ordinate e visualizzate in modo intelligente. I metadati di notifica controlla anche come le notifiche vengono presentate i lockscreen e in formato Heads-up. Le applicazioni possono impostare i seguenti tipi di metadati di notifica:

-   **Priorità** &ndash; il livello di priorità determina come e quando vengono visualizzate. Ad esempio, In Android 5.0, le notifiche con priorità alta vengono visualizzate come notifiche Heads-up.

-   **Visibilità** &ndash; specifica la quantità di contenuto notifica è visualizzata quando la notifica viene visualizzata sul lockscreen.

-   **Categoria** &ndash; informa il sistema come gestire la notifica in diverse circostanze, ad esempio quando il dispositivo viene *non disturbi* modalità.

**Nota:** **visibilità** e **categoria** introdotti in Android 5.0 e non sono disponibili nelle versioni precedenti di Android. A partire da Android 8.0, [canali di notifica](#notif-chan) consentono di controllare come le notifiche vengono presentate all'utente.

<a name="expanded-layouts" />

### <a name="expanded-layouts"></a>Layout espanso

A partire da Android 4.1, notifiche possono essere configurate con gli stili di layout espansa che consentono all'utente di espandere l'altezza della notifica per visualizzare una maggiore quantità di contenuto. Ad esempio, l'esempio seguente illustra una notifica di layout espanso in modalità contratto:

![Contratto di notifica](local-notifications-images/07-contracted-notification.png)

Quando questa notifica viene espanso, viene visualizzato l'intero messaggio:

![Notifica espansa](local-notifications-images/08-expanded-notification.png)

Android supporta tre stili di layout espanso per le notifiche degli eventi-singolo:

-   ***Testo grande*** &ndash; In modalità di contratto, consente di visualizzare un estratto della prima riga del messaggio seguita da due punti. In modalità espansa, Visualizza l'intero messaggio (come illustrato nell'esempio precedente).

-   ***Posta in arrivo*** &ndash; In modalità di contratto, Visualizza il numero di nuovi messaggi. In modalità espansa, Visualizza il primo messaggio di posta elettronica o un elenco dei messaggi in arrivo.

-   ***Immagine*** &ndash; In modalità di contratto, viene visualizzato solo il testo del messaggio. In modalità espansa, Visualizza il testo e un'immagine.

[Oltre la notifica di base](#beyond-the-basic-notification) (più avanti in questo articolo) viene illustrato come creare *testo Big*, *posta in arrivo*, e *immagine* notifiche.

<a name="notification-creation" />

## <a name="notification-creation"></a>Creazione di notifica

Per creare una notifica in Android, si utilizza il [Notification.Builder](https://developer.xamarin.com/api/type/Android.App.Notification+Builder/) classe. `Notification.Builder` è stata introdotta in Android 3.0 per semplificare la creazione di oggetti di notifica. Per creare le notifiche che sono compatibili con le versioni precedenti di Android, è possibile utilizzare il [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html) classe anziché `Notification.Builder` (vedere [compatibilità](#compatibility) più avanti in questo argomento per ulteriori informazioni sull'utilizzo `NotificationCompat.Builder`).
`Notification.Builder` fornisce metodi per l'impostazione di varie opzioni in una notifica, ad esempio:

-   Il contenuto, incluso il titolo, il testo del messaggio e l'icona di notifica.

-   Lo stile della notifica, ad esempio *testo Big*, *posta in arrivo*, o *immagine* stile.

-   La priorità della notifica: minimo, bassa, predefinita elevata o massimo.

-   La visibilità della notifica sul lockscreen: public, private o segreto.

-   I metadati di categoria che consente di Android classificare e filtrare la notifica.

-   Un intento facoltativo che indica un'attività da avviare quando si tocca la notifica.

Dopo aver impostato le opzioni nel generatore, si genera un oggetto di notifica che contiene le impostazioni. Per pubblicare la notifica, si passa l'oggetto di notifica per il *Notification Manager*. Android fornisce il [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/) (classe), che è responsabile per la pubblicazione di notifiche e visualizzarli all'utente. Un riferimento a questa classe può essere ottenuto da qualsiasi contesto, ad esempio un'attività o un servizio.

<a name="how-to-generate" />

### <a name="how-to-generate-a-notification"></a>Come generare una notifica

Per generare una notifica in Android, seguire questi passaggi:

1.  Creare un'istanza di un `Notification.Builder` oggetto.

2.  Chiamare i metodi diversi di `Notification.Builder` per impostare le opzioni di notifica.

3.  Chiamare il [compilare](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.Build/) metodo il `Notification.Builder` oggetto per creare un'istanza di un oggetto di notifica.

4.  Chiamare il [notifica](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/(System.Int32%2cAndroid.App.Notification)) metodo di notification manager per la notifica di pubblicazione.

È necessario fornire almeno le seguenti informazioni per ogni notifica:

-   Una piccola icona (24 x 24 dp dimensioni)

-   Un titolo breve

-   Il testo della notifica

Esempio di codice seguente viene illustrato come utilizzare `Notification.Builder` per generare una notifica di base. Si noti che `Notification.Builder` metodi supportano [concatenamento del metodo](http://en.wikipedia.org/wiki/Method_chaining); ovvero, ogni metodo restituisce l'oggetto generatore in modo è possibile utilizzare il risultato dell'ultima chiamata di metodo per richiamare la successiva chiamata al metodo:

```csharp
// Instantiate the builder and set notification elements:
Notification.Builder builder = new Notification.Builder (this)
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

In questo esempio, un nuovo `Notification.Builder` oggetto denominato `builder` viene creata un'istanza, il titolo e testo della notifica vengono impostate e l'icona di notifica viene caricato da **Resources/drawable/ic_notification.png**. La chiamata per il generatore di notifica `Build` metodo crea un oggetto di notifica con queste impostazioni. Il passaggio successivo consiste nel chiamare il `Notify` metodo del gestore di notifica. Per individuare la gestione delle notifiche, si chiama `GetSystemService`, come illustrato in precedenza.

Il `Notify` metodo accetta due parametri: l'identificatore di notifica e l'oggetto di notifica. L'identificatore di notifica è un tipo integer univoco che identifica la notifica all'applicazione. In questo esempio, l'identificatore di notifica è impostata su zero (0). Tuttavia, in un'applicazione di produzione, è consigliabile fornire ogni notifica un identificatore univoco. Riutilizza il precedente valore identificatore in una chiamata a `Notify` fa sì che l'ultima notifica devono essere sovrascritti.

Quando questo codice viene eseguito in un dispositivo Android 5.0, genera una notifica che è simile alla seguente:

![Risultato di notifica per il codice di esempio](local-notifications-images/09-hello-world.png)

L'icona di notifica viene visualizzata sul lato sinistro della notifica &ndash; questa immagine di un cerchiati &ldquo;si&rdquo; ha un canale alfa, in modo da Android può disegnare uno sfondo grigio circolare sottostante. È inoltre possibile fornire un'icona senza un canale alfa. Per visualizzare un'immagine fotografica come un'icona, vedere [grande formato icona](#large-icon-format) più avanti in questo argomento.

Il timestamp viene impostato automaticamente, ma è possibile ignorare questa impostazione tramite la chiamata di [SetWhen](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetWhen/) metodo del generatore di notifica. Ad esempio, l'esempio di codice seguente imposta il timestamp per l'ora corrente:

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```
<a name="sound-and-vibr" />

### <a name="enabling-sound-and-vibration"></a>Resistenza alle vibrazioni e abilitazione di suoni

Se si desidera la notifica per anche riprodurre un suono, è possibile chiamare il generatore di notifica [SetDefaults](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetDefaults/) (metodo) e passare il `NotificationDefaults.Sound` flag:

```csharp
// Instantiate the notification builder and enable sound:
Notification.Builder builder = new Notification.Builder (this)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

Questa chiamata a `SetDefaults` determinerà il dispositivo per riprodurre un suono quando viene pubblicata la notifica. Se si vuole che il dispositivo vibrare anziché riprodurre un suono, è possibile passare `NotificationDefaults.Vibrate` a `SetDefaults.` se si vuole che il dispositivo per riprodurre un suono e vibrare il dispositivo, è possibile passare a entrambi i flag `SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

Se si abilita suono senza specificare un suono da riprodurre, Android Usa il suono di notifica di sistema predefinito. Tuttavia, è possibile modificare il suono viene riprodotto chiamando il generatore di notifica [SetSound](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetSound/p/Android.Net.Uri/) metodo. Ad esempio, per riprodurre l'audio dell'allarme audio con la notifica (anziché il suono di notifica predefinito), è possibile ottenere l'URI per l'allarme suono dal [RingtoneManager](https://developer.xamarin.com/api/type/Android.Media.RingtoneManager/) e passarlo a `SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

In alternativa, utilizzare la suoneria predefinito sistema audio per la notifica:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

Dopo aver creato un oggetto di notifica, è possibile impostare le proprietà di notifica per l'oggetto notifica (invece di configurarli in anticipo tramite `Notification.Builder` metodi). Ad esempio, anziché chiamare il `SetDefaults` metodo per consentire alle vibrazioni su una notifica, è possibile modificare direttamente il flag di bit della notifica [predefinite](https://developer.xamarin.com/api/property/Android.App.Notification.Defaults/) proprietà:

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

In questo esempio fa sì che il dispositivo di far vibrare quando viene pubblicata la notifica.

<a name="updating-a-notification" />

### <a name="updating-a-notification"></a>L'aggiornamento di una notifica

Se si desidera aggiornare il contenuto di una notifica dopo che è stata pubblicata, è possibile riutilizzare esistente `Notification.Builder` oggetto per creare un nuovo oggetto di notifica e questa notifica con l'identificatore dell'ultima notifica di pubblicazione. Ad esempio:

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

In questo esempio esistente `Notification.Builder` oggetto viene utilizzato per creare un nuovo oggetto di notifica con un titolo diverso e un messaggio.
Il nuovo oggetto di notifica è pubblicato utilizzando l'identificatore della notifica precedente e si aggiorna il contenuto della notifica pubblicata in precedenza:

![Notifica aggiornata](local-notifications-images/12-updated-notification.png)

Il corpo della notifica precedente viene riutilizzato &ndash; solo il titolo e il testo della notifica varia durante la notifica viene visualizzata nel Pannello di notifiche. Il testo del titolo cambia da "Notifica di esempio" a "Notifica aggiornato" e il testo del messaggio viene modificato da "Hello World! Questa è la prima notifica!" per "Modificate a questo messaggio".

Una notifica rimane visibile finché non si verifica una delle tre operazioni:

-   L'utente chiude la notifica (o tocca *Cancella tutto*).

-   L'applicazione effettua una chiamata a `NotificationManager.Cancel`, passando l'ID di notifica univoco assegnato al momento è stata pubblicata la notifica.

-   L'applicazione chiama `NotificationManager.CancelAll`.

Per ulteriori informazioni sull'aggiornamento delle notifiche Android, vedere [una notifica di modifica](http://developer.android.com/training/notify-user/managing.html#Updating).

<a name="starting-an-activity" />

### <a name="starting-an-activity-from-a-notification"></a>Avvio di un'attività da una notifica

In Android, è normale che una notifica può essere associato un *azione* &ndash; un'attività che viene avviata quando l'utente tocca la notifica. Questa attività può risiedere in un'altra applicazione o anche in un'altra attività. Per aggiungere un'azione a una notifica, si crea un [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/) dell'oggetto e associare il `PendingIntent` con la notifica. Oggetto `PendingIntent` è un tipo speciale di finalità che consente all'applicazione destinataria per l'esecuzione di un frammento di codice predefinito con le autorizzazioni dell'applicazione mittente. Quando l'utente tocca la notifica, l'attività specificata da avvio Android di `PendingIntent`.

Frammento di codice seguente viene illustrato come creare una notifica con un `PendingIntent` che verrà avviata l'attività dell'applicazione di origine, `MainActivity`:

```csharp
// Set up an intent so that tapping the notifications returns to this app:
Intent intent = new Intent (this, typeof(MainActivity));

// Create a PendingIntent; we're only using one PendingIntent (ID = 0):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    PendingIntent.GetActivity (this, pendingIntentId, intent, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including pending intent:
Notification.Builder builder = new Notification.Builder(this)
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

Questo codice è molto simile al codice di notifica nella sezione precedente, tranne il fatto che un `PendingIntent` viene aggiunto all'oggetto notifica. In questo esempio, il `PendingIntent` è associata all'attività dell'app origine prima di essere passato per il generatore di notifica [SetContentIntent](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetContentIntent/) metodo. Il `PendingIntentFlags.OneShot` flag viene passato al `PendingIntent.GetActivity` metodo in modo che il `PendingIntent` viene utilizzata una sola volta. Quando si esegue questo codice, viene visualizzato il seguente messaggio:

![Prima notifica di azione](local-notifications-images/10-first-action-notification.png)

Se si tocca la notifica, l'utente passa all'attività di origine.

In un'applicazione di produzione, l'applicazione deve gestire il *stack Indietro* quando l'utente preme il **nuovamente** pulsante all'interno dell'attività di notifica (se non si ha familiarità con Android attività e stack indietro, vedere [ Attività e Stack Indietro](http://developer.android.com/guide/components/tasks-and-back-stack.html)).
Nella maggior parte dei casi, spostarsi all'indietro verso l'attività di notifica deve restituire all'utente dall'app e tornare alla schermata Home. Per gestire stack indietro, l'applicazione utilizzi la [TaskStackBuilder](https://developer.xamarin.com/api/type/Android.App.TaskStackBuilder/) classe per creare un `PendingIntent` con uno stack indietro.

Un'altra considerazione del mondo reale è che l'attività di origine potrebbe essere necessario inviare dati all'attività di notifica. Ad esempio, la notifica può indicare che è arrivato un messaggio di testo e l'attività di notifica (messaggio di visualizzazione dello schermo), richiede l'ID del messaggio per visualizzare il messaggio all'utente. Attività che crea il `PendingIntent` possibile utilizzare il [Intent.PutExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.PutExtra/p/System.String/System.String/) metodo per aggiungere dati (ad esempio, una stringa) per lo scopo, in modo che questi dati vengono passati all'attività di notifica.

Esempio di codice seguente viene illustrato come utilizzare `TaskStackBuilder` per gestire lo stack indietro e include un esempio di come inviare una stringa singolo messaggio a un'attività di notifica denominata `SecondActivity`:

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
Notification.Builder builder = new Notification.Builder (this)
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

In questo esempio di codice, l'app è costituito da due attività: `MainActivity` (che contiene il codice di notifica sopra), e `SecondActivity`, la schermata viene visualizzata dopo toccare la notifica. Quando viene eseguito questo codice, viene visualizzata una notifica semplice (simile all'esempio precedente). Selezionando la notifica richiede all'utente di `SecondActivity` schermo:

![Seconda schermata di attività](local-notifications-images/11-second-activity.png)

La stringa di messaggio (passato la finalità `PutExtra` metodo) viene recuperato nel `SecondActivity` tramite la riga di codice:

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

Questo messaggio recuperato, "I messaggi di saluto da MainActivity!," viene visualizzato nel `SecondActivity` schermata, come illustrato nella schermata precedente. Quando l'utente preme il **nuovamente** pulsante durante `SecondActivity`, navigazione lead dall'app e tornare alla schermata precedente l'avvio dell'app.

Per ulteriori informazioni sulla creazione in sospeso intenti, vedere [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/).


<a name="notif-chan" />

## <a name="notification-channels"></a>Canali di notifica

A partire da Android 8.0 (Oreo), è possibile utilizzare il *canali di notifica* funzionalità per creare un canale personalizzabili dall'utente per ogni tipo di notifica che si desidera visualizzare. Canali di notifica rendono possibile per le notifiche di gruppo in modo che tutte le notifiche registrati lo stesso comportamento presentano un canale. Ad esempio, potrebbe essere un canale di notifica che è previsto per le notifiche che richiedono attenzione immediata e un canale separato "silenzioso" che viene utilizzato per i messaggi informativi.

Il **YouTube** app installata con Android Oreo Elenca due categorie di notifica: **scaricare notifiche** e **notifiche generali**:

[![Schermate di notifica per YouTube in Oreo Android](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png)

Ognuna di queste categorie corrisponde a un canale di notifica. App YouTube implementa un **scaricare notifiche** canale e un **notifiche generali** canale. L'utente può toccare **scaricare notifiche**, che viene visualizzata la schermata di impostazioni per il canale di notifiche di download dell'app:

[![Schermata di notifiche dell'App YouTube di download](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png)

In questa schermata, l'utente può modificare il comportamento del **scaricare** notifiche canale effettuando le operazioni seguenti:

-   Impostare la priorità di livello **urgente**, **elevata**, **Media**, o **bassa**, che consentono di configurare il livello di interruzione di audio e visual.

-   Disattivare il punto di notifica.

-   Disattivare la luce intermittente.

-   Mostrare o nascondere le notifiche nella schermata di blocco.

-   Eseguire l'override di **non si applica** impostazione.

Il **notifiche generali** canale con impostazioni simili:

[![Schermata di notifiche generali per l'app YouTube](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png)

Si noti che non è assoluto controllo sulla modalità di interazione dei canali di notifica con l'utente &ndash; l'utente può modificare le impostazioni per qualsiasi canale di notifica sul dispositivo, come illustrato nelle schermate precedenti. Tuttavia, è possibile configurare i valori predefiniti (come verrà descritta di seguito). Come illustrano in questi esempi, la nuova funzionalità di canali di notifica rende possibile per fornire agli utenti di un controllo accurato diversi tipi di notifiche.

Si deve aggiungere il supporto per i canali di notifica all'App? Se si sta destinazione Android 8.0, l'app *deve* implementare canali di notifica.
Per visualizzare la notifica nei dispositivi Oreo un'app destinata Oreo che tenta di inviare una notifica locale all'utente senza utilizzare un canale di notifica non riuscirà. Se non la destinazione è Android 8.0, l'app verrà comunque runas su Android 8.0, ma con lo stesso comportamento di notifica si presentata durante l'esecuzione su Android 7.1 o versioni precedenti.

<a name="notif-chan-create" />

### <a name="creating-a-notification-channel"></a>Creazione di un canale di notifica

Per creare un canale di notifica, eseguire le operazioni seguenti:

1. Costruire un [NotificationChannel](https://developer.android.com/reference/android/app/NotificationChannel.html) oggetto con le operazioni seguenti:

    - Una stringa ID univoco all'interno del pacchetto. Nell'esempio seguente, la stringa `com.xamarin.myapp.urgent` viene utilizzato.

    - Il nome visibile dall'utente del canale (meno di 40 caratteri). Nell'esempio seguente, il nome **urgente** viene utilizzato.

    - L'importanza del canale, che controlla la modalità di interruzione notifiche vengono inviate al `NotificationChannel`. Può essere l'importanza `Default`, `High`, `Low`, `Max`, `Min`, `None`, o `Unspecified`.

    Passare questi valori per il [costruttore](https://developer.android.com/reference/android/app/NotificationChannel.html#NotificationChannel%28java.lang.String,%20java.lang.CharSequence,%20int%29) (in questo esempio, `Resource.String.noti_chan_urgent` è impostato su **urgente**):

    ```csharp
    public const string URGENT_CHANNEL = "com.xamarin.myapp.urgent";
    . . .
    string chanName = GetString (Resource.String.noti_chan_urgent);
    var importance = NotificationImportance.High;
    NotificationChannel chan =
       new NotificationChannel (URGENT_CHANNEL, chanName, importance);
    ```

2.  Configurare il `NotificationChannel` oggetto con le impostazioni iniziali.
    Ad esempio, il codice seguente consente di configurare il `NotificationChannel` dell'oggetto in modo che le notifiche registrate per questo canale verranno vibrare il dispositivo e vengono visualizzati nella schermata di blocco per impostazione predefinita:

    ```csharp
    chan.EnableVibration (true);
    chan.LockscreenVisibility = NotificationVisibility.Public;
    ```

3.  Inviare l'oggetto di canale di notifica per la gestione delle notifiche:

    ```csharp
    NotificationManager notificationManager =
        (NotificationManager) GetSystemService (NotificationService);
    notificationManager.CreateNotificationChannel (chan);
    ```

<a name="notif-chan-post" />

### <a name="posting-to-a-notifications-channel"></a>Registrazione a un canale di notifiche

Per registrare una notifica a un canale di notifica, eseguire le operazioni seguenti:

1.  Configurare la notifica usando il `Notification.Builder`, passando l'ID del canale per il `SetChannelId` metodo. Ad esempio:

    ```csharp
    Notification.Builder builder = new Notification.Builder (this)
        .SetContentTitle ("Attention!")
        .SetContentText ("This is an urgent notification message!")
        .SetChannelId (URGENT_CHANNEL);
    ```

2.  Compilare ed eseguire la notifica usando la gestione di notifica [notifica](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/p/System.Int32/Android.App.Notification/) metodo:

    ```csharp
    const int notificationId = 0;
    notificationManager.Notify (notificationId, builder.Build());
    ```

È possibile ripetere i passaggi precedenti per creare un altro canale di notifica per i messaggi informativi. Questo secondo canale può, per impostazione predefinita, disabilitare resistenza alle vibrazioni, impostare la visibilità di schermata di blocco predefinito su `Private`e impostare l'importanza di notifica `Default`.

Per un esempio di codice completo Android Oreo dei canali di notifica in azione, vedere il [NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) esempio; questa app di esempio gestisce due canali e imposta le opzioni di notifica aggiuntivo.



<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>Oltre la notifica di base

Notifiche predefinito in un formato di layout di base basilare in Android, ma è possibile migliorare il formato di base apportando ulteriori `Notification.Builder` chiamate al metodo. In questa sezione si apprenderà come aggiungere un'icona di foto di grandi dimensioni per la notifica e verrà visualizzato esempi su come creare le notifiche di layout espanso.

<a name="large-icon-format" />

### <a name="large-icon-format"></a>Formato di icone grandi

Notifiche Android, in genere, visualizzare l'icona dell'app origine (sul lato sinistro della notifica). Tuttavia, è possono visualizzare le notifiche di un'immagine o una foto (un *icone grandi*) anziché l'icona di piccole dimensioni standard. Ad esempio, un'applicazione di messaggistica visualizzi una foto del mittente, anziché l'icona dell'app.

Di seguito è riportato un esempio di una notifica di Android 5.0 base &ndash; viene visualizzata solo l'icona di piccole dimensioni app:

![Notifica normale di esempio](local-notifications-images/13-sample-notification.png)

Di seguito è riportata una schermata della notifica dopo averlo modificato per visualizzare un'icona di grandi dimensioni &ndash; Usa un'icona creata da un'immagine di un monkey codice Xamarin:

![Notifica di esempio icone grandi](local-notifications-images/14-large-icon-sample.png)

Si noti che, quando una notifica verrà visualizzata in formato di icone grandi, l'icona di piccole dimensioni app viene visualizzata come una notifica nell'angolo inferiore destro dell'icona di grandi dimensioni.

Per utilizzare un'immagine come un'icona di grandi dimensioni in una notifica, chiamare il generatore di notifica [SetLargeIcon](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetLargeIcon/) (metodo) e passare una bitmap dell'immagine. A differenza di `SetSmallIcon`, `SetLargeIcon` accetta solo una bitmap. Per convertire un file di immagine in una bitmap, utilizzare il [BitmapFactory](https://developer.xamarin.com/api/type/Android.Graphics.BitmapFactory/) classe. Ad esempio:

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

Questo codice di esempio apre il file di immagine in **Resources/drawable/monkey_icon.png**, lo converte in una bitmap e passa la bitmap risulta da `Notification.Builder`. In genere, la risoluzione dell'immagine di origine è maggiore dell'icona piccola &ndash; ma non molto più grande. Un'immagine troppo grande può causare inutili operazioni di ridimensionamento che possono ritardare la registrazione della notifica.
Per altre informazioni sulle dimensioni dell'icona di notifica in Android, vedere [le icone di notifica](http://developer.android.com/design/style/iconography.html#notification).

<a name="big-text-style" />

### <a name="big-text-style"></a>Stile del testo di grandi dimensioni

Il *testo Big* stile è un modello di layout espanso che è possibile utilizzare per visualizzare i messaggi lunghi nelle notifiche. Come tutte le notifiche di layout espanso, la notifica di testo di grandi dimensioni deve essere visualizzata inizialmente in un formato compatto:

![Notifica di testo di grandi dimensioni di esempio](local-notifications-images/15-big-text-notification.png)

In questo formato, viene visualizzato solo un frammento del messaggio, terminato da due punti. Quando l'utente trascina verso il basso sulla notifica, si espande per visualizzare il messaggio di notifica intero:

![Notifica di testo di grandi dimensioni espansa](local-notifications-images/16-big-text-expanded.png)

Questo formato espanso layout include anche il testo di riepilogo nella parte inferiore della notifica. L'altezza massima del *testo Big* notifica è 256 punto di distribuzione.

Per creare un *testo Big* notifica, si crea un'istanza un `Notification.Builder` dell'oggetto, come in precedenza, quindi creare un'istanza e aggiungere un [BigTextStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigTextStyle/) dell'oggetto per il `Notification.Builder` oggetto. Ad esempio:

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

In questo esempio, il testo del messaggio e il testo di riepilogo vengono archiviati nel `BigTextStyle` oggetto (`textStyle`) prima di essere passato a `Notification.Builder.`

<a name="image-style" />

### <a name="image-style"></a>Stile di immagine

Il *immagine* stile (chiamato anche il *quadro generale* stile) è un formato espanso notifica che è possibile utilizzare per visualizzare un'immagine nel corpo di una notifica. Ad esempio, possibile usare un'app schermata o un'app di foto di *immagine* notifica stile per fornire all'utente una notifica dell'ultima immagine che è stata acquisita. Si noti che l'altezza massima del *immagine* notifica è 256 dp &ndash; Android verrà ridimensionato l'immagine in modo da adattarsi a questa restrizione altezza massima, entro i limiti di memoria disponibile.

Come tutte espanso notifiche di layout, *immagine* prima vengono visualizzate notifiche in un formato compatto che consente di visualizzare un estratto del testo del messaggio associato:

![Notifica di Compact immagine non mostra alcuna immagine](local-notifications-images/17-image-compact.png)

Quando l'utente trascina verso il basso *immagine* notifica, si espande per visualizzare un'immagine. Ad esempio, ecco la versione espansa della precedente notifica:

![Immagine ingrandita notifica rivela immagine](local-notifications-images/18-image-expanded.png)

Si noti che quando viene visualizzata la notifica in formato compatto, viene visualizzato testo della notifica (il testo che viene passato per il generatore di notifica `SetContentText` (metodo), come illustrato in precedenza). Tuttavia, quando la notifica viene espansa per visualizzare l'immagine, Visualizza il testo del riassunto sopra l'immagine.

Per creare un *immagine* creare un'istanza di notifica, un `Notification.Builder` oggetto come in precedenza, quindi creare e inserire un [BigPictureStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigPictureStyle/) nell'oggetto di `Notification.Builder` oggetto. Ad esempio:

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

Ad esempio il `SetLargeIcon` metodo di `Notification.Builder`, il [BigPicture](https://developer.xamarin.com/api/member/Android.App.Notification+BigPictureStyle.BigPicture/) metodo `BigPictureStyle` richiede una bitmap dell'immagine che si desidera visualizzare nel corpo della notifica. In questo esempio, il [DecodeResource](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeResource/(Android.Content.Res.Resources%2cSystem.Int32)) metodo `BitmapFactory` legge il file di immagine disponibile all'indirizzo **Resources/drawable/x_bldg.png** e lo converte in una bitmap.

È inoltre possibile visualizzare le immagini che non sono state inserite come una risorsa. Ad esempio, il seguente codice di esempio carica un'immagine dalla scheda SD locale e lo visualizza in un *immagine* notifica:

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

In questo esempio si trova il file di immagine in **/sdcard/Pictures/my-tshirt.jpg** viene caricato, ridimensionato alla metà della dimensione originale e quindi convertito in una bitmap per l'utilizzo della notifica:

![Immagine di esempio shirt nella notifica](local-notifications-images/19-tshirt-notification.png)

Se si conoscono in anticipo le dimensioni del file di immagine, è consigliabile eseguire il wrapping della chiamata a [BitmapFactory.DecodeFile](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeFile/p/System.String/Android.Graphics.BitmapFactory+Options/) in un gestore eccezioni &ndash; un `OutOfMemoryError` eccezione potrebbe essere generata se l'immagine è troppo grande per Android da ridimensionare.

Per ulteriori informazioni sul caricamento e la decodifica delle immagini bitmap di grandi dimensioni, vedere [carico elevato bitmap in modo efficiente](https://developer.xamarin.com/recipes/android/resources/general/load_large_bitmaps_efficiently).

<a name="inbox-style" />

### <a name="inbox-style"></a>Stile della posta in arrivo

Il *posta in arrivo* formato è un modello di layout espanso previsto per la visualizzazione di righe distinte di testo (ad esempio un messaggio di posta elettronica della posta in arrivo riepilogo) nel corpo della notifica. Il *posta in arrivo* formato notifica viene prima visualizzata in un formato compatto:

![Notifica di posta in arrivo compact esempio](local-notifications-images/20-inbox-compact.png)

Quando l'utente trascina verso il basso sulla notifica, questo si espande per visualizzare un riepilogo di posta elettronica, come illustrato nella schermata seguente:

![Notifica di posta in arrivo esempio espansa](local-notifications-images/21-inbox-expanded.png)

Per creare un *posta in arrivo* creare un'istanza di notifica, un `Notification.Builder` , come in precedenza e aggiungere un [InboxStyle](https://developer.xamarin.com/api/type/Android.App.Notification+InboxStyle/) dell'oggetto per il `Notification.Builder`. Ad esempio:

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

Per aggiungere nuove righe di testo al corpo della notifica, chiamare il [Addline](https://developer.xamarin.com/api/member/Android.App.Notification+InboxStyle.AddLine/p/System.String/) metodo il `InboxStyle` oggetto (l'altezza massima del *posta in arrivo* notifica è 256 dp). Si noti che, a differenza di *testo Big* stile, la *posta in arrivo* stile supporta singole righe di testo nel corpo della notifica.

È inoltre possibile utilizzare il *posta in arrivo* stile per le notifiche che deve visualizzare singole righe di testo in formato espanso. Ad esempio, il *posta in arrivo* stile notifica può essere utilizzato per combinare più notifiche in sospeso in una notifica di riepilogo &ndash; è possibile aggiornare una singola *posta in arrivo* stile notifica con new righe di contenuto di notifica (vedere [l'aggiornamento di una notifica](#updating-a-notification) sopra), invece di generare un flusso continuo di notifiche di nuove e per lo più simile. Per ulteriori informazioni, vedere [riepilogare le notifiche](http://developer.android.com/design/patterns/notifications.html#summarize_your_notifications).

<a name="configuring-metadata" />

## <a name="configuring-metadata"></a>Configurazione di metadati

`Notification.Builder` include metodi che è possibile chiamare per impostare i metadati sulla notifica, ad esempio, priorità, visibilità e la categoria. Queste informazioni vengono utilizzate Android &mdash; insieme alle impostazioni delle preferenze utente &mdash; per determinare come e quando visualizzare le notifiche.

<a name="priority-settings" />

### <a name="priority-settings"></a>Impostazioni di priorità

L'impostazione di priorità di una notifica determina due risultati quando viene pubblicata la notifica:

-   Rispetto alle altre notifiche in cui viene visualizzata la notifica.
    Ad esempio, ad alta priorità vengono visualizzate sopra le notifiche di priorità inferiore nel cassetto notifiche, indipendentemente da quando ogni notifica è stata pubblicata.

-   Indica se la notifica viene visualizzata nel formato Heads-up notifica (Android 5.0 e versioni successive). Solo *elevata* e *massimo* le notifiche di priorità vengono visualizzate come notifiche Heads-up.

Xamarin definisce le enumerazioni seguenti per l'impostazione di priorità di notifica:

-   `NotificationPriority.Max` &ndash; Avvisa l'utente a una condizione urgente o critica (ad esempio, una chiamata in ingresso, direzioni attiva dopo strada o un avviso di emergenza). In Android 5.0 e versioni successive, le notifiche di priorità massima vengono visualizzate in formato Heads-up.

-   `NotificationPriority.High` &ndash; Informa l'utente di eventi importanti (ad esempio messaggi di posta elettronica importanti o la ricezione di messaggi di chat in tempo reale). In Android 5.0 e versioni successive, le notifiche di priorità alta vengono visualizzate in formato Heads-up.

-   `NotificationPriority.Default` &ndash; Notifica all'utente di condizioni che hanno un livello medio di importanza.

-   `NotificationPriority.Low` &ndash; Per priorità bassa informazioni che l'utente deve essere informato di (ad esempio, promemoria di aggiornamento software o aggiornamenti di social network).

-   `NotificationPriority.Min` &ndash; Per le informazioni che l'utente notifiche solo quando la visualizzazione delle notifiche (ad esempio, le informazioni di percorso o weather).

Per impostare la priorità di una notifica, chiamare il [SetPriority](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetPriority/) metodo il `Notification.Builder` oggetto, passando il livello di priorità. Ad esempio:

```csharp
builder.SetPriority (NotificationPriority.High);
```

Nell'esempio seguente, la notifica ad alta priorità, "Messaggio importante!" viene visualizzata nella parte superiore del cassetto della notifica:

![Notifica ad alta priorità di esempio](local-notifications-images/22-hi-priority-drawer.png)

Poiché si tratta di una notifica ad alta priorità, viene visualizzato anche come notifica preliminare sopra lo schermo dell'utente corrente attività Android 5.0:

![Notifica preliminare di esempio](local-notifications-images/23-heads-up-example.png)

Nell'esempio seguente, la notifica "Pensato per il giorno" con priorità bassa viene visualizzata in una notifica di livello di priorità più alta batteria:

![Notifica di esempio con priorità bassa](local-notifications-images/24-lo-priority-drawer.png)

Poiché la notifica "Pensiero del giorno" è una notifica con priorità bassa, Android visualizzerà non in formato Heads-up.

<a name="visibility-settings" />

### <a name="visibility-settings"></a>Impostazioni di visibilità

A partire da Android 5.0, il *visibilità* impostazione è disponibile per controllare la quantità di contenuto notifica viene visualizzata sul lockscreen sicura.
Xamarin definisce le enumerazioni seguenti per l'impostazione di visibilità di notifica:

-   `NotificationVisibility.Public` &ndash; Il contenuto completo della notifica viene visualizzato nella lockscreen sicura.

-   `NotificationVisibility.Private` &ndash; Viene visualizzate solo le informazioni essenziali sul lockscreen sicura (ad esempio l'icona di notifica e il nome dell'app che si è registrato), ma il resto dei dettagli della notifica sono nascoste. Per impostazione predefinita tutte le notifiche `NotificationVisibility.Private`.

-   `NotificationVisibility.Secret` &ndash; Nel lockscreen sicura, nemmeno l'icona di notifica viene visualizzato nulla. Il contenuto di notifica è disponibile solo dopo che l'utente consente di sbloccare il dispositivo.

Per impostare la visibilità di una notifica, chiamata di App di `SetVisibility` metodo il `Notification.Builder` oggetto, passando l'impostazione di visibilità. Ad esempio, questa chiamata a `SetVisibility` rende la notifica `Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

Quando un `Private` notifica è registrata, solo il nome e l'icona dell'app viene visualizzata sul lockscreen sicura. Anziché il messaggio di notifica, l'utente visualizza "Sblocco del dispositivo per visualizzare la notifica":

![Sbloccare il messaggio di notifica dispositivo](local-notifications-images/25-lockscreen-private.png)

In questo esempio, **NotificationsLab** è il nome dell'applicazione di origine. Questa versione di adattamento della notifica viene visualizzata solo quando il lockscreen è sicura (ad esempio, protetto tramite password, il criterio o PIN) &ndash; se il lockscreen non è protetta, il contenuto completo della notifica è disponibile nel lockscreen.

<a name="category-settings" />

### <a name="category-settings"></a>Impostazioni di categoria

A partire da Android 5.0, le categorie predefinite sono disponibili per la classificazione e il filtraggio delle notifiche. Xamarin offre le enumerazioni seguenti per queste categorie:

-   `Notification.CategoryCall` &ndash; Telefonata in arrivo.

-   `Notification.CategoryMessage` &ndash; Testo del messaggio in ingresso.

-   `Notification.CategoryAlarm` &ndash; Un allarme condizione o timer della scadenza.

-   `Notification.CategoryEmail` &ndash; Messaggio di posta elettronica in arrivo.

-   `Notification.CategoryEvent` &ndash; Un evento del calendario.

-   `Notification.CategoryPromo` &ndash; Un messaggio promozionale o dell'annuncio.

-   `Notification.CategoryProgress` &ndash; Lo stato di avanzamento di un'operazione in background.

-   `Notification.CategorySocial` &ndash; Aggiornamento della rete sociale.

-   `Notification.CategoryError` &ndash; Errore del processo di autenticazione o di operazione in background.

-   `Notification.CategoryTransport` &ndash; Aggiornamento di riproduzione supporti.

-   `Notification.CategorySystem` &ndash; Riservato per utilizzi di sistema (stato di sistema o un dispositivo).

-   `Notification.CategoryService` &ndash; Indica che un servizio in background è in esecuzione.

-   `Notification.CategoryRecommendation` &ndash; Un messaggio correlato all'app attualmente in esecuzione.

-   `Notification.CategoryStatus` &ndash; Informazioni sul dispositivo.

Quando le notifiche sono ordinate, la priorità della notifica ha la priorità relativa categoria. Ad esempio, verrà visualizzata una notifica ad alta priorità come Heads-up anche se appartiene al `Promo` categoria. Per impostare la categoria di una notifica, chiamare il `SetCategory` metodo il `Notification.Builder` oggetto, passando l'impostazione della categoria. Ad esempio:

```csharp
builder.SetCategory (Notification.CategoryCall);
```

Il *non disturbi* funzionalità (nuovo in Android 5.0) consente di filtrare in base alle categorie di notifiche. Ad esempio, il *non disturbi* nella schermata **impostazioni** consente all'utente esente notifiche per le chiamate e messaggi:

![Non disturbi schermata Opzioni](local-notifications-images/26-do-not-disturb.png)

Quando l'utente configura *non disturbi* per bloccare tutti gli interrupt, ad eccezione di telefonate (come illustrato nella schermata precedente), Android consente le notifiche con un'impostazione di categoria di `Notification.CategoryCall` devono essere presentati quando il dispositivo è in *non disturbi* modalità. Si noti che `Notification.CategoryAlarm` notifiche mai bloccate in *non disturbi* modalità.


<a name="compatibility" />

## <a name="compatibility"></a>Compatibilità

Se si sta creando un'app anche eseguita nelle versioni precedenti di Android (fino a livello di API 4), si userà il [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html) classe anziché `Notification.Builder`. Quando si creano notifiche con `NotificationCompat.Builder`, Android assicura che il contenuto di notifica di base sia visualizzato correttamente nella dispositivi meno recenti. Tuttavia, poiché alcune funzionalità avanzate di notifica non sono disponibili nelle versioni precedenti di Android, il codice in modo esplicito deve gestire problemi di compatibilità per gli stili di notifica espanso, categorie e i livelli di visibilità come illustrato di seguito.

Per utilizzare `NotificationCompat.Builder` nell'app, è necessario includere il [libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet nel progetto.

Esempio di codice seguente viene illustrato come creare una notifica di base utilizzando `NotificationCompat.Builder`:

```csharp
// Instantiate the builder and set notification elements:
NotificationCompat.Builder builder = new NotificationCompat.Builder (this)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();
```

Come illustrato in questo esempio, per le opzioni di notifica essenziali chiamate al metodo sono identiche a quelli di `Notification.Builder`. Tuttavia, il codice gestire i problemi di compatibilità verso il basso per le notifiche più complessi (come descritte nella sezione successiva).

Il [LocalNotifications](https://developer.xamarin.com/samples/monodroid/LocalNotifications) esempio viene illustrato come utilizzare `NotificationCompat.Builder` per avviare una seconda attività da una notifica. Questo codice di esempio è illustrato il [utilizzando locale notifiche in xamarin](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md) procedura dettagliata.

<a name="notification-styles" />

### <a name="notification-styles"></a>Stili di notifica

Per creare *testo Big*, *immagine*, o *posta in arrivo* stile notifiche con `NotificationCompat.Builder`, l'app deve usare le versioni di compatibilità di questi stili. Ad esempio, per usare il *testo Big* stile, creare un'istanza di `NotificationCompat.BigTextstyle`:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

Analogamente, è possibile usare l'app `NotificationCompat.InboxStyle` e `NotificationCompat.BigPictureStyle` per *posta in arrivo* e *immagine* stili, rispettivamente.

<a name="priority-and-category" />

### <a name="notification-priority-and-category"></a>Categoria e la priorità di notifica

`NotificationCompat.Builder` supporta il `SetPriority` metodo (disponibile a partire da Android 4.1). Tuttavia, il `SetCategory` metodo *non* supportati da `NotificationCompat.Builder` perché categorie fanno parte del sistema dei metadati notifica che è stata introdotta in Android 5.0.

Per supportare le versioni precedenti di Android, dove `SetCategory` è non disponibile, il codice può verificare il livello di API in fase di esecuzione per chiamare in modo condizionale `SetCategory` quando il livello di API è uguale o maggiore di Android 5.0 (livello API 21):

```csharp
if ((int) Android.OS.Build.Version.SdkInt >= 21) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

In di questo esempio, l'app **Framework di destinazione** è impostato su Android 5.0 e **la versione minima Android** è impostato su **Android 4.1 (API livello 16)**. Poiché `SetCategory` è disponibile nel livello API 21 e versioni successiva, questo codice di esempio chiama `SetCategory` solo quando è disponibile &ndash; non chiamerà `SetCategory` quando il livello API è minore di
21.

<a name="lockscreen-visibility" />

### <a name="lockscreen-visibility"></a>Visibilità Lockscreen

Perché non supporta le notifiche lockscreen prima Android 5.0 (livello API 21), Android `NotificationCompat.Builder` non supporta il `SetVisibility` metodo. Come descritto in precedenza per `SetCategory`, il codice può verificare il livello di API in fase di chiamata e `SetVisiblity` solo quando è disponibile:

```csharp
if ((int) Android.OS.Build.Version.SdkInt >= 21) {
    builder.SetVisibility (Notification.Public);
}
```

<a name="summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come creare le notifiche locale in Android. Descritto l'anatomia di una notifica, illustrato come utilizzare `Notification.Builder` per creare le notifiche, come le notifiche di stile nell'icona di grandi dimensioni, *testo Big*, *immagine* e *posta in arrivo*  formati, come impostare le impostazioni dei metadati, ad esempio, priorità, visibilità e la categoria di notifica e come avviare un'attività da una notifica. Questo articolo viene inoltre descritto il funzionano di queste impostazioni di notifica con la nuova Heads-up, lockscreen, e *non disturbi* funzionalità introdotte in Android 5.0. Infine, si è appreso come utilizzare `NotificationCompat.Builder` per mantenere la compatibilità di notifica con le versioni precedenti di Android.

Per indicazioni sulla progettazione di notifiche per Android, vedere [notifiche](http://developer.android.com/preview/notifications.html).


## <a name="related-links"></a>Collegamenti correlati

- [NotificationsLab (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/NotificationsLab/)
- [LocalNotifications (esempio)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Notifiche locale nella procedura dettagliata di Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [Notifiche](http://developer.android.com/design/patterns/notifications.html)
- [Notificare all'utente](http://developer.android.com/training/notify-user/index.html)
- [notifica](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
