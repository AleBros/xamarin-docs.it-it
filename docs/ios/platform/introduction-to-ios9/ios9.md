---
title: iOS 9 compatibilità
description: Anche se non si intende aggiungere le funzionalità iOS 9 immediatamente all'app, è necessario ricompilare le app con la versione più recente di Xamarin.
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b7cbec3f064e6000f991fb0f9ce256415f6ce5dd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30777551"
---
# <a name="ios-9-compatibility"></a>iOS 9 compatibilità

_Anche se non si intende aggiungere le funzionalità iOS 9 immediatamente all'app, è necessario ricompilare le app con la versione più recente di Xamarin._

> [!IMPORTANT]
> Le informazioni in questa pagina sono riservata ai clienti con le app già nell'archivio di App per iOS 8 o versioni precedenti, che non sono già state inviate gli aggiornamenti per la compatibilità di iOS 9. Se si usa già le versioni più recenti - 7 Xcode e xamarin. IOS 9 - per lo sviluppo delle app, visitare il [Introduzione a iOS 9](~/ios/platform/introduction-to-ios9/index.md).

Quando è presente il primo versioni beta di iOS 9, identificate due problemi con le versioni precedenti di Xamarin che si manifesta come applicazioni meno recenti in grado di avviare in iOS 9.

Questi due problemi (come [dettagliate nei forum](http://forums.xamarin.com/discussion/comment/131529/#Comment_131529)) sono stati:

- Compilazione di App per iOS 8 o versioni precedenti non è in grado di avviare in periferiche a 32 bit (inclusi App compilate con il [Unified API](~/cross-platform/macios/unified/index.md)).
- P/Invoke non riesce e il percorso completo non è specificato.

Aggiornamento dell'installazione di Xamarin per l'ultima versione stabile canale e quindi ricompilare e ridistribuire le app, consente di correggere questi due problemi.

_Anche se non si intende aggiornare immediatamente l'app con le funzionalità di iOS 9, è consigliabile compilare nuovamente con la versione più recente di Xamarin e inviare di nuovo all'App Store_.



Ciò garantisce che l'app viene eseguita in iOS 9 dopo l'aggiornamento.
È possibile continuare a supportare iOS 8 - ricompilazione con la versione più recente non influisce sulla versione di destinazione dell'applicazione.

Se si dispone di ulteriori problemi durante il test di App in iOS 9, leggere la [miglioramento compatibilità](#compat) sezione riportata di seguito.


### <a name="updating-with-visual-studio"></a>L'aggiornamento con Visual Studio

È consigliabile eseguire in modo esplicito il controllo che Visual Studio viene aggiornato all'ultima versione stabile.

## <a name="what-about-components-nugets-and-other-libraries"></a>Per quanto riguarda i componenti, Nugets e altre librerie?

Si **non** necessario attendere nuove versioni di qualsiasi componente o Nugets utilizzate per risolvere i problemi di due indicati in precedenza.
Questi problemi sono risolti semplicemente ricompilare l'app con l'ultima versione stabile di xamarin. IOS.

Analogamente, gli autori di Nuget e fornitori di componenti sono **non** richiesto di inviare nuove compilazioni solo per risolvere i problemi di due indicati in precedenza. Tuttavia, eventuali utilizza un componente o Nuget `UICollectionView` o viste da caricare **XI** file, un aggiornamento *potrebbe* necessario per risolvere i problemi di compatibilità di iOS 9 riportati di seguito.


<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>Miglioramento della compatibilità del codice

Ecco alcuni casi di codice i modelli che *utilizzato* funzionamento nelle versioni precedenti di iOS in iOS 9 di rilievo. Ecco alcuni possibili problemi (e le relative soluzioni) che possono verificarsi durante il test in iOS 9:

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView è null nei costruttori

**Motivo:** In iOS 9 di `initWithFrame:` costruttore è obbligatorio, a causa di modifiche del comportamento in iOS 9 come il [stati documentazione UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Se è necessario creare una nuova cella di una classe per l'identificatore specificato è stato registrato, la cella a questo punto viene inizializzata chiamando il relativo `initWithFrame:` metodo.

**Correzione:** Aggiungi il `initWithFrame:` costruttore analogo al seguente:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Esempi correlati: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)



### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView non riesce a init con codificatore durante il caricamento di una vista da un XI/Nib

**Motivo:** il `initWithCoder:` costruttore è quella chiamato durante il caricamento di una vista da un file di interfaccia generatore XI. Se questo costruttore non viene esportato il codice non gestito non è possibile chiamare la versione gestita. In precedenza (ad es. in iOS 8) di `IntPtr` costruttore è stato richiamato per inizializzare una vista.

**Correzione:** creazione ed esportazione di `initWithCoder:` costruttore analogo al seguente:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Esempio correlato: [Chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)


### <a name="dyld-message-no-cache-image-with-name"></a>Messaggio Dyld: Nessuna cache immagine con nome...

Potrebbe verificarsi un arresto anomalo del sistema con le seguenti informazioni nel log:

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** si tratta di un bug nel linker Apple nativo, si verifica quando vengono rendere pubblico un framework privato (JavaScriptCore è stato reso pubblico in iOS 7, prima che sia stato di un framework privato), e la destinazione di distribuzione dell'app è una versione di iOS quando il Framework è privata. In questo caso il linker Apple verrà collegato con la versione di framework anziché la versione pubblica privata.

**Correzione:** questo verrà risolto per iOS 9, ma non esiste una soluzione semplice, è possibile applicare manualmente nel frattempo: destinati solo a una versione iOS successiva nel progetto (è possibile provare in questo caso iOS 7). Altri Framework possono presentare problemi simili, ad esempio il framework WebKit è stato reso pubblico in iOS 8 (e destinato a iOS 7 causerà questo errore; necessario scegliere come destinazione iOS 8 utilizzare WebKit nell'app).



## <a name="related-links"></a>Collegamenti correlati

- [informazioni sulla versione di compatibilità iOS 9](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [Introduzione a iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [Aggiornare le app xamarin a iOS9 (video)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
