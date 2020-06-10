---
title: "ListView performance" Description: "Sebbene ListView sia una vista potente per la visualizzazione dei dati, presenta alcune limitazioni. Questo articolo illustra come garantire prestazioni ottimali con Xamarin.Forms ListView in un'applicazione ".
ms. prod: Novell MS. AssetID: 1B085639-652C-4862-86EB-5D55D32B9395 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 12/11/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="listview-performance"></a>Prestazioni di ListView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

Quando si scrivono applicazioni per dispositivi mobili, le prestazioni sono importanti. Gli utenti si aspettano che lo scorrimento sia uniforme e tempi di caricamento rapidi. Il mancato rispetto delle aspettative degli utenti costerà le classificazioni nello Store di applicazioni o, nel caso di un'applicazione line-of-business, costerà tempo e denaro per l'organizzazione.

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) È una vista potente per la visualizzazione dei dati, ma presenta alcune limitazioni. Lo scorrimento delle prestazioni può risentirne quando si utilizzano celle personalizzate, soprattutto quando contengono gerarchie di visualizzazione profondamente annidate o si utilizzano determinati layout che richiedono una misurazione complessa. Fortunatamente, sono disponibili tecniche che è possibile usare per evitare scarse prestazioni.

## <a name="caching-strategy"></a>Strategia di memorizzazione nella cache

I ListView vengono spesso utilizzati per visualizzare un numero maggiore di dati rispetto a quelli dello schermo. Ad esempio, un'app musicale potrebbe avere una libreria di canzoni con migliaia di voci. La creazione di un elemento per ogni voce può comportare una notevole perdita di memoria e prestazioni scarse. Per creare ed eliminare costantemente le righe, è necessario che l'applicazione crei un'istanza e pulisca costantemente gli oggetti, che potrebbero anche risultare scarsi.

Per conservare la memoria, gli [`ListView`](xref:Xamarin.Forms.ListView) equivalenti nativi per ogni piattaforma hanno funzionalità predefinite per il riutilizzo delle righe. Solo le celle visibili sullo schermo vengono caricate in memoria e il **contenuto** viene caricato nelle celle esistenti. Questo modello impedisce all'applicazione di creare istanze di migliaia di oggetti, risparmiando tempo e memoria.

Xamarin.Formsconsente il [`ListView`](xref:Xamarin.Forms.ListView) riutilizzo delle celle tramite l' [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) enumerazione, che presenta i valori seguenti:

```csharp
public enum ListViewCachingStrategy
{
    RetainElement,   // the default value
    RecycleElement,
    RecycleElementAndDataTemplate
}
```

> [!NOTE]
> Il piattaforma UWP (Universal Windows Platform) (UWP) ignora la [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) strategia di memorizzazione nella cache, perché usa sempre la memorizzazione nella cache per migliorare le prestazioni. Per impostazione predefinita, pertanto, si comporta come se venisse [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) applicata la strategia di memorizzazione nella cache.

### <a name="retainelement"></a>Mantienielement

La [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) strategia di memorizzazione nella cache specifica che genererà [`ListView`](xref:Xamarin.Forms.ListView) una cella per ogni elemento nell'elenco ed è il comportamento predefinito `ListView` . Deve essere usato nelle circostanze seguenti:

- Ogni cella ha un numero elevato di binding (20-30 +).
- Il modello di cella viene modificato di frequente.
- Il test rivela che la `RecycleElement` strategia di memorizzazione nella cache comporta una riduzione della velocità di esecuzione.

È importante riconoscere le conseguenze della [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) strategia di memorizzazione nella cache quando si lavora con celle personalizzate. Il codice di inizializzazione della cella deve essere eseguito per ogni creazione di celle, che può essere più volte al secondo. In questa circostanza, le tecniche di layout che erano ottimali in una pagina, ad esempio l'uso di più istanze nidificate [`StackLayout`](xref:Xamarin.Forms.StackLayout) , diventano colli di bottiglia delle prestazioni quando vengono configurate ed eliminate in tempo reale quando l'utente scorre.

### <a name="recycleelement"></a>Riciclielement

