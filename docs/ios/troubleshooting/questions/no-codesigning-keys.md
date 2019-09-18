---
title: "Perché la compilazione iOS non riesce con l'errore: nessuna chiave di firma del codice iPhone valida trovata nel keychain?"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: e10a04627b903c02140a6a2ead5c379c1e8bdcf6
ms.sourcegitcommit: 13e43f510da37ad55f1c2f5de1913fb0aede6362
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71021384"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>Perché la compilazione iOS non riesce con l'errore: nessuna chiave di firma del codice iPhone valida trovata nel keychain?

## <a name="cause-of-the-error"></a>Motivo dell'errore

Questo messaggio di errore viene visualizzato quando il progetto in questione cerca le credenziali di firma codice valide ma non le trova. La firma del codice è necessaria per i test e le distribuzioni nei dispositivi iOS fisici. così come le compilazioni di & App Store ad hoc.

### <a name="provisioning-devices"></a>Provisioning dei dispositivi

Se non è stato effettuato il provisioning di un dispositivo iOS in precedenza, nella Guida seguente viene illustrato il processo dettagliato: [Guida al provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md)

## <a name="bug-when-using-ios-simulator"></a>Bug quando si usa il simulatore iOS

> [!NOTE]
> Questo problema è stato risolto nelle versioni recenti di Novell per Visual Studio. Tuttavia, se il problema si verifica nella versione più recente del software, inserire un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con le informazioni complete sul controllo delle versioni e l'output del log di compilazione completo.

Si è verificato un bug in Novell. Visual Studio 3,11 che ha causato il progetto iOS in un modello Novell. Forms per aggiungere i diritti di coprogettazione. plist alle compilazioni del simulatore; blocco efficace del test tramite il simulatore.

### <a name="how-to-fix"></a>Correzione

È possibile risolvere il problema rimuovendo il `<CodesignEntitlements>` flag dalle compilazioni di debug nel file con estensione csproj. Procedere come segue:

> [!WARNING]
> Gli errori nei file con estensione csproj possono interrompere il progetto, quindi è consigliabile eseguire il backup dei file prima di provare a eseguire questa operazione.

1. Fare clic con il pulsante destro del mouse sul progetto iOS nel riquadro della soluzione e selezionare **Scarica progetto**
2. Fare nuovamente clic con il pulsante destro del mouse sul progetto e scegliere **modifica [NomeProgetto]. csproj**
3. Individuare il PropertyGroups di debug, che dovrebbe iniziare con i flag che hanno un aspetto simile al seguente:
   - Debug`<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - Versione`<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. In ognuna delle compilazioni che usano il simulatore, eliminare o impostare come commento la proprietà seguente:`<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. Ricaricare il progetto e dovrebbe essere possibile eseguire la distribuzione nel simulatore.

### <a name="next-steps"></a>Fasi successive
Per ulteriore assistenza, per contattarci o se il problema persiste anche dopo aver utilizzato le informazioni sopra riportate, vedere [quali sono le opzioni di supporto disponibili per Novell?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché su come archiviare un nuovo bug se necessario .
