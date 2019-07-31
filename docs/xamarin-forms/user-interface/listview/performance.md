---
title: Prestazioni di ListView
description: ListView è una visualizzazione potente per la visualizzazione dei dati, ma presenta alcune limitazioni. Questo articolo illustra come garantire prestazioni ottimali con un ListView di xamarin. Forms in un'applicazione.
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: 4a0a7a4db4b0ca982a162ec3a0b67dc729af0ed2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655944"
---
# <a name="listview-performance"></a>Prestazioni di ListView

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

Quando si scrivono applicazioni per dispositivi mobili, è importante delle prestazioni. Gli utenti sono ormai si aspettano uno scorrimento fluido e tempi di caricamento rapido. Non soddisfa le aspettative degli utenti verranno costo di valutazioni nell'archivio di applicazione o nel caso di un'applicazione line-of-business, costo del tempo e denaro.

Sebbene [ `ListView` ](xref:Xamarin.Forms.ListView) è una potente vista per visualizzare i dati, ma presenta alcune limitazioni. Lo scorrimento delle prestazioni può peggiorare quando si usano le celle personalizzate, in particolare quando contengono gerarchie di visualizzazione con livello di nidificazione o usano alcuni layout che richiedono una grande quantità di unità di misura. Fortunatamente, esistono tecniche che è possibile utilizzare per evitare una riduzione delle prestazioni.

<a name="cachingstrategy" />

## <a name="caching-strategy"></a>Strategia di memorizzazione nella cache

ListView che vengono spesso usati per visualizzare molti più dati rispetto a fit sullo schermo. Si consideri ad esempio un'app, brani musicali. Una libreria di brani può avere migliaia di voci. L'approccio semplice, che potrebbe essere la creazione di una riga per ogni brano, sarebbe necessario una riduzione delle prestazioni. Questo approccio comporta uno spreco di memoria preziosa e può rallentare lo scorrimento di una ricerca per indicizzazione. Un altro approccio consiste nel creare e distruggere righe come dati di scorrimento nella visualizzazione. Ciò richiede la creazione di istanze costante e la pulizia degli oggetti di visualizzazione, che può essere molto lenta.

Per risparmiare memoria, l'oggetto nativo [ `ListView` ](xref:Xamarin.Forms.ListView) equivalenti per ogni piattaforma dispongono di caratteristiche incorporate per riutilizzare le righe. Solo le celle visibili sullo schermo vengono caricate in memoria e il **contenuto** viene caricato in celle esistenti. Ciò impedisce all'applicazione di dover creare un'istanza di migliaia di oggetti, risparmiando tempo e memoria.

Xamarin. Forms consente [ `ListView` ](xref:Xamarin.Forms.ListView) riutilizzare cella tramite la [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) enumerazione, che presenta i seguenti valori:

```csharp
public enum ListViewCachingStrategy
{
  RetainElement,   // the default value
  RecycleElement,
  RecycleElementAndDataTemplate
}
```

