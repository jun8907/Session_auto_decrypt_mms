# Session_auto_decrypt_mms 🔐

복호화되지 않은 Session .mms 임시파일을 복호화하는 코드입니다.

<br><br>

## 🧪 사용법

```bash
git clone https://github.com/jun8907/Session_auto_decrypt_mms.git
cd Session_auto_decrypt_mms
pip install -r requirements.txt
python pull.py
python decrypt_mms_files.py
```

<br><br>

## 📖 관련 라이브러리 설치

```bash
pip install pycryptodome
pip install sqlcipher3-wheels
pip install python-magic
pip install python-magic-bin
```

<br><br>

## 🔧 코드 설명

- pull.py
- preferences_attachment.py
- preferences_database.py
- persistent.py
- modernkey.py
- decrypt_key.py
- data_random.py
- aes_key.py
- decrypt_mms_files.py
<br><br>
### pull.py

루팅된 Android 디바이스에서 Session 메신저의 .mms 임시파일 및 복호화에 필요한 핵심 파일들을 자동으로 추출하는 코드입니다.
.mms 임시파일은 `mms_files/` 디렉터리에 저장
복호화에 필요한 파일들은 `extracted_files/` 디렉터리에 저장

```python
[실행 결과]
[*] .mms 파일 목록 수집 중...
[+] 총 5개의 .mms 파일 발견됨
[*] /data/data/network.loki.messenger/app_parts/part1311447108672342993.mms 추출 중...
/sdcard/part1311447108672342993.mms: 1 file pulled, 0 skipped. 3.9 MB/s (14047 bytes in 0.003s)
[*] /data/data/network.loki.messenger/app_parts/part2279796186127612959.mms 추출 중...
/sdcard/part2279796186127612959.mms: 1 file pulled, 0 skipped. 19.5 MB/s (371165 bytes in 0.018s)
[*] /data/data/network.loki.messenger/app_parts/part5987799258691517169.mms 추출 중...
/sdcard/part5987799258691517169.mms: 1 file pulled, 0 skipped. 19.9 MB/s (185270 bytes in 0.009s)
[*] /data/data/network.loki.messenger/app_parts/part6948537578243099421.mms 추출 중...
/sdcard/part6948537578243099421.mms: 1 file pulled, 0 skipped. 3.0 MB/s (14047 bytes in 0.004s)
[*] /data/data/network.loki.messenger/app_parts/part994607080159938424.mms 추출 중...
/sdcard/part994607080159938424.mms: 1 file pulled, 0 skipped. 20.7 MB/s (119292 bytes in 0.005s)
[+] .mms 파일 추출 완료
[*] /data/data/network.loki.messenger/shared_prefs/network.loki.messenger_preferences.xml 추출 중...
/sdcard/network.loki.messenger_preferences.xml: 1 file pulled, 0 skipped. 0.5 MB/s (2344 bytes in 0.005s)
[*] /data/data/network.loki.messenger/databases/session.db 추출 중...
/sdcard/session.db: 1 file pulled, 0 skipped. 22.9 MB/s (1081344 bytes in 0.045s)
[*] /data/misc/keystore/persistent.sqlite 추출 중...
/sdcard/persistent.sqlite: 1 file pulled, 0 skipped. 15.5 MB/s (139264 bytes in 0.009s)
[+] 설정, DB, keystore 파일 추출 완료
```
<br><br>
### preferences_database, attachment.py

Session 메신저의 /share_pref/network.loki.messenger_preferences.xml 파일에서 SQLCipher에 사용된 패스프레이즈를 추출
- `data (hex)`
- `input (hex)`
- `GCM Tag (hex)`
- `iv (base64)`

```python
[실행 결과]
[+] data (hex)       : 085617703bc8f3aea69c0f1daaefd7d7838f0370ec3cdecf00ac04e44b270e8e3f8d7d566b45d619dddba8c482f7c89f
[+] ciphertext (hex) : 085617703bc8f3aea69c0f1daaefd7d7838f0370ec3cdecf00ac04e44b270e8e
[+] GCM tag (hex)    : 3f8d7d566b45d619dddba8c482f7c89f
[+] iv (base64)      : 1Fv01G4yumoSIscU

[DEBUG] 원본 값: {"data":"tmYgb5IwnuppdewesA5AmxwcGs7LpdBEqdctDqD7qVvj8/jOhLH6pepRDryBmPVLWuGz919jIUyo5qmVNNEXWc6+5PK+OIZpVR08oQGnpFLgSeNn/m7XvLo1cpvJH8HJfq0QGGazOwh918svsIapWogY4JE/LhPs","iv":"lwNNJy0tG9TT+5t6"}
[+] data (base64): tmYgb5IwnuppdewesA5AmxwcGs7LpdBEqdctDqD7qVvj8/jOhLH6pepRDryBmPVLWuGz919jIUyo5qmVNNEXWc6+5PK+OIZpVR08oQGnpFLgSeNn/m7XvLo1cpvJH8HJfq0QGGazOwh918svsIapWogY4JE/LhPs
[+] iv   (base64): lwNNJy0tG9TT+5t6
[+] data (decoded, hex): b666206f92309eea6975ec1eb00e409b1c1c1acecba5d044a9d72d0ea0fba95be3f3f8ce84b1faa5ea510ebc8198f54b5ae1b3f75f63214ca8e6a99534d11759cebee4f2be388669551d3ca101a7a452e049e367fe6ed7bcba35729bc91fc1c97ead101866b33b087dd7cb2fb086a95a8818e0913f2e13ec
[+] GCM Tag (hex): 7dd7cb2fb086a95a8818e0913f2e13ec
[+] Ciphertext (input, hex): b666206f92309eea6975ec1eb00e409b1c1c1acecba5d044a9d72d0ea0fba95be3f3f8ce84b1faa5ea510ebc8198f54b5ae1b3f75f63214ca8e6a99534d11759cebee4f2be388669551d3ca101a7a452e049e367fe6ed7bcba35729bc91fc1c97ead101866b33b08
```
<br><br>
### persistent.py

