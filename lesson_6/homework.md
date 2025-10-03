# Ходырев Иван dos30 Домашнее задание №6
1) Добавил команду в crontab, которая очищает кэш apt раз в месяц - 11 числа в 16:00
 <img width="1086" height="598" alt="image" src="https://github.com/user-attachments/assets/22e64a20-adf2-454f-8383-cdefde3a4704" />
2) Запустить демон nodejs-приложения через systemd

Уствноил nodejs:

<img width="967" height="193" alt="image" src="https://github.com/user-attachments/assets/1ba7c846-551b-4465-9a7f-38588e1adfdb" />

<img width="971" height="146" alt="image" src="https://github.com/user-attachments/assets/73842509-2147-4498-beb7-456053b8212f" />

добавил index.js

<img width="862" height="797" alt="image" src="https://github.com/user-attachments/assets/2788b012-31cd-4352-bae9-e382d196a79e" />

Запустил и проверил приложение:

<img width="725" height="382" alt="image" src="https://github.com/user-attachments/assets/6835cd08-4f01-4056-8c58-fe1e6a9f32d3" /> 

<img width="749" height="129" alt="image" src="https://github.com/user-attachments/assets/7298e9d9-8a55-4cde-b28b-16cf5d2969d3" />

Создал myapp.service и запустил

<img width="599" height="177" alt="image" src="https://github.com/user-attachments/assets/4bc13c18-0a7b-4d18-8b5f-93492c2eb596" />

<img width="1433" height="414" alt="image" src="https://github.com/user-attachments/assets/07a97f07-6df3-41b6-9720-26c753ef01b1" />

Дергаю curl 'kill' и проверяю статус:

<img width="580" height="93" alt="image" src="https://github.com/user-attachments/assets/bc93e412-43eb-45e5-8581-25a1069cc4e8" />

<img width="1094" height="454" alt="image" src="https://github.com/user-attachments/assets/754bb5f6-d436-4dc5-ab55-3f33d9e480ae" />

Добавил рестар в myapp.service

<img width="587" height="281" alt="image" src="https://github.com/user-attachments/assets/f50e51c9-59f3-47b9-966f-9d5b7dad8be7" />

Перезапустил:

<img width="731" height="421" alt="image" src="https://github.com/user-attachments/assets/58093cd4-3f64-44d1-94b3-be50f3a32175" />

Проверяю:

<img width="540" height="80" alt="image" src="https://github.com/user-attachments/assets/02a2699a-74a7-4ae8-b9ae-183c3b082f3c" />

<img width="1016" height="349" alt="image" src="https://github.com/user-attachments/assets/470d1de5-c733-4132-b2de-13a31039d8c3" />

Добавил пользователей и группу:

<img width="569" height="177" alt="image" src="https://github.com/user-attachments/assets/58d7fbea-e067-4239-8d88-a26b34001359" />

Добавил вывод логов:

<img width="587" height="329" alt="image" src="https://github.com/user-attachments/assets/39b3369c-68d8-4032-bec5-cbcd9cc11e8c" />

создал файл /etc/rsyslog.d/100-myapp.conf:

<img width="606" height="110" alt="image" src="https://github.com/user-attachments/assets/de0b4c4b-5ea1-4423-8192-2206e4a01cc4" />

Обновил вывод логов:

<img width="896" height="345" alt="image" src="https://github.com/user-attachments/assets/d46f1d35-b33b-41bd-8fdc-dd68877488d6" />

<img width="877" height="143" alt="image" src="https://github.com/user-attachments/assets/e9f77fe0-5c7d-4732-a801-eb6d9bffbdec" />

Добавил logrotate:

<img width="494" height="390" alt="image" src="https://github.com/user-attachments/assets/9fd5d0bb-19ab-46cf-9d90-d8e30695be5e" />

Логи из journalctl:

<img width="1541" height="757" alt="image" src="https://github.com/user-attachments/assets/8255bb63-f488-40a2-ab16-fededc7674b0" />

Долго возился с выводом логов в /var/log/myapp/debug.log - решилось тем, что создал вручную папку myapp и файлы debug.log и error.log,
прописал на myapp, debug.log, error.log владельца syslog и группу adm
сделал sudo systemctl restart rsyslog, повторно потрогал курлы
и получил запись в debug.log

<img width="1282" height="275" alt="image" src="https://github.com/user-attachments/assets/8fd45f0f-c0bb-465d-9314-d6e1ae99fc56" />














