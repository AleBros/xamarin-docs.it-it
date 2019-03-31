---
title: Utilizzo di tvOS viste delle tabelle in Xamarin
description: Questo articolo illustra la progettazione e l'utilizzo di viste delle tabelle e i controller di visualizzazione tabella all'interno di un'app xamarin. tvos.
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3e7fc3d627b5d7a1dc73caa395a9181efb0b5f08
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2019
ms.locfileid: "58678002"
---
# <a name="working-with-tvos-table-views-in-xamarin"></a>Utilizzo di tvOS viste delle tabelle in Xamarin

_Questo articolo illustra la progettazione e l'utilizzo di viste delle tabelle e i controller di visualizzazione tabella all'interno di un'app xamarin. tvos._

In tvOS, visualizzazione di una tabella viene presentata come una singola colonna di scorrimento di righe che possono facoltativamente essere organizzate in gruppi o sezioni. Usare le visualizzazioni di tabelle quando è necessario visualizzare una grande quantità di dati in modo efficiente per l'utente, in un chiaro da capire.

Viste delle tabelle vengono in genere visualizzate in un lato di una [doppia visualizzazione](~/ios/tvos/user-interface/split-views.md) come riquadro di spostamento con i dettagli dell'elemento selezionato visualizzato sul lato opposto:

[![](table-views-images/intro01.png "Visualizzazione tabella di esempio")](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views" />

## <a name="about-table-views"></a>Sulle viste delle tabelle

Oggetto `UITableView` Visualizza una singola colonna di righe scorrevoli come un elenco gerarchico di informazioni che possono facoltativamente essere organizzati in gruppi o sezioni: 

[![](table-views-images/table01.png "Un elemento selezionato")](table-views-images/table01.png#lightbox)

Apple ha i seguenti suggerimenti per l'utilizzo di tabelle:

- **Essere consapevoli della larghezza** -provare a trovare l'equilibrio corretto nella larghezza delle colonne di tabella. Se la tabella è troppo ampia, può essere difficile da analizzare a partire da una distanza e possibile vengono detratte dalle area del contenuto disponibile. Se la tabella è troppo piccolo, è possibile che le informazioni da troncare o a capo automatico, anche in questo caso può essere difficile per all'utente di leggere da attraversare la stanza.
- **Mostra tabella contenuto rapidamente** - per elenchi di grandi dimensioni dei dati, caricamento lazy il contenuto e la visualizzazione informazioni non appena la tabella viene presentata all'utente. Se la tabella richiede a lunga per il caricamento, l'utente potrebbe perdere interesse nell'app o di interazione utente che si è bloccato.
- **Informare l'utente di Long Carica contenuto** : se un tempo di caricamento della tabella lungo è inevitabile, presente una [indicatore di stato o indicatore dell'attività](~/ios/tvos/user-interface/progress-indicators.md) non sia bloccato in modo che sappiano che l'app.

<a name="Table-Cell-Types" />

## <a name="table-view-cell-types"></a>Tipi di cella di visualizzazione tabella

Oggetto `UITableViewCell` viene usato per rappresentare le singole righe di dati nella visualizzazione della tabella. Apple ha definito alcuni tipi di cella di tabella predefinito:

- **Default** : questa un'opzione di immagine sul lato sinistro della cella di e titolo allineato a sinistra a destra visualizza informazioni di tipo. 
- **Sottotitolo** - Visualizza informazioni in questo tipo titolo allineato a sinistra all'interno della prima riga e una minore allineato a sinistra del sottotitolo nella riga successiva.
- **Il valore 1** -questo tipo presenta un titolo allineato a sinistra con un sottotitolo colorato più leggeri, allineato a destra sulla stessa riga.
- **Il valore 2** -questo tipo presenta un titolo allineato a destra con un sottotitolo colorato più leggeri, allineata a sinistra sulla stessa riga.

Tutti i tipi di cella di visualizzazione tabella predefinita supportano anche gli elementi grafici, ad esempio gli indicatori di divulgazione o segni di spunta. 

Inoltre, è possibile definire un **personalizzato** tipo di cella di visualizzazione tabella e per presentare una _prototipo cella_, uno creare nella finestra di progettazione di interfaccia o tramite codice.

Apple ha i seguenti suggerimenti per l'utilizzo di celle della visualizzazione tabella:

- **Evitare di ritaglio testo** -mantenere le singole righe di testo breve in modo che non entrino troncato. Troncato parole o frasi sono l'utente deve analizzare da attraversare la stanza.
- **Prendere in considerazione lo stato della riga Focused** - perché una riga diventa maggiore, con informazioni arrotondati gli angoli quando è in stato attivo, è necessario testare l'aspetto della cella in tutti gli stati. Immagini o testo potrebbero diventare ritagliata o aspetto non corretto nello stato attivo.
- **Utilizzare sporadicamente tabelle modificabili** -lo spostamento o eliminazione di righe della tabella richiede più tempo su tvOS a iOS. È necessario stabilire con attenzione se questa funzionalità consente di aggiungere o si distaccherà dalla propria app tvOS.
- **Creazione personalizzata cella tipi dove appropriato** : mentre i tipi di cella di visualizzazione tabella incorporati sono ideali per molte situazioni, è consigliabile creare tipi di cella personalizzato per le informazioni non standard per fornire maggiore controllo e meglio presentare le informazioni da l'utente.

<a name="Working-With-Table-Views" />

## <a name="working-with-table-views"></a>Uso delle visualizzazioni di tabella

Il modo più semplice per lavorare con le visualizzazioni di tabelle in un'app xamarin. tvos è per creare e modificare l'aspetto nella finestra di progettazione dell'interfaccia.

Per iniziare, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)
    
