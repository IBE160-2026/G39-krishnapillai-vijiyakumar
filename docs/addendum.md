# Addendum til Product Brief: AI-støttet MRP II

Dette dokumentet holder briefen ren og kort ved å samle forutsetninger, kildehenvisninger og forkastede alternativer separat, slik at beslutningsgrunnlaget er sporbart for sensor uten å tynge selve briefen.

## 1. Forutsetning bak kapitalbindingseksempelet

Executive Summary og Success Criteria bruker et regneeksempel: en mellomstor grossist med 15 MNOK i lagerverdi og 8% kapitalkostnad, hvor en 10% reduksjon i unødvendig sikkerhetslager frigjør ~1,2 MNOK i arbeidskapital og ~96 000 kr i årlig kapitalkostnad.

- **Lagerverdi (15 MNOK) og reduksjonsmål (10-15%):** Syntetisk, men konservativt forankret — akademisk og praktisk litteratur om sikkerhetslagerstyring (f.eks. Silver, Pyke & Peterson, *Inventory Management and Production Planning and Scheduling*) rapporterer typisk 10-25% reduksjonspotensial ved overgang fra enkle glidende gjennomsnitt til modeller med eksplisitt usikkerhetshåndtering (scenario-/sikkerhetsmarginbasert). Vi har lagt oss i nedre del av dette intervallet for å holde forretningscasen konservativ.
- **Kapitalkostnad (8%):** Representerer en typisk WACC/intern hurdle rate for en norsk mellomstor virksomhet i dagens rentemiljø (jf. Norges Banks styringsrente-nivå og vanlige påslag for driftskapital). Dette er ikke firmaspesifikt — det er en illustrativ, men realistisk sjablong, og bør erstattes med bedriftens faktiske kapitalkostnad i en reell pilot.
- **Formelen som knytter prognosekvalitet til kapitalbinding:** Sikkerhetslager ≈ z-verdi (ønsket servicegrad) × standardavvik i prognosefeil × √ledetid. Lavere MAPE ⇒ lavere effektivt standardavvik i prognosefeilen ⇒ lavere nødvendig sikkerhetslager ved samme servicegrad. Dette er en forenklet, men standard sammenheng fra lagerstyringsteori, og er grunnen til at Success Criteria kobler det tekniske MAPE-målet direkte til det forretningsmessige kapitalbindingsmålet.

## 2. Forutsetning bak MAPE-forbedringsmålet (≥15%)

Målet om at SES-modellen skal vise ≥15% relativ forbedring i MAPE sammenlignet med et naivt glidende gjennomsnitt, er satt som et **konservativt MVP-mål**, ikke hentet fra én spesifikk kilde. Begrunnelse:

- Empiriske sammenligninger av enkle eksponentielle utjevningsmodeller mot naive/moving-average-baselines i detaljhandel- og distribusjonsdata viser typisk 10-30% relativ MAPE-forbedring, avhengig av sesongmønster og støynivå i dataene (jf. bredt referert forecasting-litteratur, f.eks. Makridakis-konkurransene (M-competitions), som sammenligner enkle vs. avanserte metoder på tvers av tusenvis av tidsserier).
- 15% er valgt fordi det er tydelig over "støygulvet" (dvs. en forbedring som ikke kan forklares med tilfeldigheter i det syntetiske datasettet), men lavt nok til å være realistisk oppnåelig med en enkel SES-modell uten avansert parametertuning innenfor tidsrammen.
- **Dette bør eksplisitt kalibreres på nytt** når/hvis modellen testes mot reelle historiske data i en pilot (jf. Vision-seksjonen), siden faktisk oppnåelig forbedring avhenger sterkt av hvor "outlier-tung" og sesongpreget de reelle dataene er.

## 3. Markedsbilde — eksisterende alternativer

| Verktøy/tilnærming | Type | Relevans for sammenligning |
|---|---|---|
| Microsoft Power BI / DAX | BI/visualisering | Mest brukte verktøy for lagerrapportering i norske SMB-er i dag; utgangspunktet for "The Problem"-seksjonen |
| Excel (manuelle modeller) | Regneark | Fortsatt dominerende for ad-hoc prognosearbeid; grunnlaget for tidsbesparelsesargumentet |
| Kommersielle demand-planning-suiter (f.eks. SAP IBP, o9 Solutions) | Full ERP-integrert planlegging | For dyre/komplekse for målgruppen (mellomstor grossist); nevnt for å vise at "full løsning" finnes, men ikke er det denne MVP-en konkurrerer med |

## 4. Forkastede alternativer (utdypet fra Scope)

Se `product-brief.md`, Scope-seksjonen, for kortversjonen. Utdypet begrunnelse:

- **ARIMA/LSTM:** Krever typisk 2-3 år med historiske datapunkter per serie for stabil trening, og hyperparameter-tuning som er vanskelig å validere innenfor tidsrammen for denne leveransen. Viktigere: en nevralnett-vekt er ikke noe en controller kan etterprøve manuelt, noe som direkte undergraver "white-box"-differensieringen som er kjernen i produktets verdiforslag.
- **Full ERP-integrasjon:** Ville krevd tilgang til et reelt ERP-system (SAP/Dynamics) med tilhørende autentisering, datamodellmapping og driftsavtaler — utenfor det en studentlevert MVP kan skaffe eller forsvare sikkerhetsmessig.
- **Automatisk write-back av bestillinger:** Ville krevd betydelig strengere validering (hva skjer hvis modellen tar feil og automatisk bestiller for mye?) enn det som er forsvarlig å levere uten pilotering. Holder seg til "beslutningsstøtte, ikke beslutningstaker"-prinsippet.

## 5. Kilder

- Silver, E.A., Pyke, D.F., Peterson, R. (1998). *Inventory Management and Production Planning and Scheduling*, 3rd ed. Wiley.
- Makridakis, S. et al. — M-competitions (M3, M4, M5): offentlig tilgjengelige sammenligninger av forecasting-metoder på tvers av tusenvis av reelle tidsserier, inkludert enkle vs. avanserte modeller.
- Norges Bank — styringsrente og rentebane (for kapitalkostnad-sjablongen), norges-bank.no.
∑