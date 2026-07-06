# config.yaml
simulation:
  iterations: 10
  population_file: "population.csv"
  output_file: "output.csv"
  random_seed: 42
  verbose: true
  id_column: "person_id"

models:
  # --- MODEL 1: Aging ---
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

  # --- MODEL 2: [YOUR MODEL HERE] ---
  # Add more models here

statistics:
  # --- STATISTIC 1: Total Population ---
  - name: "population_total"
    description: "Total population"
    script: |
      function statistic(population)
        return { total = #population }
      end

  # --- STATISTIC 2: [YOUR STATISTIC HERE] ---
  # Add more statistics here