# Kako kreirati i verzionirati vlastiti GitHub Action

Ovaj dokument opisuje korake za kreiranje i verzioniranje vlastitog GitHub Action-a, tako da ga možeš koristiti iz drugih repozitorija putem "uses:" direktive.

## NAPOMENE

➡️ Svi GitHub Actionsi namjenjeni upotrebi u RBBH kreiraju se u okviru organizacije `raiffeisen` u zasebnim repozitorijima. Ovi repozitoriji trebaju biti `public` i imenuju sa prefiksom `rbbh-gha-<ime_actionsa>`.  


➡️ Verzioniranje koda vrši se po semantičkom modelu (engl. Semantic Versioning, skraćeno SemVer) i obavezujuce je.
Model se sastoji od malog svlova "v" kojeg slijede tri broja odvojena tackom:

`vMAJOR.MINOR.PATCH`

Značenje:
```
MAJOR – povećava se kada napraviš nekompatibilne promjene (breaking changes).
MINOR – povećava se kada dodaš nove funkcionalnosti unazad kompatibilne.
PATCH – povećava se kada ispraviš bugove ili uradiš male izmjene bez novih funkcionalnosti.
```

Primjer:
```
1.0.0 – prva stabilna verzija
1.1.0 – dodana nova mogućnost
1.1.1 – ispravljen bug
2.0.0 – promjena koja ruši kompatibilnost s verzijom 1.x`
```
Više o semantičkom modelu verzioniranja nauči na [SemVer.org](https://semver.org/)


➡️ Floating taging je obavezan za svaku izmjenu i omogućava da se referencira latest MAJOR izmjenu, npr. floating tag `v2` referencira na tag `v2.7.23` sto je latest izmjena verzije gdje je MAJOR=2. Time se olakšava odabir ispravne verzije `uses:` direktivom iz workflowa.

Više o git taggingu nauči na [Git Basic - Tagging](https://git-scm.com/book/ms/v2/Git-Basics-Tagging)

➡️ Kreirati i updateovati fajl CHANGELOG.md koji se koristi za praćenje svih značajnih promjena u repozitoriju kroz njegove verzije. Cilj je omogućiti korisnicima i saradnicima da jasno vide šta se promijenilo, kada i zašto, bez potrebe da prolaze kroz Git historiju.

Primjer CHANGELOG.md:

```
## [1.1.0] - 2025-10-17
### Added
- Dodan novi API endpoint za autentifikaciju.

### Fixed
- Ispravljena greška pri validaciji JWT tokena.

## [1.0.0] - 2025-09-01
### Added
- Inicijalno izdanje.
```
Više o CHANGELOG nauci na: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)

## Kreiranje novog GitHub Actions-a

### 1️⃣ Priprema repozitorija

1. Kreiraj novi repozitorij `raiffeisen/rbbh-gha-<my-actions-name>` i kloniraj ga lokalno:

```bash
    git clone https://code.rbi.tech/raiffeisen/rbbh-gha-<my-actions-name>.git
```
2. Promjeni radni direktorij:
```bash
    cd rbbh-gha-<my-actions-name>
```
3. Napravi novi lokalni branch za svoju prvu verziju:
```bash
    git checkout -b rbbh-gha-<my-actions-name>/v1.0.0
```
4. U korijenu repozitorija napravi file za svoj action:
```bash
    touch action.yml
```
5. U file action.yml dodaj kod za svoj action:
```yaml
    name: "Ime akcije"
    description: "Primjer vlastitog GitHub Action-a"
    runs:
    using: "composite"
    steps:
        - name: Echo test
        shell: bash
        run: echo "Radi iz custom actiona!"
```
Više o GitHub Actions nauči na [GitHub Docs](https://docs.github.com/en/actions/concepts/workflows-and-actions/custom-actions)

### 2️⃣ Commit i push promjena

1. Dodaj i commitaj fajlove:
```bash
    git add .
    git commit -m "Add <ime akcije> v1.0.0"
```
2. Pushaj branch na GitHub:
```bash
    git push origin rbbh-gha-<my-actions-name>/v1.0.0
```

### 3️⃣ Napravi Pull Request (PR)

1. Otvori repozitorij na GitHubu.
2. Kreiraj PR iz branch-a `rbbh-gha-<my-actions-name>/v1.0.0` prema `main`.
3. Pregledaj promjene, odobri PR i merge-aj ga u main.

### 4️⃣ Kreiraj verzioni tag

1. Nakon što je merge završen, taguj verziju kako bi je korisnici mogli pozivati sa @1.0.0:
```bash
    git checkout main
    git pull origin main
    git tag -a v1.0.0 -m "Initial release of my-action"
    git push origin v1.0.0
```
2. Da bi korisnici mogli koristiti tvoj action kao @v1, potrebno je napraviti floating tag koji referencira latest major verziju actiona:
```bash
    git tag -f v1 v1.0.0
    git push -f origin v1
```

### 5️⃣ Korištenje Akction-a

U repou koji koristi action, pokreni workflow koji koristi:
```yaml
    uses: raiffeisen/rbbh-gha-<my-actions-name>@v1
```

##  Update/Fix Action-a

Ako postoji potreba da se izvrši update ili fix koda actiona potrebno je kreirati novu verziju, izmjeniti kod i tagove. Verzija se uvijek tagira po semantičkom modelu `vMAJOR.MINOR.PATCH`.

1. Uvijek pravi novi lokalni branch sa jasnim imenom i verzijom:
```bash
    git checkout -b rbbh-gha-<my-actions-name>/v1.0.1
```
2. Uredi/izmjeni kod (npr. action.yml ili neku helper scriptu) i testiraj lokalno ako koristiš composite action.
3. Po uspjesnom testiranju commitaj izmjene i pushaj kod:
```bash
    git add .
    git commit -m "Fix: ispravka logike u <my-actions-name> (v1.0.1)"
    git push origin rbbh-gha-<my-actions-name>/v1.0.1
```
4. Otvori PR iz `rbbh-gha-<my-actions-name>/v1.0.1` prema `main`.
5. Nakon pregleda i odobrenja, merge-aj PR u main.
6. Nakon što se PR mergao u main, napravi novi verzioni tag:
```bash
    git checkout main
    git pull origin main
    git tag -a v1.0.1 -m "Fix release for action-test"
    git push origin v1.0.1
```
7. Ažuriraj floating tag u skladu sa major verzijom latest izmjene:
```bash
git tag -f v1 v1.0.1
git push -f origin v1
```
8. Provjeri na GitHubu (Repo → Releases → Tags) da sada imas ove tagove:
```
    ✅ v1.0.0 → stara verzija
    ✅ v1.0.1 → nova verzija
    ✅ v1 → floating tag koji referencira commit id od v1.0.1
```