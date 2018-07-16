---
title: Creazione di un'applicazione xamarin. IOS mediante l'API Reflection
description: Questo documento descrive il monotouch. Dialog basato su attributi API Reflection, che crea un'interfaccia utente basati su classi decorate con attributi.
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: a1b77f46410ef20892485a866221bab2c872e54c
ms.sourcegitcommit: cb80df345795989528e9df78eea8a5b45d45f308
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39038472"
---
# <a name="creating-a-xamarinios-application-using-the-reflection-api"></a>Creazione di un'applicazione xamarin. IOS mediante l'API Reflection

Di MT. API Reflection D consente alle classi di essere decorato con attributi di tale MT. D viene utilizzato per creare automaticamente le schermate. L'API di reflection fornisce un'associazione tra queste classi e gli elementi visualizzati sullo schermo. Sebbene questa API non offre il controllo con granularità fine che esegue gli elementi API, riduce complessità creando automaticamente out la gerarchia degli elementi basata decorazione di classe.

## <a name="setting-up-mtd"></a>Configurazione di MT. 1!D

MT. 1!d viene distribuito con xamarin. IOS. Per usarlo, fare clic sui **riferimenti** nodo di xamarin. IOS del progetto in Visual Studio 2017 o Visual Studio per Mac e aggiungere un riferimento al **monotouch. Dialog-1** assembly. Aggiungere quindi `using MonoTouch.Dialog` istruzioni presenti nell'origine del codice in base alle esigenze.

## <a name="getting-started-with-the-reflection-api"></a>Introduzione all'API di Reflection

Tramite l'API di Reflection è semplice come:

1.  Creazione di una classe decorata con MT. Attributi D.
1.  Creazione di un `BindingContext` istanza, passando un'istanza della classe precedente. 
1.  Creazione di un `DialogViewController` , passando il `BindingContext’s` `RootElement` . 


