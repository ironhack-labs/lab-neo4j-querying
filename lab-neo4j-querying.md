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
<br>
MATCH (m:Movie)
<br>
WHERE m.releaseYear = 1999
<br>
RETURN m.title AS Title , m.releaseYear AS Release_Year ;

### 2. Find a Person by Name

**Task**: Retrieve details for the person named **Keanu Reeves**. <br>
**Goal**: Query nodes by exact property match.

Query:
<br>
MATCH (p:Person)
<br>
WHERE p.name= "Keanu Reeves"
<br>
RETURN p;

### 3. Find Actors in a Movie

**Task**: List all actors who acted in **The Matrix**. <br>
**Goal**: Traverse relationships from a movie to actors.

Query:
<br>
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
<br>
WHERE m.title = "The Matrix"
<br>
RETURN p.name AS Actor_name, m.title AS Movie_title;


### 4. Directors of a Movie

**Task**: Find the director(s) of **The Matrix Revolutions**. <br>
**Goal**: Filter relationships by type (`DIRECTED`).

Query:
<br>
MATCH (p:Person)-[:DIRECTED]->(m:Movie)
<br>
WHERE m.title = "The Matrix Revolutions"
<br>
RETURN p.name AS Director_name, m.title AS Movie_name;


### 5. Movies by Genre and Year

**Task**: Find all **Action** movies released after **2000**. <br>
**Goal**: Combine relationship traversal and property filtering.

Query:
<br>
MATCH (m:Movie)- [:IN_GENRE]->(g:Genre)
<br>
WHERE g.name = "Action" AND m.releaseYear > 2000
<br>
RETURN m.title AS Movie_name, g.name AS Genre, m.releaseYear AS Release_Year;

### 6. Co-Actors of an Actor

**Task**: Find all actors who worked with **Tom Hanks** in any movie. <br>
**Goal**: Use variable-length patterns and deduplication (`DISTINCT`).

Query:
<br>
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(coActor:Person)
<br>
WHERE p.name = "Tom Hanks" AND p <> coActor
<br>
RETURN DISTINCT coActor.name AS Actors_who_acted_with_Tom_Hanks;

### 7. Movies with Multiple Relationships

**Task**: Find all people who either acted in, directed, or wrote **Cloud Atlas**. <br>
**Goal**: Filter relationship types dynamically.

Query:
<br>
MATCH (p:Person)-[r:ACTED_IN|DIRECTED|WROTE]->(m:Movie)
<br>
WHERE m.title= "Cloud Atlas"
<br>
RETURN DISTINCT p.name AS Name, type(r) AS Role;

### 8. Shortest Path Between Actors

**Task**: Find the shortest path between **Tom Hanks** and **Meg Ryan** through co-acting. <br>
**Goal**: Use `shortestPath` and variable-length relationships.

Query:
<br>
MATCH (p1:Person {name: "Tom Hanks"}), (p2:Person {name: "Meg Ryan"})
<br>
MATCH p = shortestPath ((p1)-[:ACTED_IN*]-(p2))
<br>
RETURN p As Shortest_path;

### 9. Aggregation with HAVING

**Task**: List actors who have acted in **at least 3 movies**. <br>
**Goal**: Use `WITH` and `HAVING`-like filtering.

Query:
<br>
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
<br>
WITH p, count(m) AS movieCount
<br>
WHERE movieCount >= 3
<br>
RETURN p.name AS Actor, movieCount
<br>
ORDER BY movieCount DESC;

### 10. Complex Graph Pattern (Advanced)

**Task**: Find all directors who also acted in their own movies. <br>
**Goal**: Combine multiple relationships on the same node.

Query:
<br>
MATCH (p:Person)-[:DIRECTED]->(m:Movie),
<br>
      (p)-[:ACTED_IN]->(m)
      <br>
RETURN DISTINCT p.name AS Director_Actor, m.title AS Movie;

### Bonus Challenge

Create a **recommendation query** that suggests movies based on shared genres and frequent collaborators of a user’s favorite actor (e.g., **Keanu Reeves**).

**Tips:**

-   Use `PROFILE` to analyze query performance.
-   Visualize results in Neo4j Browser for graph patterns.
-   Experiment with `OPTIONAL MATCH` for partial matches.



// 1. Find Keanu's movies
<br><br>
MATCH (keanu:Person {name: "Keanu Reeves"})-[:ACTED_IN]->(keanuMovie:Movie)
<br><br>
// 2. Find collaborators
<br><br>
MATCH (keanuMovie)<-[:ACTED_IN]-(collab:Person)
<br>
WHERE collab.name <> "Keanu Reeves"
<br><br>
// 3. Get other movies collaborators acted in
<br><br>
MATCH (collab)-[:ACTED_IN]->(rec:Movie)
<br>
WHERE NOT (rec)<-[:ACTED_IN]-(keanu)
<br><br>
// 4. Match shared genres (OPTIONAL MATCH to not filter out others)
<br><br>
OPTIONAL MATCH (rec)-[:IN_GENRE]->(sharedGenre:Genre)<-[:IN_GENRE]-(keanuMovie)
<br><br>

// 5. Return recommendations ranked by shared genres & collaborations
<br><br>
RETURN DISTINCT rec.title AS RecommendedMovie,
<br>
       count(DISTINCT sharedGenre) AS SharedGenres,
       <br>
       count(DISTINCT collab) AS NumCollaborators
       <br>
ORDER BY SharedGenres DESC, NumCollaborators DESC
<br>
LIMIT 20;
