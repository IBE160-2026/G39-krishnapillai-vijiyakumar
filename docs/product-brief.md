# Product Brief: AI-støttet MRP II — Prognoser og Demand Management (Modul 4.1)

## Executive Summary

For controllere og innkjøpere som i dag styrer varelager gjennom Excel og Power BI, bygger vi et system som gir transparente, scenario-baserte etterspørselsprognoser i stedet for ett usikkert punktestimat. Systemet tar inn historiske salgstall, sesongvariasjoner, kampanjer og innestående kundebestillinger, og returnerer en optimistisk, normal og pessimistisk prognose per varelinje — matematisk etterprøvbar, ikke en sort boks.

Problemet i dag er ikke mangel på data, men mangelen på systemer som raskt og skalerbart oversetter data til beslutninger. Selskaper taper marginer på to fronter: kapitalbinding i overlager når prognosene er for høye, og tapte salg når de er for lave. For en mellomstor grossist med 15 MNOK i lagerverdi og 8% kapitalkostnad, betyr selv en 10% reduksjon i unødvendig sikkerhetslager en frigjøring av rundt 1,2 MNOK i arbeidskapital (se addendum for forutsetninger og kilder). Dagens Excel- og Power BI-baserte løsninger krever timevis med manuelt vedlikehold hver månedsslutt og kan ikke skrive beregnede prognoser tilbake til et forsyningssystem.

Nå er tiden riktig fordi de statistiske metodene som løser dette (eksponentiell utjevning, outlier-deteksjon) er velkjente og raske å implementere med moderne verktøy — det som tidligere krevde et større IT-prosjekt, kan nå bygges som en avgrenset, bevisbar MVP.

## The Problem

Controllere og innkjøpere opplever evig brannslukking. Dagens arbeidsflyt er å trekke rådata fra et ERP-system, lime det inn i Excel, og forsøke å glatte ut historikken manuelt. Når et unormalt stort salg (en outlier) inntreffer, drar enkle glidende gjennomsnitt neste måneds prognose kunstig høyt opp. Å identifisere og isolere slike outliers, og justere for kommende kampanjer og sesongsvingninger, krever kontinuerlig manuelt arbeid — typisk flere timer per månedsslutt for en portefølje på noen hundre varelinjer.

Power BI og DAX kan visualisere problemet, men er lese- og visualiseringsverktøy: de kan ikke skrive beregnede prognoser tilbake til et forsyningssystem, og er ikke bygget for iterative, tilstandsavhengige beregninger på tvers av tusenvis av varelinjer.

Konsekvensen er dobbel og målbar: bedriften binder kritisk arbeidskapital i unødvendig overlager basert på falske etterspørselssignaler, og taper omsetning fordi systemet ikke reagerer raskt nok på reelle trender. Skjer ingenting, fortsetter denne kapitalkostnaden og risikoen for stockout å vokse i takt med sortimentets størrelse.

## The Solution

Brukeren laster inn historisk salgsdata og får umiddelbart tre prognosescenarioer per varelinje — optimistisk, normal, pessimistisk — i stedet for ett tall å stole blindt på. Store avvik mellom historikk og modell (outliers) fanges opp og glattes automatisk, slik at controlleren ikke trenger å lete etter dem manuelt. Kampanjeperioder kan flagges, og prognosen justeres deretter.

Resultatet vises i et enkelt grensesnitt der brukeren velger en varelinje, ser historikk mot de tre scenarioene i en graf, ser hvor stort avviket (MAPE) har vært historisk, og kan justere sikkerhetsmarginen for å se hvordan scenarioene endrer seg. Alt er tilgjengelig samtidig som et API, slik at et fremtidig MRP- eller innkjøpssystem kan konsumere prognosene direkte — noe dagens Excel- og Power BI-løsninger ikke kan.

*Teknisk tilnærming (for denne leveransen): Python/Pandas for beregning, FastAPI som API-lag, et lett React/TypeScript-grensesnitt for visning. Se Scope for grensene på hva som bygges nå versus senere.*

## What Makes This Different

Det finnes ingen proprietær algoritme her — metodene (eksponentiell utjevning, glidende gjennomsnitt, standardavvik) er offentlig kjent og etterprøvbare. Fordelen er *hvordan* dette settes sammen, ikke en hemmelig modell:

| Alternativ i dag | Hvorfor brukere tolererer det | Hvorfor vår tilnærming er bedre |
|---|---|---|
| Excel-modeller | Kjent verktøy, lav inngangsbarriere | Knekker på volum, manuell gjeninnlesing hver runde, ingen skalerbar iterasjonslogikk |
| Power BI / DAX | God visualisering, allerede utbredt i bedriften | Kan ikke skrive beregnede prognoser tilbake til et forsyningssystem; DAX er ikke bygget for tilstandsavhengige beregninger på tusenvis av varelinjer |
| Manuell vurdering ("magefølelse") | Krever ingen verktøy, kjenner varene | Ikke skalerbart, ikke etterprøvbart, ingen sporbarhet ved avvik |

Fordelen vi kan forsvare er *utførelse*: rask, transparent og skrivbar orkestrering av velkjent forretningslogikk — en fordel som holder frem til noen andre bygger det samme, ikke en varig teknisk voll.

**Prinsipp for grensen mellom automatikk og menneskelig skjønn:** Systemet skal aldri overstyre brukerens beslutning — det beregner og forklarer scenarioer og avvik, men bestillingsbeslutningen tas alltid av controlleren. Dette holder systemet i tråd med hvordan controllere faktisk vil stole på et beslutningsstøtteverktøy: som et transparent forslag, ikke en automatisk handling.

## Who This Serves

