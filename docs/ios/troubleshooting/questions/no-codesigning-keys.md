---
title: "Perché la compilazione iOS non riesce con l'errore: nessuna chiave di firma del codice iPhone valida trovata nel keychain?"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 0c777b8d5326963e959d8bb13d81d7058caa6bde
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030942"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>Perché la compilazione iOS non riesce con l'errore: nessuna chiave di firma del codice iPhone valida trovata nel keychain?

## <a name="cause-of-the-error"></a>Motivo dell'errore

Questo messaggio di errore viene visualizzato quando il progetto in questione cerca le credenziali di firma codice valide ma non le trova. La firma del codice è necessaria per i test e le distribuzioni nei dispositivi iOS fisici. così come le compilazioni di & App Store ad hoc.

### <a name="provisioning-devices"></a>Provisioning dei dispositivi

Se non è stato effettuato il provisioning di un dispositivo iOS in precedenza, nella Guida seguente viene illustrato il processo dettagliato: Guida al [provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md)

## <a name="bug-when-using-ios-simulator"></a>Bug quando si usa il simulatore iOS

> [!NOTE]
> Questo problema è stato risolto nelle versioni recenti di Novell per Visual Studio. Tuttavia, se il problema si verifica nella versione più recente del software, inserire un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con le informazioni complete sul controllo delle versioni e l'output del log di compilazione completo.

Si è verificato un bug in Xamarin.Visual Studio 3,11 che ha causato il progetto iOS in un modello Xamarin.Forms per aggiungere i diritti di coprogettazione. plist alle compilazioni del simulatore; blocco efficace del test tramite il simulatore.

### <a name="how-to-fix"></a>Correzione

È possibile risolvere il problema rimuovendo il flag di `<CodesignEntitlements>` dalle compilazioni di debug nel file con estensione csproj. Procedere come segue:

> [!WARNING]
> Gli errori nei file con estensione csproj possono interrompere il progetto, quindi è consigliabile eseguire il backup dei file prima di provare a eseguire questa operazione.

1. Fare clic con il pulsante destro del mouse sul progetto iOS nel riquadro della soluzione e selezionare **Scarica progetto**
2. Fare nuovamente clic con il pulsante destro del mouse sul progetto e scegliere **modifica [NomeProgetto]. csproj**
3. Individuare il PropertyGroups di debug, che dovrebbe iniziare con i flag che hanno un aspetto simile al seguente:
   - Debug: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - Versione: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. In ognuna delle compilazioni che usano il simulatore, eliminare o impostare come commento la proprietà seguente: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. Ricaricare il progetto e dovrebbe essere possibile eseguire la distribuzione nel simulatore.

### <a name="next-steps"></a>Passaggi successivi
Per ulteriore assistenza, per contattarci o se il problema persiste anche dopo aver utilizzato le informazioni sopra riportate, vedere [quali sono le opzioni di supporto disponibili per Novell?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché su come archiviare un nuovo bug se necessario .
