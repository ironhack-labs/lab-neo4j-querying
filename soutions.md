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

match (m:Movie)
where m.releaseYear = 1999
return m


### 2. Find a Person by Name

**Task**: Retrieve details for the person named **Keanu Reeves**. <br>
**Goal**: Query nodes by exact property match.

match (keanue:Person {name:"Keanu Reeves"})
return keanue


### 3. Find Actors in a Movie

**Task**: List all actors who acted in **The Matrix**. <br>
**Goal**: Traverse relationships from a movie to actors.

match (actors:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.title = "The Matrix"
return actors


### 4. Directors of a Movie

**Task**: Find the director(s) of **The Matrix Revolutions**. <br>
**Goal**: Filter relationships by type (`DIRECTED`).

match(directors:Person)-[:DIRECTED]->(movie:Movie {title:"The Matrix Revolutions"})
return directors

### 5. Movies by Genre and Year

**Task**: Find all **Action** movies released after **2000**. <br>
**Goal**: Combine relationship traversal and property filtering.

match (m:Movie)-[genre:IN_GENRE]->(g:Genre)
WHERE g.name="Action" AND  m.releaseYear > 2000 
return m.title, m.releaseYear

### 6. Co-Actors of an Actor

**Task**: Find all actors who worked with **Tom Hanks** in any movie. <br>
**Goal**: Use variable-length patterns and deduplication (`DISTINCT`).

match(actor:Person)-[:ACTED_IN]->(:Movie)<-[:ACTED_IN]-(:Person {name:"Tom Hanks"})
return DISTINCT actor.name


### 7. Movies with Multiple Relationships

**Task**: Find all people who either acted in, directed, or wrote **Cloud Atlas**. <br>
**Goal**: Filter relationship types dynamically.

match(actor:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(director:Person)
WHERE m.title = "Cloud Atlas"
return actor.name, director.name

or better (Chatgpt answer)

MATCH (person:Person)-[:ACTED_IN|:DIRECTED]->(m:Movie)
WHERE m.title = "Cloud Atlas"
RETURN person.name AS person_name

### 8. Shortest Path Between Actors

**Task**: Find the shortest path between **Tom Hanks** and **Meg Ryan** through co-acting. <br>
**Goal**: Use `shortestPath` and variable-length relationships.

MATCH (a1:Person {name: "Tom Hanks"}), (a2:Person {name: "Meg Ryan"})
MATCH path = shortestPath((a1)-[:ACTED_IN*]-(a2))
RETURN [n IN nodes(path) | CASE WHEN n:Person THEN n.name ELSE n.title END] AS Path;



### 9. Aggregation with HAVING

**Task**: List actors who have acted in **at least 3 movies**. <br>
**Goal**: Use `WITH` and `HAVING`-like filtering.

match(actor:Person)-[r:ACTED_IN]->(:Movie)
with actor, count(r) as movie_count
WHERE movie_count >= 3
return actor.name, movie_count
Order by movie_count DESC


### 10. Complex Graph Pattern (Advanced)

**Task**: Find all directors who also acted in their own movies. <br>
**Goal**: Combine multiple relationships on the same node.

match(actor:Person)-[:ACTED_IN]->(:Movie)<-[:DIRECTED]-(actor)
return DISTINCT actor.name


### Bonus Challenge

Create a **recommendation query** that suggests movies based on shared genres and frequent collaborators of a user’s favorite actor (e.g., **Keanu Reeves**).

// 1. Find Keanu Reeves' top genres
MATCH (fav_actor:Person {name:"Keanu Reeves"})-[:ACTED_IN]->(:Movie)-[:IN_GENRE]->(g:Genre)
WITH fav_actor, g, count(g) AS genre_count
ORDER BY genre_count DESC
LIMIT 5
WITH fav_actor, collect(g.name) AS top_genres

// 2. Find Keanu Reeves' top collaborators
MATCH (collaborator:Person)-[:ACTED_IN|DIRECTED]->(m:Movie)<-[:ACTED_IN]-(fav_actor)
WITH fav_actor, top_genres, collaborator, count(m) AS collaborations
ORDER BY collaborations DESC
LIMIT 20
WITH fav_actor, top_genres, collect(collaborator.name) AS top_collabs  // Collect the top collaborators

// 3. Recommend movies based on shared genres and frequent collaborators
MATCH (person:Person)-[:ACTED_IN|DIRECTED]->(m:Movie)-[:IN_GENRE]->(genre:Genre)
WHERE genre.name IN top_genres AND person.name IN top_collabs  // Filter by top genres and collaborators
AND NOT (fav_actor)-[:ACTED_IN]->(m)  // Exclude movies Keanu Reeves has acted in
RETURN m.title AS recommended_movie, person.name AS collaborator, genre.name AS genre

**Tips:**

-   Use `PROFILE` to analyze query performance.
-   Visualize results in Neo4j Browser for graph patterns.
-   Experiment with `OPTIONAL MATCH` for partial matches.
