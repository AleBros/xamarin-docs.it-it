---
title: Tramite il provider di contenuti di contatti
ms.topic: article
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/22/2018
ms.openlocfilehash: 730cc1f815641d79350784790e3b33b743d1aebe
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="using-the-contacts-contentprovider"></a>Tramite il provider di contenuti di contatti

Codice che usa accedere ai dati esposti da un `ContentProvider` non richiede un riferimento alla `ContentProvider` classe affatto. Invece utilizzato per creare un cursore tramite i dati esposti da un Uri di `ContentProvider`. Android utilizza l'Uri per la ricerca di sistema per l'applicazione che espone un `ContentProvider` con tale identificatore. L'Uri è una stringa, in genere in un formato DNS inverso, ad esempio `com.android.contacts/data`.

Anziché eseguire agli sviluppatori di ricordare questa stringa, di Android *contatti* provider espone i metadati nel `android.provider.ContactsContract` classe. Questa classe viene utilizzata per determinare l'Uri del `ContentProvider` nonché i nomi delle tabelle e colonne che è possibile eseguire query.

Alcuni tipi di dati richiedono anche autorizzazioni speciali per accedere. L'elenco di contatti predefinito richiede il `android.permission.READ_CONTACTS` disporre dell'autorizzazione per la **AndroidManifest.xml** file.

Esistono tre modi per creare un cursore dall'Uri:

1. **ManagedQuery()** &ndash; la scelta migliore in Android 2.3 (API livello 10) e versioni precedenti, un `ManagedQuery` restituisce un cursore e gestisce automaticamente anche l'aggiornamento dei dati e di chiusura del cursore. Questo metodo è deprecato in Android 3.0 (API livello 11).

1. **ContentResolver.Query()** &ndash; restituisce un cursore non gestito, ovvero deve essere aggiornato e chiuso in modo esplicito nel codice.

1. **CursorLoader(). LoadInBackground()** &ndash; introdotto in Android 3.0 (API livello 11), `CursorLoader` è ora il modo migliore per utilizzare un `ContentProvider` . `CursorLoader` le query un `ContentResolver` su un thread in background, pertanto l'interfaccia utente non è bloccata.
   Questa classe sono accessibili nelle versioni precedenti di Android usando la libreria di compatibilità v4.


Ognuno di questi metodi ha lo stesso set di base di input:

-  **URI** &ndash; il nome completo del `ContentProvider` .
-  **Proiezione** &ndash; specificando le colonne da selezionare per il cursore.
-  **Selezione** &ndash; simile a un database SQL `WHERE` clausola.
-  **SelectionArgs** &ndash; parametri deve essere sostituito nella selezione.
-  **SortOrder** &ndash; colonne da ordinare.



## <a name="creating-inputs-for-a-query"></a>Creazione di input per una Query

Il `ContactsProvider` codice di esempio esegue una semplice query sul provider contatti predefinito di Android. Non è necessario conoscere i nomi di colonna o Uri effettivi - tutte le informazioni necessarie per eseguire una query i contatti `ContentProvider` è disponibile come costanti esposte dalla `ContactsContract` classe.

Indipendentemente da quale metodo viene usato per recuperare il cursore, come illustrato nel come parametri vengono utilizzati questi stessi oggetti di *ContactsProvider/ContactsAdapter.cs* file:

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

Per questo esempio, il `selection`, `selectionArgs` e `sortOrder` verranno ignorati, impostarli su `null`.



## <a name="creating-a-cursor-from-a-content-provider-uri"></a>Creazione di un cursore da un Uri del Provider di contenuti

Dopo avere creati gli oggetti parametro, possono essere utilizzati in uno dei tre modi seguenti:



### <a name="using-a-managed-query"></a>Utilizzo di una Query gestita

Applicazioni destinate a Android 2.3 (API livello 10) o versioni precedenti devono utilizzare questo metodo:

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

Questo cursore verrà gestito da Android in modo non è necessario chiuderlo.



### <a name="using-contentresolver"></a>Utilizzando ContentResolver

L'accesso a `ContentResolver` direttamente per ottenere un cursore su un `ContentProvider` può essere come segue:

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

Questo cursore non è gestito, pertanto deve essere chiuso quando non è più necessario.
Assicurarsi che il codice consente di chiudere un cursore aperto, in caso contrario si verificherà un errore.

```csharp
cursor.Close();
```

In alternativa, è possibile chiamare `StartManagingCursor()` e `StopManagingCursor()` per gestire il cursore. I cursori gestiti sono disattivati e nuovamente eseguita una query quando le attività sono arrestate e riavviate automaticamente.



### <a name="using-cursorloader"></a>Utilizzando CursorLoader

Le applicazioni compilate per Android 3.0 (API livello 11) o più recente deve utilizzare questo metodo:

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

