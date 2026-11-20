# Knowledge Base Design — Autonomous Trailer

## Purpose

This knowledge base models two directed candidate corridors for a trailer travelling from Nairobi to Kisumu. It stores locations, road-segment distances and optional road-status facts, then applies Uniform Cost Search (UCS) to select the shortest encoded path. It also fetches fuel-stop points from OpenStreetMap's Overpass API.

## Route facts

The six segment distances and Google Maps direction links were supplied with the route data:

| Candidate | Directed road segments (km) | Calculated total |
| --- | --- | ---: |
| Via Nakuru and Kericho | Nairobi→Nakuru 159; Nakuru→Kericho 107; Kericho→Kisumu 84 | **350 km** |
| Via Narok and Bomet | Nairobi→Narok 143; Narok→Bomet 81; Bomet→Kisumu 131 | **355 km** |

The notebook uses the six segment facts to build the graph and run UCS. The totals are calculated by summing the distances of the three directed segments in each route.

Only Nairobi-to-Kisumu directed arcs are encoded because those are the directions supplied. Reverse arcs and other possible roads are not assumed. The graph is limited to the two candidate corridors shown.

## Representation

| Fact type | Fields | Use |
| --- | --- | --- |
| Location | name, region, latitude, longitude | Identifies a modeled town and gives an approximate centre for point-of-interest lookup. |
| Road | from, to, corridor | Records a directed traversable segment. |
| Distance | from, to, kilometres | Supplies the non-negative cost used by UCS. |
| FuelStop | OSM type and ID, name, brand, coordinates, tags | Adds fuel-station points retrieved from Overpass at notebook runtime. |
| Blocked | from, to | Optional scenario fact that excludes a directed road from a query. |

The route cost is the sum of its directed segment distances. UCS expands the frontier node with the lowest accumulated distance and returns a minimum-cost path for this encoded graph. The notebook records generated search-tree edges and expansion order so the choice can be explained from the facts.

## Additional data source

At notebook runtime, an Overpass query retrieves OpenStreetMap objects tagged `amenity=fuel` within five kilometres of each modeled town centre. The notebook records a UTC retrieval timestamp and retains OSM IDs and tags for traceability. Results can be incomplete or stale; they do not verify opening hours, fuel availability, trailer access, or safety. The notebook includes OpenStreetMap attribution and an ODbL link. No live traffic feed is used.

## Sources and limitations

- The six Google Maps direction links, paired with their supplied segment values, are listed in the notebook's Sources section.
- [Overpass API](https://wiki.openstreetmap.org/wiki/Overpass_API), [Overpass QL guide](https://wiki.openstreetmap.org/wiki/Overpass_API/Language_Guide), and [OpenStreetMap copyright and license](https://www.openstreetmap.org/copyright).
- [OSRM project documentation](https://github.com/Project-OSRM/osrm-backend) is cited only to distinguish map routing data from live traffic data; OSRM is not used as a live traffic source in this notebook.
- Workflow-format reference: https://github.com/AllanVikiru/ArtificialIntelligence/blob/kba/Knowledge_Engineering.ipynb. Its traffic-light example is not used as route data.

Before submission, record when the map measurements were checked and confirm that the class map includes no other candidate roads. Add authentic group meeting minutes or a group photo in the notebook if required by the exercise.
