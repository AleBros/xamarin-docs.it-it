---
title: Considerazioni relative alla piattaforma a 32/64 bit
description: Considerazioni sul supporto di architetture a 32 e 64 bit per l'applicazione
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
ms.technology: xamarin-cross-platform
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 06e65b8a639345af65c6e5d3bbe57d8bb3feffa1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="3264-bit-platform-considerations"></a>Considerazioni relative alla piattaforma a 32/64 bit

Anche se iOS e Mac OS sono sempre supportate App sia 32 e 64 bit, Apple è deprecato gradualmente supporto a 32 bit.

A partire da iOS 11, non verranno avviato non è più applicazioni a 32 bit, e [tutte le comunicazioni all'App Store devono supportare 64-bit](https://developer.apple.com/news/?id=06282017b).

A partire da gennaio 2018 [nuove app presentata per Mac App Store deve supportare 64-bit](https://developer.apple.com/news/?id=06282017a), e App di esistenti devono essere aggiornate da giugno 2018.

API classica di Xamarin (`XamMac.dll` e `monotouch.dll`) supportate sole applicazioni a 32 bit. Tuttavia, le nuove applicazioni di xamarin. IOS e Xamarin.Mac il [Unified API](~/cross-platform/macios/unified/index.md) (`Xamarin.iOS` e `Xamarin.Mac`) per impostazione predefinita, e possono pertanto di destinazione sia 32 e 64 bit, in base alle esigenze.

## <a name="ios"></a>iOS

<a name="enable-64" />

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Abilitazione a 64 bit build di App xamarin

> [!WARNING]
> In questa sezione è inclusa per motivi cronologici e per spostare i progetti più vecchi di xamarin. IOS per l'API unificata e supporto a 64 bit. Tutti i nuovi progetti di xamarin. IOS utilizzerà l'API unificata e la destinazione a 64 bit per impostazione predefinita.

Per xamarin applicazioni per dispositivi mobili che sono state convertite in Unified API, gli sviluppatori devono aggiornare manualmente le impostazioni di compilazione alla destinazione a 64 bit:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Nel **soluzione riempimento**, fare doppio clic sul progetto dell'app per aprire la **opzioni progetto** finestra.
2. Selezionare **compilazione iOS**.
3. Per il simulatore di iPhone nel **architetture supportate** elenco a discesa selezionare **x86\_64** o **i386 + x86\_64**:

   [![L'impostazione di architetture supportate su x86\_64 o i386 + x86\_64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. Per i dispositivi fisici, selezionare una delle **ARM64** combinazioni:

   [![L'impostazione di architetture supportate su una delle combinazioni ARM64](Images/Image02.png "architetture impostazione supportata a una delle combinazioni ARM64")](Images/Image02-large.png#lightbox)

5. Fare clic su **OK**.
6. Eseguire una compilazione pulita.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Nel **Esplora**, fare clic sul progetto dell'app e selezionare **proprietà**.
2. Selezionare **compilazione iOS**.
3. Per il simulatore di iPhone, impostare **architetture supportate** al **x86\_64** o **i386 + x86\_64**: 

   [![L'impostazione di architetture supportate su x86_64 o i386 + x86\_64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. Per i dispositivi fisici, selezionare una delle **ARM64** combinazioni:
    
   [![L'impostazione di architetture supportate su una delle combinazioni ARM64](Images/VS01.png "architetture impostazione supportata a una delle combinazioni ARM64")](Images/VS01-large.png#lightbox)

5. Salvare le modifiche.
6. Eseguire una compilazione pulita.

-----

ARMv7s è supportato solo dal processore A6 incluso sull'iPhone 5 (o versione successiva). Codice ARMv7 è più veloce e minore di ARMv6, solo funziona con l'iPhone 3GS e versioni successive e quando è necessario Apple destinazione iPad o una versione minima di iOS 5.0. ARMv6 funziona su tutti i dispositivi, ma non è più supportato dal compilatore fornito con Xcode 4.5 e versioni successive. 

ARM64 è necessaria per supportare iOS 8 in iPhone 6 o altri dispositivi a 64 bit e verranno richiesti da Apple per l'invio di nuove o ad aggiornamento uscita dalle applicazioni in iTunes App Store.

Per un'analisi completa le funzionalità di vari dispositivi iOS, consultare Apple [compatibilità dei dispositivi](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html) documento.

### <a name="64-bit-and-binary-size-increases"></a>aumento delle dimensioni di 64 bit e binario

Durante la transizione di Apple da 32 a 64 bit, iOS App dovrà eseguire su hardware sia a 32 bit e a 64 bit. Per questo motivo, Unified API di Xamarin consente agli sviluppatori di entrambi come destinazione.

Architetture sia a 32 e 64 bit aumenterà in modo significativo le dimensioni di un'applicazione. Tuttavia, ciò pertanto consente dispositivi più recenti per l'esecuzione di codice ottimizzato supportando comunque dispositivi meno recenti.

> [!IMPORTANT]
> Se si riceve il messaggio seguente durante l'invio di un'applicazione iOS in iTunes App Store, _"avviso ITMS-9000: manca il supporto a 64 bit. Avvio iOS nuovo 1 febbraio 2015, caricata in App Store di App deve includere il supporto a 64 bit e venga compilata con iOS 8 SDK, incluso in Xcode 6 o versione successiva. Per consentire a 64 bit nel progetto, è consigliabile utilizzare il valore predefinito di compilazione Xcode impostazione architetture"Standard" per compilare un singolo binario con il codice sia a 32 bit e a 64 bit. "_ È necessario passare una delle architetture supportate **ARM64** combinazione (come illustrato in precedenza), ricompilazione e inviare di nuovo.

## <a name="mac"></a>Mac

> [!IMPORTANT]
> A partire da gennaio 2018, tutte le app Mac nuovo inviate al Mac App Store devono supportare a 64 bit. App di esistenti Mac App Store e i relativi aggiornamenti devono supportare a 64 bit a partire da giugno 2018. Vedere [announcment Apple](https://developer.apple.com/news/?id=06282017a) e [una Guida che descrive come aggiornare le app Xamarin.Mac a 64 bit](~/cross-platform/macios/32-and-64/mac-64-bit.md).

I computer Mac più recenti supportano applicazioni sia a 32 bit e a 64 bit.   MacOS 10.6 (neve Leopard) è l'ultimo sistema operativo per l'esecuzione su sistemi a 32 bit.   La maggior parte dei computer Mac rilasciati dopo 2010 supporta entrambi i sistemi.

A differenza di iOS, molti dei nuovi Framework introdotte nelle versioni recenti di macOS sono supportati solo in modalità a 64 bit (CloudKit, EventKit, GameController, LocalAuthentication, MediaLibrary, MultipeerConnectivity, NotificationCenter, GLKit, SpriteKit, sociale, e MapKit, tra gli altri).

L'API unificata consentono agli sviluppatori di scegliere il tipo di applicazioni per produrre: 32 bit o 64 bit.

**applicazioni a 32 bit** verrà eseguito nel computer Mac sia a 32 bit e a 64 bit, sono limitato a 32 bit di uno spazio di indirizzi e richiedono che tutte le raccolte sono a 32 bit.

Si utilizzerà in genere questa modalità se si dispone di dipendenze di 32 bit che non vengono eseguiti in modalità a 64 bit, se si desidera disporre di un download più piccolo, o se non esistono alcun prestazioni lo spostamento a 64 bit.

Questa modalità è la limitazione che l'utente non potrà utilizzare molti Framework disponibile in Mavericks macOS e macOS Yosemite.

**applicazioni a 64 bit** verrà eseguito solo nei dispositivi Mac a 64 bit.

Per Mac, è la modalità preferita dell'operazione come oggi la maggior parte dei computer Mac in uso supporta la modalità a 64 bit, e si ha accesso al set completo di Framework fornite da Apple.

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Abilitazione a 64 bit build di App Xamarin.Mac

Per informazioni sulla compilazione di un'applicazione a 64 bit utilizzando Xamarin.Mac, vedere il [aggiornamento Xamarin.Mac unificata applicazioni a 64 bit](~/cross-platform/macios/32-and-64/mac-64-bit.md) Guida.

## <a name="related-links"></a>Collegamenti correlati

- [Differenze tra API unificata di vs classico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
