# Talos Tutorial 1: Building an Aging Model

## Overview

In this tutorial, you'll learn how to create a simple aging model with Talos. We'll start with a CSV population file, write a configuration that ages everyone by one year, run the simulation, and analyze the output.

## Prerequisites

- Talos binary downloaded and in your PATH (or in the current directory)
- Basic understanding of CSV files
- A text editor (VS Code, Sublime, Notepad++, etc.)

## What You'll Learn

By the end of this tutorial, you'll be able to:
- Create a population CSV file
- Write a YAML configuration file
- Run a Talos simulation
- Understand the basic structure of a Talos configuration

**Important Note:** You don't need to be a programmer to use Talos! We're using **Lua**, a simple and readable scripting language, for our models. Think of it as writing instructions in plain English:

- **Change**: "Make everyone one year older" → `person.age = person.age + 1`
- **Ask**: "How many people are there?" → `return { total = #population }`

No complex programming, no compilation - just simple instructions that read like plain English!

## Step 1: Create a Population CSV

First, let's create a small population to work with. Create a file called `population.csv`:

```csv
person_id,age,sex,area,alive
1,25,F,1,true
2,30,M,1,true
3,45,F,1,true
4,68,M,1,true
5,82,F,1,true
6,2,M,1,true
7,15,F,1,true
8,35,M,1,true
9,55,F,1,true
10,70,M,1,true
```

