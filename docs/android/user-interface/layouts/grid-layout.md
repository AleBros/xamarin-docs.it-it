---
title: GridLayout
ms.topic: article
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 013db64add615e94ef3494f14bc82fc17ec2dca1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="gridlayout"></a>GridLayout

Il `GridLayout` è una nuova `ViewGroup` sottoclasse che supporta il layout di viste in una griglia 2D, simile a una tabella HTML, come illustrato di seguito:

 [ ![Visualizzazione di quattro celle GridLayout ritagliata](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png)

 `GridLayout` funziona con una gerarchia di visualizzazione semplice, in cui visualizzazioni figlio impostare le relative posizioni nella griglia specificando le righe e colonne che deve essere. In questo modo, il *GridLayout* è in grado di posizionare le visualizzazioni nella griglia senza richiedere che qualsiasi vista intermedio fornisce una struttura di tabella, come illustrato nelle righe di tabella utilizzate nella TableLayout. Tramite la gestione di una gerarchia semplice, *GridLayout* è in grado di layout più rapidamente delle visualizzazioni figlio. Esaminiamo un esempio per illustrare questo concetto effettivamente significato nel codice.

<a name="Creating_a_Grid_Layout" />

## <a name="creating-a-grid-layout"></a>Creazione di un Layout di griglia

Il codice XML seguente vengono aggiunte diverse `TextView` i controlli a un *GridLayout*.

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

Il layout verrà regolare le dimensioni di riga e colonna in modo che le celle possono adattarsi al contenuto, come illustrato nel diagramma seguente:

 [ ![Diagramma del layout con due celle a sinistra inferiori a destra](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png)

Ciò comporta l'interfaccia utente di seguenti quando in esecuzione in un'applicazione:

 [ ![Schermata di GridLayoutDemo app la visualizzazione delle quattro celle](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png)

 <a name="Specifying_Orientation" />


## <a name="specifying-orientation"></a>Specifica l'orientamento

Si noti che nel codice XML precedente, ogni `TextView` non specifica una riga o colonna. Quando questi non vengono specificati, il `GridLayout` assegna ogni visualizzazione figlio in ordine, in base all'orientamento. Ad esempio, modificare l'orientamento del GridLayout da quello predefinito, ovvero orizzontale, verticale a simile al seguente:

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

A questo punto, il `GridLayout` verranno inserite le celle dall'alto verso il basso in ogni colonna, anziché da sinistra a destra, come illustrato di seguito:

 [ ![Diagramma che illustra come le celle vengono posizionate in orientamento verticale](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png)

Ciò comporta l'interfaccia utente di seguenti in fase di esecuzione:

 [ ![Schermata di GridLayoutDemo con celle posizionate in orientamento verticale](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png)

 <a name="Specifying_Explicit_Position" />


### <a name="specifying-explicit-position"></a>Specifica di posizione espliciti

Se si vuole controllare in modo esplicito le posizioni delle visualizzazioni figlio di `GridLayout`, è possibile impostare i relativi `layout_row` e `layout_column` gli attributi. Ad esempio, il seguente codice XML comporterà il layout mostrato nella schermata prima (sopra), indipendentemente dal fatto l'orientamento.

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

 <a name="Specifying_spacing" />


### <a name="specifying-spacing"></a>Specifica la spaziatura

Sono disponibili due opzioni che fornisce le visualizzazioni di spaziatura tra l'elemento figlio di `GridLayout`. È possibile utilizzare il `layout_margin` attributo da impostare il margine per ogni visualizzazione figlio direttamente, come illustrato di seguito

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

Android 4, inoltre, una nuova visualizzazione spaziatura generica denominata `Space` è ora disponibile. Per utilizzarlo, aggiungerlo semplicemente come una visualizzazione figlio.
Ad esempio, il codice XML seguente aggiunge una riga aggiuntiva per il `GridLayout` impostando il relativo `rowcount` su 3 e aggiunge un `Space` vista che include la spaziatura tra il `TextViews`.

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

Questo codice XML Crea spaziatura di `GridLayout` come illustrato di seguito:

 [ ![Schermata di GridLayoutDemo che illustrano le celle di dimensioni maggiori con spaziatura](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png)

Il vantaggio di utilizzare il nuovo `Space` Vista è che consente la spaziatura e non richiede di impostare gli attributi per ogni visualizzazione figlio.

 <a name="Spanning_Columns_and_Rows" />


### <a name="spanning-columns-and-rows"></a>Lo spanning di colonne e righe

Il `GridLayout` supporta anche le celle che si estendono su più colonne e righe. Se ad esempio, viene aggiunta un'altra riga che contiene un pulsante per la `GridLayout` come illustrato di seguito:

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

Il risultato sarà la prima colonna del `GridLayout` verrà estesa per supportare la dimensione del pulsante, come si può vedere qui:

[ ![Schermata di GridLayoutDemo con spanning solo la prima colonna pulsante](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png)

Per impedire che l'estensione della prima colonna, è possibile impostare il pulsante deve estendersi a due colonne impostando il relativo columnspan simile al seguente:

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

Questa operazione comporta un layout per il `TextViews` che è simile al layout si è verificato in precedenza, con il pulsante aggiunto alla fine del `GridLayout` come illustrato di seguito:

 [ ![Schermata di GridLayoutDemo con pulsante che si estende su entrambe le colonne](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png)


## <a name="related-links"></a>Collegamenti correlati

- [GridLayoutDemo (sample)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/GridLayoutDemo/)
- [Introduzione a Sandwich gelato](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
