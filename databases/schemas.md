# Schemas

- try go only group related columns together, because grouping unrelated data leads to grouping unrelated functions which leads to spaghetti code (e.g. think about how bad user objects can get)

## Nulls
- allowing nulls in databases takes extra overhead so be warry about allowing too many nulls in your db if you are looking to squeeze performance or reduce your memory footprint
- cannot assume that indexes will support nulls