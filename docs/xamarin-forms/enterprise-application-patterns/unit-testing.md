---
title: Unit test di app aziendali
description: Questo capitolo illustra il modo in cui viene eseguito il testing unità nell'app per dispositivi mobili eShopOnContainers.
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 0fb63c650e73bce5a08b204f942f0c19583e4899
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770679"
---
# <a name="unit-testing-enterprise-apps"></a>Unit test di app aziendali

Le app per dispositivi mobili presentano problemi specifici che le applicazioni desktop e basate sul Web non devono preoccuparsi. Gli utenti mobili si differenziano per i dispositivi usati, dalla connettività di rete, dalla disponibilità dei servizi e da una serie di altri fattori. Pertanto, le app per dispositivi mobili devono essere testate perché verranno usate nel mondo reale per migliorare la qualità, l'affidabilità e le prestazioni. Sono disponibili molti tipi di test da eseguire in un'app, inclusi testing unità, test di integrazione e test dell'interfaccia utente, con testing unità la forma più comune di test.

Una unit test accetta una piccola unità dell'app, in genere un metodo, la isola dalla parte rimanente del codice e verifica che si comportano come previsto. Il suo obiettivo consiste nel verificare che ogni unità di funzionalità venga eseguita come previsto, in modo che gli errori non si propaghino nell'intera app. Il rilevamento di un bug in cui si verifica è più efficiente che osservando l'effetto di un bug indirettamente in un punto secondario di errore.

Il testing unità ha il massimo effetto sulla qualità del codice quando è parte integrante del flusso di lavoro di sviluppo del software. Non appena è stato scritto un metodo, è necessario scrivere unit test che verificano il comportamento del metodo in risposta a casi standard, limite e non corretti di dati di input e che controllano qualsiasi presupposto esplicito o implicito eseguito dal codice. In alternativa, con lo sviluppo basato su test, gli unit test vengono scritti prima del codice. In questo scenario gli unit test agiscono sia come documentazione di progettazione sia come specifiche funzionali.

> [!NOTE]
> Gli unit test sono molto efficaci rispetto alla regressione, ovvero le funzionalità che hanno usato per lavorare ma sono state disturbate da un aggiornamento difettoso.

Gli unit test usano in genere il modello Arrange-Act-Assert:

- La sezione *Arrange* del metodo unit test Inizializza gli oggetti e imposta il valore dei dati passati al metodo sottoposto a test.
- La sezione *Act* richiama il metodo sottoposto a test con gli argomenti obbligatori.
- La sezione *Assert* verifica che l'azione del metodo sottoposto a test si comportano come previsto.

Seguendo questo modello si garantisce che gli unit test siano leggibili e coerenti.

## <a name="dependency-injection-and-unit-testing"></a>Inserimento di dipendenze e unit test

Una delle motivazioni per l'adozione di un'architettura a regime di controllo libero è che facilita il testing unità. Uno dei tipi registrati con Autofac è la `OrderService` classe. Nell'esempio di codice seguente viene illustrata una struttura di questa classe:

```csharp
public class OrderDetailViewModel : ViewModelBase  
{  
    private IOrderService _ordersService;  

    public OrderDetailViewModel(IOrderService ordersService)  
    {  
        _ordersService = ordersService;  
    }  
    ...  
}
```

La `OrderDetailViewModel` classe presenta una dipendenza `IOrderService` dal tipo che il contenitore risolve quando crea un'istanza di un `OrderDetailViewModel` oggetto. Tuttavia, invece di creare un `OrderService` oggetto per unit test la `OrderDetailViewModel` classe, sostituire l' `OrderService` oggetto con una simulazione per lo scopo dei test. La figura 10-1 illustra questa relazione.

![](unit-testing-images/unittesting.png "Classi che implementano l'interfaccia IOrderService")

**Figura 10-1:** Classi che implementano l'interfaccia IOrderService

Questo approccio consente di `OrderService` passare `OrderDetailViewModel` l'oggetto alla classe in fase di esecuzione e, per motivi di testabilità, consente di passare `OrderDetailViewModel` la `OrderMockService` classe alla classe in fase di test. Il vantaggio principale di questo approccio è che consente di eseguire gli unit test senza richiedere risorse non ingombranti, ad esempio servizi Web o database.

## <a name="testing-mvvm-applications"></a>Test delle applicazioni MVVM

Il testing dei modelli e la visualizzazione di modelli dalle applicazioni MVVM è identico a quello di qualsiasi altra classe ed è possibile usare gli stessi strumenti e tecniche, ad esempio gli unit test e le simulazioni. Tuttavia, esistono alcuni modelli tipici per modellare e visualizzare le classi di modelli, che possono trarre vantaggio da specifiche tecniche di unit test.

