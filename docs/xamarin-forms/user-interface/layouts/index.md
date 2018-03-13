---
title: Layout
description: Il layout viste sullo schermo.
ms.topic: article
ms.prod: xamarin
ms.assetid: 65030DA3-C7C1-4A02-B478-811073C39139
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2017
ms.openlocfilehash: 0ede9bbb47f398a82d6eae5d827122f469ad6ea4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="layouts"></a>Layout

Xamarin. Forms dispone di diversi layout e le funzionalità nell'organizzazione del contenuto sullo schermo. 

> [!VIDEO https://youtube.com/embed/4HlLjTZQzjM]

**Layout di xamarin. Forms, da [University Xamarin](https://university.xamarin.com/)**

Ogni controllo di layout è descritta di seguito, nonché informazioni dettagliate su come gestire le modifiche di orientamento dello schermo:

* **[StackLayout](stack-layout.md)**  &ndash; usato per disporre le visualizzazioni in modo lineare, orizzontalmente o verticalmente. Visualizzazioni in un StackLayout possono essere allineate al centro, a sinistro o a destro del layout.
* **[AbsoluteLayout](absolute-layout.md)**  &ndash; utilizzato per disporre le visualizzazioni impostando coordinate & dimensioni in termini di valori assoluti o rapporti. AbsoluteLayout utilizzabile per le visualizzazioni di livello, nonché ancorarli a sinistra, destra o al centro.
* **[RelativeLayout](relative-layout.md)**  &ndash; usato per disporre le visualizzazioni impostando vincoli rispetto al rispettivo elemento padre le dimensioni e posizione.
* **[Griglia](grid.md)**  &ndash; usato per disporre le visualizzazioni in una griglia. È possibile specificare le righe e colonne in termini di valori assoluti o rapporti.
* **[Elemento ScrollView](scroll-view.md)**  &ndash; utilizzato per fornire lo scorrimento quando una vista non può essere contenuta interamente all'interno dei limiti dello schermo.
* **[LayoutOptions](layout-options.md)**  &ndash; definire l'allineamento e l'espansione di una visualizzazione, rispetto al padre.
* **[La trasparenza di input](#input_transparency)**  &ndash; specifica se un elemento riceve l'input.
* **[Margine e spaziatura interna](margin-and-padding.md)**  &ndash; viene illustrato come controllare il comportamento di layout quando viene eseguito il rendering di un elemento nell'interfaccia utente.
* **[Orientamento del dispositivo](device-orientation.md)**  &ndash; viene illustrato come gestire le modifiche di orientamento di dispositivo.
* **[Il layout nei dispositivi desktop e tablet](tablet.md)**  &ndash; viene illustrato come ottimizzare per schermi di dimensioni maggiori in ciascuna piattaforma.
* **[Creazione di un Layout personalizzato](custom.md)**  &ndash; viene illustrato come creare una classe di layout personalizzato.
* **[La compressione di layout](layout-compression.md)**  &ndash; rimuove specificato layout dall'albero visuale nel tentativo di migliorare le prestazioni per il rendering della pagina.

Controlli di piattaforma inoltre possono essere utilizzati direttamente nel layout di xamarin. Forms con [ **incorporamento Native** ](~/xamarin-forms/platform/native-views/index.md) (nuovo in xamarin. Forms 2.2), è anche possibile [ **creare layout personalizzati** ](custom.md) per soddisfare i requisiti specifici.

Nella figura seguente mostra i controlli di layout:

[![](images/layouts-sml.png "Xamarin.Forms Layouts")](images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="choosing-the-right-layout"></a>Scegliere il Layout di destra

Il layout desiderato nell'app della Guida oppure influire negativamente sulle come si crea un'app xamarin. Forms interessante e utilizzabile. Scorrendo da considerare come funziona ogni layout consente di scrivere codice dell'interfaccia utente semplice e più scalabile. Una schermata può avere una combinazione di diversi layout per ottenere una progettazione specifica.

### <a name="stacklayoutstack-layoutmd"></a>[StackLayout](stack-layout.md)

Il `StackLayout` viene utilizzato per visualizzare le visualizzazioni lungo una riga orizzontale o verticale. Posizione e dimensioni all'interno del layout è determinato in base a una vista `HeightRequest`, `WidthRequest`, `HorizontalOptions` e `VerticalOptions`. `StackLayout` viene spesso utilizzato come il layout di base, la disposizione di altri layout sullo schermo.

Per un esempio di quando `StackLayout` potrebbe essere una scelta ottimale, si consideri un'applicazione che deve visualizzare un pulsante e un'etichetta, con l'etichetta allineato a sinistra e il pulsante allineato a destra.

```xaml
<StackLayout Orientation="Horizontal">
  <Label HorizontalOptions="StartAndExpand" Text="Label" />
  <Button HorizontalOptions="End" Text="Button" />
</StackLayout>
```

### <a name="absolutelayoutabsolute-layoutmd"></a>[AbsoluteLayout](absolute-layout.md)

Il `AbsoluteLayout` viene utilizzato per visualizzare le visualizzazioni, con dimensioni e posizione viene specificato come valori espliciti o relativo alle dimensioni del layout. A differenza di `StackLayout` e `Grid`, `AbsoluteLayout` consente figlio viste si sovrappongono. A differenza di `RelativeLayout`, `AbsoluteLayout` non è possibile posizionare gli elementi fuori dello schermo.

Per un esempio di quando `AbsoluteLayout` potrebbe essere una scelta ottimale, si consideri un'applicazione che deve presentare le raccolte di oggetti come stack. Ciò viene spesso visualizzato quando si presentano album foto o brani. Il codice seguente fornisce l'aspetto di una pila, con elementi per l'hint per l'accumulo il relativo contenuto:

In XAML:

```xaml
<AbsoluteLayout Padding="15">
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="30"
    Source="bottom.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="60"
    Source="middle.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
    Source="cover.png" />
</AbsoluteLayout>
```

Tenere presente i seguenti aspetti di codice sopra riportato:

- Ogni `Image` viene visualizzata nella stessa posizione (al centro lo spazio orizzontale)
- Il `Padding` viene considerato da `AbsoluteLayout`, a differenza `RelativeLayout`, che viene ignorato.
- `AbsoluteLayout.LayoutFlags` Specifica la modalità di interpretazione dei limiti di layout. In questo caso `PositionProportional`, significa che le coordinate sarà una percentuale delle dimensioni del layout, mentre la dimensione verrà interpretata come una dimensione esplicita.
- `AbsoluteLayout.Layoutbounds` Specifica la posizione orizzontale, la posizione verticale, la larghezza e l'altezza, in quest'ordine.

### <a name="relativelayoutrelative-layoutmd"></a>[RelativeLayout](relative-layout.md)

Il `RelativeLayout` viene utilizzato per visualizzare le visualizzazioni, con dimensioni e posizione specificati come valori rispetto ai valori del layout o un'altra visualizzazione. Valori non è necessario affinché corrisponda allo corrispondente valore per la vista correlata. Ad esempio, è possibile impostare una visualizzazione `Width` proprietà sia proporzionale a un'altra visualizzazione `X` proprietà.

RelativeLayout consente di creare interfacce utente che si adattano in proporzione tra le dimensioni di dispositivo. Il codice XAML seguente implementa una struttura con le caselle negli angoli superiori, con un'asta con flag nell'area:

```xaml
<RelativeLayout HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand">
  <BoxView Color="Blue" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = 0}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Red" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .9}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Gray" WidthRequest="15" x:Name="pole"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.75}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .45}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = .25}" />
  <BoxView Color="Green"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.10, Constant=10}"
    RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent,Property=Width, Factor=.2, Constant=20}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=X, Constant=15}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=Y, Constant=0}" />
</RelativeLayout>
```

Tenere presente i seguenti aspetti di codice sopra riportato:

- Le posizioni e le dimensioni vengono specificate come vincoli.
- Asta denominato in modo che il flag (verde della casella) posizione può essere impostata relativo asta.
- Le espressioni di vincolo è `Factor` e `Constant` proprietà, che può essere utilizzata per definire le posizioni e le dimensioni come multipli (o le frazioni di secondo) delle proprietà di altri oggetti, oltre a una costante. Costanti possono essere negative.

### <a name="gridgridmd"></a>[Griglia](grid.md)

Il `Grid` viene utilizzato per visualizzare gli elementi in righe e colonne. Si noti che la griglia non è una tabella, pertanto non è il concetto di celle, righe di intestazione e piè di pagina o i bordi tra righe e colonne. Griglia in generale, non è appropriata per la visualizzazione dei dati in formato tabulare. Per tale utilizzo, prendere in considerazione un [ListView](~/xamarin-forms/user-interface/listview/index.md) o [TableView](~/xamarin-forms/user-interface/tableview.md).

Per un esempio di quando un `Grid` è il layout destra da usare, considerare un input numerico per una calcolatrice. Un input numerico per un calcolo può essere costituito da quattro righe e tre colonne, ognuna con un pulsante. Il codice seguente implementa questa struttura:

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>

  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Button Text="1" Grid.Row="0" Grid.Column="0" />
  <Button Text="2" Grid.Row="0" Grid.Column="1" />
  <Button Text="3" Grid.Row="0" Grid.Column="2" />
  <Button Text="4" Grid.Row="1" Grid.Column="0" />
  <Button Text="5" Grid.Row="1" Grid.Column="1" />
  <Button Text="6" Grid.Row="1" Grid.Column="2" />
  <Button Text="7" Grid.Row="2" Grid.Column="0" />
  <Button Text="8" Grid.Row="2" Grid.Column="1" />
  <Button Text="9" Grid.Row="2" Grid.Column="2" />
  <Button Text="0" Grid.Row="3" Grid.Column="1" />
  <Button Text="&lt;-" Grid.Row="3" Grid.Column="2" />
</Grid>
```

Tenere presente i seguenti aspetti di codice sopra riportato:

- Griglie e le colonne vengono specificate esplicitamente, non è stato dedotto dal contenuto.
- `Height` e `Width` valori possono essere impostati su proporzionale, il che significa che la griglia verrà impostare tali valori per riempire lo spazio disponibile.
- Posizione di ogni pulsante è specificata da `Grid.Row`  &  `Grid.Column` proprietà.

### <a name="layoutoptionslayout-optionsmd"></a>[LayoutOptions](layout-options.md)

Il [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) struttura può essere utilizzata per definire l'allineamento e l'espansione di una visualizzazione, rispetto al padre.

### <a name="margin-and-paddingmargin-and-paddingmd"></a>[Margine e spaziatura interna](margin-and-padding.md)

Il [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) e [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) proprietà controllano il comportamento di layout quando viene eseguito il rendering di un elemento nell'interfaccia utente.

<a name="input_transparency" />

### <a name="input-transparency"></a>Trasparenza di input

Ogni elemento dispone di un [ `InputTransparent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.InputTransparent/) proprietà che consentono di definire se l'elemento riceve l'input. Il valore predefinito è `false`, assicurandosi che l'elemento riceve l'input.

Quando questa proprietà è impostata su una classe contenitore, ad esempio una classe di layout, i trasferimenti di valore per gli elementi figlio. Pertanto, l'impostazione di [ `InputTransparent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.InputTransparent/) proprietà `true` in un layout di classe comporterà gli elementi all'interno del layout non riceve l'input.

### <a name="device-orientationdevice-orientationmd"></a>[Orientamento del dispositivo](device-orientation.md)

Xamarin. Forms e il layout predefinito sono in grado di gestire le modifiche in orientamento del dispositivo. Considerare quali orientamenti app supporterà, nonché come ti utilizzo di spazio disponibile in modalità verticale e orizzontale.

### <a name="layout-for-tablet-and-desktop-appstabletmd"></a>[Layout per le applicazioni Desktop e Tablet](tablet.md)

iOS, Android e Windows piattaforme supportano dimensioni dello schermo più grande sul tablet dispositivi (nonché portatili e desktop per Windows). Xamarin. Forms consente di ottimizzare l'app per schermi di dimensioni maggiori rilevare il tipo di dispositivo e una modifica del layout di pagina o utilizzando una pagina diversa completamente completamente per schermi di dimensioni maggiori.

### <a name="creating-a-custom-layoutcustommd"></a>[Creazione di un layout personalizzato](custom.md)

Xamarin. Forms definisce quattro classi layout - [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), [ `AbsoluteLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/), [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/), e [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/), e ognuno dispone i relativi elementi figlio in modo diverso. Tuttavia, talvolta il necessario per organizzare il contenuto di pagina utilizzando un layout non forniti da xamarin. Forms. In questo articolo viene illustrato come scrivere una classe di layout personalizzato e viene illustrato un orientamento sensibili `WrapLayout` classe che dispone i relativi elementi figlio orizzontalmente attraverso la pagina e quindi esegue il wrapping la visualizzazione di elementi figlio successivi a righe aggiuntive.

### <a name="layout-compressionlayout-compressionmd"></a>[Compressione del layout](layout-compression.md)

La compressione di layout rimuove layout specificato dall'albero visuale nel tentativo di migliorare le prestazioni per il rendering della pagina. Il miglioramento delle prestazioni offerto varia in base alla complessità di una pagina, alla versione del sistema operativo in uso e al dispositivo in cui viene eseguita l'applicazione. Tuttavia, le prestazioni miglioreranno in modo più evidente nei dispositivi meno recenti.

## <a name="making-your-choice"></a>La scelta.

Tenere presente nella maggior parte dei casi, più di un'opzione di layout può essere utilizzata per implementare la progettazione desiderata. Quando sono presenti più le scelte valide, prendere in considerazione l'approccio sarà più semplice per la situazione specifica.
Impossibile realizzare la maggior parte dei progetti con un solo layout, in modo nidificare layout come necessari per creare progetti più complessi.


## <a name="related-links"></a>Collegamenti correlati

- [Linee guida dell'interfaccia umana Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG)
- [Sito Web di progettazione di Android](https://developer.android.com/design/index.html)
- [Layout (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Esempio BusinessTumble (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
