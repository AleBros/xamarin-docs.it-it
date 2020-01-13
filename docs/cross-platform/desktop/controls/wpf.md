---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: 'WPF rispetto a Xamarin.Forms: analogie & differenze'
description: Questo documento consente di confrontare e contrapporre WPF a Xamarin.Forms. Vengono illustrati i modelli di controllo, XAML, l'infrastruttura di binding, i modelli di dati, ItemsControl, UserControl, la navigazione e l'esplorazione URL.
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: 798839457a418d457bac83e6e20397722423dbac
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016482"
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>WPF rispetto a Xamarin.Forms: analogie & differenze

## <a name="control-templates"></a>Modelli di controllo

WPF supporta il concetto di *modelli di controllo* che forniscono le istruzioni di visualizzazione per un controllo (`Button`, `ListBox` e così via). Come indicato in precedenza, Xamarin.Forms usa classi di _rendering_ concrete per questo che interagiscono con la piattaforma nativa (iOS, Android e così via) per visualizzare il controllo.

Tuttavia, Novell _. Forms ha un_ tipo di `ControlTemplate`, che viene usato per i `Page` oggetti. Fornisce una definizione per una `Page` che fornisce contenuto coerente, ma consente all'utente della pagina di modificare i colori, i tipi di carattere e così via e persino di aggiungere elementi per renderli univoci per l'applicazione.

Gli utilizzi comuni per questa operazione sono, ad esempio, le finestre di dialogo di autenticazione, le richieste e fornire un aspetto standardizzato, ma a tema, che può essere personalizzato all'interno dell'app. Come parte di questo supporto, vengono usati molti controlli noti con nome WPF:

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

