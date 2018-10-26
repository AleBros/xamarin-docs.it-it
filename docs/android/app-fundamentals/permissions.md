---
title: Autorizzazioni In xamarin. Android
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: a5295ef05eae213206fada135801bb4fadbcf6dd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106338"
---
# <a name="permissions-in-xamarinandroid"></a>Autorizzazioni In xamarin. Android


## <a name="overview"></a>Panoramica

Le applicazioni Android vengono eseguite nel proprio ambiente sandbox e per la sicurezza motivi non hanno accesso a determinate risorse di sistema o hardware nel dispositivo. L'utente deve concedere esplicitamente l'autorizzazione per l'app prima che possono usare queste risorse. Ad esempio, un'applicazione non può accedere il GPS in un dispositivo senza autorizzazione esplicita da parte dell'utente. Android genererà un `Java.Lang.SecurityException` se un'app tenta di accedere a una risorsa protetta senza autorizzazione.

Le autorizzazioni vengono dichiarate nel **androidmanifest. XML** dallo sviluppatore dell'applicazione quando l'app è sviluppata. Android offre due diversi flussi di lavoro per ottenere il consenso dell'utente per tali autorizzazioni:
 
* Per le app di destinazione Android 5.1 (livello API 22) o versione precedente, la richiesta di autorizzazione, si è verificato durante l'installazione dell'app. Se l'utente non ha concesso le autorizzazioni, l'app potrebbe non essere installato. Dopo aver installato l'app, non è possibile revocare le autorizzazioni eccetto disinstallando l'app.
* A partire da Android 6.0 (livello API 23), gli utenti hanno avuti maggiore controllo sulle autorizzazioni. è possibile concedere o revocare autorizzazioni fino a quando l'app viene installata nel dispositivo. In questo screenshot Mostra le impostazioni di autorizzazione per l'app dei contatti di Google. Elenca le diverse autorizzazioni e consente all'utente di abilitare o disabilitare le autorizzazioni:

![Schermata di esempio di autorizzazioni](permissions-images/01-permissions-check.png) 

In fase di esecuzione per verificare se dispongono dell'autorizzazione per accedere a una risorsa protetta devono controllare le app Android. Se l'app non dispone delle autorizzazioni, quindi deve assicurarsi usando le nuove API fornite da Android SDK per l'utente per concedere le autorizzazioni richieste. Le autorizzazioni sono suddivisi in due categorie:

