---
title: Visualizzazioni con layout alternativi
description: Questo argomento viene illustrato come i layout possono essere con controllo delle versioni con qualificatori delle risorse. Ad esempio, è possibile una versione di un layout che viene usato solo quando il dispositivo è in modalità orizzontale e una versione di layout che è solo per la modalità verticale.
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 03b80d3fb1ed7c8db108f86b3b3923c20e1d908f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61089819"
---
# <a name="alternative-layout-views"></a>Visualizzazioni con layout alternativi

_In questo argomento viene spiegato come aggiungere i layout di versione con qualificatori delle risorse. Ad esempio, la creazione di una versione di un layout che viene usato solo quando il dispositivo è in modalità orizzontale e una versione di layout che è solo per la modalità verticale._

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="creating-alternative-layouts"></a>Creazione di layout alternativi

Quando si fa clic il **visualizzazione Layout alternativi** icona (a sinistra del **dispositivo**), viene aperto un riquadro di anteprima per visualizzare un elenco di layout alternativi disponibili nel progetto. Se non esistono nessun layout alternativi, i **predefinito** viene presentata una vista: 

[![Riquadro di visualizzazione layout alternativo](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "riquadro visualizzazione layout alternativo")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

Quando si fa clic sul segno più verde accanto a **nuova versione**, il **Crea variante del Layout** verrà visualizzata la finestra di dialogo in cui è possibile selezionare i qualificatori di risorse per questa variante del layout: 

[![Crea variante del layout](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "Crea variante del layout")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

Nell'esempio seguente, il qualificatore delle risorse per la **orientamento dello schermo** è impostata su **Landscape**e il **dimensioni dello schermo** viene modificato in **grande**. Verrà creata una nuova versione del layout denominata **grandi ' s land**:

[![Variazione Large-'s land](alternative-layout-views-images/vs/03-large-land-sml.png "variazione land di grandi dimensioni")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

Si noti che nel riquadro di anteprima a sinistra vengono visualizzati gli effetti delle selezioni qualificatore risorse. Facendo clic **Add** crea il layout alternativo e attiva la finestra di progettazione di layout in questione. Il **visualizzazione Layout alternativo** riquadro di anteprima indica quale layout viene caricato nella finestra di progettazione tramite un puntatore a destra piccolo come indicato nello screenshot seguente: 

[![Indicatore del layout caricati](alternative-layout-views-images/vs/04-new-layout-sml.png "indicatore layout caricato")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)


## <a name="editing-alternative-layouts"></a>Modifica layout alternativi

Quando si crea layout alternativi, è spesso opportuno apportare una sola modifica che si applica a tutte le versioni con fork di un layout. Ad esempio, è possibile modificare il testo del pulsante in giallo in tutti i layout. Se si dispone di un numero elevato di layout ed è necessario propagare una singola modifica a tutte le versioni, manutenzione può diventare rapidamente un'operazione complessa e soggetta a errori.

Per semplificare la manutenzione di più versioni di layout, la finestra di progettazione è disponibile un' **multi-edit** modalità che propaga le modifiche apportate a uno o più layout. Quando più di un layout è presente, il **multi-edit** viene visualizzata l'icona: 

[![Icona di modifica più](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "multi-edit icona")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

Quando si sceglie la **multi-edit** icona, le righe vengono visualizzati che indicano che sono collegati i layout (come illustrato di seguito), vale a dire, quando si apporta una modifica a un layout, tale modifica viene propagata a tutti i layout collegati. È possibile scollegare tutti i layout facendo clic sull'icona in un cerchio indicato nello screenshot seguente: 

[![Scollega tutti i layout](alternative-layout-views-images/vs/06-multi-linked-sml.png "Scollega tutti i layout")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

Se si dispone di più di due layout, è possibile attivare in modo selettivo il pulsante di modifica a sinistra di ogni anteprima layout per determinare quali layout sono collegate tra loro. Ad esempio, se si desidera eseguire una singola modifica che viene propagata al primo e ultimo di tre layout, si sarebbe è prima necessario scollegare il layout intermedio come illustrato di seguito: 

[![Scollega layout intermedio](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "Scollega layout intermedio")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)

In questo esempio, una modifica apportata a entrambi il **predefinito** o **lungo** layout verrà propagato al altri layout, ma non il **grandi ' s land** layout.

### <a name="multi-edit-example"></a>Esempio di multi-modifica 

In generale, quando si apporta una modifica a un layout, la stessa modifica viene propagata a tutti gli altri layout collegato. Ad esempio, aggiungendo un nuovo `TextView` widget per il **Default** layout e la modifica del testo da string a `Portrait` causerà la stessa modifica da apportare ai layout di tutto collegato. Di seguito è riportato l'aspetto **predefinito** layout: 

[![Aggiungere TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "aggiungere TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)
 
Il `TextView` viene anche aggiunto al **grandi ' s land** layout visualizzazione perché è collegato al **Default** layout: 

[![Landscape TextView](alternative-layout-views-images/vs/09-landscape-textview-sml.png "Landscape TextView")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)
 
Ma cosa accade se si vuole apportare una modifica locale in un solo layout (vale a dire, non si desidera la modifica da propagare a qualsiasi di altri layout)? A tale scopo, è necessario scollegare il layout che si desidera modificare prima di modificarlo, come descritto di seguito. 

### <a name="making-local-changes"></a>Apportare le modifiche locali 

Si supponga che si vuole che entrambi i layout avere aggiunto `TextView`, ma è anche possibile modificare la stringa di testo nel **grandi ' s land** layout `Landscape` anziché `Portrait`. Se si apporta questa modifica al **grandi ' s land** anche se entrambi i layout sono collegati, la modifica verrà propagata al **predefinito** layout. Pertanto, è necessario prima di eliminare i due layout prima della modifica. Quando si modifica il testo nella **grandi ' s land** al `Landscape`, la finestra di progettazione contrassegna questa modifica a un frame rosso per indicare che la modifica è locale per il **grandi ' s land** layout ed è *non* propagate le **predefinito** layout: 

[![Modifica locale](alternative-layout-views-images/vs/10-local-change-sml.png "modifiche locale")](alternative-layout-views-images/vs/10-local-change.png#lightbox)
 
Quando si fa clic il **predefiniti** layout per la visualizzazione, il `TextView` stringa di testo è ancora impostata su `Portrait`. 

## <a name="handling-conflicts"></a>Gestione dei conflitti 

Se si decide di modificare il colore del testo nel **predefinito** layout verde, verrà visualizzato un'icona di avviso sul layout collegato. Facendo clic su tale layout viene aperto il layout per rivelare il conflitto. Il widget che ha causato il conflitto viene evidenziato un frame di colore rosso e viene visualizzato il messaggio seguente: *Le modifiche recenti hanno causato conflitti in questo layout alternativo*. 

[![Modifica in conflitto](alternative-layout-views-images/vs/11-conflicting-change-sml.png "modifica in conflitto")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)
 
Oggetto *finestra conflitti* viene visualizzato a destra del widget per spiegare il conflitto: 

[![Avviso di conflitto](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

La casella di conflitto Mostra l'elenco delle proprietà che sono state modificate e vengono elencati i relativi valori. Facendo clic **Ignora conflitto** la modifica di proprietà viene applicata solo a questo widget. Facendo clic **Apply** si applica la modifica delle proprietà per questo widget anche il widget controparte in collegato **predefinito** layout. Se vengono applicate tutte le modifiche alle proprietà, il conflitto viene automaticamente eliminato. 

### <a name="view-group-conflicts"></a>Visualizzare i conflitti di gruppo 

Le modifiche alle proprietà non sono l'unica fonte di conflitti. I conflitti possono essere rilevati durante l'inserimento o rimozione di widget. Ad esempio, quando il **grandi ' s land** layout è scollegato dal **predefinito** layout e il `TextView` nel **grandi ' s land** layout viene trascinato e rilasciato in precedenza il `Button`, la finestra di progettazione contrassegna il widget spostato per indicare il conflitto:

[![Visualizzare i conflitti di gruppo](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "visualizzazione conflitto di gruppo")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)
 
Tuttavia, non è presente alcun marcatore sul `Button`. Anche se la posizione del `Button` è stato modificato, il `Button` non Mostra modifiche applicate specifiche per il **grandi ' s land** configurazione. 

Se un `CheckBox` viene aggiunto al **Default** layout, viene generato il conflitto di un altro e viene visualizzata un'icona di avviso tramite il **grandi ' s land** layout: 

[![Casella di controllo conflitti](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "conflitto di casella di controllo")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)
 
Facendo clic sui **grandi ' s land** layout rivela il conflitto. Viene visualizzato il seguente messaggio: *Le modifiche recenti hanno causato conflitti in questo layout alternativo*: 

[![Conflitto di layout ALT](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "conflitto layout Alt")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)

Inoltre, la casella di conflitto Visualizza il messaggio seguente:

[![Messaggio di conflitto](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

Aggiunta il `CheckBox` provoca un conflitto in quanto il **grandi ' s land** layout contiene modifiche `LinearLayout` che lo contiene. Tuttavia, in questo caso il conflitto viene visualizzata widget è stato semplicemente inserito nel **predefinite** layout (il `CheckBox`).

Se si fa clic **Ignora conflitto**, la finestra di progettazione risolve il conflitto, consentendo il widget visualizzato nella casella di conflitti in cui risulta manca il widget da trascinare e rilasciare nel layout (in questo caso, il **grandi ' s land**  layout): 

[![Gruppo conflitto risolto](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "gruppo conflitto risolto")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)

Come illustrato nell'esempio precedente con il `Button`, il `CheckBox` non dispone di un indicatore di modifica di colore rosso perché solo le `LinearLayout` contiene modifiche che sono state applicate nel **grandi ' s land** layout.



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="creating-alternative-layouts"></a>Creazione di layout alternativi

Quando si fa clic il **visualizzazione Layout alternativi** icona (a sinistra del **dispositivo**), viene aperto un riquadro di anteprima per visualizzare un elenco di layout alternativi disponibili nel progetto. Se non esistono nessun layout alternativi, i **predefinito** viene presentata una vista: 

[![Riquadro di visualizzazione layout alternativo](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

Quando si fa clic sul segno più verde accanto a **nuova versione**, il **Crea variante del Layout** verrà visualizzata la finestra di dialogo in cui è possibile selezionare i qualificatori di risorse per questa variante del layout: 

[![Crea variante del layout](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

Nell'esempio seguente, il qualificatore delle risorse per la **orientamento dello schermo** è impostata su **Landscape**e il **dimensioni dello schermo** viene modificato in **grande**. Verrà creata una nuova versione del layout denominata **grandi ' s land**:

[![Grande-land variazione](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

Si noti che nel riquadro di anteprima a sinistra vengono visualizzati gli effetti delle selezioni qualificatore risorse. Facendo clic **Add** crea il layout alternativo e attiva la finestra di progettazione di layout in questione. Il **visualizzazione Layout alternativo** riquadro di anteprima indica quale layout viene caricato nella finestra di progettazione tramite un puntatore a destra piccolo come indicato nello screenshot seguente: 

[![Indicatore del layout caricato](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>Modifica layout alternativi

Quando si crea layout alternativi, è spesso opportuno apportare una sola modifica che si applica a tutte le versioni con fork di un layout. Ad esempio, è possibile modificare il testo del pulsante in giallo in tutti i layout. Se si dispone di un numero elevato di layout ed è necessario propagare una singola modifica a tutte le versioni, manutenzione può diventare rapidamente un'operazione complessa e soggetta a errori.

Per semplificare la manutenzione di più versioni di layout, la finestra di progettazione è disponibile un' **multi-edit** modalità che propaga le modifiche apportate a uno o più layout. Quando più di un layout è presente, il **multi-edit** viene visualizzata l'icona: 

[![Icona di multi-modifica](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

Quando si sceglie la **multi-edit** icona, le righe vengono visualizzati che indicano che sono collegati i layout (come illustrato di seguito), vale a dire, quando si apporta una modifica a un layout, tale modifica viene propagata a tutti i layout collegati. È possibile scollegare tutti i layout facendo clic sull'icona in un cerchio indicato nello screenshot seguente: 

[![Scollega tutti i layout](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

Se si dispone di più di due layout, è possibile attivare in modo selettivo il pulsante di modifica a sinistra di ogni anteprima layout per determinare quali layout sono collegate tra loro. Ad esempio, se si desidera eseguire una singola modifica che viene propagata al primo e ultimo di tre layout, si sarebbe è prima necessario scollegare il layout intermedio come illustrato di seguito: 

[![Scollega layout intermedio](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)

In questo esempio, una modifica apportata a entrambi il **predefinito** o **lungo** layout verrà propagato a altri layout, ma non il **grandi ' s land** layout. 

### <a name="multi-edit-example"></a>Esempio di multi-modifica 

In generale, quando si apporta una modifica a un layout, la stessa modifica viene propagata a tutti gli altri layout collegato. Ad esempio, aggiungendo un nuovo `TextView` widget per il **Default** layout e la modifica del testo da string a `Portrait` causerà la stessa modifica da apportare ai layout di tutto collegato. Di seguito è riportato l'aspetto **predefinito** layout: 

[![Aggiungere TextView](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)

Il `TextView` viene anche aggiunto al **grandi ' s land** layout visualizzazione perché è collegato al **Default** layout: 

[![Panorama applicativo TextView](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)
 
Ma cosa accade se si vuole apportare una modifica locale in un solo layout (vale a dire, non si desidera la modifica da propagare a qualsiasi di altri layout)? A tale scopo, è necessario scollegare il layout che si desidera modificare prima di modificarlo, come descritto di seguito. 

### <a name="making-local-changes"></a>Apportare le modifiche locali 

Si supponga che si vuole che entrambi i layout avere aggiunto `TextView`, ma è anche possibile modificare la stringa di testo nel **grandi ' s land** layout `Landscape` anziché `Portrait`. Se si apporta questa modifica al **grandi ' s land** anche se entrambi i layout sono collegati, la modifica verrà propagata al **predefinito** layout. Pertanto, è necessario prima di eliminare i due layout prima della modifica. Quando si modifica il testo nella **grandi ' s land** al `Landscape`, la finestra di progettazione contrassegna questa modifica a un frame rosso per indicare che la modifica è locale per il **grandi ' s land** layout ed è *non* propagate le **predefinito** layout: 

[![Modifiche locale](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)

Quando si fa clic il **predefiniti** layout per la visualizzazione, il `TextView` stringa di testo è ancora impostata su `Portrait`. 

## <a name="handling-conflicts"></a>Gestione dei conflitti 

Se si decide di modificare il colore del testo nel **predefinito** layout verde, verrà visualizzato un'icona di avviso sul layout collegato. Facendo clic su tale layout viene aperto il layout per rivelare il conflitto. Il widget che ha causato il conflitto viene evidenziato un frame di colore rosso e viene visualizzato il messaggio seguente: *Le modifiche recenti hanno causato conflitti in questo layout alternativo*. 

[![Modifica in conflitto](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)

Oggetto *finestra conflitti* viene visualizzato a destra del widget per spiegare il conflitto: 

[![Avviso di conflitto](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

La casella di conflitto Mostra l'elenco delle proprietà che sono state modificate e vengono elencati i relativi valori. Facendo clic **Ignora conflitto** la modifica di proprietà viene applicata solo a questo widget. Facendo clic **Apply** si applica la modifica delle proprietà per questo widget anche il widget controparte in collegato **predefinito** layout. Se vengono applicate tutte le modifiche alle proprietà, il conflitto viene automaticamente eliminato. 

### <a name="view-group-conflicts"></a>Visualizzare i conflitti di gruppo 

Le modifiche alle proprietà non sono l'unica fonte di conflitti. I conflitti possono essere rilevati durante l'inserimento o rimozione di widget. Ad esempio, quando il **grandi ' s land** layout è scollegato dal **predefinito** layout e il `TextView` nel **grandi ' s land** layout viene trascinato e rilasciato in precedenza il `Button`, la finestra di progettazione contrassegna il widget spostato per indicare il conflitto:

[![Visualizzazione dei conflitti di gruppo](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)
 
Tuttavia, non è presente alcun marcatore sul `Button`. Anche se la posizione del `Button` è stato modificato, il `Button` non Mostra modifiche applicate specifiche per il **grandi ' s land** configurazione. 

Se un `CheckBox` viene aggiunto al **Default** layout, viene generato il conflitto di un altro e viene visualizzata un'icona di avviso tramite il **grandi ' s land** layout: 

[![Casella di controllo conflitti](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)
 
Facendo clic sui **grandi ' s land** layout rivela il conflitto. Viene visualizzato il seguente messaggio: *Le modifiche recenti hanno causato conflitti in questo layout alternativo*. 

[![Conflitto di layout ALT](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)
 
Inoltre, la casella di conflitto Visualizza il messaggio seguente:

[![Messaggio di conflitto](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

Aggiunta il `CheckBox` provoca un conflitto in quanto il **grandi ' s land** layout contiene modifiche `LinearLayout` che lo contiene. Tuttavia, in questo caso il conflitto viene visualizzata widget è stato semplicemente inserito nel **predefinite** layout (il `CheckBox`).

Se si fa clic **Ignora conflitto**, la finestra di progettazione risolve il conflitto, consentendo il widget visualizzato nella casella di conflitti in cui risulta manca il widget da trascinare e rilasciare nel layout (in questo caso, il **grandi ' s land**  layout): 

[![Gruppo conflitto risolto](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)
 
Come illustrato nell'esempio precedente con il `Button`, il `CheckBox` non dispone di un indicatore di modifica di colore rosso perché solo le `LinearLayout` contiene modifiche che sono state applicate nel **grandi ' s land** layout.


-----


### <a name="conflict-persistence"></a>Persistenza dei conflitti

È in conflitto vengono rese persistenti nel file di layout come commenti in formato XML, come illustrato di seguito:

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

Di conseguenza, quando un progetto viene chiuso e riaperto, tutti i conflitti verranno comunque essere presenti &ndash; anche a quelli che sono stati ignorati.



