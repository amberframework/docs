# Docker and Docker Compose

When you generate a new Amber project, we include `Docker` and `docker-compose.yml` files to simplify development. This is an alternative way of getting an Amber project up and running quickly and doesn't require installing a database.

## Dockerfile

The Docker file will have everything you need to build and run your amber project.  This includes all needed the libraries, Nodejs, Crystal and Amber command line tools.  It starts from the official `crystallang/crystal:{version}` docker image.

It will also add the project to the `/app` folder on the image so you can use this in production as well.

## docker-compose.yml

The `docker-compose.yml` file will allow you to easily get up and running.  It includes the `database`, `mailcatcher`, `migrate`, `app`, and `web` containers.

### The `database` container

This container is using an image based on the `-d` option you chose when you created the project.  Right now, we support pg, mysql, sqlite

A `db` volume is also created so you can start and stop the containers but the data will still be available.

### The `mailcatcher` container

Mail Catcher is a simple smtp server that will allow you to see the email that gets generated.  You can access the user interface by hitting [http://localhost:1080](http://localhost:1080).  If you generate mailers, it will configure the `SMTP_URL` to point to the mail catcher container.

### The `migrate` container

This container will run the migrations using `amber db migrate seed`.  It will wait for the database port to be available before running the migrations.  This container will exit after completing the migrations.

### The `app` container

This container will run `amber watch` on port 3000.  You can access the application at [http://localhost:3000](http://localhost:3000).  The external project directory is mapped to `/app/local` so as you make changes to your project and amber will watch for changes, recompile and re-launch the application.

### The `web` container

This container will run `npm install && npm run watch`.  This will compile your front-end assets and will watch for changes.  You can see the webpack configuration in the `config/webpack` directory to see what assets are compiled.

## Usage

You can launch the docker-compose environment by running:

```bash
docker-compose up
```

Once all the docker containers are launched, you can access the site using:
```bash
open http://localhost:3000
```

And you can access the mailcatcher at:
```bash
open http://localhost:1080
```
