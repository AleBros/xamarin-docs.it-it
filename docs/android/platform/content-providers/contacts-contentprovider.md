---
title: Uso del ContentProvider Contacts
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/22/2018
ms.openlocfilehash: fca57b7af34ae2b28dda9bf20a95183138cbc641
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020536"
---
# <a name="using-the-contacts-contentprovider"></a>Uso del ContentProvider Contacts

Il codice che utilizza `ContentProvider` i dati di accesso `ContentProvider` esposti da un oggetto non richiede affatto un riferimento alla classe . Al contrario, un Uri viene utilizzato per `ContentProvider`creare un cursore sui dati esposti dal . Android utilizza l'URI per cercare nel sistema `ContentProvider` l'applicazione che espone un con tale identificatore. Uri è una stringa, in genere in `com.android.contacts/data`un formato DNS inverso, ad esempio .

Anziché fare in modo che gli sviluppatori ricordino `android.provider.ContactsContract` questa stringa, il provider di *contatti* Android espone i metadati nella classe. Questa classe viene utilizzata per `ContentProvider` determinare l'URI dell'URI e i nomi delle tabelle e delle colonne su cui è possibile eseguire query.

Alcuni tipi di dati richiedono anche un'autorizzazione speciale per l'accesso. L'elenco di contatti `android.permission.READ_CONTACTS` predefinito richiede l'autorizzazione nel file **AndroidManifest.xml.**

Esistono tre modi per creare un cursore dall'URI:There are three ways to create a cursor from the Uri:

1. **ManagedQuery()** &ndash; L'approccio preferito in Android 2.3 (livello `ManagedQuery` API 10) e versioni precedenti, un restituisce un cursore e gestisce anche automaticamente l'aggiornamento dei dati e la chiusura del cursore. Questo metodo è deprecato in Android 3.0 (livello API 11).

1. **ContentResolver.Query()** &ndash; Restituisce un cursore non gestito, il che significa che deve essere aggiornato e chiuso in modo esplicito nel codice.

1. **CursorLoader(). LoadInBackground()** &ndash; Introdotto in Android 3.0 `CursorLoader` (livello API 11), `ContentProvider` è ora il modo preferito per utilizzare un file . `CursorLoader`esegue `ContentResolver` una query su un thread in background in modo che l'interfaccia utente non sia bloccata.
   Questa classe è accessibile nelle versioni precedenti di Android utilizzando la libreria di compatibilità v4.

Ognuno di questi metodi ha lo stesso set di input di base:

- **URI** &ndash; Il nome completo `ContentProvider` del file .
- Specifica di **proiezione** &ndash; delle colonne da selezionare per il cursore.
- **Selezione** &ndash; Simile a `WHERE` una clausola SQL.
- **SelectionArgs** &ndash; Parametri da sostituire nella selezione.
- **SortOrder** &ndash; Colonne in base alle quale eseguire l'ordinamento.

## <a name="creating-inputs-for-a-query"></a>Creazione di input per una queryCreating Inputs for a Query

Il `ContactsProvider` codice di esempio esegue una query molto semplice sul provider Contatti incorporato di Android.The sample code performs a very simple query against Android's built-in Contacts provider. Non è necessario conoscere l'URI effettivo o i nomi di `ContentProvider` colonna: tutte le `ContactsContract` informazioni necessarie per eseguire query sui contatti sono disponibili come costanti esposte dalla classe.

Indipendentemente dal metodo utilizzato per recuperare il cursore, questi stessi oggetti vengono utilizzati come parametri, come illustrato nel file *ContactsProvider/ContactsAdapter.cs:*

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

Per questo esempio, `selection`il , `selectionArgs` e `sortOrder` verrà `null`ignorato impostandoli su .

## <a name="creating-a-cursor-from-a-content-provider-uri"></a>Creazione di un cursore da un URI del provider di contenutiCreating a Cursor from a Content Provider Uri

Una volta creati, gli oggetti parametro possono essere utilizzati in uno dei tre modi seguenti:

