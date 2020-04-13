---
title: Creazione di un ContentProvider personalizzato
description: Nella sezione precedente è stato illustrato come utilizzare i dati da un'implementazione di ContentProvider incorporata. In questa sezione verrà illustrato come compilare un ContentProvider personalizzato e quindi utilizzare i dati.
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3e57e0cd2fa87db8035fa68995b69f231151fa09
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020535"
---
# <a name="creating-a-custom-contentprovider"></a>Creazione di un ContentProvider personalizzato

_Nella sezione precedente è stato illustrato come utilizzare i dati da un'implementazione di ContentProvider incorporata. In questa sezione verrà illustrato come compilare un ContentProvider personalizzato e quindi utilizzare i dati._

## <a name="about-contentproviders"></a>Informazioni su ContentProviders

Una classe del provider `ContentProvider`di contenuti deve ereditare da . Deve essere costituito da un archivio dati interno utilizzato per rispondere alle query e deve esporre URI e tipi MIME come costanti per consentire l'utilizzo di codice che effettua richieste di dati valide.

### <a name="uri-authority"></a>URI (Autorità)

`ContentProviders`è accessibile in Android utilizzando un Uri. Un'applicazione che `ContentProvider` espone un set il Uris che risponderà nel relativo **AndroidManifest.xml** file. Quando l'applicazione viene installata, questi URI vengono registrati in modo che altre applicazioni possano accedervi.

In Mono per Android, la classe `[ContentProvider]` del provider di contenuti deve avere un attributo per specificare l'URI (o Uris) che deve essere aggiunto a **AndroidManifest.xml**.

### <a name="mime-type"></a>Tipo MIME

Il formato tipico per i tipi MIME è costituito da due parti. Android `ContentProviders` in genere utilizzano queste due stringhe per la prima parte del tipo MIME:

1. `vnd.android.cursor.item`&ndash; per rappresentare una singola `ContentResolver.CursorItemBaseType` riga, utilizzare la costante nel codice.

1. `vnd.android.cursor.dir`&ndash; per più righe, `ContentResolver.CursorDirBaseType` utilizzare la costante nel codice.

La seconda parte del tipo MIME è specifica per l'applicazione e `vnd.` deve utilizzare uno standard DNS inverso con un prefisso. Il codice `vnd.com.xamarin.sample.Vegetables`di esempio utilizza .

### <a name="data-model-metadata"></a>Metadati del modello di datiData Model Metadata

Le applicazioni che utilizzano devono costruire query URI per accedere a diversi tipi di dati. L'URI di base può essere espanso per fare riferimento a una particolare tabella di dati e può anche includere parametri per filtrare i risultati. È inoltre necessario dichiarare le colonne e le clausole utilizzate con il cursore risultante per visualizzare i dati.

Per garantire che vengano costruite solo query Uri valide, è consuetudine fornire le stringhe valide come valori costanti. In questo modo è `ContentProvider` più semplice accedere a metodo perché rende individuabili i valori tramite il completamento del codice e impedisce errori di battitura nelle stringhe.

Nell'esempio precedente `android.provider.ContactsContract` la classe ha esposto i metadati per i dati Contacts. Per la `ContentProvider` nostra abitudine ci limiteremo a esporre le costanti sulla classe stessa.

## <a name="implementation"></a>Implementazione

Esistono tre passaggi per la `ContentProvider`creazione e l'utilizzo di un oggetto personalizzato:

1. **Creare una** &ndash; classe `SQLiteOpenHelper`di database Implement .

2. **Creare `ContentProvider` una** `ContentProvider` classe &ndash; Implement con un'istanza del database, metadati esposti come valori e metodi costanti per accedere ai dati.

3. **Accedere `ContentProvider` al tramite il relativo URI** &ndash; popolare un `CursorAdapter` utilizzando il , a cui si accede tramite il `ContentProvider`relativo URI.

Come illustrato `ContentProviders` in precedenza, può essere utilizzato da applicazioni diverse da quelle in cui sono definite. In questo esempio i dati vengono utilizzati nella stessa applicazione, ma tenere presente che anche altre applicazioni possono accedervi purché conoscano l'URI e le informazioni sullo schema (che in genere vengono esposte come valori costanti).

## <a name="create-a-database"></a>Creare un database

La `ContentProvider` maggior parte delle `SQLite` implementazioni sarà basata su un database. Il codice di database di esempio in SimpleContentProvider/VegetableDatabase.cs crea un database a due colonne molto semplice, come illustrato di seguito:The example database code in **SimpleContentProvider/VegetableDatabase.cs** creates a very simple two-column database, as shown:

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

