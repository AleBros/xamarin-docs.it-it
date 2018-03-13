---
title: Creazione di un provider di contenuti personalizzati
description: "La sezione precedente è stato illustrato come utilizzare i dati da un'implementazione incorporata del provider di contenuti. In questa sezione verrà illustrato come compilare un provider di contenuti personalizzato e quindi utilizzare i dati."
ms.topic: article
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/07/2018
ms.openlocfilehash: 9fac4a233cecd9332602047bc83830d145b5fb08
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="creating-a-custom-contentprovider"></a>Creazione di un provider di contenuti personalizzati

_La sezione precedente è stato illustrato come utilizzare i dati da un'implementazione incorporata del provider di contenuti. In questa sezione verrà illustrato come compilare un provider di contenuti personalizzato e quindi utilizzare i dati._

## <a name="about-contentproviders"></a>Su ContentProviders

È necessario ereditare una classe di provider di contenuti `ContentProvider`. Deve includere un archivio dati interno utilizzato per rispondere alle query e deve esporre gli URI e i tipi MIME come costanti per consentire l'utilizzo di codice richiedono valide per i dati.

### <a name="uri-authority"></a>URI (Authority)

`ContentProviders` sono accessibili in Android utilizzando un Uri. Un'applicazione che espone un `ContentProvider` imposta gli URI che risponderà al relativo **AndroidManifest.xml** file. Quando l'applicazione è installata, gli URI vengono registrati in modo che le altre applicazioni possano accedervi.

Mono per Android, la classe di provider di contenuti deve avere un `[ContentProvider]` attributo per specificare l'Uri (o un URI) che deve essere aggiunto a **AndroidManifest.xml**.


### <a name="mime-type"></a>Tipo MIME

Il formato tipico per i tipi MIME è costituito da due parti. Android `ContentProviders` utilizzano in genere questi due stringhe per la prima parte del tipo MIME:

1. `vnd.android.cursor.item` &ndash; per rappresentare una singola riga, utilizzare il `ContentResolver.CursorItemBaseType` costante nel codice.

1. `vnd.android.cursor.dir` &ndash; per più righe, utilizzare il `ContentResolver.CursorDirBaseType` costante nel codice.

La seconda parte del tipo MIME è specifica per l'applicazione e deve utilizzare standard inversa DNS con un `vnd.` prefisso. Il codice di esempio utilizza `vnd.com.xamarin.sample.Vegetables`.


### <a name="data-model-metadata"></a>Metadati del modello di dati

Utilizzo di applicazioni è necessario costruire le query dell'Uri per accedere a diversi tipi di dati. L'Uri di base può essere espanso per fare riferimento a una particolare tabella di dati e può includere parametri per filtrare i risultati. Le colonne e le clausole utilizzate con il cursore risulta per visualizzare dati devono essere dichiarate.

Per garantire che solo le query dell'Uri valide sono costruite, è facoltativa per fornire le stringhe come valori di costante valide. Questo rende più semplice per l'accesso di `ContentProvider` perché rende individuabili tramite completamento di codice i valori e impedisce l'ortografia nelle stringhe.

Nell'esempio precedente il `android.provider.ContactsContract` classe esposti i metadati per i dati di contatti. Per personalizzato `ContentProvider` si dovrà esporre solo le costanti sulla classe stessa.


## <a name="implementation"></a>Implementazione

Esistono tre passaggi per la creazione e l'utilizzo di un oggetto personalizzato `ContentProvider`:

1. **Creare una classe di database** &ndash; implementare `SQLiteOpenHelper`.

2. **Creare un `ContentProvider` classe** &ndash; implementare `ContentProvider` con un'istanza del database, i metadati esposti come valori delle costanti e metodi per accedere ai dati.

3. **Accesso di `ContentProvider` tramite il relativo Uri** &ndash; popola un `CursorAdapter` utilizzando il `ContentProvider`, a cui si accede tramite il relativo Uri.

