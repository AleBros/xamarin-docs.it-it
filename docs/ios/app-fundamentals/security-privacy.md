---
title: Funzionalità per la sicurezza e la privacy di iOS
description: In questo documento vengono descritte le funzionalità di sicurezza e privacy di iOS e viene illustrato come usarle con Xamarin.iOS. Vengono esaminati gli aggiornamenti apportati in iOS 10 e viene illustrato come accedere ai dati utente privati.
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: cdc59c4eb3863435482acf5eb0dcd1368beea693
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009656"
---
# <a name="ios-security-and-privacy-features"></a>Funzionalità per la sicurezza e la privacy di iOS

_Questo articolo illustra l'uso della sicurezza e della privacy in iOS e del modo in cui influiscono su un'app Xamarin.iOS._

Apple ha apportato diversi miglioramenti alla sicurezza e alla privacy in iOS 10 (e versioni successive) che aiuteranno lo sviluppatore a migliorare la sicurezza delle app e garantire la privacy degli utenti finali. In questo articolo viene illustrata l'implementazione di queste funzionalità in un'app Xamarin.iOS.

<a name="General-Enhancements" />

## <a name="general-enhancements"></a>Miglioramenti generali

In iOS 10 sono state apportate le seguenti modifiche generali alla sicurezza e alla privacy:

- L'API CDSA (Common Data Security Architecture) è stata deprecata e deve essere sostituita con l'API SecKey per generare chiavi asimmetriche.
- È possibile aggiungere la nuova chiave di `NSAllowsArbitraryLoadsInWebContent` al file **info. plist** di un'app e consentire il caricamento corretto delle pagine Web mentre la protezione di Apple Transport Security (ATS) è ancora abilitata per il resto dell'app. Per ulteriori informazioni, vedere la documentazione [sulla sicurezza del trasporto delle app](~/ios/app-fundamentals/ats.md) .
- Poiché i nuovi Appunti in iOS 10 e macOS Sierra consentono all'utente di copiare e incollare tra dispositivi, l'API è stata espansa in modo da consentire la limitazione di uno degli Appunti a un dispositivo specifico e il timestamp da cancellare automaticamente in un determinato punto. Inoltre, il nome Pasteboards non è più permanente e deve essere sostituito con i contenitori di montaggio condivisi.
- Per tutte le connessioni SSL/TLS, la crittografia simmetrica RC4 è ora disabilitata per impostazione predefinita. Inoltre, l'API trasporto sicuro non supporta più SSLv3 ed è consigliabile arrestare il prima possibile l'utilizzo della crittografia SHA-1 e 3DES.

<a name="Accessing-Private-User-Data" />

## <a name="accessing-private-user-data"></a>Accesso ai dati utente privati

Le app in esecuzione in iOS 10 (o versioni successive) devono dichiarare in modo statico l'intenzione di accedere a funzionalità specifiche o a informazioni utente immettendo una o più chiavi di privacy nei file **info. plist** che spiegano all'utente il motivo per cui l'app desidera ottenere l'accesso.

> [!IMPORTANT]
> Le app che non riescono a fornire le chiavi obbligatorie verranno interrotte automaticamente dal sistema quando tenteranno di accedere a una delle funzionalità limitate o alle informazioni dell'utente, _senza errori_. Se un'app viene avviata in modo imprevisto in iOS 10, verificare che siano stati specificati tutti i file **info. plist** richiesti.

Sono disponibili le seguenti chiavi correlate alla privacy:

