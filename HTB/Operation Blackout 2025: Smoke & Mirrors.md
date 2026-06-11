сценарий:
Специалист по анализу инцидентов Рейес расследует скрытую атаку после взлома, в ходе которой отсутствуют несколько ожидаемых журналов безопасности и оповещения Windows Defender. 
Он подозревает, что злоумышленник использовал методы обхода защиты, чтобы отключить или манипулировать средствами контроля безопасности, что значительно осложнило обнаружение. 
Используя экспортированные журналы событий, ваша задача — выяснить, как злоумышленник скомпрометировал средства защиты системы, чтобы остаться незамеченным.

1:Злоумышленник отключил защиту LSA на скомпрометированном хосте, изменив ключ реестра. Какой полный путь к этому ключу реестра?
ответ: HKLM\SYSTEM\CurrentControlSet\Control\LSA
изменение реестра происходит через команду reg add прописав это мы найдем:

<img width="532" height="105" alt="image" src="https://github.com/user-attachments/assets/8e93b02d-0e7c-426d-a157-ad2bc6fe08f2" />

2:Какую команду PowerShell злоумышленник выполнил первой, чтобы отключить Windows Defender?
ответ:Set-MpPreference -DisableIOAVProtection $true -DisableEmailScanning $true -DisableBlockAtFirstSeen $true
Наиболее часто используемая команда PowerShell для отключения Windows Defender — это 'SetMpPreference'. Ища по этой команде мы находим

<img width="708" height="553" alt="image" src="https://github.com/user-attachments/assets/2e127220-395f-4661-93d6-a95d9fcfc813" />

3:Злоумышленник загрузил патч AMSI, написанный на PowerShell. 
Какая функция в DLL-файле модифицируется скриптом для фактического отключения AMSI?
учитывая вопрос про dll я ищу по .dll фильтру. я нашел IntPtr a = GetProcAddress(...)
подставив все буквы в одну строку мы получаем ответ:AmsiScanBuffer

<img width="728" height="483" alt="image" src="https://github.com/user-attachments/assets/ecc022ee-fa0b-49df-93b1-1fadc69740ef" />

4:Какую команду использовал злоумышленник для перезагрузки компьютера в безопасном режиме?
ответ:bcdedit.exe /set safeboot network
ищу через фильтр safeboot мы находим команду bcdedit /set safeboot network
т.к это ултилита а точнее exe , то полная команда запуска 
ответ: bcdedit.exe /set safeboot network

<img width="535" height="181" alt="image" src="https://github.com/user-attachments/assets/0574b4cc-a60d-4c90-a5fd-457cac248212" />

5:Какую команду PowerShell использовал злоумышленник для отключения регистрации истории команд PowerShell?
cmd ултилита Set-PSReadlineOption настраивает поведение модуля PSReadLine во время редактирования командной строки.
по этому филдьтру я нахожу ответ:Set-PSReadlineOption -HistorySaveStyle SaveNothing

<img width="644" height="375" alt="image" src="https://github.com/user-attachments/assets/b2ffb8b1-e9f9-41a0-aaa6-0c20864b6a8c" />
