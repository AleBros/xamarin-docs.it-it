---
title: Vincoli di layout a livello di codice in Novell. iOS
description: Questa guida illustra come usare i vincoli di layout automatici C# iOS nel codice anziché crearli in iOS designer.
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 2ec012f882d6bc721e657385db333fce7a9e1aaf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002181"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>Vincoli di layout a livello di codice in Novell. iOS

_Questa guida illustra come usare i vincoli di layout automatici C# iOS nel codice anziché crearli in iOS designer._

Il layout automatico (detto anche "layout adattivo") è un approccio di progettazione reattivo. A differenza del sistema di layout di transizione, in cui il percorso di ogni elemento è hardcoded a un punto sullo schermo, il layout automatico riguarda le *relazioni* , ovvero le posizioni degli elementi rispetto ad altri elementi nell'area di progettazione. Il fulcro del layout automatico è l'idea di vincoli o regole che definiscono la posizione di un elemento o di un set di elementi nel contesto di altri elementi sullo schermo. Poiché gli elementi non sono collegati a una particolare posizione sullo schermo, i vincoli consentono di creare un layout adattivo che risulti valido in dimensioni dello schermo e orientamenti dei dispositivi diversi.

In genere, quando si usa il layout automatico in iOS, si userà la finestra di progettazione iOS per inserire graficamente vincoli di layout sugli elementi dell'interfaccia utente. In alcuni casi, tuttavia, è possibile che sia necessario creare e applicare vincoli C# nel codice. Ad esempio, quando si usano elementi dell'interfaccia utente creati dinamicamente aggiunti a una `UIView`.

Questa guida illustra come creare e usare vincoli usando C# il codice anziché crearli graficamente nella finestra di progettazione di iOS.

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>Creazione di vincoli a livello di codice

Come indicato in precedenza, in genere si lavorerà con i vincoli di layout automatico in iOS designer. Per gli orari in cui è necessario creare vincoli a livello di codice, è possibile scegliere tra tre opzioni:

