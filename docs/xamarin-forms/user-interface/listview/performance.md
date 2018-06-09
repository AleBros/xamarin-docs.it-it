---
title: Prestazioni di ListView
description: ListView è una vista potente per la visualizzazione dei dati, ma presenta alcune limitazioni. In questo articolo viene illustrato come garantire prestazioni elevate con un controllo ListView xamarin. Forms in un'applicazione.
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: 4803a612e2b06e458f2859dbbbd30b970f0fc8ea
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244903"
---
# <a name="listview-performance"></a>Prestazioni di ListView

Quando si scrivono applicazioni per dispositivi mobili, è importante delle prestazioni. Gli utenti aspettano scorrimento continuo e tempi di caricamento rapido. Non soddisfa le aspettative degli utenti verranno costo si classificazioni nell'archivio di applicazione o nel caso di un'applicazione line-of-business, costo del tempo e denaro.

Sebbene [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) è una vista potente per la visualizzazione dei dati, presenta alcune limitazioni. Lo scorrimento delle prestazioni può risultare compromesse se si utilizza celle personalizzate, soprattutto quando che contengono gerarchie visualizzazione eccessivamente annidate o utilizzare determinate layout che richiedono una grande quantità di misura. Per fortuna, esistono tecniche che è possibile utilizzare per evitare un peggioramento delle prestazioni.

<a name="cachingstrategy" />

## <a name="caching-strategy"></a>Strategia di memorizzazione nella cache

Controlli ListView vengono spesso utilizzati per visualizzare più dati rispetto a dimensioni e la posizione sullo schermo. Si consideri un'app di musica, ad esempio. Una libreria di brani di migliaia di voci. L'approccio semplice, che potrebbe essere la creazione di una riga per ogni canzone, sarebbe necessario un peggioramento delle prestazioni. Questo approccio comporta uno spreco di memoria preziose e può rallentare lo scorrimento di una ricerca per indicizzazione. Un altro approccio consiste nella creazione ed eliminazione di righe di dati lo scorrimento nella visualizzazione. Ciò richiede la creazione di istanze costante e la pulizia degli oggetti di visualizzazione, che può essere molto lenta.

Per risparmiare memoria, l'oggetto nativo [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) equivalenti per ogni piattaforma includono funzionalità incorporate per riutilizzare le righe. Solo le celle visibili nella schermata vengono caricate in memoria e **contenuto** viene caricato in celle esistenti. Ciò impedisce all'applicazione la necessità di creare un'istanza di migliaia di oggetti, risparmiando tempo e memoria.

Consente di xamarin. Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) cella utilizzata nuovamente tramite il [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) enumerazione, che presenta i seguenti valori:

```csharp
public enum ListViewCachingStrategy
{
  RetainElement,   // the default value
  RecycleElement,
  RecycleElementAndDataTemplate
}
```

> [!NOTE]
> Ignora la piattaforma UWP (Universal Windows) di [ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/) strategia, la memorizzazione nella cache perché utilizza sempre la memorizzazione nella cache per migliorare le prestazioni. Pertanto, per impostazione predefinita si comporta come se il [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) strategia di memorizzazione nella cache viene applicato.

### <a name="retainelement"></a>RetainElement

Il [ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/) strategia di memorizzazione nella cache specifica che il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) genererà una cella per ogni elemento nell'elenco e il valore predefinito è `ListView` comportamento. E deve essere utilizzato in genere nelle circostanze seguenti:

- Quando ogni cella dispone di un numero elevato di associazioni (20-30 +).
- Quando il modello di cella cambia di frequente.
- Quando il test rivela che il `RecycleElement` la memorizzazione nella cache i risultati strategia una velocità di esecuzione ridotta.

È importante essere consapevoli delle conseguenze del [ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/) strategia di memorizzazione nella cache quando si lavora con celle personalizzate. Sarà necessario eseguire per la creazione di ogni cella, un codice di inizializzazione della cella che possono essere più volte al secondo. In questo caso, le tecniche di layout che sono stati correttamente in una pagina, ad esempio utilizzo di più annidato [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) istanze, diventano i colli di bottiglia delle prestazioni in caso di installazione ed eliminati definitivamente in tempo reale come l'utente scorre.

<a name="recycleelement" />

### <a name="recycleelement"></a>RecycleElement

