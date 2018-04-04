---
title: Visualizzazioni alternative di Layout
description: In questo argomento viene illustrato come layout può essere con controllo delle versioni con qualificatori delle risorse. Ad esempio, si può essere una versione di un layout in cui viene utilizzato solo quando il dispositivo è in modalità orizzontale e una versione di layout che è solo per la modalità verticale.
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: d2228169ed5d8575c9e332c85d963fca0400dea8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="alternative-layout-views"></a>Visualizzazioni alternative di Layout

_In questo argomento viene illustrato come layout può essere con controllo delle versioni con qualificatori delle risorse. Ad esempio, si può essere una versione di un layout in cui viene utilizzato solo quando il dispositivo è in modalità orizzontale e una versione di layout che è solo per la modalità verticale._


## <a name="creating-alternative-layouts"></a>Creazione di layout alternativi

Quando si fa clic il **visualizzazione Layout alternativi** icona (a sinistra del **dispositivo**), viene aperto un riquadro di anteprima per visualizzare un elenco di layout alternativi disponibili nel progetto. Se nessun layout alternativi, il **predefinito** viene presentata una vista: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Riquadro di visualizzazione layout alternativi](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "riquadro visualizzazione layout alternativi")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Riquadro di visualizzazione layout alternativi](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

-----

Quando si fa clic su verde sul segno più accanto a **nuova versione**, **creare Layout variazione** verrà visualizzata la finestra di dialogo, in modo che è possibile selezionare i qualificatori di risorse per questa variante layout: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Creare layout variante](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "Crea variante di layout")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Creare una variante di layout](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

-----


Nell'esempio seguente, il qualificatore delle risorse per **orientamento dello schermo** è impostato su **orizzontale**e **dimensioni dello schermo** viene modificato in **grande**. Crea una nuova versione di layout denominata **grandi terreni**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Variante di grande terreni](alternative-layout-views-images/vs/03-large-land-sml.png "variazione terreni di grandi dimensioni")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Variante di terreni di grandi dimensioni](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

-----


Si noti che nel riquadro di anteprima a sinistra mostra gli effetti delle selezioni qualificatore risorse. Fare clic su **Aggiungi** crea il layout alternativo e attiva la finestra di progettazione di tale layout. Il **visualizzazione Layout alternativa** riquadro di anteprima indica il layout viene caricato nella finestra di progettazione tramite un puntatore a destra piccolo come indicato nella schermata seguente: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Indicatore del layout caricato](alternative-layout-views-images/vs/04-new-layout-sml.png "indicatore del layout caricato")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Indicatore del layout caricato](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

-----



## <a name="editing-alternative-layouts"></a>Modifica layout alternativi

Quando si crea layout alternativi, è spesso opportuno eseguire una singola modifica che si applica a tutte le versioni di scenari di layout. Potrebbe ad esempio, si desidera modificare il testo del pulsante in giallo in tutti i layout. Se si dispone di un numero elevato di layout, manutenzione può diventare rapidamente un'operazione complessa e soggetta a errori se si desidera propagare una singola modifica a tutte le versioni. 

Per semplificare la gestione di più versioni di layout, la finestra di progettazione fornisce un **multi-modifica** modalità che propaga le modifiche tra uno o più layout. Quando è presente più di un layout di **multi-modifica** viene visualizzata l'icona: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Icona di modifica più](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "multi-Modifica icona")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Icona di modifica di più](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

-----


Quando si sceglie il **multi-modifica** icona, vengono visualizzate le righe che indicano i layout sono collegati (come illustrato di seguito); ovvero, quando si apporta una modifica a un layout, tale modifica viene propagata a qualsiasi layout collegato. È possibile scollegare tutti i layout facendo clic sull'icona cerchiato indicato nella schermata seguente: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Scollega tutti i layout](alternative-layout-views-images/vs/06-multi-linked-sml.png "Scollega tutti i layout")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Scollega tutti i layout](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

-----


Se si dispone di più di due layout, è possibile attivare in modo selettivo il pulsante di modifica a sinistra di ogni anteprima di layout per determinare quali layout sono collegate tra loro. Ad esempio, se si desidera eseguire una singola modifica che propaga al primo e ultimo di tre layout, si sarebbe Scollega innanzitutto il layout intermedio come illustrato di seguito: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Scollega intermedio layout](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "Scollega intermedio layout")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Scollegare layout centrale](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)
 
-----
 

In questo esempio, una modifica apportata a un il **predefinito** o **lungo** verrà propagato a altri layout, ma non al layout di **grandi terreni** layout. 



### <a name="multi-edit-example"></a>Esempio di multi-modifica 

In generale, quando si apporta una modifica a un layout, la stessa modifica viene propagata a tutti gli altri layout collegato. Ad esempio, aggiungendo un nuovo `TextView` widget per il **predefinito** layout e la modifica del testo da string a `Portrait` causerà la stessa modifica da apportare ai layout tutti collegati. Ecco come appare **predefinito** layout: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Aggiungere TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "aggiungere TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Aggiungere TextView](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)
 
-----
 

Il `TextView` viene inoltre aggiunto al **grandi terreni** visualizzazione layout perché è collegato il **predefinito** layout: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Landscape TextView](alternative-layout-views-images/vs/09-landscape-textview-sml.png "Landscape TextView")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![TextView orizzontale](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)
 
-----
 

