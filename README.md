SQL Homework Assignment


Better option: make a github repo, put the sql files for pagilla in it. Make a README.md file and put all the queries and questions in there formated nicely 
and give instructions on how to populate the database.

To create this database
Open up psql in the directory of these files and run these commands.

\i pagila-schema.sql
\i pagila-insert-data.sql
\i pagila-data.sql

1a. You need a list of all the actors’ first name and last name

    Select first_name, last_name from actor;

1b. Display the first and last name of each actor in a single column in upper case letters. Name the column Actor Name

    Select upper (first_name) || ' ' || upper(last_name) from actor as Actor_Name; 

2a. You need to find the id, first name, and last name of an actor, of whom you know only the first name of "Joe."
 What is one query would you use to obtain this information?

    Select actor_id, first_name, last_name from actor where lower(first_name) = lower ('Joe');

2b. Find all actors whose last name contain the letters GEN. Make this case insensitive.

	Select actor_id, first_name, last_name from actor where lower(last_name)  like lower('%GEN%') ;

2c. Find all actors whose last names contain the letters LI. This time, order the rows by last name and first name, in that order. Make this case insensitive.

	Select first_name, last_name from actor where lower(last_name) like lower('%LI%')
	     order by last_name, first_name;                
     
2d. Using IN, display the country_id and country columns of the following countries: Afghanistan, Bangladesh, and China:

	Select country_id, country from country where country in ('Afghanistan', 'Bangladesh', 'China');

3a. Add a middle_name column to the table actor. Specify the appropriate column type

	Alter table actor
	Add column middle_name VARCHAR;

3b. You realize that some of these actors have tremendously long last names. Change the data type of the middle_name column to something that can hold more than varchar.

	Alter table actor
	Alter column middle_name type text;

Now we didn’t explicitly cover this in class so exercise your googling skills. 
3c. Now write a query that would remove the middle_name column.

	Alter table actor
	Drop column If Exists middle_name; 

4a. List the last names of actors, as well as how many actors have that last name.

	Select last_name, count(last_name) from actor as No_Actors 
	   group by last_name; 

4b. List last names of actors and the number of actors who have that last name, but only for names that are shared by at least two actors.

	Select last_name, last_name as No_Actors from actor
	   having count(last_name) > 1 group by last_name; 

4c. Oh, no! The actor HARPO WILLIAMS was accidentally entered in the actor table as GROUCHO WILLIAMS. Write a query to fix the record.

	Select Actor_id, first_name, last_name from actor 
	   where lower (last_name) = lower('Williams');   and lower (first_name) = lower('Groucho');
	 Update actor set first_name = 'HARPO' where actor_id = 172;  

4d. Perhaps we were too hasty in changing GROUCHO to HARPO. It turns out that GROUCHO was the correct name after all! 
In a single query, 
if the first name of the actor is currently HARPO, change it to GROUCHO. 
Otherwise, change the first name to MUCHO GROUCHO, as that is exactly what the actor will be with the grievous error. 
BE CAREFUL NOT TO CHANGE THE FIRST NAME OF EVERY ACTOR TO MUCHO GROUCHO
(Hint: update the record using a unique identifier.)

	Select 
	     case first_name
	       when 'HARPO' then 'GROUCHO'
	       ELSE 'MUCHO GROUCHO'
	     end as first_name
	 from actor
	 where actor_id = 172;

5a. 

