---
title: GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: bbc764adc204a1f5b9ef4674a183473995be55c1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115159"
---
# <a name="gridlayout"></a>GridLayout

Il `GridLayout` è un nuovo `ViewGroup` sottoclasse che supporta la disposizione di viste in una griglia 2D, simile a una tabella HTML, come illustrato di seguito:

 [![Ritagliata GridLayout la visualizzazione di quattro celle](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` funziona con una gerarchia di visualizzazione semplice, in cui visualizzazioni figlio impostare le relative posizioni nella griglia specificando le righe e colonne che devono essere nel. In questo modo, il *GridLayout* è in grado di posizionare le visualizzazioni nella griglia senza richiedere che tutte le viste intermedie forniscono una struttura di tabella, come le righe della tabella utilizzate nel TableLayout. Tramite la gestione di una gerarchia piatta *GridLayout* è in grado di layout più rapidamente delle visualizzazioni figlio. Esaminiamo un esempio per illustrare questo concetto in realtà significato nel codice.


## <a name="creating-a-grid-layout"></a>Creazione di un Layout griglia

Il codice XML seguente vengono aggiunti numerosi `TextView` controlli a un *GridLayout*.

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

 [![Diagramma del layout che mostra due celle a sinistra inferiore a destra](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

Ciò comporta l'interfaccia utente seguente quando eseguita in un'applicazione:

 [![Schermata di GridLayoutDemo app la visualizzazione di quattro celle](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)



## <a name="specifying-orientation"></a>Specifica l'orientamento

Si noti che nel file XML precedente, ogni `TextView` non specifica una riga o colonna. Quando questi non vengono specificati, il `GridLayout` assegna ogni visualizzazione figlio in ordine, in base all'orientamento. Ad esempio, modificare l'orientamento dell'elemento GridLayout da quello predefinito, ovvero orizzontale, verticale a simile al seguente:

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

 [![Diagramma che illustra come le celle vengono posizionate in orientamento verticale](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

Questo comporterà la seguente interfaccia utente in fase di esecuzione:

 [![Schermata di GridLayoutDemo con celle è posizionate in orientamento verticale](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)



### <a name="specifying-explicit-position"></a>Specifica di posizione espliciti

Se si vuole controllare in modo esplicito le posizioni delle visualizzazioni figlio di `GridLayout`, è possibile impostare loro `layout_row` e `layout_column` attributi. Ad esempio, il codice XML seguente comporterà il layout mostrato nella prima schermata (illustrata in precedenza), indipendentemente dall'orientamento.

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



### <a name="specifying-spacing"></a>Specifica la spaziatura

Sono disponibili un paio di opzioni che fornisce le visualizzazioni di spaziatura tra l'elemento figlio di `GridLayout`. È possibile usare il `layout_margin` attributo da impostare il margine per ogni visualizzazione figlio direttamente, come illustrato di seguito

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

Inoltre, in Android 4, una nuova visualizzazione per utilizzo generico spaziatura chiamato `Space` è ora disponibile. Per usarla, è sufficiente aggiungerlo come una visualizzazione figlio.
Ad esempio, il codice XML seguente aggiunge una riga aggiuntiva per i `GridLayout` impostando relativi `rowcount` su 3 e aggiunge un `Space` vista che include la spaziatura tra il `TextViews`.

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

Questo codice XML crea la spaziatura nel `GridLayout` come illustrato di seguito:

 [![Schermata di GridLayoutDemo illustrando le celle più grandi con spaziatura](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

Il vantaggio di usare il nuovo `Space` Vista è che consente la spaziatura e non richiede di impostare gli attributi in tutte le visualizzazioni figlio.



### <a name="spanning-columns-and-rows"></a>Spanning colonne e righe

Il `GridLayout` supporta anche le celle che si estendono su più colonne e righe. Ad esempio, si aggiunge un'altra riga che contiene un pulsante per la `GridLayout` come illustrato di seguito:

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

Ciò comporterà la prima colonna della `GridLayout` verrà estesa per supportare le dimensioni del pulsante, come illustrato di seguito:

[![Schermata di GridLayoutDemo con il pulsante che si estende solo la prima colonna](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

Per evitare che l'estensione della prima colonna, è possibile impostare il pulsante per estendersi su due colonne tramite l'impostazione relativa columnspan simile al seguente:

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

Questa operazione comporta un layout per la `TextViews` che è simile al layout si aveva in precedenza, con il pulsante aggiunto in fondo il `GridLayout` come illustrato di seguito:

 [![Schermata di GridLayoutDemo con il pulsante che si estende su entrambe le colonne](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)


## <a name="related-links"></a>Collegamenti correlati

- [GridLayoutDemo (esempio)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/GridLayoutDemo/)
- [Introduzione a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4.0](http://developer.android.com/sdk/android-4.0.html)
