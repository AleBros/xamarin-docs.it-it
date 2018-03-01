---
title: Utilizzo delle visualizzazioni di tabella
description: Questo articolo descrive la progettazione e l'utilizzo di viste delle tabelle e i controller di visualizzazione tabella all'interno di un'app Xamarin.tvOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 26a73c2536bf4b4959928bfb27958b1a10734bf5
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-table-views"></a>Utilizzo delle visualizzazioni di tabella

_Questo articolo descrive la progettazione e l'utilizzo di viste delle tabelle e i controller di visualizzazione tabella all'interno di un'app Xamarin.tvOS._

In tvOS, visualizzazione di una tabella viene presentata come una singola colonna di scorrimento di righe che possono facoltativamente essere organizzate in gruppi o sezioni. Viste delle tabelle devono essere utilizzate quando è necessario visualizzare una grande quantità di dati in modo efficiente per l'utente, in una forma non crittografata da capire.

Viste delle tabelle vengono in genere visualizzate in un lato di una [doppia visualizzazione](~/ios/tvos/user-interface/split-views.md) come barra di navigazione, con i dettagli dell'elemento selezionato visualizzato sul lato opposto:

[ ![](table-views-images/intro01.png "Visualizzazione tabella di esempio")](table-views-images/intro01.png)

<a name="About-Table-Views" />

## <a name="about-table-views"></a>Informazioni sulle visualizzazioni di tabella

Oggetto `UITableView` Visualizza una sola colonna di righe scorrevoli come un elenco gerarchico di informazioni che possono facoltativamente essere organizzati in gruppi o sezioni: 

[ ![](table-views-images/table01.png "Un elemento selezionato")](table-views-images/table01.png)

Apple ha i seguenti suggerimenti per l'utilizzo di tabelle:

- **Essere compatibile con larghezza di** -provare l'equilibrio corretto la larghezza delle tabelle. Se la tabella è troppo grande, può essere difficile da analizzare a distanza e può richiedere fuori dell'area di contenuto disponibile. Se la tabella è troppo piccolo, è possibile che le informazioni da troncare o wrap, nuovamente questo può risultare difficile per l'utente di leggere da parte della stanza.
- **Mostra tabella contenuto rapidamente** - per elenchi di dati di grandi dimensioni, caricamento lazy del contenuto e visualizzazione di informazioni non appena la tabella viene presentata all'utente. Se la tabella a lunga per il caricamento, l'utente potrebbe perdere interesse nell'applicazione o di interazione utente che si è bloccato.
- **Informare di tempo contenuto carichi utente** - se il tempo di caricamento lunga tabella è presente, inevitabile una [indicatore di stato o di un indicatore di attività](~/ios/tvos/user-interface/progress-indicators.md) in modo che sappiano l'app non è stata bloccata.

<a name="Table-Cell-Types" />

## <a name="table-view-cell-types"></a>Tipi di cella di visualizzazione tabella

Oggetto `UITableViewCell` viene utilizzata per rappresentare le singole righe di dati nella visualizzazione tabella. Apple ha definito i diversi tipi di cella di tabella predefinito:

- **Predefinito** - Visualizza questo tipo un'opzione immagine sul lato sinistro della cella di e allineato a sinistra del titolo sulla destra. 
- **Sottotitolo** - Visualizza questo tipo un titolo allineato a sinistra nella prima riga e una minore allineato a sinistra sottotitolo nella riga successiva.
- **Il valore 1** -questo tipo presenta un titolo allineato a sinistra con un sottotitolo intensi colorato, a destra sulla stessa riga.
- **Il valore 2** -questo tipo presenta un titolo allineato a destra con un sottotitolo intensi colorato, allineato a sinistra sulla stessa riga.

Tutti i tipi di cella predefinito visualizzazione tabella supporta anche gli elementi grafici, ad esempio la diffusione di indicatori o segni di spunta. 

Inoltre, è possibile definire un **personalizzato** tabella tipo di cella di visualizzazione e presentazione un _cella prototipo_, che è creare nella finestra di progettazione dell'interfaccia o tramite codice.

Apple ha i seguenti suggerimenti per l'utilizzo di celle di tabella vista:

- **Evitare di ritaglio testo** -mantenere le singole righe di testo breve, in modo che finiscono non troncato. Troncato parole o frasi difficili l'utente deve analizzare da attraverso la chat.
- **Prendere in considerazione lo stato attivo della riga** - perché una riga diventa più grande, con più arrotondare gli angoli in stato attivo, è necessario testare l'aspetto della cella in tutti gli stati. Testo o immagini può diventare troncato o aspetto corretto nello stato attivo.
- **Utilizzare sporadicamente tabelle modificabili** -spostamento o eliminazione di righe della tabella richiede più tempo in tvOS di iOS. È necessario scegliere con attenzione se questa funzionalità consente di aggiungere o attenuare l'app tvOS.
- **Creare personalizzato cella tipi dove appropriato** , mentre i tipi di cella di visualizzazione tabella incorporati sono ideali per molte situazioni, è consigliabile creare tipi di cella personalizzata per le informazioni non standard per fornire un maggiore controllo e presentare meglio le informazioni per l'utente.

