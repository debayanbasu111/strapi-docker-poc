# 🚀 Getting started with Strapi

Strapi comes with a full featured [Command Line Interface](https://docs.strapi.io/dev-docs/cli) (CLI) which lets you scaffold and manage your project in seconds.

### `develop`

Start your Strapi application with autoReload enabled. [Learn more](https://docs.strapi.io/dev-docs/cli#strapi-develop)

```
npm run develop
# or
yarn develop
```

### `start`

Start your Strapi application with autoReload disabled. [Learn more](https://docs.strapi.io/dev-docs/cli#strapi-start)

```
npm run start
# or
yarn start
```

### `build`

Build your admin panel. [Learn more](https://docs.strapi.io/dev-docs/cli#strapi-build)

```
npm run build
# or
yarn build
```

## ⚙️ Deployment

Strapi gives you many possible deployment options for your project including [Strapi Cloud](https://cloud.strapi.io). Browse the [deployment section of the documentation](https://docs.strapi.io/dev-docs/deployment) to find the best solution for your use case.

```
yarn strapi deploy
```

## 📚 Learn more

- [Resource center](https://strapi.io/resource-center) - Strapi resource center.
- [Strapi documentation](https://docs.strapi.io) - Official Strapi documentation.
- [Strapi tutorials](https://strapi.io/tutorials) - List of tutorials made by the core team and the community.
- [Strapi blog](https://strapi.io/blog) - Official Strapi blog containing articles made by the Strapi team and the community.
- [Changelog](https://strapi.io/changelog) - Find out about the Strapi product updates, new features and general improvements.

Feel free to check out the [Strapi GitHub repository](https://github.com/strapi/strapi). Your feedback and contributions are welcome!

## ✨ Community

- [Discord](https://discord.strapi.io) - Come chat with the Strapi community including the core team.
- [Forum](https://forum.strapi.io/) - Place to discuss, ask questions and find answers, show your Strapi project and get feedback or just talk with other Community members.
- [Awesome Strapi](https://github.com/strapi/awesome-strapi) - A curated list of awesome things related to Strapi.

## Detailed Steps Debayan followed to set this up

- Install Node.js
- Run - npx create-strapi-app
- What would you like to name your project? docker-strapi-tutorial
- Move into the project folder - cd docker-strapi-tutorial
- Open it in VSCode - code .
- Create a file called Dockerfile
- Paste below content -
```
FROM node:18

# Installing libvips-dev for sharp Compatability

RUN apt-get update && apt-get install libvips-dev -y
ARG NODE_ENV=development
ENV NODE_ENV=${NODE_ENV}
WORKDIR /opt/
COPY ./package.json ./package-lock.json ./
ENV PATH /opt/node_modules/.bin:$PATH
RUN npm install
WORKDIR /opt/app
COPY ./ .
RUN npm run build
EXPOSE 1337
CMD ["npm", "run", "develop"]
```
Create a file called .dockerignore
Paste below content -
```
.tmp/
.cache/
.git/
build/
node_modules/
data/
```
- Build the image - docker build -t mystrapi:latest .
- Run the image - docker run -d -p 1337:1337 mystrapi

- List running containers: docker ps
- Stop the container by its name or ID: docker stop <container_name_or_id>

- Article to refer - https://blog.dehlin.dev/docker-with-strapi-v4

- Created a docker-compose.yml file the root position
- Pasted below content
```
version: "3"
services:
strapi:
container_name: strapi
build: .
image: mystrapi:latest
restart: unless-stopped
env_file: .env
environment:
DATABASE_CLIENT: ${DATABASE_CLIENT}
DATABASE_HOST: strapiDB
DATABASE_NAME: ${DATABASE_NAME}
DATABASE_USERNAME: ${DATABASE_USERNAME}
DATABASE_PORT: ${DATABASE_PORT}
JWT_SECRET: ${JWT_SECRET}
ADMIN_JWT_SECRET: ${ADMIN_JWT_SECRET}
DATABASE_PASSWORD: ${DATABASE_PASSWORD}
NODE_ENV: ${NODE_ENV}
volumes: - ./config:/opt/app/config - ./src:/opt/app/src - ./package.json:/opt/package.json - ./yarn.lock:/opt/yarn.lock # Replace with package-lock.json if using npm - ./.env:/opt/app/.env
ports: - "1337:1337"
networks: - strapi
depends_on: - strapiDB

strapiDB:
image: postgres:12.0-alpine
container_name: strapiDB
platform: linux/amd64 #for platform error on Apple M1 chips
restart: unless-stopped
env_file: .env
environment:
POSTGRES_USER: ${DATABASE_USERNAME}
POSTGRES_PASSWORD: ${DATABASE_PASSWORD}
POSTGRES_DB: ${DATABASE_NAME}
volumes: - strapi-data:/var/lib/postgresql/data/ #using a volume
#- ./data:/var/lib/postgresql/data/ # if you want to use a bind folder
ports: - "5432:5432"
networks: - strapi

volumes:
strapi-data:

networks:
strapi:
name: Strapi
driver: bridge
```
- Install pg to point to postgres DB instead of the default SQLite DB - npm i pg
- Rebuild the Docker image: docker build -t mystrapi:latest .
- Update below line in config/database.ts file - const client = env('DATABASE_CLIENT', 'postgres');
- Update DB details in .env file as below -
```
# Database

DATABASE_CLIENT=postgres
DATABASE_HOST=strapiDB
DATABASE_PORT=5432
DATABASE_NAME=strapi
DATABASE_USERNAME=strapi
DATABASE_PASSWORD=strapi
DATABASE_SSL=false
DATABASE_FILENAME=.tmp/data.db
JWT_SECRET=Gjk+U5Sxm8LXEo/qxfv8sQ==
```
- To run this -

- docker-compose up -d strapiDB && npm run develop - This will now spin up just a Postgres database, and we can run and change files just like working on strapi anywhere. Here, DATABASE_HOST=localhost

- docker-compose up -d' - This will run strapi inside a docker container and the database in its own container. Here, DATABASE_HOST=strapiDB

I hope it helps you to set the whole system up!!

---

<sub> [Connect with me on LinkedIn for any doubts](linkedin.com/in/debayanbasu11/).</sub>
