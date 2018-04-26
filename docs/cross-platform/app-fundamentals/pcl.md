---
title: Introduzione alle librerie di classi portabile
description: In questo articolo vengono presentati i progetti libreria di classe portabile (PCL) e vengono illustrati la creazione e l'utilizzo di progetti di libreria di classi Portabile in Visual Studio per Mac e Visual Studio.
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 799ee198f776b963bb8779d71bacc322001e2882
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2018
---
# <a name="introduction-to-portable-class-libraries"></a>Introduzione alle librerie di classi portabile

_In questo articolo vengono presentati i progetti libreria di classe portabile (PCL) e vengono illustrati la creazione e l'utilizzo di progetti di libreria di classi Portabile in Visual Studio per Mac e Visual Studio._


Un componente fondamentale della compilazione di applicazioni multipiattaforma è la possibilità di condividere il codice tra i vari progetti specifici della piattaforma. Tuttavia, questo è complicato dal fatto che usano spesso un set diverso di sottodirectory della libreria di classi Base (BCL) .NET, piattaforme diverse e sono effettivamente compilate a un altro profilo di libreria .NET Core. Ciò significa che è possibile utilizzare solo le librerie di classi di destinazione per lo stesso profilo appaiono per richiedere i progetti libreria di classi separato per ogni piattaforma per ogni piattaforma.

Sono disponibili tre approcci principali per la condivisione del codice che consentono di risolvere il problema: **progetti .NET Standard**, **progetti libreria di classe portabile (PCL)**, e **progetti di Asset condivisi**.

- **Progetti .NET standard** [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md).
-  **Libreria di classi Portabile** progetti destinati a profili specifici che supportano un set noto di BCL classi o le funzionalità. Tuttavia, il lato verso il basso per libreria di classi Portabile è che richiedono spesso molto architetturale allo scopo di separare il codice specifico di profilo nelle proprie librerie. Per informazioni più dettagliate su questi due approcci, vedere il [opzioni di condivisione del codice Guida](~/cross-platform/app-fundamentals/code-sharing.md) .
-  **Progetti di Asset condivisi** usare un singolo set di file e offre un modo rapido e semplice in cui si desidera condividere il codice all'interno di una soluzione e in genere utilizza le direttive di compilazione condizionale per specificare i percorsi del codice per diverse piattaforme in uso (per ulteriori informazioni informazioni, vedere il [articolo progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md) e [impostazione di una Guida di soluzione multipiattaforma con Xamarin](~/cross-platform/app-fundamentals/code-sharing.md) ).


Questa pagina viene illustrato come creare un **PCL** progetto destinato a un profilo specifico, che è possibile fare riferimento da più progetti specifici della piattaforma.


## <a name="what-is-a-portable-class-library"></a>Che cos'è una libreria di classi portabile?

Quando si crea un progetto di applicazione o un progetto di libreria, il file DLL risultante è limitata a operano sulla piattaforma specifica di che cui viene creato. Ciò impedisce all'utente di scrivere un'app di Windows in un assembly e quindi usare nuovamente in xamarin. IOS e xamarin.

Quando si crea una libreria di classi portabile, tuttavia, è possibile scegliere una combinazione delle piattaforme che si desidera che il codice per l'esecuzione. Compatibilità scelte effettuate durante la creazione di una libreria di classi portabile vengono convertite in un identificatore "Profilo", che descrive le piattaforme su cui la libreria supporta.

Nella tabella seguente vengono illustrate alcune delle funzionalità che variano in base alla piattaforma .NET. Per scrivere un assembly libreria di classi Portabile che è sicuramente eseguito su dispositivi/piattaforme specifiche, è sufficiente scegliere cui supporto è obbligatorio quando si crea il progetto.

|Funzionalità|.NET Framework|App UWP|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|Base|Y|Y|Y|Y|Y|
|LINQ|Y|Y|Y|Y|Y|
|IQueryable|Y|Y|Y|7.5 +|Y|
|Serializzazione|Y|Y|Y|Y|Y|
|Annotazioni dei dati|4.0.3 +|Y|Y||Y|

La colonna Xamarin dovuto al fatto che xamarin. IOS e xamarin supporta tutti i profili forniti con Visual Studio e la disponibilità di funzionalità nelle librerie create sarà limitata solo da altre piattaforme, che si sceglie di supportare.

Sono inclusi i profili di combinazioni di:

-  .NET 4 o .NET 4.5
-  Silverlight 5
-  Windows Phone 8
-  App UWP

