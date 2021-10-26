# CrackingPasswords


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
<h3> Mask Attack  </h3>


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

<h3> Modo Hybrido </h3>

 6 | Hybrid Wordlist + Mask                                                                                    
   
```bash 
hashcat -a 6 -m 0 hybrid_hash /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt '?d?s' # de la lista de rockyou metele un numero ?d al final y un caracter especial ?s de cada uno de los elementos de esa lista
```
 7 | Hybrid Mask + Wordlist  

```bash 
hashcat -a 7 -m 0 hybrid_hash_prefix -1 01 '20?1?d' /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt# pone un prefijo '20?1?d' con la mascara -1 a la lista de rockyou
```

<h3> Generador de Diccionarios </h3>


<h4> Chunch </h4>

```bash 
crunch <minimum length> <maximum length> <charset> -t <pattern> -o <output file> # en general se usa asi para ingresar como "mascaras" 
# The "-t" option is used to specify the pattern for generated passwords. The pattern can contain "@," representing lower case characters, "," (comma) will insert upper case characters, "%" will insert numbers, and "^" will insert symbols.

crunch 17 17 -t ILFREIGHT201%@@@@ -o wordlist # Ejemplo The pattern "ILFREIGHT201%@@@@" will create words with the years 2010-2019 followed by four letters. The length here is 17, which is constant for all words.

```

<h4> CeWL Generador de Passwords tipo spider </h4>

```bash 
cewl -d <depth to spider> -m <minimum word length> -w <output wordlist> <url of website> # general 

cewl -d 1 -m 6 -w cweout.txt https://bloggerkingindia.blogspot.com/2017/03/how-to-crack-or-decrypt-wifi-handshake.html # ejemplo

```

<h4> Hashcat-utilsGenerador en base a los strings de las mascaras </h4>

```bash 
/mp64.bin Welcome?s
Welcome#
Welcome$
Welcome%
Welcome&
Welcome'
Welcome(
Welcome)
Welcome*
Welcome+
for i in $(cat words); do echo -n $i | sha1sum | tr -d ' -';done
```

<h4> Hashcat Rules (L33tspeak) </h4>

```bash 
hashcat -a 0 -m 100 hash rockyou.txt -r rule.txt# leetspeak rule c so0 si1 se3 ss5 sa@
```

```bash 
/etc/shadow

root:$6$tOA0cyybhb/Hr7DN$htr2vffCWiPGnyFOicJiXJVMbk1muPORR.eRGYfBYUnNPUjWABGPFiphjIjJC5xPfFUASIbVKDAHS3vTW1qU.1:18285:0:99999:7:::
Pero solo se usa esta parte para crackear $6$tOA0cyybhb/Hr7DN$htr2vffCWiPGnyFOicJiXJVMbk1muPORR.eRGYfBYUnNPUjWABGPFiphjIjJC5xPfFUASIbVKDAHS3vTW1qU.1

hashcat -m 1800 hash rockyou.txt
```
<h4> Crack Common Files  </h4>

```bash 
python office2john.py hashcat_Word_example.docx # Extraer el password de un documento de office protegido

hashcat -m 9600 office_hash /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt

zip2john a.zip

hashcat -a 0 -m 17200 hash rockyou.txt # Busca solo que tipo de archivo es y cambia el -m 

python pdf2john.py inventory.pdf | awk -F":" '{ print $2}'# Para Pdfs protegidos 

```

```bash 
aircrack-ng YourFile.cap -J YourNewfilename # handshake pasarlo a formato que JTR pueda entender para crackiarlo  .hccap

hccap2john Your.hccapfile > Newfilename

john yourfilename --wordlist=rockyou.txt

```


```bash 
./cap2hccapx.bin corp_capture1-01.cap mic_to_crack.hccapx #  convertir de .cap a .hccapx para que lo entienda hashcat ( no me funciona aveces)
hcxpcaptool -z pmkidhash_corp cracking_pmkid.cap / si funciona


hashcat -a 0 -m 2500 mic.hccapx rockyou.txt

```

