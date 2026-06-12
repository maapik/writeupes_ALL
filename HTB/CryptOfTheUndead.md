<img width="754" height="63" alt="image" src="https://github.com/user-attachments/assets/d0724b1a-77a5-4bcd-8a35-f8acc92abe50" />имеем CTF таск:
Все мои важные файлы были зомбированы печально известной бандой киберпреступников-зомби. Можете ли вы вернуть их и успокоить?
имеются два файла crypt и flag.txt.undead
1. я запустил данный файл на vm итог:

<img width="294" height="57" alt="image" src="https://github.com/user-attachments/assets/6aa1c258-a0ab-48d8-8176-1c5a849084a1" />

чтобы истользовать на мнужен файл в папке у нас есть уже flag.txt.undead
я попытался открыть crypt как в прошлом пункте но выдала ошибка

<img width="477" height="70" alt="image" src="https://github.com/user-attachments/assets/19c425b5-a008-441d-badb-c5bce50ed578" />

что переводится как: То, что является нежитью, не может быть зашифровано.(гугл переводчик)

2. учитывая что 1 способ не помог я открываюю сам crypt в ida
открыв и декомпилировав программу(горяч клавиша f5) я вижу фулл программу

<img width="873" height="650" alt="image" src="https://github.com/user-attachments/assets/2120f881-77c2-4189-93e8-a88dc71d08cb" />


<img width="1061" height="697" alt="image" src="https://github.com/user-attachments/assets/a8b29e2b-41f5-4cff-8c52-76de563bfed0" />

я ищу строчку с нашей ошибкой чтобы понять её работу и вижу эту часть кода

<img width="569" height="134" alt="image" src="https://github.com/user-attachments/assets/dec6e29e-7b68-4ca9-9986-b08c676a6575" />

по логике вещей  я перевел всю часть кода как
old_filename = файл
если конец old_filename оканчивается как .undead то ошибочка
(если максимально легко)

поняв это я решил раз проверка не дает запустить файл с .unhead давай-ка уберем его, я просто перименовал flag.txt.undead в flag.txt
выполнил команду из 1 пункта и получил такой ответ:

<img width="754" height="63" alt="image" src="https://github.com/user-attachments/assets/809f1e6d-33bf-4477-9e8a-e404c4a19386" />

файл зомбирован. Праллельно с сэтим я следил за самой папкой и увидел что снова вместо flag.txt появился flag.txt.undead. ПО логике вещей он шифрует файл
открыв этот самый новый flag.txt.undead мы находим флаг

