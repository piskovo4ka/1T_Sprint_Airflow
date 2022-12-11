# 1T_Sprint_3.6

Вот, что в итоге получилось! 

![alt text]https://github.com/piskovo4ka/1T_Sprint_3.6/blob/main/img/2022-12-11_07-12-53.png






Практика. Airflow.
1. Для того, чтобы использовать airflow локально, можно воспользоваться двумя способами:
     a. Установить непосредственно сам Airflow, инструкция: https://airflow.apache.org/docs/apache-airflow/stable/installation/index.html
     b. Воспользоваться официальным образом Airflow для Docker: https://airflow.apache.org/docs/docker-stack/index.html
Данный способ является рекомендуемым. В LMS у вас должна быть доступна видео-инструкция по сборке образа Airflow.
2. Создайте в произвольном месте новую папку. Скачайте внутрь этой папки docker-compose файл с официальным образом.
3. Запустите установку сборки образа Airflow
4. После успешной установки вам будет доступе Airflow UI по адресу: http://localhost:8080. Для авторизации используйте сочетание airflow-airflow
5. Перейдите в раздел DAGs и попробуйте открыть и запустить любой из тестовых примеров.
6. Вы можете обратить внимание, что в вашей папке появились 3 подпапки:
     a. dags
     b. logs
     c. plugins
7. В папке dags мы будем вести нашу основную разработку. Давайте создадим наш первый DAG – для это создайте .py-файл в папке dags и наполните следующим содержимым:


8. with DAG … - это код для создания DAG’а, в скобках перечислены параметры на уровень всего DAG’а
     a. dag_id – уникальное имя dag’a, не должно повторяться у других dag’ов
     b. start_date – дата начала работы нашего DAG’a
     c. schedule – настройка интервала запуска DAG’а. В данном примере DAG запускается один раз каждые сутки. Для того, чтобы разобраться как правильно настраивать формат для данного параметра советуем потренироваться на https://crontab.guru/
9. Внутри тела DAG’a мы можем описать работу некоторого количества операторов. В airflow представлено множество операторов для разных целей. В данном примере вы можете наблюдать использование BashOperator – для выполнения команд на языке bash и PythonOperator – для выполнения python-скриптов. Также в Airflow существует возможность добавлять свои собственные операторы.
10. Сохраните ваш .py-скрипт с описанием DAG’а и перейдите в Airflow UI. Далее, в разделе DAGs обновите страницу и найдите только что созданный DAG по dag_id, который мы задавали при его создании. Если вдруг вы не обнаружите ваш новый DAG, то обратите внимание на верхнюю часть страницы, там будут отображены логи ошибок импорта DAG’ов.
11. Перейдите в новый DAG и запустите его выполнение. Если все предыдущие шаги прошли удачно, то вы увидите перед собой два зеленых прямоугольника с названиями task’ов, которые вы задавали в скрипте создания DAG. Чтобы оценить результаты работы ваших task’ов – нажмите на прямоугольник с task’ом и перейдите в раздел logs – там вы сможете наблюдать, что ваши task’и вывели в консоль то, что нужно. Также в разделе logs можно мониторить ошибки.
1.      12. Поздравляем, вы успешно создали первый DAG в Airflow! Теперь попробуем усовершенствовать его:

a.      Создайте еще один PythonOperator, который генерирует два произвольных числа и печатает их. Добавьте вызов нового оператора в конец вашего pipeline с помощью >>.

b.      Попробуйте снова запустить ваш DAG и убедитесь, что все работает верно.

c.      Если запуск произошел успешно, попробуйте изменить логику вашего Python-оператора следующим образом – сгенерированные числа должны записываться в текстовый файл – через пробел. При этом должно соблюдаться условие, что каждые новые два числа должны записываться с новой строки не затирая предыдущие.

d.      Создайте новый оператор, который подключается к файлу и вычисляет сумму всех чисел из первой колонки, затем сумму всех чисел из второй колонки и рассчитывает разность полученных сумм. Вычисленную разность необходимо записать в конец того же файла, не затирая его содержимого.

e.      Измените еще раз логику вашего оператора из пунктов 12.а – 12.с. При каждой новой записи произвольных чисел в конец файла, вычисленная сумма на шаге 12.d должна затираться.

f.       Настройте ваш DAG таким образом, чтобы он запускался каждую минуту в течение 5 минут.

g.     *Добавьте новый Sensor-оператор, который возвращает True в том случае, если выполнены следующие условия:

                                                    i.     Файл существует

                                                   ii.     Количество строк в файле минус одна последняя - соответствует кол-ву запусков

                                                 iii.     Финальное число рассчитано верно.

h.      *Дальнейшие задачи pipeline выполнять только если верно предыдущее условие.

i.     *Добавьте образ Postgres в docker-compose файл. Для подключения к Postgres из Airflow зарегистрируйте подключение и используйте BaseHooks. Важно: использовать уже существующий образ Postgres, в котором развернута база Airflow – нельзя.

j.      *Создайте оператор вейтвления, который в случае, если текстовый файл существует и не пустой - создает таблицу аналогичной структуры в Postgres и наполняет ее данными из файла(за иск. последнего числа). В случае если файл не существует или в нем нет данных – печатает сообщение об ошибке(*** для любителей экстрима – выбросьте в данном случае собственное исключение).

k.       *Если удалось создать таблицу с данными – создайте Postgres-оператор, который добавляет в таблицу еще одну колонку, в которой будут размещаться числа рассчитанные по логике из пункта 12.d. 

Пример:

value_1 value_2 coef

1            2             -1                          1 – 2 = -1

4            2             1                           (1 + 4) – (2 + 2) = 1

7            5             3                           (1 + 4 + 7) – (2 + 2 + 5) = 3

 

Выложите ваш dag-файл или весь проект в ваш репозиторий. Для тех, кто развернул Postgres, приложите данные для подключения или docker-compose-файл.

