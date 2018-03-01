---
title: "Testing unità"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 0842ce24139da5d963e2c528b440e6592d5910f8
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="unit-testing"></a>Testing unità

App per dispositivi mobili prevedono problemi univoci che desktop e applicazioni basate sul web non sono necessario preoccuparsi. Gli utenti mobili variano dai dispositivi che usano, alla connettività di rete, la disponibilità dei servizi e un intervallo di altri fattori. Pertanto, è necessario testare App per dispositivi mobili che verranno utilizzate nel mondo reale, per migliorare la qualità, affidabilità e prestazioni. Esistono molti tipi di test che devono essere eseguiti in un'app, tra cui unit test, test di integrazione e test, con gli unit test da una forma più comune di test dell'interfaccia utente.

Uno unit test accetta una piccola unità dell'app, in genere un metodo, consente di isolare dal resto del codice e consente di verificare che tutto funzioni come previsto. L'obiettivo consiste nel verificare che ogni unità funzionale eseguita come previsto, in modo che gli errori non vengano propagate nell'intera app. Rilevamento di un bug in cui si verifica è più efficiente che osservare l'effetto di un bug indirettamente a un punto di errore secondario.

Unit test ha l'effetto maggiore sulle qualità del codice quando è parte integrante del flusso di lavoro di sviluppo software. Non appena è stato scritto un metodo, devono essere scritti gli unit test per verificare il comportamento del metodo in risposta a casi standard, limite e non corretti di dati di input e che il controllo supposizioni implicite o esplicite nel codice. In alternativa, con sviluppo basato su test, scrittura di unit test prima del codice. In questo scenario, unit test agiscono come documentazione di progettazione sia come specifiche funzionali.

> [!NOTE]
> Unit test sono molto efficace nel caso di regressione, vale a dire, la funzionalità che consentono di utilizzare, ma è stata distribuita da un aggiornamento non corretto.

Gli unit test in genere utilizzano il modello arrange-act-assert:

-   Il *disponi* sezione del metodo di unit test Inizializza oggetti e imposta il valore dei dati che viene passati al metodo sottoposto a test.
-   Il *agire* sezione richiama il metodo sottoposto a test con gli argomenti obbligatori.
-   Il *assert* sezione verifica che l'azione del metodo da testare si comporti come previsto.

Seguendo questo modello garantisce che gli unit test sono leggibili e coerente.

## <a name="dependency-injection-and-unit-testing"></a>Inserimento di dipendenze e gli Unit test

Uno dei motivi per l'adozione di un'architettura a regime è che facilita l'esecuzione di unit test. Uno dei tipi registrati con Autofac è la `OrderService` classe. Esempio di codice seguente viene illustrata una struttura di questa classe:

```csharp
public class OrderDetailViewModel : ViewModelBase  
{  
    private IOrderService _ordersService;  

    public OrderDetailViewModel(IOrderService ordersService)  
    {  
        _ordersService = ordersService;  
    }  
    ...  
}
```

Il `OrderDetailViewModel` classe ha una dipendenza di `IOrderService` digitare che consente di risolvere il contenitore quando crea un'istanza di un `OrderDetailViewModel` oggetto. Tuttavia, anziché creare un `OrderService` oggetto allo unit test di `OrderDetailViewModel` (classe), invece, sostituire il `OrderService` oggetto con una simulazione allo scopo di test. Nella figura 10-1 viene illustrata questa relazione.

![](unit-testing-images/unittesting.png "Classi che implementano l'interfaccia IOrderService")

**Figura 10-1:** classi che implementano l'interfaccia IOrderService

Questo approccio consente la `OrderService` oggetto deve essere passato nel `OrderDetailViewModel` classe in fase di esecuzione e ai fini di testabilità, consente di `OrderMockService` classe deve essere passato nel `OrderDetailViewModel` classe in fase di test. Il vantaggio principale di questo approccio è che consente di unit test da eseguire senza difficile da gestire risorse, ad esempio servizi web o database.

## <a name="testing-mvvm-applications"></a>Test delle applicazioni MVVM

Testing di modelli e visualizzazione di modelli da applicazioni MVVM è identico per tutte le altre classi di test e gli stessi strumenti e tecniche, ad esempio unit test e tali, possono essere utilizzate. Tuttavia, esistono alcuni modelli tipici di modello e le classi di modello di visualizzazione, che possono trarre vantaggio dalle tecniche di test unità specifica.

> [!TIP]
> Testare una cosa con ogni unit test. Non può essere tentati di far sì che un'unità di esercizio più di un aspetto del comportamento dell'unità. Questa operazione comporta i test che sono difficili da leggere e aggiornare. Inoltre può generare confusione durante l'interpretazione di un errore.

