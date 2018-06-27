---
title: Risoluzione dei problemi di configurazione dell'emulatore
description: Questo articolo spiega come diagnosticare e risolvere i problemi che possono verificarsi quando si usa Android Device Manager.
ms.prod: xamarin
ms.assetid: 4F053CC9-9378-47CB-8002-978A6558C4D0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/01/2018
ms.openlocfilehash: 4cbd7d7626d114856d6c68fe7bc9feb7c2a5abc2
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34733653"
---
# <a name="troubleshooting-emulator-setup-problems"></a>Risoluzione dei problemi di configurazione dell'emulatore

_Questo articolo spiega come diagnosticare e risolvere i problemi che possono verificarsi quando si usa Android Device Manager per configurare l'emulatore Android. Per informazioni sulla risoluzione dei problemi durante l'esecuzione dell'emulatore Android, vedere [Risoluzione dei problemi dell'emulatore Android di Google](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md)._

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="android-sdk-in-non-standard-location"></a>Android SDK in un percorso non standard

In genere, Android SDK viene installato nel percorso seguente:

**C:\\Programmi (x86)\\Android\\android-sdk**

Se l'SDK non viene installato in questa posizione, è possibile che venga visualizzato il messaggio di errore seguente all'avvio di Android Device Manager:

![Errore di istanza di Android SDK](troubleshooting-images/win/01-sdk-error.png)

Per evitare questo problema, seguire questa procedura:

1. Nel desktop di Windows passare a **C:\\Utenti\\*nomeutente*\\AppData\\Roaming\\XamarinDeviceManager**:

    ![Percorso del file di log di Android Device Manager](troubleshooting-images/win/02-log-files.png)

2. Fare doppio clic per aprire uno dei file di log e trovare il **percorso del file di configurazione**. Ad esempio:

    [![Percorso del file di configurazione nel file di log](troubleshooting-images/win/03-config-file-path-sml.png)](troubleshooting-images/win/03-config-file-path.png#lightbox)

3. Passare a questo percorso e fare doppio clic su **user.config** per aprirlo. 

4. In **user.config** trovare l'elemento **&lt;UserSettings&gt;** e aggiungere un attributo **AndroidSdkPath** a tale elemento. Impostare l'attributo sul percorso in cui è installato Android SDK nel computer in uso e salvare il file. Ad esempio **&lt;UserSettings&gt;** avrà un aspetto simile al seguente se Android SDK è stato installato in **C:\\Programmi\\Android\\SDK**:
        
    ```xml
    <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:\Programs\Android\SDK" />
    ```

Dopo aver modificato in questo modo **user.config**, è possibile avviare Android Device Manager.

## <a name="snapshot-disables-wifi-on-android-oreo"></a>Uno snapshot disabilita l'accesso Wi-Fi in Android Oreo

In presenza di un dispositivo virtuale Android configurato per Android Oreo con accesso Wi-Fi simulato, il riavvio dell'AVD dopo uno snapshot può causare la disabilitazione dell'accesso Wi-Fi.

Per risolvere il problema:

1. Selezionare l'AVD in Android Device Manager.

2. Scegliere **Visualizza in Esplora risorse** dal menu delle opzioni aggiuntive.

3. Passare a **Snapshot > default_boot**.

4. Eliminare il file **snapshot.pb**:

    ![Percorso del file snapshot.pb](troubleshooting-images/win/05-delete-snapshot.png)

5. Riavviare l'AVD. 

Dopo aver apportato queste modifiche, l'AVD verrà riavviato in uno stato che consente di nuovo il funzionamento dell'accesso Wi-Fi.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

## <a name="snapshot-disables-wifi-on-android-oreo"></a>Uno snapshot disabilita l'accesso Wi-Fi in Android Oreo

In presenza di un dispositivo virtuale Android configurato per Android Oreo con accesso Wi-Fi simulato, il riavvio dell'AVD dopo uno snapshot può causare la disabilitazione dell'accesso Wi-Fi.

Per risolvere il problema:

1. Selezionare l'AVD in Android Device Manager.

2. Scegliere **Visualizza in Finder** dal menu delle opzioni aggiuntive.

3. Passare a **Snapshot > default_boot**.

4. Eliminare il file **snapshot.pb**:

    [![Percorso del file snapshot.pb](troubleshooting-images/mac/02-delete-snapshot-sml.png)](troubleshooting-images/mac/02-delete-snapshot.png#lightbox)

5. Riavviare l'AVD. 

Dopo aver apportato queste modifiche, l'AVD verrà riavviato in uno stato che consente di nuovo il funzionamento dell'accesso Wi-Fi.


-----

## <a name="generating-a-bug-report"></a>Generazione di un report sui bug

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Se si rileva un problema in Android Device Manager che non può essere risolto con i suggerimenti sopra elencati, inoltrare un report sui bug facendo clic con il pulsante destro del mouse sulla barra del titolo e selezionando **Generate Bug Report** (Genera report sui bug):

[![Voce di menu per l'inoltro di un report sui bug](troubleshooting-images/win/04-bug-report-sml.png)](troubleshooting-images/win/04-bug-report.png#lightbox)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Se si rileva un problema in Android Device Manager che non può essere risolto con i suggerimenti sopra elencati, inoltrare un report sui bug facendo clic su **Help > Generate Bug Report** (Aiuto > Genera report sui bug):

[![Voce di menu per l'inoltro di un report sui bug](troubleshooting-images/mac/01-bug-report-sml.png)](troubleshooting-images/mac/01-bug-report.png#lightbox)

-----
