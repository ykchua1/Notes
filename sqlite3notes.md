PT 1 - simple query from table
	select * from table;
	select lastname, firstname from employees;

PT 2 - order by clause
	select column1, column2 from table order by column1 asc, column2 desc;
- ordering by column position
	select column1, column2, column3 from tracks order by 3 asc/desc, 2 asc/desc;
- sorting NULLs
- NULLs are designed to solve issue of unknown information
- NULLs usually cannot be compared
- SQLite considers NULL to be smaller than any other value
	select trackid, name, composer from tracks order by composer NULLS LAST;
- for sqlite, NULLS LAST not supported
	select trackid, name, composer from tracks order by composer is null, composer asc;

PT 3 - select distinct
- distinct is a clause of the select statement
- distinct allows you to remove the duplicate rows in the result set
	select distinct city from customers order by city;
- applying distinct on 2 columns
	select distinct city, country from customers order by country;

PT 4 - where clause
	select columnlist from table where searchcondition;
	select name, milliseconds, bytes, albumid from tracks where albumid = 1 and milliseconds > 250000; 
	select name, albumid, composer from tracks where composer like "%smith%" order by albumid;
- in operator: check whether a value is inside a set (list)
	select name, albumid, mediatypeid from tracks where mediatypeid in (2,3);

PT 5 - limit clause to constrain the number of rows
- basic syntax:
	select columnlist from table limit rowcount;
- offset
	select columnlist from table limit rowcount offset n;
- example: to get top 10 list
	select trackid, name, bytes from tracks order by bytes desc limit 10;
- example: to get the nth largest (hint: use offset)
	select trackid, name, milliseconds from tracks order by milliseconds desc limit 1 offset 1;

PT 6 - between:
- the between operator is a logical operator that tests whether a value is in a range of values
- it is used in the WHERE clause of the SELECT, DELETE, UPDATE and REPLACE statements
	testexpression BETWEEN lowexpression AND highexpression
- note that the between operator is inclusive
	select invoiceid, billingaddress, total from invoices where total between 14 and 18 order by total;
- is the same as:
	select invoiceid, billingaddress, total from invoices where total >= 14 and total <= 18 order by total;
- NOT BETWEEN
	select invoiceid, billingaddress, total from invoices where total NOT BETWEEN 1 and 20 order by total;
- between DATES
	select invoiceid, billingaddress, invoicedate, total from invoices where invoicedate between..
	.. '2010-01-01' and '2010-01-31' order by invoicedate;

PT 7 - IN operator
- syntax:
	expression [NOT] in (valuelist | subquery);
	select trackid, name, mediatypeid from tracks where mediatypeid in (1,2) order by name asc limit 5;
- combine in operator with a SUBQUERY
	select trackid, name, albumid from tracks where albumid in
	(select albumid from albums where artist_id = 12);
- negative search using NOT IN
	select trackid, name, genreid from tracks where genreid not in (1,2,3);

PT 8 - LIKE
- the % sign is a wildcard, similar to a * in globbing
- % matches any sequence of zero or more characters
- underscore wildcard
- note: SQL like operator is case insensitive
- note: for unicode characters that are not in the ASCII ranges, the LIKE operator is case-sensitive 
- to make LIKE word case-sensitively:
	PRAGMA case_sensitive_like = true;
- for regex equivalent of '.', use the underscore wildcard '_'
	select trackid, name from tracks where name like "%br_wn%";
- if ESCAPE characters are required, do the following syntax:
	column_1 like pattern escape expression;
	column_1 like pattern escape '\';
- example of escape clause usage:
	select c from t where c like "%10\%%" escape "\";

PT 8 - GLOB
- GLOB is like LIKE
- glob is case sensitive and uses the UNIX wildcards
	select trackid, name from tracks where name glob '*[1-9]';
	select trackid, name from tracks where name glob '*[^1-9]*'; <-- doesnt work for excluding numbers

PT 9 - IS NULL
- NULL = NULL returns 0!
- therefore you cannot do [where compose = NULL;]
- instead, have to use IS NULL
- syntax: (column | expression) IS NULL;
	select name, composer from tracks where composer is null order by name;
- opposite of IS NULL: IS NOT NULL

PT 9 - JOIN
- SQLite joins are to query data from 2 or more tables
- you can use INNER JOIN, LEFT JOIN, or CROSS JOIN clause
- note that SQLite does not directly support the RIGHT JOIN and FULL OUTER JOIN
	select title, name from albums INNER JOIN artists ON artists.artistid = albums.artistid;
- (albums.artistid = artists.artistid) is the join condition
- use aliases to shorten the query by:
	select l.title, r.name from albums l INNER JOIN artists r ON l.artistid = r.artistid;
- implement the same thing using shortening syntax - USING:
	select title, name from albums inner join artists USING(artistid);
- LEFT JOINING: if the right table has no matching rows, then NULL is output
	select name, title from artists left join albums on artists.artistid = albums.artistid order by name;
	select name, title from artists left join albums USING(artistid) order by name;
