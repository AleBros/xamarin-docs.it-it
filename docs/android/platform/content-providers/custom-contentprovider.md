---
title: Creazione di un ContentProvider personalizzato
description: Nella sezione precedente è stato illustrato come utilizzare i dati di un'implementazione di ContentProvider incorporata. In questa sezione viene illustrato come creare un ContentProvider personalizzato e quindi utilizzarne i dati.
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3e57e0cd2fa87db8035fa68995b69f231151fa09
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020535"
---
# <a name="creating-a-custom-contentprovider"></a>Creazione di un ContentProvider personalizzato

_Nella sezione precedente è stato illustrato come utilizzare i dati di un'implementazione di ContentProvider incorporata. In questa sezione viene illustrato come creare un ContentProvider personalizzato e quindi utilizzarne i dati._

## <a name="about-contentproviders"></a>Informazioni su ContentProviders

Una classe del provider di contenuti deve ereditare da `ContentProvider`. Deve essere costituito da un archivio dati interno utilizzato per rispondere alle query ed esporre gli URI e i tipi MIME come costanti per consentire l'utilizzo del codice per eseguire richieste valide per i dati.

### <a name="uri-authority"></a>URI (autorità)

è possibile accedere a `ContentProviders` in Android usando un URI. Un'applicazione che espone un `ContentProvider` imposta gli URI a cui risponderà nel file **file AndroidManifest. XML** . Quando l'applicazione viene installata, questi URI vengono registrati in modo che le altre applicazioni possano accedervi.

In mono per Android la classe del provider di contenuti deve avere un attributo `[ContentProvider]` per specificare l'URI (o gli URI) che devono essere aggiunti a **file AndroidManifest. XML**.

### <a name="mime-type"></a>Tipo MIME

Il formato tipico per i tipi MIME è costituito da due parti. Android `ContentProviders` comunemente usano queste due stringhe per la prima parte del tipo MIME:

1. `vnd.android.cursor.item` &ndash; per rappresentare una singola riga, usare la costante `ContentResolver.CursorItemBaseType` nel codice.

1. `vnd.android.cursor.dir` &ndash; per più righe, usare la costante `ContentResolver.CursorDirBaseType` nel codice.

La seconda parte del tipo MIME è specifica dell'applicazione e deve usare uno standard DNS inverso con un prefisso `vnd.`. Il codice di esempio USA `vnd.com.xamarin.sample.Vegetables`.

### <a name="data-model-metadata"></a>Metadati del modello di dati

Per l'utilizzo di applicazioni è necessario creare query URI per accedere a tipi di dati diversi. L'URI di base può essere espanso per fare riferimento a una particolare tabella di dati e può includere anche parametri per filtrare i risultati. Anche le colonne e le clausole utilizzate con il cursore risultante per la visualizzazione dei dati devono essere dichiarate.

Per assicurarsi che vengano costruite solo query URI valide, è consuetudine fornire le stringhe valide come valori costanti. In questo modo è più semplice accedere al `ContentProvider` perché rende i valori individuabili tramite il completamento del codice e impedisce la digitazione nelle stringhe.

Nell'esempio precedente la classe `android.provider.ContactsContract` espone i metadati per i dati dei contatti. Per la `ContentProvider` personalizzata si esporrà solo le costanti sulla classe stessa.

## <a name="implementation"></a>Implementazione

Per la creazione e l'utilizzo di un `ContentProvider`personalizzato sono necessari tre passaggi:

1. **Creare una classe di database** &ndash; implementare `SQLiteOpenHelper`.

2. **Creare una classe `ContentProvider`** &ndash; implementare `ContentProvider` con un'istanza del database, i metadati esposti come valori e metodi costanti per accedere ai dati.

3. **Accedere al `ContentProvider` tramite il relativo uri** &ndash; popolare un `CursorAdapter` usando il `ContentProvider`, a cui si accede tramite il relativo URI.

Come descritto in precedenza, `ContentProviders` può essere utilizzato da applicazioni diverse da quelle in cui sono definite. In questo esempio i dati vengono utilizzati nella stessa applicazione, ma tenere presente che anche altre applicazioni possono accedervi purché conoscano l'URI e le informazioni sullo schema, che in genere viene esposto come valori costanti.

## <a name="create-a-database"></a>Creare un database

La maggior parte delle implementazioni di `ContentProvider` sarà basata su un database `SQLite`. Il codice del database di esempio in **SimpleContentProvider/VegetableDatabase. cs** crea un database con due colonne molto semplice, come illustrato di seguito:

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

