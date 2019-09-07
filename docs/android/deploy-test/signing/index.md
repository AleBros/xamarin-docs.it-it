---
title: Firma del pacchetto dell'applicazione Android
description: Come firmare il pacchetto dell'applicazione Android (APK) per la pubblicazione
ms.prod: xamarin
ms.assetid: 8E3EFBB2-F8AD-C126-5F32-7FD140791E53
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/02/2018
ms.openlocfilehash: 0a60031481f16a3a600cca090d39ec47cbb8ca9a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756051"
---
# <a name="signing-the-android-application-package"></a>Firma del pacchetto dell'applicazione Android

In [Preparazione di un'applicazione per il rilascio](~/android/deploy-test/release-prep/index.md) è stato usato lo strumento **Gestione archivi** per compilare l'app e inserirla in un archivio per la firma e la pubblicazione. Questa sezione spiega come creare un'identità di firma di Android e un nuovo certificato di firma per le applicazioni Android e come pubblicare l'app archiviata *ad hoc* nel disco. Il pacchetto dell'applicazione Android (APK) risultante può essere trasferito localmente in dispositivi Android senza passare attraverso un app store.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

In [Archivia per la pubblicazione](~/android/deploy-test/release-prep/index.md#archive), la finestra di dialogo **Canale di distribuzione** offre due opzioni di distribuzione. Selezionare **Ad hoc**:

[![Finestra di dialogo Canale di distribuzione](images/vs/01-distribution-channel-sml.png)](images/vs/01-distribution-channel.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

In [Archivia per la pubblicazione](~/android/deploy-test/release-prep/index.md#archive), la finestra di dialogo **Firma e distribuisci** offre due opzioni di distribuzione. Selezionare **Ad hoc** e fare clic su **Avanti**:

[![Finestra di dialogo Firma e distribuisci](images/xs/01-select-ad-hoc-sml.png)](images/xs/01-select-ad-hoc.png#lightbox)

-----

<a name="newcertvs" />
<a name="newcert" />
<a name="newcertxs" />

## <a name="create-a-new-certificate"></a>Creare un nuovo certificato

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Dopo la selezione di **Ad hoc**, Visual Studio apre la pagina **Identità di firma** della finestra di dialogo, come illustrato nello screenshot seguente. Perché possa essere pubblicato, il file con estensione APK deve prima essere firmato con una chiave di firma (detta anche certificato).

È possibile usare un certificato esistente facendo clic sul pulsante **Importa** e quindi passando a [Firma APK](#sign-the-apk). In alternativa, fare clic sul pulsante **+** per creare un nuovo certificato:

[![Identità di firma ad hoc](images/vs/02-ad-hoc-signing-identity-vs-sml.png)](images/vs/02-ad-hoc-signing-identity-vs.png#lightbox)

Verrà visualizzata la finestra di dialogo **Create Android Key Store** (Crea archivio chiavi Android). Usare questa finestra di dialogo per creare un nuovo certificato di firma utilizzabile per firmare applicazioni Android. Immettere le informazioni obbligatorie (evidenziate in rosso) come illustrato in questa finestra di dialogo:

[![Finestra di dialogo Create Android Key Store (Crea archivio chiavi Android)](images/vs/03-create-android-key-store-vs-sml.png)](images/vs/03-create-android-key-store-vs.png#lightbox)

L'esempio seguente illustra il tipo di informazioni che è necessario specificare. Fare clic su **Crea** per creare il nuovo certificato:

[![Creazione di un nuovo certificato](images/vs/04-key-store-example-vs-sml.png)](images/vs/04-key-store-example-vs.png#lightbox)

L'archivio chiavi risultante si trova nel percorso seguente:

**C:\\Utenti\\*NOMEUTENTE*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\*ALIAS*\\*ALIAS*.keystore**

Ad esempio, usando **chimp** come alias, i passaggi precedenti consentono di creare una nuova chiave di firma nel percorso seguente:

**C:\\Utenti\\*NOMEUTENTE*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\chimp\\chimp.keystore**

> [!NOTE]
> Assicurarsi di eseguire il backup del file dell'archivio chiavi risultante e della password in un luogo sicuro &ndash; non è incluso nella soluzione. Se si perde il file dell'archivio chiavi, ad esempio a causa dello spostamento in un altro computer o della reinstallazione di Windows, non sarà possibile firmare l'app con lo stesso certificato delle versioni precedenti.

Per altre informazioni sull'archivio chiavi, vedere [Ricerca della firma SHA1 o MD5 dell'archivio chiavi](~/android/deploy-test/signing/keystore-signature.md).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Dopo che si è fatto clic su **Ad hoc**, Visual Studio per Mac apre la finestra di dialogo **Identità di firma Android**, come illustrato nel prossimo screenshot. Perché possa essere pubblicato, il file con estensione APK deve prima essere firmato con una chiave di firma (detta anche certificato). Se un certificato esiste già, fare clic sul pulsante **Importa una chiave esistente** per importarlo e quindi passare a [Firma APK](#sign-the-apk). In alternativa, fare clic sul pulsante **Crea una nuova chiave** per creare un nuovo certificato:

[![Finestra di dialogo Identità di firma Android](images/xs/02-android-signing-identity-sml.png)](images/xs/02-android-signing-identity.png#lightbox)

La finestra di dialogo **Crea nuovo certificato** viene usata per creare un nuovo certificato di firma utilizzabile per firmare applicazioni Android. Dopo aver immesso le informazioni necessarie, fare clic su **OK**:

[![Finestra di dialogo Crea nuovo certificato](images/xs/03-create-new-certificate-sml.png)](images/xs/03-create-new-certificate.png#lightbox)

L'archivio chiavi risultante si trova nel percorso seguente:

**~/Library/Developer/Xamarin/Keystore/alias/alias.keystore**

I passaggi precedenti, ad esempio, consentono di creare una nuova chiave di firma nel percorso seguente:

**~/Library/Developer/Xamarin/Keystore/chimp/chimp.keystore**

> [!NOTE]
> Assicurarsi di eseguire il backup del file dell'archivio chiavi risultante e della password in un luogo sicuro &ndash; non è incluso nella soluzione. Se si perde il file dell'archivio chiavi, ad esempio a causa dello spostamento in un altro computer o della reinstallazione di macOS, non sarà possibile firmare l'app con lo stesso certificato delle versioni precedenti.

Per altre informazioni sull'archivio chiavi, vedere [Ricerca della firma SHA1 o MD5 dell'archivio chiavi](~/android/deploy-test/signing/keystore-signature.md).

-----

## <a name="sign-the-apk"></a>Firmare il pacchetto APK

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Quando si fa clic su **Crea**, un nuovo archivio chiavi, contenente un nuovo certificato, viene salvato ed elencato in **Identità di firma**, come illustrato nel prossimo screenshot. Per pubblicare un'app in Google Play, fare clic su **Annulla** e passare a [Pubblicazione in Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md).
Per eseguire la pubblicazione *ad hoc* e pubblicare l'app per la distribuzione indipendente, selezionare l'identità di firma da usare e fare clic su **Salva con nome**. In questo screenshot, ad esempio, è selezionata l'identità di firma **chimp** creata in precedenza:

[![Esempio di identità di firma](images/vs/05-save-as-vs-sml.png)](images/vs/05-save-as-vs.png#lightbox)

**Gestione archivi** visualizza quindi lo stato della pubblicazione. Al termine del processo di pubblicazione, viene visualizzata la finestra di dialogo **Salva con nome** in cui specificare il percorso in cui deve essere archiviato il file con estensione APK generato:

[![Finestra di dialogo Salva con nome](images/vs/06-save-as-dialog-vs-sml.png)](images/vs/06-save-as-dialog-vs.png#lightbox)

Passare al percorso desiderato e fare clic su **Salva**. Se la password della chiave non è nota, viene visualizzata la finestra di dialogo **Password per la firma**, che richiede la password per il certificato selezionato:

[![Finestra di dialogo Password per la firma](images/vs/07-signing-password-vs-sml.png)](images/vs/07-signing-password-vs.png#lightbox)

Dopo il completamento del processo di firma, fare clic su **Apri distribuzione**:

[![Pulsante Apri distribuzione](images/vs/08-open-distribution-sml.png)](images/vs/08-open-distribution.png#lightbox)

Esplora risorse aprirà la cartella contenente il file APK generato. A questo punto, Visual Studio ha compilato l'applicazione Xamarin.Android in un file APK pronto per la distribuzione.
Lo screenshot seguente illustra un esempio di app pronta per la pubblicazione, **MyApp.MyApp.apk**:

[![APK visualizzato in Esplora risorse](images/vs/09-generated-app-vs-sml.png)](images/vs/09-generated-app-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Come si è visto, all'archivio chiavi è stato aggiunto un nuovo certificato. Per pubblicare un'app in Google Play, fare clic su **Annulla** e passare a [Pubblicazione in Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md).
In alternativa, fare clic su **Avanti** per pubblicare l'app *ad hoc* (per la distribuzione indipendente), come illustrato in questo esempio:

[![Finestra di dialogo Firma e distribuisci](images/xs/04-select-identity-sml.png)](images/xs/04-select-identity.png#lightbox)

La finestra di dialogo **Pubblica come ad hoc** visualizza un riepilogo dell'app firmata prima della pubblicazione. Se queste informazioni sono corrette, fare clic su **Pubblica**.

[![Pubblica come ad hoc](images/xs/05-publish-ad-hoc-sml.png)](images/xs/05-publish-ad-hoc.png#lightbox)

La finestra di dialogo **File APK di output** salva il pacchetto APK nel percorso specificato. Fare clic su **Save**.

![Finestra di dialogo File APK di output](images/xs/06-output-apk-file.png)

Immettere quindi la password per il certificato (la password usata nella finestra di dialogo **Crea nuovo certificato**) e fare clic su **OK**:

![Immettere la password del certificato](images/xs/07-signing-certificate.png)

Il file APK viene firmato con il certificato e salvato nel percorso specificato. Fare clic su **Visualizza in Finder**:

[![Finestra di dialogo Publication Succeeded (Pubblicazione completata)](images/xs/08-app-is-ready-sml.png)](images/xs/08-app-is-ready.png#lightbox)

Finder si aprirà in corrispondenza del percorso del file APK firmato:

[![APK visualizzato in Finder](images/xs/09-show-in-finder-sml.png)](images/xs/09-show-in-finder.png#lightbox)

È ora possibile copiare il pacchetto APK da Finder e inviarlo alla destinazione finale. È consigliabile installare il pacchetto APK in un dispositivo Android e provarlo prima della distribuzione. Vedere [Pubblicazione indipendente](~/android/deploy-test/publishing/publishing-independently.md) per altre informazioni sulla pubblicazione di un APK *ad hoc*.

-----

## <a name="next-steps"></a>Fasi successive

Dopo che il pacchetto dell'applicazione è stato firmato per il rilascio, deve essere pubblicato. Le sezioni seguenti descrivono diversi modi per pubblicare un'applicazione.
