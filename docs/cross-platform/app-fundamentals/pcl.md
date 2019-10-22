---
title: Introduzione alle librerie di classi portabili (PCL)
description: In questo articolo vengono presentati i progetti libreria di classi portabile (PCL) e vengono illustrati i passaggi per la creazione e l'utilizzo di progetti PCL in Visual Studio per Mac e Visual Studio.
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: a4ee81f7d59c9fb680dfd371a7aaba7660fb3343
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "68681066"
---
# <a name="portable-class-libraries-pcl"></a>Librerie di classi portabili

> [!TIP]
> Le librerie di classi portabili (classi portabili) sono considerate deprecate nelle versioni più recenti di Visual Studio.
> Sebbene sia ancora possibile aprire, modificare e compilare classi portabili, per i nuovi progetti è consigliabile usare [.NET standard librerie](~/cross-platform/app-fundamentals/net-standard.md) per accedere a una superficie di attacco API più ampia.

Un componente chiave della creazione di applicazioni multipiattaforma è la possibilità di condividere il codice tra diversi progetti specifici della piattaforma. Tuttavia, questo è complicato dal fatto che diverse piattaforme utilizzano spesso un subset diverso della libreria di classi base .NET (BCL) e pertanto sono effettivamente compilate in un profilo della libreria .NET Core diverso. Ciò significa che ogni piattaforma può utilizzare solo le librerie di classi destinate allo stesso profilo, in modo che risultino necessari progetti di libreria di classi separate per ogni piattaforma.

Esistono tre approcci principali alla condivisione del codice che affrontano questo problema: **.NET standard progetti**, **progetti di asset condivisi**e **progetti libreria di classi portabile (PCL)** .

- I **progetti .NET standard** rappresentano l'approccio preferito per la condivisione del codice .NET, altre informazioni sui [progetti di .NET standard e Novell](~/cross-platform/app-fundamentals/net-standard.md).
- I **progetti di asset condivisi** utilizzano un unico set di file e offrono un modo semplice e rapido per condividere il codice all'interno di una soluzione e in genere utilizzano direttive di compilazione condizionale per specificare i percorsi del codice per varie piattaforme che lo utilizzeranno (per altre informazioni per informazioni, vedere l' [articolo progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md).
- I progetti **PCL** sono destinati a profili specifici che supportano un set noto di classi/funzionalità BCL. Tuttavia, il lato inferiore di PCL è che spesso richiedono un lavoro di architettura aggiuntivo per separare il codice specifico del profilo nelle rispettive librerie.

In questa pagina viene illustrato come creare un progetto **PCL** destinato a un profilo specifico, a cui è possibile fare riferimento da più progetti specifici della piattaforma.

## <a name="what-is-a-portable-class-library"></a>Che cos'è una libreria di classi portabile?

Quando si crea un progetto di applicazione o un progetto di libreria, la DLL risultante è limitata all'utilizzo della piattaforma per la quale viene creato. Ciò impedisce di scrivere un assembly per un'app di Windows e quindi di riutilizzarlo in Novell. iOS e Novell. Android.

Quando si crea una libreria di classi portabile, tuttavia, è possibile scegliere una combinazione di piattaforme in cui si vuole eseguire il codice. Le scelte di compatibilità effettuate durante la creazione di una libreria di classi portabile vengono convertite in un identificatore "profile", che descrive le piattaforme supportate dalla libreria.

La tabella seguente illustra alcune delle funzionalità che variano in base alla piattaforma .NET. Per scrivere un assembly PCL garantito per l'esecuzione su dispositivi/piattaforme specifici, è sufficiente scegliere il supporto necessario quando si crea il progetto.

|Funzionalità|.NET Framework|App UWP|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|Base|Y|Y|Y|Y|Y|
|LINQ|Y|Y|Y|Y|Y|
|IQueryable|Y|Y|Y|7,5 +|Y|
|Serializzazione|Y|Y|Y|Y|Y|
|Annotazioni dei dati|4.0.3 +|Y|Y||Y|

