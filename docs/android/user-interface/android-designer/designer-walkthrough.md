---
title: Uso di Xamarin.Android Designer
description: Questo articolo è una procedura dettagliata di Xamarin.Android Designer. Viene illustrato come creare un'interfaccia utente per un'app per browser a colori piccoli; Questa interfaccia utente viene creata interamente nella finestra di progettazione.
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/25/2018
ms.openlocfilehash: df83bdfcc847b07754a349060c9be1613efd0b08
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029528"
---
# <a name="using-the-xamarinandroid-designer"></a>Uso di Xamarin.Android Designer

_Questo articolo è una procedura dettagliata di Xamarin.Android Designer. Viene illustrato come creare un'interfaccia utente per un'app per browser a colori piccoli; Questa interfaccia utente viene creata interamente nella finestra di progettazione._

## <a name="overview"></a>Panoramica

Le interfacce utente Android possono essere create in modo dichiarativo tramite file XML o a livello di codice scrivendo codice. Xamarin.Android Designer consente agli sviluppatori di creare e modificare visivamente layout dichiarativi, senza richiedere la modifica manuale dei file XML. La finestra di progettazione fornisce anche commenti in tempo reale che consentono allo sviluppatore di valutare le modifiche dell'interfaccia utente senza dover ridistribuire l'applicazione in un dispositivo o in un emulatore. Queste funzionalità di progettazione possono velocizzare notevolmente lo sviluppo dell'interfaccia utente di Android.
Questo articolo illustra come usare Xamarin.Android Designer per creare visivamente un'interfaccia utente.

> [!TIP]
> Le versioni più recenti di Visual Studio supportano l'apertura dei file con estensione xml all'interno di Android Designer.
>
> I file con estensione axml e xml sono entrambi supportati in Android Designer.

## <a name="walkthrough"></a>Procedura dettagliata

L'obiettivo di questa procedura dettagliata consiste nell'usare la Android Designer per creare un'interfaccia utente per un'app del browser colori di esempio. L'app color browser visualizza un elenco di colori, i relativi nomi e i rispettivi valori RGB. Si apprenderà come aggiungere widget alla **area di progettazione** e come disporre visivamente questi widget. Successivamente, si apprenderà come modificare i widget in modo interattivo nel **area di progettazione** o tramite il riquadro **Proprietà** della finestra di progettazione. Infine, si vedrà come appare la progettazione quando l'app viene eseguita in un dispositivo o in un emulatore.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="creating-a-new-project"></a>Creazione di un nuovo progetto

Il primo passaggio consiste nel creare un nuovo progetto Xamarin.Android. Avviare Visual Studio, fare clic su **nuovo progetto...** e scegliere il modello **visual C\# > Android > Android app (Xamarin)** .
Assegnare alla nuova app il nome **DesignerWalkthrough** e fare clic su **OK**.

