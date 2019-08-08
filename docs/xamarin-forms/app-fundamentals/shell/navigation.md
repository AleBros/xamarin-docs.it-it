---
title: Navigazione nella shell Xamarin.Forms
description: Le applicazioni shell Xamarin.Forms possono utilizzare un'esperienza di navigazione basata su URI che consente di passare a qualsiasi pagina nell'applicazione senza dover seguire una gerarchia di navigazione specifica.
ms.prod: xamarin
ms.assetid: 57079D89-D1CB-48BD-9FEE-539CEC29EABB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 4c0fc5edd309d5ed8103c5fc3e33817001049241
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739262"
---
# <a name="xamarinforms-shell-navigation"></a>Navigazione nella shell Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

La shell Xamarin.Forms include un'esperienza di navigazione basata su URI che usa le route per il passaggio a qualsiasi pagina nell'applicazione, senza dover seguire una gerarchia di navigazione specifica. Offre inoltre la possibilità di navigare all'indietro senza dover visitare tutte le pagine dello stack di navigazione.

`Shell` definisce le proprietà correlate alla navigazione seguenti:

- `BackButtonBehavior`, di tipo `BackButtonBehavior`, una proprietà associata che definisce il comportamento del pulsante Indietro.
- `CurrentItem`, di tipo `FlyoutItem`, l'oggetto `FlyoutItem` attualmente selezionato.
- `CurrentState`, di tipo `ShellNavigationState`, lo stato di navigazione corrente di `Shell`.
- `Current`, di tipo `Shell`, un alias con casting del tipo per `Application.Current.MainPage`.

Le proprietà `BackButtonBehavior`, `CurrentItem` e `CurrentState` sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) e ciò significa che tali proprietà possono essere destinazioni di data binding.

La navigazione viene eseguita richiamando il metodo `GoToAsync` dalla classe `Shell`. Quando la navigazione sta per essere eseguita viene generato un evento `Navigating` e al completamento della navigazione viene generato un evento `Navigated`.

> [!NOTE]
> La navigazione può comunque essere eseguita in un'applicazione shell Xamarin.Forms usando la proprietà [Navigation](xref:Xamarin.Forms.NavigableElement.Navigation). Per altre informazioni, vedere [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="routes"></a>Route

La navigazione in un'applicazione shell avviene specificando un URI a cui passare. Gli URI di navigazione possono avere tre componenti:

- Una *route*, che definisce il percorso del contenuto che fa parte della gerarchia visiva della shell.
- Una *pagina*. Se le pagine non sono presenti nella gerarchia visiva della shell, è possibile eseguirne il push nello stack di navigazione da qualsiasi posizione all'interno di un'applicazione shell. Una pagina di dettagli di un elemento, ad esempio, non viene definita nella gerarchia visiva della shell, ma è possibile eseguirne il push nello stack di navigazione in base alle esigenze.
- Uno o più *parametri di query*. I parametri di query sono parametri che è possibile passare alla pagina di destinazione durante la navigazione.

Quando un URI di navigazione include tutti e tre i componenti, la struttura è: //route/page?queryParameters

### <a name="register-routes"></a>Registrare le route

Le route possono essere definite negli oggetti `FlyoutItem`, `Tab` e `ShellContent` tramite la relativa proprietà `Route`:

```xaml
<Shell ...>
    <FlyoutItem ...
                Route="animals">
        <Tab ...
             Route="domestic">
            <ShellContent ...
                          Route="cats" />
            <ShellContent ...
                          Route="dogs" />
        </Tab>
        <ShellContent ...
                      Route="monkeys" />
        <ShellContent ...
                      Route="elephants" />  
        <ShellContent ...
                      Route="bears" />
    </FlyoutItem>
    <ShellContent ...
                  Route="about" />                  
    ...
</Shell>
```

> [!NOTE]
> A tutti gli elementi nella gerarchia della shell è associata una route. Se una route non viene impostata dallo sviluppatore, viene generata in fase di esecuzione. Tuttavia, non c'è garanzia che le route generate siano coerenti tra sessioni diverse dell'applicazione.

