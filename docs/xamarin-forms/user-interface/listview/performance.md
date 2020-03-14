---
title: Prestazioni di ListView
description: ListView è una visualizzazione potente per la visualizzazione dei dati, ma presenta alcune limitazioni. Questo articolo illustra come garantire prestazioni ottimali con un ListView di xamarin. Forms in un'applicazione.
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: ed920db129d2af203046a648c0069580f99a295e
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305339"
---
# <a name="listview-performance"></a>Prestazioni di ListView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

Quando si scrivono applicazioni per dispositivi mobili, è importante delle prestazioni. Gli utenti sono ormai si aspettano uno scorrimento fluido e tempi di caricamento rapido. Non soddisfa le aspettative degli utenti verranno costo di valutazioni nell'archivio di applicazione o nel caso di un'applicazione line-of-business, costo del tempo e denaro.

Il [`ListView`](xref:Xamarin.Forms.ListView) Novell. Forms è una vista potente per la visualizzazione dei dati, ma presenta alcune limitazioni. Lo scorrimento delle prestazioni può risentirne quando si utilizzano celle personalizzate, soprattutto quando contengono gerarchie di visualizzazione profondamente annidate o si utilizzano determinati layout che richiedono una misurazione complessa. Fortunatamente, esistono tecniche che è possibile utilizzare per evitare una riduzione delle prestazioni.

## <a name="caching-strategy"></a>Strategia di memorizzazione nella cache

I ListView vengono spesso utilizzati per visualizzare un numero maggiore di dati rispetto a quelli dello schermo. Ad esempio, un'app musicale potrebbe avere una libreria di canzoni con migliaia di voci. La creazione di un elemento per ogni voce può comportare una notevole perdita di memoria e prestazioni scarse. Per creare ed eliminare costantemente le righe, è necessario che l'applicazione crei un'istanza e pulisca costantemente gli oggetti, che potrebbero anche risultare scarsi.

Per conservare la memoria, i [`ListView`](xref:Xamarin.Forms.ListView) nativi equivalenti per ogni piattaforma includono funzionalità predefinite per il riutilizzo delle righe. Solo le celle visibili sullo schermo vengono caricate in memoria e il **contenuto** viene caricato nelle celle esistenti. Questo modello impedisce all'applicazione di creare istanze di migliaia di oggetti, risparmiando tempo e memoria.

Novell. Forms consente il riutilizzo delle celle [`ListView`](xref:Xamarin.Forms.ListView) tramite l'enumerazione [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) , che presenta i valori seguenti:

```csharp
public enum ListViewCachingStrategy
{
    RetainElement,   // the default value
    RecycleElement,
    RecycleElementAndDataTemplate
}
```

> [!NOTE]
> Il piattaforma UWP (Universal Windows Platform) (UWP) ignora la strategia di [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) Caching, perché usa sempre la memorizzazione nella cache per migliorare le prestazioni. Per impostazione predefinita, pertanto, si comporta come se venisse applicata la strategia di memorizzazione nella cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) .

### <a name="retainelement"></a>RetainElement

La strategia di memorizzazione nella cache [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) specifica che la [`ListView`](xref:Xamarin.Forms.ListView) genererà una cella per ogni elemento nell'elenco ed è il comportamento predefinito di `ListView`. Deve essere usato nelle circostanze seguenti:

- Ogni cella ha un numero elevato di binding (20-30 +).
- Il modello di cella viene modificato di frequente.
- Il test rivela che la strategia di memorizzazione nella cache `RecycleElement` comporta una riduzione della velocità di esecuzione.

È importante riconoscere le conseguenze della strategia di [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) Caching quando si utilizzano celle personalizzate. Sarà necessario un codice di inizializzazione di cella per l'esecuzione per la creazione di ogni cella, che può essere più volte al secondo. In questa circostanza, le tecniche di layout che erano ottimali in una pagina, ad esempio l'uso di più istanze di [`StackLayout`](xref:Xamarin.Forms.StackLayout) annidate, diventano colli di bottiglia delle prestazioni quando vengono configurate ed eliminate in tempo reale quando l'utente scorre.

### <a name="recycleelement"></a>RecycleElement

