---
title: Prestazioni di ListView
description: ListView è una visualizzazione potente per la visualizzazione dei dati, ma presenta alcune limitazioni. Questo articolo illustra come garantire prestazioni ottimali con un ListView di Xamarin.Forms in un'applicazione.
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: ed920db129d2af203046a648c0069580f99a295e
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998186"
---
# <a name="listview-performance"></a>Prestazioni di ListView

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

Quando si scrivono applicazioni per dispositivi mobili, è importante delle prestazioni. Gli utenti sono ormai si aspettano uno scorrimento fluido e tempi di caricamento rapido. Non soddisfa le aspettative degli utenti verranno costo di valutazioni nell'archivio di applicazione o nel caso di un'applicazione line-of-business, costo del tempo e denaro.

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) è una vista potente per la visualizzazione dei dati, ma presenta alcune limitazioni. Lo scorrimento delle prestazioni può risentirne quando si utilizzano celle personalizzate, soprattutto quando contengono gerarchie di visualizzazione profondamente annidate o si utilizzano determinati layout che richiedono una misurazione complessa. Fortunatamente, esistono tecniche che è possibile utilizzare per evitare una riduzione delle prestazioni.

## <a name="caching-strategy"></a>Strategia di memorizzazione nella cache

I ListView vengono spesso utilizzati per visualizzare un numero maggiore di dati rispetto a quelli dello schermo. Ad esempio, un'app musicale potrebbe avere una libreria di canzoni con migliaia di voci. La creazione di un elemento per ogni voce può comportare una notevole perdita di memoria e prestazioni scarse. Per creare ed eliminare costantemente le righe, è necessario che l'applicazione crei un'istanza e pulisca costantemente gli oggetti, che potrebbero anche risultare scarsi.

Per conservare la memoria, gli equivalenti nativi [`ListView`](xref:Xamarin.Forms.ListView) per ogni piattaforma hanno funzionalità predefinite per il riutilizzo delle righe. Solo le celle visibili sullo schermo vengono caricate in memoria e il **contenuto** viene caricato in celle esistenti. Questo modello impedisce all'applicazione di creare istanze di migliaia di oggetti, risparmiando tempo e memoria.

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) consente il riutilizzo [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) delle celle tramite l'enumerazione, che presenta i valori seguenti:

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

Il [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) strategia di memorizzazione nella cache consente di specificare che il [ `ListView` ](xref:Xamarin.Forms.ListView) genererà una cella per ogni elemento nell'elenco e il valore predefinito è `ListView` comportamento. Deve essere usato nelle circostanze seguenti:

- Ogni cella ha un numero elevato di binding (20-30 +).
- Il modello di cella viene modificato di frequente.
- Il test rivela che `RecycleElement` la strategia di memorizzazione nella cache comporta una riduzione della velocità di esecuzione.

È importante tenere presente le conseguenze del [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) strategia di memorizzazione nella cache quando si lavora con le celle personalizzate. Sarà necessario un codice di inizializzazione di cella per l'esecuzione per la creazione di ogni cella, che può essere più volte al secondo. In questa circostanza, le tecniche di layout che erano ottimali in una pagina, ad [`StackLayout`](xref:Xamarin.Forms.StackLayout) esempio l'uso di più istanze nidificate, diventano colli di bottiglia delle prestazioni quando vengono configurate ed eliminate in tempo reale quando l'utente scorre.

### <a name="recycleelement"></a>RecycleElement

Il [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache consente di specificare che il [ `ListView` ](xref:Xamarin.Forms.ListView) proverà a ridurre al minimo la velocità di esecuzione e footprint di memoria, il riciclo delle celle di elenco. Questa modalità non sempre offre un miglioramento delle prestazioni e deve essere eseguito il test per determinare eventuali miglioramenti. Tuttavia, si tratta della scelta preferita e deve essere usata nelle circostanze seguenti:

- Ogni cella ha un numero limitato di binding.
- Ogni cella [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) definisce tutti i dati delle celle.
- Ogni cella è in gran parte simile, con il modello di cella che non cambia.

Durante la virtualizzazione della cella avrà il relativo contesto di associazione aggiornato e pertanto se un'applicazione usa questa modalità è necessario assicurarsi che gli aggiornamenti di contesto dell'associazione sono gestiti in modo appropriato. Tutti i dati relativi alla cella devono provenire dal contesto dell'associazione o, potrebbero verificarsi errori di coerenza. Questo problema può essere evitato utilizzando data binding per visualizzare i dati delle celle. In alternativa, i dati delle celle devono essere impostati nel `OnBindingContextChanged` esegue l'override, invece che nel costruttore della cella personalizzata, come illustrato nell'esempio di codice seguente:

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
> Il [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache è un prerequisito, introdotta in Xamarin.Forms 2.4, che, quando un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) viene chiesto di selezionare un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)ognuna `DataTemplate` deve restituire lo stesso [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) tipo. Si consideri, ad esempio, un [ `ListView` ](xref:Xamarin.Forms.ListView) con un `DataTemplateSelector` che può restituire `MyDataTemplateA` (dove `MyDataTemplateA` restituisce un `ViewCell` typu `MyViewCellA`), o `MyDataTemplateB` (dove `MyDataTemplateB`restituisce un `ViewCell` typu `MyViewCellB`), quando `MyDataTemplateA` restituito deve essere restituito `MyViewCellA` o verrà generata un'eccezione.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

