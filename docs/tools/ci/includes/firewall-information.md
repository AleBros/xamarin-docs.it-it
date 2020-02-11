---
ms.openlocfilehash: d46968f9c53314abe561e7f4871cfbf6e07b7002
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "61047857"
---
## <a name="firewall-configuration"></a>Configurazione del firewall

Per poter inviare i test a Xamarin Test Cloud, il computer che invia i test deve essere in grado di comunicare con i server Test Cloud. I firewall devono essere configurati per consentire il traffico di rete da e verso i server che si trovano in **testcloud.Xamarin.com** sulle porte 80 e 443. Questo endpoint è gestito da DNS e l'indirizzo IP è soggetto a modifiche. 

In alcune situazioni, un test (o un dispositivo che esegue il test) deve comunicare con i server Web protetti da un firewall. In questo scenario è necessario configurare il firewall per consentire il traffico dagli indirizzi IP seguenti:

* **195.249.159.238**
* **195.249.159.239**
