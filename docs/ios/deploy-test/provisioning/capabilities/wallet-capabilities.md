---
title: Funzionalità Portafoglio in Xamarin.iOS
description: L'aggiunta di funzionalità a un'applicazione spesso richiede un'ulteriore configurazione del provisioning. Questa guida illustra la configurazione necessaria per le funzionalità Portafoglio.
ms.prod: xamarin
ms.assetid: BD9475E6-F586-488C-93D4-8A2A1629B99B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: 658184203c81079af9b4946c4da18dfb98381b0f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73028546"
---
# <a name="wallet-capabilities-in-xamarinios"></a>Funzionalità Portafoglio in Xamarin.iOS

_L'aggiunta di funzionalità a un'applicazione spesso richiede un'ulteriore configurazione di provisioning. Questa guida illustra la configurazione necessaria per le funzionalità di Wallet._

Portafoglio è un'app che consente di archiviare e visualizzare codici a barre e altro contenuto consentendo agli utenti di visualizzare biglietti, carte d'imbarco e buoni sconto direttamente dal dispositivo. Queste informazioni vengono archiviate in un _pass_. Una carta d'imbarco o un singolo biglietto, ad esempio, costituiscono un singolo pass. 

Gli sviluppatori possono operare con Portafoglio in diversi modi:

* Per creare un pass non è necessario compilare l'applicazione. Un Passfile è un archivio compresso che contiene alcuni file JSON e file di metadati facoltativi. Per prepararlo sono necessari un [ID del tipo di pass](~/ios/platform/passkit.md) e un [certificato per il pass](~/ios/platform/passkit.md). Queste informazioni vengono quindi dichiarate in un file JSON. Altre informazioni sul provisioning di un Passfile sono disponibili nella guida [Introduction to PassKit](~/ios/platform/passkit.md) (Introduzione a PassKit).

* Per distribuire pass è possibile scrivere app complementari che includono anche le funzionalità necessarie per creare, modificare e aggiornare i pass e per aggiungerli quindi all'app Portafoglio. Un buon esempio di questo tipo di app è un'app per il cinema: dopo che un utente ha acquistato un biglietto tramite l'app, questo potrà essere aggiunto a Portafoglio direttamente dall'app. Per usare un'app complementare, il profilo di provisioning deve includere un ID app con le funzionalità Portafoglio che è possibile impostare seguendo i passaggi indicati di seguito. L'app deve includere anche gli entitlement necessari.

* Le app Conduit sono app che non modificano direttamente i pass. A parte la possibilità di ricevere il pass e offrire all'utente l'opzione di aggiungerlo all'app Portafoglio, l'interazione di queste app con il pass è minima. Non necessitano di provisioning o entitlement speciali, ma usano alcuni metodi del framework di PassKit.

## <a name="developer-center"></a>Centro per sviluppatori

Per creare un nuovo profilo di provisioning per l'uso con Portafoglio, seguire questa procedura:

1. Passare alla sezione [Certificates, Identifiers & Profiles](https://developer.apple.com/account/ios/certificate/) (Certificati, identificatori e profili) del portale per sviluppatori Apple.
2. In **Identifiers** (Identificatori) passare a **App IDs** (ID app): 
    
    ![Selezione di un ID app](wallet-capabilities-images/image17.png)

3. Fai **+** clic sull'icona in alto a destra nella pagina.
4. Registrare un nuovo ID app assegnandogli un valore nel campo **Name** (Nome) e un identificatore del bundle. Si noti che questo identificatore del bundle deve corrispondere all'ID bundle presente nel progetto:
   
    ![Aggiungere i dettagli dell'ID app](wallet-capabilities-images/image18.png)

5. Selezionare il servizio app **Wallet** (Portafoglio) dall'elenco dei servizi:
    
    ![Schermata di selezione del servizio](wallet-capabilities-images/image19.png)

6. Premere **Continue** (Continua) e quindi **Register** (Registra) per creare l'ID app.

Se necessario, è possibile modificare gli ID app esistenti per aggiungere la funzionalità Portafoglio.

Questo ID app può ora essere usato per generare o rigenerare un nuovo profilo di provisioning, come descritto nella guida [Working with Capabilities](~/ios/deploy-test/provisioning/capabilities/index.md) (Uso delle funzionalità):

![Uso del nuovo ID app per creare un profilo di provisioning](wallet-capabilities-images/image20.png)

Per altre informazioni sull'uso di Portafoglio, vedere le guide seguenti:

* [Introduction to PassKit](~/ios/platform/passkit.md) (Introduzione a PassKit)

## <a name="next-steps"></a>Passaggi successivi

Nell'elenco seguente vengono descritti i passaggi aggiuntivi che potrebbero essere necessari:

* Usare lo spazio dei nomi del framework nell'app.
* Aggiungere all'app gli entitlement necessari. Per informazioni dettagliate sugli entitlement necessari e su come aggiungerli, vedere la guida [Uso degli entitlement](~/ios/deploy-test/provisioning/entitlements.md).
* In **Firma del bundle iOS** dell'app assicurarsi che **Entitlement personalizzati** sia impostato su **Entitlements.plist**. Questa _non_ è l'impostazione predefinita per le build di debug e del simulatore iOS.

In caso di problemi con i servizi app, vedere la sezione [Troubleshooting](~/ios/deploy-test/provisioning/capabilities/index.md) (Risoluzione dei problemi) della guida principale.