> [!NOTE]
> Universal Windows Platform (UWP) ignora i [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) strategia, la memorizzazione nella cache perché Usa sempre la memorizzazione nella cache per migliorare le prestazioni. Pertanto, per impostazione predefinita si comporta come se il [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache viene applicato.

### <a name="retainelement"></a>RetainElement

Il [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) strategia di memorizzazione nella cache consente di specificare che il [ `ListView` ](xref:Xamarin.Forms.ListView) genererà una cella per ogni elemento nell'elenco e il valore predefinito è `ListView` comportamento. In genere, dovrebbe essere utilizzata nelle circostanze seguenti:

- Quando ogni cella con un numero elevato di associazioni (20-30 +).
- Quando il modello della cella viene spesso modificato.
- Quando il test rivela che il `RecycleElement` la memorizzazione nella cache i risultati di strategia in una velocità di esecuzione ridotta.

È importante tenere presente le conseguenze del [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) strategia di memorizzazione nella cache quando si lavora con le celle personalizzate. Sarà necessario un codice di inizializzazione di cella per l'esecuzione per la creazione di ogni cella, che può essere più volte al secondo. In questo caso, le tecniche di layout che sono stati correttamente in una pagina, ad esempio usando più annidati [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) istanze, diventano i colli di bottiglia delle prestazioni quando sono il programma di installazione e distrutti in tempo reale man mano che l'utente scorre.

<a name="recycleelement" />

### <a name="recycleelement"></a>RecycleElement

Il [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache consente di specificare che il [ `ListView` ](xref:Xamarin.Forms.ListView) proverà a ridurre al minimo la velocità di esecuzione e footprint di memoria, il riciclo delle celle di elenco. Questa modalità non offre sempre un miglioramento delle prestazioni e il test deve essere eseguito per determinare eventuali miglioramenti. Tuttavia, è in genere la scelta preferita e deve essere utilizzato nelle circostanze seguenti:

- Quando ogni cella dispone di un piccolo numero moderato di associazioni.
- Quando ogni cella [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) definisce tutti i dati delle celle.
- Quando ogni cella è ampiamente simile, con il modello della cella rimane invariato.

Durante la virtualizzazione della cella avrà il relativo contesto di associazione aggiornato e pertanto se un'applicazione usa questa modalità è necessario assicurarsi che gli aggiornamenti di contesto dell'associazione sono gestiti in modo appropriato. Tutti i dati relativi alla cella devono provenire dal contesto dell'associazione o, potrebbero verificarsi errori di coerenza. È possibile tramite l'associazione dati per visualizzare i dati delle celle. In alternativa, i dati delle celle devono essere impostati nel `OnBindingContextChanged` esegue l'override, invece che nel costruttore della cella personalizzata, come illustrato nell'esempio di codice seguente:

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

Per altre informazioni, vedere [modifica del contesto di associazione](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes).

In iOS e Android, se le celle renderer personalizzati, è necessario assicurarsi che che sia implementata correttamente la notifica di modifiche. Quando le celle vengono riutilizzate i relativi valori di proprietà cambia il contesto di associazione viene aggiornato a quella di una cella, disponibile con `PropertyChanged` eventi generati. Per altre informazioni, vedere [personalizzazione di un elemento ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>RecycleElement con un DataTemplateSelector

Quando un [ `ListView` ](xref:Xamarin.Forms.ListView) utilizza una [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) per selezionare un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), il [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) la memorizzazione nella cache strategia di non memorizzare nella cache `DataTemplate`s. Al contrario, un `DataTemplate` è selezionato per ogni elemento di dati nell'elenco.

> [!NOTE]
> Il [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache è un prerequisito, introdotta in xamarin. Forms 2.4, che, quando un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) viene chiesto di selezionare un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)ognuna `DataTemplate` deve restituire lo stesso [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) tipo. Si consideri, ad esempio, un [ `ListView` ](xref:Xamarin.Forms.ListView) con un `DataTemplateSelector` che può restituire `MyDataTemplateA` (dove `MyDataTemplateA` restituisce un `ViewCell` typu `MyViewCellA`), o `MyDataTemplateB` (dove `MyDataTemplateB`restituisce un `ViewCell` typu `MyViewCellB`), quando `MyDataTemplateA` restituito deve essere restituito `MyViewCellA` o verrà generata un'eccezione.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

Il [ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) strategia di memorizzazione nella cache si basa sul [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache, garantendo inoltre che quando un [ `ListView` ](xref:Xamarin.Forms.ListView) Usa un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) per selezionare un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), `DataTemplate`s vengono memorizzati nella cache dal tipo di elemento dell'elenco. Pertanto, `DataTemplate`s vengono selezionate una sola volta per ogni tipo di elemento, anziché una volta per ogni istanza dell'elemento.

> [!NOTE]
> Il [ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) strategia di memorizzazione nella cache è un prerequisito che il `DataTemplate`s restituito dal [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) devono usare la [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) costruttore che accetta un `Type`.

### <a name="setting-the-caching-strategy"></a>Definizione della strategia di memorizzazione nella cache

Il [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) viene specificato il valore di enumerazione con un [ `ListView` ](xref:Xamarin.Forms.ListView) overload del costruttore, come illustrato nell'esempio di codice seguente:

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

Ciò ha lo stesso effetto dell'impostazione dell'argomento di strategia di memorizzazione nella cache del costruttore in c#. Si noti che è presente alcun `CachingStrategy` proprietà `ListView`.

#### <a name="setting-the-caching-strategy-in-a-subclassed-listview"></a>Definizione della strategia di memorizzazione nella cache in un ListView sottoclassati

