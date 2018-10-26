---
title: Le barre di ricerca in xamarin. IOS
description: Questo documento descrive come usare le barre di ricerca in xamarin. IOS. Illustra come creare le barre di ricerca a livello di codice e in uno storyboard.
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2017
ms.openlocfilehash: 923eebc2674abdbbd66d9db11bebf9f503928569
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107326"
---
# <a name="search-bars-in-xamarinios"></a>Le barre di ricerca in xamarin. IOS

Il UISearchBar viene utilizzato per eseguire ricerche in un elenco di valori. 

Contiene tre componenti principali: 

- Un campo usato per immettere testo. Gli utenti possono utilizzare questa opzione per immettere i termini di ricerca.
- Pulsante cancellato, rimuovere qualsiasi testo dal campo di ricerca.
- Un pulsante Annulla per uscire dalla funzione di ricerca.

![Barra di ricerca](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>Implementazione della barra di ricerca

Per implementare l'inizio barra di ricerca creando una nuova:

```csharp
searchBar = new UISearchBar();
```

E posizionarlo. L'esempio seguente viene illustrato come posizionarlo nella barra di spostamento o in HeaderView di una tabella:

```csharp
NavigationItem.TitleView = searchBar;

\\or

TableView.TableHeaderView = searchBar;
```

Impostazione delle proprietà nella barra di ricerca:

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

Per informazioni sulla gestione della presentazione della barra di ricerca e i risultati della ricerca, vedere la [ricerca Controller ](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller) recipe.

## <a name="using-the-search-bar-in-the-designer"></a>Tramite la barra di ricerca nella finestra di progettazione

La finestra di progettazione offre due opzioni per l'implementazione di una barra di ricerca nella finestra di progettazione

- Barra di ricerca
- Barra di ricerca con il Controller di visualizzazione di ricerca (deprecato)

![Controlli barra di ricerca nella finestra di progettazione](searchbar-images/image2.png)

Utilizzare il pannello delle proprietà per impostare proprietà sulla barra di ricerca

![Progettazione delle proprietà barra di ricerca](searchbar-images/image3.png)

Queste proprietà vengono spiegate di seguito:

- **Testo segnaposto, prompt dei comandi** – queste proprietà vengono usate per suggerire e indicare come gli utenti devono usare la barra di ricerca. Se l'app è visualizzato un elenco degli archivi, ad esempio, è possibile utilizzare la proprietà prompt per indicare che gli utenti possono "Immettere una città, nome del brano o codice postale"
- **Stile di visualizzazione di ricerca** – è possibile impostare la barra di ricerca per essere **Prominent** oppure **minimo**. Usando il notificate all'utente verrà tingere tutto il resto sullo schermo, fatta eccezione per la ricerca della barra, causando lo stato attivo su cui disegnare la barra di ricerca. La barra di ricerca di stile minimo si mescoleranno con adatto al contesto.
- **Funzionalità** : abilitazione di queste proprietà vengono visualizzate solo l'elemento dell'interfaccia utente. La funzionalità deve essere implementata per questi generando l'evento corretto come illustrato in dettaglio nella [documentazione delle API di barra di ricerca](https://developer.xamarin.com/api/type/UIKit.UISearchBar/)
    - Mostra i risultati della ricerca / pulsante dei segnalibri – Mostra un'icona di segnalibri o i risultati della ricerca nella barra di ricerca
    - Mostra pulsante Annulla: consente agli utenti di uscire dalla funzione di ricerca. È consigliabile che questa opzione è selezionata.
    - Mostra barra ambito – ciò consente agli utenti di limitare l'ambito della ricerca. Ad esempio, durante la ricerca nell'app music l'utente può selezionare se si desidera cercare la libreria per un brano musicale particolare o di un artista o Apple Music. Per visualizzare diverse opzioni, aggiungere una matrice di titoli per il **ScopeBarTitles** proprietà.
    ![Titoli di ambito sulla barra di ricerca](searchbar-images/image4.png)

- **Comportamento del testo** : queste opzioni vengono utilizzate per risolvere la formattazione dell'input utente durante la digitazione. L'uso delle maiuscole imposterà l'inizio di ogni parola o frase, ogni carattere come lettere maiuscole o. Correzione e ortografico con chiedere all'utente di forme suggerite parole digitazione.
- **Tastiera** : controlli lo stile della tastiera visualizzata per l'input e pertanto le chiavi sono disponibili sulla tastiera. Oltre ad altre opzioni sono inclusi tastierino numerico, tastierino telefonico, indirizzo di posta elettronica, URL.
- **Aspetto** : controlla l'aspetto e lo stile della tastiera e verranno entrambi scuro o con tema chiaro di.
- **Il tasto** : modificare l'etichetta per la chiave Return per riflettere meglio l'azione che verrà eseguita. I valori supportati includono Go, Join, Next, Route, operazione eseguita e ricerca.
- **Proteggere** : indica se l'input viene mascherato (ad esempio un input di Password).

## <a name="related-links"></a>Collegamenti correlati

- [Controller di ricerca](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
