---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: 'Confronto tra WPF e Novell. Forms: Analogie & differenze'
description: Questo documento consente di confrontare e contrapporre WPF a Novell. Forms. Vengono illustrati i modelli di controllo, XAML, l'infrastruttura di binding, i modelli di dati, ItemsControl, UserControl, la navigazione e l'esplorazione URL.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 149636719c7f8046b8a32d8d2f4157b663388cb1
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227604"
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>Confronto tra WPF e Novell. Forms: Analogie & differenze

## <a name="control-templates"></a>Modelli di controllo

WPF supporta il concetto di *modelli di controllo* che forniscono le istruzioni di visualizzazione per un controllo`Button`( `ListBox`, e così via). Come indicato in precedenza, Novell. Forms usa classi di _rendering_ concrete per questo che interagiscono con la piattaforma nativa (iOS, Android e così via) per visualizzare il controllo.

Tuttavia, Novell. Forms ha un `ControlTemplate` tipo, che viene `Page` usato per gli oggetti di tema. Fornisce una definizione per un oggetto `Page` che fornisce contenuto coerente, ma consente all'utente della pagina di modificare i colori, i tipi di carattere e così via e persino di aggiungere elementi per renderli univoci per l'applicazione.

Gli utilizzi comuni per questa operazione sono, ad esempio, le finestre di dialogo di autenticazione, le richieste e fornire un aspetto standardizzato, ma a tema, che può essere personalizzato all'interno dell'app. Come parte di questo supporto, vengono usati molti controlli noti con nome WPF:

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

