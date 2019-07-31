---
title: ViewPager con visualizzazioni
description: ViewPager è un gestore di layout che consente di implementare la navigazione gestuale. La navigazione gestuale consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare un'interfaccia utente di swipeable con ViewPager e PagerTabStrip, usando le visualizzazioni come pagine di dati (una guida successiva illustra come usare i frammenti per le pagine).
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 15ff11c5100f697e1945793da0baca68add082be
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646555"
---
# <a name="viewpager-with-views"></a>ViewPager con visualizzazioni

_ViewPager è un gestore di layout che consente di implementare la navigazione gestuale. La navigazione gestuale consente all'utente di scorrere verso sinistra e destra per scorrere le pagine di dati. Questa guida illustra come implementare un'interfaccia utente di swipeable con ViewPager e PagerTabStrip, usando le visualizzazioni come pagine di dati (una guida successiva illustra come usare i frammenti per le pagine)._

 
## <a name="overview"></a>Panoramica

Questa guida è una procedura dettagliata che fornisce una dimostrazione dettagliata di come usare `ViewPager` per implementare una raccolta di immagini di alberi decidui e sempreverdi. In questa app, l'utente scorre verso sinistra e verso destra attraverso un "catalogo ad albero" per visualizzare le immagini dell'albero. Nella parte superiore di ogni pagina del catalogo, il nome dell'albero è elencato in un`PagerTabStrip`oggetto e un'immagine dell'albero viene visualizzata in un oggetto. `ImageView` Un adapter viene utilizzato per interfacciare `ViewPager` al modello di dati sottostante. Questa app implementa un adapter derivato da `PagerAdapter`. 

Sebbene `ViewPager`le app basate su siano spesso implementate con i, sono disponibili alcuni casi d'uso relativamente semplici in `Fragment`cui `Fragment`la complessità aggiuntiva di s non è necessaria. Ad esempio, l'app raccolta di immagini di base illustrata in questa procedura dettagliata non richiede `Fragment`l'uso di s. Poiché il contenuto è statico e l'utente scorre in avanti e indietro solo tra immagini diverse, l'implementazione può essere mantenuta più semplice usando visualizzazioni e layout standard di Android. 



## <a name="start-an-app-project"></a>Avviare un progetto di app

Creare un nuovo progetto Android denominato **TreePager** (per altre informazioni sulla creazione di nuovi progetti Android [, vedere Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) ). Avviare quindi Gestione pacchetti NuGet. Per ulteriori informazioni sull'installazione di pacchetti NuGet, vedere [procedura dettagliata: Inclusione di NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). Trovare e installare la **libreria di supporto Android V4**: 

