# ♫ Album A Day ♫
![Album A Day](https://github.com/albumaday/.github/blob/main/public/album_a_day_logo.png?raw=true)

# Repos
| Repo       | Access  | Tags                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | 
|------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .github    | read    | ![README](https://img.shields.io/badge/ReadMe-018EF5.svg?style=for-the-badge&logo=ReadMe&logoColor=white)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| aad-docker | read    | ![Docker](https://img.shields.io/badge/Docker-2496ED.svg?style=for-the-badge&logo=Docker&logoColor=white) ![.env](https://img.shields.io/badge/.ENV-ECD53F.svg?style=for-the-badge&logo=dotenv&logoColor=black)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| aad-sdk    | read    | ![Java](https://img.shields.io/badge/java-%23ED8B00.svg?style=for-the-badge&logo=openjdk&logoColor=white) ![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F.svg?style=for-the-badge&logo=Spring-Boot&logoColor=white) ![AWS](https://img.shields.io/badge/Amazon%20AWS-232F3E.svg?style=for-the-badge&logo=Amazon-AWS&logoColor=white) ![JUnit5](https://img.shields.io/badge/JUnit5-25A162.svg?style=for-the-badge&logo=JUnit5&logoColor=white)                                                                                                                                                                                                                                                                                                                                                                      |
| aad-bot    | private | ![Java](https://img.shields.io/badge/java-%23ED8B00.svg?style=for-the-badge&logo=openjdk&logoColor=white) ![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F.svg?style=for-the-badge&logo=Spring-Boot&logoColor=white) ![Discord](https://img.shields.io/badge/Discord-5865F2.svg?style=for-the-badge&logo=Discord&logoColor=white) ![Spotify](https://img.shields.io/badge/Spotify-1DB954.svg?style=for-the-badge&logo=Spotify&logoColor=white) ![Apple Music](https://img.shields.io/badge/Apple%20Music-FA243C.svg?style=for-the-badge&logo=Apple-Music&logoColor=white) ![Google Sheets](https://img.shields.io/badge/Google%20Sheets-34A853.svg?style=for-the-badge&logo=Google-Sheets&logoColor=white) ![OpenAI](https://img.shields.io/badge/OpenAI-412991.svg?style=for-the-badge&logo=OpenAI&logoColor=white) |
| aad-client | private | Coming Soon                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

# Contributions

If you have any ideas for something that should be added to Album A Day, feel free to submit an issue as described below.

Code contributions are only being accepted by CODEOWNERS at the moment.

### Submitting issues

**NOTE:** You will need to have a GitHub account to be able to submit issues.

To submit issues, head to the **.github** public repository in this organization, then navigate to the **Issues** tab.
![image](https://user-images.githubusercontent.com/13096122/225754587-fe71956f-379d-4010-9934-2918c2793a07.png)

# B2EMO Bot

The active Kanban board for the B2EMO Bot can be [here](https://github.com/orgs/albumaday/projects/1/views/1).

## AWS EC2

### New Instance

#### Env Configuration

Make sure you have the following in place within your aad-docker folder
```markdown
aad-docker:
- env
    - aad-bot
        - .env.local
        - .env.staging
        - .env.production
        - google-creds.json
    - aad-sdk
        - .env.local
        - .env.staging
        - .env.production
```
The contents of which, well, I can't tell you that!

#### First Commands

After first connection via SSH:
```shell
ssh -i $AAD_PEM $AAD_EC2_USER@<EC2_INSTANCE_IPv4_DNS>
```

You'll want to run the following commands for your first visit to the EC2 instance via SSH:
```shell
# Open session to your EC2 instance
ssh -i $AAD_PEM $AAD_EC2_USER@<EC2_INSTANCE_IPv4_DNS>
# --

sudo yum update -y
sudo amazon-linux-extras install docker
sudo service docker start
sudo usermod -a -G docker ec2-user

# Close session
exit
```
```shell
# Open new session to the same EC2 instance
ssh -i $AAD_PEM $AAD_EC2_USER@<EC2_INSTANCE_IPv4_DNS>
# --

 # Test permissions for docker
docker ps

# Login to Docker
docker login

# Install and confirm docker-compose is working
sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose version

# Make necessary directories for deployment
mkdir env; cd env; mkdir aad-bot; mkdir aad-sdk;

# Close session
exit
```

Now you're all set for Deployment

### Deployment

#### Production
![B2EMO](https://github.com/albumaday/.github/blob/main/public/B2EMO.png?raw=true)

Copy the proper files for running the Bot and SDK images correctly:
```shell
scp -i $AAD_PEM $AAD_DOCKER_DIR/docker-compose-production.yml $AAD_EC2_USER@$AAD_PRODUCTION_EC2:.
scp -i $AAD_PEM $AAD_DOCKER_DIR/env/aad-sdk/.env.production $AAD_EC2_USER@$AAD_PRODUCTION_EC2:./env/aad-sdk/.env.production
scp -i $AAD_PEM $AAD_DOCKER_DIR/env/aad-bot/.env.production $AAD_EC2_USER@$AAD_PRODUCTION_EC2:./env/aad-bot/.env.production
scp -i $AAD_PEM $AAD_DOCKER_DIR/env/aad-bot/google-creds.json $AAD_EC2_USER@$AAD_PRODUCTION_EC2:./env/aad-bot/google-creds.json
```

Login to the Staging EC2 instance:
```shell
ssh -i $AAD_PEM $AAD_EC2_USER@$AAD_PRODUCTION_EC2
```

Run docker-compose:
```shell
docker-compose -f docker-compose-production.yml up --build --detach
```

##### Extra

To check the status:
```shell
docker ps
```

To check the logs of each container created:
```shell
docker container logs aad-bot
docker container logs aad-sdk
```

To stop either the containers:
```shell
docker stop aad-bot
docker stop aad-sdk
```

#### Staging
![S2EMO](https://github.com/albumaday/.github/blob/main/public/S2EMO.png?raw=true)

Copy the proper files for running the Bot and SDK images correctly:
```shell
scp -i $AAD_PEM $AAD_DOCKER_DIR/docker-compose-staging.yml $AAD_EC2_USER@$AAD_STAGING_EC2:.
scp -i $AAD_PEM $AAD_DOCKER_DIR/env/aad-sdk/.env.staging $AAD_EC2_USER@$AAD_STAGING_EC2:./env/aad-sdk/.env.staging
scp -i $AAD_PEM $AAD_DOCKER_DIR/env/aad-bot/.env.staging $AAD_EC2_USER@$AAD_STAGING_EC2:./env/aad-bot/.env.staging
scp -i $AAD_PEM $AAD_DOCKER_DIR/env/aad-bot/google-creds.json $AAD_EC2_USER@$AAD_STAGING_EC2:./env/aad-bot/google-creds.json
```

Login to the Staging EC2 instance:
```shell
ssh -i $AAD_PEM $AAD_EC2_USER@$AAD_STAGING_EC2
```

Run docker-compose:
```shell
docker-compose -f docker-compose-staging.yml up --build --detach
```

##### Extra

To check the status:
```shell
docker ps
```

To check the logs of each container created:
```shell
docker container logs aad-bot-staging
docker container logs aad-sdk-staging
```

To stop either the containers:
```shell
docker stop aad-bot-staging
docker stop aad-sdk-staging
```

#### Updating Images

Run the following to get the latest images:
```shell
docker pull mattgrantumich/album-a-day:aad-bot-latest
docker pull mattgrantumich/album-a-day:aad-sdk-latest
```

## Local Development
![L2EMO](https://github.com/albumaday/.github/blob/main/public/L2EMO.png?raw=true)

To develop, run the following command:
```shell
docker-compose -f $AAD_DOCKER_DIR/docker-compose-local.yml up
```

Assuming you have all the correct files, values, and Docker configured, you should be able to access L2EMO!
