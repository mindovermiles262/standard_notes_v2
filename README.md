elf-hosting with Docker

This guide walks you through the process of installing the self-hosted backend of Standard Notes. While writing this guide, we used a server running Ubuntu 22.04, with 2GB RAM and 1 CPU.
Version 2.0

This is Version 2 of our self-hosted setup, released in February 2023. If you began self-hosting prior to this, you are using the "legacy" setup.

Version 2 features only 4 total containers (compared to 13 for legacy) and an over 65% reduction in memory usage (570MB in V2 vs. 1.7GB in legacy for the primary container(s)). Migration instructions from legacy to V2 can be found here.
Prerequisities

Configure Docker using the most up to date installation instructions on the Docker website.
Configuration

Running Standard Notes services requires specifying certain environment variables. You can find them in our Docker entrypoint file
Step By Step

    Create a folder where you want to store your working files. We will refer to this folder as your working directory.

    mkdir standardnotes
    cd standardnotes

Create a .env file in your working directory. Then copy our example values to your file:

touch .env
curl https://raw.githubusercontent.com/standardnotes/server/main/.env.sample > .env

This file contains only the minimum variables required for functioning. You can view the full listing of in-use env vars here.

Open your .env file and make sure the values for the variables in the KEYS section are properly set. You can run the following command in order to generate a key value for each of the required environment variable:

openssl rand -hex 32

Copy the LocalStack bootstrap script and place it in your working directory. Ensure the file has executable permissions:

curl https://raw.githubusercontent.com/standardnotes/server/main/docker/localstack_bootstrap.sh > localstack_bootstrap.sh
chmod +x localstack_bootstrap.sh

Copy the docker-compose.yml file to a file called docker-compose.yml in your working folder:

curl https://raw.githubusercontent.com/standardnotes/server/main/docker-compose.example.yml > docker-compose.yml

(Optional) You can customize your setup in the docker-compose.yml file to your liking. For example, in the server service you can edit the port mappings (ports property) to whatever fits your needs.

Change the database password:

    Generate a secure random string:

openssl rand -hex 12

    Edit DB_PASSWORD to this value in .env

    Edit MYSQL_ROOT_PASSWORD and MYSQL_PASSWORD to this value in docker-compose.yml.

After you've finished your edits run the following command:

docker compose pull && docker compose up -d

It will take a moment for the infrastructure to spin up. You can monitor the logs of all services by running the following command:

tail -f logs/*.log

    If you encounter issues, check the error logs: tail -f logs/*.err.

After the infrastructure is up and running, you can check it's available by visiting http://localhost:3000 in your browser.
Next Steps
Updating your server:

Before updating your server, please make sure you have a backup of your data. You can find instructions on how to backup your data here.

After you've made sure you have a backup of your data, you need to stop the server by running the following command:

docker compose down

First, you need to update the following files:

    docker-compose.yml - latest version available here
    .env - latest version available here
    localstack_bootstrap.sh - latest version available here

After you've updated the files, run the following commands:

docker compose pull
docker compose up -d

You are now running the latest version of the server.
Customize ports and endpoints

By default, the server will run on port 3000 and the files server will run on port 3125. If you have a different service running on these ports, you can customize the port on which you want to run the infrastructure by editing the docker-compose.yml file.
Files Functionality

In your .env file you can add the PUBLIC_FILES_SERVER_URL environment variable to point the final public-facing URL of your files server. For example, if you are self-hosting on a server on your local network which you know has an IP of 10.0.1.2, set this value to http://10.0.1.2:3125. Read more about configuring your setup for files.
Securing your server

Head over to Securing HTTP traffic of your Sync server for more information on how to set up a reverse proxy.
Using your new server

In the account menu, choose Advanced options. Under "Sync Server", select Custom, then enter the address of your new server. Then, register for a new account or log in to an existing account and begin using your private new secure Standard Notes server!

You can use your new server with the Standard Notes desktop or mobile app. You will not be able to use app.standardnotes.com with your new server. For web-app access, you'll need to self-host the web app.
Unlock client-side advanced features

Learn more about how to unlock client-side advanced features when using official Standard Notes client distributions.