- after LEFT JOIN, if you want to find the artists without any albums:
	select name, title from artists left join albums on artists.artistid = albums.artistid WHERE title is null order by name;
- CROSS JOIN - basic syntax:
	select list from table1 cross join table2
- if the first table has n rows, the second has m rows, the final results will have nxm rows!
- cross joining is something like a cross product

PT 10 - INNER JOIN
- syntax: select a1, a2, b1, b2 from A INNER JOIN B on B.f = A.f;
- inner join is most probably the most common type of join
- it is also possible to chain inner joins together to join 3 or more tables
- use AS operator to rename the columns

PT 11 - LEFT JOIN
- note that in A.f = B.f, the = operator can also be > or <
- note that all rows in table A are included whether there are matching rows in B or not
- note on order of operations: SELECT Comes last usually, and ORDER BY comes even after SELECT

PT 12 - CROSS JOIN
- useful for getting all the combinations of attributes 
- example: getting all the card combinations in a deck

PT 13 - SELF JOIN
- note: '||' is a concatenation operator
- note:  AND operator is a logical operator, () also helps with order of operations
- example given: find the employees from the same city (hint: use select distinct)
- note: <> means NOT EQUAL
select distinct e1.city, e1.firstname || ' ' || e1.lastname as fullname from employees as e1 inner join employees as e2 ON e1.city = e2.city and (e1.firstname <> e2.firstname and e1.lastname <> e2.lastname) order by e1.city, e2.last name limit 10

PT 13.5 - FULL OUTER JOIN emulation
- please refer to the sqlitetutorial.net website for more details

PT 14 - GROUP BY
- returns one row for each group
- for each group, can apply aggregate function such as MIN, MAX etc..
- example syntax:
	select column1, aggfn(column2) from table GROUP BY column1, column2;
- example:
	select albumid, COUNT(trackid) from tracks group by albumid;
- example:
	select albumid, COUNT(trackid) from tracks group by albumid order by COUNT(trackid) DESC;
- GROUP BY and INNER JOIN: to grab colums from another table eg. Title of album
	select tracks.albumid, title, count(trackid) from tracks INNER JOIN albums ON albums.albumid = tracks.albumid 
	GROUP BY albums.albumid;
- GROUP BY with HAVING clause (for filtering groups)
	select tracks.albumid, title, count(trackid) from tracks inner join albums on albums.albumid = tracks.albumid
	group by tracks.albumid HAVING count(trackid) > 15;
- GROUP BY multiple columns
	it will use the combination of values of mediatypeid and genreid
- GROUP BY date:
	select strftime("%Y", invoicedate) invoiceyear, count(invoiceid) invoicecount from invoices group by
	strftime('%Y', invoicedate) order by invoiceyear;

PT 15 - HAVING clause
- a GROUP BY clause is required before HAVING
- syntax:
	GROUP BY
		column1,
		column2
	HAVING
		searchcondition;  
- example:
	select albumid, count(trackid) from tracks group by albumid having count(albumid) between 18 and 20
	order by albumid;
- example with INNER JOIN:
	select tracks.albumid, title, sum(milliseconds) as length from tracks inner join albums on
	albums.albumid = tracks.albumid GROUP BY tracks.albumid HAVING length > 60000000;

PT 16 - UNION operator
- used to combine rows from 2 or more queries
- syntax: 
	query_1
	UNION [ALL]
	query_2
	UNION [ALL]
	query_3
	...;
- example:
	select firstname, lastname, 'employee' as type from employees UNION select firstname, lastname, 
	'customer' from customers ORDER BY firstname, lastname;

PT 17 - EXCEPT operator
- example:
	select artistid from artists EXCEPT select artistid from albums;


PT 18 - INTERSECT operator
- example:
	select customerid from customers INTERSECT select customerid from invoices order by customerid

PT 19 - subqueries
- note: we have seen this before
- a subquery is a SELECT statement nested in another statement
- examples:
	select column1 from table1 where column1 = (select column1 from table2);
- case 1: subquery in the WHERE clause
	select trackid, name, albumid from tracks where albumid = (select albumid from albums where title LIKE
	"let there be rock");
	select customerid, firstname, lastname from customers where supportrepid IN (select employeeid from
	employees where country = "Canada");
- case 2: subquery in the FROM clause
	select avg(album.size) from (select sum(bytes) as size from tracks group by albumid) as album;
- case 3: correlated subquery (note that albums.albumid is equal to a number)
	select albumid, title from albums where (select sum(bytes) from tracks where tracks.albumid = albums.albumid
	) < 10000000 order by title;
- case 3.5: correlated subquery in the SELECT clause
	select albumid, title, (select count(trackid) from tracks where tracks.albumid = albums.albumid) as
	tracks_count from albums order by tracks_count DESC;

PT 20 - EXISTS operator
- checks whether the subquery returns any row
- example:
	select * from artists a where not exists (select 1 from albums where artistid = a.artistid) order by name;
- another example:
	select customerid, firstname, lastname, company from customers c where exists (select 1 from invoices where
	customerid = c.customerid) order by firstname, lastname;
