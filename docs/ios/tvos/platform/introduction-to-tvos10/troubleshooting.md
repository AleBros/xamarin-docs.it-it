---
title: Risoluzione dei problemi relativi a tvOS 10 le app compilate con Xamarin
description: Questo articolo fornisce alcuni suggerimenti sulla risoluzione dei problemi per l'uso di tvOS 10 nelle App Xamarin. Descrive problemi riferiti alla Store l'App, la compatibilità binaria, CFNetwork HttpProtocol, CloudKit, immagine di base, NSUserActivity e UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3815790cfb73f93f399c14d3da44aa3210725388
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60932438"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>Risoluzione dei problemi relativi a tvOS 10 le app compilate con Xamarin

Le sezioni seguenti elencano alcuni problemi noti che possono verificarsi quando si usa tvOS 10 con Xamarin e la soluzione per risolvere tali problemi:

- [App Store](#App-Store)
- [Compatibilità binaria](#Binary-Compatibility)
- [Protocollo HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Immagine di base](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problemi noti:

 - Quando acquisti In-App di test nell'ambiente sandbox, la finestra di dialogo di autenticazione vengano visualizzati due volte.
 - Durante il test di acquisti In-App con il contenuto ospitato nell'ambiente sandbox, verrà visualizzata la finestra di dialogo password ogni volta che l'app viene portato in primo piano fino a quando non viene completato il download del contenuto.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilità binaria

Problemi noti:

 - La chiamata `NSObject.ValueForKey` sarà un `null` chiave verrà generata un'eccezione.
 - Riferimento a un tipo di carattere in base al nome quando si chiama `UIFont.WithName` causerà un arresto anomalo del sistema.
 - Entrambe `NSURLSession` NSURLConnection e` no longer RC4 cipher suites during the TLS handshake for `http://' URL.
 - Le app possono bloccarsi se modificano la geometria della superview una in entrambi i `ViewWillLayoutSubviews` o `LayoutSubviews` metodi.
 - Per tutte le connessioni SSL/TLS, la crittografia simmetrica RC4 è disabilitata per impostazione predefinita. Inoltre, l'API di trasporto sicuro non supporta più SSLv3 ed è consigliabile che l'app interrompere l'uso della crittografia SHA-1 e 3DES appena possibile.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocollo HTTP CFNetwork

Il `HTTPBodyStream` proprietà del `NSMutableURLRequest` classe deve essere impostata su un flusso aperto dopo `NSURLConnection` e `NSURLSession` ora applicherà in modo rigoroso questo requisito.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Operazioni a esecuzione prolungata restituirà un _"Non si è autorizzati a salvare il file."_ Errore.

<a name="CoreImage" />

## <a name="core-image"></a>Immagine di base

Il `CIImageProcessor` API supporta ora un numero di immagini input arbitrario. `CIImageProcessor` API che è stato incluso nella versione beta di tvOS 10 1 verrà rimossa.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Dopo un'operazione di consegna, il `UserInfo` proprietà di un `NSUserActivity` oggetto potrebbe essere vuoto. Chiamare in modo esplicito `BecomeCurrent` NSUserActivity' oggetto come soluzione alternativa corrente.

<a name="UIKit" />

## <a name="uikit"></a>UIKit

Problemi noti:

 - Diventa l'aspetto dello sfondo `UINavigationBar`, `UITabBar` o `UIToolBar` può comportare un passaggio di layout per risolvere il nuovo aspetto. Non è possibile modificare questi aspetti visivi all'interno di un `LayoutSubviews`, `UpdateConstraints`, `WillLayoutSubviews` o `DidUpdateSubviews` eventi possono causare un ciclo infinito di layout.
 - In tvOS 10, la chiamata di `RemoveGestureRecognizer` metodo di un `UIView` oggetto Annulla in modo esplicito qualsiasi riconoscitore di movimento in corso.
 - Presentato i controller di visualizzazione possono ora modificare l'aspetto della barra di stato.
 - tvOS 10 richiede allo sviluppatore di chiamare `base.AwakeFromNib` quando si crea una sottoclasse `UIViewController` ed eseguire l'override di `AwakeFromNib` (metodo).
 - Le app con personalizzato `UIView` sottoclassi che eseguono l'override `LayoutSubviews` e il layout prima di chiamare dirty `base.LayoutSubviews` può attivare un ciclo infinito di layout in tvOS 10.
 - Gli asset immagini specifiche direzione o flippable non sono alcun capovolgimento quando assegnato a `UIButton` oggetti.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Che cos'è una novità a tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
