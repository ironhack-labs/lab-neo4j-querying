![Ironhack Logo](https://i.imgur.com/1QgrNNw.png)

# Answers

## Querying the Movies Dataset

**1. List all movies released in the year 1999.**

MATCH (m:Movie)
WHERE m.releaseYear = 1999
RETURN m

<br>

**2. Retrieve details for the person named Keanu Reeves.**

MATCH (p:Person)
WHERE p.name = "Keanu Reeves"
RETURN p

<br>

**3. List all actors who acted in The Matrix.**

MATCH (p:Person)-[:ACTED_IN]->(m:Movie {title: "The Matrix"})
RETURN p;

<br>

**4. Find the director(s) of The Matrix Revolutions.**

MATCH (p:Person)-[:DIRECTED]->(m:Movie {title: "The Matrix Revolutions"})
RETURN p

<br>

**5. Find all Action movies released after 2000.**

MATCH (m:Movie)-[:IN_GENRE]->(g:Genre)
WHERE g.name = "Action" AND m.releaseYear > 2000
RETURN m.title AS title, m.releaseYear AS year
ORDER BY year ASC

<br>

**6. Find all actors who worked with Tom Hanks in any movie.**

MATCH (tom:Person)-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(coActor:Person)
WHERE tom.name = "Tom Hanks" AND coActor.name <> "Tom Hanks"
RETURN DISTINCT coActor.name AS actor
ORDER BY actor

<br>

**7. Find all people who either acted in or directed Cloud Atlas.**

MATCH (p:Person)-[r]->(m:Movie)
WHERE m.title = "Cloud Atlas" AND type(r) IN ["ACTED_IN", "DIRECTED"]
RETURN DISTINCT p.name AS person, type(r) AS role

<br>

**8. Find the shortest path between Tom Hanks and Meg Ryan through co-acting.**

MATCH path = shortestPath(
  (tom:Person {name: "Tom Hanks"})-[:ACTED_IN*]-(meg:Person {name: "Meg Ryan"})
)
RETURN path

<br>

**9. List actors who have acted in at least 3 movies.**

MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WITH p, COUNT(m) AS movieCount
WHERE movieCount >= 3
RETURN p.name AS actor, movieCount
ORDER BY movieCount DESC, actor

<br>

**10. Find all directors who also acted in their own movies.**

MATCH (p:Person)-[:DIRECTED]->(m:Movie)<-[:ACTED_IN]-(p)
RETURN DISTINCT p.name AS person
ORDER BY p.name

<br>