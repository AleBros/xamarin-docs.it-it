---
title: Vincoli di Layout a livello di codice
description: "Questa guida presenta vincoli di Layout automatico funzionante con iOS nel codice c# anziché crearne di nuove nella finestra di progettazione iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 17b9c14579b30ea27d700424c52c94c3b3da9146
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="programmatic-layout-constraints"></a>Vincoli di Layout a livello di codice

_Questa guida presenta vincoli di Layout automatico funzionante con iOS nel codice c# anziché crearne di nuove nella finestra di progettazione iOS._

Layout automatico (detto anche "adattivo layout") è un approccio di progettazione reattiva. A differenza di sistema di layout di transizione, in cui ogni elemento si trova a livello di codice per un punto dello schermo, Layout automatico sta *relazioni* -le posizioni degli elementi relazione ad altri elementi nell'area di progettazione. Il fulcro di Layout automatico è l'idea di vincoli o le regole che definiscono la posizione di un elemento o un set di elementi nel contesto di altri elementi sullo schermo. Poiché gli elementi non sono collegati a una particolare posizione sullo schermo, i vincoli consentono di creare un layout adattivo può sembrare adeguato su orientamenti di dispositivo e dimensioni dello schermo diverse.

In genere quando si lavora con Layout automatico in iOS, si utilizzerà la finestra di progettazione iOS per posizionare graficamente i vincoli di Layout, gli elementi di interfaccia utente. Tuttavia, potrebbe essere necessario creare e applicare vincoli nel codice c#. Ad esempio, quando utilizza in modo dinamico creata elementi dell'interfaccia utente aggiunto a un `UIView`.

Questa guida Mostra come creare e utilizzare i vincoli usando codice c# invece di creare graficamente in Progettazione iOS.

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>Creazione di vincoli a livello di codice

Come descritto in precedenza, in genere lavorerà con vincoli di Layout automatico nella finestra di progettazione iOS. Tutte le volte che è necessario creare i vincoli a livello di codice, è possibile scegliere tra tre opzioni:

* [Layout Anchor](#Layout-Anchors) -l'API fornisce l'accesso alle proprietà di ancoraggio (ad esempio `TopAnchor`, `BottomAnchor` o `HeightAnchor`) degli elementi dell'interfaccia utente viene applicato il vincolo.
* [Vincoli di layout](#Layout-Constraints) -è possibile creare vincoli utilizzando direttamente la `NSLayoutConstraint` classe.
* [Il linguaggio di formattazione visivo](#Visual-Format-Language) -fornisce un'immagine di ASCII come metodo per definire i vincoli.

Nelle sezioni seguenti verranno esaminate in dettaglio ogni opzione.

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>Punti di ancoraggio di layout

Tramite il `NSLayoutAnchor` (classe), si dispone di un'interfaccia intuitiva per la creazione di vincoli in base alle proprietà di ancoraggio degli elementi dell'interfaccia utente viene applicato il vincolo. Ad esempio, una vista del Controller superiore e inferiore Guide espone il `TopAnchor`, `BottomAnchor` e `HeightAnchor` proprietà di ancoraggio, mentre una vista espone le proprietà di bordo, center, dimensioni e della linea di base.

> [!IMPORTANT]
> **Nota:** oltre al set standard di proprietà di ancoraggio, visualizzazioni di iOS includono inoltre il `LayoutMarginsGuides` e `ReadableContentGuide` proprietà. Queste proprietà espongono `UILayoutGuide` oggetti per l'utilizzo con i margini della visualizzazione e la leggibilità del contenuto Guide rispettivamente.

Layout Anchor fornisce diversi metodi per la creazione di vincoli in un formato di facile lettura, compact:

- **ConstraintEqualTo** -definisce una relazione in cui `first attribute = second attribute + [constant]` con specificato facoltativamente `constant` valore di offset.
- **ConstraintGreaterThanOrEqualTo** -definisce una relazione in cui `first attribute >= second attribute + [constant]` con specificato facoltativamente `constant` valore di offset.
- **ConstraintLessThanOrEqualTo** -definisce una relazione in cui `first attribute <= second attribute + [constant]` con specificato facoltativamente `constant` valore di offset.

Ad esempio:

```csharp
// Get the parent view's layout
var margins = View.LayoutMarginsGuide;

// Pin the leading edge of the view to the margin
OrangeView.LeadingAnchor.ConstraintEqualTo (margins.LeadingAnchor).Active = true;

// Pin the trailing edge of the view to the margin
OrangeView.TrailingAnchor.ConstraintEqualTo (margins.TrailingAnchor).Active = true;

// Give the view a 1:2 aspect ratio
OrangeView.HeightAnchor.ConstraintEqualTo (OrangeView.WidthAnchor, 2.0f);
```

Un vincolo di Layout tipico può essere espresso come un'espressione lineare. Vedere l'esempio seguente:

[ ![](programmatic-layout-constraints-images/graph01.png "Un vincolo di Layout, espresso come un'espressione lineare")](programmatic-layout-constraints-images/graph01.png)

Che viene convertita come la seguente riga di codice c# usando Anchor Layout:

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

In cui le parti del codice c# corrispondono alle parti dell'equazione specificate come indicato di seguito:

<table width="100%" border="1">
<tr>
<td width="50%"><b>Equazione</b></td><td><b>Codice</b></td>
</tr>
<tr>
<td width="50%">Elemento 1</td><td>PurpleView</td>
</tr>
<tr>
<td width="50%">Attributo 1</td><td>LeadingAnchor</td>
</tr>
<tr>
<td width="50%">Relationship</td><td>ConstraintEqualTo</td>
</tr>
<tr>
<td width="50%">Moltiplicatore</td><td>Valore predefinito è 1.0 in modo non specificato</td>
</tr>
<tr>
<td width="50%">Elemento 2</td><td>OrangeView</td>
</tr>
<tr>
<td width="50%">Attributo 2</td><td>TrailingAnchor</td>
</tr>
<tr>
<td width="50%">Costante</td><td>10.0</td>
</tr>
</table>

Oltre a fornire solo i parametri necessari per risolvere un'equazione di vincolo layout specificato, ciascuno dei metodi di ancoraggio Layout imporre l'indipendenza dai tipi dei parametri passati a essi. Vincolo in modo orizzontale consente di limitare, ad esempio `LeadingAnchor` o `TrailingAnchor` può essere utilizzato solo con altri ancoraggio orizzontale tipi e i moltiplicatori vengono forniti solo per i vincoli di dimensioni.

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>Vincoli di layout

È possibile aggiungere manualmente i vincoli di Layout automatico creando direttamente un `NSLayoutConstraint` nel codice c#. A differenza di punti di ancoraggio di Layout, è necessario specificare un valore per ogni parametro, anche se non avrà alcun effetto sulla viene definito il vincolo. Di conseguenza, si finirà che produce una notevole quantità di difficile lettura, il codice di boilerplate. Ad esempio:

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

In cui il `NSLayoutAttribute` enum definisce il valore per i margini della vista che corrispondono al `LayoutMarginsGuide` proprietà, ad esempio `Left`, `Right`, `Top` e `Bottom` e `NSLayoutRelation` enumerazione definisce la relazione che verranno creati tra gli attributi specificati come `Equal`, `LessThanOrEqual` o `GreaterThanOrEqual`.

A differenza con l'API di ancoraggio, Layout di `NSLayoutConstraint` metodi di creazione non vengono evidenziati gli aspetti importanti di un vincolo specifico e non vi sono senza compilazione dei controlli effettuati nel vincolo di tempo. Di conseguenza, è facile creare un vincolo non valido che verrà generata un'eccezione in fase di esecuzione.

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>Formato visivo Language

Il linguaggio Visual formato consente di definire vincoli mediante grafica ASCII come le stringhe che forniscono una rappresentazione visiva del vincolo viene creato. Questo presenta i vantaggi e gli svantaggi seguenti:

- Il formato di lingua Visual applica la creazione di solo i vincoli validi.
 - Layout automatico genera vincoli nella console mediante il linguaggio formato visivo in modo i messaggi di debug saranno simile al codice utilizzato per creare il vincolo.
 - Il linguaggio Visual formato consente di creare più vincoli contemporaneamente con un'espressione molto compact.
 - Poiché non esiste alcuna convalida sul lato di compilazione delle stringhe di linguaggio formato visivo, i problemi possono essere individuati solamente in fase di esecuzione.
 - Poiché il linguaggio Visual formato privilegia visualizzazione rispetto completezza che alcuni tipi di vincolo non è possibile creare con esso (ad esempio rapporti).

Eseguire i passaggi seguenti quando si utilizza il formato di lingua Visual per creare un vincolo:

1. Creare un `NSDictionary` che contiene gli oggetti di visualizzazione e guide e una chiave che verrà utilizzata quando si definiscono i formati di stringa.
2. Se si desidera creare un `NSDictionary` che definisce un set di chiavi e valori (`NSNumber`) utilizzato come valore costante per il vincolo.
3. Creare la stringa di formato di layout una singola colonna o riga degli elementi.
4. Chiamare il `FromVisualFormat` metodo la `NSLayoutConstraint` classe deve generare i vincoli.
5. Chiamare il `ActivateConstraints` metodo la `NSLayoutConstraint` classe per attivare e applicare il vincolo.

Ad esempio, per creare iniziale sia un vincolo finale in linguaggio il formato visivo, è possibile utilizzare le operazioni seguenti:

```csharp
// Get views being constrained
var views = new NSMutableDictionary (); 
views.Add (new NSString ("orangeView"), OrangeView);

// Define format and assemble constraints
var format = "|-[orangeView]-|";
var constraints = NSLayoutConstraint.FromVisualFormat (format, NSLayoutFormatOptions.AlignAllTop, null, views);

// Apply constraints
NSLayoutConstraint.ActivateConstraints (constraints);
```

Poiché il formato di lingua Visual crea sempre zero vincoli di punti collegati ai margini della visualizzazione padre quando si utilizza la spaziatura predefinita, questo codice produce risultati identici per gli esempi descritti in precedenza.

Per le progettazioni dell'interfaccia utente più complesse, ad esempio più visualizzazioni figlio in una singola riga, il linguaggio Visual formato specifica sia la spaziatura orizzontale e l'allineamento verticale. Come illustrato nell'esempio precedente in cui viene specificato il `AlignAllTop` `NSLayoutFormatOptions` Allinea tutte le viste in una riga o colonna per le parti superiori.

Vedere Apple [Visual formato lingua appendice](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1) per alcuni esempi di specifica dei vincoli comuni e la grammatica di stringa di formato Visual.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questa guida è visualizzata la creazione e utilizzo dei vincoli di Layout automatico in c#, invece di creare graficamente in Progettazione iOS. Innanzitutto, descritto l'utilizzo di Layout Anchor (`NSLayoutAnchor`) per gestire il Layout automatico. Successivamente, è stato illustrato come utilizzare i vincoli di Layout (`NSLayoutConstraint`). Infine, visualizzata utilizzando il linguaggio Visual formato per il Layout automatico.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md)
- [procedura dettagliata controlli possa iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Layout automatico con la finestra di progettazione Xamarin iOS](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple - creazione di vincoli a livello di codice](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple - formato visivo Language appendice](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