[App vuota![Android](designer-walkthrough-images/vs/01-android-app-w158-sml.png)](designer-walkthrough-images/vs/01-android-app-w158.png#lightbox)

Nella finestra di dialogo **nuova app Android** scegliere **app vuota** e fare clic su **OK**:

[![selezione del modello app vuota Android](designer-walkthrough-images/vs/02-blank-app-w158-sml.png)](designer-walkthrough-images/vs/02-blank-app-w158.png#lightbox)

### <a name="adding-a-layout"></a>Aggiunta di un layout

Il passaggio successivo consiste nel creare un **LinearLayout** che conterrà gli elementi dell'interfaccia utente. Fare clic con il pulsante destro del mouse su **risorse/layout** nella **Esplora soluzioni** e scegliere **Aggiungi > nuovo elemento...** . Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **layout Android**. Denominare il file **list_item** e fare clic su **Aggiungi**:

[![nuovo layout](designer-walkthrough-images/vs/03-new-layout-w158-sml.png)](designer-walkthrough-images/vs/03-new-layout-w158.png#lightbox)

Il nuovo layout **list_item** viene visualizzato nella finestra di progettazione. Si noti che vengono visualizzati due riquadri &ndash; il *area di progettazione* per **list_item** è visibile nel riquadro sinistro mentre l'origine XML viene visualizzata nel riquadro di destra. È possibile scambiare le posizioni dei riquadri **area di progettazione** e **origine** facendo clic sull'icona **Scambia riquadri** presente tra i due riquadri:

[visualizzazione di progettazione![](designer-walkthrough-images/vs/04-designer-view-w158-sml.png)](designer-walkthrough-images/vs/04-designer-view-w158.png#lightbox)

Dal menu **Visualizza** fare clic su **altre finestre > Struttura documento** per aprire la **struttura del documento**. La **struttura del documento** Mostra che il layout contiene attualmente un singolo widget **LinearLayout** :

[Struttura documento![](designer-walkthrough-images/vs/06-document-outline-w158-sml.png)](designer-walkthrough-images/vs/06-document-outline-w158.png#lightbox)

Il passaggio successivo consiste nel creare l'interfaccia utente per l'app del browser colori all'interno di questo `LinearLayout`.

### <a name="creating-the-list-item-user-interface"></a>Creazione dell'interfaccia utente dell'elemento elenco

Se il riquadro **casella degli strumenti** non viene visualizzato, fare clic sulla scheda **casella degli strumenti** a sinistra. Nella **casella degli strumenti**scorrere verso il basso fino alla sezione **Immagini & supporto** e scorrere verso il basso fino a individuare una `ImageView`:

[![individuare ImageView](designer-walkthrough-images/vs/07-locate-imageview-w158-sml.png)](designer-walkthrough-images/vs/07-locate-imageview-w158.png#lightbox)

In alternativa, è possibile immettere *ImageView* nella barra di ricerca per individuare la `ImageView`:

[ricerca![ImageView](designer-walkthrough-images/vs/08-imageview-search-w158-sml.png)](designer-walkthrough-images/vs/08-imageview-search-w158.png#lightbox)

Trascinare questo `ImageView` sul Area di progettazione (questo `ImageView` verrà usato per visualizzare un campione di colore nell'app del browser colori):

[![ImageView nell'area di disegno](designer-walkthrough-images/vs/09-imageview-on-canvas-w158-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas-w158.png#lightbox)

Trascinare quindi un widget `LinearLayout (Vertical)` dalla **casella degli strumenti** nella finestra di progettazione. Si noti che un contorno blu indica i limiti della `LinearLayout`aggiunta. La **struttura del documento** Mostra che è un elemento figlio di `LinearLayout`, situato in `imageView1 (ImageView)`:

[![struttura blu](designer-walkthrough-images/vs/10-blue-outline-w158-sml.png)](designer-walkthrough-images/vs/10-blue-outline-w158.png#lightbox)

Quando si seleziona il `ImageView` nella finestra di progettazione, il contorno blu si sposta per racchiudere l'`ImageView`. Inoltre, la selezione viene spostata in `imageView1 (ImageView)` nella **struttura del documento**:

[![selezionare ImageView](designer-walkthrough-images/vs/11-select-imageview-w158-sml.png)](designer-walkthrough-images/vs/11-select-imageview-w158.png#lightbox)

Trascinare quindi un widget `Text (Large)` dalla **casella degli strumenti** all'`LinearLayout`appena aggiunta. Si noti che nella finestra di progettazione vengono utilizzate le evidenziazioni verdi per indicare la posizione in cui verrà inserito il nuovo widget:

[evidenziazione![verde](designer-walkthrough-images/vs/12-green-highlight-w158-sml.png)](designer-walkthrough-images/vs/12-green-highlight-w158.png#lightbox)

Aggiungere quindi un widget `Text (Small)` sotto il widget `Text (Large)`:

[![Aggiungi widget di testo di piccole dimensioni](designer-walkthrough-images/vs/13-add-small-text-w158-sml.png)](designer-walkthrough-images/vs/13-add-small-text-w158.png#lightbox)

A questo punto, l'area di progettazione dovrebbe somigliare allo screenshot seguente:

[layout della finestra di progettazione![](designer-walkthrough-images/vs/14-raw-layout-w158-sml.png)](designer-walkthrough-images/vs/14-raw-layout-w158.png#lightbox)

Se i due widget `textView` non sono all'interno di `linearLayout1`, è possibile trascinarli in `linearLayout1` nella **struttura del documento** e posizionarli in modo che vengano visualizzati come mostrato nella schermata precedente (rientrato in `linearLayout1`).

### <a name="arranging-the-user-interface"></a>Disposizione dell'interfaccia utente

Il passaggio successivo consiste nel modificare l'interfaccia utente per visualizzare la `ImageView` a sinistra, con i due `TextView` widget in pila a destra della `ImageView`.

1. Selezionare `ImageView`.

2. Nella **finestra Proprietà**immettere *Width* nella casella di ricerca e individuare **Larghezza layout**.

3. Modificare l'impostazione della **larghezza del layout** in `wrap_content`:

![Imposta contenuto a capo](designer-walkthrough-images/vs/15-wrap-content-w158.png)

Un altro modo per modificare l'impostazione di `Width` consiste nel fare clic sul triangolo sul lato destro del widget per impostare la larghezza su `wrap_content`:

![Trascina per impostare la larghezza](designer-walkthrough-images/vs/15b-width-arrow-w158.png)

Se si fa nuovamente clic sul triangolo, viene restituita l'impostazione `Width` `match_parent`. Passare quindi al riquadro **struttura documento** e selezionare la `LinearLayout`radice:

[![selezionare LinearLayout radice](designer-walkthrough-images/vs/16-root-linearlayout-w158-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout-w158.png#lightbox)

Con la `LinearLayout` radice selezionata, tornare al riquadro **Proprietà** , immettere *orientamento* nella casella di ricerca e individuare l'impostazione **orientamento** . Modificare l' **orientamento** in `horizontal`:

![Seleziona orientamento orizzontale](designer-walkthrough-images/vs/17-horizontal-orientation-w158.png)

A questo punto, l'area di progettazione dovrebbe essere simile alla schermata seguente.
Si noti che i widget `TextView` sono stati spostati a destra del `ImageView`:

[layout della finestra di progettazione![](designer-walkthrough-images/vs/18-designer-layout-w158-sml.png)](designer-walkthrough-images/vs/18-designer-layout-w158.png#lightbox)

### <a name="modifying-the-spacing"></a>Modifica della spaziatura

Il passaggio successivo consiste nel modificare le impostazioni di spaziatura interna e margini nell'interfaccia utente per fornire più spazio tra i widget. Selezionare il `ImageView` nell'area di progettazione. Nel riquadro **Proprietà** immettere `min` nella casella di ricerca. Immettere `70dp` per **altezza minima** e `50dp` per **larghezza minima**:

[altezza e larghezza del set di![](designer-walkthrough-images/vs/18b-set-height-width-sml.png)](designer-walkthrough-images/vs/18b-set-height-width.png#lightbox)

Nel riquadro **Proprietà** immettere `padding` nella casella di ricerca e immettere `10dp` per la **spaziatura interna**. Queste `minHeight`, `minWidth` e `padding` le impostazioni aggiungono spaziatura interna attorno a tutti i lati del `ImageView` e lo allungano verticalmente. Si noti che il layout XML cambia quando si immettono i valori seguenti:

[riempimento set![](designer-walkthrough-images/vs/19-padding-widths-w158-sml.png)](designer-walkthrough-images/vs/19-padding-widths-w158.png#lightbox)

Le impostazioni di riempimento inferiore, sinistro, destro e superiore possono essere impostate in modo indipendente inserendo i valori nei campi riempimento **inferiore**, a **sinistra**, a **destra**e **spaziatura interna,** rispettivamente.
Ad esempio, impostare il **campo riempimento a sinistra** su `5dp` e i campi **riempimento inferiore**, **riempimento a destra**e **spaziatura interna** su `10dp`:

[![impostazioni di riempimento personalizzate](designer-walkthrough-images/vs/20-custom-padding-w158-sml.png)](designer-walkthrough-images/vs/20-custom-padding-w158.png#lightbox)

Modificare quindi la posizione del widget `LinearLayout` che contiene i due widget `TextView`. Nella **struttura del documento**selezionare `linearLayout1`. Nella finestra **Proprietà** immettere `margin` nella casella di ricerca. Imposta il margine **inferiore**del layout, il **margine**di layout a sinistra e il **margine di layout superiore** a `5dp`. Imposta il **margine di layout diritto** a `0dp`:

[![impostare i margini](designer-walkthrough-images/vs/21-margins-w158-sml.png)](designer-walkthrough-images/vs/21-margins-w158.png#lightbox)

### <a name="removing-the-default-image"></a>Rimozione dell'immagine predefinita

Poiché il `ImageView` viene usato per visualizzare i colori (anziché le immagini), il passaggio successivo consiste nel rimuovere l'origine immagine predefinita aggiunta dal modello.

1. Selezionare il `ImageView` nell' **area di progettazione**.

2. In **Proprietà**immettere *src* nella casella di ricerca.

3. Fare clic sul piccolo quadrato a destra dell'impostazione della proprietà **src** e selezionare **Reset (Reimposta**):

[![deselezionare l'impostazione src ImageView](designer-walkthrough-images/vs/22-clear-img-src-w158-sml.png)](designer-walkthrough-images/vs/22-clear-img-src-w158.png#lightbox)

Questa operazione rimuove `android:src="@android:drawable/ic_menu_gallery"` dal codice XML di origine per tale `ImageView`.

### <a name="adding-a-listview-container"></a>Aggiunta di un contenitore ListView

Ora che è stato definito il layout **list_item** , il passaggio successivo consiste nell'aggiungere un `ListView` al layout principale. Questo `ListView` conterrà un elenco di **list_item**. 

Nel **Esplora soluzioni**aprire **risorse/layout/activity_main. aXML**. Nella **casella degli strumenti**individuare il widget `ListView` e trascinarlo nella **area di progettazione**. I `ListView` nella finestra di progettazione saranno vuoti, ad eccezione delle linee blu che delineano il bordo quando viene selezionato. È possibile visualizzare la **struttura del documento** per verificare che **ListView** sia stato aggiunto correttamente:

[![nuovo ListView](designer-walkthrough-images/vs/23-new-listview-w158-sml.png)](designer-walkthrough-images/vs/23-new-listview-w158.png#lightbox)

Per impostazione predefinita, al `ListView` viene assegnato un valore `Id` `@+id/listView1`.
Mentre `listView1` è ancora selezionato nella **struttura del documento**, aprire il riquadro **Proprietà** , fare clic su **Disponi per**e selezionare **categoria**.
Aprire **Main**, individuare la proprietà **ID** e modificarne il valore in `@+id/myListView`:

[![rinominare l'ID in ListView](designer-walkthrough-images/vs/24-change-id-w158-sml.png)](designer-walkthrough-images/vs/24-change-id-w158.png#lightbox)

A questo punto, l'interfaccia utente è pronta per essere usata.

### <a name="running-the-application"></a>Esecuzione dell'applicazione

Aprire **MainActivity.cs** e sostituire il codice con il codice seguente:

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using Android.Support.V7.App;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
    public class MainActivity : AppCompatActivity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.activity_main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
        }
    }

    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}

```

Questo codice utilizza un adapter `ListView` personalizzato per caricare le informazioni sui colori e visualizzare questi dati nell'interfaccia utente appena creata. Per evitare questo esempio, le informazioni sul colore sono hardcoded in un elenco, ma è possibile che l'adapter venga modificato per estrarre le informazioni sul colore da un'origine dati o per calcolarle in tempo reale. Per ulteriori informazioni sugli adapter `ListView`, vedere [ListView](~/android/user-interface/layouts/list-view/index.md).

Compilare ed eseguire l'applicazione. Lo screenshot seguente è un esempio di come viene visualizzata l'app quando è in esecuzione in un dispositivo:

[schermata finale![](designer-walkthrough-images/vs/25-final-screenshot-sml.png)](designer-walkthrough-images/vs/25-final-screenshot.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

### <a name="creating-a-new-project"></a>Creazione di un nuovo progetto

Il primo passaggio consiste nel creare un nuovo progetto Xamarin.Android.

Avviare Visual Studio per Mac e fare clic su **nuovo progetto...** . Scegliere il modello **app Android** e fare clic su **Avanti**:

[App vuota![Android](designer-walkthrough-images/xs/01-android-app-m75-sml.png)](designer-walkthrough-images/xs/01-android-app-m75.png#lightbox)

Assegnare alla nuova app il nome **DesignerWalkthrough**. In **piattaforme di destinazione**selezionare **più recente** e quindi fare clic su **Avanti**:

[app nome![](designer-walkthrough-images/xs/02-designer-walkthrough-m75-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough-m75.png#lightbox)

Nella successiva schermata della finestra di dialogo fare clic su **Crea**.

### <a name="adding-a-layout"></a>Aggiunta di un layout

Il passaggio successivo consiste nel creare un **LinearLayout** che conterrà gli elementi dell'interfaccia utente.

In Visual Studio per Mac fare clic con il pulsante destro del mouse su **risorse/layout** nel riquadro della **soluzione** e selezionare **Aggiungi > nuovo file.** Nella finestra di dialogo **nuovo file** selezionare **Android > layout**. Assegnare al file il nome **list_item** e fare clic su **nuovo**:

[![nuovo layout](designer-walkthrough-images/xs/03-new-layout-m75-sml.png)](designer-walkthrough-images/xs/03-new-layout-m75.png#lightbox)

Dopo l'aggiunta del file, il nuovo layout **list_item** viene visualizzato nel **area di progettazione** (se viene visualizzato il messaggio, *questo progetto contiene risorse non compilate correttamente, il rendering potrebbe essere interessato*, fare clic su **Compila > Compila tutto** per compilare il progetto):

[visualizzazione di progettazione![](designer-walkthrough-images/xs/04-designer-view-m75-sml.png)](designer-walkthrough-images/xs/04-designer-view-m75.png#lightbox)

Fare clic sulla scheda **origine** nella parte inferiore della finestra di progettazione per visualizzare l'origine XML per questo layout. Quando si fa clic sulla scheda **struttura documento** a destra, viene indicato che il layout contiene attualmente un singolo widget **LinearLayout** :

[XML di![designer](designer-walkthrough-images/xs/05-designer-xml-m75-sml.png)](designer-walkthrough-images/xs/05-designer-xml-m75.png#lightbox)

Il passaggio successivo consiste nel creare l'interfaccia utente per l'app del browser colori.

### <a name="creating-the-list-item-user-interface"></a>Creazione dell'interfaccia utente dell'elemento elenco

Fare clic sulla scheda **finestra di progettazione** nella parte inferiore della schermata per tornare all' **area di progettazione**. Nel riquadro **casella degli strumenti** a destra scorrere verso il basso fino alla sezione **Immagini & supporto** e individuare `ImageView`:

[![individuare ImageView](designer-walkthrough-images/xs/06-locate-imageview-m75-sml.png)](designer-walkthrough-images/xs/06-locate-imageview-m75.png#lightbox)

In alternativa, è possibile immettere *ImageView* nella barra di ricerca per individuare la `ImageView`:

[ricerca![ImageView](designer-walkthrough-images/xs/07-imageview-search-m75-sml.png)](designer-walkthrough-images/xs/07-imageview-search-m75.png#lightbox)

Trascinare questo `ImageView` sul **area di progettazione** (questo `ImageView` verrà usato per visualizzare un campione di colore nell'app del browser colori):

[![ImageView nell'area di disegno](designer-walkthrough-images/xs/08-imageview-on-canvas-m75-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas-m75.png#lightbox)

Trascinare quindi un widget `LinearLayout (Vertical)` dalla **casella degli strumenti** nel **area di progettazione**. Si noti che un contorno blu indica i limiti della `LinearLayout`aggiunta. La **struttura del documento** Mostra che è un elemento figlio di `LinearLayout`, situato sotto `imageView1 (ImageView)`:

[![struttura blu](designer-walkthrough-images/xs/10-blue-outline-m75-sml.png)](designer-walkthrough-images/xs/10-blue-outline-m75.png#lightbox)

Quando si seleziona il `ImageView` nella finestra di progettazione, il contorno blu si sposta per racchiudere l'`ImageView`. Inoltre, la selezione viene spostata in `imageView1 (ImageView)` nella **struttura del documento**:

[![selezionare ImageView](designer-walkthrough-images/xs/11-select-imageview-m75-sml.png)](designer-walkthrough-images/xs/11-select-imageview-m75.png#lightbox)

Trascinare quindi un widget `Text (Large)` dalla **casella degli strumenti** all'`LinearLayout`appena aggiunta. Si noti che quando si trascina il mouse sul **area di progettazione**, viene evidenziata la posizione in cui verrà inserito il nuovo widget.
Il widget `Text (Large)` dovrebbe trovarsi all'interno di `linearLayout1`, come illustrato di seguito:

[![Aggiungere widget di testo di grandi dimensioni](designer-walkthrough-images/xs/12-green-highlight-m75-sml.png)](designer-walkthrough-images/xs/12-green-highlight-m75.png#lightbox)

Successivamente, aggiungere un widget `Text (Small)` sotto il widget `Text (Large)`. A questo punto, il **area di progettazione** dovrebbe essere simile allo screenshot seguente:

[![Aggiungi widget di testo di piccole dimensioni](designer-walkthrough-images/xs/13-add-small-text-m75-sml.png)](designer-walkthrough-images/xs/13-add-small-text-m75.png#lightbox)

Se i due widget `textView` non sono all'interno di `linearLayout1`, è possibile trascinarli in `linearLayout1` nella **struttura del documento** e posizionarli in modo che vengano visualizzati come mostrato nella schermata precedente (rientrato in `linearLayout1`).

### <a name="arranging-the-user-interface"></a>Disposizione dell'interfaccia utente

Il passaggio successivo consiste nel modificare l'interfaccia utente per visualizzare la `ImageView` a sinistra, con i due `TextView` widget in pila a destra della `ImageView`.

1. Con la `ImageView` selezionata, fare clic sulla scheda **Proprietà** .

2. Appena sotto la scheda **Proprietà** , fare clic su **layout**.

3. Scorrere verso il basso fino a **ViewGroup** e modificare l'impostazione di `Width` in `wrap_content`:

[![imposta contenuto a capo](designer-walkthrough-images/xs/15-wrap-content-m75-sml.png)](designer-walkthrough-images/xs/15-wrap-content-m75.png#lightbox)

Un altro modo per modificare l'impostazione di `Width` consiste nel fare clic sul triangolo sul lato destro del widget per impostare la larghezza su `wrap_content`:

[![trascinare per impostare la larghezza](designer-walkthrough-images/xs/16-width-arrow-m75-sml.png)](designer-walkthrough-images/xs/16-width-arrow-m75.png#lightbox)

Se si fa nuovamente clic sul triangolo, viene restituita l'impostazione `Width` `match_parent`. Passare quindi al riquadro **struttura documento** e selezionare la `LinearLayout`radice:

[![selezionare LinearLayout radice](designer-walkthrough-images/xs/17-root-linearlayout-m75-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout-m75.png#lightbox)

Con la `LinearLayout` radice selezionata, tornare alla scheda **Proprietà** e fare clic su **widget**. Modificare l'impostazione di `Orientation` in `horizontal` come illustrato di seguito. A questo punto, la **area di progettazione** dovrebbe essere simile alla schermata seguente. Si noti che i widget `TextView` sono stati spostati a destra del `ImageView`:

[![selezionare orientamento orizzontale](designer-walkthrough-images/xs/18-horizontal-orientation-m75-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation-m75.png#lightbox)

### <a name="modifying-the-spacing"></a>Modifica della spaziatura

Il passaggio successivo consiste nel modificare le impostazioni di spaziatura interna e margini nell'interfaccia utente per fornire più spazio tra i widget. Selezionare il `ImageView` e fare clic sulla scheda **layout** in **Proprietà**. Modificare il `Min Width` `50dp`, il `Min Height` `70dp`e il `Padding` per `10dp`.
Questa operazione applica la spaziatura interna intorno a tutti i lati del `ImageView` e la allunga verticalmente:

[riempimento set![](designer-walkthrough-images/xs/20-padding-widths-m75-sml.png)](designer-walkthrough-images/xs/20-padding-widths-m75.png#lightbox)

Le impostazioni di riempimento in alto, a destra, in basso e a sinistra possono essere impostate in modo indipendente inserendo i valori rispettivamente nei campi `Top`, `Right`, `Bottom`e `Left` spaziatura interna. Ad esempio, impostare il valore di riempimento `Left` su `5dp` e i valori di spaziatura `Top`, `Right`e `Bottom` per `10dp`. Si noti che l'impostazione di `Padding` viene modificata in un elenco delimitato da virgole di questi valori:

[![impostazioni di riempimento personalizzate](designer-walkthrough-images/xs/21-custom-padding-m75-sml.png)](designer-walkthrough-images/xs/21-custom-padding-m75.png#lightbox)

Modificare quindi la posizione del widget `LinearLayout` che contiene i due widget `TextView`. Nella **struttura del documento**selezionare `linearLayout1`. Nel riquadro **Proprietà** selezionare la scheda **layout** . scorrere verso il basso fino alla sezione **ViewGroup** e impostare i margini `Left`, `Top`, `Right`e `Bottom` su `5dp`, `5dp`, `0dp`e `5dp` rispettivamente :

[![impostare i margini](designer-walkthrough-images/xs/22-margins-m75-sml.png)](designer-walkthrough-images/xs/22-margins-m75.png#lightbox)

### <a name="removing-the-default-image"></a>Rimozione dell'immagine predefinita

Poiché il `ImageView` viene usato per visualizzare i colori (anziché le immagini), il passaggio successivo consiste nel rimuovere l'origine immagine predefinita aggiunta dal modello.

1. Selezionare `ImageView`.

2. Fare clic sulla scheda **widget** in **Proprietà**.

3. Deselezionare l'impostazione `Src` in modo che sia vuota:

[![deselezionare l'impostazione src ImageView](designer-walkthrough-images/xs/23-clear-src-m75-sml.png)](designer-walkthrough-images/xs/23-clear-src-m75.png#lightbox)

Questa operazione rimuove `android:src="@android:drawable/ic_menu_gallery"` dal codice XML di origine per tale `ImageView`.

### <a name="adding-a-listview-container"></a>Aggiunta di un contenitore ListView

Ora che è stato definito il layout **list_item** , il passaggio successivo consiste nell'aggiungere un `ListView` al layout principale. Questo `ListView` conterrà un elenco di **list_item**. 

Nel **Esplora soluzioni**aprire **risorse/layout/Main. aXML**.
Fare clic sul widget `Button` (se presente) ed eliminarlo. Nella **casella degli strumenti**individuare il widget `ListView` e trascinarlo nella **area di progettazione**.
I `ListView` nella finestra di progettazione saranno vuoti, ad eccezione delle linee blu che delineano il bordo quando viene selezionato. È possibile visualizzare la **struttura del documento** per verificare che **ListView** sia stato aggiunto correttamente:

[![nuovo ListView](designer-walkthrough-images/xs/24-new-listview-m75-sml.png)](designer-walkthrough-images/xs/24-new-listview-m75.png#lightbox)

Per impostazione predefinita, al `ListView` viene assegnato un valore `Id` `@+id/listView1`.
Mentre `listView1` è ancora selezionato nella **struttura del documento**, aprire il riquadro **Proprietà** , fare clic su **Disponi per**e selezionare **categoria**.
Aprire **Main**, individuare la proprietà **ID** e modificarne il valore in `@+id/myListView`:

[![rinominare l'ID in ListView](designer-walkthrough-images/xs/25-change-id-m75-sml.png)](designer-walkthrough-images/xs/25-change-id-m75.png#lightbox)

A questo punto, l'interfaccia utente è pronta per essere usata.

### <a name="running-the-application"></a>Esecuzione dell'applicazione

Aprire **MainActivity.cs** e sostituire il codice con il codice seguente:

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", MainLauncher = true)]
    public class MainActivity : Activity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.Main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
        }
    }

    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}
```

Questo codice utilizza un adapter `ListView` personalizzato per caricare le informazioni sui colori e visualizzare questi dati nell'interfaccia utente appena creata. Per evitare questo esempio, le informazioni sul colore sono hardcoded in un elenco, ma è possibile che l'adapter venga modificato per estrarre le informazioni sul colore da un'origine dati o per calcolarle in tempo reale. Per ulteriori informazioni sugli adapter `ListView`, vedere [ListView](~/android/user-interface/layouts/list-view/index.md).

Compilare ed eseguire l'applicazione. Lo screenshot seguente è un esempio di come viene visualizzata l'app quando è in esecuzione in un dispositivo:

[schermata finale![](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)

-----

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato il processo di uso di Xamarin.Android Designer in Visual Studio per creare un'interfaccia utente per un'app di base.
È stato illustrato come creare l'interfaccia per un singolo elemento in un elenco e come aggiungere widget e disporli visivamente.
È stato inoltre illustrato come assegnare le risorse e quindi impostare varie proprietà su tali widget.