This gives us 10 individuals with various ages. The columns are:
- `person_id`: Unique identifier for each person
- `age`: Age in years
- `sex`: Gender (M/F)
- `area`: Geographic area (we'll use just one area for now)
- `alive`: Whether the person is alive (true/false)

## Step 2: Understanding YAML

YAML (YAML Ain't Markup Language) is a human-readable data format that Talos uses for configuration.

**Important Rule:** YAML uses **spaces** for indentation, **never tabs**. The number of spaces doesn't matter as long as it's consistent (2 spaces is standard). 

That's it for now! We'll cover more YAML rules in the next tutorial. For now, just copy the configuration below exactly as shown.

## Step 3: Create the Configuration File

Create a file called `config_aging.yaml` and paste in the following:

```yaml
# config_aging.yaml
# A simple aging model configuration

simulation:
  iterations: 5                    # Run for 5 years
  population_file: "population.csv" # Input population
  output_file: "population_aged.csv" # Output after aging
  random_seed: 42                   # For reproducibility
  verbose: true                     # Show detailed output
  id_column: "person_id"            # REQUIRED: Primary key column

models:
  - name: "age_increment"
    type: "lua_model"
    priority: 1
    enabled: true
    description: "Increment everyone's age by 1 year"
    parameters:
      script: |
        function transition(population, params)
          for _, person in ipairs(population) do
            if person.alive == true then
              person.age = person.age + 1
            end
          end
          return population
        end

statistics:
  - name: "population_total"
    description: "Total population"
    script: |
      function statistic(population)
        return { total = #population }
      end
```

## Step 4: Run the Simulation

Now run Talos with your configuration:

```bash
# If talos is in your PATH
talos config_aging.yaml

# Or if talos is in the current directory
./talos config_aging.yaml
```

## Expected Output

You should see output similar to this:

```
2024/01/15 10:00:00 ═══ Talos-Pure: Migration Microsimulation ═══
2024/01/15 10:00:00 Iterations: 5
2024/01/15 10:00:00 Population file: population.csv
2024/01/15 10:00:00 ID column: person_id
2024/01/15 10:00:00 Models loaded: 1
2024/01/15 10:00:00 Statistics defined: 1
2024/01/15 10:00:00 Loaded 10 individuals with 4 columns
2024/01/15 10:00:00 Columns: [person_id age sex area alive]
2024/01/15 10:00:00 Enabled models: 1
2024/01/15 10:00:00   - age_increment (priority: 1)

2024/01/15 10:00:00 ═══ Iteration 1/5 ═══
2024/01/15 10:00:00   ▶ age_increment
2024/01/15 10:00:00   📊 Statistics:
2024/01/15 10:00:00     population_total (Total population): total: 10

2024/01/15 10:00:00 ═══ Iteration 2/5 ═══
2024/01/15 10:00:00   ▶ age_increment
2024/01/15 10:00:00   📊 Statistics:
2024/01/15 10:00:00     population_total (Total population): total: 10

2024/01/15 10:00:00 ═══ Iteration 3/5 ═══
2024/01/15 10:00:00   ▶ age_increment
2024/01/15 10:00:00   📊 Statistics:
2024/01/15 10:00:00     population_total (Total population): total: 10

2024/01/15 10:00:00 ═══ Iteration 4/5 ═══
2024/01/15 10:00:00   ▶ age_increment
2024/01/15 10:00:00   📊 Statistics:
2024/01/15 10:00:00     population_total (Total population): total: 10

2024/01/15 10:00:00 ═══ Iteration 5/5 ═══
2024/01/15 10:00:00   ▶ age_increment
2024/01/15 10:00:00   📊 Statistics:
2024/01/15 10:00:00     population_total (Total population): total: 10

2024/01/15 10:00:00 ═══ Simulation Complete ═══
2024/01/15 10:00:00 Results saved to population_aged.csv
```

## Step 5: Examine the Output

Open `population_aged.csv`:

```csv
person_id,age,sex,area,alive
1,30,F,1,true
2,35,M,1,true
3,50,F,1,true
4,73,M,1,true
5,87,F,1,true
6,7,M,1,true
7,20,F,1,true
8,40,M,1,true
9,60,F,1,true
10,75,M,1,true
```

Notice that everyone has aged exactly 5 years:
- Person 1: 25 → 30
- Person 2: 30 → 35
- Person 3: 45 → 50
- Person 4: 68 → 73
- Person 5: 82 → 87
- Person 6: 2 → 7
- Person 7: 15 → 20
- Person 8: 35 → 40
- Person 9: 55 → 60
- Person 10: 70 → 75

## Understanding Your Configuration

Now let's break down what the configuration does. The structure is simple - three main sections:

### 1. The Simulation Section

```yaml
simulation:
  iterations: 5                    # Run for 5 years
  population_file: "population.csv" # Input population
  output_file: "population_aged.csv" # Output after aging
  random_seed: 42                   # For reproducibility
  verbose: true                     # Show detailed output
  id_column: "person_id"            # REQUIRED: Primary key column
```

This tells Talos:
- **How many times** to run the model (`iterations: 5`)
- **Where to read** the population from (`population_file`)
- **Where to save** the results (`output_file`)
- **What random seed** to use for reproducible results (`random_seed: 42`)
- **Whether to show detailed output** (`verbose: true`)
- **Which column is the unique identifier** (`id_column: "person_id"`)

**Why is `id_column` required?** Talos uses this column to order the output CSV consistently, making it easier to compare results across different runs.

### 2. The Models Section

```yaml
models:
  - name: "age_increment"
    type: "lua_model"
    priority: 1
    enabled: true
    description: "Increment everyone's age by 1 year"
    parameters:
      script: |
        function transition(population, params)
          for _, person in ipairs(population) do
            if person.alive == true then
              person.age = person.age + 1
            end
          end
          return population
        end
```

This defines our aging model:

- **`name`**: A descriptive name for the model
- **`type`**: The type of model (`lua_model` means it's a Lua script)
- **`priority`**: The order to run models (lower numbers run first)
- **`enabled`**: Whether this model is active (`true` or `false`)
- **`description`**: Human-readable description
- **`parameters.script`**: The Lua code that does the work

### Understanding the Lua Script

Let's look at our model's Lua script:

```lua
function transition(population, params)
  for _, person in ipairs(population) do
    if person.alive == true then
      person.age = person.age + 1
    end
  end
  return population
end
```

**First, what does this Lua do in plain English?**

> "Go through everyone in the population. For each person who is alive, take their current age, add 1 to it, and save that new age back to the population."

**Now let's break it down piece by piece:**

| Part | What it does | In plain English |
|------|--------------|------------------|
| `function transition(population, params)` | Defines the model function | "This is the model that will run each year" |
| `for _, person in ipairs(population) do` | Loop through each person | "For each person in the population..." |
| `if person.alive == true then` | Check if alive | "...if they are alive..." |
| `person.age = person.age + 1` | Add 1 to age | "...add 1 to their age" |
| `return population` | Return updated population | "Send back the updated population" |

**Why does this work?** When Talos runs this script, it goes through each person one at a time:
1. Checks if they are alive (`person.alive == true`)
2. If they are, it reads their current age
3. Adds 1 to it
4. Saves the new age back

So someone who is 25 becomes 26. Someone who is 68 becomes 69. Someone who is 2 becomes 3. Everyone gets one year older!

### 3. The Statistics Section

```yaml
statistics:
  - name: "population_total"
    description: "Total population"
    script: |
      function statistic(population)
        return { total = #population }
      end
```

This defines a statistic:

- **`name`**: A descriptive name for the statistic
- **`description`**: Human-readable description
- **`script`**: Lua code that calculates the statistic

### Understanding the Statistics Script

Let's look at our statistics script:

```lua
function statistic(population)
  return { total = #population }
end
```

**First, what does this Lua do in plain English?**

> "Count up every single person in the population and tell me how many there are. Call this number 'total' so I know what it means."

**Now let's break it down piece by piece:**

| Part | What it does | In plain English |
|------|--------------|------------------|
| `function statistic(population)` | Defines the statistic function | "This is the statistic that will run each year" |
| `return` | Send back the result | "Tell me the answer" |
| `{ total = #population }` | Create a table with count | "The answer is the number of people in the population" |

**What is `#population`?** In Lua, `#` counts the number of items in a list. So `#population` means "the number of people in the population."

**Why do we need this?** The statistic shows us the total population count at each iteration. Since we're only aging people (not adding or removing anyone), the total should always be 10. This confirms our model is working correctly.

## What You've Accomplished

Congratulations! You've successfully:

1. ✅ Created a population CSV file
2. ✅ Written a YAML configuration file
3. ✅ Run a Talos simulation
4. ✅ Aged an entire population by 5 years
5. ✅ Tracked population totals

You now know the basic workflow for using Talos.

## Next Steps

In the next tutorial, we'll dive deeper into YAML and Lua. You'll learn:

- **YAML rules**: Why indentation matters, why you need the pipe (`|`) for multi-line strings, and how to avoid common mistakes
- **More Lua**: Adding more statistics like age groups, sex distribution, and age range
- **Column name matching**: Why column names must match your CSV exactly
- **Mortality**: Adding a mortality model to make your simulation more realistic

For now, take a moment to appreciate what you've built - a working demographic microsimulation model!

---

**Tutorial 2 Preview:** We'll explore the YAML and Lua rules in detail, add more statistics like age groups and sex distribution, then add a mortality model with age-specific death probabilities.
