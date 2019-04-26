---
title: iOS funzionalità di sicurezza e Privacy
description: Questo documento vengono descritte funzionalità di sicurezza e privacy di iOS e illustra come usarli con xamarin. IOS. Prende in considerazione aggiornamenti apportati in iOS 10 e come accedere ai dati personali dell'utente.
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 119fd478727a002622861c94462b93b75720a992
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400583"
---
# <a name="ios-security-and-privacy-features"></a>iOS funzionalità di sicurezza e Privacy

_Questo articolo descrive l'uso con la sicurezza e privacy in iOS e gli effetti di un'app xamarin. IOS._

Apple ha introdotto numerosi miglioramenti al sia sicurezza e privacy in iOS 10 (e versioni successive) che consentono allo sviluppatore di migliorare la sicurezza delle proprie App e garantire la privacy dell'utente finale. Questo articolo illustra l'implementazione di queste funzionalità in un'app xamarin. IOS.
    
<a name="General-Enhancements" />

## <a name="general-enhancements"></a>Miglioramenti generali

Le seguenti modifiche generali sono state apportate alla sicurezza e Privacy in iOS 10:

- Common Data Security Architecture (CDSA) API è obsoleta e deve essere sostituito con l'API SecKey per generare le chiavi asimmetriche.
- Il nuovo `NSAllowsArbitraryLoadsInWebContent` chiave può essere aggiunti a di un'app **Info. plist** file e consentirà alle pagine web di caricare correttamente mentre protezione Apple Transport Security (ATS) è ancora abilitata per il resto dell'app. Per altre informazioni, vedere la [App Transport Security](~/ios/app-fundamentals/ats.md) documentazione.
- Poiché nuovi negli Appunti in iOS 10 e macOS Sierra consente all'utente di copia e incolla tra i dispositivi, l'API è stata estesa per consentire gli Appunti essere limitata a un dispositivo specifico e che timestamp da cancellare automaticamente in un determinato punto. Inoltre, pasteboards denominati non sono più persistenti e devono essere sostituiti con i contenitori tavolo di montaggio condivisi.
- Per tutte le connessioni SSL/TLS, la crittografia simmetrica RC4 è disabilitata per impostazione predefinita. Inoltre, l'API di trasporto sicuro non supporta più SSLv3 ed è consigliabile che lo sviluppatore di interrompere l'uso della crittografia SHA-1 e 3DES appena possibile.

<a name="Accessing-Private-User-Data" />

## <a name="accessing-private-user-data"></a>Accesso ai dati utente privati

Le App in esecuzione in iOS 10 (o versione successiva) devono dichiarare in modo statico manifestato l'intenzione di accedere a funzionalità specifiche o informazioni utente immettendo una o più chiavi di Privacy nelle loro **Info. plist** i file in cui viene illustrato all'utente perché l'app desidera ottenere accesso.

> [!IMPORTANT]
> Le app che non riescono a fornire le chiavi necessarie verranno automaticamente terminate dal sistema quando tentano di accedere a una delle funzionalità con restrizioni o informazioni utente, _senza errori_! Se un'app inizia a generare errori in modo imprevisto in iOS 10, assicurarsi che tutti i necessari **Info. plist** sono stati specificati.

La Privacy seguente relative chiavi sono disponibili:

- **Privacy - descrizione utilizzo di Apple Music** (`NSAppleMusicUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui vuole che l'app per accedere alla libreria di supporto dell'utente.
- **Privacy - descrizione Utilizzo periferica Bluetooth** (`NSBluetoothPeripheralUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app deve accedere a Bluetooth nel dispositivo dell'utente.
- **Privacy - descrizione utilizzo calendari** (`NSCalendarsUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app desidera accedere al calendario dell'utente.
- **Privacy - descrizione utilizzo fotocamera** (`NSCameraUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app desidera accedere la fotocamera del dispositivo.
- **Privacy - descrizione utilizzo contatti** (`NSContactsUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app desidera accedere ai contatti dell'utente.
- **Privacy - descrizione utilizzo dati salute** (`NSHealthShareUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app desidera accedere ai dati di integrità dell'utente. Per altre informazioni, vedere di Apple [riferimento alla classe HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore).
- **Privacy - descrizione utilizzo aggiornamento informazioni salute** (`NSHealthUpdateUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app desidera modificare i dati di integrità dell'utente. Per altre informazioni, vedere di Apple [riferimento alla classe HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore).
- **Privacy - descrizione utilizzo HomeKit** (`NSHomeKitUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app desidera accedere ai dati di configurazione dell'utente HomeKit.
- **Privacy - descrizione utilizzo di percorso sempre** (`NSLocationAlwaysUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app desidera abbiano sempre accesso alla posizione dell'utente.
- [Deprecato] **Privacy - descrizione utilizzo posizione** (`NSLocationUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app desidera accedere al percorso utente. *NOTA: Questa chiave è stata deprecata in iOS 8 (e versioni successive). Uso `NSLocationAlwaysUsageDescription` o `NSLocationWhenInUseUsageDescription` invece.*
- **Privacy - quando In uso descrizione utilizzo posizione** (`NSLocationWhenInUseUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app desidera accedere al percorso dell'utente durante l'esecuzione.
- [Deprecato] **Privacy - descrizione utilizzo libreria multimediale** -consente agli sviluppatori di descrivere il motivo per cui l'app desidera accedere al catalogo multimediale dell'utente. *NOTA: Questa chiave è stata deprecata in iOS 8 (e versioni successive). Usare `NSAppleMusicUsageDescription` invece.*
- **Privacy - descrizione utilizzo microfono** (`NSMicrophoneUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app desidera accedere al microfono di dispositivi.
- **Privacy - descrizione utilizzo movimento** (`NSMotionUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app desidera accedere a accelerometro del dispositivo.
- **Privacy - descrizione utilizzo raccolta foto** (`NSPhotoLibraryUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui vuole che l'app per accedere alla libreria di foto dell'utente.
- **Privacy - descrizione utilizzo promemoria** (`NSRemindersUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app desidera accedere a promemoria dell'utente.
- **Privacy - descrizione utilizzo Siri** (`NSSiriUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app desidera inviare i dati dell'utente a Siri.
- **Privacy - descrizione utilizzo di riconoscimento vocale** (`NSSpeechRecognitionUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app desidera inviare i dati utente nei server di riconoscimento vocale di Apple.
- **Privacy - descrizione utilizzo Provider TV** (`NSVideoSubscriberAccountUsageDescription`)-consente agli sviluppatori di descrivere il motivo per cui l'app desidera accedere l'account dell'utente TV provider.

Per altre informazioni sull'uso dei **Info. plist** le chiavi, vedere di Apple [informazioni di riferimento sulle chiavi elenco proprietà](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1).

<a name="Setting-Privacy-Keys" />

## <a name="setting-privacy-keys"></a>Impostazione delle chiavi di Privacy

Eseguire l'esempio seguente di accesso a HomeKit in iOS 10 (e versioni successive), lo sviluppatore avrà bisogno di aggiungere il `NSHomeKitUsageDescription` chiave per l'app **Info. plist** file e fornire una dichiarazione di stringa perché l'app desidera accedere a HomeKit dell'utente database. Questa stringa verrà visualizzata il tempo utente la prima che esecuzione dell'app:

![Un avviso di esempio NSHomeKitUsageDescription](security-privacy-images/info01.png "un avviso NSHomeKitUsageDescription di esempio")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Xamarin. IOS per Visual Studio attualmente non supporta la modifica di **Info. plist** chiavi privacy all'interno di iOS predefinito manifest editor. È necessario invece utilizzare l'editor PList generico, quindi, eseguire le operazioni seguenti:

1. Fare clic sui **Info. plist** del file nei **Esplora soluzioni** e selezionare **Apri con...** .
2. Selezionare il **l'Editor PList generico** dall'elenco dei programmi per aprire il file, quindi fare clic su **OK**.

    ![Selezionare l'Editor PList generici](security-privacy-images/InfoEditorSelectionVs.png "selezionare l'Editor PList generico")
3. Scegliere il **+** pulsante nell'ultima riga nell'editor aggiungere una nuova voce all'elenco. Questo evento viene chiamato "Proprietà personalizzata", con il tipo impostato su `String` e un valore vuoto.
4. Fare clic sul nome della proprietà e verrà visualizzato un elenco a discesa.
5. Nell'elenco a discesa, selezionare una chiave privata (ad esempio **Privacy - descrizione utilizzo HomeKit**): 

    ![Selezionare una chiave per la Privacy](security-privacy-images/InfoPListEditorSelectKey.png "selezionare una chiave per la Privacy")
6. Immettere una descrizione nella colonna valore per il motivo per cui l'app deve accedere alle informazioni utente o funzionalità specificati: 

    ![Immettere una descrizione](security-privacy-images/InfoPListSetValue.png "immettere una descrizione")
7. Salvare le modifiche apportate al file.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per impostare una delle chiavi di Privacy, eseguire le operazioni seguenti:

1. Fare doppio clic sul file **Info.plist** in **Esplora soluzioni** per aprirlo e modificarlo.
2. Nella parte inferiore della schermata, passare al **origine** visualizzazione.
3. Aggiungere un nuovo **voce** all'elenco.
4. Nell'elenco a discesa, selezionare una chiave privata (ad esempio **Privacy - descrizione utilizzo HomeKit**): 

    ![Selezionare una chiave per la Privacy](security-privacy-images/info02.png "selezionare una chiave per la Privacy")
5. Immettere una descrizione per il motivo per cui l'app deve accedere alle informazioni utente o funzionalità specificati: 

    ![Immettere una descrizione](security-privacy-images/info03.png "immettere una descrizione")
6. Salvare le modifiche apportate al file.

-----

> [!IMPORTANT]
> Nell'esempio indicato in precedenza, il tentativo di impostare il `NSHomeKitUsageDescription` chiavi nel **Info. plist** file comporterebbe l'app _invisibile all'utente non riusciti_ (viene chiuso dal sistema in fase di esecuzione) senza errori quando si esegue in iOS 10 ( o versione successiva).

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la sicurezza e privacy apportate ai che Apple con iOS 10 e gli effetti di un'app xamarin. IOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
