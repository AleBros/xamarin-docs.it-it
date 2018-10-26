---
title: Controllo Menu (Force Touch) in Xamarin watchOS
description: Questo documento descrive come usare il movimento di tocco force watchOS in Xamarin. Illustra come rispondere a un dispositivo a tocco force, come aggiungere un menu di scelta e la modifica di voci di menu.
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 7696c820ab6fdf19bdef46db31061fb5914e6cf4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109757"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>Controllo Menu (Force Touch) in Xamarin watchOS

Guarda che il Kit costituisce un movimento Force Touch che attiva un menu quando viene implementato in una schermata di app watch.

![](menu-images/menu.png "Visualizzare un menu di Apple Watch")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>Risponde al tocco Force

Se un `Menu` è stata implementata per un controller di interfaccia, quando un utente esegue un Force Touch verrà visualizzato il menu di scelta. Se nessun menu è stato implementato, la schermata viene brevemente animata si verifica un'alcun'altra azione.

I tocchi force non sono associati a un determinato elemento sullo schermo; menu solo uno può essere collegato a un controller di interfaccia e verrà visualizzata indipendentemente da dove si verifica la pressione Force Touch sullo schermo.

Tra uno e quattro menu opzioni possono essere presentate.


## <a name="adding-a-menu"></a>Aggiunta di un Menu

Oggetto `Menu` deve essere aggiunto a un `InterfaceController` sullo storyboard in fase di progettazione. Quando un controllo menu viene trascinato in un controller di interfaccia non è presente alcuna indicazione visual all'anteprima dello storyboard, ma il **dal Menu** viene visualizzato nel **struttura documento** riempimento:

![](menu-images/menu-action.png "Modifica di un menu di scelta in fase di progettazione")

Fino a quattro menu è possibile aggiungere elementi al controllo menu. È possibile configurarle nel **proprietà** riempimento. È possibile impostare gli attributi seguenti:

- Titolo, e
- Immagine personalizzata, o
- Un'immagine del sistema: accetta, Aggiungi, blocco, rifiuto, Info, forse, inoltre, disattiva, sospendere, riprodurre, ripetute, Resume, condivisione, la riproduzione casuale, relatore, nel Cestino.

Creare un `Action` selezionando il **eventi** sezione del **proprietà** riempimento e digitando il nome per il metodo di azione. Verrà creato un metodo parziale nel codice, che può essere implementato nella classe di controller di interfaccia, simile al seguente:

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>Immagini personalizzate

Come per le immagini di scheda in iOS, immagini delle voci di menu richiedono un modello con un canale alfa che consente lo sfondo sia visibile attraverso opaco.

È necessario aggiungere le immagini usate per il menu di scelta per il progetto di app watch (non progetto estensione app watch) per ottenere prestazioni ottimali.


## <a name="changing-the-menu-items"></a>Modificare le voci di Menu

<!--
### Design Time Items

Menu items added the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>Aggiunta in fase di esecuzione

È possibile fare in modo un `Menu` da aggiungere a un controller di interfaccia in fase di esecuzione, anche se la raccolta di `MenuItem`s *possibile* essere modificata a livello di codice.
Usare il `AddMenuItem` metodo come illustrato:

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

L'API di Kit espressioni di controllo di xamarin. IOS richiede attualmente un `selector` per il `AdMenuItem` metodo, che deve essere dichiarata come segue:

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>Rimozione in fase di esecuzione

Il `ClearAllMenuItems` metodo può essere chiamato per rimuovere tutti *a livello di codice aggiunto* voci di menu.

Non è possibile cancellare le voci di menu configurate nello storyboard.



## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Documento di Menu di Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
