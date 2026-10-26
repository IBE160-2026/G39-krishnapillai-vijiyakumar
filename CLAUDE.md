# CLAUDE.md

Dette prosjektet bygger MVP-en beskrevet i docs/product-brief.md.
Se docs/addendum.md for kildehenvisninger, forutsetninger bak tallene, og forkastede alternativer.

Viktigst å følge strikt:
- Kun det som står under "IN — første versjon" i Scope skal bygges.
- Alt under "OUT — ikke nå" skal IKKE bygges, selv om det virker naturlig å legge til.
- Success Criteria (særlig ytelseskrav og MAPE-mål) skal kunne testes automatisk — skriv tester for disse.
- Stack: Python/Pandas/FastAPI (backend), React/TypeScript (enkelt grensesnitt for validering, ikke fullt dashboard).

Les docs/product-brief.md for full kontekst før du planlegger noe.

## Sesjonslogg

Når en oppgave eller økt avsluttes, før sluttoppsummeringen, bruk `sesjonslogg`-skillen: dokumenter Oppgave + Plan + Resultat + åpne punkter i `.logs/<bruker>/`, og før åpne punkter i `.logs/<bruker>/issues.md`. Skillen committer loggfila lokalt; push gjør du selv. Gruppens egne regler for commit og språk gjelder foran dette.
