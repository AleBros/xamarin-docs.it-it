---
title: Autorizzazioni in Xamarin.Android
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 911f56026a1495099e81a542b30b280f26b6a9e1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025451"
---
# <a name="permissions-in-xamarinandroid"></a>Autorizzazioni in Xamarin.Android

## <a name="overview"></a>Panoramica

Le applicazioni Android vengono eseguite nella propria sandbox e, per motivi di sicurezza, non hanno accesso a determinate risorse di sistema o hardware nel dispositivo. L'utente deve concedere in modo esplicito l'autorizzazione all'app prima di poter usare queste risorse. Ad esempio, un'applicazione non può accedere al GPS su un dispositivo senza autorizzazione esplicita da parte dell'utente. Android genererà un `Java.Lang.SecurityException` se un'app tenta di accedere a una risorsa protetta senza autorizzazione.

Le autorizzazioni vengono dichiarate in **file AndroidManifest. XML** dallo sviluppatore dell'applicazione quando l'app viene sviluppata. Android dispone di due flussi di lavoro diversi per ottenere il consenso dell'utente per le autorizzazioni seguenti:

- Per le app destinate a Android 5,1 (livello API 22) o inferiore, la richiesta di autorizzazione si è verificata durante l'installazione dell'app. Se l'utente non ha concesso le autorizzazioni, l'app non verrà installata. Una volta installata l'app, non è possibile revocare le autorizzazioni ad eccezione della disinstallazione dell'app.
- A partire da Android 6,0 (API level 23), gli utenti hanno avuto maggiore controllo sulle autorizzazioni. possono concedere o revocare le autorizzazioni fino a quando l'app viene installata nel dispositivo. Questa schermata mostra le impostazioni di autorizzazione per l'app Google Contacts. Elenca le varie autorizzazioni e consente all'utente di abilitare o disabilitare le autorizzazioni:

![Schermata delle autorizzazioni di esempio](permissions-images/01-permissions-check.png) 

Le app Android devono verificare in fase di esecuzione per verificare se sono autorizzati ad accedere a una risorsa protetta. Se l'app non dispone delle autorizzazioni necessarie, deve effettuare richieste usando le nuove API fornite dal Android SDK per consentire all'utente di concedere le autorizzazioni. Le autorizzazioni sono divise in due categorie:

