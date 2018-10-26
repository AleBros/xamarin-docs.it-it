---
title: Vincoli per il Layout a livello di codice in xamarin. IOS
description: Questa guida vengono illustrati i vincoli di Layout automatico in uso di iOS C# codice anziché crearne di nuove in iOS Designer.
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 3d8e69af7f790415343abf464ea2bb22e879e025
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106961"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>Vincoli per il Layout a livello di codice in xamarin. IOS

_Questa guida vengono illustrati i vincoli di Layout automatico in uso di iOS C# codice anziché crearne di nuove in iOS Designer._

Layout automatico (detto anche "layout adattivo") è un approccio di progettazione reattiva. A differenza di sistema di layout transitorio, posizione di ogni elemento in cui è hardcoded su un punto dello schermo, Layout automatico riguarda *relazioni* -le posizioni degli elementi relazione ad altri elementi nell'area di progettazione. Il cuore del Layout automatico è l'idea di vincoli o le regole che definiscono la posizione di un elemento o un set di elementi nel contesto di altri elementi sullo schermo. Poiché gli elementi non sono associati a una determinata posizione sullo schermo, i vincoli consentono di creare un layout adattivo in diverse dimensioni dello schermo e orientamenti del dispositivo.

In genere quando si lavora con Layout automatico in iOS, si userà iOS Designer per posizionare graficamente i vincoli per il Layout degli elementi dell'interfaccia utente. Tuttavia, potrebbero esserci casi è necessario creare e applicare i vincoli in C# codice. Ad esempio, quando utilizza in modo dinamico creato gli elementi dell'interfaccia utente aggiunto a un `UIView`.

Questa Guida illustrerà come creare e utilizzare i vincoli utilizzando C# codice invece di creare graficamente in iOS Designer.

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>Creazione dei vincoli a livello di codice

Come indicato in precedenza, in genere si lavorerà con vincoli per il Layout automatico in iOS Designer. Nei casi in cui è necessario creare i vincoli a livello di codice, sono disponibili tre opzioni per scegliere tra:

