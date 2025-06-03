Лабораторная работа №3
Поднимаем контейнеры

docker compose up


Создаем и активируем виртуальное окружение Python:

python3 -m venv venv
source venv/bin/activate
Установим:

pip install psycopg2-binary matplotlib
Создадим скрипт 'mmonitor.py' который будет выполнять автоматический тест, который при сбое primary переключается на standby, собирает метрики числа записей и выводит их динамику на одном графике. Состоит из:

Класс MetricsCollector

Хранит списки: timestamps (время), primary_rows и standby_rows (число записей).
_row_count(db) → SELECT COUNT(*) из data_table.
collect() добавляет текущее время и оба счётчика.
visualize() строит график, выводит максимумы, сохраняет в picture.png и вызывает plt.show().
Класс SmartInserter

Флаг use_primary выбирает, куда писать (по умолчанию – primary).
_try_insert(db, val) пытается вставить запись в заданную БД.
switch_to_standby() → SELECT pg_promote() на standby, делая его новым primary.
write(val) сначала вставляет в primary; при неудаче переключается на standby и продолжает туда.
Основная main()

Создаёт SmartInserter и MetricsCollector.
Вызывает setup_table().
В цикле 100 раз: • write() → вставка, • collect() → лог, • sleep(0.1).
По окончании или Ctrl+C — visualize() строит график.
Теперь запускаем mmonitor.py, а далее стопим primary командой:

python3 mmonitor.py
docker stop pg_primary
Через пару секунд (не больше 10) программа завершается, а на выходе создает график.



Итого: 
