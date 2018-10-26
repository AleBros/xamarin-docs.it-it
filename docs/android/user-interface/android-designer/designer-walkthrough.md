---
title: Usando la finestra di progettazione di xamarin. Android
description: Questo articolo è una procedura dettagliata della finestra di progettazione di xamarin. Android. Viene illustrato come creare un'interfaccia utente per un'app browser colore piccoli; Questa interfaccia utente viene creata interamente nella finestra di progettazione.
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 1174fe5cb417d4977fd6519086e6c4942e74c10b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120274"
---
# <a name="using-the-xamarinandroid-designer"></a>Usando la finestra di progettazione di xamarin. Android

_Questo articolo è una procedura dettagliata della finestra di progettazione di xamarin. Android. Viene illustrato come creare un'interfaccia utente per un'app browser colore piccoli; Questa interfaccia utente viene creata interamente nella finestra di progettazione._


## <a name="overview"></a>Panoramica

È possibile creare interfacce utente di Android in modo dichiarativo tramite i file XML o a livello di codice mediante la scrittura di codice. La finestra di progettazione di xamarin. Android consente agli sviluppatori di creare e modificare visivamente, layout dichiarativo senza richiedere la modifica manuale dei file XML. La finestra di progettazione fornisce anche un feedback in tempo reale che consente allo sviluppatore di valutare le modifiche dell'interfaccia utente senza dover ridistribuire l'applicazione in un dispositivo o a un emulatore. Queste funzionalità di progettazione possono velocizzare di enormemente lo sviluppo dell'interfaccia utente Android.
Questo articolo illustra come usare la finestra di progettazione di xamarin. Android per creare visivamente un'interfaccia utente.

## <a name="walkthrough"></a>Procedura dettagliata

L'obiettivo di questa procedura dettagliata consiste nell'usare la finestra di progettazione Android per creare un'interfaccia utente per un'app browser colore. L'app browser colore presenta un elenco di colori, i relativi nomi e i relativi valori RGB. Si apprenderà come aggiungere widget per la **nell'area di progettazione** , nonché il layout di questi widget visivamente. Successivamente, si apprenderà come modificare i widget in modo interattivo i **nell'area di progettazione** oppure usando la finestra di progettazione **proprietà** riquadro. Infine, si noterà come la progettazione appare quando l'app viene eseguita in un dispositivo o emulatore.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="creating-a-new-project"></a>Crea un nuovo progetto

Il primo passaggio consiste nel creare un nuovo progetto xamarin. Android. Avviare Visual Studio, fare clic su **nuovo progetto...** , scegliere il **Visual C#\# > Android > App per Android (Xamarin)** modello.
Denominare la nuova app **DesignerWalkthrough** e fare clic su **OK**.

