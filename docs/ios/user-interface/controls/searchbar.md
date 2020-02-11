---
title: Barre di ricerca in Xamarin.iOS
description: Questo documento descrive come usare le barre di ricerca in Xamarin.iOS. Viene illustrato come creare barre di ricerca a livello di codice e in uno storyboard.
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2017
ms.openlocfilehash: 8b129e0e70bf3ded787094d1b1f740e73a8cbca1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021975"
---
# <a name="search-bars-in-xamarinios"></a>Barre di ricerca in Xamarin.iOS

UISearchBar viene usato per eseguire la ricerca in un elenco di valori.

Contiene tre componenti principali:

- Campo utilizzato per immettere il testo. Questo può essere utilizzato dagli utenti per immettere il termine di ricerca.
- Pulsante Cancella per rimuovere il testo dal campo di ricerca.
- Pulsante Annulla per uscire dalla funzione search.

![Barra di ricerca](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>Implementazione della barra di ricerca

Per implementare la barra di ricerca iniziare creando un'istanza di un nuovo:

```csharp
searchBar = new UISearchBar();
```

E quindi posizionarlo. Nell'esempio seguente viene illustrato come inserirlo nella barra di spostamento o nel HeaderView di una tabella:

```csharp
NavigationItem.TitleView = searchBar;

// or

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

Genera l'evento `SearchButtonClicked` quando viene premuto il pulsante Cerca. Verrà chiamata la logica di ricerca:

```csharp
searchBar.SearchButtonClicked += (sender, e) => {
                Search ();
            };
```

Per informazioni sulla gestione della presentazione della barra di ricerca e dei risultati della ricerca, vedere la ricetta del [controller](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller) di ricerca.

## <a name="using-the-search-bar-in-the-designer"></a>Uso della barra di ricerca nella finestra di progettazione

La finestra di progettazione offre due opzioni per implementare una barra di ricerca nella finestra di progettazione

- Barra di ricerca
- Barra di ricerca con il controller di visualizzazione della ricerca (deprecato)

![Controlli della barra di ricerca nella finestra di progettazione](searchbar-images/image2.png)

Utilizzare il pannello Proprietà per impostare le proprietà nella barra di ricerca

![Finestra di progettazione proprietà barra di ricerca](searchbar-images/image3.png)

Queste proprietà sono illustrate di seguito:

- **Testo, segnaposto, prompt** : queste proprietà vengono usate per suggerire e indicare in che modo gli utenti devono usare la barra di ricerca. Ad esempio, se l'app Visualizza un elenco di archivi, è possibile usare la proprietà Richiedi per consigliare agli utenti di immettere una città, un nome di storia o un codice postale.
- **Stile di ricerca** : è possibile impostare la barra di ricerca in modo che sia **prominente** o **minima**. Se si usa il primo piano, tutti gli altri elementi vengono tinti sullo schermo, ad eccezione della barra di ricerca, causando la traccia dello stato attivo sulla barra di ricerca. La barra di ricerca con stile minimo si mescolerà tra loro intorno.
- **Funzionalità** : l'abilitazione di queste proprietà Visualizza solo l'elemento dell'interfaccia utente. La funzionalità deve essere implementata per questi generando l'evento corretto come descritto in dettaglio nella [documentazione API della barra di ricerca](xref:UIKit.UISearchBar)
  - Pulsante Cerca risultati/segnalibri-Mostra i risultati della ricerca o l'icona dei segnalibri nella barra di ricerca
  - Mostra pulsante Annulla: consente agli utenti di uscire dalla funzione di ricerca. È consigliabile selezionare questa opzione.
  - Mostra la barra dell'ambito: consente agli utenti di limitare l'ambito della ricerca. Ad esempio, quando si esegue la ricerca nell'app Music, l'utente può selezionare se desidera eseguire la ricerca Apple Music o la relativa libreria per un brano o un artista specifico. Per visualizzare varie opzioni, aggiungere una matrice di titoli alla proprietà **ScopeBarTitles** .
  ![i titoli dell'ambito della barra di ricerca](searchbar-images/image4.png)

- **Comportamento del testo** : queste opzioni vengono usate per risolvere il modo in cui l'input dell'utente viene formattato durante la digitazione. Con l'uso di maiuscole, l'inizio di ogni parola o frase viene impostata in lettere maiuscole o ogni carattere. Correzione e controllo ortografico con la richiesta all'utente di ortografia suggerite delle parole durante la digitazione.
- **Tastiera** : controlla lo stile della tastiera visualizzato per l'input e quindi le chiavi disponibili sulla tastiera. Sono inclusi il tastierino numerico, il tastierino telefonico, la posta elettronica e l'URL insieme ad altre opzioni.
- **Aspetto** : controlla lo stile di aspetto della tastiera e sarà scuro o chiaro.
- **Chiave restituita** : modificare l'etichetta della chiave restituita in modo da riflettere meglio l'azione che verrà eseguita. I valori supportati includono go, join, Next, Route, done e search.
- **Secure** : indica se l'input è mascherato, ad esempio per un input di password.

## <a name="related-links"></a>Collegamenti correlati

- [Controller di ricerca](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
