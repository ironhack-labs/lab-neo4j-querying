![logo_ironhack_blue 7](https://user-images.githubusercontent.com/23629340/40541063-a07a0a8a-601a-11e8-91b5-2f13e4e6b441.png)

# Lab | Neo4j: Querying the Movies Dataset

### **Setup**

1.  **Load the dataset** (if not preloaded):

    -   Ensure you have `Movie`, `Person`, `Genre`, and relationships like `ACTED_IN`, `DIRECTED`, etc.
    -   Use `:play movies` in Neo4j Browser to load the sample dataset if needed.

## Exercises

### 1. Basic Node Retrieval

**Task**: List all movies released in the year **1999**. <br>
**Goal**: Practice filtering node properties.

Query: 

```cypher
match (m:Movie {releaseYear: 1999})
return m
```

### 2. Find a Person by Name

**Task**: Retrieve details for the person named **Keanu Reeves**. <br>
**Goal**: Query nodes by exact property match.

Query:

```cypher
match (p:Person {name: "Keanu Reeves"})
return p;
```



### 3. Find Actors in a Movie

**Task**: List all actors who acted in **The Matrix**. <br>
**Goal**: Traverse relationships from a movie to actors.

Query:

```cypher
match (p:Person)-[ACTED_IN]->(m:Movie)
WHERE m.title = 'The Matrix'

RETURN p, m
```


### 4. Directors of a Movie

**Task**: Find the director(s) of **The Matrix Revolutions**. <br>
**Goal**: Filter relationships by type (`DIRECTED`).

Query:
```cypher
match (p:Person)-[:DIRECTED]->(m:Movie)
where m.title = "The Matrix Revolutions"
return p, m
```


### 5. Movies by Genre and Year

**Task**: Find all **Action** movies released after **2000**. <br>
**Goal**: Combine relationship traversal and property filtering.

Query:
```cypher
match (m:Movie)-[:IN_GENRE]-> (g:Genre) <br>
where m.releaseYear > 2000 AND g.name = "Action"
return  m, g;
```

### 6. Co-Actors of an Actor

**Task**: Find all actors who worked with **Tom Hanks** in any movie. <br>
**Goal**: Use variable-length patterns and deduplication (`DISTINCT`).

Query:
```cypher
MATCH (a1:Person)-[r1:ACTED_IN]->(m:Movie)<-[r2:ACTED_IN]-(a2:Person)
WHERE a1.name = 'Tom Hanks' and a1 <>a2
RETURN a1.name,  m.title AS MovieTitle, a2.name AS worked_with;
```


### 7. Movies with Multiple Relationships

**Task**: Find all people who either acted in or directed **Cloud Atlas**. <br>
**Goal**: Filter relationship types dynamically.

Query: <br>
```cypher
MATCH (p:Person)-[r]->(m:Movie)
WHERE m.title = "Cloud Atlas" AND
(type(r) = "ACTED_IN" or type(r)= "DIRECTED")
RETURN p, m;
```

### 8. Shortest Path Between Actors

**Task**: Find the shortest path between **Tom Hanks** and **Meg Ryan** through co-acting. <br>
**Goal**: Use `shortestPath` and variable-length relationships.

Query:

```cypher
MATCH (a1:Person {name: "Tom Hanks"}), (a2:Person {name: "Meg Ryan"})
MATCH path = shortestPath((a1)-[:ACTED_IN*]-(a2))
RETURN [n IN nodes(path) | CASE WHEN n:Person THEN n.name ELSE n.title END] AS Path;
```

### 9. Aggregation with HAVING

**Task**: List actors who have acted in **at least 3 movies**. <br>
**Goal**: Use `WITH` and `HAVING`-like filtering.

```cypher
match (p:Person)-[:ACTED_IN]->(m:Movie)

WITH p, count(m) AS MovieCount

WHERE MovieCount >=3

RETURN p.name, MovieCount
ORDER BY MovieCount desc;
```

### 10. Complex Graph Pattern (Advanced)

**Task**: Find all directors who also acted in their own movies. <br>
**Goal**: Combine multiple relationships on the same node.

Query:

```cypher
MATCH (p:Person)-[:DIRECTED]->(m:Movie)<-[:ACTED_IN]-(p)
RETURN p.name AS DirectorActor, m.title AS MovieTitle;
```

<!-- ### Bonus Challenge

Create a **recommendation query** that suggests movies based on shared genres and frequent collaborators of a user’s favorite actor (e.g., **Keanu Reeves**). -->

```cypher
// Genre-based recommendations

MATCH (keanu:Person {name: "Keanu Reeves"})-[:ACTED_IN]->(m:Movie)-[:IN_GENRE]->(genre:Genre)
WITH keanu, COLLECT(DISTINCT genre) AS keanuGenres
MATCH (recommended:Movie)-[:IN_GENRE]->(g:Genre)
WHERE g IN keanuGenres 
  AND NOT EXISTS { (keanu)-[:ACTED_IN]->(recommended) }
RETURN recommended.title AS Movie, 
       "Shared Genre: " + g.name AS Reason

UNION

// Collaborator-based recommendations

MATCH (keanu:Person {name: "Keanu Reeves"})-[:ACTED_IN]->(movie:Movie)<-[:ACTED_IN]-(coActor:Person)
WITH coActor, COUNT(*) AS timesWorked // Find frequent collaborators
ORDER BY timesWorked DESC
MATCH (coActor)-[:ACTED_IN]->(recommended:Movie)
WHERE NOT EXISTS { (keanu)-[:ACTED_IN]->(recommended) }
RETURN recommended.title AS Movie, 
       "Collaborator: " + coActor.name + " (Worked together " + toString(timesWorked) + " times)" AS Reason
```




**Tips:**

-   Use `PROFILE` to analyze query performance.
-   Visualize results in Neo4j Browser for graph patterns.
-   Experiment with `OPTIONAL MATCH` for partial matches.
c