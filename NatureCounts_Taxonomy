# NatureCounts Taxonomy #

NatureCounts generally follow Clements and eBird taxonomy for birds species, and is being updated annually
shortly after the Clements updates are published (usually in August). The eBird taxonomy includes species and 
additional concepts such as species and subspecies groups and hybrids. NatureCounts also recognizes a snall number of
non-species bird taxa, and also includes taxonomic concepts for other groups (plants, insects, mammals, etc.).

Each concept is assigned a numeric species ID, which is used in all data tables. Those arbitrary numeric ID are stable 
representations of taxonomic concepts. They do not imply taxonomic sequence, but you can use the sort_order field provided
by the species API entry point. Readers interested in learning more about unique taxonomic concepts identifiers
should read the [Avibase paper](https://zookeys.pensoft.net/article/3906/) published in Zookeys.

Be aware that the numbers may change over time, but only in cases where species limits are being redefined (e.g. a split or a 
lump). For instance, until 2018, Thayer's Gull was considered a distinct species (species_id: 5190). Until then, Iceland Gull
was assigned species_id 5200. When Thayer's Gull was lumped with Iceland Gull, both species formed a new species concept
which was assigned a distinct species_id: 40393. In the new taxonomy, 5190 and 5200 are now considered subspecies groups.
In the species table, both are assigned 40393 as their parent concept.

| Species_id | Scientific name < 2018 | Scientific name > 2018 | Parent_id |
| -------------- | ---- | ----------- | ------- |
| 40939 | - | Larus glaucoides |  - |
| 5190 | Larus glaucoides  | Larus glaucoides glaucoides/kumlieni |  40939 |
| 5200 | Larus thayeri  | Larus glaucoides thayeri |  40939 |



metadata/species
metadata/species_code_authority
metadata/species_codes
