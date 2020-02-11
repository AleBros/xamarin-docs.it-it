---
title: GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 4b6fcfc3120c3ef09556f3466295f4bee35fad0c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029002"
---
# <a name="xamarinandroid-gridlayout"></a>Xamarin.Android GridLayout

Il `GridLayout` è una nuova sottoclasse `ViewGroup` che supporta la disposizione di viste in una griglia 2D, simile a una tabella HTML, come illustrato di seguito:

 [![GridLayout ritagliate che visualizza quattro celle](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` funziona con una gerarchia di visualizzazione piatta, in cui le visualizzazioni figlio impostano le rispettive posizioni nella griglia specificando le righe e le colonne in cui devono trovarsi. In questo modo, *GridLayout* è in grado di posizionare le visualizzazioni nella griglia senza richiedere che le visualizzazioni intermedie forniscano una struttura di tabella, come illustrato nelle righe della tabella usate in tableLayout. Grazie alla gestione di una gerarchia Flat, *GridLayout* è in grado di eseguire un layout più rapido delle visualizzazioni figlio. Diamo un'occhiata a un esempio per illustrare il significato effettivo del concetto nel codice.

## <a name="creating-a-grid-layout"></a>Creazione di un layout di griglia

Il codice XML seguente aggiunge diversi controlli `TextView` a un *GridLayout*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip" />
</GridLayout>
```

Il layout modificherà le dimensioni delle righe e delle colonne in modo che le celle possano adattarsi al contenuto, come illustrato nel diagramma seguente:

 [![diagramma del layout che mostra due celle a sinistra inferiori a destra](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

Il risultato è la seguente interfaccia utente quando viene eseguita in un'applicazione:

 [![screenshot dell'app GridLayoutDemo che visualizza quattro celle](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)

## <a name="specifying-orientation"></a>Specifica dell'orientamento

Si noti che nel codice XML precedente ogni `TextView` non specifica una riga o una colonna. Quando queste non vengono specificate, il `GridLayout` assegna ogni visualizzazione figlio in ordine, in base all'orientamento. Si modifichi, ad esempio, l'orientamento del GridLayout dall'impostazione predefinita, orizzontale, alla verticale come la seguente:

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

A questo punto, il `GridLayout` posiziona le celle dall'alto verso il basso in ogni colonna, anziché da sinistra a destra, come illustrato di seguito:

 [![diagramma che illustra in che modo le celle sono posizionate in orientamento verticale](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

Il risultato è la seguente interfaccia utente in fase di esecuzione:

 [![screenshot di GridLayoutDemo con celle posizionate in orientamento verticale](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)

### <a name="specifying-explicit-position"></a>Specifica della posizione esplicita

Se si desidera controllare in modo esplicito le posizioni delle visualizzazioni figlio nella `GridLayout`, è possibile impostare gli attributi `layout_row` e `layout_column`. Il codice XML seguente, ad esempio, comporterà il layout illustrato nella prima schermata (mostrata sopra), indipendentemente dall'orientamento.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="1" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="1"  />
</GridLayout>
```

### <a name="specifying-spacing"></a>Impostazione della spaziatura

Sono disponibili due opzioni che consentono di spaziare tra le visualizzazioni figlio della `GridLayout`. È possibile usare l'attributo `layout_margin` per impostare il margine in ogni visualizzazione figlio direttamente, come illustrato di seguito.

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

Inoltre, in Android 4, è ora disponibile una nuova visualizzazione di spaziatura per utilizzo generico denominata `Space`. Per usarlo, è sufficiente aggiungerlo come visualizzazione figlio.
Nel codice XML seguente, ad esempio, viene aggiunta una riga aggiuntiva al `GridLayout` impostando il relativo `rowcount` su 3 e viene aggiunta una visualizzazione `Space` che consente di spaziare tra le `TextViews`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="3"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"         
            android:layout_height="50dp" />    
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="1" />
</GridLayout>
```

Questo codice XML crea spaziatura nel `GridLayout` come illustrato di seguito:

 [![screenshot di GridLayoutDemo che illustra le celle più grandi con spaziatura](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

Il vantaggio di usare la nuova visualizzazione `Space` è che consente la spaziatura e non richiede l'impostazione degli attributi in ogni visualizzazione figlio.

### <a name="spanning-columns-and-rows"></a>Spanning di colonne e righe

Il `GridLayout` supporta inoltre le celle che si estendono su più colonne e righe. Si immagini, ad esempio, di aggiungere un'altra riga contenente un pulsante al `GridLayout` come illustrato di seguito:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="4"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"        
            android:layout_height="50dp" />   
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"        
            android:layout_row="2"        
            android:layout_column="1" />
     <Button
            android:id="@+id/myButton"
            android:text="@string/hello"        
            android:layout_row="3"
            android:layout_column="0" />
</GridLayout>
```

Questa operazione comporterà l'estensione della prima colonna dell'`GridLayout` per adattarla alle dimensioni del pulsante, come illustrato di seguito:

[![screenshot di GridLayoutDemo con pulsante che occupa solo la prima colonna](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

Per evitare l'allungamento della prima colonna, è possibile impostare il pulsante in modo che si estenda su due colonne impostando il relativo oggetto ColumnSpan come segue:

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

In questo modo si ottiene un layout per il `TextViews` simile al layout precedente, con il pulsante aggiunto alla fine del `GridLayout`, come illustrato di seguito:

 [![screenshot di GridLayoutDemo con pulsante che si estende su entrambe le colonne](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [GridLayoutDemo (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/gridlayoutdemo)
- [Introduzione a Ice Cream Sandwich](https://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4,0](https://developer.android.com/sdk/android-4.0.html)