Android 기기에서 추출한 Session 메신저의 `persistent.sqlite` 키스토어 DB에서 `SignalSecret` alias에 해당하는 복호화 키(16바이트)를 자동으로 추출하는 코드입니다.

```python
[실행 결과]
[+] SignalSecret #1 id: 7284520658499830241
    → 추출된 복호화 키 (16바이트 hex): d1ccc1ae4d0e3a5ef0b1074794e076b7
[+] SignalSecret #2 id: 6456924783388765775
    → 추출된 복호화 키 (16바이트 hex): d843d662011f92d82c69659c4311904f
```
<br><br>
### modernkey.py

Session 메신저의 설정 파일과 키 저장소를 이용하여, 첨부파일(.mms 등) 복호화에 사용되는 `modernKey` 값을 자동으로 복호화하고 출력

```python
[실행 결과]
[+] modernKey (base64): 6l5o/A+zLDQyNUzaEs/y/LkMx1lBw+6Vcx4Ju6hybZU
```
<br><br>
### data_random.py

Session 메신저 데이터베이스(session.db)에서 첨부파일 복호화에 필요한 `data_random` 값을 추출합니다. 추출된 `data_random`은 `modernKey`와 함께 HMAC-SHA256을 이용해 AES 복호화 키를 생성하는 데 사용됩니다.

```python
[실행 결과]
[*] SignalSecret #1 복호화 시도 중...
[+] 복호화 성공! SQLCipher Key (hex): e0ec6de02a377c48b179351992ade4982540ba184324e24f9f92b8795f679696
[+] part5987799258691517169.mms → data_random (hex): 224d5e20b35627a8ef659d06297b73e2b1f7a7c334ac672d25fb705abc0a6bb2
[+] part2279796186127612959.mms → data_random (hex): 90bb6e01564149fd13fb04d9f7dc0c5ffaecb5d1e95d5a923eb400778281a44a
[+] part6948537578243099421.mms → data_random (hex): 787e7189d7955dc1d048e3296ddd9f90f1ae067dcd7db0881d29b2daaa16bf7d
[+] part1311447108672342993.mms → data_random (hex): 391743b67d292f346f7ced5060811a810d27929b64f3cd151be2690689bb1090
[+] 매칭된 mms 파일 수: 4
```
<br><br>
### aes_key.py

Session 메신저에서 전송된 .mms 임시파일을 복호화하기 위한 AES 키를 자동으로 파생합니다.

modernKey, data_random, 그리고 HMAC-SHA256 알고리즘을 통해 각 파일에 대응되는 256비트 AES 키를 생성합니다.

```python
[실행 결과]
[+] 총 4개 .mms 파일의 AES 키:
 - part5987799258691517169.mms → AES Key: d58722b1e5d094d9cc8d2b154eab42e421c6d28b2eb22ea5f78d57c4308fc493
 - part2279796186127612959.mms → AES Key: 12956a6fb8122e477a55fd43000fad3b6ac38ed7f56b42343068cd6e0ede4496
 - part6948537578243099421.mms → AES Key: fda3f56e3d8de79452bde5c53e5213138041bdc9b5552f9a63387c15bfacb783
 - part1311447108672342993.mms → AES Key: 9306eecbf678bcadfca8bda93992c6c00534ce390ca7f054e953f0b9e79066d3
```
<br><br>
### decrypt_mms_files.py

Session 메신저의 암호화된 `.mms` 첨부파일을 자동으로 복호화하여 원본 이미지, 문서 파일(.jpg, .png, .doc, .docx, .hwp, .pdf ...)로 복원해주는 코드입니다.

복원된 원본 이미지 파일은 dec_mms_files/ 디렉터리에 저장

```python
[실행 결과]
[+] 총 4개 .mms 파일 복호화 중...
[+] part5987799258691517169.mms → 복호화 완료 → dec_mms_files\part5987799258691517169.mms.jpg
[+] part2279796186127612959.mms → 복호화 완료 → dec_mms_files\part2279796186127612959.mms.jpg
[+] part6948537578243099421.mms → 복호화 완료 → dec_mms_files\part6948537578243099421.mms.docx
[+] part1311447108672342993.mms → 복호화 완료 → dec_mms_files\part1311447108672342993.mms.docx
```

