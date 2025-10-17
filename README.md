# Kako kreirati i verzionirati vlastiti GitHub Action

Ovaj dokument opisuje korake za kreiranje i verzioniranje vlastitog GitHub Action-a, tako da ga možeš koristiti iz drugih repozitorija putem `uses:` direktive.

---

## 1️⃣ Priprema repozitorija

1. Otvori repozitorij `raiffeisen/rbbh-gha-<my-actions-name>`
2. Napravi novi lokalni branch za svoju prvu verziju:
```bash
    git checkout -b action-test/v1.0.0
```
3. U korijenu repozitorija napravi folder za svoj action:
```bash
    mkdir action-test
```
4. U folder action-test dodaj fajl action.yml sa osnovnom definicijom:
```yaml
    name: "Action Test"
    description: "Primjer vlastitog GitHub Action-a"
    runs:
    using: "composite"
    steps:
        - name: Echo test
        run: echo "Radi iz custom actiona!"
        shell: bash
```

## 2️⃣ Commit i push promjena

1. Dodaj i commitaj fajlove:
```bash
    git add .
    git commit -m "Dodaj action-test v1.0.0"
```
2. Pushaj branch na GitHub:
```bash
    git push origin action-test/v1.0.0
```

## 3️⃣ Napravi Pull Request (PR)

1. Otvori repozitorij na GitHubu.
2. Kreiraj PR iz branch-a action-test/v1.0.0 prema main.
3. Pregledaj promjene, odobri PR i merge-aj ga u main.

## 4️⃣ Kreiraj verzioni tag

1. Nakon što je merge završen, taguj verziju kako bi je korisnici mogli pozivati sa @1.0.0:
```bash
    git checkout main
    git pull origin main
    git tag -a v1.0.0 -m "Initial release of my-action"
    git push origin v1.0.0
```
2. Da bi korisnici mogli koristiti tvoj action kao @v1, potrebno je napraviti floating tag koji pokazuje na latest major verziju actiona:
```bash
    git tag -f v1 v1.0.0
    git push -f origin v1
```

## 5️⃣ Korištenje Akction-a

U repou koji koristi action, pokreni workflow koji koristi:
```yaml
    uses: raiffeisen/rbbh-gha-<my-actions-name>/action-test@v1 ??????
```

## 6️⃣ Update/Fix Action-a

Ako postoji potreba da se izvrši update ili fix koda actiona potrebno je kreirati novu verziju, izmjeniti kod i tagove. Verzija se uvijek tagira po pravilu major.minor.patch.
1. Uvijek pravi novi lokalni branch sa jasnim imenom i verzijom:
```bash
    git checkout -b action-test/v1.0.1
```
2. Uredi kod (npr. action.yml ili neku helper scriptu) i testiraj lokalno ako koristiš composite action.
3. Po uspjesnom testiranju commitaj izmjene i pushaj kod
```bash
    git add .
    git commit -m "Fix: ispravka logike u action-test (v1.0.1)"
    git push origin action-test/v1.0.1
```
4. Otvori PR iz action-test/v1.0.1 prema main.
5. Nakon pregleda i odobrenja, merge-aj PR u main.
6. Nakon što se PR mergao u main, napravi novi verzioni tag:
```bash
    git checkout main
    git pull origin main
    git tag -a v1.0.1 -m "Fix release for action-test"
    git push origin v1.0.1
```
7. Ažuriraj floating tag u skladu sa major verzijom:
```bash
git tag -f v1 v1.0.1
git push -f origin v1
```
8. Provjeri verzije na GitHubu:
```markdown
    Na GitHubu otvori:
    Repo → Releases → Tags
    Provjeri da sada imaš:

    ✅ v1.0.0 (stara verzija)

    ✅ v1.0.1 (nova verzija)

    ✅ v1 → floating tag koji pokazuje na v1.0.1
```

 7️⃣ 8️⃣ ➡️