Esaminiamo un esempio per illustrare come usare l'API di Reflection. In questo esempio, verrà compilata una schermata di immissione dati semplici, come illustrato di seguito:

 [![](reflection-api-walkthrough-images/01-expense-entry.png "In questo esempio viene creata una schermata di immissione di dati semplici come illustrato di seguito")](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

## <a name="creating-a-class-with-mtd-attributes"></a>Creazione di una classe con MT. Attributi D

La prima cosa che è necessario usare l'API di Reflection è una classe decorata con attributi. Questi attributi verranno utilizzati da MT. 1!d internamente per creare gli oggetti dall'API di elementi. Ad esempio, si consideri la seguente definizione di classe:

```csharp
public class Expense
{
        [Section("Expense Entry")]

        [Entry("Enter expense name")]
        public string Name;
        
        [Section("Expense Details")]
  
        [Caption("Description")]
        [Entry]
        public string Details;
        
        [Checkbox]
        public bool IsApproved = true;
}
```

Il `SectionAttribute` comporterà sezioni del `UITableView` creati, con l'argomento di stringa utilizzato per popolare l'intestazione della sezione. Dopo aver dichiarata una sezione, ogni campo dopo di esso verrà inclusi in tale sezione fino a quando non viene dichiarata un'altra sezione.
Il tipo di elemento dell'interfaccia utente creato per il campo verrà dipendono dal tipo del campo e di MT. Aggiunta dell'attributo di D.

Ad esempio, il `Name` campo è un `string` e si è decorato con un `EntryAttribute`. Ciò comporta una riga che viene aggiunta alla tabella con un campo di immissione testo e la didascalia specificata. Analogamente, il `IsApproved` campo è un `bool` con un `CheckboxAttribute`, risultante in una riga della tabella con una casella di controllo a destra della cella della tabella. MT. 1!d usa il nome del campo, aggiungendo automaticamente uno spazio, per creare in questo caso, la didascalia, poiché non è specificato in un attributo.

## <a name="adding-the-bindingcontext"></a>Aggiunta di BindingContext

Usare la `Expense` (classe), è necessario creare un `BindingContext`. Oggetto `BindingContext` è una classe che verrà associato i dati dalla classe con attributa per creare una gerarchia di elementi. Per crearne uno, è sufficiente crearne un'istanza e passare un'istanza della classe con attributa al costruttore.

Ad esempio, per aggiungere l'interfaccia utente che viene dichiarate utilizzando l'attributo nel `Expense` classe, includere il codice seguente nel `FinishedLaunching` metodo del `AppDelegate`:

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

Non ci resta da eseguire per creare l'interfaccia utente è aggiungere il `BindingContext` per il `DialogViewController` e impostarlo come il `RootViewController` della finestra, come illustrato di seguito:

```csharp
UIWindow window;

public override bool FinishedLaunching (UIApplication app, 
        NSDictionary options)
{
   
        window = new UIWindow (UIScreen.MainScreen.Bounds);
            
        var expense = new Expense ();
        var bctx = new BindingContext (null, expense, "Create a task");
        var dvc = new DialogViewController (bctx.Root);
            
        window.RootViewController = dvc;
        window.MakeKeyAndVisible ();
            
        return true;
}
```

Eseguire ora l'applicazione comporta la schermata illustrata sopra la visualizzazione.

### <a name="adding-a-uinavigationcontroller"></a>Aggiunta di un UINavigationController

Si noti tuttavia che il titolo "crea un'attività" che viene passato al `BindingContext` non viene visualizzata. Infatti, il `DialogViewController` è non fa parte di un `UINavigatonController`. Modifichiamo il codice per aggiungere un `UINavigationController` della finestra `RootViewController,` e aggiungere il `DialogViewController` come radice del `UINavigationController` come illustrato di seguito:

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

A questo punto quando si esegue l'applicazione, il titolo viene visualizzato il `UINavigationController’s` barra di spostamento come lo screenshot seguente mostra:

 [![](reflection-api-walkthrough-images/02-create-task.png "A questo punto quando si esegue l'applicazione, il titolo viene visualizzato nella barra di spostamento UINavigationControllers")](reflection-api-walkthrough-images/02-create-task.png#lightbox)

Includendo un `UINavigationController`, ora possiamo usufruire dei vantaggi di altre caratteristiche di MT. Per il quale è necessaria navigazione 3!d. Ad esempio, è possibile aggiungere un'enumerazione per il `Expense` classe per definire la categoria per la spesa e MT. 1!d creerà automaticamente una schermata di selezione. Per una dimostrazione, modificare il `Expense` classe per includere un `ExpenseCategory` campo come indicato di seguito:

```csharp
public enum Category
{
        Travel,
        Lodging,
        Books
}
        
public class Expense
{
        …

        [Caption("Category")]
        public Category ExpenseCategory;
}
```

Esecuzione dell'applicazione ora restituisce una nuova riga nella tabella per la categoria come illustrato:

 [![](reflection-api-walkthrough-images/03-set-details.png "Eseguire ora l'applicazione come illustrato comporterà una nuova riga nella tabella per la categoria")](reflection-api-walkthrough-images/03-set-details.png#lightbox)

Se si seleziona la riga determina l'applicazione passare a una nuova schermata con le righe corrispondenti in un'enumerazione, come illustrato di seguito:

 [![](reflection-api-walkthrough-images/04-set-category.png "Se si seleziona la riga comporta il passaggio dell'applicazione a una nuova schermata con le righe corrispondenti all'enumerazione")](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary" />


## <a name="summary"></a>Riepilogo

Questo articolo è presentata una procedura dettagliata dell'API di Reflection. È stato illustrato come aggiungere attributi a una classe per controllare gli elementi visualizzati. Viene anche illustrato come usare un `BindingContext` associare i dati da una classe per la gerarchia degli elementi che viene creata, nonché come usare MT. 1!d con un `UINavigationController`.


## <a name="related-links"></a>Collegamenti correlati

- [MTDReflectionWalkthrough (esempio)](https://developer.xamarin.com/samples/MTDReflectionWalkthrough/)
- [Screencast - Miguel de Icaza consente di creare una schermata di accesso di iOS con monotouch. Dialog](http://youtu.be/3butqB1EG0c)
- [Screencast - facilmente creare interfacce utente iOS con monotouch. Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introduzione alla finestra di dialogo MonoTouch](~/ios/user-interface/monotouch.dialog/index.md)
- [Procedura dettagliata sull'API di elementi](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Procedura dettagliata elemento JSON](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Finestra di dialogo di MonoTouch su Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Applicazione TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Riferimento alla classe UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Riferimento alla classe UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
