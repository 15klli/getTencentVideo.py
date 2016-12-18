# getTencentVideo.py
腾讯视频爬虫初例

from urllib.request import Request, urlopen
from urllib.error import URLError, HTTPError
from bs4 import BeautifulSoup
import re

print('https://v.qq.com')
web = input('请输入网址：')
if re.search(r'vid=', web):
    patten = re.compile(r'vid=(.*)')
    vid = patten.findall(web)
    vid = vid[0]

else:
    newurl = (web.split('/')[-1])
    vid = newurl.replace('.html', ' ')
#从视频页面找出vid

getinfo = 'https://vv.video.qq.com/getvinfo?vids={vid}&otype=xlm&defaultfmt=fhd'.format(vid=vid.strip())
def getpage(url):
    req = Request(url)
    user_agent = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36'
    req.add_header('User-Agent', user_agent)
    try:
        response = urlopen(url)
    except HTTPError as e:
        print('The server couldn\'t fulfill the request.')
        print('Error code:', e.code)
    except URLError as e:
        print('We failed to reach a server.')
        print('Reason:', e.reason)
    html = response.read().decode('utf-8')
    return(html)
#打开网页的函数

a = getpage(getinfo)

soup = BeautifulSoup(a, 'html.parser')
for e1 in soup.find_all('url'):
    ippattent = re.compile(r'((?:(2[0-4]\d)|(25[0-5])|([01]?\d\d?))\.){3}(?:(2[0-4]\d)|(255[0-5])|([01]?\d\d?))')
    if re.search(ippattent, e1.get_text()):
        ip = (e1.get_text())
for e2 in soup.find_all('id'):
    idpattent = re.compile(r'\d{5}')
    if re.search(idpattent, e2.get_text()):
        id = (e2.get_text())
filename = vid.strip()+'.p'+id[2:]+'.1.mp4'
#找到ID和拼接FILENAME

getkey = 'https://vv.video.qq.com/getkey?format={id}&otype=xml&vt=150&vid={vid}&ran=0.4184574941173196&charge=0&filename={filename}&platform=11'.format(id=id, vid=vid.strip(), filename=filename)
#利用getinfo中的信息拼接getkey网址
b = getpage(getkey)

key = (re.findall(r'<key>(.*)<\/key>', b))

videourl = ip + filename + '?' + 'vkey=' + key[0]

print('视频播放地址 ' + videourl)
print('Done')
#完成了