È tuttavia importante tenere presente che questi _non_ hanno lo stesso scopo in Novell. Forms. Per ulteriori informazioni su questa funzionalità, vedere la [pagina della documentazione](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="xaml"></a>XAML

XAML viene utilizzato come linguaggio di markup dichiarativo per WPF e Novell. Forms. Nella maggior parte dei casi, la sintassi è identica. la differenza principale è rappresentata dagli oggetti definiti/creati dai grafici XAML.

- Novell. Forms supporta la [specifica XAML 2009](/dotnet/framework/xaml-services/xaml-2009-language-features/); Questo semplifica la definizione di dati quali `string`s, `int`s e così via, nonché la definizione di tipi generici e il passaggio di argomenti ai costruttori.

- Attualmente non è possibile caricare in modo dinamico XAML come WPF con `XamlReader`. Tuttavia, è possibile ottenere le stesse funzionalità di base con un [pacchetto NuGet](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/) .

### <a name="markup-extensions"></a>Estensioni di markup

Novell. Forms supporta l'estensione di XAML tramite estensioni di markup, molto simile a WPF. Con gli stessi blocchi predefiniti di base:

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

Include `{x:Reference}` inoltre dalla specifica XAML 2009 e da un' `{TemplateBinding}` estensione di markup utilizzata per la versione specializzata di `ControlTemplate` supportata da Novell. Forms.

> [!WARNING]
> Il `ControlTemplate` supporto non è lo stesso, anche se ha lo stesso nome.

Novell. Forms supporta anche le estensioni di markup personalizzate, ma l'implementazione è leggermente diversa. In WPF è necessario derivare da `MarkupExtension` una classe base astratta. In Novell. Forms, che viene sostituito con un' `IMarkupExtension` interfaccia `IMarkupExtension<T>` o più flessibile.

Proprio come WPF, il singolo metodo richiesto è un `ProvideValue` metodo per restituire il valore dall'estensione di markup.

## <a name="binding-infrastructure"></a>Infrastruttura di binding

Uno dei concetti di base trasportati è un'infrastruttura data binding per connettere le proprietà visive alle proprietà dei dati .NET. Questo consente modelli di architettura come MVVM. La progettazione di base è identica. si dispone di una classe di base associabile [bindableObject](xref:Xamarin.Forms.BindableObject), in WPF è la classe [DependencyObject](xref:System.Windows.DependencyObject) . Questa classe di base viene usata come predecessore radice per tutti gli oggetti che parteciperanno come destinazioni in data binding. Le classi derivate espongono quindi gli oggetti [BindableProperty](xref:Xamarin.Forms.BindableProperty) che fungono da archiviazione di backup per i valori delle proprietà (definiti come oggetti [DependencyProperty](xref:System.Windows.DependencyProperty) in WPF).

### <a name="defining-bindable-properties"></a>La definizione di proprietà associabili

La definizione di una proprietà associabile in Novell. Forms è identica a quella di WPF:
1. L'oggetto deve derivare `BindableObject`da.
2. Deve essere presente un campo statico pubblico di tipo `BindableProperty` dichiarato per definire la chiave di archiviazione di supporto per la proprietà.
3. Deve essere presente un wrapper della proprietà di istanza pubblica `GetValue` che `SetValue` utilizza e per recuperare e modificare il valore delle proprietà.

Per un esempio completo, vedere [proprietà associabili in Novell. Forms](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="attached-properties"></a>Proprietà associate

Le proprietà associate sono un subset della proprietà associabile e funzionano allo stesso modo in WPF. La differenza principale consiste nel fatto che il wrapper della proprietà è omesso in questo caso e sostituito con un set di metodi get/set statici sulla classe proprietaria. Per ulteriori informazioni, vedere [proprietà associate in Novell. Forms](~/xamarin-forms/xaml/attached-properties.md) .

### <a name="using-the-binding-engine"></a>Uso del motore di associazione

Il processo di utilizzo del motore di binding è identico a quello di WPF. Può essere utilizzato nel code-behind creando un `Binding` oggetto associato a un oggetto di origine (qualsiasi tipo .NET) e un valore della proprietà facoltativa (se omesso, considera l'oggetto di origine come la proprietà stessa, proprio come WPF). È quindi possibile usare `SetBinding` su Any `BindableObject` per associare l'associazione a un `BindableProperty`oggetto.

In alternativa, è possibile definire la relazione di associazione in XAML usando `BindingExtension`. Ha gli stessi valori di base dell'estensione in WPF.

Il motore e il supporto dell'associazione sono molto simili all'implementazione di Silverlight rispetto a WPF. Esistono diverse funzionalità mancanti che non sono state implementate in Novell. Forms:

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

Non è disponibile alcun supporto `RelativeSource` per le associazioni. In WPF, questi consentono di eseguire il binding ad altri elementi visivi definiti in XAML. In Novell. Forms è possibile ottenere questa stessa funzionalità usando l' `{x:Reference}` estensione di markup. Ad esempio, supponendo che sia presente un controllo con il nome "otherControl" con una proprietà Text, è possibile associarlo come segue:

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

La stessa funzionalità può essere usata per la `{RelativeSource Self}` funzionalità. Tuttavia, non è disponibile alcun supporto per l'individuazione di predecessori`{RelativeSource FindAncestor}`per tipo ().

#### <a name="binding-context"></a>Contesto di associazione

In WPF è possibile definire un `DataContext` valore di proprietà che reprents l'origine di associazione predefinita. Se l'origine per un'associazione non è definita, viene utilizzato questo valore della proprietà. Il valore viene ereditato dalla struttura ad albero visuale, consentendo la definizione a un livello superiore e quindi utilizzata dagli elementi figlio.

In Novell. Forms, questa stessa funzionalità è disponibili, ma il nome della `BindingContext`proprietà è.

#### <a name="value-converters"></a>Convertitori di valori

I convertitori di valori sono completamente supportati in Novell. Forms, proprio come WPF. Viene utilizzata la stessa forma di interfaccia, ma in Novell. Forms è definita l' `Xamarin.Forms` interfaccia nello spazio dei nomi.

### <a name="model-view-viewmodel"></a>Model-View-ViewModel

MVVM è completamente supportato da WPF e Novell. Forms.

WPF include un oggetto incorporato `RoutedCommand` , a volte usato; Novell. Forms non dispone del supporto incorporato per i comandi oltre `ICommand` la definizione dell'interfaccia. È possibile includere un'ampia gamma di framework MVVM per aggiungere le classi di base necessarie per implementare MVVM.

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged e INotifyCollectionChanged

Entrambe le interfacce sono completamente supportate nelle associazioni Novell. Forms. A differenza di molti Framework basati su XAML, le notifiche di modifica delle proprietà possono essere generate sui thread in background in Novell. Forms (proprio come WPF) e il motore di binding eseguirà correttamente la transizione al thread dell'interfaccia utente.

Inoltre, entrambi gli ambienti supportano `SynchronziationContext` e `async` / `await` per eseguire il marshalling di thread appropriato. WPF include la `Dispatcher` classe su tutti gli elementi visivi, Novell. Forms ha `Device.BeginInvokeOnMainThread` un metodo statico che può essere usato `SynchronizationContext` anche (sebbene sia preferibile per la codifica multipiattaforma).

- Novell. Forms include `ObservableCollection<T>` un che supporta le notifiche di modifica della raccolta.
- È possibile usare `BindingBase.EnableCollectionSynchronization` per abilitare gli aggiornamenti tra thread per una raccolta. L'API è leggermente diversa dalla variante WPF. [per informazioni dettagliate sull'utilizzo, vedere la documentazione](xref:Xamarin.Forms.BindingBase.EnableCollectionSynchronization*).

## <a name="data-templates"></a>Modelli di dati

I modelli di dati sono supportati in Novell. Forms per personalizzare il `ListView` rendering di una riga (cella). Diversamente da WPF che può utilizzare `DataTemplate`per qualsiasi controllo orientato al contenuto, Novell. Forms attualmente li utilizza solo per. `ListView` È possibile definire la definizione del modello inline (assegnata `ItemTemplate` alla proprietà) o come risorsa in un oggetto `ResourceDictionary`.

Inoltre, non sono altrettanto flessibili della loro controparte WPF.

1. L'elemento radice di `DataTemplate` deve _sempre_ essere un `ViewCell` oggetto.
2. I trigger di dati sono completamente supportati in un modello di dati, ma `DataType` devono includere una proprietà che indica il tipo della proprietà a cui è associato il trigger.
3. `DataTemplateSelector`è supportato anche, ma deriva da `DataTemplate` e viene quindi semplicemente assegnato direttamente `ItemTemplate` alla proprietà (vs. `ItemTemplateSelector` in WPF).

## <a name="itemscontrol"></a>ItemsControl

Non esiste un equivalente incorporato a `ItemsControl` in Novell. Forms, ma è presente un oggetto [personalizzato per Novell. Forms disponibile qui](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs).

## <a name="user-controls"></a>Controlli utente

In WPF, `UserControl`i vengono usati per fornire una sezione dell'interfaccia utente che ha un comportamento associato. In Novell. Forms, `ContentView` usiamo per lo stesso scopo. Supportano sia l'associazione che l'inclusione in XAML.

## <a name="navigation"></a>Navigazione

WPF include un oggetto raramente `NavigationService` utilizzato che può essere utilizzato per fornire una funzionalità di navigazione "simile a un browser". Tuttavia, la maggior parte delle app non ha avuto alcun `Window` problema con questa operazione, ma usava invece elementi diversi o sezioni diverse della finestra per visualizzare i dati.

Nei dispositivi telefonici, diverse schermate sono spesso la soluzione, quindi Novell. Forms include il supporto per diverse forme di navigazione:

| Stile di navigazione | Tipo di pagina |
|--- |--- |
|Basata su stack (push/pop)|NavigationPage|
|Report master o di dettaglio|MasterDetailPage|
|Schede|TabbedPage|
|Scorri a sinistra/a destra|CarouselView|

È l'approccio più comune e ogni pagina dispone di una `Navigation` proprietà che può essere utilizzata per eseguire il push o il pop di pagine nello stack di navigazione. `NavigationPage` Si tratta dell'equivalente più vicino a `NavigationService` trovato in WPF.

### <a name="url-navigation"></a>Esplorazione URL

WPF è una tecnologia orientata al desktop ed è in grado di accettare parametri della riga di comando per il comportamento di avvio diretto. Novell. Forms può usare il [collegamento URL approfondito](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) per passare a una pagina all'avvio.
