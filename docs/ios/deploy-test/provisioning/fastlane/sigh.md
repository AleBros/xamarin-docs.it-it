---
title: 'fastlane per iOS: sigh'
description: Questo documento descrive il comando sigh di fastlane, che consente di creare, rinnovare e ripristinare i profili di provisioning per tutte le configurazione della build Xamarin.iOS.
ms.prod: xamarin
ms.assetid: CD17276F-2C8C-4A46-A54C-DD532EBD5720
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 8eedc86807035887cade48c42868649b362b7cb2
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34785502"
---
# <a name="fastlane-for-ios--sigh"></a>fastlane per iOS: sigh

> [!IMPORTANT]
> fastlane consiglia di usare [`match`](~/ios/deploy-test/provisioning/fastlane/match.md) per generare e gestire i profili di provisioning. Usare sigh direttamente solo se si vuole il controllo completo e si hanno conoscenze sufficienti sulla firma del codice.

## <a name="overview"></a>Panoramica

Il provisioning di dispositivi viene tradizionalmente effettuato da ogni membro di un team di sviluppo tramite Xcode o in Apple Developer Portal. La procedura prevede diversi passaggi:

- Richiesta di un certificato di sviluppo
- Aggiunta di un dispositivo al portale
- Creazione di un ID app
- Creazione di un profilo di provisioning
- Download di profili e certificati

Ognuno di questi passaggi contiene variabili che devono essere risolte, a seconda del tipo di applicazione che si sta sviluppando. Altre informazioni sui passaggi necessari per configurare un dispositivo per lo sviluppo manualmente o tramite Xcode sono disponibili nella guida [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md).

Questa guida presenta gli strumenti fastlane come alternativa all'uso di Xcode ed espone gli argomenti seguenti:

- [Cos'è sigh?](#whatissigh)
- [Creazione di un ID app](#appid)
- [Aggiunta di nuovi dispositivi](#newdevices)
- [Uso di sigh](#using)
- [Opzioni aggiuntive](#options)

> [!NOTE]
> Se esiste già un ID app che corrisponde all'ID bundle dell'app e se il dispositivo è presente nel portale per sviluppatori, è possibile ignorare i passaggi descritti in [Creazione di un ID app](#appid) e [Aggiunta di nuovi dispositivi](#newdevices). In questo caso, passare direttamente alla sezione [Uso di sigh](#using) per iniziare.

## <a name="installation"></a>Installazione

Per informazioni sull'installazione di fastlane, vedere l'introduzione alla guida di [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).

<a name="whatissigh" />

## <a name="what-is-sigh"></a>Cos'è sigh

sigh offre un'interfaccia di terminale che consente di creare e rinnovare profili di provisioning per tutte le configurazioni: sviluppo, distribuzione tramite l'App Store, distribuzione ad hoc e distribuzione aziendale. Offre inoltre un modo semplice per scaricare e riparare i profili di provisioning.

<a name="appid" />

## <a name="creating-an-app-id"></a>Creazione di un ID app

È possibile creare un ID app con il comando seguente:

    fastlane produce -u your@appleid.com -a com.company.appname --skip_itc

dove `com.company.appname` è l'ID bundle dell'app, disponibile nel file Info.plist dell'applicazione Xamarin.iOS, come illustrato di seguito:

[![](sigh-images/fastlane-image5.png "File Info.plist dell'applicazione Xamarin.iOS")](sigh-images/fastlane-image5.png#lightbox)

L'ID app univoco deve essere una stringa di tipo DNS inverso. Dopo che è stato creato, annotare l'ID perché sarà necessario usarlo più avanti in questa guida quando si userà sigh.

Se è necessario creare l'app in [iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md), rimuovere il flag `--skip_itc` dal comando precedente.

<a name="newdevices" />

## <a name="adding-new-devices"></a>Aggiunta di nuovi dispositivi

Per aggiungere un singolo dispositivo al portale per sviluppatori dalla riga di comando, immettere il comando seguente:

```bash
fastlane run register_device name:"Adam iPhone" udid:"abcdeg1234567"
```

Per aggiungere più di un dispositivo usare il comando `register_devices`:

```bash
    register_devices(
        devices: {
            "iPhone 6" => "1234567890123456789012345678901234567890",
            "iPad Air 2" => "abcdefghijklmnopqrstvuwxyzabcdefghijklmn"
         }
    )
```

<a name="using" />

## <a name="using-sigh"></a>Uso di sigh

Per iniziare a usare l'utilità sigh, immettere il comando seguente nel terminale:

```bash
fastlane sigh
```

Per impostazione predefinita, verrà creato un profilo di provisioning di [distribuzione tramite l'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md). Per configurare il dispositivo per lo sviluppo, passare il flag `--development`:

```bash
fastlane sigh --development
```

Quando richiesto da fastlane, immettere il nome utente ID Apple. È possibile che venga richiesta anche la password se è la prima volta che si usa fastlane. In caso contrario, verrà estratta la variabile di ambiente della password dal keychain.

Se l'ID Apple è connesso a più team, questi verranno visualizzati qui. Selezionare il numero corrispondente al team che si vuole usare:

[![](sigh-images/fastlane-image2.png "Selezionare il team che si vuole usare")](sigh-images/fastlane-image2.png#lightbox)

È possibile passare l'ID team all'interfaccia della riga di comando anche nel modo seguente:

```bash
fastlane sigh -l 2TU993NY9J
```

Immettere l'[ID app](#appid) dell'app. Tenere presente che deve corrispondere all'identificatore del bundle disponibile nel file Info.plist dell'app.

Tutti i dispositivi connessi all'account verranno aggiunti al profilo di provisioning.

fastlane quindi creerà, scaricherà e installerà automaticamente il profilo di provisioning.

Nel centro sviluppatori sarà possibile visualizzare il profilo di provisioning appena creato, come illustrato di seguito:

[![](sigh-images/fastlane-image10.png "Visualizzare il profilo di provisioning appena creato")](sigh-images/fastlane-image10.png#lightbox)

Per impostazione predefinita, sigh archivierà i profili di provisioning nella cartella corrente. Per cambiare la directory di output, modificare `output_path` o eseguire il comando seguente:

```bash
fastlane sigh -o "~/Library/MobileDevice/Provisioning Profiles"
```

<a name="options" />

## <a name="sigh-additional-options"></a>Opzioni aggiuntive di sigh

Per un supporto aggiuntivo durante l'uso di sigh, è possibile usare le opzioni seguenti:

- Per scaricare tutti i profili di provisioning, usare:

    ```bash
    fastlane sigh download_all
    ```

- Per usare un'identità di forma specifica per il profilo di provisioning, usare:

    ```bash
    fastlane sigh -c "Amy cert"
    ```
    
    Dove `Amy cert` è il nome Identità di firma del codice.


## <a name="related-links"></a>Collegamenti correlati

- [fastlane - sigh](https://github.com/fastlane/fastlane/tree/master/sigh#readme)
