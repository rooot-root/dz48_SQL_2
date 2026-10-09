# Домашнее задание к занятию "SQL. Часть 2" - Кошелев Дмитрий Владимирович


###Задание 1
Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию:

фамилия и имя сотрудника из этого магазина;
город нахождения магазина;
количество пользователей, закреплённых в этом магазине.

SELECT 
    s.first_name,
    s.last_name,
    c.city,
    COUNT(cu.customer_id) AS customer_count
FROM store st
JOIN staff s ON st.store_id = s.store_id
JOIN address a ON st.address_id = a.address_id
JOIN city c ON a.city_id = c.city_id
JOIN customer cu ON st.store_id = cu.store_id
GROUP BY st.store_id, s.first_name, s.last_name, c.city
HAVING COUNT(cu.customer_id) > 300;

Пояснение:
store соединяем со staff по store_id — получаем сотрудника магазина.
Через address → city получаем город магазина.
customer присоединяем по store_id — это покупатели, закреплённые за магазином.
Группируем и фильтруем через HAVING (агрегат нельзя в WHERE).

###Задание 2
Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

SELECT COUNT(*) AS film_count
FROM film
WHERE length > (SELECT AVG(length) FROM film);

Пояснение:
Подзапрос (SELECT AVG(length) FROM film) вычисляет среднюю длительность.
Внешний запрос считает фильмы с length больше этого значения.

###Задание 3
Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.

SELECT 
    DATE_FORMAT(p.payment_date, '%Y-%m') AS payment_month,
    SUM(p.amount) AS total_amount,
    COUNT(r.rental_id) AS rental_count
FROM payment p
JOIN rental r ON p.rental_id = r.rental_id
GROUP BY DATE_FORMAT(p.payment_date, '%Y-%m')
ORDER BY total_amount DESC
LIMIT 1;

Пояснение:
DATE_FORMAT(..., '%Y-%m') группирует платежи по месяцам.
SUM(p.amount) — сумма платежей за месяц.
JOIN rental по rental_id нужен, чтобы посчитать количество аренд за тот же месяц.
Сортируем по убыванию суммы и берём первую строку (LIMIT 1).
Вариант для Задания 3 (без JOIN, если аренды считать отдельно)