Altre informazioni sulle funzionalità di diversi profili in [del sito Web Microsoft](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx) e vedere un altro membro della community [profilo PCL riepilogo](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) che include informazioni sul framework e ad altre note è supportata.



Creazione di una libreria di classi Portabile per condividere il codice ha una serie di vantaggi e svantaggi e il collegamento di File alternativo:


**Vantaggi**

1. Condivisione del codice centralizzata: scrivere e testare il codice in un unico progetto che può essere utilizzato da altre applicazioni o librerie.
1. Le operazioni di refactoring influirà tutto il codice caricato nella soluzione (libreria di classi portabile e i progetti specifici della piattaforma).
1. Progetto PCL possa essere facilmente a cui fa riferimento da altri progetti in una soluzione, o l'assembly di output può essere condivisa di riferimento nelle soluzioni di altri utenti.


**Svantaggi**

1. Poiché la stessa libreria di classi portabile è condiviso tra più applicazioni, librerie specifiche della piattaforma non possono fare riferimento (ad es. Community.CsharpSqlite.WP7).
1. Il subset di libreria di classi portabile potrebbe non includere le classi che diversamente sarebbero visibili MonoTouch sia Mono per Android (ad esempio DllImport o System.IO).



In una certa misura entrambi gli svantaggi possono essere aggirati utilizzando il modello del Provider o l'inserimento di dipendenze per l'implementazione effettiva nei progetti di piattaforma su un'interfaccia o una classe base che è definito nella libreria di classi portabile di codice.



Questo diagramma illustra l'architettura di un'applicazione e multipiattaforma usando una libreria di classi portabile per condividere codice, ma anche le opzioni di inserimento di dipendenze di passare la funzionalità dipendente dalla piattaforma:



