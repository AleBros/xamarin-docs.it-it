---
title: Unit test di App aziendali
description: In questo capitolo viene illustrato come gli unit test viene eseguito nell'app per dispositivi mobili di eShopOnContainers.
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 02aeedd5498c47950e2fbc0d218de05bc0bb3204
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61298986"
---
# <a name="unit-testing-enterprise-apps"></a>Unit test di App aziendali

App per dispositivi mobili presentano problemi univoci che applicazioni desktop e basate sul web non sono necessario preoccuparsi. Gli utenti mobili variano dai dispositivi che usano, dalla connettività di rete, la disponibilità dei servizi e una gamma di altri fattori. Pertanto, è necessario testare le App per dispositivi mobili perché verrà usati nel mondo reale per migliorare la qualità, affidabilità e le prestazioni. Esistono molti tipi di test che devono essere eseguiti in un'app, tra cui unit test, test di integrazione e test, con gli unit test in corso la forma più comune di test dell'interfaccia utente.

Uno unit test accetta un'unità di piccole dimensioni dell'app, in genere un metodo, isolato dal resto del codice e consente di verificare che tutto funzioni come previsto. L'obiettivo consiste nel verificare che ogni unità funzionale eseguita come previsto, in modo che gli errori non propagano in tutta l'app. Rilevamento di bug in cui si verifica è più efficiente che osservare l'effetto di un bug indirettamente in un momento secondario di errore.

Gli unit test raggiungono la massima efficacia sulla qualità del codice quando è parte integrante del flusso di lavoro di sviluppo software. Non appena è stato scritto un metodo, devono essere scritti gli unit test verificare il comportamento del metodo in risposta a casi standard, limite e non corretti dei dati di input e che il controllo eventuali ipotesi esplicite o implicite effettuate dal codice. In alternativa, lo sviluppo basato su test e unit test vengono scritti prima del codice. In questo scenario, gli unit test è fungere da documentazione di progettazione sia come specifiche funzionali.

> [!NOTE]
> Unit test risultano molto efficace contro la regressione, vale a dire, la funzionalità che consentono di utilizzare, ma è stata distribuita da un aggiornamento difettoso.

Gli unit test usano in genere il modello arrange-act-assert:

-   Il *disponi* sezione del metodo di unit test Inizializza oggetti e imposta il valore dei dati che viene passati al metodo sottoposto a test.
-   Il *agire* sezione richiama il metodo sottoposto a test con gli argomenti obbligatori.
-   Il *assert* sezione verifica che l'azione del metodo da testare si comporti come previsto.

Seguendo questo modello garantisce che gli unit test sono leggibili e coerente.

## <a name="dependency-injection-and-unit-testing"></a>Inserimento di dipendenze e gli Unit test

Uno dei motivi per adottare un'architettura a regime di controllo è che facilita gli unit test. Uno dei tipi registrati con Autofac è il `OrderService` classe. Esempio di codice seguente mostra un contorno di questa classe:

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

Il `OrderDetailViewModel` classe presenta una dipendenza la `IOrderService` digitare che si risolve il contenitore quando crea un'istanza di un `OrderDetailViewModel` oggetto. Tuttavia, invece di creare un `OrderService` oggetto lo unit test la `OrderDetailViewModel` (classe), invece, sostituire il `OrderService` oggetto con una simulazione allo scopo di test. Figura 10-1 illustra questa relazione.

![](unit-testing-images/unittesting.png "Classi che implementano l'interfaccia IOrderService")

**Figura 10-1:** Classi che implementano l'interfaccia IOrderService

Questo approccio consente la `OrderService` oggetto da passare nel `OrderDetailViewModel` classi in fase di esecuzione e ai fini di testabilità, consente il `OrderMockService` classe deve essere passato nel `OrderDetailViewModel` classe in fase di test. Il vantaggio principale di questo approccio è che consente gli unit test per essere eseguiti senza risorse difficile da gestire, ad esempio servizi web o database.