<a name="Working-With-Table-Views" />

## <a name="working-with-table-views"></a>Utilizzo delle visualizzazioni di tabella

Il modo più semplice per utilizzare le visualizzazioni di tabella in un'app Xamarin.tvOS consiste nel creare e modificare l'aspetto nella finestra di progettazione dell'interfaccia.

Per iniziare, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)
    
1. In Visual Studio per Mac, avviare un nuovo progetto di app tvOS e selezionare **tvOS** > **App** > **singola vista App** e fare clic su di  **Avanti** pulsante: 

    [ ![](table-views-images/table02.png "Selezionare una singola visualizzazione App")](table-views-images/table02.png)
1. Immettere un **nome** per l'app e fare clic su **Avanti**: 

    [ ![](table-views-images/table03.png "Immettere un nome per l'app")](table-views-images/table03.png)
1. Una regolare il **nome progetto** e **Nome soluzione** o accettare le impostazioni predefinite e fare clic su di **crea** pulsante per creare la nuova soluzione: 

    [ ![](table-views-images/table04.png "Il nome di progetto e soluzione")](table-views-images/table04.png)
1. Nel **soluzione riempimento**, fare doppio clic su di `Main.storyboard` file per aprirlo nella finestra di progettazione iOS: 

    [ ![](table-views-images/table05.png "Il file Main")](table-views-images/table05.png)
1. Selezionare ed eliminare il **predefinito View Controller**: 

    [ ![](table-views-images/table06.png "Selezionare ed eliminare il Controller di visualizzazione predefinito")](table-views-images/table06.png)
1. Selezionare un **Split View Controller** dal **della casella degli strumenti** e trascinarlo nell'area di progettazione.
1. Per impostazione predefinita, si otterrà un [doppia visualizzazione](~/ios/tvos/user-interface/split-views.md) con un **navigazione View Controller** e **tabella View Controller** sul lato sinistro e un **View Controller** sul lato destro. Questo è l'utilizzo consigliato di Apple di una visualizzazione tabella in tvOS: 

    [ ![](table-views-images/table08.png "Aggiungere una visualizzazione suddivisa")](table-views-images/table08.png)
1. È necessario selezionare tutte le parti della visualizzazione della tabella e assegnarlo a un oggetto personalizzato **nome della classe** nel **Widget** scheda della finestra il **Esplora proprietà** in modo che è possibile accedervi in un secondo momento nel linguaggio c# codice. Ad esempio, il **tabella View Controller**: 

    [ ![](table-views-images/table09.png "Assegnare un nome di classe")](table-views-images/table09.png)
1. Assicurarsi di creare una classe personalizzata per il **tabella View Controller**, **tabella vista** e qualsiasi **celle prototipo**. Visual Studio per Mac aggiungerà le classi personalizzate per la struttura del progetto al momento della creazione: 

    [ ![](table-views-images/table10.png "Le classi personalizzate nell'albero del progetto")](table-views-images/table10.png)
1. Successivamente, selezionare la visualizzazione tabella nell'area di progettazione e modificare la proprietà in base alle esigenze. Ad esempio il numero di **celle prototipo** e **stile** (normale o raggruppato): 

    [ ![](table-views-images/table11.png "La scheda widget")](table-views-images/table11.png)
1. Per ogni **cella prototipo**, selezionarlo e assegnare un univoco **identificatore** nel **Widget** scheda della finestra il **Esplora proprietà**. Questo passaggio è _molto importante_ quanto è necessario questo identificatore in un secondo momento quando si popola la tabella. Ad esempio `AttrCell`: 

    [ ![](table-views-images/table12.png "La scheda Widget")](table-views-images/table12.png)
