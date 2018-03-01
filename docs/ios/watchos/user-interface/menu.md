---
title: Controllo menu (Force tocco)
ms.topic: article
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 87ab9bdf6af68448649d1b3e518743a73bb23fe7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="menu-control-force-touch"></a>Controllo menu (Force tocco)

Espressioni di controllo Kit fornisce un movimento Force tocco che attiva un menu quando viene implementato in una schermata di app di espressioni di controllo.

![](menu-images/menu.png "Apple Watch che mostra un menu")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>Risponde a Force tocco

Se un `Menu` è stata implementata per un controller di interfaccia, quando un utente esegue un tocco Force sarà visualizzato il menu. Se non è stato implementato alcun menu, la schermata viene animata brevemente si verifica un'alcuna altra azione.

Force ritocchi non sono associati a un determinato elemento sullo schermo. può essere collegato solo a un menu a un controller di interfaccia e viene visualizzato indipendentemente dalla posizione premere Force tocco sullo schermo.

Tra uno e quattro menu opzioni possono essere presentate.


## <a name="adding-a-menu"></a>Aggiunta di un Menu

Oggetto `Menu` deve essere aggiunto a un `InterfaceController` nello storyboard in fase di progettazione. Quando un controllo menu viene trascinato da un controller di interfaccia non sono disponibili indicatori visivi sull'anteprima della storyboard ma la **Menu** presenti il **struttura documento** riempimento:

![](menu-images/menu-action.png "Modifica di un menu in fase di progettazione")

Fino a quattro menu è possano aggiungere elementi al controllo menu. È possibile configurarle nel **proprietà** riempimento. È possibile impostare gli attributi seguenti:

- Titolo, e
- Immagine personalizzata, o
- Un'immagine del sistema: accettare, Add, blocco, Rifiuta, Info, forse, più, disattivare, sospendere, riprodurre, ripetere, Resume, condivisione, casuale, altoparlante, Cestino.

Creare un `Action` selezionando il **eventi** sezione la **proprietà** riempimento e digitando il nome per il metodo di azione. Verrà creato un metodo parziale nel codice, che può essere implementato nella classe di controller di interfaccia, simile al seguente:

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>Immagini personalizzate

Analogamente alle immagini di scheda in iOS, le immagini di voce di menu richiedono un modello opaco con un canale alfa che consente il colore di sfondo tramite.

È necessario aggiungere le icone utilizzate per il menu di progetto di applicazione di espressioni di controllo (non il controllo app progetto di estensione) per ottenere prestazioni ottimali.


## <a name="changing-the-menu-items"></a>Modifica le voci di Menu

<!--
### Design Time Items

Menu items added the the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>Aggiunta in fase di esecuzione

È possibile causare un `Menu` da aggiungere a un controller di interfaccia in fase di esecuzione, anche se la raccolta di `MenuItem`s *può* essere modificato a livello di codice.
Utilizzare il `AddMenuItem` metodo come illustrato:

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

L'API di Kit xamarin espressioni di controllo attualmente richiede un `selector` per il `AdMenuItem` metodo, che deve essere dichiarata come segue:

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>Rimozione in fase di esecuzione

Il `ClearAllMenuItems` metodo può essere chiamato per rimuovere tutti *a livello di codice aggiunto* voci di menu.

Non è possibile eliminare le voci di menu configurate nello storyboard.



## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Documento dal Menu di Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
