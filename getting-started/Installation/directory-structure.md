# Amber Application Directory Structure

A generated Amber app has a number of auto-generated files and folders that make up the structure of an Amber application. 

When you use the `amber new` command to create your application, it creates the entire directory structure for the application, because of this Amber knows where to find the files it needs within this structure, so you don't have to provide additional input.

| File Folder | Purpose |
| :--- | :--- |
| config/ | Contains configuration code for initializers, environments, routing, and deployment |
| db/ | Contains database seeds and migrations. |
| lib/ | Contains local installation of all dependent shards |
| public/ | Location for html, css, and javscripts. This will be the default directory for static assets. |
| spec/ | Location for application specification and tests |
| src/ | Source code that composes your application |
| .encryption\_key | This file is used to encrypt application secrets so they are not stored in source control |
| .amber.yml | This file is generate for Amber CLI and contains settings for the command line tools |
| package.json | Amber utilizes Webpack and NPM to compile static assets |
| .gitignore | This file tells git which files and patterns it should ignore. |
| docker-compose.yml | A file which defines services, networking, and drives for docker deployment. |
| Dockerfile | A file which contains all the commands needed to build a docker image. |
| README.md | A brief instruction manual about your application |
