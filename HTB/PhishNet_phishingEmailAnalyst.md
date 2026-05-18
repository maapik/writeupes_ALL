Бухгалтерская служба получает срочный запрос на оплату от известного поставщика. 
Электронное письмо выглядит правдоподобно, но содержит подозрительную ссылку и вложение в формате .zip, в котором скрыто вредоносное ПО. 
Ваша задача — проанализировать заголовки письма и раскрыть схему злоумышленника.

1. Executive Summary (Краткое резюме)
Тип инцидента: Фишинговая рассылка (Phishing / Malspam).
Вектор атаки: Вредоносное вложение с двойным расширением, упакованное в ZIP и закодированное в Base64 внутри .eml файла.

Поскольку файл письма (.eml) представляет собой обычный текст, я не стал использовать тяжелые анализаторы, а провел первичный осмотр через CLI Linux (команда less).

1)Какой IP-адрес у отправителя?
начальная часть письма:

<img width="1082" height="556" alt="image" src="https://github.com/user-attachments/assets/b45b1abd-d2fa-4560-8387-b0f2d3bb2a5c" />

ответ:45.67.89.10

2)Какой почтовый сервер перенаправил это письмо, прежде чем оно попало к адресату?

ответ:203.0.113.25

3)Какой адрес электронной почты у отправителя?

ответ:finance@business-finance.com

4)Какой адрес электронной почты указан в поле «Ответить»?

ответ:support@business-finance.com

5)Каков результат проверки SPF (Sender Policy Framework) для этого письма?

ответ:pass

6)Какой домен указан в фишинговом URL в письме?

основной текст письма:

<img width="1095" height="542" alt="image" src="https://github.com/user-attachments/assets/bc9fc806-004e-4bae-b045-a614f01057ad" />

ответ:secure.business-finance.com

7)Какое вымышленное название компании указано в письме?

ответ:Business Finance Ltd.

8)Как называется вложение в этом письме?

ответ:Invoice_2025_Payment.zip

9)Какой хэш SHA-256 вложения?

я использовал команду:echo UEsDBBQAAAAIABh/WloXPY4qcxITALvMGQAYAAAAaW52b2ljZV9kb2N1bWVudC5wZGYuYmF0zL3ZzuzIsR18LQN+h62DPujWX0e7 | base64 -d | sha256sum
1) echo выводит нашу строку
2) мы декодируем его в base64
3) sha256sum получаем хэш 256

ответ:8379C41239E9AF845B2AB6C27A7509AE8804D7D73E455C800A551B22BA25BB4A

10)Как называется вредоносный файл, содержащийся во вложении ZIP?

мы понимаем что у нас есть кодировка вредоносного вложения

<img width="965" height="135" alt="image" src="https://github.com/user-attachments/assets/3608274f-ab55-4ab8-b6fb-86ec3e9ebb53" />

Обнаружив Base64-строку в теле письма, я извлек ее содержимое и сохранил в формате ZIP-архива с помощью перенаправления вывода: 
echo UEsDBBQAAAAIABh/WloXPY4qcxITALvMGQAYAAAAaW52b2ljZV9kb2N1bWVudC5wZGYuYmF0zL3ZzuzIsR18LQN+h62DPujWX0e7 | base64 -d > "Invoice.zip". 
При попытке анализа архива через 7zip был обнаружен исполняемый файл с двойным расширением: invoice_document.pdf.bat, использующий технику маскировки под безопасный PDF-документ.

после чего я попытался разархивировать через 7zip:

<img width="623" height="63" alt="image" src="https://github.com/user-attachments/assets/a1351f44-cc91-4080-95c9-08f976f77d47" />

<img width="746" height="423" alt="image" src="https://github.com/user-attachments/assets/96ee89e6-4e94-4faa-a199-772e3e5ccf9c" />

мы видим ошибку , и как раз тут же мы видим доп опции, где у нас видно кк раз таки вредоносный файл:invoice_document.pdf.bat

ответ:invoice_document.pdf.bat

11)Какие методы из MITRE ATT&CK связаны с этой атакой?

<img width="1289" height="528" alt="image" src="https://github.com/user-attachments/assets/45c8abea-bee1-41d6-8c63-fe9557584485" />

ответ:T1566.001

IOC:
Имя файла: invoice_document.pdf.bat

SHA-256 хеш: 8379C41239E9AF845B2AB6C27A7509AE8804D7D73E455C800A551B22BA25BB4A

kill chain:
1)Delivery (Доставка): Злоумышленник отправляет email с поддельными заголовками и вредоносным вложением.
2)User Execution (Действия пользователя): Жертва распаковывает архив и запускает файл-приманку, обманувшись двойным расширением .pdf.bat.
3)Execution (Исполнение): Вредоносный скрипт (bat-файл) выполняется в контексте ОС жертвы, инициируя дальнейшее заражение.

