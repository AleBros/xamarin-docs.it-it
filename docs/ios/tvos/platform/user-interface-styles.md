---
title: Stili dell'interfaccia utente di tvOS in Novell
description: Questo articolo illustra i temi dell'interfaccia utente chiaro e scuro che Apple ha aggiunto a tvOS 10 e come implementarli in un'app Novell. tvOS.
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: fc9a8acb2c04d805e7abc52b2996ec5546b0fb69
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657375"
---
# <a name="tvos-user-interface-styles-in-xamarin"></a>Stili dell'interfaccia utente di tvOS in Novell

_Questo articolo illustra i temi dell'interfaccia utente chiaro e scuro che Apple ha aggiunto a tvOS 10 e come implementarli in un'app Novell. tvOS._

tvOS 10 supporta ora sia un tema di interfaccia utente scuro che chiaro che tutti i controlli UIKit di compilazione verranno adattati automaticamente in base alle preferenze dell'utente. Inoltre, lo sviluppatore può modificare manualmente gli elementi dell'interfaccia utente in base al tema selezionato dall'utente e può eseguire l'override di un determinato tema.

<a name="About-the-New-User-Interface-Styles" />

## <a name="about-the-new-user-interface-styles"></a>Informazioni sui nuovi stili dell'interfaccia utente

Come indicato in precedenza, tvOS 10 supporta ora sia un tema di interfaccia utente scuro che chiaro che tutti i controlli UIKit di compilazione si adatteranno automaticamente, in base alle preferenze dell'utente.

L'utente può cambiare questo tema passando a **Impostazioni** > **aspetto** **generale** > e passando da **chiaro** a **scuro**:

[![](user-interface-styles-images/theme01.png "App impostazioni")](user-interface-styles-images/theme01.png#lightbox)

Quando il tema **scuro** è selezionato, tutti gli elementi dell'interfaccia utente passano al testo chiaro sullo sfondo scuro:

[![](user-interface-styles-images/theme02.png "Tema scuro")](user-interface-styles-images/theme02.png#lightbox)

L'utente ha la possibilità di cambiare il tema in qualsiasi momento e può eseguire questa operazione in base all'attività corrente, in cui si trova l'Apple TV o l'ora del giorno.

Il tema dell'interfaccia utente chiaro è il tema predefinito e tutte le app tvOS esistenti utilizzeranno comunque il tema chiaro, indipendentemente dalle preferenze dell'utente, a meno che non vengano modificate per tvOS 10 per sfruttare il tema scuro. Un'app tvOS 10 ha anche la possibilità di eseguire l'override del tema corrente e di usare sempre il tema chiaro o scuro per alcune o tutte le relative interfacce utente.

<a name="Adopting-the-Light-and-Dark-Themes" />

## <a name="adopting-the-light-and-dark-themes"></a>Adozione dei temi chiaro e scuro

Per supportare questa funzionalità, Apple ha aggiunto una nuova API alla `UITraitCollection` classe e un'app tvOS deve acconsentire esplicitamente per supportare l'aspetto scuro (tramite un'impostazione `Info.plist` nel file).

Per acconsentire esplicitamente al supporto del tema chiaro e scuro, seguire questa procedura:

1. In **Esplora soluzioni** fare doppio clic sul file `Info.plist` per aprirlo e modificarlo.
2. Selezionare la visualizzazione **origine** (nella parte inferiore dell'editor).
3. Aggiungere una nuova chiave e chiamarla `UIUserInterfaceStyle`: 

    [![](user-interface-styles-images/theme03.png "Chiave UIUserInterfaceStyle")](user-interface-styles-images/theme03.png#lightbox)
4. Lasciare il tipo impostato su `String` e immettere un `Automatic`valore: 

    [![](user-interface-styles-images/theme04.png "Immetti automaticamente")](user-interface-styles-images/theme04.png#lightbox)
5. Salvare le modifiche apportate al file.

Sono disponibili tre valori possibili per la `UIUserInterfaceStyle` chiave:

- **Legge** : forza l'interfaccia utente dell'app tvOS a usare sempre il tema chiaro.
- **Dark** : forza l'interfaccia utente dell'app tvOS a usare sempre il tema scuro.
- **Automatico** : consente di alternare il tema chiaro e scuro in base alle preferenze dell'utente nelle impostazioni. Si tratta dell'impostazione preferita.

<a name="UIKit-Theme-Support" />

### <a name="uikit-theme-support"></a>Supporto del tema UIKit

Se un'app tvOS usa controlli `UIView` basati su standard e incorporati, risponderà automaticamente al tema dell'interfaccia utente senza alcun intervento da parte dello sviluppatore.

Inoltre, `UILabel` `UITextView` cambierà automaticamente il colore in base al tema Seleziona interfaccia utente:

- Il testo sarà nero nel tema chiaro.
- Il testo sarà bianco nel tema scuro.

Se lo sviluppatore modifica manualmente il colore del testo (nello storyboard o nel codice), sarà responsabile della gestione delle modifiche dei colori in base al tema dell'interfaccia utente.

<a name="New-Blur-Effects" />

### <a name="new-blur-effects"></a>Nuovi effetti sfocatura

Per supportare i temi chiaro e scuro in un'app tvOS 10, Apple ha aggiunto due nuovi effetti di sfocatura. Questi nuovi effetti modificheranno automaticamente la sfocatura in base al tema dell'interfaccia utente selezionato dall'utente, come indicato di seguito:

- `UIBlurEffectStyleRegular`: Usa una sfocatura chiara nel tema chiaro e una sfocatura scura nel tema scuro.
- `UIBlurEffectStyleProminent`: Usa una sfocatura molto chiara nel tema chiaro e una sfocatura più scura nel tema scuro.

<a name="Working-with-Trait-Collections" />

## <a name="working-with-trait-collections"></a>Uso delle raccolte di tratti

La nuova `UserInterfaceStyle` proprietà `UITraitCollection` della classe può essere usata per ottenere il tema dell'interfaccia utente attualmente selezionato e sarà un' `UIUserInterfaceStyle` enumerazione di uno dei valori seguenti:

- **Chiaro** : è selezionato il tema chiaro dell'interfaccia utente.
- **Scuro** : è selezionato il tema scuro dell'interfaccia utente.
- Non **specificato** : la visualizzazione non è ancora stata visualizzata, quindi il tema dell'interfaccia utente corrente è sconosciuto.

Inoltre, le raccolte di tratti hanno le funzionalità seguenti in tvOS 10:
 
- Il proxy `UserInterfaceStyle` di aspetto può essere personalizzato in base a di un `UITraitCollection` dato per modificare elementi quali immagini o colori di elementi in base al tema.
- Un'app tvOS può gestire le modifiche alla raccolta dei tratti eseguendo `TraitCollectionDidChange` l'override del `UIView` metodo `UIViewController` di una classe o.

> [!IMPORTANT]
> L'anteprima di Novell. tvOS per tvOS 10 non è ancora `UIUserInterfaceStyle` supportata `UITraitCollection` in modo completo. Il supporto completo verrà aggiunto in una versione futura.




<a name="Customizing-Appearance-Based-on-Theme" />

### <a name="customizing-appearance-based-on-theme"></a>Personalizzazione dell'aspetto in base al tema

Per gli elementi dell'interfaccia utente che supportano il proxy di aspetto, il loro aspetto può essere regolato in base al tema dell'interfaccia utente della relativa raccolta di tratti. Quindi, per un determinato elemento dell'interfaccia utente, lo sviluppatore può specificare un colore per il tema chiaro e un altro colore per il tema scuro.

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> Sfortunatamente, l'anteprima di Novell. tvOS per tvOS 10 non supporta `UIUserInterfaceStyle` `UITraitCollection`completamente, quindi questo tipo di personalizzazione non è ancora disponibile. Il supporto completo verrà aggiunto in una versione futura.

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="responding-to-theme-changes-directly"></a>Risposta diretta alle modifiche del tema

Nello sviluppatore è necessario un controllo più approfondito sull'aspetto di un elemento dell'interfaccia utente in base al tema dell'interfaccia utente selezionato `TraitCollectionDidChange` , che può `UIView` eseguire `UIViewController` l'override del metodo di una classe o.

Ad esempio:

```csharp
public override void TraitCollectionDidChange (UITraitCollection previousTraitCollection)
{
    base.TraitCollectionDidChange (previousTraitCollection);
    
    // Take action based on the Light or Dark theme
    ...
}
```

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="overriding-a-trait-collection"></a>Override di una raccolta di tratti

In base alla progettazione di un'app tvOS, a volte lo sviluppatore deve eseguire l'override della raccolta di tratti di un determinato elemento dell'interfaccia utente e usare sempre un tema specifico dell'interfaccia utente.

Questa operazione può essere eseguita usando `SetOverrideTraitCollection` il metodo `UIViewController` nella classe. Ad esempio:

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

Per altre informazioni, [vedere le sezioni](~/ios/user-interface/storyboards/unified-storyboards.md) relative ai tratti e all' [override dei tratti](~/ios/user-interface/storyboards/unified-storyboards.md) della documentazione [introduttiva per gli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) .

<a name="Trait-Collections-and-Storyboards" />

### <a name="trait-collections-and-storyboards"></a>Raccolte di tratti e storyboard

In tvOS 10, lo storyboard di un'app può essere impostato in modo da rispondere alle raccolte di tratti e molti elementi dell'interfaccia utente possono essere resi consapevoli del tema chiaro e scuro. La versione corrente di Novell. tvOS Early Preview per tvOS 10 non supporta ancora questa funzionalità nella finestra di progettazione dell'interfaccia, pertanto lo storyboard deve essere modificato nel Interface Builder di Xcode come soluzione alternativa.

Per abilitare il supporto per la raccolta di tratti, procedere come segue:

1. Fare clic con il pulsante destro del mouse sul file dello storyboard nel **Esplora soluzioni** e scegliere **Apri con** > **Xcode Interface Builder**: 

    [![](user-interface-styles-images/theme05.png "Apri con Xcode Interface Builder")](user-interface-styles-images/theme05.png#lightbox) 
2. Per abilitare il supporto per la raccolta di tratti, passare a controllo **file** e selezionare la proprietà **USA variazioni tratto** nella sezione **documento Interface Builder** : 

    [![](user-interface-styles-images/theme06.png "Abilita supporto per la raccolta di tratti")](user-interface-styles-images/theme06.png#lightbox)
3. Confermare la modifica per usare le variazioni dei tratti: 

    [![](user-interface-styles-images/theme07.png "Avviso di utilizzo delle variazioni dei tratti")](user-interface-styles-images/theme07.png#lightbox)
4. Salvare le modifiche apportate al file dello storyboard.

Apple ha aggiunto le funzionalità seguenti quando si modificano gli storyboard tvOS in Interface Builder:

* Lo sviluppatore può specificare diverse varianti di elementi dell'interfaccia utente in base al tema dell'interfaccia utente nel **controllo attributi**:
    
    * Diverse proprietà dispongono ora di **+** un accanto a cui è possibile fare clic per aggiungere una versione specifica del tema dell'interfaccia utente: 

        [![](user-interface-styles-images/theme08.png "Aggiungere una versione specifica del tema dell'interfaccia utente")](user-interface-styles-images/theme08.png#lightbox) 
    
    * Lo sviluppatore può specificare una nuova proprietà o fare clic sul pulsante **x** per rimuoverlo: 

        [![](user-interface-styles-images/theme09.png "Specificare una nuova proprietà o fare clic sul pulsante x per rimuoverlo")](user-interface-styles-images/theme09.png#lightbox)
* Lo sviluppatore può visualizzare in anteprima una progettazione dell'interfaccia utente in un tema chiaro o scuro dall'interno Interface Builder:
    
    * La parte inferiore del Area di progettazione consente allo sviluppatore di cambiare il tema corrente dell'interfaccia utente: 

        [![](user-interface-styles-images/theme10.png "Parte inferiore del Area di progettazione")](user-interface-styles-images/theme10.png#lightbox)
        
    * Il nuovo tema verrà visualizzato in Interface Builder e verranno visualizzate le regolazioni specifiche della raccolta di tratti: 

        [![](user-interface-styles-images/theme11.png "Il tema visualizzato in Interface Builder")](user-interface-styles-images/theme11.png#lightbox)

Inoltre, il simulatore tvOS dispone ora di un tasto di scelta rapida che consente allo sviluppatore di spostarsi rapidamente tra i temi chiaro e scuro durante il debug di un'app tvOS. Usare la sequenza di tasti **MAIUSC + D** per passare da chiaro a scuro e viceversa.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i temi di interfaccia utente chiaro e scuro che Apple ha aggiunto a tvOS 10 e come implementarli in un'app Novell. tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Novità di tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
