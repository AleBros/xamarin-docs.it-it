---
title: Considerazioni sulla piattaforma a 32/64 bit
description: Questo documento descrive le varie considerazioni da tenere presenti quando si è interessati a architetture a 32 bit e a 64 bit per un'applicazione Novell. iOS o Novell. Mac.
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 5ba451de857444bc5b12b750ae479b62abdb75a3
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910749"
---
# <a name="3264-bit-platform-considerations"></a>Considerazioni sulla piattaforma a 32/64 bit

Anche se iOS e macOS hanno supportato in passato le app 32 e 64 bit, Apple ha gradualmente deprecato il supporto di 32 bit.

A partire da iOS 11, le app a 32 bit non vengono più avviate e [tutti gli invii all'App Store devono supportare 64 bit](https://developer.apple.com/news/?id=06282017b).

A partire dal 2018 gennaio, [le nuove app inviate a Mac App Store devono supportare 64 bit e le](https://developer.apple.com/news/?id=06282017a)app esistenti devono essere aggiornate entro il 2018 giugno.

Il API classica di Novell (`XamMac.dll` e `monotouch.dll`) supporta solo applicazioni a 32 bit. Tuttavia, le nuove applicazioni Novell. iOS e Novell. Mac usano la [API unificata](~/cross-platform/macios/unified/index.md) (`Xamarin.iOS` e `Xamarin.Mac`) per impostazione predefinita e possono quindi essere destinate a 32 e 64 bit, in base alle esigenze.

## <a name="ios"></a>iOS

<a name="enable-64" />

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Abilitazione di Build a 64 bit di app Novell. iOS

> [!WARNING]
> Questa sezione è inclusa per motivi cronologici e consente di spostare i progetti Novell. iOS meno recenti nel API unificata e supportare 64 bit. Per impostazione predefinita, tutti i nuovi progetti Novell. iOS utilizzeranno il API unificata e la destinazione 64 bit.

Per le applicazioni per dispositivi mobili Novell. iOS convertite nel API unificata, gli sviluppatori devono aggiornare manualmente le impostazioni di compilazione per la destinazione 64 bit:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

1. Nella **riquadro della soluzione**fare doppio clic sul progetto dell'app per aprire la finestra **Opzioni progetto** .
2. Selezionare **compilazione iOS**.
3. Per il simulatore iPhone, nell'elenco a discesa **architetture supportate** selezionare **x86\_64** o **I386 + x86\_64**:

   [![Impostazione delle architetture supportate su x86\_64 o I386 + x86\_64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. Per i dispositivi fisici, selezionare una delle combinazioni di **arm64** disponibili:

   [![Impostazione delle architetture supportate su una delle combinazioni ARM64](Images/Image02.png "Impostazione delle architetture supportate su una delle combinazioni ARM64")](Images/Image02-large.png#lightbox)

5. Fare clic su **OK**.
6. Eseguire una compilazione pulita.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto dell'app e selezionare **Proprietà**.
2. Selezionare **compilazione iOS**.
3. Per il simulatore iPhone, impostare le **architetture supportate** su **x86\_64** o **I386 + x86\_64**: 

   [![Impostazione delle architetture supportate su x86_64 o I386 + x86\_64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. Per i dispositivi fisici, selezionare una delle combinazioni di **arm64** disponibili:
    
   [![Impostazione delle architetture supportate su una delle combinazioni ARM64](Images/VS01.png "Impostazione delle architetture supportate su una delle combinazioni ARM64")](Images/VS01-large.png#lightbox)

5. Fare clic su Salva per salvare le modifiche.
6. Eseguire una compilazione pulita.

-----

ARMv7s è supportato solo dal processore A6 incluso in iPhone 5 (o versione successiva). Il codice ARMv7 è più veloce e di dimensioni inferiori rispetto a ARMv6, funziona solo con iPhone 3GS e versioni successive ed è richiesto da Apple quando la destinazione è iPad o una versione minima di iOS 5,0. ARMv6 funziona su tutti i dispositivi, ma non è più supportato dal compilatore fornito con Xcode 4,5 e versioni successive. 

ARM64 è necessario per supportare iOS 8 in iPhone 6 o altri dispositivi a 64 bit e sarà richiesto da Apple quando si inviano applicazioni nuove o aggiornate in uscita nell'App Store di iTunes.

Per una panoramica completa delle funzionalità di diversi dispositivi iOS, vedere il documento sulla [compatibilità](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html) dei dispositivi di Apple.

### <a name="64-bit-and-binary-size-increases"></a>aumento dimensioni a 64 bit e binarie

Durante la transizione di Apple da 32 a 64 bit, le app iOS dovranno essere eseguite sia su hardware 32 bit che su hardware a 64 bit. Per questo motivo, il API unificata di Novell consente agli sviluppatori di usare entrambe le destinazioni.

La definizione delle architetture a 32 bit e a 64 bit aumenterà significativamente le dimensioni di un'applicazione. Questa operazione consentirà tuttavia ai dispositivi più recenti di eseguire codice ottimizzato, supportando al tempo stesso i dispositivi meno recenti.

> [!IMPORTANT]
> Se viene visualizzato il messaggio seguente quando si invia un'applicazione iOS all'iTunes App Store _", avviso itms-9000: manca il supporto per 64 bit. A partire dal 1 ° febbraio 2015, le nuove app iOS caricate nell'App Store devono includere il supporto a 64 bit e possono essere compilate con iOS 8 SDK, incluso in Xcode 6 o versione successiva. Per abilitare 64 bit nel progetto, è consigliabile usare l'impostazione di compilazione Xcode predefinita "architetture standard" per creare un singolo file binario con codice sia a 32 bit che a 64 bit._ È necessario passare le architetture supportate a una delle combinazioni di **arm64** disponibili (come illustrato in precedenza), ricompilare e inviare nuovamente.

## <a name="mac"></a>Mac

> [!IMPORTANT]
> A partire dal 2018 gennaio, tutte le nuove app Mac inviate a Mac App Store devono supportare 64 bit. Le app Mac App Store esistenti e i relativi aggiornamenti devono supportare 64 bit a partire da giugno 2018. Vedere l' [annuncio di Apple](https://developer.apple.com/news/?id=06282017a) e [una guida che illustra come aggiornare le app Novell. Mac a 64 bit](~/cross-platform/macios/32-and-64/mac-64-bit.md).

La maggior parte dei moderni computer Mac supporta sia applicazioni a 32 bit che a 64 bit.   MacOS 10,6 (Snow Leopard) è stato l'ultimo sistema operativo per l'esecuzione su sistemi a 32 bit.   La maggior parte dei computer Mac rilasciati da 2010 supporta entrambi i sistemi.

A differenza di iOS, molti dei nuovi Framework introdotti nelle versioni recenti di macOS sono supportati solo in modalità a 64 bit (CloudKit, EventKit, GameController, LocalAuthentication, MediaLibrary, MultipeerConnectivity, NotificationCenter, GLKit, SpriteKit, social, e MapKit, tra gli altri.

Il API unificata consente agli sviluppatori di scegliere il tipo di applicazioni che si desidera produrre: 32 bit o 64 bit.

**le applicazioni a 32 bit** verranno eseguite su computer Mac a 32 e 64 bit, hanno uno spazio degli indirizzi limitato a 32 bit e richiedono che tutte le librerie siano di 32 bit.

Questa modalità viene in genere utilizzata se si dispone di dipendenze a 32 bit che non vengono eseguite in modalità a 64 bit, se si desidera disporre di un download più piccolo o se non vi sono vantaggi a scopo di prestazioni nel passaggio a 64 bit.

Questa modalità è limitata perché non è possibile usare molti Framework disponibili in macOS Mavericks e macOS Yosemite.

**le applicazioni a 64 bit** vengono eseguite solo su dispositivi Mac a 64 bit.

Per Mac, questa è la modalità di funzionamento preferita poiché la maggior parte dei Mac attualmente in uso supporta la modalità a 64 bit ed è possibile accedere al set completo di Framework fornito da Apple.

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Abilitazione di Build a 64 bit di app Novell. Mac

Per informazioni sulla compilazione di un'app a 64 bit con Novell. Mac, vedere la Guida [aggiornamento delle applicazioni unificate di Novell. Mac a 64 bit](~/cross-platform/macios/32-and-64/mac-64-bit.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Differenze rispetto a API unificata classiche](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
