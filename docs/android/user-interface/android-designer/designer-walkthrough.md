---
title: Uso di Novell. Android Designer
description: Questo articolo è una procedura dettagliata di Novell. Android Designer. Viene illustrato come creare un'interfaccia utente per un'app per browser a colori piccoli; Questa interfaccia utente viene creata interamente nella finestra di progettazione.
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 9387b44419af87785d45a25ab254d3361a5615a3
ms.sourcegitcommit: c75c1d2132a4f46a7b38e454d5f24705165026bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68485929"
---
# <a name="using-the-xamarinandroid-designer"></a>Uso di Novell. Android Designer

_Questo articolo è una procedura dettagliata di Novell. Android Designer. Viene illustrato come creare un'interfaccia utente per un'app per browser a colori piccoli; Questa interfaccia utente viene creata interamente nella finestra di progettazione._


## <a name="overview"></a>Panoramica

Le interfacce utente Android possono essere create in modo dichiarativo tramite file XML o a livello di codice scrivendo codice. Novell. Android Designer consente agli sviluppatori di creare e modificare visivamente layout dichiarativi, senza richiedere la modifica manuale dei file XML. La finestra di progettazione fornisce anche commenti in tempo reale che consentono allo sviluppatore di valutare le modifiche dell'interfaccia utente senza dover ridistribuire l'applicazione in un dispositivo o in un emulatore. Queste funzionalità di progettazione possono velocizzare notevolmente lo sviluppo dell'interfaccia utente di Android.
Questo articolo illustra come usare Novell. Android Designer per creare visivamente un'interfaccia utente.

> [!TIP]
> Le versioni più recenti di Visual Studio supportano l'apertura dei file con estensione XML all'interno del Android Designer.
>
> I file con estensione aXML e XML sono supportati nell'Android Designer.

## <a name="walkthrough"></a>Procedura dettagliata

L'obiettivo di questa procedura dettagliata consiste nell'usare la Android Designer per creare un'interfaccia utente per un'app del browser colori di esempio. L'app color browser visualizza un elenco di colori, i relativi nomi e i rispettivi valori RGB. Si apprenderà come aggiungere widget alla **area di progettazione** e come disporre visivamente questi widget. Successivamente, si apprenderà come modificare i widget in modo interattivo nel **area di progettazione** o tramite il riquadro **Proprietà** della finestra di progettazione. Infine, si vedrà come appare la progettazione quando l'app viene eseguita in un dispositivo o in un emulatore.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="creating-a-new-project"></a>Crea un nuovo progetto

Il primo passaggio consiste nel creare un nuovo progetto Novell. Android. Avviare Visual Studio, fare clic su **nuovo progetto...** e scegliere il modello **\# Visual C > Android > Android app (Novell)** .
Assegnare alla nuova app il nome **DesignerWalkthrough** e fare clic su **OK**.