La strategia di memorizzazione nella cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) specifica che il [`ListView`](xref:Xamarin.Forms.ListView) tenterà di ridurre al minimo il footprint di memoria e la velocità di esecuzione riciclo delle celle dell'elenco. Questa modalità non sempre offre un miglioramento delle prestazioni e deve essere eseguito il test per determinare eventuali miglioramenti. Tuttavia, si tratta della scelta preferita e deve essere usata nelle circostanze seguenti:

- Ogni cella ha un numero limitato di binding.
- Il [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) di ogni cella definisce tutti i dati delle celle.
- Ogni cella è in gran parte simile, con il modello di cella che non cambia.

Durante la virtualizzazione della cella avrà il relativo contesto di associazione aggiornato e pertanto se un'applicazione usa questa modalità è necessario assicurarsi che gli aggiornamenti di contesto dell'associazione sono gestiti in modo appropriato. Tutti i dati relativi alla cella devono provenire dal contesto dell'associazione o, potrebbero verificarsi errori di coerenza. Questo problema può essere evitato utilizzando data binding per visualizzare i dati delle celle. In alternativa, i dati delle celle devono essere impostati nell'override del `OnBindingContextChanged`, anziché nel costruttore della cella personalizzata, come illustrato nell'esempio di codice seguente:

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

Per ulteriori informazioni, vedere [Binding context changes](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes).

In iOS e Android, se le celle renderer personalizzati, è necessario assicurarsi che che sia implementata correttamente la notifica di modifiche. Quando le celle vengono riutilizzate, i valori delle proprietà verranno modificati quando il contesto di associazione viene aggiornato a quello di una cella disponibile, con `PropertyChanged` eventi generati. Per altre informazioni, vedere [personalizzazione di un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>RecycleElement con un DataTemplateSelector

Quando un [`ListView`](xref:Xamarin.Forms.ListView) usa un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) per selezionare un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), la strategia di memorizzazione nella cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) non memorizza nella cache `DataTemplate`s. Viene invece selezionato un `DataTemplate` per ogni elemento di dati nell'elenco.

> [!NOTE]
> La strategia di memorizzazione nella cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) dispone di un prerequisito, introdotto in Novell. forms 2,4, che quando viene richiesto a un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) di selezionare un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che ogni `DataTemplate` deve restituire lo stesso tipo di [`ViewCell`](xref:Xamarin.Forms.ViewCell) . Ad esempio, data una [`ListView`](xref:Xamarin.Forms.ListView) con un `DataTemplateSelector` che può restituire `MyDataTemplateA` (dove `MyDataTemplateA` restituisce un `ViewCell` di tipo `MyViewCellA`) o `MyDataTemplateB` (dove `MyDataTemplateB` restituisce un `ViewCell` di tipo `MyViewCellB`), quando viene restituito `MyDataTemplateA`, deve restituire `MyViewCellA` o verrà generata un'eccezione.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

La strategia di memorizzazione nella cache [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) si basa sulla strategia di memorizzazione nella cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) garantendo in tal modo che, quando una [`ListView`](xref:Xamarin.Forms.ListView) utilizza un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) per selezionare un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), i `DataTemplate`vengono memorizzati nella cache dal tipo di elemento nell'elenco. Pertanto, `DataTemplate`s vengono selezionati una volta per ogni tipo di elemento, anziché una volta per ogni istanza dell'elemento.