Come spiegato in precedenza, `ContentProviders` può essere utilizzato da applicazioni diverse da dove sono definiti. In questo esempio i dati vengono utilizzati nella stessa applicazione, ma tenere presente che altre applicazioni possono inoltre accedervi come conoscano l'Uri e informazioni sullo schema (che è in genere esposti come valori costanti).


## <a name="create-a-database"></a>Creare un Database

La maggior parte delle `ContentProvider` implementazioni saranno basate su un `SQLite` database. Codice di database di esempio in **SimpleContentProvider/VegetableDatabase.cs** crea un database molto semplice di due colonne, come illustrato:

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
  const string create_table_sql =
    "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
  const string DatabaseName = "vegetables.db";
  const int DatabaseVersion = 1;

  public VegetableDatabase(Context context) : base(context, DatabaseName, null, DatabaseVersion) { }
  public override void OnCreate(SQLiteDatabase db)
  {
    db.ExecSQL(create_table_sql);
    // seed with data
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Vegetables')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Fruits')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Flower Buds')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Legumes')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Bulbs')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Tubers')");
  }
  public override void OnUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
  {
    throw new NotImplementedException();
  }
}
```

L'implementazione del database non necessita di considerazioni speciali da esporre con un `ContentProvider`, tuttavia se si desidera associare il `ContentProvider's` dati da un `ListView` il controllo di una colonna di tipo integer univoco denominata quindi `_id` deve far parte del set di risultati. Vedere il [controlli ListView e schede](~/android/user-interface/layouts/list-view/index.md) documento per informazioni dettagliate sull'utilizzo di `ListView` controllo.


## <a name="create-the-contentprovider"></a>Creare il provider di contenuti

Il resto di questa sezione vengono fornite istruzioni dettagliate su come l'oggetto **SimpleContentProvider/VegetableProvider.cs** è stato compilato l'esempio di classe.


### <a name="initialize-the-database"></a>Inizializzare il Database

Il primo passaggio consiste nella sottoclasse `ContentProvider` e aggiungere il database che verrà utilizzato.

```csharp
public class VegetableProvider : ContentProvider 
{
    VegetableDatabase vegeDB;
    public override bool OnCreate()
    {
       vegeDB = new VegetableDatabase(Context);
        return true;
    }
}
```

Il resto del codice verrà formano l'implementazione del provider di contenuto effettivo che consente di essere individuati e sottoposta a query i dati.



## <a name="add-metadata-for-consumers"></a>Aggiungere i metadati per i consumer

Esistono quattro tipi diversi di metadati che verrà espongano il `ContentProvider` classe. Solo l'autorità è obbligatoria, le altre vengono eseguite per convenzione.

- **Autorità** &ndash; il `ContentProvider` attributo *deve* essere aggiunto alla classe in modo che la registrazione con il Android quando l'applicazione è installata.

- **URI** &ndash; il `CONTENT_URI` viene esposto come una costante in modo che sia facile da usare nel codice. Deve corrispondere l'autorità, ma includono lo schema e il percorso di base.

- **Tipi MIME** &ndash; elenchi di risultati e i risultati vengono considerati come tipi di contenuto diversi, pertanto è possibile definire due tipi MIME per rappresentare le.

- **InterfaceConsts** &ndash; fornire un valore costante per ogni nome di colonna di dati, in modo che utilizza codice possibile individuare facilmente e farvi riferimento senza rischiare gli errori tipografici.

Questo codice viene illustrato come ognuno di questi elementi viene implementato, aggiunta alla definizione del database nel passaggio precedente:

```csharp
[ContentProvider(new string[] { CursorTableAdapter.VegetableProvider.AUTHORITY })]
public class VegetableProvider : ContentProvider 
{
   public const string AUTHORITY = "com.xamarin.sample.VegetableProvider";
   static string BASE_PATH = "vegetables";
   public static readonly Android.Net.Uri CONTENT_URI = Android.Net.Uri.Parse("content://" + AUTHORITY + "/" + BASE_PATH);
   // MIME types used for getting a list, or a single vegetable
   public const string VEGETABLES_MIME_TYPE = ContentResolver.CursorDirBaseType + "/vnd.com.xamarin.sample.Vegetables";
   public const string VEGETABLE_MIME_TYPE = ContentResolver.CursorItemBaseType + "/vnd.com.xamarin.sample.Vegetables";
   // Column names
   public static class InterfaceConsts {
       public const string Id = "_id";
       public const string Name = "name";
   }
   VegetableDatabase vegeDB;
   public override bool OnCreate()
   {
       vegeDB = new VegetableDatabase(Context);
       return true;
   }
}
```


## <a name="implement-the-uri-parsing-helper"></a>Implementare l'URI di analisi di supporto

Poiché l'utilizzo di codice Usa l'URI per effettuare richieste di un `ContentProvider`, è necessario essere in grado di analizzare le richieste per determinare i dati da restituire. Il `UriMatcher` classe consentono di analizzare gli URI, una volta inizializzata con l'Uri di modelli di `ContentProvider` supporta.

Il `UriMatcher` nell'esempio verrà inizializzato con due URI:

1. *"com.xamarin.sample.VegetableProvider/vegetables"* &ndash; richiesta per cui restituire l'elenco completo di verdure.

2. *"com.xamarin.sample.VegetableProvider/vegetables/\#"* &ndash; in cui il \# è un segnaposto per un parametro numerico (il `_id` della riga nel database). Il segnaposto di un asterisco ("\*") consente anche di corrisponde a un parametro di testo.

Nel codice è utilizzare le costanti per fare riferimento ai valori dei metadati come l'autorità e BASE\_percorso. I codici restituiti da utilizzare in metodi che consentono di Uri di analisi, per determinare i dati da restituire.

```csharp
const int GET_ALL = 0; // return code when list of Vegetables requested
const int GET_ONE = 1; // return code when a single Vegetable is requested by ID
static UriMatcher uriMatcher = BuildUriMatcher();
static UriMatcher BuildUriMatcher()
{
  var matcher = new UriMatcher(UriMatcher.NoMatch);
  // Uris to match, and the code to return when matched
  matcher.AddURI(AUTHORITY, BASE_PATH, GET_ALL); // all vegetables
  matcher.AddURI(AUTHORITY, BASE_PATH + "/#", GET_ONE); // specific vegetable by numeric ID
  return matcher;
}
```

Questo codice è tutte privato per la `ContentProvider` classe. Fare riferimento a [documentazione UriMatcher di Google](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/) per ulteriori informazioni.


## <a name="implement-the-querymethod"></a>Implementare il QueryMethod

La più semplice `ContentProvider` metodo per implementare il `Query` metodo. L'implementazione seguente viene utilizzato il `UriMatcher` per analizzare il `uri` parametro e chiamare il metodo di database corretto. Se il `uri` contiene un parametro di tipo ID, viene analizzato l'intero (utilizzando `LastPathSegment`) e utilizzato nella query del database.

```csharp
public override Android.Database.ICursor Query(Android.Net.Uri uri, string[] projection, string selection, string[] selectionArgs, string sortOrder)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return GetFromDatabase();
  case GET_ONE:
    var id = uri.LastPathSegment;
    return GetFromDatabase(id); // the ID is the last part of the Uri
  default:
    throw new Java.Lang.IllegalArgumentException("Unknown Uri: " + uri);
  }
}
Android.Database.ICursor GetFromDatabase()
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables", null);
}
Android.Database.ICursor GetFromDatabase(string id)
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables WHERE _id = " + id, null);
}
```

Il `GetType` deve anche eseguire l'override di metodo. Questo metodo può essere chiamato per determinare il tipo di contenuto che verrà restituito per un Uri specificato.
Questo potrebbe indicare che l'applicazione consumer come gestire i dati.

