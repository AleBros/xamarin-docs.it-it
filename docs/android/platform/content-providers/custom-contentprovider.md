---
title: Creazione di un ContentProvider personalizzato
description: La sezione precedente è stato illustrato come utilizzare dati da un'implementazione ContentProvider incorporata. In questa sezione verrà illustrate le procedure compilare un ContentProvider personalizzato e quindi utilizzare i dati.
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: da8aacac1f282fefb6b8d0e84cae168cf3a7148b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953361"
---
# <a name="creating-a-custom-contentprovider"></a>Creazione di un ContentProvider personalizzato

_La sezione precedente è stato illustrato come utilizzare dati da un'implementazione ContentProvider incorporata. In questa sezione verrà illustrate le procedure compilare un ContentProvider personalizzato e quindi utilizzare i dati._

## <a name="about-contentproviders"></a>Sulle ContentProviders

È necessario ereditare una classe di provider di contenuti `ContentProvider`. Deve essere costituito un archivio dati interno usato per rispondere alle query e deve esporre gli URI e i tipi MIME come costanti di codice viene usato per effettuare richieste valide per i dati.

### <a name="uri-authority"></a>URI (autorità)

`ContentProviders` sono accessibili in Android tramite un Uri. Un'applicazione che espone un `ContentProvider` imposta gli URI di risposta nel relativo **androidmanifest. XML** file. Quando l'applicazione è installata, questi URI sono registrato in modo che altre applicazioni possono accedervi.

In Mono per Android, la classe di provider di contenuto deve avere una `[ContentProvider]` attributo per specificare l'Uri (o URI) che deve essere aggiunto al **androidmanifest. XML**.


### <a name="mime-type"></a>Tipo MIME

Il formato tipico per i tipi MIME è costituito da due parti. Android `ContentProviders` utilizzano in genere questi due stringhe per la prima parte del tipo MIME:

1. `vnd.android.cursor.item` &ndash; per rappresentare una singola riga, utilizzare il `ContentResolver.CursorItemBaseType` costante nel codice.

1. `vnd.android.cursor.dir` &ndash; per più righe, usare il `ContentResolver.CursorDirBaseType` costante nel codice.

La seconda parte del tipo MIME è specifica dell'applicazione e deve usare uno standard di DNS inverso con un `vnd.` prefisso. Il codice di esempio Usa `vnd.com.xamarin.sample.Vegetables`.


### <a name="data-model-metadata"></a>Metadati del modello dati

Utilizzo di applicazioni necessario costruire le query dell'Uri per accedere a diversi tipi di dati. L'Uri di base può essere espanso per fare riferimento a una determinata tabella di dati e può includere parametri per filtrare i risultati. Devono essere dichiarate anche le colonne e le clausole utilizzate con il cursore risulta per visualizzare i dati.

Per garantire che solo le query dell'Uri valide vengono costruite, è facoltativa per fornire le stringhe come valori di costante valide. Questo rende più semplice per l'accesso di `ContentProvider` perché rende individuabili tramite completamento di codice i valori e impedisce che gli errori di battitura nelle stringhe.

Nell'esempio precedente il `android.provider.ContactsContract` classe esposti i metadati per i dati dei contatti. Per profilo `ContentProvider` si esporrà solo le costanti sulla classe stessa.


## <a name="implementation"></a>Implementazione

Esistono tre passaggi per la creazione e l'utilizzo di un oggetto personalizzato `ContentProvider`:

1. **Creare una classe di database** &ndash; implementare `SQLiteOpenHelper`.

2. **Creare un `ContentProvider` classe** &ndash; implementare `ContentProvider` con un'istanza del database, i metadati esposti come valori costanti e metodi per accedere ai dati.

3. **Accesso di `ContentProvider` tramite un Uri** &ndash; popola una `CursorAdapter` usando il `ContentProvider`, a cui si accede tramite un Uri.

Come illustrato in precedenza, `ContentProviders` possono essere utilizzati dalle applicazioni diverso da in cui sono definiti. In questo esempio i dati vengono utilizzati nella stessa applicazione, ma tenere presente che altre applicazioni possono inoltre accedervi purché si conosca l'Uri e informazioni sullo schema (che in genere viene esposta come valori costanti).


## <a name="create-a-database"></a>Creare un database

La maggior parte degli `ContentProvider` implementazioni si baseranno su una `SQLite` database. Il codice di database di esempio nella **SimpleContentProvider/VegetableDatabase.cs** crea un database molto semplice di due colonne, come illustrato:

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

L'implementazione del database stesso non è necessario considerazioni speciali da esporre con un `ContentProvider`, tuttavia se si prevede di eseguire l'associazione il `ContentProvider's` dati a un `ListView` controllare quindi una colonna di tipo integer univoco denominata `_id` deve far parte del set di risultati. Vedere le [ListView e adapter](~/android/user-interface/layouts/list-view/index.md) documento per altri dettagli sull'uso di `ListView` controllo.


## <a name="create-the-contentprovider"></a>Creare il provider di contenuti

La parte restante di questa sezione vengono fornite istruzioni dettagliate sul modo in cui il **SimpleContentProvider/VegetableProvider.cs** è stato compilato l'esempio di classe.


### <a name="initialize-the-database"></a>Inizializzare il Database

