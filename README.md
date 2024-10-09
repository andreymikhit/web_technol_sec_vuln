# web_technol_sec_vuln
GeekBrains
# Beб-технологии: уязвимости и безопасность

### Урок 1. Введение в веб
1.Установите VirtualBox и виртуальную машину с Ubuntu. Все дальнейшие задания рекомендуется делать на Ubuntu.

2.Установите и запустите nginx. Введите в адресной строке браузера http://localhost и убедитесь, что nginx показывает приветственную страницу. Найдите ваш запрос к домашней странице в логах nginx.

3(*).Установите Burp Suite в качестве прокси. Попробовать основные функции: history, interception, sitemap, repeater.

### Урок 2. URL
1.Открыть терминал. Установить программы host и whois (если уже не установлены). Выяснить IP адрес сайта https://geekbrains.ru. Выяснить IP адрес http://localhost.

2. Найти файл hosts на своем компьютере (виртуальной машине). Сделать так, чтобы адрес сайта http://attacker.com и http://victim.com соответствовал адрес http://localhost.

3. Запустите nginx и создайте в корневом каталоге директорию blog, а в директории blog создайте файл post.txt. Составьте полный URL (со схемой http или https) к этому файлу и запросите его через браузер.

4. (*) Поменяйте порт, который слушает ваш сервер с 80 на 31337. Перезапустите сервер. Выполните задание 3 с учетом того, что сервер слушает на новом порту.

### Урок 3. HTTP
1.Создать файл test.txt в корневом каталоге сервера. Получить этот файл через браузер.
- Установить в терминале программу curl, получить тот же файл с помощью этой программы.
- Установить telnet или netcat, получить тот же файл с помощью одной из этих программ.

2.Создать на сервере файл sensitive_info.txt. Добавить базовую HTTP авторизацию для этого файла.
- Получить этот файл через браузер.
- Получить тот же файл с помощью curl и telnet или netcat.

3. Открыть инструменты разработчика, вкладку Сеть (Network). Зайти на сайт https://geekbrains.ru. Проанализировать куки каждого запроса за HTML и картинками. Какие запросы уходят с куками, а какие без кук? Почему в каждом из случаев происходит именно такое поведение?

4. * Для выполнения этого задания вам потребуется:
1)Настроить домены attacker.com, sub.attacker.com, sub.sub.attacker.com, victim.com. Каждый из этих доменов должен указывать на 127.0.0.1
2)Настроить установку кук для доменов. Добавьте следюущий конфигурационный файл nginx (изменив root сервера на свой):
```
$ cat /etc/nginx/sites-available/cookie-research.conf
server {
listen 80;
server_name attacker.com;
root /var/www/html;

    location / {
     add_header "Set-Cookie" "test1=attacker-com_sub-attacker-com; Domain=sub.attacker.com";
            add_header "Set-Cookie" "test2=attacker-com_victim-com; Domain=victim.com";
            try_files $uri $uri/ =404;
    }
}

server {
listen 80;
server_name sub.attacker.com;
root /var/www/html;

    location / {
     add_header "Set-Cookie" "test3=sub-attacker-com_attacker-com; Domain=attacker.com";
            try_files $uri $uri/ =404;
    }
}

server {
listen 80;
server_name sub.sub.attacker.com;
root /var/www/html;

    location / {
     add_header "Set-Cookie" "test4=sub-sub-attacker-com_attacker-com; Domain=attacker.com";
            try_files $uri $uri/ =404;
    }
}
```
Проведите исследование механизма проставления кук, для этого попробуйте установить следующие куки:
1. С доменаattacker.comна доменsub.attacker.com
2. С доменаattacker.comна доменvictim.com
3. С доменаsub.attacker.comна доменattacker.com
4. С доменаsub.sub.attacker.comна доменattacker.com`
По каждому пункту ответьте на вопросы:
1. Куда установились куки?
2. Если не установились, то почему?
Обобщите полученные знания и напишите вывод в формате: "Домен может проставлять куки для себя, для ... и ..., но не может проставлять куки для ..., ... и ...".

5. (*) Сгенерировать самоподписанный сертификат и разместить его на своем сервере.

### Урок 4. HTML и CSS
1.Создайте HTML-страницу на которой будет картинка с другого домена и ссылка. Пишите без тегов <html> и <body>, изучите реакцию браузера в панели веб-разработчика “Элементы” (Elements).

2. Добавить стили к HTML: поменять цвет, позицию, прозрачность и размер всех элементов на странице из задания 1.

3. Дан HTML документ. Необходимо добиться того, чтобы парсинг документа успешно завершался и была отображена надпись All this text in </h1> tag! Amazing! стандартным стилем <h1>.

4. (*) Создать iframe. Изменить стили iframe, так чтобы он был растянут на весь экран, находился сверху основной страницы и был полупрозрачен.
Открыть в получившемся iframe страницу https://geekbrains.ru. Разобраться что произошло и почему.


### Урок 5. JavaScript
1.Создать страницу javascript.html с кнопкой, при нажатии на которую появлялся алерт с текстом JavaScript is magiс!

2. Написать функцию на языке JavaScript, которая будет создавать тег с картинкой и вставлять картинку в HTML.

3. Создать файл array.json и разместить его в корневом каталоге своего сервера.
Содержимое файла array.json:
```
{
"summe": [10, 0, -2, 114, 125]
}

