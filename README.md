# CrackingPaasswords


<h2> Hashcat </h2>

```bash 
echo -n "algo" | md5sum # la opcion -n evita que se ponga un salto de linea automaticamente lo cual genera diferente hash CUIDADO 
```

```bash
vim hash -> ctrl + v# pone automaticamente el salto de linea y te lo indica con el simbolo $ nano igual mete ese salto usar sublime
```

```bash 
xxd file # permite ver archivos en forma hex es asi como entendi lo de el salto de linea 0a
```

```bash 
# pagina para revisar los modos -m osea los tipos de hash que pueden usarse en hashcat al dia de hoy este es el link
https://hashcat.net/wiki/doku.php?id=example_hashes
```

Identificar tipos de hashes siempre es preferible usar el contexto y la logica ejemplo apache guarda los hashes asi $asr1$7asdadfs50310$gh9masdfasGxogwX/ztry
por lo regular usan el delimitador $

<h3> Modos de ataque parametro -a </h3>

0	Straight (Diccionario)
1	Combination (Dos listas y una las palabras ejemplo pass:marcos quita los 2 puntos passmarcos uniria de lista1 y de lista2)
3	Brute-force
6	Hybrid Wordlist + Mask
7	Hybrid Mask + Wordlist