[![](pcl-images/image1.png "Questo diagramma mostra l'architettura di un'applicazione e multipiattaforma usando una libreria di classi portabile per condividere codice, ma anche le opzioni di inserimento di dipendenze di passare la funzionalità dipendente dalla piattaforma")](pcl-images/image1.png#lightbox)



# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)



## <a name="visual-studio-for-mac-walkthrough"></a>Procedura dettagliata per Visual Studio per Mac


In questa sezione illustra in dettaglio come creare e usare una libreria di classi portabile con Visual Studio per Mac. Vedere la sezione esempio di libreria di classi Portabile per un'implementazione completa.



### <a name="creating-a-pcl"></a>Creazione di una libreria di classi Portabile


Aggiunta di una libreria di classi portabile per la soluzione è molto simile all'aggiunta di un progetto di libreria regolare.


1. Nella finestra di dialogo Nuovo progetto, selezionare il `Multiplatform > Library > Portable Library` opzione


    ![](pcl-images/image2.png "Multipiattaforma > Libreria > libreria portabile")


1. Quando viene creata una libreria di classi Portabile in Visual Studio per Mac viene configurato automaticamente con un profilo che funziona per xamarin. IOS e xamarin. Il progetto PCL verrà visualizzato come illustrato in questo screenshot:

    ![](pcl-images/image3.png "Verrà visualizzato il progetto libreria di classi Portabile, come illustrato in questa schermata")

Libreria di classi Portabile è ora pronto per il codice da aggiungere. Anche possibile fare riferimento da altri progetti (progetti di applicazione, i progetti di libreria e anche altri progetti libreria di classi Portabile).



### <a name="editing-pcl-settings"></a>Modifica delle impostazioni di libreria di classi Portabile


Per visualizzare e modificare le impostazioni di libreria di classi Portabile per questo progetto, fare clic sul progetto e scegliere **Opzioni > compilare > Generale** per visualizzare la schermata illustrata di seguito:



[![](pcl-images/image4.png "Per visualizzare e modificare le impostazioni di libreria di classi Portabile per questo progetto, fare clic sul progetto e scegliere le opzioni di compilazione generale per visualizzare la schermata visualizzata qui")](pcl-images/image4.png#lightbox)



Le impostazioni in questa schermata di controllare quali piattaforme è compatibile con questa particolare libreria di classi Portabile. La modifica di una di queste opzioni consente di modificare il profilo utilizzato da questa libreria di classi Portabile, che a sua volta controlla quali funzionalità possono essere utilizzati nel codice portabile.



Modificare qualsiasi il `Target Framework` Aggiorna automaticamente le opzioni di `Current Profile`; la schermata verrà inoltre visualizzato un avviso se si selezionano le opzioni incompatibili.



[![](pcl-images/image5.png "Modificando le opzioni Framework di destinazione viene automaticamente aggiornato il profilo corrente la schermata verrà inoltre visualizzato un avviso se si selezionano le opzioni incompatibili")](pcl-images/image5.png#lightbox)



Se il profilo viene modificato dopo il codice è già stato aggiunto a una libreria di classi Portabile, è possibile che la libreria non verrà più compilato se il codice fa riferimento a funzionalità che non fanno parte del profilo appena selezionata.


## <a name="working-with-a-pcl"></a>Utilizzo di una libreria di classi Portabile


Quando il codice è scritto in una libreria PCL, Visual Studio per editor Mac riconoscerà le limitazioni del profilo selezionato e modificare le opzioni di completamento automatico in modo appropriato. Ad esempio, questa schermata mostra le opzioni di completamento automatico per System.IO usando il profilo predefinito (Profile136) utilizzato in Visual Studio per Mac: si noti la barra di scorrimento indica circa la metà delle classi disponibili sono visualizzata (in realtà sono disponibili solo 14 classi disponibili).



[![](pcl-images/image6.png "IO che utilizzano il profilo predefinito Profile136 utilizzata in Visual Studio per Mac si noti la barra di scorrimento indica circa la metà delle classi disponibili sono visualizzata in realtà sono solo 14 classi disponibili")](pcl-images/image6.png#lightbox)



Confrontare con il System.IO completamento automatico in un progetto xamarin. IOS o xamarin: sono presenti 40 utilizzate comunemente disponibili tra cui classi come `File` e `Directory` che non sono presenti in un profilo della libreria di classi Portabile.



[![](pcl-images/image7.png "Esistono 40 utilizzate comunemente disponibili tra cui classi come File e Directory che non si trovano in un profilo della libreria di classi Portabile")](pcl-images/image7.png#lightbox)



Ciò riflette il compromesso sottostante dell'utilizzo PCL: la possibilità di condividere facilmente il codice su molte piattaforme significa che alcune API non sono disponibili poiché non dispongono di implementazioni simili in tutte le piattaforme possibili.



### <a name="using-pcl"></a>Utilizzando una libreria di classi Portabile


Dopo aver creato un progetto libreria di classi Portabile, è possibile aggiungere un riferimento da qualsiasi progetto di applicazioni o librerie compatibile nello stesso modo, che si aggiungono in genere i riferimenti. In Visual Studio per Mac, fare clic sul nodo Riferimenti e scegliere `Edit References…` quindi passare alla scheda progetti, come illustrato:



[![](pcl-images/image8.png "In Visual Studio per Mac, fare doppio clic sul nodo Riferimenti e scegliere Modifica riferimenti, quindi passare alla scheda progetti, come illustrato")](pcl-images/image8.png#lightbox)



La schermata seguente mostra il riquadro di soluzione per l'applicazione di esempio TaskyPortable, che mostra la libreria PCL nella parte inferiore e un riferimento a tale raccolta PCL nel progetto xamarin. IOS.



[![](pcl-images/image9.png "Il riquadro di soluzione per l'applicazione di esempio TaskyPortable")](pcl-images/image9.png#lightbox)



L'output di una libreria di classi Portabile (ie. la DLL dell'assembly risultante) può essere inoltre aggiunto come riferimento per la maggior parte dei progetti. In questo modo PCL una soluzione ideale per fornire le librerie e componenti multipiattaforma.




# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)




## <a name="visual-studio-walkthrough"></a>Procedura dettagliata in Visual Studio


In questa sezione illustra in dettaglio come creare e usare una libreria di classi portabile con Visual Studio. Vedere la sezione esempio di libreria di classi Portabile per un'implementazione completa.



### <a name="creating-a-pcl"></a>Creazione di una libreria di classi Portabile


Aggiunta di una libreria di classi Portabile alla soluzione in Visual Studio è leggermente diverso per l'aggiunta di un progetto regolari.



1. Nella schermata Aggiungi nuovo progetto, selezionare il `Portable Class Library` opzione


    ![](pcl-images/image10.png "Libreria di classi portabile")


1. Visual Studio verrà immediatamente richiesto con la seguente finestra di dialogo in modo che sia possibile configurare il profilo.
 Seleziona le piattaforme, che è necessario per supportare e fare clic su OK.


    ![](pcl-images/image11.png "Seleziona le piattaforme, che è necessario per supportare e fare clic su OK")


1. Il progetto PCL verrà visualizzati come mostrato in Esplora soluzioni. Il nodo Riferimenti indicherà che la libreria viene utilizzato un subset di .NET Framework (definiti dal profilo di PCL).

    ![](pcl-images/image12.png "NET Framework definiti dal profilo di libreria di classi Portabile")

Libreria di classi Portabile è ora pronto per il codice da aggiungere. Anche possibile fare riferimento da altri progetti (progetti di applicazione, i progetti di libreria e anche altri progetti libreria di classi Portabile).



### <a name="editing-pcl-settings"></a>Modifica delle impostazioni di libreria di classi Portabile


Le impostazioni della libreria di classi Portabile possono essere visualizzate e modificate facendo clic sul progetto e scegliendo **proprietà > libreria** , come illustrato in questo screenshot:



[![](pcl-images/image13.png "Le impostazioni della libreria di classi Portabile possono essere visualizzate e modificate facendo clic sul progetto e scegliendo Proprietà libreria, come illustrato in questa schermata")](pcl-images/image13.png#lightbox)



Se il profilo viene modificato dopo il codice è già stato aggiunto a una libreria di classi Portabile, è possibile che la libreria non verrà più compilato se il codice fa riferimento a funzionalità che non fanno parte del profilo appena selezionata.



### <a name="working-with-a-pcl"></a>Utilizzo di una libreria di classi Portabile


Quando il codice è scritto in una libreria PCL, Visual Studio riconoscerà le limitazioni del profilo selezionato e modificare le opzioni di Intellisense in modo appropriato. Ad esempio, questa schermata mostra le opzioni di completamento automatico per System.IO usando il profilo predefinito (Profile136): si noti la barra di scorrimento indica circa la metà delle classi disponibili sono visualizzata (in realtà sono disponibili solo le 14 classi).



[![](pcl-images/image14.png "IO che utilizzano il profilo predefinito Profile136")](pcl-images/image14.png#lightbox)



Confrontare con il System.IO completamento automatico in un progetto regolari: sono presenti 40 utilizzate comunemente disponibili tra cui classi come `File` e `Directory` che non sono presenti in un profilo della libreria di classi Portabile.



[![](pcl-images/image15.png "Completamento automatico in un progetto regolari")](pcl-images/image15.png#lightbox)



Ciò riflette il compromesso sottostante dell'utilizzo PCL: la possibilità di condividere facilmente il codice su molte piattaforme significa che alcune API non sono disponibili poiché non dispongono di implementazioni simili in tutte le piattaforme possibili.



### <a name="using-pcl"></a>Utilizzando una libreria di classi Portabile


Dopo aver creato un progetto libreria di classi Portabile, è possibile aggiungere un riferimento da qualsiasi progetto di applicazioni o librerie compatibile nello stesso modo, che si aggiungono in genere i riferimenti. In Visual Studio, fare clic sul nodo Riferimenti e scegliere `Add Reference...` quindi passare il **soluzione: progetti** scheda come illustrato:



[![](pcl-images/image16.png "Scheda progetti come mostrato")](pcl-images/image16.png#lightbox)



Nella schermata seguente mostra il riquadro di soluzione per l'app di esempio TaskyPortable, che mostra la libreria PCL nella parte inferiore e un riferimento a tale raccolta PCL nel progetto xamarin. IOS.



[![](pcl-images/image17.png "Riquadro della soluzione per l'app di esempio TaskyPortable")](pcl-images/image17.png#lightbox)



L'output di una libreria di classi Portabile (ie. la DLL dell'assembly risultante) può essere inoltre aggiunto come riferimento per la maggior parte dei progetti.
In questo modo PCL una soluzione ideale per fornire le librerie e componenti multipiattaforma.




-----



## <a name="pcl-example"></a>Esempio di libreria di classi Portabile


Il [TaskyPortable](https://developer.xamarin.com/samples/mobile/TaskyPortable/) applicazione di esempio viene illustrato come utilizzare una libreria di classi portabile con Xamarin.
Ecco alcune schermate delle App risultante in esecuzione su iOS, Android e Windows Phone:



[![](pcl-images/image18.png "Ecco alcune schermate delle App risultante in esecuzione su iOS, Android e Windows Phone")](pcl-images/image18.png#lightbox)



Condivide una serie di classi di dati e logica di codice portabile puramente e viene inoltre illustrato come incorporare i requisiti specifici della piattaforma mediante l'inserimento di dipendenze per l'implementazione di database SQLite.




Di seguito è riportata la struttura della soluzione (in Visual Studio per Mac e Visual Studio rispettivamente):



[![](pcl-images/image19.png "La struttura della soluzione è illustrata di seguito in Visual Studio per Mac e Visual Studio rispettivamente")](pcl-images/image19.png#lightbox)



Poiché il codice di SQLite NET dispone parti specifiche della piattaforma (lavorare con le implementazioni di SQLite in ogni sistema operativo diverso) per dimostrazione scopi è stata sottoposta a refactoring in una classe astratta classe che può essere compilato in una libreria di classi portabile, e il codice effettivo implementato come sottoclassi nei progetti iOS e Android.



### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

Libreria di classi portabile è limitata in grado di supportare le funzionalità .NET. Poiché viene compilato per l'esecuzione su più piattaforme, non riesce a stabilire utilizzare `[DllImport]` funzionalità che vengono utilizzati in SQLite NET. Invece SQLite NET viene implementata come una classe astratta e quindi viene fatto riferimento tramite il resto del codice condiviso. Di seguito è riportato un estratto dell'API astratta:


```csharp
public abstract class SQLiteConnection : IDisposable {

    public string DatabasePath { get; private set; }
    public bool TimeExecution { get; set; }
    public bool Trace { get; set; }
    public SQLiteConnection(string databasePath) {
         DatabasePath = databasePath;
    }
    public abstract int CreateTable<T>();
    public abstract SQLiteCommand CreateCommand(string cmdText, params object[] ps);
    public abstract int Execute(string query, params object[] args);
    public abstract List<T> Query<T>(string query, params object[] args) where T : new();
    public abstract TableQuery<T> Table<T>() where T : new();
    public abstract T Get<T>(object pk) where T : new();
    public bool IsInTransaction { get; protected set; }
    public abstract void BeginTransaction();
    public abstract void Rollback();
    public abstract void Commit();
    public abstract void RunInTransaction(Action action);
    public abstract int Insert(object obj);
    public abstract int Update(object obj);
    public abstract int Delete<T>(T obj);

    public void Dispose()
    {
        Close();
    }
    public abstract void Close();

}
```


Nella parte restante di codice condiviso utilizza la classe astratta per "archivio" e "recuperare" oggetti dal database. In qualsiasi applicazione che utilizza questa classe astratta è necessario passare un'implementazione completa che fornisce la funzionalità di database effettivo.



### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid e TaskyiOS


Di iOS e i progetti di applicazione Android contengono l'interfaccia utente e altro codice specifico della piattaforma usato per il codice condiviso nella libreria di classi Portabile wire-up.



I progetti contengono anche un'implementazione astratta API di database che funziona su questa piattaforma. In iOS e Android di Sqlite è integrato nel sistema operativo, il motore di database consentono di usare l'implementazione `[DllImport]` come illustrato per fornire l'implementazione concreta di connettività del database. In seguito è riportato un frammento di codice di implementazione specifica della piattaforma:


```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```


L'implementazione completa può essere visualizzato nel codice di esempio.

### <a name="taskywinphone"></a>TaskyWinPhone


L'applicazione di Windows Phone è relativa interfaccia utente compilata con XAML e contiene altri codice specifico della piattaforma per la connessione di oggetti condivisi con l'interfaccia utente.



A differenza di implementazione utilizzato per iOS e Android, l'app di Windows Phone è necessario creare e utilizzare un'istanza di `Community.Sqlite.dll` API di database come parte della relativa classe astratta. Anziché utilizzare `DllImport`, rispetto all'assembly Community.Sqlite cui fa riferimento vengono implementati i metodi come l'apertura di `TaskWinPhone` progetto. Un frammento è illustrato qui, per il confronto con la iOS e Android versione precedente


```csharp
public static Result Open(string filename, out Sqlite3.sqlite3 db)
{
    db = new Sqlite3.sqlite3();
    return (Result)Sqlite3.sqlite3_open(filename, ref db);
}

public static Result Close(Sqlite3.sqlite3 db)
{
    return (Result)Sqlite3.sqlite3_close(db);
}
```


## <a name="summary"></a>Riepilogo


In questo articolo è brevemente descritti i vantaggi e i problemi di librerie di classi portabili, è stato illustrato come creare e utilizzare PCLs da Visual Studio per Mac e Visual Studio. e infine introdotta un'applicazione di esempio completo – TaskyPortable – che mostra una libreria di classi Portabile in azione.


## <a name="related-links"></a>Collegamenti correlati

- [TaskyPortable (esempio)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Creazione di applicazioni multipiattaforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Portatile Visual Basic](~/cross-platform/platform/visual-basic/index.md)
- [Progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md)
- [Sharing Code Options](~/cross-platform/app-fundamentals/code-sharing.md)
- [Lo sviluppo multipiattaforma con .NET Framework (Microsoft)](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
