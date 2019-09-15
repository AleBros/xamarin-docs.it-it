---
title: Procedura di installazione-accedere con Apple per Novell. Forms
description: L'accesso con l'installazione di Apple è diverso a seconda delle diverse piattaforme di destinazione dell'applicazione mobile.
ms.prod: xamarin
ms.assetid: 8F712802-395B-469B-B5BE-C927AD1A8391
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
ms.openlocfilehash: c46da6b4ed877cc85f98e6ef0ab2b9a28d811723
ms.sourcegitcommit: cf56d2bae34dc0f8e94c2d3d28d5f460d59807bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70986009"
---
# <a name="setup-sign-in-with-apple-for-xamarinforms"></a>Configurare l'accesso con Apple per Novell. Forms

![Questa API è attualmente in anteprima](~/media/shared/preview.png)

Questa guida illustra la serie di passaggi necessari per configurare le applicazioni multipiattaforma in modo da richiedere l'accesso avanzato con Apple. Mentre l'installazione di Apple è diretta nel portale per sviluppatori di Apple, sono necessari passaggi aggiuntivi per creare una relazione sicura tra Android e Apple. 

## <a name="apple-developer-setup"></a>Installazione di Apple Developer

Prima di poter usare l'accesso con Apple nelle applicazioni, è necessario risolvere alcuni passaggi di configurazione nella sezione [Certificates, identifiers & Profiles](https://developer.apple.com/account/resources/) del portale per sviluppatori di Apple.

### <a name="apple-sign-in-domain"></a>Dominio di accesso Apple

Registrare il nome di dominio e verificarlo con Apple nella sezione [altre](https://developer.apple.com/account/resources/services/list) della sezione *Certificates, Identifiers & Profiles* .

![Altre sezioni](sign-in-images/readme-signin-domain-configure.png)

Aggiungere il dominio e fare clic su **registra**.

![modulo registra dominio](sign-in-images/readme-signin-domain-more.png)

> [!NOTE]
> Se viene visualizzato un errore relativo al fatto che il dominio non è conforme a SPF, è necessario aggiungere un record TXT DNS SPF al dominio e attendere la propagazione prima di continuare: Il TXT SPF potrebbe avere un aspetto simile al seguente:`v=spf1 a a:myapp.com -all`

Sarà quindi necessario verificare la proprietà del dominio facendo clic su **download** per recuperare il `apple-developer-domain-association.txt` file e `.well-known` caricarlo nella cartella del sito Web del dominio.

Una volta `.well-known/apple-developer-domain-association.txt` caricato e raggiungibile il file, è possibile fare clic su **Verifica** per fare in modo che Apple verifichi la proprietà del dominio.

> [!NOTE]
> Apple verificherà la proprietà `https://`con. Assicurarsi di disporre della configurazione SSL e che il file sia accessibile tramite un URL sicuro.

Il processo è stato completato prima di continuare.

## <a name="setup-your-app-id"></a>Configurare l'ID app

Nella sezione [identificatori](https://developer.apple.com/account/resources/identifiers/list) creare un nuovo identificatore e scegliere **ID app**. Se si dispone già di un ID app, scegliere di modificarlo.

![Crea un nuovo ID app](sign-in-images/readme-appid-create.png)

Abilitare l' **accesso con Apple**. Probabilmente si vuole usare l'opzione **Abilita come ID app primaria** .

![Abilitare l'accesso con Apple](sign-in-images/readme-appid-signin.png)

Salvare le modifiche apportate all'ID app.

## <a name="create-a-service-id"></a>Creare un ID servizio

Nella sezione [identificatori](https://developer.apple.com/account/resources/identifiers/list/serviceId) creare un nuovo identificatore e scegliere **ID servizio**.

![Crea un nuovo ID servizio](sign-in-images/readme-serviceid-create.png)

Assegnare all'ID servizi una descrizione e un identificatore.  Questo identificatore sarà il `ServerId`.  Assicurarsi di abilitare l' **accesso con Apple**.

Prima di continuare, fare clic su **Configura** accanto all'opzione _Accedi con Apple_ abilitata.

Nel pannello configurazione assicurarsi che sia selezionato l' **ID app principale** corretto.

Scegliere quindi il **dominio web** configurato in precedenza.

Infine, aggiungere uno o più **URL restituiti**.  Qualsiasi `redirect_uri` utente usato in seguito deve essere registrato in questo modo esattamente quando lo usi.  Assicurarsi di includere `http://` o `https://` nell'URL quando viene immesso.

> [!NOTE]
> A scopo di test, non è `127.0.0.1` possibile `localhost`usare o, ma è possibile `local.test`usare altri domini, ad esempio.  Se si sceglie di eseguire questa operazione, è possibile modificare il file `hosts` del computer per risolvere questo dominio fittizio nell'indirizzo IP locale.

![Configurare l'accesso Apple](sign-in-images/readme-serviceid-configure.png)

Al termine, salvare le modifiche.

## <a name="create-a-key-for-your-services-id"></a>Creare una chiave per l'ID servizi

Nella sezione [chiavi](https://developer.apple.com/account/resources/authkeys/list) creare una nuova **chiave**.

Assegnare un nome alla chiave e abilitare l' **accesso con Apple**.

![Crea una nuova chiave](sign-in-images/readme-key-create.png)

Fare clic su **Configura** accanto a _Accedi con Apple_.

Verificare che sia selezionato l' **ID app primario** corretto e fare clic su **Salva**.

Fare clic su **continue (continua** ) e quindi su **registra** per creare la nuova chiave.

Successivamente, sarà possibile scaricare solo la chiave appena generata.  Scegliere **Download**.

![Scarica la chiave](sign-in-images/readme-key-download.png)

Prendere nota anche dell' **ID chiave** in questo passaggio. Che verrà usato `KeyId` in un secondo momento.

Il file di `.p8` chiave sarà stato scaricato.  È possibile aprire questo file in blocco note o VSCode per visualizzare il contenuto del testo.  Dovrebbero avere un aspetto simile al seguente:

```
-----BEGIN PRIVATE KEY-----
MIGTAgEAMBMGBasGSM49AgGFCCqGSM49AwEHBHkwdwIBAQQg3MX8n6VnQ2WzgEy0
Skoz9uOvatLMKTUIPyPCAejzzUCgCgYIKoZIzj0DAQehRANCAARZ0DoM6QPqpJxP
JKSlWz0AohFhYre10EXPkjrih4jTm+b0AeG2BGuoIWd18i8FimGDgK6IzHHPsEqj
DHF5Svq0
-----END PRIVATE KEY-----
```

Assegnare un nome `P8FileContents` a questa chiave e mantenerla in un luogo sicuro. Che verrà usato quando si integra questo servizio nell'applicazione per dispositivi mobili.

## <a name="summary"></a>Riepilogo

Questo articolo descrive i passaggi necessari per configurare l'accesso con Apple per l'uso nelle applicazioni Novell. Forms.

## <a name="related-links"></a>Collegamenti correlati

- [Accedere con le linee guida di Apple](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
  