- Le **normali autorizzazioni** &ndash; si tratta di autorizzazioni che comportano un rischio di sicurezza limitato per la sicurezza o la privacy dell'utente. Android 6,0 concederà automaticamente le autorizzazioni normali al momento dell'installazione. Per un [elenco completo delle autorizzazioni normali](https://developer.android.com/guide/topics/permissions/normal-permissions.html), vedere la documentazione di Android.
- Le autorizzazioni **pericolose** &ndash; diversamente dalle normali autorizzazioni, le autorizzazioni pericolose sono quelle che proteggono la privacy o la sicurezza dell'utente. Queste devono essere esplicitamente concesse dall'utente. L'invio o la ricezione di un messaggio SMS è un esempio di azione che richiede un'autorizzazione pericolosa.

> [!IMPORTANT]
> La categoria a cui appartiene un'autorizzazione può cambiare nel tempo.  È possibile che un'autorizzazione che è stata categorizzata come "normale" possa essere elevata nei livelli API futuri in un'autorizzazione pericolosa.

Le autorizzazioni pericolose sono ulteriormente suddivise in [_gruppi di autorizzazioni_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups). Un gruppo di autorizzazioni conterrà le autorizzazioni correlate logicamente. Quando l'utente concede l'autorizzazione a un membro di un gruppo di autorizzazioni, Android concede automaticamente l'autorizzazione a tutti i membri di tale gruppo. Il gruppo di autorizzazioni [`STORAGE`](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE) , ad esempio, include le autorizzazioni `WRITE_EXTERNAL_STORAGE` e `READ_EXTERNAL_STORAGE`. Se l'utente concede l'autorizzazione per la `READ_EXTERNAL_STORAGE`, l'autorizzazione `WRITE_EXTERNAL_STORAGE` viene concessa automaticamente nello stesso momento.

Prima di richiedere una o più autorizzazioni, è consigliabile fornire una spiegazione razionale del motivo per cui l'applicazione richiede l'autorizzazione prima di richiedere l'autorizzazione. Quando l'utente riconosce la logica, l'app può richiedere l'autorizzazione all'utente. Comprendendo la logica, l'utente può prendere una decisione consapevole se desidera concedere l'autorizzazione e comprendere le ripercussioni in caso contrario. 

L'intero flusso di lavoro per la verifica e la richiesta delle autorizzazioni è noto come controllo _delle autorizzazioni di run-time_ e può essere riepilogato nel diagramma seguente: 

[grafico del flusso di controllo delle autorizzazioni di runtime![](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png#lightbox)

La libreria di supporto Android consente di eseguire il backporting di alcune delle nuove API per le autorizzazioni per le versioni precedenti di Android. Queste API sottoportate controlleranno automaticamente la versione di Android nel dispositivo, pertanto non è necessario eseguire ogni volta un controllo a livello di API.  

Questo documento illustra come aggiungere autorizzazioni a un'applicazione Xamarin.Android e in che modo le app destinate a Android 6,0 (API level 23) o versione successiva devono eseguire un controllo delle autorizzazioni di Runtime.

> [!NOTE]
> È possibile che le autorizzazioni per l'hardware possano influenzare il modo in cui l'app viene filtrata in base Google Play. Ad esempio, se l'app richiede l'autorizzazione per la fotocamera, Google Play non visualizzerà l'app nella Google Play Store su un dispositivo in cui non è installata una fotocamera.

<a name="requirements" />

## <a name="requirements"></a>Requisiti

Si consiglia vivamente che i progetti Xamarin.Android includano il pacchetto NuGet [Xamarin.Android. support. compat](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/) . Questo pacchetto backporting le API specifiche per le versioni precedenti di Android, offrendo un'interfaccia comune senza la necessità di verificare costantemente la versione di Android su cui è in esecuzione l'app.

## <a name="requesting-system-permissions"></a>Richiesta delle autorizzazioni di sistema

Il primo passaggio per lavorare con le autorizzazioni di Android è dichiarare le autorizzazioni nel file manifesto Android. Questa operazione deve essere eseguita indipendentemente dal livello API che l'app è destinazione.

Le app destinate a Android 6,0 o versione successiva non possono presupporre che, poiché l'utente ha concesso l'autorizzazione in un determinato momento nel passato, che l'autorizzazione sarà valida la volta successiva. Un'app destinata a Android 6,0 deve sempre eseguire un controllo delle autorizzazioni in fase di esecuzione. Per le app destinate a Android 5,1 o versioni precedenti non è necessario eseguire un controllo delle autorizzazioni in fase di esecuzione.

> [!NOTE]
> Le applicazioni devono richiedere solo le autorizzazioni necessarie.

### <a name="declaring-permissions-in-the-manifest"></a>Dichiarazione delle autorizzazioni nel manifesto

Le autorizzazioni vengono aggiunte a **file AndroidManifest. XML** con l'elemento `uses-permission`. Se, ad esempio, un'applicazione è in grado di individuare la posizione del dispositivo, sono necessarie le autorizzazioni per percorsi accurati e dei corsi. Al manifesto vengono aggiunti i due elementi seguenti: 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

È possibile dichiarare le autorizzazioni utilizzando il supporto dello strumento incorporato in Visual Studio:

1. Fare doppio clic su **Proprietà** nel **Esplora soluzioni** e selezionare la scheda **manifesto Android** nel finestra Proprietà:

    [![le autorizzazioni necessarie nella scheda Manifesto Android](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png#lightbox)

2. Se l'applicazione non dispone già di un file file AndroidManifest. XML, fare clic su non è stato **trovato alcun file AndroidManifest. XML. Fare clic per aggiungere uno** come illustrato di seguito:

    [![nessun messaggio file AndroidManifest. XML](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png#lightbox)

3. Selezionare le autorizzazioni necessarie per l'applicazione dall'elenco di **autorizzazioni necessarie** e salvare:

    [![le autorizzazioni della fotocamera di esempio selezionate](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

È possibile dichiarare le autorizzazioni utilizzando il supporto dello strumento incorporato in Visual Studio per Mac:

1. Fare doppio clic sul progetto nel **riquadro della soluzione** e selezionare **opzioni > Compila > applicazione Android**:

    [![sezione autorizzazioni necessarie visualizzata](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png#lightbox)

2. Fare clic sul pulsante **Aggiungi manifesto Android** se il progetto non dispone già di un **file AndroidManifest. XML**:

    [![manca il manifesto Android del progetto](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png#lightbox)

3. Selezionare le autorizzazioni necessarie per l'applicazione dall'elenco **delle autorizzazioni necessarie** e fare clic su **OK**:

    [![le autorizzazioni della fotocamera di esempio selezionate](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png#lightbox)
    
-----

Xamarin.Android aggiungerà automaticamente alcune autorizzazioni in fase di compilazione per eseguire il debug delle build. Questo renderà più semplice il debug dell'applicazione. In particolare, vengono `INTERNET` e `READ_EXTERNAL_STORAGE`due autorizzazioni rilevanti. Queste autorizzazioni di impostazione automatica non verranno visualizzate nell'elenco **delle autorizzazioni necessarie** . Le build di rilascio, tuttavia, usano solo le autorizzazioni impostate in modo esplicito nell'elenco di **autorizzazioni necessarie** . 

Per le app destinate a Android 5.1 (livello API 22) o inferiore, non è necessario eseguire altre operazioni. Le app che verranno eseguite in Android 6,0 (API 23 Level 23) o versioni successive dovrebbero passare alla sezione successiva su come eseguire i controlli delle autorizzazioni in fase di esecuzione. 

### <a name="runtime-permission-checks-in-android-60"></a>Controlli delle autorizzazioni di runtime in Android 6,0

Il metodo `ContextCompat.CheckSelfPermission` (disponibile con la libreria di supporto Android) viene usato per verificare se è stata concessa un'autorizzazione specifica. Questo metodo restituirà un [`Android.Content.PM.Permission`](xref:Android.Content.PM.Permission) enum con uno dei due valori seguenti:

- **`Permission.Granted`** &ndash; è stata concessa l'autorizzazione specificata.
- **`Permission.Denied`** &ndash; non è stata concessa l'autorizzazione specificata.

Questo frammento di codice è un esempio di come verificare l'autorizzazione della fotocamera in un'attività: 

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

È consigliabile informare l'utente del motivo per cui un'autorizzazione è necessaria per un'applicazione in modo da poter prendere una decisione informata per concedere l'autorizzazione. Un esempio è costituito da un'app che prende le foto e i tag geografici. È chiaro per l'utente che l'autorizzazione della fotocamera è necessaria, ma potrebbe non essere chiaro perché l'app necessita anche del percorso del dispositivo. La logica dovrebbe visualizzare un messaggio per consentire all'utente di comprendere il motivo per cui l'autorizzazione per la posizione è auspicabile e che l'autorizzazione della fotocamera sia obbligatoria.

Il metodo `ActivityCompat.ShouldShowRequestPermissionRationale` viene usato per determinare se la logica deve essere visualizzata all'utente. Questo metodo restituirà `true` se deve essere visualizzata la logica per una data autorizzazione. Questo screenshot mostra un esempio di snackbar visualizzato da un'applicazione che spiega perché l'app deve conoscerne il percorso:

![Spiegazione razionale per la posizione](permissions-images/07-rationale-snackbar.png) 

Se l'utente concede l'autorizzazione, è necessario chiamare il metodo `ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)`. Questo metodo richiede i parametri seguenti:

- **attività** &ndash; questa è l'attività che richiede le autorizzazioni e deve essere aggiornata da Android dei risultati.
- **autorizzazioni** &ndash; un elenco delle autorizzazioni richieste.
- **requestCode** &ndash; un valore integer utilizzato per trovare la corrispondenza con i risultati della richiesta di autorizzazione a una chiamata di `RequestPermissions`. Questo valore deve essere maggiore di zero.

Questo frammento di codice è un esempio dei due metodi illustrati. In primo luogo, viene eseguito un controllo per determinare se deve essere visualizzata la logica di autorizzazione. Se la logica deve essere mostrata, viene visualizzata una snackbar con la logica. Se l'utente fa clic su **OK** nel snackbar, l'app richiederà le autorizzazioni. Se l'utente non accetta la logica, l'app non deve continuare a richiedere le autorizzazioni. Se la logica non viene visualizzata, l'attività richiederà l'autorizzazione:

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

`RequestPermission` può essere chiamato anche se l'utente ha già concesso l'autorizzazione. Le chiamate successive non sono necessarie, ma forniscono all'utente la possibilità di confermare o revocare l'autorizzazione. Quando viene chiamato `RequestPermission`, il controllo viene passato al sistema operativo, che visualizzerà un'interfaccia utente per l'accettazione delle autorizzazioni:  

![Finestra di dialogo permssion](permissions-images/08-location-permission-dialog.png)

Al termine dell'utente, Android restituirà i risultati all'attività tramite un metodo di callback `OnRequestPermissionResult`. Questo metodo fa parte dell'interfaccia `ActivityCompat.IOnRequestPermissionsResultCallback` che deve essere implementata dall'attività. Questa interfaccia ha un solo metodo, `OnRequestPermissionsResult`, che verrà richiamato da Android per informare l'attività delle scelte dell'utente. Se l'utente ha concesso l'autorizzazione, l'app può continuare a usare la risorsa protetta. Di seguito è riportato un esempio di come implementare `OnRequestPermissionResult`: 

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
            Snackbar.Make(layout, Resource.String.permission_available_camera, Snackbar.LengthShort).Show();            
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

Questa guida ha illustrato come aggiungere e verificare le autorizzazioni in un dispositivo Android. Le differenze nella modalità di funzionamento delle autorizzazioni tra le app Android precedenti (livello API < 23) e le nuove app Android (livello API > 22). È stato illustrato come eseguire i controlli delle autorizzazioni di run-time in Android 6,0.

## <a name="related-links"></a>Collegamenti correlati

- [Elenco di autorizzazioni normali](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [App di esempio per le autorizzazioni di runtime](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [Gestione delle autorizzazioni in Xamarin.Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)
