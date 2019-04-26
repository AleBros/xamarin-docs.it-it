---
title: ViewPager con visualizzazioni
description: ViewPager è un gestore di layout che consente di implementare l'esplorazione gestuali. Navigazione gestuali consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare un'interfaccia utente swipeable con insieme ViewPager e PagerTabStrip, usando le visualizzazioni delle pagine di dati (Guida successiva illustra come usare frammenti per le pagine).
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a8b7fa53d3384821d028e4a88ba22071a17e5bd9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61311569"
---
# <a name="viewpager-with-views"></a>ViewPager con visualizzazioni

_ViewPager è un gestore di layout che consente di implementare l'esplorazione gestuali. Navigazione gestuali consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare un'interfaccia utente swipeable con insieme ViewPager e PagerTabStrip, usando le visualizzazioni delle pagine di dati (Guida successiva illustra come usare frammenti per le pagine)._

 
## <a name="overview"></a>Panoramica

Questa guida è una procedura dettagliata che fornisce una spiegazione dettagliata di come usare `ViewPager` per implementare una raccolta di immagini di alberi caducifoglio ed ecocompatibile. In questa app, l'utente passa a sinistra e destra all'interno di un catalogo"albero" per visualizzare le immagini di struttura ad albero. Nella parte superiore di ogni pagina del catalogo, il nome della struttura ad albero è elencato una`PagerTabStrip`, e un'immagine dell'albero viene visualizzata in un `ImageView`. Viene utilizzato un adapter all'interfaccia di `ViewPager` al modello di dati sottostante. Questa app implementa un adattatore derivato da `PagerAdapter`. 

Sebbene `ViewPager`-applicazioni vengono spesso implementate con `Fragment`s, esistono alcuni casi di utilizzo relativamente semplici in cui la complessità aggiuntiva degli `Fragment`s non è necessario. Ad esempio, l'app della raccolta immagini di base illustrate in questa procedura dettagliata non richiede l'uso di `Fragment`s. Poiché il contenuto statico sia le tessere magnetiche a utente solo tra le diverse immagini e viceversa, l'implementazione può essere mantenuto più semplice usando i layout e visualizzazioni di Android standard. 



## <a name="start-an-app-project"></a>Avviare un progetto di App

