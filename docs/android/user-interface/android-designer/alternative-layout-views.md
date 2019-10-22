---
title: Visualizzazioni con layout alternativi
description: Questo argomento illustra come è possibile usare il controllo delle versioni dei layout usando i qualificatori delle risorse. Ad esempio, può essere presente una versione di un layout che viene usata solo quando il dispositivo è in modalità orizzontale e una versione del layout solo per la modalità verticale.
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: c872baa99496352a1934d10356a1001b309aa63e
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70757411"
---
# <a name="alternative-layout-views"></a>Visualizzazioni di layout alternative

_In questo argomento viene illustrato come è possibile utilizzare i qualificatori di versione per il layout delle versioni. Ad esempio, la creazione di una versione di un layout che viene usata solo quando il dispositivo è in modalità orizzontale e una versione del layout solo per la modalità verticale._

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="creating-alternative-layouts"></a>Creazione di layout alternativi

Quando si fa clic sull'icona di **visualizzazione layout alternativa** (a sinistra del **dispositivo**), viene visualizzato un riquadro di anteprima per elencare i layout alternativi disponibili nel progetto. Se non sono presenti layout alternativi, viene visualizzata la visualizzazione **predefinita** : 

[![Riquadro visualizzazione layout alternativo](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "Riquadro visualizzazione layout alternativo")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

Quando si fa clic sul segno di addizione verde accanto a **nuova versione**, viene visualizzata la finestra di dialogo **Crea variante di layout** in cui è possibile selezionare i qualificatori delle risorse per la variazione del layout: 

[![Crea variazione layout](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "Crea variazione layout")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

Nell'esempio seguente, il qualificatore di risorsa per l' **orientamento dello schermo** è impostato su **orizzontale**e le **dimensioni dello schermo** vengono modificate in **large**. Viene creata una nuova versione del layout denominata **Large-Land**:

[![Variazione di terra elevata](alternative-layout-views-images/vs/03-large-land-sml.png "Variazione di terra elevata")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

Si noti che il riquadro di anteprima a sinistra Mostra gli effetti delle selezioni dei qualificatori di risorse. Facendo clic su **Aggiungi** si crea il layout alternativo e si passa alla finestra di progettazione a tale layout. Il riquadro di anteprima **visualizzazione layout alternativo** indica il layout caricato nella finestra di progettazione tramite un piccolo puntatore a destra, come indicato nello screenshot seguente: 

[![Indicatore di layout caricato](alternative-layout-views-images/vs/04-new-layout-sml.png "Indicatore di layout caricato")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>Modifica di layout alternativi

Quando si creano layout alternativi, è spesso consigliabile apportare una singola modifica applicabile a tutte le versioni con fork di un layout. È ad esempio possibile modificare il testo del pulsante in giallo in tutti i layout. Se si dispone di un numero elevato di layout ed è necessario propagare una singola modifica a tutte le versioni, la manutenzione può diventare rapidamente complessa e soggetta a errori.

Per semplificare la manutenzione di più versioni di layout, la finestra di progettazione fornisce una modalità di **modifica multipla** che propaga le modifiche in uno o più layout. Quando è presente più di un layout, viene visualizzata l'icona di **modifica multifunzione** : 

[![Icona a più modifiche](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "Icona a più modifiche")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

Quando si fa clic sull'icona **a più modifiche** , vengono visualizzate linee che indicano che i layout sono collegati (come illustrato di seguito); ovvero, quando si modifica un layout, tale modifica viene propagata a tutti i layout collegati. È possibile scollegare tutti i layout facendo clic sull'icona a forma di cerchio indicata nello screenshot seguente: 

[![Scollega tutti i layout](alternative-layout-views-images/vs/06-multi-linked-sml.png "Scollega tutti i layout")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

Se si dispone di più di due layout, è possibile impostare selettivamente il pulsante modifica a sinistra di ogni anteprima del layout per determinare quali layout sono collegati tra loro. Se, ad esempio, si desidera apportare una singola modifica che si propaga al primo e all'ultimo di tre layout, è necessario prima scollegare il layout intermedio, come illustrato di seguito: 

[![Scollega layout intermedio](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "Scollega layout intermedio")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)

In questo esempio, una modifica apportata al layout **predefinito** o **lungo** verrà propagata all'altro layout ma non al layout di **grandi dimensioni** .

### <a name="multi-edit-example"></a>Esempio di più modifiche 

In generale, quando si modifica un layout, la stessa modifica viene propagata a tutti gli altri layout collegati. Se ad esempio si aggiunge un nuovo widget `TextView` al layout **predefinito** e si modifica la relativa stringa di testo in `Portrait`, verrà apportata la stessa modifica a tutti i layout collegati. Ecco come viene visualizzato nel layout **predefinito** : 

[![Aggiungi TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "Aggiungi TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)

Il `TextView` viene inoltre aggiunto alla visualizzazione layout di **grandi dimensioni** perché è collegato al layout **predefinito** : 

[![TextView orizzontale](alternative-layout-views-images/vs/09-landscape-textview-sml.png "TextView orizzontale")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)

Tuttavia, se si vuole apportare una modifica locale a un solo layout (ovvero, non si vuole che la modifica venga propagata a uno degli altri layout)? A tale scopo, è necessario scollegare il layout che si desidera modificare prima di modificarlo, come illustrato di seguito. 

### <a name="making-local-changes"></a>Apportare modifiche locali 

Si supponga di volere che entrambi i layout includano il `TextView` aggiunto, ma si vuole anche modificare la stringa di testo nel layout di **grandi dimensioni** per `Landscape` invece che `Portrait`. Se questa modifica viene apportata a una **grande superficie** mentre entrambi i layout sono collegati, la modifica viene propagata al layout **predefinito** . È pertanto necessario scollegare i due layout prima di apportare la modifica. Quando si modifica il testo in `Landscape` di **grandi dimensioni** , la finestra di progettazione contrassegna questa modifica con una cornice rossa per indicare che la modifica è locale rispetto al layout di **grandi dimensioni** e *non* viene propagata al layout **predefinito** : 

[![Modifica locale](alternative-layout-views-images/vs/10-local-change-sml.png "Modifica locale")](alternative-layout-views-images/vs/10-local-change.png#lightbox)

Quando si fa clic sul layout **predefinito** per visualizzarlo, la stringa di testo `TextView` è ancora impostata su `Portrait`. 

## <a name="handling-conflicts"></a>Gestione dei conflitti 

Se si decide di modificare il colore del testo nel layout **predefinito** in verde, viene visualizzata un'icona di avviso nel layout collegato. Quando si fa clic sul layout, viene aperto il layout per rivelare il conflitto. Il widget che ha causato il conflitto viene evidenziato con un frame rosso e viene visualizzato il messaggio seguente: le *modifiche recenti hanno causato conflitti in questo layout alternativo*. 

[![Modifica in conflitto](alternative-layout-views-images/vs/11-conflicting-change-sml.png "Modifica in conflitto")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)

Una *casella di conflitto* viene visualizzata a destra del widget per illustrare il conflitto: 

[avviso ![Conflict](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

Nella casella conflitto viene visualizzato l'elenco delle proprietà che sono state modificate e ne sono elencati i valori. Se si fa clic su **Ignora conflitto** viene applicata la modifica della proprietà solo a questo widget. Se si fa clic su **applica** , la modifica della proprietà viene applicata a questo widget, oltre che al widget corrispondente nel layout **predefinito** collegato. Se vengono applicate tutte le modifiche alle proprietà, il conflitto viene ignorato automaticamente. 

### <a name="view-group-conflicts"></a>Visualizza conflitti di gruppo 

Le modifiche alle proprietà non sono l'unica fonte di conflitti. I conflitti possono essere rilevati durante l'inserimento o la rimozione di widget. Ad esempio, quando il layout di **grandi dimensioni** viene scollegato dal layout **predefinito** e il `TextView` nel layout di **grandi dimensioni** viene trascinato e rilasciato sopra il `Button`, la finestra di progettazione contrassegna il widget spostato per indicare il conflitto:

[![Visualizza conflitto di gruppo](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "Visualizza conflitto di gruppo")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)

Tuttavia, non è presente alcun marcatore sul `Button`. Anche se la posizione del `Button` è cambiata, il `Button` non Mostra modifiche applicate specifiche per la configurazione di **grandi dimensioni** . 

Se un `CheckBox` viene aggiunto al layout **predefinito** , viene generato un altro conflitto e viene visualizzata un'icona di avviso sul layout **di grandi dimensioni** : 

[![Casella di controllo-conflitto](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "Casella di controllo-conflitto")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)

Facendo clic sul layout **di grandi dimensioni** viene evidenziato il conflitto. Viene visualizzato il messaggio seguente: le *modifiche recenti hanno causato conflitti in questo layout alternativo*: 

[![Conflitto di layout Alt](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "Conflitto di layout Alt")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)

Nella casella conflitti viene inoltre visualizzato il messaggio seguente:

[messaggio ![Conflict](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

L'aggiunta del `CheckBox` causa un conflitto perché il layout di **grandi dimensioni** contiene modifiche nell'`LinearLayout` che la contiene. Tuttavia, in questo caso, nella casella conflitto viene visualizzato il widget appena inserito nel layout **predefinito** (il `CheckBox`).

Se si fa clic su **Ignora conflitto**, la finestra di progettazione risolve il conflitto, consentendo al widget visualizzato nella casella dei conflitti di essere trascinato e rilasciato nel layout in cui manca il widget (in questo caso, il layout di **grandi dimensioni** ): 

[![Conflitto di gruppo risolto](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "Conflitto di gruppo risolto")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)

Come illustrato nell'esempio precedente con il `Button`, il `CheckBox` non dispone di un marcatore di modifica rosso perché solo il `LinearLayout` contiene modifiche che sono state applicate nel layout di **grandi dimensioni** .

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="creating-alternative-layouts"></a>Creazione di layout alternativi

Quando si fa clic sull'icona di **visualizzazione layout alternativa** (a sinistra del **dispositivo**), viene visualizzato un riquadro di anteprima per elencare i layout alternativi disponibili nel progetto. Se non sono presenti layout alternativi, viene visualizzata la visualizzazione **predefinita** : 

[riquadro visualizzazione layout ![Alternate](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

Quando si fa clic sul segno di addizione verde accanto a **nuova versione**, viene visualizzata la finestra di dialogo **Crea variante di layout** in cui è possibile selezionare i qualificatori delle risorse per la variazione del layout: 

[variazione del layout ![Create](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

Nell'esempio seguente, il qualificatore di risorsa per l' **orientamento dello schermo** è impostato su **orizzontale**e le **dimensioni dello schermo** vengono modificate in **large**. Viene creata una nuova versione del layout denominata **Large-Land**:

[variazione ![Large-Land](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

Si noti che il riquadro di anteprima a sinistra Mostra gli effetti delle selezioni dei qualificatori di risorse. Facendo clic su **Aggiungi** si crea il layout alternativo e si passa alla finestra di progettazione a tale layout. Il riquadro di anteprima **visualizzazione layout alternativo** indica il layout caricato nella finestra di progettazione tramite un piccolo puntatore a destra, come indicato nello screenshot seguente: 

[indicatore di layout ![Loaded](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>Modifica di layout alternativi

Quando si creano layout alternativi, è spesso consigliabile apportare una singola modifica applicabile a tutte le versioni con fork di un layout. È ad esempio possibile modificare il testo del pulsante in giallo in tutti i layout. Se si dispone di un numero elevato di layout ed è necessario propagare una singola modifica a tutte le versioni, la manutenzione può diventare rapidamente complessa e soggetta a errori.

Per semplificare la manutenzione di più versioni di layout, la finestra di progettazione fornisce una modalità di **modifica multipla** che propaga le modifiche in uno o più layout. Quando è presente più di un layout, viene visualizzata l'icona di **modifica multifunzione** : 

[icona di modifica ![Multi](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

Quando si fa clic sull'icona **a più modifiche** , vengono visualizzate linee che indicano che i layout sono collegati (come illustrato di seguito); ovvero, quando si modifica un layout, tale modifica viene propagata a tutti i layout collegati. È possibile scollegare tutti i layout facendo clic sull'icona a forma di cerchio indicata nello screenshot seguente: 

[![Unlink tutti i layout](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

Se si dispone di più di due layout, è possibile impostare selettivamente il pulsante modifica a sinistra di ogni anteprima del layout per determinare quali layout sono collegati tra loro. Se, ad esempio, si desidera apportare una singola modifica che si propaga al primo e all'ultimo di tre layout, è necessario prima scollegare il layout intermedio, come illustrato di seguito: 

[layout intermedio ![Unlink](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)

In questo esempio, una modifica apportata al layout **predefinito** o **lungo** verrà propagata ad altro layout ma non al layout di **grandi dimensioni** . 

### <a name="multi-edit-example"></a>Esempio di più modifiche 

In generale, quando si modifica un layout, la stessa modifica viene propagata a tutti gli altri layout collegati. Se ad esempio si aggiunge un nuovo widget `TextView` al layout **predefinito** e si modifica la relativa stringa di testo in `Portrait`, verrà apportata la stessa modifica a tutti i layout collegati. Ecco come viene visualizzato nel layout **predefinito** : 

[TextView ![Add](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)

Il `TextView` viene inoltre aggiunto alla visualizzazione layout di **grandi dimensioni** perché è collegato al layout **predefinito** : 

[TextView ![Landscape](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)

Tuttavia, se si vuole apportare una modifica locale a un solo layout (ovvero, non si vuole che la modifica venga propagata a uno degli altri layout)? A tale scopo, è necessario scollegare il layout che si desidera modificare prima di modificarlo, come illustrato di seguito. 

### <a name="making-local-changes"></a>Apportare modifiche locali 

Si supponga di volere che entrambi i layout includano il `TextView` aggiunto, ma si vuole anche modificare la stringa di testo nel layout di **grandi dimensioni** per `Landscape` invece che `Portrait`. Se questa modifica viene apportata a una **grande superficie** mentre entrambi i layout sono collegati, la modifica viene propagata al layout **predefinito** . È pertanto necessario scollegare i due layout prima di apportare la modifica. Quando si modifica il testo in `Landscape` di **grandi dimensioni** , la finestra di progettazione contrassegna questa modifica con una cornice rossa per indicare che la modifica è locale rispetto al layout di **grandi dimensioni** e *non* viene propagata al layout **predefinito** : 

[modifica ![Local](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)

Quando si fa clic sul layout **predefinito** per visualizzarlo, la stringa di testo `TextView` è ancora impostata su `Portrait`. 

## <a name="handling-conflicts"></a>Gestione dei conflitti 

Se si decide di modificare il colore del testo nel layout **predefinito** in verde, viene visualizzata un'icona di avviso nel layout collegato. Quando si fa clic sul layout, viene aperto il layout per rivelare il conflitto. Il widget che ha causato il conflitto viene evidenziato con un frame rosso e viene visualizzato il messaggio seguente: le *modifiche recenti hanno causato conflitti in questo layout alternativo*. 

[modifica ![Conflicting](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)

Una *casella di conflitto* viene visualizzata a destra del widget per illustrare il conflitto: 

[avviso ![Conflict](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

Nella casella conflitto viene visualizzato l'elenco delle proprietà che sono state modificate e ne sono elencati i valori. Se si fa clic su **Ignora conflitto** viene applicata la modifica della proprietà solo a questo widget. Se si fa clic su **applica** , la modifica della proprietà viene applicata a questo widget, oltre che al widget corrispondente nel layout **predefinito** collegato. Se vengono applicate tutte le modifiche alle proprietà, il conflitto viene ignorato automaticamente. 

### <a name="view-group-conflicts"></a>Visualizza conflitti di gruppo 

Le modifiche alle proprietà non sono l'unica fonte di conflitti. I conflitti possono essere rilevati durante l'inserimento o la rimozione di widget. Ad esempio, quando il layout di **grandi dimensioni** viene scollegato dal layout **predefinito** e il `TextView` nel layout di **grandi dimensioni** viene trascinato e rilasciato sopra il `Button`, la finestra di progettazione contrassegna il widget spostato per indicare il conflitto:

[conflitto tra gruppi di ![View](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)

Tuttavia, non è presente alcun marcatore sul `Button`. Anche se la posizione del `Button` è cambiata, il `Button` non Mostra modifiche applicate specifiche per la configurazione di **grandi dimensioni** . 

Se un `CheckBox` viene aggiunto al layout **predefinito** , viene generato un altro conflitto e viene visualizzata un'icona di avviso sul layout **di grandi dimensioni** : 

[conflitto ![Checkbox](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)

Facendo clic sul layout **di grandi dimensioni** viene evidenziato il conflitto. Viene visualizzato il messaggio seguente: le *modifiche recenti hanno causato conflitti in questo layout alternativo*. 

[conflitto di layout ![Alt](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)

Nella casella conflitti viene inoltre visualizzato il messaggio seguente:

[messaggio ![Conflict](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

L'aggiunta del `CheckBox` causa un conflitto perché il layout di **grandi dimensioni** contiene modifiche nell'`LinearLayout` che la contiene. Tuttavia, in questo caso, nella casella conflitto viene visualizzato il widget appena inserito nel layout **predefinito** (il `CheckBox`).

Se si fa clic su **Ignora conflitto**, la finestra di progettazione risolve il conflitto, consentendo al widget visualizzato nella casella dei conflitti di essere trascinato e rilasciato nel layout in cui manca il widget (in questo caso, il layout di **grandi dimensioni** ): 

[conflitto tra gruppi di ![Resolved](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)

Come illustrato nell'esempio precedente con il `Button`, il `CheckBox` non dispone di un marcatore di modifica rosso perché solo il `LinearLayout` contiene modifiche che sono state applicate nel layout di **grandi dimensioni** .

-----

### <a name="conflict-persistence"></a>Persistenza dei conflitti

I conflitti vengono mantenuti nel file di layout come commenti XML, come illustrato di seguito:

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

Pertanto, quando un progetto viene chiuso e riaperto, tutti i conflitti saranno ancora presenti &ndash; anche quelli che sono stati ignorati.