[![App Android vuota](designer-walkthrough-images/vs/01-android-app-w158-sml.png)](designer-walkthrough-images/vs/01-android-app-w158.png#lightbox)

Nel **nuova App per Android** finestra di dialogo, scegliere **App vuota** e fare clic su **OK**:

[![Selezionando il modello di App Android vuota](designer-walkthrough-images/vs/02-blank-app-w158-sml.png)](designer-walkthrough-images/vs/02-blank-app-w158.png#lightbox)


### <a name="adding-a-layout"></a>Aggiunta di un layout

Il passaggio successivo consiste nel creare un **LinearLayout** quale saranno contenuti gli elementi dell'interfaccia utente. Fare doppio clic su **risorse/layout** nel **Esplora soluzioni** e selezionare **Aggiungi > Nuovo elemento...** . Nel **Aggiungi nuovo elemento** finestra di dialogo, seleziona **Layout Android**. Denominare il file **list_item** e fare clic su **Add**:

[![Nuovo layout](designer-walkthrough-images/vs/03-new-layout-w158-sml.png)](designer-walkthrough-images/vs/03-new-layout-w158.png#lightbox)

Il nuovo **list_item** layout viene visualizzato nella finestra di progettazione. Si noti che vengono visualizzati due riquadri &ndash; il *nell'area di progettazione* per il **list_item** è visibile nel riquadro sinistro, mentre la relativa origine XML viene visualizzato nel riquadro di destra. È possibile scambiare le posizioni del **nell'area di progettazione** e **origine** riquadri facendo il **Scambia riquadri** icona che si trova tra i due riquadri:

[![Visualizzazione di progettazione](designer-walkthrough-images/vs/04-designer-view-w158-sml.png)](designer-walkthrough-images/vs/04-designer-view-w158.png#lightbox)

Dal **vista** menu, fare clic su **Other Windows > Struttura documento** per aprire il **struttura documento**. Il **struttura documento** mostra che il layout include attualmente una singola **LinearLayout** widget:

[![Struttura documento](designer-walkthrough-images/vs/06-document-outline-w158-sml.png)](designer-walkthrough-images/vs/06-document-outline-w158.png#lightbox)

Il passaggio successivo consiste nel creare l'interfaccia utente per l'app browser colore all'interno di questo `LinearLayout`.

### <a name="creating-the-list-item-user-interface"></a>Creazione dell'interfaccia utente di elemento di elenco

Se il **casella degli strumenti** riquadro non viene visualizzato, fare clic sui **della casella degli strumenti** scheda a sinistra. Nel **casella degli strumenti**, scorrere verso il basso il **immagini ed elementi multimediali** sezione e scorrere ulteriormente verso il basso fino a individuare un `ImageView`:

[![Individuare ImageView](designer-walkthrough-images/vs/07-locate-imageview-w158-sml.png)](designer-walkthrough-images/vs/07-locate-imageview-w158.png#lightbox)

In alternativa, è possibile immettere *ImageView* nella barra di ricerca per individuare il `ImageView`:

[![Ricerca ImageView](designer-walkthrough-images/vs/08-imageview-search-w158-sml.png)](designer-walkthrough-images/vs/08-imageview-search-w158.png#lightbox)

Trascinare `ImageView` nell'area di progettazione (ciò `ImageView` verrà usato per visualizzare un campione di colore nell'app browser colore):

[![ImageView nell'area di disegno](designer-walkthrough-images/vs/09-imageview-on-canvas-w158-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas-w158.png#lightbox)

Procedere quindi trascinando una `LinearLayout (Vertical)` widget dal **casella degli strumenti** nella finestra di progettazione. Si noti che un bordo blu indica i limiti del controllo aggiunto `LinearLayout`. Il **struttura documento** mostra che è un figlio `LinearLayout`, che si trova sotto `imageView1 (ImageView)`:

[![Contorno blu](designer-walkthrough-images/vs/10-blue-outline-w158-sml.png)](designer-walkthrough-images/vs/10-blue-outline-w158.png#lightbox)

Quando si seleziona il `ImageView` nella finestra di progettazione, si sposta il contorno blu racchiudere il `ImageView`. Inoltre, la selezione si sposta alla `imageView1 (ImageView)` nella **struttura documento**:

[![Selezionare ImageView](designer-walkthrough-images/vs/11-select-imageview-w158-sml.png)](designer-walkthrough-images/vs/11-select-imageview-w158.png#lightbox)

Procedere quindi trascinando una `Text (Large)` widget dal **casella degli strumenti** in appena aggiunto `LinearLayout`. Si noti che la finestra di progettazione Usa verde Evidenzia per indicare dove verrà inserito il nuovo widget:

[![Verde evidenzia](designer-walkthrough-images/vs/12-green-highlight-w158-sml.png)](designer-walkthrough-images/vs/12-green-highlight-w158.png#lightbox)

Successivamente, aggiungere un `Text (Small)` widget riportato di seguito il `Text (Large)` widget:

[![Aggiungere il widget di testo di piccole dimensioni](designer-walkthrough-images/vs/13-add-small-text-w158-sml.png)](designer-walkthrough-images/vs/13-add-small-text-w158.png#lightbox)

A questo punto, l'area di progettazione dovrebbe essere simile alla seguente:

[![Layout della finestra di progettazione](designer-walkthrough-images/vs/14-raw-layout-w158-sml.png)](designer-walkthrough-images/vs/14-raw-layout-w158.png#lightbox)

Se i due `textView` widget non sono all'interno `linearLayout1`, è possibile trascinarli `linearLayout1` nel **struttura documento** e il posizionarli in modo che appaiano come illustrato nello screenshot precedente (rientrata `linearLayout1`).


### <a name="arranging-the-user-interface"></a>Disposizione dell'interfaccia utente

Il passaggio successivo consiste nel modificare l'interfaccia utente per visualizzare il `ImageView` a sinistra, con due `TextView` widget in pila a destra del `ImageView`.

1.  Selezionare `ImageView`.

2.  Nel **finestra delle proprietà**, immettere *larghezza* nella ricerca e individuare **Layout larghezza**.

3.  Modifica il **larghezza Layout** se si imposta su `wrap_content`:

![Set di incapsulamento contenuto](designer-walkthrough-images/vs/15-wrap-content-w158.png)

Un altro modo per modificare la `Width` impostazione consiste nel fare clic sul triangolo sul lato destro del widget per attivare o disattivare l'impostazione della larghezza in `wrap_content`:

![Trascinare per impostare la larghezza](designer-walkthrough-images/vs/15b-width-arrow-w158.png)

Fare nuovamente clic sul triangolo restituisce il `Width` se si imposta su `match_parent`. Passare quindi ad il **struttura documento** riquadro, selezionare la radice `LinearLayout`:

[![Selezionare radice LinearLayout](designer-walkthrough-images/vs/16-root-linearlayout-w158-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout-w158.png#lightbox)

Con la radice `LinearLayout` selezionato, tornare al **delle proprietà** riquadro immettere *orientamento* nel campo di ricerca e individuare il **orientamento** impostazione. Change **orientamento** a `horizontal`:

![Selezionare l'orientamento orizzontale](designer-walkthrough-images/vs/17-horizontal-orientation-w158.png)

A questo punto, l'area di progettazione dovrebbe essere simile alla seguente.
Si noti che il `TextView` i widget sono stati spostati a destra del `ImageView`:

[![Layout della finestra di progettazione](designer-walkthrough-images/vs/18-designer-layout-w158-sml.png)](designer-walkthrough-images/vs/18-designer-layout-w158.png#lightbox)

### <a name="modifying-the-spacing"></a>Modificare la spaziatura

Il passaggio successivo consiste nel modificare le impostazioni di spaziatura e margini nell'interfaccia utente per fornire più spazio tra i widget. Selezionare il `ImageView` nell'area di progettazione. Nel **delle proprietà** riquadro, immettere `min` nella casella di ricerca. Immettere `70dp` per **altezza minima** e `50dp` per **Min Width**:

[![Impostare l'altezza e larghezza](designer-walkthrough-images/vs/18b-set-height-width-sml.png)](designer-walkthrough-images/vs/18b-set-height-width.png#lightbox)

Nel **delle proprietà** riquadro, immettere `padding` nella ricerca e immettere `10dp` per **Padding**. Questi `minHeight`, `minWidth` e `padding` impostazioni aggiungere spaziatura interna intorno a tutti i lati del `ImageView` e per allungare verticale. Si noti che il layout XML cambia quando si immettono questi valori:

[![Impostare la spaziatura interna](designer-walkthrough-images/vs/19-padding-widths-w158-sml.png)](designer-walkthrough-images/vs/19-padding-widths-w158.png#lightbox)

Inferiore, sinistro destro del mouse, e impostazioni di spaziatura interna superiore può essere impostata in modo indipendente tramite l'immissione dei valori nel **riempimento basso**, **spaziatura interna sinistra**, **spaziatura interna destra**e  **Spaziatura in alto** campi, rispettivamente.
Ad esempio, impostare il **spaziatura interna sinistra** campo `5dp` e il **spaziatura interna inferiore**, **spaziatura interna destra**, e **superiore spaziatura interna** campi per `10dp`:

[![Impostazioni di riempimento personalizzata](designer-walkthrough-images/vs/20-custom-padding-w158-sml.png)](designer-walkthrough-images/vs/20-custom-padding-w158.png#lightbox)

Successivamente, regolare la posizione delle `LinearLayout` widget che contiene i due `TextView` widget. Nel **struttura documento**, selezionare `linearLayout1`. Nel **delle proprietà** finestra immettere `margin` nella casella di ricerca. Impostare **Layout dei margini inferiore**, **Layout Margin Left**, e **Layout margine superiore** a `5dp`. Impostare **Layout margine destro** a `0dp`:

[![Impostare i margini](designer-walkthrough-images/vs/21-margins-w158-sml.png)](designer-walkthrough-images/vs/21-margins-w158.png#lightbox)

### <a name="removing-the-default-image"></a>Rimuovere l'immagine predefinita

Poiché il `ImageView` viene usato per la visualizzazione di colori (invece di immagini), il passaggio successivo consiste nel rimuovere l'origine dell'immagine predefinito aggiunto dal modello.

1.  Selezionare il `ImageView` nella **nell'area di progettazione**.

2.  Nelle **delle proprietà**, immettere *src* nella casella di ricerca.

3.  Fare clic sul quadratino a destra del **Src** proprietà di impostazione e selezionare **reimpostare**:

[![Deselezionare l'impostazione di src ImageView](designer-walkthrough-images/vs/22-clear-img-src-w158-sml.png)](designer-walkthrough-images/vs/22-clear-img-src-w158.png#lightbox)

Questa operazione rimuove `android:src="@android:drawable/ic_menu_gallery"` dal XML di origine per cui `ImageView`.

### <a name="adding-a-listview-container"></a>Aggiunta di un contenitore di ListView

Ora che il **list_item** layout è definito, il passaggio successivo consiste nell'aggiungere un `ListView` al layout del principale. Ciò `ListView` conterrà un elenco delle **list_item**. 

Nel **Esplora soluzioni**aprire **Resources/layout/activity_main.axml**. Nel **casella degli strumenti**, individuare il `ListView` widget e trascinarla nel **nell'area di progettazione**. Il `ListView` nella finestra di progettazione verrà lasciato vuoto, ad eccezione delle linee blu che delineano il bordo quando viene selezionato. È possibile visualizzare il **struttura documento** per verificare che il **ListView** sia stato aggiunto correttamente:

[![Nuovo ListView](designer-walkthrough-images/vs/23-new-listview-w158-sml.png)](designer-walkthrough-images/vs/23-new-listview-w158.png#lightbox)

Per impostazione predefinita, il `ListView` viene assegnato un `Id` pari a `@+id/listView1`.
Mentre `listView1` sia ancora selezionato nel **struttura documento**, aprire la **delle proprietà** riquadro, fare clic su **Disponi per**e selezionare **categoria**.
Aprire **Main**, individuare il **Id** proprietà e modificarne il valore su `@+id/myListView`:

[![Rinominare l'id in myListView](designer-walkthrough-images/vs/24-change-id-w158-sml.png)](designer-walkthrough-images/vs/24-change-id-w158.png#lightbox)

A questo punto, l'interfaccia utente è pronto per l'uso.

### <a name="running-the-application"></a>Esecuzione dell'applicazione

Aprire **MainActivity.cs** e sostituire il codice con quanto segue:

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

Questo codice Usa un oggetto personalizzato `ListView` adapter per caricare le informazioni sui colori e tali dati vengono visualizzati nell'interfaccia utente che appena creato. Per semplificare questo esempio breve, le informazioni sul colore è hardcoded in un elenco, ma l'adapter può essere modificato per estrarre le informazioni sui colori da un'origine dati o per calcolarlo in tempo reale. Per altre informazioni sulle `ListView` adapter, vedere [ListView](~/android/user-interface/layouts/list-view/index.md).

Compilare ed eseguire l'applicazione. Lo screenshot seguente è riportato un esempio di come viene visualizzata l'app durante l'esecuzione in un dispositivo:

[![Schermata finale](designer-walkthrough-images/vs/25-final-screenshot-sml.png)](designer-walkthrough-images/vs/25-final-screenshot.png#lightbox)



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

### <a name="creating-a-new-project"></a>Crea un nuovo progetto

Il primo passaggio consiste nel creare un nuovo progetto xamarin. Android.

Avviare Visual Studio per Mac e fare clic su **nuovo progetto...** . Scegliere il **App per Android** modello, quindi scegliere **successivo**:

[![App Android vuota](designer-walkthrough-images/xs/01-android-app-m75-sml.png)](designer-walkthrough-images/xs/01-android-app-m75.png#lightbox)

Denominare la nuova app **DesignerWalkthrough**. Sotto **destinate a piattaforme**, selezionare **versione più recente e massimo** e fare clic su **Next**:

[![Nome app](designer-walkthrough-images/xs/02-designer-walkthrough-m75-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough-m75.png#lightbox)

Nella schermata successiva finestra di dialogo, fare clic su **Create**.

### <a name="adding-a-layout"></a>Aggiunta di un layout

Il passaggio successivo consiste nel creare un **LinearLayout** quale saranno contenuti gli elementi dell'interfaccia utente.

In Visual Studio per Mac, fare doppio clic su **risorse/layout** nel **soluzione** riempire e scegliere **Aggiungi > Nuovo File...** . Nel **nuovo File** finestra di dialogo, seleziona **Android > Layout**. Denominare il file **list_item** e fare clic su **New**:

[![Nuovo layout](designer-walkthrough-images/xs/03-new-layout-m75-sml.png)](designer-walkthrough-images/xs/03-new-layout-m75.png#lightbox)

Dopo l'aggiunta di questo file, il nuovo **list_item** viene visualizzato nel layout il **nell'area di progettazione** (se viene visualizzato il messaggio, *questo progetto contiene risorse che non sono state compilate correttamente, per il rendering potrebbe essere interessato*, fare clic su **compilazione > compila tutto** per compilare il progetto):

[![Visualizzazione di progettazione](designer-walkthrough-images/xs/04-designer-view-m75-sml.png)](designer-walkthrough-images/xs/04-designer-view-m75.png#lightbox)

Scegliere il **origine** scheda nella parte inferiore della finestra di progettazione per visualizzare l'origine XML per il layout. Quando si fa clic il **struttura documento** della scheda a destra, mostra che il layout include attualmente una singola **LinearLayout** widget:

[![XML della finestra di progettazione](designer-walkthrough-images/xs/05-designer-xml-m75-sml.png)](designer-walkthrough-images/xs/05-designer-xml-m75.png#lightbox)

Il passaggio successivo consiste nel creare l'interfaccia utente per l'app browser colore.

### <a name="creating-the-list-item-user-interface"></a>Creazione dell'interfaccia utente di elemento di elenco

Fare clic sul **Designer** nella parte inferiore della schermata per tornare alla scheda la **nell'area di progettazione**. Nel **casella degli strumenti** riquadro a destra, scorrere verso il basso il **immagini ed elementi multimediali** sezione e individuare `ImageView`:

[![Individuare ImageView](designer-walkthrough-images/xs/06-locate-imageview-m75-sml.png)](designer-walkthrough-images/xs/06-locate-imageview-m75.png#lightbox)

In alternativa, è possibile immettere *ImageView* nella barra di ricerca per individuare il `ImageView`:

[![Ricerca ImageView](designer-walkthrough-images/xs/07-imageview-search-m75-sml.png)](designer-walkthrough-images/xs/07-imageview-search-m75.png#lightbox)

Trascinare `ImageView` nella **nell'area di progettazione** (ciò `ImageView` verrà usato per visualizzare un campione di colore nell'app browser colore):

[![ImageView nell'area di disegno](designer-walkthrough-images/xs/08-imageview-on-canvas-m75-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas-m75.png#lightbox)

Procedere quindi trascinando una `LinearLayout (Vertical)` widget dal **casella degli strumenti** nel **nell'area di progettazione**. Si noti che un bordo blu indica i limiti del controllo aggiunto `LinearLayout`. Il **struttura documento** mostra che è un figlio `LinearLayout`, che si trova sotto `imageView1 (ImageView)`:

[![Contorno blu](designer-walkthrough-images/xs/10-blue-outline-m75-sml.png)](designer-walkthrough-images/xs/10-blue-outline-m75.png#lightbox)

Quando si seleziona il `ImageView` nella finestra di progettazione, si sposta il contorno blu racchiudere il `ImageView`. Inoltre, la selezione si sposta alla `imageView1 (ImageView)` nella **struttura documento**:

[![Selezionare ImageView](designer-walkthrough-images/xs/11-select-imageview-m75-sml.png)](designer-walkthrough-images/xs/11-select-imageview-m75.png#lightbox)

Procedere quindi trascinando una `Text (Large)` widget dal **casella degli strumenti** in appena aggiunto `LinearLayout`. Si noti che quando si trascina il puntatore del mouse sul **nell'area di progettazione**, vengono evidenziati in cui verrà inserito il nuovo widget.
Il `Text (Large)` widget deve trovarsi all'interno di `linearLayout1` come illustrato di seguito:

[![Aggiungere widget di testo di grandi dimensioni](designer-walkthrough-images/xs/12-green-highlight-m75-sml.png)](designer-walkthrough-images/xs/12-green-highlight-m75.png#lightbox)

Successivamente, aggiungere un `Text (Small)` widget riportato di seguito il `Text (Large)` widget. A questo punto, il **nell'area di progettazione** dovrebbe essere simile alla seguente:

[![Aggiungere il widget di testo di piccole dimensioni](designer-walkthrough-images/xs/13-add-small-text-m75-sml.png)](designer-walkthrough-images/xs/13-add-small-text-m75.png#lightbox)

Se i due `textView` widget non sono all'interno `linearLayout1`, è possibile trascinarli `linearLayout1` nel **struttura documento** e posizionarli in modo che vengano visualizzati come illustrato nello screenshot precedente (vengono visualizzati rientrati sotto `linearLayout1`).


### <a name="arranging-the-user-interface"></a>Disposizione dell'interfaccia utente

Il passaggio successivo consiste nel modificare l'interfaccia utente per visualizzare il `ImageView` a sinistra, con due `TextView` widget in pila a destra del `ImageView`.

1.  Con il `ImageView` selezionato, fare clic sui **proprietà** scheda.

2.  Immediatamente sotto il **delle proprietà** scheda, fare clic su **Layout**.

3.  Scorrere verso il basso **ViewGroup** e modificare le `Width` impostando su `wrap_content`:

[![Set di incapsulamento contenuto](designer-walkthrough-images/xs/15-wrap-content-m75-sml.png)](designer-walkthrough-images/xs/15-wrap-content-m75.png#lightbox)

Un altro modo per modificare la `Width` impostazione consiste nel fare clic sul triangolo sul lato destro del widget per attivare o disattivare l'impostazione della larghezza in `wrap_content`:

[![Trascinare per impostare la larghezza](designer-walkthrough-images/xs/16-width-arrow-m75-sml.png)](designer-walkthrough-images/xs/16-width-arrow-m75.png#lightbox)

Fare nuovamente clic sul triangolo restituisce il `Width` se si imposta su `match_parent`. Passare quindi ad il **struttura documento** riquadro, selezionare la radice `LinearLayout`:

[![Selezionare radice LinearLayout](designer-walkthrough-images/xs/17-root-linearlayout-m75-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout-m75.png#lightbox)

Con la radice `LinearLayout` selezionato, tornare al **delle proprietà** scheda e fare clic su **Widget**. Modifica il `Orientation` se si imposta su `horizontal` come illustrato di seguito. A questo punto, il **nell'area di progettazione** dovrebbe essere simile alla seguente. Si noti che il `TextView` i widget sono stati spostati a destra del `ImageView`:

[![Selezionare l'orientamento orizzontale](designer-walkthrough-images/xs/18-horizontal-orientation-m75-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation-m75.png#lightbox)


### <a name="modifying-the-spacing"></a>Modificare la spaziatura

Il passaggio successivo consiste nel modificare le impostazioni di spaziatura e margini nell'interfaccia utente per fornire più spazio tra i widget. Selezionare il `ImageView` e fare clic sui **Layout** disponibile nella scheda **proprietà**. Modifica il `Min Width` al `50dp`, il `Min Height` a `70dp`e il `Padding` per `10dp`.
Ciò si applica la spaziatura interna intorno a tutti i lati del `ImageView` ed elongates verticale:

[![Impostare la spaziatura interna](designer-walkthrough-images/xs/20-padding-widths-m75-sml.png)](designer-walkthrough-images/xs/20-padding-widths-m75.png#lightbox)

La parte superiore, destro, nella parte inferiore e impostazioni di spaziatura interna sinistra può essere impostare in modo indipendente tramite l'immissione dei valori nel `Top`, `Right`, `Bottom`, e `Left` padding campi, rispettivamente. Ad esempio, impostare il `Left` al valore di spaziatura interna `5dp` e il `Top`, `Right`, e `Bottom` spaziatura interna di valori da `10dp`. Si noti che il `Padding` impostazione viene modificata per un elenco delimitato da virgole dei valori seguenti:

[![Impostazioni di riempimento personalizzata](designer-walkthrough-images/xs/21-custom-padding-m75-sml.png)](designer-walkthrough-images/xs/21-custom-padding-m75.png#lightbox)

Successivamente, regolare la posizione delle `LinearLayout` widget che contiene i due `TextView` widget. Nel **struttura documento**, selezionare `linearLayout1`. Nel **delle proprietà** riquadro, selezionare la **Layout** scheda. Scorrere verso il basso il **ViewGroup** della sezione e impostare le `Left`, `Top`, `Right`, e `Bottom` ai margini per `5dp`, `5dp`, `0dp`, e `5dp` rispettivamente:

[![Impostare i margini](designer-walkthrough-images/xs/22-margins-m75-sml.png)](designer-walkthrough-images/xs/22-margins-m75.png#lightbox)

### <a name="removing-the-default-image"></a>Rimuovere l'immagine predefinita

Poiché il `ImageView` viene usato per la visualizzazione di colori (invece di immagini), il passaggio successivo consiste nel rimuovere l'origine dell'immagine predefinito aggiunto dal modello.

1.  Selezionare `ImageView`.

2.  Scegliere il **Widget** disponibile nella scheda **proprietà**.

3.  Cancella il `Src` impostando in modo che sia vuota:

[![Deselezionare l'impostazione di src ImageView](designer-walkthrough-images/xs/23-clear-src-m75-sml.png)](designer-walkthrough-images/xs/23-clear-src-m75.png#lightbox)

Questa operazione rimuove `android:src="@android:drawable/ic_menu_gallery"` dal XML di origine per cui `ImageView`.

### <a name="adding-a-listview-container"></a>Aggiunta di un contenitore di ListView

Ora che il **list_item** layout è definito, il passaggio successivo consiste nell'aggiungere un `ListView` al layout del principale. Ciò `ListView` conterrà un elenco delle **list_item**. 

Nel **Esplora soluzioni**aprire **Resources/layout/Main.axml**.
Fare clic su di `Button` widget (se presente) e lo elimini. Nel **casella degli strumenti**, individuare il `ListView` widget e trascinarla nel **nell'area di progettazione**.
Il `ListView` nella finestra di progettazione verrà lasciato vuoto, ad eccezione delle linee blu che delineano il bordo quando viene selezionato. È possibile visualizzare il **struttura documento** per verificare che il **ListView** sia stato aggiunto correttamente:

[![Nuovo ListView](designer-walkthrough-images/xs/24-new-listview-m75-sml.png)](designer-walkthrough-images/xs/24-new-listview-m75.png#lightbox)

Per impostazione predefinita, il `ListView` viene assegnato un `Id` pari a `@+id/listView1`.
Mentre `listView1` sia ancora selezionato nel **struttura documento**, aprire la **delle proprietà** riquadro, fare clic su **Disponi per**e selezionare **categoria**.
Aprire **Main**, individuare il **Id** proprietà e modificarne il valore su `@+id/myListView`:

[![Rinominare l'id in myListView](designer-walkthrough-images/xs/25-change-id-m75-sml.png)](designer-walkthrough-images/xs/25-change-id-m75.png#lightbox)

A questo punto, l'interfaccia utente è pronto per l'uso.

### <a name="running-the-application"></a>Esecuzione dell'applicazione

Aprire **MainActivity.cs** e sostituire il codice con quanto segue:

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

Questo codice Usa un oggetto personalizzato `ListView` adapter per caricare le informazioni sui colori e tali dati vengono visualizzati nell'interfaccia utente che appena creato. Per semplificare questo esempio breve, le informazioni sul colore è hardcoded in un elenco, ma l'adapter può essere modificato per estrarre le informazioni sui colori da un'origine dati o per calcolarlo in tempo reale. Per altre informazioni sulle `ListView` adapter, vedere [ListView](~/android/user-interface/layouts/list-view/index.md).

Compilare ed eseguire l'applicazione. Lo screenshot seguente è riportato un esempio di come viene visualizzata l'app durante l'esecuzione in un dispositivo:

[![Schermata finale](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)

-----


## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato il processo di uso della finestra di progettazione di xamarin. Android in Visual Studio per creare un'interfaccia utente per un'app di base.
È stato illustrato come creare l'interfaccia per un singolo elemento in un elenco e illustrato come aggiungere widget e disporli in modo visivo.
Spiega inoltre come assegnare risorse e quindi impostare varie proprietà a questi widget.