Il primo passaggio consiste nel sottoclasse `ContentProvider` e aggiungere il database che utilizza.

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

Il resto del codice costituirà l'implementazione del provider contenuto effettivo che consente di essere individuati e sottoposti a query i dati.



## <a name="add-metadata-for-consumers"></a>Aggiungere i metadati per gli utenti

Esistono quattro tipi diversi di metadati che si intende esporre nel `ContentProvider` classe. Solo l'autorità è obbligatoria, per convenzione vengono eseguiti il resto.

- **Autorità** &ndash; le `ContentProvider` attributo *necessario* da aggiungere alla classe in modo che è registrato con l'Android quando l'applicazione viene installata.

- **URI** &ndash; il `CONTENT_URI` viene esposto come una costante in modo che sia facile da usare nel codice. Deve corrispondere l'autorità, ma includono lo schema e il percorso di base.

- **Tipi MIME** &ndash; gli elenchi di risultati singoli e i risultati vengono considerati diversi tipi di contenuto, in modo che si definiscono due tipi MIME per li rappresenta.

- **InterfaceConsts** &ndash; fornire un valore costante per ogni nome di colonna di dati, in modo che l'utilizzo di codice e trovare facilmente vi fanno riferimento senza rischiare errori tipografici.

Questo codice viene illustrato come ognuno di questi elementi viene implementata, aggiungendo alla definizione del database nel passaggio precedente:

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


## <a name="implement-the-uri-parsing-helper"></a>Implementare il supporto di analisi URI

Poiché utilizza codice utilizza gli URI per effettuare richieste di un `ContentProvider`, dobbiamo essere in grado di analizzare le richieste per determinare i dati da restituire. Il `UriMatcher` classe può rivelarsi utile per analizzare gli URI, dopo che è stato inizializzato con l'Uri pattern che il `ContentProvider` supporta.

Il `UriMatcher` nell'esempio verrà inizializzato con due URI:

1. *"com.xamarin.sample.VegetableProvider/vegetables"* &ndash; richiesta per restituire l'elenco completo di verdure.

2. *"com.xamarin.sample.VegetableProvider/vegetables/\#"* &ndash; in cui il \# è un segnaposto per un parametro numerico (il `_id` della riga nel database). Un segnaposto dell'asterisco ("\*") è anche utilizzabile per corrispondere a un parametro di testo.

Nel codice è utilizzare le costanti per fare riferimento a valori di metadati, ad esempio l'autorità e BASE\_percorso. I codici restituiti da utilizzare nei metodi che eseguono l'analisi, per determinare i dati da restituire Uri.

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

Questo codice è tutti privato per il `ContentProvider` classe. Fare riferimento a [documentazione di Google UriMatcher](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/) per altre informazioni.


## <a name="implement-the-querymethod"></a>Implementare il QueryMethod

La più semplice `ContentProvider` metodo da implementare è il `Query` (metodo). L'implementazione seguente usa il `UriMatcher` analizzare il `uri` parametro e chiamare il metodo di database corretto. Se il `uri` contiene un parametro ID out viene analizzato il valore integer (usando `LastPathSegment`) e utilizzato nella query del database.

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
Ciò potrebbe indicare all'applicazione dispendiosa in termini di come gestire i dati.

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

L'esempio non consente la modifica o eliminazione di dati, ma devono essere implementati i metodi Insert, Update e Delete quindi aggiungerli senza un'implementazione:

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

È stata completata la basic `ContentProvider` implementazione. Dopo aver installata l'applicazione, i dati che espone sarà disponibili sia all'interno dell'applicazione, ma anche a qualsiasi altra applicazione che riconosce l'Uri per farvi riferimento.


## <a name="access-the-contentprovider"></a>Accesso il provider di contenuti

Una volta il `VegetableProvider` è stato implementato, accede a tale operazione viene eseguita esattamente come il provider di contatti all'inizio di questo documento: ottenere un cursore utilizzando l'Uri specificato e quindi usare un adattatore per accedere ai dati.


## <a name="bind-a-listview-to-a-contentprovider"></a>Associare un ListView di un ContentProvider

Per popolare un `ListView` con i dati è usare l'Uri che corrisponde all'elenco filtrato di verdura. Nel codice viene usato il valore costante `VegetableProvider.CONTENT_URI`, che sappiamo che viene risolta in `com.xamarin.sample.vegetableprovider/vegetables`. Nostri `VegetableProvider.Query` implementazione restituirà un cursore che può quindi essere associato ai `ListView`.

Il codice nel `SimpleContentProvider/HomeScreen.cs` mostra quanto sia semplice per visualizzare i dati da un `ContentProvider`:

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

L'applicazione risultante è simile alla seguente:

[![Screenshot dell'app listato verdura, frutti, in silicone in fiore, legumi, Bulbs, tuberi](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)



## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Recuperare un singolo elemento di un ContentProvider

Un'applicazione consumer potrebbe essere necessario anche accedere alle singole righe di dati, che possono essere eseguiti mediante la costruzione di un Uri diverso che fa riferimento a una riga specifica (ad esempio).

Uso `ContentResolver` direttamente per accedere a un singolo elemento, tramite la compilazione di un Uri con la necessaria `Id`.

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

Il metodo completo è simile alla seguente:

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
