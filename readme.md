# 1. google drive 연동

```python
from google.colab import drive
drive.mount('/content/drive')
```



# 2. python3-libtorrent 패키지 인스톨

```python
!apt install python3-libtorrent
```



# 3. torrent 파일 다운로드

다운로드 위치 설정 및 토렌트 마그넷 링크로 다운로드

```python
import libtorrent as lt
import time
import datetime

ses = lt.session()
ses.listen_on(6881, 6891)
params = {
    'save_path': '/content/drive/MyDrive/볼거 애니',                              # 저장위치
    'storage_mode': lt.storage_mode_t(2),
    'paused': False,
    'auto_managed': True,
    'duplicate_is_error': True}

link = "magnet:?xt=urn:btih:b845d94a140e6b7c7681a2419c214c7362ba0e03" # 토렌트 파일 다운로드 되는 링크 / 혹은 마그넷 주소
print(link)

handle = lt.add_magnet_uri(ses, link, params)
ses.start_dht()

begin = time.time()
print(datetime.datetime.now())

print ('Downloading Metadata...')
while (not handle.has_metadata()):
    time.sleep(1)
print ('Got Metadata, Starting Torrent Download...')

print("Starting", handle.name())

while (handle.status().state != lt.torrent_status.seeding):
    s = handle.status()
    print(s)
    state_str = ['queued', 'checking', 'downloading metadata', \
            'downloading', 'finished', 'seeding', 'allocating']
    print ('%.2f%% complete (down: %.1f kb/s up: %.1f kB/s peers: %d) %s ' % \
            (s.progress * 100, s.download_rate / 1000, s.upload_rate / 1000, \
            s.num_peers, state_str[s.state]))
    time.sleep(5)

end = time.time()
print(handle.name(), "COMPLETE")

print("Elapsed Time: ",int((end-begin)//60),"min :", int((end-begin)%60), "sec")

print(datetime.datetime.now())
```

# 4 . 여러개의 토렌트 다운받기 및 로딩 표현 변경

> 진행도가 약 0.5초마다 한번씩 출력되는 것을 
>
> 같은 줄에 계속 덮어씌어지면서 하도록 변경
>
> 만약 중간에 다운로드가 끊어질 경우 다시 실행하면 어디까지 다운로드 했는데 스스로 인식하고 다운로드가 안된 부분에 해당하는 것을 다운함

```python
import libtorrent as lt
import time
import datetime

links = [
    "https://eu.ohys.net/t/disk/%5BOhys-Raws%5D%20Assault%20Lily%20Bouquet%20%28TBS%201280x720%20x264%20AAC%29.torrent" # 여기에 여러개의 파일들
    ]

ses = lt.session()
ses.listen_on(6881, 6891)
params = {
    'save_path': '/content/drive/MyDrive/볼거 애니',                              # 저장위치
    'storage_mode': lt.storage_mode_t(2),
    'paused': False,
    'auto_managed': True,
    'duplicate_is_error': True}

for link in links :
  
  #link = "magnet:?xt=urn:btih:67e0a3f53d1292c87387871df1f51595e361afe5" # 토렌트 파일 다운로드 되는 링크 / 혹은 마그넷 주소
  print(link)

  handle = lt.add_magnet_uri(ses, link, params)
  ses.start_dht()

  begin = time.time()
  print(datetime.datetime.now())

  print ('Downloading Metadata...')
  while (not handle.has_metadata()):
      time.sleep(1)
  print ('Got Metadata, Starting Torrent Download...')

  print("Starting", handle.name())

  while (handle.status().state != lt.torrent_status.seeding):
      s = handle.status()
      #print(s)
      state_str = ['queued', 'checking', 'downloading metadata', \
              'downloading', 'finished', 'seeding', 'allocating']
      print ('\r%.2f%% complete (down: %.1f kb/s up: %.1f kB/s peers: %d)' % \
              (s.progress * 100, s.download_rate / 1000, s.upload_rate / 1000, \
              s.num_peers), end="")
      time.sleep(5)
  print()
  end = time.time()
  print(handle.name(), "COMPLETE")

  print("Elapsed Time: ",int((end-begin)//60),"min :", int((end-begin)%60), "sec")

  print(datetime.datetime.now())
print("전체 끝")
```





# 5. 코랩 세션 종료 방지

> 90분동안 움직임 없으면, 연결이 끊어지기 때문에 F12- console 에 이거 넣으두면 세션 안끊어짐

```javascript
function ClickConnect(){
    console.log("코랩 연결 끊김 방지"); 
    document.querySelector("colab-toolbar-button#connect").click() 
}
setInterval(ClickConnect, 60 * 1000)
```