1. È inoltre possibile selezionare per presentare la cella come una del [predefinito di tipi di cella visualizzazione tabella](#Table-View-Cell-Types) tramite il **stile** elenco a discesa o impostarlo su **personalizzato** e utilizzare l'area di progettazione per la cella di layout mediante il trascinamento di altri widget dell'interfaccia utente dal **della casella degli strumenti**: 

    [ ![](table-views-images/table13.png "Il layout della cella")](table-views-images/table13.png)
1. Assegnare univoco **nome** a ogni elemento dell'interfaccia utente nella soluzione il prototipo cella nel **Widget** scheda della finestra di **Esplora proprietà** in modo che è possibile accedervi in un secondo momento nel codice c#: 

    [ ![](table-views-images/table14.png "Assegnare un nome")](table-views-images/table14.png)
1. Ripetere il passaggio precedente per tutte le celle di prototipo nella visualizzazione tabella.
1. Assegnare quindi le classi personalizzate al resto della progettazione dell'interfaccia utente, la visualizzazione dei dettagli di layout e assegnare univoco **nomi** a ogni elemento dell'interfaccia utente nei dettagli della visualizzazione in modo che è possibile accedervi in c# e. Di seguito è riportato un esempio: 

    [ ![](table-views-images/table15.png "Il layout dell'interfaccia utente")](table-views-images/table15.png)
1. Salvare le modifiche allo Storyboard.
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. In Visual Studio, avviare un nuovo progetto di app tvOS e selezionare **tvOS** > **singola vista App** e immettere un nome per l'app. Fare clic su di **OK** pulsante per creare una nuova soluzione: 

    [ ![](table-views-images/table02-vs.png "Selezionare una singola visualizzazione App")](table-views-images/table02-vs.png)
1. Nel **Esplora**, fare doppio clic su di `Main.storyboard` file per aprirlo nella finestra di progettazione iOS: 

    [ ![](table-views-images/table05-vs.png "Il file Main")](table-views-images/table05-vs.png)
1. Selezionare ed eliminare il **predefinito View Controller**: 

    [ ![](table-views-images/table06-vs.png "Selezionare ed eliminare il Controller di visualizzazione predefinito")](table-views-images/table06-vs.png)
1. Selezionare un **Split View Controller** dal **della casella degli strumenti** e trascinarlo nell'area di progettazione: 

    [ ![](table-views-images/table07-vs.png "Un Controller di visualizzazione divisa")](table-views-images/table07-vs.png)
1. Per impostazione predefinita, si otterrà un [doppia visualizzazione](~/ios/tvos/user-interface/split-views.md) con un **navigazione View Controller** e **tabella View Controller** sul lato sinistro e un **View Controller** sul lato destro. Questo è l'utilizzo consigliato di Apple di una visualizzazione tabella in tvOS: 

    [ ![](table-views-images/table08-vs.png "Layout dell'interfaccia utente")](table-views-images/table08-vs.png)
1. È necessario selezionare tutte le parti della visualizzazione della tabella e assegnarlo a un oggetto personalizzato **nome della classe** nel **Widget** scheda della finestra il **Esplora proprietà** in modo che è possibile accedervi in un secondo momento nel linguaggio c# codice. Ad esempio, il **tabella View Controller**: 

    [ ![](table-views-images/table09-vs.png "La scheda Widget")](table-views-images/table09-vs.png)
1. Assicurarsi di creare una classe personalizzata per il **tabella View Controller**, **tabella vista** e qualsiasi **celle prototipo**. Visual Studio per Mac aggiungerà le classi personalizzate per la struttura del progetto al momento della creazione: 

    [ ![](table-views-images/table10-vs.png "Le classi personalizzate nell'albero del progetto")](table-views-images/table10-vs.png)
1. Successivamente, selezionare la visualizzazione tabella nell'area di progettazione e modificare la proprietà in base alle esigenze. Ad esempio il numero di **celle prototipo** e **stile** (normale o raggruppato): 

    [ ![](table-views-images/table11-vs.png "La scheda Widget")](table-views-images/table11-vs.png)
1. Per ogni **cella prototipo**, selezionarlo e assegnare un univoco **identificatore** nel **Widget** scheda della finestra il **Esplora proprietà**. Questo passaggio è _molto importante_ quanto è necessario questo identificatore in un secondo momento quando si popola la tabella. Ad esempio `AttrCell`: 

    [ ![](table-views-images/table12-vs.png "Assegnare un identificatore")](table-views-images/table12-vs.png)
1. È inoltre possibile selezionare per presentare la cella come una del [predefinito di tipi di cella visualizzazione tabella](#Table-View-Cell-Types) tramite il **stile** elenco a discesa o impostarlo su **personalizzato** e utilizzare l'area di progettazione per la cella di layout mediante il trascinamento di altri widget dell'interfaccia utente dal **della casella degli strumenti**: 

    [ ![](table-views-images/table13-vs.png "L'elenco a discesa di stile")](table-views-images/table13-vs.png)
1. Assegnare univoco **nome** a ogni elemento dell'interfaccia utente nella soluzione il prototipo cella nel **Widget** scheda della finestra di **Esplora proprietà** in modo che è possibile accedervi in un secondo momento nel codice c#: 

    [ ![](table-views-images/table14-vs.png "La scheda Widget")](table-views-images/table14-vs.png)
1. Ripetere il passaggio precedente per tutte le celle di prototipo nella visualizzazione tabella.
1. Assegnare quindi le classi personalizzate al resto della progettazione dell'interfaccia utente, la visualizzazione dei dettagli di layout e assegnare univoco **nomi** a ogni elemento dell'interfaccia utente nei dettagli della visualizzazione in modo che è possibile accedervi in c# e. Di seguito è riportato un esempio: 

    [ ![](table-views-images/table15.png "Il Layout dell'interfaccia utente")](table-views-images/table15.png)
1. Salvare le modifiche allo Storyboard.
    
-----

<a name="Designing-a-Data-Model" />

## <a name="designing-a-data-model"></a>Progettazione di un modello di dati

Per lavorare con le informazioni di visualizzazione della tabella visualizzerà più semplice e per semplificare la presentazione di informazioni dettagliate (come l'utente seleziona o evidenzia le righe nella visualizzazione tabella), creare una classe personalizzata o classi di agire come modello di dati per le informazioni presentate .

Nell'esempio di un'applicazione di prenotazione di viaggio che contiene un elenco di **Città**, ognuna contenente un elenco univoco di **attrazioni** che l'utente può selezionare. L'utente sarà in grado di contrassegnare un attrazione come un *Preferiti*, selezionare questa opzione per ottenere *direzioni* per un attrazione e *Book un volo* a una determinata città.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per creare il modello di dati per un **attrazione**, fare clic sul nome del progetto nel **soluzione riempimento** e selezionare **Aggiungi** > **nuovo File...** . Immettere `AttractionInformation` per il **nome** e fare clic su di **New** pulsante: 

[ ![](table-views-images/data01.png "Immettere il nome AttractionInformation")](table-views-images/data01.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per creare il modello di dati per un **attrazione**, fare clic sul nome del progetto nel **Esplora** e selezionare **Aggiungi** > **nuovo elemento ...** . Selezionare **classe** e immettere `AttractionInformation` per il **nome** e fare clic su di **Aggiungi** pulsante: 

[ ![](table-views-images/data01-vs.png "Selezionare una classe e immettere AttractionInformation per il nome")](table-views-images/data01-vs.png)

-----

Modificare il `AttractionInformation.cs` file e renderlo simile al seguente:

```csharp
using System;
using Foundation;

namespace tvTable
{
    public class AttractionInformation : NSObject
    {
        #region Computed Properties
        public CityInformation City { get; set;}
        public string Name { get; set;}
        public string Description { get; set;}
        public string ImageName { get; set;}
        public bool IsFavorite { get; set;}
        public bool AddDirections { get; set;}
        #endregion

        #region Constructors
        public AttractionInformation (string name, string description, string imageName)
        {
            // Initialize
            this.Name = name;
            this.Description = description;
            this.ImageName = imageName;
        }
        #endregion
    }
}
```

Questa classe fornisce le proprietà per archiviare le informazioni su un determinato **attrazione**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Successivamente, fare clic sul nome del progetto nel **soluzione riempimento** nuovamente e selezionare **Aggiungi** > **nuovo File...** . Immettere `CityInformation` per il **nome** e fare clic su di **New** pulsante: 

[ ![](table-views-images/data02.png "Immettere il nome CityInformation")](table-views-images/data02.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Successivamente, fare clic sul nome del progetto nel **Esplora** nuovamente e selezionare **Aggiungi** > **nuovo elemento...** . Immettere `CityInformation` per il **nome** e fare clic su di **Aggiungi** pulsante: 

[ ![](table-views-images/data02-vs.png "Immettere il nome CityInformation")](table-views-images/data02-vs.png)

-----

Modificare il `CityInformation.cs` file e renderlo simile al seguente:

```csharp
using System;
using System.Collections.Generic;
using Foundation;

namespace tvTable
{
    public class CityInformation : NSObject
    {
        #region Computed Properties
        public string Name { get; set; }
        public List<AttractionInformation> Attractions { get; set;}
        public bool FlightBooked { get; set;}
        #endregion

        #region Constructors
        public CityInformation (string name)
        {
            // Initialize
            this.Name = name;
            this.Attractions = new List<AttractionInformation> ();
        }
        #endregion

        #region Public Methods
        public void AddAttraction (AttractionInformation attraction)
        {
            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }

        public void AddAttraction (string name, string description, string imageName)
        {
            // Create attraction
            var attraction = new AttractionInformation (name, description, imageName);

            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }
        #endregion
    }
}
```

Questa classe contiene tutte le informazioni su una destinazione **Città**, una raccolta di **attrazioni** per la città e fornisce due metodi helper (`AddAttraction`) per renderne più semplice aggiungere attrazioni per il città.

<a name="The-Table-Data-Source" />

## <a name="the-table-view-data-source"></a>L'origine dati di visualizzazione tabella

La visualizzazione di ogni tabella richiede un'origine dati (`UITableViewDataSource`) per fornire i dati della tabella e generare le righe necessarie come necessari per la visualizzazione della tabella.

Per l'esempio sopra riportato, fare clic sul nome del progetto nel **Esplora**selezionare **Aggiungi** > **nuovo File...**  e chiamarlo `AttractionTableDatasource` e fare clic su di **New** pulsante per la creazione. Successivamente, modificare il `AttractionTableDatasource.cs` file e renderlo simile al seguente:

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDatasource : UITableViewDataSource
    {
        #region Constants
        const string CellID = "AttrCell";
        #endregion

        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        public List<CityInformation> Cities { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDatasource (AttractionTableViewController controller)
        {
            // Initialize
            this.Controller = controller;
            this.Cities = new List<CityInformation> ();
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities ()
        {
            // Clear existing
            Cities.Clear ();

            // Define cities and attractions
            var Paris = new CityInformation ("Paris");
            Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
            Paris.AddAttraction ("Musée du Louvre", "is one of the world's largest museums and a historic monument in Paris, France.", "Louvre");
            Paris.AddAttraction ("Moulin Rouge", "French for 'Red Mill', is a cabaret in Paris, France.", "MoulinRouge");
            Paris.AddAttraction ("La Seine", "Is a 777-kilometre long river and an important commercial waterway within the Paris Basin.", "RiverSeine");
            Cities.Add (Paris);

            var SanFran = new CityInformation ("San Francisco");
            SanFran.AddAttraction ("Alcatraz Island", "Is located in the San Francisco Bay, 1.25 miles (2.01 km) offshore from San Francisco.", "Alcatraz");
            SanFran.AddAttraction ("Golden Gate Bridge", "Is a suspension bridge spanning the Golden Gate strait between San Francisco Bay and the Pacific Ocean", "GoldenGateBridge");
            SanFran.AddAttraction ("San Francisco", "Is the cultural, commercial, and financial center of Northern California.", "SanFrancisco");
            SanFran.AddAttraction ("Telegraph Hill", "Is primarily a residential area, much quieter than adjoining North Beach.", "TelegraphHill");
            Cities.Add (SanFran);

            var Houston = new CityInformation ("Houston");
            Houston.AddAttraction ("City Hall", "It was constructed in 1938-1939, and is located in Downtown Houston.", "CityHall");
            Houston.AddAttraction ("Houston", "Is the most populous city in Texas and the fourth most populous city in the US.", "Houston");
            Houston.AddAttraction ("Texas Longhorn", "Is a breed of cattle known for its characteristic horns, which can extend to over 6 ft.", "LonghornCattle");
            Houston.AddAttraction ("Saturn V Rocket", "was an American human-rated expendable rocket used by NASA between 1966 and 1973.", "Rocket");
            Cities.Add (Houston);
        }
        #endregion

        #region Override Methods
        public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Get cell
            var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

            // Populate cell
            cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

            // Return new cell
            return cell;
        }

        public override nint NumberOfSections (UITableView tableView)
        {
            // Return number of cities
            return Cities.Count;
        }

        public override nint RowsInSection (UITableView tableView, nint section)
        {
            // Return the number of attractions in the given city
            return Cities [(int)section].Attractions.Count;
        }

        public override string TitleForHeader (UITableView tableView, nint section)
        {
            // Get the name of the current city
            return Cities [(int)section].Name;
        }
        #endregion
    }
}
```

Esaminiamo un alcune sezioni della classe in modo dettagliato.

In primo luogo, abbiamo definita una costante per contenere l'identificatore univoco della cella prototipo (questo è lo stesso identificatore assegnato nella finestra di progettazione dell'interfaccia precedente), aggiungere un collegamento al Controller visualizzazione tabella e archiviazione creata per i dati:

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

Successivamente, si salva il Controller di visualizzazione tabella, quindi compilare e popolare l'origine dati (tramite i modelli di dati definiti in precedenza) quando viene creata la classe:

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

Ai fini di esempio, il `PopulateCities` metodo crea semplicemente gli oggetti del modello di dati in memoria ma questi potrebbe essere lette facilmente da un database o servizio web in un'applicazione reale:

```csharp
public void PopulateCities ()
{
    // Clear existing
    Cities.Clear ();

    // Define cities and attractions
    var Paris = new CityInformation ("Paris");
    Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
    ...
}
```

Il `NumberOfSections` metodo restituisce il numero di sezioni nella tabella:

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    // Return number of cities
    return Cities.Count;
}
```

Per **normale** concepito viste delle tabelle, viene restituito sempre 1.

Il `RowsInSection` metodo restituisce il numero di righe nella sezione corrente:

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

Nuovamente, per **normale** viste delle tabelle, restituire il numero totale di elementi nell'origine dati.

Il `TitleForHeader` metodo restituisce il titolo per un determinato sezione:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

Per un **normale** visualizzazione tabella digitare, lasciare vuoto il titolo (`""`).

Infine, quando richiesto da visualizzazione della tabella, creare e popolare un prototipo cella utilizzando il `GetCell` metodo: 

```csharp
public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Get cell
    var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

    // Populate cell
    cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

    // Return new cell
    return cell;
}
```

Per ulteriori informazioni sull'utilizzo di un `UITableViewDatasource`, vedere Apple [UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941) documentazione.

<a name="The-Table-View-Delegate" />

## <a name="the-table-view-delegate"></a>Il delegato di vista della tabella

La visualizzazione di ogni tabella richiede un delegato (`UITableViewDelegate`) di rispondere all'interazione dell'utente o altri eventi di sistema per la tabella.

Per l'esempio sopra riportato, fare clic sul nome del progetto nel **Esplora**selezionare **Aggiungi** > **nuovo File...**  e chiamarlo `AttractionTableDelegate` e fare clic su di **New** pulsante per la creazione. Successivamente, modificare il `AttractionTableDelegate.cs` file e renderlo simile al seguente:

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDelegate : UITableViewDelegate
    {
        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDelegate (AttractionTableViewController controller)
        {
            // Initializw
            this.Controller = controller;
        }
        #endregion

        #region Override Methods
        public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
            attraction.IsFavorite = (!attraction.IsFavorite);

            // Update UI
            Controller.TableView.ReloadData ();
        }

        public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Inform caller of highlight change
            RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
            return true;
        }
        #endregion

        #region Events
        public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
        public event AttractionHighlightedDelegate AttractionHighlighted;

        internal void RaiseAttractionHighlighted (AttractionInformation attraction)
        {
            // Inform caller
            if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
        }
        #endregion
    }
}
```

Diamo un'occhiata a diverse sezioni di questa classe nei dettagli.

Innanzitutto, è creare un collegamento al Controller di visualizzazione tabella quando viene creata la classe:

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

Quindi, quando una riga è selezionata (l'utente fa clic nell'area di tocco della connessione remota Apple) si desidera contrassegnare il **attrazione** rappresentata dalla riga selezionata come preferito:

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

Successivamente, quando l'utente evidenzia una riga (assegnandole lo stato attivo usando l'area di tocco remoto Apple) che si desidera presentare i dettagli del **attrazione** rappresentato da tale riga nella sezione dei dettagli del Controller di visualizzazione suddivisione:

```csharp
public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Inform caller of highlight change
    RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
    return true;
}
...

public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
public event AttractionHighlightedDelegate AttractionHighlighted;

internal void RaiseAttractionHighlighted (AttractionInformation attraction)
{
    // Inform caller
    if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
}
```

Il `CanFocusRow` metodo viene chiamato per ogni riga che sta per ottenere lo stato attivo nella visualizzazione tabella. Restituire `true` se la riga è possibile ottenere lo stato attivo, in caso contrario restituisce `false`. In questo esempio abbiamo creato un oggetto personalizzato `AttractionHighlighted` evento che verrà generato in ogni riga riceve lo stato attivo.

Per ulteriori informazioni sull'utilizzo di un `UITableViewDelegate`, vedere Apple [UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942) documentazione.

<a name="The-Table-View-Cell" />

## <a name="the-table-view-cell"></a>Cella di visualizzazione della tabella

Per ogni cella prototipo aggiunti alla visualizzazione della tabella nella finestra di progettazione dell'interfaccia, è stato creato anche un'istanza personalizzata della cella di visualizzazione della tabella (`UITableViewCell`) consente di popolare la nuova cella (riga) quando viene creata.

Per l'applicazione di esempio, fare doppio clic su di `AttractionTableCell.cs` file per aprirlo e modificarlo e renderlo simile al seguente:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableCell : UITableViewCell
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public AttractionTableCell (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Trap all errors
            try {
                Title.Text = Attraction.Name;
                Favorite.Hidden = (!Attraction.IsFavorite);
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion
    }
}
```

Questa classe fornisce l'archiviazione per l'oggetto modello di dati attrazione (`AttractionInformation` definito in precedenza) visualizzato nella riga specificata:

```csharp
private AttractionInformation _attraction = null;
...

public AttractionInformation Attraction {
    get { return _attraction; }
    set {
        _attraction = value;
        UpdateUI ();
    }
}
```

Il `UpdateUI` metodo popola i widget dell'interfaccia utente (che sono stati aggiunti al prototipo della cella nella finestra di progettazione dell'interfaccia) in base alle esigenze:

