---
title: Applicazione di modifiche
description: Come iniziare a usare l'applicazione delle modifiche nei progetti Novell. Android.
ms.assetid: 38950B0C-8880-448F-B12E-08D5BFE87D0D
author: JonDouglas
ms.author: jodou
ms.date: 03/09/2020
ms.openlocfilehash: 03bace97908a53ac6112cd2600b20d429fe2f521
ms.sourcegitcommit: ec112800a76089ab1db66fe24b8bbcc510e067b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80215348"
---
# <a name="apply-changes"></a>Applicazione di modifiche

Apply Changes consente di effettuare il push delle modifiche alle risorse nell'app in esecuzione senza riavviare l'app. In questo modo è possibile controllare la quantità di app riavviata quando si desidera distribuire e testare piccole modifiche incrementali, conservando allo stesso tempo lo stato corrente del dispositivo o dell'emulatore.

Apply Changes usa le funzionalità nell' [implementazione di Android jvmti](https://docs.oracle.com/javase/8/docs/platform/jvmti/jvmti.html#bci) , supportata nei dispositivi o negli emulatori che eseguono Android 8,0 (livello API 26) o versione successiva.

## <a name="requirements"></a>Requisiti

Nell'elenco seguente vengono illustrati i requisiti per l'utilizzo di Apply Changes:

- **Visual Studio** -in Windows, eseguire l'aggiornamento a visual studio 2019 versione 16,5 o successiva. In macOS eseguire l'aggiornamento a Visual Studio 2019 per Mac versione 8,5 o successiva.
- **Novell. Android** : Novell. Android 10,2 o versione successiva deve essere installato con Visual Studio (Novell. Android viene installato automaticamente come parte del carico di lavoro **sviluppo di applicazioni per dispositivi mobili con .NET** in Windows e installato come parte del **programma di installazione di Visual Studio per Mac**).
- **Android SDK** -Android API 28 o versione successiva deve essere installato tramite gestione Android SDK.
- **Dispositivo o emulatore di destinazione** : il dispositivo o l'emulatore deve eseguire Android 8,0 (livello API 26) o versione successiva.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="get-started"></a>Attività iniziali

Per iniziare a applicare le modifiche, è necessario assicurarsi che un dispositivo o un emulatore esegua Android 8,0 (livello API 26) o versione successiva. Quindi eseguire l'applicazione Android con o senza debug.

È quindi possibile interagire con applica modifiche con gli approcci seguenti:

1. **Icona della barra degli strumenti.** È possibile fare clic sull'icona della barra degli strumenti applica modifiche per applicare le modifiche al dispositivo o all'emulatore di destinazione.

    [![applica modifiche-icona della barra degli strumenti](apply-changes-images/Apply-Changes-Toolbar.png)](apply-changes-images/Apply-Changes-Toolbar.png#lightbox)

2. **Tasto di scelta rapida.** È possibile usare il tasto di scelta rapida **MAIUSC + ALT + F5** per applicare le modifiche al dispositivo o all'emulatore di destinazione.
3. **Menu debug.** È possibile usare la voce di menu **Debug > applica modifiche** per applicare modifiche al dispositivo o all'emulatore di destinazione.

    [![applica modifiche-menu debug](apply-changes-images/Apply-Changes-Debug-Menu.png)](apply-changes-images/Apply-Changes-Debug-Menu.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

## <a name="get-started"></a>Attività iniziali

Per iniziare a applicare le modifiche, è necessario assicurarsi che un dispositivo o un emulatore esegua Android 8,0 (livello API 26) o versione successiva. Quindi eseguire l'applicazione Android con o senza debug.

È quindi possibile interagire con applica modifiche con gli approcci seguenti:

1. **Tasto di scelta rapida.** È possibile usare il tasto di scelta rapida **⌥ + ⇧ + R** per applicare le modifiche al dispositivo o all'emulatore di destinazione.
2. **Menu Esegui.** È possibile usare la voce di menu **esegui > applica modifiche** per applicare modifiche al dispositivo o all'emulatore di destinazione.

    [![applica modifiche-menu debug](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png)](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png#lightbox)

-----

## <a name="limitations"></a>Limitazioni

Per le modifiche seguenti è necessario riavviare l'applicazione:

- Modifica C# del codice.
- Aggiunta o rimozione di una risorsa.
- Modifica di file AndroidManifest. XML.
- Modifica delle librerie native (file. so).

## <a name="related-links"></a>Collegamenti correlati

- [Applica modifiche](https://developer.android.com/studio/run#apply-changes)
