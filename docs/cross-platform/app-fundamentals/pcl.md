---
title: Introduzione alle librerie di classi portabile (PCL)
description: Questo articolo presenta i progetti libreria di classi portabile (PCL) e illustra la creazione e l'uso dei progetti di libreria di classi Portabile in Visual Studio per Mac e Visual Studio.
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: a1e7152ca0f24d1ac660742e97907ea9300df9a7
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57666879"
---
# <a name="portable-class-libraries-pcl"></a>Librerie di classi portabili

> [!WARNING]
> Librerie di classi portabili (librerie di classi portabili) sono considerate obsoleti nelle ultime versioni di Visual Studio.
> Mentre è ancora possibile aprire, modificare e compilare librerie di classi portabili, per i nuovi progetti è consigliabile usare [librerie .NET Standard](~/cross-platform/app-fundamentals/net-standard.md).

Un componente fondamentale della compilazione di applicazioni multipiattaforma è la possibilità di condividere codice tra progetti specifici delle piattaforme diverse. Tuttavia, ciò è ulteriormente complicata dal fatto che usano spesso un sottoinsieme diverso della libreria di classi Base (BCL) .NET, piattaforme diverse e sono effettivamente compilate da un altro profilo di libreria .NET Core. Ciò significa che ogni piattaforma può usare solo le librerie di classi che sono destinate allo stesso profilo in modo che compaiono in modo da richiedere progetti libreria di classi separata per ogni piattaforma.

Esistono tre approcci principali per la condivisione del codice che consentono di risolvere questo problema: **i progetti .NET Standard**, **progetti di Asset condivisi**, e **progetti libreria di classi portabile (PCL)**.

- **I progetti .NET standard** sono la scelta migliore per la condivisione di codice .NET, altre informazioni, vedere [i progetti .NET Standard e Xamarin](~/cross-platform/app-fundamentals/net-standard.md).
- **I progetti di Asset condivisi** usare un singolo set di file e offre un modo rapido e semplice in cui si desidera condividere il codice all'interno di una soluzione e in genere si avvale di direttive di compilazione condizionale per specificare i percorsi del codice per varie piattaforme a cui verranno usati, (per ulteriori informazioni informazioni, vedere la [articolo progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md)).
- **Libreria di classi Portabile** progetti sono destinati a profili specifici che supportano un set noto di classi di libreria di classi base e le funzionalità. Tuttavia, dal lato negativo alla libreria di classi Portabile è che spesso richiedono molto architetturale allo scopo di separare il codice specifico profilo nelle proprie librerie.

Questa pagina illustra come creare un **libreria di classi Portabile** progetto destinato a un profilo specifico, che è possibile fare riferimento da più progetti specifici della piattaforma.

## <a name="what-is-a-portable-class-library"></a>Che cos'è una libreria di classi portabile?

Quando si crea un progetto di applicazione o un progetto di libreria, la DLL risultante è limitata a lavorare sulla piattaforma specifica che viene creato. Ciò impedisce la scrittura di un assembly per un'app di Windows e quindi usarlo nuovamente in xamarin. IOS e xamarin. Android.

Quando si crea una libreria di classi portabile, tuttavia, è possibile scegliere una combinazione di piattaforme di cui si desidera che il codice per l'esecuzione. Compatibilità delle scelte effettuate durante la creazione di una libreria di classi portabile vengono convertite in un identificatore "Profilo", che descrive le piattaforme su cui la libreria supporta.

Nella tabella seguente vengono illustrate alcune delle funzionalità che variano in base alla piattaforma .NET. Per scrivere un assembly libreria di classi Portabile che verrà sicuramente eseguito su dispositivi specifiche/piattaforme è sufficiente scegliere quale supporto è obbligatorio quando si crea il progetto.

|Funzionalità|.NET Framework|App UWP|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|Base|Y|Y|Y|Y|Y|
|LINQ|Y|Y|Y|Y|Y|
|IQueryable|Y|Y|Y|7.5 +|Y|
|Serializzazione|Y|Y|Y|Y|Y|
|Annotazioni dei dati|4.0.3 +|Y|Y||Y|

