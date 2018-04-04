---
title: Barra di ricerca
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: 4ea39f6f935adba2c035884c625c57c0039b1165
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="search-bar"></a>Barra di ricerca

Il UISearchBar viene utilizzato per cercare un elenco di valori. 

Include tre componenti principali: 

- Un campo usato per immettere testo. Gli utenti possono utilizzare questa opzione per immettere i termini di ricerca.
- Pulsante Cancella, rimuovere qualsiasi testo dal campo di ricerca.
- Un pulsante Annulla per uscire dalla funzione di ricerca.

![Barra di ricerca](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>Implementazione della barra di ricerca

Per implementare l'inizio della barra di ricerca creando uno nuovo:

```csharp
searchBar = new UISearchBar();
```

E quindi viene inserito. Nell'esempio seguente viene illustrato come attivare la barra di spostamento o HeaderView di una tabella:

```csharp
NavigationItem.TitleView = searchBar;

\\or

TableView.TableHeaderView = searchBar;
```

Impostazione delle proprietà sulla barra di ricerca:

```csharp
 searchBar = new UISearchBar(){
                Placeholder = "Enter your search Item",
                Prompt = "Search Entered here",
                ShowsScopeBar = true,
                ScopeButtonTitles = new string[]{ "Boston", "London", "SF" },
            };
```

![Proprietà barra di ricerca](searchbar-images/image6.png)

Generare il `SearchButtonClicked` evento quando viene premuto il pulsante di ricerca. Verrà richiamata la logica di ricerca:

```csharp
searchBar.SearchButtonClicked += (sender, e) => {
                Search ();
            };
```

Per informazioni sulla gestione della presentazione della barra di ricerca e i risultati di ricerca, vedere il [ricerca Controller ](https://developer.xamarin.com/recipes/ios/content_controls/search-controller/) recipe.

## <a name="using-the-search-bar-in-the-designer"></a>Utilizzando la barra di ricerca nella finestra di progettazione

La finestra di progettazione sono disponibili due opzioni per l'implementazione di una barra di ricerca nella finestra di progettazione

- Barra di ricerca
- Barra di ricerca con Controller di visualizzazione di ricerca (deprecata)

![Controlli della barra di ricerca nella finestra di progettazione](searchbar-images/image2.png)

Utilizzare il Pannello proprietà per impostare proprietà sulla barra di ricerca

![Progettazione di proprietà barra di ricerca](searchbar-images/image3.png)

Queste proprietà vengono spiegate di seguito:

- **Prompt dei comandi di testo segnaposto,** – queste proprietà vengono utilizzate per suggerire e indicare come gli utenti devono utilizzare la barra di ricerca. Se l'app viene visualizzato un elenco degli archivi, ad esempio, è possibile utilizzare la proprietà prompt dei comandi per indicare che gli utenti possono "Immettere una città, il nome di storia o codice postale"
- **Stile di ricerca** : È possibile impostare la barra di ricerca per essere **Prominent** o **minimo**. Utilizzando le principali verrà tingere tutto il resto sullo schermo, ad eccezione della barra, causando lo stato attivo su cui disegnare la barra di ricerca di ricerca. La barra di ricerca stile minima verrà blend con circostanti.
- **Funzionalità** : abilitazione di queste proprietà vengono visualizzati solo l'elemento dell'interfaccia utente. La funzionalità deve essere implementata per tali generando l'evento corretto come descritto in dettaglio nella [docs API barra di ricerca](https://developer.xamarin.com/api/type/UIKit.UISearchBar/)
    - Mostra i risultati della ricerca / pulsante segnalibri, viene visualizzata un'icona di risultati della ricerca o nei segnalibri sulla barra di ricerca
    - Mostra pulsante Annulla: consente agli utenti di uscire dalla funzione di ricerca. È consigliabile che questa opzione è selezionata.
    - Mostra barra ambito – in questo modo gli utenti limitare l'ambito della ricerca. Ad esempio, durante la ricerca nell'app musica l'utente può selezionare se si desidera effettuare la ricerca musica di Apple o la libreria per un particolare brano o l'artista. Per visualizzare diverse opzioni, aggiungere una matrice di titoli di **ScopeBarTitles** proprietà.
    ![Titoli di ambito sulla barra di ricerca](searchbar-images/image4.png)

- **Comportamento del testo** – queste opzioni vengono utilizzate per risolvere la formattazione dell'input dell'utente durante la digitazione. Uso delle maiuscole imposterà l'inizio di ogni parola o frase, ogni carattere come lettere maiuscole o. Con il controllo ortografico e la correzione richiede all'utente con suggerimenti di parole durante la digitazione.
- **Tastiera** : i controlli visualizzati lo stile della tastiera per l'input e pertanto le chiavi sono disponibili sulla tastiera. Ciò include tastierino numerico, il riempimento di telefono, posta elettronica, URL con altre opzioni.
- **Aspetto** : controlla l'aspetto e lo stile della tastiera e verranno entrambi scuro o chiaro temi.
- **Il tasto** : modificare l'etichetta per la chiave restituita per meglio riflettere l'azione che verrà eseguita. I valori supportati includono Go, Join, successivo, Route, di fatto e la ricerca.
- **Proteggere** – indica se l'input viene mascherato (quali per un input di Password).

## <a name="related-links"></a>Collegamenti correlati

- [Controller di ricerca](https://developer.xamarin.com/recipes/ios/content_controls/search-controller/)