Primær bruker: **controlleren eller innkjøperen** som i dag bruker flere timer hver månedsslutt på å glatte ut og korrigere prognoser i Excel. De trenger et system som håndterer normalen automatisk og bare krever deres oppmerksomhet ved reelle unntak. Suksess for dem er at denne månedlige jobben går fra timer til minutter, og at de kan forklare *hvorfor* en prognose ser ut som den gjør — ikke bare akseptere den.

Sekundær bruker: **Supply Chain Manageren** som skal dimensjonere sikkerhetslager og trenger å se worst-case og best-case, ikke bare ett punktestimat.

## Success Criteria

**Brukerutfall:** Tiden det tar å generere og forstå en ny rullerende prognose går fra timer (Excel) til sekunder, verifisert ved tidsstempling av en full kjøring for 500+ varelinjer.

**Kvalitet:** Modellen har lavere MAPE enn et naivt glidende gjennomsnitt på det syntetiske testdatasettet (mål: ≥15% relativ forbedring — se addendum for grunnlaget for dette tallet).

**Forretning/oppdrag:** Den beregnede kapitalbindingsgevinsten (jf. Executive Summary: ~1,2 MNOK frigjort ved 10% lagerreduksjon, gitt 15 MNOK lagerverdi og 8% kapitalkostnad) reproduseres som en konkret output fra scenariomotoren på det syntetiske datasettet — forretningscasen skal kunne testes, ikke bare påstås.

| Signal | Metrikk/bevis | Mål | Måles når |
|---|---|---|---|
| Brukerutfall | Tidsbruk pr. rullerende prognose | Timer → sekunder | Ved kjøring på 500+ varelinjer |
| Kvalitet | MAPE vs. naivt glidende gjennomsnitt | ≥15% relativ forbedring | På syntetisk testdatasett |
| Forretning | Frigjort arbeidskapital (modellert) | ~1,2 MNOK ved 10% lagerreduksjon | Reprodusert fra scenariomotoren |
| Teknisk | Prosesseringstid, 10 000 varelinjer | <1 sekund | Belastningstest av API |

## Scope

**IN — første versjon:**
1. Python/Pandas backend som identifiserer og glatter outliers (IQR/Z-score) og beregner SES-baserte prognoser.
2. Beregning av tre scenarioer (optimistisk/normal/pessimistisk) basert på standardavvik og sikkerhetsmargin, med enkel kampanjeflagg-justering.
3. REST API (FastAPI, OpenAPI-dokumentert) som eksponerer prognosene.
4. Ett enkelt React/TypeScript-grensesnitt: velg varelinje, se graf (historikk vs. scenarioer), se MAPE, juster sikkerhetsmargin.
5. Syntetisk datasett som beviser både prognosekvalitet og den modellerte kapitalbindingsgevinsten.

**OUT — ikke nå:**
1. Integrasjon mot faktiske live ERP-systemer (SAP, Dynamics).
2. Avanserte ML-modeller (ARIMA, nevralnettverk/LSTM).
3. Write-back-funksjonalitet som automatisk legger inn bestillinger.
4. Fullt sluttbruker-dashboard med brukerhåndtering, avviksflagging, drilldown og arbeidsflyt.
5. Multi-tenant/rollestyring, autentisering og produksjonsdrift.

**Scope-test:** Kjerneverdien — transparente, scenario-baserte prognoser med en etterprøvbar kapitalbindingsgevinst — kan bevises selv om punkt 4 (frontend) fjernes helt, ved å lese API-responsen direkte. Frontenden er likevel inne fordi den er et krav fra emnet, ikke fordi den er nødvendig for å bevise kjerneverdien.

**Forkastede alternativer:**
- *ARIMA/LSTM i stedet for SES:* Forkastet for MVP fordi de krever mer data og trening enn et syntetisk datasett realistisk kan gi, og fordi de bryter med white-box-prinsippet — en controller kan ikke etterprøve en LSTM-vekt for hånd slik hun kan med et glidende gjennomsnitt.
- *Full ERP-integrasjon i MVP:* Forkastet fordi det krever tilgang til et faktisk ERP-system og autentiseringsinfrastruktur som ikke er del av oppgavens kjerneleveranse — verdien (transparente prognoser) kan bevises uten det.
- *Automatisk write-back av bestillinger:* Forkastet bevisst, i tråd med prinsippet i "What Makes This Different" om at systemet foreslår, ikke handler — en reell write-back-funksjon krever langt strengere test- og sikkerhetskrav enn det en MVP kan dekke.

## Vision

**Neste bruk (etter denne leveransen):** Det fulle beslutningsgrensesnittet — brukerhåndtering, avviksflagging, drilldown, arbeidsflyt for controllere — bygges ut, og systemet blir det primære beslutningsstøtteverktøyet for bedriftens innkjøpsavdeling. Målet er å reprodusere den modellerte kapitalbindingsgevinsten i praksis: en reduksjon i unødvendig sikkerhetslager på 10–15% ved uendret eller bedre servicegrad, verifisert mot en pilotbedrifts faktiske lager- og salgsdata.

**2–3 år:** Modulen utvikles fra en passiv prognosemotor til et integrert, AI-støttet MRP II-system som ikke bare forutsier etterspørsel, men foreslår optimale bestillingskvantum (EOQ), tar høyde for leverandørers ledetider, og justerer for makroøkonomiske trender. Hver prognose, hvert avvik og hver manuell korreksjon en controller gjør bygger et treningsgrunnlag for mer avanserte modeller — samtidig som white-box-prinsippet beholdes som kjernedifferensiator.

---
*Se `addendum.md` for kildehenvisninger, forutsetninger bak tallene, og markedsbilde.*