Gli utilizzi di app per dispositivi mobili eShopOnContainers [xUnit](https://xunit.github.io/) per eseguire unit test, che supporta due tipi diversi di unit test:

-   I fact sono i test che sono sempre true, il test di condizioni non variabili associate.
-   Teorie sono test che sono solo true per un particolare set di dati.

Gli unit test inclusi con l'app mobile eShopOnContainers sono fatti test e quindi ogni metodo di unit test è decorata con il `[Fact]` attributo.

> [!NOTE]
> xUnit test vengono eseguiti da un test runner. Per eseguire il test runner, eseguire il progetto eShopOnContainers.TestRunner per la piattaforma richiesta.

### <a name="testing-asynchronous-functionality"></a>Test della funzionalità asincrona

Quando si implementa il modello MVVM, visualizzazione di modelli in genere richiama le operazioni sui servizi, spesso in modo asincrono. Test per il codice che richiama le operazioni in genere utilizzare mocks come sostituzioni per i servizi effettivamente. Esempio di codice riportato di seguito viene illustrato come test della funzionalità asincrona passando un servizio fittizio in un modello di visualizzazione:

```csharp
[Fact]  
public async Task OrderPropertyIsNotNullAfterViewModelInitializationTest()  
{  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.NotNull(orderViewModel.Order);  
}
```

Questo unit test verifica che il `Order` proprietà del `OrderDetailViewModel` istanza avrà un valore dopo il `InitializeAsync` è stato richiamato metodo. Il `InitializeAsync` metodo viene richiamato quando si accede vista corrispondente del modello di visualizzazione. Per ulteriori informazioni sulla navigazione, vedere [navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md).

Quando il `OrderDetailViewModel` viene creata l'istanza, è previsto che un `OrderService` istanza specificare come argomento. Tuttavia, il `OrderService` recupera dati da un servizio web. Pertanto, un `OrderMockService` istanza, che è una versione fittizia del `OrderService` classe, viene specificato come argomento per il `OrderDetailViewModel` costruttore. Quindi, quando il modello di visualizzazione `InitializeAsync` metodo viene richiamato, che richiama `IOrderService` dati fittizi operazioni, viene recuperato, piuttosto che la comunicazione con un servizio web.

### <a name="testing-inotifypropertychanged-implementations"></a>Implementazioni di testing INotifyPropertyChanged

Implementazione di `INotifyPropertyChanged` interfaccia consente di rispondere alle modifiche che derivano dalla visualizzazione di viste di modelli e modelli. Queste modifiche non sono limitate ai dati visualizzati nei controlli – vengono inoltre utilizzati per controllare la visualizzazione, ad esempio gli stati di visualizzazione del modello che causano animazioni deve essere avviata o controlli deve essere disabilitata.

Proprietà che possono essere aggiornate direttamente tramite lo unit test può essere testata mediante l'aggiunta di un gestore eventi per il `PropertyChanged` eventi e verifica se l'evento viene generato dopo aver impostato un nuovo valore per la proprietà. Esempio di codice seguente viene illustrato un test di questo tipo:

```csharp
[Fact]  
public async Task SettingOrderPropertyShouldRaisePropertyChanged()  
{  
    bool invoked = false;  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    orderViewModel.PropertyChanged += (sender, e) =>  
    {  
        if (e.PropertyName.Equals("Order"))  
            invoked = true;  
    };  
    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.True(invoked);  
}
```

Richiama questo unit test di `InitializeAsync` metodo il `OrderViewModel` classe, causando relativo `Order` proprietà da aggiornare. Lo unit test passerà, a condizione che il `PropertyChanged` evento viene generato per il `Order` proprietà.

### <a name="testing-message-based-communication"></a>Comunicazione basata su messaggi di test

Visualizzazione di modelli che utilizzano il [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe per la comunicazione tra le classi ad accoppiamento debole possibile unit test tramite sottoscrive il messaggio inviato dal codice sottoposto a test, come illustrato nell'esempio di codice seguente:

```csharp
[Fact]  
public void AddCatalogItemCommandSendsAddProductMessageTest()  
{  
    bool messageReceived = false;  
    var catalogService = new CatalogMockService();  
    var catalogViewModel = new CatalogViewModel(catalogService);  

    Xamarin.Forms.MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
        this, MessageKeys.AddProduct, (sender, arg) =>  
    {  
        messageReceived = true;  
    });  
    catalogViewModel.AddCatalogItemCommand.Execute(null);  

    Assert.True(messageReceived);  
}
```

Questo unit test verifica che il `CatalogViewModel` pubblica il `AddProduct` messaggio in risposta a relativo `AddCatalogItemCommand` in esecuzione. Perché il [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe supporta le sottoscrizioni di messaggio multicast, lo unit test può sottoscrivere il `AddProduct` messaggio ed eseguire un delegato di callback in risposta alla ricezione. Imposta il delegato di callback, specificato come un'espressione lambda, un `boolean` campo che viene utilizzato il `Assert` istruzione per verificare il comportamento del test.

### <a name="testing-exception-handling"></a>Test di gestione delle eccezioni

Unit test possono essere scritti anche che il controllo che vengono generate eccezioni specifiche ad azioni non valide o input, come illustrato nell'esempio di codice seguente:

```csharp
[Fact]  
public void InvalidEventNameShouldThrowArgumentExceptionText()  
{  
    var behavior = new MockEventToCommandBehavior  
    {  
        EventName = "OnItemTapped"  
    };  
    var listView = new ListView();  

    Assert.Throws<ArgumentException>(() => listView.Behaviors.Add(behavior));  
}
```

Questo unit test verrà generata un'eccezione, perché il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) controllo non dispone di un evento denominato `OnItemTapped`. Il `Assert.Throws<T>` è un metodo generico in cui `T` è il tipo dell'eccezione prevista. L'argomento passato al `Assert.Throws<T>` metodo è un'espressione lambda che verrà generata un'eccezione. Pertanto, lo unit test passerà condizione che l'espressione lambda genera un `ArgumentException`.

>💡 **Suggerimento**: evitare la scrittura di unit test che esaminano le stringhe di messaggio eccezione. Le stringhe di messaggio di eccezione potrebbero cambiare nel tempo e quindi gli unit test che si basano alla loro presenza sono considerate fragile.

### <a name="testing-validation"></a>Test della convalida

Sono disponibili due opzioni per testare l'implementazione di convalida: test che qualsiasi regola di convalida viene implementate in modo corretto e che la `ValidatableObject<T>` classe eseguita come previsto.

La logica di convalida è in genere semplice da testare, perché in genere è un processo autonomo in cui l'output dipende dall'input. Dovrebbe esserci test sui risultati della chiamata di `Validate` metodo su ogni proprietà che ha almeno una regola di convalida associati, come illustrato nell'esempio di codice seguente:

```csharp
[Fact]  
public void CheckValidationPassesWhenBothPropertiesHaveDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  
    mockViewModel.Surname.Value = "Smith";  

    bool isValid = mockViewModel.Validate();  

    Assert.True(isValid);  
}
```

Questo unit test verifica che la convalida ha esito positivo quando le due `ValidatableObject<T>` le proprietà di `MockViewModel` istanza entrambi disporre di dati.

Oltre a controllare che la convalida ha esito positivo, unit test di convalida deve inoltre verificare i valori del `Value`, `IsValid`, e `Errors` proprietà di ogni `ValidatableObject<T>` istanza, verificare che la classe eseguita come previsto. Esempio di codice riportato di seguito viene illustrato uno unit test che esegue questa operazione:

```csharp
[Fact]  
public void CheckValidationFailsWhenOnlyForenameHasDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  

    bool isValid = mockViewModel.Validate();  

    Assert.False(isValid);  
    Assert.NotNull(mockViewModel.Forename.Value);  
    Assert.Null(mockViewModel.Surname.Value);  
    Assert.True(mockViewModel.Forename.IsValid);  
    Assert.False(mockViewModel.Surname.IsValid);  
    Assert.Empty(mockViewModel.Forename.Errors);  
    Assert.NotEmpty(mockViewModel.Surname.Errors);  
}
```

Questo unit test verifica che la convalida ha esito negativo quando il `Surname` proprietà del `MockViewModel` non contiene alcun dato e `Value`, `IsValid`, e `Errors` proprietà di ogni `ValidatableObject<T>` istanza sono state impostate correttamente.

## <a name="summary"></a>Riepilogo

Uno unit test accetta una piccola unità dell'app, in genere un metodo, consente di isolare dal resto del codice e consente di verificare che tutto funzioni come previsto. L'obiettivo consiste nel verificare che ogni unità funzionale eseguita come previsto, in modo che gli errori non vengano propagate nell'intera app.

Il comportamento di un oggetto sottoposto a test possa essere isolato sostituendo gli oggetti dipendenti con oggetti fittizi che simulano il comportamento degli oggetti dipendenti. In questo modo gli unit test per essere eseguito senza richiedere risorse difficile da gestire, ad esempio servizi web o database.

Testing di modelli e visualizzazione di modelli da applicazioni MVVM è identico per tutte le altre classi di test e gli stessi strumenti e tecniche possono essere utilizzate.


## <a name="related-links"></a>Collegamenti correlati

- [Scaricare eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