La [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache specifica che tenterà [`ListView`](xref:Xamarin.Forms.ListView) di ridurre al minimo il footprint di memoria e la velocità di esecuzione tramite il riciclo delle celle dell'elenco. Questa modalità non sempre offre un miglioramento delle prestazioni e deve essere eseguito il test per determinare eventuali miglioramenti. Tuttavia, si tratta della scelta preferita e deve essere usata nelle circostanze seguenti:

- Ogni cella ha un numero limitato di binding.
- Ogni cella [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) definisce tutti i dati delle celle.
- Ogni cella è in gran parte simile, con il modello di cella che non cambia.

Durante la virtualizzazione, il contesto di associazione della cella verrà aggiornato e, di conseguenza, se un'applicazione usa questa modalità, deve garantire che gli aggiornamenti del contesto di associazione vengano gestiti in modo appropriato. Tutti i dati relativi alla cella devono provenire dal contesto dell'associazione o possono verificarsi errori di coerenza. Questo problema può essere evitato utilizzando data binding per visualizzare i dati delle celle. In alternativa, i dati delle celle devono essere impostati nell' `OnBindingContextChanged` override, anziché nel costruttore della cella personalizzata, come illustrato nell'esempio di codice seguente:

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

In iOS e Android, se le celle usano renderer personalizzati, devono assicurarsi che la notifica delle modifiche alle proprietà sia implementata correttamente. Quando le celle vengono riutilizzate, i valori delle proprietà verranno modificati quando il contesto di associazione viene aggiornato a quello di una cella disponibile, con la `PropertyChanged` generazione di eventi. Per altre informazioni, vedere [personalizzazione di un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>Riciclaelement con DataTemplateSelector

Quando un oggetto [`ListView`](xref:Xamarin.Forms.ListView) Usa un oggetto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) per selezionare un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , la [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache non memorizza nella cache `DataTemplate` . Viene invece selezionato un oggetto `DataTemplate` per ogni elemento di dati nell'elenco.

> [!NOTE]
> La [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache prevede un prerequisito, introdotto in Xamarin.Forms 2,4, che quando [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) viene richiesto a un oggetto di selezionare un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che `DataTemplate` deve restituire lo stesso [`ViewCell`](xref:Xamarin.Forms.ViewCell) tipo. Ad esempio, dato un oggetto [`ListView`](xref:Xamarin.Forms.ListView) con un `DataTemplateSelector` che può restituire `MyDataTemplateA` (dove `MyDataTemplateA` restituisce un oggetto `ViewCell` di tipo `MyViewCellA` ) o `MyDataTemplateB` (dove `MyDataTemplateB` restituisce un oggetto `ViewCell` di tipo `MyViewCellB` ), quando `MyDataTemplateA` viene restituito, deve restituire `MyViewCellA` o verrà generata un'eccezione.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

La [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) strategia di memorizzazione nella cache si basa sulla [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache garantendo che quando un [`ListView`](xref:Xamarin.Forms.ListView) utilizza un oggetto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) per selezionare un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , i `DataTemplate` vengono memorizzati nella cache in base al tipo di elemento nell'elenco. Pertanto, `DataTemplate` i vengono selezionati una volta per ogni tipo di elemento, anziché una volta per ogni istanza dell'elemento.

> [!NOTE]
> La [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) strategia di memorizzazione nella cache ha un prerequisito per il quale gli oggetti `DataTemplate` restituiti da [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) devono utilizzare il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) costruttore che accetta un oggetto `Type` .

### <a name="set-the-caching-strategy"></a>Impostare la strategia di memorizzazione nella cache

Il [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) valore di enumerazione viene specificato con un [`ListView`](xref:Xamarin.Forms.ListView) Overload del costruttore, come illustrato nell'esempio di codice seguente:

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

Questo metodo ha lo stesso effetto dell'impostazione dell'argomento di strategia di memorizzazione nella cache nel costruttore in C#.

#### <a name="set-the-caching-strategy-in-a-subclassed-listview"></a>Impostare la strategia di memorizzazione nella cache in un controllo ListView sottoclassato

L'impostazione dell' `CachingStrategy` attributo da XAML in una sottoclasse [`ListView`](xref:Xamarin.Forms.ListView) non produrrà il comportamento desiderato, perché non è presente alcuna `CachingStrategy` Proprietà in `ListView` . Inoltre, se [XAMLC](~/xamarin-forms/xaml/xamlc.md) è abilitato, verrà generato il messaggio di errore seguente: **Nessuna proprietà, proprietà associabile o evento trovato per ' CachingStrategy '**

La soluzione a questo problema consiste nel specificare un costruttore sulla sottoclasse [`ListView`](xref:Xamarin.Forms.ListView) che accetta un [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) parametro e lo passa alla classe di base:

```csharp
public class CustomListView : ListView
{
    public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
    {
    }
    ...
}
```

Il [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) valore di enumerazione può quindi essere specificato da XAML usando la `x:Arguments` sintassi:

```xaml
<local:CustomListView>
    <x:Arguments>
        <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
    </x:Arguments>
</local:CustomListView>
```

## <a name="listview-performance-suggestions"></a>Suggerimenti sulle prestazioni di ListView

Sono disponibili diverse tecniche per migliorare le prestazioni di un oggetto `ListView` . I suggerimenti seguenti possono migliorare le prestazioni del controllo ListView

- Associare la `ItemsSource` proprietà a una `IList<T>` raccolta anziché a una `IEnumerable<T>` raccolta, perché le `IEnumerable<T>` raccolte non supportano l'accesso casuale.
- Usare le celle predefinite, ad esempio `TextCell`  /  `SwitchCell` , anziché `ViewCell` ogni volta che è possibile.
- Utilizzare un minor numero di elementi. Si consideri, ad esempio, l'utilizzo di una singola `FormattedString` etichetta anziché di più etichette.
- Sostituire `ListView` con `TableView` quando si visualizzano dati non omogenei, ovvero dati di tipi diversi.
- Limitare l'utilizzo del [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) metodo. In caso di sovrautilizzo, le prestazioni risulteranno ridotte.
- In Android evitare di impostare la `ListView` visibilità o il colore del separatore di riga dopo che è stata creata un'istanza, in quanto comporta un notevole calo delle prestazioni.
- Evitare di modificare il layout delle celle in base a [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) . La modifica del layout comporta costi elevati di misurazione e inizializzazione.
- Evitare gerarchie di layout profondamente nidificate. Usare `AbsoluteLayout` o `Grid` per ridurre la nidificazione.
- Evitare specifici `LayoutOptions` diversi da `Fill` ( `Fill` è il più economico da calcolare).
- Evitare di inserire un oggetto `ListView` all'interno `ScrollView` di per i motivi seguenti:
  - `ListView`Implementa il proprio scorrimento.
  - `ListView`Non riceverà alcun movimento, poiché verrà gestito dall'elemento padre `ScrollView` .
  - `ListView`Può presentare un'intestazione e un piè di pagina personalizzati che scorrono con gli elementi dell'elenco, offrendo potenzialmente la funzionalità per la quale `ScrollView` è stato utilizzato. Per ulteriori informazioni, vedere [intestazioni e piè](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers)di pagina.
- Si consideri un renderer personalizzato se è necessaria una progettazione specifica e complessa presentata nelle celle.

`AbsoluteLayout`è possibile eseguire layout senza una singola chiamata di misura, rendendola estremamente efficiente. Se `AbsoluteLayout` non è possibile utilizzare, prendere in considerazione [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) . Se `RelativeLayout` si usa, il passaggio diretto dei vincoli sarà notevolmente più veloce rispetto all'uso dell'API Expression. Questo metodo è più veloce perché l'API Expression USA JIT e in iOS l'albero deve essere interpretato, che è più lento. L'API Expression è adatta per i layout di pagina in cui è necessaria solo per il layout iniziale e la rotazione, ma in `ListView` , in cui viene eseguito costantemente durante lo scorrimento, danneggia le prestazioni.

La creazione di un renderer personalizzato per un [`ListView`](xref:Xamarin.Forms.ListView) o le relative celle è un approccio per ridurre l'effetto dei calcoli di layout sulle prestazioni di scorrimento. Per altre informazioni, vedere [personalizzazione di un controllo ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) e [personalizzazione di un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazione renderer personalizzata (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [Renderer personalizzato ViewCell (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
