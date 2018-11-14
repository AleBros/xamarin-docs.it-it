---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: 'Visual Studio WPF. Xamarin. Forms: Analogie e differenze'
description: Questo documento vengono confrontate e contrapposte WPF a xamarin. Forms. Illustra i modelli di controllo, XAML, l'infrastruttura di associazione, i modelli di dati, ItemsControl, UserControl, navigazione e navigazione URL con.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: d3e772c270f6003d01e3e7b487f69f682fec2d61
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617592"
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>Visual Studio WPF. Xamarin. Forms: Analogie e differenze

## <a name="control-templates"></a>Modelli di controllo

WPF supporta il concetto *modelli di controllo* che forniscono le istruzioni di visualizzazione per un controllo (`Button`, `ListBox`e così via.). Come indicato in precedenza, xamarin. Forms Usa concreto _rendering_ classi per questo che interagiscono con la piattaforma nativa (iOS, Android, ecc.) per visualizzare il controllo.

Tuttavia, xamarin. Forms _viene_ hanno una `ControlTemplate` tipo - viene usato per i temi `Page` oggetti. Fornisce una definizione per un `Page` che fornisce contenuto uniforme, ma consente all'utente della pagina modificare i colori, tipi di carattere e così via e persino aggiungere elementi per renderlo univoco per l'applicazione.

Utilizzi comuni per questo sono aspetti, ad esempio finestre di dialogo di autenticazione, richieste e per fornire un pagina standardizzati, ma con temi utilizzabili aspetto che possono essere personalizzato all'interno dell'app. Come parte di questo supporto, vengono usati molti controlli di WPF-denominato familiarità:

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

