---
title: GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: ef965bed5b50402e1659e45c55a02f9382001521
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511489"
---
# <a name="xamarinandroid-gridlayout"></a>Novell. Android GridLayout

È `GridLayout` una nuova `ViewGroup` sottoclasse che supporta il layout di viste in una griglia 2D, simile a una tabella HTML, come illustrato di seguito:

 [![GridLayout ritagliate che visualizza quattro celle](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout`funziona con una gerarchia di visualizzazione piatta, in cui le visualizzazioni figlio impostano le rispettive posizioni nella griglia specificando le righe e le colonne in cui devono trovarsi. In questo modo, *GridLayout* è in grado di posizionare le visualizzazioni nella griglia senza richiedere che le visualizzazioni intermedie forniscano una struttura di tabella, come illustrato nelle righe della tabella usate in tableLayout. Grazie alla gestione di una gerarchia Flat, *GridLayout* è in grado di eseguire un layout più rapido delle visualizzazioni figlio. Diamo un'occhiata a un esempio per illustrare il significato effettivo del concetto nel codice.


## <a name="creating-a-grid-layout"></a>Creazione di un layout di griglia

Il codice XML seguente aggiunge `TextView` diversi controlli a un *GridLayout*.

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

 [![Diagramma del layout che mostra due celle a sinistra inferiori a destra](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

Il risultato è la seguente interfaccia utente quando viene eseguita in un'applicazione:

 [![Screenshot dell'app GridLayoutDemo che visualizza quattro celle](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)



## <a name="specifying-orientation"></a>Specifica dell'orientamento

Si noti che nel codice XML precedente `TextView` non è specificata una riga o una colonna. Quando tali elementi non vengono specificati, `GridLayout` assegna ogni visualizzazione figlio nell'ordine, in base all'orientamento. Si modifichi, ad esempio, l'orientamento del GridLayout dall'impostazione predefinita, orizzontale, alla verticale come la seguente:

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

A questo punto `GridLayout` , le celle verranno posizionate dall'alto verso il basso in ogni colonna, anziché da sinistra a destra, come illustrato di seguito:

 [![Diagramma che illustra in che modo le celle sono posizionate in orientamento verticale](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

Il risultato è la seguente interfaccia utente in fase di esecuzione:

 [![Screenshot di GridLayoutDemo con celle posizionate in orientamento verticale](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)



### <a name="specifying-explicit-position"></a>Specifica della posizione esplicita

Se si desidera controllare in modo esplicito le posizioni delle visualizzazioni figlio in `GridLayout`, è possibile `layout_row` impostare gli attributi e `layout_column` . Il codice XML seguente, ad esempio, comporterà il layout illustrato nella prima schermata (mostrata sopra), indipendentemente dall'orientamento.

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

Sono disponibili due opzioni che consentono di spaziare tra le visualizzazioni figlio di `GridLayout`. È possibile usare l' `layout_margin` attributo per impostare il margine in ogni visualizzazione figlio direttamente, come illustrato di seguito.

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

Inoltre, in Android 4, è ora disponibile una nuova visualizzazione di spaziatura per utilizzo generico denominata `Space` . Per usarlo, è sufficiente aggiungerlo come visualizzazione figlio.
Il codice XML seguente, ad esempio, aggiunge una riga `GridLayout` aggiuntiva a `rowcount` impostando la proprietà su 3 e `Space` aggiunge una visualizzazione che fornisce spaziatura tra. `TextViews`

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

Questo codice XML crea spaziatura `GridLayout` in come illustrato di seguito:

 [![Screenshot di GridLayoutDemo che illustra le celle più grandi con spaziatura](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

Il vantaggio di usare la nuova `Space` vista è che consente la spaziatura e non richiede l'impostazione degli attributi in ogni visualizzazione figlio.



### <a name="spanning-columns-and-rows"></a>Spanning di colonne e righe

Supporta `GridLayout` inoltre le celle che si estendono su più colonne e righe. Si immagini, ad esempio, di aggiungere un'altra riga contenente un `GridLayout` pulsante a come illustrato di seguito:

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

Questa operazione comporterà l'estensione della prima `GridLayout` colonna dell'oggetto per adattarla alle dimensioni del pulsante, come illustrato di seguito:

[![Screenshot di GridLayoutDemo con pulsante che occupa solo la prima colonna](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

Per evitare l'allungamento della prima colonna, è possibile impostare il pulsante in modo che si estenda su due colonne impostando il relativo oggetto ColumnSpan come segue:

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

In questo modo si ottiene un layout per `TextViews` l'oggetto simile al layout precedente, con il pulsante aggiunto alla fine `GridLayout` di, come illustrato di seguito:

 [![Screenshot di GridLayoutDemo con pulsante che estende entrambe le colonne](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)


## <a name="related-links"></a>Collegamenti correlati

- [GridLayoutDemo (esempio)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/GridLayoutDemo/)
- [Introduzione a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4,0](https://developer.android.com/sdk/android-4.0.html)
