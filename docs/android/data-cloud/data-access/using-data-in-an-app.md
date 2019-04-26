---
title: Uso dei dati in un'App per Android
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: e9af8df3cbe6f125788f298be6a181472c1cd27d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61012913"
---
# <a name="using-data-in-an-app"></a>Uso dei dati in un'app

Il **DataAccess_Adv** illustra un'applicazione funzionante che consente l'input dell'utente e le funzionalità di database CRUD (Create, Read, Update e Delete). L'applicazione è costituita da due schermate seguenti: un elenco e un modulo di immissione dati. Tutto il codice di accesso di dati è riutilizzato in iOS e Android senza alcuna modifica.

Dopo aver aggiunto alcuni dati le schermate dell'applicazione l'aspetto seguente in Android:

![Elenco di esempi di Android](using-data-in-an-app-images/image11.png "elenco di esempi di Android")

![Dettagli di esempio di Android](using-data-in-an-app-images/image12.png "dettaglio di esempio di Android")

Di seguito è riportato il progetto Android &ndash; il codice riportato in questa sezione è contenuto all'interno di **Orm** directory:

![Struttura del progetto Android](using-data-in-an-app-images/image14.png "albero del progetto Android")

Il codice dell'interfaccia utente nativo per le attività in Android non è compreso nell'ambito di questo documento. Vedere le [Android ListView e adapter](~/android/user-interface/layouts/list-view/index.md) Guida per altre informazioni sui controlli dell'interfaccia utente.

## <a name="read"></a>Lettura

Esistono un paio di operazioni di lettura nell'esempio:

-  La lettura dell'elenco
-  La lettura dei singoli record

I due metodi nel `StockDatabase` classe sono:

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

Android viene eseguito il rendering dei dati come un `ListView`.

## <a name="create-and-update"></a>Creare e aggiornare

Per semplificare il codice dell'applicazione, un singolo metodo di salvataggio è fornito che consente di eseguire un'istruzione Insert o Update a seconda che sia stata impostata il PrimaryKey. Poiché il `Id` proprietà è contrassegnata con un `[PrimaryKey]` attributo è consigliabile non impostarlo nel codice. Questo metodo rileva se il valore è stato precedente salvata (controllando la proprietà di chiave primaria) e inserire o aggiornare di conseguenza l'oggetto:

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

Applicazioni reali richiede in genere una convalida (ad esempio i campi obbligatori, le lunghezze minima o altre regole di business). Le applicazioni multipiattaforma buona implementare la maggior parte della convalida logico possibile nel codice condiviso, il passaggio di errori di convalida, eseguire il backup l'interfaccia utente per la visualizzazione in base alle funzionalità della piattaforma.

## <a name="delete"></a>Eliminare

A differenza di `Insert` e `Update` metodi, il `Delete<T>` metodo accetta semplicemente il valore di chiave primaria anziché completa `Stock` oggetto. In questo esempio un `Stock` oggetto viene passato al metodo, ma solo la proprietà Id viene passata al `Delete<T>` (metodo).

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Utilizzo di un file di database SQLite prepopolato

Alcune applicazioni vengono forniti con un database già popolato con i dati. È possibile farlo facilmente nell'applicazione per dispositivi mobili da un file di database SQLite esistente con l'app di spedizione e copiarla in una directory scrivibile prima dell'accesso. Poiché SQLite è un formato di file standard che viene usato in molte piattaforme, sono disponibili numerosi strumenti disponibili per creare un file di database SQLite:

-   **Estensione per Firefox Manager SQLite** &ndash; funziona su Mac e Windows e produce file che sono compatibili con iOS e Android.

-   **Riga di comando** &ndash; vedere [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .

Prestare attenzione quando si crea un file di database per la distribuzione con l'app, con la denominazione di tabelle e colonne per assicurarsi che corrispondano a ciò che prevede che il codice, soprattutto se si usa SQLite.NET che si aspetta di ricevere i nomi in base ai in c# le classi e proprietà (o il attributi personalizzati associati).

Per garantire che venga eseguito codice prima di altre operazioni nell'app per Android, è possibile inserirlo nella prima attività per caricare o è possibile creare un `Application` sottoclasse che viene caricata prima di qualsiasi attività. Il codice seguente mostra un' `Application` sottoclasse che copia un file di database esistente **data.sqlite** fuori il **/Resources./Raw/** directory.

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
- [DataAccess avanzate (esempio)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Ricette dei dati Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
