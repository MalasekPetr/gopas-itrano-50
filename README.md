# IT Ráno #50 — Copilot agenti ve VS Code: bez Copilot Studia

**Petr Malášek | MCT | Malach IS s.r.o.**  
*GOPAS IT Ráno #50 — duben 2026*

---

## O čem bylo vystoupení

Vlastní AI agent v Microsoft 365 nemusí znamenat platbu za Copilot Studio ani plnou M365 Copilot licenci pro každého uživatele. Stačí VS Code, Microsoft 365 Agents Toolkit a Pay-As-You-Go model.

Toto repo obsahuje zdrojový kód agenta ukázaného živě — **MCT Learning Prep** — deklarativního M365 Copilot agenta groundovaného v SharePoint knihovně s MCT courseware.

---

## Struktura projektu

```
appPackage/
  manifest.json          ← M365 app manifest (v1.19)
  declarativeAgent.json  ← Definice agenta: instrukce + SPO grounding
  color.png              ← Ikona 192×192 px
  outline.png            ← Ikona 32×32 px
env/
  .env.dev               ← Proměnné prostředí (bez secrets)
  .env.dev.user          ← Secrets — NENÍ v gitu
m365agents.yml           ← Lifecycle: provision + publish
m365agents.local.yml     ← Lifecycle: lokální vývoj
```

---

## Step-by-step: jak dosáhnout stejného výsledku

### Prerekvizity

- [VS Code](https://code.visualstudio.com/) (zdarma)
- Rozšíření **Microsoft 365 Agents Toolkit** (VS Code Marketplace, zdarma)
- M365 tenant s:
  - ✅ Custom App Upload povolen
  - ✅ Copilot Access enabled
  - Copilot licencí nebo aktivním **PAYG billing**
- Git (doporučeno)

### Krok 1 — Instalace Toolkitu

1. Otevři VS Code
2. Přejdi do **Extensions** (Ctrl+Shift+X)
3. Vyhledej `Microsoft 365 Agents Toolkit`
4. Klikni **Install**

Po instalaci se vlevo objeví ikona Toolkitu. V panelu Accounts se přihlas k M365 tenantovi.  
Ověř že vidíš ✅ **Custom App Upload Enabled** a ✅ **Copilot Access Enabled**.

### Krok 2 — Scaffold projektu

1. Klikni na ikonu Toolkitu vlevo
2. Vyber **Create a New Agent/App**
3. Zvol **Declarative Agent**
4. Zvol **No Action** ← žádný backend, žádný server
5. Zadej název: `mct-learning-prep`
6. Vyber cílovou složku

Toolkit vytvoří strukturu projektu za 20–30 sekund.

### Krok 3 — Uprav `declarativeAgent.json`

Otevři `appPackage/declarativeAgent.json` a nahraď obsah:

```json
{
  "$schema": "https://developer.microsoft.com/json-schemas/copilot/declarative-agent/v1.4/schema.json",
  "version": "v1.4",
  "name": "MCT Learning Prep",
  "description": "Pomáhá MCT lektorům najít relevantní kurzy, moduly a lekce v knihovně MCT courseware.",
  "instructions": "Jsi MCT Learning Prep, asistent pro Microsoft Certified Trainers při přípravě na kurzy. Tvoje znalosti jsou striktně omezeny na MCT courseware knihovnu ke které máš přístup — nepoužívej žádné informace mimo ni.\n\nKdyž lektor položí dotaz:\n1. Prohledej knihovnu a najdi relevantní kurzy, moduly a lekce.\n2. Uveď kód kurzu (např. MS-NNNN), číslo modulu a název lekce.\n3. Stručně popiš co lekce obsahuje — necituj obsah slidů doslova.\n4. Pokud téma pokrývá víc kurzů, uveď všechny relevantní.\n\nOdpovídej v jazyce lektora. Zaměř se na to KDE studovat, ne na reprodukci obsahu.\n\nDůležité: Knihovna obsahuje licencovaný Microsoft courseware. Nikdy necituj text slidů přímo.",
  "capabilities": [
    {
      "name": "OneDriveAndSharePoint",
      "items_by_url": [
        {
          "url": "https://{tenant}.sharepoint.com/sites/{site}/Shared%20Documents"
        }
      ]
    }
  ]
}
```

> ⚠️ Nahraď `{tenant}` a `{site}` svou SPO URL.

**Toto je celý "backend".** Microsoft Search indexuje SPO knihovnu, agent přes něj dotazuje dokumenty — žádný kód, žádný server.

### Krok 4 — Provision (jednou za projekt)

1. V Toolkit panelu vlevo klikni na **Lifecycle → Provision**
2. Vyber environment: **dev**
3. Počkej ~30–60 sekund

Toolkit provede 5 kroků:
- Vytvoří AAD app registration
- Zabalí app package
- Validuje manifest (57 pravidel)
- Nahraje do Developer Portalu
- Rozšíří na M365 přes `extendToM365`

Po úspěchu uvidíš v output panelu `SHARE_LINK` — přímý odkaz na agenta.

> 💡 Provision stačí jednou. Při každé další změně stačí **Publish to Organization**.

### Krok 5 — Test

Otevři v prohlížeči:
```
https://m365.cloud.microsoft/chat
```

Vlevo v seznamu agentů najdi **MCT Learning Prep** a otestuj dotazem:

> *"Which course covers declarative agents?"*

### Krok 6 — Publish to Organization

Po ověření funkčnosti:

1. Toolkit panel → **Lifecycle → Publish to Organization**
2. Agent se odešle do org katalogu
3. Admin musí schválit v M365 Admin Center → Teams apps

---

## Cost model: kdy použít co

| | Copilot Chat | **PAYG** | Copilot Studio | Pro-code SDK |
|---|---|---|---|---|
| Cena | součást M365 | jen spotřebované tokeny | od $200/měs. | Azure + tokeny |
| Vhodné pro | denní použití | sezónní, dev/test | citizen dev | plná kontrola |
| Kde žije | M365 Copilot Chat | M365 Copilot Chat | standalone + M365 | kdekoliv |
| Kód? | ne | ne | ne / low-code | TypeScript / C# |

**250 dotazů za měsíc v PAYG ≈ cena Premium licence.**  
Jak nastavit PAYG billing: [docs.microsoft.com/m365-copilot-payg](https://learn.microsoft.com/en-us/microsoft-365/admin/misc/copilot-for-microsoft-365-pay-as-you-go)

---

## Navazující kurzy (GOPAS)

- **[MS-4010](https://www.gopas.cz)** — Rozšíření M365 Copilot o deklarativní agenty pomocí VS Code
- **[MS-4022](https://www.gopas.cz)** — Rozšíření M365 Copilot v Copilot Studio  
- **[SPO-COPILOT](https://www.gopas.cz)** — Microsoft 365 Agents SDK a Copilot Extensions

---

## Compliance argument pro Git přístup

| | Power Platform / Copilot Studio | Git + Agent Toolkit |
|---|---|---|
| Audit trail | export ZIP, proprietární formát | `git log` — kdo, kdy, co, proč |
| Code review | neexistuje | Pull Request + 4-eyes princip |
| Rollback | manuální | `git revert` |
| ISMS / ISO 27001 | custom procesy | nativně v CI/CD pipeline |

> *"Další varianta citizen developmentu — pro ty, kteří preferují Git, verzování a vlastní deployment pipeline místo point-and-click."*

---

*Malach IS s.r.o. · [malachis.eu](https://malachis.eu)*