Impostando il `CachingStrategy` attributo da XAML in una sottoclasse [ `ListView` ](xref:Xamarin.Forms.ListView) non produrrà il comportamento desiderato, poiché non esiste alcun `CachingStrategy` proprietà `ListView`. Inoltre, se [XAMLC](~/xamarin-forms/xaml/xamlc.md) è abilitato, verrà generato il seguente messaggio di errore: **Non sono state trovate proprietà, proprietà associabili o eventi per ' CachingStrategy '**

Per risolvere questo problema consiste nello specificare un costruttore nel sottoclassato [ `ListView` ](xref:Xamarin.Forms.ListView) che accetta un [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) parametro e lo passa alla classe di base:

```csharp
public class CustomListView : ListView
{
  public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
  {
  }
  ...
}
```

Il [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) valore di enumerazione può essere specificato da XAML usando il `x:Arguments` sintassi:

```xaml
<local:CustomListView>
  <x:Arguments>
    <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
  </x:Arguments>
</local:CustomListView>
```

<a name="improving-performance" />

## <a name="improving-listview-performance"></a>Miglioramento delle prestazioni di ListView

Sono disponibili molte tecniche per migliorare le prestazioni di un `ListView`:

-  Associare il `ItemsSource` proprietà da un' `IList<T>` raccolta invece di un `IEnumerable<T>` raccolta, perché `IEnumerable<T>` raccolte non supportano l'accesso casuale.
-  Usare le celle predefinite (ad esempio `TextCell`  /  `SwitchCell` ) invece di `ViewCell` ogni volta che è possibile.
-  Usare meno elementi. Ad esempio è consigliabile usare un singolo `FormattedString` etichetta anziché più etichette.
-  Sostituire il `ListView` con un `TableView` quando si visualizzano dati omogenei: vale a dire i dati di diverso tipo.
-  Limitare l'utilizzo dei [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) (metodo). Se eccessivo, avrà alcun effetto sulle prestazioni.
-  In Android, evitare di impostare un `ListView`della visibilità di separatore di riga o a colori dopo che è stata creata un'istanza, poiché comporta una riduzione delle prestazioni di grandi dimensioni.
-  Evitare di modificare il layout della cella in base il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext). Ciò comporta costi di inizializzazione e layout di grandi dimensioni.
-  Evitare gerarchie di layout eccessivamente annidato. Uso `AbsoluteLayout` o `Grid` per contribuire a ridurre l'annidamento.
-  Evitare di specifiche `LayoutOptions` diverso da `Fill` (riempimento è di economico per il calcolo).
-  Evitare di inserire un `ListView` all'interno di un `ScrollView` per i motivi seguenti:
    - Il `ListView` implementa un proprio lo scorrimento.
    - Il `ListView` non riceveranno i movimenti di qualsiasi tipo, come verranno gestiti dall'elemento padre `ScrollView`.
    - Il `ListView` può presentare un'intestazione personalizzata e un piè di pagina che consente di scorrere gli elementi dell'elenco, potenzialmente offrendo le funzionalità di `ScrollView` è stata usata per. Per altre informazioni, vedere [informazioni sulle intestazioni e piè di pagina](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#Headers_and_Footers).
-  Se occorre una progettazione molto specifica e complessa presentata in celle del, prendere in considerazione un renderer personalizzato.

`AbsoluteLayout` è in grado di eseguire i layout senza una chiamata di singola misura. Questo rende molto potente per le prestazioni. Se `AbsoluteLayout` non può essere usato, prendere in considerazione [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout). Se si usa `RelativeLayout`, passando direttamente i vincoli saranno notevolmente più veloce rispetto all'uso dell'API di espressione. Ciò avviene perché l'espressione API Usa JIT e in iOS è illustrata la struttura deve essere interpretato, che è più lento. L'espressione API è adatto per layout di pagina in cui richiesto solo su layout iniziale e la rotazione, ma in `ListView`, in cui viene eseguito continuamente durante lo scorrimento, influisce negativamente sulle prestazioni.

Creazione di un renderer personalizzato per un [ `ListView` ](xref:Xamarin.Forms.ListView) o le relative celle è un approccio per ridurre l'effetto di calcoli di layout su prestazioni di scorrimento. Per altre informazioni, vedere [personalizzazione di un ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) e [personalizzazione di un elemento ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).


## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazione di Renderer personalizzato (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [Elemento ViewCell Renderer personalizzati (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