> [!TIP]
> Testare una cosa con ogni unit test. Non tentare di creare un unit test esercitare più di un aspetto del comportamento dell'unità. Questa operazione conduce a test difficili da leggere e aggiornare. Può anche causare confusione durante l'interpretazione di un errore.

L'app per dispositivi mobili eShopOnContainers USA [xUnit](https://xunit.github.io/) per eseguire unit test, che supporta due tipi diversi di unit test:

- I fact sono test sempre veri, che testano le condizioni invariabili.
- Le teorie sono test che sono veri solo per un determinato set di dati.

Gli unit test inclusi con l'app per dispositivi mobili eShopOnContainers sono test di fact, quindi ogni unit test metodo è decorato con l' `[Fact]` attributo.

> [!NOTE]
> i test xUnit vengono eseguiti da un test runner. Per eseguire il test runner, eseguire il progetto eShopOnContainers. TestRunner per la piattaforma richiesta.

### <a name="testing-asynchronous-functionality"></a>Test della funzionalità asincrona

Quando si implementa il modello MVVM, i modelli di visualizzazione in genere richiamano le operazioni sui servizi, spesso in modo asincrono. I test per il codice che richiama queste operazioni in genere utilizzano simulazioni come sostituzioni per i servizi effettivi. Nell'esempio di codice seguente viene illustrato il test della funzionalità asincrona passando un servizio fittizio in un modello di visualizzazione:

```csharp
[Fact]  
public async Task OrderPropertyIsNotNullAfterViewModelInitializationTest()  
{  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.NotNull(orderViewModel.Order);  
}
```

Questo unit test verifica che la `Order` proprietà `OrderDetailViewModel` dell'istanza avrà un valore dopo che il `InitializeAsync` metodo è stato richiamato. Il `InitializeAsync` metodo viene richiamato quando si passa alla visualizzazione corrispondente del modello di visualizzazione. Per ulteriori informazioni sulla navigazione, vedere [navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md).

Quando viene `OrderDetailViewModel` creata l'istanza, prevede che un' `OrderService` istanza venga specificata come argomento. Tuttavia, recupera `OrderService` i dati da un servizio Web. Pertanto, un' `OrderMockService` istanza `OrderService` di, che è una versione fittizia della classe, viene specificata come argomento per il `OrderDetailViewModel` costruttore. Quindi, quando viene richiamato il `InitializeAsync` metodo del modello di visualizzazione, che `IOrderService` richiama le operazioni, vengono recuperati i dati fittizi anziché comunicare con un servizio Web.

### <a name="testing-inotifypropertychanged-implementations"></a>Test delle implementazioni di INotifyPropertyChanged

L'implementazione `INotifyPropertyChanged` dell'interfaccia consente alle viste di rispondere alle modifiche che hanno origine da modelli e modelli di visualizzazione. Queste modifiche non sono limitate ai dati visualizzati nei controlli, ma vengono usate anche per controllare la visualizzazione, ad esempio gli Stati di visualizzazione del modello che provocano l'avvio delle animazioni o la disabilitazione dei controlli.

Le proprietà che possono essere aggiornate direttamente dalla unit test possono essere testate connettendo un gestore eventi all' `PropertyChanged` evento e controllando se l'evento viene generato dopo l'impostazione di un nuovo valore per la proprietà. Nell'esempio di codice seguente viene illustrato un test di questo tipo:

```csharp
[Fact]  
public async Task SettingOrderPropertyShouldRaisePropertyChanged()  
{  
    bool invoked = false;  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    orderViewModel.PropertyChanged += (sender, e) =>  
    {  
        if (e.PropertyName.Equals("Order"))  
            invoked = true;  
    };  
    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.True(invoked);  
}
```

Questo unit test richiama il metodo `InitializeAsync` `OrderViewModel` della classe, che determina l'aggiornamento della `Order` relativa proprietà. Il unit test passerà, a condizione che `PropertyChanged` l'evento venga generato per `Order` la proprietà.

### <a name="testing-message-based-communication"></a>Test della comunicazione basata su messaggi

I modelli di visualizzazione che [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) usano la classe per la comunicazione tra classi a regime di controllo libero possono essere unit test sottoscrivendo il messaggio inviato dal codice sottoposto a test, come illustrato nell'esempio di codice seguente:

```csharp
[Fact]  
public void AddCatalogItemCommandSendsAddProductMessageTest()  
{  
    bool messageReceived = false;  
    var catalogService = new CatalogMockService();  
    var catalogViewModel = new CatalogViewModel(catalogService);  

    Xamarin.Forms.MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
        this, MessageKeys.AddProduct, (sender, arg) =>  
    {  
        messageReceived = true;  
    });  
    catalogViewModel.AddCatalogItemCommand.Execute(null);  

    Assert.True(messageReceived);  
}
```