La colonna Novell riflette il fatto che Novell. iOS e Novell. Android supportano tutti i profili forniti con Visual Studio e la disponibilità delle funzionalità in tutte le librerie create sarà limitata solo dalle altre piattaforme che si sceglie di supportare.

Sono inclusi i profili che sono combinazioni di:

- .NET 4 o .NET 4,5
- Silverlight 5
- Windows Phone 8
- App UWP

Per altre informazioni sulle funzionalità dei diversi profili, vedere il [sito Web di Microsoft](https://msdn.microsoft.com/library/gg597391(v=vs.110).aspx) e vedere il riepilogo del [profilo PCL](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) di un altro membro della community, che include informazioni del Framework supportate e altre note.

**Vantaggi**

1. Condivisione del codice centralizzata: scrivere e testare il codice in un singolo progetto che può essere utilizzato da altre librerie o applicazioni.
2. Le operazioni di refactoring avranno effetto su tutto il codice caricato nella soluzione (la libreria di classi portabile e i progetti specifici della piattaforma).
3. Il progetto PCL può essere facilmente usato per fare riferimento ad altri progetti in una soluzione oppure è possibile condividere l'assembly di output per fare in modo che altri utenti facciano riferimento alle soluzioni.

**Svantaggi**

1. Poiché la stessa libreria di classi portabile è condivisa tra più applicazioni, non è possibile fare riferimento alle librerie specifiche della piattaforma, ad esempio Community. CsharpSqlite. WP7).
2. Il subset della libreria di classi portabile non può includere classi che altrimenti sarebbero disponibili sia in MonoTouch che in mono per Android, ad esempio DllImport o System. IO. file.

> [!NOTE]
> Le librerie di classi portabili sono state deprecate nella versione più recente di Visual Studio e invece sono consigliate le [librerie .NET standard](net-standard.md) .

In alcuni casi è possibile aggirare entrambi gli svantaggi usando il modello di provider o l'inserimento delle dipendenze per codificare l'implementazione effettiva nei progetti della piattaforma rispetto a un'interfaccia o a una classe di base definita nella libreria di classi portabile.

Questo diagramma illustra l'architettura di un'applicazione multipiattaforma che usa una libreria di classi portabile per condividere il codice, ma anche l'uso dell'inserimento delle dipendenze per passare le funzionalità dipendenti dalla piattaforma:

[![](pcl-images/image1.png "This diagram shows the architecture of a cross-platform application using a Portable Class Library to share code, but also using Dependency Injection to pass in platform-dependent features")](pcl-images/image1.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Procedura dettagliata Visual Studio per Mac

Questa sezione illustra come creare e usare una libreria di classi portabile usando Visual Studio per Mac. Per un'implementazione completa, fare riferimento alla sezione esempio per la libreria PCL.

### <a name="creating-a-pcl"></a>Creazione di una libreria di classi portabile

L'aggiunta di una libreria di classi portabile alla soluzione è molto simile all'aggiunta di un normale progetto di libreria.

1. Nella finestra di dialogo **nuovo progetto** selezionare l'opzione **Libreria > multipiattaforma > Libreria portatile** :

    ![Creazione di un nuovo progetto PCL](pcl-images/image2.png)

1. Quando viene creata una libreria di classi portabile in Visual Studio per Mac viene automaticamente configurata con un profilo che funziona per Novell. iOS e Novell. Android. Il progetto PCL verrà visualizzato come illustrato nello screenshot seguente:

    ![Progetto PCL nel riquadro della soluzione](pcl-images/image3.png)

Il PCL è ora pronto per l'aggiunta del codice. È anche possibile farvi riferimento da altri progetti (progetti di applicazioni, progetti di libreria e anche altri progetti PCL).

### <a name="editing-pcl-settings"></a>Modifica delle impostazioni di PCL

Per visualizzare e modificare le impostazioni di PCL per questo progetto, fare clic con il pulsante destro del mouse sul progetto e scegliere **opzioni > compila > generale** per visualizzare la schermata riportata di seguito:

[![PCL le opzioni del progetto per impostare il profilo](pcl-images/image4-sml.png)](pcl-images/image4.png#lightbox)

Fare clic su **Cambia..** . per modificare il profilo di destinazione per la libreria di classi portabile.

Se il profilo viene modificato dopo che il codice è già stato aggiunto alla PCL, è possibile che la libreria non venga più compilata se il codice fa riferimento a funzionalità che non fanno parte del profilo appena selezionato.

## <a name="working-with-a-pcl"></a>Utilizzo di una libreria di classi portabile

Quando il codice viene scritto in una libreria di librerie di classi portabili, l'editor di Visual Studio per Mac rileverà le limitazioni del profilo selezionato e modificherà di conseguenza le opzioni di completamento automatico. Questo screenshot, ad esempio, Mostra le opzioni di completamento automatico per System.IO usando il profilo predefinito (Profile136) usato nella Visual Studio per Mac: si noti la barra di scorrimento che indica circa la metà delle classi disponibili. in effetti, sono presenti solo 14 classi disponibili).

[![Intellisense elenco di 14 classi nella classe System.IO di una libreria di classi portabile](pcl-images/image6.png)](pcl-images/image6.png#lightbox)

Confrontare con il completamento automatico di System.IO in un progetto Novell. iOS o Novell. Android. sono disponibili 40 classi, tra cui classi di uso comune come `File` e `Directory` che non si trovano in alcun profilo PCL.

[![Intellisense elenco di classi 40 nello spazio dei nomi .NET Framework System.IO](pcl-images/image7.png)](pcl-images/image7.png#lightbox)

Questo rispecchia il compromesso sottostante rispetto all'uso di PCL: la possibilità di condividere il codice senza interruzioni in molte piattaforme significa che alcune API non sono disponibili perché non hanno implementazioni confrontabili in tutte le piattaforme possibili.

### <a name="using-pcl"></a>Uso di PCL

Una volta creato un progetto di libreria di classi portabile, è possibile aggiungere un riferimento a esso da qualsiasi applicazione o progetto di libreria compatibile nello stesso modo in cui si aggiungono in genere i riferimenti. In Visual Studio per Mac fare clic con il pulsante destro del mouse sul nodo riferimenti e scegliere **modifica riferimenti...** quindi passare alla scheda **progetti** come illustrato:

[![Add un riferimento a una libreria di classi portabile tramite l'opzione modifica riferimenti](pcl-images/image8.png)](pcl-images/image8.png#lightbox)

Lo screenshot seguente mostra il riquadro della soluzione per l'app di esempio TaskyPortable, che mostra la libreria PCL nella parte inferiore e un riferimento a tale libreria PCL nel progetto Novell. iOS.

[![TaskyPortable soluzione di esempio che mostra il progetto PCL](pcl-images/image9.png)](pcl-images/image9.png#lightbox)

L'output di una libreria di classi portabile (ad esempio, la DLL di assembly risultante) può essere aggiunto anche come riferimento alla maggior parte dei progetti. Questo rende la libreria di classi portabile una soluzione ideale per la distribuzione di componenti e librerie multipiattaforma.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Procedura dettagliata di Visual Studio

Questa sezione illustra come creare e usare una libreria di classi portabile con Visual Studio. Per un'implementazione completa, fare riferimento alla sezione esempio per la libreria PCL.

### <a name="creating-a-pcl"></a>Creazione di una libreria di classi portabile

L'aggiunta di una libreria di classi portabile alla soluzione in Visual Studio è leggermente diversa dall'aggiunta di un normale progetto:

1. Nella schermata **Aggiungi nuovo progetto** selezionare l'opzione **libreria di classi (portabile legacy)** . Si noti che la descrizione a destra informa che questo tipo di progetto è stato deprecato.

    [![Finestra nuovo progetto per creare una libreria di classi portabile](pcl-images/image10-sml.png "Libreria di classi portabile")](pcl-images/image10.png#lightbox)

2. In Visual Studio verrà immediatamente visualizzata la finestra di dialogo seguente in modo che sia possibile configurare il profilo.
 Selezionare le piattaforme che è necessario supportare e fare clic su OK.

    [![Selezionare le piattaforme di destinazione per la libreria](pcl-images/image11-sml.png "Selezionare le piattaforme che è necessario supportare e premere OK")](pcl-images/image11.png#lightbox)

3. Il progetto PCL verrà visualizzato come illustrato nella Esplora soluzioni &ndash; viene visualizzato il testo (portabile) accanto al nome del progetto per indicare che si tratta di una libreria di classi portabile **(PCL)** :

    ![NET Framework definito dal profilo PCL](pcl-images/image12.png "NET Framework definito dal profilo PCL")

Il PCL è ora pronto per l'aggiunta del codice. È anche possibile farvi riferimento da altri progetti (progetti di applicazioni, progetti di libreria e anche altri progetti PCL).

### <a name="editing-pcl-settings"></a>Modifica delle impostazioni di PCL

Le impostazioni di PCL possono essere visualizzate e modificate facendo clic con il pulsante destro del mouse sul progetto e scegliendo **proprietà > libreria** , come illustrato nello screenshot seguente:

[![Edit le destinazioni della piattaforma](pcl-images/image13-sml.png)](pcl-images/image13.png#lightbox)

Se il profilo viene modificato dopo che il codice è già stato aggiunto alla PCL, è possibile che la libreria non venga più compilata se il codice fa riferimento a funzionalità che non fanno parte del profilo appena selezionato.

> [!TIP]
> Inoltre, è presente un messaggio che ne consiglia **. NETStandard è il metodo consigliato per la condivisione del codice**. Questo indica che, mentre classi portabili sono ancora supportati, è consigliabile eseguire l'aggiornamento a .NET Standard.

### <a name="working-with-a-pcl"></a>Utilizzo di una libreria di classi portabile

Quando il codice viene scritto in una libreria PCL, Visual Studio riconosce le limitazioni del profilo selezionato e modifica di conseguenza le opzioni di IntelliSense. Questo screenshot, ad esempio, Mostra le opzioni di completamento automatico per System.IO usando il profilo predefinito (Profile136). si noti la barra di scorrimento che indica circa la metà delle classi disponibili. in realtà, sono disponibili solo 14 classi.

[![Reduced numero di classi IO disponibili in una libreria di classi portabile](pcl-images/image14.png)](pcl-images/image14.png#lightbox)

Confrontare con il completamento automatico di System.IO in un progetto normale. sono disponibili 40 classi, tra cui classi di uso comune come `File` e `Directory` che non si trovano in alcun profilo PCL.

[![Many più classi di i/o disponibili nell'.NET Framework](pcl-images/image15.png)](pcl-images/image15.png#lightbox)

Questo rispecchia il compromesso sottostante rispetto all'uso di PCL: la possibilità di condividere il codice senza interruzioni in molte piattaforme significa che alcune API non sono disponibili perché non hanno implementazioni confrontabili in tutte le piattaforme possibili.

> [!TIP]
> .NET Standard 2,0 rappresenta una superficie di attacco API molto più ampia rispetto a classi portabili, incluso lo spazio dei nomi System.IO. Per i nuovi progetti, è consigliabile .NET Standard su PCL.

### <a name="using-pcl"></a>Uso di PCL

Una volta creato un progetto di libreria di classi portabile, è possibile aggiungere un riferimento a esso da qualsiasi applicazione o progetto di libreria compatibile nello stesso modo in cui si aggiungono in genere i riferimenti. In Visual Studio fare clic con il pulsante destro del mouse sul nodo riferimenti e scegliere `Add Reference...` quindi passare alla scheda **progetti > soluzione** , come illustrato di seguito:

[![Add un riferimento a una libreria di classi portabile tramite la scheda Aggiungi progetti di riferimento](pcl-images/image16.png)](pcl-images/image16.png#lightbox)

La schermata seguente mostra il riquadro della soluzione per l'app di esempio TaskyPortable, che mostra la libreria PCL nella parte inferiore e un riferimento a tale libreria PCL nel progetto Novell. iOS.

[![TaskyPortable soluzione di esempio che mostra una libreria PCL](pcl-images/image17.png)](pcl-images/image17.png#lightbox)

L'output di una libreria di classi portabile (ad esempio, la DLL di assembly risultante) può essere aggiunto anche come riferimento alla maggior parte dei progetti.
Questo rende la libreria di classi portabile una soluzione ideale per la distribuzione di componenti e librerie multipiattaforma.

-----

## <a name="pcl-example"></a>Esempio di PCL

L'applicazione di esempio [TaskyPortable](https://docs.microsoft.com/samples/xamarin/mobile-samples/taskyportable/) illustra come è possibile usare una libreria di classi portabile con Novell.
Ecco alcune schermate delle app risultanti eseguite in iOS e Android:

[![](pcl-images/image18.png "Here are some screenshots of the resulting apps running on iOS, Android and Windows Phone")](pcl-images/image18.png#lightbox)

Condivide una serie di dati e classi di logica che sono codice puramente portabile, oltre a illustrare come incorporare i requisiti specifici della piattaforma usando l'inserimento di dipendenze per l'implementazione del database SQLite.

La struttura della soluzione è illustrata di seguito (in Visual Studio per Mac e in Visual Studio rispettivamente):

[![](pcl-images/image19.png "The solution structure is shown here in Visual Studio for Mac and Visual Studio respectively")](pcl-images/image19.png#lightbox)

Poiché il codice SQLite-NET presenta parti specifiche della piattaforma (per lavorare con le implementazioni di SQLite in ogni sistema operativo diverso) a scopo dimostrativo, è stato eseguito il refactoring in una classe astratta che può essere compilata in una libreria di classi portabile e il codice effettivo implementato come sottoclassi nei progetti iOS e Android.

### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

La libreria di classi portabile è limitata nelle funzionalità .NET che può supportare. Poiché è compilato per l'esecuzione su più piattaforme, non può usare la funzionalità `[DllImport]` usata in SQLite-NET. SQLite-NET viene invece implementato come una classe astratta e quindi vi viene fatto riferimento tramite il resto del codice condiviso. Di seguito è riportato un estratto dell'API astratta:

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

Il resto del codice condiviso usa la classe astratta per "archiviare" e "recuperare" oggetti dal database. In qualsiasi applicazione che usa questa classe astratta è necessario passare un'implementazione completa che fornisce le funzionalità di database effettive.

### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid e TaskyiOS

I progetti di applicazioni iOS e Android contengono l'interfaccia utente e altri codici specifici della piattaforma usati per collegare il codice condiviso nella libreria di classi portabile (PCL).

Questi progetti contengono anche un'implementazione dell'API di database astratta che funziona su tale piattaforma. In iOS e Android il motore di database SQLite è incorporato nel sistema operativo, pertanto l'implementazione può utilizzare `[DllImport]` come illustrato per fornire l'implementazione concreta della connettività del database. Di seguito è riportato un estratto del codice di implementazione specifico della piattaforma:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```

Nell'esempio di codice è possibile osservare l'implementazione completa.

## <a name="summary"></a>Riepilogo

Questo articolo ha brevemente illustrato i vantaggi e le insidie delle librerie di classi portabili, ha illustrato come creare e utilizzare classi portabili dall'interno Visual Studio per Mac e Visual Studio. Infine, è stata introdotta un'applicazione di esempio completa, TaskyPortable, che mostra una libreria di classi portabile in azione.

## <a name="related-links"></a>Collegamenti correlati

- [TaskyPortable (esempio)](https://docs.microsoft.com/samples/xamarin/mobile-samples/taskyportable/)
- [Creazione di applicazioni multipiattaforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Visual Basic portatile](~/cross-platform/platform/visual-basic/index.md)
- [Progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md)
- [Opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md)
- [Sviluppo multipiattaforma con il .NET Framework (Microsoft)](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)
