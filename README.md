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

<ol>
<li> 0	Straight (Diccionario) </li>
<li> 1	Combination (Dos listas y una las palabras ejemplo pass:marcos quita los 2 puntos passmarcos uniria de lista1 y de lista2) </li>
<li> 3	Brute-force </li>
<li> 6	Hybrid Wordlist + Mask </li>
<li> 7	Hybrid Mask + Wordlist </li>
</ol>

```bash
hashcat -a 0 -m <hash type> <hash file> <wordlist> ataque de diccionario basico -a ataque 0 de diccionario -m modo de hash o tipo de hash ej. md5 = 0
```

```bash
hashcat -a 1 -m <hash type> <hash file> <wordlist1> <wordlist2> # ataque de convinacion passmarcos ;)
```
<h3> Mask Attack (el mas potente en mi opinion) </h3>


<h4> Mascaras por defecto en hashcat </h4>
<ol>
<li> ?l	 lower-case ASCII letters (a-z) </li>
<li> ?u	 upper-case ASCII letters (A-Z) </li>
<li> ?d	 digits (0-9) </li>
<li> ?h	 0123456789abcdef </li>
<li> ?H	 0123456789ABCDEF </li>
<li> ?s	 special characters («space»!"#$%&'()*+,-./:;<=>?@[]^_`{ </li>
<li> ?a	 ?l?u?d?s </li>
<li> ?b	 0x00 - 0xff </li>
</ol>

```bash 
Por ejemplo cada una representa un caracter en ese rango password?d lo cual hace match con password1 password2 passwordn
Mascaras definidas por el usuario -1 al -4 
```

<h3>Mascaras personalizadas ejemplos</h3>

```bash
hashcat -a 3 -m 0 md5hash -1 01 '20?1?d'# Defini una mascara en -1 01 que solo puede tomar esos dos valores 0 y 1 no mas y el otro ?d cualquier nuemero 
```

```bash
hashcat64.bin –m 1000 –a 3 ntlm.hp pass1234 #Comprobaría únicamente la contraseña (pass1234), mientras que el comando:
```
```bash
hashcat64.bin –m 1000 –a 3 ntlm.hp pass?d?d?d?d #Comprobaría todo el espectro de contraseñas que empezaran por “pass” y cuyos cuatro siguientes caracteres fueran dígitos decimales (pass0000-pass9999)
```
```bash
./hashcat64.bin –m 1000 –a 3 ntlm.ph ?a?a?a?a?a –i –increment—min 2 increment—max 4 #Se probarán secuencialmente las máscaras:

?a?a

?a?a?a

?a?a?a?a
Con el fin de reducir las combinaciones posibles para nuestro objetivo, podríamos utilizar la máscara ¿u?l?l?l?l?l?l?l?ld?d?d?d que comprendería todo el espectro de contraseñas desde Aaaaaaaa0000 hasta Zzzzzzzz9999.
```
```bash
./hashcat64.bin -m 1000 -a 3 ntlm.hp ?u?l?l?l?l?l?l?l19?d?d #En este caso, necesitaremos crear 2 máscaras, una para aquellas contraseñas que contengan la fecha de nacimiento desde 1900 hasta 1999:
```
```bash
./hashcat64.bin -m 1000 -a 3 ntlm.hp -1 01 -2 012345678 ?u?l?l?l?l?l?l?l20?1?2 # Y otra para aquellas del 2000 al 2018.
```

```bash
hashcat64.bin –m 1000 –a 3 ejemplo.hcmask # se puede pasar una lista de mascaras definidas por nosotros ya adentro se ponen asi 19,?l?l o normal password,?d o ?l?l?l?l?d
```
```
