#Описание интерфейса сервиса приема платежей через систему EKO
- [Введение](#Введение)
- [Форма регистрации платежа](#form-registration)
- [Фрагмент формы регистрации платежа](#fragment-registration)
- [Форма проверки статуса платежа](#form-check)
- [Фрагмент формы регистрации платежа](#fragment-check)
- [Проверка информации о платеже](#check)


## Введение
Для передачи информации между веб-сайтом продавца и системой ЕКО используютcя HTML-формы: <br />
1. Форма регистрации платежа - генерируется веб-сайтом продавца для формирования запроса на проведение платежа в  системе ЕКО и передачи его через веб-браузер покупателя. <br />
2. Форма проверки статуса платежа - генерируется системой ЕКО для передачи оповещения о платеже на веб-сайт продавца. <br />
Оповещение передается без использования веб-браузера покупателя. 

## Форма регистрации платежа
Эта форма передает запрос с веб-сайта продавца в систему ЕКО через веб-браузер покупателя. <br /> 
Она должна иметь следующие атрибуты и поля: <br />
<b>Action</b> — https://edinaya.com:8443/eko/makePayment.do <br />
<b>Method</b> - POST  <br />
<b>Fields</b> - поля, передаваемые в форме, описаны в таблице ниже: <br />

Название поля | Обязательное | Тип поля | Описание
:--- | :---: |:---:|:---:|:---:
agentId |да| Целое число, от 1 до 999999 | Идентификатор агента (сайта) продавца, на который покупатель должен совершить платеж.
orderId |да| Целое число, от 1 до 999999 | В этом поле продавец задает уникальный номер заказа (счета) в соответствии со своей системой учета.
agentName |да| Строка |Торговое название агента,выводится пользователю
amount |да| Дробное число,больше нуля, дробная часть отделяется точкой, два знака после точки.| Сумма платежа, которую продавец желает получить от покупателя.
goods | да | Строка |Название товара (или счета), которые оплачивает клиент
currency |нет | Строка, 3 символа | Валюта платежа (только RUR)
email |да |Строка до 50 символов |Адрес электронной почты покупателя
phone | да | 11 или более цифр | Телефон покупателя в международном формате. Например, для России: 79161234567
preference |да |Целое число |Способ оплаты, который будет выбран при оплате за товар. см. Приложение №1
agentTime | да | Строка в формате «HH:mm:SS dd.MM.yyyy»| Дата создания платежа на стороне агента
limitTime  |нет  |Строка в формате «HH:mm:SS dd.MM.yyyy» | Дата, по истечении которой, заказ считается просроченным. По умолчанию 24 часа
successUrl |нет | Строка длиной до 1024 символов| Адрес на который будет перенаправлен пользователь в случае успешной оплаты
failUrl | нет | Строка длиной до 1024 символов | Адрес на который будет перенаправлен пользователь в случае отмены оплаты
addInfo_N | нет | Строка длиной до 1024 символов | Все поля формы, имеющие в названии префикса "addInfo_N"(где N порядковый номер),обрабатываются системой ЕКО автоматически и передаются на сайт продавца. (Кодировка UTF-8)
redirect |Нет |Любое значение |Если указано, то будет автоматически произведен переадресация пользователя на систему оплаты.
sign | да |Строка | Контрольная подпись оповещения осоздании платежа, которая используется для проверки однозначной идентификации отправителя.

* Если в процессе регистрации платежа произошла ошибка и в запросе не указан failUrl
то система выводит ошибку 500, иначе к строке переадресация failUrl добавляется 
query параметр error с описанием ошибки.
* Если процесс регистрации платежа прошел успешно и указан successUrl, то к строке
переадресации successUrl добавляется query параметр paymentId с номером заказа в 
системе ЕКО. <br/>

### Фрагмент формы регистрации платежа

```

<form action="URL регистрации платежа" method="POST">
 <input type="hidden" name="agentId" value="8686"> 
 <input type="hidden" name="agentName" value="Superstore"> 
 <input type="hidden" name="orderId" value="87876"> 
 <input type="hidden" name="amount" value="166.70"> 
 <input type="hidden" name="email" value="user@example.ru"> 
 <input type="hidden" name="phone" value="79090000001"> 
 <input type="hidden" name="agentTime" value="20:35:67 01.01.2010"> 
 <input type="hidden" name="goods" value="Notebook"> 
 <input type="hidden" name="currency" value="RUR"> 
 <input type="hidden" name="preference" value="1"> 
 <input type="hidden" name="successUrl" value="http://example.ru/success.html"> 
 <input type="hidden" name="failUrl" value="http://example.ru/fail.html"> 
 <input type="hidden" name="sign" value="f849a1c57cccb372ec4a3a2e04d2feba">
 <input type="hidden" name="addInfo_1" value="addinf1"> 
 ...
 ...
 <input type="submit" value="Оплатить "> 
</form>

 ```

### Форма проверки статуса платежа
Эта форма оповещения о платеже, отправляемая продавцу при изменении статуса платежа.
Она имеет следующие атрибуты и поля:

**Action** - URL информирования о статусе платежа (предоставляется клиентом ЕКО) <br/>
**Method** - POST <br/>
**Fields** - поля, передаваемые в форме, описаны в таблице ниже: <br/>

Название поля | Обязательное | Тип поля | Описание
:---|:---:|:---:|---:
agentId | да | Целое число, от 1 до 999999 | Идентификатор агента (сайта) продавца, на который покупатель должен совершить платеж.
orderId | да |Целое число, от 1 до 999999 | В этом поле продавец задает уникальный номер заказа (счета) всоответствии со своей системой учета.
paymentId | да | Целое число большее нуля | В этом поле передается номер покупки в системе EKO (Номер транзакции).
amount | да | Дробное число, больше нуля, дробная часть отделяется | Сумма платежа, которую продавец желает получить от покупателя. точкой, два знака после точки.
currency | нет | Строка, 3 символа | Валюта платежа (только RUR)
phone | да | 11 или более цифр | Телефон покупателя в международном формате. Например, для России: 79161234567
preference | да | Целое число | Способ оплаты, который будет выбран при оплате за товар. см. Приложение №1
paymentStatus | да | Целое число | Статус платежа может иметь следующие значения : <br/> 1 — Прошел успешно <br/> 2 — Фатальная ошибка, платеж не  прошел <br/> 3 — В очереди <br/>
paymentDate | да | Строка в формате «HH:mm:SS dd.MM.YYYY» | Дата и время реального прохождения платежа в системе ЕКО
goods | Да | Строка | Описание предоставляемой услуги или товара
agentName | Да | Строка | Название агента, понятное плательщику, например брэнд или товарный знак под которым работает агент
sign | да | Строка | Контрольная подпись оповещения о выполнении платежа, которая используется для проверки целостности полученной информации и однозначной идентификации отправителя.
comment | нет | Строка длиной до 1024 символов | Комментарий к платежу (Например,  при оплате по смс — это текстотправленного сообщения), текст  закодирован алгоритмом url encoding.
addInfo_N | нет | Строка длиной до 1024 символов | Все поля, переданные с веб-сайта продавца в "Форме регистрации  платежа"

### Фрагмент формы регистрации платежа

```

<form action="URL информирования о статусе платежа" method="POST">
 <input type="hidden" name="agentId" value="8686"> 
 <input type="hidden" name="paymentId" value="12345678">
 <input type="hidden" name="orderId" value="87876"> 
 <input type="hidden" name="amount" value="166.70"> 
 <input type="hidden" name="phone" value="79090000001"> 
 <input type="hidden" name="currency" value="RUR"> 
 <input type="hidden" name="preference" value="1"> 
 <input type="hidden" name="goods" value="Рога, 10 кг">
 <input type="hidden" name="agentName" value="Рога и Копыта (TM)">
 <input type="hidden" name="paymentStatus" value="1">
 <input type="hidden" name="paymentDate" value="13:12:03 10.01.2010">
 <input type="hidden" name="sign" value="f849a1c57c66b372ec4a3a2e04d2feba">
 <input type="hidden" name="addInfo_1" value="addinfoxxxxxxxx"> 
 ...
 ...
</form>

```
### Проверка информации о платеже
При выполнении платежа система ЕКО высылает оповещение о платеже через "Форму проверки статуса платежа" на URL информирования о статусе платежа, указанный
продавцом. Сообщение считается доставленным, если страница обрабатывающая уведомление, вернет 'OK', иначе оповещение о выполненном платеже будет повторяться.
Мы рекомендуем вам проверить данные, полученные через "Форму оповещения о платеже": <br/>

1. Проверить, действительно ли данные переданы от системы ЕКО
2. Проверить, не исказились ли данные в процессе передачи
3. Проверить сумму платежа 
4. Проверить идентификатор агента (сайта) продавца

Проверка источника данных
При формировании счета
При формировании счета в форме регистрации платежа, продавец передает системе ЕКО 
реквизиты платежа и контрольную подпись, позволяющую однозначно идентифицировать 
агента.
При формировании контрольной подписи, продавец "склеивает" значения полей разделяя их 
символом “#”, в одну строку в следующем порядке
1. Идентификатор агента (продавца) (agentId); 
2. Уникальный номер заказа (счета) (orderId)
3. Дата создания платежа (agentTime)
4. Сумма платежа (amount)
65. Телефон покупателя (phone)
Пример : 
MD5(8686# 87876#13:12:03 10.01.2010#166.70#79090000001#MD5(secret))
secret — секретная фраза агента, сохраняется через личный кабинет агента
MD5 формируется последовательность из 32-х шестнадцатеричных цифр в соответствии с
широко распространенным алгоритмом Message Digest 5 (MD5).
При отправке оповещения о статусе платежа
Высылая оповещение о проведении платежа, система ЕКО передает реквизиты платежа и
контрольную подпись, позволяющую проверять неизменность передаваемых данных. 
Контрольная подпись данных о платеже позволяет продавцу проверять источник данных,
переданных на продавцу.
При формировании контрольной подписи система ЕКО "склеивает" значения полей разделяя
их “#”, передаваемых "Форма проверки статуса платежа", в одну строку в следующем
порядке: 
1. Идентификатор агента (продавца) (agentId); 
2. Уникальный номер заказа (счета) (orderId)
3. Номер покупки в системе EKO (paymentId)
4. Сумма платежа (amount)
5. Телефон покупателя (phone)
6. Статус платежа (paymentStatus)
7. Дата и время реального прохождения платежа (paymentDate)
8. Секретный ключ (пароль) агента 
Пример : 
MD5(8686# 87876# 12345678# 166.70# 79090000001#1# 13:12:03 10.01.2010#MD5(secret))
MD5 формируется последовательность из 32-х шестнадцатеричных цифр в соответствии с
широко распространенным алгоритмом Message Digest 5 (MD5).
Приложение №1
Коды типов платежей
Код типа платежа Описание Примечания
2 Терминалы "QIWI"
76 Яндекс.деньги
8 Киви-кошелек
10 Терминалы "Башкомснаб Банк"
12 Терминалы Новоплат
124 Терминалы Элекснет
125 Пластиковые карты
126 Альфа-клик В параметре addInfo_1 магазин должен 
передавать идентификатор плательщика в 
Alfa-Click
128 SMS оплата (мобильная 
коммерция)
129 Веб мани
130 Салоны связи Евросеть и 
салоны МТС
8