[![Screenshot del supporto NuGet V4 selezionato in Gestione pacchetti NuGet](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

Vengono installati anche tutti i pacchetti aggiuntivi reaquired dalla **libreria di supporto Android V4**.



## <a name="add-an-example-data-source"></a>Aggiungere un'origine dati di esempio

In questo esempio l'origine dati del catalogo della struttura ad albero, `TreeCatalog` rappresentata dalla classe `ViewPager` , fornisce il contenuto dell'elemento con. 
`TreeCatalog`contiene una raccolta predisposta di immagini albero e titoli di albero che l'adapter utilizzerà per la `View`creazione di. Il `TreeCatalog` costruttore non richiede argomenti:

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

La raccolta di immagini in `TreeCatalog` è organizzata in modo che ogni immagine possa accedere a un indicizzatore. Ad esempio, la riga di codice seguente recupera l'ID risorsa immagine per la terza immagine nella raccolta: 

```csharp
int imageId = treeCatalog[2].imageId;
```

Poiché i dettagli di implementazione `TreeCatalog` di non sono rilevanti per `ViewPager`la comprensione `TreeCatalog` , il codice non è elencato qui. Il codice sorgente di `TreeCatalog` è disponibile in [TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs). Scaricare questo file di origine (oppure copiare e incollare il codice in un nuovo file **TreeCatalog.cs** ) e aggiungerlo al progetto. Inoltre, scaricare e decomprimere i [file di immagine](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true) nella cartella Resources/di cui è possibile **creare** e includerli nel progetto. 



## <a name="create-a-viewpager-layout"></a>Creare un layout ViewPager

Aprire **risorse/layout/Main. aXML** e sostituirne il contenuto con il codice XML seguente:

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

1.  Imposta la visualizzazione dalla risorsa di layout **Main. aXML** .

2.  Recupera un riferimento a `ViewPager` dal layout.

3.  Crea un'istanza di `TreeCatalog` un nuovo oggetto come origine dati.

Quando si compila ed esegue questo codice, viene visualizzata una schermata simile alla seguente: 

[![Screenshot dell'app che visualizza un ViewPager vuoto](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

A questo punto, `ViewPager` è vuoto perché manca un adattatore per accedere al contenuto in **TreeCatalog**. Nella sezione successiva viene creato un **PagerAdapter** per connettere `ViewPager` al **TreeCatalog**. 


## <a name="create-the-adapter"></a>Creare l'adapter

`ViewPager`Usa un oggetto controller di adapter che risiede tra `ViewPager` e l'origine dati (vedere l'illustrazione nell' [Adapter](~/android/user-interface/controls/view-pager/index.md#adapter)). Per accedere a questi dati, `ViewPager` è necessario fornire un adapter personalizzato derivato da. `PagerAdapter` Questo adapter popola ogni `ViewPager` pagina con contenuto dall'origine dati. Poiché questa origine dati è specifica dell'app, l'adapter personalizzato è il codice che comprende come accedere ai dati. Quando l'utente scorre le pagine del `ViewPager`, l'adapter estrae le informazioni dall'origine dati e le carica nelle pagine per la `ViewPager` visualizzazione di. 

Quando si implementa un `PagerAdapter`, è necessario eseguire l'override di quanto segue:

-   **InstantiateItem** Crea la pagina (`View`) per una posizione specificata `ViewPager`e la aggiunge alla raccolta di viste di. &ndash; 

-   **DestroyItem** &ndash; Rimuove una pagina da una posizione specificata.

-   **Numero** di &ndash; Proprietà di sola lettura che restituisce il numero di visualizzazioni (pagine) disponibili. 

-   **IsViewFromObject** &ndash; Determina se una pagina è associata a un oggetto chiave specifico. Questo oggetto viene creato dal `InstantiateItem` metodo. In questo esempio, l'oggetto chiave è l' `TreeCatalog` oggetto dati.

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

Questo codice esclude l'implementazione essenziale `PagerAdapter` . Nelle sezioni seguenti, ognuno di questi metodi viene sostituito con codice funzionante. 



### <a name="implement-the-constructor"></a>Implementare il costruttore

Quando l'app crea un'istanza `TreePagerAdapter`di, fornisce un contesto `MainActivity`( `TreeCatalog`) e un oggetto di cui è stata creata un'istanza. Aggiungere le variabili membro e il costruttore seguenti all'inizio della `TreePagerAdapter` classe in **TreePagerAdapter.cs**: 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

Lo scopo di questo costruttore è archiviare il contesto e `TreeCatalog` l'istanza `TreePagerAdapter` che verrà utilizzata da. 



### <a name="implement-count"></a>Numero di implementazioni

L' `Count` implementazione è relativamente semplice: restituisce il numero di alberi nel catalogo della struttura ad albero. Sostituisci `Count` con il seguente codice:

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

La `NumTrees` proprietà di `TreeCatalog` restituisce il numero di alberi (numero di pagine) nel set di dati.



### <a name="implement-instantiateitem"></a>Implementare InstantiateItem

Il `InstantiateItem` metodo crea la pagina per una posizione specificata. Deve inoltre aggiungere la visualizzazione appena creata alla `ViewPager`raccolta di viste di. Per eseguire questa operazione, l' `ViewPager` oggetto viene passato come parametro del contenitore. 

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

1.  Crea un'istanza di `ImageView` un nuovo oggetto per visualizzare l'immagine della struttura ad albero in corrispondenza della posizione specificata. L'app `MainActivity` è il contesto che verrà passato `ImageView` al costruttore.

2.  Imposta la `ImageView` risorsa `TreeCatalog` sull'ID risorsa dell'immagine in corrispondenza della posizione specificata.

3.  Esegue il cast del contenitore `View` passato a `ViewPager` un riferimento.
    Si noti che è necessario `JavaCast<ViewPager>()` usare per eseguire correttamente questo cast (necessario in modo che Android esegua una conversione del tipo controllata dal Runtime).

4.  Aggiunge l'oggetto di `ImageView` cui è `ViewPager` stata creata un' `ImageView` istanza all'oggetto e restituisce al chiamante.

Quando l' `ViewPager` immagine viene visualizzata in `position`, viene visualizzata `ImageView`. Inizialmente, `InstantiateItem` viene chiamato due volte per popolare le prime due pagine con le visualizzazioni. Mentre l'utente scorre, viene chiamato di nuovo per mantenere le visualizzazioni immediatamente dietro e avanti rispetto all'elemento attualmente visualizzato. 



### <a name="implement-destroyitem"></a>Implementare DestroyItem

Il `DestroyItem` metodo rimuove una pagina dalla posizione specificata. Nelle app in cui è possibile modificare la visualizzazione in una determinata `ViewPager` posizione, è necessario rimuovere una visualizzazione non aggiornata in tale posizione prima di sostituirla con una nuova visualizzazione. Nell'esempio, la vista in ogni posizione non cambia, quindi una vista rimossa da `DestroyItem` viene semplicemente riaggiunta quando `InstantiateItem` viene chiamato per tale posizione. `TreeCatalog` Per una migliore efficienza, è possibile implementare un pool per riciclare `View`i che verranno visualizzati nuovamente nella stessa posizione. 

Sostituire il metodo `DestroyItem` con il codice seguente: 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

Questo codice esegue le operazioni seguenti:

1.  Esegue il cast del contenitore `View` passato in `ViewPager` un riferimento.

2.  Esegue il cast dell'oggetto Java passato`view`() in C# `View` un`view as View`();

3.  Rimuove la visualizzazione da `ViewPager`. 



### <a name="implement-isviewfromobject"></a>Implementare IsViewFromObject

Quando l'utente scorre verso sinistra e verso destra attraverso le pagine `ViewPager` di `IsViewFromObject` contenuto, chiama per verificare `View` che l'elemento figlio in corrispondenza della posizione specificata sia associato all'oggetto dell'adapter per la stessa posizione, quindi l'oggetto della scheda è denominato *chiave dell'oggetto*). Per le app relativamente semplici, l'associazione è una delle &ndash; identità la chiave dell'oggetto dell'adapter in tale istanza è la vista che in precedenza era `ViewPager` stata `InstantiateItem`restituita al via. Per altre app, tuttavia, la chiave dell'oggetto può essere un'altra istanza della classe specifica dell'adapter associata a (ma non uguale a) alla visualizzazione `ViewPager` figlio visualizzata in tale posizione. Solo l'adapter sa se sono associate la visualizzazione passata e la chiave dell'oggetto. 

`IsViewFromObject`deve essere implementato affinché `PagerAdapter` funzioni correttamente. Se `IsViewFromObject` `ViewPager` restituisce `false` per una determinata posizione, la vista non viene visualizzata in tale posizione. Nell'app la chiave dell'oggetto restituita da `InstantiateItem` è la pagina `View` di un albero, quindi il codice deve verificare solo l'identità, ovvero la chiave dell'oggetto e la visualizzazione sono le stesse. `TreePager` Sostituisci `IsViewFromObject` con il seguente codice: 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```


## <a name="add-the-adapter-to-the-viewpager"></a>Aggiungere l'adapter a ViewPager

Ora che `TreePagerAdapter` è implementato, è possibile aggiungerlo `ViewPager`a. In **MainActivity.cs**aggiungere la seguente riga di codice alla fine del `OnCreate` metodo:

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

Questo codice crea un'istanza `TreePagerAdapter`di, passando `MainActivity` come contesto (`this`). L'oggetto di `TreeCatalog` cui è stata creata un'istanza viene passato nel secondo argomento del costruttore. La `ViewPager`proprietà `Adapter` di è `TreePagerAdapter` impostata sull'`ViewPager`oggetto di cui è stata creata un'istanza, che `TreePagerAdapter` inserisce in. 

L'implementazione di base è ora &ndash; completare la compilazione ed eseguire l'app. La prima immagine del catalogo della struttura ad albero dovrebbe essere visualizzata nella schermata, come illustrato a sinistra nello screenshot successivo. Scorri verso sinistra per visualizzare altre visualizzazioni ad albero, quindi scorri a destra per tornare indietro nel catalogo della struttura ad albero: 

[![Screenshot dell'app TreePager scorrere rapidamente le immagini dell'albero](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Aggiungi un indicatore cercapersone

Questa implementazione `ViewPager` minima consente di visualizzare le immagini del catalogo della struttura ad albero, ma non fornisce alcuna indicazione sul punto in cui l'utente si trova nel catalogo. Il passaggio successivo consiste nell'aggiungere un `PagerTabStrip`. `PagerTabStrip` Informa l'utente sulla visualizzazione della pagina e fornisce il contesto di navigazione visualizzando un hint delle pagine precedenti e successive. `PagerTabStrip`è progettato per essere utilizzato come indicatore per la pagina corrente di un oggetto `ViewPager`; scorre e aggiorna quando l'utente scorre ogni pagina. 

Aprire **risorse/layout/Main. aXML** e aggiungere un `PagerTabStrip` al layout:

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

`ViewPager`e `PagerTabStrip` sono progettati per collaborare. Quando si dichiara un `PagerTabStrip` oggetto all' `ViewPager` interno di un `ViewPager` layout, `PagerTabStrip` il troverà automaticamente e lo collegherà all'adapter. Quando si compila ed esegue l'app, viene visualizzato il vuoto `PagerTabStrip` nella parte superiore di ogni schermata: 

[![Schermata di primo piano di un PagerTabStrip vuoto](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)



### <a name="display-a-title"></a>Visualizza un titolo

Per aggiungere un titolo a ogni scheda della pagina, implementare `GetPageTitleFormatted` il metodo `PagerAdapter`nella classe derivata da. `ViewPager`chiama `GetPageTitleFormatted` (se implementato) per ottenere la stringa del titolo che descrive la pagina in corrispondenza della posizione specificata. Aggiungere il metodo seguente alla `TreePagerAdapter` classe in **TreePagerAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

Questo codice recupera la stringa della didascalia della struttura ad albero dalla pagina specificata (position) nel catalogo della struttura ad albero `String`, la converte in un oggetto `ViewPager`Java e la restituisce a. Quando si esegue l'app con questo nuovo metodo, in ogni pagina viene visualizzata la didascalia `PagerTabStrip`della struttura ad albero in. Il nome dell'albero verrà visualizzato nella parte superiore della schermata senza sottolineatura: 

[![Screenshot delle pagine con schede PagerTabStrip riempite con testo](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

È possibile scorrere in avanti e indietro per visualizzare ogni immagine dell'albero didascalia nel catalogo. 



### <a name="pagertitlestrip-variation"></a>Variazione PagerTitleStrip

`PagerTitleStrip`è molto simile a `PagerTabStrip` , ad `PagerTabStrip` eccezione del fatto che aggiunge una sottolineatura per la scheda attualmente selezionata. È possibile sostituire `PagerTabStrip` con `PagerTitleStrip` nel layout precedente ed eseguire di nuovo l'app per `PagerTitleStrip`verificarne l'aspetto: 

[![PagerTitleStrip con sottolineature rimosse dal testo](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

Si noti che la sottolineatura viene rimossa `PagerTitleStrip`quando si esegue la conversione a. 


 
## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stato fornito un esempio dettagliato di come creare un'app basata `ViewPager`su base senza usare. `Fragment` Viene presentata un'origine dati di esempio contenente immagini e stringhe di didascalie, un `ViewPager` layout per visualizzare le immagini e una `PagerAdapter` sottoclasse `ViewPager` che connette l'oggetto all'origine dati. Per consentire all'utente di spostarsi nel set di dati, sono state incluse istruzioni che spiegano come `PagerTabStrip` aggiungere `PagerTitleStrip` o per visualizzare la didascalia dell'immagine nella parte superiore di ogni pagina. 


## <a name="related-links"></a>Collegamenti correlati

- [TreePager (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager)
