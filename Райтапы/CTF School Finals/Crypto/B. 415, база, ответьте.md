При подготовке к выезду на одно из CTF-соревнований одна из команд предложила нам обмен информацией через простейший формат связи. Мы согласились, однако, точных инструкций, как это читать, не поступало. Поможете? А то мы так на этих же соревнованиях и прогорим.

*По заданию дан файл «task_415.txt».*
___
Дан файл, содержащий строку вида:
```
Yankee_three_Romeo_mike_charlie_two_November_oscar_bravo_two_nine_sierra_echo_three_charlie_whiskey_charlie_mike_sierra_x-ray_bravo_mike_delta_foxtrot_November_Golf_Foxtrot_foxtrot_November_Foxtrot_nine_whiskey_Mike_Whiskey_whiskey_whiskey_delta_Foxtrot_nine_three_alpha_Delta_Foxtrot_sierra_Mike_one_nine_november_Mike_three_Romeo_zero_Mike_Whiskey_five_november_X-ray_three_Alpha_whiskey_Mike_Whiskey_five_zero_charlie_one_eight_x-ray_charlie_one_nine_romeo_Mike_Whiskey_five_kilo_November_Foxtrot_nine_juliet_Mike_Delta_Bravo_sierra_foxtrot_Quebec_equals_equals
```

Первая идея была выписать первые буквы каждого слова, однако в конце были слова "равно" (equals), что подсказало, что это может быть закодировано в base64. Для этого решили заменить все слова, обозначающие цифры ("one", "two", "three", и т.д.) на соответствующие цифры, а слово "равно" заменить на символ "=". Затем выписали первые буквы остальных слов.

В результате получили строку:
`Y3Rmc2Nob29se3cwcmsxbmdfNGFfNF9wMWwwdF93aDFsM19nM3R0MW5nX3AwMW50c18xc19rMW5kNF9jMDBsfQ==`

Раскодировали эту строку из base64 с помощью `DenCode`, получили флаг.
## `ctfschool{w0rk1ng_4a_4_p1l0t_wh1l3_g3tt1ng_p01nts_1s_k1nd4_c00l}`