Il [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) strategia di memorizzazione nella cache specifica che il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) tenterà di ridurre al minimo la velocità di footprint e l'esecuzione di memoria, il riciclo delle celle di elenco. Questa modalità non sempre offrono un miglioramento delle prestazioni e il test deve essere eseguito per determinare eventuali miglioramenti. Tuttavia, è in genere la scelta migliore e deve essere utilizzata nelle seguenti circostanze:

- Quando ogni cella ha un basso numero moderato di associazioni.
- Quando ogni cella [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) definisce tutti i dati delle celle.
- Quando ogni cella sono molto simile, con il modello di cella non modificabile.

Durante la virtualizzazione della cella avrà aggiornato il relativo contesto di associazione e pertanto se un'applicazione utilizza questa modalità è necessario assicurarsi che gli aggiornamenti di contesto dell'associazione sono gestiti in modo appropriato. Tutti i dati relativi alla cella devono provenire dal contesto dell'associazione o potrebbero verificarsi errori di coerenza. Questo può essere eseguito tramite l'associazione dati per visualizzare i dati delle celle. In alternativa, i dati delle celle devono essere impostati nel `OnBindingContextChanged` esegue l'override, invece che nel costruttore della cella personalizzati, come illustrato nell'esempio di codice seguente:

```csharp
public class CustomCell : ViewCell
{
  Image image = null;

  public CustomCell ()
  {
    image = new Image();
    View = image;
  }

  protected override void OnBindingContextChanged ()
  {
    base.OnBindingContextChanged ();

    var item = BindingContext as ImageItem;
    if (item != null) {
      image.Source = item.ImageUrl;
    }
  }
}
```

Per ulteriori informazioni, vedere [modifiche di contesto di associazione](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes).

In iOS e Android, se le celle renderer personalizzato, è necessario assicurarsi che la notifica di modifiche è implementata correttamente. Quando le celle vengono riutilizzate i valori di proprietà verranno modificato quando il contesto di associazione viene aggiornato a quello di una cella, disponibile con `PropertyChanged` eventi generati. Per ulteriori informazioni, vedere [personalizzazione un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>RecycleElement con un DataTemplateSelector

Quando un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) utilizza un [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) per selezionare un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) la memorizzazione nella cache strategia di non memorizzare nella cache `DataTemplate`s. Al contrario, un `DataTemplate` è selezionato per ogni elemento di dati nell'elenco.

> [!NOTE]
> Il [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) strategia di memorizzazione nella cache è un prerequisito, introdotto in 2.4 xamarin. Forms, che, quando un [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) viene chiesto di selezionare un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)che ogni `DataTemplate` deve restituire lo stesso [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) tipo. Si consideri, ad esempio, un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) con un `DataTemplateSelector` che possono restituire `MyDataTemplateA` (dove `MyDataTemplateA` restituisce un `ViewCell` di tipo `MyViewCellA`), o `MyDataTemplateB` (dove `MyDataTemplateB`restituisce un `ViewCell` di tipo `MyViewCellB`), quando `MyDataTemplateA` restituito deve essere restituito `MyViewCellA` oppure verrà generata un'eccezione.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

Il [ `RecycleElementAndDataTemplate` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate/) strategia di memorizzazione nella cache si basa sul [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) strategia di memorizzazione nella cache garantendo inoltre che quando un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) utilizza un [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) per selezionare un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), `DataTemplate`s vengono memorizzati nella cache dal tipo di elemento nell'elenco. Pertanto, `DataTemplate`s viene selezionata una volta per ogni tipo di elemento, anziché una volta per ogni istanza dell'elemento.

> [!NOTE]
> Il [ `RecycleElementAndDataTemplate` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate/) strategia di memorizzazione nella cache è un prerequisito che il `DataTemplate`restituiti dal [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) deve utilizzare il [ `DataTemplate` ](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Type/) costruttore che accetta un `Type`.

### <a name="setting-the-caching-strategy"></a>Impostazione della strategia di memorizzazione nella cache

Il [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) viene specificato il valore di enumerazione con un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) overload del costruttore, come illustrato nell'esempio di codice seguente:

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

In XAML, impostare il `CachingStrategy` attributo come illustrato nel codice seguente:

