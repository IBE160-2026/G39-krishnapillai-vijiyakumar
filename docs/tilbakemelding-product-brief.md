# Tilbakemelding på product brief

| | |
|---|---|
| **Gruppe** | G39 – G39-krishnapillai-vijiyakumar |
| **Product brief** | `docs/product-brief.md` (commit `69b3d48`), med `docs/addendum.md` |
| **Tilbakemelding fra** | Faglærer i IBE160 (utarbeidet med KI-støtte) |
| **Dato** | 2026-10-06 |

## Samlet vurdering

- **Godt utgangspunkt med justeringer.** Gruppen kan gå videre og innarbeide punktene under.

**Det som er bra:**

1. Dere har avgrenset MRP II-forslaget til én modul (4.1 Prognoser og Demand Management) og valgt etterprøvbare metoder (SES, IQR/Z-score, standardavvik) i stedet for ARIMA eller LSTM. Begrunnelsen med «white-box»-prinsippet, at controlleren skal kunne etterprøve prognosen, er faglig god og gjør det mulig å kontrollere Claude Codes kode.
2. Scope har tydelige IN- og OUT-lister og forkastede alternativer med begrunnelse. Kjerneflyten i grensesnittet – velg varelinje, se historikk mot tre scenarioer, se MAPE, juster sikkerhetsmargin – er konkret nok til å bygge stories på.

**De viktigste endringene:**

1. Kontroller regneeksempelet. 10 % reduksjon av 15 MNOK er 1,5 MNOK, mens 1,2 MNOK er 8 % av 15 MNOK. Gjør tydelig hva «10 % reduksjon i unødvendig sikkerhetslager» regnes av. Når tallet skal være et suksesskriterium som scenariomotoren reproduserer, må formelen være entydig.
2. Legg til funksjonelle suksesskriterier som kan bli testtilfeller, for eksempel «en kjent outlier i testdatasettet blir flagget og glattet», «SES-prognosen for en testserie stemmer med en håndregnet verdi» og «når kampanjeflagget settes, øker normalscenarioet med X». Dagens kriterier er mest forretnings- og ytelsesmål.
3. MAPE-målet (≥15 % bedre enn glidende gjennomsnitt) måles på et syntetisk datasett som dere lager selv. Beskriv hvordan datasettet lages (trend, sesong, støy, innlagte outliers), slik at målet ikke blir oppfylt bare fordi dataene er laget for det.

## Vanskelighetsgrad og gjennomførbarhet

### Vurdert vanskelighetsgrad

- **Middels**

**Sammenlignbart med:** 4) KI-støttet MRP II, avgrenset til modul 4.1 Prognoser og Demand Management. Én modul av et vanskelig forslag, med statistisk prognoselogikk, ligger på middels nivå.

**Begrunnelse:**

| Faktor | Nivå (lav / middels / høy) | Kommentar |
|---|---|---|
| Domenelogikk – hvor mange og hvor kompliserte regler og beregninger må stemme? | Middels | SES, outlier-deteksjon, standardavviksbaserte scenarioer, kampanjejustering og MAPE. Velkjente formler, men flere beregninger som må henge sammen riktig. |
| Datamodell – antall entiteter og relasjoner mellom dem | Lav | Varelinje, salgshistorikk per periode, kampanjeflagg og beregnede prognoser. |
| Brukere, roller og innlogging | Lav | Ingen autentisering i v1. |
| KI-funksjonalitet i appen, f.eks. kall til språkmodell, prompts i koden og håndtering av usikre svar | Lav | «AI-støttet» betyr her statistiske prognosemetoder, ikke språkmodell. Det er et fornuftig valg, men si det tydelig i briefen. |
| Integrasjoner og eksterne tjenester, f.eks. API-er, betaling og e-post | Lav | Ingen ERP-integrasjon. Dere tilbyr et eget REST-API, men er ikke avhengige av eksterne tjenester. |
| Sanntid, samtidighet eller flere brukere som påvirker hverandre | Lav | Ikke relevant. |
| Filhåndtering, f.eks. opplasting, PDF-lesing og eksport | Middels | «Laster inn historisk salgsdata» krever en definert filstruktur (for eksempel CSV) og validering. |
| Sikkerhet og personvern | Lav | Syntetiske data uten personopplysninger. |