Написать программу на языке JavaScript, которая будет делать XHR запрос на http://localhost/array.json, парсить полученный JSON и выводить сумму чисел массива summe.

4. ()Защититься от выполнения JavaScript кода на victim.com/xss.php?text=<script>alert(1)</script> (код страницы ниже) с помощью HTML энкодинга.

<body>
<?php
echo $_GET["text"];
?>
</body>
```

5. () Написать функцию, которая будет сохранять ввод пользователя в переменную и отправлять значение переменной на http://attacker.com


### Урок 6. Браузеры: концепции безопасности
Открыть консоль браузера на http://attacker.com и запросить файл с http://victim.com с помощью XHR. Изучить реакцию браузера в консоли.
Примечание: домены attacker.com и victim.com должны резолвиться в 127.0.0.1, конфиг nginx тоже должен отдавать все так, чтобы на начало задания работало оба алерта. ### Добавить данную политику CSP на сайте http://victim.com. Загрузить страницу victim.com/csp.php?js=<script/src=//attacker.com/evil.js></script>, посмотреть что произошло. Исправить политику CSP так, чтобы вредоносный код не выполнялся.
Файл csp.php 
```<body> <h3>Whatever _malicious_ you inserted shouldn’t be executed!</h3> <?php echo $_GET["js"]; ?> <h3>But legitimate code still should execute</h3> <script src="http://victim.com/some.js"></script> </body>```
Политика CSP Content-Security-Policy: default-src 'none'; script-src 'unsafe-inline' http:
Файл some.js alert("I’m legitimate!")
Файл evil.js alert("I’m evil!")
Не дать вредоносному коду http://victim.com/hw-6-3.php?name=<script>alert("hacked")</script> выполниться на странице http://victim.com/hw-6-3.php (представлена ниже) с помощью политики CSP (написать политику CSP). Легитимный код при это должен выполняться.
Страница hw-6-3.php <body> <h3>Whatever _malicious_ you inserted shouldn’t be executed!</h3> ```<?php echo $_GET["name"]; ?> <h3>But legitimate code still should execute</h3> <script src="http://victim.com/some.js"></script> <script src="http://sub.victim.com/some.js"></script> </body>```
(*) Обойти политику CSP: script-src ‘unsafe-eval’ http://victim.com http://partner.com http://home.victim.com на странице http://victim.com/hw-6-4.html?text=123. Сделать безопасно, понять почему теперь безопасно.
Файл hw-6-4.html <body> <h3>Legitimate code still should execute</h3> <script src="/hw-6-4.js"></script> </body>
Файл hw-6-4.js ```function okFunction () { alert("I'm legitimate!"); } setTimeout(document.URL.split("#")[1], 1000); setTimeout(okFunction, 1000);```
(*) Установить bWAPP. Задания необходимо сдавать в формате скриншотов, которые сопровождаются комментариями. Отчет должен быть в pdf формате. Пожалуйста, пишите в названии pdf файла домашнего задания своё имя.

### Урок 7. Same Origin Policy
1.Это задание выполняется на домене attacker.com. Прочитать куки домена attacker.com и вывести их. Попробовать прочитать и вывести куки домена victim.com.

2. Дан сайт, который при нажатии на кнопку меняет цвет фона. Дописать, чтобы при открытии сайта JS обращался в web storage за цветом фона и восстанавливает его.
```
<body>
<script>
function changeBodyColor(color) {
document.body.style.backgroundColor = color;
}
</script>
<button onclick="changeBodyColor('red')">Make it hell!</button>
<button onclick="changeBodyColor('green')">Make it grass!</button>
</body>
```

Задание 3. () Самостоятельно настроить CORS на http://victim.com. Разрешить http://localhost с помощью CORS делать запросы к http://victim.com.

Задание 4. () Решить как можно больше XSS на уровне low в bWAPP.

### Урок 8. Современные ClientSide-технологии и другие технологии веба
Перед выполнением задания необходимо:
Создать страницу user_info.html на домене localhost
Добавить на домене localhost заголовок CORS: Access-Control-Allow-Origin: * На домене attacker.com создать страницу, которая:
Выполнит XHR запрос за страницей localhost/user_info.html
Выведет содержимое страницы user_info.html Настройте CORS так, чтобы вывести содержимое страницы user_info.html мог только http://localhost или http://trustedhost.com.
Вы - злоумышленник, поэтому в Firefox вы заходите только через приватное окно. Вы хотите украсть супер секретные данные со страницы http://victim.com/hw-8-2.php. На ней установлена защита по сессии. Но вы знаете пользователя у которого эта сессия есть и что секрет отдается postMessage после открытия страницы…
Заманите пользователя на страницу http://attacker.com/hw-8-2-attacker.html и получите секретные данные.
Допишите страницу http://victim.com/hw-8-2.php, так чтобы она была безопасной. Страница hw-8-2.php ```<?php if ($_COOKIE['sessionid'] == '0a7016d5f7346a6f14b273a66e0770fb7d6608769f233156570e878a1397a175') { echo "<body> Hello, sir! Sending data to window.opener! <script> window.opener.postMessage('TOP secret data', '*'); </script> </body>"; } else { echo "Access denied"; } ?>``` 3 () Пройти RCE (os command injection) на bWAPP 4 () Пройти WebStorage на bWAPP (A-6 webstorage) Задания необходимо сдавать в формате