* [Layout ancoraggi](#Layout-Anchors) -questa API fornisce accesso alle proprietà di ancoraggio (ad esempio `TopAnchor`, `BottomAnchor` o `HeightAnchor`) degli elementi dell'interfaccia utente che è vincolati.
* [Vincoli per il layout](#Layout-Constraints) -è possibile creare direttamente usando i vincoli di `NSLayoutConstraint` classe.
* [Linguaggio Visual formattazione](#Visual-Format-Language) -fornisce un'arte ASCII come metodo per definire i vincoli.

Le sezioni seguenti verranno esaminati in dettaglio ogni opzione.

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>Ancoraggi layout

Tramite il `NSLayoutAnchor` (classe), è disponibile un'interfaccia intuitiva per la creazione di vincoli di base alle proprietà degli elementi dell'interfaccia utente che è vincolati ancoraggio. Ad esempio, una visualizzazione del Controller superiore e inferiore Guide espone il `TopAnchor`, `BottomAnchor` e `HeightAnchor` ancorare proprietà mentre una vista espone le proprietà di Microsoft edge, center, dimensioni e della linea di base.

> [!IMPORTANT]
> Oltre al set standard di proprietà di ancoraggio, le visualizzazioni di iOS includono anche il `LayoutMarginsGuides` e `ReadableContentGuide` proprietà. Queste proprietà espongano `UILayoutGuide` oggetti per l'utilizzo con i margini della visualizzazione e la leggibilità del contenuto Guide rispettivamente.

Gli ancoraggi layout forniscono numerosi metodi per la creazione di vincoli in un formato compatto e facile da leggere:

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

Un vincolo di Layout tipico può essere espresso semplicemente come un'espressione lineare. Vedere l'esempio seguente:

[![](programmatic-layout-constraints-images/graph01.png "Un vincolo di Layout espresso come un'espressione lineare")](programmatic-layout-constraints-images/graph01.png#lightbox)

Che viene convertito in seguente riga di C# codice che usa gli ancoraggi Layout:

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

In cui le parti del C# codice corrispondono alle parti dell'equazione specificate come indicato di seguito:

|Equazione|Codice|
|---|---|
|Elemento 1|PurpleView|
|Attributo 1|LeadingAnchor|
|Relationship|ConstraintEqualTo|
|Moltiplicatore|Il valore predefinito è 1,0 così non specificato|
|Elemento 2|OrangeView|
|Attributo 2|TrailingAnchor|
|Costante|10.0|

Oltre a fornire solo i parametri necessari per risolvere un'equazione di vincolo layout specificato, ognuno dei metodi di ancoraggio Layout imporre l'indipendenza dai tipi dei parametri passati a essi. Vincolo in modo orizzontale ancora, ad esempio `LeadingAnchor` o `TrailingAnchor` può essere usato solo con altri ancoraggio orizzontale tipi e i moltiplicatori vengono forniti solo per i vincoli di dimensione.

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>Vincoli per il layout

È possibile aggiungere manualmente i vincoli di Layout automatico creando direttamente un `NSLayoutConstraint` in C# codice. A differenza di punti di ancoraggio di Layout, è necessario specificare un valore per ogni parametro, anche se avrà alcun effetto sul vincolo da definire. Di conseguenza, finirà che produce una considerevole quantità di difficile lettura, il codice boilerplate. Ad esempio:

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

In cui il `NSLayoutAttribute` enum definisce il valore per i margini della visualizzazione e corrispondono ai `LayoutMarginsGuide` proprietà, ad esempio `Left`, `Right`, `Top` e `Bottom` e il `NSLayoutRelation` enum definisce la relazione che verranno creati tra gli attributi specificati come `Equal`, `LessThanOrEqual` o `GreaterThanOrEqual`.

A differenza con l'API di ancoraggio, Layout di `NSLayoutConstraint` metodi di creazione non di evidenziare gli aspetti importanti di un vincolo specifico e sono non disponibili compilazione dei controlli effettuati nel vincolo di tempo. Di conseguenza, è facile creare un vincolo valido che verrà generata un'eccezione in fase di esecuzione.

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>Formato visivo Language

Il linguaggio formato visivo consente di definire vincoli mediante la grafica ASCII conferisce simili a stringhe che forniscono una rappresentazione visiva del vincolo viene creato. Ciò comporta i vantaggi e gli svantaggi seguenti:

- Il linguaggio Visual formato impone la creazione di vincoli validi solo.
 - Layout automatico restituisce i vincoli nella console usando il linguaggio formato visivo in modo che i messaggi di debug saranno simile al codice usato per creare il vincolo.
 - Il linguaggio formato visivo consente di creare più vincoli allo stesso tempo con un'espressione molto compatta.
 - Poiché non esiste alcuna convalida sul lato compilazione delle stringhe di linguaggio formato visivo, i problemi possono essere individuati solamente in fase di esecuzione.
 - Poiché il linguaggio Visual formato privilegia visualizzazione rispetto alla completezza alcuni tipi di vincolo non è possibile creare con esso (ad esempio rapporti).

Quando si usa il linguaggio visivo di formato per creare un vincolo, è eseguire i passaggi seguenti:

1. Creare un `NSDictionary` che contiene gli oggetti di visualizzazione e le guide e una chiave di stringa che verrà usata quando si definiscono i formati.
2. Se si desidera creare un `NSDictionary` che definisce un set di chiavi e valori (`NSNumber`) utilizzato come valore costante per il vincolo.
3. Creare la stringa di formato di layout una singola colonna o riga degli elementi.
4. Chiamare il `FromVisualFormat` metodo di `NSLayoutConstraint` classe per cui generare i vincoli.
5. Chiamare il `ActivateConstraints` metodo di `NSLayoutConstraint` classe per l'attivazione e si applicano i vincoli.

Ad esempio, per creare una barra iniziale sia un vincolo finale nel linguaggio formato visivo, è possibile usare quanto segue:

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

Poiché il linguaggio formato Visual crea sempre zero vincoli punto collegati ai margini della visualizzazione padre quando si usa la spaziatura predefinita, questo codice produce risultati identici agli esempi presentati in precedenza.

Per progettazioni di interfaccia utente più complesse, ad esempio più visualizzazioni figlio in una singola riga, il linguaggio formato Visual specifica la spaziatura orizzontale e l'allineamento verticale. Come illustrato nell'esempio precedente in cui specifica la `AlignAllTop` `NSLayoutFormatOptions` Allinea tutte le viste in una riga o colonna per le parti superiori.

Vedere di Apple [Visual formato lingua appendice](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1) per alcuni esempi di specifica dei vincoli comuni e la grammatica di stringa di formato Visual.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questa Guida presentato la creazione e utilizzo dei vincoli di Layout automatico in C# invece di creare graficamente in iOS Designer. In primo luogo, esaminato utilizzando Layout Anchor (`NSLayoutAnchor`) per gestire il Layout automatico. Successivamente, è stato illustrato come usare i vincoli per il Layout (`NSLayoutConstraint`). Infine, visualizzata usando il linguaggio Visual formato per il Layout automatico.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md)
- [procedura dettagliata di controlli manipolati iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Layout automatico con la finestra di progettazione di Xamarin per iOS](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple - creazione di vincoli a livello di codice](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple - formato visivo Language appendice](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
