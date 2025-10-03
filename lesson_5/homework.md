#Ходырев Иван Домашнее задание №5
1) Установка mongodb
<img width="1539" height="319" alt="image" src="https://github.com/user-attachments/assets/d066e228-b20d-4be6-97ae-3b217560ffff" />
<img width="1301" height="827" alt="image" src="https://github.com/user-attachments/assets/62c5cdf7-0e78-4814-bf5a-4c5041be9bc4" />

Сохраняю для истории. mongodb не завелась, нейросеть и гугл сказали, что проблема в avx, на stack overflow нашел решение https://stackoverflow.com/questions/68862012/unable-to-install-remove-mongodb-org-on-ubuntu-18-04-bionic-mongodb-org-tools и была еще проблема с libssl1.1, нашел решение здесь https://www.dmosk.ru/miniinstruktions.php?mini=mongodb-ubuntu&ysclid=mdybwrm2e3573708577
Скрин проблемы:
<img width="1040" height="661" alt="image" src="https://github.com/user-attachments/assets/cfb42ed4-fa0a-4852-93b9-77d4859a7997" />

Переустановка mongodb на версию 4.4.8
Удалил все пакеты mongodb
<img width="950" height="397" alt="image" src="https://github.com/user-attachments/assets/d6f9cb85-24fb-4b87-a1c0-cbddd45ebd0e" />
Импортировал открытый ключ, используемый системой управления пакетами и использовал инструкцию предназначеную для Ubuntu 18.04 (Bionic)
<img width="1556" height="138" alt="image" src="https://github.com/user-attachments/assets/5346ecab-846c-4501-8336-c93f3669f278" />
Поставил пакет libssl1.1:
<img width="1219" height="295" alt="image" src="https://github.com/user-attachments/assets/fc913b92-232c-489b-957b-32d9a14ade76" />
Установил mongodb
<img width="1543" height="426" alt="image" src="https://github.com/user-attachments/assets/fef17f9f-7570-43c8-9049-defa281c1cd1" />
Проверил версию и запустил
<img width="1147" height="838" alt="image" src="https://github.com/user-attachments/assets/119a35d2-3255-4390-874d-bfbf6a5da4ec" />

Запускаю mongodb
<img width="1817" height="624" alt="image" src="https://github.com/user-attachments/assets/84ce41c8-b5eb-4944-b4bc-f62bfd35c7c5" />
Перехожу в базу test, создаю таблицу и пользователя
<img width="1121" height="736" alt="image" src="https://github.com/user-attachments/assets/ffa6050e-6828-4e88-b581-6217f7284cec" />
Пробую подключиться под пользователем manager и проверить доступ
<img width="1810" height="716" alt="image" src="https://github.com/user-attachments/assets/b4af1435-3d16-4964-bbc5-107299cbe4d9" />
Скорректировал /etc/mongod.conf
<img width="1540" height="824" alt="image" src="https://github.com/user-attachments/assets/467349b0-e492-49e2-a932-a27a041a2e7d" />
Перезапустил mongodb
<img width="1038" height="339" alt="image" src="https://github.com/user-attachments/assets/c8510fe3-d2cf-4f8f-a91c-c3e76256463c" />
Пробую подключиться под manager и проверить доступ повторно
<img width="1785" height="732" alt="image" src="https://github.com/user-attachments/assets/5ea4e742-589b-461e-84a5-40b72f929773" />