1. In Visual Studio per Mac, avviare un nuovo progetto di app tvOS e selezionare **tvOS** > **App** > **App visualizzazione singola** e fare clic su di  **Avanti** pulsante: 

    [![](table-views-images/table02.png "Selezionare App visualizzazione singola")](table-views-images/table02.png#lightbox)
1. Immettere un **Name** per l'app e fare clic su **successivo**: 

    [![](table-views-images/table03.png "Immettere un nome per l'app")](table-views-images/table03.png#lightbox)
1. Una regolare la **nome progetto** e **Nome soluzione** o accettare le impostazioni predefinite e fare clic sui **Create** pulsante per creare la nuova soluzione: 

    [![](table-views-images/table04.png "Il nome di progetto e soluzione")](table-views-images/table04.png#lightbox)
1. Nel **riquadro della soluzione**, fare doppio clic il `Main.storyboard` file per aprirlo in iOS Designer: 

    [![](table-views-images/table05.png "File Main.")](table-views-images/table05.png#lightbox)
1. Selezionare ed eliminare le **Controller di visualizzazione predefinito**: 

    [![](table-views-images/table06.png "Selezionare ed eliminare il Controller di visualizzazione predefinito")](table-views-images/table06.png#lightbox)
1. Selezionare una **Controller doppia visualizzazione** dalle **della casella degli strumenti** e trascinarlo nell'area di progettazione.
1. Per impostazione predefinita, si otterrà un [doppia visualizzazione](~/ios/tvos/user-interface/split-views.md) con un **Controller di visualizzazione esplorazione** e un **Controller visualizzazione tabella** nella parte sinistra e un **Controllerdivisualizzazione** nella parte destra. Questo è l'utilizzo consigliato di Apple di una visualizzazione tabella in tvOS: 

    [![](table-views-images/table08.png "Aggiungere una doppia visualizzazione")](table-views-images/table08.png#lightbox)
1. Si dovrà selezionare tutte le parti della visualizzazione della tabella e assegnarla a una classe personalizzata **nome della classe** nel **Widget** scheda della finestra di **Esplora proprietà** in modo che è possibile accedervi in un secondo momento nel C#codice. Ad esempio, il **Controller visualizzazione tabella**: 

    [![](table-views-images/table09.png "Assegnare un nome di classe")](table-views-images/table09.png#lightbox)
1. Assicurarsi di creare una classe personalizzata per il **Controller visualizzazione tabella**, il **tabella vista** e l'eventuale **celle prototipo**. Visual Studio per Mac aggiungerà le classi personalizzate all'albero del progetto man mano che vengono creati: 

    [![](table-views-images/table10.png "Le classi personalizzate nell'albero del progetto")](table-views-images/table10.png#lightbox)
1. Successivamente, selezionare la visualizzazione tabella nell'area di progettazione e modificare proprietà in base alle esigenze. Ad esempio il numero di **celle prototipo** e il **stile** (normale o raggruppate): 

    [![](table-views-images/table11.png "La scheda di widget")](table-views-images/table11.png#lightbox)
1. Per ogni **cella prototipo**, selezionarlo e assegnare un valore univoco **identificatore** nel **Widget** scheda della finestra di **Esplora proprietà**. Questo passaggio viene _molto importante_ perché sarà necessario questo identificatore in un secondo momento quando si popola la tabella. Ad esempio `AttrCell`: 

    [![](table-views-images/table12.png "La scheda di Widget")](table-views-images/table12.png#lightbox)
1. È anche possibile selezionare per presentare la cella come una del [visualizzazione di tabella predefinito di tipi di cella](#table-view-cell-types) tramite il **stile** elenco a discesa o impostarlo su **personalizzato** e utilizzare l'area di progettazione al layout della cella mediante il trascinamento di altri widget dell'interfaccia utente dal **casella degli strumenti**: 

    [![](table-views-images/table13.png "Il layout della cella")](table-views-images/table13.png#lightbox)
1. Assegnare un valore univoco **nome** a ogni elemento dell'interfaccia utente nella soluzione il prototipo cella nel **Widget** scheda della finestra di **Esplora proprietà** in modo che sia possibile accedervi successivamente in C# codice: 

    [![](table-views-images/table14.png "Assegnare un nome")](table-views-images/table14.png#lightbox)
1. Ripetere il passaggio precedente per tutte le celle di prototipo nella visualizzazione della tabella.
1. Quindi assegnare le classi personalizzate per il resto di progettazione dell'interfaccia utente, layout la visualizzazione dei dettagli e assegnare univoco **nomi** a ogni elemento dell'interfaccia utente nei dettagli di visualizzare in modo che sia possibile accedervi in C# nonché. Di seguito è riportato un esempio: 

    [![](table-views-images/table15.png "Il layout dell'interfaccia utente")](table-views-images/table15.png#lightbox)
1. Salvare le modifiche allo Storyboard.
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. In Visual Studio, avviare un nuovo progetto di app tvOS e selezionare **tvOS** > **App visualizzazione singola** e immettere un nome per l'app. Scegliere il **Okay** pulsante per creare una nuova soluzione: 

    [![](table-views-images/table02-vs.png "Selezionare App visualizzazione singola")](table-views-images/table02-vs.png#lightbox)
1. Nel **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file per aprirlo in iOS Designer: 

    [![](table-views-images/table05-vs.png "File Main.")](table-views-images/table05-vs.png#lightbox)
1. Selezionare ed eliminare le **Controller di visualizzazione predefinito**: 

    [![](table-views-images/table06-vs.png "Selezionare ed eliminare il Controller di visualizzazione predefinito")](table-views-images/table06-vs.png#lightbox)
1. Selezionare una **Controller doppia visualizzazione** dalle **della casella degli strumenti** e trascinarlo nell'area di progettazione: 

    [![](table-views-images/table07-vs.png "Un Controller di visualizzazione suddivisa")](table-views-images/table07-vs.png#lightbox)
1. Per impostazione predefinita, si otterrà un [doppia visualizzazione](~/ios/tvos/user-interface/split-views.md) con un **Controller di visualizzazione esplorazione** e un **Controller visualizzazione tabella** nella parte sinistra e un **Controllerdivisualizzazione** nella parte destra. Questo è l'utilizzo consigliato di Apple di una visualizzazione tabella in tvOS: 

    [![](table-views-images/table08-vs.png "Layout dell'interfaccia utente")](table-views-images/table08-vs.png#lightbox)
1. Si dovrà selezionare tutte le parti della visualizzazione della tabella e assegnarla a una classe personalizzata **nome della classe** nel **Widget** scheda della finestra di **Esplora proprietà** in modo che è possibile accedervi in un secondo momento nel C#codice. Ad esempio, il **Controller visualizzazione tabella**: 

    [![](table-views-images/table09-vs.png "La scheda di Widget")](table-views-images/table09-vs.png#lightbox)
1. Assicurarsi di creare una classe personalizzata per il **Controller visualizzazione tabella**, il **tabella vista** e l'eventuale **celle prototipo**. Visual Studio per Mac aggiungerà le classi personalizzate all'albero del progetto man mano che vengono creati: 

    [![](table-views-images/table10-vs.png "Le classi personalizzate nell'albero del progetto")](table-views-images/table10-vs.png#lightbox)
1. Successivamente, selezionare la visualizzazione tabella nell'area di progettazione e modificare proprietà in base alle esigenze. Ad esempio il numero di **celle prototipo** e il **stile** (normale o raggruppate): 

    [![](table-views-images/table11-vs.png "La scheda di Widget")](table-views-images/table11-vs.png#lightbox)
1. Per ogni **cella prototipo**, selezionarlo e assegnare un valore univoco **identificatore** nel **Widget** scheda della finestra di **Esplora proprietà**. Questo passaggio viene _molto importante_ perché sarà necessario questo identificatore in un secondo momento quando si popola la tabella. Ad esempio `AttrCell`: 

    [![](table-views-images/table12-vs.png "Assegnare un identificatore")](table-views-images/table12-vs.png#lightbox)
1. È anche possibile selezionare per presentare la cella come una del [visualizzazione di tabella predefinito di tipi di cella](#table-view-cell-types) tramite il **stile** elenco a discesa o impostarlo su **personalizzato** e utilizzare l'area di progettazione al layout della cella mediante il trascinamento di altri widget dell'interfaccia utente dal **casella degli strumenti**: 

    [![](table-views-images/table13-vs.png "L'elenco a discesa stile")](table-views-images/table13-vs.png#lightbox)
1. Assegnare un valore univoco **nome** a ogni elemento dell'interfaccia utente nella soluzione il prototipo cella nel **Widget** scheda della finestra di **Esplora proprietà** in modo che sia possibile accedervi successivamente in C# codice: 

    [![](table-views-images/table14-vs.png "La scheda di Widget")](table-views-images/table14-vs.png#lightbox)
1. Ripetere il passaggio precedente per tutte le celle di prototipo nella visualizzazione della tabella.
1. Quindi assegnare le classi personalizzate per il resto di progettazione dell'interfaccia utente, layout la visualizzazione dei dettagli e assegnare univoco **nomi** a ogni elemento dell'interfaccia utente nei dettagli di visualizzare in modo che sia possibile accedervi in C# nonché. Di seguito è riportato un esempio: 

    [![](table-views-images/table15.png "Il Layout dell'interfaccia utente")](table-views-images/table15.png#lightbox)
1. Salvare le modifiche allo Storyboard.
    
-----

<a name="Designing-a-Data-Model" />

## <a name="designing-a-data-model"></a>Progettazione di un modello di dati

Per agevolare l'uso con le informazioni che la tabella verranno visualizzati più facilmente e per semplificare la presentazione di informazioni dettagliate (come l'utente seleziona o evidenzia le righe nella vista tabella), creare una classe personalizzata o le classi da usare come modello di dati per le informazioni presentate .

Considerare di un'applicazione di prenotazioni di viaggio che contiene un elenco di esempio **Città**, ognuna contenente un elenco univoco di **attrazioni famosi** che l'utente può selezionare. L'utente sarà in grado di contrassegnare un attrazione come un *preferite*, selezionare questa opzione per ottenere *direzioni* a un attrazione e *prenotare un volo* per una determinata città.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per creare il modello di dati per un **attrazione**, fare clic sul nome del progetto nel **riquadro della soluzione** e selezionare **Add** > **nuovo File...** . Immettere `AttractionInformation` per il **Name** e fare clic sui **New** pulsante: 

[![](table-views-images/data01.png "Immettere nome AttractionInformation")](table-views-images/data01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per creare il modello di dati per un **attrazione**, fare clic sul nome del progetto nel **Esplora soluzioni** e selezionare **Add** > **nuovo elemento ...** . Selezionare **classe** e immettere `AttractionInformation` per il **Name** e fare clic sui **Aggiungi** pulsante: 

[![](table-views-images/data01-vs.png "Seleziona classe e immettere AttractionInformation per nome")](table-views-images/data01-vs.png#lightbox)

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

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Successivamente, fare clic sul nome del progetto nel **riquadro della soluzione** nuovamente e selezionare **Add** > **nuovo File...** . Immettere `CityInformation` per il **Name** e fare clic sui **New** pulsante: 

[![](table-views-images/data02.png "Immettere nome CityInformation")](table-views-images/data02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Successivamente, fare clic sul nome del progetto nel **Esplora soluzioni** nuovamente e selezionare **Add** > **nuovo elemento...** . Immettere `CityInformation` per il **Name** e fare clic sui **Add** pulsante: 

[![](table-views-images/data02-vs.png "Immettere nome CityInformation")](table-views-images/data02-vs.png#lightbox)

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

Questa classe contiene tutte le informazioni su una destinazione **Città**, una raccolta di **attrazioni famosi** per la città e sono disponibili due metodi helper (`AddAttraction`) per renderne più semplice aggiungere attrazioni famosi per la città.

<a name="The-Table-Data-Source" />

## <a name="the-table-view-data-source"></a>L'origine dati di visualizzazione tabella

Ogni visualizzazione tabella richiede un'origine dati (`UITableViewDataSource`) per fornire i dati per la tabella e generare le righe necessarie come richiesto dalla visualizzazione della tabella.

Per l'esempio sopra riportato, pulsante destro del mouse sul nome del progetto nel **Esplora soluzioni**, selezionare **Add** > **nuovo File...**  e denominarlo `AttractionTableDatasource` e fare clic sui **New** pulsante per la creazione. Modificare quindi il `AttractionTableDatasource.cs` file e renderlo simile al seguente:

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

Diamo un'occhiata solo alcune sezioni della classe in modo dettagliato.

Innanzitutto, abbiamo definito una costante per contenere l'identificatore univoco della cella prototipo (questo è lo stesso identificatore assegnato nella finestra di progettazione dell'interfaccia precedente), aggiunto un collegamento al Controller di visualizzazione tabella e creato una risorsa di archiviazione per i dati:

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

Ai fini di esempio, il `PopulateCities` metodo crea semplicemente gli oggetti del modello di dati in memoria ma queste possono essere letti con facilità da un servizio web o di database in una vera app:

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

Il `NumberOfSections` metodo restituisce il numero delle sezioni della tabella:

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    // Return number of cities
    return Cities.Count;
}
```

Per la **Plain** nello stile viste delle tabelle, viene restituito sempre 1.

Il `RowsInSection` metodo restituisce il numero di righe della sezione corrente:

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

Anche in questo caso, per **Plain** viste delle tabelle, restituisce il numero totale di elementi nell'origine dati.

Il `TitleForHeader` metodo restituisce il titolo per un determinato sezione:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

Per un **Plain** visualizzazione tabella digitare, lasciare vuoto il titolo (`""`).

Infine, quando richiesto da visualizzazione tabella, creare e popolare un prototipo cella tramite la `GetCell` metodo: 

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

Per altre informazioni sull'uso di un `UITableViewDatasource`, vedere di Apple [UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941) documentazione.

<a name="The-Table-View-Delegate" />

## <a name="the-table-view-delegate"></a>Il delegato di visualizzazione tabella

Ogni visualizzazione tabella richiede un delegato (`UITableViewDelegate`) di rispondere all'interazione dell'utente o altri eventi di sistema per la tabella.

Per l'esempio sopra riportato, pulsante destro del mouse sul nome del progetto nel **Esplora soluzioni**, selezionare **Add** > **nuovo File...**  e denominarlo `AttractionTableDelegate` e fare clic sui **New** pulsante per la creazione. Modificare quindi il `AttractionTableDelegate.cs` file e renderlo simile al seguente:

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

In primo luogo, creiamo un collegamento al Controller di visualizzazione tabella quando viene creata la classe:

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

Quindi, quando è selezionata una riga (l'utente fa clic sulla superficie Touch di remoto di Apple) si desidera contrassegnare il **attrazione** rappresentato dalla riga selezionata come preferito:

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

Successivamente, quando l'utente viene evidenziata una riga (assegnandogli lo stato attivo tramite l'area di Apple remoto Touch) che si desidera presentare i dettagli del **attrazione** rappresentato da tale riga nella sezione dei dettagli del nostro Controller doppia visualizzazione:

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

Il `CanFocusRow` viene chiamato per ogni riga che sta per ottenere lo stato attivo nella visualizzazione della tabella. Restituire `true` se la riga è possibile ottenere lo stato attivo, altrimenti restituire `false`. In questo esempio abbiamo creato un oggetto personalizzato `AttractionHighlighted` evento che viene generato ogni riga quando riceve lo stato attivo.

Per altre informazioni sull'uso di un `UITableViewDelegate`, vedere di Apple [UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942) documentazione.

<a name="The-Table-View-Cell" />

## <a name="the-table-view-cell"></a>La cella di visualizzazione tabella

Per ogni cella di prototipo che è stato aggiunto alla visualizzazione tabella nella finestra di progettazione dell'interfaccia, è stato creato anche un'istanza personalizzata di cella di visualizzazione della tabella (`UITableViewCell`) consente di popolare la nuova cella (riga) quando viene creata.

Per l'app di esempio, fare doppio clic il `AttractionTableCell.cs` file per aprirlo e modificarlo e renderlo simile al seguente:

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

Questa classe fornisce l'archiviazione per l'oggetto modello di dati attrazione (`AttractionInformation` come definito in precedenza) visualizzato nella riga specificata:

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

Il `UpdateUI` metodo popola i widget dell'interfaccia utente (che sono stati aggiunti per il prototipo della cella nella finestra di interfaccia di progettazione) in base alle esigenze:

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

Per altre informazioni sull'uso di un `UITableViewCell`, vedere di Apple [UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938) documentazione.

<a name="The-Table-View-Controller" />

## <a name="the-table-view-controller"></a>Il Controller di visualizzazione tabella

Un Controller visualizzazione tabella (`UITableViewController`) gestisce una visualizzazione tabella che è stato aggiunto a uno Storyboard tramite la finestra di progettazione di interfaccia.

Per l'app di esempio, fare doppio clic il `AttractionTableViewController.cs` file per aprirlo e modificarlo e renderlo simile al seguente:

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

Diamo uno sguardo a questa classe. Innanzitutto, abbiamo creato tasti di scelta rapida per renderne più semplice accedere alla visualizzazione di tabella `DataSource` e `TableDelegate`. Si userà quelli in un secondo momento per la comunicazione tra la visualizzazione di tabella sul lato sinistro della visualizzazione suddivisa e la visualizzazione dettagli di destra.

Infine, quando la visualizzazione della tabella viene caricata in memoria, si crea istanze del `AttractionTableDatasource` e `AttractionTableDelegate` (sia creato in precedenza) e collegarli alla visualizzazione della tabella.

Per altre informazioni sull'uso di un `UITableViewController`, vedere di Apple [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523) documentazione.

<a name="Pulling-it-All-Together" />

## <a name="pulling-it-all-together"></a>Eseguendone il pull tutte insieme

Come indicato all'inizio di questo documento, le visualizzazioni di tabelle vengono in genere visualizzate in un lato di una [doppia visualizzazione](~/ios/tvos/user-interface/split-views.md) come riquadro di spostamento con i dettagli dell'elemento selezionato visualizzato sul lato opposto. Ad esempio: 

[![](table-views-images/intro01.png "Esecuzione di app di esempio")](table-views-images/intro01.png#lightbox)

Poiché si tratta di un modello standard in tvOS, esaminiamo i passaggi finali per riunire tutti gli elementi e avere i lati sinistro e destro della visualizzazione suddivisa interagiscono tra loro.

<a name="The-Detail-View" />

### <a name="the-detail-view"></a>La visualizzazione dettagli

Per l'esempio di app di viaggio presentata nell'esempio precedente, una classe personalizzata (`AttractionViewController`) è definito per il Controller di visualizzazione standard presentati nel lato destro della visualizzazione suddivisa come la visualizzazione di dettaglio:

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

In questo caso, è stata fornita il **attrazione** (`AttractionInformation`) viene visualizzato come una proprietà e creato una `UpdateUI` metodo che consente di popolare i widget dell'interfaccia utente aggiunte alla vista nella finestra di progettazione dell'interfaccia.

Abbiamo inoltre definito un collegamento al Controller doppia visualizzazione (`SplitView`) che si userà per comunicare modifiche tornare alla visualizzazione di tabella (`AcctractionTableView`).

Infine, le azioni personalizzate (eventi) sono stati aggiunti ai tre `UIButton` le istanze create nella finestra di progettazione dell'interfaccia, che consente di contrassegnare un attrazione come un _preferito_, ottenere _direzioni_ a un attrazione e _prenotare un volo_ per una determinata città.

<a name="The-Navigation-View-Controller" />

### <a name="the-navigation-view-controller"></a>Il Controller di visualizzazione esplorazione

Poiché il Controller di visualizzazione tabella è annidato in un Controller di visualizzazione di spostamento sul lato sinistro della visualizzazione divisa, il Controller di visualizzazione di esplorazione è stato assegnato una classe personalizzata (`MasterNavigationController`) nella finestra di progettazione dell'interfaccia e definiti come segue:

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

Anche in questo caso, questa classe definisce solo alcuni tasti di scelta rapida per renderne più semplice per la comunicazione tra i due lati del Controller di visualizzazione di suddivisione:

* `SplitView` -È un collegamento al Controller di visualizzazione Split (`MainSpiltViewController`) a cui appartiene il Controller di visualizzazione esplorazione.
* `TableController` -Ottiene il Controller di visualizzazione tabella (`AttractionTableViewController`) che viene presentato come la visualizzazione di alto nel Controller di visualizzazione esplorazione.

<a name="The-Split-View-Controller" />

### <a name="the-split-view-controller"></a>Il Controller di visualizzazione suddivisa

Poiché il Controller di visualizzazione di divisione è la base dell'applicazione, abbiamo creato una classe personalizzata (`MasterSplitViewController`) nella finestra di progettazione dell'interfaccia e viene definito come segue:

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

È necessario innanzitutto creare tasti di scelta rapida per il **dettagli** lato della visualizzazione divisa (`AttractionViewController`) e il **Master** lato (`MasterNavigationController`). Anche in questo caso, ciò rende più semplice per la comunicazione tra i due lati in un secondo momento.

Successivamente, quando la doppia visualizzazione viene caricata in memoria, si collega il Controller di visualizzazione divisa per entrambi i lati della visualizzazione suddivisa e rispondere all'utente l'evidenziazione nella visualizzazione tabella un attrazione (`AttractionHighlighted`) visualizzando il nuovo attrazione nel **dettagli**  lato della visualizzazione suddivisa.

Vedere le [tvTables](https://developer.xamarin.com/samples/monotouch/tvos/tvTable/) app di esempio per un'implementazione completa di viste delle tabelle all'interno di una doppia visualizzazione.

## <a name="table-views-in-detail"></a>Visualizzazioni di tabelle in modo dettagliato

Poiché tvOS si basa su iOS, le visualizzazioni di tabelle e i controller di visualizzazione tabella sono progettati e si comportano in modo analogo. Per informazioni più dettagliate sull'uso di visualizzazione tabella in un'app Xamarin, vedere il nostro iOS [utilizzo di tabelle e celle](~/ios/user-interface/controls/tables/index.md) documentazione.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la progettazione e l'utilizzo di viste delle tabelle all'interno di un'app xamarin. tvos. E ha presentato un esempio di utilizzo di una visualizzazione tabella all'interno di una doppia visualizzazione, ovvero l'uso tipico di una visualizzazione tabella in un'app tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
