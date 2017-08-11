node-qiwi-api
================
Официальная [документация](https://developer.qiwi.com/qiwiwallet/qiwicom_ru.html) к Qiwi api.

Get started
----------------
Для начала вам необходимо получить токен на сайте [Qiwi](https://qiwi.com/api).
Инициализируйте новый кошелек с выданным токеном:
```js
var Qiwi = require('node-qiwi-api').Qiwi;
var Wallet = new Qiwi(token);
```
Теперь вы можете получать информацию о кошельке и совершать переводы на другие кошельки, мобильный телефон и карты.

Информация об аккаунте
----------------
```js
Wallet.getAccountInfo((err, info) => {
  if(err) {
    /*hanle error*/
  }
  console.log(info);
}
```
История операций
----------------
```js
Wallet.getOperationHistory(requestOptions, (err, operations) => {
  if(err) {
    /*hanle error*/
  }
  console.log(operations);
}
```
requestOptions включают в себя: 
* **rows** - Число платежей в ответе, для разбивки отчета на части. Целое число от 1 до 50. Обязательный параметр.
* **operation** - Тип операций в отчете, для отбора. Допустимые значения: ALL - все операции, IN - только пополнения, OUT - только платежи, QIWI_CARD - только платежи по картам QIWI (QVC, QVP). По умолчанию ALL
* **sources** - Источники платежа, для отбора. Каждый источник задается как отдельный параметр и нумеруется элементом массива, начиная с нуля (sources[0], sources[1] и т.д.). Допустимые значения: QW_RUB - рублевый счет кошелька, QW_USD - счет кошелька в долларах, QW_EUR - счет кошелька в евро, CARD - привязанные и непривязанные к кошельку банковские карты, MK - счет мобильного оператора. Если не указаны, учитываются все источники
* **startDate** - Начальная дата поиска платежей (формат ГГГГ-ММ-ДДTчч:мм:ссZ). По умолчанию, равен вчерашней дате. Используется только вместе с endDate
* **endDate** - Конечная дата поиска платежей (формат ГГГГ-ММ-ДДTчч:мм:ссZ). По умолчанию, равен текущей дате. Используется только вместе с startDate
* **nextTxnDate** - Дата транзакции (формат ГГГГ-ММ-ДДTчч:мм:ссZ), для отсчета от предыдущего списка (см. параметр nextTxnDate в ответе). Используется только вместе с nextTxnId
* **nextTxnId** - 	Номер предшествующей транзакции, для отсчета от предыдущего списка (см. параметр nextTxnId в ответе). Используется только вместе с nextTxnDate
Максимальный допустимый интервал между startDate и endDate - 90 календарных дней.

Например информация о 25 исходящих платежах может быть получена следующим образом:
```js
Wallet.getOperationHistory({rows: 25, operation: "OUT"}, (err, operations) => {
  /* some code */
}
```
Перевод на Qiwi кошелек
----------------
```js
Wallet.toWallet({ id: 1000 * Date.now(), amount: '0.01', comment: 'test', account: '+79261234567' }, (err, data) => {
  if(err) {
    /* handle err*/
    }
  console.log(data);
}
```
* **id** - Клиентский ID транзакции (максимум 20 цифр). Должен быть уникальным для каждой транзакции и увеличиваться с каждой последующей транзакцией. Для выполнения этих требований рекомендуется задавать равным 1000*(Standard Unix time в секундах).
* **amount** - Сумма
* **comment** - Комментарий к платежу.
* **account** - Номер телефона получателя (с международным префиксом)

Перевод на мобильный телефон
----------------
Ничем не отличается от перевода на кошелек, за исключением того, что номер указывается без международного префикса:
```js
Wallet.toWallet({ id: 1000 * Date.now(), amount: '0.01', comment: 'test', account: '9261234567' }, (err, data) => {
  if(err) {
    /* handle err*/
    }
  console.log(data);
}
```
