[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/Nf5nD3dq)
# Gagnavinnsla með SQLite3

## 1. Tíðni nafna á Íslandi
Til að keyra kóðan hjá ykkur þurfið þið að opna sqlite3 í terminal og síðan muna að vista kóðann áður en þið keyrið hann í VScode.

Keyrið svo kóðann með skipuninni
```bash
.read code\names.sql
```
Hér er ég með code\ þar sem kóðinn minn er geymdur í möppunni code í VScode. Ef þið vistið kóðann á öðrum stað þurfið þið að breyta code í heitið á möppunni í VScode þar sem ykkar kóði er. Líka ef þið vistið þetta bara beint inn án þess að hafa möppu þá sleppið þið algjörlega þessu og hafið aðeins .read names.sql.


## 2. Saga Ísfólksins

Til að keyra kóðann skrifar maður í terminal 
```bash
sqlite3 isfolkid.db og svo sqlite> .read isfolkid.sql 
```


## 3. 
Tafla fyrir hvaða hlaup sem er í ágúst 2019.

Til að keyra forritið og búa til csv og html skrá notið þið inntakið:

```bash
python3 timatakasql3.py --url "https://timataka.net/skalafell2019/urslit/?race=2&cat=overall" --output hlaup/skalafell.csv --debug
```

Munið bara að vera búin að gera möppu sem heitir hlaup eða getið líka gert bara ....--output skalafell.csv.


