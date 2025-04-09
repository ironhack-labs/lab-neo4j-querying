# Lab | Neo4j: Querying the Movies Dataset

### **Setup**

1.  **Load the dataset** (if not preloaded):

    -   Ensure you have `Movie`, `Person`, `Genre`, and relationships like `ACTED_IN`, `DIRECTED`, etc.
    -   Use `:play movies` in Neo4j Browser to load the sample dataset if needed.

## Exercises

### 1. Basic Node Retrieval

**Task**: List all movies released in the year **1999**. <br>
**Goal**: Practice filtering node properties.

MATCH (m:Movie)
WHERE m.releaseYear = 1999
RETURN m.title AS title, m.releaseYear AS releaseyear;


### 2. Find a Person by Name

**Task**: Retrieve details for the person named **Keanu Reeves**. <br>
**Goal**: Query nodes by exact property match.

MATCH (p:Person)
WHERE p.name = "Keanu Reeves"
RETURN p.name, p.birthYear, p.personId;


### 3. Find Actors in a Movie

**Task**: List all actors who acted in **The Matrix**. <br>
**Goal**: Traverse relationships from a movie to actors.

MATCH (p:Person)-[:ACTED_IN]->(m:Movie {title: "The Matrix"})
RETURN p.name AS name;


### 4. Directors of a Movie

**Task**: Find the director(s) of **The Matrix Revolutions**. <br>
**Goal**: Filter relationships by type (`DIRECTED`).

MATCH (p:Person)-[:DIRECTED]->(m:Movie {title: "The Matrix Revolutions"})
RETURN p.name;


### 5. Movies by Genre and Year

**Task**: Find all **Action** movies released after **2000**. <br>
**Goal**: Combine relationship traversal and property filtering.

MATCH (m:Movie)-[:IN_GENRE]->(g:Genre {name:"Action"})
WHERE m.releaseYear > 2000
RETURN m.title;


### 6. Co-Actors of an Actor

**Task**: Find all actors who worked with **Tom Hanks** in any movie. <br>
**Goal**: Use variable-length patterns and deduplication (`DISTINCT`).

MATCH (tom:Person {name: "Tom Hanks"})-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(coActor:Person)
RETURN DISTINCT coActor.name AS name, m.title AS MovieName;


### 7. Movies with Multiple Relationships

**Task**: Find all people who either acted in, directed, or wrote **Cloud Atlas**. <br>
**Goal**: Filter relationship types dynamically.

MATCH (p:Person)-[rel:ACTED_IN|DIRECTED|WROTE]->(m:Movie {title: "Cloud Atlas"})
RETURN DISTINCT p.name AS name, type(rel) AS role;


### 8. Shortest Path Between Actors

**Task**: Find the shortest path between **Tom Hanks** and **Meg Ryan** through co-acting. <br>
**Goal**: Use `shortestPath` and variable-length relationships.

MATCH (p1:Person {name: "Tom Hanks"}), (p2:Person {name: "Meg Ryan"})
MATCH path = shortestPath((p1)-[:ACTED_IN*]-(p2))
RETURN [n IN nodes(path) | CASE WHEN n:Person THEN n.name ELSE n.title END] AS Path;


### 9. Aggregation with HAVING

**Task**: List actors who have acted in **at least 3 movies**. <br>
**Goal**: Use `WITH` and `HAVING`-like filtering.

MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WITH p.name AS name, COUNT(m) AS movie_count
WHERE movie_count >= 3
RETURN name, movie_count
ORDER BY movie_count DESC;


### 10. Complex Graph Pattern (Advanced)

**Task**: Find all directors who also acted in their own movies. <br>
**Goal**: Combine multiple relationships on the same node.

MATCH (p:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(p:Person)
RETURN p.name AS name, m.title as movie_name;

### Bonus Challenge

Create a **recommendation query** that suggests movies based on shared genres and frequent collaborators of a user’s favorite actor (e.g., **Keanu Reeves**).

**Tips:**

-   Use `PROFILE` to analyze query performance.
-   Visualize results in Neo4j Browser for graph patterns.
-   Experiment with `OPTIONAL MATCH` for partial matches.
