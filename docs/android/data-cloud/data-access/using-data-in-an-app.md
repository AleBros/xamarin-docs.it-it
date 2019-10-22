---
title: Uso dei dati in un'app Android
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 922b1fa411a176df580050384e7555120fd68137
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70754449"
---
# <a name="using-data-in-an-app"></a>Uso dei dati in un'app

L'esempio **DataAccess_Adv** Mostra un'applicazione funzionante che consente la funzionalità di database di input utente e CRUD (creazione, lettura, aggiornamento ed eliminazione). L'applicazione è costituita da due schermate: un elenco e un form di immissione dati. Tutto il codice di accesso ai dati è riutilizzabile in iOS e Android senza modifiche.

Dopo l'aggiunta di alcuni dati, le schermate dell'applicazione hanno un aspetto simile al seguente in Android:

![Elenco di esempi Android](using-data-in-an-app-images/image11.png "Elenco di esempi Android")

![Dettagli dell'esempio Android](using-data-in-an-app-images/image12.png "Dettagli dell'esempio Android")

Il progetto Android è riportato di seguito &ndash; il codice illustrato in questa sezione è contenuto nella directory **ORM** :

![Albero del progetto Android](using-data-in-an-app-images/image14.png "Albero del progetto Android")

Il codice dell'interfaccia utente nativa per le attività in Android è esterno all'ambito di questo documento. Per altre informazioni sui controlli dell'interfaccia utente, vedere la guida per i [ListView e gli adapter Android](~/android/user-interface/layouts/list-view/index.md) .

## <a name="read"></a>Lettura

Nell'esempio sono presenti due operazioni di lettura:

- Lettura dell'elenco
- Lettura di singoli record

I due metodi della classe `StockDatabase` sono:

```csharp
public IEnumerable<Stock> GetStocks ()
{
    lock (locker) {
        return (from i in Table<Stock> () select i).ToList ();
    }
}
public Stock GetStock (int id)
{
    lock (locker) {
        return Table<Stock>().FirstOrDefault(x => x.Id == id);
    }
}
```

Android esegue il rendering dei dati come `ListView`.

## <a name="create-and-update"></a>Creazione e aggiornamento

Per semplificare il codice dell'applicazione, viene fornito un singolo metodo Save che esegue un inserimento o un aggiornamento a seconda che sia stato impostato PrimaryKey. Poiché la proprietà `Id` è contrassegnata con un attributo `[PrimaryKey]` non è necessario impostarla nel codice. Questo metodo consente di rilevare se il valore è stato salvato in precedenza (controllando la proprietà della chiave primaria) e inserire o aggiornare l'oggetto di conseguenza:

```csharp
public int SaveStock (Stock item)
{
    lock (locker) {
        if (item.Id != 0) {
            Update (item);
            return item.Id;
    } else {
            return Insert (item);
        }
    }
}
```

Le applicazioni reali richiederanno in genere una convalida, ad esempio campi obbligatori, lunghezze minime o altre regole business. Le applicazioni multipiattaforma valide implementano la maggior parte del possibile di convalida nel codice condiviso, passando gli errori di convalida all'interfaccia utente per la visualizzazione in base alle funzionalità della piattaforma.

## <a name="delete"></a>Eliminazione

A differenza dei metodi `Insert` e `Update`, il metodo `Delete<T>` può accettare solo il valore della chiave primaria invece di un oggetto `Stock` completo. In questo esempio un oggetto `Stock` viene passato al metodo, ma solo la proprietà ID viene passata al metodo `Delete<T>`.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Uso di un file di database SQLite pre-popolato

Alcune applicazioni vengono fornite con un database già popolato con i dati. Per eseguire questa operazione nell'applicazione per dispositivi mobili, è possibile spedire un file di database SQLite esistente all'app e copiarlo in una directory scrivibile prima di accedervi. Poiché SQLite è un formato di file standard usato in molte piattaforme, sono disponibili diversi strumenti per creare un file di database SQLite:

- **SQLite Manager Firefox Extension** &ndash; funziona su Mac e Windows e produce file compatibili con iOS e Android.

- **Riga di comando** &ndash; vedere [www.sqlite.org/SQLite.html](http://www.sqlite.org/sqlite.html) .

Quando si crea un file di database per la distribuzione con l'app, prestare attenzione alla denominazione di tabelle e colonne per assicurarsi che corrispondano a quanto previsto dal codice, specialmente se si usa SQLite.NET, che prevede che i nomi C# corrispondano alle classi e alle proprietà ( o gli attributi personalizzati associati).

Per assicurarsi che il codice venga eseguito prima di qualsiasi altro elemento nell'app Android, è possibile inserirlo nella prima attività per il caricamento oppure è possibile creare una sottoclasse `Application` caricata prima di qualsiasi attività. Il codice seguente mostra una sottoclasse `Application` che copia un file di database esistente **. sqlite** dalla directory **/Resources/RAW/** .

```csharp
[Application]
public class YourAndroidApp : Application {
    public override void OnCreate ()
    {
        base.OnCreate ();
        var docFolder = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
        Console.WriteLine ("Data path:" + Database.DatabaseFilePath);
        var dbFile = Path.Combine(docFolder, "data.sqlite"); // FILE NAME TO USE WHEN COPIED
        if (!System.IO.File.Exists(dbFile)) {
            var s = Resources.OpenRawResource(Resource.Raw.data);  // DATA FILE RESOURCE ID
            FileStream writeStream = new FileStream(dbFile, FileMode.OpenOrCreate, FileAccess.Write);
            ReadWriteStream(s, writeStream);
        }
    }
    // readStream is the stream you need to read
    // writeStream is the stream you want to write to
    private void ReadWriteStream(Stream readStream, Stream writeStream)
    {
        int Length = 256;
        Byte[] buffer = new Byte[Length];
        int bytesRead = readStream.Read(buffer, 0, Length);
        // write the required bytes
        while (bytesRead > 0)
        {
            writeStream.Write(buffer, 0, bytesRead);
            bytesRead = readStream.Read(buffer, 0, Length);
        }
        readStream.Close();
        writeStream.Close();
    }
}
```

## <a name="related-links"></a>Collegamenti correlati

- [DataAccess Basic (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Ricette per i dati Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accesso ai dati di Novell. Forms](~/xamarin-forms/data-cloud/data/databases.md)
