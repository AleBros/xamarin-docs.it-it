---
title: Layout di xamarin. Forms
description: Xamarin. Forms dispone di diversi layout e le funzionalità nell'organizzazione del contenuto sullo schermo e vengono spiegati in questo articolo.
ms.prod: xamarin
ms.assetid: 65030DA3-C7C1-4A02-B478-811073C39139
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: 1889579a48364204a977d63bd9bdb875df37a2bf
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657026"
---
# <a name="layouts-in-xamarinforms"></a>Layout di xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

Xamarin. Forms dispone di diversi layout e le funzionalità nell'organizzazione del contenuto sullo schermo.

> [!VIDEO https://youtube.com/embed/4HlLjTZQzjM]

**Video sui layout di Novell. Forms**

Ogni controllo di layout è descritta di seguito, oltre a informazioni dettagliate su come gestire le modifiche apportate orientamento dello schermo:

* **[StackLayout](stack-layout.md)** : usato per disporre le visualizzazioni in modo lineare, orizzontalmente o verticalmente. Viste in un StackLayout possono essere allineate al centro, a sinistra o destro del layout.
* **[AbsoluteLayout](absolute-layout.md)** : consente di disporre le viste impostando le coordinate & dimensioni in termini di valori assoluti o rapporti. AbsoluteLayout utilizzabile per le visualizzazioni di livello, nonché ancorarli a sinistra, destra o al centro.
* **[Sul relativelayout](relative-layout.md)** : utilizzato per disporre le viste impostando vincoli relativi alle dimensioni del padre & posizione.
* **[Grid](grid.md)** : usato per disporre le visualizzazioni in una griglia. Le righe e colonne possono essere specificate in termini di valori assoluti o rapporti.
* **[FlexLayout](flex-layout.md)** : usato per disporre le visualizzazioni orizzontalmente o verticalmente con il wrapping.
* **[ScrollView](scroll-view.md)** : viene usato per fornire lo scorrimento quando una visualizzazione non può adattarsi interamente ai limiti dello schermo.
* **[LayoutOptions](layout-options.md)** : definisce l'allineamento e l'espansione per una vista rispetto al relativo elemento padre.
* **[Trasparenza input](#input_transparency)** : specifica se un elemento riceve input.
* **[Margin e Padding](margin-and-padding.md)** : illustra come controllare il comportamento del layout quando viene eseguito il rendering di un elemento nell'interfaccia utente.
* **[Orientamento del dispositivo](device-orientation.md)** : spiega come gestire le modifiche dell'orientamento del dispositivo.
* **[Layout su tablet e dispositivi desktop](tablet.md)** : Mostra come ottimizzare per schermi più grandi in ogni piattaforma.
* **[Layout associabili](bindable-layouts.md)** : consente alle classi di layout di generare il contenuto mediante associazione a una raccolta di elementi.
* **[Creazione di un layout personalizzato](custom.md)** : viene illustrato come creare una classe di layout personalizzata.
* **[Compressione del layout](layout-compression.md)** : rimuove il layout specificato dalla struttura ad albero visuale nel tentativo di migliorare le prestazioni di rendering della pagina.

I controlli della piattaforma sono anche utilizzabile direttamente nei layout di xamarin. Forms con [ **incorporamento nativa** ](~/xamarin-forms/platform/native-views/index.md) (novità in xamarin. Forms 2.2), ed è possibile [ **creare layout personalizzati** ](custom.md) per soddisfare requisiti specifici.

Il grafico seguente visualizza i controlli di layout:

[![](images/layouts-sml.png "Xamarin.Forms Layouts")](images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="choosing-the-right-layout"></a>Scegliere il Layout giusto

I layout che scelto nell'app per la Guida in linea oppure influire negativamente sulle come si crea un'app xamarin. Forms interessante e utilizzabile. Mettendo un po' da considerare come funziona ogni layout consente di scrivere il codice dell'interfaccia utente più pulita e più scalabile. Una schermata può avere una combinazione di layout diversi per ottenere una progettazione specifica.

### <a name="stacklayoutstack-layoutmd"></a>[StackLayout](stack-layout.md)

Il `StackLayout` viene utilizzato per visualizzare le visualizzazioni lungo una riga orizzontale o verticale. Posizione e dimensioni all'interno del layout viene determinata in base a una vista `HeightRequest`, `WidthRequest`, `HorizontalOptions` e `VerticalOptions`. `StackLayout` viene spesso utilizzato come il layout di base, la disposizione di altri layout sullo schermo.

Per un esempio di quando `StackLayout` potrebbe essere una buona scelta, prendere in considerazione un'app che è necessario visualizzare un pulsante e un'etichetta con l'etichetta allineato a sinistra e il pulsante allineato a destra.

```xaml
<StackLayout Orientation="Horizontal">
  <Label HorizontalOptions="StartAndExpand" Text="Label" />
  <Button HorizontalOptions="End" Text="Button" />
</StackLayout>
```

### <a name="flexlayoutflex-layoutmd"></a>[FlexLayout](flex-layout.md)

Il `FlexLayout` è simile a `StackLayout` in cui viene visualizzata visualizzazioni figlio orizzontalmente o verticalmente:

```xaml
<FlexLayout Direction="Column"
            AlignItems="Center"
            JustifyContent="SpaceEvenly">

    <Label Text="FlexLayout in Action" />
    <Button Text="Button" />
    <Label Text="Another Label" />
</FlexLayout>
```

Tuttavia, se sono presenti troppi elementi figlio per rientrare in una singola riga o colonna, `FlexLayout` è inoltre in grado di ritorno a capo le visualizzazioni. `FlexLayout` si basa sul modulo di Layout casella flessibile di CSS e ha molte delle stesse opzioni predefinite per posizionare e allineare gli elementi figlio.

### <a name="absolutelayoutabsolute-layoutmd"></a>[AbsoluteLayout](absolute-layout.md)

Il `AbsoluteLayout` viene utilizzato per visualizzare le visualizzazioni, con dimensioni e posizione viene specificata sia come valori espliciti o relativo alle dimensioni del layout. A differenza `StackLayout` e `Grid`, `AbsoluteLayout` consente figlio viste si sovrappongano. A differenza `RelativeLayout`, `AbsoluteLayout` non consente di posizionare gli elementi fuori dallo schermo.

Per un esempio di quando `AbsoluteLayout` potrebbe essere una buona scelta, si consideri un'applicazione che deve presentare le raccolte di oggetti come stack. Questo spesso si verifica quando la presentazione di album di foto o brani. Il codice seguente fornisce l'aspetto di una pila, con elementi ruotati hint il contenuto dell'accumulo per:

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

Tenere presente i seguenti aspetti di codice riportato in precedenza:

- Ogni `Image` viene visualizzata nella stessa posizione (all'interno dello spazio orizzontale)
- Il `Padding` viene considerato dal `AbsoluteLayout`, a differenza `RelativeLayout`, che viene ignorato.
- `AbsoluteLayout.LayoutFlags` Specifica come verranno interpretati i limiti di layout. In questo caso `PositionProportional`, significa che le coordinate sarà un rapporto tra le dimensioni del layout, mentre la dimensione verrà interpretata come una dimensione esplicita.
- `AbsoluteLayout.Layoutbounds` Specifica la posizione orizzontale, la posizione verticale, la larghezza e l'altezza in quell'ordine.

### <a name="relativelayoutrelative-layoutmd"></a>[RelativeLayout](relative-layout.md)

Il `RelativeLayout` viene utilizzato per visualizzare le visualizzazioni, con dimensioni e posizione specificati come valori rispetto ai valori del layout o un'altra visualizzazione. Valori relativi non sono necessario affinché corrisponda corrispondente valore per la vista correlata. Ad esempio, è possibile impostare una visualizzazione `Width` della proprietà proporzionale a un'altra visualizzazione `X` proprietà.

RelativeLayout utilizzabile per creare interfacce utente con scalabilità in modo proporzionale in dimensioni del dispositivo. il XAML seguente implementa una progettazione con le caselle negli angoli in primo piano, con un'asta con flag al centro:

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

Tenere presente i seguenti aspetti di codice riportato in precedenza:

- Entrambe le posizioni e le dimensioni vengono specificate come vincoli.
- Asta è denominato in modo che il flag (del riquadro verde) posizione può essere impostata rispetto all'asta.
- Le espressioni di vincolo hanno `Factor` e `Constant` proprietà, che può essere usata per definire le posizioni e le dimensioni in multipli (o le frazioni) delle proprietà di altri oggetti, oltre a una costante. Le costanti possono essere negative.

### <a name="gridgridmd"></a>[Griglia](grid.md)

Il `Grid` viene utilizzato per visualizzare gli elementi in righe e colonne. Si noti che la griglia non è una tabella, in modo che non includa il concetto di celle, righe di intestazione e piè di pagina o i bordi tra righe e colonne. Griglia in generale, non è appropriata per la visualizzazione dei dati in formato tabulare. Per tale uso, prendere in considerazione una [ListView](~/xamarin-forms/user-interface/listview/index.md) oppure [TableView](~/xamarin-forms/user-interface/tableview.md).

Per un esempio di quando un `Grid` è il layout giusto da usare, prendere in considerazione un input numerico per una calcolatrice. Un input numerico per un calcolo può essere costituito da quattro righe e tre colonne, ognuna con un pulsante. Il codice seguente implementa questa struttura:

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

Tenere presente i seguenti aspetti di codice riportato in precedenza:

- Griglie e le colonne sono specificate in modo esplicito, non è stato dedotto dal contenuto.
- `Height` e `Width` valori possono essere impostati su stelle, il che significa che la griglia verrà impostato tali valori per riempire lo spazio disponibile.
- Posizione di ogni pulsante viene specificata da `Grid.Row`  &  `Grid.Column` proprietà.

### <a name="layoutoptionslayout-optionsmd"></a>[LayoutOptions](layout-options.md)

Il [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) struttura può essere utilizzata per definire l'allineamento e la sostituzione per una vista, rispetto al padre.

### <a name="margin-and-paddingmargin-and-paddingmd"></a>[Margine e spaziatura interna](margin-and-padding.md)

Il [ `Margin` ](xref:Xamarin.Forms.View.Margin) e [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) proprietà controllano il comportamento di layout quando viene eseguito il rendering di un elemento nell'interfaccia utente.

<a name="input_transparency" />

### <a name="input-transparency"></a>Trasparenza di input

Ogni elemento ha un [ `InputTransparent` ](xref:Xamarin.Forms.VisualElement.InputTransparent) proprietà che consente di definire se l'elemento riceve l'input. Il valore predefinito è `false`, assicurando che l'elemento riceve l'input.

Quando questa proprietà è impostata su una classe di contenitore, ad esempio una classe di layout, i trasferimenti di valore agli elementi figlio. Pertanto, l'impostazione di [ `InputTransparent` ](xref:Xamarin.Forms.VisualElement.InputTransparent) proprietà `true` in un layout di classe comporterà gli elementi all'interno del layout non riceve l'input.

### <a name="device-orientationdevice-orientationmd"></a>[Orientamento del dispositivo](device-orientation.md)

Xamarin. Forms e il layout predefinito sono in grado di gestire modifiche apportate orientamento del dispositivo. Prendere in considerazione quali orientamenti di verranno supportate dall'app, nonché come si apporteranno utilizzo di spazio disponibile in modalità verticale e orizzontale.

### <a name="layout-for-tablet-and-desktop-appstabletmd"></a>[Layout per le app Desktop e Tablet](tablet.md)

iOS, Android e Universal Windows Platform tutte supportano le dimensioni dello schermo più grande su tablet dispositivi (nonché i computer portatili e desktop per Windows). Xamarin. Forms consente di ottimizzare l'app per schermi di dimensioni maggiori rilevamento del tipo di dispositivo e una regolazione del layout di pagina o utilizzando una pagina di completamente diversa completamente per schermi di dimensioni maggiori.

### <a name="bindable-layoutsbindable-layoutsmd"></a>[Layout associabili](bindable-layouts.md)

La `BindableLayout` classe consente a tutte le classi di layout che derivano [`Layout<T>`](xref:Xamarin.Forms.Layout`1) dalla classe di generare il contenuto mediante l'associazione a una raccolta di elementi, con l'opzione per impostare l'aspetto di [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)ogni elemento con un oggetto.

### <a name="creating-a-custom-layoutcustommd"></a>[Creazione di un layout personalizzato](custom.md)

Xamarin. Forms definisce quattro classi di layout - [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), [ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout), [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout), e [ `Grid` ](xref:Xamarin.Forms.Grid), e ognuno dispone i relativi elementi figlio in modo diverso. Tuttavia, in alcuni casi la necessità per organizzare i contenuti di pagina utilizzando un layout non forniti da xamarin. Forms. Questo articolo spiega come scrivere una classe di layout personalizzato e viene illustrato un orientamento minuscole `WrapLayout` classe che dispone gli elementi figlio in senso orizzontale attraverso la pagina e quindi esegue il wrapping la visualizzazione degli elementi figlio successivi per le righe aggiuntive.

### <a name="layout-compressionlayout-compressionmd"></a>[Compressione del layout](layout-compression.md)

Compressione dei layout rimuove i layout specificati dall'albero visuale nel tentativo di migliorare le prestazioni per il rendering della pagina. Il miglioramento delle prestazioni offerto varia in base alla complessità di una pagina, alla versione del sistema operativo in uso e al dispositivo in cui viene eseguita l'applicazione. Tuttavia, le prestazioni miglioreranno in modo più evidente nei dispositivi meno recenti.

## <a name="making-your-choice"></a>Rendendo la scelta

Tenere presente che nella maggior parte dei casi, più opzioni di layout sono utilizzabile per implementare la progettazione desiderata. Quando sono presenti più le scelte valide, prendere in considerazione l'approccio sarà più semplice alla situazione specifica.
Non è possibile realizzare la maggior parte delle progettazioni con un solo layout, pertanto i layout di annidamento come necessario creare strutture più complesse.

## <a name="related-links"></a>Collegamenti correlati

- [Human Interface Guidelines di Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG)
- [Sito Web di progettazione di Android](https://developer.android.com/design/index.html)
- [Layout (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Esempio BusinessTumble (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
