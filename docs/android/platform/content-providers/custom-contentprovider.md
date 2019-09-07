---
title: Creazione di un ContentProvider personalizzato
description: Nella sezione precedente è stato illustrato come utilizzare i dati di un'implementazione di ContentProvider incorporata. In questa sezione viene illustrato come creare un ContentProvider personalizzato e quindi utilizzarne i dati.
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: f72baaa4c74eb4bf0bb5eec64211d6ea2b18076c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756674"
---
# <a name="creating-a-custom-contentprovider"></a>Creazione di un ContentProvider personalizzato

_Nella sezione precedente è stato illustrato come utilizzare i dati di un'implementazione di ContentProvider incorporata. In questa sezione viene illustrato come creare un ContentProvider personalizzato e quindi utilizzarne i dati._

## <a name="about-contentproviders"></a>Informazioni su ContentProviders

Una classe del provider di contenuti deve `ContentProvider`ereditare da. Deve essere costituito da un archivio dati interno utilizzato per rispondere alle query ed esporre gli URI e i tipi MIME come costanti per consentire l'utilizzo del codice per eseguire richieste valide per i dati.

### <a name="uri-authority"></a>URI (autorità)

`ContentProviders`è possibile accedervi in Android usando un URI. Un'applicazione che espone un `ContentProvider` oggetto imposta gli URI a cui risponderà nel file **file AndroidManifest. XML** . Quando l'applicazione viene installata, questi URI vengono registrati in modo che le altre applicazioni possano accedervi.

In mono per Android la classe del provider di contenuti deve avere `[ContentProvider]` un attributo per specificare l'URI (o gli URI) che devono essere aggiunti a **file AndroidManifest. XML**.

### <a name="mime-type"></a>Tipo MIME

Il formato tipico per i tipi MIME è costituito da due parti. Android `ContentProviders` usa comunemente queste due stringhe per la prima parte del tipo MIME:

1. `vnd.android.cursor.item`per rappresentare una singola riga, usare la `ContentResolver.CursorItemBaseType` costante nel codice. &ndash;

1. `vnd.android.cursor.dir`per più righe, usare la `ContentResolver.CursorDirBaseType` costante nel codice. &ndash;

La seconda parte del tipo MIME è specifica dell'applicazione e deve usare uno standard DNS inverso con un `vnd.` prefisso. Il codice di esempio `vnd.com.xamarin.sample.Vegetables`USA.

### <a name="data-model-metadata"></a>Metadati del modello di dati

Per l'utilizzo di applicazioni è necessario creare query URI per accedere a tipi di dati diversi. L'URI di base può essere espanso per fare riferimento a una particolare tabella di dati e può includere anche parametri per filtrare i risultati. Anche le colonne e le clausole utilizzate con il cursore risultante per la visualizzazione dei dati devono essere dichiarate.

Per assicurarsi che vengano costruite solo query URI valide, è consuetudine fornire le stringhe valide come valori costanti. In questo modo è più semplice accedere `ContentProvider` a perché rende individuabili i valori tramite il completamento del codice e impedisce la digitazione nelle stringhe.

Nell'esempio precedente la `android.provider.ContactsContract` classe ha esposto i metadati per i dati dei contatti. Per la nostra `ContentProvider` personalizzata, esporrà solo le costanti sulla classe stessa.

## <a name="implementation"></a>Implementazione

Sono disponibili tre passaggi per la creazione e l'utilizzo di `ContentProvider`un oggetto personalizzato:

1. **Creare una classe di database** &ndash; Implementare .`SQLiteOpenHelper`

2. **Creare una `ContentProvider` classe** &ndash; implementata`ContentProvider` con un'istanza del database, i metadati esposti come valori costanti e metodi per accedere ai dati.

3. **Accedere al `ContentProvider` tramite il relativo URI** &ndash; popolare `CursorAdapter` un oggetto `ContentProvider`utilizzando l'oggetto, a cui si accede tramite il relativo URI.

Come descritto in precedenza `ContentProviders` , può essere utilizzato da applicazioni diverse da quelle in cui sono definite. In questo esempio i dati vengono utilizzati nella stessa applicazione, ma tenere presente che anche altre applicazioni possono accedervi purché conoscano l'URI e le informazioni sullo schema, che in genere viene esposto come valori costanti.

## <a name="create-a-database"></a>Creare un database

La `ContentProvider` maggior parte delle implementazioni sarà basata `SQLite` su un database. Il codice del database di esempio in **SimpleContentProvider/VegetableDatabase. cs** crea un database con due colonne molto semplice, come illustrato di seguito:

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

