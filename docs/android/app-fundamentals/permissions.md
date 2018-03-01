---
title: Autorizzazioni di xamarin
ms.topic: article
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: d20b2aa7df17f2000e2de9cb67f091c52989719b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="permissions-in-xamarinandroid"></a>Autorizzazioni di xamarin

<a name="overview" />

## <a name="overview"></a>Panoramica

Eseguire applicazioni Android nella propria sandbox e per la sicurezza motivi non hanno accesso a determinate risorse di sistema o hardware sul dispositivo. L'utente deve concedere esplicitamente l'autorizzazione per l'app prima può utilizzare queste risorse. Ad esempio, un'applicazione non può accedere il GPS in un dispositivo senza autorizzazione esplicita da parte dell'utente. Android genererà un `Java.Lang.SecurityException` se un'app tenta di accedere a una risorsa protetta senza autorizzazione.

Le autorizzazioni vengono dichiarate nel **AndroidManifest.xml** dallo sviluppatore dell'applicazione quando l'applicazione viene sviluppata. Android include due flussi di lavoro diversi per ottenere il consenso dell'utente per le autorizzazioni:
 
* Per le applicazioni che la destinazione Android 5.1 (livello API 22) o un livello inferiore, la richiesta di autorizzazione, si è verificato durante l'installazione dell'app. Se l'utente non ha concesso le autorizzazioni, l'app potrebbe non essere installata. Dopo aver installato l'app, non è possibile revocare le autorizzazioni tranne disinstallando l'app.
* A partire da Android 6.0 (livello API 23), gli utenti hanno avuti maggiore controllo sulle autorizzazioni. possono concedere o revocare le autorizzazioni, purché l'app viene installata nel dispositivo. Questa schermata mostra le impostazioni di autorizzazione per l'app dei contatti di Google. Il file elenca le diverse autorizzazioni e consente all'utente di abilitare o disabilitare le autorizzazioni:

![Schermata di autorizzazioni di esempio](permissions-images/01-permissions-check.png) 

Le app Android devono controllare in fase di esecuzione per vedere se hanno l'autorizzazione per accedere a una risorsa protetta. Se l'app non dispone dell'autorizzazione, è necessario apportare delle richieste effettuate utilizzando le nuove API fornite da Android SDK per l'utente per concedere le autorizzazioni. Le autorizzazioni sono divisi in due categorie:

* **Autorizzazioni normale** &ndash; queste sono autorizzazioni che problemi di sicurezza minimo di sicurezza o di privacy dell'utente. Android 6.0 concederà autorizzazioni normali automaticamente al momento dell'installazione. Consultare la documentazione per Android un [elenco completo delle autorizzazioni normale](https://developer.android.com/guide/topics/permissions/normal-permissions.html).
* **Autorizzazioni pericolose** &ndash; a differenza delle normali autorizzazioni, autorizzazioni pericolose sono quelli che proteggono sicurezza o privacy dell'utente. E devono essere esplicitamente concesso dall'utente. Invia o riceve un messaggio SMS è un esempio di un'azione che richiede un'autorizzazione pericolosa.

> [!IMPORTANT]
> La categoria a cui appartiene un'autorizzazione potrebbe cambiare nel tempo.  È possibile che un'autorizzazione che è stata categorizzata come un'autorizzazione "normale" potrebbe essere in futuro elevati livelli di API per un'autorizzazione pericolosa.

Autorizzazioni pericolose sono Sub suddivisa ulteriormente in [ _gruppi di autorizzazioni_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups). Un gruppo di autorizzazioni conterrà le autorizzazioni correlate logicamente. Quando l'utente concede l'autorizzazione a un membro di un gruppo di autorizzazioni, Android concede automaticamente l'autorizzazione a tutti i membri di tale gruppo. Ad esempio, il [ `STORAGE` ](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE) gruppo di autorizzazione contiene sia il `WRITE_EXTERNAL_STORAGE` e `READ_EXTERNAL_STORAGE` le autorizzazioni. Se l'utente concede l'autorizzazione per `READ_EXTERNAL_STORAGE`, quindi il `WRITE_EXTERNAL_STORAGE` autorizzazione viene concessa automaticamente nello stesso momento.

Prima di richiedere una o più autorizzazioni, è consigliabile fornire una spiegazione logica per il motivo per cui l'app richiede l'autorizzazione prima di richiedere l'autorizzazione. Una volta che l'utente in grado di comprendere la logica alla base, l'app può richiedere l'autorizzazione da parte dell'utente. Conoscendo la logica alla base, l'utente può prendere una decisione consapevole se si desidera concedere l'autorizzazione e comprendere le conseguenze in caso contrario. 

Intero flusso di lavoro di controllo e la richiesta di autorizzazioni è noto come un _autorizzazioni in fase di esecuzione_ controllare e può essere riepilogato nel diagramma seguente: 

[ ![Diagramma di flusso di controllo dell'autorizzazione in fase di esecuzione](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png)

Backports la libreria di supporto Android per alcune delle nuove API per le autorizzazioni per le versioni precedenti di Android. Queste API backported controlla automaticamente la versione di Android nel dispositivo, pertanto non è necessario eseguire un controllo a livello di API ogni volta.  

Questo documento verrà illustrate le procedure aggiungere autorizzazioni a un'applicazione di xamarin e in che modo le app destinate a Android 6.0 (livello API 23) o versioni successive deve eseguire un controllo di autorizzazione in fase di esecuzione.


> [!NOTE]
> **Nota:** è possibile che le autorizzazioni per l'hardware possono influire sulle modalità di filtro l'app da Google Play. Ad esempio, se l'applicazione richiede l'autorizzazione per la fotocamera, quindi Google Play non visualizzerà l'app in Google Play Store su un dispositivo che non è installata una webcam.


<a name="requirements" />

## <a name="requirements"></a>Requisiti

È consigliabile che i progetti di xamarin includono le [Xamarin.Android.Support.Compat](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/) pacchetto NuGet. Questa autorizzazione di backport pacchetto verrà API specifiche per le versioni precedenti di Android, garantendo una interfaccia costantemente senza la necessità di controllare la versione di Android che è in esecuzione l'app.

<a name="requesting_permissions" />

## <a name="requesting-system-permissions"></a>La richiesta di autorizzazioni di sistema

Utilizzo delle autorizzazioni Android il primo passaggio consiste nel dichiarare che le autorizzazioni di Android file manifesto. Questa operazione deve essere eseguita indipendentemente dal livello di API che l'applicazione è destinata.

App di destinazione Android 6.0 o successiva non è possibile presupporre che perché l'utente dell'autorizzazione a un certo punto in passato, che l'autorizzazione saranno valido al successivo. Un'app destinate a Android 6.0 è necessario effettuare sempre un controllo di autorizzazione di runtime. Le app destinate a Android 5.1 o versione precedente non è necessario eseguire un controllo di autorizzazione in fase di esecuzione.

> [!NOTE]
> **Nota:** applicazioni dovrebbero richiedere solo le autorizzazioni necessarie.

<a name="declaring_permissions_in_the_manifest" />

### <a name="declaring-permissions-in-the-manifest"></a>Dichiarazione di autorizzazioni nel manifesto

Vengono aggiunte le autorizzazioni per il **AndroidManifest.xml** con il `uses-permission` elemento. Ad esempio, se un'applicazione per individuare la posizione del dispositivo, richiede correttamente e corso le autorizzazioni del percorso. I seguenti due elementi vengono aggiunti al manifesto: 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

È possibile dichiarare le autorizzazioni utilizzando il supporto dello strumento integrato in Visual Studio:

1. Fare doppio clic su **proprietà** nel **Esplora** e selezionare il **manifesto Android** scheda nella finestra Proprietà:

    [![Autorizzazioni necessarie nella scheda manifesto Android](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png)

2. Se l'applicazione non ha già un AndroidManifest.xml, fare clic su **AndroidManifest.xml non trovato. Fare clic per aggiungere uno** come illustrato di seguito:

    [![Nessun messaggio AndroidManifest.xml](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png)

3. Selezionare l'applicazione necessita di autorizzazioni di **delle autorizzazioni necessarie** elenco e salvare:

    [![Autorizzazioni di fotocamera esempio selezionate](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

È possibile dichiarare le autorizzazioni utilizzando il supporto dello strumento integrato in Visual Studio per Mac:

1. Fare doppio clic sul progetto nel **soluzione riempimento** e selezionare **Opzioni > compilare > applicazione Android**:

    [![Sezione di autorizzazioni richiesti mostrato](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png)

2. Fare clic su di **aggiungere manifesto Android** se il progetto non dispone già di un **AndroidManifest.xml**:

    [![Il manifesto Android del progetto è mancante](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png)

3. Selezionare l'applicazione necessita di autorizzazioni di **delle autorizzazioni necessarie** elenco e fare clic su **OK**:

    [![Autorizzazioni di fotocamera esempio selezionate](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png)
    
-----

Xamarin aggiungerà automaticamente alcune autorizzazioni in fase di compilazione per le compilazioni di Debug. In questo modo il debug dell'applicazione più semplice. In particolare, sono di due autorizzazioni rilevanti `INTERNET` e `READ_EXTERNAL_STORAGE`. Queste automaticamente set di autorizzazioni non verranno visualizzati da attivare per il **delle autorizzazioni necessarie** elenco. Build di rilascio, tuttavia, utilizzare solo le autorizzazioni impostate in modo esplicito nel **delle autorizzazioni necessarie** elenco. 

Per le app destinate a Android 5.1 (livello API 22) o un livello inferiore, non vi sono altre che deve essere eseguita. App eseguibili in Android 6.0 (livello API 23 23) o versione successiva è possibile procedere alla sezione successiva su come eseguire i controlli delle autorizzazioni di fase di esecuzione. 

<a name="run_time_permission_checks" />

### <a name="runtime-permission-checks-in-android-60"></a>Controlli delle autorizzazioni di runtime in Android 6.0

Il `ContextCompat.CheckSelfPermission` (disponibile con la libreria di supporto Android) consente di verificare se è stata concessa un'autorizzazione specifica. Questo metodo restituirà un [ `Android.Content.PM.Permission` ](https://developer.xamarin.com/api/type/Android.Content.PM.Permission/) enum con uno dei due valori:

* **`Permission.Granted`** &ndash; È stata concessa l'autorizzazione specificata.
* **`Permission.Denied`** &ndash; Non è stata concessa l'autorizzazione specificata.

Questo frammento di codice è riportato un esempio di come controllo di autorizzazione della fotocamera in un'attività: 

```csharp
if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.Camera) == (int)Permission.Granted) 
{
    // We have permission, go ahead and use the camera.
} 
else 
{
    // Camera permission is not granted. If necessary display rationale & request.
}
```

È consigliabile informare l'utente per un'autorizzazione perché è necessaria per un'applicazione in modo che una decisione consapevole, è possibile eseguire per concedere l'autorizzazione. Un esempio di questo potrebbe essere un'applicazione che accetta le foto e geografica tag li. È chiaro per l'utente che è necessaria l'autorizzazione della fotocamera, ma potrebbe non essere chiaro il motivo per cui l'app è anche necessario specificare il percorso del dispositivo. La logica alla base deve essere visualizzato un messaggio di aiuto all'utente di comprendere perché l'autorizzazione di percorso è opportuno e che è necessaria l'autorizzazione della fotocamera.

Il `ActivityCompat.ShouldShowRequestPermissionRational` metodo viene utilizzato per determinare se la logica alla base deve essere visualizzata all'utente. Questo metodo restituirà `true` se la logica di un'autorizzazione specificata deve essere visualizzata. Questa schermata è riportato un esempio di un Snackbar visualizzata da un'applicazione che spiega perché l'app deve conoscere il percorso del dispositivo:

![Spiegazione logica per il percorso](permissions-images/07-rationale-snackbar.png) 

Se l'utente concede l'autorizzazione, il `ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)` metodo deve essere chiamato. Questo metodo richiede i seguenti parametri:

* **attività** &ndash; questa è l'attività che sta richiedendo le autorizzazioni e deve essere informato da Android dei risultati.
* **autorizzazioni** &ndash; un elenco di autorizzazioni che sono stati richiesti.
* **requestCode** &ndash; un valore intero che viene utilizzato per associare i risultati della richiesta di autorizzazione per un `RequestPermissions` chiamare. Questo valore deve essere maggiore di zero.

Questo frammento di codice è riportato un esempio dei due metodi che sono stati descritti. Innanzitutto, viene eseguito un controllo per determinare se la logica di autorizzazione deve essere visualizzata. Se la logica alla base è visualizzato, un Snackbar viene visualizzato con la logica alla base. Se l'utente fa clic **OK** in Snackbar, quindi l'app richiederà le autorizzazioni. Se l'utente non accetta la logica alla base, l'applicazione non deve continuare per richiedere le autorizzazioni. Se non viene visualizzata la logica alla base, l'attività verrà richiesta l'autorizzazione:

```csharp
if (ActivityCompat.ShouldShowRequestPermissionRationale(this, Manifest.Permission.AccessFineLocation)) 
{
    // Provide an additional rationale to the user if the permission was not granted
    // and the user would benefit from additional context for the use of the permission.
    // For example if the user has previously denied the permission.
    Log.Info(TAG, "Displaying camera permission rationale to provide additional context.");

    var requiredPermissions = new String[] { Manifest.Permission.AccessFineLocation };
    Snackbar.Make(layout, 
                   Resource.String.permission_location_rationale,
                   Snackbar.LengthIndefinite)
            .SetAction(Resource.String.ok, 
                       new Action<View>(delegate(View obj) {
                           ActivityCompat.RequestPermissions(this, requiredPermissions, REQUEST_LOCATION);
                       }    
            )
    ).Show();
}
else 
{
    ActivityCompat.RequestPermissions(this, new String[] { Manifest.Permission.Camera }, REQUEST_LOCATION);
}
```

`RequestPermission` può essere chiamato anche se l'utente ha già concessa l'autorizzazione. Le chiamate successive non sono necessarie, ma forniscono all'utente la possibilità di verificare l'autorizzazione (o revocare). Quando `RequestPermission` viene chiamato, controllo viene passato al sistema operativo, che visualizza un'interfaccia utente per accettare le autorizzazioni:  

![Finestra di dialogo autorizzazioni limitate sono](permissions-images/08-location-permission-dialog.png)

Al termine, l'utente Android verranno restituiti i risultati all'attività tramite un metodo di callback, `OnRequestPermissionResult`. Questo metodo fa parte dell'interfaccia `ActivityCompat.IOnRequestPermissionsResultCallback` che deve essere implementata dall'attività. Questa interfaccia dispone di un singolo metodo, `OnRequestPermissionsResult`, che sarà richiamato da Android per informare l'attività delle scelte dell'utente. Se l'utente dispone dell'autorizzazione, l'app può procedere e usare la risorsa protetta. Un esempio di come implementare `OnRequestPermissionResult` è illustrato di seguito: 

```csharp
public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Permission[] grantResults)
{
    if (requestCode == REQUEST_LOCATION) 
    {
        // Received permission result for camera permission.
        Log.Info(TAG, "Received response for Location permission request.");

        // Check if the only required permission has been granted
        if ((grantResults.Length == 1) && (grantResults[0] == Permission.Granted)) {
            // Location permission has been granted, okay to retrieve the location of the device.
            Log.Info(TAG, "Location permission has now been granted.");
            Snackbar.Make(layout, Resource.String.permision_available_camera, Snackbar.LengthShort).Show();            
        } 
        else 
        {
            Log.Info(TAG, "Location permission was NOT granted.");
            Snackbar.Make(layout, Resource.String.permissions_not_granted, Snackbar.LengthShort).Show();
        }
    } 
    else 
    {
        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
}
```  

<a name="summary" />

## <a name="summary"></a>Riepilogo

Questa guida viene spiegato come aggiungere e verificare le autorizzazioni in un dispositivo Android. Le differenze di funzionamento delle autorizzazioni tra le app di Android precedente (API livello < 23) e nuove app Android (22 > il livello API). È stato illustrato come eseguire i controlli delle autorizzazioni in fase di esecuzione in Android 6.0.


## <a name="related-links"></a>Collegamenti correlati

- [Elenco di autorizzazioni normale](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [App di esempio del runtime delle autorizzazioni](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [Gestione delle autorizzazioni di xamarin](https://developer.xamarin.com/recipes/android/general/projects/add_permissions_to_android_manifest)