[![App vuota Android](designer-walkthrough-images/vs/01-android-app-w158-sml.png)](designer-walkthrough-images/vs/01-android-app-w158.png#lightbox)

Nella finestra di dialogo **nuova app Android** scegliere **app vuota** e fare clic su **OK**:

[![Selezione del modello app vuota Android](designer-walkthrough-images/vs/02-blank-app-w158-sml.png)](designer-walkthrough-images/vs/02-blank-app-w158.png#lightbox)


### <a name="adding-a-layout"></a>Aggiunta di un layout

Il passaggio successivo consiste nel creare un **LinearLayout** che conterrà gli elementi dell'interfaccia utente. Fare clic con il pulsante destro del mouse su **risorse/layout** nella **Esplora soluzioni** e scegliere **Aggiungi > nuovo elemento...** . Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **layout Android**. Denominare il file **list_item** e fare clic su **Aggiungi**:

[![Nuovo layout](designer-walkthrough-images/vs/03-new-layout-w158-sml.png)](designer-walkthrough-images/vs/03-new-layout-w158.png#lightbox)

Il nuovo layout **list_item** viene visualizzato nella finestra di progettazione. Si noti che vengono visualizzati &ndash; due riquadri, il *area di progettazione* per **list_item** è visibile nel riquadro sinistro mentre l'origine XML viene visualizzata nel riquadro di destra. È possibile scambiare le posizioni dei riquadri **area di progettazione** e **origine** facendo clic sull'icona **Scambia riquadri** presente tra i due riquadri:

[![Visualizzazione di progettazione](designer-walkthrough-images/vs/04-designer-view-w158-sml.png)](designer-walkthrough-images/vs/04-designer-view-w158.png#lightbox)

Dal menu **Visualizza** fare clic su **altre finestre > Struttura documento** per aprire la **struttura del documento**. La **struttura del documento** Mostra che il layout contiene attualmente un singolo widget **LinearLayout** :

[![Struttura documento](designer-walkthrough-images/vs/06-document-outline-w158-sml.png)](designer-walkthrough-images/vs/06-document-outline-w158.png#lightbox)

Il passaggio successivo consiste nel creare l'interfaccia utente per l'app del browser colori all' `LinearLayout`interno di questo.

### <a name="creating-the-list-item-user-interface"></a>Creazione dell'interfaccia utente dell'elemento elenco

Se il riquadro **casella degli strumenti** non viene visualizzato, fare clic sulla scheda **casella degli strumenti** a sinistra. Nella **casella degli strumenti**scorrere verso il basso fino alla sezione **Immagini & supporto** e scorrere verso il basso fino `ImageView`a individuare:

[![Individuare ImageView](designer-walkthrough-images/vs/07-locate-imageview-w158-sml.png)](designer-walkthrough-images/vs/07-locate-imageview-w158.png#lightbox)

In alternativa, è possibile immettere *ImageView* nella barra di ricerca per individuare `ImageView`:

[![Ricerca ImageView](designer-walkthrough-images/vs/08-imageview-search-w158-sml.png)](designer-walkthrough-images/vs/08-imageview-search-w158.png#lightbox)

Trascinarlo nell'area di progettazione `ImageView` (verrà usato per visualizzare un campione di colore nell'app del browser colori): `ImageView`

[![ImageView sull'area di disegno](designer-walkthrough-images/vs/09-imageview-on-canvas-w158-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas-w158.png#lightbox)

Trascinare quindi un `LinearLayout (Vertical)` widget dalla **casella degli strumenti** alla finestra di progettazione. Si noti che un contorno blu indica i limiti dell' `LinearLayout`oggetto aggiunto. La **struttura del documento** Mostra che è figlio di `LinearLayout`, che si trova in: `imageView1 (ImageView)`

[![Contorno blu](designer-walkthrough-images/vs/10-blue-outline-w158-sml.png)](designer-walkthrough-images/vs/10-blue-outline-w158.png#lightbox)

Quando si seleziona l' `ImageView` oggetto nella finestra di progettazione, il contorno blu si `ImageView`sposta per racchiudere l'oggetto. Inoltre, la selezione passa a `imageView1 (ImageView)` nella struttura del **documento**:

[![Seleziona ImageView](designer-walkthrough-images/vs/11-select-imageview-w158-sml.png)](designer-walkthrough-images/vs/11-select-imageview-w158.png#lightbox)

Trascinare quindi un `Text (Large)` widget dalla **casella degli strumenti** all'oggetto appena aggiunto `LinearLayout`. Si noti che nella finestra di progettazione vengono utilizzate le evidenziazioni verdi per indicare la posizione in cui verrà inserito il nuovo widget:

[![Evidenziazioni verdi](designer-walkthrough-images/vs/12-green-highlight-w158-sml.png)](designer-walkthrough-images/vs/12-green-highlight-w158.png#lightbox)

Aggiungere quindi un `Text (Small)` widget sotto il `Text (Large)` widget:

[![Aggiungi widget di testo di piccole dimensioni](designer-walkthrough-images/vs/13-add-small-text-w158-sml.png)](designer-walkthrough-images/vs/13-add-small-text-w158.png#lightbox)

A questo punto, l'area di progettazione dovrebbe somigliare allo screenshot seguente:

[![Layout della finestra di progettazione](designer-walkthrough-images/vs/14-raw-layout-w158-sml.png)](designer-walkthrough-images/vs/14-raw-layout-w158.png#lightbox)

Se i due `textView` widget non si trovano all' `linearLayout1`interno di `linearLayout1` , è possibile trascinarli nella **struttura del documento** e posizionarli in modo che vengano visualizzati `linearLayout1`come mostrato nella schermata precedente (rientrato in).


### <a name="arranging-the-user-interface"></a>Disposizione dell'interfaccia utente

Il passaggio successivo consiste nel modificare l'interfaccia utente per visualizzare `ImageView` l'oggetto a sinistra, con i `TextView` due widget `ImageView`in pila a destra di.

1.  Selezionare `ImageView`.

2.  Nella **finestra Proprietà**immettere *Width* nella casella di ricerca e individuare **Larghezza layout**.

3.  Modificare l'impostazione della **larghezza** del `wrap_content`layout in:

![Imposta contenuto a capo](designer-walkthrough-images/vs/15-wrap-content-w158.png)

Un altro modo per modificare `Width` l'impostazione consiste nel fare clic sul triangolo sul lato destro del widget per impostare la larghezza `wrap_content`su:

![Trascina per impostare la larghezza](designer-walkthrough-images/vs/15b-width-arrow-w158.png)

Se si fa nuovamente clic sul `Width` triangolo, `match_parent`l'impostazione viene restituita a. Passare quindi al riquadro **struttura documento** e selezionare la radice `LinearLayout`:

[![Seleziona LinearLayout radice](designer-walkthrough-images/vs/16-root-linearlayout-w158-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout-w158.png#lightbox)

Con la radice `LinearLayout` selezionata, tornare al riquadro **proprietà** , immettere *orientamento* nella casella di ricerca e individuare l'impostazione **orientamento** . Modificare  l'orientamento `horizontal`in:

![Seleziona orientamento orizzontale](designer-walkthrough-images/vs/17-horizontal-orientation-w158.png)

A questo punto, l'area di progettazione dovrebbe essere simile alla schermata seguente.
Si noti che `TextView` i widget sono stati spostati a destra `ImageView`del:

[![Layout della finestra di progettazione](designer-walkthrough-images/vs/18-designer-layout-w158-sml.png)](designer-walkthrough-images/vs/18-designer-layout-w158.png#lightbox)

### <a name="modifying-the-spacing"></a>Modifica della spaziatura

Il passaggio successivo consiste nel modificare le impostazioni di spaziatura interna e margini nell'interfaccia utente per fornire più spazio tra i widget. Selezionare nell' `ImageView` area di progettazione. Nel riquadro **Proprietà** immettere `min` nella casella di ricerca. Immettere `70dp` per **altezza minima** e `50dp` per **larghezza minima**:

[![Imposta altezza e larghezza](designer-walkthrough-images/vs/18b-set-height-width-sml.png)](designer-walkthrough-images/vs/18b-set-height-width.png#lightbox)

Nel riquadro **Proprietà** immettere `padding` nella casella di ricerca e immettere `10dp` per la **spaziatura interna**. Queste `minHeight`impostazioni e aggiungono`padding` spaziatura interna intorno a tutti i lati di esiallungano`ImageView`verticalmente `minWidth` . Si noti che il layout XML cambia quando si immettono i valori seguenti:

[![Imposta spaziatura interna](designer-walkthrough-images/vs/19-padding-widths-w158-sml.png)](designer-walkthrough-images/vs/19-padding-widths-w158.png#lightbox)

Le impostazioni di riempimento inferiore, sinistro, destro e superiore possono essere impostate in modo indipendente inserendo i valori **nei campi riempimento** **inferiore**, a **sinistra**, a **destra**e spaziatura interna, rispettivamente.
Ad esempio, impostare il campo di **riempimento** a `5dp` sinistra su e i campi **riempimento inferiore**, **riempimento a destra**e **riempimento superiore** per `10dp`:

[![Impostazioni di riempimento personalizzate](designer-walkthrough-images/vs/20-custom-padding-w158-sml.png)](designer-walkthrough-images/vs/20-custom-padding-w158.png#lightbox)

Modificare quindi la posizione del `LinearLayout` widget che contiene i due `TextView` widget. Nella **struttura del documento**selezionare `linearLayout1`. Nella finestra **Proprietà** immettere `margin` nella casella di ricerca. Imposta il margine **inferiore**del layout, il **margine**di layout a sinistra e il **margine di layout superiore** a `5dp`. Imposta il **margine di layout diritto** su `0dp`:

[![Imposta margini](designer-walkthrough-images/vs/21-margins-w158-sml.png)](designer-walkthrough-images/vs/21-margins-w158.png#lightbox)

### <a name="removing-the-default-image"></a>Rimozione dell'immagine predefinita

`ImageView` Poiché viene usato per visualizzare i colori (anziché le immagini), il passaggio successivo consiste nel rimuovere l'origine immagine predefinita aggiunta dal modello.

1.  Selezionare la `ImageView` nell' **area di progettazione**.

2.  In **Proprietà**immettere *src* nella casella di ricerca.

3.  Fare clic sul piccolo quadrato a destra dell'impostazione della proprietà **src** e selezionare **Reset (Reimposta**):

[![Deselezionare l'impostazione src di ImageView](designer-walkthrough-images/vs/22-clear-img-src-w158-sml.png)](designer-walkthrough-images/vs/22-clear-img-src-w158.png#lightbox)

Questa operazione `android:src="@android:drawable/ic_menu_gallery"` rimuove dal codice XML `ImageView`di origine.

### <a name="adding-a-listview-container"></a>Aggiunta di un contenitore ListView

Ora che è stato definito il layout **list_item** , il passaggio successivo consiste nell'aggiungere `ListView` un al layout principale. Questo `ListView` conterrà un elenco di **list_item**. 

Nel **Esplora soluzioni**aprire **risorse/layout/activity_main. aXML**. Nella **casella degli strumenti**individuare il `ListView` widget e trascinarlo sul **area di progettazione**. L' `ListView` oggetto nella finestra di progettazione sarà vuoto, ad eccezione delle linee blu che delineano il bordo quando viene selezionato. È possibile visualizzare la **struttura del documento** per verificare che **ListView** sia stato aggiunto correttamente:

[![Nuovo ListView](designer-walkthrough-images/vs/23-new-listview-w158-sml.png)](designer-walkthrough-images/vs/23-new-listview-w158.png#lightbox)

Per impostazione predefinita, `ListView` a viene `Id` assegnato il valore `@+id/listView1`.
Mentre `listView1` è ancora selezionato nella **struttura del documento**, aprire il riquadro **Proprietà** , fare clic su **Disponi per**e selezionare **categoria**.
Aprire **Main**, individuare la proprietà **ID** e modificarne il valore in `@+id/myListView`:

[![Rinomina ID in ListView](designer-walkthrough-images/vs/24-change-id-w158-sml.png)](designer-walkthrough-images/vs/24-change-id-w158.png#lightbox)

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

Questo codice utilizza un adapter `ListView` personalizzato per caricare le informazioni sui colori e visualizzare questi dati nell'interfaccia utente appena creata. Per evitare questo esempio, le informazioni sul colore sono hardcoded in un elenco, ma è possibile che l'adapter venga modificato per estrarre le informazioni sul colore da un'origine dati o per calcolarle in tempo reale. Per ulteriori informazioni sugli `ListView` adapter, vedere [ListView](~/android/user-interface/layouts/list-view/index.md).

Compilare ed eseguire l'applicazione. Lo screenshot seguente è un esempio di come viene visualizzata l'app quando è in esecuzione in un dispositivo:

[![Schermata finale](designer-walkthrough-images/vs/25-final-screenshot-sml.png)](designer-walkthrough-images/vs/25-final-screenshot.png#lightbox)



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

### <a name="creating-a-new-project"></a>Crea un nuovo progetto

Il primo passaggio consiste nel creare un nuovo progetto Novell. Android.

Avviare Visual Studio per Mac e fare clic su **nuovo progetto...** . Scegliere il modello **app Android** e fare clic su **Avanti**:

[![App vuota Android](designer-walkthrough-images/xs/01-android-app-m75-sml.png)](designer-walkthrough-images/xs/01-android-app-m75.png#lightbox)

Assegnare alla nuova app il nome **DesignerWalkthrough**. In **piattaforme di destinazione**selezionare **più recente** e quindi fare clic su **Avanti**:

[![App nome](designer-walkthrough-images/xs/02-designer-walkthrough-m75-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough-m75.png#lightbox)

Nella successiva schermata della finestra di dialogo fare clic su **Crea**.

### <a name="adding-a-layout"></a>Aggiunta di un layout

Il passaggio successivo consiste nel creare un **LinearLayout** che conterrà gli elementi dell'interfaccia utente.

In Visual Studio per Mac fare clic con il pulsante destro del mouse su **risorse/layout** nel riquadro della **soluzione** e selezionare **Aggiungi > nuovo file.** Nella finestra di dialogo **nuovo file** selezionare **Android > layout**. Assegnare al file il nome **list_item** e fare clic su **nuovo**:

[![Nuovo layout](designer-walkthrough-images/xs/03-new-layout-m75-sml.png)](designer-walkthrough-images/xs/03-new-layout-m75.png#lightbox)

Dopo l'aggiunta del file, il nuovo layout **list_item** viene visualizzato nel **area di progettazione** (se viene visualizzato il messaggio, *questo progetto contiene risorse non compilate correttamente, il rendering potrebbe essere interessato*, fare clic su **Compila > Compila tutto** per compilare il progetto):

[![Visualizzazione di progettazione](designer-walkthrough-images/xs/04-designer-view-m75-sml.png)](designer-walkthrough-images/xs/04-designer-view-m75.png#lightbox)

Fare clic sulla scheda **origine** nella parte inferiore della finestra di progettazione per visualizzare l'origine XML per questo layout. Quando si fa clic sulla scheda **struttura documento** a destra, viene indicato che il layout contiene attualmente un singolo widget **LinearLayout** :

[![XML di progettazione](designer-walkthrough-images/xs/05-designer-xml-m75-sml.png)](designer-walkthrough-images/xs/05-designer-xml-m75.png#lightbox)

Il passaggio successivo consiste nel creare l'interfaccia utente per l'app del browser colori.

### <a name="creating-the-list-item-user-interface"></a>Creazione dell'interfaccia utente dell'elemento elenco

Fare clic sulla scheda **finestra di progettazione** nella parte inferiore della schermata per tornare all' **area di progettazione**. Nel riquadro **casella degli strumenti** a destra scorrere verso il basso fino alla sezione **Immagini & supporto** e `ImageView`individuare:

[![Individuare ImageView](designer-walkthrough-images/xs/06-locate-imageview-m75-sml.png)](designer-walkthrough-images/xs/06-locate-imageview-m75.png#lightbox)

In alternativa, è possibile immettere *ImageView* nella barra di ricerca per individuare `ImageView`:

[![Ricerca ImageView](designer-walkthrough-images/xs/07-imageview-search-m75-sml.png)](designer-walkthrough-images/xs/07-imageview-search-m75.png#lightbox)

Trascinarlo `ImageView` nell' **area di progettazione** (verrà usato per visualizzare un campione di colore nell'app del browser colori): `ImageView`

[![ImageView sull'area di disegno](designer-walkthrough-images/xs/08-imageview-on-canvas-m75-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas-m75.png#lightbox)

Trascinare quindi un `LinearLayout (Vertical)` widget dalla **casella degli strumenti** nel **area di progettazione**. Si noti che un contorno blu indica i limiti dell' `LinearLayout`oggetto aggiunto. La **struttura del documento** Mostra che è un elemento figlio `LinearLayout`di, situato `imageView1 (ImageView)`di seguito:

[![Contorno blu](designer-walkthrough-images/xs/10-blue-outline-m75-sml.png)](designer-walkthrough-images/xs/10-blue-outline-m75.png#lightbox)

Quando si seleziona l' `ImageView` oggetto nella finestra di progettazione, il contorno blu si `ImageView`sposta per racchiudere l'oggetto. Inoltre, la selezione passa a `imageView1 (ImageView)` nella struttura del **documento**:

[![Seleziona ImageView](designer-walkthrough-images/xs/11-select-imageview-m75-sml.png)](designer-walkthrough-images/xs/11-select-imageview-m75.png#lightbox)

Trascinare quindi un `Text (Large)` widget dalla **casella degli strumenti** all'oggetto appena aggiunto `LinearLayout`. Si noti che quando si trascina il mouse sul **area di progettazione**, viene evidenziata la posizione in cui verrà inserito il nuovo widget.
Il `Text (Large)` widget dovrebbe trovarsi all' `linearLayout1` interno, come illustrato qui:

[![Aggiungere widget di testo di grandi dimensioni](designer-walkthrough-images/xs/12-green-highlight-m75-sml.png)](designer-walkthrough-images/xs/12-green-highlight-m75.png#lightbox)

Successivamente, aggiungere un `Text (Small)` widget sotto il `Text (Large)` widget. A questo punto, il **area di progettazione** dovrebbe essere simile allo screenshot seguente:

[![Aggiungi widget di testo di piccole dimensioni](designer-walkthrough-images/xs/13-add-small-text-m75-sml.png)](designer-walkthrough-images/xs/13-add-small-text-m75.png#lightbox)

Se i due `textView` widget non si trovano all' `linearLayout1`interno di `linearLayout1` , è possibile trascinarli nella **struttura del documento** e posizionarli in modo che vengano visualizzati `linearLayout1`come mostrato nella schermata precedente (rientrato in).


### <a name="arranging-the-user-interface"></a>Disposizione dell'interfaccia utente

Il passaggio successivo consiste nel modificare l'interfaccia utente per visualizzare `ImageView` l'oggetto a sinistra, con i `TextView` due widget `ImageView`in pila a destra di.

1.  Con l' `ImageView` opzione selezionata, fare clic sulla scheda **proprietà** .

2.  Appena sotto la scheda **Proprietà** , fare clic su **layout**.

3.  Scorrere verso il  basso fino a ViewGroup `Width` e modificare `wrap_content`l'impostazione in:

[![Imposta contenuto a capo](designer-walkthrough-images/xs/15-wrap-content-m75-sml.png)](designer-walkthrough-images/xs/15-wrap-content-m75.png#lightbox)

Un altro modo per modificare `Width` l'impostazione consiste nel fare clic sul triangolo sul lato destro del widget per impostare la larghezza `wrap_content`su:

[![Trascina per impostare la larghezza](designer-walkthrough-images/xs/16-width-arrow-m75-sml.png)](designer-walkthrough-images/xs/16-width-arrow-m75.png#lightbox)

Se si fa nuovamente clic sul `Width` triangolo, `match_parent`l'impostazione viene restituita a. Passare quindi al riquadro **struttura documento** e selezionare la radice `LinearLayout`:

[![Seleziona LinearLayout radice](designer-walkthrough-images/xs/17-root-linearlayout-m75-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout-m75.png#lightbox)

Con la radice `LinearLayout` selezionata, tornare alla scheda **proprietà** e fare clic su **widget**. Modificare l' `Orientation` impostazione in `horizontal` come illustrato di seguito. A questo punto, la **area di progettazione** dovrebbe essere simile alla schermata seguente. Si noti che `TextView` i widget sono stati spostati a destra `ImageView`del:

[![Seleziona orientamento orizzontale](designer-walkthrough-images/xs/18-horizontal-orientation-m75-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation-m75.png#lightbox)


### <a name="modifying-the-spacing"></a>Modifica della spaziatura

Il passaggio successivo consiste nel modificare le impostazioni di spaziatura interna e margini nell'interfaccia utente per fornire più spazio tra i widget. Selezionare e fare clic sulla scheda **layout** in **Proprietà.** `ImageView` `Min Width` Modificare in `50dp`, in`Min Height` e`Padding`in. `10dp` `70dp`
Viene applicata la spaziatura interna intorno a `ImageView` tutti i lati di e si allunga verticalmente:

[![Imposta spaziatura interna](designer-walkthrough-images/xs/20-padding-widths-m75-sml.png)](designer-walkthrough-images/xs/20-padding-widths-m75.png#lightbox)

Le impostazioni di riempimento in alto, a destra, in basso e a sinistra possono essere impostate in modo indipendente `Top`inserendo i `Bottom`valori rispettivamente `Left` nei campi di riempimento, `Right`, e. Ad esempio, impostare il `Left` valore di riempimento `5dp` su e `Top`i `Right`valori di `Bottom` riempimento, e `10dp`su. Si noti che `Padding` l'impostazione viene modificata in un elenco delimitato da virgole di questi valori:

[![Impostazioni di riempimento personalizzate](designer-walkthrough-images/xs/21-custom-padding-m75-sml.png)](designer-walkthrough-images/xs/21-custom-padding-m75.png#lightbox)

Modificare quindi la posizione del `LinearLayout` widget che contiene i due `TextView` widget. Nella **struttura del documento**selezionare `linearLayout1`. Nel riquadro **Proprietà** selezionare la scheda **layout** . Scorrere verso il basso fino alla sezione **ViewGroup** e `Left`impostare `Top`i `Right`margini `Bottom` ,, e `5dp`rispettivamente `5dp`su `0dp`,, `5dp` e:

[![Imposta margini](designer-walkthrough-images/xs/22-margins-m75-sml.png)](designer-walkthrough-images/xs/22-margins-m75.png#lightbox)

### <a name="removing-the-default-image"></a>Rimozione dell'immagine predefinita

`ImageView` Poiché viene usato per visualizzare i colori (anziché le immagini), il passaggio successivo consiste nel rimuovere l'origine immagine predefinita aggiunta dal modello.

1.  Selezionare `ImageView`.

2.  Fare clic sulla scheda **widget** in **Proprietà**.

3.  Deselezionare l' `Src` impostazione in modo che sia vuota:

[![Deselezionare l'impostazione src di ImageView](designer-walkthrough-images/xs/23-clear-src-m75-sml.png)](designer-walkthrough-images/xs/23-clear-src-m75.png#lightbox)

Questa operazione `android:src="@android:drawable/ic_menu_gallery"` rimuove dal codice XML `ImageView`di origine.

### <a name="adding-a-listview-container"></a>Aggiunta di un contenitore ListView

Ora che è stato definito il layout **list_item** , il passaggio successivo consiste nell'aggiungere `ListView` un al layout principale. Questo `ListView` conterrà un elenco di **list_item**. 

Nel **Esplora soluzioni**aprire **risorse/layout/Main. aXML**.
Fare clic `Button` sul widget, se presente, ed eliminarlo. Nella **casella degli strumenti**individuare il `ListView` widget e trascinarlo sul **area di progettazione**.
L' `ListView` oggetto nella finestra di progettazione sarà vuoto, ad eccezione delle linee blu che delineano il bordo quando viene selezionato. È possibile visualizzare la **struttura del documento** per verificare che **ListView** sia stato aggiunto correttamente:

[![Nuovo ListView](designer-walkthrough-images/xs/24-new-listview-m75-sml.png)](designer-walkthrough-images/xs/24-new-listview-m75.png#lightbox)

Per impostazione predefinita, `ListView` a viene `Id` assegnato il valore `@+id/listView1`.
Mentre `listView1` è ancora selezionato nella **struttura del documento**, aprire il riquadro **Proprietà** , fare clic su **Disponi per**e selezionare **categoria**.
Aprire **Main**, individuare la proprietà **ID** e modificarne il valore in `@+id/myListView`:

[![Rinomina ID in ListView](designer-walkthrough-images/xs/25-change-id-m75-sml.png)](designer-walkthrough-images/xs/25-change-id-m75.png#lightbox)

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

Questo codice utilizza un adapter `ListView` personalizzato per caricare le informazioni sui colori e visualizzare questi dati nell'interfaccia utente appena creata. Per evitare questo esempio, le informazioni sul colore sono hardcoded in un elenco, ma è possibile che l'adapter venga modificato per estrarre le informazioni sul colore da un'origine dati o per calcolarle in tempo reale. Per ulteriori informazioni sugli `ListView` adapter, vedere [ListView](~/android/user-interface/layouts/list-view/index.md).

Compilare ed eseguire l'applicazione. Lo screenshot seguente è un esempio di come viene visualizzata l'app quando è in esecuzione in un dispositivo:

[![Schermata finale](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)

-----


## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato il processo di uso di Novell. Android Designer in Visual Studio per creare un'interfaccia utente per un'app di base.
È stato illustrato come creare l'interfaccia per un singolo elemento in un elenco e come aggiungere widget e disporli visivamente.
È stato inoltre illustrato come assegnare le risorse e quindi impostare varie proprietà su tali widget.
