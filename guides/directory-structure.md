# Directory Structure

Every Amber app is generated with a variety of default files and folders that make up the structure of the application. When you run `amber new` to create your application, you will see that the Amber CLI creates the entire directory structure for the application for you to use. The table below details out the variety of files and folders created in the generation process as well as their purpose.

| File Folder | Purpose |
| :--- | :--- |
| config/ | Contains configuration code for initializers, environments, routing, and deployment |
| db/ | Contains database seeds and migrations. |
| lib/ | Contains local installation of all dependent shards |
| public/ | Location for html, css, and javascripts. This will be the default directory for static assets. |
| spec/ | Location for application specification and tests |
| src/ | Source code that composes your application |
| .encryption\_key | This file is used to encrypt application secrets so they are not stored in source control |
| .amber.yml | This file is generated for Amber CLI and contains settings for the command line tools |
| package.json | Amber uses Webpack and NPM to compile static assets |
| .gitignore | This file tells git which files and patterns it should ignore. |
| docker-compose.yml | A file which defines services, networking, and drives for docker deployment. |
| Dockerfile | A file which contains all the commands needed to build a docker image. |
| README.md | A brief instruction manual about your application |