- **Privacy-Descrizione utilizzo Apple Music** (`NSAppleMusicUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app desidera accedere al catalogo multimediale dell'utente.
- **Privacy-Descrizione Utilizzo periferica Bluetooth** (`NSBluetoothPeripheralUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app vuole accedere a Bluetooth sul dispositivo dell'utente.
- **Privacy-Calendars Usage Description** (`NSCalendarsUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app vuole accedere al calendario dell'utente.
- **Privacy-Descrizione utilizzo fotocamera** (`NSCameraUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app vuole accedere alla fotocamera del dispositivo.
- **Privacy-contacts Usage Description** (`NSContactsUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app vuole accedere ai contatti dell'utente.
- **Privacy-Descrizione Utilizzo condivisione integrità** (`NSHealthShareUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app desidera accedere ai dati di integrità dell'utente. Per ulteriori informazioni, vedere la pagina relativa al [riferimento alla classe HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)di Apple.
- **Privacy-Descrizione utilizzo aggiornamento integrità** (`NSHealthUpdateUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app desidera modificare i dati di integrità dell'utente. Per ulteriori informazioni, vedere la pagina relativa al [riferimento alla classe HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)di Apple.
- **Privacy-Descrizione utilizzo HomeKit** (`NSHomeKitUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app vuole accedere ai dati di configurazione HomeKit dell'utente.
- **Descrizione utilizzo: privacy-location always** (`NSLocationAlwaysUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app vuole avere sempre accesso alla posizione dell'utente.
- Deprecato **Descrizione utilizzo: privacy-location** (`NSLocationUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app vuole accedere al percorso utente. *Nota: questa chiave è stata deprecata in iOS 8 (e versioni successive). In alternativa, usare `NSLocationAlwaysUsageDescription` o `NSLocationWhenInUseUsageDescription`.*
- **Privacy-location quando è in uso Descrizione utilizzo** (`NSLocationWhenInUseUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app vuole accedere alla posizione dell'utente mentre è in esecuzione.
- Deprecato **Privacy-Descrizione utilizzo libreria multimediale** : consente allo sviluppatore di descrivere il motivo per cui l'app vuole accedere al catalogo multimediale dell'utente. *Nota: questa chiave è stata deprecata in iOS 8 (e versioni successive). In alternativa, usare `NSAppleMusicUsageDescription`.*
- **Privacy-Descrizione utilizzo microfono** (`NSMicrophoneUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app vuole accedere al microfono dei dispositivi.
- **Privacy-Motion Usage Description** (`NSMotionUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app vuole accedere all'accelerometro del dispositivo.
- **Privacy-Descrizione utilizzo raccolta foto** (`NSPhotoLibraryUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app vuole accedere alla raccolta foto dell'utente.
- **Descrizione utilizzo: privacy-promemoria** (`NSRemindersUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app desidera accedere ai promemoria dell'utente.
- **Privacy-Siri Usage Description** (`NSSiriUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app vuole inviare i dati utente a Siri.
- **Descrizione utilizzo: privacy-riconoscimento vocale** (`NSSpeechRecognitionUsageDescription`): consente allo sviluppatore di descrivere il motivo per cui l'app vuole inviare i dati utente ai server di riconoscimento vocale Apple.
- **Descrizione utilizzo: privacy-TV provider** (`NSVideoSubscriberAccountUsageDescription`)-consente allo sviluppatore di descrivere il motivo per cui l'app desidera accedere all'account del provider TV dell'utente.

Per altre informazioni sull'uso delle chiavi **info. plist** , vedere [le informazioni di riferimento sulla chiave dell'elenco delle proprietà](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1)di Apple.

<a name="Setting-Privacy-Keys" />

## <a name="setting-privacy-keys"></a>Impostazione delle chiavi di privacy

Per accedere a HomeKit in iOS 10 e versioni successive, lo sviluppatore dovrà aggiungere la chiave `NSHomeKitUsageDescription` al file **info. plist** dell'app e fornire una stringa che dichiara il motivo per cui l'app desidera accedere al database HomeKit dell'utente. Questa stringa verrà presentata all'utente la prima volta che esegue l'app:

![Un avviso di NSHomeKitUsageDescription di esempio](security-privacy-images/info01.png "Un avviso di NSHomeKitUsageDescription di esempio")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Xamarin.iOS per Visual Studio attualmente non supporta la modifica delle chiavi di privacy **info. plist** nell'editor di manifesti iOS predefinito. Sarà invece necessario usare l'editor PList generico, quindi eseguire le operazioni seguenti:

1. Fare clic con il pulsante destro del mouse sul file **info. plist** nel **Esplora soluzioni** e scegliere **Apri con...**.
2. Selezionare l' **Editor PList generico** nell'elenco dei programmi per aprire il file e quindi fare clic su **OK**.

    ![Selezionare l'editor PList generico](security-privacy-images/InfoEditorSelectionVs.png "Selezionare l'editor PList generico")
3. Fare clic sul pulsante **+** nell'ultima riga dell'editor per aggiungere una nuova voce all'elenco. Questa operazione verrà denominata "proprietà personalizzata", con il tipo impostato su `String` e un valore vuoto.
4. Fare clic sul nome della proprietà e verrà visualizzato un elenco a discesa.
5. Nell'elenco a discesa selezionare una chiave per la privacy, ad esempio **privacy-HomeKit Usage Description**: 

    ![Selezionare una chiave per la privacy](security-privacy-images/InfoPListEditorSelectKey.png "Selezionare una chiave per la privacy")
6. Immettere una descrizione nella colonna valore per il motivo per cui l'app vuole accedere alla funzionalità o alle informazioni utente specificate: 

    ![Immettere una descrizione](security-privacy-images/InfoPListSetValue.png "Immettere una descrizione")
7. Salvare le modifiche apportate al file.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per impostare le chiavi sulla privacy, procedere come segue:

1. Fare doppio clic sul file **Info.plist** in **Esplora soluzioni** per aprirlo e modificarlo.
2. Nella parte inferiore della schermata passare alla visualizzazione **origine** .
3. Consente di aggiungere una nuova **voce** all'elenco.
4. Nell'elenco a discesa selezionare una chiave per la privacy, ad esempio **privacy-HomeKit Usage Description**: 

    ![Selezionare una chiave per la privacy](security-privacy-images/info02.png "Selezionare una chiave per la privacy")
5. Immettere una descrizione per il motivo per cui l'app vuole accedere alla funzionalità o alle informazioni utente specificate: 

    ![Immettere una descrizione](security-privacy-images/info03.png "Immettere una descrizione")
6. Salvare le modifiche apportate al file.

-----

> [!IMPORTANT]
> Nell'esempio precedente, l'impossibilità di impostare la chiave `NSHomeKitUsageDescription` nel file **info. plist** comporterebbe la _mancata esecuzione invisibile_ dell'app (chiusa dal sistema in fase di esecuzione) senza errori durante l'esecuzione in iOS 10 (o versione successiva).

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato le modifiche alla sicurezza e alla privacy apportate da Apple in iOS 10 e come incidono sull'app Xamarin.iOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