```xaml
<ListView CachingStrategy="RecycleElement">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
              ...
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Questo ha lo stesso effetto dell'impostazione dell'argomento di strategia di memorizzazione nella cache nel costruttore di c#. Si noti che non esiste alcun `CachingStrategy` proprietà `ListView`.

#### <a name="setting-the-caching-strategy-in-a-subclassed-listview"></a>Impostazione della strategia di memorizzazione nella cache in un controllo sottoclassato ListView

L'impostazione di `CachingStrategy` attributo da XAML in una sottoclasse [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) non produrrà il comportamento desiderato, perché non esiste alcun `CachingStrategy` proprietà `ListView`. Inoltre, se [XAMLC](~/xamarin-forms/xaml/xamlc.md) è abilitata, verrà prodotto il seguente messaggio di errore: **alcuna proprietà, la proprietà associabile o evento trovato per 'CachingStrategy'**

Per risolvere questo problema consiste nello specificare un costruttore nel tipo di sottoclasse [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) che accetta un [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) parametro e lo passa la classe di base:

```csharp
public class CustomListView : ListView
{
  public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
  {
  }
  ...
}
```

Il [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) valore di enumerazione può essere specificato da XAML tramite il `x:Arguments` sintassi:

```xaml
<local:CustomListView>
  <x:Arguments>
    <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
  </x:Arguments>
</local:CustomListView>
```

<a name="improving-performance" />

## <a name="improving-listview-performance"></a>Miglioramento delle prestazioni di ListView

Sono disponibili numerose tecniche per migliorare le prestazioni di un `ListView`:

-  Associare il `ItemsSource` proprietà per un `IList<T>` raccolta invece di un `IEnumerable<T>` insieme, perché `IEnumerable<T>` insiemi non supportano l'accesso casuale.
-  Utilizzare le celle predefinite (ad esempio `TextCell`  /  `SwitchCell` ) anziché `ViewCell` ogni volta che è possibile.
-  Utilizzare un minor numero di elementi. Ad esempio utilizzare un singolo `FormattedString` etichetta anziché più etichette.
-  Sostituire il `ListView` con un `TableView` quando si visualizzano dati omogenei: i dati di tipi diversi.
-  Limitare l'utilizzo del [ `Cell.ForceUpdateSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.ForceUpdateSize()/) metodo. Se l'utilizzo eccessivo, influirà negativamente sulle prestazioni.
-  In Android, evitare di impostare un `ListView`della visibilità di separatore di riga o di colore dopo che è stata creata un'istanza, poiché comporta una riduzione delle prestazioni di grandi dimensioni.
-  Evitare di modificare il layout della cella in base il [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/). Ciò comporta costi di layout e l'inizializzazione di grandi dimensioni.
-  Evitare gerarchie di layout di annidamento. Utilizzare `AbsoluteLayout` o `Grid` per ridurre la nidificazione.
-  Evitare specifico `LayoutOptions` diverso da `Fill` (riempimento è cheapest di calcolo).
-  Evitare di inserire un `ListView` all'interno di un `ScrollView` per i motivi seguenti:
  - Il `ListView` implementa un proprio lo scorrimento.
  - Il `ListView` non riceveranno i movimenti di qualsiasi tipo, come verranno gestiti dall'elemento padre `ScrollView`.
  - Il `ListView` può presentare un'intestazione personalizzata e un piè di pagina che consente di scorrere gli elementi dell'elenco, potenzialmente offre le funzionalità di `ScrollView` è stata utilizzata per. Per ulteriori informazioni vedere [intestazioni e piè di pagina](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#Headers_and_Footers).
-  Se è necessaria una progettazione molto specifica e complessa presentata in alle celle, prendere in considerazione un renderer personalizzato.

`AbsoluteLayout` è in grado di eseguire layout senza una chiamata a singola misura. Questo rende molto potente per le prestazioni. Se `AbsoluteLayout` non può essere utilizzato, prendere in considerazione [ `RelativeLayout` ](http://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/). Se si utilizza `RelativeLayout`, passando direttamente i vincoli saranno notevolmente più veloce rispetto all'utilizzo dell'API di espressione. Ciò avviene perché l'espressione API utilizza JIT e in iOS è illustrata la struttura deve essere interpretato, che è più lento. L'API di espressione sia adatta per layout di pagina in cui richiesto solo nel layout iniziale e la rotazione, ma `ListView`, in cui viene eseguita costantemente durante lo scorrimento, influisce negativamente sulle prestazioni.

La creazione di un renderer personalizzato per un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) o le relative celle, è uno degli approcci disponibili per ridurre l'effetto di scorrimento prestazioni i calcoli layout. Per ulteriori informazioni, vedere [personalizzazione di un controllo ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) e [personalizzazione un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).


## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazione di rendering personalizzata (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithListviewNative/)
- [ViewCell Renderer personalizzato (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/viewcell/)
- [ListViewCachingStrategy](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/)