```csharp
public override String GetType(Android.Net.Uri uri)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return VEGETABLES_MIME_TYPE; // list
  case GET_ONE:
    return VEGETABLE_MIME_TYPE; // single item
  default:
    throw new Java.Lang.IllegalArgumentExceptoin ("Unknown Uri: " + uri);
   }
}
```


## <a name="implement-the-other-overrides"></a>Implementare le altre sostituzioni

L'esempio non consente la modifica o eliminazione di dati, ma i metodi di inserimento, aggiornamento ed eliminazione devono essere implementati aggiungerli pertanto senza alcuna implementazione:

```csharp
public override int Delete(Android.Net.Uri uri, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override Android.Net.Uri Insert(Android.Net.Uri uri, ContentValues values)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override int Update(Android.Net.Uri uri, ContentValues values, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
```

È stata completata la basic `ContentProvider` implementazione. Dopo aver installato l'applicazione, i dati che espone saranno disponibili sia all'interno dell'applicazione, ma anche per qualsiasi altra applicazione che conosca l'Uri per farvi riferimento.


## <a name="access-the-contentprovider"></a>Accesso il provider di contenuti

Una volta il `VegetableProvider` è stato implementato, accede a tale operazione viene eseguita esattamente come il provider di contatti all'inizio di questo documento: ottenere un cursore tramite l'Uri specificato e quindi usare un adattatore per accedere ai dati.


## <a name="bind-a-listview-to-a-contentprovider"></a>Associare un provider di contenuti di un controllo ListView

Per popolare un `ListView` con i dati è utilizzare l'Uri che corrisponde all'elenco filtrato di verdure. Nel codice viene utilizzato il valore costante `VegetableProvider.CONTENT_URI`, è noto che si risolve in `com.xamarin.sample.vegetableprovider/vegetables`. Il nostro `VegetableProvider.Query` implementazione restituirà un cursore che può quindi essere associato al `ListView`.

Il codice in `SimpleContentProvider/HomeScreen.cs` di seguito viene illustrato come è possibile visualizzare dati da un `ContentProvider`:

```csharp
listView = FindViewById<ListView>(Resource.Id.List);
string[] projection = new string[] { VegetableProvider.InterfaceConsts.Id, VegetableProvider.InterfaceConsts.Name} ;
string[] fromColumns = new string[] { VegetableProvider.InterfaceConsts.Name };
int[] toControlIds = new int[] { Android.Resource.Id.Text1 };

// CursorLoader introduced in Honeycomb (3.0, API_11)
var loader = new CursorLoader(this,
   VegetableProvider.CONTENT_URI, projection, null, null, null);
cursor = (ICursor)loader.LoadInBackground();

// Create a SimpleCursorAdapter
adapter = new SimpleCursorAdapter(this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlIds);
listView.Adapter = adapter;
```

L'applicazione risulta è simile al seguente:

[![Schermata di elenco verdura, frutta, fiore conformate, legumi, lampadine, tuberi app](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)



## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Recuperare un singolo elemento da un provider di contenuti

Un'applicazione consumer potrebbe risultare necessaria accedere alle singole righe di dati, che possono essere eseguiti costruendo un Uri diverso che fa riferimento a una riga specifica (ad esempio).

Utilizzare `ContentResolver` direttamente per accedere a un singolo elemento, tramite la compilazione di un Uri con obbligatorio `Id`.

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

Il metodo completo è simile al seguente:

```csharp
protected void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
  var id = e.Id;
  string[] projection = new string[] { "name" };
  var uri = Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
  ICursor vegeCursor = ContentResolver.Query(uri, projection, null, new string[] { id.ToString() }, null);
  string text = "";
  if (vegeCursor.MoveToFirst()) {
    text = vegeCursor.GetInt(0) + " " + vegeCursor.GetString(1);
    Android.Widget.Toast.MakeText(this, text, Android.Widget.ToastLength.Short).Show();
  }
  vegeCursor.Close();
}
```


## <a name="related-links"></a>Collegamenti correlati

- [SimpleContentProvider (sample)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
