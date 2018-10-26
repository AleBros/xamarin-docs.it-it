---
title: tvOS stili dell'interfaccia utente in Xamarin
description: Questo articolo descrive la luce e scuro temi dell'interfaccia utente di Apple è aggiunto a tvOS 10 e come implementarli in un'app xamarin. tvos.
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 2536ca5d3bff3f5b7962bc4fcf58b31a130fd03c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104765"
---
# <a name="tvos-user-interface-styles-in-xamarin"></a>tvOS stili dell'interfaccia utente in Xamarin

_Questo articolo descrive la luce e scuro temi dell'interfaccia utente di Apple è aggiunto a tvOS 10 e come implementarli in un'app xamarin. tvos._

tvOS 10 ora supporta sia un'interfaccia utente chiaro e scuro tema che tutti UIKit compilazione aggiuntivo controlli verranno automaticamente adattarsi alle, in base alle preferenze dell'utente. Inoltre, lo sviluppatore possibile regolare manualmente gli elementi dell'interfaccia utente in base al tema selezionato dall'utente e può eseguire l'override di un dato tema.

<a name="About-the-New-User-Interface-Styles" />

## <a name="about-the-new-user-interface-styles"></a>Sui nuovi stili dell'interfaccia utente

Come indicato in precedenza, tvOS 10 ora supporta sia un'interfaccia utente chiaro e scuro tema che tutti UIKit compilazione aggiuntivo controlli verranno automaticamente adattarsi alle, in base alle preferenze dell'utente.

L'utente può cambiare il tema passando **le impostazioni** > **generali** > **aspetto** e il passaggio tra **Light**  e **scuro**:

[![](user-interface-styles-images/theme01.png "L'app impostazioni")](user-interface-styles-images/theme01.png#lightbox)

Quando la **scuro** tema è selezionato, tutti gli elementi dell'interfaccia utente passerà a chiaro su scuro sfondo:

[![](user-interface-styles-images/theme02.png "Il tema scuro")](user-interface-styles-images/theme02.png#lightbox)

L'utente ha la possibilità di cambiare il tema in qualsiasi momento e potrebbe essere eseguito in modo basato su attività corrente, in cui si trova Apple TV o l'ora del giorno.

Il tema dell'interfaccia utente di luce è il tema predefinito e qualsiasi App tvOS esistenti continuerà a utilizzare il tema chiaro, indipendentemente dalle preferenze dell'utente, a meno che non vengono modificati per tvOS 10 in modo da poter sfruttare il tema scuro. Un'app tvOS 10 ha anche la possibilità di eseguire l'override del tema corrente e usano sempre il tema chiaro o scuro per alcuni o tutti dell'interfaccia utente.

<a name="Adopting-the-Light-and-Dark-Themes" />

## <a name="adopting-the-light-and-dark-themes"></a>Adottare i temi chiaro e scuri

Per supportare questa funzionalità, Apple ha aggiunto una nuova API per il `UITraitCollection` classe e un'app tvOS deve acconsentire esplicitamente per supportare l'aspetto scuro (tramite un'impostazione nel relativo `Info.plist` file).

Per acconsentire esplicitamente al supporto dei temi chiaro e scuro, effettuare le operazioni seguenti:

1. In **Esplora soluzioni** fare doppio clic sul file `Info.plist` per aprirlo e modificarlo.
2. Selezionare il **origine** visualizzazione (nella parte inferiore dell'editor).
3. Aggiungere una nuova chiave e chiamarlo `UIUserInterfaceStyle`: 

    [![](user-interface-styles-images/theme03.png "La chiave UIUserInterfaceStyle")](user-interface-styles-images/theme03.png#lightbox)
4. Lasciare il tipo impostato su `String` e immettere un valore di `Automatic`: 

    [![](user-interface-styles-images/theme04.png "Immettere automatico")](user-interface-styles-images/theme04.png#lightbox)
5. Salvare le modifiche apportate al file.

Esistono tre possibili valori per il `UIUserInterfaceStyle` chiave:

- **Light** -forza l'interfaccia utente dell'app tvOS usi sempre il tema chiaro.
- **Scuro** -forza l'interfaccia utente dell'app tvOS usi sempre il tema scuro.
- **Automatica** -passa tra il tema chiaro e scuro in base alle preferenze dell'utente nelle impostazioni. Si tratta dell'impostazione preferita.

<a name="UIKit-Theme-Support" />

### <a name="uikit-theme-support"></a>Supporto dei temi UIKit

Se un'app tvOS Usa standard e incorporati `UIView` , i controlli basati su automaticamente risponderà al tema dell'interfaccia utente senza alcun intervento da parte di sviluppatori.

È inoltre `UILabel` e `UITextView` cambierà automaticamente i colori in base al tema dell'interfaccia utente di selezionare:

- Il testo verrà nero il tema chiaro.
- Il testo sarà bianco nel tema scuro.

Se lo sviluppatore dovesse cambia il colore del testo manualmente (per la Storyboard o codice), saranno responsabili della gestione modifiche di colore in base al tema dell'interfaccia utente.

<a name="New-Blur-Effects" />

### <a name="new-blur-effects"></a>Nuovi effetti di sfocatura

Per supportare i temi chiaro e scuro in un'app tvOS 10, Apple ha aggiunto due nuovi effetti sfocatura. Questi nuovi effetti regolerà automaticamente la sfocatura dipende dal tema dell'interfaccia utente che l'utente ha selezionato come indicato di seguito:

- `UIBlurEffectStyleRegular` -Viene utilizzata una luce sfocare il tema chiaro e scuro sfocatura nel tema scuro.
- `UIBlurEffectStyleProminent` -Usa un' sfocatura molto chiaro il tema chiaro e un' sfocatura extra-scuro nel tema scuro.

<a name="Working-with-Trait-Collections" />

## <a name="working-with-trait-collections"></a>Uso delle raccolte di tratto

Il nuovo `UserInterfaceStyle` proprietà del `UITraitCollection` classe può essere utilizzata per ottenere il tema dell'interfaccia utente attualmente selezionato e sarà un `UIUserInterfaceStyle` enum di uno dei valori seguenti:

- **Light** -tema dell'interfaccia utente di luce è selezionata.
- **Scuro** -tema dell'interfaccia utente scuro sia selezionata.
- **Non specificato** -la vista non è stata visualizzata su schermo, in modo che il tema dell'interfaccia utente corrente è sconosciuto.

Inoltre, le raccolte di tratto di avere le funzionalità seguenti in tvOS 10:
 
- Il proxy di aspetto può essere personalizzato in base il `UserInterfaceStyle` di un determinato `UITraitCollection` per modificare elementi, ad esempio immagini o basati sul tema colori di elementi.
- Un'app tvOS può gestire le modifiche alla raccolta dei tratti eseguendo l'override di `TraitCollectionDidChange` metodo di un `UIView` o `UIViewController` classe.

> [!IMPORTANT]
> L'anteprima di xamarin. tvos per tvOS 10 non supportano completamente `UIUserInterfaceStyle` per `UITraitCollection` ancora. Verrà aggiunto il supporto completo in una versione futura.




<a name="Customizing-Appearance-Based-on-Theme" />

### <a name="customizing-appearance-based-on-theme"></a>Personalizzazione dell'aspetto di base del tema

Per gli elementi dell'interfaccia utente che supportano il proxy di aspetto, l'aspetto può essere modificata in base al tema dell'interfaccia utente della raccolta dei tratti. Pertanto, per un determinato elemento dell'interfaccia utente, lo sviluppatore può specificare un colore per il tema chiaro e un altro per il tema scuro.

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> Sfortunatamente, l'anteprima di xamarin. tvos per tvOS 10 non supportano completamente `UIUserInterfaceStyle` per `UITraitCollection`, pertanto questo tipo di personalizzazione non è ancora disponibile. Verrà aggiunto il supporto completo in una versione futura.

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="responding-to-theme-changes-directly"></a>Rispondere alle modifiche al tema direttamente

In developer richiede una maggiore controllo sull'aspetto di un elemento dell'interfaccia utente in base al tema dell'interfaccia utente selezionato, è possibile eseguire l'override di `TraitCollectionDidChange` metodo di un `UIView` o `UIViewController` classe.

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

### <a name="overriding-a-trait-collection"></a>Si esegue l'override di una raccolta di tratto

Basato sulla progettazione di un'app tvOS, talvolta potrebbe essere quando lo sviluppatore deve eseguire l'override la raccolta dei tratti di un determinato elemento dell'interfaccia utente che verrà sempre utilizzare un tema dell'interfaccia utente specifico.

Questa operazione può essere eseguita usando il `SetOverrideTraitCollection` metodo su di `UIViewController` classe. Ad esempio:

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

Per altre informazioni, vedere la [tratti](~/ios/user-interface/storyboards/unified-storyboards.md) e [Traits viene sottoposto a override](~/ios/user-interface/storyboards/unified-storyboards.md) sezioni del nostro [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) documentazione.

<a name="Trait-Collections-and-Storyboards" />

### <a name="trait-collections-and-storyboards"></a>Gli storyboard e le raccolte dei tratti

In tvOS 10, Storyboard di un'app può essere impostato su rispondere alle raccolte di tratto e molti elementi dell'interfaccia utente possono essere informati tema scuro e chiaro. L'anteprima corrente xamarin. tvos per tvOS 10 non supporta questa funzionalità nella finestra di progettazione dell'interfaccia ancora, quindi lo Storyboard dovranno essere modificati in Interface Builder di Xcode come soluzione alternativa.

Per abilitare il supporto dei tratti raccolta, eseguire le operazioni seguenti:

1. Fare doppio clic sul file Storyboard nel **Esplora soluzioni** e selezionare **aperta con** > **Interface Builder Xcode**: 

    [![](user-interface-styles-images/theme05.png "Apri con Interface Builder di Xcode")](user-interface-styles-images/theme05.png#lightbox) 
2. Per abilitare il supporto di raccolta dei tratti, passare al **File Inspector** e controllare il **variazioni dei tratti di utilizzare** proprietà nel **documento di Interface Builder** sezione: 

    [![](user-interface-styles-images/theme06.png "Abilitare il supporto di raccolta dei tratti")](user-interface-styles-images/theme06.png#lightbox)
3. Confermare la modifica per usare variazioni dei tratti: 

    [![](user-interface-styles-images/theme07.png "L'uso avviso variazioni dei tratti")](user-interface-styles-images/theme07.png#lightbox)
4. Salvare le modifiche apportate al file Storyboard.

Apple ha aggiunto le possibilità seguenti quando si modifica tvOS storyboard di Interface Builder:

* Lo sviluppatore può specificare varianti diverse degli elementi dell'interfaccia utente basati sul tema dell'interfaccia utente nel **Inspector attributo**:
    
    * Molte proprietà hanno ora un **+** accanto che è possibile fare clic per aggiungere una versione specifica di tema dell'interfaccia utente: 

        [![](user-interface-styles-images/theme08.png "Aggiungere una versione specifica di tema dell'interfaccia utente")](user-interface-styles-images/theme08.png#lightbox) 
    
    * Lo sviluppatore può specificare una nuova proprietà oppure fare clic sui **x** pulsante per rimuoverlo: 

        [![](user-interface-styles-images/theme09.png "Specificare una nuova proprietà oppure fare clic sul pulsante x per rimuoverlo")](user-interface-styles-images/theme09.png#lightbox)
* Lo sviluppatore può visualizzare l'anteprima di una progettazione dell'interfaccia utente con tema chiaro o scuro all'interno di Interface Builder:
    
    * La parte inferiore dell'area di progettazione consente allo sviluppatore di cambiare il tema dell'interfaccia utente corrente: 

        [![](user-interface-styles-images/theme10.png "La parte inferiore dell'area di progettazione")](user-interface-styles-images/theme10.png#lightbox)
        
    * Verrà visualizzato il nuovo tema in Interface Builder e verranno visualizzate le modifiche specifiche dei tratti raccolta: 

        [![](user-interface-styles-images/theme11.png "Il tema visualizzato in Interface Builder")](user-interface-styles-images/theme11.png#lightbox)

Inoltre, il simulatore di tvOS dispone ora di un tasto di scelta rapida per consentire agli sviluppatori di passare rapidamente tra vari temi chiaro e scuro durante il debug di un'app tvOS. Usare la **comando-MAIUSC-D** sequenza per alternare chiaro e scuro di tastiera.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la luce e scuro temi dell'interfaccia utente di Apple è aggiunto a tvOS 10 e come implementarli in un'app xamarin. tvos.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Che cos'è una novità a tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