**Hva vanskelighetsgraden betyr for dere:**

- _Middels:_ Et godt balansert valg. Pass på at kjerneflyten – last inn data, beregn prognose og scenarioer, vis graf og MAPE – blir ferdig og stabil før dere legger til mer, som belastningstester for 10 000 varelinjer.

### Gjennomførbarhet med BMAD og Claude Code

Dere skal planlegge med BMAD (product brief → PRD → arkitektur → epics og stories) og implementere med Claude Code. Vurderingen under tar hensyn til at det må være tid til hele denne flyten, og til testing, retting og README til slutt.

| Spørsmål | Vurdering (OK / risiko / stor risiko) | Kommentar |
|---|---|---|
| **Tid og omfang** – kan v1 realistisk bli ferdig og stabil i løpet av semesteret, med tid til flere iterasjoner? | OK | Fem tydelige IN-punkter er et realistisk omfang for to personer. Det haster litt å komme i gang med PRD, siden repoet ennå bare har brief. |
| **BMAD-flyten** – er briefen konkret nok til at PRD, arkitektur og stories kan lages uten store hull, og blir det overkommelig mange stories? | OK | Briefen er konkret. Det som mangler er input-formatet for salgsdata og hvordan kampanjejusteringen beregnes. |
| **Egnet for Claude Code** – bruker løsningen en vanlig, godt dokumentert teknologistakk som Claude Code håndterer godt, eller krever den nisjeteknologi, spesialmaskinvare eller mye manuell konfigurasjon? | OK | Python/Pandas, FastAPI og React/TypeScript er godt dokumentert og passer Claude Code godt. Vurder om to språk er nødvendig, eller om et enklere grensesnitt holder. |
| **Kontroll på KI-ens arbeid** – kan gruppen selv avgjøre om koden gjør det riktige? Krever domenet kunnskap gruppen ikke har, f.eks. avanserte beregninger eller fagregler, så er det vanskelig å kvalitetssikre. | Risiko | SES og MAPE kan regnes for hånd eller i Excel for en kort serie. Lag slike fasitserier før implementeringen, ellers er det vanskelig å avsløre små feil i Claude Codes formler (for eksempel feil startverdi eller feil fortegn i scenarioene). |
| **Testbarhet** – finnes det tydelige regler og forventede resultater som tester kan skrives mot? | OK | Formlene er deterministiske og godt egnet for enhetstester, når fasitverdiene er laget. |
| **Kjørbar for sensor** – kan appen kjøres lokalt etter README, uten gruppens nøkler, betalte kontoer eller egen infrastruktur? | OK | Ingen nøkler eller kontoer. Legg det syntetiske datasettet i repoet. |
| **Avhengigheter og kostnader** – krever løsningen betalte API-er, f.eks. språkmodeller, og finnes det en plan for kostnad, testmodus eller mock-data? | OK | Ingen betalte tjenester. |

**Konklusjon om gjennomførbarhet:**

- **Gjennomførbart som beskrevet.**

**Forslag til justering av omfang eller vanskelighetsgrad:**

1. Ikke undervurder grensesnittet. Briefen sier at frontenden bare er med «fordi den er et krav fra emnet», men design og brukeropplevelse teller i vurderingen. Planlegg grafen, scenariovisningen og justering av sikkerhetsmargin som en like viktig del av v1 som API-et.
2. Flytt ytelseskravet «10 000 varelinjer på under 1 sekund» til et senere trinn, og prioriter korrekthet med fasitserier først.

## Hvorfor product brief er viktig for mappen

Product brief er utgangspunktet for PRD, arkitektur, stories og til slutt koden. Del 1 av mappen vurderes blant annet på om sensor kan følge en sporbar vei fra plan til ferdig app. Den vurderes også på om appen gjør det dere har beskrevet, om den er testet, om den er godt designet, og om den kan kjøres etter README. Et uklart, for stort eller for lite brief gjør alt dette vanskeligere senere. Det er mye enklere å rette nå enn sent i semesteret.

## 1. Gjennomgang av briefens deler

