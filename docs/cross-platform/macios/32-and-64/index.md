---
title: Considerazioni sulle piattaforme a 32 o 64 bit
description: Questo documento vengono descritte varie considerazioni da tenere presenti quando la destinazione architetture a 32 bit e a 64 bit per un'applicazione xamarin. IOS o xamarin. Mac.
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 31eb0bfae58ecdca40548e46d1d9d95828be67b4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347839"
---
# <a name="3264-bit-platform-considerations"></a>Considerazioni sulle piattaforme a 32 o 64 bit

Se iOS e macOS sono supportate in passato App sia 32 e 64 bit, Apple ha gradualmente deprecata supporto a 32 bit.

A partire da iOS 11, non verrà avviata non è più App a 32 bit, e [tutti gli invii per l'App Store devono supportare 64-bit](https://developer.apple.com/news/?id=06282017b).

A partire da gennaio 2018 [nuove app inviate a Store di App Mac deve supportare 64-bit](https://developer.apple.com/news/?id=06282017a), e le app esistenti devono essere aggiornate da giugno 2018.

API classica di Xamarin (`XamMac.dll` e `monotouch.dll`) supportate solo le applicazioni a 32 bit. Tuttavia, usano le nuove applicazioni xamarin. IOS e xamarin. Mac la [API unificata](~/cross-platform/macios/unified/index.md) (`Xamarin.iOS` e `Xamarin.Mac`) per impostazione predefinita, e possono pertanto destinazione sia 32 e 64 bit, in base alle esigenze.

## <a name="ios"></a>iOS

<a name="enable-64" />

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Abilitazione a 64 bit le compilazioni di App xamarin. IOS

> [!WARNING]
> In questa sezione è inclusa per motivi cronologici e per spostare i progetti xamarin. IOS precedenti all'API unificata e supporto a 64 bit. Tutti i nuovi progetti xamarin. IOS userà l'API unificata e la destinazione a 64 bit per impostazione predefinita.

Per applicazioni per dispositivi mobili xamarin. IOS che sono state convertite per l'API unificata, gli sviluppatori devono aggiornare manualmente le impostazioni di compilazione alla destinazione a 64 bit:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Nel **riquadro della soluzione**, fare doppio clic sul progetto dell'app per aprire il **opzioni progetto** finestra.
2. Selezionare **compilazione iOS**.
3. Per il simulatore di iPhone nel **architetture supportate** elenco a discesa, selezionare **x86\_64** oppure **i386 + x86\_64**:

   [![L'impostazione di architetture supportate su x86\_64 o i386 + x86\_64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. Per i dispositivi fisici, selezionare uno dei contatori **ARM64** combinazioni:

   [![L'impostazione di architetture supportate su una delle combinazioni di ARM64](Images/Image02.png "architetture supporta l'impostazione per una delle combinazioni di ARM64")](Images/Image02-large.png#lightbox)

5. Fare clic su **OK**.
6. Eseguire una compilazione pulita.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Nel **Esplora soluzioni**, fare clic sul progetto dell'app e selezionare **proprietà**.
2. Selezionare **compilazione iOS**.
3. Per il simulatore di iPhone, impostare **architetture supportate** a una delle due **x86\_64** oppure **i386 + x86\_64**: 

   [![L'impostazione di architetture supportate su x86_64 o i386 + x86\_64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. Per i dispositivi fisici, selezionare uno dei contatori **ARM64** combinazioni:
    
   [![L'impostazione di architetture supportate su una delle combinazioni di ARM64](Images/VS01.png "architetture supporta l'impostazione per una delle combinazioni di ARM64")](Images/VS01-large.png#lightbox)

5. Salvare le modifiche.
6. Eseguire una compilazione pulita.

-----

ARMv7s è supportato solo dal processore A6 incluso in iPhone 5 (o versione successiva). ARMv7 codice risulta più veloce e minore di ARMv6 solo funziona con l'iPhone 3GS e versioni successive e viene richiesto da Apple quando la destinazione è una versione minima di iOS 5.0 o l'iPad. ARMv6 funziona in tutti i dispositivi, ma non è più supportato dal compilatore fornito con Xcode 4.5 e versioni successive. 

ARM64 è necessaria per supportare iOS 8 in iPhone 6 o altri dispositivi a 64 bit e verrà richiesto di Apple durante l'invio di nuove o ad aggiornamento uscita dalle applicazioni in iTunes App Store.

Per un quadro completo le funzionalità di vari tipi di dispositivi iOS, check-out di Apple [compatibilità del dispositivo](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html) documento.

### <a name="64-bit-and-binary-size-increases"></a>aumento delle dimensioni a 64 bit e binario

Durante la transizione di Apple da 32 a 64 bit, iOS App dovrà essere eseguite su hardware sia a 32 e 64 bit. Per questo motivo, API unificata di Xamarin consente agli sviluppatori di utilizzare entrambi.

Architetture sia a 32 e 64 bit aumentare notevolmente le dimensioni di un'applicazione. Tuttavia, ciò consente a dispositivi più recenti per l'esecuzione di codice ottimizzato ma continua a supportare i dispositivi meno recenti.

> [!IMPORTANT]
> Se si riceve il messaggio seguente durante l'invio di un'applicazione iOS a iTunes App Store, _"avviso ITMS-9000: Manca il supporto a 64 bit. A partire dall'1 febbraio 2015, nuova iOS caricata per l'App Store di App deve includere il supporto a 64 bit e venga compilata con iOS 8 SDK, incluso in Xcode 6 o versione successiva. Per consentire a 64 bit nel progetto, è consigliabile usare l'impostazione predefinita l'impostazione di "Architetture Standard" compilazione Xcode per creare un unico file binario con il codice sia a 32 e 64 bit. "_ È necessario passare le architetture supportate su uno dei contatori **ARM64** combinazione (come illustrato in precedenza), ricompilazione e inviare di nuovo.

## <a name="mac"></a>Mac

> [!IMPORTANT]
> A partire da gennaio 2018, tutte le nuove app Mac inviate a Store di App Mac deve supportare a 64 bit. Le app esistenti di Mac App Store e i relativi aggiornamenti devono supportare a 64 bit a partire da giugno 2018. Visualizzare [announcment di Apple](https://developer.apple.com/news/?id=06282017a) e [una Guida che descrive come aggiornare le app xamarin. Mac a 64 bit](~/cross-platform/macios/32-and-64/mac-64-bit.md).

I computer Mac più recenti supportano applicazioni a 32 bit sia 64 bit.   10.6 MacOS (Snow Leopardato) è stato l'ultimo sistema operativo per l'esecuzione su sistemi a 32 bit.   La maggior parte dei computer Mac rilasciati a partire da 2010 supportano entrambi i sistemi.

A differenza di iOS, molti dei nuovi Framework introdotte in versioni recenti di macOS sono supportate solo in modalità a 64 bit (CloudKit, EventKit, GameController, LocalAuthentication, MediaLibrary, MultipeerConnectivity, NotificationCenter, GLKit, SpriteKit, social network, e MapKit, tra gli altri).

L'API unificata consentono agli sviluppatori di scegliere quali applicazioni desiderano generare: 32 bit o 64 bit.

**applicazioni a 32 bit** verranno eseguiti su computer Mac sia a 32 e 64 bit, hanno uno spazio degli indirizzi limitato a 32 bit e richiedono che tutte le raccolte sono a 32 bit.

Si userà in genere questa modalità se sono presenti dipendenze da 32 bit che non vengono eseguite in modalità a 64 bit, se si desidera disporre di un download più piccolo, o se non sono presenti alcun vantaggio delle prestazioni nel passaggio a 64 bit.

Questa modalità è la limitazione che l'utente non potrà usare molti Framework disponibili in macOS Mavericks e macOS Yosemite.

**le applicazioni a 64 bit** verrà eseguito solo nei dispositivi Mac a 64 bit.

Per Mac, si tratta della modalità preferita di operazione come la maggior parte dei computer Mac in uso oggi supporta la modalità a 64 bit, e si ha accesso al set completo di appositi Framework forniti da Apple.

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Abilitazione a 64 bit le compilazioni di App xamarin. Mac

Per informazioni sulla compilazione di un'app a 64 bit usando xamarin. Mac, vedere la [unificata di xamarin. Mac l'aggiornamento di applicazioni a 64 bit](~/cross-platform/macios/32-and-64/mac-64-bit.md) Guida.

## <a name="related-links"></a>Collegamenti correlati

- [Visual Studio classico differenze API unificata](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
