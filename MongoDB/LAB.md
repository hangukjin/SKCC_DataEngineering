## Lab
___
1.
```sh
> db.movieDetails.find({"awards.wins" : 2, "awards.nominations" : 2}).count()  
12  

> db.movieDetails.find({"rated" : "PG", "awards.nominations" : 10}).count()
3
```
  
2.
```sh
> db.movieDetails.find({"writers" : ["Ethan Coen", "Joel Coen"]}).count()
1

> db.movieDetails.find({"genres" : "Family"}).count()
124

> db.movieDetails.find({"country.1" : "Western"})
```