- [Ancoraggi del layout](#Layout-Anchors) : questa API fornisce l'accesso alle proprietà di ancoraggio, ad esempio `TopAnchor`, `BottomAnchor` o `HeightAnchor`, degli elementi dell'interfaccia utente vincolati.
- [Vincoli di layout](#Layout-Constraints) : è possibile creare vincoli direttamente usando la classe `NSLayoutConstraint`.
- [Linguaggio di formattazione visuale](#Visual-Format-Language) : fornisce un metodo di tipo Art ASCII per definire i vincoli.

Le sezioni seguenti illustrano in dettaglio ogni opzione.

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>Ancoraggi layout

Usando la classe `NSLayoutAnchor`, è presente un'interfaccia Fluent per la creazione di vincoli basati sulle proprietà di ancoraggio degli elementi dell'interfaccia utente vincolati. Ad esempio, le guide di layout Top e Bottom del controller di visualizzazione espongono le proprietà `TopAnchor`, `BottomAnchor` e `HeightAnchor` Anchor mentre una visualizzazione espone le proprietà Edge, Center, Size e baseline.

> [!IMPORTANT]
> Oltre al set standard di proprietà di ancoraggio, le visualizzazioni iOS includono anche le proprietà `LayoutMarginsGuides` e `ReadableContentGuide`. Queste proprietà espongono `UILayoutGuide` oggetti per l'utilizzo dei margini della vista e delle guide di contenuto leggibili rispettivamente.

Gli ancoraggi del layout forniscono diversi metodi per la creazione di vincoli in un formato compatto e di facile lettura:

- **ConstraintEqualTo** : definisce una relazione in cui `first attribute = second attribute + [constant]` con un valore di offset `constant` fornito facoltativamente.
- **ConstraintGreaterThanOrEqualTo** : definisce una relazione in cui `first attribute >= second attribute + [constant]` con un valore di offset `constant` fornito facoltativamente.
- **ConstraintLessThanOrEqualTo** : definisce una relazione in cui `first attribute <= second attribute + [constant]` con un valore di offset `constant` fornito facoltativamente.

Esempio:

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

Un vincolo di layout tipico può essere espresso semplicemente come espressione lineare. Vedere l'esempio seguente:

[![](programmatic-layout-constraints-images/graph01.png "A Layout Constraint expressed as a linear expression")](programmatic-layout-constraints-images/graph01.png#lightbox)

Che verrebbe convertito nella riga di codice seguente usando C# gli ancoraggi del layout:

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

Dove le parti del C# codice corrispondono alle parti specificate dell'equazione come indicato di seguito:

|Equazione|Codice|
|---|---|
|Elemento 1|PurpleView|
|Attributo 1|LeadingAnchor|
|Relationship|ConstraintEqualTo|
|Moltiplicatore|Il valore predefinito è 1,0, pertanto non è specificato|
|Elemento 2|OrangeView|
|Attributo 2|TrailingAnchor|
|Costante|10.0|

Oltre a fornire solo i parametri necessari per risolvere un'equazione di vincolo di layout specificata, ogni metodo di ancoraggio del layout impone l'indipendenza dai tipi dei parametri passati. Pertanto, i vincoli orizzontali, ad esempio `LeadingAnchor` o `TrailingAnchor` possono essere utilizzati solo con altri tipi di ancoraggio orizzontali e i moltiplicatori vengono forniti solo ai vincoli di dimensione.

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>Vincoli di layout

È possibile aggiungere manualmente i vincoli di layout automatico costruendo direttamente un `NSLayoutConstraint` nel C# codice. Diversamente da quanto avviene con gli ancoraggi del layout, è necessario specificare un valore per ogni parametro, anche se non avrà alcun effetto sul vincolo definito. Di conseguenza, si otterrà una notevole quantità di codice standard e di difficile lettura. Esempio:

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

Dove l'enumerazione `NSLayoutAttribute` definisce il valore per i margini della vista e corrisponde alle proprietà `LayoutMarginsGuide`, ad esempio `Left`, `Right``Top` e `Bottom` e l'enumerazione `NSLayoutRelation` definisce la relazione che verrà creata tra gli attributi specificati come `Equal`, `LessThanOrEqual` o `GreaterThanOrEqual`.

Diversamente da quanto avviene con l'API di ancoraggio del layout, i metodi di creazione `NSLayoutConstraint` non evidenziano gli aspetti importanti di un determinato vincolo e non vengono eseguiti controlli relativi al tempo di compilazione sul vincolo. Di conseguenza, è facile creare un vincolo non valido che genererà un'eccezione in fase di esecuzione.

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>Lingua del formato visuale

Il linguaggio del formato visuale consente di definire vincoli usando immagini ASCII come stringhe che forniscono una rappresentazione visiva del vincolo da creare. Ciò offre i vantaggi e gli svantaggi seguenti:

- Il linguaggio del formato visivo applica solo la creazione di vincoli validi.
- Il layout automatico restituisce vincoli alla console utilizzando il linguaggio del formato visivo, in modo che i messaggi di debug siano simili al codice utilizzato per creare il vincolo.
- Il linguaggio del formato visivo consente di creare più vincoli contemporaneamente con un'espressione molto compatta.
- Poiché non esiste una convalida sul lato compilazione delle stringhe del linguaggio Visual Format, i problemi possono essere individuati solo in fase di esecuzione.
- Poiché il linguaggio del formato visivo enfatizza la visualizzazione rispetto alla completezza, non è possibile creare i tipi di vincolo con esso (ad esempio, i rapporti).

Quando si usa il linguaggio del formato visivo per creare un vincolo, seguire questa procedura:

1. Creare un `NSDictionary` contenente gli oggetti visualizzazione e le guide di layout e una chiave di stringa che verrà usata durante la definizione dei formati.
2. Facoltativamente, è possibile creare un `NSDictionary` che definisce un set di chiavi e valori (`NSNumber`) usati come valore costante per il vincolo.
3. Creare la stringa di formato per il layout di una singola colonna o riga di elementi.
4. Chiamare il metodo `FromVisualFormat` della classe `NSLayoutConstraint` per generare i vincoli.
5. Chiamare il metodo `ActivateConstraints` della classe `NSLayoutConstraint` per attivare e applicare i vincoli.

Per creare, ad esempio, un vincolo finale e un vincolo finale nel linguaggio del formato visivo, è possibile usare quanto segue:

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

Poiché il linguaggio del formato visivo crea sempre vincoli di punti zero collegati ai margini della visualizzazione padre quando si usa la spaziatura predefinita, questo codice produce risultati identici agli esempi presentati in precedenza.

Per progettare interfacce utente più complesse, ad esempio più visualizzazioni figlio su una sola riga, il linguaggio del formato visivo specifica sia la spaziatura orizzontale che l'allineamento verticale. Come nell'esempio precedente, in cui specifica il `AlignAllTop` `NSLayoutFormatOptions` allinea tutte le visualizzazioni di una riga o di una colonna ai relativi vertici.

Per alcuni esempi relativi alla specifica di vincoli comuni e alla grammatica della stringa di formato visivo, vedere l' [appendice del linguaggio Visual Format](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1) di Apple.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questa guida ha presentato la creazione e l'uso di vincoli C# di layout automatici in invece di crearli graficamente nella finestra di progettazione di iOS. In primo luogo, viene esaminato l'utilizzo di ancoraggi del layout (`NSLayoutAnchor`) per gestire il layout automatico. Viene quindi illustrato come usare i vincoli di layout (`NSLayoutConstraint`). Infine, è stato presentato utilizzando il linguaggio del formato visivo per il layout automatico.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md)
- [Procedura dettagliata per i controlli iOS designable](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Layout automatico con il Xamarin Designer per iOS](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple-creazione di vincoli a livello di codice](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Appendice per il linguaggio Apple-Visual Format](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
