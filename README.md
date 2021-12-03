# Authorize From Oberton

## Website backend:

Формирует параметры авторизации и отрпавляет пользователя на https://oberton.io/deeplink с параметрами

**type**\
Константное значение **auth**\
Обязательный параметр, по нему oberton поймёт, что это запрос авторизации через кошелёк.

**id**\
Значение **string**\
Рекомендуемое значение: идентификатор запроса по которому бэкенд сайта найдёт у себя **otp**

**otp**\
Значение **string**\
Рекомендуемое значение: одноразовый пароль привязанный к генерации этой ссылки или этого QR кода. Рекомендуется связать с текущей сессией пользователя.

**callbackUrl**\
Значение **string**\
url на который кошелёк отправит POST запрос, подтверждающий владение пользователем конкретным кошельком.

**warningText**\
Значение **string**\
Сообщение которое пользователь увидит при авторизации.

### Пример
https://oberton.io/deeplink?type=auth&id=testid&otp=testopt&callbackUrl=https://callback.url&warningText=Attention

## Пользователь подписывает транзакцию
Пользователь в кошельке видит запрос на авторизацию с текстом из параметра **warningText**\
Пользователь подтверждает транзакцию

## Oberton отправляет POST сайту

Oberton берёт адрес кошелька пользователя и otp. Подписывает получившуюся строку приватным ключём пользователя. Получившиюся подпись отправляет на **callbackUrl** пользователя вместе с сопутствующими данными

### Пример
ПРИЛОЖИТЬ ПРИМЕР ЗАПРОСА И BODY ОТВЕТА

## Сайт получает POST запрос
1. По параметру id сайт находит otp у себя в локальной БД
2. Берём из ответа **addr** адрес кошелька и **pk** проверяем через БЧ что у этого кошелька есть такой публичный ключ.
3. Проверяем корректность подписи.

```javascript
   const { hash } = await this.crypto.sha256({
	data: Buffer.from(`${otp}${callbackUrl}${address}`, 'utf-8').toString(
		'base64'
	),
   });


   const { succeeded } = await this.client.crypto.nacl_sign_detached_verify({
      unsigned: Buffer.from(hash, 'hex').toString('base64'),
      signature: Buffer.from(signature.replace(/ /g, '+'), 'base64').toString('hex'),
   });
```
4. Пользователя можно авторизовать в системе.