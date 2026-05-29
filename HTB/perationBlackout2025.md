Сценарий:
Talion подозревает, что злоумышленник проводил антивиртуализационные проверки, чтобы избежать обнаружения в изолированных средах. Ваша задача — проанализировать журналы событий и определить конкретные методы, используемые для обнаружения виртуализации. 
Byte Doctor нужны доказательства того, что злоумышленник выполнял проверки реестра или процессов для выполнения этих проверок.

Что мы имеем (Артефакты):
У нас есть только два файла логов Windows (.evtx), которые зафиксировали всю активность PowerShell в системе:  Windows-Powershell-Operational.evtx — здесь нас больше всего интересует Event ID 4104 (Script Block Logging). 
Он записывает исходный код скриптов и команд, которые запускал хакер.  

<img width="632" height="111" alt="image" src="https://github.com/user-attachments/assets/f4719f61-b823-4384-b0db-cead3ed4edea" />

<img width="601" height="213" alt="image" src="https://github.com/user-attachments/assets/a813aac5-e6aa-4336-b3c9-f55b2bc65b1a" />


Microsoft-Windows-Powershell.evtx — здесь мы смотрим Event ID 800 (Pipeline Execution Details). 
Он показывает, как эти команды выполнялись и, иногда, какой результат они возвращали.  

Цепочка действий хакера (Kill Chain):

Шаг 1: Базовая проверка железа (Производитель)
Сначала хакер решает проверить базовую информацию о компьютере.
Действие: Он использует встроенный инструмент Windows (WMI) и выполняет команду Get-WmiObject -Class Win32_ComputerSystem. 

<img width="838" height="277" alt="image" src="https://github.com/user-attachments/assets/31850cc9-0b44-4095-b75e-d0e34bc8f7c0" />


Логика: У реального компьютера производителем будет условный Dell, ASUS или Lenovo. У виртуальной машины в поле "Model" или "Manufacturer" будет написано "VMware", "VirtualBox" или "QEMU".
Win32_ComputerSystem - это класс WMI который содержит в себе информацию о модели и производителе

Шаг 2: Проверка датчиков температуры
Это очень хитрый и популярный трюк.
Действие: Хакер выполняет запрос SELECT * FROM MSAcpi_ThermalZoneTemperature.

<img width="703" height="376" alt="image" src="https://github.com/user-attachments/assets/97cdbcb2-9bb6-4666-9b2e-58c3b9e0fb68" />


Логика: На реальных физических материнских платах стоят датчики температуры. 
Виртуальные машины (в большинстве случаев) не эмулируют аппаратные термодатчики.
Если запрос возвращает ошибку или пустоту, хакер понимает — это виртуалка.


Шаг 3: Загрузка тяжелой артиллерии (Скрипт Check-VM)
Поняв базовые вещи, хакер решает прогнать систему через специализированный скрипт для комплексной проверки.
Действие: Он загружает в память PowerShell-скрипт. В логах мы видим функцию под названием Check-VM.  Логика: Анализ логов показывает, что это скрипт из известного хакерского фреймворка Nishang. 

<img width="733" height="172" alt="image" src="https://github.com/user-attachments/assets/b804ce54-5307-45c2-ab4a-3c4cf207e857" />


<img width="539" height="85" alt="image" src="https://github.com/user-attachments/assets/38a8b6b8-9f79-45f5-a252-076099f1c4e7" />


доква что скрипт на фреймворке nishang

<img width="730" height="404" alt="image" src="https://github.com/user-attachments/assets/5e804953-759b-4c46-80af-8ec34b99cd04" />

часть кода где показывается проверка по папкам(как я понял)

Внутри этого скрипта уже зашиты "отпечатки" всех популярных виртуалок (Hyper-V, VMWare, Virtual PC, Virtual Box и т.д.).  


Шаг 4: Глубокая проверка системы скриптом 
Запущенный скрипт Check-VM начинает искать следы виртуализации в недрах системы:
Проверка реестра: Скрипт лезет в ветку реестра HKLM:\SYSTEM\ControlSet001\Services. Зачем? Он ищет специфичные службы, которые ставятся только на виртуалки (например, службы интеграции vmicheartbeat или vmicvss для Hyper-V).  
Проверка процессов: Скрипт проверяет запущенные программы в памяти (через команду Get-Process). Например, для VirtualBox он ищет процессы vboxservice.exe и vboxtray.exe — это драйверы и утилиты, которые ставятся для удобной работы внутри виртуалки.  

<img width="585" height="392" alt="image" src="https://github.com/user-attachments/assets/a3d40b87-108e-4d43-bdff-a6d3ef93defa" />

Шаг 5: Итог работы хакерского скрипта
Скрипт успешно отрабатывает и находит следы. 

<img width="555" height="361" alt="image" src="https://github.com/user-attachments/assets/71c84aef-9e24-43ec-a035-b2d311cd1bbb" />


Действие: В логах (Event ID 800) аналитик видит финальный вывод, который скрипт вернул хакеру.  Результат: Выводятся строки "This is a Hyper-V machine." и "This is a VMWare machine.".
