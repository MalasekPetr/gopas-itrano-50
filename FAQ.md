# FAQ z chatu — IT Ráno #50

## Technické prerekvizity

**Q: Je potřeba nejdřív zapnout Custom Apps v Teams?**
A: Ano — bez povoleného Custom App Upload Toolkit agenta neprovisionuje. Teams Admin Center → Teams apps → Setup policies → {Policy} → povolit nahrávání vlastních aplikací. Stav vidíte přímo v Toolkit panelu jako zelený checkmark.

**Q: Jak zapadá Copilot addon k M365?**
A: Copilot addon (M365 Copilot Premium) je placená licence navíc k existujícímu M365 plánu. PAYG je alternativa pro uživatele bez tohoto addonu — platíte za skutečnou spotřebu, ne za sedadla. Viz [Azure Pricing — Copilot Studio](https://azure.microsoft.com/en-us/pricing/details/copilot-studio/) a [Set up Microsoft 365 Copilot pay-as-you-go](https://learn.microsoft.com/en-us/microsoft-365/copilot/pay-as-you-go/setup).

---

## SharePoint a data

**Q: Když přidám do SharePointu nový soubor, jak dlouho trvá než ho agent zahrne do odpovědí?**
A: Microsoft Search indexuje SPO průběžně — typicky 5 minut až několik hodin pro nový soubor. Při změně existujícího souboru bývá reindex rychlejší.

**Q: Chybí vám v knihovně metadata? Nezlepšila by výsledky?**
A: Ano, metadata relevanci zlepšují — Microsoft Search je využívá při rankingu výsledků a Retrieval API s nimi aktivně pracuje. V ukázce jsem je záměrně vynechal aby byl příklad co nejjednodušší. V produkčním nasazení doporučuji přidat co nejvíce metadat.

**Q: Agent najde jen prvních 10 výsledků — co když je relevantních 20?**
A: Toto je omezení Retrieval API vrstvy Microsoft Search. Částečně to řeší přesnější dotaz nebo lepší metadata pro ranking. Plná kontrola nad chunkingem a rankingem je dostupná jen v pro-code přístupu přes vlastní RAG pipeline.

---

## Srovnání s jinými nástroji

**Q: Jaký je rozdíl mezi deklarativním agentem a SharePoint agentem?**
A: SharePoint agent je point-and-click přímo v SPO — nula konfigurace, ale taky nula flexibility. Především ale jen jeden zdroj. Deklarativní agent vám dá plnou kontrolu nad instrukcemi, více datovými zdroji, nasazením přes organizační katalog a třeba i verzování v Gitu. Struktura JSON je podobná — deklarativní agent je v podstatě jeho rozvinutá verze.

**Q: Používá se lepší embedding model než v Copilot Studiu?**
A: Oba nástroje využívají Microsoft Search a stejné modely na pozadí. Rozdíl není v modelu ale v tom kolik máte kontroly nad retrieval pipeline.

**Q: Lze deklarativního agenta konvertovat na custom engine agenta?**
A: Ano — Microsoft to přímo podporuje. Viz [Convert your declarative agent to a custom engine agent](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/convert-declarative-agent). Zachová se logika, přidáte vlastní orchestraci.

---

## Nasazení a přístup

**Q: Je složité zpřístupnit agenta na webu?**
A: Technicky ne — ale M365 Copilot Chat je primárně určen pro přihlášené uživatele. Na veřejném webu narazíte nejprve na problém s autentizací. Pro veřejný přístup bez přihlášení je vhodnější Azure Bot Service nebo pro-code řešení.

---

## MS-4010 kurz

**Q: Je v MS-4010 vyžadován JavaScript nebo C#?**
A: Pro část s deklarativními agenty ne — stačí základní orientace v JSON. Pro moduly s API pluginy a custom engine agenty se TypeScript hodí, ale není podmínkou porozumění obsahu. Kurz je stavěný pro začátečníky.

---

## PAYG (Pay-As-You-Go)

**Q: Kde v M365 Admin Center PAYG nastavím?**
A: Microsoft 365 Admin Center → Copilot → Settings → View all → Copilot pay-as-you-go billing. Potřebujete propojený Azure subscription (sám o sobě nic nestojí, je to jen skořápka).

**Q: Co se stane když uživatel překročí limit?**
A: Dotazy se zablokují do dalšího měsíce nebo do navýšení limitu adminem. Nic se nezúčtuje navíc bez vědomého zásahu.

**Q: Může mít jeden uživatel PAYG i Premium licenci zároveň?**
A: Může, ale je to k ničemu. Nebude se mu PAYG spotřeba počítat — Premium licence má přednost. PAYG je fallback pro uživatele bez Premium.

**Q: Jak přesně se 250 dotazů promítne do ceny?**
A: Záleží na modelu a typu dotazu — přibližně $0.10-0.30 per message v závislosti na délce kontextu. Pro přesnou kalkulaci viz Azure Pricing Calculator, služba "Microsoft 365 Copilot metered" viz odkaz výše.

**Q: Funguje PAYG i pro Copilot Studio agenty?**
A: Ano, ale Copilot Studio má vlastní PAYG model (Messages). Dnešní ukázka se týkala výhradně M365 Copilot Chat s deklarativními agenty.

---

## Deklarativní agenti a Agent Toolkit

**Q: Jaký je rozdíl mezi SharePoint agentem a deklarativním agentem?**
A: SharePoint agent je point-and-click přímo v SPO — nula konfigurace, ale nula flexibility, jen jeden zdroj dat. Deklarativní agent vám dá plnou kontrolu nad instrukcemi, datovými zdroji i nasazením.

**Q: Musím umět programovat?**
A: Pro deklarativního agenta bez akcí ne — editujete JSON a systémový prompt. GitHub Copilot zvládne napsat 90 % za vás. Pro akce (API pluginy, Power Automate) se hodí základní znalost TypeScriptu, ale není to podmínkou pro pochopení konceptů.

**Q: Může agent přistupovat k více SPO knihovnám najednou?**
A: Ano — v capabilities lze přidat více items_by_url záznamů.

**Q: Jak agent ví co má odpovědět — kde běží AI?**
A: AI běží v Microsoft cloudu, přes váš M365 tenant. Agent definuje jak se má chovat a kde hledat data. Samotný model je aktuálně GPT-4 na straně Microsoftu.

**Q: Může agent zapisovat do SharePointu, nebo jen číst?**
A: Deklarativní agent bez akcí pouze čte přes Microsoft Search. Pro zápis potřebujete akci (API plugin nebo Power Automate connector) — to je ale téma na MS-4010.

**Q: Jak řeším oprávnění — kdo vidí agenta?**
A: Tři úrovně: Share link (konkrétní uživatelé), Publish to Organization (všichni v org katalogu po schválení adminem), nebo distribuce přes Teams Admin Center policy.

**Q: Je agent dostupný na mobilu?**
A: Ano — M365 Copilot Chat běží i v mobilní appce.

---

## Obecné

**Q: Lze dnes deklarativního agenta nasadit na web nebo do jiné aplikace mimo M365?**
A: Přímo ne — deklarativní agent žije v M365 Copilot Chat. Pro embedding do vlastní aplikace nebo webu potřebujete pro-code přístup přes Agents SDK.

**Q: Do kolika úrovní webu se dostane WebSearch capability?**
A: Agent neprochází web jako crawler — využívá Bing Search API a přistupuje přímo k nalezeným stránkám. Neexistuje pevný limit úrovní.

---

## Compliance a správa

**Q: Kde jsou uloženy konverzace s agentem?**
A: V Microsoft 365 compliance úložišti stejně jako ostatní Copilot konverzace — podléhají eDiscovery a retention policies - EDP obecně.

**Q: Jak verzuji změny agenta?**
A: declarativeAgent.json a další jsou textové soubory — Git, PR, CI/CD. Každá změna je auditovatelná.

**Q: Může agent přistoupit k datům jiného tenanta?**
A: Ne — SPO grounding je omezeno na váš tenant. Přeshraniční přístup vyžaduje pro-code řešení s explicitním delegováním.

**Q: Co je env/.env.dev.user a proč nesmí být v Gitu?**
A: Obsahuje TEAMS_APP_ID a tenant-specifické secrets generované při provision. Commitnutí by znamenalo veřejně dostupné identifikátory vaší M365 aplikace.
