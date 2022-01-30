# 04: Introduction to Unity Scripting - Keeping Track of GameObjects

## Lists

Start by creating a new empty Game Object at the root of the **hierarchy** and name it **Sheep Spawn Points** and reset its Transform. Create three empty Game Objects as children **Sheep Spawn Points** and name each one **Spawn Point**.

Set the position of each spawn point to (respectively):

- **(X:-16, Y:0, Z:60)**
- **(X:0, Y:0, Z:60)**
- **(X:16, Y:0, Z:60)**

This spaces them evenly just at the edge of the far end of the field. Now let's write the script that spawns the sheep.

Create a new C# script and name it **SheepSpawner**. Add these variables above `Start`:

```csharp
public bool canSpawn = true; 

public GameObject sheepPrefab; 
public List<Transform> sheepSpawnPositions = new List<Transform>(); 
public float timeBetweenSpawns; 

private List<GameObject> sheepList = new List<GameObject>(); 
```

- The **canSpawn** variable will control whether the script can spawn sheep or not. 
- The **sheepPrefab** variable is, obviously, a reference to the sheep prefab. 
- The next variable is a List that will hold the spawn points we just created before. 
- **timeBetweenSpawns** is the delay before a new sheep will spawn.
- The last List will hold all the 'alive' sheep currently in the scene.

Now add this method:

```csharp
private void SpawnSheep()
{
    Vector3 randomPosition = sheepSpawnPositions[Random.Range(0, sheepSpawnPositions.Count)].position; 
    GameObject sheep = Instantiate(sheepPrefab, randomPosition, sheepPrefab.transform.rotation); 
    sheepList.Add(sheep); 
    sheep.GetComponent<Sheep>().SetSpawner(this); 
}
```

This method spawns a single sheep randomly at one of the spawn points.

Let's break down the first line: this grabs one of the spawn points at random. We can rewrite that line out in longer form to see what's happening:

```csharp
int maxSpawnPoints = sheepSpawnPositions.Count;
int randomNum = Random.Range(0, maxSpawnPoints);
Transform randomSpawnPoint = sheepSpawnPositions[randomNum];
Vector3 randomPosition = randomSpawnPoint.position; 
```

`Random.Range` returns a random int between the provided min and max numbers - min **inclusive** and max **exclusive**. So, here we are returning either 0, 1 or 2. We get the Transform at that index from the List, and then get the position info from that Transform.

The next line instantiates a new sheep: it uses the sheepPrefab reference as its template, the randomPosition we just grabbed as its start position, and its own default rotation as its starting rotation.

Then, add this newly created sheep to the **sheepList**.

The final line won't compile because we are trying to set a reference to **this** spawner script *onto* the Sheep... but we haven't created the `SetSpawner();` method in the **Sheep** script yet. We'll do that now.

Save the **SheepSpawner** script and open the **Sheep** script. Add this variable below the others:

```csharp
private SheepSpawner sheepSpawner;
```

Now add this method to fill in this reference:

```csharp
public void SetSpawner(SheepSpawner spawner)
{
    sheepSpawner = spawner;
}
```
