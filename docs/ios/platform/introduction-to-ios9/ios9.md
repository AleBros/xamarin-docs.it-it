---
title: iOS 9 compatibilità
description: Anche se non si intende aggiungere immediatamente le funzionalità di iOS 9 all'app, è necessario ricompilare le tue App con la versione più recente di Xamarin.
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 6ade1c05c8e1cc64a4d24df1284d86175083ab80
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119882"
---
# <a name="ios-9-compatibility"></a>iOS 9 compatibilità

_Anche se non si intende aggiungere immediatamente le funzionalità di iOS 9 all'app, è necessario ricompilare le tue App con la versione più recente di Xamarin._

> [!IMPORTANT]
> Le informazioni in questa pagina sono per i clienti con le app già in Store le App destinate a iOS 8 o versioni precedenti, che non hanno già inviato gli aggiornamenti per la compatibilità di iOS 9. Se si usa già le versioni più recenti - Xcode 7 e 9 di xamarin. ios: per lo sviluppo di app, visitare il [Introduzione a iOS 9](~/ios/platform/introduction-to-ios9/index.md).

Quando è stata visualizzata le prima versioni di iOS 9 beta, è stato rilevato due problemi con le versioni precedenti di Xamarin che si manifesta come le applicazioni meno recenti sia in grado di avviare in iOS 9.

Questi due problemi (come [dettagliate nei nostri forum](http://forums.xamarin.com/discussion/comment/131529/#Comment_131529)) sono stati:

- Compilare App per iOS 8 o versioni precedenti non riesca a avviare su dispositivi a 32 bit (incluse le app compilate con la [API unificata](~/cross-platform/macios/unified/index.md)).
- P/Invoke ha esito negativo con il percorso completo non è specificato.

Aggiornamento di un'installazione di Xamarin per l'ultimo rilascio del canale Stable e quindi ricompilare e ridistribuire le app, consente di correggere questi due problemi.

_Anche se non si intende aggiornare immediatamente l'app con funzionalità di iOS 9, è consigliabile compilare nuovamente con la versione più recente di Xamarin e inviare di nuovo per l'App Store_.



Ciò garantisce che verrà eseguita l'app in iOS 9 dopo l'aggiornamento.
È possibile continuare a supporto di iOS 8: ricompilare con la versione più recente non influisce sulla versione di destinazione dell'applicazione.

Se si dispone di altri problemi durante il test delle App esistenti in iOS 9, vedere la [miglioramento della compatibilità](#compat) sezione riportata di seguito.


### <a name="updating-with-visual-studio"></a>L'aggiornamento con Visual Studio

È consigliabile in modo esplicito verificare che Visual Studio sia aggiornato alla versione stabile più recente.

## <a name="what-about-components-nugets-and-other-libraries"></a>Per quanto riguarda i componenti, pacchetti NuGet e altre librerie?

È effettuare **non** necessario attendere per nuove versioni di tutti i componenti o i pacchetti NuGet in uso per risolvere i problemi di due indicato in precedenza.
Risolto questi problemi semplicemente creando nuovamente l'app con la versione stabile più recente di xamarin. IOS.

Analogamente, i produttori di componenti e gli autori di Nuget sono **non** richiesto di inviare nuove build solo per risolvere due problemi menzionati in precedenza. Tuttavia, se presente un componente o Nuget Usa `UICollectionView` o viste da caricare **Xib** file, un aggiornamento *potrebbe* essere necessarie per risolvere i problemi di compatibilità di iOS 9 indicati di seguito.


<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>Miglioramento della compatibilità del codice

Ecco alcuni casi di codice i modelli che *utilizzato* a funzionare nelle versioni precedenti di iOS di rilievo in iOS 9. Ecco alcuni dei problemi possibili (e le relative soluzioni) che possono verificarsi durante il test in iOS 9:

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView è null nei costruttori

**Motivo:** In iOS 9 il `initWithFrame:` costruttore è ora obbligatorio, a causa di modifiche del comportamento in iOS 9, come le [stati documentazione UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Se una classe per l'identificatore specificato è stato registrato e deve essere creata una nuova cella, la cella a questo punto viene inizializzata chiamando il `initWithFrame:` (metodo).

**Correzione:** Aggiungi il `initWithFrame:` costruttore analogo al seguente:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Esempi correlati: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)



### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView non riesce a init con programmatore durante il caricamento di una vista da un file Xib/Nib

**Motivo:** il `initWithCoder:` costruttore è quella chiamata durante il caricamento di una vista da un file Xib di Interface Builder. Se questo costruttore non viene esportato il codice non gestito non è possibile chiamare la versione gestita di esso. In precedenza (ad es. in iOS 8) di `IntPtr` costruttore è stato richiamato per inizializzare visualizzazione.

**Correzione:** creazione ed esportazione di `initWithCoder:` costruttore analogo al seguente:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Esempio correlato: [avvia una Chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)


### <a name="dyld-message-no-cache-image-with-name"></a>Messaggio Dyld: Nessuna cache immagine con nome...

Potrebbe verificarsi un arresto anomalo del sistema con le informazioni seguenti nel registro:

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** si tratta di un bug nel linker nativo di Apple, cosa che accade quando effettuano una struttura di private pubblico (JavaScriptCore è stato reso pubblico in iOS 7, prima che fosse una struttura di private), e la distribuzione dell'app sia destinata per una versione iOS durante il sia privato al Framework. In questo caso si collegherà linker di Apple con la versione di framework anziché la versione pubblica privata.

**Correzione:** questo problema verrà risolto per iOS 9, ma non esiste una soluzione semplice è possibile applicare manualmente nel frattempo: semplicemente come destinazione una versione iOS successiva nel progetto (è possibile provare in questo caso iOS 7). Altri Framework possono presentare problemi simili, ad esempio il framework di WebKit è stato reso pubblico in iOS 8 e destinate a iOS 7 causerà questo errore, si deve avere come destinazione iOS 8 per usare WebKit nell'app.



## <a name="related-links"></a>Collegamenti correlati

- [informazioni sul rilascio compatibilità di iOS 9](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [Introduzione a iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [L'aggiornamento delle App xamarin. IOS per iOS9 (video)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