Questo unit test verifica che il `CatalogViewModel` pubblichi il `AddProduct` messaggio in risposta alla sua `AddCatalogItemCommand` esecuzione. Poiché la [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe supporta le sottoscrizioni di messaggi multicast, il unit test può `AddProduct` sottoscrivere il messaggio ed eseguire un delegato di callback in risposta alla ricezione. Questo delegato di callback, specificato come espressione lambda, imposta un `boolean` campo usato `Assert` dall'istruzione per verificare il comportamento del test.

### <a name="testing-exception-handling"></a>Test della gestione delle eccezioni

È anche possibile scrivere unit test che verificano che vengano generate eccezioni specifiche per le azioni o gli input non validi, come illustrato nell'esempio di codice seguente:

```csharp
[Fact]  
public void InvalidEventNameShouldThrowArgumentExceptionText()  
{  
    var behavior = new MockEventToCommandBehavior  
    {  
        EventName = "OnItemTapped"  
    };  
    var listView = new ListView();  

    Assert.Throws<ArgumentException>(() => listView.Behaviors.Add(behavior));  
}
```

Questa unit test genererà un'eccezione, perché il [`ListView`](xref:Xamarin.Forms.ListView) controllo non ha un evento denominato. `OnItemTapped` Il `Assert.Throws<T>` metodo è un metodo generico in `T` cui è il tipo dell'eccezione prevista. L'argomento passato al `Assert.Throws<T>` metodo è un'espressione lambda che genererà l'eccezione. Pertanto, il unit test passerà a condizione che l'espressione lambda generi `ArgumentException`un'eccezione.

> [!TIP]
> Evitare di scrivere unit test che esaminano le stringhe di messaggio di eccezione. Le stringhe dei messaggi di eccezione potrebbero cambiare nel tempo, quindi gli unit test che si basano sulla loro presenza vengono considerati fragili.

### <a name="testing-validation"></a>Verifica della convalida

Esistono due aspetti per testare l'implementazione della convalida: verificare che le regole di convalida siano implementate correttamente e verificare che `ValidatableObject<T>` la classe esegua come previsto.

La logica di convalida è in genere semplice da testare, perché si tratta in genere di un processo indipendente in cui l'output dipende dall'input. Devono essere testati i risultati della chiamata al `Validate` metodo su ogni proprietà con almeno una regola di convalida associata, come illustrato nell'esempio di codice seguente:

```csharp
[Fact]  
public void CheckValidationPassesWhenBothPropertiesHaveDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  
    mockViewModel.Surname.Value = "Smith";  

    bool isValid = mockViewModel.Validate();  

    Assert.True(isValid);  
}
```

Questo unit test verifica che la convalida abbia esito positivo `ValidatableObject<T>` quando le due `MockViewModel` proprietà nell'istanza dispongono di dati.

Oltre a verificare che la convalida abbia esito positivo, gli unit test di convalida devono anche controllare `Value`i `IsValid`valori della `Errors` proprietà, e `ValidatableObject<T>` di ogni istanza, per verificare che la classe venga eseguita come previsto. Nell'esempio di codice seguente viene illustrata una unit test che esegue questa operazione:

```csharp
[Fact]  
public void CheckValidationFailsWhenOnlyForenameHasDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  

    bool isValid = mockViewModel.Validate();  

    Assert.False(isValid);  
    Assert.NotNull(mockViewModel.Forename.Value);  
    Assert.Null(mockViewModel.Surname.Value);  
    Assert.True(mockViewModel.Forename.IsValid);  
    Assert.False(mockViewModel.Surname.IsValid);  
    Assert.Empty(mockViewModel.Forename.Errors);  
    Assert.NotEmpty(mockViewModel.Surname.Errors);  
}
```

Questo unit test verifica che la convalida abbia esito negativo quando `MockViewModel` la `Surname` proprietà di non contiene dati e `Value`la `IsValid`proprietà, `Errors` e di ogni `ValidatableObject<T>` istanza sono impostate correttamente.

## <a name="summary"></a>Riepilogo

Una unit test accetta una piccola unità dell'app, in genere un metodo, la isola dalla parte rimanente del codice e verifica che si comportano come previsto. Il suo obiettivo consiste nel verificare che ogni unità di funzionalità venga eseguita come previsto, in modo che gli errori non si propaghino nell'intera app.

Il comportamento di un oggetto sottoposto a test può essere isolato sostituendo gli oggetti dipendenti con oggetti fittizi che simulano il comportamento degli oggetti dipendenti. In questo modo è possibile eseguire unit test senza richiedere risorse ingombranti, ad esempio servizi Web o database.

Il testing di modelli e modelli di visualizzazione da applicazioni MVVM è identico a quello di altre classi ed è possibile usare gli stessi strumenti e tecniche.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
