---
title: Uso del ContentProvider Contacts
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/22/2018
ms.openlocfilehash: 48bb334e7e400d57e7eddc23b0b4ff183a7eba9b
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669414"
---
# <a name="using-the-contacts-contentprovider"></a>Uso del ContentProvider Contacts

Codice che usa accedere ai dati esposti da un `ContentProvider` non richiede un riferimento al `ContentProvider` classe affatto. Al contrario, un Uri viene usato per creare un cursore tramite i dati esposti dal `ContentProvider`. Android Usa l'Uri per la ricerca di sistema per l'applicazione che espone un `ContentProvider` con tale identificatore. L'Uri è una stringa, in genere in un formato DNS inverso, ad esempio `com.android.contacts/data`.

Anziché fare in modo che gli sviluppatori memorizzare questa stringa, Android *contatti* provider espone i relativi metadati nel `android.provider.ContactsContract` classe. Questa classe viene utilizzata per determinare l'Uri del `ContentProvider` , nonché i nomi delle tabelle e colonne che è possibile eseguire query.

Alcuni tipi di dati richiedono anche un'autorizzazione speciale per accedere. L'elenco di contatti predefinito richiede il `android.permission.READ_CONTACTS` l'autorizzazione per il **androidmanifest. XML** file.

Esistono tre modi per creare un cursore dall'Uri:

1. **ManagedQuery()** &ndash; approccio preferito in Android 2.3 (livello API 10) e versioni precedenti, un `ManagedQuery` restituisce un cursore e gestisce automaticamente anche l'aggiornamento dei dati e la chiusura del cursore. Questo metodo è deprecato in Android 3.0 (API livello 11).

1. **ContentResolver.Query()** &ndash; restituisce un cursore non gestito, ovvero deve essere aggiornato e chiuso in modo esplicito nel codice.

1. **CursorLoader(). LoadInBackground()** &ndash; introdotte in Android 3.0 (API livello 11), `CursorLoader` è ora il modo migliore per utilizzare un `ContentProvider` . `CursorLoader` le query un `ContentResolver` su un thread in background in modo che l'interfaccia utente non è bloccato.
   Questa classe sono accessibili nelle versioni precedenti di Android usando la libreria di compatibilità v4.


Ognuno di questi metodi ha lo stesso set di base di input:

-  **URI** &ndash; il nome completo del `ContentProvider` .
-  **Proiezione** &ndash; specificare le colonne da selezionare per il cursore.
-  **Selezione** &ndash; simile a un database SQL `WHERE` clausola.
-  **SelectionArgs** &ndash; parametri deve essere sostituito nella selezione.
-  **SortOrder** &ndash; colonne da ordinare.



## <a name="creating-inputs-for-a-query"></a>Creazione di input per una Query

Il `ContactsProvider` codice di esempio esegue una query molto semplice sul provider contatti incorporato di Android. Non è necessario conoscere i nomi di colonna o Uri effettivi - tutte le informazioni necessarie per eseguire query sui contatti `ContentProvider` è disponibile come costanti esposte dal `ContactsContract` classe.

Indipendentemente da quale metodo viene usato per recuperare il cursore, questi stessi oggetti vengono utilizzati come parametri, come illustrato nella *ContactsProvider/ContactsAdapter.cs* file:

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

Per questo esempio, il `selection`, `selectionArgs` e `sortOrder` verranno ignorate impostandoli su `null`.



## <a name="creating-a-cursor-from-a-content-provider-uri"></a>Creazione di un cursore da un Uri di Provider di contenuti

Dopo avere creati gli oggetti parametro, possono essere utilizzati in uno dei tre modi seguenti:



### <a name="using-a-managed-query"></a>Utilizzo di una Query gestita

Le applicazioni destinate a Android 2.3 (livello API 10) o versioni precedenti devono usare questo metodo:

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

Questo cursore verrà gestito da Android in modo che non è necessario chiuderlo.



### <a name="using-contentresolver"></a>Usando ContentResolver

L'accesso a `ContentResolver` direttamente per ottenere un cursore su un `ContentProvider` può essere simile al seguente:

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

Questo cursore non è gestito, in modo che deve essere chiuso quando non è più necessario.
Assicurarsi che il codice consente di chiudere un cursore aperto, in caso contrario si verificherà un errore.

```csharp
cursor.Close();
```

In alternativa, è possibile chiamare `StartManagingCursor()` e `StopManagingCursor()` per gestire il cursore. I cursori gestiti automaticamente disattivati e nuovamente la query quando le attività sono arrestate e riavviate.



### <a name="using-cursorloader"></a>Usando CursorLoader

Le applicazioni compilate per Android 3.0 (API livello 11) o versioni successive devono utilizzare questo metodo:

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

Il `CursorLoader` garantisce che tutte le operazioni del cursore vengono eseguite in un thread in background e possono in modo intelligente usare nuovamente un cursore esistente tra istanze di attività quando un'attività viene riavviata (ad esempio a causa di una modifica della configurazione) piuttosto che ricaricare i dati anche in questo caso.

Le versioni precedenti di Android possono anche usare il `CursorLoader` classe usando il [v4 supporto librerie](https://developer.android.com/tools/support-library/index.html).



## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>Visualizzazione dei dati di cursore con un Adapter personalizzato

Per visualizzare l'immagine di contatto si userà un adapter personalizzato, in modo che è possibile risolvere manualmente il `PhotoId` riferimento a un percorso di file di immagine.

Per visualizzare i dati con un adapter personalizzato, l'esempio Usa un' `CursorLoader` per recuperare tutti i dati di contatto in una raccolta locale nel **FillContacts** metodo **ContactsProvider/ContactsAdapter.cs**:

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

Quindi implementare metodi di BaseAdapter utilizzando il `contactList` raccolta. L'adapter viene implementato proprio come con qualsiasi altra raccolta sarebbe &ndash; non è presente alcuna gestione speciale qui poiché i dati di origine sono un `ContentProvider`:

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

Viene visualizzata l'immagine (se presente) usando l'Uri del file di immagine nel dispositivo. L'applicazione è simile alla seguente:

[![Screenshot dell'app per la visualizzazione di contatti in un ListView; viene visualizzata un'immagine a sinistra di una voce](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

Usa un modello di codice simile, l'applicazione possa accedere a un'ampia gamma di dati di sistema, tra cui foto, video e musica dell'utente.
Alcuni tipi di dati richiedono autorizzazioni speciali che verrà richiesto di progetto **androidmanifest. XML**.



## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>Visualizzazione dei dati di cursore con un SimpleCursorAdapter

Il cursore è stato possibile visualizzare anche con un `SimpleCursorAdapter` (anche se solo il nome verrà visualizzato, non la foto). Questo codice illustra come usare un `ContentProvider` con `SimpleCursorAdapter` (questo codice non sia presente nell'esempio):

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

Vedere le [ListView e adapter](~/android/user-interface/layouts/list-view/index.md) per altre informazioni sull'implementazione `SimpleCursorAdapter`.


## <a name="related-links"></a>Collegamenti correlati

- [ContactsAdapter Demo (esempio)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