* **Le autorizzazioni Normal** &ndash; queste sono autorizzazioni cui mettere a rischio di sicurezza poco la sicurezza o privacy dell'utente. Android 6.0 concederà autorizzazioni normale automaticamente al momento dell'installazione. Vedere la documentazione di Android per un [elenco completo delle autorizzazioni normali](https://developer.android.com/guide/topics/permissions/normal-permissions.html).
* **Autorizzazioni pericolose** &ndash; a differenza dei normali autorizzazioni, autorizzazioni pericolose sono quelli che proteggono sicurezza o privacy dell'utente. Questi devono essere concessa esplicitamente dall'utente. Inviare o ricevere un messaggio SMS è un esempio di un'azione che richiede un'autorizzazione pericolosa.

> [!IMPORTANT]
> La categoria a cui appartiene un'autorizzazione può cambiare nel tempo.  È possibile che un'autorizzazione che è stata classificata come un'autorizzazione "normale" può essere elevati in futuro i livelli API da un'autorizzazione pericolosa.

Autorizzazioni pericolose sono ulteriormente suddivisi in Sub [ _gruppi di autorizzazione_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups). Un gruppo l'autorizzazione includerà le autorizzazioni che sono correlate logicamente. Quando l'utente concede l'autorizzazione a un membro di un gruppo l'autorizzazione, Android automaticamente concessa l'autorizzazione a tutti i membri di tale gruppo. Ad esempio, il [ `STORAGE` ](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE) gruppo l'autorizzazione contiene entrambi le `WRITE_EXTERNAL_STORAGE` e `READ_EXTERNAL_STORAGE` autorizzazioni. Se l'utente concede l'autorizzazione per `READ_EXTERNAL_STORAGE`, quindi il `WRITE_EXTERNAL_STORAGE` autorizzazione viene concessa automaticamente nello stesso momento.

Prima di richiedere una o più autorizzazioni, è consigliabile fornire una spiegazione logica per quanto riguarda il motivo per cui l'app richiede l'autorizzazione prima di richiedere l'autorizzazione. Una volta che l'utente comprenda la logica alla base, l'app può richiedere l'autorizzazione da parte dell'utente. Comprendendo la base logica, l'utente possa prendere una decisione informata se si desidera concedere l'autorizzazione e comprendere le conseguenze in caso contrario. 

L'intero flusso di lavoro di controllo e la richiesta di autorizzazioni è noto come un _le autorizzazioni di runtime_ controllare e può essere riepilogato nel diagramma seguente: 

[![Diagramma di flusso di controllo dell'autorizzazione in fase di esecuzione](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png#lightbox)

La libreria di supporto Android backports alcune delle nuove API per le autorizzazioni per le versioni precedenti di Android. Queste API eseguito il backport controlla automaticamente la versione di Android nel dispositivo in modo che non è necessario eseguire un controllo a livello di API ogni volta.  

Questo documento illustra come aggiungere autorizzazioni a un'applicazione xamarin. Android e in che modo le app destinate a Android 6.0 (livello API 23) o versione successiva deve eseguire una verifica delle autorizzazioni in fase di esecuzione.


> [!NOTE]
> È possibile che le autorizzazioni per l'hardware possono influire sul modo in cui l'app viene filtrato da Google Play. Ad esempio, se l'app richiede l'autorizzazione per la fotocamera, quindi Google Play non visualizzerà l'app nel Store di Google Play in un dispositivo che non è installata una webcam.


<a name="requirements" />

## <a name="requirements"></a>Requisiti

È consigliabile che i progetti xamarin. Android includono la [Xamarin.Android.Support.Compat](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/) pacchetto NuGet. Questa autorizzazione di eseguire il backporting will pacchetto API specifiche per le versioni precedenti di Android, garantendo una interfaccia costantemente senza la necessità di controllare la versione di Android che viene eseguita l'app.


## <a name="requesting-system-permissions"></a>La richiesta di autorizzazioni di sistema

Il primo passaggio nell'utilizzo delle autorizzazioni Android consiste nel dichiarare che le autorizzazioni in Android il file manifesto. Questa operazione deve essere eseguita indipendentemente dal livello di API che l'app è destinata ad.

App destinate a Android 6.0 o versione successiva non possono presupporre che poiché l'utente è concessa l'autorizzazione a un certo punto nel passato, che l'autorizzazione sarà valida la volta successiva. Un'applicazione destinata ad Android 6.0 è necessario eseguire sempre un controllo di autorizzazione di runtime. Le app destinate a Android 5.1 o inferiore non sono necessario eseguire una verifica delle autorizzazioni in fase di esecuzione.

> [!NOTE]
> Le applicazioni devono richiedere solo le autorizzazioni necessarie.


### <a name="declaring-permissions-in-the-manifest"></a>La dichiarazione di autorizzazioni nel manifesto

Vengono aggiunte le autorizzazioni per il **androidmanifest. XML** con il `uses-permission` elemento. Ad esempio, se un'applicazione consiste nell'individuare la posizione del dispositivo, richiede correttamente e le autorizzazioni del percorso di corsi. I due elementi seguenti vengono aggiunte al manifesto: 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

È possibile dichiarare le autorizzazioni usando il supporto dello strumento integrato in Visual Studio:

1. Fare doppio clic su **delle proprietà** nel **Esplora soluzioni** e selezionare il **manifesto Android** scheda nella finestra Proprietà:

    [![Autorizzazioni necessarie nella scheda manifesto Android](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png#lightbox)

2. Se l'applicazione non possieda già un file androidmanifest. XML, fare clic su **androidmanifest. XML non trovato. Fare clic per aggiungere uno** come illustrato di seguito:

    [![Nessun messaggio androidmanifest. Xml](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png#lightbox)

3. Selezionare le autorizzazioni necessarie all'applicazione dal **autorizzazioni necessarie** elencare e salvare:

    [![Autorizzazioni di fotocamera di esempio selezionate](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

È possibile dichiarare le autorizzazioni usando il supporto dello strumento incorporato in Visual Studio per Mac:

1. Fare doppio clic sul progetto nel **riquadro della soluzione** e selezionare **Opzioni > compilazione > applicazione Android**:

    [![Sezione di autorizzazioni richiesti mostrato](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png#lightbox)

2. Fare clic sui **Aggiungi manifesto Android** se il progetto non dispone già di un **androidmanifest. XML**:

    [![Manca il manifesto Android del progetto](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png#lightbox)

3. Selezionare le autorizzazioni necessarie all'applicazione dal **autorizzazioni necessarie** elenco e fare clic su **OK**:

    [![Autorizzazioni di fotocamera di esempio selezionate](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png#lightbox)
    
-----

Xamarin. Android aggiungerà automaticamente alcune autorizzazioni in fase di compilazione per le compilazioni di Debug. In questo modo il debug dell'applicazione più semplice. In particolare, sono due le autorizzazioni rilevanti `INTERNET` e `READ_EXTERNAL_STORAGE`. Questi automaticamente-impostare le autorizzazioni non verranno visualizzati da attivare per il **autorizzazioni necessarie** elenco. Build di rilascio, tuttavia, usare solo le autorizzazioni impostate in modo esplicito nel **autorizzazioni necessarie** elenco. 

Per le app destinate a 5.1 Android (livello API 22) o versione precedente, non è nient'altro che deve essere eseguita. Le app che verranno eseguito in Android 6.0 (livello API 23 23) o versione successiva devono continuare con la sezione successiva su come eseguire la fase di esecuzione controllo dell'autorizzazione. 


### <a name="runtime-permission-checks-in-android-60"></a>Controlli delle autorizzazioni di runtime in Android 6.0

Il `ContextCompat.CheckSelfPermission` metodo (disponibile con la libreria di supporto Android) viene usato per verificare se sia stata concessa un'autorizzazione specifica. Questo metodo restituisce un [ `Android.Content.PM.Permission` ](https://developer.xamarin.com/api/type/Android.Content.PM.Permission/) enum con uno dei due valori:

* **`Permission.Granted`** &ndash; È stata concessa l'autorizzazione specificata.
* **`Permission.Denied`** &ndash; Non è stata concessa l'autorizzazione specificata.

Questo frammento di codice è un esempio di come controllo di autorizzazione della fotocamera in un'attività: 

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

È consigliabile informare l'utente per quanto riguarda il motivo per cui un'autorizzazione è necessaria per un'applicazione per consentire una decisione consapevole può essere effettuata per concedere l'autorizzazione. Un esempio di questo potrebbe essere un'app che accetta le foto e geo-tags li. È chiaro per l'utente che è necessaria l'autorizzazione di fotocamera, ma potrebbe non essere chiaro il motivo per cui l'app deve anche la posizione del dispositivo. La base logica deve visualizzare un messaggio di aiuto all'utente di comprendere il motivo per cui l'autorizzazione di posizione è auspicabile e che è necessaria l'autorizzazione di fotocamera.

Il `ActivityCompat.ShouldShowRequestPermissionRational` metodo viene utilizzato per determinare se la base logica deve essere visualizzata all'utente. Questo metodo restituirà `true` se deve essere visualizzata la base logica per un'autorizzazione specificata. Questo screenshot Mostra un esempio di un Snackbar visualizzata da un'applicazione che spiega il motivo per cui l'app deve conoscere il percorso del dispositivo:

![Motivi per percorso](permissions-images/07-rationale-snackbar.png) 

Se l'utente concede l'autorizzazione, il `ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)` deve chiamare il metodo. Questo metodo richiede i parametri seguenti:

* **impegno** &ndash; corrisponderà all'attività che richiede le autorizzazioni e deve essere informato da Android dei risultati.
* **le autorizzazioni** &ndash; un elenco delle autorizzazioni richieste.
* **requestCode** &ndash; valore intero che verrà usato per confrontare i risultati della richiesta dell'autorizzazione per un `RequestPermissions` chiamare. Questo valore deve essere maggiore di zero.

Questo frammento di codice è un esempio dei due metodi che sono stati trattati. Prima di tutto viene verificato per determinare se la logica alla base dell'autorizzazione deve essere visualizzato. Se la logica alla base è visualizzato, viene visualizzato un Snackbar con la logica alla base. Se l'utente sceglie **OK** in Snackbar, quindi l'app richiederà le autorizzazioni. Se l'utente non accetta la logica alla base, l'app non deve continuare a richiedere le autorizzazioni. Se non viene visualizzata la logica alla base, l'attività verrà richiesta l'autorizzazione:

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

`RequestPermission` può essere chiamato anche se l'utente ha già concesso l'autorizzazione. Le chiamate successive non sono necessari, ma forniscono all'utente la possibilità di confermare l'autorizzazione (o revoca). Quando si `RequestPermission` viene chiamato, controllo viene passato al sistema operativo, che consentirà di visualizzare un'interfaccia utente per accettare le autorizzazioni:  

![Finestra di dialogo autorizzazioni limitate sono](permissions-images/08-location-permission-dialog.png)

Al termine, l'utente Android verranno restituiti i risultati all'attività tramite un metodo di callback, `OnRequestPermissionResult`. Questo metodo fa parte dell'interfaccia `ActivityCompat.IOnRequestPermissionsResultCallback` che deve essere implementata dall'attività. Questa interfaccia ha un solo metodo, `OnRequestPermissionsResult`, che verrà richiamato da Android per informare l'attività delle scelte dell'utente. Se l'utente ha concesso l'autorizzazione, l'app può procedere e usare la risorsa protetta. Un esempio di come implementare `OnRequestPermissionResult` è illustrato di seguito: 

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


## <a name="summary"></a>Riepilogo

Questa guida ha illustrato come aggiungere e verificare le autorizzazioni in un dispositivo Android. Le differenze di funzionamento delle autorizzazioni tra le app di Android precedenti (API livello 23 <) e di nuove app per Android (API livello 22 >). È stato illustrato come eseguire controlli delle autorizzazioni in fase di esecuzione in Android 6.0.


## <a name="related-links"></a>Collegamenti correlati

- [Elenco di autorizzazioni normali](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [App di esempio di autorizzazioni di runtime](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [Gestione delle autorizzazioni in xamarin. Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)
