---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: 'Visual Studio WPF. Xamarin. Forms: Analogie e differenze'
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 21ffca65ee72308d1340a1db43471228b2adbe91
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>Visual Studio WPF. Xamarin. Forms: Analogie e differenze

## <a name="control-templates"></a>Modelli di controllo

WPF supporta il concetto di *modelli di controllo* che forniscono le istruzioni di visualizzazione per un controllo (`Button`, `ListBox`, ecc.). Come indicato in precedenza, xamarin. Forms Usa concrete _rendering_ classi per questo che interagiscono con la piattaforma nativa (iOS, Android, ecc.) per visualizzare il controllo.

Tuttavia, xamarin. Forms _does_ hanno un `ControlTemplate` tipo - viene usato per i temi `Page` oggetti. Fornisce una definizione per un `Page` che fornisce contenuti coerenti, ma consente all'utente della pagina modificare i colori, tipi di carattere e così via e anche aggiungere elementi per renderla univoca per l'applicazione.

Utilizzi comuni per questo sono aspetti, ad esempio le finestre di dialogo autenticazione, richieste e per fornire un pagina standardizzato, ma con temi utilizzabili e l'aspetto che può essere personalizzato all'interno dell'app. Come parte di questo supporto, vengono utilizzati molti controlli di WPF con nome familiari:

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

