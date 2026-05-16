СЦЕНАРИЙ:
Вы, аналитик по анализу киберугроз, расследующий операцию «Работа мечты», выяснили, что группировка Lazarus использовала различные вредоносные программы и инструменты, созданные на заказ, для проведения своих операций.  
Ваша задача — проанализировать и собрать информацию о вредоносных программах, используемых этой APT-группировкой.

нам даны файлы :

<img width="368" height="181" alt="image" src="https://github.com/user-attachments/assets/b420ee0c-d3f4-4ce4-a0d9-9ed8c96e7260" />

первый вопрос 
1:Согласно MITRE ATT&CK, с какими ранее известными вредоносными программами имеет сходство DRATzarus?
зайдя на сайт и вбив DRATzarus и перейдя мы увидим
<img width="909" height="166" alt="image" src="https://github.com/user-attachments/assets/2bcb1e3d-4a12-43d5-bca5-cce1f9b396f5" />
ответ:  Bankshot
2:Какую функцию Windows API использует DRATzarus для определения наличия отладчика?
пролистав чуть ниже мы увидим строчку отклонение использования отладчика, как раз таки она и показывает какую функцию использует арт группа
<img width="1258" height="49" alt="image" src="https://github.com/user-attachments/assets/74a4e614-6197-47c1-94f4-3e137f27581a" />

ответ:IsDebuggerPresent
3:Torisma — еще одна вредоносная программа, используемая группировкой Lazarus. По данным MITRE, она шифрует командные каналы с помощью операции XOR и какого-то другого метода.
если вобъем так же как и в 1 вопросе Torisma и перейдя по ней, прокрутив вниз мы увидим строчки рядом с шифрованием XOR

<img width="1160" height="58" alt="image" src="https://github.com/user-attachments/assets/f9ee5cd5-fb6e-4203-89fa-bd2af918b0c6" />

ответ:VEST-32

4:Какой метод упаковки использовался для маскировки Torisma?
еще пролистывая мы вниз ищем в названии обфускацию файлов находя это рядом мы увидим использование 
<img width="946" height="52" alt="image" src="https://github.com/user-attachments/assets/052fc8c0-af24-484b-b093-3c85a1f00dc8" />

ответ: Iz4 compression

далее практика:
5:Проанализировать предоставленный ISO-файл и определить, какой исполняемый файл в нем содержится?
для анализа мы можем использовать cmd команду isoinfo (я использую wsl)
<img width="750" height="117" alt="image" src="https://github.com/user-attachments/assets/6c7eecea-cb4b-4cc8-82f0-06d9ad788bae" />

iso файл это просто запакованная папка с другими файлами, скопируем оттуда эти файлы и увидим 
<img width="181" height="32" alt="image" src="https://github.com/user-attachments/assets/50cd1bae-c28d-4bfc-9bbe-28428b6b8d14" />

ответ:InternalViewer.exe

6:Исполняемый файл, упомянутый в предыдущем вопросе, был переименован. Можете ли вы назвать его исходное название?
чтобы это сделать нам понадобится инструмент exiftool
введя команду мы увидим:
<img width="543" height="27" alt="image" src="https://github.com/user-attachments/assets/cb4579d7-ab04-4ab1-9d10-c15f290ecae1" />

ответ:SumatraPDF.exe
7:По данным VirusTotal, когда EXE-файл из предыдущего вопроса впервые был обнаружен в дикой природе? (UTC)
Проводим анализ хэш-суммы файла (SHA256) в базе VirusTotal:

<img width="543" height="42" alt="image" src="https://github.com/user-attachments/assets/554fc9a0-37d6-4fd0-b36e-96f5ed0e8b80" />

ответ: 2020-08-13 08:44:50

8:Какой упаковщик использовался для упаковки исполняемого файла из вопроса 6? (Полное название)
Чтобы ответить на этот вопрос, мы проанализируем исполняемый файл с помощью pescan — инструмента командной строки для анализа PE-файлов (Portable Executable). 
Он помогает выявлять потенциальные угрозы безопасности, обнаруживая упаковщики, подозрительные разделы, методы защиты от анализа и другие аномалии в исполняемом файле.
<img width="874" height="311" alt="image" src="https://github.com/user-attachments/assets/880fb0c3-657f-42da-8876-df3dbe9d4e71" />

мы видим секции если скопируем и вставим в браузер мы увидим ответ:
<img width="606" height="97" alt="image" src="https://github.com/user-attachments/assets/09300643-9358-4e52-8609-0670a6acd841" />

ответ:Ultimate Packer for eXecutables

9:Какой полный URL указан в макросе в документе Salary_Lockheed_Martin_job_opportunities_confidential.doc?
чтобы сделать это мы воспользуемся инструментом olevba , она  извлекает и анализирует макросы VBA, встроенные в документы Office.
введя команду(olevba Salary_Lockheed_Martin_job_opportunities_confidential.doc) мы увидим это :
<img width="726" height="509" alt="image" src="https://github.com/user-attachments/assets/a7ea93d6-604b-4ab1-90a6-ab8e1766adb7" />

ответ:https://markettrendingcenter.com/lk_job_oppor.docx
10: Кто является автором документа Salary_Lockheed_Martin_job_opportunities_confidential.doc?
чтобы понять мы можем через виртуалку винды просто посмотреть в свойствах файла
<img width="261" height="60" alt="image" src="https://github.com/user-attachments/assets/f4a90ed3-32e3-4cba-8e1b-6d7f3ae31b6c" />

вот два ответа на 10 и 11 вопросы
ответ:Mickey
ответ на 11: Challenger
12:Проанализируйте документ "17.dotm". В каком каталоге была создана подозрительная папка? (Формат: укажите путь, начинающийся сразу после <ПОЛЬЗОВАТЕЛЬ>. Обратите внимание на заполнитель.)
так тут я сначала решил воспользоваться vipermonkey но он не сработал, поэтому я пошел обходным путем:
просмотрим файл через olevba:

<img width="832" height="428" alt="image" src="https://github.com/user-attachments/assets/1253a686-8b2a-454a-a702-00204e349add" />

Исходный код макроса обфусцирован/удален (VBA Stomping), доступен только скомпилированный p-code. 
т.е хакеры удаляют читаемый исходный код макроса и оставляют только скомпилированный p-code, чтобы обмануть антивирусы.
тогда поработает через программу pcode2code
введя и полистая мы понимаем что этот скрипт работает сначала создает правильную службу по типу svhost , а потом проверяет нахождение каталога, если его нет то создает 
вот часть кода где находится ответ:

<img width="799" height="408" alt="image" src="https://github.com/user-attachments/assets/23c653ef-f67a-4471-91b8-b32091391305" />

ответ: \AppData\Local\Microsoft\Notice
13:Какой подозрительный файл был проверен на наличие в этом каталоге?
в том же скрине 12 задания проверка файла была у wsuser.db
ответ:wsuser.db

всем спасибо. За этот таск мы научились пользоваться такими инструментами как exiftool,olevba,pcode2code.
