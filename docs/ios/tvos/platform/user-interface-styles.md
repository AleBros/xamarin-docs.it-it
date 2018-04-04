---
title: Nuovi stili di interfaccia utente
description: Questo articolo vengono illustrate la luce e temi scuro di interfaccia utente che Apple sia aggiunto al tvOS 10 e come implementarli in un'app Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: da75a99e842b13d42251cdd1c5195ec66ff4a513
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="new-user-interface-styles"></a>Nuovi stili di interfaccia utente

_Questo articolo vengono illustrate la luce e temi scuro di interfaccia utente che Apple sia aggiunto al tvOS 10 e come implementarli in un'app Xamarin.tvOS._

tvOS 10 ora supporta sia un'interfaccia utente chiaro e scuro tema tutti UIKit compilazione aggiuntivo controlli verranno automaticamente adattarsi alle, in base alle preferenze dell'utente. Inoltre, lo sviluppatore possibile regolare manualmente gli elementi dell'interfaccia utente in base al tema che l'utente ha selezionato e può eseguire l'override di un tema specificato.


<a name="About-the-New-User-Interface-Styles" />

## <a name="about-the-new-user-interface-styles"></a>Sui nuovi stili di interfaccia utente

Come descritto in precedenza, tvOS 10 ora supporta sia un'interfaccia utente chiaro e scuro tema tutti UIKit compilazione aggiuntivo controlli verranno automaticamente adattarsi alle, in base alle preferenze dell'utente.

L'utente può passare questo tema, passare a **impostazioni** > **generale** > **aspetto** e il passaggio tra **Light**  e **scuro**:

[![](user-interface-styles-images/theme01.png "L'app impostazioni")](user-interface-styles-images/theme01.png#lightbox)

Quando il **scuro** tema è selezionato, tutti gli elementi dell'interfaccia utente passerà in chiaro su uno sfondo scuro:

[![](user-interface-styles-images/theme02.png "Il tema scuro")](user-interface-styles-images/theme02.png#lightbox)

L'utente ha la possibilità di cambiare il tema in qualsiasi momento e potrebbe essere eseguito in modo basato su attività corrente, in cui si trova Apple TV o l'ora del giorno.

Il tema dell'interfaccia utente di luce è il tema predefinito e qualsiasi App tvOS esistente continuerà a utilizzare il tema chiaro, indipendentemente dalle preferenze dell'utente, a meno che non che vengano modificati per tvOS 10 per sfruttare i vantaggi del tema scuro. Inoltre, un'app tvOS 10 ha la possibilità di eseguire l'override del tema corrente e di utilizzare sempre il tema chiaro o scuro per alcuni o tutti dell'interfaccia utente.

<a name="Adopting-the-Light-and-Dark-Themes" />

## <a name="adopting-the-light-and-dark-themes"></a>Adozione i temi chiaro e scuri

Per supportare questa funzionalità, Apple ha aggiunto una nuova API per la `UITraitCollection` classe e un'app tvOS deve registrarsi per supportare l'aspetto scuro (tramite un'impostazione nel relativo `Info.plist` file).

Per acconsentire esplicitamente al supporto dei temi chiaro e scuro, eseguire le operazioni seguenti:

1. In **Esplora soluzioni** fare doppio clic sul file `Info.plist` per aprirlo e modificarlo.
2. Selezionare il **origine** visualizzazione (dalla parte inferiore dell'editor).
3. Aggiungere una nuova chiave e chiamarlo `UIUserInterfaceStyle`: 

    [![](user-interface-styles-images/theme03.png "La chiave UIUserInterfaceStyle")](user-interface-styles-images/theme03.png#lightbox)
4. Lasciare il tipo impostato su `String` e immettere un valore di `Automatic`: 

    [![](user-interface-styles-images/theme04.png "Immettere automatico")](user-interface-styles-images/theme04.png#lightbox)
5. Salvare le modifiche apportate al file.

Esistono tre possibili valori per il `UIUserInterfaceStyle` chiave:

- **Luce** -forza dell'interfaccia utente dell'app tvOS utilizzare sempre il tema chiaro.
- **Scuro** -forza dell'interfaccia utente dell'app tvOS utilizzare sempre il tema scuro.
- **Automatico** -passa tra il tema chiaro e scuro in base alle preferenze dell'utente nelle impostazioni. Si tratta dell'impostazione preferita.

<a name="UIKit-Theme-Support" />

### <a name="uikit-theme-support"></a>Supporto dei temi UIKit

Se si utilizza un'app tvOS predefinita standard `UIView` , i controlli basati su automaticamente risponderà al tema dell'interfaccia utente senza alcun intervento da parte di sviluppatori.

Inoltre, `UILabel` e `UITextView` verrà modificato automaticamente il colore in base al tema dell'interfaccia utente di selezionare:

- Il testo sarà nero tema chiaro.
- Il testo sarà bianco nel tema scuro.

Se lo sviluppatore dovesse cambia il colore del testo manualmente (per il codice o uno Storyboard), sarà responsabile della gestione delle modifiche di colore in base al tema dell'interfaccia utente.

<a name="New-Blur-Effects" />

### <a name="new-blur-effects"></a>Nuovi effetti sfocatura

Per supportare i temi chiaro e scuro in un'app tvOS 10, Apple ha aggiunto due nuovi effetti sfocatura. Questi nuovi effetti regolerà automaticamente la sfocatura in base al tema dell'interfaccia utente che l'utente ha selezionato come indicato di seguito:

- `UIBlurEffectStyleRegular` -Viene utilizzata una luce sfocatura nel tema chiaro e scuro sfocatura nel tema scuro.
- `UIBlurEffectStyleProminent` -Usa una sfocatura molto chiaro il tema chiaro e una sfocatura molto scuro nel tema scuro.

<a name="Working-with-Trait-Collections" />

## <a name="working-with-trait-collections"></a>Utilizzo di raccolte di tratto

Il nuovo `UserInterfaceStyle` proprietà del `UITraitCollection` classe può essere utilizzato per ottenere il tema dell'interfaccia utente attualmente selezionato e sarà un `UIUserInterfaceStyle` enum di uno dei valori seguenti:

- **Luce** -tema dell'interfaccia utente di luce è selezionata.
- **Scuro** -tema dell'interfaccia utente scuro è selezionata.
- **Non specificato** -la vista non è stato visualizzato sullo schermo, pertanto il tema dell'interfaccia utente corrente è sconosciuto.

Inoltre, le raccolte di tratto presentano le caratteristiche seguenti in tvOS 10:
 
- Il proxy di aspetto può essere personalizzato in base il `UserInterfaceStyle` di un determinato `UITraitCollection` per modificare, ad esempio, immagini o colori basati sul tema di elementi.
- Un'app tvOS può gestire le modifiche di tratto raccolta eseguendo l'override di `TraitCollectionDidChange` metodo di un `UIView` o `UIViewController` classe.

> [!IMPORTANT]
> L'anteprima Xamarin.tvOS per tvOS 10 non supporta completamente `UIUserInterfaceStyle` per `UITraitCollection` ancora. Verrà aggiunto il supporto completo in una versione futura.




<a name="Customizing-Appearance-Based-on-Theme" />

### <a name="customizing-appearance-based-on-theme"></a>Personalizzazione dell'aspetto basato sul tema

Per gli elementi di interfaccia utente che supportano il proxy di aspetto, il loro aspetto può essere regolato in base al tema dell'interfaccia utente della propria raccolta tratto. In tal caso, per un determinato elemento dell'interfaccia utente, lo sviluppatore può specificare un colore per il tema chiaro e un altro per il tema scuro.

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> Purtroppo, non supporta completamente l'anteprima Xamarin.tvOS per tvOS 10 `UIUserInterfaceStyle` per `UITraitCollection`, pertanto questo tipo di personalizzazione non è ancora disponibile. Verrà aggiunto il supporto completo in una versione futura.

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="responding-to-theme-changes-directly"></a>Risponde direttamente alle modifiche al tema

Lo sviluppatore richiede maggiore controllo sull'aspetto di un elemento dell'interfaccia utente in base al tema dell'interfaccia utente selezionato, è possibile eseguire l'override di `TraitCollectionDidChange` metodo di un `UIView` o `UIViewController` classe.

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

In base alla struttura di un'app tvOS, talvolta potrebbe essere quando lo sviluppatore deve di sostituire la raccolta di tratti di un determinato elemento dell'interfaccia utente e di utilizzare sempre un tema dell'interfaccia utente specifico.

Questa operazione può essere eseguita utilizzando il `SetOverrideTraitCollection` metodo la `UIViewController` classe. Ad esempio:

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

Per ulteriori informazioni, vedere il [tratti](~/ios/user-interface/storyboards/unified-storyboards.md) e [tratti si esegue l'override](~/ios/user-interface/storyboards/unified-storyboards.md) sezioni del nostro [Introduzione agli storyboard unificata](~/ios/user-interface/storyboards/unified-storyboards.md) documentazione.

<a name="Trait-Collections-and-Storyboards" />

### <a name="trait-collections-and-storyboards"></a>Storyboard e le raccolte di tratto

In tvOS 10, Storyboard di un'app può essere impostato su rispondere alle raccolte di tratto e molti elementi dell'interfaccia utente possono essere informati chiaro e scuro di tema. L'anteprima corrente Xamarin.tvOS per tvOS 10 non supporta questa funzionalità nella finestra di progettazione dell'interfaccia ancora, pertanto lo Storyboard dovrà essere modificato in Generatore del Xcode di interfaccia come soluzione alternativa.

Per abilitare il supporto di tratto raccolta, eseguire le operazioni seguenti:

1. Fare doppio clic sul file Storyboard nel **Esplora** e selezionare **Apri con** > **Xcode interfaccia generatore**: 

    [![](user-interface-styles-images/theme05.png "Apri con Generatore interfaccia Xcode")](user-interface-styles-images/theme05.png#lightbox) 
2. Per abilitare il supporto di tratto raccolta, passare al **controllo File** e controllare il **utilizzare tratto variazioni** proprietà nel **interfaccia generatore documento** sezione: 

    [![](user-interface-styles-images/theme06.png "Abilitare il supporto di raccolta tratto")](user-interface-styles-images/theme06.png#lightbox)
3. Confermare la modifica per usare il tratto di variazioni: 

    [![](user-interface-styles-images/theme07.png "L'avviso tratto variazioni di utilizzo")](user-interface-styles-images/theme07.png#lightbox)
4. Salvare le modifiche al file di Storyboard.

Apple ha aggiunto le funzionalità seguenti quando si modifica tvOS storyboard in Generatore di interfaccia:

* Lo sviluppatore può specificare diverse varianti di elementi dell'interfaccia utente basati sul tema dell'interfaccia utente nel **controllo attributo**:
    
    * Diverse proprietà ora hanno un **+** accanto a essi che è possibile fare clic per aggiungere una versione specifica del tema dell'interfaccia utente: 

        [![](user-interface-styles-images/theme08.png "Aggiungere una versione specifica del tema dell'interfaccia utente")](user-interface-styles-images/theme08.png#lightbox) 
    
    * Lo sviluppatore può specificare una nuova proprietà o scegliere il **x** pulsante per rimuoverlo: 

        [![](user-interface-styles-images/theme09.png "Specificare una nuova proprietà o fare clic sul pulsante x per rimuoverlo")](user-interface-styles-images/theme09.png#lightbox)
* Lo sviluppatore può visualizzare l'anteprima di una progettazione dell'interfaccia utente con tema chiaro o scuro dall'interfaccia Builder:
    
    * Nella parte inferiore dell'area di progettazione consente allo sviluppatore di cambiare il tema dell'interfaccia utente corrente: 

        [![](user-interface-styles-images/theme10.png "Nella parte inferiore dell'area di progettazione")](user-interface-styles-images/theme10.png#lightbox)
        
    * Verrà visualizzato il nuovo tema nel generatore di interfaccia e verranno visualizzate le modifiche specifiche tratto raccolta: 

        [![](user-interface-styles-images/theme11.png "Il tema visualizzato in Generatore di interfaccia")](user-interface-styles-images/theme11.png#lightbox)

Inoltre, tvOS simulatore dispone ora di un tasto di scelta rapida per consentire allo sviluppatore di passare rapidamente tra vari temi chiaro e scuro durante il debug di un'app tvOS. Utilizzare il **comando-MAIUSC-D** sequenza per alternare chiaro e scuro di tastiera.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati la luce e temi scuro di interfaccia utente che Apple sia aggiunto al tvOS 10 e come implementarli in un'app Xamarin.tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Novità di tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