L'implementazione del database non necessita di considerazioni speciali da esporre con una `ContentProvider`. Tuttavia, se si intende associare i dati `ContentProvider's` a un controllo `ListView`, una colonna integer univoca denominata `_id` deve far parte del set di risultati. Per ulteriori informazioni sull'utilizzo del controllo `ListView`, vedere il documento [listviews and Adapters](~/android/user-interface/layouts/list-view/index.md) .

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

Sono disponibili quattro tipi diversi di metadati che verranno esposti sulla classe `ContentProvider`. Solo l'autorità è obbligatoria, il resto viene eseguito per convenzione.

- **Autorità** &ndash; l'attributo `ContentProvider` *deve* essere aggiunto alla classe in modo che sia registrato con Android quando viene installata l'applicazione.

- **Uri** &ndash; il `CONTENT_URI` viene esposto come costante in modo che sia facile da usare nel codice. Deve corrispondere all'autorità, ma includere lo schema e il percorso di base.

- I **tipi mime** &ndash; gli elenchi di risultati e i singoli risultati vengono considerati tipi di contenuto diversi, quindi vengono definiti due tipi MIME per rappresentarli.

- **InterfaceConsts** &ndash; fornire un valore costante per ogni nome di colonna di dati, in modo che il codice consumer possa facilmente individuarli e farvi riferimento senza rischiare errori tipografici.

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

Poiché l'utilizzo di codice usa gli URI per eseguire richieste di una `ContentProvider`, è necessario essere in grado di analizzare tali richieste per determinare quali dati restituire. La classe `UriMatcher` può semplificare l'analisi degli URI, una volta che è stata inizializzata con i modelli URI supportati dall'`ContentProvider`.

Il `UriMatcher` nell'esempio verrà inizializzato con due URI:

1. *"com. Novell. Sample. VegetableProvider/vegetables"* &ndash; richiesta di restituire l'elenco completo di ortaggi.

2. *"com. Novell. Sample. VegetableProvider/vegetables/\#"* &ndash; dove il \# è un segnaposto per un parametro numerico (il `_id` della riga nel database). Un segnaposto asterisco ("\*") può essere usato anche per trovare la corrispondenza con un parametro di testo.

Nel codice vengono usate le costanti per fare riferimento ai valori dei metadati, ad esempio l'autorità e il percorso di\_di BASE. I codici restituiti verranno utilizzati nei metodi che eseguono l'analisi URI per determinare quali dati restituire.

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

Questo codice è interamente privato per la classe `ContentProvider`. Per ulteriori informazioni, fare riferimento alla [documentazione di UriMatcher di Google](xref:Android.Content.UriMatcher) .

## <a name="implement-the-querymethod"></a>Implementare QueryMethod

Il metodo `ContentProvider` più semplice da implementare è il `Query` metodo. L'implementazione seguente usa il `UriMatcher` per analizzare il parametro di `uri` e chiamare il metodo di database corretto. Se il `uri` contiene un parametro ID, il valore integer verrà analizzato (usando `LastPathSegment`) e usato nella query del database.

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

È necessario eseguire l'override anche del metodo `GetType`. Questo metodo può essere chiamato per determinare il tipo di contenuto che verrà restituito per un URI specificato.
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

Questa operazione completa l'implementazione del `ContentProvider` di base. Una volta installata l'applicazione, i dati esposti saranno disponibili sia all'interno dell'applicazione, ma anche a qualsiasi altra applicazione che conosce l'URI a cui fa riferimento.

## <a name="access-the-contentprovider"></a>Accedere a ContentProvider

Una volta implementata la `VegetableProvider`, l'accesso viene eseguita in modo analogo al provider Contacts all'inizio del documento: ottenere un cursore utilizzando l'URI specificato e quindi utilizzare un adapter per accedere ai dati.

## <a name="bind-a-listview-to-a-contentprovider"></a>Associare un controllo ListView a un ContentProvider

Per popolare un `ListView` con i dati, viene usato l'URI che corrisponde all'elenco non filtrato di ortaggi. Nel codice viene usato il valore costante `VegetableProvider.CONTENT_URI`, che è noto come risolto `com.xamarin.sample.vegetableprovider/vegetables`. L'implementazione del `VegetableProvider.Query` restituirà un cursore che può quindi essere associato al `ListView`.

Il codice in `SimpleContentProvider/HomeScreen.cs` Mostra quanto sia semplice visualizzare i dati da un `ContentProvider`:

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

[![screenshot dell'app che elenca verdure, frutti, boccioli fiori, legumi, lampadine, tuberi](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)

## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Recuperare un singolo elemento da un ContentProvider

Un'applicazione consumer può anche voler accedere a singole righe di dati, operazione che può essere eseguita costruendo un URI diverso che fa riferimento a una riga specifica (ad esempio).

Utilizzare `ContentResolver` direttamente per accedere a un singolo elemento, creando un URI con la `Id`richiesta.

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

- [SimpleContentProvider (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