What’s the difference between a left join and a right join. 

	A left join includes all rows in left table whether there is   a matching row in the right table.
	Select apples from tree
	     left join orchard on (apple_color = orchard.apple_color; 

What about an inner join and an outer join? 

	An inner join includes only rows from both tables that match. 


When would you use rank? 

	 To assign a ranking based on some datum to each entry in a partitioned group. 
	 If 2 items in a partition are the same, they are given the same rank and the next rank value
	 is skipped.    

What about dense_rank? 

	  Just like rank except no rank is skipped.  When 2 entries           have the same ranked value theyare given the same rank
	  but the next rank is not skipped. 
   Example query for rank and dense rank:   
	SELECT
	 product_name,
	 group_name,
	 price,
	 DENSE_RANK () OVER (   >>> or RANK
	 PARTITION BY group_name
	 ORDER BY
	 price
	 )
	FROM
	 products
	INNER JOIN product_groups USING (group_id);

When would you use a subquery in a select? 
When you wish to retrieve a calculation using an aggregate function but you do not want the aggregate function to apply to the main query.

SELECT p1.product_name,
  (SELECT MAX(product_id)
   FROM products p2
   WHERE p1.product_id = p2.product_id) subquery2
FROM products p1;

When would you use a right join?

 Use a right join when you want all rows from the right table to be returned in the query whether or not there are matching rows fromthe other table.

When would you use an inner join over an outer join?

Use an inner join when you want only matching rows in result set.
Use an outer join when you want all rows  from your primary table whether or not ther are matching rows in secondary tables.   

What’s the difference between a left outer and a left join

No difference

When would you use a group by?

	GROUP BY clause can be used with an aggregate function to collect data across multiple rows and then group the results by one or more columns.
	Example:   SELECT category_id, COUNT(*) AS total_products
			FROM products
			WHERE category_id IS NOT NULL
			GROUP BY category_id


Describe how you would do data reformatting

	This question refers to formatting functions which use templates for specific formats such as  date and time,  char to int.   xample:  SELECT to_char("date", 'DD/MM/YYYY') FROM mytable; 

When would you use a with clause?

    With clause is used in complex queries to create table expressions.  
    Common table expressions are just temporary tables created within a larger query to make it easier to get the results needed.
	In example regional_sales and top_regions are both table expressions that contain intermediate values needed  for thefinal result.
	WITH regional_sales AS (
	        SELECT region, SUM(amount) AS total_sales
	        FROM orders
	        GROUP BY region
	     ), top_regions AS (
	        SELECT region
	        FROM regional_sales
	        WHERE total_sales > (SELECT SUM(total_sales)/10 FROM regional_sales)
	     )
SELECT region,
       product,
       SUM(quantity) AS product_units,
       SUM(amount) AS product_sales
FROM orders
WHERE region IN (SELECT region FROM top_regions)

bonus: When would you use a self join?

	A self-join is used when you need to join a table to itself to retrieve 2 different sets of data and show their relationship.
	For example:  a table has all employees,  org ids,  job role. 
	You need to get all employees and all mgrs and match on org id to get each employee's mgr. 

You’re answering each of these questions. Please include examples and answer them in plain english. 

6a. Use a JOIN to display the first and last names, as well as the address, of each staff member. Use the tables staff and address:

	Select first_name, last_name, address from staff a  left join address b  on ( a.address_id = b.address_id) ;
	
6b. Use a JOIN to display the total amount rung up by each staff member in January of 2007. Use tables staff and payment.
You’ll have to google for this one, we didn’t cover it explicitly in class. 

Select first_name, last_name, cast(sum(amount) as money)
from staff a Left join payment_p2007_01 b on (a.staff_id = b.staff_id)
group by a.staff_id;  

or

Select first_name, last_name, cast(sum(amount) as money) as Total_payments
from staff a Left join payment b on (a.staff_id = b.staff_id)
where date(payment_date) between '2007-01-01' and '2007-01-31'  
group by a.staff_id;

6c. List each film and the number of actors who are listed for that film. Use tables film_actor and film. Use inner join.

	Select title, count(film_actor.actor_id)
	from film inner join film_actor on (film.film_id = film_actor.film_id)
	group by title 
	order by title;

6d. How many copies of the film Hunchback Impossible exist in the inventory system?

	Select count(inventory.film_id), title from inventory, film 
	   where inventory.film_id = film.film_id and lower(title) = lower ('Hunchback Impossible')
	group by title, inventory.film_id;

6e. Using the tables payment and customer and the JOIN command, list the total paid by each customer. List the customers alphabetically by last name:

Select first_name, last_name , cast(sum(amount)as money) as Total_Payments from customer a Left join payment b on ( a.customer_id = b.customer_id) 
group by a.customer_id
order by last_name; 

7a. The music of Queen and Kris Kristofferson have seen an unlikely resurgence. As an unintended consequence, films starting with the letters K and Q have also soared in popularity. display the titles of movies starting with the letters K and Q whose language is English.

Select title, original_language from film where title in 'Q' , 'K' and 

Select title, name from film a join language b on (a.language_id = b.language_id)
WHERE lower(title) SIMILAR TO '(q|k)%' and a.language_id = 1;

7b. Use subqueries to display all actors who appear in the film Alone Trip.

Select a.first_name, a.last_name, b.title 
from actor a , film b 
where a.actor_id in(select c.actor_id 
   from  film_actor c 
   where c.film_id = b.film_id 
                    and lower(b.title) = lower('Alone Trip'));
   
7c. You want to run an email marketing campaign in Canada, for which you will need the names and email addresses of all Canadian customers. Use joins to retrieve this information.

Select first_name, last_name, email, country from customer a
inner join address b on a.address_id = b.address_id
inner join city c on c.city_id = b.city_id 
inner join country d on d.country_id = c.country_id
where lower(country) = lower('canada');

7d. Sales have been lagging among young families, and you wish to target all family movies for a promotion. Identify all movies categorized as a family film.
Now we mentioned family film, but there is no family film category. There’s a category that resembles that. In the real world nothing will be exact.

select title, rating  from film
where rating = 'G'
order by title;

or 
 
select title, name from film a
inner join film_category b on a.film_id = b.film_id
inner join category c on c.category_id = b.category_id 
where lower(name) = lower('family');

7e. Display the most frequently rented movies in descending order.

Select title, count(b.film_id)  No_rentals from film a,
inventory b where a.film_id = b.film_id  
group by title, b.film_id 
order by No_rentals desc, title asc;


7f. Write a query to display how much business, in dollars, each store brought in.

Select store_id, sum(amount) as Store_income from store
, payment where staff_id = manager_staff_id
group by store_id;

7g. Write a query to display for each store its store ID, city, and country.
 
Select store_id, city, country from store a
inner join address b on a.address_id = b.address_id
inner join city c on c.city_id = b.city_id 
inner join country d on d.country_id = c.country_id
;

7h. List the top five genres in gross revenue in descending 

 Select name as genre, cast(sum(amount) as money) as gross from category a
inner join film_category b on a.category_id = b.category_id
inner join inventory c on c.film_id = b.film_id 
inner join rental d on c.inventory_id = d.inventory_id 
inner join payment e on d.customer_id = e.customer_id
group by genre
order by gross desc 
limit 5;

8a. In your new role as an executive, you would like to have an easy way of viewing the Top five genres by gross revenue. Use the solution from the problem above to create a view. 
Create or Replace view top_five_genres AS

CREATE VIEW top_five_genres AS
Select name as genre, cast(sum(amount) as money) as gross from category a
inner join film_category b on a.category_id = b.category_id
inner join inventory c on c.film_id = b.film_id 
inner join rental d on c.inventory_id = d.inventory_id 
inner join payment e on d.customer_id = e.customer_id
group by genre
order by gross desc 
limit 5;

    
8b. How would you display the view that you created in 8a?

Select * from top_five_genres;

8c. You find that you no longer need the view top_five_genres. Write a query to delete it.

DROP VIEW [IF EXISTS] top_five_genres;


Appendix: List of Tables in the Pagilla DB
A schema is also available as sakila_schema.svg. Open it with a browser to view.
	'actor'
	'actor_info'
	'address'
	'category'
	'city'
	'country'
	'customer'
	'customer_list'
	'film'
	'film_actor'
	'film_category'
	'film_list'
	'film_text'
	'inventory'
	'language'
	'nicer_but_slower_film_list'
	'payment'
	'rental'
	'sales_by_film_category'
	'sales_by_store'
	'staff'
	'staff_list'
	'store'

The link:
https://dev.mysql.com/doc/sakila/en/sakila-structure.html