```csharp
private void UpdateUI ()
{
    // Trap all errors
    try {
        Title.Text = Attraction.Name;
        Favorite.Hidden = (!Attraction.IsFavorite);
    } catch {
        // Since the UI might not be fully loaded, ignore
        // all errors at this point
    }
}
```

Per ulteriori informazioni sull'utilizzo di un `UITableViewCell`, vedere Apple [UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938) documentazione.

<a name="The-Table-View-Controller" />

## <a name="the-table-view-controller"></a>Il Controller di visualizzazione tabella

Un Controller di vista della tabella (`UITableViewController`) gestisce una visualizzazione tabella che è stato aggiunto a uno Storyboard tramite la finestra di progettazione dell'interfaccia.

Per l'applicazione di esempio, fare doppio clic su di `AttractionTableViewController.cs` file per aprirlo e modificarlo e renderlo simile al seguente:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableViewController : UITableViewController
    {
        #region Computed Properties
        public AttractionTableDatasource Datasource {
            get { return TableView.DataSource as AttractionTableDatasource; }
        }

        public AttractionTableDelegate TableDelegate {
            get { return TableView.Delegate as AttractionTableDelegate; }
        }
        #endregion

        #region Constructors
        public AttractionTableViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Setup table
            TableView.DataSource = new AttractionTableDatasource (this);
            TableView.Delegate = new AttractionTableDelegate (this);
            TableView.ReloadData ();
        }
        #endregion
    }
}
```

Diamo un'occhiata a questa classe. In primo luogo, abbiamo creato i tasti di scelta rapida per rendere più semplice accedere alla visualizzazione di tabella `DataSource` e `TableDelegate`. Utilizzeremo quelli in un secondo momento per la comunicazione tra la visualizzazione tabella sul lato sinistro della doppia visualizzazione e la visualizzazione dei dettagli in a destra.

Infine, quando la vista della tabella viene caricata in memoria, creare istanze di `AttractionTableDatasource` e `AttractionTableDelegate` (entrambi creato in precedenza) e collegarli alla visualizzazione tabella.

Per ulteriori informazioni sull'utilizzo di un `UITableViewController`, vedere Apple [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523) documentazione.

<a name="Pulling-it-All-Together" />

## <a name="pulling-it-all-together"></a>Eseguendone il pull tutti insieme

Come indicato all'inizio di questo documento, viste delle tabelle vengono in genere visualizzate in un lato di una [doppia visualizzazione](~/ios/tvos/user-interface/split-views.md) come barra di navigazione, con i dettagli dell'elemento selezionato visualizzato sul lato opposto. Ad esempio: 

[ ![](table-views-images/intro01.png "Eseguire app di esempio")](table-views-images/intro01.png)

Poiché si tratta di un modello standard di tvOS, ecco i passaggi finali per riunire tutti gli elementi e avere i lati sinistro e destro della visualizzazione suddivisa interagiscono tra loro.

<a name="The-Detail-View" />

### <a name="the-detail-view"></a>Nel riquadro Visualizzazione dettagli

Ad esempio dell'app viaggio presentata nell'esempio precedente, una classe personalizzata (`AttractionViewController`) è definito per il Controller di visualizzazione standard presentate nel lato destro della visualizzazione suddivisa come visualizzazione Dettagli:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionViewController : UIViewController
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }

        public MasterSplitView SplitView { get; set;}
        #endregion

        #region Constructors
        public AttractionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void UpdateUI ()
        {
            // Trap all errors
            try {
                City.Text = Attraction.City.Name;
                Title.Text = Attraction.Name;
                SubTitle.Text = Attraction.Description;

                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
                IsFavorite.Hidden = (!Attraction.IsFavorite);
                IsDirections.Hidden = (!Attraction.AddDirections);
                BackgroundImage.Image = UIImage.FromBundle (Attraction.ImageName);
                AttractionImage.Image = BackgroundImage.Image;
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Ensure the UI Updates
            UpdateUI ();
        }
        #endregion

        #region Actions
        partial void BookFlight (NSObject sender)
        {
            // Ask user to book flight
            AlertViewController.PresentOKCancelAlert ("Book Flight",
                                                      string.Format ("Would you like to book a flight to {0}?", Attraction.City.Name),
                                                      this,
                                                      (ok) => {
                Attraction.City.FlightBooked = ok;
                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
            });
        }

        partial void GetDirections (NSObject sender)
        {
            // Ask user to add directions
            AlertViewController.PresentOKCancelAlert ("Add Directions",
                                                     string.Format ("Would you like to add directions to {0} to you itinerary?", Attraction.Name),
                                                     this,
                                                     (ok) => {
                                                         Attraction.AddDirections = ok;
                                                         IsDirections.Hidden = (!Attraction.AddDirections);
                                                     });
        }

        partial void MarkFavorite (NSObject sender)
        {
            // Flip favorite state
            Attraction.IsFavorite = (!Attraction.IsFavorite);
            IsFavorite.Hidden = (!Attraction.IsFavorite);

            // Reload table
            SplitView.Master.TableController.TableView.ReloadData ();
        }
        #endregion
    }
}
```