Il [ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) strategia di memorizzazione nella cache si basa sul [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache, garantendo inoltre che quando un [ `ListView` ](xref:Xamarin.Forms.ListView) Usa un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) per selezionare un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), `DataTemplate`s vengono memorizzati nella cache dal tipo di elemento dell'elenco. Pertanto, `DataTemplate`s vengono selezionate una sola volta per ogni tipo di elemento, anziché una volta per ogni istanza dell'elemento.

> [!NOTE]
> Il [ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) strategia di memorizzazione nella cache è un prerequisito che il `DataTemplate`s restituito dal [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) devono usare la [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) costruttore che accetta un `Type`.

### <a name="set-the-caching-strategy"></a>Impostare la strategia di memorizzazione nella cache

Il [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) viene specificato il valore di enumerazione con un [ `ListView` ](xref:Xamarin.Forms.ListView) overload del costruttore, come illustrato nell'esempio di codice seguente:

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

In XAML, impostare l' `CachingStrategy` attributo come illustrato nel codice XAML seguente:

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

L'impostazione `CachingStrategy` dell'attributo da XAML in una [`ListView`](xref:Xamarin.Forms.ListView) sottoclasse non produrrà il comportamento desiderato, perché non è presente `CachingStrategy` alcuna proprietà `ListView`in. Inoltre, se [XAMLC](~/xamarin-forms/xaml/xamlc.md) è abilitato, verrà generato il seguente messaggio di errore: **Non sono state trovate proprietà, proprietà associabili o eventi per ' CachingStrategy '**

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

## <a name="listview-performance-suggestions"></a>Suggerimenti sulle prestazioni di ListView

Sono disponibili diverse tecniche per migliorare le prestazioni di un `ListView`oggetto. I suggerimenti seguenti possono migliorare le prestazioni del controllo ListView

- Associare il `ItemsSource` proprietà da un' `IList<T>` raccolta invece di un `IEnumerable<T>` raccolta, perché `IEnumerable<T>` raccolte non supportano l'accesso casuale.
- Usare le celle `TextCell` predefinite, ad esempio  /  `SwitchCell` , anziché `ViewCell` ogni volta che è possibile.
- Usare meno elementi. Si consideri, ad esempio `FormattedString` , l'utilizzo di una singola etichetta anziché di più etichette.
- Sostituire il `ListView` con un `TableView` quando si visualizzano dati omogenei: vale a dire i dati di diverso tipo.
- Limitare l'utilizzo dei [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) (metodo). Se eccessivo, avrà alcun effetto sulle prestazioni.
- In Android, evitare di impostare un `ListView`della visibilità di separatore di riga o a colori dopo che è stata creata un'istanza, poiché comporta una riduzione delle prestazioni di grandi dimensioni.
- Evitare di modificare il layout della cella in base il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext). La modifica del layout comporta costi elevati di misurazione e inizializzazione.
- Evitare gerarchie di layout eccessivamente annidato. Uso `AbsoluteLayout` o `Grid` per contribuire a ridurre l'annidamento.
- Evitare specifici `LayoutOptions` `Fill` diversi da `Fill` (è il più economico da calcolare).
- Evitare di inserire un `ListView` all'interno di un `ScrollView` per i motivi seguenti:
  - Il `ListView` implementa un proprio lo scorrimento.
  - Il `ListView` non riceveranno i movimenti di qualsiasi tipo, come verranno gestiti dall'elemento padre `ScrollView`.
  - Il `ListView` può presentare un'intestazione personalizzata e un piè di pagina che consente di scorrere gli elementi dell'elenco, potenzialmente offrendo le funzionalità di `ScrollView` è stata usata per. Per ulteriori informazioni, vedere [intestazioni e piè](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers)di pagina.
- Si consideri un renderer personalizzato se è necessaria una progettazione specifica e complessa presentata nelle celle.

`AbsoluteLayout`è possibile eseguire layout senza una singola chiamata di misura, rendendola estremamente efficiente. Se `AbsoluteLayout` non può essere usato, prendere in considerazione [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout). Se si usa `RelativeLayout`, passando direttamente i vincoli saranno notevolmente più veloce rispetto all'uso dell'API di espressione. Questo metodo è più veloce perché l'API Expression USA JIT e in iOS l'albero deve essere interpretato, che è più lento. L'espressione API è adatto per layout di pagina in cui richiesto solo su layout iniziale e la rotazione, ma in `ListView`, in cui viene eseguito continuamente durante lo scorrimento, influisce negativamente sulle prestazioni.

Creazione di un renderer personalizzato per un [ `ListView` ](xref:Xamarin.Forms.ListView) o le relative celle è un approccio per ridurre l'effetto di calcoli di layout su prestazioni di scorrimento. Per altre informazioni, vedere [personalizzazione di un ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) e [personalizzazione di un elemento ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazione di Renderer personalizzato (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [Elemento ViewCell Renderer personalizzati (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