È tuttavia importante tenere presente che questi _non_ hanno lo stesso scopo in Xamarin.Forms. Per ulteriori informazioni su questa funzionalità, vedere la [pagina della documentazione](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="xaml"></a>XAML

XAML viene utilizzato come linguaggio di markup dichiarativo per WPF e Xamarin.Forms. Nella maggior parte dei casi, la sintassi è identica. la differenza principale è rappresentata dagli oggetti definiti/creati dai grafici XAML.

- Xamarin.Forms supporta la [specifica XAML 2009](/dotnet/framework/xaml-services/xaml-2009-language-features/); Questo semplifica la definizione dei dati, ad esempio `string`s, `int`s e così via, nonché la definizione di tipi generici e il passaggio di argomenti ai costruttori.

- Attualmente non è possibile caricare in modo dinamico XAML come WPF con `XamlReader`. Tuttavia, è possibile ottenere le stesse funzionalità di base con un [pacchetto NuGet](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/) .

### <a name="markup-extensions"></a>Estensioni di markup

Xamarin.Forms supporta l'estensione di XAML tramite estensioni di markup, molto simile a WPF. Con gli stessi blocchi predefiniti di base:

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

Include inoltre `{x:Reference}` dalla specifica XAML 2009 e un'estensione di markup `{TemplateBinding}` utilizzata per la versione specializzata di `ControlTemplate` supportata da Xamarin.Forms.

> [!WARNING]
> Il supporto `ControlTemplate` non è lo stesso, anche se ha lo stesso nome.

Xamarin.Forms supporta anche le estensioni di markup personalizzate, ma l'implementazione è leggermente diversa. In WPF è necessario derivare da `MarkupExtension` una classe base astratta. In Xamarin.Forms, che viene sostituito con un'interfaccia `IMarkupExtension` o `IMarkupExtension<T>` più flessibile.

Proprio come WPF, il singolo metodo richiesto è un metodo di `ProvideValue` per restituire il valore dall'estensione di markup.

## <a name="binding-infrastructure"></a>Infrastruttura di binding

Uno dei concetti di base trasportati è un'infrastruttura data binding per connettere le proprietà visive alle proprietà dei dati .NET. Questo consente modelli di architettura come MVVM. La progettazione di base è identica. si dispone di una classe di base associabile [bindableObject](xref:Xamarin.Forms.BindableObject), in WPF è la classe [DependencyObject](xref:System.Windows.DependencyObject) . Questa classe di base viene usata come predecessore radice per tutti gli oggetti che parteciperanno come destinazioni in data binding. Le classi derivate espongono quindi gli oggetti [BindableProperty](xref:Xamarin.Forms.BindableProperty) che fungono da archiviazione di backup per i valori delle proprietà (definiti come oggetti [DependencyProperty](xref:System.Windows.DependencyProperty) in WPF).

### <a name="defining-bindable-properties"></a>Definizione di proprietà associabili

La definizione di una proprietà associabile in Xamarin.Forms è identica a quella di WPF:

1. L'oggetto deve derivare da `BindableObject`.
2. Deve essere presente un campo statico pubblico di tipo `BindableProperty` dichiarato per definire la chiave di archiviazione di supporto per la proprietà.
3. Deve essere presente un wrapper della proprietà di istanza pubblica che usa `GetValue` e `SetValue` per recuperare e modificare il valore delle proprietà.

Per un esempio completo, vedere [proprietà associabili in Xamarin.Forms](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="attached-properties"></a>Proprietà associate

Le proprietà associate sono un subset della proprietà associabile e funzionano allo stesso modo in WPF. La differenza principale consiste nel fatto che il wrapper della proprietà è omesso in questo caso e sostituito con un set di metodi get/set statici sulla classe proprietaria. Per ulteriori informazioni, vedere [proprietà associate in Xamarin.Forms](~/xamarin-forms/xaml/attached-properties.md) .

### <a name="using-the-binding-engine"></a>Uso del motore di associazione

Il processo di utilizzo del motore di binding è identico a quello di WPF. Può essere utilizzato nel code-behind creando un oggetto `Binding` associato a un oggetto di origine (qualsiasi tipo .NET) e un valore di proprietà facoltativo (se omesso, considera l'oggetto di origine come la proprietà stessa, proprio come WPF). È quindi possibile utilizzare `SetBinding` su qualsiasi `BindableObject` per associare l'associazione a un `BindableProperty`.

In alternativa, è possibile definire la relazione di binding in XAML usando il `BindingExtension`. Ha gli stessi valori di base dell'estensione in WPF.

Il motore e il supporto dell'associazione sono molto simili all'implementazione di Silverlight rispetto a WPF. Esistono diverse funzionalità mancanti che non sono state implementate in Xamarin.Forms:

- Nelle associazioni non è disponibile alcun supporto per le seguenti funzionalità:
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

#### <a name="relativesource"></a>RelativeSource

Non è disponibile alcun supporto per i binding `RelativeSource`. In WPF, questi consentono di eseguire il binding ad altri elementi visivi definiti in XAML. In Xamarin.Forms è possibile ottenere questa stessa funzionalità usando l'estensione di markup `{x:Reference}`. Ad esempio, supponendo che sia presente un controllo con il nome "otherControl" con una proprietà Text, è possibile associarlo come segue:

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

La stessa funzionalità può essere utilizzata per la funzionalità `{RelativeSource Self}`. Non è tuttavia disponibile alcun supporto per l'individuazione di predecessori in base al tipo (`{RelativeSource FindAncestor}`).

#### <a name="binding-context"></a>Contesto di associazione

In WPF è possibile definire un valore di proprietà `DataContext` che reprents l'origine di associazione predefinita. Se l'origine per un'associazione non è definita, viene utilizzato questo valore della proprietà. Il valore viene ereditato dalla struttura ad albero visuale, consentendo la definizione a un livello superiore e quindi utilizzata dagli elementi figlio.

In Xamarin.Forms, questa stessa funzionalità è disponibili, ma il nome della proprietà è `BindingContext`.

#### <a name="value-converters"></a>Convertitori di valori

I convertitori di valori sono completamente supportati in Xamarin.Forms, proprio come WPF. Viene utilizzata la stessa forma di interfaccia, ma in Xamarin.Forms è definita l'interfaccia nello spazio dei nomi `Xamarin.Forms`.

### <a name="model-view-viewmodel"></a>Model-View-ViewModel

MVVM è completamente supportato da WPF e Xamarin.Forms.

WPF include un `RoutedCommand` incorporato a volte usato; Xamarin.Forms non dispone di supporto incorporato per i comandi oltre la definizione dell'interfaccia `ICommand`. È possibile includere un'ampia gamma di framework MVVM per aggiungere le classi di base necessarie per implementare MVVM.

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged e INotifyCollectionChanged

Entrambe le interfacce sono completamente supportate nelle associazioni Xamarin.Forms. A differenza di molti Framework basati su XAML, le notifiche di modifica delle proprietà possono essere generate sui thread in background in Xamarin.Forms (proprio come WPF) e il motore di binding eseguirà correttamente la transizione al thread dell'interfaccia utente.

Inoltre, entrambi gli ambienti supportano `SynchronziationContext` e `async` / `await` per eseguire il marshalling del thread appropriato. WPF include la classe `Dispatcher` su tutti gli elementi visivi, Xamarin.Forms ha un metodo statico `Device.BeginInvokeOnMainThread` che può essere usato anche, anche se `SynchronizationContext` è preferibile per la codifica multipiattaforma.

- Xamarin.Forms include un `ObservableCollection<T>` che supporta le notifiche di modifica della raccolta.
- È possibile usare `BindingBase.EnableCollectionSynchronization` per abilitare gli aggiornamenti tra thread per una raccolta. L'API è leggermente diversa dalla variante WPF. [per informazioni dettagliate sull'utilizzo, vedere la documentazione](xref:Xamarin.Forms.BindingBase.EnableCollectionSynchronization*).

## <a name="data-templates"></a>Modelli di dati

I modelli di dati sono supportati in Xamarin.Forms per personalizzare il rendering di una riga di `ListView` (cella). Diversamente da WPF che può utilizzare `DataTemplate`s per qualsiasi controllo orientato al contenuto, Xamarin.Forms attualmente li utilizza solo per `ListView`. È possibile definire la definizione del modello inline (assegnata alla proprietà `ItemTemplate`) o come risorsa in una `ResourceDictionary`.

Inoltre, non sono altrettanto flessibili della loro controparte WPF.

1. L'elemento radice del `DataTemplate` deve essere _sempre_ un oggetto `ViewCell`.
2. I trigger di dati sono completamente supportati in un modello di dati, ma devono includere una `DataType` proprietà che indica il tipo della proprietà a cui è associato il trigger.
3. `DataTemplateSelector` è inoltre supportato, ma deriva da `DataTemplate` e viene quindi semplicemente assegnato direttamente alla proprietà `ItemTemplate` (vs.  `ItemTemplateSelector` in WPF).

## <a name="itemscontrol"></a>ItemsControl

Non esiste alcun equivalente incorporato a un `ItemsControl` in Xamarin.Forms; Tuttavia, esiste un oggetto [personalizzato per Xamarin.Forms disponibile qui](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs).

## <a name="user-controls"></a>Controlli utente

In WPF, `UserControl`s vengono utilizzati per fornire una sezione dell'interfaccia utente che ha un comportamento associato. In Xamarin.Forms, usiamo la `ContentView` per lo stesso scopo. Supportano sia l'associazione che l'inclusione in XAML.

## <a name="navigation"></a>Navigazione

WPF include una `NavigationService` utilizzata raramente, che può essere utilizzata per fornire una funzionalità di navigazione "simile a un browser". Tuttavia, la maggior parte delle app non ha avuto alcun problema con questa operazione, ma usava invece elementi `Window` diversi o sezioni diverse della finestra per visualizzare i dati.

Nei dispositivi telefonici, diverse _schermate_ sono spesso la soluzione, quindi Xamarin.Forms include il supporto per diverse forme di navigazione:

| Stile di navigazione | Tipo di pagina |
|--- |--- |
|Basata su stack (push/pop)|NavigationPage|
|Report master o di dettaglio|MasterDetailPage|
|Schede|TabbedPage|
|Scorri a sinistra/a destra|CarouselView|

Il `NavigationPage` è l'approccio più comune e ogni pagina dispone di una proprietà `Navigation` che può essere usata per eseguire il push o il pop di pagine nello stack di navigazione. Si tratta dell'equivalente più vicino al `NavigationService` trovato in WPF.

### <a name="url-navigation"></a>Esplorazione URL

WPF è una tecnologia orientata al desktop ed è in grado di accettare parametri della riga di comando per il comportamento di avvio diretto. Xamarin.Forms può usare il [collegamento URL approfondito](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) per passare a una pagina all'avvio.