### <a name="using-a-managed-query"></a>Utilizzo di una query gestitaUsing a Managed Query

Le applicazioni destinate ad Android 2.3 (livello API 10) o precedenti devono usare questo metodo:Applications targeting Android 2.3 (API Level 10) or earlier should use this method:

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

Questo cursore verrà gestito da Android in modo che non sia necessario chiuderlo.

### <a name="using-contentresolver"></a>Utilizzo di ContentResolverUsing ContentResolver

L'accesso `ContentResolver` diretto per ottenere `ContentProvider` un cursore su un può essere fatto in questo modo:

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

Questo cursore non è gestito, pertanto deve essere chiuso quando non è più necessario.
Assicurarsi che il codice chiuda un cursore aperto, altrimenti si verificherà un errore.

```csharp
cursor.Close();
```

In alternativa, è `StartManagingCursor()` `StopManagingCursor()` possibile chiamare e "gestire" il cursore. I cursori gestiti vengono disattivati automaticamente e sottoposti a query quando le attività vengono arrestate e riavviate.

### <a name="using-cursorloader"></a>Utilizzo di CursorLoaderUsing CursorLoader

Le applicazioni create per Android 3.0 (livello API 11) o più recente devono usare questo metodo:Applications built for Android 3.0 (API Level 11) or newer should use this method:

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

Il `CursorLoader` assicura che tutte le operazioni del cursore vengono eseguite su un thread in background e può riutilizzare in modo intelligente un cursore esistente tra le istanze di attività quando un'attività viene riavviata (ad esempio a causa di una modifica della configurazione) anziché ricaricare nuovamente i dati.

Le versioni precedenti di `CursorLoader` Android possono anche utilizzare la classe utilizzando le librerie di [supporto v4](https://developer.android.com/tools/support-library/index.html).

## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>Visualizzazione dei dati del cursore con un adattatore personalizzatoDisplaying the Cursor Data with a Custom Adapter

Per visualizzare l'immagine del contatto useremo un adattatore `PhotoId` personalizzato, in modo da poter risolvere manualmente il riferimento a un percorso di file di immagine.

Per visualizzare i dati con un `CursorLoader` adattatore personalizzato, nell'esempio viene utilizzato un per recuperare tutti i dati Contact in una raccolta locale nel metodo **FillContacts** da **ContactsProvider/ContactsAdapter.cs**:

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

Implementare quindi i metodi del `contactList` BaseAdapter utilizzando la raccolta. L'adattatore viene implementato come sarebbe &ndash; con qualsiasi altra raccolta non esiste una `ContentProvider`gestione speciale in questo caso perché i dati provengono da un oggetto :

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

L'immagine viene visualizzata (se esistente) utilizzando l'URI al file di immagine sul dispositivo. L'applicazione è simile alla seguente:The application looks like this:

[![Screenshot dell'app che visualizza i contatti in un controllo ListView; un'immagine viene visualizzata a sinistra di una voce](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

Utilizzando un modello di codice simile, l'applicazione può accedere a un'ampia gamma di dati di sistema, tra cui foto, video e musica dell'utente.
Alcuni tipi di dati richiedono autorizzazioni speciali da richiedere in **AndroidManifest.xml**del progetto.

## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>Visualizzazione dei dati del cursore con un SimpleCursorAdapterDisplaying the Cursor Data with a SimpleCursorAdapter

Il cursore potrebbe anche `SimpleCursorAdapter` essere visualizzato con un (anche se verrà visualizzato solo il nome, non la foto). Questo codice viene illustrato `ContentProvider` `SimpleCursorAdapter` come utilizzare a with (questo codice non viene visualizzato nell'esempio):This code demonstrates how to use a with (this code does not appear in the sample):

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

Fare riferimento a [ListViews e Adapters](~/android/user-interface/layouts/list-view/index.md) per ulteriori informazioni sull'implementazione `SimpleCursorAdapter`di .

## <a name="related-links"></a>Collegamenti correlati

- [Demo di ContactsAdapter (esempio)ContactsAdapter Demo (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