Ma cosa accade se si desidera apportare una modifica locale in un solo layout (ovvero, non si desidera la modifica di propagare a qualsiasi altro layout)? A tale scopo, è necessario scollegare il layout che si desidera modificare prima di modificarlo, come descritto di seguito. 



### <a name="making-local-changes"></a>Apportare le modifiche locali 

Si supponga che si desidera che entrambi i layout abbia aggiunto `TextView`, ma è anche possibile modificare la stringa di testo nel **grandi terreni** layout `Landscape` anziché `Portrait`. Se si apporta questa modifica al **grandi terreni** mentre entrambi i layout sono collegati, la modifica verrà propagate nuovamente il **predefinito** layout. Due layout prima di tutto necessario, pertanto, scollegare prima della modifica. Quando si modifica il testo in **grandi terreni** a `Landscape`, la finestra di progettazione contrassegna questa modifica con un intervallo di colore rosso per indicare che la modifica è locale per il **grandi terreni** layout e *non* ripropagati al **predefinito** layout: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Modifica locale](alternative-layout-views-images/vs/10-local-change-sml.png "modifica locale")](alternative-layout-views-images/vs/10-local-change.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Modifica locale](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)
 
-----
 

Quando si sceglie il **predefinito** layout per visualizzarlo, il `TextView` stringa di testo è ancora impostato su `Portrait`. 



## <a name="handling-conflicts"></a>Gestione dei conflitti 

Se si decide di modificare il colore del testo di **predefinito** layout verde, si noterà un'icona di avviso vengono visualizzati sul layout collegato. Fare clic su tale layout viene aperto il layout per rivelare il conflitto. Il widget che ha causato il conflitto viene evidenziato con una cornice di colore rossa e viene visualizzato il messaggio seguente: *le modifiche recenti hanno causato conflitti in questo layout alternativo*. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Modifica in conflitto](alternative-layout-views-images/vs/11-conflicting-change-sml.png "modifica in conflitto")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Modifica in conflitto](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)
 
-----
 

Oggetto *finestra conflitti* viene visualizzato a destra del widget per spiegare il conflitto: 

[![Avviso di conflitto](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

Il conflitto Mostra l'elenco di proprietà che sono stati modificati ed elenca i relativi valori. Fare clic su **conflitto ignorare** applica la modifica di proprietà solo per questo widget. Fare clic su **applica** si applica la modifica delle proprietà per questo widget nonché il widget controparte in collegato **predefinito** layout. Se vengono applicate tutte le modifiche alle proprietà, il conflitto viene eliminato automaticamente. 


### <a name="view-group-conflicts"></a>Visualizzazione gruppo è in conflitto 

Le modifiche alle proprietà non sono l'unica origine di conflitti. I conflitti possono essere rilevati durante l'inserimento o rimozione di widget. Ad esempio, quando il **grandi terreni** layout è scollegato dal **predefinito** layout e `TextView` nel **grandi terreni** layout viene trascinato e rilasciato sopra il `Button`, la finestra di progettazione contrassegna il widget spostato per indicare il conflitto:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Visualizzare i conflitti di gruppo](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "consente di visualizzare i conflitti di gruppo")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Visualizzazione dei conflitti di gruppo](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)
 
-----
 

Tuttavia, è non connesso alcun marcatore di `Button`. Anche se la posizione del `Button` è stata modificata, il `Button` non mostra le modifiche applicate specifiche per la **grandi terreni** configurazione. 

Se un `CheckBox` viene aggiunto per il **predefinito** layout, viene generato un altro conflitto e viene visualizzata un'icona di avviso tramite il **grandi terreni** layout: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Casella di controllo conflitto](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "conflitto di casella di controllo")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Conflitto di casella di controllo](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)
 
-----
 

Fare clic su di **grandi terreni** layout, viene visualizzato il conflitto. Viene visualizzato il messaggio seguente: *le modifiche recenti hanno causato conflitti in questo layout alternativo*. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Conflitto di layout ALT](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "conflitto layout Alt")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Conflitto di layout ALT](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)
 
-----
 

Inoltre, nella casella di conflitto viene visualizzato il messaggio seguente:

[![Messaggio di conflitto](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

Aggiunta di `CheckBox` provoca un conflitto in quanto il **grandi terreni** layout include le modifiche `LinearLayout` che lo contiene. Tuttavia, in questo caso nella casella di conflitto viene visualizzato il widget che è stato appena inserito il **predefinito** layout (il `CheckBox`).

Se si fa clic **conflitto ignorare**, la finestra di progettazione consente di risolvere il conflitto, consentendo il widget visualizzato nella casella di conflitto di trascinamento e rilascio nel layout in cui il widget risulta manca (in questo caso, il **grandi terreni**  layout): 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Risoluzione dei conflitti gruppo](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "risolto il conflitto di gruppo")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Risoluzione dei conflitti di gruppo](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)
 
-----
 

Come illustrato nell'esempio precedente con il `Button`, `CheckBox` non dispone di un indicatore di modifica rosso perché solo il `LinearLayout` contiene modifiche che sono state applicate nel **grandi terreni** layout.



### <a name="conflict-persistence"></a>Persistenza dei conflitti

I conflitti vengono rese persistenti nel file di layout come commenti XML, come illustrato di seguito:

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

Pertanto, quando un progetto viene chiuso e riaperto, tutti i conflitti verranno comunque essere presente &ndash; anche a quelli che sono stati ignorati.

