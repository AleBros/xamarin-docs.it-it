---
title: iOS funzionalità di sicurezza e Privacy
description: Questo documento vengono illustrate funzionalità di sicurezza e privacy di iOS e viene descritto come utilizzarli con xamarin. IOS. Prende in considerazione gli aggiornamenti eseguiti in iOS 10 e come accedere ai dati personali dell'utente.
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: fd50e42536aabe16de3e44146d28950970dd801d
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784416"
---
# <a name="ios-security-and-privacy-features"></a>iOS funzionalità di sicurezza e Privacy

_In questo articolo viene descritto l'utilizzo con protezione e privacy in iOS e gli effetti di un'app xamarin. IOS._

Apple ha migliorato notevolmente sia sicurezza e privacy in iOS 10 (e versioni successive) che lo sviluppatore consente di migliorare la sicurezza delle proprie App e garantire la privacy dell'utente finale. Questo articolo vengono descritte l'implementazione di queste funzionalità in un'app xamarin. IOS.

Gli argomenti seguenti vengono descritti nel dettaglio:

- [Miglioramenti generali](#General-Enhancements)
- [Accesso ai dati utente privati](#Accessing-Private-User-Data)
    - [Impostazione delle chiavi sulla Privacy](#Setting-Privacy-Keys)
    
<a name="General-Enhancements" />

## <a name="general-enhancements"></a>Miglioramenti generali

Le seguenti modifiche generali apportate alla sicurezza e Privacy in iOS 10:

- Comuni dati sicurezza architettura (CDSA) API è obsoleta e deve essere sostituito con l'API SecKey per generare le chiavi asimmetriche.
- Il nuovo `NSAllowsArbitraryLoadsInWebContent` su cui è possibile aggiungere chiave di un'app `Info.plist` file e consentirà di pagine web caricare correttamente durante la protezione di sicurezza di trasporto di Apple (AT) è ancora abilitata per il resto dell'applicazione. Per ulteriori informazioni, vedere il nostro [la sicurezza del trasporto App](~/ios/app-fundamentals/ats.md) documentazione.
- Poiché negli Appunti di nuovo in iOS 10 e macOS Sierra consente all'utente di copiare e incollare dati tra i dispositivi, l'API è stato espanso per consentire un blocco per Appunti essere limitato a un dispositivo specifico e timestamp da cancellare automaticamente in un determinato punto. Inoltre, denominata pasteboards non sono più persistenti e deve essere sostituito con i contenitori tavolo di montaggio condivisi.
- Per tutte le connessioni SSL/TLS, la crittografia simmetrica RC4 è disabilitata per impostazione predefinita. Inoltre, l'API di trasporto protetto non supporta più crittografia SSLv3 e, è consigliabile che lo sviluppatore può arrestare l'uso della crittografia SHA-1 e 3DES appena possibile.

<a name="Accessing-Private-User-Data" />

## <a name="accessing-private-user-data"></a>Accesso ai dati utente privati

App in esecuzione su iOS 10 (o versioni successive) necessario dichiarare in modo statico l'intenzione di accedere a funzionalità specifiche o informazioni utente immettendo una o più chiavi di Privacy nella loro `Info.plist` file segnalare all'utente perché l'app desidera accedere.

> [!IMPORTANT]
> Le app che non forniscono le chiavi necessarie verranno automaticamente terminate dal sistema quando tentano di accedere a una delle funzionalità con restrizioni o informazioni utente, i _senza errori_! Se un'app viene avviata in modo imprevisto esito negativo in iOS 10, assicurarsi che tutti i obbligatori `Info.plist` sono stati specificati.

La Privacy seguente relative chiavi sono disponibili:

- **Privacy - descrizione dell'utilizzo di Apple musica** (`NSAppleMusicUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera accedere alla libreria di supporto dell'utente.
- **Privacy - descrizione dell'utilizzo delle periferiche Bluetooth** (`NSBluetoothPeripheralUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera accedere Bluetooth nel dispositivo dell'utente.
- **Privacy - descrizione dell'utilizzo di calendari** (`NSCalendarsUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera accedere del calendario dell'utente.
- **Privacy - descrizione dell'utilizzo della fotocamera** (`NSCameraUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera accedere la fotocamera del dispositivo.
- **Privacy - descrizione dell'utilizzo di contatti** (`NSContactsUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera accedere ai contatti dell'utente.
- **Privacy - descrizione dell'utilizzo di integrità condivisione** (`NSHealthShareUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera accedere ai dati di integrità dell'utente. Per ulteriori informazioni, vedere Apple [riferimento alla classe HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore).
- **Privacy - descrizione dell'utilizzo di Update integrità** (`NSHealthUpdateUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera modificare i dati di integrità dell'utente. Per ulteriori informazioni, vedere Apple [riferimento alla classe HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore).
- **Privacy - descrizione dell'utilizzo di HomeKit** (`NSHomeKitUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera accedere ai dati di configurazione HomeKit dell'utente.
- **Privacy - descrizione dell'utilizzo di percorso sempre** (`NSLocationAlwaysUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera abbiano sempre accesso alla posizione dell'utente.
- [Deprecato] **Privacy - descrizione dell'utilizzo di percorso** (`NSLocationUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera accedere al percorso dell'utente. *Nota: Questa chiave è stata deprecata in iOS 8 (e versioni successive). Utilizzare `NSLocationAlwaysUsageDescription` o `NSLocationWhenInUseUsageDescription` invece.*
- **Privacy - percorso quando In uso descrizione dell'utilizzo** (`NSLocationWhenInUseUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera accedere al percorso dell'utente in fase di esecuzione.
- [Deprecato] **Privacy - descrizione dell'utilizzo della libreria di supporto** -consente allo sviluppatore descrivere i motivi per cui l'app desidera accedere al catalogo multimediale dell'utente. *Nota: Questa chiave è stata deprecata in iOS 8 (e versioni successive). Utilizzare `NSAppleMusicUsageDescription` invece.*
- **Privacy - descrizione dell'utilizzo di microfono** (`NSMicrophoneUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app richiede accedere al microfono dispositivi.
- **Privacy - descrizione dell'utilizzo di movimento** (`NSMotionUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera accedere a accelerometro del dispositivo.
- **Privacy - descrizione dell'utilizzo di raccolta foto** (`NSPhotoLibraryUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera accedere a raccolta foto dell'utente.
- **Privacy - descrizione dell'utilizzo di promemoria** (`NSRemindersUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera accedere a promemoria dell'utente.
- **Privacy - descrizione dell'utilizzo di Siri** (`NSSiriUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera inviare i dati utente a Siri.
- **Privacy - descrizione dell'utilizzo di riconoscimento vocale** (`NSSpeechRecognitionUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera inviare i dati utente per i server di riconoscimento vocale Apple.
- **Privacy - descrizione dell'utilizzo di Provider TV** (`NSVideoSubscriberAccountUsageDescription`)-consente allo sviluppatore descrivere i motivi per cui l'app desidera accedere l'account dell'utente TV provider.

Per ulteriori informazioni sull'utilizzo di `Info.plist` chiavi, vedere Apple [informazioni di riferimento di chiave proprietà List](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1).

<a name="Setting-Privacy-Keys" />

## <a name="setting-privacy-keys"></a>Impostazione delle chiavi sulla Privacy

Nell'esempio seguente di accesso HomeKit in iOS 10 (e versioni successive), lo sviluppatore dovrà aggiungere la `NSHomeKitUsageDescription` chiave per l'app `Info.plist` file e fornire una stringa di dichiarazione perché l'applicazione desidera accedere a database HomeKit dell'utente. Questa stringa viene presentata il tempo utente la prima che esecuzione dell'app:

[![](security-privacy-images/info01.png "Un avviso di esempio NSHomeKitUsageDescription")](security-privacy-images/info01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Xamarin per Visual Studio corrente non supporta il miglioramento della protezione Modifica `Info.plist` impostazioni all'interno dell'IDE, pertanto sarà necessaria la soluzione alternativa:

1. Aprire il `Info.plist` file in un editor di testo esterno.
2. Prima dell'ultimo `</dict>` nodo, aggiungere il nodo seguente: `<key>NSHomeKitUsageDescription</key>`
3. Aggiungere il seguente nodo per fornire la descrizione necessaria: `<string>Allows the app to control HomeKit enabled devices.</string>`
4. Il `Info.plist` file dovrebbe essere simile al seguente: 

    [![](security-privacy-images/info02vs.png "Il file Info. plist dovrebbe essere simile al seguente")](security-privacy-images/info02vs.png#lightbox)
4. Salvare le modifiche apportate al file.
5. Tornare a Visual Studio e ricompilazione dell'applicazione.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per impostare una delle chiavi di Privacy, eseguire le operazioni seguenti:

1. Fare doppio clic su di `Info.plist` file nel **Esplora** per aprirlo e modificarlo.
2. Nella parte inferiore della schermata, passare il **origine** visualizzazione.
3. Aggiungere un nuovo **voce** all'elenco.
4. Nell'elenco a discesa, selezionare una chiave privata (ad esempio **Privacy - descrizione dell'utilizzo di HomeKit**): 

    [![](security-privacy-images/info02.png "Selezionare una chiave privata")](security-privacy-images/info02.png#lightbox)
5. Immettere una descrizione per il motivo per cui l'app desidera accedere alle informazioni di funzionalità o l'utente specificate: 

    [![](security-privacy-images/info03.png "Immettere una descrizione")](security-privacy-images/info03.png#lightbox)
6. Salvare le modifiche apportate al file.

-----

> [!IMPORTANT]
> Nell'esempio sopra, Impossibile impostare il `NSHomeKitUsageDescription` chiave nel `Info.plist` file comporterebbe l'app _invisibile all'utente hanno generato errori_ (viene chiuso dal sistema in fase di esecuzione) senza errori quando si esegue in iOS 10 (o versione successiva).

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è descritta la sicurezza e privacy modifiche che Apple in iOS 10 e gli effetti di un'app xamarin. IOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
