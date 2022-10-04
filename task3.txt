1.Вывести количество фильмов в каждой категории, отсортировать по убыванию.

SELECT category.name,count(film_category.film_id) FROM category
Inner Join film_category ON category.category_id = film_category.category_id
GROUP BY category.name
ORDER BY count(film_category.film_id) desc

2.Вывести 10 актеров, чьи фильмы большего всего арендовали, 
отсортировать по убыванию.

SELECT actor.actor_id,count(film_actor.film_id) FROM actor
Inner Join film_actor ON actor.actor_id = film_actor.actor_id
Group By actor.actor_id
Order By count(film_actor.film_id) desc
LIMIT 10

3.Вывести категорию фильмов, на которую потратили больше всего денег.

SELECT category."name" ,max(payment.amount) FROM category
Inner Join film_category On category.category_id= film_category.category_id
Inner Join film On film_category.film_id = film.film_id
Inner Join inventory 
ON film.film_id = inventory.film_id
Inner Join rental ON inventory.inventory_id = rental.inventory_id
Inner Join payment On rental.rental_id = payment.rental_id
GROUP BY category."name"
ORDER BY max(payment.amount) desc
LIMIT 1

4.Вывести названия фильмов, которых нет в inventory. 
Написать запрос без использования оператора IN.

SELECT * FROM film
where NOT EXISTS 
(SELECT inventory.film_id FROM inventory where film_id = film.film_id)

5.Вывести топ 3 актеров, которые больше всего появлялись в фильмах 
в категории “Children”.
Если у нескольких актеров одинаковое кол-во фильмов, вывести всех.

SELECT film_actor.actor_id,count(film.film_id) FROM film_actor
Inner Join film ON film_actor.film_id = film.film_id
Inner Join film_category ON film.film_id = film_category.film_id
Inner Join category ON film_category.category_id = category.category_id
WHERE category.name like 'Children'
GROUP BY film_actor.actor_id
HAVING count(film.film_id) IN (
	SELECT DISTINCT count(film.film_id) FROM film_actor
	Inner Join film ON film_actor.film_id = film.film_id
	Inner Join film_category ON film.film_id = film_category.film_id
	Inner Join category ON film_category.category_id = category.category_id
	WHERE category.name like 'Children'
	GROUP BY film_actor.actor_id
	ORDER BY count(film.film_id) DESC
	LIMIT 3)
	ORDER BY count(film.film_id) DESC

6.Вывести города с количеством активных и неактивных клиентов 
(активный — customer.active = 1).
Отсортировать по количеству неактивных клиентов по убыванию.

SELECT city.city, count(customer.active = 1),count(customer.active = 0) FROM city
Inner Join address ON city.city_id = address.city_id
Inner Join customer ON address.address_id = customer.address_id
GROUP BY city.city
ORDER BY count(customer.active = 0) desc

7.Вывести категорию фильмов, у которой самое большое кол-во часов
 суммарной аренды в городах (customer.address_id в этом city), 
и которые начинаются на букву “a”. То же самое сделать 
для городов в которых есть символ “-”. Написать все в одном запросе.

SELECT category.name,max(rental.rental_id) FROM city
Inner Join address ON city.city_id = address.city_id
Inner Join customer ON address.address_id = customer.address_id
Inner Join payment ON customer.customer_id = payment.customer_id
Inner Join rental ON payment.rental_id = rental.rental_id
Inner Join inventory ON rental.inventory_id = inventory.inventory_id
Inner Join film_category ON inventory.film_id = film_category.film_id
Inner Join category on film_category.category_id = category.category_id
Where city.city like 'A%' and city.city like '%-%' 
GROUP BY category.name
ORDER BY max(rental.rental_id) DESC
LIMIT 1

