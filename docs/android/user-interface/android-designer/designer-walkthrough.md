---
title: Utilizzando la finestra di progettazione Android
description: In questo argomento è una procedura dettagliata della finestra di progettazione di xamarin. Viene illustrato come creare un'interfaccia utente per un'applicazione browser del colore; Questa interfaccia utente viene creata interamente nella finestra di progettazione.
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/10/2018
ms.openlocfilehash: 8d1dc410d5336d9c2505a18720cc7f734e838c39
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798632"
---
# <a name="using-the-android-designer"></a>Utilizzando la finestra di progettazione Android

_In questo argomento è una procedura dettagliata della finestra di progettazione di xamarin. Viene illustrato come creare un'interfaccia utente per un'applicazione browser del colore; Questa interfaccia utente viene creata interamente nella finestra di progettazione._


## <a name="overview"></a>Panoramica

È possibile creare interfacce utente Android in modo dichiarativo tramite i file XML o a livello di codice mediante la scrittura di codice. La finestra di progettazione di xamarin consente agli sviluppatori di creare e modificare il layout dichiarativo visivamente, senza la necessità di gestire la necessità di modifica manuale di file XML. La finestra di progettazione fornisce un feedback in tempo reale, che consente allo sviluppatore di valutare le modifiche dell'interfaccia utente senza dover ridistribuire l'applicazione in un dispositivo o un emulatore. Questo può velocizzare lo sviluppo di Android dell'interfaccia utente rappresenta un'enorme opportunità. In questo articolo è presente una procedura dettagliata viene illustrato come utilizzare la finestra di progettazione di xamarin per creare visivamente un'interfaccia utente.


## <a name="walkthrough"></a>Procedura dettagliata

L'obiettivo di questa procedura dettagliata consiste nell'utilizzare la finestra di progettazione Android per creare un'interfaccia utente per un'applicazione browser di colore di esempio che visualizza un elenco di colori, i relativi nomi e i relativi valori RGB. Viene illustrato come aggiungere widget nell'area di progettazione, nonché il layout di questi widget visivamente. Successivamente, verrà illustrato come modificare in modo interattivo nell'area di progettazione o utilizzando la finestra di progettazione di widget **proprietà riempimento**. Infine, vedremo aspetto il progetto quando si esegue l'app.

Iniziamo!


### <a name="creating-a-new-project"></a>Creazione di un nuovo progetto

Il primo passaggio consiste nel creare un nuovo progetto xamarin.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Avviare Visual Studio e fare clic su **nuovo progetto...**  quindi scegliere il **Visual C\# > Android > App per Android (Xamarin)** modello:

[![Applicazione vuota Android](designer-walkthrough-images/vs/01-android-app-sml.w157.png)](designer-walkthrough-images/vs/01-android-app.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Avviare Visual Studio per Mac e fare clic su **nuova soluzione...** . Scegliere il **App Android** modello e fare clic su **Avanti**:

[![Applicazione vuota Android](designer-walkthrough-images/xs/01-android-app-sml.png)](designer-walkthrough-images/xs/01-android-app.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Denominare la nuova app **DesignerWalkthrough** e fare clic su **OK**.

[![Nome applicazione](designer-walkthrough-images/vs/02-name-app-sml.png)](designer-walkthrough-images/vs/02-name-app.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Denominare la nuova app **DesignerWalkthrough**. In **piattaforme di destinazione**selezionare **più recenti e massimo** e fare clic su **Avanti**:

[![Nome applicazione](designer-walkthrough-images/xs/02-designer-walkthrough-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough.png#lightbox)

Nella schermata successiva finestra di dialogo, fare clic su **crea**.

-----



### <a name="adding-a-layout"></a>Aggiunta di un Layout

Creare un **LinearLayout** che verrà utilizzato per contenere l'utente gli elementi di interfaccia.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

In Visual Studio, fare doppio clic su **risorse/layout** nel **Esplora** e selezionare **Aggiungi > Nuovo elemento...** . Nel **Aggiungi nuovo elemento** finestra di dialogo Seleziona **Layout Android**. Nome del file **ListItem.axml** e fare clic su **Aggiungi**:

[![Nuovo layout](designer-walkthrough-images/vs/03-new-layout-sml.w157.png)](designer-walkthrough-images/vs/03-new-layout.w157.png#lightbox)

Il nuovo **ListItem** layout viene visualizzato nella finestra di progettazione:

[![Visualizzazione di progettazione](designer-walkthrough-images/vs/04-designer-view-sml.png)](designer-walkthrough-images/vs/04-designer-view.png#lightbox)

Fare clic su di **origine** scheda nella parte inferiore della finestra di progettazione per visualizzare l'origine XML per il layout:

[![Finestra di progettazione XML](designer-walkthrough-images/vs/05-designer-xml-sml.png)](designer-walkthrough-images/vs/05-designer-xml.png#lightbox)

Dal **vista** menu, fare clic su **altre finestre > Struttura documento** per aprire la **struttura documento**. Il **struttura documento** indica che il layout attualmente contiene un singolo **LinearLayout** widget:

[![Struttura documento](designer-walkthrough-images/vs/06-document-outline-sml.png)](designer-walkthrough-images/vs/06-document-outline.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

In Visual Studio per Mac, fare doppio clic su **risorse/layout** nel **soluzione** di riempimento e selezionare **Aggiungi > Nuovo File...** . Nel **nuovo File** finestra di dialogo Seleziona **Android > Layout**. Nome del file **ListItem** e fare clic su **New**:

[![Nuovo layout](designer-walkthrough-images/xs/03-new-layout-sml.png)](designer-walkthrough-images/xs/03-new-layout.png#lightbox)

Il nuovo **ListItem** layout viene visualizzato nella finestra di progettazione:

[![Visualizzazione di progettazione](designer-walkthrough-images/xs/04-designer-view-sml.png)](designer-walkthrough-images/xs/04-designer-view.png#lightbox)

Fare clic su di **origine** scheda nella parte inferiore della finestra di progettazione per visualizzare l'origine XML per il layout. Quando si sceglie il **struttura documento** scheda a destra, mostra che il layout attualmente contiene un singolo **LinearLayout** widget:

[![Finestra di progettazione XML](designer-walkthrough-images/xs/05-designer-xml-sml.png)](designer-walkthrough-images/xs/05-designer-xml.png#lightbox)

-----



### <a name="creating-the-list-item-user-interface"></a>Creazione dell'interfaccia utente di elemento elenco

Verrà successivamente, creare l'interfaccia utente per l'applicazione browser di colore.
Fare clic su di **progettazione** tab per tornare alla finestra di progettazione.
Nel **della casella degli strumenti**, scorrere verso il basso il **immagini & Media** sezione e consultare l'elenco di ogni elemento fino a individuare un *ImageView*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Individuare ImageView](designer-walkthrough-images/vs/07-locate-imageview-sml.png)](designer-walkthrough-images/vs/07-locate-imageview.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Individuare ImageView](designer-walkthrough-images/xs/06-locate-imageview-sml.png)](designer-walkthrough-images/xs/06-locate-imageview.png#lightbox)

-----

In alternativa, è possibile immettere *ImageView* nella barra di ricerca per individuare il `ImageView` widget:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Ricerca ImageView](designer-walkthrough-images/vs/08-imageview-search-sml.png)](designer-walkthrough-images/vs/08-imageview-search.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Ricerca ImageView](designer-walkthrough-images/xs/07-imageview-search-sml.png)](designer-walkthrough-images/xs/07-imageview-search.png#lightbox)

-----

Trascinare questo `ImageView` nell'area di progettazione:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![ImageView nell'area di disegno](designer-walkthrough-images/vs/09-imageview-on-canvas-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![ImageView nell'area di disegno](designer-walkthrough-images/xs/08-imageview-on-canvas-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas.png#lightbox)

-----

Questo `ImageView` verrà utilizzato per visualizzare un campione di colore nella nostra app browser colore.

Successivamente, trascinare un `LinearLayout (Vertical)` widget dal **della casella degli strumenti** nella finestra di progettazione. Si noti che un contorno blu indica i limiti dell'oggetto aggiunto `LinearLayout`e **struttura documento** indica che si trova direttamente sotto `imageView1 (ImageView)`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Struttura blu](designer-walkthrough-images/vs/10-blue-outline-sml.png)](designer-walkthrough-images/vs/10-blue-outline.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Struttura blu](designer-walkthrough-images/xs/10-blue-outline-sml.png)](designer-walkthrough-images/xs/10-blue-outline.png#lightbox)

-----

Quando si seleziona il `ImageView` nella finestra di progettazione, si sposta il contorno blu racchiudere il `ImageView`, nel **struttura documento**, la selezione si sposta a `imageView1 (ImageView)`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Selezionare ImageView](designer-walkthrough-images/vs/11-select-imageview-sml.png)](designer-walkthrough-images/vs/11-select-imageview.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Selezionare ImageView](designer-walkthrough-images/xs/11-select-imageview-sml.png)](designer-walkthrough-images/xs/11-select-imageview.png#lightbox)

-----

Successivamente, trascinare un `Text (Large)` widget dal **della casella degli strumenti** nelle aggiunte `LinearLayout`. Si noti che la finestra di progettazione utilizza verde vengono evidenziate per indicare dove verrà inserito il nuovo widget:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Evidenzia verde](designer-walkthrough-images/vs/12-green-highlight-sml.png)](designer-walkthrough-images/vs/12-green-highlight.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Evidenzia verde](designer-walkthrough-images/xs/12-green-highlight-sml.png)](designer-walkthrough-images/xs/12-green-highlight.png#lightbox)

-----

Successivamente, aggiungere un `Text (Small)` widget sotto il `Text (Large)` widget:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Aggiungere il widget di testo di piccole dimensioni](designer-walkthrough-images/vs/13-add-small-text-sml.png)](designer-walkthrough-images/vs/13-add-small-text.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Aggiungere il widget di testo di piccole dimensioni](designer-walkthrough-images/xs/13-add-small-text-sml.png)](designer-walkthrough-images/xs/13-add-small-text.png#lightbox)

-----

A questo punto, la finestra di progettazione dovrebbe essere simile nella schermata seguente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Layout della finestra di progettazione](designer-walkthrough-images/vs/14-raw-layout-sml.png)](designer-walkthrough-images/vs/14-raw-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Layout della finestra di progettazione](designer-walkthrough-images/xs/14-raw-layout-sml.png)](designer-walkthrough-images/xs/14-raw-layout.png#lightbox)

-----

Se i due `textView` widget non sono compresi in `linearLayout1`, è possibile trascinarli `linearLayout1` nel **struttura documento** e posizionarli in modo che appaiano come illustrato nella schermata precedente (rientrata `linearLayout1`).



### <a name="arranging-the-user-interface"></a>Disposizione dell'interfaccia utente

È opportuno modificare l'interfaccia utente per visualizzare il `ImageView` a sinistra, con i due `TextView` widget in pila a destra del `ImageView`.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Selezionare `ImageView`.

2.  Nel **finestra proprietà**, fare clic su di **categoria** ordinare icona e scorrere verso il basso per il **Layout - ViewGroup** sezione.

3.  Modifica il `layout_width` impostando su `wrap_content`:

![Impostare il contenuto di incapsulamento](designer-walkthrough-images/vs/15-wrap-content.png "Set incapsulamento content")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1.  Con il `ImageView` selezionato, fare clic su di **proprietà** scheda.

2.  Immediatamente sotto il **proprietà** scheda, fare clic su **Layout**.

3.  Scorrere verso il basso **ViewGroup** e modificare il `Width` impostando su `wrap_content`:

[![Set di contenuto di a capo automatico](designer-walkthrough-images/xs/15-wrap-content-sml.png)](designer-walkthrough-images/xs/15-wrap-content.png#lightbox)

-----

Un altro modo per modificare il `Width` impostazione consiste nel fare clic sul triangolo nella parte destra del widget per attivare o disattivare l'impostazione della larghezza in `wrap_content`:

[![Trascinare per impostare larghezza](designer-walkthrough-images/xs/16-width-arrow-sml.png)](designer-walkthrough-images/xs/16-width-arrow.png#lightbox)

Fare nuovamente clic sul triangolo restituisce il `Width` impostando su `match_parent`.

Successivamente, passare il **struttura documento** e selezionare la radice `LinearLayout`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Selezionare radice LinearLayout](designer-walkthrough-images/vs/16-root-linearlayout-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Selezionare radice LinearLayout](designer-walkthrough-images/xs/17-root-linearlayout-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout.png#lightbox)

-----
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Con la radice `LinearLayout` selezionata, tornare al **proprietà** finestra, fare clic sul **in ordine alfabetico** ordinare icona e scorrere fino a individuare `orientation`. Modifica il `orientation` impostando su `horizontal`:

![Selezionare l'orientamento orizzontale](designer-walkthrough-images/vs/17-horizontal-orientation.png "selezionare l'orientamento orizzontale")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Con la radice `LinearLayout` selezionata, tornare al **proprietà** scheda e fare clic su **Widget**. Modifica il `Orientation` impostando su `horizontal`:

[![Selezionare l'orientamento orizzontale](designer-walkthrough-images/xs/18-horizontal-orientation-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation.png#lightbox)

-----

A questo punto, la finestra di progettazione dovrebbe essere simile nella schermata seguente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Layout della finestra di progettazione](designer-walkthrough-images/vs/18-designer-layout-sml.png)](designer-walkthrough-images/vs/18-designer-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Layout della finestra di progettazione](designer-walkthrough-images/xs/19-designer-layout-sml.png)](designer-walkthrough-images/xs/19-designer-layout.png#lightbox)

-----


### <a name="modifying-the-spacing"></a>Modificare la spaziatura

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Successivamente, verrà modificata spaziatura e margini impostazioni nell'interfaccia utente per fornire più spazio tra i widget. Selezionare il `ImageView`, fare clic su di **categoria** sull'icona di ricerca nel **proprietà** finestra e scorrere verso il basso per il **Layout** sezione. Modifica il `Min Height` per `70dp`, `Min Width` per `50dp`e `padding` a `10dp`. Si applica la spaziatura interna intorno a tutti i lati del `ImageView` ed elongates verticalmente:

[![Impostare la spaziatura interna](designer-walkthrough-images/vs/19-padding-widths-sml.png)](designer-walkthrough-images/vs/19-padding-widths.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Successivamente, verrà modificata spaziatura e margini impostazioni nell'interfaccia utente per fornire più spazio tra i widget. Selezionare il `ImageView` e fare clic su di **Layout** scheda **proprietà**. Modifica il `Padding` per `10dp`, `Min Width` per `50dp`e `Min Height` a `70dp`. Si applica la spaziatura interna intorno a tutti i lati del `ImageView` ed elongates verticalmente:

[![Impostare la spaziatura interna](designer-walkthrough-images/xs/20-padding-widths-sml.png)](designer-walkthrough-images/xs/20-padding-widths.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Inferiore, sinistro, destro e le impostazioni di spaziatura interna superiore può essere impostato in modo indipendente dall'immissione di valori nel `paddingBottom`, `paddingLeft`, `paddingRight`, e `paddingTop` campi, rispettivamente.
Ad esempio, impostare il `paddingLeft` campo `5dp` e `paddingBottom`, `paddingRight`, e `paddingTop` campi `10dp`:

[![Impostazioni di riempimento personalizzata](designer-walkthrough-images/vs/20-custom-padding-sml.png)](designer-walkthrough-images/vs/20-custom-padding.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Superiore, destro, inferiore e impostazioni di spaziatura interna sinistra impostare in modo indipendente tramite l'immissione di valori nel `Top`, `Right`, `Bottom`, e `Left` padding campi, rispettivamente. Ad esempio, impostare il `Left` valore per `5dp` e `Top`, `Right`, e `Bottom` padding valori `10dp`. Si noti che il `Padding` impostazione viene modificata per un elenco delimitato da virgole dei valori seguenti:

[![Impostazioni di riempimento personalizzata](designer-walkthrough-images/xs/21-custom-padding-sml.png)](designer-walkthrough-images/xs/21-custom-padding.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Successivamente, modificare la posizione del `LinearLayout` widget che contiene i due `TextView` widget. Nel **struttura documento**selezionare `linearLayout1`. Nel **proprietà** finestra, scorrere verso il **Layout - ViewGroup** sezione. Impostare `layout_marginBottom`, `layout_marginLeft`, `layout_marginRight`, e `layout_marginTop` a `5dp`, `5dp`, `0dp`, e `5dp` rispettivamente:

[![Impostare i margini](designer-walkthrough-images/vs/21-margins-sml.png)](designer-walkthrough-images/vs/21-margins.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Successivamente, modificare la posizione del `LinearLayout` widget che contiene i due `TextView` widget. Nel **struttura documento**selezionare `linearLayout1`. Nel **proprietà** riquadro, selezionare il **Layout** scheda. Scorrere verso il basso il **ViewGroup** sezione e impostare il `Left`, `Top`, `Right`, e `Bottom` margini per `5dp`, `5dp`, `0dp`, e `5dp` rispettivamente:

[![Impostare i margini](designer-walkthrough-images/xs/22-margins-sml.png)](designer-walkthrough-images/xs/22-margins.png#lightbox)

-----



### <a name="removing-the-default-image"></a>Rimuovere l'immagine predefinita

Perché viene usata la `ImageView` per visualizzare i colori (anziché di immagini), rimuovere l'origine dell'immagine predefinita aggiunto dal modello.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Selezionare `ImageView`.

2.  In **proprietà**, trovare il `src` campo.

3.  Cancella il `src` impostazione in modo che sia vuota:

![Deselezionare l'impostazione di src ImageView](designer-walkthrough-images/vs/22-clear-img-src.png "deselezionare l'impostazione di src ImageView")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1.  Selezionare `ImageView`.

2.  Fare clic su di **Widget** scheda **proprietà**.

3.  Cancella il `Src` impostazione in modo che sia vuota:

[![Deselezionare l'impostazione di src ImageView](designer-walkthrough-images/xs/23-clear-src-sml.png)](designer-walkthrough-images/xs/23-clear-src.png#lightbox)

-----


### <a name="adding-a-listview-container"></a>Aggiunta di un contenitore di ListView

Ora che il **ListItem** layout è definito, verrà aggiunto un `ListView` per il layout principale. Questo `ListView` conterrà un elenco di **ListItems**.
Nel **della casella degli strumenti**, individuare il `ListView` widget e trascinarlo nell'area di progettazione. Il `ListView` nella finestra di progettazione verrà lasciato vuoto, ad eccezione di linee blu che descrive il bordo quando è selezionata. È possibile visualizzare il **struttura documento** per verificare che il **ListView** è stata aggiunta correttamente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Nuovo ListView](designer-walkthrough-images/vs/23-new-listview.png "nuovo ListView")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Nuovo controllo ListView](designer-walkthrough-images/xs/24-new-listview-sml.png)](designer-walkthrough-images/xs/24-new-listview.png#lightbox)

-----

Per impostazione predefinita, il `ListView` viene fornito un `Id` valore `@+id/listView1`.
Aprire il **Widget** scheda **proprietà** e modificare il `Id` a `@+id/myListView`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Rinominare id myListView](designer-walkthrough-images/vs/24-change-id.png "id Rename myListView")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Rinominare id myListView](designer-walkthrough-images/xs/25-change-id-sml.png)](designer-walkthrough-images/xs/25-change-id.png#lightbox)

-----

A questo punto, l'interfaccia utente è pronto per l'uso.



### <a name="running-the-application"></a>Esecuzione dell'applicazione


Aprire **Mainactivity** e sostituire il codice con quanto segue:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity (Label = "DesignerWalkthrough", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        List<ColorItem> colorItems = new List<ColorItem> ();
        ListView listView;

        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);

            SetContentView (Resource.Layout.Main);
            listView = FindViewById<ListView> (Resource.Id.myListView);

            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.DarkRed,
                                               ColorName = "Dark Red", Code = "8B0000" });
            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.SlateBlue,
                                               ColorName = "Slate Blue", Code = "6A5ACD" });
            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.ForestGreen,
                                               ColorName = "Forest Green", Code = "228B22" });

            listView.Adapter = new ColorAdapter (this, colorItems);
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
        public override View GetView (int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.ListItem, null);
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

Questo codice Usa un oggetto personalizzato `ListView` adapter per caricare le informazioni di colore e visualizzare i dati nell'interfaccia utente abbiamo appena creato. Per mantenere questo breve esempio, le informazioni sui colori è codificato in un elenco, ma l'adapter può essere modificato per estrarre informazioni sui colori da un'origine dati o calcolarla in tempo reale. Per ulteriori informazioni su `ListView` adapter, vedere [controlli ListView e schede](~/android/user-interface/layouts/list-view/index.md).

Compilare ed eseguire l'applicazione. Nella schermata riportata di seguito è riportato un esempio di come l'app viene visualizzata durante l'esecuzione in un dispositivo:

[![Schermata finale](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)



## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come utilizzare la finestra di progettazione di xamarin in Visual Studio per Mac per creare un'interfaccia utente. Successivamente, è stato illustrato come creare l'interfaccia per un singolo elemento in un elenco.
Inoltre, è stato esaminato come aggiungere widget e disporli visivamente, nonché come assegnare risorse e impostare varie proprietà per i widget. In conclusione, è illustrata la modalità di esecuzione l'interfaccia creata nella finestra di progettazione in un'applicazione di esempio.