La colonna Xamarin riflette il fatto che xamarin. IOS e xamarin. Android supporta tutti i profili forniti con Visual Studio e la disponibilità di funzionalità nelle librerie create sarà limitata solo da altre piattaforme che si sceglie di supportare.

Sono inclusi i profili che sono combinazioni di:

- .NET 4 o 4.5 di .NET
- Silverlight 5
- Windows Phone 8
- App UWP

Altre informazioni sulle funzionalità dei profili diversi nel [sito Web di Microsoft](https://msdn.microsoft.com/library/gg597391(v=vs.110).aspx) e vedere un altro membro della community [il riepilogo del profilo di libreria di classi Portabile](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) info framework e ad altre note, che include supportati.

**Vantaggi**

1. Condivisione di codice centralizzati, scrivere e testare il codice in un singolo progetto che può essere usato da altre applicazioni o librerie.
2. Operazioni di refactoring influirà tutto il codice caricato nella soluzione (libreria di classi portabile e i progetti specifici della piattaforma).
3. Il progetto libreria di classi Portabile possa facilmente farvi riferimento da altri progetti in una soluzione o l'assembly di output può essere condivisi per altri utenti a cui fare riferimento nelle loro soluzioni.

**Svantaggi**

1. Poiché la stessa libreria di classi portabile è condiviso tra più applicazioni, librerie specifiche della piattaforma non è possibile fare riferimento (ad es. Community.CsharpSqlite.WP7).
2. Il sottoinsieme della libreria di classi portabile potrebbe non includere le classi che altrimenti sarebbero disponibili sia in MonoTouch che Mono for Android (ad esempio, DllImport o IO).

> [!NOTE]
> Librerie di classi portabili sono state deprecate nella versione più recente di Visual Studio, e [le librerie .NET Standard](net-standard.md) sono consigliati invece.

In una certa misura entrambi gli svantaggi possono essere aggirati usando il modello di Provider o l'inserimento delle dipendenze per l'implementazione effettiva nei progetti di piattaforma in una classe di base o interfaccia definita nella libreria di classi portabile di codice.

Questo diagramma mostra l'architettura di un'applicazione multipiattaforma usando una libreria di classi portabile per condividere codice, ma anche tramite inserimento delle dipendenze da passare nelle funzionalità dipendente dalla piattaforma:

[![](pcl-images/image1.png "Questo diagramma illustra l'architettura di un'applicazione multipiattaforma usando una libreria di classi portabile per condividere codice, ma anche tramite inserimento delle dipendenze da passare nelle funzionalità dipendente dalla piattaforma")](pcl-images/image1.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio per Mac procedura dettagliata

In questa sezione illustra in dettaglio come creare e usare una libreria di classi portabile con Visual Studio per Mac. Vedere la sezione di esempio di libreria di classi Portabile per un'implementazione completa.

### <a name="creating-a-pcl"></a>Creazione di una libreria di classi Portabile

Aggiunta di una libreria di classi portabile per la soluzione è molto simile all'aggiunta di un progetto di libreria regolare.

1. Nel **nuovo progetto** finestra di dialogo selezionare la **multipiattaforma > Libreria > libreria portabile** opzione:

    ![Creare un nuovo progetto di libreria di classi Portabile](pcl-images/image2.png)

1. Quando viene creata una libreria di classi Portabile in Visual Studio per Mac viene configurato automaticamente con un profilo che funziona per xamarin. IOS e xamarin. Android. Il progetto libreria di classi Portabile apparirà come illustrato in questo screenshot:

    ![Progetto libreria di classi Portabile nel riquadro della soluzione](pcl-images/image3.png)

Libreria di classi Portabile è ora pronto per il codice da aggiungere. È possibile anche farvi riferimento da altri progetti (progetti di applicazione, i progetti di libreria e anche altri progetti PCL).

### <a name="editing-pcl-settings"></a>Modifica delle impostazioni di libreria di classi Portabile

Per visualizzare e modificare le impostazioni di libreria di classi Portabile per questo progetto, fare clic sul progetto e scegliere **Opzioni > compilazione > Generale** per visualizzare la schermata illustrata di seguito:

[![Opzioni di progetto libreria di classi Portabile per impostare il profilo](pcl-images/image4-sml.png)](pcl-images/image4.png#lightbox)

Fare clic su **modifica...**  per modificare il profilo di destinazione per la libreria di classi portabile.

Se il profilo viene modificato dopo che è già stato aggiunto codice alla libreria di classi Portabile, è possibile che la libreria non sarà più compilate se il codice fa riferimento a funzionalità che non fanno parte del profilo appena selezionato.

## <a name="working-with-a-pcl"></a>Utilizzo di una libreria di classi Portabile

Quando il codice è scritto in una libreria di classi Portabile, l'editor Visual Studio per Mac riconoscerà le limitazioni del profilo selezionato e modificare le opzioni di completamento automatico di conseguenza. Ad esempio, questo screenshot Mostra le opzioni di completamento automatico per System.IO usando il profilo predefinito (Profile136) usato in Visual Studio per Mac: si noti che la barra di scorrimento indica circa metà delle classi disponibili verranno visualizzate (in realtà esistono solo 14 classi disponibili).

[![Elenco di IntelliSense di 14 classi nella classe System.IO di una libreria di classi Portabile](pcl-images/image6.png)](pcl-images/image6.png#lightbox)

Confrontare con il System.IO completamento automatico in un progetto xamarin. IOS o xamarin. Android: sono presenti 40 classi inclusi disponibile comunemente utilizzate classi come `File` e `Directory` che non sono presenti in qualsiasi profilo di libreria di classi Portabile.

[![Elenco di IntelliSense di 40 classi nello spazio dei nomi System.IO di .NET Framework](pcl-images/image7.png)](pcl-images/image7.png#lightbox)

Questo riflette il compromesso sottostante dell'uso di libreria di classi Portabile, si intende la possibilità di condividere facilmente il codice su molte piattaforme che determinate API non sono disponibili all'utente perché non contengono implementazioni simili in tutte le piattaforme possibili.

### <a name="using-pcl"></a>Con libreria di classi Portabile

Dopo aver creato un progetto libreria di classi Portabile, è possibile aggiungere un riferimento al codice da qualsiasi progetto di applicazione o una libreria compatibile nello stesso modo che è in genere aggiungere riferimenti. In Visual Studio per Mac, fare clic sul nodo Riferimenti e scegliere **Modifica riferimenti...**  passerà successivamente al **progetti** scheda come illustrato:

[![Aggiungere un riferimento a una libreria di classi Portabile tramite l'opzione Modifica riferimenti](pcl-images/image8.png)](pcl-images/image8.png#lightbox)

Lo screenshot seguente mostra il riquadro della soluzione per l'app di esempio TaskyPortable, che mostra la libreria di classi Portabile nella parte inferiore e un riferimento a tale libreria di classi Portabile nel progetto xamarin. IOS.

[![Progetto libreria di classi Portabile che mostra di TaskyPortable esempio soluzione](pcl-images/image9.png)](pcl-images/image9.png#lightbox)

L'output di una libreria di classi Portabile (ie. la DLL dell'assembly risultante) possono anche essere aggiunti come riferimento per la maggior parte dei progetti. In questo modo PCL un modo ideale per la spedizione di librerie e componenti di multi-piattaforma.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Procedura dettagliata di Visual Studio

Questa sezione illustra come creare e usare una libreria di classi portabile con Visual Studio. Vedere la sezione di esempio di libreria di classi Portabile per un'implementazione completa.

### <a name="creating-a-pcl"></a>Creazione di una libreria di classi Portabile

Aggiunta di una libreria di classi Portabile per la soluzione in Visual Studio è leggermente diverso per l'aggiunta di un normale progetto:

1. Nel **Aggiungi nuovo progetto** schermata, seleziona la **libreria di classi (portabile Legacy)** opzione. Si noti che la descrizione nella parte destra avvisa che questo tipo di progetto è stato deprecato.

    [![Finestra Nuovo progetto per creare una libreria di classi portabile](pcl-images/image10-sml.png "libreria di classi portabile")](pcl-images/image10.png#lightbox)

2. Visual Studio richiederà immediatamente con la finestra di dialogo seguente, in modo che il profilo può essere configurato.
 Selezionare le piattaforme da supportare e fare clic su OK.

    [![Selezionare le piattaforme di destinazione per la libreria](pcl-images/image11-sml.png "selezionare le piattaforme da supportare e fare clic su OK")](pcl-images/image11.png#lightbox)

3. Verrà visualizzato il progetto libreria di classi Portabile, come illustrato in Esplora soluzioni &ndash; il testo **(portatile)** viene visualizzata accanto al nome del progetto per indicare che è una libreria di classi Portabile:

    ![NET Framework definiti dal profilo di libreria di classi Portabile](pcl-images/image12.png "NET Framework definiti dal profilo di libreria di classi Portabile")

Libreria di classi Portabile è ora pronto per il codice da aggiungere. È possibile anche farvi riferimento da altri progetti (progetti di applicazione, i progetti di libreria e anche altri progetti PCL).

### <a name="editing-pcl-settings"></a>Modifica delle impostazioni di libreria di classi Portabile

Le impostazioni di libreria di classi Portabile possono essere visualizzate e modificate facendo clic sul progetto e scegliendo **proprietà > libreria** , come illustrato in questo screenshot:

[![Modificare le destinazioni di piattaforma](pcl-images/image13-sml.png)](pcl-images/image13.png#lightbox)

Se il profilo viene modificato dopo che è già stato aggiunto codice alla libreria di classi Portabile, è possibile che la libreria non sarà più compilate se il codice fa riferimento a funzionalità che non fanno parte del profilo appena selezionato.

> [!TIP]
> È inoltre disponibile un messaggio che informa che **. NETStandard è il metodo consigliato per condividere codice**. Questo evento indica che, sebbene librerie di classi portabili sono ancora supportate, è consigliabile eseguire l'aggiornamento a .NET Standard.

### <a name="working-with-a-pcl"></a>Utilizzo di una libreria di classi Portabile

Quando il codice è scritto in una libreria di classi Portabile, Visual Studio riconosce le limitazioni del profilo selezionato e modificare di conseguenza le opzioni Intellisense. Ad esempio, questo screenshot Mostra le opzioni di completamento automatico per System.IO usando il profilo predefinito (Profile136): si noti che la barra di scorrimento indica circa metà delle classi disponibili verranno visualizzate (in realtà esistono solo 14 classi disponibili).

[![Numero ridotto di classi dei / o disponibili in una libreria di classi Portabile](pcl-images/image14.png)](pcl-images/image14.png#lightbox)

Confrontare con il System.IO completamento automatico in un progetto regolare: sono presenti 40 classi comunemente disponibili tra cui utilizzate classi come `File` e `Directory` che non sono presenti in qualsiasi profilo di libreria di classi Portabile.

[![Molte classi i/o altre disponibili in .NET Framework](pcl-images/image15.png)](pcl-images/image15.png#lightbox)

Questo riflette il compromesso sottostante dell'uso di libreria di classi Portabile, si intende la possibilità di condividere facilmente il codice su molte piattaforme che determinate API non sono disponibili all'utente perché non contengono implementazioni simili in tutte le piattaforme possibili.

> [!TIP]
> .NET standard 2.0 rappresenta una quantità eccessiva API superficie maggiore rispetto a librerie di classi portabili, incluso lo spazio dei nomi System.IO. Per i nuovi progetti, .NET Standard è preferibile libreria di classi Portabile.

### <a name="using-pcl"></a>Con libreria di classi Portabile

Dopo aver creato un progetto libreria di classi Portabile, è possibile aggiungere un riferimento al codice da qualsiasi progetto di applicazione o una libreria compatibile nello stesso modo che è in genere aggiungere riferimenti. In Visual Studio, fare clic sul nodo Riferimenti e scegliere `Add Reference...` passerà successivamente al **soluzione > progetti** scheda come illustrato:

[![Aggiungere un riferimento a una libreria di classi Portabile tramite la scheda di aggiungere i progetti di riferimento](pcl-images/image16.png)](pcl-images/image16.png#lightbox)

Lo screenshot seguente mostra il riquadro della soluzione per l'app di esempio TaskyPortable, che mostra la libreria di classi Portabile nella parte inferiore e un riferimento a tale libreria di classi Portabile nel progetto xamarin. IOS.

[![Soluzione di esempio TaskyPortable che mostra una libreria di classi Portabile](pcl-images/image17.png)](pcl-images/image17.png#lightbox)

L'output di una libreria di classi Portabile (ie. la DLL dell'assembly risultante) possono anche essere aggiunti come riferimento per la maggior parte dei progetti.
In questo modo PCL un modo ideale per la spedizione di librerie e componenti di multi-piattaforma.

-----

## <a name="pcl-example"></a>Esempio di libreria di classi Portabile

Il [TaskyPortable](https://developer.xamarin.com/samples/mobile/TaskyPortable/) applicazione di esempio viene illustrato come utilizzare una libreria di classi portabile con Xamarin.
Ecco alcune schermate dell'App risultante in esecuzione in iOS e Android:

[![](pcl-images/image18.png "Ecco alcune schermate delle App risultante che eseguono iOS, Android e Windows Phone")](pcl-images/image18.png#lightbox)

Condivide una serie di classi di dati e per la logica che sono codice puramente portabile e viene inoltre illustrato come incorporare i requisiti specifici della piattaforma tramite inserimento delle dipendenze per l'implementazione di database SQLite.

Seguito è riportata la struttura della soluzione (in Visual Studio per Mac e Visual Studio rispettivamente):

[![](pcl-images/image19.png "La struttura della soluzione è illustrata di seguito in Visual Studio per Mac e Visual Studio rispettivamente")](pcl-images/image19.png#lightbox)

Poiché il codice di SQLite-NET dispone di parti specifiche della piattaforma (per lavorare con le implementazioni di SQLite in ogni sistema operativo diversa) per dimostrazione scopi è stata sottoposta a refactoring in una classe astratta classe che può essere compilato in una libreria di classi portabile e il codice effettivo implementato come sottoclassi nei progetti iOS e Android.

### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

Libreria di classi portabile è limitata che può supportare le funzionalità .NET. Poiché viene compilato per l'esecuzione su più piattaforme, non riesce a stabilire Usa `[DllImport]` funzionalità che vengono utilizzati in SQLite-NET. SQLite-NET viene invece implementata come una classe astratta e farvi riferimento tramite il resto del codice condiviso. Di seguito è riportato un estratto dell'API astratto:

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

Il resto del codice condiviso Usa la classe astratta "store" e "recuperare" oggetti dal database. In qualsiasi applicazione che usa questa classe astratta è necessario passare un'implementazione completa che fornisce la funzionalità di database effettivo.

### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid e TaskyiOS

I progetti di applicazione Android e iOS contengono l'interfaccia utente e altro codice specifico della piattaforma usato per il codice condiviso nella libreria di classi Portabile wire-up.

Questi progetti contengono anche un'implementazione del database astratta API che funziona su quella piattaforma. In iOS e Android di Sqlite motore di database è integrata nel sistema operativo, in modo che l'implementazione può utilizzare `[DllImport]` come illustrato per fornire l'implementazione concreta di connettività del database. Seguito è riportato un estratto di codice di implementazione specifica della piattaforma:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```

L'implementazione completa può essere visualizzato nel codice di esempio.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i vantaggi e gli svantaggi di librerie di classi portabili brevemente, è stato illustrato come creare e utilizzare librerie di classi portabili all'interno di Visual Studio per Mac e Visual Studio. e infine introdotta un'applicazione di esempio completo: TaskyPortable – che mostra una libreria di classi Portabile in azione.

## <a name="related-links"></a>Collegamenti correlati

- [TaskyPortable (esempio)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Creazione di applicazioni multipiattaforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Portabile Visual Basic](~/cross-platform/platform/visual-basic/index.md)
- [Progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md)
- [Opzioni di condivisione codice](~/cross-platform/app-fundamentals/code-sharing.md)
- [Sviluppo multipiattaforma con .NET Framework (Microsoft)](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)