È tuttavia importante sapere che queste siano _non_ che servono lo stesso scopo in xamarin. Forms. Per altre informazioni su questa funzionalità, consultare il [pagina della documentazione](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="xaml"></a>XAML

XAML viene usato come linguaggio di markup dichiarativo per WPF e xamarin. Forms. Nella maggior parte, la sintassi è identica: la differenza principale è costituita dagli oggetti che vengono definiti/creati per i grafici XAML.

- Xamarin. Forms supporta il [specifica di XAML 2009](/dotnet/framework/xaml-services/xaml-2009-language-features/); questo rende più semplice definire i dati, ad esempio `string`s, `int`s, i tipi generici anche come definizione e così via e il passaggio di argomenti ai costruttori.

- Non è attualmente possibile caricare dinamicamente XAML, ad esempio WPF può con `XamlReader`. È possibile ottenere la stessa funzionalità di base con un [mobileengagement](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/) se.

### <a name="markup-extensions"></a>Estensioni di markup

Xamarin. Forms supporta l'estensione XAML tramite le estensioni di markup, molto simile a WPF. Per impostazione predefinita, ha gli stessi blocchi predefiniti di base:

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

Include inoltre `{x:Reference}` dalla specifica di XAML 2009 e una `{TemplateBinding}` estensione di markup che viene usato per la versione specializzata del `ControlTemplate` supportati da xamarin. Forms.

> [!WARNING]
> Il `ControlTemplate` supporto non è gli stessi, anche se ha lo stesso nome.

Xamarin. Forms supporta anche - le estensioni di markup personalizzata, ma l'implementazione è leggermente diversa. In WPF, è necessario derivare da `MarkupExtension` -classe di base astratta. In xamarin. Forms, che viene sostituito con un'interfaccia `IMarkupExtension` o `IMarkupExtension<T>` che è più flessibile.

Come WPF, è il metodo richiesto solo un `ProvideValue` metodo per restituire il valore dell'estensione di markup.

## <a name="binding-infrastructure"></a>Infrastruttura di associazione

Uno dei concetti di base trasferiti è un'infrastruttura di associazione dati per la connessione proprietà visive alle proprietà di dati .NET. In questo modo, i modelli di architettura, ad esempio MVVM. La progettazione di base è identica, si dispone di una classe di base associabile [BindableObject](xref:Xamarin.Forms.BindableObject), in WPF questo è il [DependencyObject](xref:System.Windows.DependencyObject) classe. Questa classe di base viene utilizzata come il predecessore di radice per tutti gli oggetti che parteciperanno come destinazioni nel data binding. Le classi derivate quindi espongono [BindableProperty](xref:Xamarin.Forms.BindableProperty) gli oggetti che agiscono come spazio di archiviazione sottostante per i valori delle proprietà (questi vengono definiti [DependencyProperty](xref:System.Windows.DependencyProperty) agli oggetti in WPF).

### <a name="defining-bindable-properties"></a>La definizione di proprietà associabili

La definizione per una proprietà associabile in xamarin. Forms è lo stesso come WPF:
1. L'oggetto deve derivare da `BindableObject`.
2. Deve esistere un campo statico pubblico di tipo `BindableProperty` dichiarato per definire la chiave di archiviazione sottostante per la proprietà.
3. Dovrebbe esserci un wrapper della proprietà di istanza pubblica che utilizza `GetValue` e `SetValue` per recuperare e modificare il valore di proprietà.

Per un esempio completo, vedere [proprietà associabili in xamarin. Forms](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="attached-properties"></a>Proprietà associate

Le proprietà associate sono un subset della proprietà associabile e funzionano esattamente come che avviene in WPF. La differenza principale è che il wrapper della proprietà in questo caso è omesso e sostituito con un set di metodi get/set statico nella classe proprietario. Visualizzare [delle proprietà associata in xamarin. Forms](~/xamarin-forms/xaml/attached-properties.md) per altre informazioni.

### <a name="using-the-binding-engine"></a>Tramite il motore di binding

La procedura per usare il motore di binding è lo stesso perché si trova in WPF. Può essere utilizzato nel code-behind creando un `Binding` oggetto associato all'oggetto di origine (qualsiasi tipo .NET) e un valore della proprietà facoltativo (se omesso, considera l'oggetto di origine della proprietà stessa - come WPF). È quindi possibile usare `SetBinding` su qualsiasi `BindableObject` associare l'associazione a un `BindableProperty`.

In alternativa, è possibile definire la relazione di associazione in XAML usando il `BindingExtension`. Include gli stessi valori di base come l'estensione in WPF.

Il motore e il supporto dell'associazione sono più simile all'implementazione di Silverlight a WPF. Esistono numerose funzionalità mancante che non sono state implementate in xamarin. Forms:

- Non è supportata per le funzionalità seguenti nelle associazioni:
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
    - Raccolta di proprietà ValidationRules
    - XPath
    - XmlNamespaceManager

#### <a name="relativesource"></a>RelativeSource

Non è disponibile alcun supporto per `RelativeSource` associazioni. In WPF, consentono di eseguire l'associazione agli altri elementi visual definiti in XAML. In xamarin. Forms, questa stessa funzionalità può essere ottenuta utilizzando il `{x:Reference}` estensione di markup. Ad esempio, supponendo di che avere un controllo con il nome "otherControl" che dispone di una proprietà di testo, è possibile associare ad esso simile al seguente:

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

La stessa funzionalità può essere usata per il `{RelativeSource Self}` funzionalità. Tuttavia non è supportata per l'individuazione di predecessori dal tipo (`{RelativeSource FindAncestor}`).

#### <a name="binding-context"></a>Contesto di associazione

In WPF, è possibile definire un `DataContext` reprents quale valore della proprietà dell'origine di associazione predefinita. Se l'origine per un'associazione non è definito, viene utilizzato il valore della proprietà. Il valore viene ereditato lungo la struttura ad albero visuale, in modo che possa essere definiti a un livello superiore e quindi usati da elementi figlio.

In xamarin. Forms, questa stessa funzionalità è insufficiente, ma è il nome della proprietà `BindingContext`.

#### <a name="value-converters"></a>Convertitori di valori

Convertitori di valori sono completamente supportati in xamarin. Forms - come WPF. Viene utilizzata la stessa forma di interfaccia, ma xamarin. Forms con interfaccia definita nella `Xamarin.Forms` dello spazio dei nomi.

### <a name="model-view-viewmodel"></a>Model-View-ViewModel

MVVM è completamente supportata da WPF e xamarin. Forms.

WPF include una `RoutedCommand` che viene a volte usato; Xamarin. Forms non dispone di alcun supporto predefinito di esecuzione dei comandi di là di `ICommand` definizione dell'interfaccia. È possibile includere un'ampia gamma di Framework MVVM per aggiungere le classi di base necessarie per implementare MVVM.

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged e INotifyCollectionChanged

Entrambe le interfacce sono completamente supportate nei binding di xamarin. Forms. A differenza di molti framework basati su XAML, le notifiche di modifica di proprietà possono essere generate nel thread in background in xamarin. Forms (proprio come WPF) e il motore di binding verrà correttamente la transizione al thread dell'interfaccia utente.

Inoltre, supportano entrambi gli ambienti `SynchronziationContext` e `async` / `await` per effettuare il marshalling di thread appropriato. WPF include il `Dispatcher` classe su tutti gli elementi visivi, xamarin. Forms è un metodo statico `Device.BeginInvokeOnMainThread` che può essere usato anche (sebbene `SynchronizationContext` diventi preferito per multi-piattaforma di codifica).

- Xamarin. Forms include un `ObservableCollection<T>` che supporta le notifiche di modifica raccolta.
- È possibile usare `BindingBase.EnableCollectionSynchronization` per abilitare gli aggiornamenti di cross-thread per una raccolta. L'API è leggermente diversa dalla variazione, WPF [controllare la documentazione per i dettagli di utilizzo](xref:Xamarin.Forms.BindingBase.EnableCollectionSynchronization*).

## <a name="data-templates"></a>Modelli di dati

Modelli di dati sono supportati in xamarin. Forms per personalizzare il rendering di un `ListView` riga (cella). A differenza di WPF che possono utilizzare `DataTemplate`s per un controllo contenuto-oriented, xamarin. Forms attualmente solo li utilizza per `ListView`. La definizione di modello può essere definite inline (assegnato per il `ItemTemplate` proprietà), o come una risorsa in un `ResourceDictionary`.

Inoltre, non sono abbastanza flessibile come loro controparte WPF.

1. L'elemento radice del `DataTemplate` devono _sempre_ da un `ViewCell` oggetto.
2. I trigger sono completamente supportati in un modello di dati, ma deve includere un `DataType` proprietà che indica il tipo della proprietà che è associato il trigger.
3. `DataTemplateSelector` è anche supportato, ma deriva da `DataTemplate` e pertanto viene semplicemente assegnata direttamente al `ItemTemplate` proprietà (confronto `ItemTemplateSelector` in WPF).

## <a name="itemscontrol"></a>ItemsControl

Non vi è alcun equivelente predefinite per un `ItemsControl` in xamarin. Forms, ma non esiste un [uno personalizzato per xamarin. Forms disponibile qui](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs).

## <a name="user-controls"></a>Controlli utente

In WPF, `UserControl`s vengono usate per fornire una sezione dell'interfaccia utente che ha associato il comportamento. In xamarin. Forms, utilizziamo il `ContentView` per lo stesso scopo. Entrambi supportano l'associazione e inclusione in XAML.

## <a name="navigation"></a>Navigazione

WPF include un usati raramente `NavigationService` che può essere usato per fornire una funzionalità di "browser" per l'esplorazione. La maggior parte delle App non preoccuparsi di ciò tuttavia e invece usati diversi `Window` elementi o varie sezioni della finestra per visualizzare i dati.

Nei dispositivi di telefono, diversi _schermate_ rappresentano spesso la soluzione e quindi, xamarin. Forms include il supporto per varie forme di navigazione:

| Stile di navigazione | Tipo di pagina |
|--- |--- |
|Basato su stack (push/pop)|NavigationPage|
|Master-Details|MasterDetailPage|
|Schede|TabbedPage|
|Scorrere verso sinistra/destra|CarouselView|

Il `NavigationPage` è l'approccio più comune e ogni pagina possiede un `Navigation` proprietà che può essere utilizzato per eseguire il push o Apri nuova finestra pagine e disattivare lo stack di navigazione. Questo è il più vicino equivelente al `NavigationService` disponibili in WPF.

### <a name="url-navigation"></a>Navigazione URL con

WPF è una tecnologia basata su desktop e può accettare parametri della riga di comando per indirizzare il comportamento di avvio. Può usare xamarin. Forms [deep URL collegato](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) per passare a una pagina all'avvio.
