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


### 2. Find a Person by Name

**Task**: Retrieve details for the person named **Keanu Reeves**. <br>
**Goal**: Query nodes by exact property match.


### 3. Find Actors in a Movie

**Task**: List all actors who acted in **The Matrix**. <br>
**Goal**: Traverse relationships from a movie to actors.


### 4. Directors of a Movie

**Task**: Find the director(s) of **The Matrix Revolutions**. <br>
**Goal**: Filter relationships by type (`DIRECTED`).


### 5. Movies by Genre and Year

**Task**: Find all **Action** movies released after **2000**. <br>
**Goal**: Combine relationship traversal and property filtering.


### 6. Co-Actors of an Actor

**Task**: Find all actors who worked with **Tom Hanks** in any movie. <br>
**Goal**: Use variable-length patterns and deduplication (`DISTINCT`).


### 7. Movies with Multiple Relationships

**Task**: Find all people who either acted in or directed **Cloud Atlas**. <br>
**Goal**: Filter relationship types dynamically.


### 8. Shortest Path Between Actors

**Task**: Find the shortest path between **Tom Hanks** and **Meg Ryan** through co-acting. <br>
**Goal**: Use `shortestPath` and variable-length relationships.


### 9. Aggregation with HAVING

**Task**: List actors who have acted in **at least 3 movies**. <br>
**Goal**: Use `WITH` and `HAVING`-like filtering.


### 10. Complex Graph Pattern (Advanced)

**Task**: Find all directors who also acted in their own movies. <br>
**Goal**: Combine multiple relationships on the same node.


<!-- ### Bonus Challenge

Create a **recommendation query** that suggests movies based on shared genres and frequent collaborators of a user’s favorite actor (e.g., **Keanu Reeves**). -->

**Tips:**

-   Use `PROFILE` to analyze query performance.
-   Visualize results in Neo4j Browser for graph patterns.
-   Experiment with `OPTIONAL MATCH` for partial matches.
