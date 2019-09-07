---
title: Controllo menu watchos (Force Touch) in Novell
description: Questo documento descrive come usare il gesto watchos Force Touch in Novell. Viene illustrato come rispondere a un Force Touch, come aggiungere un menu e modificare le voci di menu.
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: c37d8592b7aadc2c88c31826bc954abfa3c0836d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766796"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>Controllo menu watchos (Force Touch) in Novell

Il kit Watch fornisce un movimento Force Touch che attiva un menu quando viene implementato in una schermata dell'app Watch.

![](menu-images/menu.png "Apple Watch visualizzazione di un menu")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>Risposta a Force Touch

Se una `Menu` è stata implementata per un controller di interfaccia, quando un utente esegue una Force Touch il menu verrà visualizzato. Se non è stato implementato alcun menu, lo schermo viene brevemente animato e non si verifica nessun'altra azione.

I tocchi di forza non sono associati a un particolare elemento sullo schermo; è possibile collegare un solo menu a un controller di interfaccia, che verrà visualizzato indipendentemente dal punto in cui si verifica la Force Touch premere sullo schermo.

È possibile presentare tra una e quattro opzioni di menu.

## <a name="adding-a-menu"></a>Aggiunta di un menu

Un `Menu` oggetto deve essere aggiunto a `InterfaceController` un oggetto nello storyboard in fase di progettazione. Quando un controllo menu viene trascinato su un controller di interfaccia, non è presente alcuna indicazione visiva nell'anteprima dello storyboard, ma il **menu** viene visualizzato nel riquadro **struttura documento** :

![](menu-images/menu-action.png "Modifica di un menu in fase di progettazione")

Al controllo menu è possibile aggiungere fino a quattro voci di menu. Possono essere configurate nel riquadro **Proprietà** . È possibile impostare gli attributi seguenti:

- Titolo e
- Immagine personalizzata o
- Un'immagine di sistema: Accetta, Aggiungi, blocca, rifiuta, info, forse, più, disattiva, Sospendi, gioca, Ripeti, Riprendi, Condividi, shuffle, speaker, cestino.

Creare un `Action` oggetto selezionando la sezione **eventi** del riquadro **Proprietà** e digitando il nome per il metodo di azione. Nel codice verrà creato un metodo parziale, che può essere implementato nella classe controller di interfaccia, come indicato di seguito:

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>Immagini personalizzate

Analogamente alle immagini di schede in iOS, le immagini delle voci di menu richiedono un modello opaco con un canale alfa che consente di visualizzare lo sfondo.

Per ottenere prestazioni ottimali, è necessario aggiungere le immagini usate per il menu al progetto Watch app (non il progetto di estensione Watch app).

## <a name="changing-the-menu-items"></a>Modifica delle voci di menu

<!--
### Design Time Items

Menu items added the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>Aggiunta in fase di esecuzione

Non è possibile fare `Menu` in modo che un oggetto venga aggiunto a un controller di interfaccia in fase `MenuItem`di esecuzione, anche se la raccolta di oggetti *può* essere modificata a livello di codice.
Usare il `AddMenuItem` metodo come illustrato:

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

L'API del kit di controllo Novell. iOS richiede `selector` attualmente un `AdMenuItem` oggetto per il metodo, che deve essere dichiarato come segue:

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>Rimozione in fase di esecuzione

È `ClearAllMenuItems` possibile chiamare il metodo per rimuovere tutte le voci di menu *aggiunte a livello di codice* .

Non è possibile cancellare le voci di menu configurate nello storyboard.

## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Doc menu di Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
