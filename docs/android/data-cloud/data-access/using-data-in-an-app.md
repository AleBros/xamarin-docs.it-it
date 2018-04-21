---
title: Utilizzo di dati in un'App per Android
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: b79b2e44e79a6ff75b096c7443f6d46c20e27144
ms.sourcegitcommit: 797597d902330652195931dec9ac3e0cc00792c5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2018
---
# <a name="using-data-in-an-app"></a>Utilizzo di dati in un'App

Il **DataAccess_Adv** illustra un'applicazione funzionante che consente l'input dell'utente e le funzionalità di database CRUD (Create, Read, Update e Delete). L'applicazione è costituita da due schermate: un elenco e un form di immissione dati. Tutto il codice di accesso ai dati è riutilizzabile in iOS e Android senza alcuna modifica.

Dopo avere aggiunto alcuni dati le schermate dell'applicazione simile al seguente in Android:

![Elenco di esempi Android](using-data-in-an-app-images/image11.png "elenco di esempi di Android")

![Dettagli campione Android](using-data-in-an-app-images/image12.png "dettagli campione Android")

Di seguito è riportato il progetto Android &ndash; il codice illustrato in questa sezione è contenuto all'interno di **Orm** directory:

![Struttura ad albero di progetto Android](using-data-in-an-app-images/image14.png "della struttura di progetto Android")

Il codice dell'interfaccia utente nativo per le attività in Android esula dall'ambito di questo documento. Consultare il [Android controlli ListView e schede](~/android/user-interface/layouts/list-view/index.md) Guida per ulteriori informazioni sui controlli dell'interfaccia utente.

## <a name="read"></a>Lettura

Esistono un paio di operazioni di lettura nell'esempio:

-  La lettura dell'elenco
-  Lettura dei singoli record

I due metodi di `StockDatabase` sono:

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

## <a name="create-and-update"></a>Creazione e aggiornamento

Per semplificare il codice dell'applicazione, un singolo metodo di salvataggio è fornito che esegue un'istruzione Insert o Update a seconda se è stata impostata la PrimaryKey. Poiché il `Id` proprietà è contrassegnata con un `[PrimaryKey]` attributo, è consigliabile non impostare nel codice. Questo metodo consentirà di rilevare se il valore è stato precedente salvata (selezionando la proprietà di chiave primaria) e inserire o aggiornare di conseguenza l'oggetto:

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

Applicazioni reali in genere richiedono una convalida (ad esempio i campi obbligatori, lunghezza minima o altre regole di business). Le applicazioni multipiattaforma buona implementare la maggior parte della convalida logica possibile nel codice condiviso, il passaggio di errori di convalida, eseguire il backup l'interfaccia utente per la visualizzazione in base alle funzionalità della piattaforma.

## <a name="delete"></a>Eliminare

A differenza di `Insert` e `Update` metodi, il `Delete<T>` metodo può accettare solo il valore di chiave primaria anziché completa `Stock` oggetto. In questo esempio un `Stock` oggetto viene passato al metodo, ma solo la proprietà Id viene passata al `Delete<T>` metodo.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Utilizzo di un file di database SQLite prepopolato

Alcune applicazioni vengono forniti con un database già popolato con dati. Si può facilmente questo scopo nell'applicazione per dispositivi mobili per la spedizione di un file di database SQLite esistente con l'app e copiarlo in una directory accessibile in scrittura prima di accedervi. Poiché SQLite è un formato di file standard che viene utilizzato in molte piattaforme, sono disponibili numerosi strumenti disponibili per creare un file di database SQLite:

-   **Estensione di Firefox Manager SQLite** &ndash; funziona su Mac e Windows e genera file che sono compatibili con iOS e Android.

-   **Riga di comando** &ndash; vedere [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .

Prestare attenzione quando si crea un file di database per la distribuzione con l'app, con la denominazione di tabelle e colonne per assicurarsi che corrispondano cosa prevede che il codice, in particolare se si utilizza SQLite.NET che si aspetta di ricevere i nomi in modo che corrisponda l'in c# le classi e proprietà (o attributi personalizzati associati).

Per garantire l'esecuzione di codice prima di qualsiasi altra nell'app Android, è possibile inserirlo nella prima attività per caricare o creare un `Application` sottoclasse che viene caricata prima di qualsiasi attività. Il codice seguente viene illustrato un `Application` sottoclasse che copia un file di database esistente **data.sqlite** fuori il **/Resources./Raw/** directory.

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
- [Recipe dati Android](https://developer.xamarin.com/recipes/android/data/)
- [Accesso ai dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
