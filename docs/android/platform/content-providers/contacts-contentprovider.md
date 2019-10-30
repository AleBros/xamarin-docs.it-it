---
title: Uso del ContentProvider Contacts
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/22/2018
ms.openlocfilehash: fca57b7af34ae2b28dda9bf20a95183138cbc641
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020536"
---
# <a name="using-the-contacts-contentprovider"></a>Uso del ContentProvider Contacts

Il codice che usa i dati di accesso esposti da un `ContentProvider` non richiede alcun riferimento alla classe `ContentProvider`. Viene invece utilizzato un URI per creare un cursore sui dati esposti dal `ContentProvider`. Android usa l'URI per cercare nel sistema l'applicazione che espone un `ContentProvider` con tale identificatore. L'URI è una stringa, in genere in un formato DNS inverso, ad esempio `com.android.contacts/data`.

Anziché far ricordare a questa stringa gli sviluppatori, il provider di *contatti* Android ne espone i metadati nella classe `android.provider.ContactsContract`. Questa classe viene utilizzata per determinare l'URI del `ContentProvider` nonché i nomi delle tabelle e delle colonne su cui è possibile eseguire una query.

Alcuni tipi di dati richiedono anche un'autorizzazione speciale per accedere a. L'elenco dei contatti incorporato richiede l'autorizzazione `android.permission.READ_CONTACTS` nel file **file AndroidManifest. XML** .

Esistono tre modi per creare un cursore dall'URI:

1. **ManagedQuery ()** &ndash; l'approccio preferito in Android 2,3 (API level 10) e versioni precedenti, una `ManagedQuery` restituisce un cursore e inoltre gestisce automaticamente l'aggiornamento dei dati e la chiusura del cursore. Questo metodo è deprecato in Android 3,0 (livello API 11).

1. **ContentResolver. query ()** &ndash; restituisce un cursore non gestito, il che significa che deve essere aggiornato e chiuso in modo esplicito nel codice.

1. **CursorLoader (). LoadInBackground ()** &ndash; introdotto in Android 3,0 (livello API 11), `CursorLoader` è ora il modo migliore per utilizzare un `ContentProvider`. `CursorLoader` esegue una query su un `ContentResolver` in un thread in background in modo che l'interfaccia utente non sia bloccata.
   È possibile accedere a questa classe nelle versioni precedenti di Android usando la libreria di compatibilità V4.

Ognuno di questi metodi ha lo stesso set di input di base:

- **Uri** &ndash; il nome completo del `ContentProvider`.
- **Proiezione** &ndash; specifica delle colonne da selezionare per il cursore.
- La **selezione** &ndash; simile a una clausola SQL `WHERE`.
- **SelectionArgs** &ndash; i parametri da sostituire nella selezione.
- **SortOrder** &ndash; le colonne in base alle quali eseguire l'ordinamento.

## <a name="creating-inputs-for-a-query"></a>Creazione di input per una query

Il codice di esempio `ContactsProvider` esegue una query molto semplice sul provider di contatti incorporato di Android. Non è necessario avere familiarità con l'URI o i nomi di colonna effettivi. tutte le informazioni necessarie per eseguire query sui contatti `ContentProvider` sono disponibili come costanti esposte dalla classe `ContactsContract`.

Indipendentemente dal metodo usato per recuperare il cursore, questi stessi oggetti vengono usati come parametri, come illustrato nel file *ContactsProvider/ContactsAdapter. cs* :

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

Per questo esempio, le `selection`, `selectionArgs` e `sortOrder` verranno ignorate impostando le `null`.

## <a name="creating-a-cursor-from-a-content-provider-uri"></a>Creazione di un cursore da un URI del provider di contenuti

Una volta creati gli oggetti Parameter, è possibile utilizzarli in uno dei tre modi seguenti:

### <a name="using-a-managed-query"></a>Utilizzo di una query gestita

Le applicazioni destinate a Android 2,3 (livello API 10) o versioni precedenti devono usare questo metodo:

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

Questo cursore verrà gestito da Android, quindi non è necessario chiuderlo.

### <a name="using-contentresolver"></a>Uso di ContentResolver

L'accesso diretto a `ContentResolver` per ottenere un cursore rispetto a una `ContentProvider` può essere eseguito come segue:

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

Questo cursore non è gestito, quindi è necessario chiuderlo quando non è più necessario.
Verificare che il codice chiuda un cursore aperto; in caso contrario, si verificherà un errore.

```csharp
cursor.Close();
```

In alternativa, è possibile chiamare `StartManagingCursor()` e `StopManagingCursor()` per "gestire" il cursore. I cursori gestiti vengono automaticamente disattivati e nuovamente sottoposti a query quando le attività vengono arrestate e riavviate.

### <a name="using-cursorloader"></a>Uso di CursorLoader

Le applicazioni compilate per Android 3,0 (livello API 11) o versione successiva devono usare questo metodo:

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

Il `CursorLoader` garantisce che tutte le operazioni del cursore vengano eseguite in un thread in background e possano riutilizzare in modo intelligente un cursore esistente tra le istanze dell'attività quando un'attività viene riavviata (ad esempio, a causa di una modifica della configurazione), anziché ricaricare nuovamente i dati.

Le versioni precedenti di Android possono anche usare la classe `CursorLoader` usando le [librerie di supporto V4](https://developer.android.com/tools/support-library/index.html).

## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>Visualizzazione dei dati del cursore con un adapter personalizzato

Per visualizzare l'immagine del contatto verrà usato un adapter personalizzato, in modo da poter risolvere manualmente il riferimento `PhotoId` a un percorso di file di immagine.

Per visualizzare i dati con un adapter personalizzato, nell'esempio viene utilizzato un `CursorLoader` per recuperare tutti i dati di contatto in una raccolta locale nel metodo **FillContacts** da **ContactsProvider/ContactsAdapter. cs**:

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

Implementare quindi i metodi di BaseAdapter usando la raccolta `contactList`. L'adapter viene implementato in modo analogo a qualsiasi altra raccolta &ndash; non esiste una gestione speciale perché i dati vengono originati da una `ContentProvider`:

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

L'immagine viene visualizzata (se esistente) usando l'URI del file di immagine nel dispositivo. L'applicazione ha un aspetto simile al seguente:

[![screenshot dell'app che Visualizza i contatti in un controllo ListView; un'immagine viene visualizzata a sinistra di una voce](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

Usando un modello di codice simile, l'applicazione può accedere a un'ampia gamma di dati di sistema, tra cui foto, video e musica dell'utente.
Alcuni tipi di dati richiedono autorizzazioni speciali da richiedere nel **file file AndroidManifest. XML**del progetto.

## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>Visualizzazione dei dati del cursore con un SimpleCursorAdapter

Il cursore può anche essere visualizzato con un `SimpleCursorAdapter` (anche se verrà visualizzato solo il nome, non la foto). Questo codice illustra come usare un `ContentProvider` con `SimpleCursorAdapter` (questo codice non viene visualizzato nell'esempio):

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

Per ulteriori informazioni sull'implementazione di `SimpleCursorAdapter`, vedere gli [Adapter e ListView](~/android/user-interface/layouts/list-view/index.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Demo di ContactsAdapter (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
