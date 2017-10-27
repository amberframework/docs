# Routes Command

The `amber routes` command returns all of your routes in a pretty table format.

## Example

```zsh
~/crystal/amberdemo  ᐅ amber routes
╔════════╦══════════════════╦═════════╦══════════╦═══════╦════════════════╗
║ Verb   | Controller       | Action  | Pipeline | Scope | URI Pattern    ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼────────────────╣
║ get    | StaticController | index   | static   |       | /*             ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼────────────────╣
║ get    | PetController    | index   | web      |       | /pets          ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼────────────────╣
║ get    | PetController    | show    | web      |       | /pets/:id      ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼────────────────╣
║ get    | PetController    | new     | web      |       | /pets/new      ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼────────────────╣
║ get    | PetController    | edit    | web      |       | /pets/:id/edit ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼────────────────╣
║ post   | PetController    | create  | web      |       | /pets          ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼────────────────╣
║ patch  | PetController    | update  | web      |       | /pets/:id      ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼────────────────╣
║ put    | PetController    | update  | web      |       | /pets/:id      ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼────────────────╣
║ delete | PetController    | destroy | web      |       | /pets/:id      ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼────────────────╣
║ get    | HomeController   | index   | web      |       | /              ║
╚════════╩══════════════════╩═════════╩══════════╩═══════╩════════════════╝
```