Questo esempio, relativo all'applicazione di esempio, crea la gerarchia di route seguente, che può essere usata per la navigazione a livello di codice:

```
animals
  domestic
    cats
    dogs
  monkeys
  elephants
  bears
about
```

Per passare all'oggetto `ShellContent` per la route `dogs`, l'URI della route assoluto è `//animals/domestic/dogs`. Analogamente, per passare all'oggetto `ShellContent` per la route `about`, l'URI della route assoluto è `//about`.

> [!IMPORTANT]
> Sono consentiti nomi duplicati di route. Tuttavia, non sono consentite route duplicate. Se viene rilevata una route duplicata, viene generata un'eccezione `ArgumentException` all'avvio dell'applicazione.

#### <a name="register-page-routes"></a>Registrare le route di pagine

Nel costruttore della sottoclasse Shell, o in qualsiasi altra posizione eseguita prima che venga richiamata una route, è possibile registrare in modo esplicito altre route per tutte le pagine che non sono rappresentate nella gerarchia visiva della shell:

```csharp
Routing.RegisterRoute("monkeydetails", typeof(MonkeyDetailPage));
Routing.RegisterRoute("beardetails", typeof(BearDetailPage));
Routing.RegisterRoute("catdetails", typeof(CatDetailPage));
Routing.RegisterRoute("dogdetails", typeof(DogDetailPage));
Routing.RegisterRoute("elephantdetails", typeof(ElephantDetailPage));
```

Questo esempio registra come route le pagine di dettagli degli elementi che non sono definite nella sottoclasse Shell. È possibile spostarsi quindi in queste pagine usando la navigazione basata su URI, da un punto qualsiasi all'interno dell'applicazione. Le route per queste pagine sono dette *route globali*.

> [!NOTE]
> Per le pagine le cui route sono state registrate con il metodo `Routing.RegisterRoute`, è possibile annullare la registrazione con il metodo `Routing.UnRegisterRoute`, se necessario.

In alternativa, le pagine possono essere registrate in gerarchie di route diverse, se necessario:

```csharp
Routing.RegisterRoute("monkeys/details", typeof(MonkeyDetailPage));
Routing.RegisterRoute("bears/details", typeof(BearDetailPage));
Routing.RegisterRoute("cats/details", typeof(CatDetailPage));
Routing.RegisterRoute("dogs/details", typeof(DogDetailPage));
Routing.RegisterRoute("elephants/details", typeof(ElephantDetailPage));
```

Questo esempio abilita la navigazione contestuale tra le pagine, con la quale se si passa alla route `details` dalla pagina relativa a `monkeys` viene visualizzato l'oggetto `MonkeyDetailPage`. Analogamente, se si passa alla route `details` dalla pagina relativa a `elephants` viene visualizzato l'oggetto `ElephantDetailPage`.

> [!IMPORTANT]
> Attualmente sono consentiti i nomi di route duplicati quando si usa il metodo `Routing.RegisterRoute` e la registrazione di un duplicato sovrascrive la registrazione precedente.

## <a name="perform-navigation"></a>Eseguire la navigazione

Per eseguire la navigazione, è prima necessario ottenere un riferimento alla sottoclasse `Shell`. Questo riferimento può essere ottenuto eseguendo il cast della proprietà `App.Current.MainPage` in un oggetto `Shell` oppure tramite la proprietà `Shell.Current`. La navigazione può quindi essere eseguita chiamando il metodo `GoToAsync` nell'oggetto `Shell`. Questo metodo consente di passare a `ShellNavigationState` e restituisce un oggetto `Task` che verrà completato al termine dell'animazione relativa alla navigazione. L'oggetto `ShellNavigationState` viene costruito dal metodo `GoToAsync`, da un oggetto `string` o da un oggetto `Uri` e la sua proprietà `Location` è impostata sull'argomento `string` o `Uri`.