L'implementazione del database stessa non richiede `ContentProvider`considerazioni speciali per essere `ContentProvider's` esposte `ListView` con un oggetto `_id` , tuttavia se si intende associare i dati a un controllo, una colonna Integer univoca denominata deve far parte del set di risultati. Vedere il [listViews and Adapters](~/android/user-interface/layouts/list-view/index.md) documento `ListView` per ulteriori dettagli sull'utilizzo del controllo.

## <a name="create-the-contentprovider"></a>Creare il ContentProviderCreate the ContentProvider

Il resto di questa sezione fornisce istruzioni dettagliate su come è stata compilata la classe di esempio **SimpleContentProvider/VegetableProvider.cs.**

### <a name="initialize-the-database"></a>Inizializzare il database

Il primo passaggio `ContentProvider` consiste nel creare una sottoclasse e aggiungere il database che verrà utilizzato.

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

Il resto del codice formerà l'implementazione effettiva del provider di contenuti che consente di individuare ed eseguire query sui dati.

## <a name="add-metadata-for-consumers"></a>Aggiungere metadati per i consumerAdd Metadata for Consumers

Esistono quattro diversi tipi di metadati che `ContentProvider` verranno esposti nella classe. Solo l'autorità è richiesta, il resto è fatto per convenzione.

- **Authority** &ndash; `ContentProvider` L'attributo *deve* essere aggiunto alla classe in modo che venga registrato con Android quando viene installata l'applicazione.

- **URI** &ndash; `CONTENT_URI` L'oggetto viene esposto come costante in modo che sia facile da usare nel codice. Deve corrispondere all'Autorità, ma includere lo schema e il percorso di base.

- **Tipi** &ndash; MIME Gli elenchi dei risultati e i singoli risultati vengono trattati come tipi di contenuto diversi, pertanto vengono definiti due tipi MIME per rappresentarli.

- **InterfaceConsts** &ndash; Fornisce un valore costante per ogni nome di colonna di dati, in modo che l'utilizzo di codice possa facilmente individuarli e farvi riferimento senza rischiare errori tipografici.

Questo codice mostra come viene implementato ognuno di questi elementi, aggiungendo alla definizione del database dal passaggio precedente:This code shows how each of these items is implemented, adding to the database definition from the previous step:

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

## <a name="implement-the-uri-parsing-helper"></a>Implementare l'helper di analisi URIImplement the URI Parsing Helper

Poiché l'utilizzo di codice `ContentProvider`utilizza URIs per effettuare richieste di un oggetto , è necessario essere in grado di analizzare tali richieste per determinare i dati da restituire. La `UriMatcher` classe può aiutare ad analizzare Uris, una volta `ContentProvider` che è stato inizializzato con i uri modelli che supporta il.

Nell'esempio verrà inizializzato con due URI:The `UriMatcher` in the example will be initialized with two Uris:

1. *richiesta "com.xamarin.sample.VegetableProvider/vegetables"* &ndash; per restituire l'elenco completo delle verdure.

2. *"com.xamarin.sample.VegetableProvider/vegetables/\#"* &ndash; \# dove il è un segnaposto `_id` per un parametro numerico (la della riga nel database). Un segnaposto\*asterisco (" ") può essere utilizzato anche per trovare una corrispondenza con un parametro di testo.

Nel codice utilizziamo le costanti per fare riferimento a\_valori di metadati come AUTHORITY e BASE PATH. I codici restituiti verranno utilizzati nei metodi che eseguono l'analisi Uri per determinare i dati da restituire.

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

Questo codice è tutto `ContentProvider` privato per la classe. Consulta la [documentazione UriMatcher di Google](xref:Android.Content.UriMatcher) per ulteriori informazioni.

## <a name="implement-the-querymethod"></a>Implementare il Metodo QueryMethodImplement the QueryMethod

Il `ContentProvider` metodo più semplice `Query` da implementare è il metodo. L'implementazione `UriMatcher` seguente utilizza `uri` l'oggetto per analizzare il parametro e chiamare il metodo di database corretto. Se `uri` l'oggetto contiene un parametro ID, `LastPathSegment`il numero intero viene analizzato (utilizzando ) e utilizzato nella query del database.

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

È `GetType` inoltre necessario eseguire l'override del metodo. Questo metodo può essere chiamato per determinare il tipo di contenuto che verrà restituito per un determinato URI.
Ciò potrebbe indicare all'applicazione consumer come gestire i dati.

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

## <a name="implement-the-other-overrides"></a>Implementare le altre sostituzioniImplement the Other Overrides

Il nostro semplice esempio non consente la modifica o l'eliminazione dei dati, ma i metodi Insert, Update e Delete devono essere implementati in modo da aggiungerli senza un'implementazione:

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

Ciò completa `ContentProvider` l'implementazione di base. Una volta installata l'applicazione, i dati esposti saranno disponibili sia all'interno dell'applicazione che a qualsiasi altra applicazione che conosce l'URI per farvi riferimento.

## <a name="access-the-contentprovider"></a>Accedere a ContentProvider

Una `VegetableProvider` volta implementato, l'accesso viene eseguito allo stesso modo del provider Contacts all'inizio di questo documento: ottenere un cursore utilizzando l'URI specificato e quindi utilizzare un adattatore per accedere ai dati.

## <a name="bind-a-listview-to-a-contentprovider"></a>Associare un Controllo ListView a un oggetto ContentProviderBind a ListView to a ContentProvider

Per popolare `ListView` un con i dati usiamo l'URI che corrisponde all'elenco non filtrato di verdure. Nel codice viene utilizzato `VegetableProvider.CONTENT_URI`il valore costante , `com.xamarin.sample.vegetableprovider/vegetables`che sappiamo si risolve in . La `VegetableProvider.Query` nostra implementazione restituirà un cursore che può quindi essere associato al `ListView`.

Il codice `SimpleContentProvider/HomeScreen.cs` in mostra quanto sia semplice `ContentProvider`visualizzare i dati da un :

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

L'applicazione risultante è simile alla seguente:The resulting application looks like this:

[![Screenshot dell'app che elenca verdure, frutta, gemme di fiori, legumi, bulbi, tuberi](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)

## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Recuperare un singolo elemento da un ContentProviderRetrieve a Single Item from a ContentProvider

Un'applicazione consumer potrebbe anche voler accedere a singole righe di dati, operazione che può essere eseguita creando un URI diverso che fa riferimento a una riga specifica, ad esempio.

Utilizzare `ContentResolver` direttamente per accedere a un singolo elemento, `Id`creando un URI con il file .

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

Il metodo completo è simile al seguente:The complete method looks like this:

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

- [SimpleContentProvider (esempio)SimpleContentProvider (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