## <a name="testing-mvvm-applications"></a>Test delle applicazioni MVVM

Test di modelli di visualizzazione dei modelli e dalle applicazioni MVVM è identico a un'altra classe di test e gli stessi strumenti e tecniche, ad esempio unit test e simulazione, possono essere usate. Tuttavia, esistono alcuni modelli tipici al modello e le classi di modello di visualizzazione, che possono trarre vantaggio dalle tecniche di test di unità specifica.

> [!TIP]
> Testare una cosa con ogni unit test. Non può essere tentati di far sì che un'unità di esercizio più di un aspetto del comportamento dell'unità. Questa operazione comporta i test che sono difficili da leggere e aggiornare. Può anche causare confusione durante l'interpretazione di un errore.

L'app per dispositivi mobili Usa eShopOnContainers [xUnit](https://xunit.github.io/) per eseguire unit test, che supporta due tipi diversi di unit test:

-   I fact sono i test che sono sempre true, quale testare condizioni invarianti.
-   Teorie sono test che sono solo true per un particolare set di dati.

Gli unit test inclusi con l'app per dispositivi mobili di eShopOnContainers vengono fatti i test e quindi, ogni metodo di unit test è decorata con il `[Fact]` attributo.

> [!NOTE]
> xUnit test vengono eseguiti da un test runner. Per eseguire il test runner, eseguire il progetto eShopOnContainers.TestRunner per la piattaforma richiesta.

### <a name="testing-asynchronous-functionality"></a>Test della funzionalità asincrona

Quando si implementa il pattern MVVM, visualizzazione di modelli in genere richiama le operazioni sui servizi, spesso in modo asincrono. Test per codice che richiama in genere queste operazioni utilizzano gli oggetti fittizi come sostituzioni per i servizi effettivamente. Esempio di codice seguente viene illustrato il test di funzionalità asincrona passando un servizio fittizio in un modello di visualizzazione:

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

Questo unit test verifica che il `Order` proprietà del `OrderDetailViewModel` istanza avrà un valore dopo il `InitializeAsync` metodo è stato richiamato. Il `InitializeAsync` metodo viene richiamato quando si accede alla vista corrispondente del modello di visualizzazione. Per altre informazioni sulla navigazione, vedere [navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md).

Quando la `OrderDetailViewModel` viene creata l'istanza, è previsto che un `OrderService` istanza specificare come argomento. Tuttavia, il `OrderService` recupera i dati da un servizio web. Di conseguenza, un' `OrderMockService` istanza, che è una versione fittizia del `OrderService` classe, viene specificato come argomento per il `OrderDetailViewModel` costruttore. Quindi, quando il modello di visualizzazione `InitializeAsync` metodo viene richiamato, che richiama `IOrderService` operazioni, dati fittizi sono recuperati, piuttosto che stanno comunicando con un servizio web.

### <a name="testing-inotifypropertychanged-implementations"></a>Test di implementazioni INotifyPropertyChanged

Implementazione di `INotifyPropertyChanged` interfaccia consente di reagire alle modifiche che derivano dalla visualizzazione viste modelli e i modelli. Queste modifiche non sono limitate ai dati visualizzati nei controlli, vengono usati anche per gestire la visualizzazione, ad esempio gli stati del modello di visualizzazione che generano le animazioni da avviare o controlli deve essere disabilitata.

Proprietà che possono essere aggiornate direttamente dallo unit test può essere testata mediante l'aggiunta di un gestore eventi per il `PropertyChanged` eventi e verifica se l'evento viene generato dopo aver impostato un nuovo valore della proprietà. Esempio di codice seguente mostra un test di questo tipo:

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

Richiama questo unit test di `InitializeAsync` metodo per il `OrderViewModel` classe, determinando in tal modo relativo `Order` proprietà da aggiornare. Lo unit test passerà, a condizione che il `PropertyChanged` evento viene generato per il `Order` proprietà.

### <a name="testing-message-based-communication"></a>Test della comunicazione basata su messaggi