Per l'implementazione del database non è necessaria alcuna considerazione speciale da esporre con un `ContentProvider`, tuttavia se si intende associare i `ContentProvider's` dati a un `ListView` controllo, una colonna integer univoca denominata `_id` deve essere parte del set di risultati. Per ulteriori informazioni sull'utilizzo del `ListView` controllo, vedere il documento [listviews and Adapters](~/android/user-interface/layouts/list-view/index.md) .

## <a name="create-the-contentprovider"></a>Creare il ContentProvider

Nella parte restante di questa sezione vengono fornite istruzioni dettagliate su come è stata compilata la classe di esempio **SimpleContentProvider/VegetableProvider. cs** .

### <a name="initialize-the-database"></a>Inizializzare il database

Il primo passaggio consiste nella sottoclasse `ContentProvider` e nell'aggiunta del database che verrà utilizzato.

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

Il resto del codice formerà l'effettiva implementazione del provider di contenuti che consente l'individuazione e la query dei dati.

## <a name="add-metadata-for-consumers"></a>Aggiungere metadati per i consumer

Sono disponibili quattro tipi diversi di metadati che verranno esposti `ContentProvider` alla classe. Solo l'autorità è obbligatoria, il resto viene eseguito per convenzione.

- **Autorità** di certificazione L'attributo deve essere aggiunto alla classe in modo che sia registrato con Android quando viene installata l'applicazione. &ndash; `ContentProvider`

- **URI** di &ndash; Il`CONTENT_URI` viene esposto come costante in modo che sia facile da usare nel codice. Deve corrispondere all'autorità, ma includere lo schema e il percorso di base.

- **Tipi MIME** &ndash; Gli elenchi dei risultati e dei singoli risultati vengono considerati tipi di contenuto diversi, quindi vengono definiti due tipi MIME per rappresentarli.

- **InterfaceConsts** &ndash; Fornire un valore costante per ogni nome di colonna di dati, in modo che il codice consumer possa facilmente individuarlo e farvi riferimento senza rischiare errori tipografici.

Questo codice Mostra come ognuno di questi elementi viene implementato, aggiungendo alla definizione del database dal passaggio precedente:

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

## <a name="implement-the-uri-parsing-helper"></a>Implementare l'helper di analisi URI

Poiché l'utilizzo di codice utilizza URI per eseguire richieste di `ContentProvider`un, è necessario essere in grado di analizzare tali richieste per determinare quali dati restituire. La `UriMatcher` classe può essere utile per analizzare gli URI, una volta che è stata inizializzata con i `ContentProvider` modelli URI supportati da.

`UriMatcher` Nell'esempio verrà inizializzato con due URI:

1. *"com. Novell. Sample. VegetableProvider/ortaggi"* &ndash; richiesta di restituire l'elenco completo di ortaggi.

2. *"com. Novell. Sample. VegetableProvider/vegetables\#/"* &ndash; dove \# è un segnaposto per un parametro numerico ( `_id` la proprietà della riga nel database). Un segnaposto asterisco (\*"") può essere usato anche per trovare la corrispondenza con un parametro di testo.

Nel codice vengono usate le costanti per fare riferimento ai valori dei metadati, ad esempio l'autorità\_e il percorso di base. I codici restituiti verranno utilizzati nei metodi che eseguono l'analisi URI per determinare quali dati restituire.

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

Questo codice è completamente privato per la `ContentProvider` classe. Per ulteriori informazioni, fare riferimento alla [documentazione di UriMatcher di Google](xref:Android.Content.UriMatcher) .

## <a name="implement-the-querymethod"></a>Implementare QueryMethod

Il `ContentProvider` metodo più semplice da implementare è il `Query` metodo. Nell'implementazione seguente viene utilizzato `UriMatcher` per analizzare il `uri` parametro e chiamare il metodo di database corretto. Se contiene un parametro ID, il valore integer verrà analizzato (utilizzando `LastPathSegment`) e utilizzato nella query di database. `uri`

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

Anche `GetType` il metodo deve essere sottoposto a override. Questo metodo può essere chiamato per determinare il tipo di contenuto che verrà restituito per un URI specificato.
Questo potrebbe indicare all'applicazione che utilizza come gestire i dati.

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

Questo semplice esempio non consente la modifica o l'eliminazione dei dati, ma è necessario implementare i metodi Insert, Update e DELETE, quindi aggiungerli senza implementare:

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

Viene completata l'implementazione di `ContentProvider` base. Una volta installata l'applicazione, i dati esposti saranno disponibili sia all'interno dell'applicazione, ma anche a qualsiasi altra applicazione che conosce l'URI a cui fa riferimento.

## <a name="access-the-contentprovider"></a>Accedere a ContentProvider

`VegetableProvider` Una volta implementato, l'accesso viene eseguito in modo analogo al provider Contacts all'inizio del documento: ottenere un cursore utilizzando l'URI specificato e quindi utilizzare un adapter per accedere ai dati.

## <a name="bind-a-listview-to-a-contentprovider"></a>Associare un controllo ListView a un ContentProvider

Per popolare un `ListView` oggetto con i dati, viene usato l'URI che corrisponde all'elenco non filtrato di ortaggi. Nel codice viene usato il valore `VegetableProvider.CONTENT_URI`costante, che è noto come `com.xamarin.sample.vegetableprovider/vegetables`risolto. L'implementazione restituirà un cursore che può quindi essere associato `ListView`a. `VegetableProvider.Query`

Il codice in `SimpleContentProvider/HomeScreen.cs` Mostra la semplicità di visualizzazione dei dati da un: `ContentProvider`

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

L'applicazione risultante ha un aspetto simile al seguente:

[![Screenshot dell'app che elenca verdure, frutti, boccioli fiori, legumi, lampadine, tuberi](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)

## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Recuperare un singolo elemento da un ContentProvider

Un'applicazione consumer può anche voler accedere a singole righe di dati, operazione che può essere eseguita costruendo un URI diverso che fa riferimento a una riga specifica (ad esempio).

Utilizzare `ContentResolver` direttamente per accedere a un singolo elemento, creando un URI con l'oggetto obbligatorio `Id`.

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

Il metodo completo ha un aspetto simile al seguente:

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

- [SimpleContentProvider (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
