---
title: Creazione di un'applicazione di xamarin. IOS tramite l'API Reflection
description: Questo documento descrive il MonoTouch.Dialog basato su attributi Reflection API, che crea un'interfaccia utente basati su classi decorate con attributi.
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: c60d2a8ca58fe807dc340125d0db21b6f9ddff9f
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790510"
---
# <a name="creating-a-xamarinios-application-using-the-reflection-api"></a>Creazione di un'applicazione di xamarin. IOS tramite l'API Reflection

La voce MT. API di Reflection D consente alle classi di essere decorato con attributi che MT D viene utilizzato per creare schermate automaticamente. L'API di reflection fornisce un'associazione tra queste classi e cosa viene visualizzata sullo schermo. Sebbene questa API non offre il controllo con granularità fine che esegue gli elementi API, riduce complessità compilando automaticamente out la gerarchia degli elementi in effetti di classe basata.

 <a name="Getting_Started_with_the_Reflection_API" />


## <a name="getting-started-with-the-reflection-api"></a>Guida introduttiva con l'API di Reflection

Tramite l'API di Reflection è semplice come:

1.  Creazione di una classe decorata con MT Attributi di D.
1.  Creazione di un `BindingContext` istanza, passando un'istanza della classe precedente. 
1.  Creazione di un `DialogViewController` , passandogli il `BindingContext’s` `RootElement` . 


Esaminiamo un esempio per illustrare come utilizzare l'API di Reflection. In questo esempio verrà creata una schermata di immissione dati semplici, come illustrato di seguito:

 [![](reflection-api-walkthrough-images/01-expense-entry.png "In questo esempio creeremo una schermata di immissione di dati semplici come illustrato di seguito")](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

 <a name="Creating_a_Class_with_MT.D_Attributes" />

## <a name="creating-a-class-with-mtd-attributes"></a>Creazione di una classe con MT Attributi D

La prima cosa che è necessario utilizzare l'API di Reflection è una classe decorata con attributi. Questi attributi verranno utilizzati da principio D internamente per creare oggetti dell'API di elementi. Ad esempio, si consideri la seguente definizione di classe:

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

Il `SectionAttribute` comporterà sezioni del `UITableView` creati, con l'argomento di stringa utilizzato per popolare l'intestazione della sezione. Dopo aver dichiarata una sezione, ogni campo che segue verrà incluso in questa sezione fino a quando non viene dichiarata un'altra sezione.
Il tipo di elemento dell'interfaccia utente creato per il campo dipende dal tipo del campo e il principio Attributo D decorarla.

Ad esempio, il `Name` campo è un `string` ed è decorata con un `EntryAttribute`. Il risultato di una riga viene aggiunta alla tabella con un campo di immissione testo e la didascalia specificata. Analogamente, il `IsApproved` campo è un `bool` con un `CheckboxAttribute`, risultante in una riga della tabella con una casella di controllo a destra della cella della tabella. MT D utilizza il nome del campo, aggiungere automaticamente uno spazio, per creare la didascalia in questo caso, poiché non è specificato in un attributo.

 <a name="Adding_the_BindingContext" />


## <a name="adding-the-bindingcontext"></a>Aggiunta di BindingContext

Utilizzare il `Expense` (classe), è necessario creare un `BindingContext`. Oggetto `BindingContext` è una classe che verrà associato i dati della classe con attributi per creare la gerarchia di elementi. Per crearne uno, è sufficiente crearne un'istanza e passare un'istanza della classe attribuita al costruttore.

Ad esempio, per aggiungere un'interfaccia utente che è dichiarati con attributo la `Expense` classe, includere il codice seguente nel `FinishedLaunching` metodo il `AppDelegate`:

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

Sarà sufficiente per creare l'interfaccia utente di aggiungere il `BindingContext` per il `DialogViewController` e impostarlo come il `RootViewController` della finestra, come illustrato di seguito:

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

Esegue l'applicazione ora comporta la schermata illustrata in precedenza viene visualizzato.

 <a name="Adding_a_UINavigationController" />


### <a name="adding-a-uinavigationcontroller"></a>Aggiunta di un UINavigationController

Si noti tuttavia che il titolo "Creazione di un'attività" che è passato per il `BindingContext` non viene visualizzata. In questo modo il `DialogViewController` è non fa parte di un `UINavigatonController`. Modificare il codice per aggiungere un `UINavigationController` come la finestra `RootViewController,` e aggiungere il `DialogViewController` come radice del `UINavigationController` come illustrato di seguito:

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

Ora quando si esegue l'applicazione, il titolo viene visualizzato il `UINavigationController’s` barra di spostamento come la schermata seguente viene illustrato:

 [![](reflection-api-walkthrough-images/02-create-task.png "Quando si esegue l'applicazione, il titolo visualizzato nella barra di spostamento UINavigationControllers")](reflection-api-walkthrough-images/02-create-task.png#lightbox)

Includendo un `UINavigationController`, è possibile usufruire di altre funzionalità di ad D per il quale è necessaria navigazione. Ad esempio, è possibile aggiungere un'enumerazione per la `Expense` classe per definire la categoria per la spesa e MT D creerà automaticamente una schermata di selezione. Per dimostrare, modificare il `Expense` classe per includere un `ExpenseCategory` campo come indicato di seguito:

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

Esegue l'applicazione ora produce una nuova riga nella tabella per la categoria come illustrato:

 [![](reflection-api-walkthrough-images/03-set-details.png "Esegue l'applicazione ora comporta una nuova riga nella tabella per la categoria come mostrato")](reflection-api-walkthrough-images/03-set-details.png#lightbox)

Selezionando la riga determina l'applicazione spostarsi in una nuova schermata con le righe corrispondenti in un'enumerazione, come illustrato di seguito:

 [![](reflection-api-walkthrough-images/04-set-category.png "La riga di risultati nell'applicazione passando a una nuova schermata con le righe corrispondenti all'enumerazione")](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary" />


## <a name="summary"></a>Riepilogo

In questo articolo è visualizzata una procedura dettagliata dell'API di Reflection. È stato illustrato come aggiungere attributi a una classe per controllare cosa viene visualizzata. È stato descritto come utilizzare un `BindingContext` per associare i dati da una classe per la gerarchia degli elementi creati, nonché come utilizzare MT D con un `UINavigationController`.


## <a name="related-links"></a>Collegamenti correlati

- [MTDReflectionWalkthrough (esempio)](https://developer.xamarin.com/samples/MTDReflectionWalkthrough/)
- [Screencast - Miguel de Icaza crea una schermata di accesso di iOS con MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Screencast - creare con facilità iOS interfacce utente con MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introduzione alla finestra di dialogo MonoTouch](~/ios/user-interface/monotouch.dialog/index.md)
- [Procedura dettagliata di API di elementi](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Procedura dettagliata elemento JSON](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Finestra di dialogo MonoTouch su Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Applicazione TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Riferimento alla classe UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Riferimento alla classe UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
