
# Gagnavinnsla með SQLite3

## Tíðni nafna á Íslandi

Búa til töflu til að geyma nafna fjöldann*
CREATE TABLE IF NOT EXISTS names (
- hér er lesið inn name frá csv skránum*
name TEXT NOT NULL, 
- hér er lesið inn year frá csv skránum*
year INTEGER NOT NULL, 
- hér er lesið inn frequency frá csv skránum*
frequency INTEGER NOT NULL, 
- hér er lesið inn annað sem gæti verið í csv skránum, ef ekkert kemur þá er sett *NULL í staðinn**
type TEXT, 
Setja megin flokkana okkar sem primary key*
PRIMARY KEY (name, year, type)); 

#### Setja gögnin í töflu

- Merkja að verið sé að lesa inn gögn frá csv skrám: 
.mode csv
- importa gögnin, byrjum á first_names (er með csv skránna undir data hjá mér í VScode): 
.import 'data\first_names_freq.csv' names
- Setja öll gögn fyrir "first_name" sem first_names: 
UPDATE names SET type = 'first_name' WHERE type IS NULL;
- Gera það sama við seinni skránni nema middle_name: 
.import 'data\middle_names_freq.csv' names
UPDATE names SET type = 'middle_name' WHERE type IS NULL;

Output file sem texta skrá sem heitir names_output.txt*
.output names_output.txt


### Spurning 1

Prenta út spurninguna:
SELECT 'Spurning 1: Hvaða hópmeðlimur á algengasta eiginnafnið??' AS question;
Setja niðurstöðurnar sem summu af öllu count eftir nöfnunum og prenta út hvaða nafn hefur flest count s.s. er algengast:
SELECT name, SUM(frequency) AS total_frequency
FROM names
- Leita af eignarnöfnunum okkar þrem og prenta út svarið með fjöldanum af count fyrir það nafn sem er alngengast
WHERE type = 'first_name' AND name IN ('Þráinn', 'Ásdís', 'Gígja')
GROUP BY name
ORDER BY total_frequency DESC
LIMIT 1;

### Spurning 2

Prenta út spurninguna:
SELECT 'Spurning 2: Hvenær voru öll nöfnin vinsælust?' AS question;
- Þessi kóði les bæði fyrstu og milli nöfnin okkar og skilar þeirri línu (row) sem hefur flest count s.s. hvenær hvert nafn var vinsælast. Niðurstöðurnar prenta öll nöfnin og það ár sem þau voru vinsælust:
SELECT name, year
- Hvað skal vera prentað út í lokin:
FROM ( 
- Hvar við leitum af svarinu: 
SELECT name, year, frequency, 
ROW_NUMBER() OVER (PARTITION BY name ORDER BY frequency DESC) AS row_num
FROM names
- Leitum af þessum þrem eignarnöfnum
WHERE (type = 'first_name' AND name IN ('Ásdís', 'Gígja', 'Þráinn')) 
UNION ALL
SELECT name, year, frequency,
ROW_NUMBER() OVER (PARTITION BY name ORDER BY frequency DESC) AS row_num
FROM names
- Leitum af þessum milli nöfnum:
WHERE (type = 'middle_name' AND name IN ('Marín', 'Ágúst')) 
) AS subquery
WHERE row_num = 1;

### Spurning 3

Prenta út spurninguna: 
SELECT 'Spurning 3: Hvenær komu þau fyrst fram?' AS question;
- Þessi kóði finnur minnsta árið sem er við hvert nafn og skilar svo nafninu með því ártali.
- Velur "name" og min(year) sem minnsta árið og stillir það sem first_appearance:
SELECT name, MIN(year) AS first_appearance 
FROM names
- Hér er verið að segja hvar á að leita eftir hvaða nafni s.s. í hvaða skrá:
- Leitum fyrst af eignarnöfnunum
WHERE (type = 'first_name' AND name IN ('Ásdís', 'Gígja', 'Þráinn'))
- Og svo að miðju nöfnunum:
OR (type = 'middle_name' AND name IN ('Marín', 'Ágúst'))
GROUP BY name
ORDER BY first_appearance;

Endurstilla output mode:
.output stdout


## 2. Saga ísfólksins

Þetta verkefni felur í sér að lesa inn SQLite gagnagrunninn isfolkid.db sem inniheldur ýmis gögn um bókabálkinn Söga Ísfólksins eftir norska rithöfundinn Margit Sandemo, sem tröllreið íslenskt samfélag á 9. áratug 20. aldar.

Til að átta ykkur á grunninum, útbúið skipanaskrá sem sýnir hvernig gagnagrunninn er uppbyggður (ekki hafa gögnin sjálf með). Skilið henni sem create_isfolkid.sql.

