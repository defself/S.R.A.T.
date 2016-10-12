# S.R.A.T.
## Service of Remarkable Air Tickets

Сделать ресурс обслуживания пассажиров самолета.

Технологии для использования:
- PostgreSQL в качестве адаптера бд
- Redis для кеширования запросов
- ActiveAdmin для админки
- Sidekiq для очистки кеша
- Rspec для тестирования

Ресурс должен представлять собой 3 страницы подключеные в routes.
- страница отображения свободных мест самолета (/seats/:flight_id) — здесь нужно вывести все места самолета кроме занятых списком

> A1 B1 C1 &nbsp;&nbsp; D1 E1 F1

> A2 B2 C2 &nbsp;&nbsp; D2 E2 F2

> A3 B3 C3 &nbsp;&nbsp; D3 E3 F3

где цифра - номер ряда, а буква - сидение.
Желательно отображать проход по центру.
Данный запрос нужно кешировать. т.е. если в кеше нет значений делается выборка из базы и подготавливается данные, потом происходит запись в кеш. следующий запрос будет только брать данные из кеша.

- страница с формой бронирования места (/booking/:flight_id)
  * имя
  * фамилия
  * ряд (должен быть в соответствии с самолетом)
  * сидение (должно быть в соответствии с самолетом)
  * обед (на выбор из списка: чай, кофе, бутерброд или полноценный обед. может быть пустым)
  при отправке валидной формы отправляется запрос на Sidekiq Worker для инвалидации кешей по :flight_id

- страница с количеством заказанных обедов (/meals/:flight_id)
  * список
  * чай: 2
  * кофе: 7
  * бутерброд: 5
  * полноценный обед: 0
  * данный запрос нужно кешировать. т.е. если в кеше нет значений делается выборка из базы и подготавливается данные, потом происходит запись в кеш. следующий запрос будет только брать данные из кеша.

По запросу /admin попадаем в ActiveAdmin где есть такие вкладки:
- Cache
  * страница Cahe Settings
    * количество ключей для запроса /seats
    * кнопка инвалидации кеша для запроса /seats
    * количество ключей для запроса /meals
    * кнопка инвалидации кеша для запроса /meals
    кнопки инвалидации отправляют запрос на Sidekiq Worker
- Data
  * Airplanes (страница список самолетов с возможностью фильтровать по параметрам, просматривать, добавлять и удалять)
  * Flights (страница список полетов с возможностью фильтровать по параметрам, просматривать, добавлять и удалять)
  * Meals (страница список обедов с возможностью фильтровать по параметрам, просматривать, добавлять и удалять)
  * Bookings (страница список бронированных мест со скопами по полету без возможности редактировать или удалять)
для админки желательна авторизация (не обязательно)

В базе нужно хранить:
- самолеты
  * имя
  * количество рядов
  * количество мест в ряду (кратно 2)
- полеты
  * референс на самолет
  * дата + время вылета
  * дата + время прибытия
- обеды
  * имя
- букинги
  * имя
  * фамилия
  * ряд
  * место
  * референс на полет
  * референс на обед

Сделать дата миграции для заполнения базы:
  * 1 самолет с 5 рядами по 2 сидения
  * 2 полета на этом самолете
  * 4 обеда (чай, кофе, бутерброд, полноценный обед)

Необходимые тесты Rspec:
  * тесты на валидность моделей
  * тесты записи / удаления кеша

Остальные тесты не обязательны но приветствуются.