Quando si passa a una route dalla gerarchia visiva della shell, non viene creato uno stack di navigazione. Tuttavia, quando si passa a una pagina non presente nella gerarchia visiva della shell, lo stack di navigazione viene creato.

> [!NOTE]
> Lo stato corrente della navigazione di `Shell` può essere recuperato tramite la proprietà `Shell.Current.CurrentState`, che include l'URI della route visualizzata nella proprietà `Location`.

### <a name="absolute-routes"></a>Route assolute

La navigazione può essere eseguita specificando un URI assoluto valido come argomento del metodo `GoToAsync`:

```csharp
await Shell.Current.GoToAsync("//animals/monkeys");
```

Questo esempio consente di passare alla pagina per la route `monkeys`, con la route definita in un oggetto `ShellContent`. L'oggetto `ShellContent` che rappresenta la route `monkeys` è figlio di un oggetto `FlyoutItem`, la cui route è `animals`.

### <a name="relative-routes"></a>Route relative

La navigazione può essere eseguita anche specificando un URI relativo valido come argomento del metodo `GoToAsync`. Il sistema di routing cercherà di trovare una corrispondenza tra l'URI e un oggetto `ShellContent`. Se quindi tutte le route in un'applicazione sono univoche, la navigazione può essere eseguita specificando solo il nome della route univoca come URI relativo:

```csharp
await Shell.Current.GoToAsync("monkeydetails");
```

Questo esempio consente di passare alla pagina per la route `monkeydetails`.

Sono inoltre supportati i seguenti formati di route relative:

| Formato | DESCRIZIONE |
| --- | --- |
| //*route* | Verrà eseguita una ricerca della route specificata nella gerarchia di route, verso l'alto a partire dalla route attualmente visualizzata. |
| ///*route* | Verrà eseguita una ricerca della route specificata nella gerarchia di route, verso il basso a partire dalla route attualmente visualizzata. |

#### <a name="contextual-navigation"></a>Navigazione contestuale

Le route relative consentono la navigazione contestuale. Si consideri ad esempio la gerarchia di route seguente:

```
monkeys
  details
bears
  details
```

