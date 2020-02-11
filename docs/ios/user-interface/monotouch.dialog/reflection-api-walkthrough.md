---
title: Creazione di un'applicazione Xamarin.iOS tramite l'API Reflection
description: Questo documento descrive l'API di Reflection basata sugli attributi MonoTouch. Dialog, che crea l'interfaccia utente in base alle classi decorate con attributi.
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: 323b92190dc3ea18bc78871f5c19e51d0a6ea94e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002213"
---
# <a name="creating-a-xamarinios-application-using-the-reflection-api"></a>Creazione di un'applicazione Xamarin.iOS tramite l'API Reflection

MT. L'API di Reflection D consente di decorare le classi con attributi che MT. D utilizza per creare automaticamente le schermate. L'API Reflection fornisce un'associazione tra queste classi e ciò che viene visualizzato sullo schermo. Sebbene questa API non fornisca il controllo granulare svolto dall'API degli elementi, riduce la complessità compilando automaticamente la gerarchia di elementi in base alla decorazione della classe.

## <a name="setting-up-mtd"></a>Configurazione di MT. D

Mt. D viene distribuito con Xamarin.iOS. Per usarlo, fare clic con il pulsante destro del mouse sul nodo **riferimenti** di un progetto Xamarin.iOS in Visual Studio 2017 o Visual Studio per Mac e aggiungere un riferimento all'assembly **MonoTouch. Dialog-1** . Aggiungere quindi `using MonoTouch.Dialog` istruzioni nel codice sorgente in base alle esigenze.

## <a name="getting-started-with-the-reflection-api"></a>Introduzione all'API di Reflection

L'uso dell'API di Reflection è semplice quanto segue:

1. Creazione di una classe decorata con MT. Attributi D.
1. Creazione di un'istanza di `BindingContext`, passando un'istanza della classe precedente. 
1. Creazione di un `DialogViewController`, passandogli il `RootElement` `BindingContext’s`. 

Viene ora esaminato un esempio per illustrare come usare l'API di Reflection. In questo esempio verrà creata una schermata di immissione dati semplice, come illustrato di seguito:

 [![](reflection-api-walkthrough-images/01-expense-entry.png "In this example, we'll build a simple data entry screen as shown here")](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

## <a name="creating-a-class-with-mtd-attributes"></a>Creazione di una classe con MT. Attributi D

Per prima cosa è necessario usare l'API di Reflection è una classe decorata con attributi. Questi attributi verranno usati da MT. D internamente per creare oggetti dall'API Elements. Si consideri, ad esempio, la definizione di classe seguente:

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

Il `SectionAttribute` comporterà la creazione di sezioni del `UITableView`, con l'argomento stringa utilizzato per popolare l'intestazione della sezione. Una volta dichiarata una sezione, ogni campo che lo segue verrà incluso in tale sezione, fino a quando non viene dichiarata un'altra sezione.
Il tipo di elemento dell'interfaccia utente creato per il campo dipenderà dal tipo e dal MT del campo. Attributo D che lo decora.

Ad esempio, il campo `Name` è un `string` ed è decorato con un `EntryAttribute`. Ciò comporta l'aggiunta di una riga alla tabella con un campo di immissione di testo e la didascalia specificata. Analogamente, il campo `IsApproved` è un `bool` con una `CheckboxAttribute`, ottenendo una riga di tabella con una casella di controllo a destra della cella della tabella. Mt. D usa il nome del campo, aggiungendo automaticamente uno spazio, per creare la didascalia in questo caso, poiché non è specificato in un attributo.

## <a name="adding-the-bindingcontext"></a>Aggiunta di BindingContext

Per usare la classe `Expense`, è necessario creare una `BindingContext`. Un `BindingContext` è una classe che associa i dati della classe con attributi per creare la gerarchia di elementi. Per crearne uno, è sufficiente crearne un'istanza e passare un'istanza della classe con attributi al costruttore.

Ad esempio, per aggiungere un'interfaccia utente dichiarata usando l'attributo nella classe `Expense`, includere il codice seguente nel metodo `FinishedLaunching` della `AppDelegate`:

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

Quindi, per creare l'interfaccia utente, è sufficiente aggiungere il `BindingContext` al `DialogViewController` e impostarlo come `RootViewController` della finestra, come illustrato di seguito:

```csharp
UIWindow window;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
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

Se si esegue l'applicazione, viene visualizzato lo schermo sopra riportato.

### <a name="adding-a-uinavigationcontroller"></a>Aggiunta di un UINavigationController

Si noti tuttavia che il titolo "crea un'attività" passata al `BindingContext` non viene visualizzato. Questo perché il `DialogViewController` non fa parte di un `UINavigatonController`. Modificare il codice per aggiungere un `UINavigationController` come `RootViewController,` della finestra e aggiungere il `DialogViewController` come radice del `UINavigationController`, come illustrato di seguito:

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

A questo punto, quando si esegue l'applicazione, il titolo viene visualizzato nella barra di spostamento `UINavigationController’s`, come illustrato nella schermata seguente:

 [![](reflection-api-walkthrough-images/02-create-task.png "Now when we run the application, the title appears in the UINavigationControllers navigation bar")](reflection-api-walkthrough-images/02-create-task.png#lightbox)

Includendo una `UINavigationController`, è ora possibile sfruttare le altre funzionalità di MT. D per cui è necessaria la navigazione. Ad esempio, è possibile aggiungere un'enumerazione alla classe `Expense` per definire la categoria per la spesa e MT. D creerà automaticamente una schermata di selezione. Per illustrare, modificare la classe `Expense` per includere un campo di `ExpenseCategory` come segue:

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

L'esecuzione dell'applicazione ora produce una nuova riga nella tabella per la categoria, come illustrato:

 [![](reflection-api-walkthrough-images/03-set-details.png "Running the application now results in a new row in the table for the category as shown")](reflection-api-walkthrough-images/03-set-details.png#lightbox)

Selezionando la riga, l'applicazione passa a una nuova schermata con righe corrispondenti all'enumerazione, come illustrato di seguito:

 [![](reflection-api-walkthrough-images/04-set-category.png "Selecting the row results in the application navigating to a new screen with rows corresponding to the enumeration")](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha presentato una procedura dettagliata dell'API Reflection. È stato illustrato come aggiungere attributi a una classe per controllare ciò che viene visualizzato. È stato inoltre illustrato come utilizzare un `BindingContext` per associare i dati di una classe alla gerarchia di elementi creata, nonché come utilizzare MT. D con un `UINavigationController`.

## <a name="related-links"></a>Collegamenti correlati

- [MTDReflectionWalkthrough (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdreflectionwalkthrough)
- [Introduzione alla finestra di dialogo MonoTouch](~/ios/user-interface/monotouch.dialog/index.md)
- [Procedura dettagliata per l'API elementi](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Procedura dettagliata sull'elemento JSON](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Finestra di dialogo MonoTouch su GitHub](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Applicazione TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Riferimento alla classe UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Riferimento alla classe UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