> [!NOTE]
> La strategia di memorizzazione nella cache [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) dispone di un prerequisito che i `DataTemplate`restituiti dal [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) devono usare il costruttore [`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) che accetta un `Type`.

### <a name="set-the-caching-strategy"></a>Impostare la strategia di memorizzazione nella cache

Il valore di enumerazione [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) viene specificato con un overload del costruttore [`ListView`](xref:Xamarin.Forms.ListView) , come illustrato nell'esempio di codice seguente:

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

In XAML, impostare l'attributo `CachingStrategy` come illustrato nel codice XAML seguente:

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

Questo metodo ha lo stesso effetto dell'impostazione dell'argomento della strategia di memorizzazione nella cache nel C#costruttore in.

#### <a name="set-the-caching-strategy-in-a-subclassed-listview"></a>Impostare la strategia di memorizzazione nella cache in un controllo ListView sottoclassato

L'impostazione dell'attributo `CachingStrategy` da XAML in una [`ListView`](xref:Xamarin.Forms.ListView) con una sottoclasse non produrrà il comportamento desiderato, perché non è presente alcuna proprietà `CachingStrategy` in `ListView`. Inoltre, se [XAMLC](~/xamarin-forms/xaml/xamlc.md) è abilitato, verrà generato il messaggio di errore seguente: **Nessuna proprietà, proprietà associabile o evento trovato per ' CachingStrategy '**

La soluzione a questo problema consiste nel specificare un costruttore per la [`ListView`](xref:Xamarin.Forms.ListView) sottoclassata che accetta un parametro di [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) e lo passa alla classe di base:

```csharp
public class CustomListView : ListView
{
    public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
    {
    }
    ...
}
```

Il valore di enumerazione [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) può quindi essere specificato da XAML tramite la sintassi `x:Arguments`:

```xaml
<local:CustomListView>
    <x:Arguments>
        <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
    </x:Arguments>
</local:CustomListView>
```

## <a name="listview-performance-suggestions"></a>Suggerimenti sulle prestazioni di ListView

Sono disponibili diverse tecniche per migliorare le prestazioni di un `ListView`. I suggerimenti seguenti possono migliorare le prestazioni del controllo ListView

- Associare la proprietà `ItemsSource` a una raccolta di `IList<T>` invece che a una raccolta di `IEnumerable<T>`, perché le raccolte di `IEnumerable<T>` non supportano l'accesso casuale.
- Usare le celle predefinite, ad esempio `TextCell` / `SwitchCell`, anziché `ViewCell` ogni volta che è possibile.
- Usare meno elementi. Si supponga, ad esempio, di utilizzare una singola etichetta di `FormattedString` anziché più etichette.
- Sostituire il `ListView` con un `TableView` quando si visualizzano dati non omogenei, ovvero dati di tipi diversi.
- Limitare l'utilizzo del metodo [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) . Se eccessivo, avrà alcun effetto sulle prestazioni.
- In Android evitare di impostare la visibilità o il colore del separatore di riga di un `ListView`dopo che ne è stata creata un'istanza, in quanto comporta un notevole calo delle prestazioni.
- Evitare di modificare il layout della cella in base al [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext). La modifica del layout comporta costi elevati di misurazione e inizializzazione.
- Evitare gerarchie di layout eccessivamente annidato. Usare `AbsoluteLayout` o `Grid` per ridurre la nidificazione.
- Evitare `LayoutOptions` specifici diversi da `Fill` (`Fill` è il più economico da calcolare).
- Evitare di inserire un `ListView` all'interno di un `ScrollView` per i motivi seguenti:
  - Il `ListView` implementa il proprio scorrimento.
  - Il `ListView` non riceverà alcun movimento, poiché verrà gestito dall'`ScrollView`padre.
  - Il `ListView` può presentare un'intestazione e un piè di pagina personalizzati che scorrono con gli elementi dell'elenco, offrendo potenzialmente la funzionalità per cui è stata usata la `ScrollView`. Per ulteriori informazioni, vedere [intestazioni e piè](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers)di pagina.
- Si consideri un renderer personalizzato se è necessaria una progettazione specifica e complessa presentata nelle celle.

`AbsoluteLayout` possibile eseguire layout senza una singola chiamata di misura, rendendola estremamente efficiente. Se non è possibile usare `AbsoluteLayout`, prendere in considerazione [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout). Se si utilizza `RelativeLayout`, il passaggio diretto dei vincoli sarà notevolmente più veloce rispetto all'utilizzo dell'API Expression. Questo metodo è più veloce perché l'API Expression USA JIT e in iOS l'albero deve essere interpretato, che è più lento. L'API Expression è adatta per i layout di pagina in cui è necessaria solo per il layout e la rotazione iniziali, ma in `ListView`, in cui viene eseguito costantemente durante lo scorrimento, danneggia le prestazioni.

La creazione di un renderer personalizzato per un [`ListView`](xref:Xamarin.Forms.ListView) o le relative celle è un approccio per ridurre l'effetto dei calcoli di layout sullo scorrimento delle prestazioni. Per altre informazioni, vedere [personalizzazione di un controllo ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) e [personalizzazione di un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazione renderer personalizzata (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [Renderer personalizzato ViewCell (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