È tuttavia importante sapere che si tratta _non_ che servono lo stesso scopo in xamarin. Forms. Per ulteriori informazioni su questa caratteristica, consultare il [pagina della documentazione](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="xaml"></a>XAML

XAML viene usato come linguaggio di markup dichiarativo per WPF e xamarin. Forms. Nella maggior parte, la sintassi è identica: la differenza principale è costituita dagli oggetti che sono definiti/creato nei grafici XAML.

- Xamarin. Forms supporta il [specifica di XAML 2009](/dotnet/framework/xaml-services/xaml-2009-language-features/); questo rende più semplice definire i dati, ad esempio `string`s, `int`s, i tipi generici anche come definizione e così via e il passaggio di argomenti ai costruttori.

- Non è attualmente possibile caricare dyanmically XAML WPF può essere con `XamlReader`. È possibile ottenere la stessa funzionalità di base con un [pacchetto NuGet](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/) se.

### <a name="markup-extensions"></a>Estensioni di markup

Xamarin. Forms supporta l'estensione XAML tramite le estensioni di markup, analogamente a WPF. Impostazione predefinita, il metodo ha lo stessi blocchi predefiniti di base:

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

Include inoltre `{x:Reference}` dalla specifica di XAML 2009 e una `{TemplateBinding}` estensione di markup che viene utilizzato per la versione specializzata del `ControlTemplate` supportati da xamarin. Forms.

> [!WARNING]
> Il `ControlTemplate` supporto differisce da quella - anche se presenta lo stesso nome.

Xamarin. Forms supporta estensioni di markup personalizzate nonché - ma l'implementazione è leggermente diversa. In WPF, è necessario derivare da `MarkupExtension` -classe di base astratta. In xamarin. Forms, che viene sostituito con un'interfaccia `IMarkupExtension` o `IMarkupExtension<T>` che è più flessibile.

Esattamente come WPF, è l'unico metodo obbligatorio un `ProvideValue` metodo per restituire il valore dell'estensione di markup.

## <a name="binding-infrastructure"></a>Infrastruttura di associazione

Uno dei principali concetti trasferiti è un'infrastruttura di associazione dati per la connessione proprietà visive alle proprietà di dati .NET. In questo modo i modelli di architettura, ad esempio MVVM. La struttura di base è identica, si dispone di una classe di basa associabile [BindableObject](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/), in WPF questo è il [DependencyObject](https://msdn.microsoft.com/en-us/library/system.windows.dependencyobject(v=vs.110).aspx) classe. Questa classe di base viene utilizzata come il predecessore radice per tutti gli oggetti che farà parte come destinazioni nel data binding. Le classi derivate, espongono [BindableProperty](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) oggetti che agiscono come spazio di archiviazione sottostante per i valori delle proprietà (questi vengono definiti [DependencyProperty](https://msdn.microsoft.com/library/system.windows.dependencyproperty(v=vs.110).aspx) agli oggetti in WPF).

### <a name="defining-bindable-properties"></a>Definizione di proprietà associabili

La definizione di una proprietà associabile in xamarin. Forms è quella di WPF:
1. L'oggetto deve derivare da `BindableObject`.
2. Deve esistere un campo statico pubblico di tipo `BindableProperty` dichiarato per definire la chiave di archiviazione sottostante per la proprietà.
3. Dovrebbe esserci un wrapper di proprietà di istanza pubblico che utilizza `GetValue` e `SetValue` per recuperare e modificare il valore di proprietà.

Per un esempio completo, vedere [proprietà associabili in xamarin. Forms](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="attached-properties"></a>Proprietà associate

Le proprietà associate sono un subset di proprietà associabile e funzionano esattamente come che avviene in WPF. La differenza principale è che il wrapper della proprietà in questo caso è omesso e sostituito con un set di metodi get/set statico nella classe proprietaria. Vedere [proprietà associata in xamarin. Forms](~/xamarin-forms/xaml/attached-properties.md) per altre informazioni.

### <a name="using-the-binding-engine"></a>Tramite il motore di associazione

Il processo per usare il motore di associazione è lo stesso come in WPF. Può essere utilizzata nel code-behind per la creazione di un `Binding` oggetto associato a un oggetto di origine (qualsiasi tipo .NET) e un valore della proprietà facoltative (se omesso, considera l'oggetto di origine della proprietà stessa - esattamente come WPF). È quindi possibile usare `SetBinding` su qualsiasi `BindableObject` associare l'associazione a un `BindableProperty`.

In alternativa, è possibile definire la relazione di associazione in XAML tramite il `BindingExtension`. Dispone gli stessi valori di base come l'estensione in WPF.

Il supporto di associazione e il motore sono più simile all'implementazione di Silverlight WPF. Esistono numerose funzionalità mancante che non sono state implementate in xamarin. Forms:

- Non è supportato per le funzionalità seguenti in associazioni:
    - BindingGroupName
    - BindsDirectlyToSource
    - IsAsync
    - MultiBinding
    - NotifyOnSourceUpdated
    - NotifyOnTargetUpdated
    - NotifyOnValidationError
    - UpdateSourceTrigger
    - UpdateSourceExceptionFilter
    - ValidatesOnDataErrors
    - ValidatesOnExceptions
    - Raccolta ValidationRules
    - XPath
    - XmlNamespaceManager
- `Binding.Mode` non supporta `OneTime`, in alternativa, usare semplicemente `OneWay`.

#### <a name="relativesource"></a>RelativeSource

Non è supportato per `RelativeSource` associazioni. In WPF, consentono di associare altri elementi visivi definiti in XAML. In xamarin. Forms, questa stessa funzionalità può essere ottenuta utilizzando il `{x:Reference}` estensione di markup. Ad esempio, supponendo che si disponga di un controllo con il nome "otherControl" che contiene una proprietà di testo, è possibile associare a esso simile al seguente:

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

La stessa funzionalità può essere utilizzata per il `{RelativeSource Self}` funzionalità. Tuttavia non è supportato per l'individuazione di predecessori dal tipo (`{RelativeSource FindAncestor}`).

#### <a name="binding-context"></a>Contesto di associazione

In WPF, è possibile definire un `DataContext` quali reprents valore della proprietà dell'origine di associazione predefinita. Se l'origine per un'associazione non è definito, viene utilizzato il valore della proprietà. Il valore viene ereditato verso il basso la struttura ad albero visuale, in modo che possa essere definito con un livello superiore e quindi usati da elementi figlio.

In xamarin. Forms, questa stessa funzionalità è insufficiente, ma è il nome della proprietà `BindingContext`.

#### <a name="value-converters"></a>Convertitori di valori

Convertitori di valori sono completamente supportati in xamarin. Forms - esattamente come WPF. Viene utilizzata la stessa forma di interfaccia, ma dispone di xamarin. Forms l'interfaccia definita nel `Xamarin.Forms` dello spazio dei nomi.

### <a name="model-view-viewmodel"></a>Model-View-ViewModel

MVVM è completamente supportata da WPF e xamarin. Forms.

WPF include incorporata `RoutedCommand` che a volte viene utilizzato; Xamarin. Forms non dispone di alcun supporto di comandi predefinito oltre il `ICommand` definizione dell'interfaccia. È possibile includere una varietà di Framework MVVM per aggiungere le classi di base necessarie per implementare MVVM.

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged e INotifyCollectionChanged

Entrambe le interfacce sono completamente supportate nelle associazioni di xamarin. Forms. A differenza di molti Framework basate su XAML, le notifiche di modifica di proprietà possono essere generate nel thread in background in xamarin. Forms (come WPF) e il motore di associazione passerà in modo corretto nel thread UI.

Inoltre, supportano entrambi gli ambienti `SynchronziationContext` e `async` / `await` per effettuare il marshalling di thread appropriato. WPF include il `Dispatcher` (classe) su tutti gli elementi visivi, xamarin. Forms è un metodo statico `Device.BeginInvokeOnMainThread` che può essere usato anche (sebbene `SynchronizationContext` sia quella preferita per la generazione di codice multipiattaforma).

- Xamarin. Forms include un `ObservableCollection<T>` che supporta le notifiche di modifica raccolta.
- È possibile utilizzare `BindingBase.EnableCollectionSynchronization` per abilitare gli aggiornamenti di cross-thread per una raccolta. L'API è leggermente diversa dalla variante di WPF [controllare la documentazione per informazioni dettagliate sull'utilizzo](https://developer.xamarin.com/api/member/Xamarin.Forms.BindingBase.EnableCollectionSynchronization/).

## <a name="data-templates"></a>Modelli di dati

I modelli di dati sono supportati in xamarin. Forms per personalizzare il rendering di un `ListView` riga (cella). A differenza di WPF che puoi usare la `DataTemplate`s per qualsiasi controllo orientata ai servizi di contenuto, xamarin. Forms attualmente solo li utilizza per `ListView`. La definizione di modello può essere definite inline (assegnato per il `ItemTemplate` proprietà), oppure come risorsa in un `ResourceDictionary`.

Non sono inoltre molto flessibile come loro controparte WPF.

1. L'elemento radice del `DataTemplate` deve _sempre_ da un `ViewCell` oggetto.
2. I trigger sono completamente supportati in un modello di dati, ma deve includere un `DataType` proprietà che indica il tipo della proprietà che è associato il trigger.
3. `DataTemplateSelector` è anche supportato, ma deriva da `DataTemplate` e pertanto assegnate direttamente al semplicemente il `ItemTemplate` proprietà (e `ItemTemplateSelector` in WPF).

## <a name="itemscontrol"></a>ItemsControl

Non vi è alcun equivelente predefinite per un `ItemsControl` in xamarin. Forms; ma non esiste un [uno personalizzato per xamarin. Forms disponibili in questo punto](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs).

## <a name="user-controls"></a>Controlli utente

In WPF, `UserControl`s vengono utilizzati per fornire una sezione dell'interfaccia utente che è associati comportamento. In xamarin. Forms, si usa il `ContentView` allo stesso scopo. Entrambi supportano l'associazione e inclusione in XAML.

## <a name="navigation"></a>Navigazione

WPF include un utilizzati raramente `NavigationService` che può essere utilizzato per fornire una funzionalità di "browser" per l'esplorazione. La maggior parte delle App non preoccuparsi di ciò tuttavia e invece utilizzati diversi `Window` elementi o varie sezioni della finestra per visualizzare i dati.

Nei dispositivi phone, diversi _schermate_ sono spesso la soluzione e quindi xamarin. Forms include il supporto per diversi tipi di navigazione:

| Stile di navigazione | Tipo di pagina |
|--- |--- |
|Basato su stack (push/pop)|NavigationPage|
|Master-Details|MasterDetailPage|
|Schede|TabbedPage|
|Scorri rapidamente verso sinistra/destra|CarouselView|

Il `NavigationPage` è l'approccio più comune, e ogni pagina possiede un `Navigation` proprietà che può essere utilizzato per eseguire il push o ingrandirlo pagine e disattivare lo stack di navigazione. Questo è il più vicino equivelente per il `NavigationService` trovato in WPF.

### <a name="url-navigation"></a>Navigazione URL con

WPF è una tecnologia basata su desktop e possono accettare parametri della riga di comando per indirizzare il comportamento di avvio. Utilizzare xamarin. Forms [collegamento URL completo](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) per passare a una pagina all'avvio.
