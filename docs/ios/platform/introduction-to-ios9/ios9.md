---
title: Compatibilità di iOS 9
description: Anche se non si prevede di aggiungere le funzionalità di iOS 9 all'app immediatamente, è necessario ricompilare le app con la versione più recente di Novell.
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 246653cee7917141ddd0f911a7c4d1b21f945360
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70751969"
---
# <a name="ios-9-compatibility"></a>Compatibilità di iOS 9

_Anche se non si prevede di aggiungere le funzionalità di iOS 9 all'app immediatamente, è necessario ricompilare le app con la versione più recente di Novell._

> [!IMPORTANT]
> Le informazioni contenute in questa pagina sono destinate ai clienti con app già presenti nell'App Store destinate a iOS 8 o versioni precedenti, che non hanno già inviato aggiornamenti per la compatibilità con iOS 9. Se si usano già le versioni più recenti-Xcode 7 e Novell. iOS 9 per lo sviluppo di app, vedere l' [Introduzione a iOS 9](~/ios/platform/introduction-to-ios9/index.md).

Quando sono state apportate le prime versioni beta di iOS 9, sono stati identificati due problemi con le versioni precedenti di Novell che si manifestavano come app meno recenti che non possono essere avviate in iOS 9.

Questi due problemi (come [descritto nei forum](http://forums.xamarin.com/discussion/comment/131529/#Comment_131529)):

- Le app compilate per iOS 8 o versioni precedenti non possono essere avviate su dispositivi a 32 bit (incluse le app compilate con il [API unificata](~/cross-platform/macios/unified/index.md)).
- Non è stato specificato alcun errore P/Invoke con percorso completo.

Se si aggiorna l'installazione di Novell all'ultima versione stabile del canale e quindi si ricompilano e ridistribuiscono le app, questi due problemi vengono risolti.

_Anche se non si prevede di aggiornare immediatamente l'app con le funzionalità di iOS 9, è consigliabile ricompilare con la versione più recente di Novell ed eseguire di nuovo l'invio all'App Store_.

In questo modo l'app verrà eseguita in iOS 9 dopo l'aggiornamento dei clienti.
È possibile continuare a supportare iOS 8: la ricompilazione con la versione più recente non influisce sulla versione di destinazione dell'applicazione.

Se sono presenti altri problemi durante il test delle app esistenti in iOS 9, vedere la sezione [miglioramento della compatibilità](#compat) riportata di seguito.

### <a name="updating-with-visual-studio"></a>Aggiornamento con Visual Studio

Si consiglia di verificare in modo esplicito che Visual Studio sia aggiornato alla versione stabile più recente.

## <a name="what-about-components-nugets-and-other-libraries"></a>Quali componenti, NuGet e altre librerie?

**Non** è necessario attendere le nuove versioni di componenti o NuGet utilizzati per risolvere i due problemi menzionati in precedenza.
Questi problemi sono corretti semplicemente ricompilando l'app con la versione stabile più recente di Novell. iOS.

Analogamente, i fornitori di componenti e gli autori NuGet **non** devono inviare nuove compilazioni solo per correggere i due problemi menzionati in precedenza. Tuttavia, se un componente o NuGet USA `UICollectionView` o carica visualizzazioni da file **XIB** , *potrebbe* essere necessario un aggiornamento per risolvere i problemi di compatibilità con iOS 9 indicati di seguito.

<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>Miglioramento della compatibilità nel codice

Ci sono alcuni casi di modelli di codice *usati* per lavorare nelle versioni precedenti di iOS che si infrangono in iOS 9. Di seguito sono riportati alcuni possibili problemi (e le relative soluzioni) che possono verificarsi durante i test in iOS 9:

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell. ContentView è null nei costruttori

**Motivo** In iOS 9 il `initWithFrame:` costruttore è ora necessario, a causa delle modifiche del comportamento in iOS 9 come [Stati della documentazione UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Se è stata registrata una classe per l'identificatore specificato ed è necessario creare una nuova cella, la cella viene ora inizializzata chiamando `initWithFrame:` il relativo metodo.

**Difficoltà** Aggiungere il `initWithFrame:` costruttore come segue:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Esempi correlati: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView non riesce a inizializzare con coder durante il caricamento di una visualizzazione da un XIB/pennino

**Motivo** Il `initWithCoder:` costruttore è quello chiamato durante il caricamento di una visualizzazione da un file di Interface Builder XIB. Se questo costruttore non viene esportato, il codice non gestito non può chiamare la versione gestita. In precedenza (ad esempio in iOS 8) il `IntPtr` costruttore è stato richiamato per inizializzare la visualizzazione.

**Difficoltà** Creare ed esportare il `initWithCoder:` costruttore come segue:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Esempio correlato: [Chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

### <a name="dyld-message-no-cache-image-with-name"></a>Messaggio dyld: nessuna immagine della cache con nome...

È possibile che si verifichi un arresto anomalo con le informazioni seguenti nel log:

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo** Si tratta di un bug nel linker nativo di Apple, che si verifica quando rende pubblico un Framework privato (JavaScriptCore è stato reso pubblico in iOS 7, prima che fosse un Framework privato) e la destinazione di distribuzione dell'app è per una versione di iOS quando il Framework era privato. In questo caso il linker di Apple verrà collegato con la versione privata del Framework invece che con la versione pubblica.

**Difficoltà** Questo problema verrà risolto per iOS 9, ma esiste una semplice soluzione alternativa che è possibile applicare nel frattempo: è sufficiente fare riferimento a una versione successiva di iOS nel progetto (in questo caso, è possibile provare iOS 7). Altri Framework possono presentare problemi simili. ad esempio, il Framework WebKit è stato reso pubblico in iOS 8 (e pertanto la destinazione di iOS 7 comporterà l'errore. è consigliabile usare iOS 8 per usare WebKit nell'app).

## <a name="related-links"></a>Collegamenti correlati

- [informazioni sulla versione per la compatibilità con iOS 9](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [Introduzione a iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [Aggiornamento delle app Novell. iOS in iOS9 (video)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