Quando è visualizzata la pagina registrata per la route `monkeys`, se si passa alla route `details` viene visualizzata la pagina registrata per la route `monkeys/details`. Analogamente, quando è visualizzata la pagina registrata per la route `bears`, se si passa alla route `details` viene visualizzata la pagina registrata per la route `bears/details`. Per informazioni su come registrare le route in questo esempio, vedere [Registrare le route di pagine](#register-page-routes).

### <a name="invalid-routes"></a>Route non valide

I formati di route seguenti non sono validi:

| Formato | Descrizione |
| --- | --- |
| *route* o /*route* | Non è possibile eseguire il push delle route nella gerarchia visiva nello stack di navigazione. |
| //*page* o ///*page* | Le route globali attualmente non possono essere l'unica pagina nello stack di navigazione. Il routing assoluto per le route globali non è quindi supportato. |

Se si usa uno di questi formati di route, viene generato un oggetto `Exception`.

> [!IMPORTANT]
> Un tentativo di passare a una route non esistente causa la generazione di un'eccezione `ArgumentException`.

### <a name="debugging-navigation"></a>Debug della navigazione

Alcune classi della shell sono decorate con `DebuggerDisplayAttribute`, che specifica in che modo il debugger visualizza una classe o un campo. Ciò può essere utile per eseguire il debug delle richieste di navigazione visualizzando i dati correlati alla richiesta di navigazione. Lo screenshot seguente mostra ad esempio le proprietà `CurrentItem` e `CurrentState` dell'oggetto `Shell.Current`:

![Screenshot del debugger](navigation-images/debugger.png "Debugger")

In questo esempio la proprietà `CurrentItem`, di tipo `FlyoutItem`, visualizza il titolo e la route dell'oggetto `FlyoutItem`. Analogamente, la proprietà `CurrentState`, di tipo `ShellNavigationState`, visualizza l'URI della route visualizzata all'interno dell'applicazione shell.

### <a name="tab-class"></a>Classe Tab

La classe `Tab` definisce una proprietà `Stack`, di tipo `IReadOnlyList<Page>`, che rappresenta lo stack di navigazione corrente, di cui è stato eseguito il push, all'interno di `Tab`. La classe fornisce anche i metodi di navigazione sottoponibili a override seguenti:

- `GetNavigationStack`, restituisce `IReadOnlyList<Page`>, lo stack di navigazione corrente.
- `OnInsertPageBefore`, chiamato quando si chiama `INavigation.InsertPageBefore`.
- `OnPopAsync`, restituisce `Task<Page>` e viene chiamato quando si chiama `INavigation.PopAsync`.
- `OnPopToRootAsync`, restituisce `Task` e viene chiamato quando si chiama `INavigation.OnPopToRootAsync`.
- `OnPushAsync`, restituisce `Task` e viene chiamato quando si chiama `INavigation.PushAsync`.
- `OnRemovePage`, chiamato quando si chiama `INavigation.RemovePage`.

## <a name="navigation-events"></a>Eventi di navigazione

La classe `Shell` definisce un evento `Navigating`, che viene generato quando sta per essere eseguita la navigazione, a livello di codice o in seguito all'interazione dell'utente. L'oggetto `ShellNavigatingEventArgs` che accompagna l'evento `Navigating` fornisce le proprietà seguenti:

| Proprietà | Tipo | DESCRIZIONE |
|---|---|---|
| `Current` | `ShellNavigationState` | URI della pagina corrente. |
| `Source` | `ShellNavigationSource` | Tipo di navigazione eseguita. |
| `Target` | `ShellNavigationState`  | URI che rappresenta la destinazione della navigazione. |
| `CanCancel`  | `bool` | Valore che indica se è possibile annullare la navigazione. |
| `Cancelled`  | `bool` | Valore che indica se la navigazione è stata annullata. |

La classe `ShellNavigatingEventArgs` fornisce anche un metodo `Cancel` che è possibile usare per annullare la navigazione.

> [!NOTE]
> L'evento `Navigated` viene generato dal metodo `OnNavigating` sottoponibile a override nella classe `Shell`.

La classe `Shell` definisce anche un evento `Navigated`, che viene generato al completamento della navigazione. L'oggetto `ShellNavigatedEventArgs` che accompagna l'evento `Navigating` fornisce le proprietà seguenti:

| Proprietà | Tipo | DESCRIZIONE |
|---|---|---|
| `Current` | `ShellNavigationState` | URI della pagina corrente. |
| `Previous`| `ShellNavigationState` | URI della pagina precedente. |
| `Source`  | `ShellNavigationSource` | Tipo di navigazione eseguita. |

> [!NOTE]
> L'evento `Navigating` viene generato dal metodo `OnNavigated` sottoponibile a override nella classe `Shell`.

Le classi `ShellNavigatedEventArgs` e `ShellNavigatingEventArgs` hanno entrambe proprietà `Source`, di tipo `ShellNavigationSource`. Questa enumerazione fornisce i valori seguenti:

- `Unknown`
- `Push`
- `Pop`
- `PopToRoot`
- `Insert`
- `Remove`
- `ShellItemChanged`
- `ShellSectionChanged`
- `ShellContentChanged`

Pertanto, in un gestore per l'evento `Navigating`, è possibile intercettare la navigazione ed eseguire azioni in base all'origine della navigazione. Il codice seguente illustra ad esempio come annullare la navigazione indietro se i dati nella pagina non sono stati salvati:

```csharp
void OnNavigating(object sender, ShellNavigatingEventArgs e)
{
    // Cancel back navigation if data is unsaved
    if (e.Source == ShellNavigationSource.Pop && !dataSaved)
    {
        e.Cancel();
    }
}
```

## <a name="pass-data"></a>Passare dati

I dati possono essere passati come parametri di query durante l'esecuzione della navigazione a livello di codice. Il codice seguente, ad esempio, viene eseguito nell'applicazione di esempio quando un utente seleziona un elefante in `ElephantsPage`:

```csharp
async void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string elephantName = (e.CurrentSelection.FirstOrDefault() as Animal).Name;
    await Shell.Current.GoToAsync($"//animals/elephants/elephantdetails?name={elephantName}");
}
```

Questo esempio di codice recupera l'elefante attualmente selezionato in [`CollectionView`](xref:Xamarin.Forms.CollectionView) e passa alla route `elephantdetails`, passando `elephantName` come parametro di query. Si noti che i parametri di query vengono codificati in URL per la navigazione, quindi "Indian Elephant" diventa "Indian%20Elephant".

Per ricevere i dati, la classe che rappresenta la pagina a cui si passa, oppure la classe per l'oggetto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina, deve essere decorata con un attributo `QueryPropertyAttribute` per ogni parametro di query:

```csharp
[QueryProperty("Name", "name")]
public partial class ElephantDetailPage : ContentPage
{
    public string Name
    {
        set
        {
            BindingContext = ElephantData.Elephants.FirstOrDefault(m => m.Name == Uri.UnescapeDataString(value));
        }
    }
    ...
}
```

Il primo argomento di `QueryPropertyAttribute` specifica il nome della proprietà che riceverà i dati, mentre il secondo argomento specifica l'ID del parametro di query. L'attributo `QueryPropertyAttribute` nell'esempio precedente specifica quindi che la proprietà `Name` riceverà i dati passati nel parametro di query `name` dall'URI nella chiamata al metodo `GoToAsync`. La proprietà `Name` esegue quindi la decodifica URL per il valore del parametro di query e lo usa per impostare l'elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina sull'oggetto che verrà visualizzato.

> [!NOTE]
> Una classe può essere decorata con più oggetti `QueryPropertyAttribute`.

## <a name="back-button-behavior"></a>Comportamento del pulsante Indietro

La classe `BackButtonBehavior` definisce le proprietà seguenti che controllano l'aspetto e il comportamento del pulsante Indietro:

- `Command`, di tipo `ICommand`. Questo oggetto viene eseguito quando si preme sul pulsante Indietro.
- `CommandParameter`, di tipo `object`, ovvero il parametro passato a `Command`.
- `IconOveride`, di tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), ovvero l'icona usata per il pulsante Indietro.
- `IsEnabled`, di tipo `boolean`, che indica se il pulsante Indietro è abilitato. Il valore predefinito è `true`.
- `TextOverride`, di tipo `string`, ovvero il testo usato per il pulsante Indietro.

Tutte queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) e ciò significa che tali proprietà possono essere destinazioni di data binding.

La classe `BackButtonBehavior` può essere utilizzata impostando la proprietà associata `Shell.BackButtonBehavior` su un oggetto `BackButtonBehavior`:

```xaml
<ContentPage ...>    
    <Shell.BackButtonBehavior>
        <BackButtonBehavior Command="{Binding BackCommand}"
                            IconOverride="back.png" />   
    </Shell.BackButtonBehavior>
    ...
</ContentPage>
```

Il codice C# equivalente è il seguente:

```csharp
Shell.SetBackButtonBehavior(this, new BackButtonBehavior
{
    Command = new Command(() =>
    {
        ...
    }),
    IconOverride = "back.png"
});
```

La proprietà `Command` è impostata su un oggetto `ICommand` da eseguire quando si preme il pulsante Indietro, mentre la proprietà `IconOverride` è impostata sull'icona usata per il pulsante Indietro:

[![Screenshot dell'override dell'icona del pulsante Indietro della shell, in iOS e Android](navigation-images/back-button.png "Override dell'icona del pulsante Indietro della shell")](navigation-images/back-button-large.png#lightbox "Override dell'icona del pulsante Indietro della shell")

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