| Del av brief | Status | Kommentar |
|---|---|---|
| Executive Summary – er det klart hva appen er, og hvilket problem den løser? | Juster | Klart hva systemet gjør. Regneeksempelet (15 MNOK, 10 %, 1,2 MNOK) går ikke opp slik det står og bør rettes. |
| The Problem – er problemet konkret, med reelle situasjoner og brukere? | OK | Månedsslutt i Excel, outliers som drar glidende gjennomsnitt opp, og begrensningene i Power BI er konkret beskrevet. |
| The Solution – beskriver løsningen brukeropplevelsen, ikke bare teknologi? | OK | Beskriver hva controlleren ser og gjør i grensesnittet. Teknologien er riktig lagt i en egen merknad. |
| What Makes This Different – er vurderingen ærlig og realistisk? | OK | Ærlig om at metodene er kjente, og at fordelen er utførelse og transparens. |
| Who This Serves – er primærbrukerne tydelige, og vet vi hva de trenger? | OK | Controller/innkjøper som primærbruker og Supply Chain Manager som sekundær er tydelig. |
| Success Criteria – kan kriteriene faktisk sjekkes eller testes? | Juster | Målbare, men mest forretnings- og ytelsesmål. Legg til funksjonelle kriterier med fasit, og beskriv det syntetiske datasettet. |
| Scope – er det klart hva som er med i første versjon, og hva som ikke er det? | OK | Tydelig IN/OUT og gode forkastede alternativer. Input-format for salgsdata bør med. |
| Vision – henger visjonen sammen med resten uten å blåse opp omfanget? | OK | EOQ og ledetider ligger tydelig etter v1. |

## 2. Utgangspunkt for del 1 av mappen

Punktene følger kriteriene i sensorveiledningen for del 1. Vektene i parentes viser hvor mye hvert kriterium teller i del 1.

| Kriterium i del 1 | Hva briefen bør legge til rette for | Status | Kommentar |
|---|---|---|---|
| **1. Prosess og KI-styring** (30 %) | Brief som er presis nok til at PRD og stories kan bygges direkte på den, slik at krav kan spores fra brief til kode. | OK | Briefen og addendumet dokumenterer beslutninger godt. Fortsett med PRD og arkitektur i `docs/`. |
| **2. Funksjonalitet og omfang** (20 %) | Realistisk omfang for gruppen og semesteret: en tydelig kjerneflyt som kan bli ferdig og stabil, og nok innhold til å vise reell funksjonalitet. | OK | Realistisk omfang med reell beregningslogikk. |
| **3. Kvalitetssikring og testing** (15 %) | Suksesskriterier og funksjoner som er konkrete nok til å bli testtilfeller. | Juster | Lag fasitserier for SES, outlier-glatting, scenarioer og MAPE, og skriv dem inn som kriterier. |
| **4. Design og brukeropplevelse** (10 %) | Tydelige brukere og brukssituasjoner som designet kan bygges rundt, gjerne med de viktigste skjermbildene eller flytene skissert. | Juster | Brukeren er tydelig, men frontenden omtales som et pliktpunkt. Skisser varelinjevalg, graf og sikkerhetsmarginjustering. |
| **5. Kodekvalitet og arkitektur** (10 %) | Teknologivalg som er begrunnet og ikke mer komplekse enn appen trenger. | OK | Ryddig skille mellom beregning, API og visning. Hold beregningsmodulen fri for API-kode, slik at den kan testes alene. |
| **6. README og kjørbarhet** (10 %) | Løsning som andre kan kjøre lokalt uten betalte kontoer, og uten tilgang til gruppens egne tjenester og nøkler. | OK | Ingen betalte avhengigheter. README må dekke både Python- og Node-oppsett. |
| **7. Ryddighet i repoet** (5 %) | En plan for hvor hemmeligheter, testdata og dokumentasjon skal ligge. | Juster | Bestem en egen mappe for syntetiske datasett og fasitserier, og hold dem adskilt fra koden. |

## 3. Neste steg for gruppen

1. Rett regneeksempelet for kapitalbinding, og skriv formelen inn i addendumet slik at den kan testes.
2. Lag det syntetiske datasettet og to–tre korte fasitserier regnet for hånd, og legg til funksjonelle suksesskriterier basert på dem.
3. Gå videre til PRD og definer input-formatet for salgsdata og kampanjeflagg.

Oppdater product brief i repoet når dere har gjort endringene, slik at historikken viser hvordan planen utviklet seg. Det er en del av prosessen sensor ser etter.
