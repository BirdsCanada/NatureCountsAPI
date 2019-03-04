# NatureCounts Taxonomy #

NatureCounts generally follow Clements and eBird taxonomy for birds species, and is being updated annually
shortly after the Clements updates are published (usually in August). The eBird taxonomy includes species and 
additional concepts such as species and subspecies groups and hybrids. NatureCounts also recognizes a snall number of
non-species bird taxa, and also includes taxonomic concepts for other groups (plants, insects, mammals, etc.).

Each concept is assigned a numeric species ID, which is used in all data tables. Those arbitrary numeric ID are stable 
representations of taxonomic concepts. They do not imply taxonomic sequence, but you can use the sort_order field provided
by the species API entry point for the purpose of taxonomic ordering. Readers interested in learning more about unique
taxonomic concepts identifiers should read the [Avibase paper](https://zookeys.pensoft.net/article/3906/) published in Zookeys.

Be aware that the numbers may change over time, but only in cases where species limits are being redefined (e.g. a split or a 
lump). Simple name changes (e.g. changing to a new genera, changing family, common name changes) have in theory no bearing on 
taxonomic concepts. On the other hand, species may retain the same names, but be subject to changes in their species limits,
and those types of changes will result in issuing a different species ID. 

For instance, Thayer's Gull (Larus thayeri) was until recently considered a distinct species (species_id: 5190). Until then, Iceland Gull
(Larus glaucoides) was assigned species_id 5200. When Thayer's Gull was lumped with Iceland Gull in 2018, both species formed a new species concept which was assigned a distinct species_id: 40393. In the new taxonomy, 5190 and 5200 are now considered subspecies groups.
In the species table, both are assigned 40393 as their parent concept. Note that old concepts are not always retained in the new taxonomy, 
so numeric ID's from NatureCounts data downloaded earlier may no longer match any number in the current list of species.

| Species_id | Scientific name < 2018 | Scientific name > 2018 | Parent_id |
| -------------- | ---- | ----------- | ------- |
| 40939 | - | Larus glaucoides |  - |
| 5190 | Larus glaucoides  | Larus glaucoides glaucoides/kumlieni |  40939 |
| 5200 | Larus thayeri  | Larus glaucoides thayeri |  40939 |

On occasion, taxonomic changes will affect extralimital regions. NatureCounts primarily focusses on Canadian and other North American data, but the taxonomy is global. Peregrine Falcon and Ruddy Duck are two such recent examples. Barbary Falcon (Falco pelegrinoides) was previously considered a separate species, and was lumped with Peregrine Falcon (Falco peregrinus) in 2016, and Andean Duck (Oxyura ferruginea) was split from Ruddy Duck (Oxyura jamaicensis) in 2018. While in a North American context, Ruddy Duck and Peregrine Falcon refer to the same birds, they were both assigned to a new ID when the change occurred. 

During the taxonomic update, a best attempt is made to reallocate the current data to new concepts. In the case of splits, this is not always possible.

**Recommendations**

If you write scripts and programs that rely on a specific list of species that you wish to analyse over time, we would recommend *Not* using numeric species ID's as part of your code. Using species alphanumeric codes (e.g. 4-letter banding codes), and converting those to numeric ID's in real time based on recent metadata is generally a more stable approach.

metadata/species
metadata/species_code_authority
metadata/species_codes