Visualizzazione di modelli che utilizzano le [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe per la comunicazione tra le classi di accoppiamento possibile unit test tramite sottoscrive il messaggio inviato dal codice sottoposto a test, come illustrato nell'esempio di codice seguente:

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

Questo unit test verifica che il `CatalogViewModel` pubblica il `AddProduct` messaggio di risposta alla relativa `AddCatalogItemCommand` in esecuzione. Poiché il [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe supporta le sottoscrizioni di messaggio multicast, lo unit test può sottoscrivere il `AddProduct` messaggi ed eseguire un delegato di callback in risposta alla ricezione. Imposta il delegato di callback, specificato come un'espressione lambda, un `boolean` campo che viene usato per il `Assert` istruzione per verificare il comportamento del test.

### <a name="testing-exception-handling"></a>La gestione delle eccezioni di test

Gli unit test possono essere scritto anche che il controllo che vengono generate eccezioni specifiche per le azioni non è valide o input, come illustrato nell'esempio di codice seguente:

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

Questo unit test verrà generata un'eccezione, perché il [ `ListView` ](xref:Xamarin.Forms.ListView) controllo non dispone di un evento denominato `OnItemTapped`. Il `Assert.Throws<T>` è un metodo generico in cui `T` è il tipo dell'eccezione prevista. L'argomento passato al `Assert.Throws<T>` metodo è un'espressione lambda che genererà l'eccezione. Pertanto, lo unit test si passerà a condizione che l'espressione lambda genera un `ArgumentException`.

>💡 **Suggerimento**: Evitare la scrittura di unit test che esaminano le stringhe di messaggio eccezione. Le stringhe di messaggio di eccezione possono cambiare nel tempo e quindi gli unit test che si basano sulla loro presenza sono considerati come fragile.

### <a name="testing-validation"></a>Test della convalida

Esistono due aspetti legati all'esecuzione di test l'implementazione di convalida: test che qualsiasi regola di convalida viene implementati in modo corretto e che il `ValidatableObject<T>` classe eseguita come previsto.

La logica di convalida è in genere semplice da testare, perché in genere è un processo self-contained in cui l'output dipende dall'input. Dovrebbe esserci test sui risultati della chiamata di `Validate` metodo su ogni proprietà dispone di almeno una regola di convalida associati, come illustrato nell'esempio di codice seguente:

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

Questo unit test verifica che la convalida ha esito positivo quando le due `ValidatableObject<T>` delle proprietà nel `MockViewModel` istanza entrambi disporre dei dati.

Oltre a controllare che la convalida ha esito positivo, convalida gli unit test devono anche verificare i valori delle `Value`, `IsValid`, e `Errors` proprietà della ognuno `ValidatableObject<T>` istanza, verificare che la classe eseguita come previsto. L'esempio di codice seguente illustra uno unit test che esegue questa operazione:

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

Questo unit test verifica che la convalida ha esito negativo quando la `Surname` proprietà del `MockViewModel` non dispone di tutti i dati e il `Value`, `IsValid`, e `Errors` proprietà della ognuno `ValidatableObject<T>` istanza siano impostate correttamente.

## <a name="summary"></a>Riepilogo

Uno unit test accetta un'unità di piccole dimensioni dell'app, in genere un metodo, isolato dal resto del codice e consente di verificare che tutto funzioni come previsto. L'obiettivo consiste nel verificare che ogni unità funzionale eseguita come previsto, in modo che gli errori non propagano in tutta l'app.

Il comportamento di un oggetto sottoposto a test può essere isolato, sostituendo gli oggetti dipendenti con oggetti fittizi che simulano il comportamento degli oggetti dipendenti. In questo modo gli unit test per essere eseguiti senza risorse difficile da gestire, ad esempio servizi web o database.

Test di modelli di visualizzazione dei modelli e dalle applicazioni MVVM è identico a un'altra classe di test e gli stessi strumenti e tecniche da utilizzare.


## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