Creare un nuovo progetto Android chiamato **TreePager** (vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) per altre informazioni sulla creazione di nuovi progetti Android). Successivamente, avviare Gestione pacchetti NuGet. (Per altre informazioni sull'installazione di pacchetti NuGet, vedere [procedura dettagliata: Inserimento di pacchetto NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Trovare e installare **libreria di supporto Android v4**: 

[![Screenshot del supporto v4 Nuget selezionata in Gestione pacchetti NuGet](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

Contestualmente verrà anche installato eventuali reaquired pacchetti aggiuntivi da **libreria di supporto Android v4**.



## <a name="add-an-example-data-source"></a>Aggiungere un'origine dati di esempio

In questo esempio, l'origine dati del catalogo di struttura ad albero (rappresentato dal `TreeCatalog` classe) fornisce il `ViewPager` con contenuto dell'elemento. 
`TreeCatalog` contiene una raccolta pronte all'uso di immagini di struttura ad albero e i titoli di struttura ad albero che l'adapter utilizzerà per la creazione di `View`s. Il `TreeCatalog` costruttore non richiede argomenti:

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

La raccolta di immagini in `TreeCatalog` è organizzata in modo che ogni immagine sono accessibili da un indicizzatore. Ad esempio, la riga di codice seguente recupera l'ID di risorsa immagine per la terza immagine nella raccolta: 

```csharp
int imageId = treeCatalog[2].imageId;
```

Poiché i dettagli di implementazione `TreeCatalog` non sono rilevanti per comprendere `ViewPager`, il `TreeCatalog` codice non è elencato qui. Il codice sorgente `TreeCatalog` è disponibile all'indirizzo [TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs). Scaricare questo file di origine (o copiare e incollare il codice in una nuova **TreeCatalog.cs** file) e aggiungerlo al progetto. Inoltre, scaricare e decomprimere il [file di immagine](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true) nel **risorse/drawable** cartella e includerli nel progetto. 



## <a name="create-a-viewpager-layout"></a>Creare un Layout ViewPager

Aprire **Resources/layout/Main.axml** e sostituirne il contenuto con il codice XML seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

</android.support.v4.view.ViewPager>

```csharp
This XML defines a `ViewPager` that occupies the entire screen. Note that
you must use the fully-qualified name **android.support.v4.view.ViewPager**
because `ViewPager` is packaged in a support library. `ViewPager` is
available only from 
[Android Support Library v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/);
it is not available in the Android SDK. 


## Set up ViewPager

Edit **MainActivity.cs** and add the following `using` statement:

```csharp
using Android.Support.V4.View;
```

Sostituire il metodo `OnCreate` con il codice seguente:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    TreeCatalog treeCatalog = new TreeCatalog();
}
```

Questo codice esegue le operazioni seguenti:

1.  Imposta la visualizzazione dal **Main. axml** risorsa del layout.

2.  Recupera un riferimento di `ViewPager` dal layout.

3.  Crea un'istanza di un nuovo `TreeCatalog` come origine dati.

Quando si compila e si esegue questo codice, verrà visualizzato un display che simile allo screenshot seguente: 

[![Screenshot dell'app per la visualizzazione di un ViewPager vuoto](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

A questo punto, il `ViewPager` è vuota perché mancano un adattatore per accedere al contenuto in **TreeCatalog**. Nella sezione successiva, un **PagerAdapter** viene creato per connettersi il `ViewPager` per il **TreeCatalog**. 


## <a name="create-the-adapter"></a>Creare l'Adapter

`ViewPager` Usa un oggetto controller di adapter che risiede tra le `ViewPager` e l'origine dati (vedere la figura nel [Adapter](~/android/user-interface/controls/view-pager/index.md#adapter)). Per poter accedere a questi dati, `ViewPager` richiede di specificare un adapter personalizzato derivato da `PagerAdapter`. Questo adapter popola ogni `ViewPager` pagina con il contenuto dall'origine dati. Poiché questa origine dati è specifico dell'app, l'adapter personalizzato è il codice che siano in grado di accedere ai dati. Come le tessere magnetiche utente tramite pagine del `ViewPager`, l'adapter estrae le informazioni dall'origine dati e li carica in pagine per il `ViewPager` da visualizzare. 

Quando si implementa un `PagerAdapter`, è necessario eseguire l'override di quanto segue:

-   **InstantiateItem** &ndash; crea la pagina (`View`) per una determinata posizione e lo aggiunge al `ViewPager`della raccolta di viste. 

-   **DestroyItem** &ndash; rimuove una pagina da una posizione specificata.

-   **Conteggio** &ndash; proprietà di sola lettura che restituisce il numero di visualizzazioni (pagine) disponibili. 

-   **IsViewFromObject** &ndash; determina se una pagina è associata a un oggetto chiave specifico. (L'oggetto viene creato mediante il `InstantiateItem` (metodo).) In questo esempio, è l'oggetto chiave di `TreeCatalog` oggetto dati.

Aggiungere un nuovo file denominato **TreePagerAdapter.cs** e sostituirne il contenuto con il codice seguente: 

```csharp
using System;
using Android.App;
using Android.Runtime;
using Android.Content;
using Android.Views;
using Android.Widget;
using Android.Support.V4.View;
using Java.Lang;

namespace TreePager
{
    class TreePagerAdapter : PagerAdapter
    {
        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override bool IsViewFromObject(View view, Java.Lang.Object obj)
        {
            throw new NotImplementedException();
        }

        public override Java.Lang.Object InstantiateItem (View container, int position)
        {
            throw new NotImplementedException();
        }

        public override void DestroyItem(View container, int position, Java.Lang.Object view)
        {
            throw new NotImplementedException();
        }
    }
}
```

Questo codice associa automaticamente l'essenziale `PagerAdapter` implementazione. Nelle sezioni seguenti, ognuno di questi metodi viene sostituito con codice di lavoro. 



### <a name="implement-the-constructor"></a>Implementare il costruttore

Quando l'app crea un'istanza di `TreePagerAdapter`, fornisce un contesto (la `MainActivity`) e un'istanza `TreeCatalog`. Aggiungere le seguenti variabili membro e il costruttore in cima il `TreePagerAdapter` classe **TreePagerAdapter.cs**: 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

Lo scopo di questo costruttore è per archiviare il contesto e `TreeCatalog` dell'istanza che il `TreePagerAdapter` userà. 



### <a name="implement-count"></a>Conteggio di implementare

Il `Count` implementazione è abbastanza semplice: restituisce il numero di alberi nel catalogo di struttura ad albero. Sostituisci `Count` con il seguente codice:

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

Il `NumTrees` proprietà di `TreeCatalog` restituisce il numero di alberi (numero di pagine) nel set di dati.



### <a name="implement-instantiateitem"></a>Implementare InstantiateItem

Il `InstantiateItem` metodo crea la pagina per una determinata posizione. Inoltre necessario aggiungere la visualizzazione appena creata per il `ViewPager`della visualizzazione raccolta. A tale scopo, il `ViewPager` stesso viene passato come parametro del contenitore. 

Sostituire il metodo `InstantiateItem` con il codice seguente:

```csharp
public override Java.Lang.Object InstantiateItem (View container, int position)
{
    var imageView = new ImageView (context);
    imageView.SetImageResource (treeCatalog[position].imageId);
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.AddView (imageView);
    return imageView;
}
```

Questo codice esegue le operazioni seguenti:

1.  Crea un'istanza di un nuovo `ImageView` per visualizzare l'immagine di struttura ad albero in corrispondenza della posizione specificata. L'app `MainActivity` è il contesto verrà passato il `ImageView` costruttore.

2.  Imposta il `ImageView` risorse per il `TreeCatalog` immagine ID di risorsa nella posizione specificata.

3.  Viene eseguito il cast del contenitore passato `View` a un `ViewPager` riferimento.
    Si noti che è necessario usare `JavaCast<ViewPager>()` per eseguire correttamente questo cast (questo è necessario in modo che Android esegue una conversione del tipo di controllo del runtime).

4.  Aggiunge istanziata `ImageView` per il `ViewPager` e restituisce il `ImageView` al chiamante.

Quando la `ViewPager` consente di visualizzare l'immagine nella `position`, viene visualizzato questo `ImageView`. Inizialmente, `InstantiateItem` viene chiamato due volte per popolare le prime due pagine con le visualizzazioni. Mentre l'utente scorre, viene chiamato nuovamente per mantenere le viste semplicemente precedenti e successivi l'elemento attualmente visualizzato. 



### <a name="implement-destroyitem"></a>Implementare DestroyItem

Il `DestroyItem` metodo rimuove una pagina dalla posizione specificata. Nelle App in cui è possibile modificare la visualizzazione in una determinata posizione, `ViewPager` deve essere in grado di rimozione di una vista non aggiornata in tale posizione prima di sostituirla con una nuova visualizzazione. Nel `TreeCatalog` esempio, la visualizzazione in ogni posizione non cambia, in modo che una visualizzazione rimosso dal `DestroyItem` sarà semplicemente aggiunti di nuovo quando `InstantiateItem` viene chiamato per tale posizione. (Per una maggiore efficienza, una possibile implementare un pool per riciclare `View`s che verrà visualizzato nuovamente nella stessa posizione.) 

Sostituire il metodo `DestroyItem` con il codice seguente: 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

Questo codice esegue le operazioni seguenti:

1.  Viene eseguito il cast del contenitore passato `View` in un `ViewPager` riferimento.

2.  Viene eseguito il cast l'oggetto Java passato (`view`) in un C# `View` (`view as View`);

3.  Rimuove la vista dal `ViewPager`. 



### <a name="implement-isviewfromobject"></a>Implementare IsViewFromObject

Come utente diapositive a sinistra e destra nelle pagine di contenuto `ViewPager` chiamate `IsViewFromObject` per verificare che l'elemento figlio `View` in corrispondenza della posizione specificata è associato l'oggetto dell'adattatore per tale posizione stessa (di conseguenza, viene chiamato l'oggetto dell'adattatore un *chiave dell'oggetto*). Per le app relativamente semplice, l'associazione è una delle identità &ndash; chiave dell'oggetto dell'adapter in quell'istanza è la visualizzazione che è stata restituita precedentemente per il `ViewPager` tramite `InstantiateItem`. Per altre App, tuttavia può essere la chiave dell'oggetto alcuni altri istanza della classe specifica dell'adapter associato (ma non identico) consente di visualizzare l'elemento figlio che `ViewPager` vengono visualizzati in tale posizione. Solo l'adapter conosca se sono o meno la visualizzazione passato e una chiave dell'oggetto associati. 

`IsViewFromObject` deve essere implementato per `PagerAdapter` per funzionare correttamente. Se `IsViewFromObject` restituisce `false` per una determinata posizione, `ViewPager` non visualizzerà la visualizzazione in tale posizione. Nel `TreePager` app, l'oggetto chiave restituita dal `InstantiateItem` *viene* pagina `View` di un albero, in modo che solo il codice deve verificare la presenza di identità (ad esempio, la chiave dell'oggetto e la visualizzazione sono dello stesso). Sostituisci `IsViewFromObject` con il seguente codice: 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```


## <a name="add-the-adapter-to-the-viewpager"></a>Aggiungere l'Adapter per il ViewPager

Ora che il `TreePagerAdapter` viene implementato, è possibile aggiungerla al `ViewPager`. Nelle **MainActivity.cs**, aggiungere la riga di codice seguente alla fine del `OnCreate` metodo:

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

Questo codice crea un'istanza di `TreePagerAdapter`, passando il `MainActivity` come contesto (`this`). Istanziata `TreeCatalog` viene passato nel secondo argomento del costruttore. Il `ViewPager`del `Adapter` viene impostata per istanziata `TreePagerAdapter` oggetto; questo plugs il `TreePagerAdapter` nel `ViewPager`. 

L'implementazione di base è stata completata &ndash; compilare ed eseguire l'app. Dovrebbe essere la prima immagine del catalogo dell'albero vengono visualizzate sullo schermo, come illustrato a sinistra nel prossimo screenshot. Scorrere verso sinistra per visualizzare altre visualizzazioni dell'albero, quindi scorrere rapidamente per spostarsi all'interno del catalogo di struttura ad albero: 

[![Screenshot di TreePager app scorrendo rapidamente tramite le immagini di struttura ad albero](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Aggiungere un indicatore di cercapersone

Questo minimo `ViewPager` implementazione consente di visualizzare le immagini del catalogo di struttura ad albero, ma è non presente alcuna indicazione per capire dove l'utente è all'interno del catalogo. Il passaggio successivo consiste nell'aggiungere un `PagerTabStrip`. Il `PagerTabStrip` informa l'utente al quale pagina viene visualizzata e fornisce il contesto di navigazione visualizzando un hint delle pagine precedenti e successivi. `PagerTabStrip` dovrà essere utilizzato come indicatore per la pagina corrente di un `ViewPager`; si scorre e viene aggiornato come tessere magnetiche l'utente in ciascuna pagina. 

Aprire **Resources/layout/Main.axml** e aggiungere un `PagerTabStrip` al layout:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <android.support.v4.view.PagerTabStrip
          android:layout_width="match_parent"
          android:layout_height="wrap_content"
          android:layout_gravity="top"
          android:paddingBottom="10dp"
          android:paddingTop="10dp"
          android:textColor="#fff" />

</android.support.v4.view.ViewPager>
```

`ViewPager` e `PagerTabStrip` sono progettati per interagire. Quando si dichiara un `PagerTabStrip` all'interno di un `ViewPager` layout, il `ViewPager` troverà automaticamente la `PagerTabStrip` e connetterla all'adapter. Quando si compila e si esegue l'app, si dovrebbe essere vuoto `PagerTabStrip` visualizzato nella parte superiore di ogni schermata: 

[![Schermata di primo piano di un PagerTabStrip vuoto](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)



### <a name="display-a-title"></a>Visualizzare un titolo

Per aggiungere un titolo per ogni scheda della pagina, implementare il `GetPageTitleFormatted` metodo di `PagerAdapter`-classe derivata. `ViewPager` le chiamate `GetPageTitleFormatted` (se è implementato) per ottenere la stringa del titolo che descrive la pagina in corrispondenza della posizione specificata. Aggiungere il metodo seguente per il `TreePagerAdapter` classe **TreePagerAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

Questo codice recupera la stringa della didascalia della struttura dalla pagina (posizione) specificata nel catalogo di struttura ad albero, lo converte in un linguaggio `String`e lo restituisce al `ViewPager`. Quando si esegue l'app con questo nuovo metodo, ogni pagina Visualizza la didascalia di struttura ad albero nel `PagerTabStrip`. Verrà visualizzato il nome dell'albero nella parte superiore della schermata senza sottolineatura: 

[![Screenshot delle pagine con schede PagerTabStrip riempiti con testo](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

È possibile scorrere avanti e indietro per visualizzare ogni immagine didascalie della struttura nel catalogo. 



### <a name="pagertitlestrip-variation"></a>Variazione PagerTitleStrip

`PagerTitleStrip` è molto simile a `PagerTabStrip` ad eccezione del fatto che `PagerTabStrip` aggiunge un carattere di sottolineatura per la scheda attualmente selezionata. È possibile sostituire `PagerTabStrip` con `PagerTitleStrip` nello layout precedente ed eseguire l'app per verificarne l'aspetto con `PagerTitleStrip`: 

[![PagerTitleStrip con la sottolineatura rimosso dal testo](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

Si noti che la sottolineatura viene rimosso quando si converte in `PagerTitleStrip`. 


 
## <a name="summary"></a>Riepilogo

Questa procedura dettagliata è fornito un esempio dettagliato di come compilare una semplice `ViewPager`-basato su app senza usare `Fragment`s. Presentata un'origine dati di esempio contenente immagini e stringhe di didascalia, un `ViewPager` per visualizzare le immagini, layout e un `PagerAdapter` sottoclasse che si connette il `ViewPager` all'origine dati. Per consentire all'utente di spostarsi tra il set di dati, sono state incluse le istruzioni che illustrano come aggiungere un `PagerTabStrip` o `PagerTitleStrip` per visualizzare la didascalia di immagine nella parte superiore di ogni pagina. 


## <a name="related-links"></a>Collegamenti correlati

- [TreePager (esempio)](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