Skilið skipanaskrá isfolkid.sql sem svarar eftirfarandi spurningum:

Svar: 
-- Hversu margar aðalpersónur eru í bókabálkinum?
SELECT 'Aðalpersónur: ' || COUNT(*) FROM books WHERE characters <> '';

SELECT 'Aðalpersónur: ' || COUNT():  Er bara að sameina textann „Aðalpersónur:“ ( þannig það prentist út) við niðurstöðuna frá count() sem telur fjölda raða
FROM books: Sækja gögnin úr töflunni books
WHERE characters <>: Þetta segir að telja bara þær bækur sem hafa eitthvað gildi í dálkinum characters.

-- Hversu margar persónur eru í hverri bók?
SELECT 'Persónur í hverri bók: ' || 
  id, 
  is_title, 
  LENGTH(characters) - LENGTH(REPLACE(characters, ',', '')) + 1
FROM books;

SELECT 'Persónur í hverri bók: ' || : prenta út textan “persónur í hverri bók”
Id: númer bókarinnar
is_title: heiti bókarinnar
LENGTH(characters): Þetta finnur heildarlengd strengsins í dálkinum characters, sem inniheldur nöfnin á persónunum í hverri bók, aðskilin með kommum.
LENGHT(REPLACE(characters, ',', '')): Þetta tekur strenginn characters og fjarlægir allar kommur í honum.  Og finnur svo lengdina (án komma).
Svo er þetta mínusað: LENGTH(characters) - LENGTH(REPLACE(characters, ',', '')) + 1
Þá eru fjöldi komma í strengnum fundnar og svo +1 því ef það eru t.d þrjár persónur í charachters þá eru tvær kommur. 


-- Hversu oft kemur Þengill fyrir í bókunum?
SELECT 'Þengill: ' || COUNT(*) FROM books WHERE characters LIKE '%Þengill%';

COUNT(*) telur það sem uppyllir WHERE
WHERE characters LIKE '%Þengill%': Þetta skilyrði leitar að þeim bókum þar sem dálkurinn characters inniheldur strenginn Þengill.
(%) táknar ótilgreindan fjölda stafa fyrir framan eða á eftir orðinu Þengill sem sagt að það leitar að Þengill hvar sem er innan strengsins í dálkinum characters.

-- Hvað eru margir af Paladín ættinni?
SELECT 'Paladín: ' || COUNT(*) FROM family WHERE name LIKE '%Paladín%';
Svipað og þengill

-- Hversu algengur er illi arfurinn af þeim sem eru útvalin? 
SELECT 'Illi arfurinn: ' || COUNT(*) FROM family WHERE chosen_one LIKE 'evil';
Aftur svipað. Skipti ekki máli með % því það er bara eitt orð í dálknum. 


-- Hver er fæðingartíðni kvenna í bókabálkinum? 
SELECT 'Fæðingartíðni: ' || AVG(birth) FROM family WHERE gender LIKE 'F';

AVG(birth): Finnur meðaltal á birth 


-- Hvað er Ísfólkið margar blaðsíður samanlagt?
 SELECT 'Blaðsíður: ' || SUM(pages) FROM books;
SUM(pages): Plúsar saman allar blaðsíðurnar 

-- Hvað er meðallengd hvers þáttar af Ískisum?
SELECT 'Meðallengd: ' || AVG(length) FROM storytel_iskisur;

## 3. Gagnagrunnur fyrir tímataka.net

Forritið, timatakasql2.py er hannað til að sækja úrslit af timataka.net fyrir hlaupin í ágústmánuði 2019. Gögnin eru unnin úr HTML skrám sem sóttar eru af netinu og vistuð í cvs skrá fyrir frekari greiningu.

Forritið notar reglulegar segðir (regex) til að finna ákveðin HTML tag, eins og thead, tbody, tr, og td, sem eru notuð til að bera kennsl á og greina dálkaheiti og gögn keppenda.

Forritið: 

Sækir html gögn með requests bókasafninu 

Notar reglulegar segðir til að vinna úr HTML gögnum:

- Fyrst er leitað að hausnum í töflunni (<thead>) til að finna dálkaheiti eins og t.d "Nafn", "Tími", "Aldur".
- Síðan er leitað að tbody hlutanum í töflunni sem inniheldur gögnin um keppendur (raðirnar í töflunni).
- Hver röð (<tr>) er greind og gögnin í hverjum dálki (<td>) eru hreinsuð með því að fjarlægja HTML tag og óþarfa bil. Gögnin eru pöruð saman við dálkaheitin.

Forritið tekur við URL, CSV-skránni til að vista niðurstöðurnar. 

Þegar gögnin hafa verið greind eru þau vistuð í CSV skrá með hjálp pandas bókasafninu. 
