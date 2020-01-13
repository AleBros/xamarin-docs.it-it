---
title: Uso delle viste tabella tvOS in Novell
description: In questo articolo viene illustrata la progettazione e l'utilizzo di viste tabella e di controller di visualizzazione tabella all'interno di un'app Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: db2e692993b7d452b81024ba1d50788e82b7ab86
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022197"
---
# <a name="working-with-tvos-table-views-in-xamarin"></a>Uso delle viste tabella tvOS in Novell

_In questo articolo viene illustrata la progettazione e l'utilizzo di viste tabella e di controller di visualizzazione tabella all'interno di un'app Xamarin.tvOS._

In tvOS, una vista tabella viene presentata come una singola colonna di righe di scorrimento che può essere facoltativamente organizzata in gruppi o sezioni. Le visualizzazioni di tabella devono essere usate quando è necessario visualizzare in modo efficiente una grande quantità di dati per l'utente, in modo chiaro e comprensibile.

Le visualizzazioni di tabella vengono in genere visualizzate in un lato di una [visualizzazione divisa](~/ios/tvos/user-interface/split-views.md) come navigazione, con i dettagli dell'elemento selezionato visualizzato sul lato opposto:

[![](table-views-images/intro01.png "Sample table view")](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views" />

## <a name="about-table-views"></a>Informazioni sulle viste tabella

Un `UITableView` Visualizza una singola colonna di righe scorrevoli come un elenco gerarchico di informazioni che possono essere organizzate facoltativamente in gruppi o sezioni: 

[![](table-views-images/table01.png "A selected item")](table-views-images/table01.png#lightbox)

Apple presenta i suggerimenti seguenti per l'utilizzo delle tabelle:

- Tenere **presente la larghezza** : provare a raggiungere il giusto equilibrio nelle larghezze della tabella. Se la tabella è troppo ampia, può essere difficile eseguire l'analisi da una distanza e può ricavare dall'area del contenuto disponibile. Se la tabella è troppo stretta, può causare il troncamento o il wrapping delle informazioni, di conseguenza può essere difficile per l'utente leggere da tutta la stanza.
- **Mostra il contenuto della tabella rapidamente** : per elenchi di dati di grandi dimensioni, caricare Lazy il contenuto e iniziare a visualizzare le informazioni non appena la tabella viene presentata all'utente. Se la tabella impiega molto tempo per il caricamento, l'utente potrebbe perdere interesse nell'app o ritenere che sia bloccato.
- **Informare l'utente di carichi di contenuto lunghi** : se il tempo di caricamento di una tabella lunga è inevitabile, presentare un [indicatore di stato o un indicatore di attività](~/ios/tvos/user-interface/progress-indicators.md) in modo da sapere che l'app non è bloccata.

<a name="Table-Cell-Types" />

## <a name="table-view-cell-types"></a>Tipi di cella di visualizzazione tabella

Una `UITableViewCell` viene utilizzata per rappresentare le singole righe di dati nella visualizzazione tabella. Apple ha definito diversi tipi di celle di tabella predefiniti:

- **Impostazione predefinita** : questo tipo presenta un'immagine di opzione sul lato sinistro della cella e il titolo allineato a sinistra a destra. 
- **Sottotitolo** : questo tipo presenta un titolo allineato a sinistra nella prima riga e un sottotitolo allineato a sinistra più piccolo nella riga successiva.
- **Valore 1** : questo tipo presenta un titolo allineato a sinistra con un sottotitolo di colore più chiaro, allineato a destra sulla stessa riga.
- **Valore 2** : questo tipo presenta un titolo allineato a destra con un sottotitolo più chiaro e allineato a sinistra nella stessa riga.

Tutti i tipi di cella di visualizzazione tabella predefiniti supportano anche elementi grafici come indicatori di divulgazione o segni di spunta. 

Inoltre, è possibile definire un tipo di cella di visualizzazione tabella **personalizzato** e presentare una _cella del prototipo_, che si crea in progettazione interfaccia o tramite codice.

Apple presenta i suggerimenti seguenti per l'utilizzo delle celle di visualizzazione tabella:

- **Evitare il ritaglio del testo** : tenere le singole righe di testo brevi, in modo che non vengano troncate. Le parole o le frasi troncate sono difficili da analizzare per l'utente in tutta la stanza.
- **Considerare lo stato della riga con stato** attivo, perché una riga diventa più grande, con angoli più arrotondati quando sono concentrati, è necessario testare l'aspetto della cella in tutti gli Stati. Le immagini o il testo potrebbero essere ritagliati o sembrare non corretti nello stato attivo.
- **Usare tabelle modificabili in modalità sporadica** : lo sfasamento o l'eliminazione di righe di tabella è più lungo in tvOS rispetto a iOS. È necessario decidere attentamente se questa funzionalità verrà aggiunta o distratto dall'app tvOS.
- **Creazione di tipi di celle personalizzati, laddove appropriato** . i tipi di cella di visualizzazione tabella incorporata sono ottimi per molte situazioni, valutare la possibilità di creare tipi di celle personalizzati per informazioni non standard, in modo da fornire un maggiore controllo e di presentare meglio le informazioni al utente.

<a name="Working-With-Table-Views" />

## <a name="working-with-table-views"></a>Utilizzo delle visualizzazioni di tabella

Il modo più semplice per usare le visualizzazioni di tabella in un'app Xamarin.tvOS consiste nel crearlo e modificarne l'aspetto nella finestra di progettazione dell'interfaccia.

Per iniziare, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. In Visual Studio per Mac avviare un nuovo progetto di app tvOS e selezionare **tvOS** > **app** > **visualizzazione singola app** e fare clic sul pulsante **Avanti** : 

    [![](table-views-images/table02.png "Select Single View App")](table-views-images/table02.png#lightbox)
1. Immettere un **nome** per l'app e fare clic su **Avanti**: 

    [![](table-views-images/table03.png "Enter a Name for the app")](table-views-images/table03.png#lightbox)
1. Modificare il **nome del progetto** e il **nome della soluzione** oppure accettare le impostazioni predefinite e fare clic sul pulsante **Crea** per creare la nuova soluzione: 

    [![](table-views-images/table04.png "The Project Name and Solution Name")](table-views-images/table04.png#lightbox)
1. Nella **riquadro della soluzione**fare doppio clic sul file `Main.storyboard` per aprirlo in iOS designer: 

    [![](table-views-images/table05.png "The Main.storyboard file")](table-views-images/table05.png#lightbox)
1. Selezionare ed eliminare il **controller di visualizzazione predefinito**: 

    [![](table-views-images/table06.png "Select and delete the Default View Controller")](table-views-images/table06.png#lightbox)
1. Selezionare un **controller di visualizzazione divisa** dalla **casella degli strumenti** e trascinarlo nella area di progettazione.
1. Per impostazione predefinita, si otterrà una [visualizzazione divisa](~/ios/tvos/user-interface/split-views.md) con un **controller di visualizzazione di navigazione** e un **controller di visualizzazione tabella** sul lato sinistro e un **controller di visualizzazione** nella parte destra. Si tratta dell'utilizzo suggerito da Apple di una vista tabella in tvOS: 

    [![](table-views-images/table08.png "Add a Split View")](table-views-images/table08.png#lightbox)
1. È necessario selezionare ogni parte della visualizzazione tabella e assegnarle un **nome di classe** personalizzato nella scheda **widget** di **Esplora proprietà** in modo che sia possibile accedervi in un secondo momento nel C# codice. Ad esempio, il **controller di visualizzazione tabella**: 

    [![](table-views-images/table09.png "Assign a class name")](table-views-images/table09.png#lightbox)
1. Assicurarsi di creare una classe personalizzata per il **controller di visualizzazione tabella**, la **visualizzazione tabella** e le **celle del prototipo**. Visual Studio per Mac aggiungerà le classi personalizzate all'albero del progetto man mano che vengono create: 

    [![](table-views-images/table10.png "The custom classes in the Project Tree")](table-views-images/table10.png#lightbox)
1. Selezionare quindi la visualizzazione tabella nella Area di progettazione e modificarne le proprietà in base alle esigenze. Ad esempio il numero di **celle del prototipo** e lo **stile** (normale o raggruppato): 

    [![](table-views-images/table11.png "The widget tab")](table-views-images/table11.png#lightbox)
1. Per ogni **cella del prototipo**, selezionarla e assegnare un **identificatore** univoco nella scheda **widget** di **Esplora proprietà**. Questo passaggio è _molto importante_ perché questo identificatore sarà necessario in un secondo momento quando si popola la tabella. Ad esempio `AttrCell`: 

    [![](table-views-images/table12.png "The Widget Tab")](table-views-images/table12.png#lightbox)
1. È inoltre possibile selezionare questa opzione per presentare la cella come uno dei [tipi di cella di visualizzazione tabella predefiniti](#table-view-cell-types) tramite l'elenco a discesa **stile** o impostarlo su **personalizzato** e utilizzare il area di progettazione per il layout della cella trascinando in altri widget dell'interfaccia utente dalla **casella degli strumenti**: 

    [![](table-views-images/table13.png "The cell layout")](table-views-images/table13.png#lightbox)
1. Assegnare un **nome** univoco a ogni elemento dell'interfaccia utente nella progettazione della cella Prototype nella scheda **widget** di **Esplora proprietà** in modo che sia possibile accedervi C# in un secondo momento nel codice: 

    [![](table-views-images/table14.png "Assign a name")](table-views-images/table14.png#lightbox)
1. Ripetere il passaggio precedente per tutte le celle del prototipo nella visualizzazione tabella.
1. Assegnare quindi classi personalizzate al resto della progettazione dell'interfaccia utente, impostare il layout della visualizzazione dettagli e assegnare **nomi** univoci a ogni elemento dell'interfaccia utente nella visualizzazione dettagli, in modo da C# poter accedere anche a tali classi. Di seguito è riportato un esempio: 

    [![](table-views-images/table15.png "The UI layout")](table-views-images/table15.png#lightbox)
1. Salvare le modifiche apportate allo storyboard.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. In Visual Studio avviare un nuovo progetto di app tvOS e selezionare **tvOS** > **app visualizzazione singola** e immettere un nome per l'app. Fare clic sul pulsante **OK** per creare una nuova soluzione: 

    [![](table-views-images/table02-vs.png "Select Single View App")](table-views-images/table02-vs.png#lightbox)
1. Nella **Esplora soluzioni**fare doppio clic sul file `Main.storyboard` per aprirlo in iOS designer: 

    [![](table-views-images/table05-vs.png "The Main.storyboard file")](table-views-images/table05-vs.png#lightbox)
1. Selezionare ed eliminare il **controller di visualizzazione predefinito**: 

    [![](table-views-images/table06-vs.png "Select and delete the Default View Controller")](table-views-images/table06-vs.png#lightbox)
1. Selezionare un **controller di visualizzazione divisa** dalla **casella degli strumenti** e trascinarlo nella area di progettazione: 

    [![](table-views-images/table07-vs.png "A Split View Controller")](table-views-images/table07-vs.png#lightbox)
1. Per impostazione predefinita, si otterrà una [visualizzazione divisa](~/ios/tvos/user-interface/split-views.md) con un **controller di visualizzazione di navigazione** e un **controller di visualizzazione tabella** sul lato sinistro e un **controller di visualizzazione** nella parte destra. Si tratta dell'utilizzo suggerito da Apple di una vista tabella in tvOS: 

    [![](table-views-images/table08-vs.png "Layout the UI")](table-views-images/table08-vs.png#lightbox)
1. È necessario selezionare ogni parte della visualizzazione tabella e assegnarle un **nome di classe** personalizzato nella scheda **widget** di **Esplora proprietà** in modo che sia possibile accedervi in un secondo momento nel C# codice. Ad esempio, il **controller di visualizzazione tabella**: 

    [![](table-views-images/table09-vs.png "The Widget Tab")](table-views-images/table09-vs.png#lightbox)
1. Assicurarsi di creare una classe personalizzata per il **controller di visualizzazione tabella**, la **visualizzazione tabella** e le **celle del prototipo**. Visual Studio per Mac aggiungerà le classi personalizzate all'albero del progetto man mano che vengono create: 

    [![](table-views-images/table10-vs.png "The custom classes in the Project Tree")](table-views-images/table10-vs.png#lightbox)
1. Selezionare quindi la visualizzazione tabella nella Area di progettazione e modificarne le proprietà in base alle esigenze. Ad esempio il numero di **celle del prototipo** e lo **stile** (normale o raggruppato): 

    [![](table-views-images/table11-vs.png "The Widget Tab")](table-views-images/table11-vs.png#lightbox)
1. Per ogni **cella del prototipo**, selezionarla e assegnare un **identificatore** univoco nella scheda **widget** di **Esplora proprietà**. Questo passaggio è _molto importante_ perché questo identificatore sarà necessario in un secondo momento quando si popola la tabella. Ad esempio `AttrCell`: 

    [![](table-views-images/table12-vs.png "Assign an Identifier")](table-views-images/table12-vs.png#lightbox)
1. È inoltre possibile selezionare questa opzione per presentare la cella come uno dei [tipi di cella di visualizzazione tabella predefiniti](#table-view-cell-types) tramite l'elenco a discesa **stile** o impostarlo su **personalizzato** e utilizzare il area di progettazione per il layout della cella trascinando in altri widget dell'interfaccia utente dalla **casella degli strumenti**: 

    [![](table-views-images/table13-vs.png "The Style dropdown")](table-views-images/table13-vs.png#lightbox)
1. Assegnare un **nome** univoco a ogni elemento dell'interfaccia utente nella progettazione della cella Prototype nella scheda **widget** di **Esplora proprietà** in modo che sia possibile accedervi C# in un secondo momento nel codice: 

    [![](table-views-images/table14-vs.png "The Widget Tab")](table-views-images/table14-vs.png#lightbox)
1. Ripetere il passaggio precedente per tutte le celle del prototipo nella visualizzazione tabella.
1. Assegnare quindi classi personalizzate al resto della progettazione dell'interfaccia utente, impostare il layout della visualizzazione dettagli e assegnare **nomi** univoci a ogni elemento dell'interfaccia utente nella visualizzazione dettagli, in modo da C# poter accedere anche a tali classi. Di seguito è riportato un esempio: 

    [![](table-views-images/table15.png "The UI Layout")](table-views-images/table15.png#lightbox)
1. Salvare le modifiche apportate allo storyboard.

-----

<a name="Designing-a-Data-Model" />

## <a name="designing-a-data-model"></a>Progettazione di un modello di dati

Per fare in modo che le informazioni visualizzate nella visualizzazione tabella risultino più semplici e per semplificare la presentazione di informazioni dettagliate (quando l'utente seleziona o evidenzia righe nella visualizzazione tabella), creare una classe o classi personalizzate da utilizzare come modello di dati per le informazioni presentate .

Eseguire l'esempio di un'app di prenotazione di viaggio contenente un elenco di **città**, ognuna contenente un elenco univoco di **attrazioni** che l'utente può selezionare. L'utente potrà contrassegnare un'attrazione come *preferita*, scegliere di ottenere le *indicazioni* per un'attrazione e *prenotare un volo* a una determinata città.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per creare il modello di dati per un' **attrazione**, fare clic con il pulsante destro del mouse sul nome del progetto nella **riquadro della soluzione** e scegliere **Aggiungi** > **nuovo file**. Immettere `AttractionInformation` per il **nome** e fare clic sul pulsante **nuovo** : 

[![](table-views-images/data01.png "Enter AttractionInformation for the Name")](table-views-images/data01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per creare il modello di dati per un' **attrazione**, fare clic con il pulsante destro del mouse sul nome del progetto nella **Esplora soluzioni** e scegliere **Aggiungi** > **nuovo elemento**. Selezionare **classe** e immettere `AttractionInformation` per **nome** e fare clic sul pulsante **Aggiungi** : 

[![](table-views-images/data01-vs.png "Select Class and enter AttractionInformation for the Name")](table-views-images/data01-vs.png#lightbox)

-----

Modificare il file di `AttractionInformation.cs` e renderlo simile al seguente:

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

Questa classe fornisce le proprietà per archiviare le informazioni su una determinata **attrazione**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Fare quindi clic con il pulsante destro del mouse sul nome del progetto nella **riquadro della soluzione** e selezionare **Aggiungi** > **nuovo file.** Immettere `CityInformation` per il **nome** e fare clic sul pulsante **nuovo** : 

[![](table-views-images/data02.png "Enter CityInformation for the Name")](table-views-images/data02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Fare quindi clic con il pulsante destro del mouse sul nome del progetto nella **Esplora soluzioni** e selezionare **Aggiungi** > **nuovo elemento...**. Immettere `CityInformation` per il **nome** e fare clic sul pulsante **Aggiungi** : 

[![](table-views-images/data02-vs.png "Enter CityInformation for the Name")](table-views-images/data02-vs.png#lightbox)

-----

Modificare il file di `CityInformation.cs` e renderlo simile al seguente:

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

Questa classe contiene tutte le informazioni su una **città**di destinazione, una raccolta di **attrazioni** per la città e fornisce due metodi helper (`AddAttraction`) per semplificare l'aggiunta di attrazioni alla città.

<a name="The-Table-Data-Source" />

## <a name="the-table-view-data-source"></a>Origine dati visualizzazione tabella

Ogni vista tabella richiede un'origine dati (`UITableViewDataSource`) per fornire i dati per la tabella e generare le righe necessarie come richiesto dalla vista tabella.

Per l'esempio precedente, fare clic con il pulsante destro del mouse sul nome del progetto nella **Esplora soluzioni**, selezionare **Aggiungi** > **nuovo file** e chiamarlo `AttractionTableDatasource` e fare clic sul pulsante **nuovo** per crearlo. Modificare quindi il file di `AttractionTableDatasource.cs` e renderlo simile al seguente:

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

Verranno ora esaminate in dettaglio alcune sezioni della classe.

In primo luogo, è stata definita una costante per contenere l'identificatore univoco della cella del prototipo (si tratta dello stesso identificatore assegnato nella finestra di progettazione dell'interfaccia precedente), è stato aggiunto un collegamento al controller di visualizzazione tabella e l'archiviazione è stata creata per i dati:

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

Successivamente, si salva il controller di visualizzazione tabella, quindi si compila e si popola l'origine dati (usando i modelli di dati definiti sopra) quando viene creata la classe:

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

Per esempio, il metodo `PopulateCities` crea semplicemente oggetti del modello di dati in memoria, ma questi possono essere facilmente letti da un database o da un servizio Web in un'app reale:

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

Il metodo `NumberOfSections` restituisce il numero di sezioni della tabella:

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    // Return number of cities
    return Cities.Count;
}
```

Per le visualizzazioni di tabella con stile **semplice** , restituisce sempre 1.

Il metodo `RowsInSection` restituisce il numero di righe nella sezione corrente:

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

Anche in questo caso, per le visualizzazioni di tabelle **semplici** , viene restituito il numero totale di elementi nell'origine dati.

Il metodo `TitleForHeader` restituisce il titolo per la sezione specificata:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

Per un tipo di visualizzazione tabella **semplice** , lasciare vuoto il titolo (`""`).

Infine, quando richiesto dalla vista tabella, creare e popolare una cella del prototipo usando il metodo `GetCell`: 

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

Per altre informazioni sull'uso di un `UITableViewDatasource`, vedere la documentazione di Apple [UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941) .

<a name="The-Table-View-Delegate" />

## <a name="the-table-view-delegate"></a>Delegato della visualizzazione tabella

Ogni visualizzazione tabella richiede un delegato (`UITableViewDelegate`) per rispondere all'interazione dell'utente o ad altri eventi di sistema nella tabella.

Per l'esempio precedente, fare clic con il pulsante destro del mouse sul nome del progetto nella **Esplora soluzioni**, selezionare **Aggiungi** > **nuovo file** e chiamarlo `AttractionTableDelegate` e fare clic sul pulsante **nuovo** per crearlo. Modificare quindi il file di `AttractionTableDelegate.cs` e renderlo simile al seguente:

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

Verranno ora esaminate in dettaglio diverse sezioni di questa classe.

Prima di tutto, viene creato un collegamento al controller di visualizzazione tabella quando viene creata la classe:

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

Quindi, quando viene selezionata una riga (l'utente fa clic sull'area di tocco di Apple Remote), si vuole contrassegnare l' **attrazione** rappresentata dalla riga selezionata come preferita:

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

Quindi, quando l'utente evidenzia una riga (assegnando lo stato attivo usando la superficie di tocco remota Apple) si vuole presentare i dettagli dell' **attrazione** rappresentata da tale riga nella sezione dei dettagli del controller di visualizzazione divisa:

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

Il metodo `CanFocusRow` viene chiamato per ogni riga che sta per ottenere lo stato attivo nella visualizzazione tabella. Restituisce `true` se la riga può ottenere lo stato attivo. in caso contrario, restituisce `false`. Nel caso di questo esempio, è stato creato un evento `AttractionHighlighted` personalizzato che verrà generato in ogni riga in quanto riceve lo stato attivo.

Per altre informazioni sull'uso di un `UITableViewDelegate`, vedere la documentazione di Apple [UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942) .

<a name="The-Table-View-Cell" />

## <a name="the-table-view-cell"></a>Cella di visualizzazione tabella

Per ogni cella del prototipo aggiunta alla visualizzazione tabella in progettazione interfacce, è stata creata anche un'istanza personalizzata della cella di visualizzazione tabella (`UITableViewCell`) per consentire di popolare la nuova cella (riga) appena creata.

Per l'app di esempio, fare doppio clic sul file `AttractionTableCell.cs` per aprirlo per la modifica e renderlo simile al seguente:

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

Questa classe fornisce spazio di archiviazione per l'oggetto del modello di dati di attrazione (`AttractionInformation` come definito sopra) visualizzato nella riga specificata:

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

Il metodo `UpdateUI` popola i widget dell'interfaccia utente (che sono stati aggiunti al prototipo della cella in progettazione interfacce) come richiesto:

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

Per altre informazioni sull'uso di un `UITableViewCell`, vedere la documentazione di Apple [UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938) .

<a name="The-Table-View-Controller" />

## <a name="the-table-view-controller"></a>Controller di visualizzazione tabella

Un controller di visualizzazione tabella (`UITableViewController`) gestisce una visualizzazione tabella che è stata aggiunta a uno storyboard tramite la finestra di progettazione interfaccia.

Per l'app di esempio, fare doppio clic sul file `AttractionTableViewController.cs` per aprirlo per la modifica e renderlo simile al seguente:

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

Esaminiamo in dettaglio questa classe. In primo luogo, sono stati creati collegamenti per semplificare l'accesso alla `DataSource` e alla `TableDelegate`della visualizzazione tabella. Verranno usati in un secondo momento per comunicare tra la visualizzazione tabella sul lato sinistro della visualizzazione divisa e la visualizzazione dettagli a destra.

Infine, quando la visualizzazione tabella viene caricata in memoria, si creano istanze del `AttractionTableDatasource` e `AttractionTableDelegate` (create in precedenza) e le si collegano alla visualizzazione tabella.

Per altre informazioni sull'uso di un `UITableViewController`, vedere la documentazione di Apple [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523) .

<a name="Pulling-it-All-Together" />

## <a name="pulling-it-all-together"></a>Riunendola

Come indicato all'inizio di questo documento, le visualizzazioni di tabella vengono in genere visualizzate in un lato di una [visualizzazione divisa](~/ios/tvos/user-interface/split-views.md) come navigazione, con i dettagli dell'elemento selezionato visualizzato sul lato opposto. Esempio: 

[![](table-views-images/intro01.png "Sample app run")](table-views-images/intro01.png#lightbox)

Poiché si tratta di un modello standard in tvOS, è possibile esaminare i passaggi finali per riunire tutti gli elementi e fare in modo che i lati sinistro e destro della visualizzazione divisa interagiscano tra loro.

<a name="The-Detail-View" />

### <a name="the-detail-view"></a>Visualizzazione dettagli

Per l'esempio dell'app Travel presentata sopra, viene definita una classe personalizzata (`AttractionViewController`) per il controller di visualizzazione standard presentato sul lato destro della visualizzazione suddivisa come visualizzazione Dettagli:

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

In questo caso, l' **attrazione** (`AttractionInformation`) viene visualizzata come proprietà e viene creato un `UpdateUI` metodo che popola i widget dell'interfaccia utente aggiunti alla visualizzazione in Interface Designer.

È stato inoltre definito un collegamento al controller di visualizzazione divisa (`SplitView`) che si utilizzerà per comunicare le modifiche alla visualizzazione tabella (`AcctractionTableView`).

Infine, le azioni personalizzate (eventi) sono state aggiunte alle tre istanze di `UIButton` create in Interface Designer, che consentono all'utente di contrassegnare un'attrazione come _preferita_, ottenere _indicazioni_ per un'attrazione e _prenotare un volo_ a una determinata città.

<a name="The-Navigation-View-Controller" />

### <a name="the-navigation-view-controller"></a>Controller di visualizzazione di navigazione

Poiché il controller di visualizzazione tabella è annidato in un controller di visualizzazione di navigazione sul lato sinistro della visualizzazione divisa, al controller di visualizzazione di spostamento è stata assegnata una classe personalizzata (`MasterNavigationController`) in Interface designer e viene definita come segue:

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

Anche in questo caso, questa classe definisce solo alcuni collegamenti per semplificare la comunicazione tra i due lati del controller di visualizzazione divisa:

- `SplitView`: un collegamento al controller di visualizzazione divisa (`MainSpiltViewController`) a cui appartiene il controller di visualizzazione di navigazione.
- `TableController`: Ottiene il controller di visualizzazione tabella (`AttractionTableViewController`) presentato come visualizzazione superiore nel controller della visualizzazione di navigazione.

<a name="The-Split-View-Controller" />

### <a name="the-split-view-controller"></a>Controller di visualizzazione divisa

Poiché il controller di visualizzazione suddivisa è la base dell'applicazione, è stata creata una classe personalizzata (`MasterSplitViewController`) per il controller in Interface designer e la si è definita come segue:

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

In primo luogo, vengono creati collegamenti al lato **Dettagli** della visualizzazione divisa (`AttractionViewController`) e al lato **principale** (`MasterNavigationController`). Anche in questo caso, questo semplifica la comunicazione tra i due lati in un secondo momento.

Successivamente, quando la visualizzazione divisa viene caricata in memoria, il controller di visualizzazione suddiviso viene collegato a entrambi i lati della visualizzazione divisa e viene risposto all'utente che evidenzia un'attrazione nella visualizzazione tabella (`AttractionHighlighted`) visualizzando la nuova attrazione nel lato **Dettagli** del Visualizzazione divisa.

Per un'implementazione completa delle visualizzazioni di tabella all'interno di una visualizzazione divisa, vedere l'app di esempio [tvTables](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-tvtable) .

## <a name="table-views-in-detail"></a>Visualizzazione tabella dettagliata

Poiché tvOS è basato su iOS, le visualizzazioni di tabella e i controller di visualizzazione tabella sono progettati e si comportano in modo simile. Per informazioni più dettagliate sull'uso della visualizzazione tabella in un'app Novell, vedere la documentazione relativa all' [uso di tabelle e celle](~/ios/user-interface/controls/tables/index.md) in iOS.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stata illustrata la progettazione e l'utilizzo di viste di tabella all'interno di un'app Xamarin.tvOS. In e è stato presentato un esempio di utilizzo di una visualizzazione tabella all'interno di una visualizzazione divisa, ovvero l'utilizzo tipico di una vista tabella in un'app tvOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