In questo caso, è stato fornito il **attrazione** (`AttractionInformation`) viene visualizzato come una proprietà e creato un `UpdateUI` metodo che consente di popolare i widget dell'interfaccia utente aggiunto alla visualizzazione nella finestra di progettazione dell'interfaccia.

Vengono definite anche un collegamento al Controller visualizzazione Split (`SplitView`) che si utilizzerà per comunicare le modifiche in visualizzazione della tabella (`AcctractionTableView`).

Infine, azioni personalizzate (eventi) sono stati aggiunti tre `UIButton` le istanze create nella finestra di progettazione dell'interfaccia, che consentono di contrassegnare un attrazione come un _Preferiti_, ottenere _direzioni_ a un attrazione e _Book un volo_ a una determinata città.

<a name="The-Navigation-View-Controller" />

### <a name="the-navigation-view-controller"></a>Il Controller di vista di navigazione

Il Controller di visualizzazione tabella è nidificata in un Controller di vista di navigazione sul lato sinistro della visualizzazione divisa, il Controller di vista di navigazione è stato assegnato a una classe personalizzata (`MasterNavigationController`) nella finestra di progettazione dell'interfaccia e definiti come segue:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterNavigationController : UINavigationController
    {
        #region Computed Properties
        public MasterSplitView SplitView { get; set;}
        public AttractionTableViewController TableController {
            get { return TopViewController as AttractionTableViewController; }
        }
        #endregion

        #region Constructors
        public MasterNavigationController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Nuovamente, questa classe definisce solo alcuni tasti di scelta rapida per rendere più semplice per la comunicazione tra i due lati del Controller di visualizzazione di suddivisione:

* `SplitView` -È un collegamento al Controller di visualizzazione di divisione (`MainSpiltViewController`) a cui appartiene il Controller di vista di navigazione.
* `TableController` : Ottiene il Controller di vista della tabella (`AttractionTableViewController`) che viene presentato come la visualizzazione di alto nel Controller di vista di navigazione.

<a name="The-Split-View-Controller" />

### <a name="the-split-view-controller"></a>Il Controller di visualizzazione divisa

Perché il Controller di visualizzazione di divisione è la base dell'applicazione, viene creata una classe personalizzata (`MasterSplitViewController`) nella finestra di progettazione dell'interfaccia e viene definito come segue:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterSplitView : UISplitViewController
    {
        #region Computed Properties
        public AttractionViewController Details {
            get { return ViewControllers [1] as AttractionViewController; }
        }

        public MasterNavigationController Master {
            get { return ViewControllers [0] as MasterNavigationController; }
        }
        #endregion

        #region Constructors
        public MasterSplitView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            Master.SplitView = this;
            Details.SplitView = this;

            // Wire-up events
            Master.TableController.TableDelegate.AttractionHighlighted += (attraction) => {
                // Display new attraction
                Details.Attraction = attraction;
            };
        }
        #endregion
    }
}
```

È necessario innanzitutto creare tasti di scelta rapida per il **dettagli** di visualizzazione suddivisa (`AttractionViewController`) e il **Master** lato (`MasterNavigationController`). Nuovamente, questo rende più semplice per la comunicazione tra i due lati in un secondo momento.

Successivamente, quando doppia visualizzazione viene caricata in memoria, si collega il Controller di visualizzazione di divisione per entrambi i lati della visualizzazione suddivisa e rispondere all'utente un attrazione nella visualizzazione tabella evidenziazione (`AttractionHighlighted`) visualizzando il nuovo attrazione nel **dettagli**  di doppia visualizzazione.

Vedere il [tvTables](https://developer.xamarin.com/samples/monotouch/tvos/tvTable/) app di esempio per un'implementazione completa della tabella viste all'interno di una visualizzazione.

## <a name="table-views-in-detail"></a>Viste delle tabelle in modo dettagliato

Poiché tvOS modificata in iOS, viste delle tabelle e i controller di visualizzazione di tabella progettate e si comportano in modo simile. Per ulteriori informazioni sull'utilizzo di visualizzazione tabella in un'app Xamarin, consultare il nostro iOS [utilizzo di tabelle e le celle](~/ios/user-interface/controls/tables/index.md) documentazione.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è descritta la progettazione e l'utilizzo di viste delle tabelle all'interno di un'app Xamarin.tvOS. E ha presentato un esempio di utilizzo di visualizzazione di una tabella all'interno di una visualizzazione, ovvero l'utilizzo tipico di una visualizzazione tabella in un'app tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