Il `CursorLoader` assicura che tutte le operazioni del cursore vengono eseguite in un thread in background e in modo intelligente riutilizzare un cursore esistente tra istanze di attività quando un'attività viene riavviata (ad esempio a causa di una modifica della configurazione) piuttosto che ricaricare i dati nuovamente.

Inoltre è possono utilizzare le versioni precedenti di Android di `CursorLoader` classe utilizzando il [v4 supporto librerie](http://developer.android.com/tools/support-library/index.html).



## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>La visualizzazione dei dati di cursore con un Adapter personalizzato

Per visualizzare l'immagine di contatto si userà un adapter personalizzato, in modo che è possibile risolvere manualmente il `PhotoId` riferimento a un percorso del file di immagine.

Per visualizzare i dati con un adapter personalizzato, nell'esempio viene utilizzato un `CursorLoader` per recuperare tutti i dati di contatto in una raccolta in locale il **FillContacts** metodo **ContactsProvider/ContactsAdapter.cs**:

```csharp
void FillContacts ()
{
   var uri = ContactsContract.Contacts.ContentUri;
   string[] projection = {
       ContactsContract.Contacts.InterfaceConsts.Id,
       ContactsContract.Contacts.InterfaceConsts.DisplayName,
       ContactsContract.Contacts.InterfaceConsts.PhotoId
  };
   // CursorLoader introduced in Honeycomb (3.0, API11)
   var loader = new CursorLoader(activity, uri, projection, null, null, null);
   var cursor = (ICursor)loader.LoadInBackground();
   contactList = new List<Contact> ();
   if (cursor.MoveToFirst ()) {
      do {
          contactList.Add (new Contact{
              Id = cursor.GetLong (cursor.GetColumnIndex (projection [0])),
              DisplayName = cursor.GetString (cursor.GetColumnIndex (projection [1])),
              PhotoId = cursor.GetString (cursor.GetColumnIndex (projection [2]))
          });
       } while (cursor.MoveToNext());
   }
}
```

Quindi implementare metodi di BaseAdapter usando il `contactList` insieme. L'adapter viene implementato come sarebbe con qualsiasi altra raccolta &ndash; non è qui alcuna gestione particolare poiché i dati proviene da un `ContentProvider`:

```csharp
Activity activity;
public ContactsAdapter (Activity activity)
{
   this.activity = activity;
   FillContacts ();
}
public override int Count {
   get { return contactList.Count; }
}
public override Java.Lang.Object GetItem (int position)
{
  return null; // could wrap a Contact in a Java.Lang.Object to return it here if needed
}
public override long GetItemId (int position)
{
   return contactList [position].Id;
}
public override View GetView (int position, View convertView, ViewGroup parent)
{
   var view = convertView ?? activity.LayoutInflater.Inflate (Resource.Layout.ContactListItem, parent, false);
   var contactName = view.FindViewById<TextView> (Resource.Id.ContactName);
   var contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
   contactName.Text = contactList [position].DisplayName;
   if (contactList [position].PhotoId == null) {
       contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
       contactImage.SetImageResource (Resource.Drawable.ContactImage);
   } else {
       var contactUri = ContentUris.WithAppendedId (ContactsContract.Contacts.ContentUri, contactList [position].Id);
       var contactPhotoUri = Android.Net.Uri.WithAppendedPath (contactUri, Contacts.Photos.ContentDirectory);
       contactImage.SetImageURI (contactPhotoUri);
   }
   return view;
}
```

(Se presente), viene visualizzata l'immagine usando l'Uri del file di immagine nel dispositivo. L'applicazione è simile al seguente:

[![Schermata dell'app la visualizzazione dei contatti in un controllo ListView; viene visualizzata un'immagine a sinistra di una voce](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

Usando un modello di codice simile, l'applicazione può accedere a un'ampia gamma di dati di sistema, tra cui l'utente foto, video e musica.
Alcuni tipi di dati richiedono autorizzazioni speciali a essere richiesti del progetto **AndroidManifest.xml**.



## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>La visualizzazione dei dati di cursore con un SimpleCursorAdapter

Il cursore potrebbe inoltre essere visualizzato con un `SimpleCursorAdapter` (anche se verrà visualizzato solo il nome, non la foto). Questo codice viene illustrato come utilizzare un `ContentProvider` con `SimpleCursorAdapter` (questo codice non vengono visualizzati nell'esempio):

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName
};
var loader = new CursorLoader (this, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
var fromColumns = new string[] {ContactsContract.Contacts.InterfaceConsts.DisplayName};
var toControlIds = new int[] {Android.Resource.Id.Text1};
adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlsIds);
listView.Adapter = adapter;
```

Consultare il [controlli ListView e schede](~/android/user-interface/layouts/list-view/index.md) per ulteriori informazioni sull'implementazione `SimpleCursorAdapter`.


## <a name="related-links"></a>Collegamenti correlati

- [Demo di ContactsAdapter (esempio)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
